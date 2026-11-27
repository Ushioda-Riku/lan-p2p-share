# AirShare (Win / iPhone ファイル共有)

Windows と iPhone（Safari）などの間で、5桁のコードや QR コードを使って高速にファイルを送受信できる単一ページの Web アプリケーションです。Cookie および Web Storage（`sessionStorage` / `localStorage` / `IndexedDB`）は一切使用せず、タブ内のオンメモリのみでセッションと再接続を管理します。

## 公開 URL

https://ushioda-riku.github.io/lan-p2p-share/

## 主なアーキテクチャ（送信・受信・後処理の全段最適化）

- **Cookie / Web Storage 完全不使用のステートレス設計**: 5桁の接続コードはページ起動ごとにオンメモリ CSPRNG で発行し、Safari の全 Cookie ブロック環境やプライベートブラウズでも安全に動作します。`<meta name="referrer" content="no-referrer">` と `crossorigin="anonymous"` により外部スクリプト通信からも Cookie を遮断します。
- **3 レーン並列 Raw WebRTC P2P ストライピング**: `serialization: "raw"` と `sctp.maxMessageSize` 動的ネゴシエーション（最大 63 KB チャンク）、イベント駆動 `bufferedamountlow`、ゼロコピー `subarray` 受信により高速直結転送を行います。
- **MPNS（Per-Path Packet Numbers）+ OTIAS / BLEST + Homa 受信側フロー制御**: `draft-ietf-quic-multipath` に基づく経路別パケット番号で異種 RTT 経路間の誤再送を抑止し、OTIAS 先行スケジューリングで受信側リオーダ負荷を軽減、さらに Homa（`draft-ousterhout-tsvwg-homa`）の受信側 `SACK+GRANT` で受信デコード負荷に応じたウィンドウ制御を行います。
- **Pre-AEAD O(1) 重複除外 + TLS 1.3 ドメイン分離 XOR-Nonce AEAD**: 16 バイトワード境界平文ヘッダ上の `seq` を復号・展開前に受信済みビットマップと照合し、重複パケットの暗号復号コストをゼロ化します。
- **ゼロアロケーション TypedArray LZ4 (`0x05`) & オフメインスレッド Worker プール**: 1 MB 先読みスラブと 2 KB ストライド Shannon エントロピー判定（$H < 7.30$）を組み合わせ、Browser Stream 初期化コストを排した同期 LZ4 ブロック圧縮（`0x05`）および `CryptoKey` 構造化クローンを用いた 2-Worker 復号プールで受信側メインスレッドを解放します（`0x03` / `0x04` 互換、UI 長時間タスクは 50ms 境界で `scheduler.yield()` 分割）。
- **FlEC / Tetrys 系統的 16 チャンク XOR パリティ FEC (`0x06`)**: QoS 0 リレー上で 16 チャンクごとの `Uint32Array` XOR パリティを並列経路へ重畳し、単一パケット損失やテール損失を再送 RTT 待ちなしの 0 RTT で受信側が自己修復します。
- **逐次 Sub-Blob コンパクション＆ゼロコピー後処理**: 128 チャンクごとに `Blob` へ逐次コンパクションし、`navigator.canShare` の事前 1 バイトプローブ、`decoding="async"`、`contain: layout style paint`、および `pagehide` 時の ObjectURL 解放により Safari でのメモリ負荷と再描画を抑制します。
- **極限までシンプルな SVG UI**: 絵文字を使用せず、自端末・相手端末の 5 桁コード明示と SVG アイコンのみで構成しています。
