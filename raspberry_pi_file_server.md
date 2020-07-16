# ラズパイでファイルサーバーを作る
動画撮影等を行うため巨大なストレージを手軽に操作したいという動機です


- Version: Raspberry Pi 3 Model B
- OS: Raspberry Pi OS

## 設定
### wifiに接続：USBマウスがなくキーボードのみだったためshellから設定
```bash
$ sudo iwlist wlan0 scan # wifi のssidとpasswordがわかるのであれば省略
$ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

network={
    ssid="hogehoge_id"
    psk="hugahuga_password"
}
```

### ssh設定
（忘れたのでここでは割愛）

### ラズパイ側ファイルサーバー設定
netatalkをインストール

netatalkとは

> netatalkはUnix系OS上でClassic Mac OSやmacOSに対してAFPによるファイルサーバの機能を提供するオープンソースのソフトウェアである

らしい

```bash
$ sudo apt-get install netatalk
```

### Mac側ファイルサーバー設定
    - Finderメニュー
        - 移動
            - サーバーへ接続
                - `afp://(ラズパイのプライベートIPアドレス)`
                - +ボタンを押しておくとアドレスが保存されて次回接続時楽です

- ラズパイのプライベートIPアドレス確認方法
`$ ifconfig`で`wlan0`の`inet`を参照

Finderのネットワークから、ラズパイに接続できるかと思いますー！

### 
``` raspberry pi mount: unknown filesystem type 'exfat' ``` 
というエラーが出た
Raspbianではデフォルトではexfatフォーマットはサポートしていないので関連ライブラリインストール 
https://www.raspberrypi.org/forums/viewtopic.php?t=45607 
```
sudo apt-get update sudo apt-get install exfat-fuse exfat-utils
```
これで`/media/pi/hoge`に自動でmountされた 
しかし、またもや問題発生。 
netatalkはデフォルトでmac側からは`~/`しか見れなかった（マウントされている/media/pi/hoge見れず） 
https://a244.hateblo.jp/entry/2016/10/19/000000 を参考に`/etc/netatalk/AppleVolumes.default`に
``` 
/media/pi "Media Directory"
```
を追記しアプリ再起 
```
$ sudo systemctl restart netatalk
$ sudo systemctl status netatalk # 'active (running)' となっていればOK
```
見れるようになりました。😊

