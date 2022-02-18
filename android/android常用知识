### 奔溃日志
不同手机的存储路径可能不太一样，以下路径都找找看：
/data/local/tmp/*
/data/tmp/*
/data/system/usagestats/*
/data/system/appusagestates/*
/data/system/dropbox/*
/data/tombstones/*
/data/anr/*
/dev/log/main手机连接adb的话若出现crash也能在adb logcat中看到

### adb devices
List of devices attached
FA7290303311    unauthorized
解决：重置debug模式->重连usb->adb->kill-server->sdb start-server

### 链接木木模拟器
adb connect 127.0.0.1:7555

### adb 命令
拉取手机文件：adb pull /sdcard/AutoSdkDemo/bllog C:\Users\linwuhua\Desktop\logcat
