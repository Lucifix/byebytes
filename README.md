# byebytes

**Find and clean the gigabytes developers forget about.**

Old `node_modules`, Rust `target` folders, Python venvs, Xcode DerivedData, every Cypress version you've ever installed, old Node versions, Docker leftovers... `byebytes` finds all of it in one interactive terminal screen and lets you clean it safely.

- **One file, zero dependencies.** Pure Python standard library (3.8+). Works with the Python already on your Mac.
- **Two views:**
  - **Projects** — build and dependency folders inside your projects, across 20+ ecosystems.
  - **Caches** — global caches from package managers, test browsers, Xcode, editors, Docker and more.
- **Safe by design.** Every item is tagged *safe*, *review* or *protected*, and nothing is deleted without confirmation.
- **Uses each tool's own cleanup command** when one exists (`npm cache clean`, `pnpm store prune`, `brew cleanup`, `docker system prune`, ...).
- **Fast.** Folders appear while scanning; sizes are measured in parallel.

## Install

```bash
# run it directly
curl -O https://raw.githubusercontent.com/Lucifix/byebytes/main/byebytes.py
python3 byebytes.py

# or install it as a command
pipx install git+https://github.com/Lucifix/byebytes
byebytes
```

## Usage

```bash
byebytes                  # scan your home folder
byebytes ~/code           # scan a specific folder
byebytes -m 100M          # hide anything smaller than 100 MB
byebytes -P               # projects only
byebytes -C               # global caches only
byebytes --list           # plain tables, no UI
byebytes --json           # machine-readable output
```

### Keys

| Key | Action | Key | Action |
|---|---|---|---|
| `tab` / `1` / `2` | switch Projects / Caches | `d` | delete / clean |
| `↑` `↓` / `j` `k` | move | `o` | reveal in Finder / file manager |
| `space` | select | `c` | copy path |
| `a` | select all **safe** items | `s` | sort: size, age, name, kind |
| `A` | select all safe **and review** items | `/` | filter |
| `g` / `G` | top / bottom | `r` | rescan |
| `PgUp` / `PgDn` | page | `q` / `Esc` | quit |

The **TOUCHED** column shows when a project's `package.json`, lockfile, `Cargo.toml`, etc. last changed. Anything older than 90 days is highlighted, since those are usually the best candidates to clean.

## Safety levels

| Level | Meaning | Examples |
|---|---|---|
| **safe** | Rebuilt automatically by your tools | `node_modules`, `.next`, Rust `target`, npm cache |
| **review** | Usually fine, but check first. Never picked by `a` | `dist`, `build`, Python venvs, Xcode Archives, Maven repo, old Node versions |
| **protected** | Shown, but cannot be deleted | your active / default Node version |

More guard rails:

- Generic folder names (`build`, `dist`, `target`, `vendor`, `bin`) only count when a matching project file sits next to them. `target` needs a `Cargo.toml` or `pom.xml`, for example; a random `build` folder is ignored.
- Python venvs are only recognized when they contain `pyvenv.cfg`.
- Symlinks are never followed or deleted through.
- Editor extension folders (`~/.vscode/extensions` and similar) are skipped, because their `node_modules` belong to installed extensions.
- Home, Documents, Library and other top-level folders are hard-blocked from deletion.
- Cache folders are **emptied**, not removed, so tools find them where they expect.

## What it finds

### Projects

| Ecosystem | Folders (marker file required next to them) |
|---|---|
| JavaScript / TS | `node_modules`, `.next`, `.nuxt`, `.output`, `.svelte-kit`, `.angular`, `.turbo`, `.nx`, `.parcel-cache`, `.docusaurus`, `.astro`, `.expo`, Gatsby `.cache`, `storybook-static`, `coverage`, `bower_components`, `dist`, `build` |
| Rust / JVM | `target` (Cargo, Maven, sbt), `.gradle`, Gradle `build`, `.cxx` |
| Python | `.venv` / `venv` / `env` (with `pyvenv.cfg`), `.tox`, `.nox`, `.pytest_cache`, `.mypy_cache`, `.ruff_cache` |
| Apple / mobile | `Pods`, SwiftPM `.build`, `.dart_tool`, Flutter `build` |
| Others | Composer `vendor`, `.terraform`, `.serverless`, `.aws-sam`, Elixir `_build` / `deps`, `.stack-work`, `dist-newstyle`, `elm-stuff`, Zig cache, `cmake-build-*`, .NET `obj` / `bin` |
| Game engines | Unity `Library`, Unreal `Intermediate` / `DerivedDataCache`, `.godot` |

### Global caches

| Group | Caches |
|---|---|
| JavaScript | npm, Yarn, pnpm store, Bun, Deno, node-gyp, TypeScript, Electron |
| Node versions | each version installed by nvm, fnm, Volta, asdf, mise (active one protected) |
| Test browsers | Cypress, Playwright, Puppeteer |
| Python | pip, uv, Poetry (cache and venvs), Conda packages |
| AI models | Hugging Face, Ollama |
| Rust / Go / JVM | Cargo registry, Go module + build cache, Gradle, Maven |
| Others | Composer, NuGet, Pub cache, Bazel, Android build cache, Homebrew, Docker |
| Xcode (macOS) | DerivedData, device support, archives, simulator caches, unavailable simulators, CocoaPods, SwiftPM / Carthage |
| Editors | VS Code, Cursor, Windsurf, VSCodium caches, JetBrains caches |

## Good to know

- **Sizes are real disk usage**, which is what you get back. pnpm hard-links packages from its global store, so deleting a pnpm `node_modules` frees less than shown.
- **macOS privacy:** if Desktop, Documents or Downloads seem to be missing, give your terminal *Full Disk Access* in System Settings → Privacy & Security.
- **macOS system Python** ships a 2004-era ncurses, so `byebytes` switches to plain ASCII symbols there automatically. Use a newer Python (e.g. Homebrew's), or force it with `--unicode`.
- **Windows:** `--list` and `--json` work. The interactive UI is untested there (it may work with `pip install windows-curses`).

## Contributing

All detection lives in two lists at the top of `byebytes.py`: `RULES` (project folders) and `cache_defs()` (global caches). Adding an ecosystem is usually one line. Pull requests are welcome.

## License

MIT
