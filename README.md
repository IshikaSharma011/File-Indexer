<div align="center">

# 🗂️ Findex

**A blazing-fast filesystem indexer and search CLI, built in Rust.**

Scan once. Search instantly. No cloud, no daemon, no dependencies.

[![Rust](https://img.shields.io/badge/built%20with-Rust-orange?style=flat-square&logo=rust)](https://www.rust-lang.org/)
[![SQLite](https://img.shields.io/badge/storage-SQLite-blue?style=flat-square&logo=sqlite)](https://sqlite.org/)
[![Platform](https://img.shields.io/badge/platform-Windows-0078d4?style=flat-square&logo=windows)](https://www.microsoft.com/windows)
[![License: MIT](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

</div>

---

**Findex** recursively walks your directories, stores every file's metadata into a local SQLite database, and lets you search across thousands of files by name or extension in under a millisecond — entirely offline, with a single binary.

```powershell
.\target\release\indexer.exe build C:\Users\YourName\Projects   # index a folder
.\target\release\indexer.exe search config                       # find files by name
.\target\release\indexer.exe ext rs                              # filter by extension
.\target\release\indexer.exe stats                               # explore your filesystem
```

---

## ✨ Features

- **Instant search** — queries hit a local SQLite index, not your disk
- **Extension filter** — `ext py` lists every Python file you've indexed
- **Rich stats** — total size, top extensions with bar chart, 5 largest files
- **Hidden-folder aware** — automatically skips `.git`, `.cache`, etc.
- **Upsert on re-scan** — re-run `build` to update changed files, nothing is lost
- **JSON output** — pipe-friendly with `--json` on every command
- **Zero runtime dependencies** — SQLite is bundled, one `.exe` to copy

---

## 🖥️ Prerequisites (Windows)

### 1. Install Rust

Go to **https://rustup.rs** → download and run `rustup-init.exe`

In the installer, press **1** (standard installation) and wait for it to finish (~2 min).

**Close and reopen VS Code after install**, then verify in the terminal:

```powershell
rustc --version
cargo --version
```

### 2. Install Visual Studio C++ Build Tools

Rust needs the MSVC linker. Go to:
**https://visualstudio.microsoft.com/visual-cpp-build-tools/**

Install the **"Desktop development with C++"** workload.

### 3. Install the VS Code extension

Open Extensions (`Ctrl+Shift+X`) → search **rust-analyzer** → Install.

---

## 📦 Installation

```powershell
# 1. Clone the repo
git clone https://github.com/yourusername/findex.git
cd findex

# 2. Build (first time takes ~3-5 min, then fast)
cargo build --release

# 3. Binary is ready at
.\target\release\indexer.exe
```

You'll see this when it's done:
```
Finished release [optimized] target(s) in 3m 12s
```

---

## 🚀 Usage

> **Tip:** Open the VS Code terminal with **Ctrl + `** (backtick key, top-left of keyboard)

---

### `build` — Index a directory

```powershell
# Index Documents
.\target\release\indexer.exe build C:\Users\YourName\Documents

# Use a custom database file
.\target\release\indexer.exe build C:\Users\YourName\Projects --db C:\myindex.db

# Wipe and re-scan from scratch
.\target\release\indexer.exe build C:\Users\YourName\Projects --clean

# Use more CPU threads
.\target\release\indexer.exe build C:\Users\YourName\Projects --threads 8
```

**Output:**
```
  ╔══════════════════════════════════╗
  ║   📁  File System Indexer v1.0  ║
  ╚══════════════════════════════════╝

  Building index from: C:\Users\YourName\Projects
  Database: index.db
  Threads: 4

  ⟳ Scanning filesystem…

  ✓ Found 1,842 files in 203 directories (38 ms)

  ⠸ [00:00:02] [████████████████████░░░░░░░░░░░░░░░░░░░░] 1492/1842 (00:00:01)

  ✓ Indexed 1,842 files  (DB size: 512.00 kB)

  Run `indexer stats`  to explore the index.
```

---

### `search` — Find files by keyword

```powershell
# Search by filename
.\target\release\indexer.exe search config

# Show more results
.\target\release\indexer.exe search config --limit 50

# JSON output
.\target\release\indexer.exe search readme --json

# Use a specific database
.\target\release\indexer.exe search main --db C:\myindex.db
```

**Output:**
```
 🔍  keyword search for 'config' — 4 results (0 ms)

  #      FILENAME                          EXT       SIZE          PATH
  ────────────────────────────────────────────────────────────────────────────────────────────────────
     1.  config.toml                       .toml          2.10 kB  C:\Users\YourName\app\config.toml
     2.  config.yaml                       .yaml          1.40 kB  C:\Users\YourName\api\config.yaml
     3.  config.json                       .json          3.80 kB  C:\Users\YourName\web\config.json
     4.  database_config.py                .py            1.10 kB  C:\Users\YourName\db\database_config.py
  ────────────────────────────────────────────────────────────────────────────────────────────────────
  → 4 files matched
```

---

### `ext` — Filter by file extension

```powershell
# Find all Rust files
.\target\release\indexer.exe ext rs

# Find all Python files
.\target\release\indexer.exe ext py --limit 100

# Find all JSON files
.\target\release\indexer.exe ext json

# Find all text files
.\target\release\indexer.exe ext txt

# JSON output
.\target\release\indexer.exe ext rs --json
```

**Output:**
```
 🔍  extension search for 'rs' — 6 results (0 ms)

  #      FILENAME                          EXT       SIZE          PATH
  ────────────────────────────────────────────────────────────────────────────────────────────────────
     1.  cli.rs                            .rs            2.89 kB  C:\Users\YourName\findex\src\cli.rs
     2.  database.rs                       .rs           10.60 kB  C:\Users\YourName\findex\src\database.rs
     3.  main.rs                           .rs           10.99 kB  C:\Users\YourName\findex\src\main.rs
     4.  models.rs                         .rs            2.12 kB  C:\Users\YourName\findex\src\models.rs
     5.  scanner.rs                        .rs            6.11 kB  C:\Users\YourName\findex\src\scanner.rs
     6.  search.rs                         .rs            4.70 kB  C:\Users\YourName\findex\src\search.rs
  ────────────────────────────────────────────────────────────────────────────────────────────────────
  → 6 files matched
```

---

### `stats` — Explore index statistics

```powershell
# Full stats report
.\target\release\indexer.exe stats

# Show top 15 extensions
.\target\release\indexer.exe stats --top 15

# JSON output
.\target\release\indexer.exe stats --json

# Stats from a specific database
.\target\release\indexer.exe stats --db C:\myindex.db
```

**Output:**
```
  Index Statistics

  Summary
  Total files .................. 1,842
  Total size ................... 248.30 MB
  Unique extensions ............ 37
  Last indexed ................. 2026-07-01 05:08:54 UTC
  Database path ................ index.db
  Database size ................ 512.00 kB

  Top 10 Extensions by Count
  #      EXT          FILES      TOTAL SIZE
  ──────────────────────────────────────────────────
     1.  .rs            312       18.40 MB  ████████████████████
     2.  .toml           84        1.20 MB  █████
     3.  .md             71        3.80 MB  ████
     4.  .json           60        9.10 MB  ███
     5.  .py             45        2.30 MB  ██

  Top 5 Largest Files
  #      FILENAME                     SIZE         PATH
  ──────────────────────────────────────────────────────────────────────────
     1.  archive.bin             102.40 MB  C:\Users\YourName\data\archive.bin
     2.  database_dump.sql        48.20 MB  C:\Users\YourName\backups\dump.sql
```

---

## ⚙️ Options reference

### Global

| Flag | Default | Description |
|------|---------|-------------|
| `--db <PATH>` | `index.db` | Path to the SQLite database file |
| `-h, --help` | — | Print help |
| `-V, --version` | — | Print version |

### Per-command

| Command | Flag | Default | Description |
|---------|------|---------|-------------|
| `build` | `--clean, -c` | false | Wipe index before scanning |
| `build` | `--threads, -j` | 4 | Worker threads |
| `search` | `--limit, -n` | 20 | Max results to show |
| `search` | `--json` | false | Output as JSON |
| `ext` | `--limit, -n` | 20 | Max results to show |
| `ext` | `--json` | false | Output as JSON |
| `stats` | `--top` | 10 | Top N extensions to show |
| `stats` | `--json` | false | Output as JSON |

---

## 💡 Pro tip — use `cargo run` during development

While working on the project, skip typing the full `.exe` path by using:

```powershell
cargo run --release -- build C:\Users\YourName\Documents
cargo run --release -- search config
cargo run --release -- ext rs
cargo run --release -- stats
```

The `--` separates Cargo's own flags from your program's arguments.

---

## 🏗️ Project structure

```
findex/
├── Cargo.toml
└── src/
    ├── main.rs       — Entry point, command dispatch, output rendering
    ├── cli.rs        — Clap CLI definitions (all commands and flags)
    ├── models.rs     — Core structs: FileEntry, IndexStats, SearchResult
    ├── scanner.rs    — Async recursive directory walk (skips hidden dirs)
    ├── database.rs   — SQLite layer: open, batch upsert, search, stats queries
    └── search.rs     — Search logic and colored table renderer
```

---

## 🔧 Tech stack

| Crate | Purpose |
|-------|---------|
| [`tokio`](https://tokio.rs) | Async runtime for concurrent scan + insert |
| [`walkdir`](https://docs.rs/walkdir) | Recursive directory traversal |
| [`rusqlite`](https://docs.rs/rusqlite) | SQLite (bundled — no system install needed) |
| [`clap`](https://docs.rs/clap) | CLI argument parsing and help generation |
| [`serde`](https://serde.rs) | JSON serialization for `--json` output |
| [`anyhow`](https://docs.rs/anyhow) | Ergonomic error handling |
| [`indicatif`](https://docs.rs/indicatif) | Progress bars and spinners |
| [`colored`](https://docs.rs/colored) | Terminal color output |
| [`humansize`](https://docs.rs/humansize) | Human-readable file sizes |
| [`chrono`](https://docs.rs/chrono) | Timestamps |

---

## 🐛 Troubleshooting

**`'cargo' is not recognized`**
Close and reopen VS Code after installing Rust. It adds itself to PATH but VS Code needs a restart to pick it up.

**`linking with link.exe failed`**
Install the Visual Studio C++ Build Tools: https://visualstudio.microsoft.com/visual-cpp-build-tools/
Select the **"Desktop development with C++"** workload.

**`can't find the project / wrong folder`**
Make sure the VS Code terminal is inside the `findex` folder. Run:
```powershell
cd C:\path\to\findex
```

**`Couldn't open database`**
The folder in your `--db` path must exist first. If you use `--db C:\data\myindex.db`, create the `C:\data\` folder manually before running.

---

## 📄 License

MIT — see [LICENSE](LICENSE).
