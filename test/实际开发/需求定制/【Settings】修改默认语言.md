
# 目标

修改系统默认语言为中文
# 实现
1.修改为“中文”

```diff
diff --git a/device/mediatek/system/mssi_t_t8323_ummeiw_T105_64_k419/sys_mssi_t_t8323_ummeiw_T105_64_k419.mk b/device/mediatek/system/mssi_t_t8323_ummeiw_T105_64_k419/sys_mssi_t_t8323_ummeiw_T105_64_k419.mk
index aa8102aeed8..9d70cac149a 100644
--- a/device/mediatek/system/mssi_t_t8323_ummeiw_T105_64_k419/sys_mssi_t_t8323_ummeiw_T105_64_k419.mk
+++ b/device/mediatek/system/mssi_t_t8323_ummeiw_T105_64_k419/sys_mssi_t_t8323_ummeiw_T105_64_k419.mk
@@ -26,7 +26,7 @@ PRODUCT_PACKAGES += androidx.window.sidecar
 # SystemConfig read platform permissions last so it will take precedence
 PRODUCT_COPY_FILES += $(SYS_TARGET_PROJECT_FOLDER)/system_system_ext_platform.xml:$(TARGET_COPY_OUT_SYSTEM_EXT)/etc/permissions/platform.xml

-PRODUCT_LOCALES := en_US zh_CN zh_TW es_ES pt_BR ru_RU fr_FR de_DE tr_TR vi_VN ms_MY in_ID th_TH it_IT ar_EG hi_IN bn_IN ur_PK fa_IR pt_PT nl_NL el_GR hu_HU tl_PH ro_RO cs_CZ ko_KR km_KH iw_IL my_MM pl_PL es_US bg_BG hr_HR lv_LV lt_LT sk_SK uk_UA de_AT da_DK fi_FI nb_NO sv_SE en_GB hy_AM zh_HK et_EE ja_JP kk_KZ sr_RS sl_SI ca_ES
+PRODUCT_LOCALES := zh_CN en_US zh_TW es_ES pt_BR ru_RU fr_FR de_DE tr_TR vi_VN ms_MY in_ID th_TH it_IT ar_EG hi_IN bn_IN ur_PK fa_IR pt_PT nl_NL el_GR hu_HU tl_PH ro_RO cs_CZ ko_KR km_KH iw_IL my_MM pl_PL es_US bg_BG hr_HR lv_LV lt_LT sk_SK uk_UA de_AT da_DK fi_FI nb_NO sv_SE en_GB hy_AM zh_HK et_EE ja_JP kk_KZ sr_RS sl_SI ca_ES
 PRODUCT_MANUFACTURER := alps
 PRODUCT_NAME := sys_mssi_t_t8323_ummeiw_T105_64_k419
 PRODUCT_DEVICE := $(strip $(SYS_BASE_PROJECT))date：2023-11-23
```

2.修改为“简体中文”
```diff
diff --git a/device/mediatek/system/common/system.prop b/device/mediatek/system/common/system.prop
index 9ed4cdc1bbf..6f21299c6ac 100644
--- a/device/mediatek/system/common/system.prop
+++ b/device/mediatek/system/common/system.prop
@@ -69,3 +69,4 @@ Build.BRAND=MTK
 ro.iorapd.enable=false

 persist.sys.timezone=Asia/Shanghai
+persist.sys.locale=zh-Hans-CN
```
# 相关资料
[]()
