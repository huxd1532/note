## 一、查看xserver版本
* ```X -version```

## 二、参考资料
* https://www.x.org/wiki/
* inputlib: https://www.x.org/releases/current/doc/libXi/inputlib.html
* xorg新手开发指南：https://www.x.org/wiki/guide/


## 三、xorg配置
### 1、section
> 常见的section有以下几种：
* 1、Module: 被加载到 X Server 当中的模块 (某些功能的驱动程序)；
* 2、InputDevice: 包括输入的 1. 键盘的格式 2. 鼠标的格式，以及其他相关输入设备；
* 3、Files: 配置字型所在的目录位置等；
* 4、Monitor: 监视器的格式， 主要是配置水平、垂直的升级频率，与硬件有关；
* 5、Device: 这个重要，就是显卡芯片组的相关配置了；
* 6、Screen: 这个是在萤幕上显示的相关解析度与色彩深度的配置项目，与显示的行为有关；
* 7、ServerLayout: 上述的每个项目都可以重覆配置，这里则是此一 X server 要取用的哪个项目值的配置罗。

