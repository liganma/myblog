+++
title = '安装CUDA'
date = 2025Y-08M-29D
draft = false
+++

### [在ubuntu上安装typora](https://typora.io/#linux)
[在ubuntu上安装typora](https://typora.io/#linux)
### 安装CUDA
[cuda toolkits 12.8官网]（https://developer.nvidia.com/cuda-12-8-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=24.04&target_type=deb_local）
不要直接安装最新版本，可以先去看看pytorch支持到什么版本在选择；
cuda驱动有以下两种：
1. nvidia-open → 开源内核模块（GPL/MIT 许可证，源码可见，社区可修改）
2. cuda-drivers → 专有内核模块（二进制 blob，闭源，NVIDIA 官方完整功能）。
安装前务必 sudo apt remove --purge '^nvidia-*' 并重启，避免混合残留.个人觉得就用第一个开源的好了；
安装driversr如果报错“nvidia-kernel-common 版本冲突” ，先去nvidia-smi看看用的啥版本的驱动
把他们都卸载掉然后在重新安装：
```bash
sudo apt remove --purge nvidia-kernel-common-550 nvidia-kernel-common-580
sudo apt autoremove
清理可能损坏的包
sudo apt --fix-broken install
sudo dpkg --configure -a
再重新安装
sudo ubuntu-drivers autoinstall
```
### 安装cudnn

[官网]（https://developer.nvidia.com/cudnn-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=24.04&target_type=deb_local&Configuration=Full）


dpkg -l | grep nvidia
如果看到 nvidia-kernel-550（不带 -dkms）或 nvidia-modules-550 → 说明是 full。

tensorflow-gpu 这个包 在 2.11 之后就被官方废弃了
```python
# 安装官方 GPU 版（会自动拉 CUDA/cuDNN 运行时）
pip install --upgrade "tensorflow[and-cuda]"  # TF ≥2.15 推荐
```
### 安装pytorch

[pytorch官wang](https://pytorch.org/)

### 腾讯的图片修复项目
问题：pip install basicsr很慢卡住了？
打开调试模式看具体卡哪：pip install --verbose basicsr
发现应该改用 PEP-517 方式安装
####还有一个坑，就是需要先安装pytorch,否则还是会卡死
pip install torch torchvision

### 先确保构建工具最新
pip install -U pip setuptools wheel

### 强制使用 PEP-517 安装
```
pip install --use-pep517 basicsr --verbose
```
网速太慢的话可以使用proxychains工具

运行该项目时还有一个问题：
但PyTorch ≥2.0 的版本已经把 functional_tensor 模块合并进了 torchvision.transforms.functional
我不想给torchvision降级，台麻烦了，所以加个补丁：
找到报错的文件
/home/asd/anaconda3/envs/myenv001/lib/python3.10/site-packages/basicsr/data/degradations.py
```python
把第 8 行
from torchvision.transforms.functional_tensor import rgb_to_grayscale
改成
from torchvision.transforms.functional import rgb_to_grayscale
```


