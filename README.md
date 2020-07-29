# USBメモリもしくはUSB-HDDでつくるROS kinetic開発環境
## 概要
ROS開発環境にはUbuntuを使うのが一般的だ。
Ubuntuのインストールはとても簡単にできる。しかし、WindowsがすでにインストールされているPCに、Ubuntuを共存させてインストールするのは、慣れないとデータ消失のリスクが伴う。

専用PCを準備するのが最も確実な方法ではある。しかしそれ以外の方法として、USBメモリもしくはUSB-HDDからUbuntuを起動させる方法を紹介したい。

USBメモリもしくはUSB-HDDからUbuntuを起動する方法はいくつかある。

1. [USB-HDDにUbuntuを直接インストールする方法](#usb-hddに直接インストールする方法)
2. [Live USB Imageを使う方法](#live-usb-imageを使う方法)

1.の方法をとる場合、USBメモリを使うのではなく、USB-HDDやUSB-SSDなどを使うとよい。
USBメモリはランダムアクセスが遅いことが多い。また、書き込み回数に上限があり、頻繁な書き込みによって、データの破損などが発生する可能性がある。

2.の方法は、基本的には読み込み専用とし、設定ファイルや作業ディレクトリのみを書き込むことで、USBメモリでもある程度快適に作業ができ、データの破損のリスクを下げている。
そのためには、ROSなど必要なパッケージをあらかじめインストールされたImageを作成する必要がある。（小さなパッケージは後から追加できる。）



# USB-HDDに直接インストールする方法

[USB-HDDに直接インストールする方法](Install-USB-HDD.md)

# Live USB Imageを使う方法

[Live USB Imageを使う方法](Generate-Custom-USB-Live.md)
