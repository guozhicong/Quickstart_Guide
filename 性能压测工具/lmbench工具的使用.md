# lmbench 工具的使用

> [!NOTE]
>
> 本文档仅针对ARM服务器

## 简介

一款简易可以移植的内存测试工具，其主要功能有，带宽测评（读取缓存文件、拷贝内存、读/写内存、管道、TCP），延时测评（上下文切换、网络、文件系统的建立和删除、进程创建、[信号处理](https://so.csdn.net/so/search?q=信号处理&spm=1001.2101.3001.7020)、上层系统调用、内存读入反应时间）等功能。

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

2. 上传RPM包到服务器： lmbench-3-4.oe2203sp2.aarch64.rpm

3. Yum安装RPM包

   ```Bash
   yum install lmbench-3-4.oe2203sp2.aarch64.rpm
   ```

## 使用指导

| 内存带宽压测命令 | 内存带宽压测命令解释 |
| ---------------- | -------------------- |
| rd               | 只读                 |
| wr               | 只写                 |
| rdwr             | 读写                 |
| cp               | 拷贝                 |

1. 进行内存读写拷贝等带宽压测

   ```Bash
   
   /opt/lmbench/bin/bw_mem 1000M rd
   /opt/lmbench/bin/bw_mem 1000M wr
   /opt/lmbench/bin/bw_mem 1000M rdwr
   /opt/lmbench/bin/bw_mem 1000M cp
   
   # 测试结果
   # 1048.58为读写的数据量1G，8852.48为内存带宽（单位应该是MB/s,未确认）
   [root@hostname-h893h ~]# /opt/lmbench/bin/bw_mem 1000M rdwr
   1048.58 8852.48
   [root@hostname-h893h ~]# /opt/lmbench/bin/bw_mem 1000M rdwr
   1048.58 9105.62
   [root@hostname-h893h ~]# /opt/lmbench/bin/bw_mem 1000M rdwr
   1048.58 8692.21
   [root@hostname-h893h ~]# /opt/lmbench/bin/bw_mem 1000M rdwr
   1048.58 8480.54
   [root@hostname-h893h ~]# /opt/lmbench/bin/bw_mem 1000M rdwr
   1048.58 8991.39
   [root@hostname-h893h ~]# /opt/lmbench/bin/bw_mem 1000M rdwr
   1048.58 8998.34
   ```
