# SoFi (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates SoFi's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the gradient member hero, account tiles, cross-sell cards, and the activity row.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (Blue)
    static let sofiBlue        = Color(red: 0.000, green: 0.627, blue: 0.875) // #00A0DF
    static let sofiBlueBright  = Color(red: 0.161, green: 0.761, blue: 1.000) // #29C2FF
    static let sofiBluePressed = Color(red: 0.000, green: 0.525, blue: 0.737) // #0086BC
    static let sofiBlueSoft       = Color(red: 0.902, green: 0.957, blue: 0.984) // #E6F4FB (light)
    static let sofiBlueSoftDark   = Color(red: 0.055, green: 0.165, blue: 0.267) // #0E2A44 (dark)
    static let sofiOnBlueInk   = Color(red: 0.016, green: 0.133, blue: 0.208) // #042235

    // MARK: - Canvas & Surfaces (Dark-Native — canonical)
    static let sofiNavy      = Color(red: 0.039, green: 0.055, blue: 0.153) // #0A0E27
    static let sofiSurface1  = Color(red: 0.071, green: 0.090, blue: 0.212) // #121736
    static let sofiSurface2  = Color(red: 0.106, green: 0.129, blue: 0.278) // #1B2147
    static let sofiDivider   = Color(red: 0.145, green: 0.173, blue: 0.333) // #252C55

    // MARK: - Canvas & Surfaces (Light)
    static let sofiLightCanvas   = Color(red: 0.957, green: 0.969, blue: 0.984) // #F4F7FB
    static let sofiLightSurface1 = Color.white                                   // #FFFFFF
    static let sofiLightSurface2 = Color(red: 0.933, green: 0.949, blue: 0.973) // #EEF2F8
    static let sofiLightDivider  = Color(red: 0.886, green: 0.906, blue: 0.941) // #E2E7F0

    // MARK: - Text (on navy)
    static let sofiTextPrimary   = Color(red: 0.910, green: 0.922, blue: 0.969) // #E8EBF7
    static let sofiTextSecondary = Color(red: 0.608, green: 0.639, blue: 0.780) // #9BA3C7
    static let sofiTextTertiary  = Color(red: 0.420, green: 0.447, blue: 0.600) // #6B7299
    static let sofiTextPrimaryLight   = Color(red: 0.047, green: 0.075, blue: 0.188) // #0C1330
    static let sofiTextSecondaryLight = Color(red: 0.353, green: 0.384, blue: 0.525) // #5A6286

    // MARK: - Semantic
    static let sofiPositive     = Color(red: 0.184, green: 0.816, blue: 0.541) // #2FD08A (navy)
    static let sofiPositiveLight = Color(red: 0.059, green: 0.663, blue: 0.408) // #0FA968 (light)
    static let sofiNegative     = Color(red: 1.000, green: 0.420, blue: 0.420) // #FF6B6B (navy)
    static let sofiNegativeLight = Color(red: 0.878, green: 0.282, blue: 0.302) // #E0484D (light)
    static let sofiGold         = Color(red: 0.949, green: 0.757, blue: 0.306) // #F2C14E (navy)
    static let sofiGoldLight    = Color(red: 0.784, green: 0.573, blue: 0.059) // #C8920F (light)
}
```

## 2. Typography

SoFi uses a confident geometric sans — **Manrope** is the faithful free stand-in (SIL OFL). Bundle the TTFs via `Info.plist` / `UIAppFonts`. Use tabular figures for all amounts.

```swift
extension Font {
    static let sofiNetWorth   = Font.custom("Manrope-ExtraBold", size: 40)
    static let sofiScreenTitle = Font.custom("Manrope-ExtraBold", size: 32)
    static let sofiSection    = Font.custom("Manrope-Bold",      size: 22)
    static let sofiTileValue  = Font.custom("Manrope-Bold",      size: 18)
    static let sofiCardTitle  = Font.custom("Manrope-Bold",      size: 16)
    static let sofiBody       = Font.custom("Manrope-Regular",   size: 16)
    static let sofiRowTitle   = Font.custom("Manrope-Bold",      size: 15)
    static let sofiAmount     = Font.custom("Manrope-ExtraBold", size: 15)
    static let sofiMeta       = Font.custom("Manrope-Regular",   size: 14)
    static let sofiCaption    = Font.custom("Manrope-SemiBold",  size: 12)
    static let sofiButton     = Font.custom("Manrope-Bold",      size: 16)
    static let sofiTab        = Font.custom("Manrope-SemiBold",  size: 10)
    static let sofiChip       = Font.custom("Manrope-Bold",      size: 12)
    static let sofiLink       = Font.custom("Manrope-Bold",      size: 13)
}

// Apply tabular figures to any amount Text:
// Text(amount).font(.sofiNetWorth).monospacedDigit()
```

## 3. Signature Components

### Member Hero (gradient)

```swift
struct SofiMemberHero: View {
    let greeting: String
    let netWorth: String
    let change: String        // "+$1,840.22 this month"
    let isUp: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text(greeting).font(.custom("Manrope-Bold", size: 18)).foregroundStyle(.white)
                Spacer()
                Image(systemName: "bell").font(.system(size: 20, weight: .semibold)).foregroundStyle(.white)
                Image(systemName: "person.crop.circle").font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(.white).padding(.leading, 12)
            }
            .padding(.bottom, 18)

            Text("Net worth")
                .font(.sofiCaption).foregroundStyle(.white.opacity(0.75))
            Text(netWorth)
                .font(.sofiNetWorth).foregroundStyle(.white)
                .tracking(-1.2).monospacedDigit()
                .padding(.top, 4)

            HStack(spacing: 5) {
                Image(systemName: isUp ? "arrow.up" : "arrow.down")
                    .font(.system(size: 11, weight: .heavy))
                Text(change).font(.sofiLink)
            }
            .foregroundStyle(Color(red: 0.431, green: 0.941, blue: 0.714)) // #6EF0B6
            .padding(.horizontal, 10).padding(.vertical, 5)
            .background(Color.sofiPositive.opacity(0.18), in: Capsule())
            .padding(.top, 8)
        }
        .padding(.horizontal, 20)
        .padding(.bottom, 28)
        .padding(.top, 8)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            LinearGradient(
                stops: [
                    .init(color: .sofiBlue, location: 0.0),
                    .init(color: Color(red: 0.106, green: 0.325, blue: 0.769), location: 0.45), // #1B53C4
                    .init(color: .sofiNavy, location: 1.0),
                ],
                startPoint: .topLeading, endPoint: .bottomTrailing
            )
        )
        .ignoresSafeArea(edges: .top)
    }
}
```

### Account Tile

```swift
struct SofiAccountTile: View {
    let symbol: String
    let label: String
    let value: String
    let subStat: String
    let subKind: SubKind

    enum SubKind { case gain, loss, rewards, neutral
        var color: Color {
            switch self {
            case .gain: return .sofiPositive
            case .loss: return .sofiNegative
            case .rewards: return .sofiGold
            case .neutral: return .sofiTextSecondary
            }
        }
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Image(systemName: symbol)
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(Color.sofiBlueBright)
                .frame(width: 36, height: 36)
                .background(Color.sofiBlueSoftDark, in: RoundedRectangle(cornerRadius: 11))
                .padding(.bottom, 12)
            Text(label).font(.sofiCaption).foregroundStyle(Color.sofiTextSecondary)
            Text(value).font(.sofiTileValue).foregroundStyle(Color.sofiTextPrimary)
                .tracking(-0.4).monospacedDigit().padding(.top, 3)
            Text(subStat).font(.sofiCaption).foregroundStyle(subKind.color).padding(.top, 4)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(16)
        .background(Color.sofiSurface1, in: RoundedRectangle(cornerRadius: 18))
        .overlay(RoundedRectangle(cornerRadius: 18).stroke(Color.sofiDivider, lineWidth: 1))
        .shadow(color: .black.opacity(0.25), radius: 12, y: 2)
    }
}

// 2-column grid:
// LazyVGrid(columns: [GridItem(.flexible(), spacing: 12), GridItem(.flexible(), spacing: 12)], spacing: 12) { … }
```

### Cross-Sell Product Card

```swift
struct SofiProductCard: View {
    let symbol: String
    let title: String
    let valueProp: String
    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Image(systemName: symbol)
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(Color.sofiBlueBright)
                .frame(width: 36, height: 36)
                .background(Color.sofiBlueSoftDark, in: RoundedRectangle(cornerRadius: 11))
                .padding(.bottom, 12)
            Text(title).font(.sofiCardTitle).foregroundStyle(Color.sofiTextPrimary)
            Text(valueProp).font(.sofiCaption.weight(.regular))
                .foregroundStyle(Color.sofiTextSecondary).padding(.top, 3)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(16)
        .background(Color.sofiSurface2, in: RoundedRectangle(cornerRadius: 18))
        .overlay(RoundedRectangle(cornerRadius: 18).stroke(Color.sofiDivider, lineWidth: 1))
    }
}
```

### Activity Row

```swift
struct SofiActivityRow: View {
    let symbol: String
    let tint: Color           // .sofiBlueBright or .sofiGold
    let name: String
    let meta: String
    let amount: String
    let isPositive: Bool

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: symbol).font(.system(size: 18, weight: .semibold))
                .foregroundStyle(tint)
                .frame(width: 40, height: 40)
                .background(Color.sofiBlueSoftDark, in: RoundedRectangle(cornerRadius: 13))
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.sofiRowTitle).foregroundStyle(Color.sofiTextPrimary)
                Text(meta).font(.sofiMeta).foregroundStyle(Color.sofiTextSecondary)
            }
            Spacer()
            Text(amount).font(.sofiAmount).monospacedDigit()
                .foregroundStyle(isPositive ? Color.sofiPositive : Color.sofiTextPrimary)
        }
        .padding(.vertical, 12)
        .overlay(alignment: .bottom) { Divider().background(Color.sofiDivider) }
    }
}
```

### Primary Button & Performance Chip

```swift
struct SofiPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.sofiButton).foregroundStyle(Color.sofiOnBlueInk)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Color.sofiBlue, in: Capsule())   // FULL PILL
        }
        .buttonStyle(SofiPressStyle())
    }
}
struct SofiPressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.06 : 0)
    }
}

enum SofiPerf { case active, up(String), down(String), rewards(String) }
struct SofiPerfChip: View {
    let perf: SofiPerf
    var body: some View {
        let (bg, fg, text): (Color, Color, String) = {
            switch perf {
            case .active:          return (.sofiBlueSoftDark, .sofiBlueBright, "Active")
            case .up(let v):       return (Color.sofiPositive.opacity(0.16), .sofiPositive, "▲ \(v)")
            case .down(let v):     return (Color.sofiNegative.opacity(0.16), .sofiNegative, "▼ \(v)")
            case .rewards(let v):  return (Color.sofiGold.opacity(0.16), .sofiGold, v)
            }
        }()
        Text(text).font(.sofiChip).foregroundStyle(fg)
            .padding(.horizontal, 14).padding(.vertical, 7)
            .background(bg, in: Capsule())
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SofiTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem    { Label("Home", systemImage: "house.fill") }
            InvestView().tabItem  { Label("Invest", systemImage: "chart.line.uptrend.xyaxis") }
            BankingView().tabItem { Label("Banking", systemImage: "creditcard.fill") }
            BorrowView().tabItem  { Label("Borrow", systemImage: "building.columns.fill") }
            MeView().tabItem      { Label("Me", systemImage: "person.crop.circle") }
        }
        .tint(.sofiBlueBright)   // active = bright blue, filled icon; no pill
    }
}
```

## 5. Motion

```swift
// Net-worth count-up — odometer roll on load / after transfer
@State private var shown: Double = 0
Text(shown, format: .currency(code: "USD"))
    .contentTransition(.numericText())
    .onAppear { withAnimation(.easeOut(duration: 0.6)) { shown = actualNetWorth } }

// Gradient parallax — hero scrolls ~0.7x content
.offset(y: scrollOffset > 0 ? scrollOffset * 0.3 : 0)

// Tile press — scale + border brighten
.scaleEffect(pressed ? 0.97 : 1)
.animation(.easeOut(duration: 0.15), value: pressed)

// Performance tick — value + color animate on refresh
.contentTransition(.numericText())
.animation(.easeOut(duration: 0.2), value: percent)

// Transfer / invest success — blue check stroke draw (500ms) + medium haptic
.sensoryFeedback(.impact(weight: .medium), trigger: transferDone)

// Cross-sell carousel — paged ScrollView
ScrollView(.horizontal) { … }.scrollTargetBehavior(.viewAligned)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Invest (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Banking (tab) | `creditcard` / `creditcard.fill` | 22pt |
| Borrow (tab) | `building.columns` / `.fill` | 22pt |
| Me (tab) | `person.crop.circle` | 22pt |
| Checking & Savings tile | `creditcard` | 18pt |
| Invest tile | `chart.line.uptrend.xyaxis` | 18pt |
| Loans tile | `clock.arrow.circlepath` | 18pt |
| Credit Card tile | `star.fill` | 18pt |
| Rewards | `star.circle.fill` | 18pt |
| Net-worth up | `arrow.up` | 11pt |
| Net-worth down | `arrow.down` | 11pt |
| Notifications | `bell` | 20pt |
| Transfer | `arrow.left.arrow.right` | 16pt |
| Auto-invest | `arrow.triangle.2.circlepath` | 18pt |
| Direct deposit | `dollarsign.arrow.circlepath` | 18pt |
| Back | `chevron.left` | 17pt |
| Settings | `gearshape` | 20pt |

## 7. Dark Mode

```swift
struct SofiTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            // SoFi is dark-native: navy is the canonical canvas. Light mode lifts surfaces
            // but the brand identity (navy + electric blue) holds.
            .background(scheme == .dark ? Color.sofiNavy : Color.sofiLightCanvas)
            .foregroundStyle(scheme == .dark ? Color.sofiTextPrimary : Color.sofiTextPrimaryLight)
            .tint(.sofiBlueBright)
    }
}
extension View { func sofiTheme() -> some View { modifier(SofiTheme()) } }

// The member-hero gradient (#00A0DF → #1B53C4 → #0A0E27) and #042235 on-blue ink
// are IDENTICAL in both modes. Light mode swaps:
//   sofiSurface1 → white, sofiBlueSoftDark → sofiBlueSoft (#E6F4FB),
//   sofiPositive → sofiPositiveLight, sofiNegative → sofiNegativeLight,
//   sofiGold → sofiGoldLight, sofiBlueBright accents → sofiBlue.
// On navy, drop shadows are weak — tiles use a 1pt sofiDivider border + soft shadow.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` need iOS 17 — gate behind `#available`, fall back to `UIImpactFeedbackGenerator` and a manual count-up timer)
- Bundle Manrope TTFs (Regular, Medium, SemiBold, Bold, ExtraBold) via `Info.plist` `UIAppFonts` — Manrope is SIL OFL, free to redistribute
- **Tabular figures**: apply `.monospacedDigit()` to net worth and every amount so columns align and the count-up doesn't jitter
- Dynamic Type: scale net worth, screen titles, section headers, tile values, body, row titles, meta; keep tab labels, chip text, and the change-pill ratio FIXED
- VoiceOver: the hero announces "Net worth, $84,210.55, up $1,840.22 this month"; tiles announce "{label}, {value}, {sub-stat}"; activity rows announce "{name}, {meta}, {signed amount}"
- **Never convey performance by color alone** — keep the ▲/▼ glyph and `+`/`-` sign on every gain/loss
- Color contrast: `#042235` on `#00A0DF` passes WCAG AA (the near-black blue ink is chosen for this); white on the gradient passes throughout; `#2FD08A` on navy passes for graphical/large text — pair with the arrow glyph for small %s
- Reduce Motion: disable the net-worth odometer roll (set instantly), the gradient parallax, and the check-stroke draw (crossfade instead); keep the tile press as an opacity change
- Dark mode: navy `#0A0E27` is canonical; the hero gradient and `#042235` ink stay constant; in light mode surfaces lift to white but the navy + electric-blue brand identity must remain recognizable
