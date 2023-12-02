date：2023-11-23

# 目标

修改系统默认时区中国
# 实现

```diff
diff --git a/device/mediatek/system/common/system.prop b/device/mediatek/system/common/system.prop
index 83d8b178647..9ed4cdc1bbf 100644
--- a/device/mediatek/system/common/system.prop
+++ b/device/mediatek/system/common/system.prop
@@ -68,3 +68,4 @@ Build.BRAND=MTK
 # Disable iorapd
 ro.iorapd.enable=false

+persist.sys.timezone=Asia/Shanghai
```
# 相关资料
[]()
