# 1 常用命令
```
// 查看指定文件夹
adb shell ls system/ 

// 获取当前焦点所在应用的包名和类名
adb shell dumpsys window | findstr mCurrentFocus

adb shell pm list package  // 获取所有应用的包名（包括内置和第三方）
adb shell pm list package -3  // 获取安装的第三方应用的包名

// 查看包的安装位置
adb shell pm list packages -f | findstr camera  

// 获取应用包所在位置
adb shell pm path com.android.settings

// 获取已安装应用的apk包
adb pull /system/system_ext/priv-app/MtkSettings/MtkSettings.apk D:\MIUISettings.apk

// 根据应用包名和activity启动app
adb shell am start -n com.zkzk945.zk2048cn/com.zkzk945.zk2048cn.MainActivity

// 查看当前正在运行的应用
adb shell dumpsys activity | findstr "Running activites"

// Failure [INSTALL_FAILED_TEST_ONLY: installPackageLI]
adb install -t 

// 截图保存到设备
adb shell screencap -p /sdcard/screen.png
//将设备中的图片pull到电脑中
adb pull /sdcard/screen.png C:\Users\Administrator\Pictures

// 安装系统应用（需要在adb remount后进行，安装后杀死应用进程或重启机器方能生效）
adb push D:\MtkSystemUI.apk /system/system_ext/priv-app/MtkSystemUI/MtkSystemUI.apk

adb push \\192.168.0.246\软件部\临时文件\libin\t8323\MtkSystemUI.apk /system/system_ext/priv-app/MtkSystemUI/MtkSystemUI.apk

// kill SystemUI进程
// 注意：该命令需要在获取权限后才能生效（adb remount）
FOR /F "tokens=2" %G IN ('adb shell ps ^| find "com.android.systemui"') DO adb shell kill %G

// 手动修改电池温度（此时电池广播不会再自动发出，只有恢复设置或者手动再次改变设置时才会发出）
adb shell dumpsys battery set temp 468
// 恢复电池各项设置
adb shell dumpsys battery reset

// 跳过skd版本检查
adb install --bypass-low-target-sdk-block -g D:\Documents\ding\工具\APK\DataFillerS_v2.1填充.apk

// 修改屏幕density
adb shell wm density 240

// gms相关-查看features
pm list features
```

# 2 root（mtk）
```
Q:– go to setting -> system -> Developer options -> OEM unlocking
adb reboot bootloader

fastboot flashing unlock
press volume up key

fastboot reboot
adb root
adb disable-verity
adb reboot
adb root
adb remount

adb remount：将‘/system’置于可写入模式，默认情况下该部分为只读模式
```