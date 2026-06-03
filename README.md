<div align="center">

# 🎬 movie2manual

**作業動画から、スクリーンショット付きの手順マニュアルをブラウザだけで自動生成。**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Powered by Google Gemini](https://img.shields.io/badge/Powered%20by-Google%20Gemini-4285F4?logo=googlegemini&logoColor=white)](https://ai.google.dev/)
[![Capture: WebCodecs](https://img.shields.io/badge/Capture-WebCodecs-FF6F00?logo=googlechrome&logoColor=white)](https://developer.mozilla.org/docs/Web/API/WebCodecs_API)
[![Single HTML file](https://img.shields.io/badge/single-HTML%20file-E34F26?logo=html5&logoColor=white)](index.html)
[![No install](https://img.shields.io/badge/install-none-2ea44f)]()
[![Built with Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-D97757?logo=anthropic&logoColor=white)](https://claude.com/claude-code)

**日本語** · [English](README.en.md)

### ▶️ いますぐ使う → **https://higomon.github.io/movie2manual/**

</div>

---

Gemini API で動かす単一HTMLアプリ。サーバーもビルドもインストールも不要。装置の操作動画やソフトの操作録画を読み込ませると、Gemini が内容を解析して章立て・手順・各ステップの説明文を生成し、説明に対応する瞬間の**静止画を動画から自動でキャプチャ**して埋め込んだ、1ファイル完結のHTMLマニュアルを出力します。

## 使い方（3ステップ）

> 🌐 ブラウザは **Chrome か Edge** を使ってください。

### 1. 🔑 無料の Gemini API キーを取得する
[**Google AI Studio（こちら）**](https://aistudio.google.com/apikey) を開く → Googleアカウントでログイン → **「Create API key（APIキーを作成）」** をクリック → 表示された文字列（`AIza…`）をコピー。
※ Googleアカウントがあれば無料で取得できます。キーは「アプリのパスワード」のようなものなので、他人に教えないでください。

### 2. 🌐 アプリを開く
**[https://higomon.github.io/movie2manual/](https://higomon.github.io/movie2manual/)** をクリックするだけ。インストールもダウンロードも不要です。

### 3. ▶️ マニュアルを作る
1. コピーした API キーを入力欄に貼り付ける（次回からは不要）
2. 動画ファイルを選ぶ
3. 詳細度（簡易版 / 標準版 / 詳細版）を選ぶ
4. **「生成」** を押す → マニュアルと画像が自動でできあがる
5. 完成した HTML を保存（画像も中に入っているので1ファイルで完結）

> 💡 オフラインで使いたい・手元にファイルを置きたい場合は、この `index.html` をダウンロードしてダブルクリックでも同じように動きます。

> ⚠️ 出力された HTML マニュアルには、元動画のスクリーンショットがそのまま埋め込まれます。機密・社外秘の動画から作ったマニュアルの取り扱いにはご注意ください。

## 無料枠（AI Studio Free）と有料枠について

無料枠でも動きますが、**429（レート制限）は1日の合計回数（RPD）ではなく、1分あたりの回数（RPM）/トークン（TPM）で発生**します。動画は1回の API 呼び出しごとにフレームがトークンとして計上されるため、無料枠では並列度を上げると TPM 上限に当たりやすくなります。本ツールは:

- **既定は無料安全モード（並列度 K=2 固定）**。Gemini の 429 応答本文（`retryDelay`）を読んで、サーバーが指定した秒数だけ待って自動リトライします。
- **有料 API キーなら並列度を上げられます。** ブラウザの console で一度だけ実行:
  ```js
  window.__mfSetTier('paid')   // → 次の生成から並列度 K=4。無料に戻すなら __mfSetTier('auto')
  ```
- 生成後、console で `window.__mfGenTrace()` を実行すると、`[quota] tier=… K=… by429=… dim=tokens(TPM)/requests(RPM) …` の形で**レート制限の実測内訳**が確認できます。

## 画像キャプチャの仕組み

ブラウザの `video.currentTime` による seek は、キーフレームが疎な動画（long GOP）だと1フレーム取得に数分かかることがあります。本ツールは **WebCodecs** で動画を1回だけリニアにデコードし、全手順分のフレームを一括取得することでこれを回避します（PoC: 736秒の動画12点を約9秒）。WebCodecs が使えない環境では従来の seek にフォールバックします。

## 仕組み（アーキテクチャ概要）

1. **構造解析（skeleton）** — 動画全体から章・手順の骨子とタイムスタンプを生成（`temperature=0`）
2. **バッチ詳細化** — 骨子を複数バッチに分割し、各セクションの説明文を並列生成
3. **マージ＆サニタイズ** — タイムスタンプを単調・動画長内に再配分（capture の逆行・範囲外を防止）
4. **画像キャプチャ** — 各手順の時刻のフレームを WebCodecs で取得し埋め込み

生成の挙動は `window.__captureDiag`（エクスポートHTMLに base64 で同梱）に記録され、`window.__mfGenTrace()` で要約を確認できます。

## 動作環境・制約

- 画像取得は WebCodecs 依存のため **Chrome / Edge** が最も安定（Safari は一部制限）
- 長尺・高解像度動画はメモリと時間がかかります（解像度ゲートで 8K 等は自動的に縮小）
- 生成内容（手順・タイムスタンプ）は Gemini の動画理解に依存し、完璧ではありません。**最終的な内容と画像の一致は人間の確認を前提**としています（要確認のステップにはバッジが付きます）

## プライバシー・セキュリティ

- **API キーはあなた自身のブラウザの中にだけ保存されます**（localStorage）。Web版（`https://higomon.github.io/movie2manual/`）でも、ダウンロードしたローカルファイルでも挙動は同じです。キーはあなたのブラウザに留まり、**Google の Gemini API 以外には送信されません**（このサイトの管理者にも送られません）。
- 動画も Gemini API に送られ Google 側で処理されます（Google の利用規約に従います）。**こちら側のサーバには一切渡りません**（GitHub Pages は静的配信のみで、サーバ処理はありません）。
- より厳密に管理したい場合は、`index.html` をダウンロードしてローカルで開いてください。実行するコードを自分の手元に固定できます（Web版は管理者が更新するとコードが変わり得ます）。
- 本ソフトウェアは**無保証・自己責任**でのご利用をお願いします。動作や生成結果の正確性は保証されず、利用によって生じた損害について作者は責任を負いません（法的な条件は [LICENSE](LICENSE) を参照）。

## 使用ツール

本プロジェクトは [Claude Code](https://claude.com/claude-code)（Anthropic）を用いて開発し、各コミットに共著者（Co-authored-by）として記録しています。

## ライセンス

[MIT License](LICENSE) © 2026 Yoshiro OHGI
