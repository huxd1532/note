## 1、kwayland-wain切换

*  /usr/share/xsessions/deepin.desktop 和usr/share/wayland-sessions/Wayland.desktop 两个desktop 分别对应X和wayland启动
* usr/share/wayland-sessions/Wayland.desktop 内容如下
```
#NOTE: must set QT_QPA_PLATFORM here, dbus activated services from DDE env will 
#need it. kwin_wayland_helper won't use it since kwin_wayland will reset it.
export QT_QPA_PLATFORM=xcb
/usr/bin/dde_update_dbus_env
if [ -z "$DBUS_SESSION_BUS_ADDRESS" ]; then
  exec dbus-launch kwin_wayland_helper
else
  exec kwin_wayland_helper
fi

```
* kwin_wayland_helper 脚本：
```
exec /usr/bin/kwin_wayland -platform dde-kwin-wayland --xwayland --drm --no-lockscreen  startdde-wayland 1> $HOME/.kwin.log 2>&1
```
