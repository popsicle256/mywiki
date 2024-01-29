date：2023-12-27

# 1 目标
在第三方apk中要能直接跳转到显示中的”屏幕超时“设置菜单中。  
目前Android官方提供的api中，只能跳转到”显示“设置菜单中，需要手动点击进入到”屏幕超时“中，无法直接跳转到该小项中。

# 2 实现

步骤：  
在AndroidManifest中加入两个activity项，其中最主要的为activity的命名+fragment的类（通过该小项页面中的菜单名称去找对应的fragment，对应的类名有可能是包含“fragment”，也有可能不包含）。  

# 3 相关资料
[android开发笔记之SubSettings界面跳转](https://blog.csdn.net/hfreeman2008/article/details/52778992)  
