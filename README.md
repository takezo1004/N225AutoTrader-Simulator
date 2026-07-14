# N225AutoTrader — シミュレーション（製品1・無料サンプル）

ブリッジ（N225AutoTrader）を **`--simulator`（MockBroker・外部非接続）** で動かし、Webhook 受信 → 発注 → 約定 → 建玉計上の流れを**テストダッシュボードのボタン7種**で体験する無料サンプルです。**kabu / TradingView / Cloudflare / ネット接続は一切不要**。

```
[テストダッシュボード] ──webhook(8000)──> [N225AutoTrader ブリッジ --simulator（MockBroker）]
   7種のペイロードをボタンで発火 → レスポンス／ログを画面で確認
```

---

## インストールには2つの道があります
- **基本ルート（このREADME）**＝setup.exe で導入して起動（Claude Code 不要）。
- **保険ルート**＝うまく行かないとき **Claude Code** に手伝ってもらう（[`CLAUDE.md`](CLAUDE.md) ／ `/install` `/verify` `/diagnose`）。

---

## 必要なもの
- Windows 10（1809+）/ 11（x64）
- **Python 3.10+**（テストダッシュボード。標準ライブラリのみ使用）
- ※ .NET は**不要**（ブリッジは self-contained の setup.exe で導入）
- ※ kabu / TradingView / Cloudflare / 独自ドメインも**不要**

---

## 基本ルートの手順

### 1. ブリッジを導入する
フォルダ直下の **`N225BrokerBridge-Setup-x.y.z.exe`** をダブルクリックして導入します（管理者確認ダイアログは「はい」）。すでに導入済みなら、この手順は飛ばして構いません（「あれば入れない」）。

> GitHub からソースだけを clone した場合、setup.exe はリポジトリに含まれません。**Release ページの ZIP**（setup.exe 同梱）を取得してください。

### 2. テストダッシュボードを起動
ルートの **`起動_シミュレーション.bat`** をダブルクリック。
- ダッシュボードが導入済みブリッジを自動検出し、`--simulator` で起動します（MockBroker・本番口座に一切触れません）。
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

## 同梱物
| 場所 | 中身 |
|---|---|
| `N225BrokerBridge-Setup-x.y.z.exe` | N225AutoTrader ブリッジ導入プログラム（**実行ファイルのみ**・self-contained） |
| `n225_simulator_test_dashboard.py` ＋ `起動_シミュレーション.bat` | テストダッシュボード（Python・stdlib のみ・**ソース公開**） |
| `docs/` | 公開契約（`webhook-api-spec.md`・`simulator-mode.md`） |
| `webhook_test/` | 7種のペイロード＋手順（`payloads/`・`STEP_BY_STEP.md`・`test_all.ps1`） |
| `templates/` | 設定例（`appsettings.Local.json.example`） |
| `VERSION.json` | 版（sim_runtime / bridge / webhook-spec） |
| `CLAUDE.md`・`.claude/` | 保険ルート（Claude Code 命令書 `/install`・`/verify`・`/diagnose`） |

> ブリッジのソースコードは本パッケージには含まれません（v0.5.0〜・実行ファイルのみ）。ダッシュボードのソースは公開しており、どんな信号をどう送っているかは自由に読めます。

ライセンス：Public（無料サンプル）。
