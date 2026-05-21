# Sleep Cycle (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Sleep Cycle's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature hypnogram + score ring, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Night Gradient & Canvas (Dark, default)
    static let scNightTop    = Color(red: 0.165, green: 0.176, blue: 0.353) // #2A2D5A
    static let scNightMid    = Color(red: 0.231, green: 0.263, blue: 0.443) // #3B4371
    static let scCanvasDeep  = Color(red: 0.078, green: 0.082, blue: 0.180) // #14152E
    static let scSurface1    = Color(red: 0.118, green: 0.125, blue: 0.275) // #1E2046
    static let scSurface2    = Color(red: 0.161, green: 0.173, blue: 0.345) // #292C58
    static let scDivider     = Color(red: 0.208, green: 0.224, blue: 0.455) // #353974

    // MARK: - Canvas (Light — Dawn)
    static let scDawnCanvas  = Color(red: 0.957, green: 0.961, blue: 0.988) // #F4F5FC
    static let scDawnSurface = Color.white                                   // #FFFFFF
    static let scDawnDivider = Color(red: 0.890, green: 0.898, blue: 0.949) // #E3E5F2

    // MARK: - Text
    static let scTextPrimary   = Color(red: 0.949, green: 0.953, blue: 0.984) // #F2F3FB
    static let scTextSecondary = Color(red: 0.651, green: 0.663, blue: 0.831) // #A6A9D4
    static let scTextTertiary  = Color(red: 0.424, green: 0.439, blue: 0.659) // #6C70A8
    static let scTextPrimaryLt = Color(red: 0.106, green: 0.114, blue: 0.227) // #1B1D3A

    // MARK: - Accents
    static let scAccent        = Color(red: 0.424, green: 0.482, blue: 1.000) // #6C7BFF
    static let scAccentPressed = Color(red: 0.333, green: 0.388, blue: 0.902) // #5563E6
    static let scDeepSleep     = Color(red: 0.239, green: 0.290, blue: 0.749) // #3D4ABF
    static let scAqua          = Color(red: 0.310, green: 0.820, blue: 0.902) // #4FD1E6
    static let scAquaSoft      = Color(red: 0.498, green: 0.890, blue: 0.941) // #7FE3F0

    // MARK: - Sleep Stages (semantic)
    static let scStageAwake = Color(red: 1.000, green: 0.561, blue: 0.694) // #FF8FB1
    static let scStageLight = Color(red: 0.424, green: 0.482, blue: 1.000) // #6C7BFF
    static let scStageDeep  = Color(red: 0.239, green: 0.290, blue: 0.749) // #3D4ABF
    static let scStageREM   = Color(red: 0.310, green: 0.820, blue: 0.902) // #4FD1E6

    // MARK: - Semantic
    static let scSuccess = Color(red: 0.310, green: 0.820, blue: 0.627) // #4FD1A0
    static let scError   = Color(red: 1.000, green: 0.420, blue: 0.506) // #FF6B81
    static let scGold    = Color(red: 1.000, green: 0.773, blue: 0.420) // #FFC56B
}

// Score-banded ring color
func scScoreColor(_ score: Int) -> Color {
    switch score {
    case ..<50:  return .scStageAwake   // rose
    case 50..<75: return .scAccent      // indigo
    default:      return .scAqua        // aqua
    }
}

// The signature shell gradient
extension LinearGradient {
    static let scNightShell = LinearGradient(
        stops: [
            .init(color: .scNightTop,   location: 0.0),
            .init(color: .scNightMid,   location: 0.30),
            .init(color: .scCanvasDeep, location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

Sleep Cycle uses **Nunito** (Google Fonts, SIL OFL). Bundle the TTFs via `Info.plist` / `UIAppFonts`. Numerals are heavy (800–900); body is light (400).

```swift
extension Font {
    static func nunito(_ size: CGFloat, _ weight: Weight = .regular) -> Font {
        let face: String
        switch weight {
        case .black:    face = "Nunito-Black"
        case .heavy:    face = "Nunito-ExtraBold"
        case .bold:     face = "Nunito-Bold"
        case .semibold: face = "Nunito-SemiBold"
        case .medium:   face = "Nunito-Medium"
        default:        face = "Nunito-Regular"
        }
        return .custom(face, size: size)
    }

    static let scScreenTitle = Font.nunito(32, .black)
    static let scHeroStat    = Font.nunito(34, .black)
    static let scSection     = Font.nunito(22, .heavy)
    static let scCardTitle   = Font.nunito(18, .bold)
    static let scBody        = Font.nunito(16, .regular)
    static let scListItem    = Font.nunito(15, .semibold)
    static let scMeta        = Font.nunito(14, .regular)
    static let scEyebrow     = Font.nunito(12, .bold)
    static let scButton      = Font.nunito(16, .heavy)
    static let scPill        = Font.nunito(11, .heavy)
    static let scAxis        = Font.nunito(9,  .bold)
    static let scTab         = Font.nunito(10, .bold)
}
```

## 3. Signature Components

### Sleep-Analysis Graph (Hypnogram)

```swift
struct HypnogramCard: View {
    /// Normalized sample points 0...1 (x = time, y = depth; 0 = awake/top, 1 = deep/bottom)
    let samples: [CGPoint]
    @State private var drawProgress: CGFloat = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Text("Sleep stages").font(.nunito(13, .bold)).foregroundStyle(.scTextPrimary)
                Spacer()
                Text("23:42 — 07:18").font(.nunito(11, .semibold)).foregroundStyle(.scTextTertiary)
            }

            GeometryReader { geo in
                let w = geo.size.width, h = geo.size.height
                ZStack(alignment: .leading) {
                    // Gradient fill under the wave
                    wavePath(in: CGSize(width: w, height: h), closed: true)
                        .fill(LinearGradient(
                            colors: [.scAccent.opacity(0.45), .scAccent.opacity(0.02)],
                            startPoint: .top, endPoint: .bottom))
                        .opacity(drawProgress)
                    // The glowing aqua wave stroke
                    wavePath(in: CGSize(width: w, height: h), closed: false)
                        .trim(from: 0, to: drawProgress)
                        .stroke(Color.scAquaSoft, style: StrokeStyle(lineWidth: 2.5, lineCap: .round, lineJoin: .round))
                }
            }
            .frame(height: 110)

            HStack {
                ForEach(["00","02","04","06","07"], id: \.self) { t in
                    Text(t).font(.scAxis).foregroundStyle(.scTextTertiary)
                    if t != "07" { Spacer() }
                }
            }

            HStack(spacing: 14) {
                legend("Awake", .scStageAwake)
                legend("Light", .scStageLight)
                legend("Deep",  .scStageDeep)
                legend("REM",   .scStageREM)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 20).fill(Color.scSurface1))
        .overlay(RoundedRectangle(cornerRadius: 20).strokeBorder(Color.scDivider, lineWidth: 1))
        .shadow(color: .black.opacity(0.35), radius: 24, y: 8)
        .onAppear {
            withAnimation(.easeOut(duration: 0.9)) { drawProgress = 1 }
        }
    }

    private func wavePath(in size: CGSize, closed: Bool) -> Path {
        Path { p in
            guard let first = samples.first else { return }
            func pt(_ s: CGPoint) -> CGPoint {
                CGPoint(x: s.x * size.width, y: 8 + s.y * (size.height - 16))
            }
            p.move(to: pt(first))
            for i in 1..<samples.count {
                let prev = pt(samples[i-1]); let cur = pt(samples[i])
                let mid = CGPoint(x: (prev.x + cur.x)/2, y: (prev.y + cur.y)/2)
                p.addQuadCurve(to: mid, control: prev)
                p.addQuadCurve(to: cur, control: cur)
            }
            if closed {
                p.addLine(to: CGPoint(x: size.width, y: size.height))
                p.addLine(to: CGPoint(x: 0, y: size.height))
                p.closeSubpath()
            }
        }
    }

    private func legend(_ label: String, _ color: Color) -> some View {
        HStack(spacing: 5) {
            RoundedRectangle(cornerRadius: 3).fill(color).frame(width: 9, height: 9)
            Text(label).font(.nunito(10, .bold)).foregroundStyle(.scTextSecondary)
        }
    }
}
```

### Sleep-Quality Ring + Hero Stat

```swift
struct SleepScoreRing: View {
    let score: Int            // 0...100
    @State private var animated: CGFloat = 0

    var body: some View {
        ZStack {
            Circle().stroke(Color.scSurface2, lineWidth: 9)
            Circle()
                .trim(from: 0, to: animated)
                .stroke(scScoreColor(score),
                        style: StrokeStyle(lineWidth: 9, lineCap: .round))
                .rotationEffect(.degrees(-90))
            VStack(spacing: 1) {
                Text("\(Int(CGFloat(score) * animated))")
                    .font(.nunito(26, .black)).foregroundStyle(.scTextPrimary)
                    .contentTransition(.numericText())
                Text("SLEEP QUALITY")
                    .font(.nunito(9, .bold)).foregroundStyle(.scTextSecondary)
            }
        }
        .frame(width: 92, height: 92)
        .onAppear { withAnimation(.easeOut(duration: 0.8)) { animated = 1 } }
    }
}

struct ScoreHeader: View {
    var body: some View {
        HStack(spacing: 18) {
            SleepScoreRing(score: 78)
            VStack(alignment: .leading, spacing: 3) {
                Text("7h 36m").font(.nunito(22, .heavy)).foregroundStyle(.scTextPrimary)
                Text("Time asleep · 31m to fall asleep")
                    .font(.nunito(12, .semibold)).foregroundStyle(.scTextSecondary)
                Text("Good night")
                    .font(.scPill).foregroundStyle(.scCanvasDeep)
                    .padding(.horizontal, 10).padding(.vertical, 3)
                    .background(Capsule().fill(Color.scAqua))
                    .padding(.top, 6)
            }
            Spacer()
        }
    }
}
```

### Smart-Alarm Card

```swift
struct SmartAlarmCard: View {
    let time: String
    var body: some View {
        HStack(spacing: 14) {
            ZStack {
                RoundedRectangle(cornerRadius: 12)
                    .fill(LinearGradient(colors: [.scAccent, .scDeepSleep],
                                         startPoint: .topLeading, endPoint: .bottomTrailing))
                Image(systemName: "alarm").font(.system(size: 20, weight: .semibold))
                    .foregroundStyle(.white)
            }
            .frame(width: 38, height: 38)
            VStack(alignment: .leading, spacing: 2) {
                Text("Smart alarm").font(.nunito(15, .heavy)).foregroundStyle(.scTextPrimary)
                Text("Wakes you in your lightest sleep")
                    .font(.nunito(11, .semibold)).foregroundStyle(.scTextSecondary)
            }
            Spacer()
            Text(time).font(.nunito(19, .black)).foregroundStyle(.scTextPrimary)
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 18).fill(Color.scSurface1))
        .overlay(RoundedRectangle(cornerRadius: 18).strokeBorder(Color.scDivider, lineWidth: 1))
    }
}
```

### Primary Button

```swift
struct SCPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.scButton).foregroundStyle(.white)
                .padding(.vertical, 15).padding(.horizontal, 30)
                .frame(maxWidth: .infinity)
        }
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(LinearGradient(colors: [.scAccent, .scDeepSleep],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
        )
        .shadow(color: Color.scAccent.opacity(0.35), radius: 20, y: 8) // colored glow
        .buttonStyle(.plain)
    }
}
```

### Screen Header (Eyebrow + Title)

```swift
struct ScreenHeader: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            Text("SLEEP ANALYSIS")
                .font(.scEyebrow).tracking(1.4).foregroundStyle(.scAqua)
            Text("Last night")
                .font(.scScreenTitle).foregroundStyle(.scTextPrimary)
            Text("Mon, May 12 · 23:42 – 07:18")
                .font(.scMeta).foregroundStyle(.scTextSecondary)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SCTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundColor = UIColor(Color.scCanvasDeep.opacity(0.92))
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            SleepView().tabItem  { Label("Sleep",   systemImage: "waveform.path.ecg") }
            JournalView().tabItem{ Label("Journal", systemImage: "calendar") }
            AlarmView().tabItem  { Label("Alarm",   systemImage: "alarm") }
            SoundsView().tabItem { Label("Sounds",  systemImage: "music.note.list") }
            ProfileView().tabItem{ Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.scAqua)   // active tab = aqua; no tint pill
    }
}
```

## 5. Motion

```swift
// Hypnogram draw-in (left → right reveal)
.onAppear { withAnimation(.easeOut(duration: 0.9)) { drawProgress = 1 } }
// Fill fades in slightly faster
.opacity(drawProgress) // animate with 0.6s easeOut if separated

// Score ring sweep + count-up
.onAppear { withAnimation(.easeOut(duration: 0.8)) { animated = 1 } }
Text("\(value)").contentTransition(.numericText())

// Segmented control selection slide
withAnimation(.easeOut(duration: 0.22)) { selectedSegment = idx }

// Sheet present
.presentationDetents([.medium, .large])   // slides up 300ms

// Toggle + haptic
.sensoryFeedback(.impact(flexibility: .soft), trigger: smartAlarmOn)

// Session start: calm pulse on the moon, then auto-dim
withAnimation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true)) { moonScale = 1.06 }
UIScreen.main.brightness = max(0.05, UIScreen.main.brightness - 0.4) // ramp over ~1.5s
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Sleep (tab) | `waveform.path.ecg` | 22pt |
| Journal (tab) | `calendar` | 22pt |
| Alarm (tab) | `alarm` / `alarm.fill` | 22pt |
| Sounds (tab) | `music.note.list` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Smart alarm tile | `alarm` | 20pt |
| Moon / session | `moon.stars.fill` | 24pt |
| Sleep aid play | `play.fill` / `pause.fill` | 18pt |
| Trend up | `arrow.up.right` | 14pt |
| Streak | `flame.fill` | 14pt |
| Settings | `gearshape` | 22pt |
| Back / dismiss | `chevron.left` / `xmark` | 20pt |
| Snooze | `zzz` | 18pt |
| Heart rate | `heart.fill` | 16pt |

## 7. Dark Mode

```swift
struct SCBackground: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content.background(
            scheme == .dark
            ? AnyShapeStyle(LinearGradient.scNightShell)
            : AnyShapeStyle(Color.scDawnCanvas)
        )
    }
}
extension View { func scBackground() -> some View { modifier(SCBackground()) } }
```

Sleep Cycle is **dark-first** — the night gradient is the default and primary experience. The Dawn light scheme swaps the field to `#F4F5FC` with white cards, but the accent (`#6C7BFF`), aqua wave (`#4FD1E6`), and all sleep-stage colors are identical in both modes. On dark, shadows are soft and large-blur (`black.opacity(0.35)`); on light they tighten to `rgba(43,29,58,0.10)`.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a plain text swap on iOS 16)
- Bundle Nunito TTFs (Regular / Medium / SemiBold / Bold / ExtraBold / Black) via `Info.plist` `UIAppFonts` — SIL OFL, free to ship
- Dynamic Type: scale screen title, hero stat, section, card title, body, list item; keep axis labels, legend, tab labels, pill text, ring sublabel FIXED. Cap hero stat at +30%
- VoiceOver: announce the score ring as "Sleep quality {score} out of 100, {good/fair/poor} night"; the hypnogram as a summary "Slept 7 hours 36 minutes, {n} awakenings, {n} minutes deep sleep" rather than reading the path
- The hypnogram must have an `.accessibilityLabel` summarizing the night plus an `.accessibilityChartDescriptor` if using Swift Charts
- Color contrast: `#F2F3FB` on `#1E2046` and on the gradient passes WCAG AA; the stage colors are paired with dark ink on light fills (Awake `#3A1430`, REM `#07303A`) to keep chip text AA
- Reduce Motion: replace the wave draw-in and ring sweep with a 200ms cross-fade; bars/ring render at final value; disable the moon pulse
- Auto-dim: respect a user setting to disable brightness reduction; never drop below 0.05 brightness and always restore the original brightness on session end
- Reduce Transparency: replace the blurred tab-bar background with an opaque `#14152E`
