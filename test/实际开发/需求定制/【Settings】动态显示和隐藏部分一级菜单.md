date：2023-11-29

# 1 目标


# 2 实现

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
# 3 相关资料
[]()