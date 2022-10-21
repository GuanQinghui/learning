**总体思路：**找一台可以联网的linux，下载docker的RPM依赖包而不进行安装（yum localinstall），将所有依赖的rpm环境打包好，再在无网环境中解压逐一安装

1. 在可联网的机器上下载rpm安装包

```
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo            #添加软件源信息

yum makecache fast       #更新yum缓存

mkdir -p /root/docker         #创建安装包下载目录

yum intsall docker-ce --downloadonly --downloaddir=/root/docker    
标准的yum命令提供了 --downloadonly(只下载，不安装) 的选项来达到这个目的
```

2. 将下载好的rpm安装包上传至无网环境中的机器

![image-20221021175038976](https://raw.githubusercontent.com/GuanQinghui/image-hosting/master/image-20221021175038976.png)

3. 将上传的安装包全部安装

```
rpm -ivh *.rpm           #也可以逐个安装，逐个安装需要注意安装的顺序
```

