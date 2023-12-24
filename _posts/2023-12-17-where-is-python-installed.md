---
layout: post
title: python tmd 装哪里了？
---


## Python 是如何寻找包的

there is not just one python on your machine

if your python is $path_prefix/bin/python:
then search:
$path_prefix/lib
$path_prefix/lib/pythonX.Y/site-packages
pwd


>>> import sys
>>> sys.executable # current python path
'/usr/local/opt/python@3.11/bin/python3.11' 
>>> sys.path #search path
['', '/usr/local/Cellar/python@3.11/3.11.5/Frameworks/Python.framework/Versions/3.11/lib/python311.zip', '/usr/local/Cellar/python@3.11/3.11.5/Frameworks/Python.framework/Versions/3.11/lib/python3.11', '/usr/local/Cellar/python@3.11/3.11.5/Frameworks/Python.framework/Versions/3.11/lib/python3.11/lib-dynload', '/usr/local/lib/python3.11/site-packages']
>>> sys.prefix
'/usr/local/opt/python@3.11/Frameworks/Python.framework/Versions/3.11'


如果你的包的路径不存在上面列出的搜索路径列表里，可以把路径加到 PYTHONPATH 环境变量里
但需注意，避免把不同 Python 版本包的路径加到 PYTHONPATH 里, PYTHONPATH 里最好不要出现任何带 site-packages


## Python 是如何安装包的
现在用安装 Python 包基本是用的 pip，就算你是用 pipenv，poetry，底层依然是 pip，一律适用
pip ... vs python -m pip
是第一种方式使用的 Python 解释器是写在 pip 文件的 shebang 里的，一般情况下，如果你的 pip 路径是 $path_prefix/bin/pip，那么 Python 路径对应的就是 $path_prefix/bin/python
第二种方式则显式地指定了 Python 的位置。这条规则，对于所有 Python 的可执行程序都是适用的


## pip 中更改安装位置的选项
--prefix PATH，替换 $path_prefix 为给定的值
--root ROOT_PATH，在 $path_prefix 前面加上 ROOT_PATH，比如 --root /home/frostming，$path_prefix 就会从 /usr 变成 /home/frostming/usr
--target TARGET，直接指定安装位置到 TARGET

## 虚拟环境
虚拟环境就是为了隔离不同项目的依赖包，使他们安装到不同的路径下，以防止依赖冲突的问题。理解了 Python 是如何安装包的机制之后就不难理解虚拟环境（virtualenv, venv模块）的原理。其实，运行virtualenv myenv会复制一个新的 Python 解释器到myenv/bin下，并创建好myenv/lib，myenv/lib/pythonX.Y/site-packages等目录（venv模块不是用的复制，但结果基本一样）。执行source myenv/bin/activate以后会把myenv/bin塞到PATH前面，让这个复制出来的 Python 解释器最优先被搜索到。这样，后续安装包时，$path_prefix就会是myenv了，从而实现了安装路径的隔离。

## 脚本运行方式对搜索路径的影响

Python 找不找得到一个包，最直接的原因是 sys.path，而更进一步的原因是 sys.executable 的路径。程序写完了，我们总得需要运行它，不同的运行方法却有可能影响到 sys.path



ref:
https://frostming.com/2019/03-13/where-do-your-packages-go/


