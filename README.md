<p align="center">
  <img src="mascot-penguincow.svg" alt="Project mascot: a penguin with cow spots and small horns" width="170">
</p>

<h1 align="center">Old Mac, New Linux</h1>

<p align="center"><em>A friendly, source-validated guide to installing Linux on retired Intel Macs (2015–2020), with a full Fedora walkthrough for T2 models.</em></p>

---

**Live site:** `https://YOURNAME.github.io/REPO/` *(fill in after enabling GitHub Pages)*

This repo holds one guide in two formats, a couple of how-to docs, and a small
automation that keeps the guide current against its upstream sources.

## For readers

Just open the live site (or `index.html` locally). It works offline — save the page
and keep it open on your phone while you install. Start with the **"Which Mac do you
have?"** picker; it routes you to the right path:

- **Non-T2 Macs (2015–2017):** the easy track — boot a normal Linux installer.
- **T2 Macs (2018–2020):** the full Fedora walkthrough, plus notes for Ubuntu and Mint.
- **Apple Silicon (M-series):** out of scope — see [Asahi Linux](https://asahilinux.org).

## Repo layout

```
.
├── index.html                    # THE GUIDE — canonical source, and the live site
├── intel-mac-linux-guide.md      # generated Markdown export (do not hand-edit)
├── mascot-penguincow.svg         # project mascot / logo
├── README.md                     # you are here
├── CLAUDE.md                     # rulebook for the automated maintainer
├── sources.md                    # the upstream pages the guide depends on
├── SETUP.md                      # how to turn on automatic maintenance
├── Publishing_GitHub_Pages_and_t2linux.md   # hosting + community tutorial
└── .github/workflows/maintain.yml           # weekly PR-gated update job
```

> When you first publish, rename the canonical file to `index.html` so GitHub Pages
> serves it at the root URL.

## How it's maintained

**HTML is canon.** Edit `index.html` and nothing else by hand. The Markdown file is a
generated export that mirrors it — if you edit the `.md` directly, your change gets
overwritten on the next run. This keeps the two formats from drifting.

**Updates are automated, but gated.** A weekly GitHub Actions job re-checks the
guide against the t2linux wiki and the Fedora ISO releases (the watch list lives in
`sources.md`). When something material changes, it prepares the edit on a branch and
opens a **pull request** with a plain-English summary. You skim and merge; merging
redeploys the live page. It never edits the live site directly — by design, because
this guide tells people to erase their disks. Setup is in `SETUP.md`.

## Versioning

Each format carries a version and a changelog (in the HTML footer, and in the
export's header block). Small factual fixes bump the patch number; new sections bump
the minor. The version travels with the file, so anyone you share it with can tell
which edition they have.

## Contributing

Spotted something wrong or stale? Open an issue or a pull request against
`index.html`. Please keep the voice (warm, direct, honest about trade-offs), keep the
disk-wipe warnings prominent, and cite the upstream source for any factual change.

## Credits & sources

Built on the excellent work of the **[t2linux project](https://wiki.t2linux.org)**
and validated against their wiki, **[Apple's T2 model list](https://support.apple.com/en-us/103265)**,
**[Asahi Linux](https://asahilinux.org)**, and **[RPM Fusion](https://rpmfusion.org)**.

## Disclaimer & license

This is an independent community guide. It is **not affiliated with or endorsed by**
Apple, the t2linux project, the Linux Foundation, or any distribution. All trademarks
belong to their respective owners. The mascot is an original character and is not
Apple's Clarus the Dogcow; it just shares a fondness for the genre.

The guide text is original work derived from public documentation.

**License:** This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0
International License (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/),
the same license the t2linux wiki uses. In short: reuse and adapt it freely, including
commercially, as long as you **credit** the source and release your derivatives under
the **same license**. The full text is in [`LICENSE`](LICENSE).

© 2026 *[your name or handle here]*. When you publish, replace that with how you'd
like to be credited.

If you ever pull text or images *from* the t2linux wiki into the guide, that content
is already CC-BY-SA, so honor its attribution + share-alike terms too. Linking to them
is always fine.
