# Todoist iOS Inspired Design System

Design system docs inspired by the [Todoist iOS app](https://apps.apple.com/us/app/todoist-to-do-list-planner/id572688855). Not the official system. Brand colors, typography (SF Pro Display / Text — Todoist uses the system stack), and component patterns are cross-referenced with Doist's public design writing and Todoist's user-selectable project color palette; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Todoist Red** (`#DC4C3E`) as a strict verb color — FAB, P1 flag, brand mark, primary CTA — never ambient
- **The tinted red FAB shadow** (`rgba(220,76,62,0.35) 0 6px 16px`) — a signature detail; everywhere else uses generic black drop-shadows, but Todoist's FAB shadow is dyed red
- **Four-tier priority via checkbox stroke** — P1 Red `#DC4C3E`, P2 Orange `#EB8909`, P3 Blue `#246FE0`, P4 gray outline — color lives on the checkbox border, never the row background
- **Edge-to-edge task rows** at 52pt minimum, 0.5pt `#EEEEEE` divider inset 64pt from the left (aligned with task content, not the checkbox)
- **Quick-add card with natural-language parsing** — typing "tomorrow at 6pm p1 #groceries" auto-generates inline smart-parsing chips before commit
- **Strikethrough + collapse-upward** task completion animation (250ms ease-out) — the next row slides up smoothly
- **SF Pro system font** — Todoist deliberately uses the platform face, no custom typeface, to feel like a respectful native iOS citizen
- **20-swatch user-selectable project palette** — every user-created project gets a color dot from the official set (Berry Red, Mint, Grape, etc.)
- **Section headers in 13pt SEMIBOLD UPPERCASE** with 0.6pt tracking — the rhythm divider across Today / Upcoming / Projects

## Brand Sources

- [Todoist Design — Doist Blog](https://doist.com/blog/category/design/)
- [Doist Brand Resources](https://doist.com/press)
- [Todoist Help Center — Priorities](https://www.todoist.com/help/articles/introduction-to-priorities-XLHkdMq)
- [Todoist Help Center — Project colors](https://www.todoist.com/help/articles/use-project-colors-805KuyOAo)
- Public brand palette: Todoist Red `#DC4C3E`, P1 `#DC4C3E`, P2 `#EB8909`, P3 `#246FE0`, P4 gray outline; Ink `#202020`, Secondary `#808080`, Surface `#FAFAFA`
