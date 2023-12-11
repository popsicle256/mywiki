# 打印调用方法栈

打印代码：  
`android.util.Log.d(TAG + " << By libin >> ", android.util.Log.getStackTr#aceString(new Throwable()));`  

注意点：  
1. 打印log开关要打开，如下中的SPEW要置true，否则可能不会打印。  
```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/SystemUI/src/com/android/systemui/statusbar/phone/ShadeControllerImpl.java b/vendor/mediatek/proprietary/packages/apps/SystemUI/src/com/android/systemui/statusbar/phone/ShadeControllerImpl.java
index cee8b335f38..7d6b932164d 100644
--- a/vendor/mediatek/proprietary/packages/apps/SystemUI/src/com/android/systemui/statusbar/phone/ShadeControllerImpl.java
+++ b/vendor/mediatek/proprietary/packages/apps/SystemUI/src/com/android/systemui/statusbar/phone/ShadeControllerImpl.java
@@ -40,7 +40,7 @@ import dagger.Lazy;
 public class ShadeControllerImpl implements ShadeController {

     private static final String TAG = "ShadeControllerImpl";
-    private static final boolean SPEW = false;
+    private static final boolean SPEW = true;

     private final CommandQueue mCommandQueue;
     private final StatusBarStateController mStatusBarStateController;
@@ -113,6 +113,7 @@ public class ShadeControllerImpl implements ShadeController {
                     + " mExpandedVisible=" + getCentralSurfaces().isExpandedVisible()
                     + " flags=" + flags);
         }
+        android.util.Log.d("*** libin", android.util.Log.getStackTr#aceString(new Throwable()));

         // TODO(b/62444020): remove when this bug is fixed
         Log.v(TAG, "NotificationShadeWindow: " + getNotificationShadeWindowView()
```