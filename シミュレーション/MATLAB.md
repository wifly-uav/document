# MATLAB

## 必要なアドオン
- Simulink Real-Time
- MATLAB Coder
- Instrument Control Toolbox
  
## シミュレート手順
1. MATLABを起動
<img src=./img/matlab.png width=500>
2. simulinkを起動
<img src=./img/simulink.png width=500>
3. wifly/sim/air_part1_python2.slxを開く(okamotoブランチのみ)
<img src=./img/model.png width=500>
4. pythonを実行(wifly/DQN2022_sim/PID_DQN.py okamotoブランチのみ)
5. simulinkの実行ボタンを押す
終了時間をシミュレート時間よりも大きくしておくこと

## モデル
モデル概要図
<img src=./img/model_.png width=700>

### モーター
モーターは[このサイト](https://www.paltek.co.jp/techblog/techinfo/220107_01)を参考にした。各パラメータは妥当なものを推測し、代入した。
変更点は、羽を動かすときの抗力を加えた。

### 上羽・下羽
最初のブロックは、リンクロッドの角角度を計算している。羽の角度以外は、モーターに与える抗力の計算のために必要。
2つめのブロックは、空力で発生する推力を計算している。[この論文](https://engineering.purdue.edu/~xdeng/TRO06a.pdf)のⅣ章を参考にしている。

### 慣性
発生した空力と羽の状態から回転方向に発生する慣性を計算している。羽ばたいたときに垂直の方向と水平の方向で2つ計算している。また、ここで乱数を発生させて再現性を低くしている。

### 運動方程式
回転に関する運動方程式を計算している。