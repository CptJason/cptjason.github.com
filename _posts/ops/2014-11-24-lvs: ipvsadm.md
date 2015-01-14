---
layout: post
title: "lvs: ipvsadm"
modified:
categories: ops
excerpt:
tags: []
image:
  feature:
date: 2014-11-24T17:55:02+08:00
---

# General查看test

命令 | 选项 | 选项解析
:-|:-|:-
ipvsadm | --version | 查看ipvsadm版本号
ipvsadm | -Ln | 查看当前虚拟服务器
ipvsadm | -Lnc | 查看当前所有连接
ipvsadm | | 查看ipvs版本号及hash表大小
cat | /proc/net/ip_vs_conn | 查看当前所有连接
ipvsadm | -lcn / grep x.x.x.x | 查看请求x.x.x.x的连接
ipvsadm | -l --rate | 查看包传递速率的近似精确值
ipvsadm | -l --stats | 查看虚拟服务和RealServer上当前的连接数、数据包数和字节数的统计值

# 保存&还原

命令 | 作用
---|---
ipvsadm-sav > ipvsadm.sav | #保存
ipvsadm-sav < ipvsadm.sav | #还原

# Help
对ipvsadm 的命令参考，并根据自己使用的经验，进行了一个简单的翻译，希望对ipvsadm 的使用者有一定的帮助。
为了更好的让大家理解这份命令手册，将手册里面用到的几个术语先简单的介绍一下：

术语 | 含义
:--|:--
virtual-service-address | 虚拟服务器的ip 地址
real-service-address | 真实服务器的ip 地址
scheduler | 调度方法

###### ipvsadm 的用法和格式如下：

命令 | 选项 |选项简称 | 选项解析
:--|:--|:--|:--
ipvsadm | --add-service | -A <br> -t/u/f virutal-service-address:port <br> [-s scheduler] <br> [-p[timeout]] <br> [-M netmask] | 在内核的虚拟服务器表中添加一条新的虚拟服务器记录。也就是增加一台新的虚拟服务器。|
ipvsadm | --edit-service | -E <br> -t/u/f virutal-service-address:port <br> [-s scheduler] <br> [-p[timeout]] <br> [-M netmask] | 编辑内核虚拟服务器表中的一条虚拟服务器记录。
ipvsadm | --delete-service | -D <br> -t/u/f virtual-service-address:port | 删除内核虚拟服务器表中的一条虚拟服务器记录。
ipvsadm | --clear | -C | 清除内核虚拟服务器表中的所有记录。
ipvsadm | --restore | -R | 恢复虚拟服务器规则。
ipvsadm | --save | -S [-n] | 保存虚拟服务器规则，输出为-R 选项可读的格式。
ipvsadm | --add-server | -a/e <br> -t/u/f service-address:port <br> -r real-server-address:port <br> [-g/i/m] <br> [-w weight] | 在内核虚拟服务器表的一条记录里添加一条新的真实服务器记录。
ipvsadm | --delete-server | -d <br> -t/u/f service-address <br> -r server-address | 删除一条虚拟服务器记录中的某条真实服务器记录
ipvsadm | --list | -L/l [options] | 显示内核虚拟服务器表
ipvsadm | --zero | -Z [-t/u/f service-address] | 虚拟服务表计数器清零（清空当前的连接数量等）
ipvsadm | --set | | tcp tcpfin udp | 设置连接超时值
ipvsadm | --start-daemon state <br> [--mcast-interface interface] | | 启动同步守护进程。他后面可以是master 或backup，用来说明LVS Router 是master 或是backup。在这个功能上也可以采用keepalived 的
ipvsadm | --stop-daemon | | 停止同步守护进程
ipvsadm | --help | -h | 显示帮助信息
11111111|11111111111111111111111|11111111111111111111111


###### 命令选项解释
有两种命令选项格式，长的和短的，具有相同的意思。在实际使用时，两种都可
以。

选项简称 |选项全称 | 选项解析
:--|:--|:--
 -A | --add-service | 在内核的虚拟服务器表中添加一条新的虚拟服务器记录。也就是增加一台新的虚拟服务器。
 -E | --edit-service | 编辑内核虚拟服务器表中的一条虚拟服务器记录。
 -D | --delete-service | 删除内核虚拟服务器表中的一条虚拟服务器记录。
 -C | --clear | 清除内核虚拟服务器表中的所有记录。
 -R | --restore | 恢复虚拟服务器规则
 -S | --save | 保存虚拟服务器规则，输出为-R 选项可读的格式
 -a | --add-server | 在内核虚拟服务器表的一条记录里添加一条新的真实服务器记录。也就是在一个虚拟服务器中增加一台新的真实服务器
 -e | --edit-server | 编辑一条虚拟服务器记录中的某条真实服务器记录
 -d | --delete-server | 删除一条虚拟服务器记录中的某条真实服务器记录
 -L<br>-l | --list | 显示内核虚拟服务器表
 -Z | --zero | 虚拟服务表计数器清零（清空当前的连接数量等）
 -| --set tcp tcpfin udp | 设置连接超时值
 | --start-daemon | 启动同步守护进程。他后面可以是master 或backup，用来说明LVS Router 是master 或是backup。在这个功能上也可以采用keepalived 的VRRP 功能。
 -| --stop-daemon | 停止同步守护进程
 -h | --help |显示帮助信息
 11111111|111111111111111111111|11111111111111111111111
 



######其他的选项

选项简称 |选项全称 | 选项解析
:--|:--|:--
-t | --tcp-service service-address | 说明虚拟服务器提供的是tcp 的服务 [vip:port] or [real-server-ip:port]
-u | --udp-service service-address | 说明虚拟服务器提供的是udp 的服务 [vip:port] or [real-server-ip:port]
-f | --fwmark-service fwmark | 说明是经过iptables 标记过的服务类型。
-s | --scheduler scheduler | 使用的调度算法，有这样几个选项: rr/wrr/lc/wlc/lblc/lblcr/dh/sh/sed/nq，默认的调度算法是： wlc.
-p | --persistent [timeout] | 持久稳固的服务。这个选项的意思是来自同一个客户的多次请求，将被同一台真实的服务器处理。timeout 的默认值为300 秒。
-M | --netmask netmask persistent granularity mask
-r | --real-server server-address | 真实的服务器[Real-Server:port]
-g | --gatewaying | 指定LVS 的工作模式为直接路由模式（也是LVS 默认的模式）
-i | --ipip | 指定LVS 的工作模式为隧道模式
-m | --masquerading | 指定LVS 的工作模式为NAT 模式
-w | --weight weight | 真实服务器的权值
- | --mcast-interface interface | 指定组播的同步接口
-c | --connection | 显示LVS 目前的连接 如：ipvsadm -L -c
- | --timeout 显示tcp tcpfin udp 的timeout 值 如：ipvsadm -L --timeout
- | --daemon | 显示同步守护进程状态
- | --stats | 显示统计信息
- | --rate | 显示速率信息
- | --sort | 对虚拟服务器和真实服务器排序输出
-n | --numeric | 输出IP 地址和端口的数字形式
11111111|1111111111111111|11111111111111111111111


###### ipvsadm – 调度算法

选项简称 |选项全称 
:--|:--
ipvsadm -A -t 192.168.1.90:80 -s wlc -p 120  | #在内核的虚拟服务器表中添加一条新的虚拟服务器记录。<br> #该服务由协议、IP地址和端口号组成编辑内核虚拟服务器表中的一条虚拟服务器记录<br>#-t表tcp -u表udp -s指定调度算法 -p指定常连接时间
ipvsadm -A -t 192.168.1.90:80 -swlc -p 120 | -A  增加一个虚拟服务，该服务由协议、IP地址和端口号组成。<br> 例如：<br> -A -t 202.99.59.110:80(增加一格虚拟服务，<br> 其协议(-t表示tcp，-u表示udp)为TCP、<br> IP为202.99.59.110、<br> 端口号为80。 <br >-s  指定服务采用的算法，常用的算法参数如下：


算法 | 名称 |简称 | 算法解析
:--|:--|:--|:--
rr轮叫 | Round Robin | rr |调度器通过“轮叫”调度算法将外部请求按顺序轮流分配到集群中的真实服务器上，它均等地对待每一台服务器，而不管服务器上实际的连接数和系统负载。
wrr加权轮叫 | Weighted Round Robin | wrr | 调度器通过“加权轮叫”调度算法根据真实服务器的不同处理能力来调度访问请求。这样可以保证处理能力强的服务器处理更多的访问流量。调度器可以自动问询真实服务器的负载情况，并动态地调整其权值。
lc最少链接 | Least Connections | lc | 调度器通过“最少连接”调度算法动态地将网络请求调度到已建立的链接数最少的服务器上。如果集群系统的真实服务器具有相近的系统性能，采用“最小连接”调度算法可以较好地均衡负载。
wlc加权最少链接 | Weighted Least Connections| wlc | 在集群系统中的服务器性能差异较大的情况下，调度器采用“加权最少链接”调度算法优化负载均衡性能，具有较高权值的服务器将承受较大比例的活动连接负载。调度器可以自动问询真实服务器的负载情况，并动态地调整其权值。
lblc基于局部性的最少链接 | Locality-Based Least Connections | lblc | “基于局部性的最少链接”调度算法是针对目标IP地址的负载均衡，目前主要用于Cache集群系统。该算法根据请求的目标IP地址找出该目标IP地址最近使用的服务器，若该服务器是可用的且没有超载，将请求发送到该服务器；若服务器不存在，或者该服务器超载且有服务器处于一半的工作负载，则用”最少链接”的原则选出一个可用的服务器，将请求发送到该服务器。
lblcr带复制的基于局部性最少链接 | Locality-Based Least Connections with Replication | lblcr |“带复制的基于局部性最少链接”调度算法也是针对目标IP地址的负载均衡，目前主要用于Cache集群系统。它与LBLC算法的不同之处是它要维护从一个目标IP地址到一组服务器的映射，而LBLC算法维护从一个目标IP地址到一台服务器的映射。该算法根据请求的目标IP地址找出该目标IP地址对应的服务器

    为了更好的让大家理解这份命令手册，将手册里面用到的几个术语先简单的介绍一下：1，virtual-service-address：是指虚拟服务器的ip 地址2，
real-service-address：是指真实服务器的ip 地址3，scheduler：调度方法

    ipvsadm 的用法和格式如下：ipvsadm -A|E -t|u|f virutal-service-address：port [-s scheduler] [-p [timeout]] [-M netmask] ipvsadm
 -D -t|u|f virtual-service-address ipvsadm -C ipvsadm -R ipvsadm -S [-n] ipvsadm -a|e -t|u|f service-address：
port -r real-server-address：port [-g|i|m] [-w weight] ipvsadm -d -t|u|f service-address -r server-address ipvsadm -L|l [options]
 ipvsadm -Z [-t|u|f service-address] ipvsadm --set tcp tcpfin udp ipvsadm --start-daemon state [--mcast-interface interface] 
ipvsadm --stop-daemon ipvsadm -h命令选项解释：有两种命令选项格式，长的和短的，具有相同的意思。在实际使用时，两种都可以。

    -A --add-service 在内核的虚拟服务器表中添加一条新的虚拟服务器记录。也就是增加一台新的虚拟服务器。
    -E --edit-service 编辑内核虚拟服务器表中的一条虚拟服务器记录。
    -D --delete-service 删除内核虚拟服务器表中的一条虚拟服务器记录。
    -C --clear 清除内核虚拟服务器表中的所有记录。
    -R --restore 恢复虚拟服务器规则
    -S --save 保存虚拟服务器规则，输出为-R 选项可读的格式
    -a --add-server 在内核虚拟服务器表的一条记录里添加一条新的真实服务器记录。也就是在一个虚拟服务器中增加一台新的真实服务器
    -e --edit-server 编辑一条虚拟服务器记录中的某条真实服务器记录
    -d --delete-server 删除一条虚拟服务器记录中的某 条真实服务器记录
    -L|-l --list 显示内核虚拟服务器表-Z --zero 虚拟服务表计数器清零（清空当前的连接数量等）
    --set tcp tcpfin udp 设置连接超时值--start-daemon 启动同步守护进程。他后面可以是master 或backup，用来说明LVS Router 是master 或是backup.在这个功能上也可以采用keepalived 的VRRP 功能。
    --stop-daemon 停止同步守护进程
    -h --help 显示帮助信息其他的选项：
    -t --tcp-service service-address 说明虚拟服务器提供的是tcp 的服务[vip：port] or [real-server-ip：port] 
    -u --udp-service service-address 说明虚拟服务器提供的是udp 的服务[vip：port] or [real-server-ip：port] 
    -f --fwmark-service fwmark 说明是经过iptables 标记过的服务类型。
    -s --scheduler scheduler 使用的调度算法，有这样几个选项rr|wrr|lc|wlc|lblc|lblcr|dh|sh|sed|nq，默认的调度算法是：wlc. 
    -p --persistent [timeout] 持久稳固的服务。这个选项的意思是来自同一个客户的多次请求，将被同一台真实的服务器处理。 timeout 的默认值为300 秒。
    -M --netmask netmask persistent granularity mask -r --real-server server-address 真实的服务器[Real-Server：port]
    -g --gatewaying 指定LVS 的工作模式为直接路由模式（也是LVS 默认的模式）
    -i --ipip 指定LVS 的工作模式为隧道模式
    -m --masquerading 指定LVS 的工作模式为NAT 模式
    -w --weight weight 真实服务器的权值
    --mcast-interface interface 指定组播的同步接口
    -c --connection 显示LVS 目前的连接 如 ：ipvsadm -L -c --timeout 
        显示tcp tcpfin udp 的timeout 值 如：ipvsadm -L --timeout --daemon 显示同步守护进程状态--stats 显示统计信息--rate 显示速率信息--sort 对虚拟服务器和真实服务器排序
        输出--numeric -n 输出IP 地址和端口的数字形式


Scheduler
LVS 负载均衡的八种调度算法

A、rr：轮循调度(Round-Robin) 它将请求依次分配不同的RS，也就是在RS中均摊请求。
           这种算法简单，但是只适合于RS处理性能相差不大的情况。
B、wrr：加权轮循调度(Weighted Round-Robin) 它将依据不同RS的权值分配任务。
              权值较高的RS将优先获得任务，并且分配到的连接数将比权值较低的RS更多。相同权值的RS得到相同数目的连接数。
C、dh：目的地址哈希调度 (Destination Hashing) 以目的地址为关键字查找一个静态hash表来获得需要的RS。
D、sh：源地址哈希调度(Source Hashing) 以源地址为关键字查找一个静态hash表来获得需要的RS。
E、Lc：最小连接数调度(Least-Connection) IPVS表存储了所有的活动的连接。把新的连接请求发送到当前连接数最小的RS。
F、Wlc：加权最小连接数调度(Weighted Least-Connection) 假设各台RS的权值依次为Wi（I = 1..n），当前的TCP连接数依次为Ti（I＝1..n）
               ，依次选取Ti/Wi为最小的RS作为下一个分配的RS。
G、Lblc：基于地址的最小连接数调度(Locality-Based Least-Connection) 将来自同一目的地址的请求分配给同一台RS如果这台服务器尚未满负荷，
                否则分配给连接数最小的RS，并以它为下一次分配的首先考虑。
H、Lblcr：基于地址的带重复最小连接数调度(Locality-Based Least-Connection with Replication) 对于某一目的地址，对应有一个RS子集。
                 对此地址的请求，为它分配子集中连接数最小的RS；如果子集中所有的服务器均已满负荷，则从集群中选择一个连接数较小的服务器，
                 将它加入到此子集并分配连接；若一定时间内，这个子集未被做任何修改，则将子集中负载最大的节点从子集删除。
