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
    1. Ubuntu 20.04 on Windowsを起動する。
    ```
    $ terminator [Enter]
    ```
    2. terminatorを4つ以上に分割する
    - terminatorのショートカットキーは[ここ](https://qiita.com/genchi-jin/items/cf7b81d8d9ecdb69b64a)を参照

1. Wifiの設定
- **ホットスポットはON**
- この時、ESP-EYE or スマホが接続しているwifiがSLAMのPCと同じwifiになっている必要がある。大学の共用wifiでは使えなかった気がする。誰かのスマホかPCのモバイルホットスポットを使うとよい。

1. `wifi_config.h`の書き換え
    1. `wifly/ESP/ESP-EYE/esp-eye_rtsp`をPlatformIOでOpen Projectから開く(okamotoブランチ)
        - `esp32_rtsp.ino`も一緒に開いておく。
    1. `esp-eye_rtsp/src/src/wifi_config.h`の設定(`*ssid, *password`)を書き換える。<br>
        - **ホットスポットではなく、おおもとのWifiのSSIDとPasswordを入力する。**
    1. `ip`,`gatway`,`subnet`の書き換え
        - `ip`: Windows設定の ネットワーク接続の表示 > おおもとのWifi > 詳細　> IPv4アドレス を参照
        - `gatway`:`XXX.YYY.ZZZ.1`とし、`XXX.YYY.ZZZ`は`ip`と共通にする。
        - `subnet`:`255.255.255.0`でOK
2. ESP-EYEへの書き込み
- esp32_rtsp.inoをESP-EYEに書き込む。書き込みが成功した場合、local IP:`XXX.XXX.XXX.XXX`が表示される。

3. ESP-EYE or スマホの起動
    1. VLC(三角コーン)を起動
    2. 上部タブのnetwork > enter a URL の部分に、`rtsp://{local IP}:8554/mjpg/1`を入力する。
    1. 映像の表示を確認

4. 接続設定
    1. 以下で`cap.py`を開く。
    ```
    $ cd catkin_ws/src/rtsp_cam
    $ code cap.py
    ```
    1. `cap.py`の５行目を`cap = cv2.VideoCapture(rtsp://{local IP}:8554/mjpg/1)`に書き換える。

    1. `cap.py`で映像を確認
    ```
    $ python3 cap.py
    ```
    - カメラ映像が映ればOK
    - パネル上でctrl+cでkillすれば終了する
    
    1. 以下で`publish_image.py`を開く。
    ```
    $ code publish_image.py
    ```

    1. 10行目を`cap = cv2.VideoCapture(rtsp://{local IP}:8554/mjpg/1)`に変更する。
    1. 以下で接続を確認
    ```
    $ ping {local IP}
    ```
    - `64　bytes from (以下略)`の表示が繰り返されていればOK
    
5. SLAM実行
    1. 1つ目のterminatorパネルで以下を実行
    ```
    $ roscore [Enter]
    ```
    2. 2つ目のterminatorパネルで以下を実行
    ```
    $ rosrun rtsp_cam publish_image.py 
    ```
    - OpenCVとROSの組み合わせが悪く、こいつはcntl+cでkillできない。
    - RTSPカメラの接続を切るか、ターミナルを消すしかない。
    
    3. 3つ目のパネルで以下を実行(カメラ映像確認、任意)
    ```
    $ rqt_image_view
    ```
    4. 4つ目のパネルで以下を実行
    ```
    $ cd catkin_ws/src/ORB_SLAM3
    $ rosrun ORB_SLAM3 Mono Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml /camera/image_raw:=/image_data
    ```
    - 最後の`/image_data`は3つ目のパネルのときのrqtでカメラが映ったときのタブの名前と一致させること(?)。これでSLAMが始まる。

- ログなどに関しては[このページ](https://github.com/UZ-SLAMLab/ORB_SLAM3)からやり方を確認して。
