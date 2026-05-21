# Noom (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Noom's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature lesson card + food log + weight graph, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let noomBlue        = Color(red: 0.165, green: 0.365, blue: 0.965) // #2A5DF6
    static let noomBluePressed = Color(red: 0.118, green: 0.278, blue: 0.784) // #1E47C8
    static let noomTeal        = Color(red: 0.122, green: 0.761, blue: 0.608) // #1FC29B
    static let noomNavy        = Color(red: 0.047, green: 0.106, blue: 0.302) // #0C1B4D

    // MARK: - Canvas & Surfaces (Light)
    static let noomCanvas      = Color(red: 0.984, green: 0.984, blue: 0.992) // #FBFBFD
    static let noomSurface     = Color.white                                   // #FFFFFF
    static let noomSurfaceSub  = Color(red: 0.949, green: 0.953, blue: 0.969) // #F2F3F7
    static let noomDividerLt   = Color(red: 0.902, green: 0.906, blue: 0.925) // #E6E7EC

    // MARK: - Canvas & Surfaces (Dark)
    static let noomDarkCanvas  = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let noomDarkSurf1   = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let noomDarkSurf2   = Color(red: 0.149, green: 0.149, blue: 0.161) // #262629
    static let noomDarkDivider = Color(red: 0.180, green: 0.180, blue: 0.192) // #2E2E31

    // MARK: - Text
    static let noomTextPriLt   = Color(red: 0.102, green: 0.102, blue: 0.122) // #1A1A1F
    static let noomTextSecLt   = Color(red: 0.416, green: 0.416, blue: 0.439) // #6A6A70
    static let noomTextPriDk   = Color(red: 0.941, green: 0.941, blue: 0.949) // #F0F0F2
    static let noomTextSecDk   = Color(red: 0.604, green: 0.604, blue: 0.627) // #9A9AA0

    // MARK: - Food-Color System (FIXED both modes)
    static let foodGreen  = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759
    static let foodYellow = Color(red: 1.000, green: 0.773, blue: 0.192) // #FFC531
    static let foodRed    = Color(red: 1.000, green: 0.353, blue: 0.322) // #FF5A52

    // MARK: - Coach
    static let coachPurple = Color(red: 0.482, green: 0.380, blue: 1.000) // #7B61FF

    // MARK: - Semantic
    static let noomSuccess = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759
    static let noomError   = Color(red: 1.000, green: 0.353, blue: 0.322) // #FF5A52
    static let noomFlame   = Color(red: 1.000, green: 0.541, blue: 0.239) // #FF8A3D
}

enum FoodClass { case green, yellow, red
    var color: Color { self == .green ? .foodGreen : self == .yellow ? .foodYellow : .foodRed }
    var label: String { self == .green ? "Green" : self == .yellow ? "Yellow" : "Red" }
}

extension LinearGradient {
    static let noomLesson = LinearGradient(
        colors: [.noomBlue, .noomTeal],
        startPoint: .topLeading, endPoint: .bottomTrailing)
}
```

## 2. Typography

Noom uses **Poppins** (Google Fonts, SIL OFL). Bundle the TTFs via `Info.plist` / `UIAppFonts`. Heavy numerals, conversational body.

```swift
extension Font {
    static func poppins(_ size: CGFloat, _ weight: Weight = .regular) -> Font {
        let face: String
        switch weight {
        case .heavy, .black: face = "Poppins-ExtraBold"
        case .bold:          face = "Poppins-Bold"
        case .semibold:      face = "Poppins-SemiBold"
        case .medium:        face = "Poppins-Medium"
        default:             face = "Poppins-Regular"
        }
        return .custom(face, size: size)
    }

    static let noomScreenTitle = Font.poppins(32, .heavy)
    static let noomGreeting    = Font.poppins(26, .bold)
    static let noomHeroStat    = Font.poppins(24, .heavy)
    static let noomSection     = Font.poppins(22, .bold)
    static let noomCardTitle   = Font.poppins(18, .bold)
    static let noomBody        = Font.poppins(16, .regular)
    static let noomListItem    = Font.poppins(15, .semibold)
    static let noomMeta        = Font.poppins(14, .regular)
    static let noomEyebrow     = Font.poppins(12, .semibold)
    static let noomButton      = Font.poppins(16, .bold)
    static let noomPill        = Font.poppins(11, .bold)
    static let noomAxis        = Font.poppins(9,  .bold)
    static let noomTab         = Font.poppins(10, .semibold)
}
```

## 3. Signature Components

### Daily Psychology Lesson Card

```swift
struct LessonCard: View {
    let eyebrow: String       // "TODAY'S LESSON · 4 MIN"
    let title: String
    let step: Int
    let total: Int
    @State private var appeared = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text(eyebrow)
                .font(.noomEyebrow).tracking(1.0)
                .foregroundStyle(.white.opacity(0.85))
            Text(title)
                .font(.noomCardTitle).foregroundStyle(.white)
                .lineSpacing(3).padding(.top, 8)
                .frame(maxWidth: 240, alignment: .leading)

            HStack(spacing: 8) {
                GeometryReader { g in
                    ZStack(alignment: .leading) {
                        Capsule().fill(.white.opacity(0.25))
                        Capsule().fill(.white)
                            .frame(width: g.size.width * CGFloat(step) / CGFloat(total))
                    }
                }
                .frame(height: 6)
                Text("\(step) of \(total)")
                    .font(.noomPill).foregroundStyle(.white)
            }
            .padding(.top, 14)

            HStack(spacing: 6) {
                Text("Continue lesson").font(.poppins(13, .bold))
                Image(systemName: "arrow.right").font(.system(size: 12, weight: .bold))
            }
            .foregroundStyle(.noomBlue)
            .padding(.horizontal, 16).padding(.vertical, 9)
            .background(Capsule().fill(.white))
            .padding(.top, 14)
        }
        .padding(18)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            RoundedRectangle(cornerRadius: 20).fill(LinearGradient.noomLesson)
                .overlay(alignment: .topTrailing) {
                    Circle().fill(.white.opacity(0.10))
                        .frame(width: 110, height: 110).offset(x: 30, y: -30)
                }
                .clipShape(RoundedRectangle(cornerRadius: 20))
        )
        .shadow(color: Color.noomBlue.opacity(0.22), radius: 26, y: 10)
        .scaleEffect(appeared ? 1 : 0.96).opacity(appeared ? 1 : 0)
        .onAppear { withAnimation(.easeOut(duration: 0.32)) { appeared = true } }
    }
}
```

### Food Log Row + Stacked Ratio Bar

```swift
struct FoodRatioBar: View {
    let green: CGFloat, yellow: CGFloat, red: CGFloat   // proportions, sum ~1
    var body: some View {
        GeometryReader { g in
            HStack(spacing: 0) {
                Rectangle().fill(Color.foodGreen).frame(width: g.size.width * green)
                Rectangle().fill(Color.foodYellow).frame(width: g.size.width * yellow)
                Rectangle().fill(Color.foodRed).frame(width: g.size.width * red)
            }
        }
        .frame(height: 10)
        .clipShape(RoundedRectangle(cornerRadius: 5))
    }
}

struct FoodRow: View {
    let name: String
    let cls: FoodClass
    let kcal: Int
    var body: some View {
        HStack(spacing: 12) {
            Circle().fill(cls.color).frame(width: 10, height: 10)
            Text(name).font(.noomListItem).foregroundStyle(.primary)
            Spacer(minLength: 8)
            Text(cls.label)
                .font(.noomPill).foregroundStyle(cls.color)
                .padding(.horizontal, 9).padding(.vertical, 3)
                .background(Capsule().fill(cls.color.opacity(0.16)))
            Text("\(kcal)")
                .font(.poppins(13, .bold)).foregroundStyle(.secondary)
                .frame(width: 52, alignment: .trailing)
        }
        .padding(.vertical, 9)
        .overlay(Divider().background(Color.noomDividerLt), alignment: .bottom)
    }
}
```

### Weight Graph Card

```swift
struct WeightGraphCard: View {
    let points: [CGPoint]      // normalized 0...1 (y 0=top/heavier-left, 1=lighter)
    let goalY: CGFloat         // normalized 0...1
    @State private var draw: CGFloat = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            HStack {
                Text("Weight").font(.poppins(14, .bold)).foregroundStyle(.primary)
                Spacer()
                Text("Last 30 days").font(.poppins(11, .semibold)).foregroundStyle(.secondary)
            }
            HStack(alignment: .firstTextBaseline, spacing: 8) {
                Text("164.2 lb").font(.noomHeroStat).foregroundStyle(.primary)
                Text("▼ 6.8 lb").font(.poppins(13, .semibold)).foregroundStyle(.noomTeal)
            }

            GeometryReader { g in
                let w = g.size.width, h = g.size.height
                func pt(_ p: CGPoint) -> CGPoint { CGPoint(x: p.x * w, y: p.y * h) }
                ZStack {
                    // gradient fill
                    Path { p in
                        p.move(to: CGPoint(x: 0, y: h))
                        p.addLine(to: pt(points.first ?? .zero))
                        for q in points.dropFirst() { p.addLine(to: pt(q)) }
                        p.addLine(to: CGPoint(x: w, y: h)); p.closeSubpath()
                    }
                    .fill(LinearGradient(colors: [.noomBlue.opacity(0.30), .noomBlue.opacity(0.02)],
                                         startPoint: .top, endPoint: .bottom))
                    .opacity(draw)
                    // trend curve
                    Path { p in
                        p.move(to: pt(points.first ?? .zero))
                        for q in points.dropFirst() { p.addLine(to: pt(q)) }
                    }
                    .trim(from: 0, to: draw)
                    .stroke(Color.noomBlue, style: StrokeStyle(lineWidth: 2.5, lineCap: .round))
                    // goal line
                    Path { p in
                        p.move(to: CGPoint(x: 0, y: goalY * h))
                        p.addLine(to: CGPoint(x: w, y: goalY * h))
                    }
                    .stroke(Color.noomTeal, style: StrokeStyle(lineWidth: 1.5, dash: [3, 4]))
                    .opacity(draw)
                }
            }
            .frame(height: 90)

            HStack {
                Text("Apr 14"); Spacer(); Text("Apr 28"); Spacer(); Text("May 12")
            }
            .font(.noomAxis).foregroundStyle(.secondary)
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 18).fill(Color.noomSurface))
        .overlay(RoundedRectangle(cornerRadius: 18).strokeBorder(Color.noomDividerLt, lineWidth: 1))
        .shadow(color: Color.noomNavy.opacity(0.06), radius: 18, y: 6)
        .onAppear { withAnimation(.easeOut(duration: 0.7)) { draw = 1 } }
    }
}
```

### Primary Button (Pill)

```swift
struct NoomPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.noomButton).foregroundStyle(.white)
                .padding(.vertical, 15).padding(.horizontal, 30)
                .frame(maxWidth: .infinity)
        }
        .background(Capsule().fill(Color.noomBlue))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct NoomTabView: View {
    var body: some View {
        TabView {
            TodayView().tabItem  { Label("Today",   systemImage: "house.fill") }
            LearnView().tabItem  { Label("Learn",   systemImage: "book.fill") }
            LogView().tabItem    { Label("Log",     systemImage: "plus.circle.fill") }
            CoachView().tabItem  { Label("Coach",   systemImage: "bubble.left.fill") }
            ProfileView().tabItem{ Label("Profile", systemImage: "person.crop.circle.fill") }
        }
        .tint(.noomBlue)   // active = Noom Blue, no tint pill
    }
}
```

## 5. Motion

```swift
// Lesson card entrance
.onAppear { withAnimation(.easeOut(duration: 0.32)) { appeared = true } }
.scaleEffect(appeared ? 1 : 0.96).opacity(appeared ? 1 : 0)

// Weight graph draw-in
.onAppear { withAnimation(.easeOut(duration: 0.7)) { draw = 1 } }
Path { ... }.trim(from: 0, to: draw)

// Food ratio bar re-animates when a food is added
withAnimation(.easeOut(duration: 0.4)) { /* update green/yellow/red */ }

// New food row insert
.transition(.move(edge: .top).combined(with: .opacity))   // 250ms easeOut

// Segmented control
withAnimation(.easeOut(duration: 0.22)) { selectedRange = idx }

// Lesson progress complete + haptic
withAnimation(.easeOut(duration: 0.4)) { progress = 1 }
.sensoryFeedback(.success, trigger: lessonComplete)

// Coach typing indicator: 3 dots scale 0.6↔1 on a 0.6s repeatForever
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Today (tab) | `house` / `house.fill` | 22pt |
| Learn (tab) | `book` / `book.fill` | 22pt |
| Log (tab) | `plus.circle` / `plus.circle.fill` | 22pt |
| Coach (tab) | `bubble.left` / `bubble.left.fill` | 22pt |
| Profile (tab) | `person.crop.circle` / `.fill` | 22pt |
| Lesson CTA arrow | `arrow.right` | 12pt |
| Weight delta down | `arrow.down` / `chevron.down` | 12pt |
| Search / scan | `magnifyingglass` / `barcode.viewfinder` | 18pt |
| Coach send | `arrow.up.circle.fill` | 26pt |
| Streak | `flame.fill` | 14pt |
| Add food | `plus` | 18pt |
| Back / close | `chevron.left` / `xmark` | 20pt |
| Goal reached | `checkmark.circle.fill` | 18pt |
| Settings | `gearshape` | 22pt |

## 7. Dark Mode

```swift
struct NoomTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content.background(scheme == .dark ? Color.noomDarkCanvas : Color.noomCanvas)
    }
}
extension View { func noomTheme() -> some View { modifier(NoomTheme()) } }
```

Noom is **light-first** and cheerful; the dark scheme keeps brand energy. Canvas `#FBFBFD` → `#121212`, surface `#FFFFFF` → `#1C1C1E`. **Noom Blue `#2A5DF6` and the food-color system (`#34C759` / `#FFC531` / `#FF5A52`) are identical in both modes** — users read food by color, so the hues must never shift. In light mode use soft Navy-tinted shadows (`Color.noomNavy.opacity(0.06)`); in dark mode drop shadows and rely on the `#2E2E31` border + surface lift. The lesson card keeps its blue→teal gradient and a faint blue glow in both modes.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UINotificationFeedbackGenerator`)
- Bundle Poppins TTFs (Regular / Medium / SemiBold / Bold / ExtraBold) via `Info.plist` `UIAppFonts` — SIL OFL, free to ship
- Dynamic Type: scale screen title, greeting, hero stat, section, card title, body, list item; keep eyebrow, food pill, axis, tab labels FIXED. Cap lesson title at +25%
- **Food-color accessibility is critical**: never rely on color alone — always pair the dot with the text label ("Green"/"Yellow"/"Red") so color-blind users can read food classes. Add an `.accessibilityLabel` like "Greek yogurt, Green food, 180 calories"
- VoiceOver: announce the weight graph as a summary ("Down 6.8 pounds over 30 days, currently 164.2") rather than reading the path; announce the ratio bar as "58% green, 30% yellow, 12% red today"
- Color contrast: `#1A1A1F` on `#FBFBFD` and `#F0F0F2` on `#121212` pass WCAG AA; white text on the blue→teal gradient passes AA at 18pt weight 700; the Yellow food pill uses a darker text token on light tints to stay AA
- Reduce Motion: replace the lesson scale-in and graph draw with a 200ms cross-fade; ratio bar and graph render at final state; disable the coach typing pulse
- Reduce Transparency: replace the blurred tab-bar background with an opaque surface color
- The lesson card gradient + white text must keep ≥ 4.5:1 — if Dynamic Type shrinks weight, keep the title at ≥ weight 600
