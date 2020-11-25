### 1、windows wls 安装 
1. Microsoft Store应用商店
2. 设置 -》 更新和安全 -》 开启开发者选项
3. 控制面板–》程序与功能–》启动或关闭Windows功能 勾选 适用于Linux的Windows子系统

### 2、远程linux服务  
scp -r C:\Users\linwuhua\Desktop\linwuhua\android\FFmpeg zhouxinke@10.156.10.146:/home/zhouxinke/  
wget https://dl.google.com/android/repository/android-ndk-r21b-linux-x86_64.zip  
unzip android-ndk-r21b-linux-x86_64.zip  
sudo chmod -R 777 FFmpeg/   递归修改文件权限  
history  查看命令记录  
#### 生成armeabi-v7a脚本
```
basepath=`pwd`
echo ${basepath}
cd ${basepath}/ffmpeg-4.3.1
ls

        ./configure \
	--prefix=${basepath}/ffmpeg_install \
	--enable-static \
	--enable-shared \
	--enable-cross-compile \
	--target-os=android \
	--arch=arm \
	--cc=/mnt/android-ndk-r21b/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi21-clang \
	--cross-prefix=/mnt/android-ndk-r21b/toolchains/llvm/prebuilt/linux-x86_64/bin/arm-linux-androideabi- \
	--disable-ffmpeg \
	--disable-ffplay \
	--disable-ffprobe \
	--disable-avdevice

make
make install
```
