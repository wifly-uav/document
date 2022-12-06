# EAGLE使い方

## symbol・footprint収集
<img src="/img/footprint.png" width=500>

左がsymbol、右がfootprint

今使っているものはBoxのUAV->に保存している

新しく増やしたければ、ICを調べてECADモデルをダウンロード
無ければ自作する
- [Mouser](https://www.mouser.jp/)
- [Free PCB Library](https://componentsearchengine.com/?gclid=Cj0KCQiA7bucBhCeARIsAIOwr-_cIxiWsXcVXk4lPbtY2RzGw4Db0WdnrfxMqymXanwLZwmNwUnXUIMaAqxsEALw_wcB)
- [Snap EDA](https://www.snapeda.com/parts/IR2302/Infineon%20Technologies/view-part/?ref=search&t=IR2302#download-modal)

※必要に応じて[Library Loader(ソフト)](https://www.samacsys.com/library-loader/)を使う
.lbrをEAGLE/librariesの中に保存して追加
（基本的にEagleを一度閉じないと更新さないので注意）

## 回路設計
## 基板設計
## デザインルールの確認
発注先のデザインルールを確認する
「(発注先) drc」で調べるとだいたい誰かが作ってくれいるはず
.drcをEAGLE/design rulesの中に保存して追加

## 発注