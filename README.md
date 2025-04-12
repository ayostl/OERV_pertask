# OERV_pertask工作前准备

## 声明

本文档仅作为 OERV 实习生的工作前准备，不涉及任何 OERV 相关的工作内容。
本人的安装过程有些许曲折，看本文档的人在以后做该项目实验时，个人建议直接使用已经成熟的体系，比如 Ubuntu 22.04 LTS 或者 ArchLinux ，这样可以避免一些不必要的麻烦。
祝你工作顺利！

---

## 有关实验详细内容请阅读相关材料

> OERV 实习生帮助文档 [github 连接](https://github.com/openeuler-riscv/oerv-team/blob/main/Intern/guide.md "OERV 实习生指南")

## pretask（省流版：不喜欢跳转连接的有福了）

> pretask 的目的是帮助实习生一起搭建工作环境，熟悉 oerv 的工作流程和合作方式。 pretask 分为三个步骤：
>
> 任务一：通过 QEMU 仿真 RISC-V 环境并启动 openEuler RISC-V 系统，设法输出 neofetch 结果并截图提交
>
> 任务二：在 openEuler RISC-V 系统上通过 obs 命令行工具 osc，从源代码构建 RISC-V 版本的 rpm 包，比如 pcre2。（提示首先需要在 openEuler的 OBS什么是 OBS？ 上注册账号 <https://build.tarsier-infra.isrc.ac.cn）>
>
> 任务三：尝试使用 qemu user & nspawn 或者 docker 加速完成任务二
>
> 任务完成的方式为在本仓库提交 PR，并在 PR 的 Conversation 中附上任务完成截图，在 Intern/intern_message.md 的 实习生信息 下中加入自己的信息（pretask 考核的一部分）。这个阶段希望实习生养成积极提问和正确提问的习惯，并且构建属于自己的工作流和环境。

## 任务一

> 通过 QEMU 仿真 RISC-V 环境并启动 openEuler RISC-V 系统，设法输出 neofetch 结果并截图提交

本机环境介绍：

[![本机环境介绍](images/system.png "本机环境介绍")](https://github.com/ayostl/OERV_pertask/tree/main/images/system.png)

由于本地声卡无 Linux 版本的驱动暂时只能使用 Windows 系统（等有钱了再买一台工作机吧），所以我们需要使用 [QEMU for Windows](https://qemu.weilnetz.de/w64/2019/ "点击跳转到 QEMU 下载页") 进行 QEMU 模拟器的安装

> 参考资料：[初始 openEuler（一）](https://www.openeuler.org/zh/blog/traffic_millions/2020-03-27-qemu.html "初试 openEuler（一）：windows 下使用 qemu 安装 openEuler ")

安装过程（可以直接跳过本部分看 WSL Ubuntu 安装）：

1. 下载 [QEMU for Windows](https://qemu.weilnetz.de/w64/qemu-w64-setup-20250326.exe "点击下载 QEMU for Windows 20250326") 并解压到本地目录中，通过 exe 可执行文件进行安装
2. 下载 [openEuler 25.03](https://www.openeuler.org/zh/download/#openEuler%2025.03 "点击跳转到 openEuler 25.03 下载页") 选择 RISC-V 架构的云计算中的 qcow2.xz 虚拟机镜像文件
3. 编辑系统环境变量 PATH ，确保 QEMU 能够在终端内执行
4. 创建一个文件夹 **openEuler_RISCV** 用于保存虚拟机文件，并将 **qcow2.xz** 解压到该文件夹中
5. 进入 QEMU 安装目录，并在其 share 子文件夹中找到 **edk2-riscv-code.fd** 文件并一同复制到该目录中
6. 打开终端（ PowerShell ），输入以下命令启动虚拟机：

```PowerShell
qemu-system-riscv64 `
  -m 4096 `
  -cpu rv64 `
  -smp 4 `
  -M virt `
  -bios edk2-riscv-code.fd `
  -hda openEuler-24.03-LTS-SP1-riscv64.qcow2 `
  -serial vc:800x600
```

启动后出现以下界面：
[![第一个错误](images/error1.png)](https://github.com/ayostl/OERV_pertask/tree/main/images/error1.png)

又找了一份文档，尝试以下脚本：

```sh
@echo off
chcp 65001
set vcpu=4
set memory=4
set drive=openEuler-22.09-riscv64-qemu-xfce.qcow2
set fw=fw_payload_oe_qemuvirt.elf
set ssh_port=12055

echo :: Starting VM...
echo :: Using following configuration

echo vCPU Cores: %vcpu%
echo Memory: %memory%G
echo Disk: %drive%
echo SSH Port: %ssh_port%

set path="F:\qemu";%PATH%
qemu-system-riscv64 `
  -nographic `
  -machine virt `
  -smp %vcpu% `
  -m %memory%G `
  -kernel "%fw%" `
  -bios none `
  -drive file=%drive%,format=qcow2,id=hd0 `
  -device virtio-vga `
  -device virtio-blk-device,drive=hd0 `
  -device virtio-net-device,netdev=usernet `
  -netdev user,id=usernet,hostfwd=tcp::"%ssh_port%"-:22 `
  -device qemu-xhci `
  -usb `
  -device usb-kbd `
  -device usb-tablet `
  -append "root=/dev/vda1 rw console=ttyS0 swiotlb=1 loglevel=3 systemd.default_timeout_start_sec=600 selinux=0 highres=off mem=512M earlycon"
```

尝试从源码构建：

> 参考资料：[QEMU Wiki](https://wiki.qemu.org/Hosts/W32 "点击跳转到 QEMU Wiki")

通过 Wiki 上的帮助文档一步步执行，其中 python 包中缺少一些依赖可以从该链接下载，以完成构建 [mysy2.packages 仓库](https://packages.msys2.org/ "点击跳转到 mysy2.packages 仓库")

但是我遇到了以下下错误：

[![第二个错误](images/error2.png)](https://github.com/ayostl/OERV_pertask/tree/main/images/error2.png)

暂时拼劲全力无法解决（日后再去解决吧），转而使用 Wsl 方案，虚拟机看起来太抽象了，还是 Wsl 好一点

仓皇建立了一个 wsl 虚拟机，试图用 wsl 来安装 qemu 模拟 riscv 环境

[![openSUSE](images/openSUSE.png)](https://github.com/ayostl/OERV_pertask/tree/main/images/openSUSE.png "openSUSE WSL2")

重新从源码构建 qemu ,但是无法编译成功，悲），以后有时间再研究吧

重新安装了一个[![Ubuntu](images/Ubuntu.png)](https://github.com/ayostl/OERV_pertask/tree/main/images/Ubuntu.png "Ubuntu WSL2")

使用 apt 命令安装 qemu 及其相关依赖

但是由于 Ubuntu 版本过低，无法使用 UEFI 启动，将 Ubuntu 升至 24.04 版本重新尝试

> 参考资料：[Ubuntu Boards documentation](https://canonical-ubuntu-boards.readthedocs-hosted.com/en/latest/how-to/qemu-riscv/)

结果居然异常的顺利，成功启动虚拟机，以下是启动代码：

```sh
qemu-system-riscv64 \
    -machine virt -nographic -m 2048 -smp 4 \
    -kernel /usr/lib/u-boot/qemu-riscv64_smode/uboot.elf \
    -device virtio-net-device,netdev=eth0 -netdev user,id=eth0 \
    -device virtio-rng-pci \
    -drive file=openEuler-24.03-LTS-SP1-riscv64.qcow2,format=qcow2,if=virtio

# machine 用于指定虚拟机启动模式，也可以用-m
# nographic 用于指定虚拟机不使用图形界面
# m 指定内存大小
# smp 指定 CPU 核心数
# kernel 指定 UEFI 启动的内核
# device 指定虚拟机设备
# netdev 指定网络设备
# drive 指定虚拟机磁盘
# format 指定磁盘格式
# if 指定磁盘接口
```

启动后出现以下界面：

[![登录](images/login.png)](https://github.com/ayostl/OERV_pertask/tree/main/images/login.png "登录界面")

默认登录账户密码在 [openEuler 官网](https://docs.openeuler.org/zh/docs/24.03_LTS_SP1/docs/Releasenotes/%E5%B8%90%E5%8F%B7%E6%B8%85%E5%8D%95.html# "点此打开 openEuler 帐号清单")上能找到

输入账号密码后先，创建一个自用的用户，检查系统依赖然后通过 yum 命令 补全系统依赖，然后 通过 git 构建源码并 make 编译

结果如下：

[![neofetch 截图](images/neofetch.png)](https://github.com/ayostl/OERV_pertask/tree/main/images/neofetch.png "neofetch 截图")

任务一完成！

## 任务二

> 任务二：在 openEuler RISC-V 系统上通过 obs 命令行工具 osc，从源代码构建 RISC-V 版本的 rpm 包，比如 pcre2。

第一步先在 openEuler 上安装 obs 命令行工具

> 参考资料：[如何在 openEuler 上使用 OBS 命令行工具](https://www.openeuler.org/zh/blog/fuchangjie/2020-03-26-how-to-OBS.html)

注册一个 obs 账号后在本地构建一个 obs 的工作主目录，并通过 osc checkout home:username 命令把远程家目录和本地工作区建立连接

[![在建立项目时服务器崩了](images/break.png)](https://github.com/ayostl/OERV_pertask/tree/main/images/break.png "在建立项目时服务器崩了")