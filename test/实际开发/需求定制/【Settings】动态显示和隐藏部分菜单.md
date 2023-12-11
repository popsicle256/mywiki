date：2023-11-29

# 1 目标
在进入开发者模式后，设置中所有菜单都显示完整；非开发者模式下，设置中只显示部分菜单。  
也即在非开发者模式下，设置中的部分一级菜单和二级菜单（对应一级菜单保留）需要去除。

# 2 实现
## 2.1 去除一级菜单
### 2.1.1 静态去除
直接注释**top_level_settings.xml**中对应的菜单选项即可。  
这种方式的劣势是注释后就完全去除了，无法再动态显示出，适合需要彻底去除的场景。
### 2.1.2 动态去除
在菜单加载的过程中根据条件选择**加载不同的xml文件**，即可实现不同情况下加载不同的设置菜单。  
其中的条件即为：**是否处于开发者模式**。  
#### 2.1.2.1 判断是否进入了开发者模式


#### 2.1.2.2 更换xml文件的时机



```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/homepage/TopLevelSettings.java b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/homepage/TopLevelSettings.java
index 70530fc8cd3..06e829c907f 100644
--- a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/homepage/TopLevelSettings.java
+++ b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/homepage/TopLevelSettings.java
@@ -29,6 +29,7 @@ import android.text.TextUtils;
 import android.util.Log;
 import android.view.LayoutInflater;
 import android.view.ViewGroup;
+import android.provider.Settings;

 import androidx.fragment.app.Fragment;
 import androidx.preference.Preference;
@@ -72,9 +73,17 @@ public class TopLevelSettings extends DashboardFragment implements SplitLayoutLi
         setArguments(args);
     }

+    /*
+     * if the development_settings is enabled, return the original settings
+     * if not, return the customed settings (by libin)
+     */
     @Override
     protected int getPreferenceScreenResId() {
-        return R.xml.top_level_settings;
+        int development_enabled = Settings.Global.getInt(getContext().getContentResolver(), "development_settings_enabled", 1);
+        if(development_enabled == 1) {
+            return R.xml.top_level_settings;
+        }
+        return R.xml.top_level_settings_custom;
     }

     @Override
```

**top_level_settings.xml**中的`android:title="@string/page_tab_title_support"`这个**preference**不能删除，否则MtkSettings.apk会无法打开，以下为报错主要内容：
```log
11-29 21:59:14.851  5718  5718 E AndroidRuntime: java.lang.NullPointerException: Attempt to invoke virtual method 'void com.android.settings.support.SupportPreferenceController.setActivity(android.app.Activity)' on a null object reference
```
貌似是因为该preference是和分页相关的，去掉后无法加载设置app。  
以下为全部报错log：
![](img/Pasted%20image%2020231129142303.png)

**network_provider_internet.xml**  
`android:title="@string/airplane_mode"`直接删除会报错：
![](img/Pasted%20image%2020231129153549.png)

经验证，是NetworkDashboardFragmen.java中会自动加载与飞行模式相关的模块，所以当将上述Preference直接删除时，就无法加载导致报错。需要在此处加入判断逻辑。
```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/network/NetworkDashboardFragment.java b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/network/NetworkDashboardFragment.java
index 197baae82a2..3b3a9786c45 100644
--- a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/network/NetworkDashboardFragment.java
+++ b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/network/NetworkDashboardFragment.java
@@ -24,6 +24,7 @@ import android.content.Intent;
 import android.os.Bundle;
 import android.provider.SearchIndexableResource;
 import android.util.Log;
+import android.provider.Settings;

 import androidx.appcompat.app.AlertDialog;
 import androidx.fragment.app.Fragment;
@@ -72,6 +73,11 @@ public class NetworkDashboardFragment extends DashboardFragment implements
     public void onAttach(Context context) {
         super.onAttach(context);

+        int development_enabled = Settings.Global.getInt(context.getContentResolver(), "development_settings_enabled", 1);
+        if(development_enabled == 1) {
+            Log.d(TAG + " << By libin >> ", "development_enabled = " + development_enabled);
+            return;
+        }
         use(AirplaneModePreferenceController.class).setFragment(this);
         getSettingsLifecycle().addObserver(use(AllInOneTetherPreferenceController.class));
     }
```

`android:title="@string/tether_settings_title_all"`直接删除会报错：
![](img/Pasted%20image%2020231129153240.png)

动态删除设置中的“壁纸”一级菜单：
```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/display/TopLevelWallpaperPreferenceController.java b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/display/TopLevelWallpaperPreferenceController.java
index 0136eac187b..5ec04d8da4c 100644
--- a/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/display/TopLevelWallpaperPreferenceController.java
+++ b/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/display/TopLevelWallpaperPreferenceController.java
@@ -26,6 +26,7 @@ import android.content.pm.ResolveInfo;
 import android.os.UserHandle;
 import android.text.TextUtils;
 import android.util.Log;
+import android.provider.Settings;

 import androidx.preference.Preference;
 import androidx.preference.PreferenceScreen;
@@ -36,6 +37,7 @@ import com.android.settings.activityembedding.ActivityEmbeddingUtils;
 import com.android.settings.core.BasePreferenceController;
 import com.android.settingslib.RestrictedLockUtilsInternal;
 import com.android.settingslib.RestrictedTopLevelPreference;
+import com.android.settingslib.development.DevelopmentSettingsEnabler;

 import java.util.List;

@@ -68,6 +70,15 @@ public class TopLevelWallpaperPreferenceController extends BasePreferenceControl
                 getComponentName(),
                 null /* secondaryIntentAction */,
                 true /* clearTop */);
+        int development_enabled = Settings.Global.getInt(mContext.getContentResolver(), "development_settings_enabled", 1);
+        Log.d(TAG + " << By libin >> ", "development_enabled = " + development_enabled);
+        if(DevelopmentSettingsEnabler.isDevelopmentSettingsEnabled(mContext)){
+            Log.d(TAG + " << By libin >> ", "developmentSettings enabled!!!!!!!!!");
+            screen.removePreference(screen.findPreference(getPreferenceKey()));
+        } else {
+            Log.d(TAG + " << By libin >> ", "developmentSettings is not enabled *********** ");
+        }
+
     }
```

## 2.2 去除部分二级菜单
二级菜单的去除也是动态的，即需要找到加载对应菜单布局的时机，然后根据条件切换不同的布局，整体思路和去除一级菜单相似，其中比较重要的是如何查找到加载对应菜单的java代码是在哪：
### 2.2.1 去除“系统”中的“备份”和“多用户”
这两个二级菜单在“系统”对应的xml布局文件中并没有对应项，表明这两个菜单是被动态加载的，所以需要在动态加载的过程中去除。

### 2.2.2 “网络和互联网”中只保留“互联网”


# 3 相关资料
[]()