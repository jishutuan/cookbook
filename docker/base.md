# Docker 安装及使用

## 安装 Docker CE

> 基于Centos 7.3 64位
>
> 使用 daocloud.io 安装源

1. 安装所需的软件包。

``` bash
sudo yum install -y yum-utils
```

2. 设置 **stable** 镜像仓库。

```bash
sudo yum-config-manager --add-repo https://download.daocloud.io/docker/linux/centos/docker-ce.repo
```

3. 安装特定版本。

```bash
sudo yum install -y -q --setopt=obsoletes=0 docker-ce-18.06.1.ce* docker-ce-selinux-18.06.1.ce*
```

4. 启动 Docker。

```bash
# 设置docker开机启动
sudo systemctl enable docker

# 启动docker
sudo systemctl start docker
```

5. 验证是否正确安装了 `docker`。

```bash
# 查看docker运行状态
sudo service docker status

# 验证是否启动
docker ps

# 验证docker版本
docker version
```