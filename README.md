# ⚡ LAN P2P ファイル共有 (Win ⇔ iPhone)

Windows と iPhone（Safari）などの間で、同じ Wi-Fi（LAN）内から高速・直接ファイル送受信を行える単一ページの Web アプリケーションです。

## 公開 URL（GitHub Pages）

https://ushioda-riku.github.io/lan-p2p-share/

## 特徴

1. **5桁コード＆QRコードによる簡単マッチング**
   - 5桁のランダム ID を発行し、PeerJS（WebRTC）で P2P 接続します。
   - 画面の QR コードを iPhone のカメラで読み取るだけで自動接続（`?connect=XXXXX`）できます。
2. **TURN サーバーなしの純粋な LAN 内 P2P 通信**
   - `iceServers` を STUN（`stun:stun.l.google.com:19302`）のみに限定し、外部中継サーバーを介さずにローカルネットワーク内で高速通信します。
3. **大容量ファイル対応（64KB チャンク分割＆バックプレッシャー制御）**
   - 64KB ごとに分割して送信し、`RTCDataChannel.bufferedAmount` を監視しながら転送することで iPhone Safari のメモリ負荷を抑えます。
4. **双方向送受信＆iPhone 最適化**
   - PC ⇔ スマホの双方向で複数ファイルを送信可能。画像を受信した場合はその場でプレビュー表示し、長押しで「写真に保存」できます。

## 使い方

1. Windows（または送信元端末）のブラウザで [公開 URL](https://ushioda-riku.github.io/lan-p2p-share/) を開きます。
2. iPhone のカメラで表示された QR コードを読み取るか、相手端末で同じ URL を開いて 5 桁のコードを入力し「接続」を押します。
3. 接続後、ファイルを選択またはドラッグ＆ドロップすると即座に P2P 転送が始まります。
