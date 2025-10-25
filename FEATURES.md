# フォーク元 Misskey との機能差分一覧
## プロフィールページ
- ファイルタブでセンシティブの確認をスキップできるように
- パブリック設定のクリップでメディア付ノートをクリップしている場合、サムネイルを表示するように
- タブを整理して「もっと！」に色々と集約している

## 設定系
- ロールでアクセストークンの発行を制御できるように
- 絵文字リアクション非対応サーバーからのお気に入り登録などを☆に
- （Web版のみ）公開タイムライン（LTL/STL/GTL）の表示/非表示設定を自分で設定できるように 

## チャンネル
- 全てのチャンネルタブを追加
- 横幅が大きいディスプレイなどで、最大3列表示をするように

## その他
- ドライブで空き容量を表示するように
- フォーク元と本リポジトリのソースコードへのリンクを追加
- 未ログイン時にローカルタイムラインへのアクセスをしやすく
- クリップをグリッド表示に対応
- 付箋を複数保存できるように
  - [隠れ家](https://github.com/hideki0403/kakurega.app)のソースコードを利用させていただきました
    - Cherry picked from https://github.com/hideki0403/kakurega.app/commit/75f4ec186872ab04327d8d88a65333e277d6d959
    - Cherry picked and based on https://github.com/hideki0403/kakurega.app/commit/e7eaa3e6c278243726a11f19d64c5dd17ac7b705
    - Cherry picked and based https://github.com/hideki0403/kakurega.app/commit/5734786d4b4d8cc8af40cc65d3dd4c924cdc4f26
