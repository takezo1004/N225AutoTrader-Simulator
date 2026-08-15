# N225AutoTrader — シミュレーション（製品1・無料サンプル）

ブリッジ（N225AutoTrader）を **`--simulator`（MockBroker・外部非接続）** で動かし、Webhook 受信 → 発注 → 約定 → 建玉計上の流れを**テストダッシュボードのボタン7種**で体験する無料サンプルです。**kabu / TradingView / Cloudflare / ネット接続は一切不要**。

```
[テストダッシュボード] ──webhook(8000)──> [N225AutoTrader ブリッジ --simulator（MockBroker）]
   7種のペイロードをボタンで発火 → レスポンス／ログを画面で確認
```

---

## インストールには2つの道があります
- **基本ルート（このREADME）**＝**インストーラ1本をダブルクリックするだけ**（配布物はこの `N225AutoTrader-Simulator-Setup-x.y.z.exe` 1種類だけです。展開先の指定・Python・.NET・Claude Code すべて不要）。
- **保険ルート**＝うまく行かないとき **Claude Code** に手伝ってもらう（[`CLAUDE.md`](CLAUDE.md) ／ `/install` `/verify` `/diagnose`）。

---

## 必要なもの
- Windows 10（1809+）/ 11（x64）
- ※ **Python も .NET も不要**（すべて実行ファイルで完結）
- ※ kabu / TradingView / Cloudflare / 独自ドメインも**不要**
- （任意）ソース `n225_simulator_test_dashboard.py` から動かしたい場合のみ Python 3.10+（標準ライブラリのみ使用）

---

## 基本ルートの手順

### 1. インストーラを実行する
[Release ページ](../../releases/latest)の「**Assets**」から **`N225AutoTrader-Simulator-Setup-x.y.z.exe`**（約 60MB）をダウンロードしてダブルクリック。
- 一緒に並ぶ `～.sha256` は改ざん検証用のテキスト、「**Source code (zip)**」「**Source code (tar.gz)**」は GitHub が自動で付けるソースだけのファイルです（**exe が入っていないため動きません**）。落とすのは Setup.exe 1つだけで大丈夫です。
- 「Windows によって PC が保護されました」と出た場合は「詳細情報」→「実行」（未署名 exe への OS の一般的な警告です）。
- 「このアプリがデバイスに変更を加えることを許可しますか？」は「はい」。
- あとは案内に沿って進むだけで、**①ダッシュボード一式 ②自動売買の本体（ブリッジ）** の両方が自動で入ります（ブリッジが導入済みの PC ではブリッジ部分は自動でスキップ＝「あれば入れない」）。
- 完了すると、デスクトップに **「N225 シミュレーション」** のアイコンができます。

### 2. デスクトップの「N225 シミュレーション」をダブルクリック
- ダッシュボードが開き、導入済みブリッジを自動検出して `--simulator` で起動できます（MockBroker・本番口座に一切触れません）。
- 起動時にシミュレータ用設定（`*.simulator.json`・passphrase=`abcdefg`・`TestStrategy` 登録）を `%LOCALAPPDATA%\N225BrokerBridge\` に書き出します（本番設定 `*.Local.json` とは別ファイル）。

### 3. 7種のペイロードを試す
| # | テスト | 期待レスポンス |
|---|---|---|
| 1 | 認証失敗（passphrase 不一致） | `Authenticated_Failed` |
| 2 | Bad JSON | `Bad Request` |
| 3 | 新規買い（flat→long） | `NewOrderDispatched_` |
| 4 | 返済（long→flat） | `ExitOrderDispatched_` |
| 5 | ドテン（short→long） | `DotenDispatched_` |
| 6 | 未定義遷移（flat→flat） | `Ignored_` |
| 7 | 戦略未登録 | `Ignored_` |

レスポンスとブリッジログが画面に表示されます。

---

## うまく入らないとき（SmartScreen・ウイルス対策ソフト）

本パッケージの exe は**未署名**のため、環境によっては Windows やウイルス対策ソフトが警告・ブロックすることがあります（exe が悪意あるものだからではなく、「署名がない・配布実績が少ない」ことへの一般的な反応です）。

| 症状 | 対処 |
|---|---|
| 「Windows によって PC が保護されました」（SmartScreen） | 「**詳細情報**」→「**実行**」 |
| ダウンロードした exe が**消える・開けない**（AV が隔離/削除） | ウイルス対策ソフトの「**隔離**（検疫）」から**復元**し、対象ファイル（またはフォルダ）を「**除外**（許可）リスト」に追加してから再実行 |
| インストール中・起動時に AV が動作を止める | 同上（除外に追加）。それでも動かない場合は保険ルート（[`CLAUDE.md`](CLAUDE.md) の `/diagnose`）へ |

ダウンロードしたファイルが改ざんされていないかは、Release に添付の `.sha256` と照合して確認できます（PowerShell で `Get-FileHash <ファイル名> -Algorithm SHA256`）。

---

## 導入後の中身（`C:\Program Files\N225AutoTrader-Simulator\`）
| 場所 | 中身 |
|---|---|
| `起動_シミュレーション.exe` | テストダッシュボード（**アイコンをダブルクリックで起動**・Python 不要） |
| `n225_simulator_test_dashboard.py` | 同ダッシュボードの**ソースコード**（上記 exe の中身。読める・Python があれば直接実行も可） |
| `docs/` | 公開契約（`webhook-api-spec.md`・`simulator-mode.md`） |
| `webhook_test/` | 7種のペイロード＋手順（`payloads/`・`STEP_BY_STEP.md`・`test_all.ps1`） |
| `templates/` | 設定例（`appsettings.Local.json.example`） |
| `VERSION.json` | 版（sim_runtime / bridge / webhook-spec） |
| `CLAUDE.md`・`.claude/` | 保険ルート（Claude Code 命令書 `/install`・`/verify`・`/diagnose`） |

自動売買の本体（ブリッジ）は別フォルダ `C:\Program Files\N225BrokerBridge\`（`N225BrokerBridge.UI.exe` ほか）に入ります。インストーラが必要なときだけ自動で導入します（導入済みの PC ではスキップ）。

> ブリッジのソースコードは本パッケージには含まれません（v0.5.0〜・実行ファイルのみ）。ダッシュボードのソースは公開しており、どんな信号をどう送っているかは自由に読めます。

ライセンス：Public（無料サンプル）。
