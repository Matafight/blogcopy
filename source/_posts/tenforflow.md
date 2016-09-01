title: 'tensorflow 安装及使用'
date: 2016-08-03 13:54:57
categories: tensorflow
tags: deep learning
---

**在ubuntu下安装tensorflow和使用tensorflow**
==============
1.安装cuda,cudnn
------------  
*a)*
ubuntu下安装需要先安装g++
```bash
sudo apt-get install build-essential
```
*b)*从navida 官网下载cuda的deb文件
从命令行安装：
```bash
sudo dpkg -i cuda-repo-***.deb
sudo apt-get update
sudo apt-get install cuda
```
之后需要在.bashrc 中设置cuda的环境变量
```bash
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda-7.5/lib64"
export CUDA_HOME=/usr/local/cuda-7.5
```
注意上述的命令中需要将cuda的相应版本改成自己下载的版本

*c)*
安装cudnn-v4
cudnn 的下载需要navidia的开发者账号，去注册一下就行，我的账号就是edu账号。
cudnn的具体安装方式参照的是tensorflow给出的安装指南

```bash
Uncompress and copy the cuDNN files into the toolkit directory. Assuming the toolkit is installed in /usr/local/cuda, run the following commands (edited to reflect the cuDNN version you downloaded):

tar xvzf cudnn-7.5-linux-x64-v4.tgz
sudo cp cudnn-7.5-linux-x64-v4/cudnn.h /usr/local/cuda/include
sudo cp cudnn-7.5-linux-x64-v4/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```
注意上面的cudnn的路径有点不正确，需要根据具体的文件路径和相应的版本更改命令。

*d)*关闭与nvidia 不兼容的nouveau
打开Terminal终端（Ctrl+Alt+T）
```bash
sudo edit /etc/modprobe.d/blacklist-nouveau.conf
```
在文件中写入：
```bash
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```
在terminal中执行
```bash
echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf
sudo update-initramfs -u
sudo reboot
```
*e)*
验证cuda的安装
```bash
cd /usr/local/cuda/samples/1_Utilities/deviceQuery
sudo make
sudo ./deviceQuery
result ：pass 则为安装成功。
```

2.安装tensorflow
-------------
使用官网的Anaconda的安装方式
```bash
conda create -n tensorflow python=2.7

source activate tensorflow

(tensorflow)$ export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-0.9.0-cp27-none-linux_x86_64.whl

(tensorflow)$ pip install --upgrade $TF_BINARY_URL
```
测试安装：参考官网

使用anaconda安装方式的特点是，在Anaconda中独立出来一个环境，名字叫做tensorflow，每次要使用tensorflow的时候，先切换到这个环境，然后再这个环境中安装和运行相关程序。环境里面的python 与 anaconda中的python是相互独立的。

注意这里提供的安装方法与可能与个人的电脑相关设置有所不同，仅供参考

参考:http://blog.csdn.net/songrotek/article/details/50770154

3.tensorflow 相关用法
----------------
https://github.com/Matafight/TFstudyNotes

to be continue...
