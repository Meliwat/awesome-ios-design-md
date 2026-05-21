# Runna (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Runna's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature week strip + session card + structure breakdown, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let runIndigo        = Color(red: 0.310, green: 0.275, blue: 0.898) // #4F46E5
    static let runIndigoBright  = Color(red: 0.388, green: 0.400, blue: 0.945) // #6366F1
    static let runIndigoPressed = Color(red: 0.263, green: 0.220, blue: 0.792) // #4338CA
    static let runLime          = Color(red: 0.761, green: 0.976, blue: 0.306) // #C2F94E
    static let runLimePressed   = Color(red: 0.639, green: 0.851, blue: 0.231) // #A3D93B

    // MARK: - Canvas & Surfaces (Dark, default)
    static let runCanvas   = Color(red: 0.055, green: 0.055, blue: 0.086) // #0E0E16
    static let runSurface1 = Color(red: 0.094, green: 0.094, blue: 0.149) // #181826
    static let runSurface2 = Color(red: 0.133, green: 0.133, blue: 0.204) // #222234
    static let runDivider  = Color(red: 0.173, green: 0.173, blue: 0.259) // #2C2C42

    // MARK: - Canvas & Surfaces (Light, optional)
    static let runLightCanvas  = Color(red: 0.965, green: 0.965, blue: 0.984) // #F6F6FB
    static let runLightSurface = Color.white                                   // #FFFFFF
    static let runLightDivider = Color(red: 0.898, green: 0.898, blue: 0.941) // #E5E5F0

    // MARK: - Text
    static let runTextPrimary   = Color(red: 0.953, green: 0.953, blue: 0.984) // #F3F3FB
    static let runTextSecondary = Color(red: 0.612, green: 0.612, blue: 0.722) // #9C9CB8
    static let runTextTertiary  = Color(red: 0.396, green: 0.396, blue: 0.494) // #65657E
    static let runTextOnLime    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A

    // MARK: - Run-Type System (FIXED both themes)
    static let runEasy     = Color(red: 0.204, green: 0.827, blue: 0.600) // #34D399
    static let runTempo    = Color(red: 0.984, green: 0.749, blue: 0.141) // #FBBF24
    static let runInterval = Color(red: 0.984, green: 0.447, blue: 0.522) // #FB7185
    static let runLong     = Color(red: 0.310, green: 0.275, blue: 0.898) // #4F46E5 (Indigo)

    // MARK: - Semantic
    static let runSuccess = Color(red: 0.204, green: 0.827, blue: 0.600) // #34D399
    static let runError   = Color(red: 0.984, green: 0.447, blue: 0.522) // #FB7185
    static let runWarning = Color(red: 0.984, green: 0.749, blue: 0.141) // #FBBF24
    static let runPR      = Color(red: 0.761, green: 0.976, blue: 0.306) // #C2F94E
}

enum RunType { case easy, tempo, interval, long
    var color: Color {
        switch self { case .easy: .runEasy; case .tempo: .runTempo
                       case .interval: .runInterval; case .long: .runLong }
    }
    var onColor: Color { self == .long ? .white : .runTextOnLime }
}

extension LinearGradient {
    static let runSession = LinearGradient(
        colors: [.runIndigo, Color(red: 0.227, green: 0.200, blue: 0.722)], // #4F46E5 → #3A33B8
        startPoint: .topLeading, endPoint: .bottomTrailing)
}
```

## 2. Typography

Runna uses **Sora** (Google Fonts, SIL OFL). Bundle the TTFs via `Info.plist` / `UIAppFonts`. Heavy stats, regular prose.

```swift
extension Font {
    static func sora(_ size: CGFloat, _ weight: Weight = .regular) -> Font {
        let face: String
        switch weight {
        case .heavy, .black: face = "Sora-ExtraBold"
        case .bold:          face = "Sora-Bold"
        case .semibold:      face = "Sora-SemiBold"
        case .medium:        face = "Sora-Medium"
        default:             face = "Sora-Regular"
        }
        return .custom(face, size: size)
    }

    static let runScreenTitle  = Font.sora(32, .heavy)
    static let runPlanTitle    = Font.sora(26, .heavy)
    static let runSessionTitle = Font.sora(21, .heavy)
    static let runSection      = Font.sora(22, .bold)
    static let runCardTitle    = Font.sora(18, .bold)
    static let runBody         = Font.sora(16, .regular)
    static let runStepItem     = Font.sora(15, .semibold)
    static let runMeta         = Font.sora(14, .regular)
    static let runStatValue    = Font.sora(18, .heavy)
    static let runEyebrow      = Font.sora(11, .bold)
    static let runButton       = Font.sora(16, .heavy)
    static let runPace         = Font.sora(12, .bold)
    static let runDayNum       = Font.sora(14, .heavy)
    static let runTab          = Font.sora(10, .semibold)
}
```

## 3. Signature Components

### Training-Plan Week Strip

```swift
struct DayCell: Identifiable {
    let id = UUID()
    let weekday: String   // "MON"
    let date: Int
    enum State { case done, today, rest, upcoming(RunType) }
    let state: State
}

struct WeekStrip: View {
    let days: [DayCell]
    @State private var appeared = false

    var body: some View {
        HStack(spacing: 7) {
            ForEach(Array(days.enumerated()), id: \.element.id) { idx, d in
                cell(d)
                    .opacity(appeared ? 1 : 0)
                    .animation(.easeOut(duration: 0.22).delay(Double(idx) * 0.04), value: appeared)
            }
        }
        .onAppear { appeared = true }
    }

    @ViewBuilder private func cell(_ d: DayCell) -> some View {
        let isToday = { if case .today = d.state { true } else { false } }()
        VStack(spacing: 0) {
            Text(d.weekday).font(.sora(10, .bold))
                .foregroundStyle(isToday ? .white : .runTextTertiary)
            Spacer(minLength: 4)
            Text("\(d.date)").font(.runDayNum)
                .foregroundStyle(dateColor(d))
            Circle().fill(dotColor(d)).frame(width: 7, height: 7).padding(.top, 6)
        }
        .frame(maxWidth: .infinity)
        .aspectRatio(0.62, contentMode: .fit)
        .padding(.vertical, 9)
        .background(bg(d))
        .clipShape(RoundedRectangle(cornerRadius: 14))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(border(d), lineWidth: 1))
    }

    private func dateColor(_ d: DayCell) -> Color {
        switch d.state {
        case .done: .runEasy; case .today: .white
        case .rest: .runTextTertiary; case .upcoming: .runTextPrimary }
    }
    private func dotColor(_ d: DayCell) -> Color {
        switch d.state {
        case .done: .runEasy; case .today: .runLime
        case .rest: .runDivider; case .upcoming(let t): t.color }
    }
    private func bg(_ d: DayCell) -> Color {
        switch d.state {
        case .done: Color.runEasy.opacity(0.12)
        case .today: .runIndigo
        default: .runSurface1 }
    }
    private func border(_ d: DayCell) -> Color {
        switch d.state {
        case .done: Color.runEasy.opacity(0.35)
        case .today: .runIndigoBright
        default: .runDivider }
    }
}
```

### Guided Run-Session Card

```swift
struct SessionCard: View {
    let tag: String        // "Today · Intervals"
    let title: String
    let distance: String
    let duration: String
    let pace: String
    let onStart: () -> Void
    @State private var appeared = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text(tag).font(.sora(10, .heavy)).textCase(.uppercase)
                .foregroundStyle(.runTextOnLime)
                .padding(.horizontal, 10).padding(.vertical, 4)
                .background(Capsule().fill(Color.runLime))

            Text(title).font(.runSessionTitle).foregroundStyle(.white).padding(.top, 12)

            HStack(spacing: 18) {
                stat(distance, "Distance"); stat(duration, "Duration"); stat(pace, "Avg pace")
            }
            .padding(.top, 12)

            Button(action: onStart) {
                HStack(spacing: 8) {
                    Image(systemName: "play.fill").font(.system(size: 14, weight: .bold))
                    Text("Start guided run").font(.runButton)
                }
                .foregroundStyle(.runTextOnLime)
                .frame(maxWidth: .infinity).padding(.vertical, 13)
                .background(RoundedRectangle(cornerRadius: 14).fill(Color.runLime))
            }
            .buttonStyle(.plain)
            .padding(.top, 16)
        }
        .padding(18)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            RoundedRectangle(cornerRadius: 22).fill(LinearGradient.runSession)
                .overlay(alignment: .bottomTrailing) {
                    Circle().fill(Color.runLime.opacity(0.14))
                        .frame(width: 130, height: 130).offset(x: 34, y: 34)
                }
                .clipShape(RoundedRectangle(cornerRadius: 22))
        )
        .shadow(color: Color.runIndigo.opacity(0.30), radius: 28, y: 10)
        .scaleEffect(appeared ? 1 : 0.97).opacity(appeared ? 1 : 0)
        .onAppear { withAnimation(.easeOut(duration: 0.3)) { appeared = true } }
    }

    private func stat(_ v: String, _ l: String) -> some View {
        VStack(alignment: .leading, spacing: 2) {
            Text(v).font(.runStatValue).foregroundStyle(.white)
            Text(l).font(.sora(10, .semibold)).textCase(.uppercase)
                .foregroundStyle(.white.opacity(0.7))
        }
    }
}
```

### Workout-Structure Breakdown

```swift
struct WorkoutStep: Identifiable {
    let id = UUID()
    let code: String       // "WU" / "6×" / "CD"
    let title: String
    let detail: String
    let pace: String       // "4:35"
    let color: Color       // phase color
    let flex: CGFloat      // segment proportion
}

struct StructureBreakdown: View {
    let steps: [WorkoutStep]
    @State private var grow: CGFloat = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text("Workout structure").font(.sora(13, .bold))
                .foregroundStyle(.runTextPrimary).padding(.bottom, 10)

            GeometryReader { g in
                HStack(spacing: 3) {
                    ForEach(steps) { s in
                        Rectangle().fill(s.color)
                            .frame(width: max(0, (g.size.width - CGFloat(steps.count - 1) * 3)
                                              * s.flex / steps.map(\.flex).reduce(0,+) * grow))
                    }
                }
            }
            .frame(height: 30)
            .clipShape(RoundedRectangle(cornerRadius: 8))

            ForEach(steps) { s in
                HStack(spacing: 12) {
                    Text(s.code).font(.sora(11, .heavy)).foregroundStyle(.runTextOnLime)
                        .frame(width: 32, height: 32)
                        .background(RoundedRectangle(cornerRadius: 9).fill(s.color))
                    VStack(alignment: .leading, spacing: 2) {
                        Text(s.title).font(.sora(15, .bold)).foregroundStyle(.runTextPrimary)
                        Text(s.detail).font(.sora(11, .medium)).foregroundStyle(.runTextSecondary)
                    }
                    Spacer()
                    VStack(alignment: .trailing, spacing: 0) {
                        Text(s.pace).font(.sora(13, .heavy)).foregroundStyle(.runTextPrimary)
                        Text("/km").font(.sora(10, .semibold)).foregroundStyle(.runTextTertiary)
                    }
                }
                .padding(.vertical, 10)
                .overlay(Divider().background(Color.runDivider), alignment: .bottom)
            }
        }
        .onAppear { withAnimation(.easeOut(duration: 0.5)) { grow = 1 } }
    }
}
```

### Primary "Go" Button

```swift
struct RunGoButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 14, weight: .bold))
                Text(title).font(.runButton)
            }
            .foregroundStyle(.runTextOnLime)
            .frame(maxWidth: .infinity).padding(.vertical, 15)
        }
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.runLime))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct RunTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundColor = UIColor(Color.runCanvas.opacity(0.94))
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            PlanView().tabItem     { Label("Plan",     systemImage: "calendar") }
            RunView().tabItem      { Label("Run",      systemImage: "figure.run") }
            ProgressView().tabItem { Label("Progress", systemImage: "chart.line.uptrend.xyaxis") }
            ClubView().tabItem     { Label("Club",     systemImage: "person.2.fill") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person.crop.circle") }
        }
        .tint(.runLime)   // active tab = Lime, no tint pill
    }
}
```

## 5. Motion

```swift
// Week strip stagger-fade
.opacity(appeared ? 1 : 0)
.animation(.easeOut(duration: 0.22).delay(Double(idx) * 0.04), value: appeared)
// Today cell one-shot pulse
.scaleEffect(pulse ? 1.04 : 1.0)
// withAnimation(.easeInOut(duration: 0.3)) { pulse = true } then back

// Session card entrance
.onAppear { withAnimation(.easeOut(duration: 0.3)) { appeared = true } }

// Structure bar grow
.onAppear { withAnimation(.easeOut(duration: 0.5)) { grow = 1 } }

// Start run press + haptic, then slide to live run
.sensoryFeedback(.impact(flexibility: .soft), trigger: startTapped)
// NavigationStack push or fullScreenCover slide-up (~350ms)

// Live run: km haptic + pace count
.sensoryFeedback(.impact(weight: .medium), trigger: kmCount)
Text(pace).contentTransition(.numericText())

// PR celebration
withAnimation(.spring(response: 0.4, dampingFraction: 0.6)) { showPR = true }
.sensoryFeedback(.success, trigger: prAchieved)

// Segmented control
withAnimation(.easeOut(duration: 0.22)) { selectedSeg = idx }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Plan (tab) | `calendar` | 22pt |
| Run (tab) | `figure.run` | 22pt |
| Progress (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Club (tab) | `person.2` / `person.2.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Start run | `play.fill` | 14pt |
| Pause / stop (live) | `pause.fill` / `stop.fill` | 20pt |
| Week switcher | `chevron.down` | 12pt |
| Completed step | `checkmark` | 14pt |
| PR / achievement | `trophy.fill` / `bolt.fill` | 16pt |
| Pace / speed | `speedometer` | 16pt |
| Distance | `figure.run` / `point.topleft.down.curvedto.point.bottomright.up` | 16pt |
| Route map | `map` | 18pt |
| Kudos | `hand.thumbsup.fill` | 16pt |
| Back / close | `chevron.left` / `xmark` | 20pt |
| Settings | `gearshape` | 22pt |

## 7. Dark Mode

```swift
struct RunTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content.background(scheme == .dark ? Color.runCanvas : Color.runLightCanvas)
    }
}
extension View { func runTheme() -> some View { modifier(RunTheme()) } }
```

Runna is **dark-first** — the Indigo-tinted blue-black `#0E0E16` canvas is the default and primary experience. The optional light theme uses `#F6F6FB` with white cards. **Runna Indigo `#4F46E5`, Runna Lime `#C2F94E`, and the run-type system (`#34D399` / `#FBBF24` / `#FB7185` / `#4F46E5`) are identical in both themes** — athletes read effort and state by color. On dark, depth is the `#181826` surface lift + `#2C2C42` border (drop shadows read poorly); the session card keeps its faint indigo glow. In light mode, cards gain a soft indigo-tinted shadow `Color.runIndigo.opacity(0.10)`. Lime always pairs with near-black text in both modes.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a plain text swap)
- Bundle Sora TTFs (Regular / Medium / SemiBold / Bold / ExtraBold) via `Info.plist` `UIAppFonts` — SIL OFL, free to ship
- Dynamic Type: scale screen title, plan/session title, section, card title, body, step item; keep eyebrow, pace/tag, day-cell number, stat labels, tab labels FIXED. Cap session title at +25%
- **Run-type accessibility**: never rely on color alone for the week strip / run types — pair the dot/cell with a state in the accessibility label ("Friday 16, today, intervals") and use the run-type name in step descriptions
- VoiceOver: announce the session card as "Today, intervals: 6 by 800 meters at 5K pace, 8.4 km, about 48 minutes, average pace 4:52"; the structure as an ordered list of steps with pace targets; the live run as periodic spoken pace/distance
- Tabular numerals: use a monospaced-digit font feature on the live clock/pace/distance so the run screen doesn't shift
- Color contrast: `#F3F3FB` on `#0E0E16`/`#181826` passes WCAG AA; near-black `#1A1A1A` on Lime `#C2F94E` passes AA strongly; white on the Indigo gradient passes AA at 18pt weight 700
- Reduce Motion: replace stagger/scale/grow with 200ms cross-fades; week strip & structure bar render at final state; no today-cell pulse; PR celebration becomes a static badge
- Reduce Transparency: replace the blurred tab-bar background with an opaque `#0E0E16`
- Live Activity / Watch: mirror pace, distance, current step and Lime progress; keep tabular numerals and high contrast for glance-ability mid-run
