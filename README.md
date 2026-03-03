# Docker Images Pusher

使用 Github Action 将 Docker 镜像转存到阿里云私有仓库，供大陆地区阿里云服务器使用

- 支持 `DockerHub` `gcr.io` `k8s.io` `ghcr.io` 等仓库
- 支持最大40GB的大型镜像
- 使用阿里云的官方线路

## 使用方式
### 配置阿里云
1. 登录阿里云容器镜像服务

https://cr.console.aliyun.com/

3. 启用个人实例，创建一个命名空间 (**ALIYUN_NAME_SPACE**)

4. 访问凭证–>获取环境变量
- 用户名 (**ALIYUN_REGISTRY_USER**)
- 密码 (**ALIYUN_REGISTRY_PASSWORD**)
- 仓库地址 (**ALIYUN_REGISTRY**)

### 启动Action
进入您自己的项目，点击Action，启用Github Action功能

### 配置环境变量
1. 进入 **GitHub Settings > Secret and variables > Actions > New Repository secret**

2. 将上一步的**四个值**：
- `ALIYUN_NAME_SPACE`
- `ALIYUN_REGISTRY_USER`
- `ALIYUN_REGISTRY_PASSWORD`
- `ALIYUN_REGISTRY`
配置成对应的环境变量。

### 添加镜像
打开 **images.txt** 文件，添加需要的镜像。

- 可以加 tag，也可以不用 (默认latest)
- 可添加 `--platform=xxxxx` 的参数指定镜像架构
- 可使用 k8s.gcr.io/kube-state-metrics/kube-state-metrics 格式指定私库
- 可使用 `#` 开头作为注释

文件提交后将自动进入Github Action构建

### 使用镜像
回到阿里云，镜像仓库，点击任意镜像，可查看镜像状态。

在国内服务器拉取镜像, 例如：
```bash
docker pull registry.cn-hangzhou.aliyuncs.com/images/alpine
```

`registry.cn-hangzhou.aliyuncs.com` 即 `ALIYUN_REGISTRY` (阿里云仓库地址)
`shrimp-images` 即 `ALIYUN_NAME_SPACE` (阿里云命名空间)
`alpine` 即阿里云中显示的镜像名

### 多架构
需要在 images.txt 中用 `--platform=xxxxx` 手动指定镜像架构
指定后的架构会以前缀的形式放在镜像名字前面

### 镜像重名
程序自动判断是否存在名称相同, 但是属于不同命名空间的情况。
如果存在，会把命名空间作为前缀加在镜像名称前。

例如:
```
xhofe/alist
xiaoyaliu/alist
```

### 定时执行
修改/.github/workflows/docker.yaml文件
添加 schedule 即可定时执行(此处cron使用UTC时区)
