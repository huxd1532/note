## 一、NVIDIA Optimus 之 prime
* 参考链接：https://download.nvidia.com/XFree86/Linux-x86_64/435.17/README/primerenderoffload.html

* prime-run: https://gist.github.com/abenson/a5264836c4e6bf22c8c8415bb616204a




## 二、NVIDIA Optimus 之 只使用nvidia独显
### 配置方法：
* 添加 **/etc/X11/xorg.conf.d/10-nvidia-drm-outputclass.conf** 配置文件， 内容如下：
``````
Section "OutputClass"
    Identifier "intel"
    MatchDriver "i915"
    Driver "modesetting"
EndSection

Section "OutputClass"
    Identifier "nvidia"
    MatchDriver "nvidia-drm"
    Driver "nvidia"
    Option "AllowEmptyInitialConfiguration"
    Option "PrimaryGPU" "yes"
    ModulePath "/usr/lib/nvidia/xorg"
    ModulePath "/usr/lib/xorg/modules"
EndSection
``````

* 新建设置脚本 **/etc/lightdm/display_setup.sh**
``````
#!/bin/sh
xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto

chmod +x /etc/lightdm/display_setup.sh
``````

* 修改lightdm配置
``````
/etc/lightdm/lightdm.conf
[Seat:*]
display-setup-script=/etc/lightdm/display_setup.sh
``````

* 重启系统

* 测试1
``````
export __NV_PRIME_RENDER_OFFLOAD=1
export __GLX_VENDOR_LIBRARY_NAME=nvidia
glxinfo | grep vendor

运行结果：
server glx vendor string: NVIDIA Corporation
client glx vendor string: NVIDIA Corporation
OpenGL vendor string: NVIDIA Corporation
``````


https://gitee.com/deepinwiki/wiki/blob/master/linux%E9%A9%B1%E5%8A%A8%E7%AE%A1%E7%90%86.md

https://gitee.com/deepinwiki/wiki/

## 三、NVIDIA Optimus 之 prime-offload 
* 增加配置文件“70-nvidia.conf”,内容如下：
```
Section "ServerLayout"
  Identifier "layout"
  Screen 0 "iGPU"
  Option "AllowNVIDIAGPUScreens"
EndSection

Section "Device"
  Identifier "iGPU"
  Driver "modesetting"
EndSection

Section "Screen"
  Identifier "iGPU"
  Device "iGPU"
EndSection

Section "Device"
  Identifier "dGPU"
  Driver "nvidia"
EndSection

```
* 参考file:///home/uos/code/nvidia-driver/nvidia-graphics-drivers-460.91.03/nvidia-graphics-drivers-460.91.03/NVIDIA-Linux-x86_64-460.91.03/html/randr14.html

## 四、NVIDIA Optimus 之 使用bemblebee
### １、安装
```sudo apt-get install bumblebee-nvidia primus primus-libs libgl1-nvidia-glx nvidia-settings```
```sudo apt-get install mesa-utils```

## 2、测试

### 1) ```optirun glxinfo |grep NVIDIA```
```
结果：
optirun glxinfo |grep NVIDIA
server glx vendor string: NVIDIA Corporation
OpenGL vendor string: NVIDIA Corporation
OpenGL core profile version string: 4.6.0 NVIDIA 418.152.00
OpenGL core profile shading language version string: 4.60 NVIDIA
OpenGL version string: 4.6.0 NVIDIA 418.152.00
OpenGL shading language version string: 4.60 NVIDIA
```
### 2) ```optirun glxgears -info```

### 3) ```primusrun glxinfo |grep NVIDIA```

### 4) 参考primusrun
* ```echo -ne 'Calue: /usr/lib/x86_64-linux-gnu/nvidia:/usr/lib/i386-linux-gnu/nvidia:/usr/lib/nvidia\0' | socat - UNIX-CONNECT:/var/run/bumblebee.socket && glxgears```
* ```export LD_LIBRARY_PATH=/usr/\$LIB/primus:/usr/lib/x86_64-linux-gnu/nvidia:/usr/lib/i386-linux-gnu/nvidia:/usr/lib/nvidia && glxinfo |grep NVIDIA```


## 五、470.32版构建问题
### 1、debhelper-compat 版本过高问题
*修改办法：修改debian/control 文件，把```debhelper-compat (= 13)```改成```debhelper-compat (= 12)```。

### 2、nvidia-kernel-kms 内核驱动编译不过
* 修改方法：
```
adding symlinks for nv-kernel.o_binary and nv-modeset-kernel.o_binary, pointing to nv-kernel-arm64.o_binary and nv-modeset-kernel.o_binary.
UOS should be able to resolve the problem locally by editing patches/nvidia-use-ARCH.o_binary.patch and patches/nvidia-modeset-use-ARCH.o_binary.patch in the DKMS package to add e.g.:

NVIDIA_BINARY_OBJECT-$(CONFIG_ARM64) += nv-kernel-arm64.o_binary
```

### 3、安装依赖问题
* 解决办法一：
```
#!/bin/bash

if [ "$(id -u)" -ne "0" ];then
    echo "Need root privileges."
    exit 1
fi


print_usage() {
	echo "Usage: install_clean.sh [amd64 | arm64] [install | clean]"
}

if [ $# -ne 2 ]; then
	echo "Params is invaild!"
	print_usage
	exit 1
fi

deb_dir=''

case $2 in
	"install")
		echo "@#@$1"
		if [[ "$1" == "amd64" ]]; then
			deb_dir='./amd64'
			dpkg -i ${deb_dir}/nvidia-installer-cleanup_20151021+13_amd64.deb 
			dpkg -i ${deb_dir}/glx-diversions_1.2.0_amd64.deb 
			dpkg -i ${deb_dir}/nvidia-legacy-check_418.152.00-1+rebuild_amd64.deb 
			dpkg -i ${deb_dir}/glx-alternative-nvidia_1.2.0_amd64.deb 
			dpkg -i ${deb_dir}/nvidia-alternative_470.42.01-1_amd64.deb
			dpkg -i ${deb_dir}/*.deb
		elif [[ "$1" == "arm64" ]]; then
			deb_dir='./arm64'
			apt-get install update-glx
			dpkg -i ${deb_dir}/nvidia-installer-cleanup_20151021+13_arm64.deb
			dpkg -i ${deb_dir}/glx-diversions_1.2.0_arm64.deb
             dpkg -i ${deb_dir}/glx-alternative-mesa_1.2.0_arm64.deb
			dpkg -i ${deb_dir}/glx-alternative-nvidia_1.2.0_arm64.deb
			dpkg -i ${deb_dir}/nvidia-alternative_470.42.01-1_arm64.deb
		    dpkg -i ${deb_dir}/*.deb
		else
			print_usage
		fi
		;;
	"clean")
		dpkg -l |grep nvidia |awk '{print $2}' |xargs apt-get  purge -y
		apt-get purge -y libxnvctrl0
		;;
	*)
		print_usage
		;;
esac

exit 0

```

* 解决办法二：
```
#!/bin/bash
if [ "$(id -u)" -ne "0" ];then
    echo "Need root privileges."
    exit 1
fi


print_usage() {
	echo "Usage: install_clean.sh [amd64 | arm64] [install | clean]"
}

set_debian_repo() {
	echo "deb http://ftp.de.debian.org/debian sid main contrib" >>/etc/apt/sources.list
	apt-get update
}

reset_debian_repo() {
	sed -i '/deb http\:\/\/ftp.de.debian.org\/debian sid main contrib/d' /etc/apt/sources.list
	apt-get update
}


if [ $# -ne 2 ]; then
	echo "Params is invaild!"
	print_usage
	exit 1
fi

deb_dir=''

case $2 in
	"install")
		set_debian_repo
		apt-get install -y nvidia-driver nvidia-smi
		reset_debian_repo
		if [[ "$1" == "amd64" ]]; then
			deb_dir='./amd64'
			dpkg -i ${deb_dir}/libxnvctrl0_460.32.03-1_bpo10+1_amd64.deb
			dpkg -i ${deb_dir}/nvidia-settings_460.32.03-1_bpo10+1_amd64.deb
		elif [[ "$1" == "arm64" ]]; then
			deb_dir='./arm64'
			dpkg -i ${deb_dir}//libxnvctrl0_460.32.03-1_bpo10+1_arm64.deb
			dpkg -i ${deb_dir}/nvidia-settings_460.32.03-1_bpo10+1_arm64.deb
		else
			print_usage
		fi
		;;
	"clean")
		dpkg -l |grep nvidia |awk '{print $2}' |xargs apt-get  purge -y
		apt-get purge -y libxnvctrl0
		;;
	*)
		print_usage
		;;
esac

exit 0
```

## 六、驱动显卡显卡型号查询
* https://wiki.debian.org/NvidiaGraphicsDrivers

## 七、国产Linux系统deepin双显卡笔记本安装NVIDIA显卡驱动教程啰嗦版（适合小白）
* https://www.bilibili.com/read/cv3749226/