# cleanup

A small, safety-first maintenance tool for **macOS, Linux and Windows**. It finds the junk that piles up on a developer machine (package-manager caches, build artifacts, temp files, the trash) and tells you how big it is. It only deletes anything after you say yes.

> **Status:** early. A macOS bash script works today. A zero-dependency Node.js CLI that runs on all three platforms is being built in the open. See the [roadmap (#21)](https://github.com/maydaythecoder/cleanup/issues/21).

## Principles

- **Report first.** Every scan shows sizes before anything is touched.
- **Ask before deleting.** Each category gets its own confirmation. `--dry-run` never deletes.
- **Never touch personal files.** Only known caches and regenerable artifacts are in scope. Documents, photos and source code are off limits.
- **Stay light.** Plain Node.js (≥ 18), no runtime dependencies, nothing to install beyond Node.

## Quick start (macOS, current bash script)

```sh
git clone https://github.com/maydaythecoder/cleanup.git
cd cleanup
./find-and-clean-caches.sh
```

The script:

1. Shows free disk space.
2. Lists the sizes of known cache folders (npm, yarn, pnpm, bun, pip, poetry, uv, Homebrew, Xcode, Gradle, Maven, Go, Cargo, Terraform).
3. Finds Python caches (`__pycache__`, `.pytest_cache`, …) and Node build folders (`node_modules`, `.next`, `dist`, …) under `~/Documents`, `~/dev` and `~/Developer`.
4. Asks y/n for each cleanup group, then shows how much space was recovered.

### What each cleanup does

| Group | What it runs | Risk |
| --- | --- | --- |
| Package-manager caches | `npm cache clean`, `pip cache purge`, `brew cleanup -s`, `uv cache clean`, … | Low. Things re-download when needed. |
| Docker | `docker builder prune -af` **and `docker system prune -af`** | **High.** Removes all unused images and stopped containers ([#8](https://github.com/maydaythecoder/cleanup/issues/8)). |
| Python project caches | deletes `__pycache__`, `.pytest_cache`, `.mypy_cache`, `.ruff_cache`, `.tox`, `.nox` | Low |
| Node project folders | deletes `node_modules`, `.next`, `.turbo`, `.vite`, `.parcel-cache`, **`dist`, `build`** | **Medium.** `dist`/`build` can be real folders ([#7](https://github.com/maydaythecoder/cleanup/issues/7)). You'll need `npm install` again. |
| macOS cache + Trash | `rm -rf ~/Library/Caches/*` and `~/.Trash/*` | Medium. Apps rebuild their caches, but emptying the Trash can't be undone. |

Read each prompt before you answer. Commit or push your work before running a cleanup.

## Planned CLI

```sh
node bin/cleanup.js --dry-run     # scan and report, delete nothing
node bin/cleanup.js               # interactive, confirm each category
node bin/cleanup.js --yes         # non-interactive (low-risk categories only)
node bin/cleanup.js report        # RAM, startup items, large/old files (read-only)
```

## Platform support

| | macOS | Linux | Windows |
| --- | --- | --- | --- |
| Bash script | ✅ | ⚠️ partly | ❌ |
| Node CLI: cache scan | 🚧 | 🚧 | 🚧 |
| Node CLI: RAM / startup report | 🚧 | 🚧 | 🚧 |
| Scheduled weekly report | 🚧 | 🚧 | 🚧 |

## Contributing

This project is built by humans, one ticket at a time.

1. Browse [open issues](https://github.com/maydaythecoder/cleanup/issues) and filter by `difficulty: easy`, `difficulty: medium` or `difficulty: hard`. New here? Start with [`good first issue`](https://github.com/maydaythecoder/cleanup/labels/good%20first%20issue).
2. Comment on an issue to claim it, then open a PR that references it (`Closes #N`).
3. Ground rules:
   - Node ≥ 18, ES modules, **no runtime dependencies**.
   - Tests use the built-in `node:test` runner.
   - Anything that deletes files needs a dry-run path and a test. Issues labeled `safety` get extra review.

## License

[MIT](LICENSE)
