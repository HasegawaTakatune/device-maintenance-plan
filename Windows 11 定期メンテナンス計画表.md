**Windows** | [macOS](macOS%20%E5%AE%9A%E6%9C%9F%E3%83%A1%E3%83%B3%E3%83%86%E3%83%8A%E3%83%B3%E3%82%B9%E8%A8%88%E7%94%BB%E8%A1%A8.md) | [iPhone](iPhone%20%E5%AE%9A%E6%9C%9F%E3%83%A1%E3%83%B3%E3%83%86%E3%83%8A%E3%83%B3%E3%82%B9%E8%A8%88%E7%94%BB%E8%A1%A8.md) | [Android](Android%20%E5%AE%9A%E6%9C%9F%E3%83%A1%E3%83%B3%E3%83%86%E3%83%8A%E3%83%B3%E3%82%B9%E8%A8%88%E7%94%BB%E8%A1%A8.md)

---

# Windows 11 定期メンテナンス計画表

Windows 11のセキュリティ確保とシステムの安定性を保つための、必要最小限の定期メンテナンスです。
コマンドは **管理者として開いたターミナル（PowerShell）** に貼り付けて実行します（スタートボタンを右クリック ＞ `ターミナル (管理者)`）。

---

## 📅 月1回

### ① 更新とウイルスチェック（コマンド一括）
```powershell
winget upgrade --all                                  # アプリを一括更新
Update-MpSignature                                    # ウイルス定義を更新
Start-MpScan -ScanType QuickScan                      # クイックスキャン
```
あわせて `設定` ＞ `Windows Update` ＞ `更新プログラムのチェック` でOS本体を更新します。

**効果**：OSとアプリの脆弱性を塞ぎ、ウイルス対策を最新の状態に保ちます。

---

## ⏳ 数ヶ月に1回

### ② システム点検・修復・一時ファイル削除（コマンド一括）
時間がかかるため、作業しない時間帯に実行します。
```powershell
Start-MpScan -ScanType FullScan                                                       # ウイルスのフルスキャン
DISM.exe /Online /Cleanup-image /Restorehealth                                        # システムイメージ修復
sfc /scannow                                                                          # システムファイル修復
DISM.exe /Online /Cleanup-Image /StartComponentCleanup                                # Windows Updateの残りファイル削除
Get-PhysicalDisk | Select FriendlyName, HealthStatus                                  # ディスク健康状態（Healthyなら正常）
Repair-Volume -DriveLetter C -Scan                                                    # ファイルシステム検査
Remove-Item "$env:TEMP\*" -Recurse -Force -ErrorAction SilentlyContinue               # 一時ファイル削除
Clear-RecycleBin -Force -ErrorAction SilentlyContinue                                 # ごみ箱を空にする
```
**効果**：ウイルス・システム破損・ディスク異常を検出して修復し、不要データを削除して空き容量を確保します。
※ディスクが `Warning` / `Unhealthy` の場合はすぐにバックアップを取ってください。

---

## 🔘 オプション：初回設定時／必要なときのみ

| メンテナンス項目 | 実施するタイミング | 具体的な操作手順 |
| :--- | :--- | :--- |
| **スタートアップアプリの見直し** | 初回設定時／起動が遅くなったとき | `設定` ＞ `アプリ` ＞ `スタートアップ` で、不要なアプリを **オフ** にする。 |
| **アカウント保護の確認** | 初回設定時 | Microsoftアカウントの2段階認証が有効か確認。BitLockerを使用している場合は、回復キーが保管されているか確認。 |
| **ドライバーの更新** | 画面の不具合やゲームの動作不良があるとき | グラフィックボード（NVIDIA/AMD等）の公式アプリ、またはPCメーカーの専用ソフトで更新。 |
| **BIOS/UEFI更新** | メーカーから重要告知があるとき／不具合が起きたとき | ① `Win + R` ＞ `msinfo32` で **システムモデル** と **BIOS バージョン/日付** を控える。<br>② PCメーカーのサポートページで機種名を検索し、BIOSの最新版と比較。<br>③ 新しければ、メーカーの手順書に従って更新。<br>※更新中は電源を切らず、ノートPCはAC電源を接続。失敗すると起動不能になるため、不具合がなければ更新不要です。 |

---

### ⚠️ 実行時の注意点
1. **過度な「高速化ソフト」や「レジストリ掃除」は避ける**
   非公式のクリーンアップソフトはシステムを破壊するリスクがあります。Windows標準機能だけで十分です。
2. **コマンドはエラーが出ても止めずに最後まで実行して構いません**
   結果の確認が必要なのは `Get-PhysicalDisk` の `HealthStatus` だけです。
