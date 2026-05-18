# Acorns (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Acorns' visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views including the allocation donut.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Growth / Action
    static let acornGreen        = Color(red: 0.435, green: 0.749, blue: 0.306) // #6FBF4E
    static let acornGreenBright  = Color(red: 0.518, green: 0.851, blue: 0.384) // #84D962
    static let acornGreenPressed = Color(red: 0.353, green: 0.647, blue: 0.239) // #5AA53D
    static let acornForestInk    = Color(red: 0.078, green: 0.188, blue: 0.165) // #14302A (text on green)

    // MARK: - Brand (Oak)
    static let acornOak        = Color(red: 0.353, green: 0.169, blue: 0.510) // #5A2B82
    static let acornOakBright  = Color(red: 0.482, green: 0.263, blue: 0.659) // #7B43A8
    static let acornOakPressed = Color(red: 0.278, green: 0.125, blue: 0.416) // #47206A
    static let acornOakSoft    = Color(red: 0.784, green: 0.682, blue: 0.875) // #C8AEDF

    // MARK: - Canvas & Surfaces (Light)
    static let acornCanvas         = Color(red: 0.961, green: 0.953, blue: 0.973) // #F5F3F8
    static let acornSurface        = Color.white                                  // #FFFFFF
    static let acornSurfacePressed = Color(red: 0.929, green: 0.914, blue: 0.949) // #EDE9F2
    static let acornDivider        = Color(red: 0.898, green: 0.882, blue: 0.925) // #E5E1EC

    // MARK: - Canvas & Surfaces (Dark)
    static let acornDarkCanvas   = Color(red: 0.078, green: 0.071, blue: 0.102) // #14121A
    static let acornDarkSurface1 = Color(red: 0.118, green: 0.106, blue: 0.153) // #1E1B27
    static let acornDarkSurface2 = Color(red: 0.161, green: 0.145, blue: 0.200) // #292533
    static let acornDarkDivider  = Color(red: 0.220, green: 0.192, blue: 0.263) // #383143

    // MARK: - Text
    static let acornTextPrimary       = Color(red: 0.122, green: 0.102, blue: 0.169) // #1F1A2B
    static let acornTextSecondary     = Color(red: 0.431, green: 0.392, blue: 0.502) // #6E6480
    static let acornTextTertiary      = Color(red: 0.612, green: 0.573, blue: 0.678) // #9C92AD
    static let acornDarkTextPrimary   = Color(red: 0.949, green: 0.937, blue: 0.965) // #F2EFF6
    static let acornDarkTextSecondary = Color(red: 0.678, green: 0.643, blue: 0.737) // #ADA4BC
    static let acornDarkTextTertiary  = Color(red: 0.459, green: 0.420, blue: 0.522) // #756B85

    // MARK: - Allocation / Chart Palette
    static let acornSliceLarge  = Color(red: 0.353, green: 0.169, blue: 0.510) // #5A2B82 Oak
    static let acornSliceSmall  = Color(red: 0.435, green: 0.749, blue: 0.306) // #6FBF4E Green
    static let acornSliceIntl   = Color(red: 0.271, green: 0.761, blue: 0.690) // #45C2B0 Teal
    static let acornSliceREIT   = Color(red: 0.949, green: 0.784, blue: 0.294) // #F2C84B Gold
    static let acornSliceBonds  = Color(red: 0.949, green: 0.471, blue: 0.361) // #F2785C Coral
    static let acornSliceEM     = Color(red: 0.608, green: 0.435, blue: 0.831) // #9B6FD4 Lilac

    // MARK: - Semantic
    static let acornGain    = Color(red: 0.518, green: 0.851, blue: 0.384) // #84D962 (dark)
    static let acornLoss    = Color(red: 1.000, green: 0.420, blue: 0.420) // #FF6B6B (dark)
    static let acornWarning = Color(red: 0.949, green: 0.784, blue: 0.294) // #F2C84B (dark)
}
```

## 2. Typography

Acorns ships a rounded brand face; bundle **Nunito Sans** (SIL OFL) as the free substitute via `Info.plist` / `UIAppFonts`. Enable tabular figures on every currency value.

```swift
extension Font {
    static let acornPortfolio  = Font.custom("NunitoSans-Black",      size: 38) // w900
    static let acornHeroNumber = Font.custom("NunitoSans-Black",      size: 28)
    static let acornSection    = Font.custom("NunitoSans-ExtraBold",  size: 22)
    static let acornSubsection = Font.custom("NunitoSans-ExtraBold",  size: 18)
    static let acornBody       = Font.custom("NunitoSans-Regular",    size: 16)
    static let acornRowTitle   = Font.custom("NunitoSans-Bold",       size: 15)
    static let acornAmount     = Font.custom("NunitoSans-ExtraBold",  size: 15)
    static let acornMeta       = Font.custom("NunitoSans-SemiBold",   size: 13)
    static let acornLabel      = Font.custom("NunitoSans-Bold",       size: 12)
    static let acornButton     = Font.custom("NunitoSans-ExtraBold",  size: 16)
    static let acornTab        = Font.custom("NunitoSans-Bold",       size: 10)
    static let acornDelta      = Font.custom("NunitoSans-Bold",       size: 14)
}

// Tabular figures — apply to all money text
struct AcornTabular: ViewModifier {
    func body(content: Content) -> some View { content.monospacedDigit() }
}
extension View { func acornTabular() -> some View { modifier(AcornTabular()) } }
```

## 3. Signature Components

### Portfolio Header

```swift
struct AcornPortfolioHeader: View {
    let total: String       // "$8,142.67"
    let gain: String        // "$214.38 (2.7%)"

    var body: some View {
        VStack(spacing: 4) {
            Text(total)
                .font(.acornPortfolio).acornTabular()
                .foregroundStyle(Color.acornDarkTextPrimary)
            HStack(spacing: 4) {
                Text("▲ \(gain)")
                    .font(.acornDelta)
                    .foregroundStyle(Color.acornGain)
                Text("all time")
                    .font(.acornDelta)
                    .foregroundStyle(Color.acornDarkTextSecondary)
            }
        }
        .frame(maxWidth: .infinity)
        .padding(.vertical, 18)
    }
}
```

### Allocation Donut + Legend

```swift
struct AllocationSlice: Identifiable {
    let id = UUID()
    let name: String
    let percent: Double      // 0...1
    let value: String        // "$3,094.21"
    let color: Color
}

struct AcornDonut: View {
    let slices: [AllocationSlice]
    let styleLabel: String   // "Aggressive"
    @State private var sweep: Double = 0

    var body: some View {
        ZStack {
            ForEach(Array(cumulative().enumerated()), id: \.offset) { i, seg in
                Circle()
                    .trim(from: seg.start, to: seg.start + (seg.end - seg.start) * sweep)
                    .stroke(slices[i].color, style: StrokeStyle(lineWidth: 29, lineCap: .butt))
                    .rotationEffect(.degrees(-90))
            }
            VStack(spacing: 3) {
                Text(styleLabel.uppercased())
                    .font(.acornLabel).kerning(0.4)
                    .foregroundStyle(Color.acornDarkTextSecondary)
                Text("100% invested")
                    .font(.acornSubsection)
                    .foregroundStyle(Color.acornDarkTextPrimary)
            }
        }
        .frame(width: 168, height: 168)
        .onAppear { withAnimation(.easeOut(duration: 0.6)) { sweep = 1 } }
    }

    private func cumulative() -> [(start: Double, end: Double)] {
        var acc = 0.0
        return slices.map { s in
            let start = acc; acc += s.percent; return (start, acc)
        }
    }
}

struct AcornLegendRow: View {
    let slice: AllocationSlice
    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 4).fill(slice.color).frame(width: 12, height: 12)
            Text(slice.name).font(.acornRowTitle).foregroundStyle(Color.acornDarkTextPrimary)
            Text("\(Int(slice.percent * 100))%").font(.acornMeta).foregroundStyle(Color.acornDarkTextSecondary)
            Spacer()
            Text(slice.value).font(.acornAmount).acornTabular()
                .foregroundStyle(Color.acornDarkTextPrimary)
                .frame(width: 78, alignment: .trailing)
        }
        .padding(.vertical, 9)
    }
}
```

### Round-Ups Card

```swift
struct AcornRoundUpsCard: View {
    let amount: String       // "$4.62"
    let subtitle: String     // "Pending · invests at $5.00"

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text("ROUND-UPS")
                    .font(.acornLabel).kerning(0.3)
                    .foregroundStyle(.white.opacity(0.85))
                Spacer()
                Text("Auto")
                    .font(.system(size: 11, weight: .heavy))
                    .foregroundStyle(.white)
                    .padding(.vertical, 4).padding(.horizontal, 10)
                    .background(Capsule().fill(.white.opacity(0.16)))
            }
            Text(amount)
                .font(.acornHeroNumber)
                .foregroundStyle(.white)
                .padding(.top, 12)
            Text(subtitle)
                .font(.acornMeta)
                .foregroundStyle(.white.opacity(0.8))
                .padding(.top, 2)
        }
        .padding(18)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            RoundedRectangle(cornerRadius: 18)
                .fill(LinearGradient(colors: [Color(hex: 0x5A2B82), Color(hex: 0x7B43A8)],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
        )
        .shadow(color: Color(hex: 0x4A206A).opacity(0.4), radius: 24, x: 0, y: 10)
        .padding(.horizontal, 18)
    }
}

extension Color {
    init(hex: UInt32) {
        self.init(.sRGB,
                  red:   Double((hex >> 16) & 0xFF) / 255,
                  green: Double((hex >> 8) & 0xFF) / 255,
                  blue:  Double(hex & 0xFF) / 255)
    }
}
```

### Found Money Row

```swift
struct AcornFoundMoneyRow: View {
    let monogram: String
    let brandColor: Color
    let brand: String
    let detail: String       // "1% back · 3 days ago"
    let amount: String       // "+$3.20"

    var body: some View {
        HStack(spacing: 14) {
            Text(monogram)
                .font(.system(size: 18, weight: .black))
                .foregroundStyle(.white)
                .frame(width: 40, height: 40)
                .background(RoundedRectangle(cornerRadius: 12).fill(brandColor))
            VStack(alignment: .leading, spacing: 2) {
                Text(brand).font(.acornRowTitle).foregroundStyle(Color.acornDarkTextPrimary)
                Text(detail).font(.acornMeta).foregroundStyle(Color.acornDarkTextSecondary)
            }
            Spacer()
            Text(amount).font(.acornAmount).acornTabular()
                .foregroundStyle(Color.acornGain)
        }
        .padding(.horizontal, 22)
        .padding(.vertical, 11)
        .contentShape(Rectangle())
    }
}
```

### Pill Buttons

```swift
struct AcornPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.acornButton).foregroundStyle(Color.acornForestInk)
                .frame(maxWidth: .infinity).padding(.vertical, 15)
        }
        .background(Capsule().fill(Color.acornGreen))
        .buttonStyle(.plain)
    }
}

struct AcornSecondaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.custom("NunitoSans-ExtraBold", size: 15)).foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 26)
        }
        .background(Capsule().fill(Color.acornOak))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct AcornTabView: View {
    var body: some View {
        TabView {
            InvestView().tabItem { Label("Invest", systemImage: "chart.line.uptrend.xyaxis") }
            LaterView().tabItem { Label("Later", systemImage: "calendar") }
            SpendView().tabItem { Label("Spend", systemImage: "creditcard.fill") }
            EarnView().tabItem { Label("Earn", systemImage: "gift.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle.fill") }
        }
        .tint(.acornGreenBright)  // dark active = Green Bright; use .acornGreen on light. No tint pill.
    }
}
```

## 5. Motion

```swift
// Donut sweep on load — each segment trims in clockwise
.onAppear { withAnimation(.easeOut(duration: 0.6)) { sweep = 1 } }

// Number ticker — portfolio total / Round-Ups
.contentTransition(.numericText())
withAnimation(.easeOut(duration: 0.3)) { displayedTotal = newTotal }

// Milestone celebration — leaf/confetti burst + success haptic
.sensoryFeedback(.success, trigger: milestoneReached)

// Card press
.scaleEffect(pressed ? 0.98 : 1).animation(.easeOut(duration: 0.08), value: pressed)

// Range switch — crossfade
.transition(.opacity).animation(.easeInOut(duration: 0.25), value: range)

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: cardTapped)
.sensoryFeedback(.success, trigger: investCompleted)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Invest (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Later (tab) | `calendar` | 22pt |
| Spend (tab) | `creditcard` / `creditcard.fill` | 22pt |
| Earn (tab) | `gift` / `gift.fill` | 22pt |
| Profile (tab) | `person.crop.circle` / `.fill` | 22pt |
| Notifications | `bell` | 18pt |
| Round-Ups | `arrow.up.circle.fill` | 20pt |
| Gain | `arrowtriangle.up.fill` | 12pt |
| Loss | `arrowtriangle.down.fill` | 12pt |
| Recurring | `repeat` | 18pt |
| One-time invest | `dollarsign.circle` | 18pt |
| Found Money | `gift.fill` | 18pt |
| Goal / projection | `chart.line.uptrend.xyaxis` | 18pt |
| Back | `chevron.left` | 20pt |
| Settings | `gearshape` | 20pt |
| Info | `info.circle` | 16pt |
| Linked account | `building.columns` | 18pt |

## 7. Dark Mode

```swift
struct AcornTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.acornDarkCanvas : Color.acornCanvas)
            .foregroundStyle(scheme == .dark ? Color.acornDarkTextPrimary : Color.acornTextPrimary)
    }
}
extension View { func acornTheme() -> some View { modifier(AcornTheme()) } }
```

Dark mode is deep aubergine `#14121A`, NOT pure black — tinted from Acorns Oak. Surfaces lift to `#1E1B27`. Acorn Green `#6FBF4E` is the action/growth signal (use `#84D962` for positive text on dark for contrast). The allocation donut keeps its full warm palette; the Round-Ups card stays the Oak gradient; Found Money "+$" stays green. Only chrome (dividers, secondary text) recedes.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a manual count animation)
- Bundle Nunito Sans TTFs via `Info.plist` (`UIAppFonts`) as the rounded-brand substitute; Nunito Sans is SIL OFL licensed for free distribution
- Always enable `.monospacedDigit()` (tabular figures) on the portfolio total, allocation values, and deltas so currency aligns
- Dynamic Type: support on portfolio total, section, body, row title, meta, delta; keep tab labels and eyebrow labels ("ALL TIME") at FIXED sizes (layout-sensitive)
- VoiceOver: announce the portfolio header as "Portfolio total {amount}, up {gain} all time"; the donut as "Allocation: Large Company 38%, Small Company 26%, …" (combine slices into one summary element + per-slice legend rows); Found Money rows as "{brand}, {detail}, earned {amount}"
- Color contrast: `#1F1A2B` on `#F5F3F8` and `#F2EFF6` on `#14121A` pass WCAG AA for body; dark forest `#14302A` on Acorn Green `#6FBF4E` passes AA (do NOT use white on the bright green); validate the gold donut slice's adjacent labels with a contrast checker
- Reduce Motion: disable the donut sweep (render final segments), the number ticker (set value directly), and the milestone confetti (show a static badge)
- Reduce Transparency: replace the tab-bar blur with a solid `#FFFFFF` / `#14121A` fill
- Dark mode: colors invert per the `acornDark*` palette; deep aubergine not black; add a 1pt `acornDarkDivider` border to cards/sheets as an elevation cue where shadows read weakly
