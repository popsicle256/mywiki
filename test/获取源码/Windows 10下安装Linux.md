# 1 开启WSL2
直接cmd命令窗口下输入：
`wsl --install`：自动安装WSL，安装完成后重启生效。  
`wsl -l -w`：查看目前系统已安装的内容（若非对应显示内容，则表明wsl开启失败）
# 2 安装Ubuntu

点击开始菜单中的Ubuntu图标可自动安装最新版本。  
也可通过命令执行

## 2.1 移动安装位置
如果WSL2是按上述默认安装的，那么在其中安装的系统都是在**系统盘**中，可以移动到其他位置。  
1）关闭wsl：`wsl --shutdown`  
2）查看wsl状态：`wsl -l --all -v`，若处于Stopped状态，则可导出  
3）将虚拟机镜像导出到其他盘 `wsl --export Ubuntu-20.04 E:\wsl-ubuntu20.04.tar`  
4）注销当前分发版：`wsl --unregister Ubuntu-20.04`
5）查看wsl状态，若显示"适用于 Linux 的 Windows 子系统没有已安装的分发版"则可继续  
6）重新将镜像导入到指定位置：`wsl --import Ubuntu-20.04 E:\wsl-ubuntu20.04 E:\wsl-ubuntu20.04.tar --version 2`  
7）设置默认登录用户名：`ubuntu2004 config --default-user [Username]`  
8）删除tar文件：`del E:\wsl-ubuntu20.04.tar`
# 3 如何进入
任意命令行窗口，`bash`直接进入  
点击图标进入
# 4 如何关闭
cmd窗口中，`wsl --shutdown`