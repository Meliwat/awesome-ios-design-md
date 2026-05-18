# KAYAK iOS Inspired Design System

Design system docs inspired by the [KAYAK iOS app](https://apps.apple.com/us/app/kayak-flights-hotels-cars/id305204535). Not the official system. Brand colors, font names, and component patterns are cross-referenced with KAYAK's public product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, fare card, price-calendar strip, forecast banner, fare-compare matrix |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, typography, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **KAYAK Orange** (`#FF690F`) is the *single* brand accent — selected date, Hacker Fare tag, primary "View Deal" CTA, active tab; kept scarce (≤ 1–2 per viewport)
- **Price-calendar strip** — horizontal scroll where each day shows its cheapest fare, color-coded `#1E9E5A` low / neutral mid / `#E5484D` high
- **Price-forecast banner** — "likely to rise/drop" + confidence %, the metasearch intelligence payoff
- **Fare-compare matrix** — airlines × stops grid with the cheapest cell highlighted green `#1E9E5A`
- **Hacker Fare** — two separate one-way tickets stitched into a cheaper round trip, flagged in orange
- **Fare card** — airline row, depart→duration-arc-with-stops→arrive leg, provider count + bold tabular price
- **Functional semantics** — price low/mid/high + forecast buy/wait/rise (green/amber `#E8A317`/red) are NOT brand colors; they always mean something
- **Provider transparency** — "2 booking sites", "JetBlue.com"; KAYAK routes you onward, it doesn't sell
- **Tabular figures everywhere** prices/times appear so calendar + matrix columns align
- **Near-neutral dark canvas** (`#101214`) with NO brand tint — keeps the lone orange accent loud
- **Plain links are blue** `#2E7CF6` — orange is never body text

## Brand Sources

- [KAYAK](https://www.kayak.com/)
- [KAYAK Price Forecast & Hacker Fares](https://www.kayak.com/news/flight-price-forecast/)
- KAYAK product sans (proprietary) — `Inter` (SIL OFL) is the closest free substitute
- Public brand palette: KAYAK Orange `#FF690F` (single accent), functional price ramp green `#1E9E5A` / red `#E5484D`, link blue `#2E7CF6`
