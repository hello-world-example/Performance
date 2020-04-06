# 硬件信息



## Disk

### 查看磁盘编号

```bash
$ fdisk -l 
...
磁盘 /dev/sda：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
```



### 查看是否 SSD

```bash
# 查看是否 SSD
$ lsblk -h
...
 -h, --help     显示此帮助并退出
 -V, --version  输出版本信息并退出

可用列(用于 --output)：
        NAME  设备名
       KNAME  internal kernel device name
     MAJ:MIN  主:次 设备号
      FSTYPE  文件系统类型
         ...
 
# ROTA 0:SSD; 1:HDD
$ lsblk -d -p -o NAME,ROTA,TYPE
NAME       ROTA TYPE
/dev/sda      1 disk
/dev/sr0      1 rom
/dev/loop0    1 loop 
/dev/loop1    1 loop 
...

# 或者 
$  cat /sys/block/sda/queue/rotational
1 # 0:SSD; 1:HDD

# 或者
$ lsscsi
[1:0:0:0]    cd/dvd  NECVMWar VMware IDE CDR10 1.00  /dev/sr0 
[2:0:0:0]    disk    VMware,  VMware Virtual S 1.0   /dev/sda 
```

### 磁盘硬件型号

```bash
$ smartctl --all /dev/sda 
=== START OF INFORMATION SECTION ===
Vendor:               VMware,
Product:              VMware Virtual S
Revision:             1.0
User Capacity:        21,474,836,480 bytes [21.4 GB]
Logical block size:   512 bytes
Rotation Rate:        Solid State Device
Device type:          disk
...

# 获取通过 BIOS 主板查看
$  dmidecode -t system
...
Handle 0x0001, DMI type 1, 27 bytes
System Information
        Manufacturer: VMware, Inc.
        Product Name: VMware Virtual Platform
...
```



## Net

### 网卡硬件信息

```bash
$ lspci | grep -i 'eth'
02:01.0 Ethernet controller: Intel Corporation 82545EM Gigabit Ethernet Controller (Copper) (rev 01)
```

### 所有网络接口

```bash
$ ifconfig -a
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.83.130  netmask 255.255.255.0  broadcast 192.168.83.255
        inet6 fe80::97e8:a33:3048:8c3b  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:77:97:89  txqueuelen 1000  (Ethernet)
        RX packets 183567  bytes 255953157 (244.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 88436  bytes 7424860 (7.0 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        ...

# 
$ ip addr
# 
$ ip link show
```

### 网络接口的详细信息

```bash
$ ethtool ens33

Supported ports: [ TP ]
Supported link modes:   10baseT/Half 10baseT/Full 
        100baseT/Half 100baseT/Full 
        1000baseT/Full                              # 支持千兆半双工，全双工模式
Supported pause frame use: No
Supports auto-negotiation: Yes
Supported FEC modes: Not reported
Advertised link modes:  10baseT/Half 10baseT/Full 
        100baseT/Half 100baseT/Full 
        1000baseT/Full
Advertised pause frame use: No
Advertised auto-negotiation: Yes                     # 默认使用自适应模式
Advertised FEC modes: Not reported
Speed: 1000Mb/s                                      #
Duplex: Full                                         # 
Port: Twisted Pair
PHYAD: 0
Transceiver: internal
Auto-negotiation: on
MDI-X: off (auto)
Supports Wake-on: d
Wake-on: d
Current message level: 0x00000007 (7)
        drv probe link
Link detected: yes
```



## CPU

```bash
$ cat /proc/cpuinfo | grep --color "model name"
model name      : Intel(R) Core(TM) i5-5257U CPU @ 2.70GHz

$ lscpu
Architecture:          x86_64               # 架构
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                1                    # CPU 个数
On-line CPU(s) list:   0
Thread(s) per core:    1                    # 每个内核支持的线程数
Core(s) per socket:    1                    # CPU 内核数
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel         # 厂商
CPU family:            6
Model:                 61
Model name:            Intel(R) Core(TM) i5-5257U CPU @ 2.70GHz
Stepping:              4
CPU MHz:               2700.074
BogoMIPS:              5400.14
Hypervisor vendor:     VMware
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              3072K

# 
$ dmidecode -t processor
```



## Mem

### 内存大小

```bash
# 内存大小
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           1980         962          72         323         945         363
Swap:             0           0       
```
### 内存详细信息
```bash
cat /proc/meminfo
MemTotal:        2027940 kB
MemFree:           74072 kB
MemAvailable:     371928 kB
Buffers:           95712 kB
Cached:           828604 kB
SwapCached:            0 kB
...
```

### 内存硬件信息

```bash
$ dmidecode -t memory
```



## 小结

|   Description    |              Command              |
| :--------------: | :-------------------------------: |
|     内核版本     |        `cat /proc/version`        |
|    BIOS 信息     |  `cat /sys/class/dmi/id/bios_*`   |
| 机器产品和序列化 | `cat /sys/class/dmi/id/product_*` |
|     硬件信息     |            `dmidecode`            |
|       键鼠       |   `cat /proc/bus/input/devices`   |



## Read More

- [`smartctl`](https://linux.cn/article-4682-1.html) 硬盘监控和分析工具
- [`lsblk`](https://www.linuxcool.com/lsblk) 查看系统的磁盘（`list block`）
- [`lsscsi`](https://idc.wanyunshuju.com/cym/1289.html) 列出 `SCSI` 设备及属性详解
- [linux 下查看硬盘型号、大小等信息（含Raid）](https://blog.csdn.net/harbor1981/article/details/42772377)

