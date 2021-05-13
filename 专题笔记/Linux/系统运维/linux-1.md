## 参考文献
* http://c.biancheng.net/linux_tutorial/

## 1、ACL权限
* ### 查看ACL权限
``` 
getfacle 文件名
```

* ### 设置ACL权限
```
setfacl 选项  文件名

选项：
-m：设定 ACL 权限。如果是给予用户 ACL 权限，则使用"u:用户名：权限"格式赋予；如果是给予组 ACL 权限，则使用"g:组名：权限" 格式赋予；
-x：删除指定的 ACL 权限；
-b：删除所有的 ACL 权限；
-d：设定默认 ACL 权限。只对目录生效，指目录中新建立的文件拥有此默认权限；
-k：删除默认 ACL 权限；
-R：递归设定 ACL 权限。指设定的 ACL 权限会对目录下的所有子文件生效；
```

* ### 举例
```
uos@uos-PC:~/test$ getfacl  test 
# file: test
# owner: root
# group: root
user::rw-
group::r--
other::r--

sudo setfacl -m u:uos:--x test 

uos@uos-PC:~/test$ getfacl  test 
# file: test
# owner: root
# group: root
user::rw-
user:uos:--x
group::r--
mask::r-x
other::r--

sudo setfacl  -x u:uos test 

uos@uos-PC:~/test$ getfacl  test 
# file: test
# owner: root
# group: root
user::rw-
group::r--
mask::r--
other::r--
```

## 2、useradd 
```
语法：
useradd [-mMnr][-c <备注>][-d <登入目录>][-e <有效期限>][-f <缓冲天数>][-g <群组>][-G <群组>][-s <shell>][-u <uid>][用户帐号]

或者：
useradd -D [-b][-e <有效期限>][-f <缓冲天数>][-g <群组>][-G <群组>][-s <shell>]
```

## 3、/etc/passwd
* 该文件共有7个字段
* 例如：uos:x:1000:1000::/home/uos:/bin/bash

| 用户名 | 密码占位符 |  uid    | gid   | 用户备注 |   家目录    |   登录shell   |
| :----:| :----:   | :----:  | :----:|  :----: |   :----:   |    :----:    |
|  uos  |    x     | 1000    | 1000  |   空    | /home/uos  |   /bin/bash   |


## 4、/etc/shadow
* 前面介绍了 /etc/passwd 文件，由于该文件允许所有用户读取，易导致用户密码泄露，因此 Linux 系统将用户的密码信息从 /etc/passwd 文件中分离出来，并单独放到了此文件中。/etc/shadow 文件只有 root 用户拥有读权限，其他用户没有任何权限，这样就保证了用户密码的安全性。

* 共9个字段：
```
用户名：加密密码：最后一次修改时间：最小修改时间间隔：密码有效期：密码需要变更前的警告天数：密码过期后的宽限时间：账号失效时间：保留字段
```

## 5、/etc/group 
* 前面讲过，etc/passwd 文件中每行用户信息的第四个字段记录的是用户的初始组 ID，那么，此 GID 的组名到底是什么呢？就要从 /etc/group 文件中查找。

* 共4个字段：
```
组名：密码：GID：该用户组中的用户列表
```

## 6、vim 使用
* vim 输入模式快捷键：http://c.biancheng.net/view/804.html
* Vim移动光标快捷键汇总: http://c.biancheng.net/view/3008.html


## 参考资料： http://c.biancheng.net/linux_tutorial/