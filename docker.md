

# 环境查看

> 系统内核

```
uname -r
```

> 系统版本

```
cat /etc/os-release
```

# 安装

https://docs.docker.com/engine/install/centos/

> 卸载旧版本

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

> 依赖包

```
sudo yum install -y yum-utils
```

> 设置镜像仓库

```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

> 安装

```
sudo yum install docker-ce docker-ce-cli containerd.io
```

> 启动

```
sudo systemctl start docker
```

> 查看版本号

```
docker version
```

![image-20200830122537716](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200830122537716.png)

> 验证，下载hello-world镜像

```
sudo docker run hello-world
```

> 查看镜像

```
docker images
```

> 卸载docke

```
sudo yum remove docker-ce docker-ce-cli containerd.io
```

```
sudo rm -rf /var/lib/docker
```

# 使用官方安装脚本自动安装

```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

# 配置阿里云加速器

https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://48u037qp.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

# 镜像命令

> 查看帮助

```
docker search --help
```

> 搜索镜像

```
docker search mysql
```

> 过滤条件

```
docker search mysql -f=STARS=3000
```

![image-20200830130132398](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200830130132398.png)

> 下载镜像

```
docker pull mysql
```

https://hub.docker.com/ 查找镜像 所支持的版本，不加版本号使用默认版本

```
docker pull mysql:5.7
```

![image-20200830130323520](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200830130323520.png)

> 删除镜像

```
docker rmi -f bf756fb1ae65
```

> 删除全部镜像

```
docker rmi -f $(docker images -aq)
```

# 容器命令

```
docker run [可选参数] images
#参数说明
--name="Name"  容器名字
-d 后台运行
-p 制定端口容器 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口
-p 随机端口

#测试
#启动并进入容器
docker run -it centos /bin/bash 

```

> 查看正在运行的容器

```
docker ps
```

> 查看历史运行过的容器+现在运行

```
docker ps -a
```

> 退出容器

```
#停止容器
exit
#不停止退出
Ctrl + P + Q
```

> 删除容器

```
#不能删除运行的容器
docker rm 容器id
#删除全部容器
docker rm $(docker ps -aq)
```

> 启动和停止容器

```
docker start 容器id
docker restart 容器id
docker stop 容器id
docker kill 容器id
```

