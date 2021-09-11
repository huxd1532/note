## 一、镜像下载
* debian 下载： http://cdimage.debian.org/cdimage/archive/

## 二、环境搭建
* ```sudo apt-get  install  -y libvirt0 libvirt-daemon qemu virt-manager bridge-utils libvirt-clients python-libvirt  qemu-efi uml-utilities  virtinst ```


## 三、创建虚拟机
### 1、创建镜像: ```qemu-img create -f qcow2 centos7.qcow2 15G```
### 2、安装系统： ```qemu-system-x86_64 -m 2048 -enable-kvm centos7.qcow2  -cdrom CentOS-7-x86_64-DVD-2009.iso``` 
### 3、镜像格式转换： ```qemu-img convert -f raw  images/generic.qcow2  -O qcow2  generic.qcow2```

### 4、 nat + tap 网络配置
```
<interface type='network'>
    <mac address='52:54:00:e8:7d:af'/>
    <source network='default' bridge='virbr0'/>
    <target dev='vnet0'/>
    <model type='e1000'/>
    <alias name='net0'/>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0'/>
</interface>
```
### 5、macvtap网络配置
```
<interface type='direct'>
    <mac address='1a:2b:3c:4d:5e:6a'/>
    <source dev='eno1' mode='bridge' />
    <model type='e1000'/>
    <alias name='net0'/>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x03'
    function='0x0'/>
</interface>
```
## 四、虚拟机镜像压缩(qcow2,raw)
* http://t.zoukankan.com/mountain2011-p-9150888.html
