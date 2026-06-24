<!--
  GENERATED EXPORT — do not edit directly.
  Canonical source: index.html
  This Markdown is derived from the HTML to match it exactly. Edit the HTML,
  then regenerate this file. Hand-edits here will be overwritten.
-->

# Reviving an Intel Mac as a Modern Linux Machine

> **Document:** Reviving an Intel Mac as a Modern Linux Machine
> **Version:** 1.2 · **Updated:** 2026-06-24
> **Format:** Markdown export · generated from the canonical HTML edition (`intel-mac-linux-guide.html`)
> **Changelog:** v1.2 added a (gentle) Windows footnote in Phase 2b. v1.1 added Ubuntu/flavors & Mint T2 options and fuller non-T2 driver guidance. v1.0 initial release (combined overview + Fedora walkthrough).

*Got an Intel Mac that macOS has left behind? Linux can give it years more life. This is the map — and one path on it comes with a full, validated walkthrough.*

---

## Who this is for

This guide covers Intel Macs from about **2015 through 2020**.

**If your Mac is Apple Silicon (M1, M2, M3, or M4), stop here.** This guide doesn't apply. The people to see are the **Asahi Linux** project (https://asahilinux.org), who have done remarkable work getting Linux running on Apple Silicon. Different hardware, different rules.

To check: Apple menu → **About This Mac**. If it lists a "Chip" like "Apple M1," that's Apple Silicon. If it lists an Intel "Processor," you're in the right place.

---

## The fork: T2 or not — that's the whole question

Every Intel Mac in scope falls into one of two camps, and which camp yours is in sets the difficulty:

- **Non-T2 Macs (roughly 2015–2017)** behave like ordinary PCs. Boot a normal Linux installer and go.
- **T2 Macs (2018–2020)** have a security chip that locks booting and needs special drivers for the keyboard, trackpad, audio, and Wi-Fi. Doable, but it's a project — and it's the one with the full walkthrough below.

### Which one is yours?

Per Apple's official list, these are the **T2** models:

| Model | T2 chip? |
|---|---|
| MacBook Pro 2018–2020 *(except 13″ M1, 2020)* | Yes → Track B |
| MacBook Air 2018–2020 *(except M1, 2020)* | Yes → Track B |
| iMac (Retina 5K, 27″, 2020) and iMac Pro (2017) | Yes → Track B |
| Mac mini 2018 | Yes → Track B |
| Mac Pro 2019 | Yes → Track B |
| **Any other Intel Mac (2015, 2016, 2017)** | No → Track A |

**Or ask the machine directly:** hold **Option** while choosing Apple menu → **System Information**. In the sidebar, click **Controller** or **iBridge**. If it shows "Apple T2 chip," you have a T2 Mac.

**One wrinkle:** the 2016–2017 MacBook Pro with Touch Bar has an earlier **T1** chip, not a T2. The T1 only runs the Touch Bar and Touch ID; it does **not** lock booting. So those install the easy way (Track A) — the Touch Bar just won't work without extra effort, which most people happily skip.

---

## Track A — Non-T2 Macs (2015–2017): the easy path

To Linux, these are basically normal Intel laptops. No Secure Boot to disable, no patched kernel. The whole process:

1. **Back up anything you care about.** Installing wipes the drive.
2. **Pick a distro and download its standard ISO.** Good choices: **Fedora Workstation** or **Ubuntu** (modern, GNOME); **Linux Mint** or **Pop!_OS** (especially friendly for newcomers). Mint's **XFCE** edition is the lightest, ideal for the oldest or lowest-RAM machines.
3. **Make a USB installer** with [Etcher](https://www.balena.io/etcher/), Rufus (Windows), or `dd`.
4. **Boot it:** hold **Option (⌥)** at startup, pick the **EFI Boot** USB icon, run the installer, and choose to **erase the disk**.
5. **Fix the two usual quirks afterward** (below).

### Wi-Fi (the most common one)

Many Macs use Broadcom Wi-Fi chips that need a proprietary driver. Have a wired or USB-tethered connection for the first boot, then install it.

```bash
# Fedora — install Broadcom driver
sudo dnf install akmod-wl
```

```bash
# Ubuntu / Mint — install Broadcom driver
sudo apt install bcmwl-kernel-source
```

On **Ubuntu and Mint** the friendliest route is to let the installer do it: tick **"Install third-party software for graphics and Wi-Fi hardware"** during setup and it usually handles the Broadcom driver for you. If you skipped that, run it afterward from a GUI — **Driver Manager** on Mint, or **Additional Drivers** (inside Software & Updates) on Ubuntu — and pick the Broadcom `wl` driver it offers. The command above is the manual equivalent if you'd rather use the terminal.

> **Flag:** The exact package depends on your specific Broadcom chip. A few older Macs use a chip served by `b43` / `firmware-b43-installer` instead of `wl`. If one doesn't connect, search your model plus "Broadcom Linux" for the right one.

### Graphics

Intel and AMD graphics generally just work. The 15-inch MacBook Pros with a discrete GPU can need extra attention; the 13-inch and Air models with Intel-only graphics are the simplest. If a dual-GPU machine misbehaves, disabling the discrete GPU is a common fix. And if the Mac won't boot the installed system, the [rEFInd](https://wiki.t2linux.org/guides/refind/) boot manager is the standard remedy.

> **What works out of the box:** Keyboard, trackpad, webcam, audio, and sleep are generally all fine on these. Wi-Fi drivers are the main hurdle, and a minor one. This track is genuinely approachable for a first-time Linux installer.

---

## Track B — T2 Macs (2018–2020): a Fedora walkthrough

The T2 chip blocks non-Apple operating systems from booting and needs special drivers (via `apple-bce`) before the keyboard, trackpad, audio, and Wi-Fi work. You can't just flash a stock ISO and expect working hardware. The smoothest distro on T2 is **Fedora**, because the [t2linux](https://wiki.t2linux.org) project ships a prebuilt T2 ISO with the patched kernel baked in and Wi-Fi working on first boot.

> **⚠ CRITICAL — this erases everything.** This wipes the entire internal SSD. macOS, your files, and settings are permanently destroyed. Back up first. Unlike some T2 routes, you do **not** need to extract Wi-Fi firmware beforehand — the T2 Fedora ISO ships it.

### The honest hardware ceiling (true on any distro)

- **Trackpad** works but lacks force touch and palm rejection, so it won't feel fully macOS-grade.
- **Audio** works but can be quirky switching between speakers and mic.
- **Bluetooth** glitches on 2.4 GHz Wi-Fi — use 5 GHz, or turn Wi-Fi off while using it.
- **Deep sleep** needs the workaround in Phase 6.
- **Touch ID** does not work on Linux.

Integrated-graphics models (the Airs and 13-inch Pros) are the friendliest T2 machines, since they avoid the discrete-GPU complications.

### Phase 1 · Disable Secure Boot

The T2 chip blocks non-Apple operating systems and external boot media by default. Turn that off in Recovery.

1. Shut the Mac down completely.
2. Power on and immediately hold **Command (⌘) + R** until the Apple logo or spinning globe appears.
3. Pick your admin account and enter your password.
4. Menu bar → **Utilities** → **Startup Security Utility**. Authenticate again if asked.
5. Set **Secure Boot** to **No Security**.
6. Set **Allowed Boot Media** to **Allow booting from external or removable media**.
7. Close the utility, then  → **Shut Down**.

### Phase 2 · Get & flash the T2 Fedora ISO

**2a · Download the right files.** Go to the [t2linux Fedora releases page](https://github.com/t2linux/fedora-iso/releases) and use the newest release marked **Latest**. Skip any release flagged as broken in its notes. Choose **Workstation** (GNOME) — the KDE build is a different desktop you can ignore.

> **Heads-up · the ISO comes in two parts.** GitHub limits each release file to 2 GB, and the live ISO is larger than that (Workstation is ~2.66 GB). So it's split into a `.iso.00` part and a `.iso.01` part. **These are not two different ISOs to choose between. They are two halves of one file, and you need both.** You rejoin them into one `.iso` before flashing.

For Workstation, download all three: `…iso.00`, `…iso.01`, and `…iso.sha256` (the checksum).

> **Prefer no command line?** The [t2linux Installer app](https://github.com/sharpenedblade/t2linux-installer/releases/latest) downloads and rejoins the ISO for you automatically. Use it and skip to Phase 3.

**2b · Rejoin the two parts.** Put both parts in the same folder, open a terminal there, and concatenate them in order (`.00` first). Substitute the actual version you downloaded.

```bash
# macOS or Linux
cat Fedora-t2linux-Workstation-Live-*.iso.00 \
    Fedora-t2linux-Workstation-Live-*.iso.01 \
    > Fedora-t2linux-Workstation-Live.x86_64.iso
```

```bat
:: Windows — Command Prompt, not PowerShell †
copy /b Fedora-t2linux-Workstation-Live-44.0.x86_64.iso.00 + Fedora-t2linux-Workstation-Live-44.0.x86_64.iso.01 Fedora-t2linux-Workstation-Live-44.0.x86_64.iso
```

> **† For the PC doing the prep work.** You went and got a Mac — and yet here you are, back at the Windows box to handle the fussy bit. Two house rules over here: it's Command Prompt, *not* PowerShell (PowerShell greets `copy /b` with the bureaucratic equivalent of a raised eyebrow), and you'll type the version number out by hand, since the breezy `*` wildcard the macOS and Linux folks get to enjoy didn't ship in this edition. You're nearly a Linux user now, though. We're proud of you.

**2c · Verify the rejoined ISO.** The `.sha256` is the checksum of the **complete, rejoined** ISO. Check it before flashing — a mismatch means a broken installer.

```bash
# macOS — compare the two hashes by eye
shasum -a 256 Fedora-t2linux-Workstation-Live*.iso
cat Fedora-t2linux-Workstation-Live-*.iso.sha256
```

```bash
# Linux — auto-checks and prints OK
sha256sum -c Fedora-t2linux-Workstation-Live-*.iso.sha256
```

**2d · Flash to USB.** Flash the **rejoined `.iso`**, not the parts. Use [Etcher](https://www.balena.io/etcher/) / [USBImager](https://gitlab.com/bztsrc/usbimager/), or `dd` on macOS:

```bash
# macOS — dd (replace diskX with your USB)
diskutil list
sudo diskutil unmountDisk /dev/diskX
sudo dd if=/path/to/fedora-t2.iso of=/dev/rdiskX bs=1m
```

> *Confidence: high. The split-and-rejoin mechanism follows from the `.00`/`.01` naming and sizes against GitHub's 2 GB per-file limit. The wiki doesn't document the join, which is exactly why the checksum step is here.*

### Phase 3 · Boot the installer

1. Plug the installer USB (and your USB-C hub) into the Mac.
2. Power on while holding **Option (⌥)** to reach the macOS Startup Manager.
3. Select the orange **EFI Boot** icon and press Return. If two appear, try the rightmost first.
4. At the GRUB menu, choose the default boot/install option and press Enter.

> **If boot stalls before the desktop:** First suspect the **USB-C adapter** — a flaky one is the most common cause; swap it. If it's a GRUB/NVRAM write failure, highlight the boot entry, press **e**, add `efi=noruntime` to the kernel line, and press **F10** to boot.

### Phase 4 · Install Fedora (full wipe)

Fedora's installer (Anaconda) is a little less hand-holding at the disk step, and its UI shifts between releases, so this describes the goal rather than exact labels.

1. Launch the installer; set language and keyboard.
2. At **Installation Destination**, select the internal NVMe disk. Choose the option that **erases all existing partitions / uses the entire disk**, and let Fedora auto-partition (Btrfs is the sensible default).
3. Want encryption? Enable **LUKS** here.
4. Begin install, set your user account, and when it finishes, reboot and remove the USB. Hold **Option** → **EFI Boot** if it doesn't boot straight in.

> *Confidence: high on the approach; medium on exact installer wording. If a label doesn't match, look for the equivalent "use entire disk / erase everything" choice.*

### Phase 5 · First-boot checks

- **Wi-Fi** should appear and connect. If it somehow doesn't, follow the [Wi-Fi guide](https://wiki.t2linux.org/guides/wifi-bluetooth/) (after a full wipe that means the "download a macOS recovery image" method).
- **Audio** — test speakers and headphones; expect occasional quirks.
- **Bluetooth** — keep Wi-Fi on 5 GHz to avoid the 2.4 GHz glitch.
- **Update** everything:

```bash
# Fedora Terminal
sudo dnf upgrade --refresh
```

### Phase 6 · Make deep sleep reliable (optional)

S3 suspend has been broken at the driver level since macOS Sonoma, but Fedora supports the documented workaround: a small service that unloads `apple-bce` before sleep and reloads it after.

First confirm the kernel allows force-unloading modules (you want `=y`), and check where the tools live:

```bash
# Fedora Terminal
grep "CONFIG_MODULE_FORCE_UNLOAD" /boot/config-$(uname -r)
which modprobe
which rmmod
```

Create `/etc/systemd/system/suspend-fix-t2.service` with this content (adjust the paths if `which` showed something other than `/usr/sbin`):

```ini
[Unit]
Description=Disable and Re-Enable Apple BCE Module (and Wi-Fi)
Before=sleep.target
StopWhenUnneeded=yes

[Service]
User=root
Type=oneshot
RemainAfterExit=yes

#ExecStart=/usr/sbin/modprobe -r brcmfmac_wcc
#ExecStart=/usr/sbin/modprobe -r brcmfmac
ExecStart=/usr/sbin/rmmod -f apple-bce

ExecStop=/usr/sbin/modprobe apple-bce
#ExecStop=/usr/sbin/modprobe brcmfmac
#ExecStop=/usr/sbin/modprobe brcmfmac_wcc

[Install]
WantedBy=sleep.target
```

Enable it, then test by suspending and resuming:

```bash
# Fedora Terminal
sudo systemctl enable suspend-fix-t2.service
```

If Wi-Fi is dead after resume, edit the file, uncomment the four `brcmfmac` lines, and reboot.

> *Confidence: high. From the t2linux Basic Setup guide, which lists this workaround as working on Fedora. Verify the binary paths with the `which` commands above.*

### Other T2 distros: Ubuntu, its flavors & Mint

Fedora is this guide's pick because it's the smoothest on T2, but it isn't the only supported option. The t2linux project also ships prebuilt T2 ISOs for **Ubuntu** and its flavors (Kubuntu, Ubuntu Budgie, MATE, Cinnamon, Unity, Xubuntu) and for **Linux Mint**. If you'd rather run one of those, you can.

- **Ubuntu and flavors:** [T2-Ubuntu releases](https://github.com/t2linux/T2-Ubuntu/releases/latest)
- **Linux Mint:** [T2-Mint releases](https://github.com/t2linux/T2-Mint/releases/latest)

Wi-Fi and Bluetooth usually work after install, with a `get-apple-firmware` fallback if not. But go in knowing the three ways these are rougher than Fedora on T2:

> **Three honest trade-offs vs Fedora**
>
> **1 · GRUB often won't boot from the Mac Startup Manager.** Many Ubuntu/Mint users have to install the [rEFInd boot manager](https://wiki.t2linux.org/guides/refind/) to boot at all — an extra step Fedora usually avoids.
>
> **2 · Manual partitioning is required.** The installer makes you use the "Something else" option and lay out the partition by hand, which is more error-prone than Fedora's guided erase.
>
> **3 · The deep-sleep fix doesn't work here.** The Phase 6 suspend workaround only works on Fedora and Arch-based distros, so on Ubuntu/Mint deep sleep stays broken.

If those are acceptable to you, follow t2linux's own [Ubuntu & Mint install page](https://wiki.t2linux.org/distributions/ubuntu/installation/) for the full steps. The Phase 1 (Secure Boot) and Phase 3 (booting) steps above still apply unchanged.

---

## Appendix — GNOME for a macOS refugee

GNOME and macOS share more DNA than you'd expect — single top bar, full-screen app overview, spaces/workspaces. The friction is in defaults and muscle memory. Here's how to ease it.

### 1 · The mental-model translation

| macOS habit | GNOME equivalent |
|---|---|
| Spotlight (⌘-Space) | Press **Super** (the ⌘ key), start typing |
| Mission Control | Press **Super**, or three-finger swipe up |
| Spaces / desktops | Workspaces (three-finger swipe left/right) |
| The Dock | Inside the overview by default (we'll pin a real one) |
| Screenshot (⌘-Shift-4) | **Print Screen** opens the screenshot tool |
| Per-app menu bar | Single top bar; app menus live in each window's header |

### 2 · Five settings to flip on day one

In **Settings → Mouse & Touchpad**, turn on **Tap to click** and confirm **Natural scrolling**. Then a couple via terminal:

```bash
# GNOME — window buttons on the left, macOS-style
gsettings set org.gnome.desktop.wm.preferences button-layout 'close,minimize,maximize:appmenu'
gsettings set org.gnome.desktop.peripherals.touchpad tap-to-click true
```

### 3 · The Command key (the big one)

**Tier 1, low-agony (try first):** make the ⌘ (Super) key *also* act as Control, so ⌘-C / ⌘-V / ⌘-Tab work from muscle memory while your real Control key stays intact (keeping the terminal sane):

```bash
# GNOME — additive Command→Control mapping
gsettings set org.gnome.desktop.input-sources xkb-options "['altwin:ctrl_win']"
```

This is additive, not a swap — that's why it's the gentle option. Test a few shortcuts; exact behavior varies by app.

> **Tier 2 · only if Tier 1 isn't enough:** Use the `keyd` daemon for a true system-level swap (works under Wayland). The tradeoff: a hard Super-to-Control swap muddies the terminal. Treat it as a project, not a quick toggle. `keyd` may need a COPR repo on Fedora rather than the default repos.

### 4 · Make the Retina screen look right

Unlock fractional scaling (125%, 150%) under Wayland, then pick the in-between option in **Settings → Displays**. 150% is the usual sweet spot for a 13-inch Retina panel.

```bash
# GNOME — enable fractional scaling
gsettings set org.gnome.mutter experimental-features "['scale-monitor-framebuffer']"
```

### 5 · A real dock and a few extensions

Enable full Flathub, then install the Extension Manager:

```bash
# Fedora Terminal
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak install flathub com.mattjakeman.ExtensionManager
```

From there, a sane starter set: **Dash to Dock** (always-visible macOS-style dock), **AppIndicator support** (restores tray icons), **Blur my Shell** (frosted look), and **Just Perfection** (fine-tuning). None is load-bearing; if one shows incompatible with your GNOME version, skip it.

---

## Desktops & realistic expectations

**Picking a desktop:**

- **GNOME** (Fedora/Ubuntu default): clean, cohesive; its overview feels natural from macOS.
- **KDE Plasma**: the most customizable, easiest to tune toward a macOS look.
- **Cinnamon** (Mint): familiar, traditional menu-and-taskbar desktop.
- **XFCE**: lightweight and fast — best for the oldest or lowest-RAM machines.

Unsure? GNOME on Fedora (newer hardware) or Cinnamon on Mint (older hardware) are safe, well-trodden defaults.

**Realistic expectations:**

- A 2015 MacBook with a light desktop can feel genuinely snappy again.
- The main first-boot hurdle is **Wi-Fi drivers** on non-T2, and the **patched-kernel setup** on T2. Plan a wired/USB connection for setup.
- A few Apple-specific features never fully work on Linux — mainly **Touch ID** and fingerprint on T2, and occasionally the webcam on certain machines.
- **It's reversible.** You can reinstall macOS later through Internet Recovery, and on a T2 Mac you can re-enable Secure Boot.

---

## Help & sources

- **Non-T2 Macs:** your distro's forums and docs; the Arch Wiki and Ubuntu community pages have great Mac-specific troubleshooting.
- **T2 Macs:** [wiki.t2linux.org](https://wiki.t2linux.org) and the t2linux Discord / Matrix.
- **Apple Silicon:** [asahilinux.org](https://asahilinux.org).

**Validated against:** [Apple's T2 model list](https://support.apple.com/en-us/103265), the [t2linux wiki](https://wiki.t2linux.org), [Asahi Linux](https://asahilinux.org), and [RPM Fusion](https://rpmfusion.org).

*Hardware support evolves — for the T2 track especially, check the t2linux wiki for the current state before you begin. Confidence is high on the T2/non-T2 split and the Apple model list; the Broadcom Wi-Fi package names are the most chip-dependent detail, so verify against your specific model if the first attempt doesn't connect. This page works offline — save it and keep it open while you install.*
