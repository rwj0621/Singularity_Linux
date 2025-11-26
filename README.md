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


