date：2024-04-18

# 1 目标
因设备不配备基座，所以在相关选项中要去除和“基座”相关的选项。

# 2 实现

```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/values/arrays.xml b/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/values/arrays.xml
index 36e9f849daf..114ec28f822 100644
--- a/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/values/arrays.xml
+++ b/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/values/arrays.xml
@@ -1019,26 +1019,26 @@
          while on battery -->
     <string-array name="when_to_start_screensaver_entries_no_battery" translatable="false">
         <item>@string/screensaver_settings_summary_sleep</item>
-        <item>@string/screensaver_settings_summary_dock_and_charging</item>
+        <!-- <item>@string/screensaver_settings_summary_dock_and_charging</item> -->
     </string-array>

     <!-- Values for screensaver "When to start" for devices that do not support screensavers
          while on battery -->
     <string-array name="when_to_start_screensaver_values_no_battery" translatable="false">
         <item>while_charging_only</item>
-        <item>while_docked_only</item>
+        <!-- <item>while_docked_only</item> -->
     </string-array>

     <string-array name="when_to_start_screensaver_entries" translatable="false">
         <item>@string/screensaver_settings_summary_sleep</item>
-        <item>@string/screensaver_settings_summary_dock</item>
+        <!-- <item>@string/screensaver_settings_summary_dock</item> -->
         <item>@string/screensaver_settings_summary_either_long</item>
     </string-array>

     <string-array name="when_to_start_screensaver_values" translatable="false">
         <item>while_charging_only</item>
-        <item>while_docked_only</item>
-        <item>either_charging_or_docked</item>
+        <!-- <item>while_docked_only</item>
+        <item>either_charging_or_docked</item> -->
     </string-array>

     <string-array name="zen_mode_contacts_calls_entries" translatable="false">
```

# 3 相关资料
[]()