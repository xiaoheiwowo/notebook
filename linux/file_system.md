Linux 文件系统 Ext2/3/4
磁盘分区partition - 文件系统filesystem
通过LVM或其他技术可以使一个分区有多个文件系统或者多个分区合成一个文件系统，所以只提文件系统，一个文件系统一般分为多个block group ，每个包含inode和block，可能有superblock的备份，在格式化时已经规划好。
superblock:记录此 filesystem 的整体信息,包括 inode/block 的总量、使用量、剩余量, 以及文件系统的格式与相关信息等;
inode:记录文件的属性文件权限(rwx)与文件属性(拥有者、群组、时间参数等),一个文件占用一个 inode,同时记录此文件的数据所在的 block 号码;
block:实际记录文件的内容,若文件太大时,会占用多个 block

inode 128/256字节大小，除了文件属性外包含12个直接1个间接1个双间接1个三间接记录区(ext2)
block大小 1k 2k 4k 不同大小会决定最大文件大小和总文件系统大小

superblock
block 与 inode 的总量;
未使用与已使用的 inode / block 数量;
block 与 inode 的大小 (block 为 1, 2, 4K,inode 为 128bytes 或 256bytes);
filesystem 的挂载时间、最近一次写入数据的时间、最近一次检验磁盘 (fsck) 的时间等文件系统的相关信息;
一个 valid bit 数值,若此文件系统已被挂载,则 valid bit 为 0 ,若未被挂载,则 valid bit 为 1 。

Filesystem Description
block bitmap 
inode bitmap

命令
blkid
dumpe2fs /dev/sdb5

文件，目录和文件系统的关系
1个文件占用1个inode和至少1个block
block中存文件的内容
一个目录占用1个inode和至少1个block
block里存目录下文件名称和此文件对应的inode
ls -i 可以查看inode
注意，文件名是存在目录的block中的
新建一个文件或目录的过程
1.  先确定用户对于欲新增文件的目录是否具有 w 与 x 的权限,若有的话才能新增;
2.  根据 inode bitmap 找到没有使用的 inode 号码,并将新文件的权限/属性写入;
3.  根据 block bitmap 找到没有使用中的 block 号码,并将实际的数据写入 block 中,且更新 inode 的 block指向数据;
4.  将刚刚写入的 inode 与 block 数据同步更新 inode bitmap 与 block bitmap,并更新 superblock 的内容。

日志式文件系统
ext3/4 增加日志防止文件读写过程中出错。！！！
文件在磁盘和内存上的同步
判断dirty clean 标志将内存中的文件写到磁盘，sync 手动同步。

Linux通过VFS虚拟文件系统管理不同文件系统
XFS文件系统 日志式的，也有inode和block，相比于ext格式化快。适合大容量磁盘，动态划分inode，block

> 命令
df 查看磁盘占用
du 

链接
hard link 是在某个目录下新增一笔档名链接到某 inode 号码的关连记录
只能链接文件
symbolic link 相当于windows快捷方式
>命令
ln -sf 来源文件 目标文件

格式化,挂载
lsblk [-dfimpt] [device]
选项与参数:
-d :仅列出磁盘本身,并不会列出该磁盘的分区数据
-f :同时列出该磁盘内的文件系统名称
-i :使用 ASCII 的线段输出,不要使用复杂的编码 (再某些环境下很有用)
-m :同时输出该装置在 /dev 底下的权限数据 (rwx 的数据)
-p :列出该装置的完整文件名!而不是仅列出最后的名字而已。
-t :列出该磁盘装置的详细数据,包括磁盘队列机制、预读写的数据量大小等

MBR 分区表请使用 fdisk 分区, GPT 分区表请使用 gdisk 分区























