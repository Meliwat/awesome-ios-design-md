# Flo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Flo's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let floCanvas        = Color.white                                  // #FFFFFF
    static let floBlush         = Color(red: 1.000, green: 0.941, blue: 0.953) // #FFF0F3
    static let floSurfaceSunken = Color(red: 0.984, green: 0.914, blue: 0.929) // #FBE9ED
    static let floDivider       = Color(red: 0.953, green: 0.867, blue: 0.890) // #F3DDE3

    // MARK: - Text
    static let floTextPrimary   = Color(red: 0.102, green: 0.102, blue: 0.180) // #1A1A2E
    static let floTextSecondary = Color(red: 0.431, green: 0.416, blue: 0.510) // #6E6A82
    static let floTextTertiary  = Color(red: 0.627, green: 0.612, blue: 0.690) // #A09CB0

    // MARK: - Coral (lead accent)
    static let floCoral        = Color(red: 1.000, green: 0.420, blue: 0.506) // #FF6B81
    static let floCoralPressed = Color(red: 0.910, green: 0.345, blue: 0.439) // #E85870
    static let floCoralSoft    = Color(red: 1.000, green: 0.851, blue: 0.875) // #FFD9DF

    // MARK: - Lavender (phase partner)
    static let floLavender     = Color(red: 0.773, green: 0.702, blue: 0.902) // #C5B3E6
    static let floLavenderDeep = Color(red: 0.659, green: 0.576, blue: 0.839) // #A893D6
    static let floLavenderSoft = Color(red: 0.929, green: 0.902, blue: 0.969) // #EDE6F7

    // MARK: - Semantic (soft)
    static let floGentlePositive = Color(red: 0.490, green: 0.788, blue: 0.659) // #7DC9A8
    static let floGentleAlert    = Color(red: 0.949, green: 0.702, blue: 0.420) // #F2B36B
}
```

## 2. Typography

Inter is open-source (SIL OFL). Bundle the TTFs via `Info.plist` (`UIAppFonts`). Use tabular figures on the wheel center and calendar.

```swift
extension Font {
    static let floWheelCenter = Font.custom("Inter-Bold",     size: 30)
    static let floGreeting    = Font.custom("Inter-Bold",     size: 28)
    static let floScreenTitle = Font.custom("Inter-Bold",     size: 24)
    static let floSection     = Font.custom("Inter-Bold",     size: 22)
    static let floCardTitle   = Font.custom("Inter-SemiBold", size: 18)
    static let floPrediction  = Font.custom("Inter-Bold",     size: 20)
    static let floBody        = Font.custom("Inter-Regular",  size: 16)
    static let floChipLabel   = Font.custom("Inter-SemiBold", size: 15)
    static let floSubtitle    = Font.custom("Inter-Regular",  size: 14)
    static let floButton      = Font.custom("Inter-SemiBold", size: 16)
    static let floCalDay      = Font.custom("Inter-SemiBold", size: 15)
    static let floMeta        = Font.custom("Inter-Medium",   size: 13)
    static let floTab         = Font.custom("Inter-SemiBold", size: 11)
    static let floLabelUpper  = Font.custom("Inter-Bold",     size: 12)
}

extension View {
    func floTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Cycle Wheel (signature)

```swift
struct CyclePhase { let fraction: ClosedRange<Double>; let color: Color }

struct CycleWheel: View {
    let cycleDay: Int
    let phaseName: String
    let phases: [CyclePhase]   // colored segments around the ring
    let todayFraction: Double  // 0...1 position of "today" on the ring
    let onLog: () -> Void

    @State private var sweep: CGFloat = 0
    @State private var centerScale: CGFloat = 0.9
    @State private var centerOpacity: Double = 0

    var body: some View {
        VStack(spacing: 16) {
            ZStack {
                Circle().stroke(Color.floDivider, lineWidth: 14)
                ForEach(phases.indices, id: \.self) { i in
                    Circle()
                        .trim(from: phases[i].fraction.lowerBound,
                              to: phases[i].fraction.upperBound * Double(sweep))
                        .stroke(phases[i].color,
                                style: StrokeStyle(lineWidth: 14, lineCap: .round))
                        .rotationEffect(.degrees(-90))
                }
                // "Today" dot
                Circle().fill(Color.floCoral)
                    .frame(width: 16, height: 16)
                    .offset(y: -130)
                    .rotationEffect(.degrees(360 * todayFraction))
                VStack(spacing: 4) {
                    Text("Day \(cycleDay)")
                        .font(.floWheelCenter).foregroundStyle(Color.floTextPrimary)
                        .floTabular()
                    Text(phaseName)
                        .font(.floSubtitle).foregroundStyle(Color.floTextSecondary)
                }
                .scaleEffect(centerScale).opacity(centerOpacity)
            }
            .frame(width: 260, height: 260)

            Button(action: onLog) {
                Text("Log period")
                    .font(.floButton).foregroundStyle(.white)
                    .padding(.vertical, 14).padding(.horizontal, 32)
                    .background(Capsule().fill(Color.floCoral))
            }
            .buttonStyle(FloPressableStyle())
        }
        .onAppear {
            withAnimation(.easeOut(duration: 0.7)) {
                sweep = 1; centerScale = 1; centerOpacity = 1
            }
        }
    }
}

struct FloPressableStyle: ButtonStyle {
    var scale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? scale : 1)
            .animation(.easeOut(duration: 0.2), value: configuration.isPressed)
    }
}
```

### Prediction Card (signature)

```swift
struct PredictionCard: View {
    let label: String
    let emphasis: String
    let sub: String

    var body: some View {
        HStack(alignment: .top, spacing: 14) {
            Image(systemName: "drop.fill")
                .font(.system(size: 28)).foregroundStyle(Color.floCoral)
            VStack(alignment: .leading, spacing: 6) {
                Text(label.uppercased())
                    .font(.floLabelUpper).tracking(0.8)
                    .foregroundStyle(Color.floTextSecondary)
                Text(emphasis)
                    .font(.floPrediction).foregroundStyle(Color.floCoral)
                Text(sub)
                    .font(.floSubtitle).foregroundStyle(Color.floTextSecondary)
            }
            Spacer()
        }
        .padding(20)
        .background(RoundedRectangle(cornerRadius: 20).fill(Color.floBlush))
        .shadow(color: Color.floCoral.opacity(0.10), radius: 20, y: 6)
    }
}
```

### Symptom Log Chip

```swift
struct SymptomChip: View {
    let title: String
    let glyph: String?
    @Binding var selected: Bool

    var body: some View {
        Button {
            withAnimation(.easeOut(duration: 0.2)) { selected.toggle() }
        } label: {
            HStack(spacing: 8) {
                if let g = glyph {
                    Image(systemName: g).font(.system(size: 18))
                }
                Text(title).font(.floChipLabel)
            }
            .foregroundStyle(selected ? Color.floCoralPressed : Color.floTextPrimary)
            .padding(.vertical, 10).padding(.horizontal, 16)
            .background(Capsule().fill(selected ? Color.floCoralSoft : Color.floBlush))
            .overlay(
                Capsule().strokeBorder(selected ? Color.floCoral : .clear, lineWidth: 1.5)
            )
        }
        .sensoryFeedback(.selection, trigger: selected)
        .buttonStyle(FloPressableStyle(scale: 0.96))
    }
}
```

### Calendar Day Cell

```swift
enum DayState { case ordinary, predictedPeriod, loggedPeriod, fertile, ovulation }

struct CalendarDayCell: View {
    let day: Int
    let state: DayState
    let isToday: Bool

    var body: some View {
        ZStack {
            switch state {
            case .loggedPeriod:
                Circle().fill(Color.floCoralPressed)
            case .predictedPeriod:
                Circle().strokeBorder(Color.floCoral, lineWidth: 1.5)
            case .fertile:
                Circle().fill(Color.floLavenderSoft)
            case .ovulation:
                Circle().strokeBorder(Color.floLavenderDeep, lineWidth: 2)
            case .ordinary:
                EmptyView()
            }
            Text("\(day)")
                .font(.floCalDay).floTabular()
                .foregroundStyle(textColor)
            if isToday {
                Circle().fill(Color.floCoral)
                    .frame(width: 5, height: 5)
                    .offset(y: 14)
            }
        }
        .frame(width: 40, height: 40)
    }

    private var textColor: Color {
        switch state {
        case .loggedPeriod: .white
        case .predictedPeriod: .floCoral
        default: .floTextPrimary
        }
    }
}
```

### Primary / Soft Buttons

```swift
struct FloPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.floButton).foregroundStyle(.white)
                .padding(.vertical, 16).padding(.horizontal, 32)
                .frame(maxWidth: .infinity)
                .background(Capsule().fill(Color.floCoral))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: title)
        .buttonStyle(FloPressableStyle())
    }
}

struct FloSoftButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.floButton).foregroundStyle(Color.floCoralPressed)
                .padding(.vertical, 14).padding(.horizontal, 28)
                .background(Capsule().fill(Color.floCoralSoft))
        }
        .buttonStyle(FloPressableStyle())
    }
}
```

## 4. Tab Bar

`TabView` with a custom `UITabBarAppearance` — a near-opaque white glass with a soft pink hairline. **Active tint is Flo Coral.**

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterialLight)
        a.backgroundColor = UIColor.white.withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            TodayView().tabItem { Label("Today", systemImage: "circle.circle.fill") }
            CalendarView().tabItem { Label("Calendar", systemImage: "calendar") }
            InsightsView().tabItem { Label("Insights", systemImage: "lightbulb.fill") }
            PartnerView().tabItem { Label("Partner", systemImage: "heart.circle.fill") }
            MoreView().tabItem { Label("More", systemImage: "ellipsis") }
        }
        .tint(.floCoral)
    }
}
```

## 5. Motion

```swift
// Wheel reveal — ring sweeps, center fades+scales up (see CycleWheel.onAppear): 700ms ease-out

// Wheel phase morph — crossfade segment colors when the cycle advances
withAnimation(.easeInOut(duration: 0.3)) { phases = newPhases }

// Chip select — fill animates blush → coral-soft over 200ms (see SymptomChip)

// Card tap
.buttonStyle(FloPressableStyle())  // 200ms ease-out, scale 0.98

// Calendar day select — cell scales ~1.08 with a soft shadow bloom
withAnimation(.easeOut(duration: 0.2)) { selectedDay = day }

// Log success — soft sage check + gentle haptic, wheel morphs to new state
.sensoryFeedback(.success, trigger: didLog)
```

Haptics via `.sensoryFeedback` (iOS 17+): `.impact(flexibility: .soft)` on primary CTA, `.selection` on chip toggle, `.success` on log saved. Keep haptics gentle — Flo is calm.

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Period / flow | `drop.fill` | 28pt |
| Fertile / nature | `leaf.fill` | 24pt |
| Ovulation | `sparkle` | 20pt |
| Mood | `face.smiling` | 18pt |
| Symptom (generic) | `heart.text.square` | 18pt |
| Add / log | `plus` | 20pt |
| Edit | `pencil` | 20pt |
| Info | `info.circle` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| Search | `magnifyingglass` | 16pt |
| Today (tab) | `circle.circle.fill` | 24pt |
| Calendar (tab) | `calendar` | 24pt |
| Insights (tab) | `lightbulb.fill` | 24pt |
| Partner (tab) | `heart.circle.fill` | 24pt |
| More (tab) | `ellipsis` | 24pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` needs iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Tabular figures: register Inter with the tabular numeral feature or rely on `.monospacedDigit()` so the wheel center and calendar grid never reflow
- Dynamic Type: scale greetings, titles, body, card titles; cap the wheel center figure (~34pt) so it fits inside the wheel; pin calendar day numbers, tab labels, captions
- VoiceOver: the cycle wheel needs a combined label — "Cycle day 14, follicular phase, period in 5 days"; calendar cells announce state ("May 19, predicted period"); never communicate phase by color alone — pair with text/legend
- Color is reassurance, not the only signal: the phase legend and text labels must decode coral/lavender for color-blind users
- Reduce Motion: replace the wheel sweep + center scale with an immediate static render; disable the phase crossfade morph (instant swap)
- Sensitive content: respect an app-lock / hide-on-background setting — Flo content is private; blur or cover the screen in the app switcher when enabled
- Contrast: `#6E6A82` on white passes AA at 14pt+; coral text `#FF6B81` on white is borderline for small text — use `#E85870` for any sub-16pt coral copy
- Light-first: a dark mode exists for OS parity but the light blush experience is primary
