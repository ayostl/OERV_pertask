# OERV_pertask工作前准备

---

## 有关详细内容请阅读相关材料

> OERV 实习生帮助文档[github连接](https://github.com/openeuler-riscv/oerv-team/blob/main/Intern/guide.md)

## pretask（省流版）

> pretask 的目的是帮助实习生一起搭建工作环境，熟悉 oerv 的工作流程和合作方式。 pretask 分为三个步骤：
> 任务一：通过 QEMU 仿真 RISC-V 环境并启动 openEuler RISC-V 系统，设法输出 fastfetch 结果并截图提交
> 任务二：在 openEuler RISC-V 系统上通过 obs 命令行工具 osc，从源代码构建 RISC-V 版本的 rpm 包，比如 pcre2。（提示首先需要在 openEuler的 OBS什么是 OBS？ 上注册账号 <https://build.tarsier-infra.isrc.ac.cn）>
> 任务三：尝试使用 qemu user & nspawn 或者 docker 加速完成任务二
> 任务完成的方式为在本仓库提交 PR，并在 PR 的 Conversation 中附上任务完成截图，在 Intern/intern_message.md 的 实习生信息 下中加入自己的信息（pretask 考核的一部分）。这个阶段希望实习生养成积极提问和正确提问的习惯，并且构建属于自己的工作流和环境。

## 任务一

> 通过 QEMU 仿真 RISC-V 环境并启动 openEuler RISC-V 系统，设法输出 fastfetch 结果并截图提交

本机环境介绍：

[![本机环境介绍](images/system.png "本机环境介绍")](https://github.com/ayostl/OERV_pertask/tree/main/images/system.png)
