## 1、QProcess
* http://www.voidcn.com/article/p-omzovjrs-bpp.html

## 2、Qt 参考资料
* http://c.biancheng.net/qt/

## 3、Qt高级——Qt元对象系统源码解析
* https://blog.csdn.net/u014421422/article/details/90740981

## 4、QT信号和槽用于对象之间的通信
* https://blog.csdn.net/zhang2531/article/details/50807616

## 5、C++ 中的容器类详解
* https://blog.csdn.net/tju_fengbo/article/details/81978595

## ６、qt 国际化
* 第一步：编写源码。在代码中，将需要国际化的内容，使用tr()函数。
* 第二步：使用```lupdate```生成.ts文件，显卡驱动管理这一步是在translations/update_ts.py　脚本中完成
* 第三步：使用```tx push```命令将xxx.ts文件推送给翻译工程师，翻译工程师再把xxx.ts里面的内容翻译程每一种语言,生成对应的ts文件（如xxx_zh_CN.ts）。
* 第四步：使用```tx pull```命令讲翻译工程师翻译的内容pull 到本地
* 第五步: 在项目的最顶层CmakeList.txt文件中调用qt5_add_translation函数把.ts文件生成.qm文件。
* 第五步：在main函数中加载翻译内容，例如：
```
    QString loc = QLocale::system().name();

    QString loc_tr_file = QString(TRANSLATIONS_DIR"/deepin-graphics-driver-manager_%1.qm");

    QTranslator trans;
    trans.load(loc_tr_file.arg(loc));
    app.installTranslator(&trans);

```
> 备注：在使用```tx pull```和```tx push```,需要先拿到token, 解决办法是添加如下文件：
```
文件：~/.transifexrc
内容：
[https://www.transifex.com]
api_hostname = https://api.transifex.com
hostname = https://www.transifex.com
password = 1/2847330938c2eebc627c8dd113ba4f58aeb3fd3d
username = api
```

