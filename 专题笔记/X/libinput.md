## 一、鼠标设置
### 1、使用鼠标按键实现滚动
* 通过配置，在按住某个鼠标按键（例如左键或者右键，或者鼠标上的其他按键）的同时移动鼠标，可以实现滚动页面的动作，这对于轨迹球之类没有滚轮的设备十分有帮助。只需要将 ScrollMethod 配置为 button，并通过 ScrollButton 选项配置对应的按键即可。可以参考下面的例子：
```
/etc/X11/xorg.conf.d/00-mouse.conf

Section "InputClass"
    Identifier "system-mouse"
    MatchIsPointer "on"
    Option "ScrollMethod" "button"
    Option "ScrollButton" "3"
EndSection
```
* 备注：上面配置是让鼠标又键实现滚动功能，如果要设置左键或中间键，把键值改为１或２。

## 二、xinput命令
## 1、查看所有设备并确定其名称和编号：
* xinput list

## 2、查看 device 的设置
* xinput list-props device

## 3、修改 device 的某项设置
* xinput set-prop device option setting

### 3.1、鼠标相关的设置
```
xinput list-props 14
Device 'TPPS/2 Elan TrackPoint':
	Device Enabled (144):	1
	Coordinate Transformation Matrix (146):	1.000000, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000, 0.000000, 0.000000, 1.000000
	libinput Natural Scrolling Enabled (279):	0				# 设置中键的滚动方向,如果设置为1的话会发现滚动方向与之前相反
	libinput Natural Scrolling Enabled Default (280):	0			# 中键滚动方向的默认值,经过测试设置这个值会报错
	libinput Scroll Methods Available (281):	0, 0, 1			# 这个属性有3个值，分别为两指滑动，边沿滑动，和中键滚动
	libinput Scroll Method Enabled (282):	0, 0, 1
	libinput Scroll Method Enabled Default (283):	0, 0, 1
	libinput Button Scrolling Button (284):	2			#  当设置为1时滚动功能为左键加小红点, 2为中键+小红点,3为右键+小红点
	libinput Button Scrolling Button Default (285):	2
	libinput Middle Emulation Enabled (286):	0			# 置为 1 时,同时按住左键和右键后相当于鼠标中键的作用
	libinput Middle Emulation Enabled Default (287):	0
	libinput Accel Speed (288):	-1.000000			# 设置小红点移动光标的灵敏度 取值范围为 [-1 , 1]
	libinput Accel Speed Default (289):	0.000000
	libinput Accel Profiles Available (290):	1, 1		# 设置移动光标的加速模式,有"adaptive",  "flat" 两个选项,但不知道是干什么用的
	libinput Accel Profile Enabled (291):	1, 0
	libinput Accel Profile Enabled Default (292):	1, 0
	libinput Left Handed Enabled (293):	0		# 左右键互换选项,设置为1时左键会变成右键(为了照顾左撇子)
	libinput Left Handed Enabled Default (294):	0
	libinput Send Events Modes Available (264):	1, 0
	libinput Send Events Mode Enabled (265):	0, 0
	libinput Send Events Mode Enabled Default (266):	0, 0
	Device Node (267):	"/dev/input/event8"
	Device Product ID (268):	2, 10
	libinput Drag Lock Buttons (295):	<no items>
	libinput Horizontal Scroll Enabled (296):	1
```

## 三、libinput配置文件
### 1、/etc/X11/xorg.conf.d/40-libinput.conf
### 2、https://man.archlinux.org/man/libinput.4
```
Section "InputClass"
        Identifier "touchpad"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
        Option "Tapping" "on"
        Option "ButtonMapping" "1 0 3 4 5 6 7"
        Option "TappingButtonMap" "lrm"
        Option "DisableWhileTyping" "on"
        Option "TappingDrag" "on"
        Option "AccelSpeed" "0.333333"
EndSection
```
* Option “Tapping” “on”：手指点击touchpad发送鼠标点击事件
* Option “TappingButtonMap” “lmr”：1个手指点击对应鼠标左键，2个手指点击对应鼠标中键，3个鼠标点击对应鼠标右键。
* Option “ButtonMapping” “1 3 0 4 5 6 7”，按钮映射，详情见libinput#Button_Mapping，这里笔者关闭了3指对应的左键。
* Option “DisableWhileTyping” “on”：打字时不检测touchpad事件，防止用户不小心触碰touchpad引起不必要的影响。
* Option “TappingDrag” “on”：开启点击拖拽。


## 四、libinput-gestures
* 参考资料：https://www.yisu.com/zixun/592772.html
* 配置文件示例：https://blog.csdn.net/weixin_44268185/article/details/120242534

## 五、dde-daemon 设置手势
* 配置文件:/usr/share/dde-daemon/gesture.json

## 六、xorg处理输入事件相关代码（临时笔记）
### 1、AddInputDevice()函数（初始化新输入设备时调用）
```
初始化设备处理函数：
dev->public.processInputProc = ProcessOtherEvent;
dev->public.realInputProc = ProcessOtherEvent;
dev->public.enqueueInputProc = EnqueueEvent;
```
### 2、ProcessOtherEvent()函数
* 键盘、鼠标事件处理流程：　ProcessOtherEvent --> ProcessDeviceEvent　--> event_set_state
* 触摸板事件处理流程：ProcessOtherEvent　--> ProcessTouchEvent --> event_set_state
　
## 七、Linux Input子系统框架
* https://blog.csdn.net/u010657219/article/details/42422139?locationNum=9&fps=1



## 八、总结
### 1、概述
* libinput是Xorg输入驱动程序。它支持libinput可以处理的所有输入设备，包括数鼠标、键盘、平板电脑和触摸屏等。

### 2、配置文件
#### 2.1 配置文件示例
```
Section "InputClass"
        Identifier "touchpad"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
        Option "Tapping" "on"
        Option "ButtonMapping" "1 0 3 4 5 6 7"
        Option "TappingButtonMap" "lrm"
        Option "DisableWhileTyping" "on"
        Option "TappingDrag" "on"
        Option "AccelSpeed" "0.333333"
EndSection

```

#### 2.2 可配置选项:
* Option "AccelProfile" "string"
* Option "AccelSpeed" "string" :设置设置次设备光标移动速度系数，取值范围为[-1,1]
* Option "ButtionMapping" "string": 设置此设备逻辑按键映射，字符串必须是一个以空格分隔的按钮映射列表，按设备上逻辑按钮的顺序排列，从按钮1开始。默认配置为"1 2 3...32"。映射值为0将停用该按钮。多个按键可以取同一个映射值。非法映射在会被丢弃，自动使用默认值代替所有的映射。用户未指定的映射使用默认值。

* Option "CalibrationMatrix" "string"：由9个空格分隔的浮点数组成的字符串，顺序为“abcdefghi”。将校准矩阵设置为3x3矩阵，其中第一行为（abc），第二行为（def），第三行为（ghi）。
* Option "ClickMethod" "string": 启用点击方法，取值可以是none, buttonareas, clickfinger。并非所有的设备都支持这几个选项，当设置的某个选项不支持时，救护使用默认设置(none??)
* Option "DisableWhileTyping" "bool": 用于当连接键盘作为输入时，是否禁用触摸板（这不适用于Ctrl或Alt等修改键）。
* Option "Device" "string": 指定可以访问的设备。取值为"/dev/input/eventX"。
* Option "DragLockButtons" "L1 B1 L2 B2...":从逻辑上模拟某个按键被按下，即使对应的物理按键它已被释放；如果要释放改逻辑按键，需要再次单击一下对应的物理按键。配置是成对出现的。每对编号对的第一个编号是锁定按钮，第二个编号是要锁定的逻辑按钮编号。
* Option "HighResolutionWheelScrolling" "bool"：禁用默认情况下启用的高分辨率滚轮滚动事件。启用时，驱动程序仅从libinput转发高分辨率的滚轮滚动事件。禁用时，驱动会转发legacy wheel滚动事件。
* Option "HorizontalScrolling" "bool"：禁用水平滚动。禁用时，此驱动程序将放弃libinput中的任何水平滚动事件。请注意，这并没有禁用水平滚动，它只是从任何滚动事件中丢弃水平轴。
* Option "LeftHanded" "bool"：启用左手按钮方向，即交换左右按钮。
* Option "MiddleEmulation" "bool"：启用中间按钮模拟。启用后，同时按下左键和右键会产生鼠标中键单击。
* Option "NaturalScrolling" "bool"：启用或禁用自然滚动行为。
* Option "RolationAngle" "float"：将设备的旋转角度设置为给定角度，以顺时针角度为单位。角度必须介于0.0（含）和360.0（不含）之间。
* Option "ScrollButton" "int"：将按钮指定为滚动按钮。如果ScrollMethod为button，且按钮在逻辑上为down，则x/y轴移动将转换为scroll事件。
* Option "ScrollButtonLock" "bool"：启用或禁用滚动按钮锁定。如果启用，则在第一次单击后，ScrollButton在逻辑上被视为按下，并在第二次单击该按钮之前保持按下。如果禁用（默认设置），则在按住滚动按钮的同时，逻辑上会将其视为按下，在物理释放后，会将其视为释放。
* Option "ScrollMethod" "string"：启用滚动方法。允许的值为none、twofinger、edge和button。并非所有设备都支持所有选项，如果某个选项不受支持，则使用此设备的默认滚动选项。
* Option "ScrollPixelDistance" "int"：设置触发一次逻辑滚轮点击所需的移动距离（以“像素”为单位）。此选项仅适用于滚动方法twofinger、edge和button。
* Option "SendEventsMethod" "string"：将发送事件模式设置为禁用、启用或“连接外部鼠标时禁用”。
* Option "TabletToolPressureCurve" "x0/y0/ x1/y1 x2/y2 x3/y3"：将平板电脑手写笔的压力曲线设置为四个点形成的贝塞尔曲线。相应的x/y坐标必须在[0.0,1.0]范围内。
* Option "TabletToolAreaRatio" "w:h"：设置平板电脑工具的面积比。该区域始终从原点（0/0）开始，并以指定的纵横比扩展到最大的可用区域。该地区以外的活动将被裁剪到该地区。特殊值“default”用于默认映射（即设备本机映射）。
* Option "Tapping" "bool"：启用或禁用点击点击行为。
* Option "TappingButtonMap" "(lrm|lmr)"：将1/2/3指轻触的按钮映射分别设置为左/右/中或左/中/右。
* Option "TappingDrag" "bool"：在点击行为期间启用或禁用拖动（“点击并拖动”）。启用时，点击后按住手指只会导致一个按钮按下，该手指的所有动作都会转化为拖动动作。点击并拖动需要启用选项点击。
* Option "TappingDragLock" "bool"：在轻敲行为期间启用或禁用拖动锁定。启用后，在点击和拖动过程中竖起手指不会立即释放按钮。如果在超时时间内再次放下手指，则拖动过程将继续。

#### 2.3 可配置属性
> libinput将运行时可配置选项导出为属性。如果下面列出的属性不可用，则设备上的对应配置选项不可用。然而，这并不意味着该功能在设备上不可用。以下属性由libinput驱动程序提供。

* libinput Accel Profiles Available
* libinput Accel Profile Enable
* libinput Accel Speed: 1-32位浮点数据，设置光标移动速度系数，取值范围［-1 1］。
* libinput Button Scrolling Button：设置用于按钮滚动的按钮编号。此设置独立于滚动方法，要启用按钮滚动，必须将方法设置为按钮滚动，并且必须设置有效的按钮。
* libinput Button Scrolling Button Lock Enabled:如果为true，则启用滚动按钮锁定。此设置独立于滚动方法或滚动按钮，要启用按钮滚动，必须将方法设置为按钮滚动，并且必须设置有效的按钮。
* libinput Calibration Matrix: ???
* libinput Click Methods Available: 2个布尔值（8位、0或1），顺序为“buttonareas”、“clickfinger”。指示此设备上可用的单击方法。
* libinput Click Methods Enabled：2个布尔值（8位、0或1），顺序为“buttonareas”、“clickfinger”。指示在此设备上启用了哪些单击方法。
* libinput Drag Lock Buttons：??
* libinput High Resolution Wheel Scroll Enabled: 1个布尔值（8位、0或1）。指示是否启用高分辨率滚轮滚动事件。
* libinput Horizontal Scroll Enabled: 1个布尔值（8位、0或1）。指示是否启用水平滚动事件。
* libinput Left Handed Enabled: 1个布尔值（8位、0或1）。指示是启用还是禁用左手模式。
* libinput Middle Emulation Enabled: 1个布尔值（8位、0或1）。指示中间仿真是启用还是禁用。
* libinput Natural Scrolling Enabled: 1个布尔值（8位、0或1）。1支持自然滚动
* libinput Rotation Angle: 1-32位浮点值[0.0到360.0]。设置设备的旋转角度，以其自然中性位置的顺时针方向为准。
* libinput Scroll Method Available: 3个布尔值（8位、0或1），顺序为“two-finger”、“edge”、“button”。指示此设备上可用的滚动方法。
* libinput Scroll Method Enabled:3个布尔值（8位、0或1），顺序为“two-finger”、“edge”、“button”。指示此设备上当前启用的滚动方法。
* libinput Scroll Pixel Distance:1-32位值（非零，带有附加的实现定义的范围检查）。更改触发一次逻辑滚轮点击所需的移动距离。
* libinput Send Events Modes Avaliable:2个布尔值（8位、0或1），顺序为“禁用”和“在外部鼠标上禁用”。指示此设备上可用的发送事件模式。
* libinput Send Events Monde Enable:2个布尔值（8位、0或1），顺序为“禁用”和“在外部鼠标上禁用”。指示此设备上当前启用的发送事件模式。
* libinput Tablet Tool Pressurecurve: ???
* libinput Table Tool Area Ratio: 2个32位值，对应于宽度和高度。特殊值0，0重置为默认比率
* libinput Tapping Enabled: ??
* libinput Tapping Button Mapping Enabled: 2个布尔值（8位，0或1），顺序为“lrm”和“lmr”。指示此设备上当前启用的按钮映射。
* libinput Tapping Drag Lock Enabled: 1个布尔值（8位、0或1）。1在轻敲过程中启用拖动锁定
* libinput Disable While Tryping Enabled:1个布尔值（8位、0或1）。指示是启用还是禁用键入时禁用。

#### 2.4 按键映射
> X客户端接收带有逻辑按钮编号的事件，其中1、2、3通常被解释为左、中、右，而逻辑按钮4、5、6、7通常被解释为向上、向下、左、右滚动。因此，设备上的第4个和第5个物理按钮将发送逻辑按钮8和9。ButtonMapping选项调整逻辑按钮映射，它不影响物理按钮映射到逻辑按钮的方式。

> 传统上，通过应用按钮映射“3 2 1…”将设备设置为左手按钮模式在使用libinput Xorg输入驱动程序的系统上，建议改用"LeftHanded option"。
> libinput Xorg输入驱动程序在安装后不使用按钮映射。使用XSetPointerMapping或xinput在运行时修改按钮映射。

#### 2.5 按键拖拽锁定
> 按钮拖动锁在逻辑上保持按钮向下，即使对应的物理按钮本身已被释放。按钮拖拽锁有两种模式。
> 如果在“元”模式下，元按钮点击会激活拖动锁定，以便下一次按下任何其他按钮。以后单击按钮将在逻辑上保持该按钮处于按下状态，直到再次单击该按钮。元按钮事件本身被丢弃。以后每次为按钮激活拖动锁时，都需要单独的元按钮点击。
> 如果处于“配对”模式，每个按钮都可以分配一个目标锁定按钮。单击按钮时，目标锁定按钮逻辑上被按住，直到下一次单击同一按钮。按钮事件本身被丢弃，只发送目标按钮事件。

#### 2.6 触摸屏压力曲线
> 压力曲线会影响手写笔压力的报告方式。默认情况下，硬件压力按原样报告。通过设置压力曲线，可以调整触针的触感，使其更像铅笔或刷子。
> 压力曲线是三次贝塞尔曲线，在提供的四个点之间的0.0到1.0的标准化范围内绘制。该标准化范围应用于平板电脑的压力输入，以便最高压力映射到1.0。这些点必须具有递增的x坐标，如果x0大于0.0，则所有低于x0的压力值都相当于y0。如果x3小于1.0，则高于x3的所有压力值都相当于y3。
> 线性曲线的输入（默认值）为“0.0/0.0.0/0.0 1.0/1.0 1.0/1.0”；略微凹陷的曲线（更为坚实）可能是“0.0/0.0 0.05/0.0 1.0/0.95 1.0/1.0”；稍微升高的曲线（较软）可能是“0.0/0.0.0/0.05 0.95/1.0 1.0/1.0”。

#### 2.7 触摸屏工具面积比
> 默认情况下，平板电脑工具可以访问整个传感器区域，平板电脑区域映射到可用屏幕区域。对于Wacom Intuos系列等外部平板电脑，平板电脑的高宽比可能与显示器的高宽比不同，从而导致输入数据的倾斜。
> 为了避免输入数据的这种倾斜，可以设置面积比以匹配屏幕设备的比率。例如，比例为4:3时，平板电脑的可用面积将以4:3的比例减少到最大可用面积。该区域内的活动将扩展到平板电脑宣布的axis范围，因此面积比对X服务器是透明的。此区域之外的任何事件将发送等于该轴最大值的事件。该区域始终从设备当前旋转的原点开始，也就是说，它考虑了左手性。
> X服务器不支持每像素滚动，但支持平滑滚动。然而，所有滚动事件都基于一个逻辑滚动单元（传统上对应于滚轮点击）。因此，不可能滚动10个像素，但驱动程序可以滚动逻辑滚轮点击的十分之一。
> libinput提供以像素为单位的滚动数据。ScrollPixelDistance选项定义相当于一次滚轮点击的移动量。例如，值为50意味着用户必须移动手指50个像素来生成一个逻辑点击事件，每个像素是轮子点击的1/50。


#### 


