# Hopper (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hopper's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let hopCanvas         = Color.white                                   // #FFFFFF
    static let hopSurface        = Color(red: 0.961, green: 0.961, blue: 0.969)  // #F5F5F7
    static let hopDivider        = Color(red: 0.898, green: 0.898, blue: 0.918)  // #E5E5EA
    static let hopSurfacePressed = Color(red: 0.922, green: 0.922, blue: 0.941)  // #EBEBF0

    // MARK: - Text
    static let hopTextPrimary    = Color(red: 0.114, green: 0.114, blue: 0.122)  // #1D1D1F
    static let hopTextSecondary  = Color(red: 0.431, green: 0.431, blue: 0.451)  // #6E6E73
    static let hopTextTertiary   = Color(red: 0.631, green: 0.631, blue: 0.651)  // #A1A1A6

    // MARK: - Brand
    static let hopRed            = Color(red: 0.980, green: 0.278, blue: 0.278)  // #FA4747
    static let hopRedPressed     = Color(red: 0.878, green: 0.227, blue: 0.227)  // #E03A3A
    static let hopBuyGreen       = Color(red: 0.204, green: 0.780, blue: 0.349)  // #34C759

    // MARK: - Heatmap (prediction scale)
    static let hopBuyBest   = Color(red: 0.204, green: 0.780, blue: 0.349)       // #34C759
    static let hopBuyGood   = Color(red: 0.659, green: 0.902, blue: 0.722)       // #A8E6B8
    static let hopNeutral   = Color(red: 0.961, green: 0.961, blue: 0.969)       // #F5F5F7
    static let hopWaitHigh  = Color(red: 1.000, green: 0.780, blue: 0.780)       // #FFC7C7
    static let hopWaitWorst = Color(red: 0.980, green: 0.278, blue: 0.278)       // #FA4747

    // MARK: - Semantic
    static let hopPriceDrop = Color(red: 0.204, green: 0.780, blue: 0.349)       // #34C759
    static let hopErrorRed  = Color(red: 0.843, green: 0.000, blue: 0.082)       // #D70015
}
```

## 2. Typography

Hopper's geometric face is proprietary. Bundle TTFs via `Info.plist` (`UIAppFonts`) or substitute Inter / fall back to `.system(design: .rounded)` for the friendly tone.

```swift
extension Font {
    static let hopPrice      = Font.custom("Inter-Bold",     size: 28).weight(.bold)
    static let hopTitleLarge = Font.custom("Inter-Bold",     size: 28).weight(.bold)
    static let hopSection    = Font.custom("Inter-Bold",     size: 22).weight(.bold)
    static let hopRoute      = Font.custom("Inter-Bold",     size: 17).weight(.bold)
    static let hopCardTitle  = Font.custom("Inter-SemiBold", size: 16).weight(.semibold)
    static let hopBody       = Font.custom("Inter-Regular",  size: 15).weight(.regular)
    static let hopButton     = Font.custom("Inter-Bold",     size: 16).weight(.bold)
    static let hopVerdict    = Font.custom("Inter-Bold",     size: 13).weight(.bold)
    static let hopMeta       = Font.custom("Inter-Regular",  size: 13).weight(.regular)
    static let hopCalDay     = Font.custom("Inter-SemiBold", size: 14).weight(.semibold)
    static let hopCalPrice   = Font.custom("Inter-SemiBold", size: 10).weight(.semibold)
    static let hopTab        = Font.custom("Inter-SemiBold", size: 11).weight(.semibold)
}

extension Font {
    static func hop(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .rounded)
    }
}
```

## 3. Signature Components

### Price-Prediction Calendar Heatmap

```swift
enum PricePrediction { case buyBest, buyGood, neutral, waitHigh, waitWorst

    var fill: Color {
        switch self {
        case .buyBest:   .hopBuyBest
        case .buyGood:   .hopBuyGood
        case .neutral:   .hopNeutral
        case .waitHigh:  .hopWaitHigh
        case .waitWorst: .hopWaitWorst
        }
    }
    var textColor: Color {
        switch self {
        case .buyBest, .waitWorst: .white
        default: .hopTextPrimary
        }
    }
}

struct CalendarDayCell: View {
    let day: Int
    let price: String
    let prediction: PricePrediction
    let isSelected: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            VStack(spacing: 2) {
                Text("\(day)").font(.hopCalDay)
                Text(price).font(.hopCalPrice).opacity(0.85)
            }
            .foregroundStyle(prediction.textColor)
            .frame(maxWidth: .infinity)
            .frame(height: 44)
            .background(RoundedRectangle(cornerRadius: 8).fill(prediction.fill))
            .overlay(
                RoundedRectangle(cornerRadius: 8)
                    .strokeBorder(isSelected ? Color.hopTextPrimary : .clear, lineWidth: 2)
            )
        }
        .sensoryFeedback(.impact(weight: .light), trigger: isSelected)
        .buttonStyle(HopCellStyle())
    }
}

struct HopCellStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 1.05 : 1)
            .animation(.spring(response: 0.2, dampingFraction: 0.6), value: configuration.isPressed)
    }
}

// Legend strip: a green→red gradient bar above the grid
struct HeatmapLegend: View {
    var body: some View {
        VStack(spacing: 4) {
            LinearGradient(colors: [.hopBuyBest, .hopBuyGood, .hopNeutral, .hopWaitHigh, .hopWaitWorst],
                           startPoint: .leading, endPoint: .trailing)
                .frame(height: 6)
                .clipShape(Capsule())
            HStack {
                Text("Buy").font(.hopMeta).foregroundStyle(.hopBuyGreen)
                Spacer()
                Text("Wait").font(.hopMeta).foregroundStyle(.hopRed)
            }
        }
    }
}
```

### Fare Verdict Pill

```swift
struct VerdictPill: View {
    enum Verdict { case buy, wait }
    let verdict: Verdict
    var detail: String? = nil

    var body: some View {
        HStack(spacing: 4) {
            Text(verdict == .buy ? "BUY" : "WAIT")
            if let detail { Text("· \(detail)").font(.hopMeta).foregroundStyle(.white.opacity(0.9)) }
        }
        .font(.hopVerdict)
        .foregroundStyle(.white)
        .padding(.vertical, 5)
        .padding(.horizontal, 12)
        .background(Capsule().fill(verdict == .buy ? Color.hopBuyGreen : Color.hopRed))
    }
}
```

### Fare Card

```swift
struct FareCard: View {
    let route: String
    let airline: String
    let duration: String
    let price: String
    let verdict: VerdictPill.Verdict
    let confidence: Int    // 0...100

    var body: some View {
        VStack(alignment: .leading, spacing: 14) {
            VStack(alignment: .leading, spacing: 4) {
                Text(route).font(.hopRoute).foregroundStyle(.hopTextPrimary)
                Text("\(airline) · \(duration)").font(.hopMeta).foregroundStyle(.hopTextSecondary)
            }
            HStack(alignment: .firstTextBaseline, spacing: 10) {
                Text(price).font(.hopPrice).foregroundStyle(.hopTextPrimary)
                VerdictPill(verdict: verdict, detail: verdict == .buy ? "won't drop" : "cheaper soon")
            }
            ConfidenceBar(percent: confidence, verdict: verdict)
            HopPrimaryButton(title: "Watch this trip") {}
        }
        .padding(20)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.hopCanvas))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.hopDivider, lineWidth: 1))
        .shadow(color: .black.opacity(0.06), radius: 16, y: 4)
    }
}

struct ConfidenceBar: View {
    let percent: Int
    let verdict: VerdictPill.Verdict
    var body: some View {
        let filled = Int((Double(percent) / 100.0 * 4).rounded())
        VStack(alignment: .leading, spacing: 6) {
            HStack(spacing: 4) {
                ForEach(0..<4) { i in
                    Capsule()
                        .fill(i < filled ? (verdict == .buy ? Color.hopBuyGreen : Color.hopRed) : Color.hopDivider)
                        .frame(height: 6)
                }
            }
            Text("Hopper is \(percent)% confident").font(.hopMeta).foregroundStyle(.hopTextSecondary)
        }
    }
}
```

### Watch-Price Toggle

```swift
struct WatchToggle: View {
    @Binding var isWatching: Bool
    var body: some View {
        Button {
            withAnimation(.spring(response: 0.22, dampingFraction: 0.7)) { isWatching.toggle() }
        } label: {
            ZStack(alignment: isWatching ? .trailing : .leading) {
                Capsule().fill(isWatching ? Color.hopRed : Color.hopDivider)
                    .frame(width: 52, height: 32)
                Circle().fill(.white)
                    .frame(width: 28, height: 28)
                    .shadow(color: .black.opacity(0.12), radius: 4, y: 2)
                    .overlay(
                        Image(systemName: "bell.fill")
                            .font(.system(size: 11))
                            .foregroundStyle(Color.hopRed)
                            .opacity(isWatching ? 1 : 0)
                    )
                    .padding(2)
            }
        }
        .sensoryFeedback(.success, trigger: isWatching)
    }
}
```

### Primary Button

```swift
struct HopPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.hopButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 16)
                .background(RoundedRectangle(cornerRadius: 14).fill(Color.hopRed))
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: title)
        .buttonStyle(HopPressableStyle())
    }
}

struct HopPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = UIColor.white.withAlphaComponent(0.96)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            WatchesView().tabItem { Label("Watches", systemImage: "bell.fill") }
            TripsView().tabItem { Label("Trips", systemImage: "suitcase.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.fill") }
        }
        .tint(.hopRed) // active = Hopper Red
    }
}
```

## 5. Motion

```swift
// Calendar date select — cell pop + light haptic (see CalendarDayCell)
.sensoryFeedback(.impact(weight: .light), trigger: selectedDay)

// Heatmap color shift on month/filter change — animate the per-cell fill
withAnimation(.easeInOut(duration: 0.3)) { predictions = newPredictions }

// Watch toggle arm — spring + success haptic (see WatchToggle)
.sensoryFeedback(.success, trigger: isWatching)

// Price-drop celebration — present a sheet, tick a counter with a TimelineView, bunny waves
// Verdict pill — scale-in when a new prediction loads
.scaleEffect(verdictAppeared ? 1 : 0.94)
.animation(.spring(response: 0.3, dampingFraction: 0.6), value: verdictAppeared)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Watch / bell | `bell` / `bell.fill` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Swap route | `arrow.up.arrow.down` | 18pt |
| Calendar | `calendar` | 20pt |
| Plane | `airplane` | 18pt |
| Price drop | `arrow.down` | 14pt |
| Prediction info | `info.circle` | 18pt |
| Back | `chevron.left` | 20pt |
| Home (tab) | `house.fill` | 24pt |
| Watches (tab) | `bell.fill` | 24pt |
| Trips (tab) | `suitcase.fill` | 24pt |
| Profile (tab) | `person.fill` | 24pt |
| Confident check | `checkmark.seal.fill` | 16pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on predicted price, route titles, body — pin calendar day/price labels and verdict pill (layout-sensitive grid) and tab labels
- VoiceOver: never rely on color alone for the verdict — set `.accessibilityLabel("Buy now, prices won't drop, 87% confident")` on the verdict block; announce each calendar cell as `"\(day), predicted \(price), good price to buy"` / `"…, expensive, wait"`
- Watch toggle: `.accessibilityLabel("Watch price")` + `.accessibilityValue(isWatching ? "On" : "Off")` with the switch trait
- Contrast: `#6E6E73` on `#FFFFFF` passes WCAG AA at 13pt+. Light heatmap tints (`#A8E6B8`, `#FFC7C7`) keep dark text; strong fills (`#34C759`, `#FA4747`) use white — verify both
- Color-blind safety: pair the heatmap with the explicit per-cell price and the textual "BUY"/"WAIT" label so red/green is never the only signal
