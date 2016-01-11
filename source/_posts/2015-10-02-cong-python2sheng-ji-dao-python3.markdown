---
layout: post
title: "从Python2升级到Python3"
date: 2015-10-02 15:50
comments: true
categories: python
---

目前Mac OS上面自带的Python是2.7版本的，最好不要卸载，因为很多系统内部的应用要用，所以就只能在不卸载2.7的情况下安装Python3。

###0，关于两种Python版本共存的问题

首先，要明白在终端运行Python的时候，是运行的`/usr/local/bin/python`。

```
~ xujin$which python
/usr/local/bin/python
```
然后，看看这个文件的类型，发现这仅仅是一个链接。因而，要改变Python的版本，只需要修改`/usr/local/bin/python`文件的的链接到就好。

```
~ xujin$ls -l /usr/local/bin/python
lrwxr-xr-x  1 root  admin  67 10  2 15:53 /usr/local/bin/python -> /System/Library/Frameworks/Python.framework/Versions/2.7/bin/python
```

<!--more-->

###1，首先下载安装Python3

有两种方法，很简单。我用的第一种。

* 1，使用Homebrew，brew install python3，等待自动完成即可。
* 2，去Python官网下载安装包安装 。


###2，移动Python3到系统目录

* 首先，确认Homebrew下载的Python的安装位置，我的安装在了 `/usr/local/Cellar/python3`上，具体细节还要看版本。所以每次安装好了之后都要确认安装的位置。
* 然后，把下载的 Python 3.4 移到`/System/Library/Frameworks/Python.framework/Versions/3.4`。

```
mv /usr/local/Cellar/python3/3.4.1_1/Frameworks/Python.framework/Versions/3.4 /System/Library/Frameworks/Python.framework/Versions/`
```
截止到目前，`/System/Library/Frameworks/Python.framework/Versions/`文件夹中，会有所有的Python版本，如2.7，3.4，还有个Current代表当前版本。


###3，配置
然后是配置系统的各种链接2，这里参考了网上其他人写的shell脚本，只是稍有修改。

保存下面的脚本a.sh，并运行`./a.sh`，配置完成。


```
#!/bin/bash
sudo -i #得到超级权限
new_version="3.4"

PYPATH=/System/Library/Frameworks/Python.framework/Versions/"$new_version"
#第2步改变用户目录的用户组
echo "chown.."
chown -R root:wheel ${PYPATH}
#第3步 删除原来的链接
echo "del.."
rm /System/Library/Frameworks/Python.framework/Versions/Current
#第4步重新链接到最新版本的python
echo "ln.."
ln -s ${PYPATH} /System/Library/Frameworks/Python.framework/Versions/Current
#第5步删除旧的命令符号链接
echo "rm.."
rm /usr/bin/{pydoc,python,pythonw,python-config}
echo "ln bin.."
#第6步重新建立新的命令符号链接
ln -s ${PYPATH}/bin/pydoc"$new_version" /usr/bin/pydoc
ln -s ${PYPATH}/bin/python"$new_version" /usr/bin/python
ln -s ${PYPATH}/bin/pythonw"$new_version" /usr/bin/pythonw
ln -s ${PYPATH}/bin/python"$new_version"m-config /usr/bin/python-config

python_param_list=`cd /usr/local/bin && ls -al |grep "Python"|awk 'ORS=" " {print $9}'`
#第7步修复其他链接
for i in $python_param_list;do
  echo "info: $i"
  rm -f /usr/local/bin/${i}
  ln -sv /System/Library/Frameworks/Python.framework/Versions/"$new_version"/bin/${i} /usr/local/bin/${i}
done

#第8步.环境变量要修改为最新的版本号
echo 'export PATH=/System/Library/Frameworks/Python.framework/Versions/"$new_version"/bin:${PATH}' >> ~/.bashrc

exit #退出超级权限
```

###4，完成
此时新开一个终端，输入`Python --version`，会得到3.4的结果。如果想回到2.7，只要更改shell脚本中的new_version为2.7即可。



#####参考资料
> [csdn](http://blog.csdn.net/wirelessqa/article/details/23261723)
