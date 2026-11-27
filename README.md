# AirShare (Win / iPhone ファイル共有)

Windows と iPhone（Safari）などの間で、5桁のコードや QR コードを使って高速にファイルを送受信できる単一ページの Web アプリケーションです。

## 公開 URL

https://ushioda-riku.github.io/lan-p2p-share/

## 主なアーキテクチャ

- **3 レーン並列 Raw WebRTC P2P ストライピング**: `serialization: "raw"` と `sctp.maxMessageSize` 動的ネゴシエーション（最大 63 KB チャンク）、イベント駆動 `bufferedamountlow` 制御により高速直結転送を行います。
- **2 ブローカ同時 WSS マルチパス・ストライピング + BBRv3/L4S + 64bit SACK**: `draft-ietf-quic-multipath` に基づき経路ごとに独立した輻輳制御（`cwnd` / `inflight_hi` / RTT 勾配）と ECF スケジューリングを実装し、64bit SACK ビットマップ・RACK 選択再送・MP-QUIC テール複製により高損失回線でも高速かつ途切れないリレー転送を実現します。
- **TLS 1.3 決定論的 XOR-Nonce AEAD**: 転送ごとの 12 バイトソルトとシーケンス番号の XOR によりチャンクごとの乱数生成と IV 送信を省き、ヘッダを `additionalData` で認証します。
- **Shannon エントロピー適応圧縮＆逐次 Sub-Blob コンパクション**: 2 KB ストライド走査（$H < 7.20$）で可逆圧縮（`deflate-raw`）と定数スパース圧縮を適用し、受信側は 128 チャンクごとに `Blob` へ逐次コンパクションして Safari のメモリ上限を回避します。
- **極限までシンプルな SVG UI**: 絵文字を使用せず、iOS 準拠のライト・ダーク自動切替と SVG アイコンで構成しています。
