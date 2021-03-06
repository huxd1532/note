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

## 四、NVIDIA Optimus 之 使用bemblebee
### １、安装
```sudo apt-get install bumblebee-nvidia primus primus-libs libgl1-nvidia-glx nvidia-settings```

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
