# Contributing to Awesome iOS DESIGN.md

Thanks for being here. This repo is a curated collection of **DESIGN.md** packs — complete, reverse-engineered design systems for real apps, written so any AI coding agent can generate pixel-matched UI. Every contribution that makes a pack sharper or adds a great app is welcome.

## Ways to contribute

### Request a new app

[**Open an app request →**](https://github.com/Meliwat/awesome-ios-design-md/issues/new?template=request-an-app.yml)

Include the app name, an App Store / Play Store / website link, and a few high-quality screenshots (light **and** dark mode if it has both). The more reference material, the faster and more accurate the pack.

### Report a wrong token

[**Report a token fix →**](https://github.com/Meliwat/awesome-ios-design-md/issues/new?template=token-fix.yml)

Tell us the app, the file, the value that's off, what it should be, and where you observed it (a screenshot of the live build is ideal). Small fixes are the most valuable contributions here — design systems drift as apps ship updates.

### Improve an existing pack

1. Open an issue first if the change is non-trivial, so we can align before you invest time.
2. Open the app's `DESIGN.md` and compare against the **latest live build**.
3. Fix incorrect hex values, missing component states, stale specs, or weak descriptions.
4. Keep the framework companions in sync — a token change in `DESIGN.md` should land in `DESIGN-swiftui.md`, `DESIGN-expo.md`, and `DESIGN-android.md` too.
5. Open a PR with before/after rationale (screenshots help a lot).

## What each pack contains

| File | Purpose |
|------|---------|
| `DESIGN.md` | Framework-neutral design system (the 9-section spec) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color`/`Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native — tokens, themed components, Reanimated |
| `DESIGN-android.md` | Jetpack Compose (Material 3, Kotlin) implementation |
| `README.md` | Pack overview, signature moves, brand sources |
| `preview.md` | Link to the interactive preview on the Spectr gallery |

## Quality bar

- **Real builds, not marketing.** Values come from the live app, not press shots or store screenshots.
- **Light and dark.** Document both appearances — virtually every app ships both.
- **Platform-correct.** Honor HIG/Material: 44pt minimum touch targets, Dynamic Type / `sp` scaling, safe areas, real motion curves and haptics.
- **Exact, not approximate.** Hex codes, point/dp values, weights, line heights, letter spacing — specifics are the entire point of a DESIGN.md.
- **One identity per pack.** Each app keeps its own palette and voice. Never copy another app's tokens.

## License

By contributing, you agree your contributions are provided under the repository's [MIT License](LICENSE).
