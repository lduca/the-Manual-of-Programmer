
学习Docker的使用。

包括Docker的一些命令，Docker容器的使用，Docker镜像的使用，Docker容器连接等内容。

@[toc]

## Docker的架构

### Docker deamon（Docker守护进程）：
Docker deamon是一个运行在宿主机（DOCKER_HOST）的后台进程。可通过Dokcer客户端与之通信。
### Client（Docker客户端）：
Docker客户端是Docker的用户界面，它可以接受用户命令和配置标识，并与Docker daemon	通信。docker build等都是Docker的相关命令。
### Images（Docker镜像）：
Docker镜像是一个只读模板，它包含创建Docker容器的说明。它和系统安装光盘有点像——使用系统安装光盘可以安装系统，同理，使用Docker镜像可以运行Docker镜像中的程序。
### Container（容器）：
容器是镜像可运行实例。镜像和容器的关系有点类似于面向对象中，类和对象的关系。可通过Docker API或者CLI命令来启停、移动、删除容器。
### Registry（注册中心）：
Docker Registry是一个集中存储与分发镜像的服务。构建完Docker镜像后，就可在当前宿主机上运行。但如果想要在启停机器上运行这个镜像，就需要手动复制。此时可借助Docker Registry来避免镜像的手动复制。

一个Docker Registry可包含多个Docker仓库，每个仓库可包含多个镜像标签，每个标签对应一个Docker镜像。

## Docker常用命令

### 搜索命令：docker search [OPTIONS] TERM
参数列表：
Name,shorthand				Default				Description
--filter,-f													根据指定条件过滤结果
--limit								25					搜索结果的最大条数
--no-trunc						false				不截断输出，显示完整的输出
| Name,shorthand | Default  |  Description|
|--|--| --|
|--filter,-f |  |根据指定条件过滤结果  |
|--limit	 |  25 |搜索结果的最大条数  |
|--no-trunc| false |不截断输出，显示完整的输出  |

示例：docker search java
| NAME| DESCRIPTION|STARS |OFFICIAL | AUTOMATED|
|--|--| --| --| --| --|
|java |Java is a concurrent,...  |1281   |  [OK]|
|anapsix/alpine-java|  Oracle Java 8(and 7),... |190  | |[OK] |

 - NAME：镜像仓库名称。
 - DESCRIPTION：镜像仓库描述。
 - STARS：镜像仓库收藏数，类似Git的Stars。
 - OFFICIAL：表示是否为官方仓库。
 - AUTOMATED：表示是否是自动构建的镜像仓库。

### 下载镜像：docker pull
使用docker pull从Docker Registry上下载镜像。
docker pull [OPTIONS] NAME [:TAG|@DIGEST]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--all-tags,-a |false  |下载所有标签的镜像  |
|--disable-content-trust|  true |忽略镜像的校验 |

示例：docker pull java；docker pull reg.itmuch.com/java:7；（可以从指定Docker Registry中下载标签为7的Java镜像）

### 列出镜像：docker images


命令格式：docker images [OPTIONS]  [REPOSITORY[:TAG]] 
| Name,shorthand | Default  |  Description|
|--|--| --|
|--all,-a |false  |列出本地所有的镜像（包括中间镜像层，默认情况下，过滤中间映像层） |
|--digests|  false |显示摘要信息 |
|--filter,-f|  |显示满足条件的镜像 |
|--format|  |通过Go语言模板文件展示镜像 |
|--no-trunc|false  |不截断输出，显示完整的镜像信息|
|--quiet,-p|false  |只显示镜像ID |
示例：

> docker images
> docker images java
> docker images java:8
> docker images --digests
> docker images --filter "dangling=true" #展示虚悬镜像

执行该命令后，会看到类似如下表格：
| REPOSITORY | TAG|  IMAGE ID|CREATED|SIZE|
|--|--| --|--|--|
|java |latest|861e95c114d6 |4 weeks ago| 643.1MB|
该表格包含5列，含义如下：

 - REPOSITORY：镜像所属仓库名称
 - TAG：镜像标签。默认是latest，表示最新。
 - IMAGE ID：镜像ID，表示镜像唯一标识。
 - CREATED：镜像创建时间。
 - SIZE：镜像大小。

### 删除本地镜像：docker rmi
命令格式：docker rmi [OPTIONS] IMAGE [IMAGE...]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--force,-f |false  |强制删除 |
|--no-prune |false  |不移除该镜像的过程镜像，默认移除 |
示例1：删除指定名称的镜像。

> docker rmi hello-world
> 表示删除hello-world这个镜像

示例2：删除所有镜像

> docker rmi -f $(docker images)


### 保存镜像：docker save
命令格式：docker save [OPTIONS] IMAGE [IMAGE...]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--output,-o |  |输出到文件，而非标准输出 |

示例：

> #将busybox保存为busybox.tar
> docker save busybox > busybox.tar
> docker save --output busybox.tar busybox


### 加载镜像：docker load
命令格式：docker load [OPTIONS]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--input,-i |  |从文件加载而非标准输入 |
|--quiet,-q |false  |静默加载 |

示例：

> #从busybox.tar 文件中加载镜像
> docker load < busybox.tar
> docker load --input busybox.tar

### 构建镜像：通过Dockerfile构建镜像
命令格式：docker build [OPTIONS] PATH | URL | -
docker build命令参数
| Name,shorthand | Default  |  Description|
|--|--| --|
|--add-host |  |添加自定义从host到IP的映射，格式为（host:ip） |
|--build-arg ||设置构建时的变量 |
|--cache-from ||作为缓存源的镜像 |
|--cgroup-parent ||为容器指定可选的父cgroup |
|--compress |false|使用gzip亚索构建上下文|
|--cpu-period |0|限制CPU CFS周期|
|--cpu-quota |0|限制CPU CFS配额|
|--cpu-shares,-c |0|CPU使用权重（相对权重）|
|--cpuset-cpus ||指定允许执行的CPU（0-3,0,1）|
|--cpuset-mems-arg ||指定允许执行的内存（0-3,0,1）|
|--disable-content-trust |true|忽略校验|
|--file,-f ||指定Dockerfile的名称，默认是PATH/Dockerfile|
|--force-rm |false|删除中间容器|
|--isolation||指定容器隔离技术|
|--label||为镜像设置元数据|
|--memory,-m |0|设置内存限制|
|--memory-swap |0|设置swap的最大值为内存+swap，如果设置为-1，表示不限swap|
|--network|default|在构建期间设置RUN指定的网络模式|
|--no-cache|false|构建镜像过程中不适用缓存|
|--pull|false|总是尝试去下载更新版本的镜像|
|--quiet,-q|false|静默模式，构建成功后只输出镜像ID|
|--rm|true|构建成功后立即删除中间容器|
|--security-opt||安全选项|
|--shm-size|0|指定/dev/shm目录的大小|
|--squash|false|[实验]将构建的层压缩成一个新的层|
|--stream||[实验]连接到服务器的流，用于协商构建上下文|
|--tag,-t||设置标签，格式为name:tag，其中tag可选|
|--target ||设置构建时的目标构建阶段|
|--ulimit||设置构建时的目标构建阶段|

示例：

> docker build -t itmuch/some-repo:some-tag .



## Docker容器常用命令

### 新建并启动容器：docker run
使用docker run 命令即可新建并启动一个容器。
| Name,shorthand | Default  |  Description|
|--|--| --|
|--detach,-d |  |后台运行容器，并打印容器ID |
|--punish-all,-p |  |随机映射所有端口 |
|--punish,-p |  |指定端口映射，该选项有以下四种格式： |
| |  |ip:hostPort：containerPort |
| |  |ip::containerPort|
| |  |hostPort：containerPort |
| |  |containerPort |
|--network |  |指定网络模式，该选项有以下四种可选参数：|
| |  |--network=bridge:默认选项，表示连接到默认的网桥|
| |  |--network=host:容器使用宿主机的网络 |
||  |--network=container：NAME_or_ ID：告诉Docker让新建的容器使用已有容器的网络配置|
| |  |--network=none：不配置该容器的网络，用户可自定义网络配置 |

示例：

> docker run -d -p 01:80 nginx
> 启动一个Nginx容器，访问http://Docker宿主机IP：91/就可以访问。

### 列出容器：docker ps 
命令格式：docker ps [OPTIONS]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--all,-a |false  |列出所有容器，包括未运行的容器。默认只展示运行的容器。 |
|--filter,-f |  |根据条件过滤显示内容 |
|--format |  |通过Go语音模板文件展示镜像 |
|--last,-n|  -1|显示最近创建的n个容器（包含所有状态） |
|--latest,-l |false  |显示最近创建的容器（包含所有状态） |
|--no-trunc| false |不截断输出 |
|--quiert,-q | false |静默模式，只展示容器的ID |
|--size,-s | false |显示总文件大小 |

示例：docker ps 
| CONTAINER ID| IMAGE|  COMMAND|CREATED |STATUS |PORTS|
|--|--| --|--|--|--|

 - CONTAINER_ID：表示容器ID
 - IMAGE：表示镜像名称
 - COMMAND：表示启动容器时运行的命令
 - CREATED：表示容器的创建时间
 - STATUS：表示容器运行的状态。UP表示运行中，Exited表示已停止
 - PORTS：表示容器对外的端口号
 - NAME：表示容器名称。该名称默认由Docker自动生成，也可使用docker run命令的-name选项自行指定。

### 停止容器：docker stop
使用docker stop命令即可停止容器。
命令格式：docker stop [OPTIONS] CANTAINER [CANTAINER...]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--time,-t |10|强制停止容器前等待的时间，单位是s |
示例：docker stop [容器ID]

### 强制停止容器：docker kill
可使用docker kill命令发送SIGKILL信号来强制停止容器。
命令格式：docker kill [OPTIONS] CONTAINER [CONTAINER...]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--signal,-s |KILL|向容器发送一个信号|
示例：
docker kill [容器ID]

### 启动已停止的容器：docker start 
使用docker run命令即可新建并启动一个容器，对于已停止的容器，可使用docker start命令来启动。
命令格式：docker start [OPTIONS] CONTAINER [CONTAINER...]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--attach,-a |false|连接STDOUT/STDERR并转发信号|
|--detach-keys ||覆盖断开容器的关键顺序|
|--interactive,-i |false|连接容器的STDIN|
示例：docker start [容器ID]

### 重启容器：docker restart
可使用docker restart命令来重启容器，该命令实际上是先执行了docker stop，然后执行了docker start。
命令格式：docker restart [OPTIONS] CONTAINER [CONIAINER...]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--time,-t |10|关闭容器前等待的时间，单位是s|

### 进入容器：docker attach
某些场景下，可能需要进入运行中的容器。
使用docker attach进入容器；例如

> docker attach [容器ID]

在很多场景下，使用docker attach命令并不方便。当多个窗口同时attach到同一个容器时，所有窗口都会同步显示。同理，如果某个窗口发生阻塞，其他窗口也无法执行操作。

使用neenter进入容器；

使用docker exec命令进入容器；
示例：

> docker exec -it 容器id /bin/bash


### 删除容器：docker rm
docker rm 即可删除指定容器；
命令格式：docker rm [OPTIONS] CONTAINER [CONTAINER...]
| Name,shorthand | Default  |  Description|
|--|--| --|
|--force,-f |false|通过SIGKILL信号强制删除正在运行中的容器|
|--link,-l |false|删除容器间的网络连接|
|--volumes,-v |false|删除与容器关联的卷|

示例1:删除指定容器。

> docker rm [容器ID]
该命令只能删除已停止的容器，如需删除正在运行的容器，可使用-f参数。

示例2：删除所有容器

> docker rm -f ${docker ps -a -q}

### 导出容器：docker export
使用docker export命令可将容器导出成一个压缩包文件。
命令格式：docker export [OPTIONS] CONTAINER
| Name,shorthand | Default  |  Description|
|--|--| --|
|--output,-o ||将内容写到文件而非标准输出|
示例：

> 将red容器到处城latest.tar文件
> docker export red > latest.tar
> docker export --output="latest.tar" red


### 导入容器：docker import
使用docker import命令即可从归档文件导入内容并创建镜像。
命令格式：
| Name,shorthand | Default  |  Description|
|--|--| --|
|--change,-c ||将Dockerfile指令应用到创建的镜像|
|--message,-m ||为导入的镜像设置提交信息|
示例：

> 从nginx2.tar文件导入，并创建nginx镜像
> docker import nginx2.tar nginx


## 查看进程并干掉进程
    jps是jdk提供的一个查看当前java进程的小工具， 可以看做是JavaVirtual Machine Process Status Tool的缩写。非常简单实用。
>    命令格式：jps [options ] [ hostid ]

    [options]选项 ：
    

> -q：仅输出VM标识符，不包括classname,jar name,arguments in main method 
-m：输出main method的参数 
-l：输出完全的包名，应用主类名，jar的完全路径名 
-v：输出jvm参数 
-V：输出通过flag文件传递到JVM中的参数(.hotspotrc文件或-XX:Flags=所指定的文件 
-Joption：传递参数到vm,例如:-J-Xms512m
    
### jps
![在这里插入图片描述](https://img-blog.csdn.net/20140830103850281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lzZ29vZA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### jps –l:输出主类或者jar的完全路径名
![在这里插入图片描述](https://img-blog.csdn.net/20140830104140824?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lzZ29vZA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### jps –v :输出jvm参数
![在这里插入图片描述](https://img-blog.csdn.net/20140830104154505?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lzZ29vZA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### jps –q ：仅仅显示java进程号
![在这里插入图片描述](https://img-blog.csdn.net/20140830104223865?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lzZ29vZA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### jps -mlv10.134.68.173
![在这里插入图片描述](https://img-blog.csdn.net/20140830104019250?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lzZ29vZA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 干掉进程
kill - 9 表示强制杀死该进程；而 kill 则有局限性，例如后台进程，守护进程等；

kill -9 id：一般不加参数kill是使用15来杀，这相当于正常停止进程，停止进程的时候会释放进程所占用的资源；他们的区别就好比电脑关机中的软关机（通过“开始”菜单选择“关机”）与硬关机（直接切断电源），虽然都能关机，但是程序所作的处理是不一样的。

执行kill命令，系统会发送一个SIGTERM信号给对应的程序。SIGTERM多半是会被阻塞的。kill -9命令，系统给对应程序发送的信号是SIGKILL，即exit。exit信号不会被系统阻塞，所以kill -9能顺利杀掉进程。

> kill命令格式：
kill -Signal pid
pid是进程号，可以用 ps 命令查出
signal是发送给进程的信号，TERM(或数字9）表示“无条件终止”；
因此 kill - 9 表示强制杀死该进程；
而 kill 则有局限性，例如后台进程，守护进程等；

## 思维导图

![docker](https://img-blog.csdnimg.cn/20181220164755353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuMTA5MDIzOTc4Mg==,size_16,color_FFFFFF,t_70)


docker常用命令：
![常用Docker命令](https://img-blog.csdnimg.cn/20181221141903353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuMTA5MDIzOTc4Mg==,size_16,color_FFFFFF,t_70)
