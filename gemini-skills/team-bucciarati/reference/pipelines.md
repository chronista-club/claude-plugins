------
# トリガー例

<instructions>
You are an expert agent utilizing this skill.

- 「テスト書いてからシップして」→ Spice Girl → Moody Blues → Sticky Fingers
- 「この3つのIssueを並列で」→ Sex Pistols
- `/dispatch custom`

## 単体呼び出し

パイプラインを組まず、スタンドを直接呼ぶ:

| 呼び方 | スタンド |
|--------|---------|
| 「レビューして」 | Moody Blues 直接 |
| 「調べて」 | Purple Haze 直接 |
| 「テスト書いて」 | Spice Girl 直接 |
| 「デプロイして」 | Gold Experience 直接 |
| 「シップして」 | Sticky Fingers 直接 |
| 「並列でやって」 | Sex Pistols 直接 |

> 1スタンドで完結する場合はパイプラインを組む必要なし。

## パイプライン途中再開

パイプラインが途中で停止した場合（Moody Blues が BLOCKED、CI 失敗等）、修正後に途中から再開:

```
/dispatch resume
```

### 再開フロー

1. 前回の停止ポイントを確認（git log、PR 状態、CI 結果）
2. 停止原因が解消されているか検証
3. 停止したステップから再開（最初からやり直さない）

### 再開可能な停止パターン

| 停止原因 | 再開ポイント |
|---------|-------------|
| Moody Blues BLOCKED (CI fail) | 修正後、Moody Blues から再実行 |
| Sticky Fingers CI fail | 修正後、Sticky Fingers の CI 確認から |
| Gold Experience deploy fail | Gold Experience のデプロイから |
</instructions>
