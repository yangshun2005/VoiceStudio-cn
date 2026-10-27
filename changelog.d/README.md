# 变更日志片段

每个 Pull Request 添加一个文件，而不是直接编辑 `CHANGELOG.md`，这样 PR 之间不会冲突。`scripts/release.py` 会把片段合并进 `## [Unreleased]`，并在发布时删除它们。

- **命名：** `<PR 编号>.<类型>.md`，例如 `3263.fixed.md`。同一 PR 有第二条条目时加短后缀（`3263-b.docs.md`）。
- **类型：** `changed`、`added`、`docs`、`fixed`、`license` 或 `ci`，对应变更日志的 `### ...` 子节。
- **内容：** 一行，面向用户撰写，以 `(#NNN)` 结尾；社区贡献再加 `— thanks @user!`。开头的 `- ` 可选。

```
Saved voice rows keep their hover actions visible over long names (#3263)
```

`tests/test_changelog_fragments.py` 会检查每个片段。已在 `## [Unreleased]` 中的条目仍然有效，但新条目写入片段，不写入 `CHANGELOG.md`。