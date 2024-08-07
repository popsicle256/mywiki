date：2024-04-18

# 1 目标
Settings中的“壁纸”项中，会默认显示“动态壁纸”入口，无论是否当前设备内是否存在动态壁纸。  

要求，根据设备内是否存在动态壁纸来动态显示该“动态壁纸”入口。  

# 2 实现
## 2.1 解决思路
要点：获取设备内当前的可用动态壁纸数量。  
在点进“动态壁纸”项中时，如果设备内当前无动态壁纸，就会显示“无动态壁纸。”，表明在此处已经查找了设备内的动态壁纸，故只要找到此处实现逻辑，挪用即可。  

根据设置壁纸中选择壁纸来源中的“动态壁纸”字符串，查找到了在`./packages/apps/WallpaperPicker2/src/com/android/wallpaper/module/DefaultCategoryProvider.java`中有对该字符串的使用（R.string.**live_wallpapers_category_title**）。  
如此发现在这个WallpaperPicker2中加载了“选择壁纸来源”中的不同类型壁纸来源，其中就包括了动态壁纸的加载。其中扫描动态壁纸数量的实现是在`LiveWallpaperInfo.getAll()`方法中，将该方法移到Settings中加载壁纸来源处代码即可。

## 2.2 具体实现

```diff

diff --git a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/wallpaper/WallpaperTypePreferenceController.java b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/wallpaper/WallpaperTypePreferenceController.java
index 95797863d43..bbb8bc4c554 100644
--- a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/wallpaper/WallpaperTypePreferenceController.java
+++ b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/wallpaper/WallpaperTypePreferenceController.java
@@ -19,9 +19,11 @@ package com.android.settings.wallpaper;
 import android.content.ComponentName;
 import android.content.Context;
 import android.content.Intent;
+import android.content.pm.ApplicationInfo;
 import android.content.pm.PackageManager;
 import android.content.pm.ResolveInfo;
 import android.text.TextUtils;
+import android.service.wallpaper.WallpaperService;

 import androidx.preference.Preference;
 import androidx.preference.PreferenceScreen;
@@ -30,8 +32,15 @@ import com.android.settings.core.BasePreferenceController;
 import com.android.settingslib.core.lifecycle.LifecycleObserver;
 import com.android.settingslib.core.lifecycle.events.OnStart;

+import java.text.Collator;
+import java.util.ArrayList;
+import java.util.Arrays;
+import java.util.Collections;
+import java.util.Comparator;
+import java.util.Iterator;
 import java.util.List;
 import java.util.stream.Collectors;
+import java.util.Set;

 public class WallpaperTypePreferenceController extends BasePreferenceController
         implements LifecycleObserver, OnStart {
@@ -77,8 +86,16 @@ public class WallpaperTypePreferenceController extends BasePreferenceController
         removeUselessExistingPreference(rList);
         mScreen.setOrderingAsAdded(false);
         // Add Preference items for each of the matching activities
+        List<ResolveInfo> resolveInfos = getAllOnDevice(mContext);
         for (ResolveInfo info : rList) {
             final String packageName = info.activityInfo.packageName;
+
+            // dynamically display the "Live Wallpaper" option based on whether any live wallpaper exits on the device
+            // the idea come from "packages/apps/WallpaperPicker2/src/com/android/wallpaper/model/LiveWallpaperInfo.java".getAll()
+            // (by libin)
+            if (packageName.equals("com.android.wallpaper.livepicker") && resolveInfos.size() == 0) {
+                continue;
+            }
             Preference pref = mScreen.findPreference(packageName);
             if (pref == null) {
                 pref = new Preference(mScreen.getContext());
@@ -98,6 +115,58 @@ public class WallpaperTypePreferenceController extends BasePreferenceController
         }
     }

+    /**
+     * to get the size of liveWallpapaers in the device
+     * copy from "/packages/apps/WallpaperPicker2/src/com/android/wallpaper/model/LiveWallpaperInfo.java"  (by libin)
+     */
+    private static List<ResolveInfo> getAllOnDevice(Context context) {
+        final PackageManager pm = context.getPackageManager();
+        final String packageName = context.getPackageName();
+
+        List<ResolveInfo> resolveInfos = pm.queryIntentServices(
+                new Intent(WallpaperService.SERVICE_INTERFACE),
+                PackageManager.GET_META_DATA);
+
+        List<ResolveInfo> wallpaperInfos = new ArrayList<>();
+
+        // Remove the "Rotating Image Wallpaper" live wallpaper, which is owned by this package,
+        // and separate system wallpapers to sort only non-system ones.
+        Iterator<ResolveInfo> iter = resolveInfos.iterator();
+        while (iter.hasNext()) {
+            ResolveInfo resolveInfo = iter.next();
+            if (packageName.equals(resolveInfo.serviceInfo.packageName)) {
+                iter.remove();
+            } else if (isSystemApp(resolveInfo.serviceInfo.applicationInfo)) {
+                wallpaperInfos.add(resolveInfo);
+                iter.remove();
+            }
+        }
+
+        if (resolveInfos.isEmpty()) {
+            return wallpaperInfos;
+        }
+
+        // Sort non-system wallpapers alphabetically and append them to system ones
+        Collections.sort(resolveInfos, new Comparator<ResolveInfo>() {
+            final Collator mCollator = Collator.getInstance();
+
+            @Override
+            public int compare(ResolveInfo info1, ResolveInfo info2) {
+                return mCollator.compare(info1.loadLabel(pm), info2.loadLabel(pm));
+            }
+        });
+        wallpaperInfos.addAll(resolveInfos);
+
+        return wallpaperInfos;
+    }
+    /**
+     * @return whether the given app is a system app
+     */
+    public static boolean isSystemApp(ApplicationInfo appInfo) {
+        return (appInfo.flags & (ApplicationInfo.FLAG_SYSTEM
+                | ApplicationInfo.FLAG_UPDATED_SYSTEM_APP)) != 0;
+    }
+
     private void removeUselessExistingPreference(List<ResolveInfo> rList) {
         final int count = mScreen.getPreferenceCount();
         if (count <= 0) {
```
## 2.3 遗留问题
在安装壁纸类apk如“动动壁纸”和应用市场类apk如“应用宝”时，“动态壁纸”中也会和其他动态壁纸一样显示这些应用，但点进去设置的话壁纸会变为黑色。这是不正常的，正常应该是只显示真正的动态壁纸。  
但在A14的pixel手机上也发现了类似现象的存在。


可能原因或许是因为这类apk声明了和wallpaper相关的**use-feature**，导致**被识别为动态壁纸**。但实际上真正的动态壁纸要声明的feature是**live_wallpaper**，经检查，上述异常apk也**未声明该feature**，而是一些其他wallpaper相关的。  

所以一种潜在的解决方式是在扫描可用动态壁纸时，用过packageName检查对应apk是否声明了live_wallpaper这个feature，如果没有，就不加入结果集合。  
以下是**未经验证**的解决方式：
```java
public class FeatureChecker {  
  
    public static boolean checkFeature(Context context, String packageName, String featureName) {  
        PackageManager pm = context.getPackageManager();  
        try {  
            PackageInfo packageInfo = pm.getPackageInfo(packageName, PackageManager.GET_CONFIGURATIONS);  
            FeatureInfo[] features = packageInfo.reqFeatures;  
            if (features != null) {  
                for (FeatureInfo feature : features) {  
                    if (feature.name != null && feature.name.equals(featureName)) {  
                        return true; // 应用程序声明了指定的特性  
                    }  
                }  
            }  
        } catch (PackageManager.NameNotFoundException e) {  
            e.printStackTrace();  
        }  
        return false; // 应用程序未声明指定的特性  
    }  
  
    public static void main(String[] args) {  
        // 示例用法  
        Context context = null; // 替换为你的应用程序上下文  
        String packageName = "com.example.myapp"; // 替换为要检查的应用程序包名  
        String featureName = "android.hardware.camera"; // 替换为要检查的特性名称  
        boolean hasFeature = checkFeature(context, packageName, featureName);  
        if (hasFeature) {  
            System.out.println("应用程序包含了 " + featureName + " 特性");  
        } else {  
            System.out.println("应用程序未声明 " + featureName + " 特性");  
        }  
    }  
}
```

# 3 相关资料
[]()