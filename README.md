# Kerwin-Workflow

個人 Claude Code 工作流工具箱：**討論 → 規格 → 施工 → 回寫**，一條龍。

```
你 ←─討論─→ Claude
        ↓
   /make-spec ──────────┬─ 出口 A → just-do-it-spec.md ─→ /just-do-it（並行 subagent 施工）
   （契約表確認閘門）     └─ 出口 B → SA 規格文件（客戶導讀／工程師規劃）
        ↓                                    ↓
   寫入知識資產                        /make-spec --close
   （CONTEXT.md／INTERFACES.md）       （回寫台帳＋捕捉教訓）
```

## 指令

| 指令 | 職能 |
|------|------|
| `/make-spec` | 規格精煉器。把討論結論精煉成介面契約鎖死的施工規格（出口 A）或 SA 文件（出口 B `--sa`）；`--close` 完工回寫台帳 |
| `/just-do-it` | 全自動施工。讀 spec → 拆 chunk → 並行 subagent → Code Review + 測試驗收 |

## 三層知識棧

| 層 | 檔案 | 回答的問題 | 生命週期 |
|----|------|-----------|---------|
| 業務 | 各專案 `CONTEXT.md` | 名詞是什麼意思 | 長存 |
| 技術 | 各專案 `INTERFACES.md` | 介面長什麼樣 | 長存 |
| 施工 | `just-do-it-spec.md` | 這次怎麼做 | 用完即棄 |

外加 `~/.claude/spec-lessons.md`：跨專案的產 spec 教訓庫（主動提議制記錄）。

## 安裝

```powershell
Copy-Item .\commands\*.md "$env:USERPROFILE\.claude\commands\" -Force
```

## 設計沿革

- 前身為 [Claude-Agent-Team](https://github.com/kerwin77106/Claude-Agent-Team)（team-workflow plugin，v2.1.0 後淘汰）。
  淘汰原因：PM Agent 被規則禁止回問使用者，需求模糊性被壓成未驗證的假設，一路傳導到實作，成品偏差大；七指令＋MySQL 狀態機的維護成本高於實際使用頻率。
- 保留其精華：SA 的契約粒度（ENTITY_CLASS／METHOD_SIGNATURE／DTO_CLASS…）與「先鎖契約再並行施工」的核心思想。
- 吸收外部精華（抄規則不裝 skill，避免觸發衝突）：
  - mattpocock/skills `grilling`：一次一題、附推薦答案、事實自查只問決策、按依賴順序問
  - mattpocock/skills `domain-modeling`：CONTEXT.md 術語字典＋衝突就抓／模糊就釘／情境壓測／與 code 對質
  - superpowers `brainstorming`：覆蓋清單（資料流／錯誤處理／邊界／測試）＋定稿自審四問＋架構級決策攤 2-3 案
