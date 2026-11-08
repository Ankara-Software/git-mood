Maintained by [Ankara Yazılım](https://github.com/Ankara-Software) | [Project page](https://ankarayazilim.org/acik-kaynak/git-mood)

**Status: stable.** It builds, the 2 unit tests pass and `init`, the commit hook, `status` and `sync` were run by hand in a temporary repository. The mood rules are simple keyword checks, see [How the mood is chosen](#how-the-mood-is-chosen).

# git-mood

> Color your GitHub contribution graph with the mood of your git commits.

`git-mood` is a local command line tool written in Rust. It installs a git `commit-msg` hook that analyzes the commit message and the staged diff stats, and records your daily coding "mood" in a local JSON file. From that file it generates a contribution graph SVG (turquoise, red, yellow, purple and green) that you can embed in your GitHub Profile README.

## Türkçe

git-mood, commit mesajlarınızın ve değişiklik istatistiklerinin "ruh halini" yerel olarak kaydeden bir Rust komut satırı aracıdır. `git-mood init` ile bir `commit-msg` kancası kurar; her commit'te mesaj ve staged diff analiz edilir, sonuç `~/.git-mood.json` dosyasına yazılır. `git-mood status` son 7 günü terminalde gösterir, `git-mood sync` ise GitHub profil README'nize ekleyebileceğiniz bir SVG katkı grafiği üretir. Commit sırasında ağ isteği yapılmaz; `sync` komutu SVG'yi commit'leyip `git push` çalıştırır.

---

## The Mood Map

| Color | Mood | Rule |
| :--- | :--- | :--- |
| Turquoise | Productive and happy | Message contains a positive keyword (`feat`, `add`, `improve`, `perf`, `docs`, `fixed`, `awesome`, `success` and others) |
| Red | Bug-fixing or stressed | Message contains a negative keyword (`bug`, `fail`, `error`, `crash`, `broken`, `issue`, swear words and others) |
| Yellow | Cleanups and refactoring | Message contains `refactor`, `cleanup`, `simplify`, `remove` or `delete`, or deletions are at least 70% of a diff with more than 10 changed lines |
| Purple | Weekend warrior | Commit made on Saturday or Sunday with a non-empty diff |
| Green | Steady progress | Anything else |

### How the mood is chosen

Rules are checked in this order and the first match wins: red, yellow, purple, turquoise, green. So on a weekend a commit with a positive message is purple, and a message with "bug" is red even if it fixes the bug. Keywords are matched as plain substrings of the lower-cased message, so a word that merely contains one (for example "hello" contains "hell") also matches. The mood of a day is derived from all commits recorded for that day.

---

## Features

- **Local only**: no external APIs, no trackers and no network requests during a commit. The data lives in `~/.git-mood.json` (`%USERPROFILE%` on Windows).
- **Diff aware**: staged additions and deletions are counted to detect refactoring commits.
- **Dark SVG widget**: `git-mood sync` writes an SVG in a dark GitHub-style theme.
- **Single binary**: no runtime to install.

---

## Installation

Download the binary for your system from the [latest release](https://github.com/Ankara-Software/git-mood/releases/latest):

- macOS (Apple Silicon): [git-mood-darwin-arm64](https://github.com/Ankara-Software/git-mood/releases/latest/download/git-mood-darwin-arm64)
- macOS (Intel): [git-mood-darwin-x64](https://github.com/Ankara-Software/git-mood/releases/latest/download/git-mood-darwin-x64)
- Linux (x64): [git-mood-linux-x64](https://github.com/Ankara-Software/git-mood/releases/latest/download/git-mood-linux-x64)
- Linux (ARM64): [git-mood-linux-arm64](https://github.com/Ankara-Software/git-mood/releases/latest/download/git-mood-linux-arm64)
- Windows (x64): [git-mood-win32-x64.exe](https://github.com/Ankara-Software/git-mood/releases/latest/download/git-mood-win32-x64.exe)

Move the downloaded binary to a folder in your `$PATH` (for example `/usr/local/bin`), rename it to `git-mood` and make it executable.

Or build from source (Rust toolchain required):
```bash
cargo install --git https://github.com/Ankara-Software/git-mood
```

---

## Usage

### 1. Initialize the hook in your project
Inside a git repository:
```bash
git-mood init
```
An existing `commit-msg` hook is copied to `commit-msg.backup` first. The hook calls the binary by the absolute path it had when you ran `init`.

### 2. Write code and commit
```bash
git commit -m "feat: add dashboard ui"

# Output:
# [git-mood] Sentiment Analyzed: Positive (Turquoise)
#            Diff: +142 / -12 lines
```

### 3. Check your mood dashboard
```bash
git-mood status
```
Shows the last 7 days and the number of days per mood.

### 4. Sync to your GitHub Profile
1. Clone your GitHub Profile Repository (the repo named like your username, for example `github.com/username/username`).
2. Run `git-mood sync` inside that repository:
   ```bash
   git-mood sync
   ```
   This writes `git-mood.svg`, commits it with `--no-verify` and runs `git push` on the current branch. If there is no remote or the push fails, it tells you to push manually.
3. Add the following line to your Profile `README.md`:
   ```markdown
   ![git-mood](https://raw.githubusercontent.com/<your-username>/<your-username>/main/git-mood.svg)
   ```

---

## Development

```bash
cargo build
cargo test
```

See [CONTRIBUTING.md](CONTRIBUTING.md), [SECURITY.md](SECURITY.md) and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

## License

MIT. See [LICENSE](LICENSE).
