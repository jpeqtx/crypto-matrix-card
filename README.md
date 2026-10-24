# Crypto Matrix Card

A deterministic password card generator that runs entirely in the browser — no build step, no dependencies, no server.

![GitHub Pages](https://img.shields.io/badge/GitHub%20Pages-live-brightgreen)
![Vanilla JS](https://img.shields.io/badge/Vanilla%20JS-ES2017-yellow)
![PBKDF2](https://img.shields.io/badge/Crypto-PBKDF2--SHA256-blue)
![License: Unlicense](https://img.shields.io/badge/License-Unlicense-blue)

**Live demo:** https://jpeqtx.github.io/crypto-matrix-card/

---

## Overview

Crypto Matrix Card is a single-file web app that generates a **credit-card-sized character matrix** (85.6 × 53.98 mm, ISO/IEC 7810 ID-1) from any seed string. By tracing a path across the tiles with your mouse or finger, you can derive passwords on demand — inspired by the concept of physical password cards.

The matrix is derived using **PBKDF2-SHA256 (200,000 iterations)** followed by SHA-256 counter-mode expansion, so the same seed always produces the same card, and brute-forcing the seed is computationally expensive.

![Screenshot](docs/screenshot.png)

---

## Features

- **Deterministic generation** — same seed → same card, every time
- **Strong key derivation** — PBKDF2-SHA256, 200,000 iterations
- **4 password patterns** matching common site requirements:
  1. Numbers only (`0-9`)
  2. Numbers + lowercase (`0-9a-z`)
  3. Numbers + upper & lowercase (`0-9a-zA-Z`)
  4. Numbers + upper/lower + symbols (`0-9a-zA-Z` + 13 symbols)
- **Drag-to-trace** password generation (mouse, touch, pen)
- **Live character counter** on the generated password
- **Character lookup** — find which tiles contain a given character
- **Seed visibility toggle** — show / mask the seed on the printed card
- **Print-ready layout** — outputs exactly 85.6 × 53.98 mm for laser engraving
- **Light theme** optimized for white cardstock and laser marking
- **Zero dependencies** — no jQuery, no framework, no build tools

---

## Usage

1. Open `index.html` (or the GitHub Pages URL) in any modern browser.
2. Enter a **seed** (any text or number — e.g. `my-secret-2026`) and click **Generate**, or click **Random** for a 64-bit hex seed.
3. To generate a password:
   - Select a pattern (① ~ ④) under **Password Generator**.
   - **Drag** across the tiles to trace a path.
   - The characters along your path are concatenated into the password shown below.
4. Use **Print / Save PDF** to export the card at actual credit-card size.

> ⚠️ **Security note**
> The card itself is not secret. The *path you trace* and the *seed* are. If you print the seed on the card and share the card, anyone can regenerate the matrix. Keep the seed private, or disable **Show seed on card**.

---

## How it works

```
seed (any string)
   │
   ├─ PBKDF2-SHA256 (salt = "crypto-matrix-card-v4", iterations = 200,000)
   │       └─→ 256-bit key
   │
   ├─ SHA-256 counter mode:  H(key ‖ i) for i = 0, 1, 2, …
   │       └─→ 2,048-byte stream
   │
   └─ BytePRNG (synchronous, deterministic)
           ├─ Fisher-Yates shuffle for each character column
           └─ 40 tiles × { center, top-left, top-right, bottom-left, bottom-right }
```

| Column | Character set | Pattern |
|---|---|---|
| top-left | `0-9` | ① |
| top-right | `0-9a-z` | ② |
| bottom-left | `0-9a-zA-Z` | ③ |
| bottom-right | `0-9a-zA-Z` + 13 symbols | ④ |
| center | `0-9` + `a-z` + 4 random symbols | (decorative / trace anchor) |

All columns are independently shuffled using the same derived stream, so the matrix is fully reproducible from the seed.

---

## Printing / Laser engraving

The `@media print` stylesheet outputs the card at **85.6 × 53.98 mm** with:

- 0.25 mm outer border
- 0.2 mm tile borders
- All corner characters in black (monochrome-friendly)
- One page per card, no overflow

To print:

1. Click **Print / Save PDF**.
2. In the browser dialog:
   - Margins: **Default / 5 mm**
   - Scale: **100% / None**
   - Paper: any size (the card is placed at the top-left)
3. Feed the resulting PDF to your laser cutter / engraver and verify the **85.6 × 53.98 mm** dimensions before marking.

---

## Browser support

| Browser | Status |
|---|---|
| Chrome / Edge 90+ | ✅ |
| Firefox 90+ | ✅ |
| Safari 15+ | ✅ |
| Mobile Chrome / Safari | ✅ (pointer events) |

Requires `crypto.subtle` (secure contexts only — `https://` or `localhost`). Opening the file via `file://` may work in some browsers but is not guaranteed.

---

## Project structure

```
crypto-matrix-card/
├── index.html          # single-file app (HTML + CSS + JS)
├── docs/
│   └── screenshot.png  # README screenshot
├── LICENSE             # Unlicense
└── README.md
```

No build step. No npm. No bundler.

---

## License

This is free and unencumbered software released into the public domain under the [Unlicense](LICENSE).

Anyone is free to copy, modify, publish, use, compile, sell, or distribute this software, either in source code form or as a compiled binary, for any purpose, commercial or non-commercial, and by any means.
