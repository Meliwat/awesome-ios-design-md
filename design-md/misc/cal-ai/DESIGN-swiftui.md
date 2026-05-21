# Cal AI (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Cal AI's visual language into paste-ready SwiftUI code.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // Canvas & surfaces (dark mode — primary)
    static let calCanvas       = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let calSurface1     = Color(red: 0.082, green: 0.082, blue: 0.082) // #151515
    static let calSurface2     = Color(red: 0.118, green: 0.118, blue: 0.118) // #1E1E1E
    static let calSurface3     = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let calDivider      = Color(red: 0.145, green: 0.145, blue: 0.145) // #252525

    // Text
    static let calTextPrimary   = Color.white                                   // #FFFFFF
    static let calTextSecondary = Color(red: 0.631, green: 0.631, blue: 0.631) // #A1A1A1
    static let calTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E

    // Macro accents (desaturated trio)
    static let calProtein  = Color(red: 0.302, green: 0.659, blue: 1.0)   // #4DA8FF
    static let calCarbs    = Color(red: 1.0,   green: 0.710, blue: 0.298) // #FFB54C
    static let calFat      = Color(red: 1.0,   green: 0.431, blue: 0.529) // #FF6E87

    // Semantic
    static let calSuccess     = Color(red: 0.259, green: 0.882, blue: 0.490) // #42E17D
    static let calWarning     = Color(red: 1.0,   green: 0.710, blue: 0.298) // #FFB54C (same as carbs)
    static let calDestructive = Color(red: 1.0,   green: 0.353, blue: 0.353) // #FF5A5A
    static let calAIAccent    = Color(red: 0.545, green: 0.553, blue: 0.961) // #8B8DF5

    // Light mode (secondary — rare)
    static let calLightCanvas     = Color.white
    static let calLightSurface1   = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let calLightText       = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let calLightSecondary  = Color(red: 0.369, green: 0.369, blue: 0.369) // #5E5E5E
}
```

## 2. Typography

Inter can be bundled via `expo-font`-style registration or used via `Font.system(.default)` as the SF Pro fallback (which is Cal AI's visual sibling).

```swift
extension Font {
    static let calHero        = Font.system(size: 72, weight: .bold, design: .default).monospacedDigit()
    static let calMealCals    = Font.system(size: 40, weight: .bold, design: .default).monospacedDigit()
    static let calStreak      = Font.system(size: 48, weight: .bold, design: .default).monospacedDigit()
    static let calScreenTitle = Font.system(size: 28, weight: .bold, design: .default)
    static let calSection     = Font.system(size: 22, weight: .bold, design: .default)
    static let calNav         = Font.system(size: 17, weight: .semibold, design: .default)
    static let calCardTitle   = Font.system(size: 17, weight: .semibold, design: .default)
    static let calBody        = Font.system(size: 15, weight: .regular, design: .default)
    static let calMacroChip   = Font.system(size: 12, weight: .semibold, design: .default).monospacedDigit()
    static let calMeta        = Font.system(size: 13, weight: .medium, design: .default).monospacedDigit()
    static let calLabelUpper  = Font.system(size: 11, weight: .bold, design: .default)
    static let calButton      = Font.system(size: 16, weight: .semibold, design: .default)
    static let calTab         = Font.system(size: 10, weight: .semibold, design: .default)
}

// Note: .monospacedDigit() enables tabular numerals — CRITICAL for calorie/macro displays
```

## 3. Signature Components

### Primary CTA (White Pill)

```swift
struct CalPrimaryButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.calButton)
                .foregroundStyle(.black) // black on white — intentional
                .padding(.vertical, 16)
                .padding(.horizontal, 28)
                .frame(maxWidth: .infinity)
                .background(Capsule().fill(Color.white))
        }
        .buttonStyle(CalPressableStyle())
    }
}

struct CalPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Capture FAB (the signature element)

```swift
struct CaptureFAB: View {
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Circle()
                .fill(Color.white)
                .frame(width: 68, height: 68)
                .overlay(
                    Circle()
                        .strokeBorder(Color.calCanvas, lineWidth: 2)
                        .padding(4)
                )
                .shadow(color: .white.opacity(0.15), radius: 40)
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .buttonStyle(CalPressableStyle(pressedScale: 0.92))
    }
}
```

### Macro Ring

```swift
struct MacroRing: View {
    enum Macro {
        case protein, carbs, fat
        var color: Color {
            switch self {
            case .protein: return .calProtein
            case .carbs:   return .calCarbs
            case .fat:     return .calFat
            }
        }
        var label: String {
            switch self {
            case .protein: return "PROTEIN"
            case .carbs:   return "CARBS"
            case .fat:     return "FAT"
            }
        }
    }

    let macro: Macro
    let current: Double   // grams consumed
    let target: Double    // grams target
    var size: CGFloat = 80

    private var progress: Double { min(current / max(target, 0.001), 1.0) }

    var body: some View {
        VStack(spacing: 6) {
            ZStack {
                Circle()
                    .stroke(Color.calSurface3, lineWidth: 6)
                Circle()
                    .trim(from: 0, to: progress)
                    .stroke(macro.color, style: StrokeStyle(lineWidth: 6, lineCap: .round))
                    .rotationEffect(.degrees(-90))
                    .animation(.easeOut(duration: 0.6), value: progress)
                VStack(spacing: 1) {
                    HStack(alignment: .firstTextBaseline, spacing: 1) {
                        Text("\(Int(current))")
                            .font(.system(size: 18, weight: .bold).monospacedDigit())
                            .foregroundStyle(.white)
                        Text("g")
                            .font(.system(size: 9, weight: .medium))
                            .foregroundStyle(.calTextSecondary)
                    }
                }
            }
            .frame(width: size, height: size)
            Text(macro.label)
                .font(.calLabelUpper)
                .foregroundStyle(.calTextSecondary)
                .tracking(0.8)
        }
    }
}
```

### Today Hero Card

```swift
struct TodayHeroCard: View {
    let remaining: Int
    let goal: Int
    let protein: (Double, Double)
    let carbs:   (Double, Double)
    let fat:     (Double, Double)

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text("TODAY · REMAINING")
                .font(.calLabelUpper)
                .foregroundStyle(.calTextSecondary)
                .tracking(0.8)
                .padding(.bottom, 6)
            Text(remaining.formatted())
                .font(.calHero)
                .foregroundStyle(.white)
                .tracking(-1.5)
                .padding(.bottom, 4)
            Text("kcal of \(goal.formatted()) goal")
                .font(.system(size: 13, weight: .medium))
                .foregroundStyle(.calTextSecondary)
                .padding(.bottom, 20)

            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.calSurface3)
                    Capsule().fill(Color.white).frame(width: geo.size.width * progressFill)
                }
            }
            .frame(height: 6)
            .padding(.bottom, 20)

            HStack(spacing: 8) {
                MacroRing(macro: .protein, current: protein.0, target: protein.1)
                    .frame(maxWidth: .infinity)
                MacroRing(macro: .carbs, current: carbs.0, target: carbs.1)
                    .frame(maxWidth: .infinity)
                MacroRing(macro: .fat, current: fat.0, target: fat.1)
                    .frame(maxWidth: .infinity)
            }
        }
        .padding(24)
        .background(
            RoundedRectangle(cornerRadius: 24, style: .continuous)
                .fill(Color.calSurface1)
        )
    }

    private var progressFill: Double {
        let consumed = Double(goal - remaining)
        return min(consumed / Double(goal), 1.0)
    }
}
```

### AI Detection Card

```swift
struct AIDetectionCard: View {
    let title: String
    let items: [(name: String, confidence: Int)]
    let onSave: () -> Void
    let onReject: () -> Void

    @State private var shimmer = false

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(spacing: 4) {
                Image(systemName: "sparkles")
                    .font(.system(size: 11, weight: .bold))
                Text("AI DETECTED")
                    .font(.calLabelUpper)
                    .tracking(0.6)
            }
            .foregroundStyle(.calAIAccent)

            Text(title)
                .font(.calCardTitle)
                .foregroundStyle(.white)

            VStack(alignment: .leading, spacing: 4) {
                ForEach(items, id: \.name) { item in
                    HStack(spacing: 4) {
                        Text(item.name)
                            .foregroundStyle(.calTextSecondary)
                        Text("\(item.confidence)%")
                            .font(.system(size: 11, weight: .regular))
                            .foregroundStyle(.calTextTertiary)
                    }
                    .font(.system(size: 13, weight: .medium))
                }
            }

            HStack(spacing: 10) {
                Button(action: onSave) {
                    Text("Save to today")
                        .font(.system(size: 14, weight: .semibold))
                        .foregroundStyle(.black)
                        .padding(.vertical, 12)
                        .padding(.horizontal, 20)
                        .background(Capsule().fill(Color.white))
                }
                Button("Reject", action: onReject)
                    .font(.system(size: 14, weight: .semibold))
                    .foregroundStyle(.calDestructive)
            }
        }
        .padding(16)
        .background(
            RoundedRectangle(cornerRadius: 20, style: .continuous)
                .fill(
                    LinearGradient(
                        colors: [.calSurface2, .calSurface3],
                        startPoint: .topLeading,
                        endPoint: .bottomTrailing
                    )
                )
                .overlay(alignment: .top) {
                    LinearGradient(
                        colors: [.calProtein, .calAIAccent, .calFat],
                        startPoint: .leading, endPoint: .trailing
                    )
                    .frame(height: 2)
                    .clipShape(RoundedRectangle(cornerRadius: 20, style: .continuous))
                }
        )
    }
}
```

### Meal Card (Photo + Stats)

```swift
struct MealCard: View {
    let title: String
    let time: String
    let servings: String
    let protein: Int
    let carbs: Int
    let fat: Int
    let calories: Int
    let photo: Image

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            photo
                .resizable()
                .aspectRatio(16/10, contentMode: .fill)
                .clipped()
                .overlay(alignment: .bottom) {
                    LinearGradient(
                        colors: [.clear, .black.opacity(0.4)],
                        startPoint: .top, endPoint: .bottom
                    )
                    .frame(height: 60)
                }
                .clipShape(
                    UnevenRoundedRectangle(
                        topLeadingRadius: 20,
                        bottomLeadingRadius: 0,
                        bottomTrailingRadius: 0,
                        topTrailingRadius: 20
                    )
                )

            VStack(alignment: .leading, spacing: 10) {
                VStack(alignment: .leading, spacing: 2) {
                    Text(title).font(.calCardTitle)
                    Text("\(time) · \(servings)")
                        .font(.system(size: 12, weight: .medium).monospacedDigit())
                        .foregroundStyle(.calTextSecondary)
                }
                HStack {
                    HStack(spacing: 6) {
                        MacroChip(label: "P", value: protein, color: .calProtein)
                        MacroChip(label: "C", value: carbs,   color: .calCarbs)
                        MacroChip(label: "F", value: fat,     color: .calFat)
                    }
                    Spacer()
                    Text("\(calories)")
                        .font(.calMealCals)
                        .foregroundStyle(.white)
                }
            }
            .padding(16)
        }
        .background(
            RoundedRectangle(cornerRadius: 20, style: .continuous)
                .fill(Color.calSurface1)
        )
    }
}

struct MacroChip: View {
    let label: String
    let value: Int
    let color: Color
    var body: some View {
        Text("\(label) \(value)")
            .font(.calMacroChip)
            .foregroundStyle(color)
            .padding(.vertical, 3)
            .padding(.horizontal, 8)
            .background(Capsule().fill(color.opacity(0.18)))
    }
}
```

## 4. Tab Bar (with centered Capture FAB)

Because SwiftUI's `TabView` doesn't natively support a raised middle button, use a custom tab bar overlay or a `ZStack`.

```swift
struct RootTabView: View {
    @State private var selection = 0
    @State private var showCamera = false

    var body: some View {
        ZStack(alignment: .bottom) {
            TabView(selection: $selection) {
                HomeView()     .tabItem { Label("Home", systemImage: "house") }.tag(0)
                HistoryView()  .tabItem { Label("History", systemImage: "calendar") }.tag(1)
                Color.clear    .tabItem { Label("", systemImage: "") }.tag(2) // reserved slot
                InsightsView() .tabItem { Label("Insights", systemImage: "chart.line.uptrend.xyaxis") }.tag(3)
                ProfileView()  .tabItem { Label("Profile", systemImage: "person") }.tag(4)
            }
            .tint(.white)
            .toolbarBackground(Color.calCanvas.opacity(0.92), for: .tabBar)

            CaptureFAB { showCamera = true }
                .offset(y: -10)
        }
        .fullScreenCover(isPresented: $showCamera) {
            CaptureScreen(onCapture: { /* AI flow */ })
        }
    }
}
```

## 5. Motion & Haptics

```swift
// Capture button: medium haptic on tap
.sensoryFeedback(.impact(weight: .medium), trigger: captureCount)

// AI result accepted: success haptic
.sensoryFeedback(.success, trigger: savedCount)

// Streak milestone: success + scale pulse
.scaleEffect(flameScale)
.animation(.spring(response: 0.6, dampingFraction: 0.5), value: flameScale)

// AI processing shimmer (2pt gradient bar sweeping across)
struct AIProcessingBar: View {
    @State private var x: CGFloat = -1
    var body: some View {
        Rectangle()
            .fill(LinearGradient(colors: [.calProtein, .calAIAccent, .calFat],
                                 startPoint: .leading, endPoint: .trailing))
            .frame(height: 2)
            .mask(
                Rectangle()
                    .frame(width: 80)
                    .offset(x: x * 300)
            )
            .onAppear {
                withAnimation(.linear(duration: 1.2).repeatForever(autoreverses: false)) {
                    x = 2
                }
            }
    }
}
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Capture | `camera.fill` or custom ring | 28pt |
| AI | `sparkles` | 11pt |
| Home (tab) | `house` / `house.fill` | 22pt |
| History (tab) | `calendar` | 22pt |
| Insights (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Profile (tab) | `person` / `person.fill` | 22pt |
| Streak | `flame.fill` | 32pt |
| Add | `plus` | 16pt |
| Edit | `pencil` | 16pt |
| Delete | `trash` | 16pt |
| Barcode | `barcode.viewfinder` | 22pt |
| Library | `photo.on.rectangle` | 22pt |

## 7. Accessibility Notes

- Minimum iOS target: 17 (for `.sensoryFeedback`, `UnevenRoundedRectangle`)
- VoiceOver: label macro rings with current/target values: `.accessibilityLabel("Protein, 112 of 140 grams, 80 percent")`
- Dynamic Type: body scales freely, hero numeral caps at 96pt, macro rings hold at 80pt (use `.minimumScaleFactor(0.8)` on macro numerals)
- Contrast: `#A1A1A1` on `#0A0A0A` passes WCAG AA for 13pt+ body. Validate `#6E6E6E` tertiary at 11pt labels — bump to `#808080` if needed
- Reduce Motion: wrap shimmer and ring fill animations in `@Environment(\.accessibilityReduceMotion)` checks
