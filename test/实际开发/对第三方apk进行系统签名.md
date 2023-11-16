# 1 通过集合的密钥包签名(win)
```bash
apksigner sign -ks "D:\Desktop\编译文件\t8323\系统签名相关\platform_t6676_r0.jks" "D:\Desktop\编译文件\t8323\系统签名相关\111.apk"
```

在win环境下进行。完成后文件夹中的111.apk即为已经签名成功的apk。  

其中的jks为打包好的密钥相关文件。  

# 2 通过密钥文件生成签名(linux)
`java -jar signapk.jar platform.x509.pem platform.pk8 111.apk irest.apk`

**签名相关文件：**
**signapk.jar**， 位置：`out/host/linux-x86/framework/signapk.jar`
**platform.x509.pem**，位置：`build/target/product/security/platform.x509.pem`
**platform.pk8**，位置：`build/target/product/security/platform.pk8`
## 2.1 no conscrypt_openjdk_jni in java.library.path相关问题
表明缺少相关库，将缺少的库放置到上述密钥文件路径下。  

**mac 系统：**  
`prebuilts/sdk/tools/darwin/lib64/libconscrypt_openjdk_jni.dylib`

**linux 系统：**  
`prebuilts/sdk/tools/linux/lib64/libconscrypt_openjdk_jni.so`

**需要使用以下新指令进行进行签名（linux环境下）：**

`java -Djava.library.path=. -jar signapk.jar platform.x509.pem platform.pk8 app-debug.apk app-debug_sign.apk`