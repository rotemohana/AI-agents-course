# Workspace Git workflow

When creating or modifying files, notebooks, or project/exercise directories in
this workspace:

- Check `git status --short` to identify new and modified files.
- Honor `.gitignore`, including Microsoft Word documents (`*.docx`), temporary
  system files, dependency caches, credentials, and local Git metadata backups.
  Never force-add ignored files.
- Provide exact, ready-to-run commands using explicit paths for relevant changes:
  `git add -- <paths>`, `git commit -m "<message>"`, and `git push origin main`.
- Draft a concise imperative commit message describing the actual change, such
  as `Add EX3 under HM` or `Add new script to langgraph`.
- Do not commit or push unless the user requests it. Do not include unrelated
  changes in suggested staging commands.
- Keep exercises as folders under `langgraph/HM`, with EX3 alongside EX2.
- Keep one root Git repository; do not initialize nested repositories.

These checks happen while working on a task; no background file watcher is
configured. Git tracks files, so an empty new folder needs a meaningful file
before it can be committed.
