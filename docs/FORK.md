# Fork 关系与本地差异说明

## 与上游的关系

- 上游仓库：`XiaoyeCodes/code-animation-prompts`（GitHub 标记为 parent / source）
- 本仓：`Janlhin/code-animation-prompts`，公开仓库，默认分支 `main`
- 本仓是上游的 fork，产品内容（风格提示词、参考静帧、技能包、脚本）以上游为准；本批治理改动只落在治理层（`docs/`、`.github/`、根目录治理文件、`tools/`），不改上游核心内容。

## 本仓相对上游的差异

比对依据：2026-11-26 在本地以 `--depth 1` 拉取上游 `main` 后执行：

- `git rev-list --left-right --count upstream/main...HEAD` → `0  0`
- `git diff --stat upstream/main HEAD` → 空

结论：快照层面本仓 `main` 与上游 `main` 一致，未检出文件级本地改动。历史提交的先后与分叉情况未核对（浅克隆，完整历史未拉取）。

本批治理分支上新增的内容（仅治理层，不属于上游产品内容）：

- `docs/FORK.md`：本文件。
- `CHANGELOG.md`：记录本仓改动。
- `.github/pull_request_template.md`：PR 填写模板。
- `.github/dependabot.yml`：仅跟踪 `github-actions` 生态的版本更新；产品依赖更新交给上游。
- `.github/workflows/verify.yml`：只检查本仓治理文件是否齐全、结构是否完整，不复刻上游校验流程。
- `tools/check-governance.py`：治理文件自检脚本（纯标准库）。

## 禁止本地魔改的目录与文件

以下内容来自上游，本地分支不得修改、不得格式化重排：

- `prompts/`：提示词与素材目录。
- `skills/`：技能包（含 `SKILL.md`、`agents/`、`assets/`、`references/`、`scripts/`）。
- `scripts/`：上游构建与校验脚本。
- `docs/INSTALL.md`、`docs/STYLES.md`、`docs/source-manifest.json`、`docs/images/`：上游文档、清单与图片。
- `README.md`、`CONTRIBUTING.md`、`LICENSE.md`：上游既有正文与许可。

如确需改动上述内容，应先向上游提交并在本仓同步，而不是在 fork 内单独魔改。

## 同步上游的步骤

1. 添加上游远程（本地备份中已添加）：

   ```bash
   git remote add upstream https://github.com/XiaoyeCodes/code-animation-prompts.git
   ```

2. 拉取上游最新 `main`：

   ```bash
   git fetch upstream main
   ```

3. 在治理分支上合并或变基上游：

   ```bash
   git merge upstream/main   # 或 git rebase upstream/main
   ```

4. 冲突只允许出现在治理文件上；若上游核心目录出现冲突，先核对是否误改。

5. 跑上游既有校验（命令以上游 `CONTRIBUTING.md` 为准）：

   ```bash
   python scripts/validate_repo.py
   python skills/code-animation-prompts/scripts/styles.py search 样式 --limit 3
   ```

6. 推送前确认 `git diff --stat upstream/main` 中不包含被禁止魔改的目录。

## 上游既有 CI 说明

上游随仓提供 `.github/workflows/validate.yml`（Validate library and skill），运行上游脚本 `scripts/validate_repo.py` 与技能脚本。本批治理**不复刻**该工作流；审计基线显示该工作流在本仓未产生运行记录，原因未核对。
