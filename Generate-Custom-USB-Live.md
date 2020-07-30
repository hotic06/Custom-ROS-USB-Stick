# Live USB Imageを使う方法
## カスタムイメージの作成方法
[Cubic](https://launchpad.net/cubic)というソフトウェアを使う。
このソフトウェアは、Ubuntuなどの公式イメージをカスタマイズし、オリジナルのLive USBイメージを作成することができる。
このソフトウェアはUbuntu上で動作する。（残念ながらWindows環境だけでカスタムイメージを作成することはできない。イメージを作成できる環境の人に作成をしてもらい、それを配布してもらうのが現実的だろう）
- 参考[How To Customize Ubuntu Or Linux Mint Live ISO With Cubic](https://www.linuxuprising.com/2018/07/how-to-customize-ubuntu-or-linux-mint.html)
- 参考[How to create a custom Ubuntu ISO with Cubic](https://www.techrepublic.com/article/how-to-create-a-custom-ubuntu-iso-with-cubic/)

下記の手順で作成したISOファイルをこのレポジトリのリリースに掲載している。

https://github.com/hotic06/Custom-ROS-USB-Stick/releases/

GitHubの容量制限があるため、ファイルは２分割されている。
下記の方法で結合してから、
ISOファイルをUSBメモリに
[イメージの書き込み](#イメージの書き込み)の手順で書き込む。


### Cubicをインストール
```
sudo apt-add-repository ppa:cubic-wizard/release
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 6494C6D6997C215E
sudo apt update
sudo apt install cubic
```

### カスタムイメージを作成
ここでは、[Lubuntu 16.04.6](http://cdimage.ubuntu.com/lubuntu/releases/16.04.6/release/)の「64-bit PC (AMD64) desktop image」をベースとした。
Lubuntuを使った理由は、イメージのサイズが小さいということと、RAM使用量が少ないということである。
Live USBではスワップ領域を確保できないためRAMの使用量を気にする必要がある。

下記の手順で行う。
#### 1. Project Directory を指定する。（10GB程度の容量が必要とされる）→Nextをクリック
#### 2. Original ISOを指定する。(上記でダウンロードしたISOファイル）→Nextをクリック
#### 3. Shellが開くので必要なパッケージをインストールする。root環境なのでsudoは不要。下記のコマンドを実行する。すべて終わったらNextをクリック
```
apt update
apt upgrade

# ROSをインストール
sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
apt update
apt install ros-kinetic-desktop-full

#依存ライブラリをインストール
apt install python-pip

#pipでエラーがでるのでLocaleを設定している
export LC_ALL=$LC_NAME

pip install requests flask
apt-get install ros-kinetic-turtlebot3 ros-kinetic-turtlebot3-msgs ros-kinetic-turtlebot3-simulations ros-kinetic-aruco-ros

#必要になりそうなパッケージを一通りインストール
apt-get install ros-kinetic-joy ros-kinetic-teleop-twist-joy ros-kinetic-teleop-twist-keyboard ros-kinetic-laser-proc ros-kinetic-rgbd-launch ros-kinetic-depthimage-to-laserscan  ros-kinetic-rosserial-arduino ros-kinetic-rosserial-python ros-kinetic-rosserial-server ros-kinetic-rosserial-client ros-kinetic-rosserial-msgs ros-kinetic-amcl ros-kinetic-map-server ros-kinetic-move-base ros-kinetic-urdf ros-kinetic-xacro ros-kinetic-compressed-image-transport ros-kinetic-rqt-image-view ros-kinetic-gmapping ros-kinetic-navigation ros-kinetic-interactive-markers ros-kinetic-slam-gmapping ros-kinetic-libg2o libopencv-dev ros-kinetic-costmap-converter libsuitesparse-dev libarmadillo-dev libarmadillo6 graphviz graphviz-dev ros-kinetic-jsk-rviz-plugins ros-kinetic-smach* gnome-terminal
pip install transitions pygraphviz

# デフォルトのタイムゾーンを東京に
dpkg-reconfigure tzdata
# Asia/Tokyo を選ぶ

# ハードウェア時計がローカルタイムを示すように設定(参考:http://manpages.ubuntu.com/manpages/bionic/ja/man8/hwclock.8.html )
# Windowsとの共存のために必要な設定
cat << EOF | tee --append /etc/adjtime
0.0 0 0
0
LOCAL
EOF

# 日本語環境をインストール 
apt install language-pack-ja
update-locale LANG=ja_JP.UTF-8
apt install fcitx-mozc

# キーボードを日本語設定
dpkg-reconfigure keyboard-configuration
# Generic 105-key (Intel) PC > Japanese > Japanese > The default for the keyboard layout > No compose key

# Visual Studio Codeをインストール
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/
sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
apt update
apt install code

# Gazeboの初回起動時にインターネット接続が無くても起動できるように、モデルをあらかじめダウンロード
mkdir -p /home/lubuntu/.gazebo/models
mkdir ~/gazebo-world/
cd ~/gazebo-world
apt install subversion
svn export https://github.com/osrf/gazebo_models/branches/master/sun
svn export https://github.com/osrf/gazebo_models/branches/master/ground_plane
chown -R 999:999 /home/lubuntu
rm -rf ~/gazebo-world

```
#### 4. Nextをクリック
#### 5. kernelの選択などの画面になるが、特に変更せずGenerateをクリック
#### 6. ISOの生成が始まる。数分かかる。
#### 7. Finishを押して完了

## イメージの書き込み
イメージの書き込みはWindowsでも行える。（Linuxでも行える）
[UNetbootin](https://unetbootin.github.io/)を使用する。
USBメモリをPCに接続する。FAT32でフォーマットをする。必ずFAT32でなければならない。
1. ディスクイメージを選択し、...をクリックして、上記で生成したISOを選択
2. そのすぐ下の「スペースは、・・・使用(Ubuntuのみ)」に4096(MB)と入力（4096MBはFAT32の制約からくる上限）
3. タイプ「USB ドライブ」ドライブを選択し、OKをクリック
（10分ほどかかります）

## USBからの起動
USBメモリをさしたままにして、Windowsを再起動する。
うまくいけば、Lubuntuが起動する。

起動しない場合、BIOSの設定で、起動順番をUSBメモリが１番目になるようにしなければならない。
また、UEFIモードに設定されている場合と、Secure Bootが有効になっている場合は起動しない場合がある。
Legacyモードに設定し、Secure Bootを無効にすると起動するだろう。

## ROS環境のセットアップ
ほぼ環境はできあがっているが、
いくつか初期設定をする必要がある。

まず端末の起動する。左下のメニューのシステムツール→LxTerminalで、端末が起動する。


基本的には、
https://github.com/OneNightROBOCON/burger_war
に書かれたことを行う。（ただし、ROSと依存ライブラリのインストールは既に完了している）

まず、環境設定をする。
```
sudo rosdep init
rosdep update
echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

つぎに、ワークスペース作成する。
```
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace
cd ~/catkin_ws/
catkin_make
echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

その後は、
https://github.com/OneNightROBOCON/burger_war
を参考にしてください。

## Lubuntu Tips
- エディター（メモ帳）⇒Leafpad
- WiFiの設定⇒右下のネットワークアイコン
- ブラウザー（Firefox）⇒左下のスタートメニューの横に並んでいる
- ファイラー⇒左下のスタートメニューの横に並んでいる
- 日本語変換⇒Ctrl+スペースで日本語変換がONになる
