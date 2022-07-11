## 1、ngrep命令
ngrep命令是grep命令的网络版，ngrep用于搜寻指定的数据包（抓包）。能识别TCP、UDP和[ICMP包](http://baike.baidu.com/item/ICMP)，理解[bpf](https://zh.wikipedia.org/wiki/BPF)的过滤机制。

### 1.1 安装
`brew install ngrep`

### 1.2 语法
```
ngrep <-LhNXViwqpevxlDtTRM> <-IO pcap_dump> <-n num> <-d dev> <-A num> <-s snaplen> <-S limitlen> <-w normal|byline|single|none> <-c cols> <-P char> <-F file>
```
### 1.3 选项
- \-q ：不显示包的详情，都以‘＃’ 号代替现实
- \-e ：显示空数据包
- \-i ：**忽略大小写**
- \-v ：**反转匹配**
- \-R ：don't do privilege revocation logic
- \-x ：以16进制格式显示
- \-X ：以16进制格式匹配
- \-w ：整字匹配
- \-p ：不使用混杂模式
- \-l ：make stdout line buffered
- \-D ：replay pcap_dumps with their recorded time intervals
- \-t ：在每个匹配的包之前显示时间戳
- \-T ：显示上一个匹配的数据包之间的时间间隔
- \-M ：仅进行单行匹配
- \-I ：从文件中读取数据进行匹配
- \-O ：将匹配的数据保存到文件
- \-n ：仅捕获指定数目的数据包进行查看
- \-A ：匹配到数据包后dump随后的指定数目的数据包
- \-s ：set the bpf caplen
- \-S ：set the limitlen on matched packets
- \-W ：设置显示格式byline将解析包中的换行符
- \-c ：强制显示列的宽度
- \-P ：set the non-printable display char to what is specified
- \-F ：使用文件中定义的bpf(Berkeley Packet Filter)
- \-N ：显示由IANA定义的子协议号
- \-d ：**使用哪个网卡，可以用-L选项查询**
- \-L ：查询网卡接口

## 2、使用实例
注意：需要以超级管理员身份执行该命令
- `ngrep host 192.168.1.8 udp`：抓udp包
- `ngrep -W byline  -qd any '.event' udp port 1514`：抓取1514端口udp包，过滤正则匹配包含‘event’字段的包
- `ngrep -W byline 'GET /' 'tcp and dst port 80' -d eth1 |  awk -v RS="#+" -v FS="\n" 'length() > 1000'`：查询大于1k的请求头
- `ngrep -d en0 -W byline host 192.168.1.9`：抓取本地 en0 和 61.135.169.125（百度ip）的通信信息，并以行打印出来

    ![屏幕快照_2016-12-06_下午4.54.13](/uploads/8db3464c1f476212018d93a66019ec36/屏幕快照_2016-12-06_下午4.54.13.png)


