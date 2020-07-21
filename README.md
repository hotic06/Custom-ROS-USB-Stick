# USBメモリだけでつくるROS開発環境
## 概要
ROS開発環境の準備は、一般的にUbuntuをインストールするところからはじまる。
Ubuntuのインストールは非常に簡単にできるようになっているが、既存のWindows環境と共存させるのは、慣れないとデータ消失のリスクが伴う。
PCを専用に1つ準備するのが最も確実な方法ではあるが、それ以外の方法として、USBメモリからUbuntuを起動させる方法を紹介したい。
USBメモリからUbuntuを起動する方法はいくつかある
1. USBメモリにUbuntuを直接インストールする方法
2. Live USB Imageを使う方法
1.は動作する方法ではあるものの、USBメモリに直接Ubuntuインストールするのは、いくつかの理由で推奨されない。
主な理由としては、USBメモリはランダムアクセスが遅いことが多い。また、書き込み回数に上限があり、頻繁な書き込みによって、データの破損などが発生する可能性がある。
2.の方法は、基本的には読み込み専用とし、設定ファイルや作業ディレクトリのみを書き込むことで、USBメモリでもある程度快適に作業ができ、データの破損のリスクを下げている。
そのためには、ROSなど必要なパッケージをあらかじめインストールされたImageを作成する必要がある。（小さなパッケージは後から追加できる。）

## カスタムイメージ

## カスタムイメージの作成方法
[Cubic](https://launchpad.net/cubic)というソフトウェアを使う。
このソフトウェアは、Ubuntuなどの公式イメージをカスタマイズし、オリジナルのLive USBイメージを作成することができる。
このソフトウェアはUbuntu上で動作する。（残念ながらWindows環境だけでカスタムイメージを作成することは、できないことはないが難しい。イメージを作成できる環境の人に作成をしてもらい、それを配布してもらうのが現実的だろう）
- 参考[How To Customize Ubuntu Or Linux Mint Live ISO With Cubic](https://www.linuxuprising.com/2018/07/how-to-customize-ubuntu-or-linux-mint.html)
- 参考[How to create a custom Ubuntu ISO with Cubic](https://www.techrepublic.com/article/how-to-create-a-custom-ubuntu-iso-with-cubic/)
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
流れは下記である。
1. Project Directory を指定する。（10GB程度の容量が必要とされる）→Nextをクリック
2. Original ISOを指定する。(上記でダウンロードしたISOファイル）→Nextをクリック
3. Shellが開くので必要なパッケージをインストールする。root環境なのでsudoは不要。下記の
```
apt update
apt upgrade

sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
apt update

apt install ros-kinetic-desktop-full

apt install python-pip

#pipでエラーがでるのでLocaleを設定している
update-locale LANG="en_US.UTF-8"
localectl set-locale LANG=en_US.UTF-8 LANGUAGE="en_US"

export LC_ALL=$LC_NAME

pip install requests flask
apt-get install ros-kinetic-turtlebot3 ros-kinetic-turtlebot3-msgs ros-kinetic-turtlebot3-simulations
apt-get install ros-kinetic-aruco-ros
#必要になりそうなパッケージを一通りインストール
apt-get install ros-kinetic-joy ros-kinetic-teleop-twist-joy ros-kinetic-teleop-twist-keyboard ros-kinetic-laser-proc ros-kinetic-rgbd-launch ros-kinetic-depthimage-to-laserscan  ros-kinetic-rosserial-arduino ros-kinetic-rosserial-python ros-kinetic-rosserial-server ros-kinetic-rosserial-client ros-kinetic-rosserial-msgs ros-kinetic-amcl ros-kinetic-map-server ros-kinetic-move-base ros-kinetic-urdf ros-kinetic-xacro ros-kinetic-compressed-image-transport ros-kinetic-rqt-image-view ros-kinetic-gmapping ros-kinetic-navigation ros-kinetic-interactive-markers
apt-get install ros-kinetic-slam-gmapping ros-kinetic-libg2o libopencv-dev ros-kinetic-costmap-converter libsuitesparse-dev libarmadillo-dev libarmadillo6 graphviz graphviz-dev ros-kinetic-jsk-rviz-plugins 
apt-get install ros-kinetic-smach*
pip install transitions pygraphviz
apt-get install gnome-terminal

dpkg-reconfigure tzdata
# Asia/Tokyo を選ぶ
# https://vz-shark.hatenablog.com/entry/2018/10/05/181118
timedatectl set-local-rtc 1

dpkg-reconfigure keyboard-configuration
# Generic 105-key (Intel) PC > Japanese > Japanese > The default for the keyboard layout > No compose key


curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/
sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
apt update
apt install code
```
4. Nextをクリック
5. kernelの選択などの画面になるが、特に変更せずGenerateをクリック
6. ISOの生成が始まる。数分かかる。
7. Finishを押して完了
## イメージの書き込み
イメージの書き込みはWindowsでも行える。（Linuxでも行える）
[UNetbootin](https://unetbootin.github.io/)を使用する。
USBメモリをPCに接続する。FAT32でフォーマットをする。
必ずFAT32でなければならない。
1. ディスクイメージを選択し、...をクリックして、上記で生成したISOを選択
2. そのすぐ下の「スペースは、・・・使用(Ubuntuのみ)」に4096(MB)と入力（4096MBはFAT32の制約からくる上限）
3. タイプ「USB ドライブ」ドライブを選択し、OKをクリック
（10分ほどかかります）
