# ORB-SLAM_起動手順

WSL2での環境構築済であること

## USBカメラ ver
1. Ubuntuの起動
Ubuntu 20.04 on Windowsを起動する
```
terminator [Enter]
```
terminatorを4つ以上に分割する
terminatorのショートカットキーは[ここ](https://qiita.com/genchi-jin/items/cf7b81d8d9ecdb69b64a)を参照

2. USBのアタッチ
windowsのターミナルを管理者権限で起動
USBカメラの確認
```
PS C:\Users\win-m\wifly> usbipd wsl list [Enter]
BUSID  VID:PID    DEVICE                                                        STATE
1-2    045e:0772  Microsoft LifeCam Studio, Microsoft® LifeCam Studio(TM), ...  Not attached
1-5    13d3:56cb  USB2.0 HD IR UVC WebCam                                       Not attached
1-10   8087:0aaa  インテル(R) ワイヤレス Bluetooth(R)                           Not attached
```
WebカメラのBUSIDを確認する。この場合は、1-5
wslにアタッチする。(管理者権限で起動していないとエラー)
```
PS C:\Users\win-m> usbipd wsl attach --busid 1-5
```
もう一度確認し、Attachedになっていることを確認する。
(この例だと1つ目がUSBカメラ,2つ目が内蔵カメラ)
```
PS C:\Users\win-m> usbipd wsl list
BUSID  VID:PID    DEVICE                                                        STATE
1-2    045e:0772  Microsoft LifeCam Studio, Microsoft® LifeCam Studio(TM), ...  Not attached
1-5    13d3:56cb  USB2.0 HD IR UVC WebCam                                       Attached - Ubuntu-20.04
1-10   8087:0aaa  インテル(R) ワイヤレス Bluetooth(R)                           Not attached
```
20.04LTS以外のUbuntuを入れていると他のUbuntuにアタッチされる。
その場合は、デフォルトのUbuntuを20.04LTSに変更する必要がある。
デタッチは、USBを抜くか```usbipd wsl detach --busid ?-?```

__WSL側のterminator__ で管理者権限を付与する。
```
sudo chmod 777 /dev/video* [Enter]
```

3. ROSの起動
1つ目のパネルで
```
roscore [Enter]
```
2つ目のパネルで
```
rosrun usb_cam usb_cam_node _video_device:=/dev/video0
```
最後のvideo0はvideo1orvideo2などになる可能性もあるので確認すること（エラーが出ないやつにすれば良い）
3つ目のパネルで(任意)
```
rqt_image_view
```
<img src=./img/rqt.png width=200>

左上のタブを変更するとカメラの映像が確認できる。
4つ目のパネルで
```
cd catkin_ws/src/ORB_SLAM3
rosrun ORB_SLAM3 Mono Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml /camera/image_raw:=/usb_cam/image_raw
```
最後の/usb_cam/image_rawは3つ目のパネルのときのrqtでカメラが映ったときのタブの名前と一致させること。
これでSLAMが始まる。

ログなどに関しては[このページ](https://github.com/UZ-SLAMLab/ORB_SLAM3)からやり方を確認して。

## ESP-EYE or スマホver

1. Ubuntuの起動
Ubuntu 20.04 on Windowsを起動する
```
terminator [Enter]
```
terminatorを4つ以上に分割する
terminatorのショートカットキーは[ここ](https://qiita.com/genchi-jin/items/cf7b81d8d9ecdb69b64a)を参照

2. ESP-EYEへの書き込み
wifly/ESP/ESP-EYE/esp-eye_rtspをPlatformIOでOpen Projectから開く(okamotoブランチ)
esp-eye_rtsp/src/src/wifi_config.hの設定を書き換える(最初の2行)
必要に応じて```const IPAddress ip(192, 168, 0, 100);```のipアドレスを書き換える。
不可能なIPアドレスを指定している時がある。
IPWebCamのIPアドレスと同じようなアドレスにするとよい。
esp32_rtsp.inoをESP-EYEに書き込む。

3. ESP-EYE or スマホの起動
ESP-EYEかスマホのIPWebCamのアプリを起動する。
この時、ESP-EYE or スマホが接続しているwifiがSLAMのPCと同じwifiになっている必要がある。大学の共用wifiでは使えなかった気がする。誰かのスマホかPCのモバイルホットスポットを使うとよい。

4. 接続設定
/catkin_ws/src/rtsp_cam/cap.pyの5行目のRTSPアドレスを変更する
```
cd catkin_ws/src/rtsp_cam
python3 cap.py
```
カメラ映像が映ればOK
パネル上でctrl+cでkillすれば終了する
/catkin_ws/src/rtsp_cam/publish_image.pyの9行目のRTSPアドレスを変更する

5. SLAM実行
1つ目のパネルで
```
roscore [Enter]
```
2つ目のパネルで
```
rosrun rtsp_cam publish_image.py 
```
OpenCVとROSの組み合わせが悪く、こいつはcntl+cでkillできない。
RTSPカメラの接続を切るか、ターミナルを消すしかない。
3つ目のパネルで(任意)
```
rqt_image_view
```
4つ目のパネルで
```
cd catkin_ws/src/ORB_SLAM3
rosrun ORB_SLAM3 Mono Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml /camera/image_raw:=/image_data
```
最後の/image_dataは3つ目のパネルのときのrqtでカメラが映ったときのタブの名前と一致させること。
これでSLAMが始まる。

ログなどに関しては[このページ](https://github.com/UZ-SLAMLab/ORB_SLAM3)からやり方を確認して。