# SLAMの環境構築

## Ubuntu+ROS
一昔前までは、オープンソースであるLinuxをベースとしたUbntuというOSでROSというミドルウェア(アプリとOSをつなぐもの)を用いて研究や実装が行われることが多かった。最近は、ROSが開発終了しROS2が迷走していることからWindowsでもSLAMの研究・開発し始めている印象。ただ、最新のSLAMを用いるわけではないので今はUbuntu+ROSで環境構築を行った。

## WSL2+Ubuntu20.04LTS+ROS(noetic)
WSL2を用いてwindows上でUbuntuを動かす。
カーネルの関係上、windows11である必要がある...らしい。
Ubuntuのバージョンは20.04LTSを用いる。Ubuntuは2年ごとに長期サポートのバージョンが出る。20.04LTSは2020年に配布されたもの。22.04LTS以降はROSに対応しておらずROS2となる。2022年時では、20.04LTS,18.04LTSがよく使われている印象。ただ、この2つは大きな違いがあるので注意。20.04LTSはデフォルトがpython3系であり、18.04LTSはデフォルトがpython2系である。
ROSのバージョン(正確にはディストリビューション)はnoeticを用いる。ROSのバージョンはUbuntuのバージョンで決まる。18.04LTSの場合はmelodic,16.04LTSの場合はkineticとなる。ROSとROS2は動作がまったく異なるので注意。githubでbranch名がfoxy,humbleとなっていた場合はROS2で動作することを意味する。

## 環境構築
### wsl2のインストール
[このサイト](https://qiita.com/whim0321/items/ed76b490daaec152dc69)を参照
注意：Ubuntuではなく、Ubuntu20.04LTSをインストールすること
### rosのインストール
[ここ参照](http://wiki.ros.org/ja/noetic/Installation/Ubuntu)
Desktop-Full Installで良い。
インストールが終わったら
[ここ参照](http://wiki.ros.org/ja/ROS/Tutorials/InstallingandConfiguringROSEnvironment)
### opencvのインストール
[ここ参照](https://symfoware.blog.fc2.com/blog-entry-2458.html)
### pangolinのインストール
[ここ参照](https://handaru.net/blog/2022/09/15/376/)
### eiginのインストール
[ここ参照](https://handaru.net/blog/2022/09/15/376/)
### ORB_SLAMのインストール
[ここ参照](https://handaru.net/blog/2022/09/15/376/)
### カーネルの変更(任意)
wsl2➝wsl2+に変更する。
wsl2では、USB入力ができないため、USBカメラ等を使用したい場合はカーネルを変更する必要がある。
[このサイト](https://zenn.dev/pinto0309/articles/e1432253d29e30)を参照
### IP Webcamのインストール(Andoroid)
画像キャプチャーの確認としてスマホを用いる。iPhone勢は知らん。
IP Webcamというアプリをインストールする。Proの必要はない。
#### IP Webcamの使い方
起動して権限等の許可をするとプラグイン設定から始まる設定の画面になる。
右上の3点リーダーから映像ストリーミング開始で撮影が始まる。
IP接続したい場合は、画面下にURLが出てくる。その他からRTSP接続の場合のURLも得ることができる。