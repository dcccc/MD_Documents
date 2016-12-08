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
[root@node21 ~]$ yum makecache
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

####使用intel编译器安装openmpi
解压文件

```bash
[root@node21 ~]$ tar -zxf openmpi-root.tar.gz
```

进入目录，开始

```bash
[root@node21 ~]$ cd  openmpi
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

####使用gcc编译器安装openmpi

与使用intel编译器步骤大致相同，所不同的是在配置环境时，选择的编译器是gcc，即

```bash
[root@node21 ~]$ ./configure --prefix="/opt/openmpi" CC=gcc CXX=g++ F77=gfortran FC=gfortran
```
其它的步骤是一样的

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
FC=ifc          #修改

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


FC=ifort       #需修改

FCL=$(FC)




CPP_ =  ./preprocess <$*.F | /usr/bin/cpp -P -C -traditional >$*$(SUFFIX)

FFLAGS =  -FR -lowercase -assume byterecl -heap-arrays 64


OFLAG=-O2 -ip 

OFLAG_HIGH = $(OFLAG)
OBJ_HIGH = 
OBJ_NOOPT = 
DEBUG  = -FR -O0
INLINE = $(OFLAG)


MKL_ROOT=/opt/intel/composer_xe_2013.2.146/mkl      #需修改
MKL_PATH=$(MKLROOT)/lib/intel64                     #需修改

MKL_FFTW_PATH=$(MKLROOT)/interfaces/fftw3xf/        #需修改


BLAS=  /opt/GotoBLAS2/libgoto2.so                   #需修改


LAPACK= ../vasp.5.lib/lapack_double.o





LIB  = -limf -lm -L../vasp.5.lib -ldmy \
     ../vasp.5.lib/linpack_double.o $(LAPACK) \
     $(BLAS)


LINK    = 



FFT3D   = fft3dfurth.o fft3dlib.o






FC=mpif90                  #需修改
FCL=$(FC)  -mkl



CPP    = $(CPP_) -DMPI  -DHOST=\"LinuxIFC\" -DIFC \
     -DCACHE_SIZE=4000 -DPGF90 -Davoidalloc -DNGZhalf \
     -DMPI_BLOCK=8000 \
     -DRPROMU_DGEMV  -DRACCMU_DGEMV



BLACS= -L$(MKL_PATH) -lmkl_blacs95_lp64              #需修改

SCA= /opt/intel/composer_xe_2013.2.146/mkl/lib/intel64/libmkl_scalapack_lp64.a  /opt/intel/composer_xe_2013.2.146/mkl/lib/intel64/libmkl_blacs_intelmpi_lp64.a                  #需修改

LAPACK=-L$(MKL_PATH) -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread               #需修改




LIB     = -L../vasp.5.lib -ldmy  \
      ../vasp.5.lib/linpack_double.o $(LAPACK) \
      $(SCA) $(BLAS)


FFT3D   = fftmpi.o fftmpi_map.o fft3dfurth.o fft3dlib.o 

INCS = -I$(MKLROOT)/include/fftw        #需修改


BASIC=   symmetry.o symlib.o   lattlib.o  random.o   
.........
```

其中，主要修改的地方是makefile中intel编译器的库位置，以及GotoBLAS2库位置，即

编译器        第8行与第58行
mkl库位置     第27行与第72行
GotoBLAS2库   第33行


安装

```bash
[root@node21 ~]$ make
```

完成后会在目录下生成名为“vasp”的可执行文件

需要新建一个任务，测试是否安装成功



###vasp过渡态功能的安装

与没有过渡态功能的安装相同，首先在/opt/intel/composer_xe_2013.2.146/mkl/interfaces/fftw3xf 目录下执行“make libintel64”，拷贝intel/composer_xe_2013.2.146/mkl/include/fftw目录下的fftw3.f，解压安装VASP.lib，之后开始vasp过渡态功能的安装

从网站“http://theory.cm.utexas.edu/vtsttools/”上下载vtstcode.tgz，vtstscripts.tgz文件，将文件解压到vasp的主目录下覆盖

进入vasp主目录，更改main.F文件，将2817行

```
CALL CHAIN_FORCE(T_INFO%NIONS,DYN%POSION,TOTEN,TIFOR, &
     LATT_CUR%A,LATT_CUR%B,IO%IU6)
```
更改为

```
CALL CHAIN_FORCE(T_INFO%NIONS,DYN%POSION,TOTEN,TIFOR, &
     TSIF,LATT_CUR%A,LATT_CUR%B,IO%IU6)
```

对于“hessian.F”, “dynconstr.F”, “dimer_heyden.F”, “gadget.F”这是个文件，保证在它们的头部有

```
USE main_mpi
USE base 
```

这两个

然后对于“dimer.F”, “chain.F”这两个文件，需要做一些修改


```bash
[root@node21 ~]$ sed -i 's/^\+#/#/' chain.F dimer.F
```


修改makefile

```bash
[root@node21 ~]$ cp makefile.linux_ifc_P4 makefile
```

在makefile中的“SOURCE=”一行中，在“chain.o”前添加以下内容

```
bfgs.o dynmat.o  instanton.o  lbfgs.o sd.o   cg.o dimer.o bbm.o \
fire.o lanczos.o neb.o  qm.o opt.o
```

其余的修改则与没有过渡态功能的安装一样

接下来就开始编译

```bash
[root@node21 ~]$ make
```
完成后会在目录下生成名为“vasp”的可执行文件

需要新建一个过渡态的任务，测试是否安装成功



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
[root@node21 ~]$ Accelrys/LicensePack/linux/bin
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

###Gaussian 09的安装


解压文件，并更改文件夹权限

```bash
[root@node21 ~]$ tar -zxf g09-x86.tar.bz2 
[root@node21 ~]$ chmod -R 750 g09
```

在g09文件夹中新建文件夹scrach，并更改权限

```bash
[root@node21 ~]$ mkdir g09/scrach 
[root@node21 ~]$ chmod -R 755 g09/scrach
```

将下面内容加入环境变量文件.bashrc中,并将yourname改为自己用户名名称

```
#gaussian09
export g09root=/home/yourusername
GAUSS_EXEDIR=$g09root/g09/
export GAUSS_SCRDIR=/home/yourusername/g09/scratch
LD_LIBRARY_PATH=$g09root/g09/:$LD_LIBRARY_PATH
PATH=$g09root/g09/:$PATH
export g09root GAUSS_EXEDIR GAUSS_SCRDIR LD_LIBRARY_PATH PATH
source $g09root/g09/bsd/g09.profile
```

使其生效

```bash
[root@node21 ~]$ source ~/.bahsrc
```

测试安装

```bash
[root@node21 ~]$ cd g09/tests/com
[root@node21 ~]$ g09 test1.com &
```

如果能正常运行，安装成功。

在安装之前，最好新建一个用户群组，将需要用Gaussian的户加入到群组中。


###nwchem的安装

####使用gcc编译器安装nwchem

在使用gcc编译器安装好openmpi后，在环境变量.bashrc文件中加入以下内容：

```bash
export NWCHEM_TOP=/opt/nwchem-6.6  #nwchem的目录位置
export NWCHEM_TARGET=LINUX64
export NWCHEM_MODULES=all
export USE_MPI=y
export USE_MPIF=y
export USE_MPIF4=y
export MPI_LOC=/opt/openmpi-gcc/openmpi/   #gcc编译的openmpi的位置
export MPI_LIB=/opt/openmpi-gcc/openmpi/lib  
export MPI_INCLUDE=/opt/openmpi-gcc/openmpi/include  
export LIBMPI="-lmpi_f90 -lmpi_f77 -lmpi -ldl -Wl,--export-dynamic -lnsl -lutil"
export MRCC_METHODS=y
export PYTHON_EXE=/usr/bin/python   #python的依赖
export PYTHONVERSION=2.7    
export USE_PYTHON64=yes
export PYTHONPATH=/usr/local/lib/python2.7/site-packages
export PYTHONHOME=/usr
export PYTHONLIBTYPE=a
export CCSDTQ=yes
export USE_INTERNALBLAS=y  #使用自带的数学库安装
```

source生效后，进入nwchem开始安装

```bash
[root@node21 ~]$ cd nwchem-6.6/src
[root@node21 ~]$ make nwchem_config  
[root@node21 ~]$ make -j12   
```

-j12 表示12核并行编译，与编译openmpi一样，速度快的多

编译完成，没有出错的话，在nwchem-6.6/bin/LINUX64目录下生成nwchem的可执行文件

新建test.nw文件，写入以下内容

```
title "Nitrogen cc-pvtz SCF geometry optimization"
geometry
n 0 0 0
n 0 0 1.08
end
basis
n library cc-pvtz
end
task scf optimize   
```
然后开始测试nwchen

```bash
[root@node21 ~]$ nwchen test.nw > test.out &            #单线程
[root@node21 ~]$ mpirun -np 4 test.nw > test.out &      #多线程  
```

mpirun 命令为gcc编译的openmpi
然后查看输出，正常说明安装成功。

####使用gcc编译器安装nwchem

与使用gcc编译器安装基本相同，在安装时将加入环境变量里面的openmpi的目录改为gcc编译的openmpi位置，同时去掉
```bash
export CCSDTQ=yes
```

开始安装

```bash
[root@node21 ~]$ cd nwchem-6.6/src
[root@node21 ~]$ make nwchem_config  
[root@node21 ~]$ make FC=ifort CC=icc -j12   
```

编译完成后，同样在nwchem-6.6/bin/LINUX64目录下生成nwchem的可执行文件。接下来就可以测试安装是否成功。

在实际安装过程中，我没有成功


###siesta的安装

siesta的安装需要intel编译器和openmpi

下载解压文件，进入目录

```bash
[root@node21 ~]$ tar -xzf siesta-4.0.tgz
[root@node21 ~]$ cd siesta-4.0/Obj    
```

开始安装

```bash
[root@node21 ~]$ sh ../Src/obj_setup.sh
[root@node21 ~]$ cd ../Src
[root@node21 ~]$ ./configure  --enable-mpi FC=mpif90   #mpif90为intel编译器编译的openmpi中的mpif90
[root@node21 ~]$ cp arch.make ../Obj
[root@node21 ~]$ cd ../Obj    
```

修改siesta-4.0-rc2/Obj的arch.make文件，主要修改BLAS、LAPACK、BLACS、SCALAPACK这几个数学库的位置，这里直接使用intel编译器自带的mkl库中有的

```
BLAS_LIBS=-L/opt/intel/composer_xe_2013.2.146/mkl/lib/intel64  -lmkl_blas95_lp64 -lmkl_intel_lp64
LAPACK_LIBS=-L/opt/intel/composer_xe_2013.2.146/mkl/lib/intel64  -lmkl_sequential -lmkl_lapack95_lp64 -lmkl_core
BLACS_LIBS=-L/opt/intel/composer_xe_2013.2.146/mkl/lib/intel64 -lmkl_blacs_openmpi_lp64
SCALAPACK_LIBS=-L/opt/intel/composer_xe_2013.2.146/mkl/lib/intel64 -lmkl_scalapack_lp64  
```

查看“FC=”这一行，看是否是“FC=mpif90”

修改完成后，开始编译安装

```bash
[root@node21 ~]$ make 
```

安装完成，没有报错的话，安装完成后会在Obj目录下生成siesta的课可执行文件
然后需要测试安装是否成功

新建文件Ag.fdf，写入一下内容(即siesta-4.0/Obj/Tests/ag/ag.fdf得文件内容)

```
#
# System which gives problems with split
# To see them, uncomment the PAO.FixSplitTable directive
#
SystemName          Ag test
SystemLabel         ag
NumberOfAtoms       1
NumberOfSpecies     1

Reparametrize.Pseudos T       # Options for more accuracy
Restricted.Radial.Grid F


XC.functional    GGA
XC.authors       PBE

Meshcutoff 100 Ry

%block ChemicalSpeciesLabel
1  47 Ag
%endblock ChemicalSpeciesLabel

# Full template for Basis parameters
#
Basis.Pressure 0.02 GPa         # As in Anglada et al

PAO.FixSplitTable T

#PAO.SoftDefault    T           # Global soft-confinement options
#PAO.SoftPotential $Global_Vsoft Ry    
#PAO.SoftInnerRadius $Global_Soft_Radius
#

PAO.BasisType    split
%block PAO.Basis                                   # Define Basis set
Ag   3 0.0700000 
n=5   0   2   S 0.1500000 E 4.5000000 -0.9000000  
4.9700000 0.000000
1.00000   1.000000 
n=5   1   1  E 2.9200000 -0.5000000
5.0100000
1.00000   
n=4   2   2 S 0.1500000 E 4.9400000 -0.5000000
5.1200000 0.000000       
1.00000   1.000000  
%endblock PAO.Basis

LatticeConstant     4.09 Ang
%block LatticeVectors
0.500  0.500  0.000
0.500  0.000  0.500
0.000  0.500  0.500
%endblock LatticeVectors

%block AtomicCoordinatesAndAtomicSpecies
  0.000000 0.0000 0.0000  1
%endblock AtomicCoordinatesAndAtomicSpecies
```	       

在http://departments.icmab.es/leem/siesta/Databases/Pseudopotentials/periodictable-intro.html下载Ag的GGA赝势文件Ag.psf文件，放在Ag.fdf同一目录下，开始测试

```bash
[root@node21 ~]$ siesta < Ag.fdf > Ag.out &                #单线程
[root@node21 ~]$ mpirun -np 4 siesta < Ag.fdf > Ag.out &   #多线程
```

然后查看输出，正常说明安装成功

以上的安装方法单线程正常运行，似乎多线程有问题。解决多线程的问题的话，似乎不能使用高版本的intel编译器，需要单独安装BLAS、LAPACK、BLACS、SCALAPACK这几个数学库。安装着几个库有点麻烦，这里没有尝试。





###Gamess的安装

这里使用intel编译器安装2016版gamess。

解压gamess

```bash
[root@node21 ~]$ tar -zxf 2016-Aug(R1).tar.gz

```
开始配置安装

```bash
[root@node21 ~]$ cd gamess
[root@node21 gamess]$ ./config

```
接下来会出现多步选项，选择如下

```
[enter]
linux64
[enter]          #在当前位置安装
[enter]          #在当前位置安装
[enter]          #版本号为00
ifort            #使用ifort编译gamess
13               #使用ifort版本号为13，可以通过"ifort -v"查看
[enter]   
[enter]
mkl              #使用mkl库
/opt/intel/mkl   #intel mkl库的位置
proceed
[enter]
[enter]
sockets          #不使用并行
no               #不尝试LIBCCHEM，这是用来通过nVidia的GPU加速HF和MP2任务用的，没有gpu加速卡，不需使用 

```

编译ddi

```bash
[root@node21 ~]$ cd ddi
[root@node21 ddi]$ ./compddi
[root@node21 ddi]$ mv  ddikick.x  ../      #将编译生成的ddikick.x移动到上一层目录，即gamess目录下
```

编译gamess

```bash
[root@node21 ~]$ cd ../
[root@node21 gamess]$ ./compall >& complie.log &  #后台编译，编译信息写入complie.log文件

```

编译需要较长的时间，待编译完成后若在gamess目录项没有生恨gamess.00.x，则需手动链接产生可执行文件

```bash
[root@node21 gamess]$ ./lked gamess 00

```

 配置运行环境

建立gamess目录下建立scr初始缓存文件夹

```bash
[root@node21 gamess]$ mkdir scr
```

对gamess目录下rungms文件作如下修改：

把set TARGET=sockets下面的三行改为

```
set SCR=/home/flw1/gamess/scr           #scr的绝对路径
set USERSCR=/home/flw1/gamess/scr       #scr的绝对路径
set GMSPATH=/home/flw1/gamess           #gamess的绝对路径
```
在
把set TARGET=sockets前面加上一行，每次计算开始是都删除上次计算的初始缓存文件

```bash
rm -fr /home/flw1/gamess/scr/*
```

为多核计算，在rungms文件的"switch (`hostname`)"这行下面插入以下内容

```
         case xxx:              # xxx 为计算机的名称，可用hostname命令查看
            set NNODES=1
            set HOSTLIST=(`hostname`:cpus=$NCPUS)
            breaksw
````

至此，安装配置完成。不过还需要测试安装是否成功


```bash
[root@node21 gamess]$ ./rungms test.gms.inp >& test.gms.out &       #单核
[root@node21 gamess]$ ./rungms test.gms.inp 00 4 >& test.gms.out &  #4核并行
```

若输出文件中出现以下错误

```
......
 DDI Process 0: error code 911
 ddikick.x: application process 0 quit unexpectedly.
 ddikick.x: Fatal error detected.
 The error is most likely to be in the application, so check for
 input errors, disk space, memory needs, application bugs, etc.
 ddikick.x will now clean up all processes, and exit...
 ddikick.x: Sending kill signal to DDI processes.
......
```

则有可能是输入文件的格式或内容有问题


###MOPAC2016的安装

解压文件MOPAC2016_for_CentOS-6.zip

```bash
[root@node21 flw1]$ unzip MOPAC2016_for_CentOS-6.zip
[root@node21 flw1]$ mv MOPAC  /opt/         #将目录移动到opt目录下
[root@node21 flw1]$ cd /opt/MOPAC
[root@node21 MOPAC]$ MOPAC2016.exe 11780405a29011200    激活MOPAC
```
至此MOPAC安装完成
接着可以将"alias mopac='/opt/mopac/MOPAC2016.exe'"加入超环境变量中，这样就可以使用mopac

提交计算的方式为

```bash
[root@node21 flw1]$ mopac test.mop.in &
```

