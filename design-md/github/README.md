# GitHub iOS Inspired Design System

Design system docs inspired by the [GitHub iOS app](https://apps.apple.com/us/app/github/id1477376905). Not the official system. Brand colors, font names, and component patterns are cross-referenced with GitHub's public Primer design system and product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, repo home + code browser + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Primer dark default** — canvas `#0D1117`, surfaces `#161B22` / `#21262D`, borders `#30363D`; light mode a first-class peer
- **One accent blue** (`#2F81F7` dark / `#0969DA` light) — links, `owner/name`, active text, focus rings
- **One reserved green** (`#238636` dark / `#1F883D` light) — the **Code** download button and **Merge pull request** button ONLY
- **Repo home** — `owner / name` + description + language dot/stars/forks + tab strip + branch pill + latest-commit strip + file tree + inline README
- **Code browser** — a monospace file viewer with a sticky path breadcrumb and a latest-commit strip
- **Semantic state colors** — open = green `#3FB950`, merged = purple `#A371F7`, closed = red `#F85149`, draft = grey `#6E7681` (fixed meaning, not decoration)
- **Contributions heatmap** — the iconic 5-step ramp `#161B22 → #0E4429 → #006D32 → #26A641 → #39D353` ("Less → More")
- **Tab-strip active underline** in orange-coral `#F78166` (dark) / `#FD8C73` (light) — not the accent blue
- **Diff viewer** — additions on a `rgba(63,185,80,0.15)` wash with a `+`, deletions on `rgba(248,81,73,0.15)` with a `-`, muted gutter
- **Monospace is the contract** — Mona Sans for UI; monospace for ALL code, SHAs, file/branch names, and diffs (glyph distinction is functional)
- **Issue/PR labels** as bordered pills tinted by the label's own color (bug red, enhancement purple, good first issue green)
- **Bottom tab bar** (Home / Notifications / Explore / Profile) — translucent over `rgba(13,17,23,0.94)`, no tint pill

## Brand Sources

- [GitHub](https://github.com/) · [Primer design system](https://primer.style/)
- [Mona Sans by GitHub](https://github.com/github/mona-sans) — SIL OFL
- [Monaspace by GitHub](https://monaspace.githubnext.com/) — SIL OFL (code; `SF Mono` / `JetBrains Mono` as fallbacks)
- Public Primer palette: canvas `#0D1117`, accent `#2F81F7`, reserved green `#238636`, heatmap `#161B22 → #39D353`
- Signature: the repo home + code browser, the contributions heatmap, semantic state pills, and the diff viewer
