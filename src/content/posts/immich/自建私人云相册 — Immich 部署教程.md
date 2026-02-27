# 前言
最近给我的小主机 HP Elite 800 G SFF 重装了 Fedora 43 WorkStation 系统，在使用 Docker Compose 安装 Immich 的时候发现了一点问题，不过顺利解决。于是决定顺便把部署过程写出来😎

# 部署过程
### 下载配置文件
首先新建文件夹用于存放配置文件，例如
``/home/Docker/immich

然后在文件夹中打开终端：

下载 docker-compose.yml
```
wget -O docker-compose.yml https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
```

下载 .env file
```
wget -O .env https://github.com/immich-app/immich/releases/latest/download/example.env
```

当然，也可以手动下载好文件再移动到文件夹中，但是要记得把`example.env` 重命名为 `.env`哦！

### 自定义 .env 文件配置

下面是默认的 .env 文件配置

```
# You can find documentation for all the supported env variables at https://docs.immich.app/install/environment-variables

# The location where your uploaded files are stored
UPLOAD_LOCATION=./library

# The location where your database files are stored. Network shares are not supported for the database
DB_DATA_LOCATION=./postgres

# To set a timezone, uncomment the next line and change Etc/UTC to a TZ identifier from this list: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List
# TZ=Etc/UTC

# The Immich version to use. You can pin this to a specific version like "v2.1.0"
IMMICH_VERSION=v2

# Connection secret for postgres. You should change it to a random password
# Please use only the characters `A-Za-z0-9`, without special characters or spaces
DB_PASSWORD=postgres

# The values below this line do not need to be changed
###################################################################################
DB_USERNAME=postgres
DB_DATABASE_NAME=immich
```

- 可以替换 `UPLOAD_LOCATION` 的值为你想要用于存放备份数据的位置，请填写绝对路径，并且需要是一个新目录，有充足的空间。
- 可以考虑修改 `DB_PASSWORD` 值，Postgres并不会公开暴露，因此该密码仅用于本地验证。为避免与 Docker 冲突，密码最好仅使用`A-Za-z0-9`的这些字符。
- 取消  `TZ=` 行的注释即可自定义你的时区。

### 修改 docker -compose .yml 文件
由于 Fedora 的 SELinux 限制，我们还需要额外修改一下 `docker-compose.yml` 文件，即在每个 `volume` 值的后方加上 `:Z`，这一步是为了让 Fedora 的 SELinux 不再拦截。

注意：timezone 不需要添加 `:Z`

### 启动 Immich
在文件夹中打开终端，运行下方命令即可以后台服务的形式启动 Immich！

启动容器
```
podman-compose up -d
```

# 结语
如果一切正常，那么恭喜你，已经成功的在 Fedora 上运行了 Immich，接下来在浏览器中访问 localhost:2283 即可开始配置 Immich，快去体验吧！