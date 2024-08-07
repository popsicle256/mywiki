date：2024-01-30

# 1 目标

因相机手动对焦后，会产生无法拍照的问题，且拍照预览界面点击无效。  
故为解决该问题，直接去除手动对焦方式。  

对于该问题，这种方式治标不治本。
# 2 实现
```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/Camera2/feature/setting/focus/src/com/mediatek/camera/feature/setting/focus/Focus.java b/vendor/mediatek/proprietary/packages/apps/Camera2/feature/setting/focus/src/com/mediatek/camera/feature/setting/focus/Focus.java
index e111efad930..268a5158bf2 100755
--- a/vendor/mediatek/proprietary/packages/apps/Camera2/feature/setting/focus/src/com/mediatek/camera/feature/setting/focus/Focus.java
+++ b/vendor/mediatek/proprietary/packages/apps/Camera2/feature/setting/focus/src/com/mediatek/camera/feature/setting/focus/Focus.java
@@ -511,7 +511,12 @@ public class Focus extends SettingBase implements
         handleAfLockRestore();
         //step3:Clear any focus UI before show touch focus UI
         mFocusViewController.clearFocusUi();
-        if (mNeedShowFocusUi) {
+
+        /**
+         * detele the auto-focus(by libin)
+         */
+
+        /**if (mNeedShowFocusUi) {
             mFocusViewController.showActiveFocusAt((int) x, (int) y);
         }

@@ -545,7 +550,7 @@ public class Focus extends SettingBase implements
                 // exposure need onSingleTapUp to reset exposure progress,so
                 // return false.
             }
-        });
+        });**/
         LogHelper.d(mTag, "[onSingleTapUp]-");
         return false;
     }
@@ -625,7 +630,7 @@ public class Focus extends SettingBase implements
                 if (isNeeedCancelAutoFocus) {
                     mFocusListener.cancelAutoFocus();
                 }
-                triggerAfLock();
+                // triggerAfLock();  // delete the auto-focus(by libin)
             }
         });
         return false;
```
该方式没有对前后摄进行判断，如果需要，可通过`getCameraId()`来判断前后摄以分开处理对焦逻辑（具体实现如下链接）。
# 3 相关资料
[MTK Android 13 应用层去掉系统相机前摄手动对焦](https://blog.csdn.net/m0_60898338/article/details/131090331)