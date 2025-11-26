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
# 三、构建自己的容器
## 1. Singularity基础知识
### build可以生产两种不同格式的容器
* SIF（Singularity Image File）：压缩后的只读的Singularity镜像文件，是生产使用的主要形式。确保了容器的可复现性和验证性。
* Sandbox ：可写的容器存在形式，是文件系统中的一个目录，常用于开发或者创建自己的容器，是开发使用的主要形式。
### 两种容器格式之间的转换
* sif 转 sandbox（只读 → 可写）

        #这会创建一个名为 my_sandbox 的目录，您可以在里面自由安装软件、修改配置。
        #将只读的 .sif 文件转换为可写的沙盒目录
        singularity build --sandbox ./my_sandbox/ ubuntu.sif
* sandbox 转 sif

        #将定制好的环境固化为一个便携的、不可变的镜像文件，便于分发和部署。
        #将沙盒目录打包回只读的 .sif 文件
        singularity build final_container.sif ./my_sandbox/
## 2. 构建自定义容器
### Docker vs Singularity 构建机制对比：
Singularity采用单次构建机制，相比Docker的分层缓存构建，在调试阶段效率较低。* 任何一步出错，都需要从头开始执行整个构建过程. 为此我们推荐三阶段工作流：
* 沙盒调试 - 在可写环境中交互式验证配置
* 配方固化 - 将成功配置写入Definition File
* 最终构建 - 一次性生成生产就绪的SIF镜像 这种流程既保证了开发调试的灵活性，又确保了最终镜像的稳定性和可复现性。
### 构建定制化容器——OFF-PEAK
（1）Sandbox 实验阶段
* **sif 转 sandbox** 通过以下命令，将只读的 ubuntu.sif 镜像转换为一个名为 ubuntu_sandbox 的可写目录，以便在容器内自由安装软件或修改配置。
  
        singularity build --sandbox ./ubuntu_sandbox/ ubuntu.sif
* **进入可写的沙盒环境** 通过以下命令，以可写模式启动一个交互式 Shell，进入之前构建的 ubuntu_sandbox 沙盒容器，允许您直接在其中安装软件或修改文件。

        singularity shell --writable ./ubuntu_sandbox/
* **基础系统配置**
    * **设置环境变量** 告诉 apt 工具以非交互模式运行，安装过程中不会弹出任何需要用户输入的对话框（如时区选择、服务配置等），实现全自动安装

            Singularity> export DEBIAN_FRONTEND=noninteractive
    * **设置语言环境** 设置系统语言和字符编码为 C.UTF-8，避免因语言环境导致的警告信息，确保命令行输出稳定一致

            Singularity> export LANG=C.UTF-8 LC_ALL=C.UTF-8


        





