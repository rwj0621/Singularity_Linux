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
        ##--disable-cache：这是关键选项，意思是 "禁用缓存"。完全忽略本地缓存；强制从网络重新下载；下载全新的副本。
* 当你运行这个命令后：
    * 它会从 Singularity Library 下载 lolcow 镜像。
    * 在本地生成一个名为 lolcow_latest.sif 的 SIF 文件。
    * 您可以通过以下命令运行它。





