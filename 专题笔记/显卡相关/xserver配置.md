## 一、查看xserver版本
* ```X -version```

## 二、参考资料
* https://www.x.org/wiki/
* inputlib: https://www.x.org/releases/current/doc/libXi/inputlib.html
* xorg新手开发指南：https://www.x.org/wiki/guide/

* Linux图像系统框架与X协议:https://blog.csdn.net/youyou1543724847/article/details/84192430


## 三、xorg
### １、xorg.conf 
* Module: 被加载到 X Server 当中的模块 (某些功能的驱动程序)；
* Files:  X系统使用的字体存放目录(字体的具体使用由FontConfig工具主持)
* InputDevice: 输入设备，如键盘鼠标的信息
* Monitor:显示器的设置，如分辨率，刷新率等
* Device:显示卡信息
* Screen:由Monitor和Device组装成一个Screen，表示由它们向这个Screen提供输出能力
* ServerLayout:将一个Screen和InputDevice组装成一个ServerLayout

### 2、X session
* X session是指X server启动后直到X server关闭之间的这段时间，这期间一切跟X相关的动作都属于X session的内容。 开启一个X session，也就是开始了图形界面的使用。在开启的过程中，Display Manager会对用户进行认证等等。这个开启过程要执行的一系列操作都可以在/etc/X11/Xsession以及/etc/X11/Xsession.d/目录下看到，其他还有一些配置文件如Xsession.options, Xresource等，都是执行的X session的初始化过程。仔细阅读这些脚本或配置文件，可以帮助你更好地理解X

### 3、Display Manager
* startx的作用可以看作是Display Manager的一种隐性实现, 它使用xinit命令，分别根据/etc/X11/xinit/xinitrc和/etc/X11/xinit/xserverrc中所指定的设置唤起X。
* 其中，xserverrc执行X server的运行任务；xinitrc则运行Xsession命令。从/etc/X11/Xsession脚本的内容可以看出，它也就是进入/etc /X11/Xsession.d/目录轮询地执行所有脚本。很明显，这些也就是前面所说的Xsession初始化工作。
* 综合起来说，Display Manager完成三个任务：1, X Server的启动; 2, X session的初始化; 3, X session的管理。

### 4、Window Manager
*  X Server提供了基本的图形显示能力。然而具体怎么绘制应用程序的界面，却是要由应用程序自己解决的。而Window Manager(桌面管理器，后简称WM)就是用来提供统一的GUI组件的(窗口、外框、菜单、按钮等)。否则，应用程序们各自为政，既增加了程序开发的负担，不统一的桌面风格对视觉也是不小的挑战。


## 四、X Clients
* 最后，就是X Client了。X客户端程序，顾名思义，就是使用X服务的程序。firefox，gedit等等都属于X Client程序。X Client部分值得考虑一下的就是DISPLAY环境变量。它主要用于远程X Client的使用。该变量表示输出目的地的位置，由三个要素组成：
```[host]:display[.screen]```
* host指网络上远程主机的名称，可以是主机名、IP地址等。默认的host是本地系统，你可以在自己系统上echo $DISPLAY看一下。
* display和screen分别代表输出画面的编号和屏幕的编号。具体细节由于硬件的缺乏，还有待进一步研究。


## 五、启动X主要有两种方法
* 一是Display Manager，如XDM、GDM、KDM，此种方法通过图形界面登录;
* 一种是通过xinit，此种方法适用于字符界面登录。我们常用于登录X的startx命令也是通过传递参数给xinit来启动X的，也就是说，最终启动X的是xinit。startx只是一个bash脚本。

## 六、X协议
* X系统背后的七条程序设计原则:http://www.linuxeden.com/html/develop/20001202/18598.html
* X只提供实现GUI环境的基本框架，如定义protocol、在显示设备上绘制基本的图形单元（点、线、面等等）、和鼠标键盘等输入设备交互、等等。它并没有实现UI设计所需的button、menu、window title-bar styles等元素，而是由第三方的应用程序提供。
* X包括X server和X client，它们之间通过X protocol通信。
* X server接收X clients的显示请求，并输出到显示设备上，同时，会把输入设备的输入事件，转递给相应的X client。X server一般以daemon进程的形式存在。
* X protocol是网络透明（network-transparently）的，也就是说，server和client可以位于同一台机器上的同一个操作系统中，也可以位于不同机器上的不同操作系统中（因此X是跨平台的）。这为远端GUI登录提供了便利，如上面图片所示的运行于remote computer 的terminal emulator，但它却可以被user computer的鼠标键盘控制，以及可以输出到user computer的显示器上。
* X将protocol封装为命令原语（X command primitives），以库的形式（xlib或者xcb）向client提供接口。X client（即应用程序）利用这些API，可以向X server发起2D（或3D，通过GLX等扩展，后面会介绍）的绘图请求。

* X 协议由 X server 和 X client 组成:(1)、server 管理主机上与显示相关的硬件设置（如显卡、硬盘、鼠标等），它负责屏幕画面的绘制与显示，以及将输入设置（如键盘、鼠标）的动作告知 X client。（２）X client (即 X 应用程序) 则主要负责事件的处理（即程序的逻辑）。

* Linux下DISPLAY环境变量的作用: DISPLAY环境变量格式如下```host:NumA.NumB```, host指Xserver所在的主机主机名或者ip地址, 图形将显示在这一机器上, 可以是启动了图形界面的Linux/Unix机器, 也可以是安装了Exceed, X-Deep/32等Windows平台运行的Xserver的Windows机器. 假如Host为空, 则表示Xserver运行于本机, 并且图形程序(Xclient)使用unix socket方式连接到Xserver, 而不是TCP方式. 使用TCP方式连接时, NumA为连接的端口减去6000的值, 假如NumA为0, 则表示连接到6000端口; 使用unix socket方式连接时则表示连接的unix socket的路径, 假如为0, 则表示连接到/tmp/.X11-unix/X0 . NumB则几乎总是0.

##　五、软件框架
### 1、Ｄispaly Server
* 如Wayland compositor和X-Server（X.Org

### 2、libX/libXCB和libwayland-client
* display server提供给Application（或者GUI Toolkits）的、访问server所提供功能的API。libX/libXCB对应X-server，libwayland-client对已Wayland compositor。

### 3、libGL

* libGL是openGL接口的实现，3D application（如这里的3D-game engine）可以直接调用libGL进行3D渲染。

* libGL可以是各种不同类型的openGL实现，如openGL（for PC场景）、openGL|ES（for嵌入式场景）、openVG（for Flash、SVG矢量图）。

* libGL的实现，既可以是基于软件的，也可以是基于硬件的。其中Mesa 3D是OpenGL的一个开源本的实现，支持3D硬件加速。

### 4、libDRM和kernel DRM
* DRI（Direct Render Infrastructure）的kernel实现，及其library。X-server或者Mesa 3D，可以通过DRI的接口，直接访问底层的图形设备（如GPU等）。

### 5、KMS（Kernel Mode Set）
* 一个用于控制显示设备属性的内核driver，如显示分辨率等。直接由X-server控制。

