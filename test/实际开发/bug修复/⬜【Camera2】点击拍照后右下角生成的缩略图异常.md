date：2024-01-30

# 1 现象

点击拍照，完成拍照后，右下角生成的缩略图异常，表现为马赛克样，很模糊。  
点击缩略图进入相册后，相册图片正常，且此时返回相机界面右下角的缩略图显示正常。  
在拍照完成后，退出相机再进入后，右下角缩略图亦正常。  
也即异常缩略图只在拍照完成后产生，离开拍照界面再返回后显示就正常了。  
# 2 原因
拍照完成后生成的缩略图与进入相机后获取的缩略图不同，拍照时生成的缩略图有问题。  

# 3 解决
由于后续进入相机界面，上一张照片的缩略图是正常的，所以找到进入相机界面时缩略图的获取路径，然后将拍照生成缩略图的方法改为从正常缩略图的路径获取缩略图。  

经验证，拍照完成后，走的是`updateThumbnail()`->`updateThumbnailView()`，
而进入相机界面时加载缩略图走的是通过`getLastThumbnail()`来**启动异步类**`LoadBitmapTask`：`onPostExecute()`->`updateThumbnailView()`,  
所以只需要将拍照完成后缩略图的生成方法改为即可  

但发现，如果直接调用，会导致缩略图获取到的是上一张的图片，所以不能直接调用，需要delay一段时间后，等正常缩略图生成后，再更新，此时获取到的就是正常的缩略图。200ms是经验证较为合适的值，既可以获取到最新值，又不会导致相机界面拍照完成后缩略图刷新时间过长。

```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/Camera2/host/src/com/mediatek/camera/ui/ThumbnailViewManager.java b/vendor/mediatek/proprietary/packages/apps/Camera2/host/src/com/mediatek/camera/ui/ThumbnailViewManager.java
index e74610d653f..661366f3916 100755
--- a/vendor/mediatek/proprietary/packages/apps/Camera2/host/src/com/mediatek/camera/ui/ThumbnailViewManager.java
+++ b/vendor/mediatek/proprietary/packages/apps/Camera2/host/src/com/mediatek/camera/ui/ThumbnailViewManager.java
@@ -47,6 +47,7 @@ import android.graphics.Canvas;
 import android.graphics.Paint;
 import android.os.AsyncTask;
 import android.os.Build;
+import android.os.Handler;
 import androidx.core.graphics.drawable.RoundedBitmapDrawable;
 import androidx.core.graphics.drawable.RoundedBitmapDrawableFactory;
 import android.view.View;
@@ -190,7 +191,19 @@ class ThumbnailViewManager extends AbstractViewManager {
      *            orientation, content. suggest thumbnail view size.
      */
     public void updateThumbnail(Bitmap bitmap) {
-        updateThumbnailView(bitmap);
+        // the original code to update thumbnail after the ShutterButton click in the camera ui（thumbnail will be mosaic）（by libin）
+        // updateThumbnailView(bitmap);
+
+        // to escape the mosaic thumbnail after the ShutterButton click by the getLastThumbnail(),
+        // which use AsyncTask to acquire the right thumbnail.
+        // the delay is to wait the right thumbnail created, otherwise, it will be the previous picture（by libin）
+        new Handler().postDelayed(new Runnable() {
+            @Override
+            public void run() {
+                getLastThumbnail();
+            }
+        }, 200);
+
         if (bitmap != null) {
             doAnimation(mAnimationView);
         } else {
```

ThumbnailViewManager.updateThumbnail() <- CameraAppUI.updateThumbnail() <- PhotoMode.onPostViewCallback() <- PhotoDevice2Controller.onPictureCallback() <- 

# 4 根本原因分析


# 相关资料
[]()