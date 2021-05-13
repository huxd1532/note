## 参考资料：
* linux图形子系统：http://www.wowotech.net/graphic_subsystem/graphic_subsystem_overview.html
* X11协议还要：https://blog.csdn.net/michael2012zhao/article/details/3961261


## 一、xrandr
### 1、设置屏幕的亮度
* 查看显示设备: ```xrandr | grep -v disconnected | grep connected```
* 设置：```xrandr --output LVDS --brightness 0.5```

### 2、设置分辨率
#### 方法一
* ```xrandr --output eDP1 --mode 1920x1080 ```
* --output:指定显示器。 
* --mode:指定一种有效的分辨率。 
* --rate:指定刷新率
#### 方法二
* ```xrandr  -s 1920x1080```

### 3、添加可选分辨率
* a、创建 modeline:
```
命令：
cvt 1280  1024  60

结果：
# 1280x1024 59.89 Hz (CVT 1.31M4) hsync: 63.67 kHz; pclk: 109.00 MHz
Modeline "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync
```
* b、使用newmode创建一个mode

 ```
 命令：
 xrandr --newmode "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync

 注：参数就是上面的modeline
 ```

* c、新建模式，将新模式添加至当前输出设备：
```
xrandr --addmode eDP1 1280x1024_60.00
```

### 4、双屏幕设置
*  a、设置主屏

```
xrandr --auto --output eDP1 --primary
```

* b、复制模式
```
xrandr --auto --output eDP1 --pos 0x0 --mode 1920x1080 --output HDMI1 --same-as eDP1
```

* c、扩展模式
```
xrandr --auto --output eDP1 --pos 0x0 --mode 1920x1080 --primary --output HDMI1 --mode 1024x768 --right-of eDP1
```

* d、单屏模式
```
xrandr --output eDP1 --pos 0x0 --mode 1920x1080 --primary --output VGA1 --off
```

* 注：如果需要永久保存配置，可以通过更改/etc/X11/xorg.conf或者/etc/X11/xorg.conf.d/xxx 进行保存


## 5、AMD显卡从Radeon驱动更换到AMD驱动
https://wikidev.uniontech.com/index.php?title=%E6%98%BE%E5%8D%A1%E9%A9%B1%E5%8A%A8