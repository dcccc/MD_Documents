#centos安装pypy(含pypy下载地址)

今天ttlsa群友提问说pypy源码安装有问题，各种问题，有些人使用epel源yum安装，但是我发现我的epel没有pypy，在网上搜索了一番，找到了对应的rpm安装包，这边分享一下下载地址以及python与pypy的简单性能对比。

##1.安装pypy


在下载地址中包含各个版本的pypy，有centos、redhat、ubuntu、debian等等.自行选择，我这边是centos6，如下：

（注意安装顺序,libs一定要先安装）

```bash
# rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/pypy-libs-2.0.2-1.el6. x86_64.rpm
Retrieving http://dl.fedoraproject.org/pub/epel/6/x86_64/pypy-libs-2.0.2-1.el6. x86_64.rpm
warning: /var/tmp/rpm-tmp.DTV0uQ: Header V3 RSA/SHA256 Signature, key ID    0608b895: NOKEY
Preparing...                ########################################### [100%]
   1:pypy-libs              ########################################### [100%]
# rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/pypy-2.0.2-1.el6.  x86_64.rpm
Retrieving http://dl.fedoraproject.org/pub/epel/6/x86_64/pypy-2.0.2-1.el6.  x86_64.rpm
warning: /var/tmp/rpm-tmp.upV9H2: Header V3 RSA/SHA256 Signature, key ID    0608b895: NOKEY
Preparing...                ########################################### [100%]
   1:pypy                   ########################################### [100%]
# rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/pypy-devel-2.0.2-1.el6 .x86_64.rpm
Retrieving http://dl.fedoraproject.org/pub/epel/6/x86_64/pypy-devel-2.0.2-1.el6 .x86_64.rpm
warning: /var/tmp/rpm-tmp.YbOckH: Header V3 RSA/SHA256 Signature, key ID    0608b895: NOKEY
Preparing...                ########################################### [100%]
   1:pypy-devel             ########################################### [100%]
```

##2.测试pypy

```bash
# cat ttlsa_pypy.py
# -------------------------------
# DateTime：2013-10-08
# 站点: www.ttlsa.com
# -------------------------------
def check(num):
     a = list(str(num))
     b = a[::-1]
     if a == b:
         return True
     return False
 
 
def main():
    all = xrange(1,10**7)
    for i in all:
        if check(i):
            if check(i**2):
                print i,i**2
 
 
if __name__ == '__main__':
    main()
```