# fio 工具的使用

> [!NOTE]
>
> 本文档仅针对ARM服务器

## 简介

stress是一个linux的压力测试工具，主要用来模拟系统负载较高时的场景，用于对系统的CPU、IO、内存、负载、磁盘等进行压力测试

## 环境配置

| 服务器 | HUAWEI Kunpeng 920-4826                       |
| ------ | --------------------------------------------- |
| CPU    | 5250（2*48core）@2600 MHz                     |
| 内存   | 8 * 32GB 2666 MT/s                            |
| 硬盘   | 1 * 6TB SATA SSD + 4 * 6TB SAS HDD (均为JBOD) |
| 网卡   | TM210 4 * GE + TM280 4 * 25GE                 |
| OS     | openEuler 22.03 (LTS-SP3)                     |

## 安装指南

1. 在鲲鹏社区兼容性清单下载lmbench的RPM包（找自己OS对应的版本）：https://www.hikunpeng.com/ecosystem/compatibility

2. 上传RPM包到服务器：stress-1.0.4-30.aarch64.rpm

3. Yum安装RPM包

   ```Bash
   yum install stress-1.0.4-30.aarch64.rpm
   ```

## 使用指导

1. 进行硬盘读写性能压测

   ```Bash
   JBOD 顺序读:
   fio -filename=/dev/sdb -direct=1 -iodepth 1 -thread -rw=read -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=60 -group_reporting -name=mytest
   read: IOPS=24.7k, BW=386MiB/s
   read: IOPS=28.4k, BW=445MiB/s
   read: IOPS=27.6k, BW=431MiB/s
   
   JBOD 随机读:
   fio -filename=/dev/sdb -direct=1 -iodepth 1 -thread -rw=randread -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=60 -group_reporting -name=mytest
   read: IOPS=709, BW=11.1MiB/s
   read: IOPS=708, BW=11.1MiB/s
   read: IOPS=709, BW=11.1MiB/s
   
   JBOD 顺序写:
   fio -filename=/dev/sdb -direct=1 -iodepth 1 -thread -rw=write -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=60 -group_reporting -name=mytest -allow_mounted_write=1
   write: IOPS=4291, BW=67.1MiB/s
   write: IOPS=3558, BW=55.6MiB/s
   write: IOPS=5235, BW=81.8MiB/s
   
   JBOD 随机写：
   fio -filename=/dev/sdb -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=60 -group_reporting -name=mytest -allow_mounted_write=1
   write: IOPS=832, BW=13.0MiB/s
   write: IOPS=805, BW=12.6MiB/s
   write: IOPS=799, BW=12.5MiB/s
   ```
