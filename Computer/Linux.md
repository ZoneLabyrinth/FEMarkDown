**目录**

![img](C:/Users/Administrator/AppData/Local/YNote/data/zlhui1111@163.com/fc94dba254a147bda00813c8b57b9dba/clipboard.png)

**帮助命令**

man



**复制**

cp -v file* /usr

匹配多位

-v覆盖

cp file? /usr

复制file后匹配一位



**查看信息**

cat 

head -() 头10行 -后加数字

tail -() 尾十行

wc 统计文件内容信息 显示行数等



**打包压缩**

备份etc

​           备份名			 源文件名

tar cf /tmp/etc-backup.tar /etc

解压缩                                重命名

tar xf /tmp/etc-backup.tar -C /root

**Vim**

hjkl 移动



yy 复制  3yy 复制3行

y$ 复制当前光标到结尾

p粘贴

dd 剪切

d$ 剪切到结尾

u 撤销

ctrl + R 取消撤销

x 单个字符删除

r 单个字符替换

:set nu 显示行

g 移动第一行

G 100 移动到100行

^ 移动到行首

$ 移动到行尾

**命令模式**

:s 替换

%s/x/X/g  全局替换

3,5s/x/X 第三第五行替换

**修改配置文件**

vim /etc/vimrc



vim可视模式

shift+i 多行插入

ctrl+v 块级选中 +d 删除



**用户权限管理**



- useradd 添加用户
- userdel 删除用户
- passwd 修改用户密码
- usermod 修改用户属性
- chage 修改用户属性

用户文件存在 `/home/`

列出用户所有信息 `ls -a /home/root/`

用户记录在 `/etc/passwd`