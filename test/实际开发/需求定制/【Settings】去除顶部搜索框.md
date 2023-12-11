date：2023-12-02

# 1 目标
去除设置中顶部的搜索框。

# 2 实现
设置中的搜索框有两种类型：只在左半部分顶端有和占据整个顶端部分。根据实际设置布局的不同而不同，所以在更改对应配置时需要分辨是哪种类型的搜索框，找到对应的文件。  
下面更改即为左半部分顶部的搜索框。  
该更改通过直接在**设置首页xml布局文件中注释引入搜索框的代码**，实现彻底去除搜索框。  
如果只是更改搜索框对应的xml布局文件，可去除搜索框相关的内容，但该位置仍然会有个白色矩形框占据，比较不美观。  


```diff
diff --git a/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/layout/settings_homepage_app_bar_two_pane_layout.xml b/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/layout/settings_homepage_app_bar_two_pane_layout.xml
index b9c149303fd..811fe7f2dad 100644
--- a/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/layout/settings_homepage_app_bar_two_pane_layout.xml
+++ b/vendor/mediatek/proprietary/packages/apps/MtkSettings/res/layout/settings_homepage_app_bar_two_pane_layout.xml
@@ -24,8 +24,8 @@
     android:padding="@dimen/homepage_app_bar_padding_two_pane"
     android:orientation="horizontal"
     android:background="@drawable/homepage_app_bar_background">
-
-    <include layout="@layout/search_bar_two_pane_version"/>
+<!--
+    <include layout="@layout/search_bar_two_pane_version"/>  -->

     <ImageView
         android:id="@+id/account_avatar_two_pane_version"
```
# 3 相关资料
[]()