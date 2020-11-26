### 方法1、windows wls 安装 
1. Microsoft Store应用商店
2. 设置 -》 更新和安全 -》 开启开发者选项
3. 控制面板–》程序与功能–》启动或关闭Windows功能 勾选 适用于Linux的Windows子系统

### 方法2、远程linux服务  
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

## Android集成  
### 拷贝.so文件到src/main/jniLibs  
### 拷贝头文件到src/cpp   
FFmpegLearning\app\src\main\java> javah -jni com.huazai.ffmpeglearning.MainActivity 生成native头文件  
### CMakeLists.txt  
```
cmake_minimum_required(VERSION 3.4.1)

include_directories(${CMAKE_SOURCE_DIR}/include)

add_library(
        native-lib
        SHARED
        native-lib.cpp)

find_library(
        log-lib
        log)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${CMAKE_SOURCE_DIR}/../jniLibs/${CMAKE_ANDROID_ARCH_ABI}")

target_link_libraries(
        native-lib
        avcodec
        avfilter
        avformat
        avutil
        swresample
        swscale
        ${log-lib})
```
### build.gradle  
```
android {
    compileSdkVersion 29
    buildToolsVersion "29.0.0"
    defaultConfig {
        applicationId "com.huazai.ffmpeglearning"
        minSdkVersion 15
        targetSdkVersion 29
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
        externalNativeBuild {
            cmake {
                abiFilters 'armeabi-v7a'
                cppFlags "-frtti -fexceptions -std=c++14"
            }
        }
        ndk{
            abiFilters 'armeabi-v7a'
        }
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    externalNativeBuild {
        cmake {
            path "src/main/cpp/CMakeLists.txt"
        }
    }
}
```
