# MongoDB 安装及使用

## 环境及版本

- Centos 7.3
- [MongoDB 4.0.5 镜像](https://hub.docker.com/_/mongo/)
- Docker 18.06.1-ce
- Docker 官方镜像

## 安装

### 1. 拉取镜像并启动容器

``` bash
# 拉取镜像
docker pull mongo:4.0.5-xenial
# host机创建目录，用于保存mongo数据
mkdir /mnt/mongo

# 该镜像映射的端口号是27017，配置文件的位置在/data/configdb，数据库文件的位置在/data/db。
# 启动容器
docker run -d -p 27017:27017 -v /mnt/mongo/configdb:/data/configdb -v /mnt/mongo/db:/data/db --name mongo mongo:4.0.5-xenial --auth

# 启动但无法链接，因为开启了身份认证模式，但还没有创建用户设置密码。
```

### 2. 配置管理员账号

```bash
# 进入容器内部的mongo命令行
docker exec -it mongo mongo admin

# 创建管理员账号
db.createUser({ user: 'admin', pwd: 'q1w2e3r4', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });

#创建好账户之后，我们再尝试使用Robo工具来连接，在Authentication栏目填写上刚才创建的用户名密码，即可连接成功。
```

### 3. 创建数据库并设置用户

```bash
# 进入mongo的命令行页面
docker exec -it mongo mongo admin

# 使用上一步骤创建的管理员账户进行授权
db.auth("admin","q1w2e3r4");

# 切换到test库（如不存在会自动创建）
use test

# 创建test库下的用户
db.createUser({ user: 'test', pwd: '123456', roles: [{ role: "readWrite", db: "test" }] });

# 连接成功
```

## 数据备份与迁移

- 数据库保存在`host`主机 `/mnt/mongo/db` 目录
- 备份还原只需保存或迁移 `/mnt/mongo/db`目录
- 不同容器，挂载相同`/mnt/mongo/db`目录，则数据库完全相同

## 附录

- [配置文件 `mongod.conf`](https://docs.mongodb.com/manual/reference/configuration-options/)

