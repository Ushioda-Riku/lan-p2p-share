# AirShare (Win / iPhone ファイル共有)

Windows と iPhone（Safari）などの間で、5桁のコードや QR コードを使ってファイルを送受信できる単一ページの Web アプリケーションです。

## 公開 URL

https://ushioda-riku.github.io/lan-p2p-share/

## 特徴

- **極限までシンプルな SVG UI**: 絵文字を使わず、iOS 準拠のライト・ダーク自動切替と SVG アイコンで構成しています。
- **連続送信・iOS Safari 対応のハイブリッド通信**: WebRTC（`serialization: "raw"`, 16KB チャンク + SCTP バックプレッシャー + ACK 制御）による直接 P2P と、ECDH P-256 + AES-256-GCM で暗号化した WSS リレー（ペース制御・ウィンドウ再送付き）を並列運用し、スマートフォンからの連続送信でも切断されません。
- **切断時の自動再接続**: `sessionStorage` によるセッション保持、Safari の `visibilitychange` / `online` 復帰検知、最大 5 回の段階的自動再接続と手動再接続ボタンを備えています。
