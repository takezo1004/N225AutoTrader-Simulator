# N225AutoTrader — シミュレーション（製品1・無料サンプル）

ブリッジ（N225AutoTrader）を **`--simulator`（MockBroker・外部非接続）** で動かし、Webhook 受信 → 発注 → 約定 → 建玉計上の流れを**テストダッシュボードのボタン7種**で体験する無料サンプルです。**kabu / TradingView / Cloudflare / ネット接続は一切不要**。

```
[テストダッシュボード] ──webhook(8000)──> [N225AutoTrader ブリッジ --simulator（MockBroker）]
   7種のペイロードをボタンで発火 → レスポンス／ログを画面で確認
```

---

## インストールには2つの道があります
- **基本ルート（このREADME）**＝**インストーラ1本をダブルクリックするだけ**（展開先の指定・Python・.NET・Claude Code すべて不要）。
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
Release ページから **`N225AutoTrader-Simulator-Setup-x.y.z.exe`** をダウンロードしてダブルクリック。
- 「Windows によって PC が保護されました」と出た場合は「詳細情報」→「実行」（未署名 exe への OS の一般的な警告です）。
- 「このアプリがデバイスに変更を加えることを許可しますか？」は「はい」。
- あとは案内に沿って進むだけで、**①ダッシュボード一式 ②自動売買の本体（ブリッジ）** の両方が自動で入ります（ブリッジが導入済みの PC ではブリッジ部分は自動でスキップ＝「あれば入れない」）。
- 完了すると、デスクトップに **「N225 シミュレーション」** のアイコンができます。

### 2. デスクトップの「N225 シミュレーション」をダブルクリック
- ダッシュボードが開き、導入済みブリッジを自動検出して `--simulator` で起動できます（MockBroker・本番口座に一切触れません）。
- 起動時にシミュレータ用設定（`*.simulator.json`・passphrase=`abcdefg`・`TestStrategy` 登録）を `%LOCALAPPDATA%\N225BrokerBridge\` に書き出します（本番設定 `*.Local.json` とは別ファイル）。

---

## ZIP 版（ポータブル）の手順

展開先を自分で決めたい人向けに、同じ中身の ZIP（`N225AutoTrader-Simulator-x.y.z.zip`）も Release に置いています。ZIP 版を使う場合の手順です。

### 1. ダウンロードする
[Release ページ](../../releases/latest)を開き、「**Assets**」の一覧から **`N225AutoTrader-Simulator-x.y.z.zip`** をクリックします（約 59MB）。
- ⚠️ すぐ下に並ぶ「**Source code (zip)**」「**Source code (tar.gz)**」は GitHub が自動で作るソースだけのファイルで、**exe が入っていないため動きません**。選ばないでください。
- ダウンロードしたファイルは、パソコンの「**ダウンロード**」フォルダに保存されます（エクスプローラー左側の「ダウンロード」から開けます）。

### 2. 展開する（この手順を飛ばさない）
1. 「ダウンロード」フォルダの `N225AutoTrader-Simulator-x.y.z.zip` を**右クリック** → 「**すべて展開**」を選ぶ。
2. **展開先を必ず指定します**：ダイアログの「**参照**」を押し、「**デスクトップ**」を選んで「フォルダーの選択」→「展開」。
   - そのまま展開するとダウンロードフォルダの中に作られてしまい、あとで見つけにくくなります。デスクトップに置けば、以後いつでもすぐ開けます。
3. 展開が終わると `N225AutoTrader-Simulator-x.y.z` というフォルダができて自動で開きます。以後はこのフォルダの中で作業します。

展開したフォルダの中身はこうなっています（使うのは★の2つだけ）:
```
N225AutoTrader-Simulator-x.y.z\
├── N225BrokerBridge-Setup-x.y.z.exe   ★本体（ブリッジ）の導入プログラム
├── 起動_シミュレーション.exe          ★ダッシュボード（操作画面）
├── n225_simulator_test_dashboard.py    ダッシュボードのソースコード（読み物）
├── README.md ほか docs\ webhook_test\  手順書・公開仕様・テストデータ
└── VERSION.json など                   版情報
```

> ⚠️ **ZIP をダブルクリックして「中を覗いた状態」のまま exe を実行しないでください。** 一見動きそうに見えますが、必要なファイルが一緒に取り出されないため正しく動きません。必ず「すべて展開」してから使います。

### 3. 実行する（2つの exe を順番に）
展開してできたフォルダの中で：
1. **`N225BrokerBridge-Setup-x.y.z.exe`**（ブリッジ導入）をダブルクリック → UAC「はい」→ 案内どおり進める（導入済みの PC ならこの手順は不要）。
2. **`起動_シミュレーション.exe`** をダブルクリック → ダッシュボードが開きます。

> GitHub からソースだけを clone した場合、exe 類はリポジトリに含まれません（Release から取得してください）。

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
| `起動_シミュレーション.exe` | テストダッシュボード（**アイコンをダブルクリックで起動**・Python 不要） |
| `n225_simulator_test_dashboard.py` | 同ダッシュボードの**ソースコード**（上記 exe の中身。読める・Python があれば直接実行も可） |
| `docs/` | 公開契約（`webhook-api-spec.md`・`simulator-mode.md`） |
| `webhook_test/` | 7種のペイロード＋手順（`payloads/`・`STEP_BY_STEP.md`・`test_all.ps1`） |
| `templates/` | 設定例（`appsettings.Local.json.example`） |
| `VERSION.json` | 版（sim_runtime / bridge / webhook-spec） |
| `CLAUDE.md`・`.claude/` | 保険ルート（Claude Code 命令書 `/install`・`/verify`・`/diagnose`） |

> ブリッジのソースコードは本パッケージには含まれません（v0.5.0〜・実行ファイルのみ）。ダッシュボードのソースは公開しており、どんな信号をどう送っているかは自由に読めます。

ライセンス：Public（無料サンプル）。
