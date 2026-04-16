# Contributing to Awesome iOS DESIGN.md

Thanks for contributing.

This repository is a curated collection of DESIGN.md files extracted from popular iOS apps. Each file captures an app's complete visual language — tokens, typography, components, HIG patterns — in a format any AI coding agent can read.

## How to Contribute

### Request a New App

To request a DESIGN.md for an iOS app, [open an issue](https://github.com/Meliwat/awesome-ios-design-md/issues/new) with the App Store link and a few reference screenshots.

### Improve an Existing DESIGN.md

If you notice issues with an existing file:

1. **Open an issue first** to describe what you'd like to change and get feedback
2. Open the app's `DESIGN.md`
3. Compare against the live iOS app (latest build)
4. Fix incorrect hex values, missing tokens, stale component specs, or weak descriptions
5. Update `preview.html` and `preview-dark.html` if your changes affect displayed tokens
6. Open a PR with before/after rationale (screenshots help)

### Quality Bar

- Values should be drawn from actual iOS builds, not marketing screenshots
- Prefer system tokens (SF Pro, `UIColor.systemXxx`, SF Symbols) when the app uses them
- Document both light and dark appearances — every iOS app ships both
- Include HIG-correct specs: 44pt minimum touch targets, Dynamic Type support, safe-area handling

## License

By contributing, you agree your contributions are provided under the repository license terms (MIT).
