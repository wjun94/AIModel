## docker 部署各种 ai 大模型

下载 docker

docker 配置镜像,打开 Docker Desktop,Settings->Docker Engine 设置保存

```bash
{
    "registry-mirrors": [
    "https://registry.docker-cn.com",
    "https://mirror.ccs.tencentyun.com",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}
```

## 拷贝文件到 docker 容器

可以使用 docker cp 命令将本地文件或目录复制到正在运行的 Docker 容器的指定目录中。以下为你详细介绍其使用方法、示例及注意事项。

```bash
docker cp [OPTIONS] SRC_PATH CONTAINER:DEST_PATH
```

- SRC_PATH：本地文件或目录的路径。
- CONTAINER：目标容器的名称或 ID。
- DEST_PATH：容器内的目标路径。
- OPTIONS（可选）：一些可选项，例如 --archive 用于保留文件的所有元数据，--follow-link 用于跟随符号链接。

## 常见使用示例
### 1. 复制单个文件到容器指定目录
```bash
docker cp /home/user/example.txt my_container:/app/
```

### 2. 复制目录到容器指定目录
```bash
docker cp /home/user/my_folder my_container:/app/
```