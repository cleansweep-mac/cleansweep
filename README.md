# CleanSweep

CleanSweep is an opinionated, stripped-down fork of [Pearcleaner](https://github.com/alienator88/Pearcleaner) that emphasizes file safety, created when upstream development was put on hold. The name is a nod to [Quarterdeck](https://en.wikipedia.org/wiki/Quarterdeck_Office_Systems)'s [CleanSweep 95](https://en.wikipedia.org/wiki/Norton_CleanSweep).

## Requirements & Installation

### Requirements
- macOS 15.6 (Sequoia) or later
- Apple Silicon only
- Full Disk Access

### Installation
1. Download the latest release `.dmg` from the Releases page.
2. Drag `CleanSweep.app` into your `/Applications` directory.

---

## How It Works

When uninstalling an application, CleanSweep inspects the following locations:

🔍 `~/Library/` and `/Library/`, searched up to two levels deep

🔍 `~/.config/`

🔍 `~/Applications/` and `/Applications/`

🔍 Subfolders within `/var/folders/.../C` and `/var/folders/.../T`

🔍 Dot-folders in the user's home directory (e.g., `~/.docker`)

🔍 A Spotlight search within the user's Library and application directories ✳️

ℹ️ Matching files are determined by the application's bundle identifier, bundle ID variants, executable name, embedded bundle IDs, and team ID. Additonally, dedicated matching rules exist for specific applications.

ℹ️ For every operation, CleanSweep creates a timestamped folder inside the Trash to hold removed files, alongside an audit log detailing the original path of each item (`CleanSweep-Removal.json`). If this log cannot be created or written, CleanSweep halts the uninstallation.

✳️ When search sensitivity is set to Deep, Spotlight searches all of a user's home folder.

---

## Search Sensitivity

CleanSweep supports three search sensitivity levels. This can be configured in **Settings**.

| Search Level | Description |
|---|---|
| **Strict** *(Default)* | Restricts discovery to exact bundle IDs and direct signature matches. |
| **Enhanced** | Expands matching to include partial app names in application directories. |
| **Deep**  | Executes Spotlight queries across the user's entire home folder. |

---

## Safety Guidelines

⚠️ File operations bypass shell execution entirely. All filesystem actions are performed through native system APIs, preventing arbitrary command injection.

⚠️ Files requiring administrative privileges are moved using `/bin/mv`, invoked directly.

⚠️ User root directories (`~`, `~/Desktop`, `~/Documents`, `~/Downloads`) cannot be deleted as parent containers and only matched items inside them can be removed.

⚠️ Group and iCloud containers potentially shared among multiple apps are unchecked by default in the GUI and omitted entirely by the CLI.

⚠️ CleanSweep will never delete a shared directory unless every item inside belongs strictly to the targeted application.

⚠️ A directory is never removed if it contains any file marked as excluded.

⚠️ System paths such as `/usr/local`, `~/.config`, `~/Library/Preferences/ByHost` and system temporary roots are never removed.

⚠️ Symbolic links and aliases are never traversed.

⚠️ Under *Strict* and *Enhanced* modes, uninstalling an application will not remove shared preferences or caches belonging to sister apps (e.g., Google Chrome vs. Chrome Canary).

⚠️ Under *Strict* mode, internal helper process names are ignored during searches. Under *Enhanced* and *Deep* modes, bundled helper names will not match identically named binaries in `/usr/local`.

⚠️ Under *Strict* and *Enhanced* modes, name-matched files inside another developer's directory are only targeted if that directory is explicitly named after the app or its developer.

⚠️ In the GUI, unverified files returned via Spotlight in *Deep* mode remain unchecked by default.

---

## Command-Line Interface

CleanSweep provides a standalone CLI binary, `sweep`, that enforces the same safety guardrails and matching logic as the graphical interface.

### Commands

| Command | Action |
|---|---|
| `sweep list <path>` | Scans and lists all associated files without removing anything. |
| `sweep uninstall <path>` | Removes only the primary application bundle. |
| `sweep uninstall-all <path>` | Scans, presents matching files, prompts for confirmation, and removes all leftovers. |

### Additional Information
ℹ️ Passing `-y` or `--yes` bypasses confirmation. This is not recommended.

ℹ️ `sweep` will not run under `sudo`.

#### Note on Errors
If any item fails to remove, `sweep` prints the affected paths, logs the failure reason, and exits with a non-zero exit code. *Previously removed files are not rolled back.*

#### 🚨 Warning 🚨
❗ `sweep uninstall-all` automatically removes Spotlight-discovered items that the GUI leaves unchecked.

### CLI Examples

```bash
# View all matched files
sweep list /Applications/Example.app

# Remove an app and all matched files
sweep uninstall-all /Applications/Example.app

# Bypass confirmation
sweep uninstall -y /Applications/Example.app
```

---

## Features Removed

The following features were stripped:

🚫 App Bundling

🚫 App Lipo

🚫 App Translations Pruning

🚫 App Updater

🚫 App URL Scheme

🚫 Custom colors

🚫 Development Environment Manager

🚫 Dock drag-and-drop uninstallation

🚫 File Search

🚫 Finder "Open with" integration

🚫 Homebrew Manager

🚫 In-app Undo

🚫 Language translations and localization

🚫 Orphaned File Search

🚫 Package Manager

🚫 Plugin Manager

🚫 Privileged Helper Tool

🚫 Sentinel Monitor

🚫 Third-party runtime dependencies

🚫 Uninstallation History

---

## Things Of Note

✍🏻 CleanSweep does not use Finder to move files to Trash. Because of this, Finder's "Put Back" is not available.

✍🏻 CleanSweep does not use a privileged helper app. When removing protected files, a password is requested.

✍🏻 Deep sensitivity searches more aggressively and may match unassociated files. Carefully review search results.

---

## License

Apache 2.0 with [Commons Clause](https://commonsclause.com/). You can use, modify, and share this source code, but the license does not permit selling the software or a modified version of it. See [LICENSE.md](LICENSE.md) for the full text.

## Credits

- [Pearcleaner](https://github.com/alienator88/Pearcleaner) by [Alin Lupascu](https://github.com/alienator88)