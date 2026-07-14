# N225AutoTrader シミュレーション（製品1・無料サンプル）— 購読者の Claude Code 用ガイド（保険ルート）

> このファイルは、**基本ルート（[`README.md`](README.md)＝オールインワン installer 1本をダブルクリック → デスクトップの「N225 シミュレーション」を起動）でうまくいかなかったとき**に、
> 購読者の Claude Code が手伝うための命令書です。基本が動いていれば本ファイルは不要です。
>
> v0.5.0（2026-07-14）：①ブリッジは **setup.exe（実行ファイル）のみ**の同梱（ソース非同梱）
> ②ダッシュボードは **`起動_シミュレーション.exe`（アイコン起動・Python 不要）** が主経路
> （ソース `.py` は透明性のため同梱継続・Python があれば直接実行も可）。
> 旧 v0.4.x の「bridge/ ソース同梱・dotnet build・起動bat」前提は廃止です。

---

## 0. このサンプルの全体像
ブリッジを `--simulator`（MockBroker・外部非接続）で動かし、Webhook の挙動を体験します。**kabu / TradingView / Cloudflare 不要**。
```
[テストダッシュボード] ──webhook(8000)──> [N225AutoTrader ブリッジ --simulator（MockBroker）]
```
- ブリッジ＝同梱の **`N225BrokerBridge-Setup-x.y.z.exe`** で導入（self-contained・**.NET 不要**・ソース非同梱）。
- テストダッシュボード＝**`起動_シミュレーション.exe`**（アイコン起動・**Python 不要**・ソース `.py` も同梱）が導入済みブリッジを自動検出して `--simulator` 起動＋7種ペイロード発火。

---

## 1. Claude Code が守る原則
1. **対話的・確認的**：導入・起動・設定変更は確認してから。
2. **秘密情報を露出しない**（このサンプルは Mock なので実秘密は不要だが原則は同じ）。
3. **環境差を吸収**：先に `/diagnose`。基本ルートは Python も .NET も不要（exe 2つのみ）。Python が要るのはソース `.py` から動かす代替経路だけ。
4. **配布ファイルを改変しない**：setup.exe・ダッシュボード・手順書を書き換えない。書込んでよいのは `%LOCALAPPDATA%\N225BrokerBridge\*.simulator.json` だけ。
5. **冪等性**：何度実行しても壊れない・済んだ項目は飛ばす。

---

## 2. スラッシュコマンド
| コマンド | 用途 |
|---|---|
| `/install` | 環境構築（同梱 **setup.exe でブリッジ導入** → `起動_シミュレーション.exe` の起動確認） |
| `/verify` | 動作確認（`--simulator` 起動 → テスト POST → レスポンス確認） |
| `/diagnose` | トラブル診断（プロセス／ポート8000／ログ） |

困ったら **`/diagnose`** から。詳細は各 `.claude/commands/*.md`。

---

## 3. 構成・ポート
- ブリッジ実体＝**setup.exe の導入先**（既定 `C:\Program Files\N225BrokerBridge\N225BrokerBridge.UI.exe`）。ダッシュボードがレジストリ／Program Files から自動検出。
- シミュレータ設定＝`%LOCALAPPDATA%\N225BrokerBridge\*.simulator.json`（本番 `*.Local.json` とは別）。
- ポート＝**8000**（simulator webhook）。passphrase＝`abcdefg`、戦略＝`TestStrategy`（自動投入）。

---

## 9. 開発者（著者）向け
本ファイルは**購読者向け命令書**。著者の開発ルールは `c:\Users\takao2\N225TradingSystem\CLAUDE.md`（別物）。配布の正本＝`DISTRIBUTION_MAP.md`。組立＝`distribution/sync_simulator.ps1`、リリース＝`distribution/release_simulator.ps1`。
