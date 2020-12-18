
# Overlayfs 文件系统使用

## 一、 概述
 
> Overlayfs也被称为联合文件系统，它可让你使用 2 个目录挂载文件系统：“下层”目录和“上层”目录。这两个目录大致具有如下特点：
* 下层目录是只读的。
* 上层目录可以读写。

&nbsp;

## 二、Overlayfs文件系统基本结构

* ### lowdir
* ### updir
* ### mergedir

> 如下图所示：

![avatar](./overlayfs-1.png)

> overlayfs最基本的特性简单的总结起来有以下3点：
* **上下层同名目录合并；**
* **上下层同名文件覆盖；**
* **Lower Dir文件写时拷贝。**

> **这三点对用户都是不感知的。**

&nbsp;

##  三、 Overlayfs文件系统使用
### 1、文件系统挂载
> 基本命令:
```
mount -t overlay overlay -o lowerdir=lower1:lower2:lower3,upperdir=upper,workdir=work merged
```
> 挂载选项支持（-o 参数）
* lowerdir=xxx：
* upperdir=xxx：
* workdir=xxx：
* merged：

> 案例演示：
```
#!/bin/bash

mkdir -p lower1 lower2 upper worker merge
mkdir -p lower1/dir lower2/dir upper/dir
touch lower2/foo1 lower1/foo2 lower1/foo3 upper/foo3 upper/foo4
touch lower1/dir/aa lower2/dir/aa lower1/dir/bb lower1/dir/cc upper/dir/bb
echo "from lower1" > lower1/dir/aa
echo "from lower2" > lower2/dir/aa
echo "from lower1" > lower1/dir/bb
echo "from upper" > upper/dir/bb
echo "from lower1" > lower1/foo3
echo "from lower2" > lower2/foo1
echo "from upper" > upper/foo3

sudo mount -t overlay overlay -o lowerdir=lower1:lower2,upperdir=upper,workdir=worker merge

```

> 结果展示：

![avatar](./overlayfs-2.png)



### 2、删除文件和目录
* **场景一：要删除的文件或目录来自upper层，且lower层中没有同名的文件或目录**
> 因为upper层是可写的，因此这种种情况会直接删除upper目录。例如删除文件foo4，结果如下:

![avatar](./example-1.png)

&nbsp;

* **场景二：要删除的文件或目录来自lower层，upper层不存在覆盖文件**
> 由于lower层中的内容对于overlayfs来说是只读的，所以并不能像之前那样直接删除lower层中的文件或目录，因此需要进行特殊的处理，让用户在删除之后即不能正真的执行删除动作又要让用户以为删除已经成功了。

> 简单的讲，当用户删除的文件来自lower时，overlayfs会在upper层创建一个同名的**Whiteout文件**，用于屏蔽lower层对应的文件，此时merge层就看不到该文件了。例如删除文件foo2，结果如下：
![avatar](./example-2.png)

> 从上面测试结果可以看出，删除一个来自lower层的文件foo2时，upper层多了一个同名的主、次设备号都为0的字符设备文件，这个文件就是**Whiteout文件**。

&nbsp;

* **场景三：要删除的文件是upper层覆盖lower层的文件**
>  overlayfs会直接删除upper层文件，同时创建同名**Whiteout文件**去屏蔽lower层文件。例如删除foo3文件，结果如下：

![avatar](./example-3.png)


&nbsp;

* **场景四：要删除的目录是上下层合并的目录**
> overlayfs会直接删除upper层目录，同时创建同名**Whiteout文件**去屏蔽lower层目录。例如删除dir目录，结果如下：

![avatar](./example-4.png)

&nbsp;

### 3、创建文件和目录
* **场景一：全新的创建一个文件或目录**
> 如果在lower层中和upper层中都不存在对应的文件或目录，那直接在upper层中对应的目录下新创建文件或目录即可。

![avatar](./example-5.png)

* **场景三：创建一个在lower层已经存在且在upper层有whiteout文件的同名文件**
> overlayfs 会在upper层创建一个同名文件替代**Whiteout文件**，例如，先把foo3删掉，upper层原foo3被删除，同时又产生了一个同名的**Whiteout文件**，然后再创建一个foo3文件，upper层**Whiteout文件**又会被新文件替代。

![avatar](./example-6.png)

* **场景三：创建一个在lower层已经存在且在upper层有whiteout文件的同名目录**
> 同上。

### 4、写时复制（copy-up）特性
> 用户在写文件时，如果文件来自upper层，那直接写入即可。但是如果文件来自lower层，由于lower层文件无法修改，因此需要先复制到upper层，然后再往其中写入内容，这就是overlayfs的写时复制（copy-up）特性。
> 例如，向foo1写入，结果如下：
![avatar](./example-7.png)


## 五、overlayroot（扩展内容）


参考资料：https://blog.csdn.net/luckyapple1028/article/details/78075358



 