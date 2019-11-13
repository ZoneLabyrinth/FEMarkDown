Shell脚本

- 一条命令只做一件事
- 为了组合命令和多次执行，使用脚本文件来保存需要执行的命令
- 赋予该文件执行权限( `chnmod u+rx filename`)

### 管道

 a | b a命令输出作为b输入



### 重定向

- `<`输入重定向 
- `>(清空) >>（追加） 2>(错误输出) &>(全部输出)`  输出重定向

### 变量

- 赋值
- 结果赋值 $()或 ``
- 引用
  - ${variable}
- 作用范围
  - 当前shell,当前终端（bash xxx.sh 会重开子进程）
  - 使用source 或者 export 可以获取父级变量
  - unset 取消赋值

#### 系统环境变量

- 系统环境变量：每个shell打开都可以获得的变量
  - set和env (PS1)
  - $?(上条命令是否正确执行) $$(显示进程pid，脚本监测) $0(当前进程名称)
  - 位置变量（获取命令参数）
    - ${1} ${2} .....
    - `${}_ / ${2-_}`  若参数为空，则显示_ 
  - 配置文件
    - `/etc/profile`  ( /etc 通用配置文件，所有文虎)
    - `/etc/profile.d` （不同版本bash）
    - `~/.bash_profile` （~ 用户配置文件）
    - `~/.baserc`
    - `/etc/bashrc`

### 数组

- 定义数组
  - `array=( 1 2 3 4 )`
- 显示数组所有元素@
  - `echo ${array[@]}`
- 显示数组元素个数#
  - `echo ${#array[@]}`
- 显示数组的第一个元素 index
  - echo ${array[0]}

### 转义字符

- #注释
- ;分号
- \转移符号
- “ 和 '  引号 ‘完全输出  "输出变量

### 运算符

- 赋值运算符
- 算数运算符
  - `expr` 只能整数运算
- 数字常量
  - `let` "变量名=变量值"
  - 变量值使用0开头为八进制
  - 变量值使用0x开头为十六进制
- 双元括号 let简化
- `(( a= 10 ))`
- `(( a++ ))`
- `echo $(( 10+ 20 ))`
- `unset` 清除变量

### 特殊符号

#### 括号

- () (()) $()
  - 单独使用圆括号会产生一个子shell 父级无法读取` ( acb=123 )`
  - 数组初始化 `array=( 1 2 3 )`
- [] [[]]
  - 单独使用方括号是测试或数组元素功能
  - 两个方括号表示测试表达式
- <> 尖括号 重定向
- {} 
  - 输出范围` echo {0..9}`  无空格 
  - 文件复制  `cp /etc/passwd{,.bak}`
- 运算符号和逻辑符号
  - <>= &&||!

 

### 测试

- test命令用于命令用于检查文件或者比较值
- test可以做一下测试
  - 文件测试
  - 整数比较测试
  - 字符串测试
- test测试语句可以简化为 [] 符号
- []符号还有扩展写法[[]]支持 && 、|| <、>

### If-else/case

```shell
if [ condition ]
then 
	if [ condition ]
	then 
	fi
elif [ condition ]
then 
else 
fi

# case
case "$var" in 
	case1 )
		echo 
		;;
	case2 )
		cmd
		;;
		* )
		;;
esac
```

### 循环

```shell
for ... in ...
do
	echo
done
# C
for (( i=1; i<=10 ; i++ ))
do
	echo $i
done
# while
while [ condition ]
do
	echo 
done

#死循环
while :
do 
	echo
done
# 处理参数 
# $* $#所有参数


```

### 函数

```shell
function name(){
	echo $1
}
name 'fn'
# unset name 取消
# local 内部变量 相当于let
# 系统函数
/etc/init.d/functions

```

### 脚本资源控制

nice /renice

### 捕获信号(备份)

```shell
# 9信号不可阻塞
# 捕获 15信号 显示sig 15
trap "echo sig 15" 15

echo $$
```

### 一次性计划任务

- 一次性计划任务 at

  ```shell
  # 命令不存在
  yum install -y at
  Can't open /var/run/atd.pid to signal atd. No atd running?
  查看atd状态：
  
  [root@dev5297 ~]# /etc/init.d/atd status
  -bash: /etc/init.d/atd: No such file or directory
  
  发现没有这个文件。  修改atd服务的默认启动等级：
  [root@dev5297 ~]# chkconfig --level 35 atd on
  Note: Forwarding request to 'systemctl enable atd.service'.
  
  启动atd服务：
  [root@dev5297 ~]# service atd start
  Redirecting to /bin/systemctl start atd.service
  
  再次运行：
  
  [root@dev5297 ~]# echo "hello" | at now +3min
  job 2 at Thu Mar 14 10:02:00 2019
  ```

- 周期性计划任务 `cron`

  - 配置方式
    - `crontab -e`
  - 查看现有的计划任务
    - `crontab -l`
  - 配置格式：
    - 分钟 小时 日期 月份 星期 执行的命令
    - 注意命令的PATH

```shell
# tail -f /var/log/cron 中可以查看运行任务 

# 分 时 日 月 星期 
 * * * * * /usr/bin/date >> /tmp/date.txt #每分钟
 
 0 0 * * 1 /usr/bin/date >> /tmp/date.txt # 每周一

# 每个用户计划任务 /var/spool/cron 
```

### 计划任务枷锁

- 如果计算机不能按照预期时间运行
  - `anacontab` 延时计划任务
  - `flock` 锁文件

### 元字符

```shell
# . * $ [] ^ + | 
```

### find

```shell
find / -name nginx #精确查找文件
find / -regex .*nx #区分大小写正则
find / -iregex .etc.*wd$ #不区分大小写
find / -type f -regex .*wd #类型
find / -atime 8 # 时间   -ctime -mtime

```

### sed\awk 非交互式

- sed 一般用于对文本内容做替换
- AWK 一般用于对文本内容进行统计、按需要的格式进行输出

#### sed基本工作方式：

- 将文件以行为单位读取到内存（模式空间）
- 使用sed的每个脚本对改行进行操作
- 处理完成后输出该行

#### sed替换命令 s：

```shell
sed 's/old/new/' filename 
sed -e 's/old/new/' -e 's/old/new.' filename #多条命令 或用分号隔开
sed -i 's/old/new' filename #原样写回

全局替换   
/g 贪婪匹配  
标志位
/2 匹配第二次 
/p 二次输出  
sed -n 只输出替换成功行
寻址
sed '1s//'第一行
sed '1,4s/'1-4行
sed '(可以使用正则)s/'
分组
sed 脚本文件
sed -f sedscript filename
```

#### sed其他指令：

```shell
#删除 /d
sed '/content/d' /d 删除匹配的整行  模式空间不对源文件改变
#追加 /i
sed '/ad/i vertise' filename
#读取 /r
sed '/ab/r afile' bfile > afile 读取
# 写 /w
# 打印行号 /=
# 下一行 /n
# 打印 /p
# 退出 q
sed -n '10q' 1.txt  # 只读取10行就退出， 效率高
```

#### sed多行模式

```shell
# N 将下一行加入到模式空间 sed 'N;s/hel\nlo/!!!/' a.txt
# D 删除模式空间的第一个字符到第一个换行符 
# P 打印模式空间中的第一个字符到第一个换行符
```

#### sed的保持空间

保持空间野是多行的一种操作方式

将内容暂存在保存空间里，便于做多行操作

#### AWK

AWK更像脚本语言

AWK用于“比较规范”的文本处理，用于统计数量兵输出指定字段

使用sed将“不规范”的文本，转为“规范文本”

AWK脚本流程控制

- 输入数据前例程BEGIN{}
- 主输入循环{}
- 所有文件读取完成历程 END {}