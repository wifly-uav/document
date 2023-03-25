# PIDチューニング~MATLAB編~

サークル時代にMATLABを使ったPIDチューニングをしていたので紹介。
WiFlyでどこまで使える変わらない。

## 環境
- MATLAB R2020a
- System Identification Toolbox(システム同定用)
- Control System Toolbox(PID調整用)
- 対象システム：一次遅れ系
<img src=./img/system.png width=500>
  
## システム同定
### データ取得
ステップ応答を用意。好きな値を入力し続ければ良い。M系列信号でもいいらしい。
縦に2列input,outputを書き込んだexcelを用意。今回は乱数を入れてsimulinkから出力することで代替した。本来はマイコンからモーターに出力してエンコーダーでoutputを取得する。
1行目はタイトル行にしておく。MATLAB内で変数名として使うことを念頭におき変な文字は使わないように。
この際サンプリング周期Tsは一定にする。今回は0.025 sにした
<img src=./img/output.png width=500>

### データのインポート
MATLABを開き「データのインポート」を押せばなんとかなる。
MATLABコンソールにて「file名.列名」で任意の列にアクセス出来る。
参考：https://jp.mathworks.com/help/matlab/import_export/ways-to-import-spreadsheets.html

### アプリ→システム同定
<img src=./img/app.png width=500>

1. import data→Time domain dataを選択。
2. Input, Outputにインポートしたデータ（file名.列名でok)を入れる。Starting timeにデータ取得開始時刻（普通0), Sampling timeにデータの周期を入れる。今回はStarting time = 0, Sampling time = 0.025 
3. importを押してアプリ内にデータインポート。
4. インポートしたデータをWorking Data, Validation Dataにドラッグ&ドロップ
5. Estimate->Polynomial Modelsをクリック
6. Structure->ARXを選択。Ordersがパラメータで3つあるので、それぞれ適当な数字を入れる。1:10などのようにすると1から10まで試してくれる。
7. 結果がグラフで表示されるので、値が収束したらへんをクリックしてパラメータを見る。バーが低いほど精度が高い。na,nbの値は小さい方がモデルとしては単純で良い。nkは遅れに起因する。いい感じの値が見つかるまで6をやり続ける。
8. いい感じの奴が見つかったらそのバーを選択してinsertボタンを押す。
9. 作成されたmodelはTo Workplaceにドラッグ&ドロップするとPID調整で使える
<img src=./img/param.png width=300>

伝達関数の形が予め分かっている場合は5で Estimate > Transfer Function Modelsを利用しても良い。極とゼロ点の数を入れる。
遅れに関してはFixedにチェックを入れなければminumum からmaximuｍまででいい値を見つけてくれる。 これでシステム同定は終了である。コンソール上でss_model = ss(モデル名）と打てばモデルがPID調整で使える形になる
参考：https://jp.mathworks.com/help/ident/gs/identify-linear-models-using-the-gui.html

### PIDオートチューニング
MATLABコンソール上でpidTuner(ss_model, ‘pidf’)と打つ。するとPIDをオートチューニングしてくれる。気に入らなければ調整ツールで応答時間や過渡特性を弄れるのでいい感じのやつを探す。が、デフォルトで十分なことも多い。
参考：https://jp.mathworks.com/help/control/getstart/pid-controller-design-for-fast-reference-tracking.html
