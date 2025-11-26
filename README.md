# 一、Linux服务器安装Singularity
## 使用 Conda 安装 Singularity（无 root 权限方案）
conda 将 Singularity 及其所有依赖库都安装在你的**个人用户目录**内，形成一个独立且完整的运行环境，从而让你在无需系统管理员权限的情况下，也能正常安装并使用 Singularity。
* 创建一个专门用于 Singularity 的 conda 环境
     
        conda create -n singularity
        conda activate singularity
* 从 conda-forge 频道安装 Singularity

        conda install -c conda-forge singularity

* 查看singularity 版本
  
        singularity --version
# 二、拉取镜像
## 1.从Singularity自己的官方镜像库拉取
* 从 Singularity 自己的官方镜像库下载一个有趣的测试镜像，用来验证你的 Singularity 安装是否成功、网络是否通畅。lolcow 是一个经典测试镜像，它会显示一头牛的 ASCII 艺术图案和一条随机有趣的格言/笑话。

        singularity pull library://lolcow
* 可能会遇到网络连接问题。这个错误表明网络连接中断了，导致下载失败。错误信息 connection reset by peer 通常意味着服务器端或中间网络设备主动关闭了连接。

        singularity pull --disable-cache library://lolcow
        #--disable-cache：这是关键选项，意思是 "禁用缓存"。完全忽略本地缓存；强制从网络重新下载；下载全新的副本。
* 当你运行这个命令后：
    * 它会从 Singularity Library 下载 lolcow 镜像。
    * 在本地生成一个名为 lolcow_latest.sif 的 SIF 文件。
    * 您可以通过以下命令运行它。
## 2. 运行
* 直接运行（最常用）

        singularity run lolcow_latest.sif
        #直接运行本地已存在的SIF文件，这会执行镜像中预设的默认命令，显示奶牛和随机笑话。
* 在容器内部执行一个特定的命令

        singularity exec lolcow_latest.sif cat /etc/os-release
        #singularity exec：执行命令的指令
        #lolcow_latest.sif：目标容器
        #cat /etc/os-release：要在容器内部运行的命令  查看容器内的操作系统信息
        #查看容器内的磁盘空间
        singularity exec lolcow_latest.sif df -h
* 进入容器的交互式 Shell

        #进入容器的命令行环境。进入后你会看到提示符变成：Singularity>，可以像在普通Linux系统中一样探索。输入 exit 退出。
        singularity shell lolcow_latest.sif
        #查看当前在容器内的位置
        pwd
        #查看容器里安装了哪些软件
        ls /usr/bin | head -20
        #退出容器
        exit
        





