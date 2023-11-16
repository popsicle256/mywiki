# 步骤
## 1 安装linux环境  
[在win10下安装linux](Windows%2010下安装Linux.md)  
## 2 安装Git
直接输入`git`，如果没提示要安装，表明安装成功
## 3 安装Repo启动器
[repo的gerrit地址](https://gerrit.googlesource.com/git-repo)
### 3.1 正常安装
```bash
sudo apt-get update
sudo apt-get install repo
```
### 3.2 非正常安装
如果上述安装出现错误，显示无法安装，尝试以下方法
```bash
export REPO=$(mktemp /tmp/repo.XXXXXXXXX)
curl -o ${REPO} https://storage.googleapis.com/git-repo-downloads/repo
gpg --recv-key 8BB9AD793E8E6153AF0F9A4416530D5E920F5C65
curl -s https://storage.googleapis.com/git-repo-downloads/repo.asc | gpg --verify - ${REPO} && install -m 755 ${REPO} ~/bin/repo
```
若仍无法安装，请尝试以下方法
```bash
# 创建bin文件夹并将其加入到系统环境变量中
mkdir ~bin
PATH=~/bin:$PATH
# 下载Repo工具（前提要有python，默认会有python3）
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
# 添加可执行权限
chmod a+x ~/bin/repo
```

安装后输入`repo --version`查看是否安装成功  
	若出现语法错误提示（print相关），表明当前安装的python版本有问题，需要卸载python2，安装pythons3
#### 3.2.1 卸载python2
```bash
sudo apt-get remove python-is-python2
```
#### 3.2.2 将python3连接为python
如果系统中安装过python（2），则会将python2设置为默认版本，所以会出现上述语法错误，需要将python3设置为默认版本。
```bash
# 查找python3的位置
whereis python3
# 为其创建符号连接
sudo ln -s /usr/bin/pythons /usr/bin/python
```
## 4 初始化Repo
### 4.1 创建放置aosp的文件夹
```bash
mkdir AOSP
cd AOSP
```
**后续所有操作都要在该文件夹下进行**。
### 4.2 repo init初始化仓库
初始化命令如下
```bash
repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest -b android-13.0.0_r60
```
其中`-u`参数指定仓库地址，`-b`指定要checkout的分支。
最后显示`repo has been initialized`说明初始化成功。
#### 4.2.1 长时间无反应
[Git Repo 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/git-repo/)
Downloading Repo source from https://gerrit.googlesource.com/git-repo
终端一直显示上述内容无变化，表明repo在使用google源更新自己（连接不上）。  
为解决该问题，需要指定使用清华的源进行更新。
`vi ~/.bashrc`，然后将如下内容复制到最后，然后重启终端。  
`export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo'  
再次执行上述init命令后，显示的为`Downloading Repo source from https://mirrors.tuna.tsinghua.edu.cn/git/git-repo`
表明在使用清华源更新了。
## 5 下载AOSP源码
`repo sync`
运行该命令会开始下载AOSP源码到**当前工作目录**中。
## 6 编译

### 6.1 swap分区过小导致fail
编译出现下述问题：  
```bash
[100% 1/1] analyzing Android.bp files and generating ninja file at out/soong/build.ninja
FAILED: out/soong/build.ninja
cd "$(dirname "out/host/linux-x86/bin/soong_build")" && BUILDER="$PWD/$(basename "out/host/linux-x86/bin/soong_build")" && cd / && env -i  "$BUILDER"     --top "$TOP"     --soong_out "out/soong"     --out "out"     -o out/soong/build.ninja --globListDir build --globFile out/soong/globs-build.ninja -t -l out/.module_paths/Android.bp.list --available_env out/soong/soong.environment.available --used_env out/soong/soong.environment.used.build Android.bp
Killed
09:39:18 soong bootstrap failed with: exit status 1
ninja: build stopped: subcommand failed.

#### failed to build some targets (05:39 (mm:ss)) ####
```
*原因*：swap分区过小，导致编译失败。  
*解决*：扩大swap分区。  
**1、查看swap分区大小** 
`free -h`  
![](img/Pasted%20image%2020231110105935.png)
**2、关闭swap分区**  
`sudo swapoff /swapfile`   
	若提示`swapoff: /swapfile: swapoff failed: No such file or directory`，表明还未建立swap分区，需要通过`sudo apt-get install dphys-swapfile`建立
若上述命令失效，可使用`sudo swapoff -a`关闭所有swap。  
关闭后可通过查看分区大小来确认是否关闭。  
![](img/Pasted%20image%2020231110110400.png)
**3、调整/swapfile大小，并格式化**  
```bash
sudo fallocate -l 8G /swapfile 
 
sudo chmod 600 /swapfile
 
sudo mkswap /swapfile 
mkswap: /swapfile: warning: wiping old swap signature.
Setting up swapspace version 1, size = 8 GiB (8589930496 bytes)
no label, UUID=000a000b-000c-000d-000e-000f00010002
```
**4、启动swap分区，查看内存分区**  
```bash
sudo swapon /swapfile
free -h
```
### 6.2 libncurses.so.5相关FAILED
```bash
FAILED: out/soong/.intermediates/external/guice/guice_munge_srcjar/gen/guice_munge.srcjar  
rm -rf out/soong/.intermediates/external/guice/guice_munge_srcjar/gen && out/soong/host/linux-x86/bin/sbox --sandbox-path out/soongemp --manifest out/soong/.intermediates/external/guice/guice_munge_srcjar/genrule.sbox.textproto  
The failing command was run inside an sbox sandbox in temporary directory  
out/soong/.temp/sbox/6c6df8b68c4e18f58c1366ddca3a64bf009d9659  
The failing command line was:  
zip -q --temp-path ${TMPDIR:-/tmp} external/guice/lib/build/munge.jar -O out/soong/.temp/sbox/6c6df8b68c4e18f58c1366ddca3a64bf009d9/out/guice_munge.srcjar -d MungeTask.java *.class  
bash: zip: command not found
```
**原因**：缺少解压相关工具  
**解决**：  
`sudo apt-get install zip`
### 6.3 srcjar相关FAILED
```bash
error while loading shared libraries: libncurses.so.5: cannot open shared object file
```
**原因**：缺少对应的so库
**解决**：
1）查看是否存在对应的库
```bash
find / -name 'libncurses*'

/usr/lib64/libncurses.so.6
```
2）若存在对应的库，则将该库进行软连接
```bash
ln -s /usr/lib64/libncurses.so.6 /usr/lib64/libncurses.so.5
ln -s /usr/lib64/libtinfo.so.6 /usr/lib64/libtinfo.so.5
```
# 问题
1、拉取repo时出现错误  

sudo apt-get install repo

这个命令后出现提示`E: Unable to locate package repo`

# 编译实况
![](img/Pasted%20image%2020231110112500.png)
PC负载情况如上，如图所示，可看到目前负载最高的是磁盘0和内存，基本拉满。说明目前编译过程中，受这两个器件的影响最大，因为磁盘是机械的，而分给Ubuntu的内存也只有9G（swap8G）。但是CPU的负载却较低，维持在20%左右。  
所以要想提高编译速度，要求磁盘快、内存大、CPU速度高。硬盘首先要固态就不提了，内存的话严重决定编译的成功和速度，官方推荐是64...，实际上远小于这个值也可以编译，但时间会拉长很多很多，官方推荐配置下，一次编译时间在40min左右，而上述硬件条件下，估计得五六个小时不止。  
所以要想在编译过程中还能愉快地进行其他工作，128的内存怕是跑不掉。  

![](img/Pasted%20image%2020231110154153.png)
表示害怕😰5h了才在第一步   

![](img/Pasted%20image%2020231110175857.png)