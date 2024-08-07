date：2024-03-13

# 1 现象
插入type-c耳机后，音量调大到3阶以上时，就会出现音量过高提示框（音量总阶为15）。  
3.5mm耳机音量过高提示时机正常，在10阶时才会提示。  

# 2 原因

`checkSafeMediaVolume()`方法的调用来源如下：  
![](img/Pasted%20image%2020240313152214.png)

检查音量是否安全，其中`index`为要调节到的目标音量，`device`为设备类型。    
`frameworks/base/services/core/java/com/android/server/audio/AudioService.java`
```java
    private boolean checkSafeMediaVolume(int streamType, int index, int device) {
        synchronized (mSafeMediaVolumeStateLock) {
            if ((mSafeMediaVolumeState == SAFE_MEDIA_VOLUME_ACTIVE)
                    && (mStreamVolumeAlias[streamType] == AudioSystem.STREAM_MUSIC)
                    && (mSafeMediaVolumeDevices.contains(device))
                    && (index > safeMediaVolumeIndex(device))) {
                return false;
            }
            return true;
        }
    }
```

当type-c耳机插入时，`device=67108864`，即为`AudioSystem.DEVICE_OUT_USB_HEADSET`，  
3.5mm耳机插入时，`device=4`.    
所以两种不同类型的耳机，对应的安全音量索引值是不同的（至少说生成方式是不一样的）。
`frameworks/base/services/core/java/com/android/server/audio/AudioService.java`
```java
    private int safeMediaVolumeIndex(int device) {
        if (!mSafeMediaVolumeDevices.contains(device)) {
            return MAX_STREAM_VOLUME[AudioSystem.STREAM_MUSIC];
        }
        if (device == AudioSystem.DEVICE_OUT_USB_HEADSET) {
            return mSafeUsbMediaVolumeIndex;  // 30
        } else {
            return mSafeMediaVolumeIndex;  // 100
        }
    }
```
其中`mSafeMediaVolumeIndex = mContext.getResources().getInteger(com.android.internal.R.integer.config_safe_media_volume_index) * 10;`  结果值为100.  

而`mSafeUsbMediaVolumeIndex = getSafeUsbMediaVolumeIndex();` 该值在开机时会生成。
该方法的主要内容为通过**二分法**找到符合**安全 USB 媒体音量要求**（`mSafeUsbMediaVolumeDbfs`）的音量索引。  
`frameworks/base/services/core/java/com/android/server/audio/AudioService.java`
```java
    private int getSafeUsbMediaVolumeIndex() {
        // determine UI volume index corresponding to the wanted safe gain in dBFS
        int min = MIN_STREAM_VOLUME[AudioSystem.STREAM_MUSIC];
        int max = MAX_STREAM_VOLUME[AudioSystem.STREAM_MUSIC];

        mSafeUsbMediaVolumeDbfs = mContext.getResources().getInteger(
                com.android.internal.R.integer.config_safe_media_volume_usb_mB) / 100.0f;
        while (Math.abs(max - min) > 1) {
            int index = (max + min) / 2;
            float gainDB = AudioSystem.getStreamVolumeDB(
                    AudioSystem.STREAM_MUSIC, index, AudioSystem.DEVICE_OUT_USB_HEADSET);
            if (Float.isNaN(gainDB)) {
                //keep last min in case of read error
                break;
            } else if (gainDB == mSafeUsbMediaVolumeDbfs) {
                min = index;
                break;
            } else if (gainDB < mSafeUsbMediaVolumeDbfs) {
                min = index;
            } else {
                max = index;
            }
        }
        return min * 10;
    }
```

# 3 解决
## 3.1 临时解决-固定值
将usb的值直接设定为3.5mm的值。  

```diff
diff --git a/frameworks/base/services/core/java/com/android/server/audio/AudioService.java b/frameworks/base/services/core/java/com/android/server/audio/AudioService.java
index 63d3d701fa..179ae36c0a 100644
--- a/frameworks/base/services/core/java/com/android/server/audio/AudioService.java
+++ b/frameworks/base/services/core/java/com/android/server/audio/AudioService.java
@@ -8826,7 +8826,8 @@ public class AudioService extends IAudioService.Stub
             return MAX_STREAM_VOLUME[AudioSystem.STREAM_MUSIC];
         }
         if (device == AudioSystem.DEVICE_OUT_USB_HEADSET) {
-            return mSafeUsbMediaVolumeIndex;
+            //return mSafeUsbMediaVolumeIndex;
+            return mSafeMediaVolumeIndex;
         } else {
             return mSafeMediaVolumeIndex;
         }
```

# 相关资料
[]()