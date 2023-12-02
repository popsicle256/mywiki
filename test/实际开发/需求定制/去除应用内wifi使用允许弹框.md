date：2023-11-23

# 目标

app内部在连接wifi时会有系统弹框：是否允许连接到建议的WLAN网络？  
要去除该弹框，默认授予其允许权限。
# 实现
```diff
diff --git a/packages/modules/Wifi/service/java/com/android/server/wifi/WifiNetworkSuggestionsManager.java b/packages/modules/Wifi/service/java/com/android/server/wifi/WifiNetworkSuggestionsManager.java
index 7a40f686c8e..87506557446 100644
--- a/packages/modules/Wifi/service/java/com/android/server/wifi/WifiNetworkSuggestionsManager.java
+++ b/packages/modules/Wifi/service/java/com/android/server/wifi/WifiNetworkSuggestionsManager.java
@@ -1617,9 +1617,14 @@ public class WifiNetworkSuggestionsManager {
             return false;
         }
     }
-
+
     private void sendUserApprovalDialog(@NonNull String packageName, int uid) {
         CharSequence appName = mFrameworkFacade.getAppName(mContext, packageName, uid);
+        if (packageName.equals("com.irest.pad_app")){
+            handleUserAllowAction(uid, packageName);
+            mIsLastUserApprovalUiDialog = true;
+            return;
+        }
         mWifiInjector.getWifiDialogManager().createSimpleDialog(
                 mResources.getString(R.string.wifi_suggestion_title),
                 mResources.getString(R.string.wifi_suggestion_content, appName),
```
## 查找过程
通过grep查找到弹框提示文字字符串所对应的属性名，然后根据再根据属性名查找到对应的使用类，进入类中查看其具体作用过程。
## 实现原理


# 相关资料
[]()