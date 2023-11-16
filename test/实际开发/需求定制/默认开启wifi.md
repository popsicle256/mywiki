# 目标
开机默认开启wifi  

# 实现
T0  
```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/SettingsProvider/res/values/defaults.xml b/vendor/mediatek/proprietary/packages/apps/SettingsProvider/res/values/defaults.xml
index a792f9b5779..470e2a5ac59 100644
--- a/vendor/mediatek/proprietary/packages/apps/SettingsProvider/res/values/defaults.xml
+++ b/vendor/mediatek/proprietary/packages/apps/SettingsProvider/res/values/defaults.xml
@@ -48,7 +48,7 @@
     <bool name="assisted_gps_enabled">true</bool>
     <bool name="def_netstats_enabled">true</bool>
     <bool name="def_usb_mass_storage_enabled">true</bool>
-    <bool name="def_wifi_on">false</bool>
+    <bool name="def_wifi_on">true</bool>
     <!-- 0 == never, 1 == only when plugged in, 2 == always -->
     <integer name="def_wifi_sleep_policy">2</integer>
     <bool name="def_wifi_wakeup_enabled">true</bool>
```