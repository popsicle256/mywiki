# 目标
从屏幕底部上滑，直接进入指定的app。  

# 实现
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