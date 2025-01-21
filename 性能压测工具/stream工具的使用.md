# stream 工具的使用

> [!NOTE]
>
> 本文档仅针对ARM服务器

## 简介

STREAM 是业界广为流行的综合性内存带宽实际性能测量工具之一。

## 环境配置

| 服务器 | HUAWEI Kunpeng 920-4826                     |
| ------ |---------------------------------------------|
| CPU    | 5250（2*48core）@2600 MHz                     |
| 内存   | 8 * 32GB 2666 MT/s                          |
| 硬盘   | 1 * 6TB SATA SSD + 4 * 6TB SAS HDD (均为JBOD) |
| 网卡   | TM210 4 * GE + TM280 4 * 25GE               |
| OS     | openEuler 22.03 (LTS-SP3)                   |

## 安装指南

1. 安装依赖软件

   ```Bash
   yum install -y make cmake gcc-c++ numactl
   ```

2. 下载源码

   ```Bash
   git clone https://github.com/jeffhammond/STREAM.git
   ```

3. 整机内存带宽压测编译指导

   *参数解释*

   | gcc参数             | 解释                                                         |
   | ------------------- | ------------------------------------------------------------ |
   | -DSTREAM_ARRAY_SIZE | 指定计算中a[],b[],c[]数组的大小（4 * L3 cache size <= STREAM ARRAY_SIZE × 8（双精度） × 3 （三个数组）<= 0.6*M；M 是用户的可用内存） |
   | -DNTIMES            | 执行次数，默认值是10                                         |
   | -fopenmp            | 适应多处理器环境；开启后，程序默认线程为CPU线程数            |

   3.1 修改Makefile文件

   ```Bash
   cd STREAM-master
   vim Makefile
   
   #Makefile 修改CFLAGS行(hang)为：
   CFLAGS = -O3 -ftree-vectorize -fopenmp -DSTREAM_ARRAY_SIZE=800000000 -DNTIMES=20 -mcmodel=large -mcpu=native
   
   # 修改all行为：
   all: stream_c.exe
   ```

   3.2 编译

   ```Bash
   make clean
   makemake
   ```

   3.3 修改执行文件名称

   ````Bash
   mv stream_c.exe stream_multi
   ````

4. 单核内存带宽压测编译指导

   4.1 修改Makefile文件

   ```Bash
   cd STREAM-master
   vim Makefile
   
   #Makefile 修改CFLAGS行(hang)为：
   CFLAGS = -O3 -ftree-vectorize -DSTREAM_ARRAY_SIZE=800000000 -DNTIMES=20 -mcmodel=large -mcpu=native
   
   # 修改all行为：
   all: stream_c.exe
   ```

   4.2 编译

   ```Bash
   make clean
   make
   ```

   4.3 修改执行文件名称

   ````Bash
   mv stream_c.exe stream_single
   ````

## 使用指导

5. 整机内存带宽压测

   ```Bash
   [root@hostname-acpym STREAM-master]# export OMP_NUM_THREADS=96
   [root@hostname-acpym STREAM-master]# export GOMP_CPU_AFFINITY=0-95
   [root@hostname-acpym STREAM-master]# numactl -N 0-3 -m 0-3 ./stream_multi
   -------------------------------------------------------------
   STREAM version $Revision: 5.10 $
   -------------------------------------------------------------
   This system uses 8 bytes per array element.
   -------------------------------------------------------------
   ...
   -------------------------------------------------------------
   Function    Best Rate MB/s  Avg time     Min time     Max time
   Function    Best Rate MB/s  Avg time     Min time     Max time
   Copy:          115277.9     0.112073     0.111036     0.116243
   Scale:         107334.7     0.120743     0.119253     0.124040
   Add:           110800.1     0.173750     0.173285     0.175508
   Triad:         110880.1     0.174127     0.173160     0.175380
   -------------------------------------------------------------
   Solution Validates: avg error less than 1.000000e-13 on all three arrays
   -------------------------------------------------------------
   ```
   
   
   
6. 单核内存带宽压测

   ```Bash
   [root@hostname-acpym STREAM-master]# export OMP_NUM_THREADS=1
   [root@hostname-acpym STREAM-master]# export GOMP_CPU_AFFINITY=3 # 如果OS上有其他负载就避开core 0， 避免系统噪声干扰
   [root@hostname-acpym STREAM-master]# numactl -C 3 -m 0 ./stream_single
   -------------------------------------------------------------
   STREAM version $Revision: 5.10 $
   -------------------------------------------------------------
   ...
   -------------------------------------------------------------
   Function    Best Rate MB/s  Avg time     Min time     Max time
   Copy:           10831.6     1.182519     1.181722     1.183202
   Scale:          11620.6     1.103828     1.101488     1.105538
   Add:            12299.7     1.565197     1.561009     1.569161
   Triad:          12308.7     1.560830     1.559876     1.561886
   -------------------------------------------------------------
   Solution Validates: avg error less than 1.000000e-13 on all three arrays
   ```
