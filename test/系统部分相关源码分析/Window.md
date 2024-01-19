# 相关经历
TeamViewer通过**FLAG_NOT_TOUCHABLE**这个Flag来实现对非白名单签名的系统禁止远程控制功能。  

# 基本概念
Window是一个抽象概念，每一个Window都对应着一个View和一个ViewRootImpl。Window和View是通过ViewRootImpl来建立联系。因此Window不是实际存在的，它是以View的形式存在。View是Window的实体，对Window的访问需要通过WindowManager。  

# 相关资料
[Android的Window和WindowManager](https://blog.csdn.net/m0_59162559/article/details/124929310?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-124929310-blog-119680476.235^v40^pc_relevant_default_base&spm=1001.2101.3001.4242.2&utm_relevant_index=4)  
[Android中View的事件分发和拦截机制](https://blog.csdn.net/xiaohuanqi/article/details/49161401?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-49161401-blog-120352289.235%5Ev40%5Epc_relevant_default_base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-49161401-blog-120352289.235%5Ev40%5Epc_relevant_default_base&utm_relevant_index=2)  
[Google官方文档相关](https://developer.android.com/about/versions/12/behavior-changes-all?hl=zh-cn#untrusted-touch-events)  

