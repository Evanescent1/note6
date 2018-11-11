# note6
&ensp;&ensp&ensp;&ensp;变量代表字符串在硬盘的对应空间
同一类型的变量归为一个变量名叫数组（多个同一类型的变量的集合）
把数组中每一个变量称为元素，通过下标表示其中一个元素（默认从零开始）
普通索引默认是用数字来表示（可以自定义）
关联索引是索引的值不是数字格式（bash4.0后支持）。bash --version查看版本
稀疏索引（索引不连续）
declare  -a  数组名   声明数组
declare  -A   数组名    关联索引（必须先声明，才能使用）
echo ${#title[*]}数组里的个数${}是为了让里面做为一个整体
echo ${title[*]}显示数组的所有元素
标准的数组的最大下标加1，就等于数组的个数
for i in `seq 0 $[${#title[@]}-1]`;do echo title is ${title[$i]};done利用循环打印数组里的元素
 $[${#title[@]}-1]数组数减1就等于下标，$[]是让里面做运算
for i   in `seq   ${#title[*]}`;do  ceho  title is ${title[$[i-1]]};done
如果不知道元素下标titile[${#title[*]}]=dev    用元素个数就可以做新的下标，原来的最大下标是元素个数减1
删除数组中其中一个元素unset  title[4]            删除数组unset title
filename=(*)对当前目录的文件生成数组，里面的文件就是元素
filename=(f{1,2,3}.{txt,log})数组的生成里面的元素用空格隔开就行
num=(`seq 10`) 支持命令
echo  ${title[*]:1:2}表示在所有元素中跳过第一个显示后面两个
echo ${title[*]:2}  在所有元素中跳过前两个，显示后面的全部

脚本（生成十个随机数，挑出最大值，最小值）
declare -a num 
for((i=0;i<10;i++));do
&ensp;&ensp;num[$i]=$RANDOM
&ensp;&ensp;if [ $i -eq 0 ];then
 &ensp;&ensp;&ensp;MAX=${num[$i]}
                MIN=${num[$i]}
        else
                if [ "$MAX" -lt ${num[$i]} ];then
                        MAX=${num[$i]}
                elif [ "$MIN" -gt ${num[$i]} ];then
                        MIN=${num[$i]}
                else
                        true
                fi
        fi
done
echo all random are ${num[*]}
echo max is $MAX
echo min is $MIN       
 echo ${#str}显示变量有几个字符
echo ${str:10}跳过10个字符，显示后面的
echo ${str:10:3}跳过10个，显示后面的前三个
echo ${str: -3}必须有空格，取倒数第三个
echo  ${str:4:-3}去掉前4个和后面三个
echo ${str: -4:-3}负4前面有空格，取负4和负3之间的数值（centos6不支持） 
写一个expect脚本，自动登录
#!/usr/bin/expect
spawn（启动进程） scp  /data/bin/shengdanshu.sh chen@192.168.66.129:/data
expect {
"yes/no" { send "yes\n";exp_continue（后续跟有命令） }
"password" { send "123456\n" }
}
expect eof                                
想要运行脚本，加执行权限 （而且要传送的主机能访问）
用./运行脚本，它不是bash所以不能要bash 执行它
ntsysv可以控制服务的启动
runlevel   --level=3修改是3模式下的启动服务
chkconfig   --list  命令可以一次看到所有模式的服务
chkconfig  --level  13  atd  on  可以一次修改3和1模式的atd的服务
当不写模式，默认修改的2，3，4，5模式  chkconfig  atd  on(启动）
启动流程（centos6启动流程）
加电自检----读取mbr里grub的第一阶段（stagel  stage1.5 )----进入2阶段 
 grub的stage2在/boot/grub/目录下--------在/boot/grub/grub.conf文件里
 定义了VMlinux的路径以及根的路径（要读取根还要借助initramfs.img文件来加载驱动）
 -----加载根后读取系统的第一个进程（/sbin/init)----读取进程/sbin/init下的inittab文件
 （根据这个文件来决定开机进入到什么模式）----然后读取第一个脚本/etc/rc.d/rc.sysinit
 (初始化模式）---运行完之后就看是运行模式所需要启
 动的进程脚本（先运行K开头，在运行S，
 最后运行/etc/rc.d/rc.local---进入登录界面
 进入救援模式
第一步挂载光盘(创建文件）
mkdir  /mnt/cdrom
mount  /dev/sr0    /mnt/cdrom
第二步（装内核包，默认当前不是硬盘的根，指定根）
rpm -ivh   /mnt/cdrom/Packages/kernel-...../     --root=/mnt/sysimage   --force
切换根（chroot   /mnt/sysimage/)
第三步（修复grub文件)
grub-install   /dev/sda  
第四步（进入grub目录，需要生成grub.conf文件）vim grub.conf
default=0
timeout=3
title  linux
kernel /vmlinuz----         root=/dev/sda2(真正的根目录）
initrd  /initra------
同步sync退出
awk  '{print "abc"$0}' /etc/passwd可以在每一行添加abc
在awk里如果是变量不要加引号不用加$，如果要打印字符串，要用双引号
awk  '{print 1+2}' /etc/fstab支持数字运算
awk 'BEGIN{print "1+2"}'表示开始文件前执行一次，所以会打印一行
awk 'BEGIN{print "1+3"}{print $0}END{print "abc"}' /etc/fstab先打印第一行，执行命令，最后总结
awk -F":" '{print $1,$3}' /etc/passwd取出第一列和第三列（-F指定分隔符：）但是以什么为分隔符，分隔符
不会显示（打印出来的默认分隔符是空白）在 $1,$3之间把要指定的分割符用引号定义，可以指定分割符
 FS变量：域（字段，列）定义分隔符
awk -v FS=":" '{print $1FS$3}' /etc/passwd要定义变量加-v，利用变量可以省略写字符串，放到$1$3之间（可以引用）
 fs=:    awk -v FS=$fs '{print $1FS$3}' /etc/passwd支持调用shell中定义的变量（-f选项也支持调用）
OFS：输出,定义输出的分隔符
awk -v FS=: -v OFS=+++  '{print $1,$3}' /etc/passwd（awk变量之间的定义，不能互相调用）
RS:定义行（记录）的分割符,记录默认等于行，也可以自己定义
awk -v   RS=： ‘{print  $0}'  f1,txt  定义冒号为记录的分割符（作为一行）
其中ccdd是一行（记录），但每一个记录默认用空格分割成列，所以$1表
