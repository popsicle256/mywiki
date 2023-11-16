# 1 现象
正常通过复制EmTester方式预置应用后，  
如果**LOCAL_CERTIFICATE**设置为**platform**，则可正常预置，无任何问题；  
但如果设置为**PRESIGNED**，则会出现刷机后找不到该应用，但在设备中的`system/app`目录下又存在对应apk  

# 2 原因
根据开机后的log，查找其中的apk名称关键词，找到`11-14 16:47:21.398  1043  1043 W PackageManager: Failed to scan /system/app/irest: No APK Signature Scheme v2 signature in package /system/app/irest/irest.apk`记录，表明是签名相关问题。  
## 2.1 查看apk签名方法
首先需要查看apk是否有签名，然后查看其签名的版本及具体内容信息。  

### 2.1.1 验证apk是否有签名
`keytool -list -printcert -jarfile D:\Desktop\编译文件\t8323\系统签名相关\111.apk`
![](img/Pasted%20image%2020231116093521.png)

#### 2.1.1.1 keytool不是内部或外部命令
keytool是java内容的工具，所以需要在java环境下才能运行。  
1）检查当前路径是否能访问到java环境  
`java -version`  
2）添加系统变量  
如果安装了java，但访问不到，需要将jdk路径配置到**系统变量Path**中，如`C:\Program Files\java\jdk1.8\bin`

### 2.1.2 验证apk具体签名的版本
`apksigner verify --v D:\Documents\ding\项目相关\8323豪中豪\1578266229997632.apk`
![](img/Pasted%20image%2020231116101844.png)
可查看apk中存在哪些版本的签名。  
#### 2.1.2.1 apksigner获取
需要在android sdk中的build-tools中打开cmd才能正常运行apksigner。  

## 2.2 对比apk签名
因为apk存在签名，且apk在非预置情况下可正常安装，故怀疑是否在安装过程中签名出现了问题。  

对比预置前的apk和预置后在设备`syetem/app`目录中的apk，看二者的签名版本以及签名具体内容是否一致。  
经对比，发现**预置后的apk没有签名**，表明在编译过程中apk的签名受到了破坏。
### 2.2.1 签名被破坏的原因
目前预置apk的方式是通过放入到工程中用android.mk进行编译，而这个过程中android编译系统会**使用zipalign对apk进行字节对齐等操作**（使得apk加载更快），而这个操作会导致apk产生变化，而**V2是对apk整体签名，一旦apk发生变化，V2就会失效**。  
所以在使用PRESIGNED签名时，编译后apk自身的V2签名会被破坏，进而无法正常打开。

# 3 解决
## 3.1 使用CP方式预置（未跑通，刷机后找不到该应用）
```mk
LOCAL_PATH:= $(call my-dir)

copy_files :=

ifeq ($(strip $(SILENT_INSTALL)), yes)
$(foreach file, $(patsubst $(LOCAL_PATH)/%, %, $(shell find $(LOCAL_PATH) -name *.apk.1)), \
    $(eval copy_files += $(LOCAL_PATH)/$(file):$(dir system/install/apk/$(file))))
endif

$(call smart-copy-file-to-target, $(copy_files))


define smart-copy-file-to-target
$(eval unique_copy_files_destinations :=) \
$(foreach cf,$(1), \
    $(eval _dest := $(lastword $(subst :, ,$(cf)))) \
    $(if $(filter $(DEST_NULL_PATH), $(_dest)), ,\
        $(eval _src := $(wildcard $(firstword $(subst :, ,$(cf))))) \
        $(foreach __src, $(_src), \
            $(eval __dest := $(subst //,/,$(PRODUCT_OUT)/$(_dest)/$(notdir $(__src)))) \
            $(if $(filter $(unique_copy_files_destinations),$(__dest)), \
                $(error discovered $(__dest) comes from serveral different location, please fix it), \
                $(eval $(call copy-one-file,$(__src),$(__dest))) $(eval ALL_DEFAULT_INSTALLED_MODULES += $(__dest)) \
                $(eval unique_copy_files_destinations += $(__dest))) \
        ) \
    ) \
) \
$(eval unique_copy_files_destinations :=)
endef
```

所以在使用**PRESIGNED**签名时，暂时无法成功预置应用。  
只能通过使用**platform**签名。后续apk要升级或重装仍然需要使用系统签名才能进行，对apk进行系统签名。

# 相关资料
[采用Signature Scheme v2签名方式的apk预置进系统失败](https://blog.csdn.net/long375577908/article/details/77165555?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170010196016800182767851%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=170010196016800182767851&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-77165555-null-null.nonecase&utm_term=%E9%A2%84%E7%BD%AE&spm=1018.2226.3001.4450)