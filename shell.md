# SHELL脚本使用

### 获取接口请求的返回值，并赋值给变量

```shell
commitHash=$(curl -s http://ci.rmxc.tech/job/Test-k8s-front-pplmc-admin/lastSuccessfulBuild/api/json）
```

注意：

1. 这里对curl的请求用$()进行了包裹
2. shell脚本对空格敏感，这里的commitHash后的=号之间没有空格
3. 返回的是json格式的字符串
4. curl的`-s`参数表示只输出结果，不包含额外的total等统计信息

### 在shell中使用正则过滤

```shell
echo 'usename123' | grep -E -o '\d+'
```

注意：

1. 这里使用了`-E`参数，表示使用正则扩展语法，在window下是`-P`

### 获取输出中的第一个值

```shell
head -n 1
# 如：echo 'usename123' | grep -E -o '\d+' | head -n 1
```