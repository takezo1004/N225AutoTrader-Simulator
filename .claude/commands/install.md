# /install — 環境構築（シミュレータ起動準備まで）

このコマンドは、Windows PC を N225AutoTrader ブリッジの **`--simulator` モードを起動できる状態まで**整えるセットアップです。

> 本パッケージ（v0.5.0〜）の基本ルートは **オールインワン installer（`N225AutoTrader-Simulator-Setup-x.y.z.exe`）1本**：
> ダッシュボード一式の導入＋ブリッジの自動導入（未導入時のみ）＋デスクトップ「N225 シミュレーション」アイコン作成まで全部行う。
> **Python も .NET も不要**。ビルド作業（dotnet build 等）も**ありません**。
> ZIP 版（ポータブル）の場合はブリッジ `N225BrokerBridge-Setup-*.exe` を手動実行する。
> Python が要るのは、ソース `n225_simulator_test_dashboard.py` から動かす**代替経路だけ**です。
> 本コマンドが扱うのはシミュレータ起動に必要な範囲のみで、本番接続（kabu / TV / Cloudflare）は対象外です。

---

## 前提

- 購読者の PC が Windows 10 (1809+) または Windows 11 (x64)
- 購読者は Release ページから installer（推奨）または ZIP を取得済み。installer 実行済みなら一式は `C:\Program Files\N225AutoTrader-Simulator\` にある
- 購読者は既に Claude Code を起動しており、本コマンドが呼ばれている

---

## 全体フロー (Phase 1 〜 Phase 4)

1. **Phase 1**. 環境の現状チェック
2. **Phase 2**. ブリッジの導入（同梱 setup.exe）
3. **Phase 3**. ダッシュボードの起動確認（`起動_シミュレーション.exe`）
4. **Phase 4**. （代替経路・通常は不要）Python の導入＝ソース `.py` から動かしたい場合のみ

---

## 動作原則 (重要 — 全 Phase 共通)

> 詳細は `CLAUDE.md` §1。本コマンド実行中も必ず守る。

- **実行環境はデスクトップ版 (Claude Desktop アプリ) 前提**。購読者はチャット欄だけを使う。**ターミナル・PowerShell ウィンドウ・VS Code を開かせない**。コマンドは Claude Code が内部で実行し、結果だけを見せる。
- **PATH 再読込が必要なときは「Claude Desktop アプリを完全に終了して起動し直してください」と案内する**。「新しいターミナル/シェルを開く」とは**言わない**（購読者の環境に存在しない）。
- **記事が進行役**。各 Phase を実行したら**結果だけを報告して止まる**。次手順の先導・予告をしない。
- **配布ファイルを書き換えない**。setup.exe・ダッシュボード・手順書・設定テンプレは編集しない。書き込んでよいのは `%LOCALAPPDATA%\N225BrokerBridge\` の `*.simulator.json` のみ。

---

## Phase 1. 環境の現状チェック

### 確認項目

| 項目 | 確認コマンド | 期待値 |
|---|---|---|
| Windows バージョン | `(Get-CimInstance Win32_OperatingSystem).Caption + ' ' + (Get-CimInstance Win32_OperatingSystem).Version` | Windows 10 build 17763 以上 または Windows 11 |
| ブリッジ導入済みか | `Test-Path "$env:ProgramFiles\N225BrokerBridge\N225BrokerBridge.UI.exe"` | `True` なら Phase 2 は不要（「あれば入れない」） |
| 同梱物が揃っているか | `Get-ChildItem "N225BrokerBridge-Setup-*.exe", "起動_シミュレーション.exe"` | 両方ある（無ければ Release ZIP の再取得を案内） |

※ **Python / .NET のチェックは不要**（ブリッジは self-contained・ダッシュボードは exe。どちらも追加ランタイム無しで動く）。

### 出力フォーマット
各項目について「✅ ある (バージョン)」「❌ ない」を一覧表示。最後に不足項目のサマリーを提示し、次に何をするか購読者に確認する。

### Windows バージョンが要件未満の場合
1809 未満 (build 17763 未満) なら**作業を中止**し、「Windows のバージョンが要件を満たしていません。1809 以上または Windows 11 へのアップグレードが必要です」と説明する。

---

## Phase 2. ブリッジの導入（同梱 setup.exe）

Phase 1 で導入済み（`Test-Path` が `True`）ならスキップ。

1. このフォルダ直下の `N225BrokerBridge-Setup-*.exe` を確認する:
   ```powershell
   Get-ChildItem "N225BrokerBridge-Setup-*.exe" | Select-Object Name, Length
   ```
   - 無い場合（git clone だけで ZIP を取っていない等）：「Release ページの ZIP（setup.exe 同梱）をダウンロードして展開してください」と案内する。
2. 購読者に「デスクトップ等に見えているこのファイルをダブルクリックしてください。『このアプリがデバイスに変更を加えることを許可しますか？』は『はい』、あとは案内に沿って進めてください」と伝える（管理者権限ダイアログは購読者がクリックする。Claude Code から静かに実行しない）。
3. 完了の申告を受けたら導入先を確認:
   ```powershell
   Test-Path "$env:ProgramFiles\N225BrokerBridge\N225BrokerBridge.UI.exe"
   ```

---

## Phase 3. ダッシュボードの起動確認

### Step 3-1. ブリッジ exe の存在確認

```powershell
Test-Path "$env:ProgramFiles\N225BrokerBridge\N225BrokerBridge.UI.exe"
```

`True` が返ればOK（既定以外の導入先を選んだ場合はレジストリ `HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{A0B1C2D3-E4F5-6789-ABCD-EF0123456789}_is1` の `InstallLocation` を確認）。

### Step 3-2. ダッシュボードの起動テスト

購読者に「`起動_シミュレーション.exe`（チャートのアイコン）をダブルクリックしてください」と案内。
- 「Windows によって PC が保護されました」（SmartScreen）が出たら「**詳細情報 → 実行**」で起動できると案内する（未署名 exe への一般的な警告）。
- 初回は展開のため数秒待つことがある。ダッシュボード画面が開けばOK（ブリッジの起動テストは記事／`/verify` の範囲）。

### Step 3-3. 完了報告

購読者に以下の**結果だけ**を提示して止まる（次の手順・次回予告はしない。次に何をするかは記事が指示する）:

- ブリッジの導入先
- ダッシュボードが起動できたこと
- 「構築作業はこれで完了です」とだけ伝える

---

## Phase 4. （代替経路・通常は不要）Python の導入

`起動_シミュレーション.exe` がどうしても起動しない環境（社内 AV による隔離等）で、ソース `n225_simulator_test_dashboard.py` から動かす場合のみ。**購読者の確認 (y/N) を取ってから実行**。

```powershell
winget install --id Python.Python.3.12 -e --accept-source-agreements --accept-package-agreements
```

- インストール後、PATH は起動中のプロセスに反映されない。「**Claude Desktop アプリを完全に終了して起動し直し、新しいチャットで作業を再開してください**」と案内する。
- 再起動後 `python --version` で反映を確認し、`python n225_simulator_test_dashboard.py` で起動（追加パッケージは不要・標準ライブラリのみ）。
- winget が無い場合は「Microsoft Store を開いて App Installer をインストールしてください」と案内する。

---

## エラー時の対処方針

各 Phase で失敗した場合は:

1. エラーメッセージを購読者に提示
2. 想定原因を 1〜3 件挙げる
3. 修復案を購読者に選んでもらう (Claude Code が勝手に修復しない)
4. 必要なら Phase を頭からやり直し

---

## バージョン

- v0.2.1 (2026-07-14): ダッシュボードをアイコン起動 exe（`起動_シミュレーション.exe`・Python 不要）へ。Python 導入は代替経路（Phase 4）に降格
- v0.2.0 (2026-07-14): ブリッジ exe-only 配布へ全面改訂（clone・venv・dotnet build の Phase を廃止）
- v0.1.0 (2026-05-27): 初版（旧3リポ構造・ソースビルド前提）
