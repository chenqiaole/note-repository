#                              服务安装发布

## 一、linux安装

#### 1、网络设置

> VMware中CentOS 7设置固定IP同时连接内外网，网络配置！！！ 分分钟解决的事儿
>
> https://blog.csdn.net/java_zyq/article/details/78280904?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-7.essearch_pc_relevant&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-7.essearch_pc_relevant

1、设置虚拟机的网络连接方式： 
按照如下图设置，英文版的对照设置即可

![这里写图片描述](https://img-blog.csdn.net/20170312215222174?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2.配置虚拟机的NAT模式具体地址参数：

（1）编辑–虚拟网络编辑器–更改设置 

![这里写图片描述](https://img-blog.csdn.net/20170312215300513?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


（2）选择VMnet8–取消勾选使用本地DHCP–设置子网IP–网关IP设置（记住此处设置，后面要用到），如下图

说明：修改子网IP设置，实现自由设置固定IP； 
若你想设置固定IP为192.168.2.2-255，比如192.168.2.2，则子网IP为192.168.2.0； 
若你想设置固定IP为192.168.1.2-255，比如192.168.1.2，则子网IP为192.168.1.0； 
也就是说，你想配置成哪个网段，IP地址最后那位为0即可。

![这里写图片描述](https://img-blog.csdn.net/20170312215522988?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

（3）网关IP可以参照如下格式修改：192.168.2.1

![这里写图片描述](https://img-blog.csdn.net/20170312215634879?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

3.配置笔记本主机具体VMnet8本地地址参数：

说明：第6步中的IP地址随意设置，但是要保证不能跟你要设置虚拟机的固定IP一样。我设置的是：192.168.116.1

![这里写图片描述](https://img-blog.csdn.net/20170312215921460?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

4.修改虚拟机中的CentOS 7系统为固定IP的配置文件：

（1）进入centos7命令行界面，修改如下内容：

```json
cd /etc/sysconfig/network-scripts/
vim ifcfg-eno16777736 #我的是ifcfg-en33,视电脑情况而定
```

![这里写图片描述](https://img-blog.csdn.net/20170312220603267?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](https://img-blog.csdn.net/20170312220615017?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

说明：

```json
BOOTPROTO=static        #开机协议，有dhcp及static；
ONBOOT=yes              #设置为开机启动；
DNS1=114.114.114.114    #这个是国内的DNS地址，是固定的；
IPADDR=192.168.2.2      #你想要设置的固定IP，理论上192.168.2.2-255之间都可以，请自行验证；
NETMASK=255.255.255.0   #子网掩码，不需要修改；
GATEWAY=192.168.2.1     #网关，这里应该和你“2.配置虚拟机的NAT模式具体地址参数”中的（2）选择VMnet8--取消勾选使用本地DHCP--设置子网IP--网关IP设置 一样才行。
```

本人实际测试过，即使勾选也可以。你也可以试试不注释。 
我最终的配置：

```json
TYPE=Ethernet
#BOOTPROTO=dhcp
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=f2367168-1c8f-4b43-8bf0-84f9d857a053
DEVICE=ens33
ONBOOT=yes
 
DNS1=114.114.114.144
ZONE=public
IPADDR=192.168.116.19
PREFIX=24
GATEWAY=192.168.116.2
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
```

（2）重启网络服务

service network restart
5.检验配置是否成功

（1）查看修改后的固定IP为192.168.2.2，配置正确；

> ifconfig

![这里写图片描述](https://img-blog.csdn.net/20170312221447718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

或者

> ip addr
>

（2）测试虚拟机中的CentOS 7系统是否能连外网，有数据返回，说明可以连接外网；

ping www.baidu.com


## 二、docker

**虚拟机**

![【Docker】容器、虚拟机与Docker概念全解析【Docker】容器、虚拟机与Docker概念全解析](https://gitee.com/top20chenql/md_imgs/raw/master/xunijiyuanli.png)

> 虚拟机在本质上就是在模拟一台真实的计算机设备，同时遵循同样的程序执行方式。虚拟机能够利用“虚拟机管理程序”运行在物理设备之上。反过来，虚拟机管理程序则可运行在主机设备或者“裸机”之上。
>
> 下面用更直白的表达来说明：
>
> 虚拟机管理程序可表现为软件、固件或者硬件，并作为虚拟机的运行基础。虚拟机管理程序本身运行在物理计算机之上，我们也将这种底层硬件称为“主机设备”。主机设备为虚拟机提供资源，包括内存与CPU。这些资源由不同虚拟机共享，并根据需要进行随意分配。因此如果一套虚拟机运行有需求大量资源的高强度应用程序，那么我们可以在同一主机设备上为其分配远高于其它虚拟机的资源配额。
>
> 运行在主机设备上的虚拟机（当然，需要配合虚拟机管理程序）通常被称为一套“客户机”。这套客户机容纳有应用程序及其运行所必需的各类组件（例如系统二进制文件及库）。它同时还包含有完整的虚拟硬件堆栈，其中包括虚拟网络适配器、存储以及CPU——这意味着它也拥有自己的完整访客操作系统。着眼于内部，这套客户机自成体系并拥有专用资源。而从外部来看，这套虚拟机使用的则是由主机设备提供的共享资源。
>
> 如上所述，客户机可以运行主机虚拟机管理程序或者裸机虚拟机管理程序。二者之间存在着多种重要区别。
>
> 首先，主机虚拟机管理程序运行在主机设备的操作系统之上。举例来说，一台运行有OS X的计算机可以在操作系统之上安装虚拟机（例如VirtualBox或者VMware Workstation 8）。该虚拟机并不会直接访问硬件，因此其需要经由主机操作系统（也就是Mac OS X）实现资源获取。
>
> 主机虚拟机管理程序的优势在于，其基本摆脱了对底层硬件的要求。该主机操作系统负责提供硬件驱动程序，而非由虚拟机管理程序自身提供，因此我们认为其具备更理想的“硬件兼容性”。在另一方面，这种介于硬件与虚拟机管理程序之间的额外层会带来更多资源消耗，进而降低虚拟机性能表现。
>
> 裸机虚拟机管理程序则将虚拟机直接安装并运行在主机设备硬件之上以改善性能表现。由于其直接接入底层硬件，因此我们不再需要主机操作系统作为辅助。在这种情况下，我们可以直接在硬件上安装虚拟机管理程序并将其作为操作系统。与主机虚拟机管理程序不同，裸机虚拟机管理程序拥有自己的设备驱动程序及接口，从而直接支持各类I/O、处理或者操作系统特定任务相关组件。这种方式能够带来更理想的性能水平、可扩展性以及稳定性。但代价是其硬件兼容性比较有限，因为虚拟机管理程序只能包含一部分设备驱动程序。
>
> 说到这里，大家可能提出疑问：为什么我们非得在虚拟机与主机设备之间添加“虚拟机管理程序”呢？
>
> 这个嘛，因为虚拟机本身拥有一套虚拟操作系统，而虚拟机管理程序则负责为虚拟机提供平台以管理并运行这套访客操作系统。如此一来，主机计算机就能够为运行于其上的各虚拟机分配共享资源了。

使用虚拟机运行多个相互隔离的应用时，如下图:

![图0：[外文翻译]虚拟机与Docker有何不同？](http://www.techug.com/wordpress/wp-content/uploads/2017/05/vm.jpg)

#### 从下到上理解上图:

- 基础设施(Infrastructure)。它可以是你的个人电脑，数据中心的服务器，或者是云主机。
- 主操作系统(Host Operating System)。你的个人电脑之上，运行的可能是MacOS，Windows或者某个Linux发行版。
- 虚拟机管理系统(Hypervisor)。利用Hypervisor，可以在主操作系统之上运行多个不同的从操作系统。类型1的Hypervisor有支持MacOS的HyperKit，支持Windows的Hyper-V以及支持Linux的KVM。类型2的Hypervisor有VirtualBox和VMWare。
- 从操作系统(Guest Operating System)。假设你需要运行3个相互隔离的应用，则需要使用Hypervisor启动3个从操作系统，也就是3个虚拟机。这些虚拟机都非常大，也许有700MB，这就意味着它们将占用2.1GB的磁盘空间。更糟糕的是，它们还会消耗很多CPU和内存。
- 各种依赖。每一个从操作系统都需要安装许多依赖。如果你的的应用需要连接PostgreSQL的话，则需要安装libpq-dev；如果你使用Ruby的话，应该需要安装gems；如果使用其他编程语言，比如Python或者Node.js，都会需要安装对应的依赖库。
- 应用。安装依赖之后，就可以在各个从操作系统分别运行应用了，这样各个应用就是相互隔离的。



**Docker的功能定位**

> Docker为基于Linux容器的开源项目，其利用Linux内核中的各项功能——例如命名空间与控制组——以在操作系统之上创建容器。
>
> 容器概念并不是什么新鲜事物; 谷歌公司多年来一直在使用自己开发的容器技术。其它Linux容器技术方案还包括Solaris Zones、BSD jails以及LXC，且其都已经拥有多年的发展历史。
>
> 那么为什么Docker的出现会快速吸引到技术业界的注意？
>
> - 易用性: Docker能够为潜在受众带来出色的易用性——开发者、系统管理员以及架构师等等——从而帮助其充分利用容器技术优势以快速构建并测试可移植应用程序。每个人都可以在自己的笔记本上打包应用程序，并将其直接运行在任何公有云、私有云甚至是裸机之上。其座右铭是：一次构建，随处运行。
> - 速度: Docker容器具备轻量化与高速特性。由于容器本身属于运行在内核之上的沙箱环境，因为其对资源的需求量极低。大家可以在数秒钟内完成容器的创建与运行，而虚拟机则由于需要引导完整的虚拟操作系统而耗费更多时间。
> - Docker Hub: Docker用户还能够享受由Docker Hub带来的丰富生态系统支持，我们可以将其理解成“Docker镜像的应用商店”。Docker Hub提供成千上万由社区开发的公共镜像，且可立即加以使用。我们可以轻松根据需要搜索到合适的镜像，将其提取并稍加修改即加以使用。
> - 模块性与可扩展性: Docker允许我们轻松将应用程序的功能拆分成多个独立容器。举例来说，我们可以将自己的Postgres数据库运行在一套容器当中，并将Redis服务器运行在另一容器内，而Node.js也拥有自己的容器系统。在Docker的帮助上，大家能够轻松将这些容器对接起来以创建完整的应用程序，这就让未来的规模伸缩或者组件更新得以通过相互独立的方式完成。

使用Docker容器运行多个相互隔离的应用时，如下图:

![图1：[外文翻译]虚拟机与Docker有何不同？](http://www.techug.com/wordpress/wp-content/uploads/2017/05/docker.jpg)

#### 从下到上理解上图:

- 基础设施(Infrastructure)。
- 主操作系统(Host Operating System)。所有主流的Linux发行版都可以运行Docker。对于MacOS和Windows，也有一些办法”运行”Docker。
- Docker守护进程(Docker Daemon)。Docker守护进程取代了Hypervisor，它是运行在操作系统之上的后台进程，负责管理Docker容器。
- 各种依赖。对于Docker，应用的所有依赖都打包在Docker镜像中，Docker容器是基于Docker镜像创建的。
- 应用。应用的源代码与它的依赖都打包在Docker镜像中，不同的应用需要不同的Docker镜像。不同的应用运行在不同的Docker容器中，它们是相互隔离的。

#### 对比虚拟机与Docker

> Docker守护进程可以直接与主操作系统进行通信，为各个Docker容器分配资源；它还可以将容器与主操作系统隔离，并将各个容器互相隔离。虚拟机启动需要数分钟，而Docker容器可以在数毫秒内启动。由于没有臃肿的从操作系统，Docker可以节省大量的磁盘空间以及其他系统资源。
>
> 说了这么多Docker的优势，大家也没有必要完全否定虚拟机技术，因为两者有不同的使用场景。虚拟机更擅长于彻底隔离整个运行环境。例如，云服务提供商通常采用虚拟机技术隔离不同的用户。而Docker通常用于隔离不同的应用，例如前端，后端以及数据库

大家需要注意，**Docker本身并不是容器**，它是创建容器的工具，是应用容器引擎。

想要搞懂Docker，其实看它的两句口号就行。

第一句，是“**Build, Ship and Run**”。

![img](https://img2018.cnblogs.com/blog/720430/201812/720430-20181226134440039-355719052.png)

也就是，“搭建、发送、运行”，三板斧。

举个例子：

我来到一片空地，想建个房子，于是我搬石头、砍木头、画图纸，一顿操作，终于把这个房子盖好了。

![img](https://img2018.cnblogs.com/blog/720430/201812/720430-20181226134448189-1614413776.png)

结果，我住了一段时间，想搬到另一片空地去。这时候，按以往的办法，我只能再次搬石头、砍木头、画图纸、盖房子。

但是，跑来一个老巫婆，教会我一种魔法。

这种魔法，可以把我盖好的房子复制一份，做成“镜像”，放在我的背包里。

![img](https://img2018.cnblogs.com/blog/720430/201812/720430-20181226134458048-1632935313.png)

等我到了另一片空地，就用这个“镜像”，复制一套房子，摆在那边，拎包入住。

![img](https://img2018.cnblogs.com/blog/720430/201812/720430-20181226134507695-502496013.png)

怎么样？是不是很神奇？

所以，Docker的第二句口号就是：“**Build once，Run anywhere（搭建一次，到处能用）**”。

Docker技术的三大核心概念，分别是：

- **镜像（Image）**
- **容器（Container）**
- **仓库（Repository）**

我刚才例子里面，那个放在包里的“镜像”，就是**Docker镜像**。而我的背包，就是**Docker仓库**。我在空地上，用魔法造好的房子，就是一个**Docker容器**。

说白了，这个Docker镜像，是一个特殊的文件系统。它除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（例如环境变量）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

也就是说，每次变出房子，房子是一样的，但生活用品之类的，都是不管的。谁住谁负责添置。

每一个镜像可以变出一种房子。那么，我可以有多个镜像呀！

也就是说，我盖了一个欧式别墅，生成了镜像。另一个哥们可能盖了一个中国四合院，也生成了镜像。还有哥们，盖了一个非洲茅草屋，也生成了镜像。。。

这么一来，我们可以交换镜像，你用我的，我用你的，岂不是很爽？

![img](https://img2018.cnblogs.com/blog/720430/201812/720430-20181226134517561-1517184540.png)

于是乎，就变成了一个大的公共仓库。

负责对Docker镜像进行管理的，是**Docker Registry服务**（类似仓库管理员）。

不是任何人建的任何镜像都是合法的。万一有人盖了一个有问题的房子呢？

所以，Docker Registry服务对镜像的管理是非常严格的。

最常使用的Registry公开服务，是官方的**Docker Hub**，这也是默认的Registry，并拥有大量的高质量的官方镜像。

好了，说完了Docker，我们再把目光转向K8S。

就在Docker容器技术被炒得热火朝天之时，大家发现，如果想要将Docker应用于具体的业务实现，是存在困难的——编排、管理和调度等各个方面，都不容易。于是，人们迫切需要一套管理系统，对Docker及容器进行更高级更灵活的管理。

就在这个时候，K8S出现了。

**K8S，就是基于容器的集群管理平台，它的全称，是kubernetes。**

### 安装部署

> Docker部署Springboot项目
>
> https://blog.csdn.net/weixin_39737132/article/details/109799923



#### 应用程序的准备

在Docker部署Springboot项目之前，首先需要将本地开发的项目打包(jar包或war包)，具体的打包可采用idea可视化工具的方式:(在package之前需要先clean)

![img](https://img-blog.csdnimg.cn/20201119084747586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTczNzEzMg==,size_16,color_FFFFFF,t_70)

plugins 中添加 Docker 构建插件

```xml
<build>
        <plugins>
            <!--Docker 插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <!-- Docker maven plugin -->
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>1.0.0</version>
                <configuration>
                    <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
                    <dockerDirectory>src/main/docker</dockerDirectory>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                </configuration>
           </plugin>
           <!-- Docker maven plugin -->
       </plugins>
</build>
```

在 src/main/docker 下创建 Dockerfile 文件（无后缀名），地址即为插件中的 <dockerDirectory>

![img](https://www.icode9.com/i/l/?n=20&i=blog/1520534/202012/1520534-20201231140419371-451561618.png)





#### 编写Dockerfile文件

![img](https://www.icode9.com/i/l/?n=20&i=blog/1520534/202012/1520534-20201231140930581-619040426.png)

Dockerfile是一个用来构建镜像的文本文件，里面包含了构建镜像的指令和说明；

(可直接在三步中创建的Dockerfile文件夹中用vi编辑文件)

```visual basic
FROM openjdk:8-jdk-alpine
VOLUME /tmp
COPY *.jar myapp.jar
ENTRYPOINT ["java","-jar","/myapp.jar"]
```

释：FROM:表示基础镜像，即运行环境；

```json
   VOLUME： 一个特别指定的目录，用于存储数据；

   COPY ：拷贝文件并且重命名；

   ENTRYPOINT ：容器启动时运行的命令，相当于我们在命令行中输入java -jar xxxx.jar；


VOLUME 指定了临时文件目录为/tmp。其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp。改步骤是可选的，如果涉及到文件系统的应用就很有必要了。/tmp目录用来持久化到 Docker 数据文件夹，因为 Spring Boot 使用的内嵌 Tomcat 容器默认使用/tmp作为工作目录
项目的 jar 文件作为 “app.jar” 添加到容器的
ENTRYPOINT 执行项目 app.jar。为了缩短 Tomcat 启动时间，添加一个系统属性指向 “/dev/./urandom” 作为 Entropy Source

如果是第一次打包，它会自动下载java 8的镜像作为基础镜像，以后再制作镜像的时候就不会再下载了。
```
#### 上传文件到服务器(阿里云)

将 jar 包和 Dockerfile 上传到服务器的同一个文件夹下

![img](https://www.icode9.com/i/l/?n=20&i=blog/1520534/202012/1520534-20201231142140218-88168418.png)

在linux服务器上创建文件夹Dockerfile

```json
mkdir Dockerfile
```

将打成的jar包和Dockerfile文件上传到Dockerfile文件夹下；

#### 构建image镜像

```c++
docker build -t myapp .
```

查看镜像，如下图

```json
docker images
```

![img](https://img-blog.csdnimg.cn/20201119090612355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTczNzEzMg==,size_16,color_FFFFFF,t_70)

#### 运行镜像

```c
docker run --name myapp -d -p 8080:8080 myapp
```

查看正在运行的容器如下：

```Delphi
docker ps
```

![img](https://img-blog.csdnimg.cn/20201119090827836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTczNzEzMg==,size_16,color_FFFFFF,t_70)

#### 访问应用程序

