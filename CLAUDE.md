# Kerwin-Workflow — 專案規範

## 專案定位

Kerwin 的個人 Claude Code 工作流工具箱。核心流水線：

```
跟 Claude 討論 → /make-spec（規格精煉）→ /just-do-it（並行施工）→ /make-spec --close（回寫台帳）
```

取代已淘汰的 team-workflow plugin（Claude-Agent-Team repo，2026-07-29 uninstall）。
設計沿革與決策記錄見 README.md。

## Source of Truth 規範（重要）

- **本 repo 的 `commands/` 是正本**；`~/.claude/commands/` 裡的同名檔案是安裝副本。
- 修改流程：改 repo → 複製安裝 → 測試。**禁止只改 `~/.claude/commands/` 的副本**（會跟正本漂移，這是 team-workflow 時代踩過的坑）。
- 安裝指令（PowerShell）：

```powershell
Copy-Item "$PSScriptRoot\commands\*.md" "$env:USERPROFILE\.claude\commands\" -Force
```

或手動複製 `commands\*.md` 到 `C:\Users\User\.claude\commands\`。

## 檔案格式

- 換行一律 CRLF（Windows 標準）
- 編碼 UTF-8
- 指令檔 frontmatter 必含 `name` 與 `description`（description 內含觸發關鍵字）

## 知識資產檔案（本 repo 不存放，只定義格式）

| 檔案 | 實際位置 | 說明 |
|------|---------|------|
| `CONTEXT.md` | 各專案根目錄 | 術語字典，格式定義在 make-spec.md |
| `INTERFACES.md` | 各專案根目錄 | 介面台帳，格式定義在 make-spec.md |
| `spec-lessons.md` | `~/.claude/` | 跨專案教訓庫。**不放本 repo**：若未來改回 plugin 發佈，cache 更新會覆蓋掉累積內容 |

## 修改原則

- 兩支指令是上下游關係：make-spec 的輸出格式必須持續命中 just-do-it Phase 1 的「路徑 A」判定（有檔案路徑＋驗收條件）。**改任一支時先確認交接格式沒破**。
- just-do-it.md 的 subagent prompt 段落除非實測證明 subagent 不守契約，否則不動。
