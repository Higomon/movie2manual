<div align="center">

# 🎬 movie2manual

**作業動画から、スクショ付きの手順マニュアルをブラウザだけで自動生成。**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Powered by Google Gemini](https://img.shields.io/badge/Powered%20by-Google%20Gemini-4285F4?logo=googlegemini&logoColor=white)](https://ai.google.dev/)
[![Single HTML file](https://img.shields.io/badge/single-HTML%20file-E34F26?logo=html5&logoColor=white)](index.html)
[![Built with Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-D97757?logo=anthropic&logoColor=white)](https://claude.com/claude-code)

**日本語** · [English](README.en.md)

### ▶️ いますぐ使う → **https://higomon.github.io/movie2manual/**

</div>

---

操作の録画を読み込ませると、AI（Gemini）が手順マニュアル（章立て・手順・説明文＋各手順の画面写真つき）を自動生成します。インストール不要、ブラウザだけで完結。

## 使い方

> **Chrome / Edge** で開いてください。

1. **🔑 キーを用意** — [Google AI Studio](https://aistudio.google.com/apikey) で無料の Gemini API キーを作成（`AIza…` をコピー）
2. **🌐 アプリを開く** — **[higomon.github.io/movie2manual](https://higomon.github.io/movie2manual/)**
3. **▶️ 作る** — キーを貼る → 動画を選ぶ → 詳細度を選ぶ → **「生成」** → 完成した HTML を保存

⚠️ **AI が作るので完璧ではありません。最後に必ず人の目で確認・修正してください。**
🔑 キーはあなたのブラウザにだけ保存され、Google 以外（このサイトの管理者を含む）には送信されません。

<details>
<summary><b>もっと詳しく（プライバシー・無料/有料枠・仕組み）</b></summary>

<br>

### プライバシー・セキュリティ

- API キーとアップロードした動画は、**ブラウザから Google の Gemini API へ直接**やり取りされます。**こちら側のサーバには一切渡りません**（GitHub Pages は静的配信のみ）。
- API キーはあなたのブラウザ内（localStorage）にだけ保存され、Web版・ローカルどちらでも挙動は同じです。
- より厳密に管理したい場合は `index.html` をダウンロードしてローカルで開けば、実行するコードを自分の手元に固定できます。
- 出力したマニュアルには元動画の画面写真が埋め込まれます。機密動画から作ったものの取り扱いには注意してください。
- 本ソフトウェアは**無保証・自己責任**です。動作・生成結果の正確性は保証されず、損害について作者は責任を負いません（法的条件は [LICENSE](LICENSE)）。

### 無料枠（AI Studio Free）と有料枠

無料枠でも動きますが、**429（レート制限）は1日あたり（RPD）ではなく1分あたりの回数（RPM）/トークン（TPM）で発生**します。動画は1呼び出しごとにフレームがトークン計上されるため、無料枠では並列を上げると TPM 上限に当たりやすい。本ツールは:

- **既定は無料安全モード（並列度 K=2 固定）**。429 応答本文の `retryDelay` を読み、指定秒だけ待って自動リトライ。
- **有料キーなら並列を上げられます。** console で一度だけ `window.__mfSetTier('paid')`（K=4 に。戻すなら `'auto'`）。
- 生成後 `window.__mfGenTrace()` で `[quota] tier=… K=… by429=… dim=tokens(TPM)/requests(RPM) …` の実測内訳を確認できます。

### 仕組み

- **画像キャプチャ**: `video.currentTime` の seek はキーフレームが疎な動画（long GOP）だと1枚に数分かかることがあるため、**WebCodecs** で動画を1回リニアデコードし全フレームを一括取得（PoC: 736秒の動画12点を約9秒）。非対応環境は従来 seek にフォールバック。
- **生成パイプライン**: 構造解析（skeleton, `temperature=0`）→ バッチ詳細化（並列）→ マージ＆サニタイズ（タイムスタンプを単調・動画長内に再配分）→ 画像キャプチャ。挙動は `window.__captureDiag` に記録。
- **動作環境**: 画像取得は WebCodecs 依存で Chrome/Edge が最安定（Safari 一部制限）。長尺・高解像度は時間とメモリがかかる（8K 等は自動縮小）。

### オフライン/ローカルで使う

`index.html` をダウンロードしてダブルクリックでも同じように動きます（`file://`）。

</details>

## 使用ツール

[Claude Code](https://claude.com/claude-code)（Anthropic）を用いて開発し、各コミットに共著者として記録。

## ライセンス

[MIT License](LICENSE) © 2026 Yoshiro OHGI
