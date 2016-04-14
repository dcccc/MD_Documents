##量子化学服务器配置

###Linux系统的安装
自行百度

###环境配置
在确定连接上了网络后，就可以开始配置环境了。

1.  进入yum源目录

```bash
[root@node21 ~]$ cd /etc/yum.repos.d 
```
2.  备份系统自带的yum源

```bash
[root@node21 ~]$ mv CentOS-Base.repo CentOS-Base.repo.bak
```

3.  下载163yum源并使其生效

```bash
[root@node21 ~]$ wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
yum makecache
```

4.  安装基本依赖环境

```bash
[root@node21 ~]$ yum groupinstall "Development Tools"
```

5. gcc编译器的安装

```bash
[root@node21 ~]$ yum install gcc
```

###Intel编译器的安装
这里安装的是2013版，为parallel_studio_xe_2013_update2

解压文件

```bash
[root@node21 ~]$ tar -zxf  parallel_studio_xe_2013_update2.tgz
```

进入到parallel_studio_xe_2013_update2中，开始安装

```bash
[root@node21 ~]$ ./install.sh
```
出现

```
Step no: 1 of 7 | Welcome
--------------------------------------------------------------------------------
Welcome to the Intel(R) Parallel Studio XE 2013 Update 2 for Linux* installation program.
--------------------------------------------------------------------------------

You will complete the steps below during this installation:
Step 1 : Welcome
Step 2 : License
Step 3 : Activation
Step 4 : Intel(R) Software Improvement Program
Step 5 : Options
Step 6 : Installation
Step 7 : Complete
--------------------------------------------------------------------------------
Press "Enter" key to continue or "q" to quit:
```

直接按“Enter”

若出现

```
Step no: 1 of 7 | Options > Missing Optional Pre-requisite(s)
--------------------------------------------------------------------------------
There are one or more optional unresolved issues. It is highly recommended to
resolve them all before you continue the installation. You can fix them without
exiting from the installation and re-check. Or you can quit from the installation, fix them and run the installation again.
--------------------------------------------------------------------------------
Missing optional pre-requisites
-- Intel(R) Composer XE 2013 Update 2 for Linux*: unsupported OS
--------------------------------------------------------------------------------
1. Skip missing optional pre-requisites [default]
2. Show the detailed info about issue(s)
3. Re-check the pre-requisites
h. Help
b. Back to the previous menu
q. Quit
--------------------------------------------------------------------------------
Please type a selection or press "Enter" to accept default choice [1]:
```
直接按“Enter”

接下来输入“accept”并“Enter”

```
Step no: 3 of 7 | Activation
--------------------------------------------------------------------------------
If you have purchased this product and have the serial number and a connection to the internet you can choose to activate the product at this time. Activation is a secure and anonymous one-time process that verifies your software licensing rights to use the product. Alternatively, you can choose to evaluate the product or defer activation by choosing the evaluate option. Evaluation software will time out in about one month. Also you can use license file, license manager, or remote activation if the system you are installing on does not have internet access activation options.
--------------------------------------------------------------------------------
1. I want to activate my product using a serial number [default]
2. I want to evaluate my product or activate later
3. I want to activate either remotely, or by using a license file, or by using a license manager
h. Help
b. Back to the previous menu
q. Quit
--------------------------------------------------------------------------------
Please type a selection or press "Enter" to accept default choice [1]:
```

输入“3”并“Enter”

```
Step no: 3 of 7 | Activation > Advanced activation
--------------------------------------------------------------------------------
You can use license file, license manager, or the system you are installing on does not have internet access activation options.
--------------------------------------------------------------------------------
1. Use a different computer with internet access [default]
2. Use a license file
3. Use a license server
h. Help
b. Back to the previous menu
q. Quit
--------------------------------------------------------------------------------
Please type a selection or press "Enter" to accept default choice [1]: 

```

输入“2”并“Enter”

```
Note: Press "Enter" key to back to the previous menu.
Please type the full path to your license file(s):
```

输入license的路径并“Enter”

接下问是否加入改进，选择“2”

接下来直接“Enter”，默认安装到/opt/intel目录下

再接下来直接安装，安装完成后直接退出就可以了

配置环境变量

```
source /opt/intel/composer_xe_2013.2.146/bin/ifortvars.sh ia32
source /opt/intel/composer_xe_2013.2.146/bin/ifortvars.sh intel64
source /opt/intel/composer_xe_2013.2.146/bin/iccvars.sh  ia32
source /opt/intel/composer_xe_2013.2.146/bin/iccvars.sh  intel64
export PATH=/opt/intel/composer_xe_2013.2.146/bin:$PATH
```
将上面的加入到自己用户目录下的.bashrc中，然后

```bash
[root@node21 ~]$ source .bashrc
```

测试安装是否成功

```bash
[root@node21 ~]$ ifort -v
ifort version 15.0.1
```
出现以上“ifort version 15.0.1”，表明安装成功




###openmpi的安装

解压文件

```bash
[root@node21 ~]$ tar -zxf openmpi-root.tar.gz
```

进入目录，开始

```bash
[root@node21 ~]$ cd  openmpi-root
[root@node21 ~]$ ./configure --prefix="/opt/openmpi" CC=icc CXX=icpc F77=ifort FC=ifort
```

build并安装，“make”后面可以加上“-j8”，表示8核并行编译安装
```bash
[root@node21 ~]$ make 
[root@node21 ~]$ make install
```

安装完成后，将下面加入到自己用户目录下的.bashrc中

```
export PATH=/opt/openmpi/bin:$PATH
export LD_LIBRARY_PATH=/opt/openmpi/lib:$LD_LIBRARY_PATH
```

并

```bash
[root@node21 ~]$ source .bashrc
```

接下来

```bash
[root@node21 ~]$ mpirun
```

若出现

```
mpirun (Open MPI) 1.5

Usage: mpirun [OPTION]...  [PROGRAM]...
Start the given program using Open RTE

.........
```

表明安装成功。

###pwscf的安装

从官网上下载espresso-5.x.0.tar.gz和neb-5.x.0.tar.gz
解压文件，并将neb移动到espresso-5.x.0文件夹中

```bash
[root@node21 ~]$ tar -zxf espresso-5.x.0.tar.gz
[root@node21 ~]$ tar -zxf neb-5.x.0.tar.gz
[root@node21 ~]$ mv neb-5.x.0 espresso-5.x.0/

```

开始配置安装

```bash
[root@node21 ~]$ cd espresso-5.x.0
[root@node21 ~]$ ./configure

```

完成后

```bash
[root@node21 ~]$ make all

```
安装完成

将下面加入到自己用户目录下的.bashrc中

```
export PATH=/xxx/espresso-5.x.0/bin:$PATH

```
并

```bash
[root@node21 ~]$ source .bashrc
```

接下来

```bash
[root@node21 ~]$ pw.x
```
若出现


```
     Program PWSCF v.5.0.1 starts on 15Apr2016 at  0:34:34

     This program is part of the open-source Quantum ESPRESSO suite
     for quantum simulation of materials; please cite
         "P. Giannozzi et al., J. Phys.:Condens. Matter 21 395502 (2009);
          URL http://www.quantum-espresso.org",
     in publications or presentations arising from this work. More details at
     http://www.quantum-espresso.org/quote.php

     Parallel version (MPI), running on     1 processors
.........
```
表面安装成功


###vasp安装

####1.安装GotoBLAS

```bash
[root@node21 ~]$ tar -zxf GotoBLAS2-1.13_bsd.tar.gz
[root@node21 ~]$ cd GotoBLAS2-1.13_bsd
[root@node21 ~]$ ./quickbuild.64bit
```

若出现

```
../kernel/x86_64/gemm_ncopy_4.S:192: Error: undefined symbol RPREFETCHSIZE' in operation 
../kernel/x86_64/gemm_ncopy_4.S:193: Error: undefined symbol RPREFETCHSIZE' in operation 
../kernel/x86_64/gemm_ncopy_4.S:194: Error: undefined symbol RPREFETCHSIZE' in operation 
../kernel/x86_64/gemm_ncopy_4.S:195: Error: undefined symbol RPREFETCHSIZE' in operation
```
则

```bash
[root@node21 ~]$ gmake clean
[root@node21 ~]$ make BINARY=64 TARGET=NEHALEM
```
安装完成

####2.vasp的安装

在/opt/intel/composer_xe_2013.2.146/mkl/interfaces/fftw3xf 目录下执行

```bash
[root@node21 ~]$ make libintel64
```
生成链接库

于/opt/intel/composer_xe_2013.2.146/mkl/include/fftw目录下把fftw3.f拷贝到你编译vasp的目录下

1.  安装VASP.lib

修改makefile

```bash
[root@node21 ~]$ cd vasp.lib
[root@node21 ~]$ cp makefile.linux_ifc_P4 makefile
```

将

```
.........
# C-preprocessor
CPP     = gcc -E -P -C $*.F >$*.f
FC=ifc

CFLAGS = -O
FFLAGS = -O0 -FI
FREE   =  -FR
.........
```
中的“FC=ifc”改为“FC=ifort”，接着


```bash
[root@node21 ~]$ make
```
完成后，在目录下生成libdmy.a文件


2.  安装vasp

修改makefile

```bash
[root@node21 ~]$ cd vasp.5.2
[root@node21 ~]$ cp makefile.linux_ifc_P4 makefile
```

makefile修改为

```
SUFFIX=.f90


FC=ifort

FCL=$(FC)




CPP_ =  ./preprocess <$*.F | /usr/bin/cpp -P -C -traditional >$*$(SUFFIX)

FFLAGS =  -FR -lowercase -assume byterecl -heap-arrays 64


OFLAG=-O2 -ip 

OFLAG_HIGH = $(OFLAG)
OBJ_HIGH = 
OBJ_NOOPT = 
DEBUG  = -FR -O0
INLINE = $(OFLAG)


MKL_ROOT=/opt/intel/composer_xe_2013.2.146/mkl
MKL_PATH=$(MKLROOT)/lib/intel64

MKL_FFTW_PATH=$(MKLROOT)/interfaces/fftw3xf/


BLAS=  /opt/GotoBLAS2/libgoto2.so


LAPACK= ../vasp.5.lib/lapack_double.o





LIB  = -limf -lm -L../vasp.5.lib -ldmy \
     ../vasp.5.lib/linpack_double.o $(LAPACK) \
     $(BLAS)


LINK    = 



FFT3D   = fft3dfurth.o fft3dlib.o






FC=mpif90
FCL=$(FC)  -mkl



CPP    = $(CPP_) -DMPI  -DHOST=\"LinuxIFC\" -DIFC \
     -DCACHE_SIZE=4000 -DPGF90 -Davoidalloc -DNGZhalf \
     -DMPI_BLOCK=8000 \
     -DRPROMU_DGEMV  -DRACCMU_DGEMV



BLACS= -L$(MKL_PATH) -lmkl_blacs95_lp64

SCA= /opt/intel/composer_xe_2013.2.146/mkl/lib/intel64/libmkl_scalapack_lp64.a  /opt/intel/composer_xe_2013.2.146/mkl/lib/intel64/libmkl_blacs_intelmpi_lp64.a

LAPACK=-L$(MKL_PATH) -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread




LIB     = -L../vasp.5.lib -ldmy  \
      ../vasp.5.lib/linpack_double.o $(LAPACK) \
      $(SCA) $(BLAS)


FFT3D   = fftmpi.o fftmpi_map.o fft3dfurth.o fft3dlib.o 

INCS = -I$(MKLROOT)/include/fftw


BASIC=   symmetry.o symlib.o   lattlib.o  random.o   
.........
```

其中，主要修改的地方为intel编译器的库位置，以及GotoBLAS2库位置，即

编译器        第8行与第58行
mkl库位置     第27行与第72行
GotoBLAS2库   第33行


安装

```bash
[root@node21 ~]$ cd make
```

完成后会在目录下生成名为“vasp”的可执行文件

keen可能需要新建一个任务，测试是否安装成功


###lammps的安装

lammps安装最为简单

```bash
[root@node21 ~]$ tar -zxf lammps-stable.tar.gz
[root@node21 ~]$ cd lammps-stable/src
[root@node21 ~]$ make mpi

```

安装完成


###Material studio的安装

解压文件，进入目录，开始安装

```bash
[root@node21 ~]$ ./install

```
一路next安装完成后，开始安装license

```bash
[root@node21 ~]$ cd  /xxx/accelrys/LicensePack/etc/
[root@node21 ~]$ source lp_profile 

```

修改license文件xxx.lic，将“hostname”改为(本机名)，安装license

```bash
[root@node21 ~]$ lp_install  xxxx.lic
```

修改 /etc/hosts，在文件后加上一行
```
192.168.0.xx(本机IP) node21(本机名) node21(本机名)
```

重启gateway

```bash
[root@node21 ~]$   ./xxx/accelrys/MaterialsStudio8.0/etc/Gateway/msgateway_control_18888 restart
```

复制msgateway_control_18888，并将其加入开机启动项中

```bash
[root@node21 ~]$  cp /opt/accelrys/MaterialsStudio8.0/etc/Gateway/msgateway_control_18888    /etc/rc.d/init.d/msgateway_control_18888
[root@node21 ~]$  chkconfig --add /etc/rc.d/init.d/msgateway_control_18888
```

Material studio安装完成


