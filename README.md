<!-- Language switcher. Keep this row identical across every README.<lang>.md. -->
<p align="center">
  <b>English</b> &nbsp;·&nbsp;
  <a href="README.zh-CN.md">简体中文</a> &nbsp;·&nbsp;
  <a href="README.es.md">Español</a> &nbsp;·&nbsp;
  <a href="README.ja.md">日本語</a> &nbsp;·&nbsp;
  <a href="README.fr.md">Français</a> &nbsp;·&nbsp;
  <a href="README.de.md">Deutsch</a>
</p>

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/hero-dark.gif">
    <source media="(prefers-color-scheme: light)" srcset="assets/hero-light.gif">
    <img alt="Sleepless: keep your Mac awake with the lid closed" src="assets/hero-light.gif" width="780">
  </picture>
</p>

<p align="center">
  <b>Sleepless keeps your MacBook awake with the lid closed, on battery, with no external display.</b><br>
  <sub>One native menu-bar switch, with an auto-off at a battery level you choose so you never drain it flat.</sub>
</p>

<p align="center">
  <a href="https://github.com/Aboudjem/Sleepless/actions/workflows/ci.yml"><img alt="CI" src="https://img.shields.io/github/actions/workflow/status/Aboudjem/Sleepless/ci.yml?branch=main&label=CI&logo=githubactions&logoColor=white"></a>
  <a href="https://github.com/Aboudjem/Sleepless/releases/latest"><img alt="Release" src="https://img.shields.io/github/v/release/Aboudjem/Sleepless?label=release&logo=apple&logoColor=white"></a>
  <a href="https://github.com/Aboudjem/Sleepless/releases"><img alt="Downloads" src="https://img.shields.io/github/downloads/Aboudjem/Sleepless/total?label=downloads"></a>
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/github/license/Aboudjem/Sleepless?color=blue"></a>
  <img alt="Platform" src="https://img.shields.io/badge/macOS-26%20·%20Apple%20Silicon-7c5cf0?logo=apple&logoColor=white">
  <a href="https://github.com/Aboudjem/Sleepless/stargazers"><img alt="Stars" src="https://img.shields.io/github/stars/Aboudjem/Sleepless?style=social"></a>
</p>

<p align="center">
  <img alt="Sleepless demo: flip the switch, drag the battery-floor slider" src="assets/demo.gif" width="760">
</p>

> [!NOTE]
> Closing the lid normally sleeps your Mac, and `caffeinate`-based apps (KeepingYouAwake and friends) **cannot** change that. Sleepless flips the one setting that can, `pmset disablesleep`, then guards it with a battery-floor auto-off so it is safe to forget.

## What you get

|  |  |
|---|---|
| 🌙 **One switch** | Click the moon in the menu bar, flip the toggle. The glyph shows the state at a glance. |
| 🔋 **Battery-floor auto-off** | Pick a floor (5–50%, default 15%). On battery, it turns itself off before you run flat. |
| 🖥️ **No display, no dongle** | Just the lid closed, on battery. No external monitor, no dummy HDMI plug. |
| 🪶 **Tiny and native** | AppKit + SF Symbols. No Dock icon, no background daemon, no kext, no dependencies. |

## Install

**Homebrew** (recommended):

```sh
brew install --cask aboudjem/tap/sleepless
# one-time: add the passwordless grant (it prints exactly what it writes first)
/Applications/Sleepless.app/Contents/Resources/grant.sh
```

**Build from source** (the trust path: read it, build it, no Gatekeeper prompt):

```sh
git clone https://github.com/Aboudjem/Sleepless.git
cd Sleepless && ./install.sh
```

**Or download the app:** grab the [latest release](https://github.com/Aboudjem/Sleepless/releases/latest), unzip, and move `Sleepless.app` to `/Applications`. It is ad-hoc signed, so approve the first launch in **System Settings → Privacy & Security → Open Anyway** (the old right-click → Open trick was removed in macOS 15).

Then click the moon, flip the switch, and close the lid. `./uninstall.sh` removes everything and proves the grant is gone.

## How it works

`caffeinate` and the power assertions it uses can't override the hardware lid-close trigger, so a closed lid always sleeps the Mac. The one system setting that overrides it is `pmset disablesleep`. Sleepless toggles it from a native switch, reads the live value back so the UI never lies, and auto-reverts at your battery floor. A reboot also resets it. [Full security model →](SECURITY.md)

## Sleepless vs the alternatives

| | **Sleepless** | Amphetamine | KeepingYouAwake | Macchiato | Clapet | `caffeinate` |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Awake, lid closed, on battery | ✅ | ✅¹ | ❌ | ✅ | ⚠️ ext. display | ❌ |
| No external display needed | ✅ | ✅ | n/a | ✅ | ❌ | n/a |
| Battery-floor auto-off | ✅ | low-batt only | ✅² | ❌ | ❌ | ❌ |
| Open source | ✅ MIT | ❌ App Store | ✅ MIT | ✅ Apache | ✅ GPL | Apple |

<sub>¹ Amphetamine does it, but on Apple Silicon relies on a separate "Power Protect" script and is widely reported to break on power/dock changes. &nbsp; ² KeepingYouAwake has a battery cutoff but, by design, cannot stay awake with the lid closed. Star counts (≈6.6k / ≈18 / ≈101) pulled 2026-06-01; corrections welcome.</sub>

## Use it to…

- 🤖 **Finish a long job after you walk away.** An AI agent run, a build, a render, ML training, a big `brew`/`npm` install: switch it on, close the lid, drop it in your bag, come back to a finished job.
- 📡 **Share your hotspot on the move.** Internet Sharing / Personal Hotspot from the Mac keeps serving with the lid shut.
- ⬇️ **Leave big transfers running.** Large downloads, uploads, or a Time Machine backup complete while you step away.
- 🖥️ **Keep a server or SSH session alive.** A local dev server, a sync daemon, or a remote session stays reachable, lid closed.
- 🎧 **Keep audio going.** Music or a long render keeps playing in the bag.

> [!TIP]
> Set the battery floor to a level you trust (say 20%) and you can do all of the above without babysitting the battery.

## Is it safe?

Yes, and it is built to be auditable. A GUI app can't type a password, so the installer adds a tightly scoped `/etc/sudoers.d` rule (root-owned, `0440`) that permits **exactly two commands and nothing else**:

```
<you> ALL=(root) NOPASSWD: /usr/bin/pmset -a disablesleep 0, /usr/bin/pmset -a disablesleep 1
```

- **It can't be widened.** `sudoers` matches arguments literally with no wildcards, so any other command re-prompts for a password.
- **No daemon, no helper script** for an attacker to hijack. It calls Apple's `/usr/bin/pmset` directly with an argv array (no shell).
- **Always reversible.** A reboot resets the flag, the battery floor turns it off, and `./uninstall.sh` removes the grant and proves it.

The full threat model, the undocumented-but-real `disablesleep` evidence, and the notarization tradeoff are in **[SECURITY.md](SECURITY.md)**.

## FAQ

<details>
<summary><b>Does it really work lid-closed, on battery, with no display?</b></summary>

Yes, that is the whole point. Verified on macOS 26 (Tahoe) / Apple Silicon.
</details>

<details>
<summary><b>The moon doesn't show in my menu bar.</b></summary>

macOS 26 can hide menu-bar items. Check System Settings (Control Center / Menu Bar) and allow Sleepless to show its item. Confirm it is running with <code>pgrep -x Sleepless</code>.
</details>

<details>
<summary><b>Why isn't it notarized?</b></summary>

It is a personal, open-source tool with no paid Apple Developer ID, so it is ad-hoc signed. Build from source to skip Gatekeeper entirely, or use the <b>Open Anyway</b> flow for the prebuilt app.
</details>

<details>
<summary><b>Will it drain my battery?</b></summary>

Only if you ignore the floor. While awake and discharging it turns off at the percentage you set (default 15%), and a reboot always restores normal sleep.
</details>

<details>
<summary><b>Does it work on Intel or older macOS?</b></summary>

It is verified on <b>macOS 26 Apple Silicon</b>. <code>disablesleep</code> is undocumented, so other versions or hardware aren't guaranteed. Try it and report back, honest reports are welcome.
</details>

## Contributing

Issues and PRs welcome, especially translations and test reports from other hardware. See [CONTRIBUTING.md](CONTRIBUTING.md) and the [Code of Conduct](CODE_OF_CONDUCT.md). Sleepless stays deliberately small.

## License

[MIT](LICENSE) © 2026 Adam Boudjemaa.

<p align="center">
  <sub>If Sleepless saved you a trip to Terminal, a ⭐ helps other people find it.</sub>
</p>
