# Simple WebXR AR demo

ローカルで 3D モデルを AR（model-viewer 経由: WebXR / Scene Viewer / Quick Look）で配置する最小構成。`index.html`（AR）と `viewer.html`（360°ビュー）があります。

## 使い方
- `models/model.glb` に使いたい GLB/GLTF を置く。iOS の Quick Look を確実にしたい場合は `models/model.usdz` も用意するとベスト。
- HTTPS でローカル配信（AR は https/localhost 必須）。
- `index.html`…AR 表示（WebXR / Scene Viewer / Quick Look にフォールバック）。  
  `viewer.html`…360°表示（ドラッグ・ピンチで回転/ズーム）。

### 起動手順（PCでサーバーを立てる）
1) モデルを置く: `models/model.glb` に差し替え（必要なら `index.html` の `model.scale.set(...)` でサイズ調整）。
2) 証明書の用意（初回のみ）  
   - Windows (PowerShell): mkcert をダウンロード → `mkcert -install` → `mkcert localhost 127.0.0.1 ::1`  
   実行するたびに `localhost.pem` / `localhost-key.pem` または `localhost+2.pem` などができます（最新のペアを使うかリネームしても可）。
3) HTTPS で配信（リポジトリ直下）  
   ```sh
   npx http-server -S -C localhost+2.pem -K localhost+2-key.pem -p 8080 .
   ```
   - WSL で `uv_interface_addresses` エラーが出る場合は、同じフォルダを Windows の PowerShell で開いて同コマンドを実行すると回避しやすい。
4) 同一 Wi‑Fi のスマホから `https://<PCのIP>:8080` を開き、証明書の警告が出たら許可する。

### iPhone でアクセスする場合
- Quick Look での AR を安定させるには `models/model.usdz` を用意し、HTTPS で配信するだけで OK（Safari で「ARで表示」をタップすると Quick Look が開く）。
- 自己署名証明書を信頼する場合: mkcert が作る `rootCA.pem` を iPhone に送付 → プロファイルをインストール → 設定 > 一般 > 情報 > 証明書信頼設定 で信頼をオン。
- WebXR での AR を試す場合（実験的）: 設定 > Safari > 詳細 > 実験的機能 で `WebXR` / `WebXR Device API` をオンにする必要がありますが、不安定なため Quick Look を推奨。

### iPhone でモデルを作るときのおすすめ
- Polycam, Scaniverse, RealityScan, KIRI Engine などの LiDAR/写真測量アプリでスキャン → GLB/GLTF で書き出し。
- 出力時に「medium」程度のポリ数に抑えると軽く動きやすい。iOS 向け AR を安定させるなら USDZ も同時に書き出す。
- 得られた `model.glb` を `models/` に置くだけで差し替え完了。大きさ調整は `model-viewer` 側の `scale` 属性（未指定なら等倍）で調整可能。

### 動作メモ
- AR ボタンは端末に応じて WebXR / Scene Viewer / Quick Look にフォールバックします。iOS では Quick Look が最も安定。
- HTTPS 配信が必須。自己署名を信頼できない場合はローカルホットスポットなどで同一ネットワークを確保し、証明書を許可してください。
