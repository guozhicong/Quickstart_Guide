# stress 工具的使用

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

1. 进行CPU压测

   ```Bash
    # -c 指定核数（产生n个进程 每个进程都反复不停的计算随机数的平方根） -t指定时间（s）  
    stress -c 8 -t 600
   ```

2. 进行磁盘IO压测
      ```Bash
    # -i  产生n个进程 每个进程反复调用sync()，sync()用于将内存上的内容写到硬盘上
    stress -i 1 -t 600
   ```
   
3. 