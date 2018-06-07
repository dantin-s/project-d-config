---
title: linux 
date: 2018-06-06
updated: 2018-06-06
tags: linux
categories: 
- Linux
- Shell
---

## 系统管理

### 软件包安装

#### YUM

>   yum 基于 rpm 包管理，能从指定源下载 rpm 包并安装，自动处理依赖

1.  基本配置

```shell
# 软件源来源： /etc/yum.repos.d/*.repo 文件指定
#   配置文件： /etc/yum.conf 

# 代理设置： [/etc/yum.conf]
proxy=http://10.57.44.162:3132

# 安装配置工具
yum install -y yum-utils \
               device-mapper-persistent-data \
               lvm2 \
               yum-config-manager
```



2.  常用命令

```shell
# 添加第三方源(也可以手动下载替换到 /etc/yum.repos.d/ 目录下)
yum-config-manager --add-repo http://mirrors.aliyun.com/repo/Centos-7.repo

yum list                      	# 列出已安装软件
yum list 
yum check-update              	# 检查可升级的软件
yum update                    	# 升级可升级软件
yum update vim					# 升级指定软件
yum remove vim					# 删除软件包

yum list docker-engine.x86_64  --showduplicates |sort -r   # 查看当前存在的软件包信息
```



3.  RPM 包管理

```shell
-i               	# install
-v					# verbose
-h					
-e					#
```



#### APT

1.  基本配置

```shell
# 软件源定义： /etc/apt/sources.list
# 配置文件：   /etc/apt/apt.conf.d/

apt-get update                	# 更新源索引
apt-get upgrade             	# 升级软件
apt-get dist-upgrade			# 升级操作系统及软件
apt-get install vim           	# 安装软件包
apt-get source vim            	# 下载源文件
apt-get remove vim				# 删除软件包
```



### 杂项命令

```shell
uname -a                        # 查看linux内核版本
lsb_release -a                  # 查看linux发行版本信息
```



### 高级运维软件

```shell
htop                 # top 的取代软件

```





## 2. 网络管理

### proxy

```shell
# ubuntu apt 代理设置： /etc/apt/apt.conf
Acquire::https::packages.cloud.google.com::Verify-Peer "false";

# curl 使用代理(- o)
curl --proxy "https:192.168.0.1:3132" -A "userAgent"
curl -o xx.xml -x https://127.0.0.1:3132 -k https://repo.maven.apache.org/maven2/com/google/guava/guava/maven-metadata.xml

# wget 使用代理
wget --no-check-certificate -c -r -np -k -L -p -e "https_proxy=https://10.57.44.162:3132" https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz

# 禁用 ipv6
root@hasaki ~/G/s/c/bin# vi /etc/sysctl.conf
root@hasaki ~/G/s/c/bin# sysctl -p /etc/sysctl.conf
net.ipv4.conf.all.arp_notify = 1
net.ipv4.conf.default.arp_notify = 1
net.ipv4.conf.docker0.arp_notify = 1
net.ipv4.conf.eth0.arp_notify = 1
net.ipv4.conf.lo.arp_notify = 1
net.ipv6.conf.all.disable_ipv6 = 1
```

### nc/netcat

>   用来设置路由

```shell
# 语法
nc/netcat [选项] [参数]

# 选项
-l：使用监听模式，监控传入的资料；
-n：直接使用ip地址，而不通过域名服务器；
-p：设置本地主机使用的通信端口；
-s：设置本地主机送出数据包的IP地址；
-u：使用UDP传输协议；
-v：显示指令执行过程；
-w：设置等待连线的时间；

# 示例
echo ruok | nc localhost 2181           # 判断zk是否正常启动，应该显示 imok

# 实战: 在 A 上执行 `nc -l 8080`, 在 B 上执行 curl 命令，可测试端口是否通
```



### netstat





## 3. 文件管理

### awk

>   文本处理神器，主要用于文本过滤以及信息统计

```shell
echo "some thing in here." | awk '{print $1,$2}'        # 输出第一 第二列
echo "some thing in here." | awk '$1=="some" ' 			# 匹配第一列的值

awk 'NR==1'
-F:   # 指定分隔符 :
# 参考：https://coolshell.cn/articles/9070.html
```



### Crontab 定时任务

> 格式说明见下图

![img](https://images0.cnblogs.com/blog/34483/201301/08090352-4e0aa3fe4f404b3491df384758229be1.png)



```shell
# 实例：http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html
```



### find

>   搜索文件

```shell
# 语法
find [目录] [选项] [搜索内容]

# 示例
find /home -name  "*.txt" 				# /home 目录下找名字匹配后缀为 txt 的文件
find /home -iname "*.txt" 				# /home 目录下找名字匹配后缀为 txt 的文件，忽略大小写
find /home -path  "*local*"				# /home 目录下找文件路径包含 local 的文件
find /home -regex '.*\(\.txt\|\.pdf\)$' # 使用正则
find /home ! -name "*.txt" 				# 否定

-maxdepth 3						# 目录深度最大 3
-mindepth 2						# 目录深度最小 2
-perm  777						# 文件权限 777
-user  root						# root 用户的文件
-group root
-type 类型参数					 # 根据文件类型来搜索
								# f 普通文件 
								# l 符号连接 
								# d 目录 
								# c 字符设备 
								# b 块设备 
								# s 套接字 
								# p Fifo

find . -type f -user root -exec chown tom {} \;  # 查找并修改
find . -name "*.txt" |xargs grep "content"       # 查档当前目录下的所有文件，看是否包含指定内容
find . |xargs grep "content"                     # 同上

# find 命令后使用 2>/dev/null 忽略错误信息
```



### grep

>   文本搜索神器，寻找匹配内容

```shell
# 选项
-c：统计查询的结果行数
-e：多个匹配时使用
-n：输出匹配行数
-i：忽略大小写
-r：递归
-l：list with match
-L：list without match

-v：不包含的字符串
-A：匹配行下面n行，After
-B：匹配行上面n行，Before
-C：匹配行上下n行
-E：正则匹配


# 实例
grep "abc" file                             # 从file中查找存在 abc 字符串的行，并打印出来
grep -v "abc" file                          # 从file中查找不包含 abc 字符串的行，并打印出来
grep -v "abc" file                          # 从file中查找不包含 abc 字符串的行，并打印出来
grep -rl "abc" /home/                       # 递归查找包含 abc 的文件列表
grep -rL "abc" /home/                       # 递归查找不包含 abc 的文件列表

# grep 命令后使用 2>/dev/null 忽略错误信息
```

### scp

>   远程拷贝文件的命令，底层基于ssh 协议，对于远程主机，若没有 sshd 服务，会报 ssh 链接错误：
>
>   **SSH**: connect to host 10.57.44.162 **port 22**: Connection refused
>
>   所以，对于 windows 和 linux 互传文件，可以使用 xsftp 或者 winscp 或者 git-bash 中自带的 scp 命令.

```shell
# 语法
scp [选项] [参数]

# [选项]
-1：使用ssh协议版本1
-2：使用ssh协议版本2
-4：使用ipv4
-6：使用ipv6
-B：以批处理模式运行
-C：使用压缩
-F：指定ssh配置文件
-l：指定宽带限制
-o：指定使用的ssh选项
-P：指定远程主机的端口号
-p：保留文件的最后修改时间，最后访问时间和权限模式
-q：不显示复制进度
-r：以递归方式复制(指定文件夹)

# [参数]
参数1：源文件
参数2：目标文件

# 示例
# 1.从远处复制文件到本地目录，若是上传文件，则将后面两个参数顺序互换
scp root@10.10.10.10:/opt/soft/nginx-0.5.38.tar.gz /tmp

# 2.从远处复制文件夹到本地目录，若是上传文件夹，则将后面两个参数顺序互换
scp -r root@10.10.10.10:/opt/soft/ /tmp
```

### sed

>   文本处理神器(流编辑器，逐行处理)，主要用于文本替换操作

```shell
# 语法
sed [options] 'command' files       # 正常使用场景
sed [options] -f scriptfile files   # 使用封装的处理脚本

# [选项]
-e <script>                         # 使用 script 中的命令来处理文本 --expression=<script>
-f <script文件>                     # 使用 文件 中包含的 命令来处理   --file=<script文件>   
-n                                  # 不打印修改后的完整内容到屏幕，跟p一起使用表示只打印修改行
-i                                  # 重定向到文件中
   
# 实例
# 替换操作 s
sed 's/book/books/'  file           # 结果：将替换后的内容打印到屏幕上，只替换每行第一个匹配值
sed 's/book/books/g' file           # 结果：将替换后的内容打印到屏幕上，替换每行所有匹配值
sed 's/book/books/2g' file          # 结果：将替换后的内容打印到屏幕上，替换每行第2个匹配值
sed 's/book/books/3g' file          # 结果：将替换后的内容打印到屏幕上，替换每行第3个匹配值
sed 's/book/books/p' file           # 结果：替换每行所有匹配值，并重复打印修改行
sed -n 's/book/books/p' file        # 结果：替换每行所有匹配值，只打印修改行
sed -n 's@book@books@p' file        # 结果：替换每行所有匹配值，只打印修改行，效果同上
sed -n 's/book//p' file             # 结果：替换每行所有匹配值为空白，只打印修改行
sed -n 's@book@@p' file             # 结果：替换每行所有匹配值为空白，只打印修改行，效果同上
sed -i 's/book/books/g' file        # 结果：不打印输出，替换每行所有匹配值，直接写到文件中

echo word1 word2|sed 's/\w\+/[&]/g' # 结果：使用正则 \w\+ 匹配每个单词，然后使用 [&] 表示将每个单词用 [] 包围，&表示每个匹配的单词
echo word1 word2|sed 's/\w\+/a&b/g' # 结果：使用正则 \w\+ 匹配每个单词，然后使用 a&b 表示将每个单词用 ab 包围，&表示每个匹配的单词

# 删除操作 d
sed '/^$/d' file                    # 结果：删除空白行
sed '2d' file                       # 结果：删除第2行
sed '2,$d' file                     # 结果：删除第2行到末尾
sed '$d' file                       # 结果：删除末尾行
sed '/^test/d' file                 # 结果：删除以test开头的行

# 查询操作 

# 其他操作
sed '3q' file                       # 结果：打印到第3行，然后退出
sed -e '1,5d' -e 's/t/ck/' file     # 结果：多点编辑，先删除1-5行，然后替换t为ck
sed '/^test/a\append' file          # 结果：使用 a\ 将 append 插入到 test 后面（分隔符可以空格）
sed '2a\append' file                # 结果：使用 a\ 将 append 插入到 第2行 后面
sed '/^test/i\append' file          # 结果：使用 a\ 将 append 插入到 test 之前
sed '2i\append' file                # 结果：使用 a\ 将 append 插入到 第2行 前面
sed '1~2p' file					    # 结果：从第1行开始，间隔两行打印，即打印基数行
sed '2~2p' file					    # 结果：从第2行开始，间隔两行打印，即打印偶数行
sed 'N;s/abc/a' file			    # 结果：将第1、2行都输入到缓冲区，然后执行一次命令，这样第2行
									#      即使有abc，只要第一行匹配到了，就不会匹配了
									
# 使用 / 作为分隔符可能存在异常报错的场景，变量里面存在 / ，被误用了。

# 高级用例：
# 截取文本段 {% schema %} 和 {% endschema %} 之间的内容
sed -n '/^{% schema %}/,/{% endschema %}$/p' index.liquid | grep -Ev '(^\{% schema %\}|\{% endschema %}$)'

# 删除 utf8 with bom
sed -i '1s/^\xEF\xBB\xBF//' xxx.txt
```

### zip/unzip

>   压缩/解压 zip 格式的压缩包

```shell
# 语法
unzip [选项] [参数]

# [选项]
-c：将解压缩的结果显示到屏幕上，并对字符做适当的转换
-f：更新现有的文件
-l：显示压缩文件内所包含的文件
-p：与-c参数类似，会将解压缩的结果显示到屏幕上，但不会执行任何的转换
-t：检查压缩文件是否正确
-u：与-f参数类似，但是除了更新现有的文件外，也会将压缩文件中的其他文件解压缩到目录中
-v：执行时显示详细的信息
-z：仅显示压缩文件的备注文字
-a：对文本文件进行必要的字符转换
-b：不要对文本文件进行字符转换
-C：压缩文件中的文件名称区分大小写
-j：不处理压缩文件中原有的目录路径
-L：将压缩文件中的全部文件名改为小写
-M：将输出结果送到more程序处理
-n：解压缩时不要覆盖原有的文件               [never overwrite]
-o：不必先询问用户，unzip执行后覆盖原有的文件 [overwrite]
-P<密码>：使用zip的密码选项
-q：执行时不显示任何信息
-s：将文件名中的空白字符转换为底线字符
-V：保留VMS的文件版本信息
-X：解压缩时同时回存文件原来的UID/GID
-d<目录>：指定文件解压缩后所要存储的目录      [dir]
-x<文件>：指定不要处理.zip压缩文件中的哪些文件
-Z：unzip -Z等于执行zipinfo指令。

# [参数]
待解压的文件

# 示例
# 1. 解压文件到 /tmp 目录，并覆盖/不覆盖已有文件
unzip [-o|-n] test.zip -d /tmp
```



## 4. SHELL 编程

### 常用技巧

```shell
# 脚本中获取当前目录

# 查找指定进程并杀掉
```



### 脚本调用(3种)

```shell
# exec
# 切换到新的脚本执行，继承上下文环境，PID 同原来的 Shell，原脚本内容不执行。示例：
exec /home/script.sh

# source
# 执行过程中的内部调用，相当于库函数导入，继承上下文环境，待新脚本执行完后，继续执行原来脚本(调用脚本修改的环境变量生效)。示例：
source /home/script.sh
. ./script.sh

# fork
# 复制一份去执行，继承上下文环境，执行完之后，继续调用父脚本(子脚本修改的环境变量不生效)。示例：
./script.sh
echo $?
```



### 重定向

>   Shell 命令执行时，默认有三个文件，分别表示标准输入、标准输出、错误输出，可以通过修改将输出重定向到其他文件中;

| 类型        | 符号表示 | 文件描述符 | 默认情况        | 对应文件位置              |
| --------- | ---- | ----- | ----------- | ------------------- |
| 标准输入      | <    | 0     | 从键盘获取       | /proc/slef/fd/0     |
| 标准输出      | >    | 1     | 输出到控制台      | /proc/slef/fd/1     |
| 错误输出      | >>   | 2     | 输出到控制台      | /proc/slef/fd/2     |
| 输入结束(EOF) | <<   |       | 常见于cat命令EOF | cat <<EOF > xxx.txt |
|           |      |       |             |                     |



|         命令          | 说明                      |
| :-----------------: | ----------------------- |
|  command >filename  | 把标准输出重定向到新文件中，若文件存在会先删除 |
| command 1>filename  | 同上                      |
| command >>filename  | 把标准输出追加到文件中，若文件存在不删除    |
| command 1>>filename | 同上                      |
| command 2>filename  | 把标准错误重定向到新文件中           |
| command 2>>filename | 把标准错误追加到新文件中            |

>   2>1   : 表示标准错误重定向到**文件1**中
>
>   2>&1: 表示标准错误重定向到**文件描述符1所对应的文件地址**，将 & 理解成取指针即可。
>
>   ​            因为是指向文件地址的，所以若这条语句先执行，再执行 >/dev/null ，1所指向的文件被重定向了，却不再修改 2 重定向的文件。
>
>   总结：将 1、2 看成指针变量，上诉过程是修改指针变量所执行的文件地址的过程



### 后台执行 &/nohup

```shell
# 在命令后接 & 即可在后台运行
bash xxx.sh &
[0] 28080

# nohub 表示 no handup 即让进程忽略 HUP 信号，即使终端断开连接也不会导致该进程中断；
# 可在最后加一个 &  表示后台执行
nohub bash xxx.sh &
```



## 5. Linux 系统参考资料

**文件目录说明**

![目录树架构](./img/directory_tree.gif)



## 6. 其他

### Server Sumary

| Server   | Port            | info                                     |
| -------- | --------------- | ---------------------------------------- |
| FTP      | 20 数据流 / 21 控制流 | 文件传输协议；明文传输                              |
| SSH/SFTP | 22              | 安全文件传输协议；                                |
| Telnet   | 23              | 远程登录控制；未加密                               |
| NFS      |                 | 网络文件系统；客户端通过 mount 挂载                    |
| VNC      |                 | 远程桌面登录，显示画面                              |
| Cron     |                 | 定时任务服务器                                  |
| Iperf    |                 | 带宽测试;[参考链接](http://blog.sina.com.cn/s/blog_49ab2ae20100cnig.html) |
| HTTP     | 80              |                                          |
| HTTPS    | 443             |                                          |



### VIM

[快捷键参考](https://ruimemo.wordpress.com/2010/03/31/vim-%E6%88%91%E4%BC%9A%E7%94%A8%E7%9A%84%E6%9C%80%E5%BC%BA%E5%A4%A7%E7%9A%84%E7%BC%96%E8%BE%91%E5%99%A8/)

-   **vimrc 配置**

```shell
set modelines=0
set shell=/bin/bash

" 通用设置，可拷贝
set backspace=2                " 设置更好的删除
syntax on                      " 语法高亮
autocmd InsertLeave * se nocul " 用浅色高亮当前行
autocmd InsertEnter * se cul
set smartindent                " 智能对齐
set autoindent                 " 自动对齐
set confirm                    " 在处理未保存或只读文件的时候，弹出确认框
set number                     " 显示行号
set hlsearch
set incsearch                  " 搜素高亮,搜索逐渐高亮
set cursorline                 " 突出显示当前行
set ignorecase                 " 搜索忽略大小写
filetype on                    " 打开文件类型检测功能
set mouse=c                    " 允许鼠标

" 统一配置
set tabstop=4                  " tab键的宽度
set softtabstop=4
set shiftwidth=4               " 统一缩进为4
set expandtab                  " 不要用空格替代制表符
set history=50                 " 历史纪录数
set gdefault                   " 行内替换
set encoding=utf-8
set fileencodings=utf-8,ucs-bom,shift-jis,gb18030,gbk,gb2312,cp936,utf-16,big5,euc-jp,latin1  " 编码设置

" 个性化配置
" colorscheme solarized8_dark_low "solarized8_dark_flat  molokai
set guifont=Menlo:h16:cANSI    " 设置字体
set langmenu=zn_CN.UTF-8
set helplang=cn                " 语言设置
set ruler                      " 在编辑过程中，在右下角显示光标位置的状态行
set laststatus=1               " 总是显示状态行
set showcmd                    " 在状态行显示目前所执行的命令，未完成的指令片段也会显示出来
set scrolloff=3                " 光标移动到buffer的顶部和底部时保持3行的距离
set showmatch                  " 高亮显示对应的括号
set matchtime=5                " 对应括号高亮时间(单位是十分之一秒)
set autowrite                  " 在切换buffer时自动保存当前文件
set wildmenu                   " 增强模式中的命令行自动完成操作
set linespace=2                " 字符间插入的像素行数目
set foldenable                 " 允许折叠
" set magic                      " magic (\m)：除了 $ . * ^ 之外其他元字符都要加反斜杠
                               " nomagic (\M)：除了 $ ^ 之外其他元字符都要加反斜杠

set t_Co=256                   " 256色
set whichwrap=b,s,<,>,[,]      " 开启normal或visual模式下的backspace键空格键，左右方向键,insert或replace模式下>的左方向键，右方向键的跳行功能

" 记录上次退出位置
au BufReadPost * if line("'\"") > 0|if line("'\"") <= line("$")|exe("norm '\"")|else|exe "norm $"|endif|endif

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'VundleVim/Vundle.vim'
"Plugin 'Valloric/YouCompleteMe'
Plugin 'godlygeek/tabular'
Plugin 'plasticboy/vim-markdown'

call vundle#end()
filetype plugin indent on
" 分为三部分命令:file on,file plugin on,file indent on 分别是自动识别文件类型,用文件类型脚本,使用缩进定义文件

" To ignore plugin indent changes, instead use:
" filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
```



### FISH

```shell
# 安装
apt-get install fish

# 配置问候语
set fish_greeting \n\n\e\[32mjust\ \e\[33mshow\ \e\[34mme\ \e\[00mthe\ \e\[36mcode.

# 配置文件位置 vi ~/.config/fish/config.fish
set PATH $PATH $GOPATH/bin

# 颜色设置
https://www.cnblogs.com/clover-toeic/p/4031618.html

#安装 fisherman
curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs git.io/fisher


curl -L https://get.oh-my.fish | fish

https://segmentfault.com/a/1190000009621753
```

#### bobthefish 主题配色设置

```shell
# omf install bobthefish
# 修改 ~/.local/share/omf/themes/bobthefish/functions/__bobthefish_colors.fish
# 替换内容如下, 效果如截图所示
# case '*' # default dark theme
@@ -441,8 +441,8 @@ function __bobthefish_colors -S -a color_scheme -d 'Define colors used by bobthe
-      set -x color_initial_segment_exit     $white $red[2] --bold
-      set -x color_initial_segment_su       $white $green[2] --bold
+      set -x color_initial_segment_exit     $black $red[2] --bold
+      set -x color_initial_segment_su       $black $grey

@@ -461,8 +461,8 @@ function __bobthefish_colors -S -a color_scheme -d 'Define colors used by bobthe
-      set -x color_username                 $grey[1] $blue[3] --bold
-      set -x color_hostname                 $grey[1] $blue[3]
+      set -x color_username                 $orange[1] black --bold
+      set -x color_hostname                 $orange[1] black --bold

# 设置 vi 为黑色
set -x color_vi_mode_insert           $black $grey[3]
```



![image-20180525142818312](img/image-20180525142818312.png)

### ZSH

```shell
# 插件
oh-my-zsh

git     # 内置
extract # 内置，一键解压
z       # 内置，目录快速跳转
sublime # 内置

# zsh-autosuggestions
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
plugins=(其他的插件 zsh-autosuggestions)

# zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
plugins=(其他的插件 zsh-syntax-highlighting)
```



### Tmux 配置

> 定义：终端复用软件
>
> 用途：分屏/保护现场/会话共享



### 环境初始化配置

```shell
cp ~/.bashrc ~/.bashrc.bak
echo 'alias ".."="cd .."              ' >> ~/.bashrc
echo 'alias "..."="cd ../.."          ' >> ~/.bashrc
echo 'alias "ll"="ls -lha"            ' >> ~/.bashrc
echo 'alias "l"="ll"                  ' >> ~/.bashrc
echo 'alias "kk"="ll"                 ' >> ~/.bashrc
echo 'alias "grep"="grep --color=auto"' >> ~/.bashrc
```



### linux 窗口大小不能自动适应

> 现象： 行列只有最初默认的 80列 24行，即 LINES/COLUMNS 变量指定为 24/80，超过的字符显示在同一行的头部，且vim窗口按80列24行从左上角布局。

**解决方案**

> 使用 `shopt -s checkwinsize` 控制每次命令之后自动检查窗口大小并调节，但是实际测试发现，只有在调整窗口大小才会触发，以 bash 初始进入之后，LINES/COLUMNS 还是以 24/80 为默认值，不稳定。
>
> 所以，最终采用 resize 的方式，`apt-get install xterm ` 安装 xterm 软件包，然后可以手动执行 `resize` 命令，使系统重新设置 LINES/COLUMNS 大小，达到重新调整窗口大小的目的。
>
> ```shell
> # resize 命令使用
> > $ resize
> COLUMNS=181;
> LINES=42;
> export COLUMNS LINES;
> ```
>
> 另外，为了不用手动调整，将 resize 集成到 PROMPT_COMMAND 变量中：
>
> ```shell
> export PROMPT_COMMAND="resize &>/dev/null;$PROMPT_COMMAND"
> ```



### 终端颜色配置

[参考](http://www.techug.com/post/shell-console-color-setting.html)

```shell
显示格式： \033[显示方式;前景色;背景色;动作m;

编码 颜色/动作
 0 重新设置属性到缺省设置
 1 设置粗体
 2 设置一半亮度（模拟彩色显示器的颜色）
 4 设置下划线（模拟彩色显示器的颜色）
 5 设置闪烁
 7 设置反向图象
22 设置一般密度
24 关闭下划线
25 关闭闪烁
27 关闭反向图象

30 设置黑色前景                  40 设置黑色背景
31 设置红色前景                  41 设置红色背景
32 设置绿色前景                  42 设置绿色背景
33 设置棕色前景                  43 设置棕色背景
34 设置蓝色前景                  44 设置蓝色背景
35 设置紫色前景                  45 设置紫色背景
36 设置青色前景                  46 设置青色背景
37 设置白色前景                  47 设置白色背景
38 设置下划线                     
39 关闭下划线                    49 设置缺省黑色背景 

\033[2J 　清除屏幕
\033[0q 　关闭所有的键盘指示灯
\033[1q 　设置“滚动锁定”指示灯 (Scroll Lock)
\033[2q 　设置“数值锁定”指示灯 (Num Lock)
\033[3q 　设置“大写锁定”指示灯 (Caps Lock)
\033[15:40H 把关闭移动到第15行，40列
\007 　　发蜂鸣生beep
```



### 文档收集

**主流 linux 系统软件包安装方式总结： [IBM RPM__DEB](!https://www.ibm.com/developerworks/cn/linux/l-cn-rpmdpkg/index.html)**

