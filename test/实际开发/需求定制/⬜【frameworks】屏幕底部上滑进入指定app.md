# 1 目标
从屏幕底部上滑，直接进入指定的app。  

# 2 问题
## 2.1 上滑进入在部分场景失效
在进入到系统相册界面后，上滑进入app失效，仍在原页面


1.24 加入action和category后仍无效，且原来桌面启动方式可正常上滑进入app也变为无效
`menuintent.setAction(Intent.ACTION_MAIN);`
`menuintent.addCategory(Intent.CATEGORY_LAUNCHER);`

将MainActivity的launchMode从初始的**singleTop**改为**singleTask**后，可正常使用  
通过Intent启动时加入的Flag会将Activity的**launchMode**设置为**singleTask**，而在apk的AndroidManifest中如果设置的MainActivity的**launchMode**为**singleTop**时，此时会产生冲突，导致跳转回MainActivity产生冲突。

1.25 将irest中MainActivity的launchMode从singleTop改为singleTask后，上滑功能可正常使用。

singleTask和singleTop之间的主要差别？为什么设置了Flag后会和singleTop产生冲突？

# 3 实现
T0
```diff
diff --git a/frameworks/base/services/core/java/com/android/server/wm/DisplayPolicy.java b/frameworks/base/services/core/java/com/android/server/wm/DisplayPolicy.java
index 6b493c112d6..2067e116b62 100644
--- a/frameworks/base/services/core/java/com/android/server/wm/DisplayPolicy.java
+++ b/frameworks/base/services/core/java/com/android/server/wm/DisplayPolicy.java
@@ -96,6 +96,7 @@ import android.app.ActivityThread;
 import android.app.LoadedApk;
 import android.app.ResourcesManager;
 import android.content.Context;
+import android.content.ComponentName;
 import android.content.Intent;
 import android.content.res.Resources;
 import android.graphics.Insets;
@@ -112,6 +113,7 @@ import android.os.SystemClock;
 import android.os.SystemProperties;
 import android.os.UserHandle;
 import android.util.ArraySet;
+import android.util.Log;
 import android.util.PrintWriterPrinter;
 import android.util.Slog;
 import android.util.SparseArray;
@@ -449,6 +451,14 @@ public class DisplayPolicy {

                     @Override
                     public void onSwipeFromBottom() {
+
+
+                        Intent menuintent = new Intent();
+                        ComponentName menu = new ComponentName("com.irest.pad_app","com.irest.pad_app.MainActivity");
+                        menuintent.setComponent(menu);
+                        menuintent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
+                        mContext.startActivity(menuintent);
+
                         synchronized (mLock) {
                             final WindowState bar = mNavigationBar != null
                                         && mNavigationBarPosition == NAV_BAR_BOTTOM
```
`DisplayPolicy.java`中还包含侧边滑动相关的操作控制。

# 相关资料