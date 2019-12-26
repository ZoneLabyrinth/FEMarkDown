### 文本查看

`cat`	 文本内容显示到终端

`head`   查看开头

`tail` -f 追踪   查看文件结尾

`wc`  统计文件内容信息

`more less`  space更多

### 打包压缩

`tar czf`   /路径  c 打包  x解压  f指定操作类型为文件 z压缩

### 用户管理

`passwd  username`    修改密码

`id  username`    0为root  

`userdel username` 删除用户   -r 删除用户下的文件夹

`usermod`  修改用户属性

- -d 修改用户根目录
- 

`chage` 更改用户密码过期时间

用户组

`groupadd groupname` 添加用户组 

`usermod -g gruopname username`  添加username到groupname

`useradd -g gruopname username` 新建gruopname的user

### sudo和su

`su` 切换用户

- ` su - username` 使用login shell方式切换用户

`sudo`  一其他用户身份执行命令

- visudo 设置需要使用sudo的用户（组）

### 修改权限

- `chmod` 修改文件/目录权限
  - chmod u+x /tmp/file   （u-x u=x)
  - chmod 755 /tmp/file
- `chown` 更改属主/属组 

### 网络管理

1. `net-tools` 
   - ifconfig
   - route
   - netstat
2. `iproute2`
   - ip
   - ss

**mii-tool eth0**   查看网卡物理连接情况

**route -n ** 查看网关路由  使用-n 参数不解析主机名

### 修改网络配置

#### 网络配置命令

- `ifconfig <接口> <IP地址> [netmask 子网掩码]`
- `ifup <接口>` 
- `ifdown <接口>`

#### 添加网关

- `route add default gw <网关ip>`
- `route add-host <指定IP>`
- `route add-net <指定网段>  netmask <子网掩码> gw <网关ip>`

#### IP命令

- `ip addr ls`
  - `ifconfig`
- `ip link set dev eth0 up`
  - `ifup eth0`
- `ip addr add 10.0.0.1/24 dev eth1`
  - `ifconfig eht1 10.0.0.1 net mask 255.255.255.0`
- `ip route add 10.0.0/24 via 192.168.0.1`
  - `route add-net 10.0.0.0 netmask 255.255.255.0 gw 192.168.0.1`

### 网络故障排除

- ping
- traceroute
- mtr
- nslookup
- telnet
- tcpdump
- netstat
- ss

### 网络服务管理

- service network start|stop|restart
- chkconfig -list network
- systemctl list-unit-files NetworkManager.service
- systemctrl start|stop|restart NetworkManager
- systemctrl enable|disable NetworkManager

配置文件：`/etc/systemconfig/network-scripts`

### 升级内核

- rpm格式内核
  - 查看内核版本
    - uname -r
  - 升级内核版本
    - yum isntall kernel-3.10.0
  - 升级软件包及补丁
    - yum update

- 安装依赖包
- 下载并解压内核
  - `https://www.kernel.org`
  - `tar xvf linux-5.1.10.tar.xz -C /usr/src/kernels`
- 配置内核编译参数
  - cd /usr/src/kernels/linux-5.1.10/
  - `make menuconfig| allyesconfig| allnoconfig`
- 使用当前系统内核配置
  - `cp /boot/config-kernelversion.platform /usr/src/kernels/linux-5.1.10/.config`
- 查看cpu
  - lscpu
- 编译
  - make -j2 all
- 安装内核
  - make modules_install
  - make install

### grub配置文件

- `/etc/default/grub`
- `/etc/grub.d`
- `/boot/grub2/grub.cfg`
- `grub2-mkconfig -o /boot/grub2/grub.cfg`

修改默认引导

```shell
grep ^menu /boot/grub2/grub.cfg

grub2-set-default 1
```

修改密码：

1. 进入页面按e 
2. 在linux16最后添加 rd.break
3. ctrl+x
4. mount -o remount,rw /sysroot
5. chroot /sysroot
6. echo password | passwd --stdin root
7. vim /etc/selinux/config
8. SELINUX=disabled
9. exit
10. reboot

### 进程管理

- 查看命令
  - `ps    (-ef| -axu)`
  - `pstree`
  - `top`
- 结论：
  - 

### 进程通信信号

kill

### 守护进程

- 使用`nohup`与&符号配合
  - `nohup`命令使进程忽略`hangup`信号
- 守护进程（daemon）和一般进程差别
- `screen`命令
  - screen进入screen环境
  - `ctrl + a` 退出（detached）screen环境
  - `screen -ls` 查看screen的会话
  - `screen -r sessionid` 恢复会话

### 服务管理工具

1. `service`
   -  `/etc/init.d/`
2. `systemctl`
   - `/usr/lib/systemd/system/`

### 内存磁盘管理

#### 内存

1. `free -m|g|t`
2. top 动态查看

#### 磁盘

1. `fdisk -l` / ls -l /dev/sd?
2. `parted -l`
3. `df -h`
4. `ls -lh  dirname` 
5. `du dirname` 

### 文件系统

- ext4
  - 超级块
  - 超级块副本
  - i节点（inode）
  - 数据块（datablock）
- xfs
- NTFS（需安装额外软件）



### 赋予权限

`setfacl -m u:user:rw | g: group` 赋予权限

`getfacl`

`setfacl -s` 回收权限

### 磁盘分区和挂载

- 常用命令
  - fdisk
  - mkfs
  - parted
  - mount
- 常见配置文件
  - /etc/fstab

### 系统综合状态查询

- `sar`查看系统综合状态
- 第三方命令查看网络流量
  - `yum install epel-release`
  - `yum install iftop`
  - `iftop -P`

### 服务

##### iptables表链

- 规则表
  - `filter nat mangle raw`
- 规则链
  - `INPUT OUTPUT FORWARD`
  - `PREROUTING POSTROUTING`

#### filter表

- `iptables -t filter` 命令 规则链 规则
  - 命令
    - -L
    - -A -l 
    - -D -F -P 
    - -N -X -E

```shell
iptables -t filter -A INPUT -s 10.0.0.1 -j ACCEPT
```

#### nat表

- `iptables -t nat` 命令 规则链 规则
  - `PREROUTING` 目的地址转换
  - `POSTROUTING` 源地址转换

配置文件

```shell
/etc/sysconfig/iptables
# centos 6
service iptables save|start| stop | restart
# centos 7
yum install iptables-services
```

#### firewallD服务

```shell
systemctl start |restart|stop  firewalld
# 所有端口
firewall-cmd --zone=public --list-ports
# zone
firewall-cmd --get-zones
 #开放443
firewall-cmd --add-service=https 
# 添加端口 --add-port   --permanent 永久开放
firewall-cmd --add-port=81/tcp --permanent 永久开放端口 
# 删除端口
firewall-cmd --remove-port=81/tcp --permanent 永久删除端口 需重启
# --permanent需重启
firewall-cmd --reload
# firewall-cmd --list-all
```

### SSH

```shell
# 配置文件
/etc/ssh/sshd_config 服务端 
/etc/ssh/ssh_config 客户端

17: port 登录端口

38 permitRootLogin root登录

systemctl restart sshd.service
```

#### 公钥认证

- `ssh-keygen -t rsa`
- `ssh-copy-id`    (`ssh-copy-id -i /root/.ssh/id_rsa.pub root@0.0.0.0`)

#### `SCP`

```shell
scp kpi.txt root@0.0.0.0:/tmp/ 拷贝到服务器
scp root@0.0.0.0:/tmp/  /codes    拷贝到本地
```

#### FTP

```shell
yum install vsftpd ftp
systemctl start vsftpd.service  
systemctl enable vsftpd.service 重启
# vsftpd 限制用户
# /etc/vsftpd/vsftpd.conf 配置文件
# /etc/vsftpd/ftpusers  记录不允许用户
# /ets/vsftpd/user_list 黑白名单
# SELINUX
getsebool -a
setsebool vsftpd -P 1
```

#### `vsftpd` 虚拟用户验证

- `guest_enable=YES`  支持虚拟用户
- `guest_username=vuser` 虚拟用户身份
- `user_config_dir=/etc/vsftpd/vuserconfig`  权限控制文件
- `allow_writeable_chroot=YES`  虚拟用户可写
- `pam_service_name=vsftpd.vuser` 验证方式

### NFS

### `Nginx`

#### `OpenResty`

```shell
yum install -y yum-utils
yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
yum install openresty
# 配置文件
/usr/local/openresty/nginx/conf/nginx.conf
service openresty start | stop | restart | reload
#nginx配置
/usr/local/openresty/nginx/sbin  #启动脚本
/usr/local/openresty/nginx/conf # 配置文件
/usr/local/openresty/nginx/logs # 日志
/usr/local/openresty/nginx/html # 静态html

```

#### 基于域名的虚拟主机`

```shell
server {
	listen 80;
	server_name www.tasseles.top;
	location / {
		root html/servera;
		index index.html index.htm
	}
}
# 配置
/etc/hosts
127.0.0.1  www.tassels.top www.desertcamel.cn

```