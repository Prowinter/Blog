+++
title =  "Matlab离线版安装拓展支持包"
date = "2022-02-22"
weight = 10
categories = [
]
tags = [
  "notes",
]

+++
通过官方离线支持包安装Matlab拓展包。
<!--more-->


## 下载离线安装包

> [官方下载链接](https://ww2.mathworks.cn/support/install/support-software-downloader.html?s_tid=srchtitle)
> 
> [百度云离线下载链接](https://pan.baidu.com/s/1ojt2KHGVHp_lrBeYBZQvrw?pwd=k7i7)


## 包下载

windows,linux(需添加权限),mac用户直接打开刚才下载的安装包即可，中途需要登陆Mathword账号，登陆后勾选需要下载的离线包。

## 包安装

官方一共提供三种安装方式,

1. Copy downloaded software
2. Interactive Installation
3. Silent Installation

```shell

a. Windows
	cd DRIVE:\\<MATLAB_PATH>\\bin\\win64
    install_supportsoftware.exe -archives <path_to_download_folder> [-matlabroot DRIVE:\\MATLAB_PATH]


    For example:

cd C:\\MATLAB\\R2018b\\bin\\win64
install_supportsoftware.exe -archives C:\\Users\\jsmith\\Downloads\\MathWorks\\SupportPackages\\R2018b\\


b. Linux

    cd <MATLAB_PATH>/bin/glnxa64
    ./install_supportsoftware.sh -matlabroot <MATLAB_PATH> -archives <path_to_download_folder>
    
    For example:
    
    cd /opt/MATLAB/R2018b/bin/glnxa64
    ./install_supportsoftware.sh -matlabroot /opt/MATLAB/R2018b -archives /home/jdoe/Downloads/MathWorks/SupportPackages/R2018b

b. Mac

    cd <MATLAB_PATH>/bin/maci64
    ./install_supportsoftware.sh -matlabroot <MATLAB_PATH> -archives <path_to_download_folder>
    
    For example:
    
    cd /Applications/MATLAB_R2018b.app/bin/maci64
    ./install_supportsoftware.sh -matlabroot /Applications/MATLAB_R2018b.app/ -archives /Users/jdoe/Downloads/MathWorks/SupportPackages/R2018b

```