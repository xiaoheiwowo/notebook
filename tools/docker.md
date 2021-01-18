# docker

```shell
# 添加到docker用户组
sudo gpasswd -a $USER docker

# 启动
sudo systemctl start docker
# 加入开机启动
sudo systemctl enable docker

```





## 镜像

```shell
# 根据名字搜索服务器上的镜像（可以过滤，官方，star ...）
docker search <image name>
# 下载镜像/上传
docker pull <image name>
docker push
# 查看本地镜像
docker images
# 删除本地镜像
docker rmi image_name

# 给新镜像添加tag
docker tag <id> <name>:<ver>

```

## 容器

```shell
# 创建
docker run -itd --name=<container_name> <image_name>
-i 交互式
-d 后台运行，返回id
-t 为容器重新分配一个伪终端

# 进入
docker exec -it <docker name/id>
docker exec -ti -u root 4650e8d1bcca bash
# 查看
docker ps /-a
# 启动，重启，停止，删除
docker start/restart/stop/rm <name/id>

# 提交新的镜像
docker commit -a 'author' -m 'message' <container_name/id> <new_image_name>:<tag_name>

# 端口映射
docker run -itd -p <host port>:<docker port>
# 文件挂载
docker run -itd -v <host dir>:<docker dir>
# 复制文件
docker cp <host dir file_name> <docker name>:<docker dir file_name>
# 容器互联
docker run -itd --lind 要关联的容器名字:容器在被关联容器中的别名

# 获取容器/镜像的元数据
docker inspect <id/name>
# docker log
docker logs <id/name>

```

## 仓库

```shell

```



---



## Dockerfile

Dockerfile 一般分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令，#Dockerfile 中的注释。

```shell
docker build -f /path/to/a/Dockerfile
```

- FROM 基础镜像信息

```dockerfile
格式：
　　FROM <image>
　　FROM <image>:<tag>
　　FROM <image>@<digest>
示例：
　　FROM mysql:5.6
注：
　　tag或digest是可选的，如果不使用这两个值时，会使用latest版本的基础镜像
```

-  MAINTAINER 维护者信息

```SHELL
格式：
    MAINTAINER <name>
示例：
    MAINTAINER Jasper Xu
    MAINTAINER sorex@163.com
    MAINTAINER Jasper Xu <sorex@163.com>
```

- RUN 用于在镜像容器中执行命令

```SHELL
有两种命令执行方式：
shell执行
格式：
    RUN <command>
exec执行
格式：
    RUN ["executable", "param1", "param2"]
示例：
    RUN ["executable", "param1", "param2"]
    RUN apk update
    RUN ["/etc/execfile", "arg1", "arg1"]
注：
　　RUN指令创建的中间镜像会被缓存，并会在下次构建中使用。如果不想使用这些缓存镜像，可以在构建时指定--no-cache参数，如：docker build --no-cache
　
```

- ADD 将本地文件添加到容器中，tar类型文件会自动解压(网络压缩资源不会被解压)，可以访问网络资源，类似wget

```shell
格式：
    ADD <src>... <dest>
    ADD ["<src>",... "<dest>"] 用于支持包含空格的路径
示例：
    ADD hom* /mydir/          # 添加所有以"hom"开头的文件
    ADD hom?.txt /mydir/      # ? 替代一个单字符,例如："home.txt"
    ADD test relativeDir/     # 添加 "test" 到 `WORKDIR`/relativeDir/
    ADD test /absoluteDir/    # 添加 "test" 到 /absoluteDir/
```

- COPY  复制文件
- CMD 容器构建后调用

```shell
# 只能用一次 可以吧要执行的命令写进脚本 用CMD运行
格式：
    CMD ["executable","param1","param2"] (执行可执行文件，优先)
    CMD ["param1","param2"] (设置了ENTRYPOINT，则直接调用ENTRYPOINT添加参数)
    CMD command param1 param2 (执行shell内部命令)
示例：
    CMD echo "This is a test." | wc -
    CMD ["/usr/bin/wc","--help"]
注：
 　　CMD不同于RUN，CMD用于指定在容器启动时所要执行的命令，而RUN用于指定镜像构建时所要执行的命令。
```

- ENV 设置环境变量

```shell
格式：
    ENV <key> <value>  #<key>之后的所有内容均会被视为其<value>的组成部分，因此，一次只能设置一个变量
    ENV <key>=<value> ...  #可以设置多个变量，每个变量为一个"<key>=<value>"的键值对，如果<key>中包含空格，可以使用\来进行转义，也可以通过""来进行标示；另外，反斜线也可以用于续行
示例：
    ENV myName John Doe
    ENV myDog Rex The Dog
    ENV myCat=fluffy
```



- EXPOSE 指定与外界交互的端口

```shell
格式：
    EXPOSE <port> [<port>...]
示例：
    EXPOSE 80 443
    EXPOSE 8080
    EXPOSE 11211/tcp 11211/udp
注：
　　EXPOSE并不会让容器的端口访问到主机。要使其可访问，需要在docker run运行容器时通过-p来发布这些端口，或通过-P参数来发布EXPOSE导出的所有端口
```

- WORKDIR 工作目录 = cd

```shell
格式：
    WORKDIR /path/to/workdir
示例：
    WORKDIR /a  (这时工作目录为/a)
    WORKDIR b  (这时工作目录为/a/b)
    WORKDIR c  (这时工作目录为/a/b/c)
注：
　　通过WORKDIR设置工作目录后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT、ADD、COPY等命令都会在该目录下执行。在使用docker run运行容器时，可以通过-w参数覆盖构建时所设置的工作目录。
```

- USER 
- **指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户。使用USER指定用户时，可以使用用户名、UID或GID，或是两者的组合。当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户**

```shell
格式:
　　USER user
　　USER user:group
　　USER uid
　　USER uid:gid
　　USER user:gid
　　USER uid:group
 示例：
    　　USER www
 注：
　　使用USER指定用户后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT都将使用该用户。镜像构建完成后，通过docker run运行容器时，可以通过-u参数来覆盖所指定的用户。
```





### eg:

```dockerfile
# This my first nginx Dockerfile
# Version 1.0

# Base images 基础镜像
FROM centos

#MAINTAINER 维护者信息
MAINTAINER tianfeiyu 

#ENV 设置环境变量
ENV PATH /usr/local/nginx/sbin:$PATH

#ADD  文件放在当前目录下，拷过去会自动解压
ADD nginx-1.8.0.tar.gz /usr/local/  
ADD epel-release-latest-7.noarch.rpm /usr/local/  

#RUN 执行以下命令 
RUN rpm -ivh /usr/local/epel-release-latest-7.noarch.rpm
RUN yum install -y wget lftp gcc gcc-c++ make openssl-devel pcre-devel pcre && yum clean all
RUN useradd -s /sbin/nologin -M www

#WORKDIR 相当于cd
WORKDIR /usr/local/nginx-1.8.0 

RUN ./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_ssl_module --with-pcre && make && make install

RUN echo "daemon off;" >> /etc/nginx.conf

#EXPOSE 映射端口
EXPOSE 80

#CMD 运行以下命令
CMD ["nginx"]
```



## docker的四种网络模式

https://www.jianshu.com/p/22a7032bb7bd



- host 和宿主机共享 Network Namespace
- container 和另一个容器共享 Network Namespace
- none 没有网络
- bridge 端口映射





## ZOOKEEPER

```SH
#创建宿主机挂载目录
mkdir -p $HOME/service/zookeeper/conf
mkdir -p $HOME/service/zookeeper/data
mkdir -p $HOME/service/zookeeper/log

docker run -d --name zk --restart always zookeeper

#把容器中的配置文件复制出来
docker cp -a zk:/conf/zoo.cfg $HOME/service/zookeeper/conf/zoo.cfg

docker stop zk
docker rm zk
docker run -d --name zk --restart always \
-p 2181:2181 -p 2888:2888 -p 3888:3888 \
-v $HOME/service/zookeeper/conf/zoo.cfg:/conf/zoo.cfg \
-v $HOME/service/zookeeper/data:/data \
-v $HOME/service/zookeeper/log:/datalog \
zookeeper
```

