# 目标
通过Android Studio（AS）查看源码，可实现不同类之间的点击跳转，如同在开发app一样。

# 进展
已生成android.iml和android.ipr文件

# 问题
无法在AS中导入aosp源码，因为生成上述两个文件的源码是基于服务器中的A11源码，而该源码无法从服务器中拿出来，所以无法导入到AS中查看。


# 相关资料
[Android Studio查看AOSP的两种方法](https://zhuanlan.zhihu.com/p/647523790)
[Android Studio 进行AOSP源码开发配置(Java 部分)](https://www.jianshu.com/p/60be116f48ef)  
[AOSP（二）AndroidStudio导入Android系统源码](https://article.juejin.cn/post/7276812358663733263?from=search-suggest)

以及CSDN中收藏夹部分
# 备注
>通过iml和ipr文件来将aosp源码导入AS中查看，其原理大致为：通过这两个文件来实现控制依赖，基础为AOSP源码，也即需要在AS所在终端磁盘中有对应的AOSP源码，然后借助两个文件来实现相关依赖的导入，进而实现不同类之间的跳转。


# 使用AIDEGen导入
[使用 AIDEGen 将 AOSP 项目导入 Android Studio](https://juejin.cn/post/7166061140298956836)

使用该方法，可以避免需要手动修改iml文件，指定需要的内容导入即可。

但该方法存在一个问题：即**需要导入的AS必须同源码在同一终端**。

所以目前暂时也无法实现。
	但或许可以通过研究其如何导入的过程来实现跨平台导入。

较为推荐该方法，因为实现过程较简单。