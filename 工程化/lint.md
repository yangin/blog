### 在CI中实现增量Lint

思路：基于上一次成功build的结果，只对本次提交的commit进行lint
> 基于Jenkins实现

在jenkins的execute shell中执行

```bash
# 获取上次build的commit hash
lastCommitHash=$(curl -s http://${JENKINS_ACCESS}@ci.rmxc.tech/job/Test-k8s-front-pplmc-admin/lastSuccessfulBuild/api/json | grep -E -o '"SHA1":"\w+","name":"origin\/test"'| grep -E -o '"SHA1":"\w+"'| grep -E -o '\w{40}' | head -n 1)
# 在ci中执行lint-staged
npx lint-staged --diff="$lastCommitHash"
```

解释说明:
1. 通过 `/job/Test-k8s-front-pplmc-admin/lastSuccessfulBuild/api/json` 接口获取上一次成功 build 的 build info
2. 对 build info进行解析获取commit hash
3. 基于`lint-staged`的 `--diff` 参数，只对本次提交的commit进行lint
4. `lint-staged` 的 `--diff` 参数在 v12.5.0+ 版本才支持
5. `JENKINS_ACCESS`这里指`用户名:token`，格式为：'username:token', 需在Jenkins里进行配置