# Docker Images Pusher

使用 Github Action 将 Docker 镜像转存到阿里云私有仓库，供大陆地区阿里云服务器使用

- 支持 `DockerHub` `gcr.io` `k8s.io` `ghcr.io` 等仓库中最大 40GB 的大型镜像
- 使用阿里云的官方线路

## 使用方式

### 一、配置阿里云

1. 登录阿里云[容器镜像服务控制台](https://cr.console.aliyun.com/) `cr.console.aliyun.com`

2. 启用个人实例，并创建一个命名空间，作为 (**ALIYUN_NAME_SPACE**) 保存。

3. 点击阿里云控制台的 `访问凭证` > `获取环境变量`，找到并保存以下变量值：

- 用户名 (**ALIYUN_REGISTRY_USER**)
- 密码 (**ALIYUN_REGISTRY_PASSWORD**)
- 仓库地址 (**ALIYUN_REGISTRY**)

### 二、启动 GitHub Actions
进入您自己的项目，点击 Actions，启用 Github Actions 功能

### 三、配置仓库环境变量

1. 进入 **GitHub Settings > Secret and variables > Actions > New Repository secret**

2. 将上一步的**四个值**：
- `ALIYUN_NAME_SPACE`
- `ALIYUN_REGISTRY_USER`
- `ALIYUN_REGISTRY_PASSWORD`
- `ALIYUN_REGISTRY`
配置成对应的环境变量。

### 添加镜像
打开 `images.txt` 文件即可添加需要的镜像。

- 可以选择加 tag，不添加是默认为 `latest`
- 可添加 `--platform=xxxxx` 的参数指定镜像架构
- 可使用 `k8s.gcr.io` `kube-state-metrics` `kube-state-metrics` 格式指定私库
- 可使用 `#` 开头作为注释

文件提交后将自动进入 Github Actions 构建

### 使用镜像
回到阿里云镜像仓库，点击任意镜像，可查看镜像状态。

在国内服务器拉取镜像时请使用命令例如：
```bash
docker pull registry.cn-hangzhou.aliyuncs.com/images/alpine
```

`registry.cn-hangzhou.aliyuncs.com` 即 `ALIYUN_REGISTRY`，阿里云仓库地址
`shrimp-images` 即 `ALIYUN_NAME_SPACE`，阿里云命名空间
`alpine` 即阿里云中显示的镜像名

### 多架构
需要在 images.txt 中用 `--platform=xxxxx` 手动指定镜像架构。指定后的架构会以前缀的形式放在镜像名字前面。

### 镜像重名
将自动判断是否存在名称相同但是属于不同命名空间的情况。如果存在，会把命名空间作为前缀加在镜像名称前。

### 定时执行
修改 `/.github/workflows/docker.yaml` 配置文件，添加 schedule 即可定时执。注意此处 cron 使用 UTC 时区，北京时间为 UTC + 8。
