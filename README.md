# PIScope 的使用

本文主要简单介绍下PIScope的使用方法。

## 目录

0. [PISope安装](#PIScope安装)
0. [PIScope连接MDSPlus](#PIScope连接MDSPlus)
0. [PIScope简单可视化数据](#PIScope简单可视化数据)
0. [PIScope脚本式可视化数据](#PIScope脚本式可视化数据)

## PIScope安装

### Windows环境安装

> 1. 安装python，推荐选择[python2.7](https://www.python.org/downloads/windows/)，选择相应的版本并安装。
> 2. 安装MDSplus，推荐选择[stable版本](http://www.mdsplus.org/index.php/Latest_Windows_Distributions)，下载并安装。
> 3. 安装相应的python依赖包，这里选择清华pypi镜像安装：
```
  pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  pip install scipy -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  pip install PyPDF2 -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  pip install hgapi -i https://pypi.tuna.tsinghua.edu.cn/simple/
  pip install PyOpenGL -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  pip install H5py -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  pip install Pillow -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  pip install mdsplus -i https://pypi.tuna.tsinghua.edu.cn/simple/
  pip install netCDF4 -i https://pypi.tuna.tsinghua.edu.cn/simple/
  pip install matplotlib -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  pip install wxPython -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
```

### Ubuntu环境安装

> 1. 安装python

```sudo apt-get install python2.7```
> 2. 安装MDSplus，具体参看[安装教程](http://www.mdsplus.org/index.php/Latest_Ubuntu/Debian_Packages)。
```
  **注意：**os-selection为ubuntu版本，如：**Ubuntu14**，flavor选择**stable**或者**alpha**
  deb http://www.mdsplus.org/dist/os-selection/repo MDSplus flavor
  wget http://www.mdsplus.org/dist/mdsplus.gpg.key
  sudo apt-key add mdsplus.gpg.key
  sudo apt-get update
  sudo apt-get install mdsplus
```
> 3. 安装相应依赖包:

* 安装相应的ubuntu依赖包
```
  sudo apt-get install dpkg-dev
  sudo apt-get install build-essential
  sudo apt-get install python2.7-dev # 选择相应的版本
  sudo apt-get install libjpeg-dev
  sudo apt-get install libtiff-dev
  sudo apt-get install libsdl1.2-dev
  sudo apt-get install libgstreamer-plugins-base0.10-dev
  sudo apt-get install libnotify-dev
  sudo apt-get install freeglut3
  sudo apt-get install freeglut3-dev
  sudo apt-get install libsm-dev
  sudo apt-get install libgtk-3-dev
  sudo apt-get install libwebkitgtk-3.0-dev 
  sudo apt-get install libxtst-dev
```

* 安装python依赖包：
```
  sudo apt-get install python-numpy
  sudo apt-get install python-scipy
  sudo pip install PyPDF2 -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  sudo pip install hgapi -i https://pypi.tuna.tsinghua.edu.cn/simple/
  sudo pip install PyOpenGL -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  sudo pip install H5py -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  sudo pip install Pillow -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
  sudo pip install mdsplus -i https://pypi.tuna.tsinghua.edu.cn/simple/
  sudo pip install netCDF4==1.4.0 -i https://pypi.tuna.tsinghua.edu.cn/simple/
  sudo pip install matplotlib==2.2.3 --ignore-installed six -i https://pypi.tuna.tsinghua.edu.cn/simple/ 
```

**注意：**安装wxPython包时，pip镜像源没有Linux版本的,这里选择官方给的安装方法，安装wxPython时建议翻墙否则速度太慢。
```pip install -U -f https://extras.wxpython.org/wxPython4/extras/linux/gtk3/Ubuntu版本（如：ubuntu-16.04） wxPython```

### 运行PIScope

> 1. 下载PIScope
```
  git clone https://github.com/sshiraiwa/piScope.git
```
> 2. 启动PIScope
```
  cd $PISCOPE_DIR
  python python/piscope.py
```
## PIScope连接MDSPlus
1. 
