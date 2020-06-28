# Raspbian のセットアップ

@import "../_assets/style.less"

## SDカードの用意

### Raspbian DesktopのSDカード用意

下記Imageファイルを使用

```
2019-09-26-raspbian-buster.img
```

### 初期設定


```
# aptの更新
sudo apt update
sudo apt upgrade
```

#### 画面をブラックアウトさせない方法

スクリーンセイバーを導入してスクリーンセイバーを無効にすればブラックアウトしない。


下記のコマンドを実行して、スクリーンセイバーを導入する。

```bash
sudo apt-get install xscreensaver
```

再起動してメニューのスクリーンセイバーを選択しスクリーンセイバーを無効にする。


下記のファイルに設定を追加して再起動する。

/etc/lightdm/lightdm.conf

```
[SeatDefaults]
xserver-command=X -s 0 -dpms
```

### Raspbainの64bit化

- https://www.raspberrypi.org/forums/viewtopic.php?f=29&t=250730&hilit=kernel+64+testing
- https://qiita.com/god19/items/463b2c089159856d23ac

ROSのインストールと高速化のために64bit化します。
```sudo rpi-update```で64bit firmwareへ更新し、
```sudo vi /boot/config.txt``` に下記の項目を記載してください。

```
[all]
arm_64bit=1
```

更新後、再起動をして```aarch64```になっていたら成功です。
```
 $ uname -a
Linux raspberrypi 4.19.108-v8+ #1298 SMP PREEMPT Fri Mar 6 18:15:51 GMT 2020 aarch64 GNU/Linux
```

## マウスカーソル消し

/etc/lightdm/lightdm.conf

xserver-command=X -bs -core -nocursor


## スタートアップ登録方法


```bash
cd /lib/systemd/system
sudo vi myStartup.service
```

```file
[Unit]
Description=start my program
Wants=network-online.target
After=network-online.target

[Service]
Type=notify
ExecStart=/home/pi/masiro_atama/sh/startup.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

---------------------------------------------------

```bash
sudo systemctl list-unit-files --type=service | grep myStartup

sudo systemctl enable myStartup
```