https://shimo.im/docs/hKgd3c9ghWgv8DtR

## 一、常用命令
### 1、设置分辨率
* cvt 1920 1080
* xrandr --newmode "1920x960_60.00"  152.00  1920 2032 2232 2544  960 963 973 996 -hsync +vsync
* xrandr --addmode eDP-1 "1920x960_60.00"
* xrandr --output  eDP-1 --mode   "1920x960_60.00"

### 2、查看显示器
* xrandr  --listmonitors 

### 3、主显示器设置
* xrandr  --output VGA-0 --auto  --primary 
* xrandr  --output HDMI-A-0 --auto  --primary 

### 4、设置扩展屏
* xrandr --output VGA-0 --right-of HDMI-A-0 --auto
* xrandr --output VGA-0 --left-of HDMI-A-0  --auto

### 5、设置复制屏
* xrandr --output VGA-0 --same-as HDMI-A-0  --auto
* xrandr --output VGA-0 --same-as HDMI-A-0  --mode  "1920x960"

### 6、关闭/打开显示器
* xrandr  --output HDMI-A-0  --off
* xrandr  --output HDMI-A-0 --auto

### 7、屏幕旋转
* xrandr  --output HDMI-A-0  --rotate left
* xrandr  --output HDMI-A-0  --rotate right
* xrandr  --output HDMI-A-0  --rotate normal
* xrandr  --output HDMI-A-0  --rotate inverted

### 8、查看屏幕亮度
* xrandr --verbose | grep Brightness

### 9、调节背光
* xrandr --output eDP --brightness 0.5
* xrandr --output VGA --brightness 0.75
* xrandr --output HDMI --brightness 0.5

### 10、关闭屏幕
* xset dpms force off 

## 二、实现原理
### 1、xrand作为客户端，所有设置功能最终有Xserver完成。在Xserver中提供这些请求服务的是xrand扩展模块：
* 主请求码：140
* 次请求码：

| 次请求码 |  宏   |   描述   | 命令
|  :---  | :---  |  :---   |  :--- |
| 0 |    X_RRQueryVersion                 |   查询版本         | xrandr  --version  |
| 1 |    X_RROldGetScreenInfo             |   获取screen信息   | xrandr --screen  0 |
| 2 |    X_RR1_0SetScreenConfig           |   设置screen配置   | 无                  |
| 3 |    X_RROldScreenChangeSelectInput   |   修改输入设备      | 无                  |
| 4 |    X_RRSelectInput                  |   NULL            | 无                  |
| 5 |    X_RRGetScreenInfo                |   NULL            | 无                  |
| 6 |    X_RRGetScreenSizeRange           |   NULL            | 无                  |
| 7 |    X_RRSetScreenSize                |   NULL            | 无                  |
| 8 |    X_RRGetScreenResources           |   NULL            | 无                  |
| 9 |    X_RRGetOutputInfo                |   NULL            | 无                  |
|10 |    X_RRListOutputProperties         |   NULL            | 无                  |
|11 |    X_RRQueryOutputProperty          |   NULL            | 无                  |
|12 |    X_RRConfigureOutputProperty      |   NULL            | 无                  |
|13 |    X_RRChangeOutputProperty         |   NULL            | 无                  |
|14 |    X_RRDeleteOutputProperty         |   NULL            | 无                  |
|15 |    X_RRGetOutputProperty            |   NULL            | 无                  |
|16 |    X_RRCreateMode                   |   NULL            | 无                  |
|17 |    X_RRDestroyMode                  |   NULL            | 无                  |
|18 |    X_RRAddOutputMode                |   NULL            | 无                  |
|19 |    X_RRDeleteOutputMode             |   NULL            | 无                  |
|20 |    X_RRGetCrtcInfo                  |   NULL            | 无                  |
|21 |    X_RRSetCrtcConfig                |   NULL            | 无                  |
|22 |    X_RRGetCrtcGammaSize             |   NULL            | 无                  |
|23 |    X_RRGetCrtcGamma                 |   NULL            | 无                  |
|24 |    X_RRSetCrtcGamma                 |   NULL            | 无                  |
|25 |    X_RRGetScreenResourcesCurrent    |   NULL            | 无                  |
|26 |    X_RRSetCrtcTransform             |   NULL            | 无                  |
|27 |    X_RRGetCrtcTransform             |   NULL            | 无                  |
|28 |    X_RRGetPanning                   |   NULL            | 无                  |
|29 |    X_RRSetPanning                   |   NULL            | 无                  |
|30 |    X_RRSetOutputPrimary             |   NULL            | 无                  |
|31 |    X_RRGetOutputPrimary             |   NULL            | 无                  |
|32 |    X_RRGetProviders                 |   NULL            | 无                  |
|33 |    X_RRGetProviderInfo              |   NULL            | 无                  |
|34 |    X_RRSetProviderOffloadSink       |   NULL            | 无                  |
|35 |    X_RRSetProviderOutputSource      |   NULL            | 无                  |
|36 |    X_RRListProviderProperties       |   NULL            | 无                  |
|37 |    X_RRQueryProviderProperty        |   NULL            | 无                  |
|38 |    X_RRConfigureProviderProperty    |   NULL            | 无                  |
|39 |    X_RRChangeProviderProperty       |   NULL            | 无                  |
|40 |    X_RRDeleteProviderProperty       |   NULL            | 无                  |
|41 |    X_RRGetProviderProperty          |   NULL            | 无                  |
|42 |    X_RRGetMonitors                  |   NULL            | 无                  |
|43 |    X_RRSetMonitor                   |   NULL            | 无                  |
|44 |    X_RRDeleteMonitor                |   NULL            | 无                  |
|45 |    X_RRCreateLease                  |   NULL            | 无                  |
|46 |    X_RRFreeLease                    |   NULL            | 无                  |

### 2、服务向量表
* ProcRandrVector
