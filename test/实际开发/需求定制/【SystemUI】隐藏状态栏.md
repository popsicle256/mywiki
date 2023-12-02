# 目标
直接隐藏状态栏  

# 实现
T0
```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/SystemUI/res/layout/status_bar.xml b/vendor/mediatek/proprietary/packages/apps/SystemUI/res/layout/status_bar.xml
index deab1ebd650..d68f5aed989 100644
--- a/vendor/mediatek/proprietary/packages/apps/SystemUI/res/layout/status_bar.xml
+++ b/vendor/mediatek/proprietary/packages/apps/SystemUI/res/layout/status_bar.xml
@@ -25,6 +25,7 @@
     android:layout_height="@dimen/status_bar_height"
     android:id="@+id/status_bar"
     android:orientation="vertical"
+    android:visibility="gone"
     android:focusable="false"
     android:descendantFocusability="afterDescendants"
     android:accessibilityPaneTitle="@string/status_bar"

```

# 相关资料
以下是通过另一种方式实现  
[How to hide status bar in AOSP build](https://stackoverflow.com/questions/46116519/how-to-hide-status-bar-in-aosp-build)