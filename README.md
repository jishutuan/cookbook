# cookbook

> 历经实践的技术笔记

## 这是什么？

cookbook 是记录各项技术实践过程的笔记。完整记录每项技术实践的环境和过程。

基于特定的环境和完整的重现，便于迅速实施各项技术方案，加速业务落地。

## 约定

- 超长 linux 命令使用"反斜杠"续行，确保命令在一屏内，无横向滚动条

错误示例：

```bash
sudo yum-config-manager --add-repo https://download.daocloud.io/docker/linux/centos/docker-ce.repo
```

正确示例：

```bash
sudo yum-config-manager \
--add-repo https://download.daocloud.io/docker/linux/centos/docker-ce.repo
```
