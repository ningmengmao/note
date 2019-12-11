# Docker

###### 概念

* docker主机(Host): 安装了Docker程序的电脑
* docke客户端(Client): 连接Docker主机的终端
* docker仓库(Registry): 
* docker镜像(images): 软件打包好后的镜像
* docker容器(Container): 镜像启动后的容器

---

* C/S结构
* REST API



![img](https://www.funtl.com/assets/620140640_31678.png)

* 容器与镜像相当于对象和类

  > 使用镜像创建容器

* 数据卷

---



---

###### 命令

```bash
docker pull image[:tag]      
docker image ls
docker container ls
docker run -d -p 8081:8080   以后台方式运行,将宿主机8081端口映射到docker容器8080端口
docker run -it               以交互模式运行容器
docker exec -it name bash    以bash命令进入容器
docker image rm [name][id]
docker ps 					 查看运行中的程序
docker logs id  查看容器日志
```

-----

##### dockerfile

> FROM  指定基础镜像
>
> WORKDIR  指定工作目录, 类似于当前文件夹 
>
> RUN   shell 命令

docker build -t name:tag

---

#### mysql

---

```sh
docker cp .well-known/pki-validation/fileauth.txt 21177060178f:/usr/local/tomcat/webapps/ssl

docker exec -it tomcat-test bash
```



