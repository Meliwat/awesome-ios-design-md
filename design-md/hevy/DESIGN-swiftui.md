# Hevy (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hevy's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the live workout log.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default)
    static let hevyCanvas    = Color(red: 0.055, green: 0.067, blue: 0.086) // #0E1116
    static let hevyCard      = Color(red: 0.086, green: 0.106, blue: 0.133) // #161B22
    static let hevyInput     = Color(red: 0.122, green: 0.149, blue: 0.188) // #1F2630
    static let hevySurface3  = Color(red: 0.157, green: 0.188, blue: 0.251) // #283040
    static let hevyDivider   = Color(red: 0.149, green: 0.173, blue: 0.212) // #262C36

    // MARK: - Canvas & Surfaces (Light — optional)
    static let hevyLightCanvas  = Color(red: 0.957, green: 0.965, blue: 0.980) // #F4F6FA
    static let hevyLightCard    = Color.white                                   // #FFFFFF
    static let hevyLightInput   = Color(red: 0.933, green: 0.945, blue: 0.965) // #EEF1F6
    static let hevyLightDivider = Color(red: 0.886, green: 0.902, blue: 0.933) // #E2E6EE

    // MARK: - Brand (single action color)
    static let hevyBlue        = Color(red: 0.118, green: 0.435, blue: 1.0)   // #1E6FFF
    static let hevyBluePressed = Color(red: 0.094, green: 0.341, blue: 0.800) // #1857CC
    static let hevyBlueSoft    = Color(red: 0.078, green: 0.137, blue: 0.247) // #14233F

    // MARK: - Text
    static let hevyTextPrimary   = Color(red: 0.949, green: 0.957, blue: 0.973) // #F2F4F8
    static let hevyTextSecondary = Color(red: 0.608, green: 0.639, blue: 0.690) // #9BA3B0
    static let hevyTextTertiary  = Color(red: 0.369, green: 0.400, blue: 0.459) // #5E6675

    // MARK: - Semantic
    static let hevyDone   = Color(red: 0.184, green: 0.749, blue: 0.443) // #2FBF71
    static let hevyPR     = Color(red: 0.961, green: 0.722, blue: 0.239) // #F5B83D
    static let hevyError  = Color(red: 0.945, green: 0.329, blue: 0.357) // #F1545B
    static let hevyWarn   = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
}
```

## 2. Typography

Hevy ships one face — **Inter** — and uses tabular figures for every number. Bundle the Inter TTFs via `Info.plist` / `UIAppFonts`. Apply `.monospacedDigit()` (or the `tnum` feature) on every numeric view.

```swift
extension Font {
    static let hevyScreenTitle = Font.custom("Inter-ExtraBold", size: 32)               // 800
    static let hevyRoutine     = Font.custom("Inter-Bold",      size: 26)               // 700
    static let hevySection     = Font.custom("Inter-Bold",      size: 22)               // 700
    static let hevyExercise    = Font.custom("Inter-Bold",      size: 18)               // 700
    static let hevyMetric      = Font.custom("Inter-Bold",      size: 17).monospacedDigit()
    static let hevyBody        = Font.custom("Inter-Regular",   size: 16)               // 400
    static let hevySetCell     = Font.custom("Inter-SemiBold",  size: 15).monospacedDigit()
    static let hevyCardTitle   = Font.custom("Inter-SemiBold",  size: 15)               // 600
    static let hevyCaption     = Font.custom("Inter-Medium",    size: 13)               // 500
    static let hevyColumn      = Font.custom("Inter-SemiBold",  size: 11)               // 600 uppercase
    static let hevyTab         = Font.custom("Inter-SemiBold",  size: 10)               // 600
    static let hevyButton      = Font.custom("Inter-Bold",      size: 16)               // 700
    static let hevyBadge       = Font.custom("Inter-ExtraBold", size: 10)               // 800 uppercase
}
```

## 3. Signature Components

### Active-Workout Header

```swift
struct WorkoutHeader: View {
    let routineName: String
    let duration: String   // "41:08"
    let volume: String     // "8,420 kg"
    let setCount: Int
    let onFinish: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Text(routineName)
                    .font(.hevyRoutine)
                    .foregroundStyle(Color.hevyTextPrimary)
                Spacer()
                Button(action: onFinish) {
                    Text("Finish")
                        .font(.custom("Inter-Bold", size: 13))
                        .foregroundStyle(.white)
                        .padding(.vertical, 7).padding(.horizontal, 16)
                        .background(RoundedRectangle(cornerRadius: 10).fill(Color.hevyBlue))
                }
            }
            HStack(spacing: 22) {
                stat("Duration", duration, live: true)
                stat("Volume", volume, live: false)
                stat("Sets", "\(setCount)", live: false)
            }
        }
        .padding(.horizontal, 18).padding(.bottom, 12)
    }

    private func stat(_ label: String, _ value: String, live: Bool) -> some View {
        VStack(alignment: .leading, spacing: 2) {
            Text(label).font(.custom("Inter-Medium", size: 11)).foregroundStyle(Color.hevyTextSecondary)
            Text(value).font(.hevyMetric).foregroundStyle(live ? Color.hevyBlue : Color.hevyTextPrimary)
        }
    }
}
```

### Set Row (the core atom)

```swift
struct SetRow: View {
    let index: Int
    let previous: String          // "60 × 10"
    @State var weight: String
    @State var reps: String
    @State var done = false
    @FocusState private var focused: Field?
    enum Field { case weight, reps }

    var body: some View {
        HStack(spacing: 10) {
            Text("\(index)")
                .font(.custom("Inter-Bold", size: 13))
                .foregroundStyle(Color.hevyTextSecondary)
                .frame(width: 28, height: 28)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.hevyInput))

            Text(previous)
                .font(.custom("Inter-Regular", size: 13).monospacedDigit())
                .foregroundStyle(Color.hevyTextTertiary)
                .frame(maxWidth: .infinity)

            cell($weight, field: .weight)
            cell($reps, field: .reps)

            Button {
                withAnimation(.easeOut(duration: 0.15)) { done.toggle() }
            } label: {
                ZStack {
                    RoundedRectangle(cornerRadius: 8)
                        .fill(done ? Color.hevyDone : Color.clear)
                        .overlay(RoundedRectangle(cornerRadius: 8)
                            .strokeBorder(done ? Color.hevyDone : Color.hevyDivider, lineWidth: 1.5))
                    if done {
                        Image(systemName: "checkmark")
                            .font(.system(size: 13, weight: .heavy)).foregroundStyle(.white)
                    }
                }
                .frame(width: 26, height: 26)
            }
            .sensoryFeedback(.success, trigger: done)
        }
        .padding(.vertical, 7)
        .background(done ? Color.hevyDone.opacity(0.16) : Color.clear)
    }

    private func cell(_ text: Binding<String>, field: Field) -> some View {
        TextField(previous.components(separatedBy: " ").first ?? "", text: text)
            .font(.hevySetCell)
            .multilineTextAlignment(.center)
            .foregroundStyle(done ? Color.hevyDone : Color.hevyTextPrimary)
            .keyboardType(.decimalPad)
            .focused($focused, equals: field)
            .frame(maxWidth: .infinity).frame(height: 36)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.hevyInput))
            .overlay(RoundedRectangle(cornerRadius: 8)
                .strokeBorder(focused == field ? Color.hevyBlue : .clear, lineWidth: 1.5))
    }
}
```

### Rest-Timer Pill

```swift
struct RestTimerPill: View {
    let remaining: TimeInterval
    let total: TimeInterval
    let onSkip: () -> Void

    private var progress: Double { total == 0 ? 0 : 1 - remaining / total }

    var body: some View {
        HStack(spacing: 10) {
            ZStack {
                Circle().stroke(Color.hevyBlue.opacity(0.3), lineWidth: 3)
                Circle().trim(from: 0, to: progress)
                    .stroke(Color.hevyBlue, style: .init(lineWidth: 3, lineCap: .round))
                    .rotationEffect(.degrees(-90))
            }
            .frame(width: 22, height: 22)

            Text("Rest · \(format(remaining))")
                .font(.custom("Inter-SemiBold", size: 14).monospacedDigit())
                .foregroundStyle(Color.hevyBlue)
            Spacer()
            Button("Skip", action: onSkip)
                .font(.custom("Inter-SemiBold", size: 13))
                .foregroundStyle(Color.hevyTextSecondary)
        }
        .padding(.horizontal, 14)
        .frame(height: 44)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.hevyBlueSoft))
        .overlay(RoundedRectangle(cornerRadius: 12)
            .strokeBorder(Color.hevyBlue.opacity(0.35), lineWidth: 1))
        .padding(.horizontal, 18)
    }

    private func format(_ t: TimeInterval) -> String {
        String(format: "%d:%02d", Int(t) / 60, Int(t) % 60)
    }
}
```

### PR Badge

```swift
struct PRBadge: View {
    let text: String   // "★ NEW PR · 1RM 87.5 kg"
    @State private var shown = false

    var body: some View {
        Text(text)
            .font(.hevyBadge)
            .textCase(.uppercase)
            .foregroundStyle(Color.hevyPR)
            .padding(.vertical, 3).padding(.horizontal, 7)
            .background(RoundedRectangle(cornerRadius: 6).fill(Color.hevyPR.opacity(0.18)))
            .scaleEffect(shown ? 1 : 0.9)
            .opacity(shown ? 1 : 0)
            .onAppear {
                withAnimation(.spring(response: 0.22, dampingFraction: 0.6)) { shown = true }
            }
            .sensoryFeedback(.impact(weight: .medium), trigger: shown)
    }
}
```

### Exercise Card

```swift
struct ExerciseCard<Sets: View>: View {
    let name: String
    let iconSystemName: String
    @ViewBuilder let sets: () -> Sets
    let onAddSet: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(spacing: 10) {
                Image(systemName: iconSystemName)
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(Color.hevyBlue)
                    .frame(width: 36, height: 36)
                    .background(RoundedRectangle(cornerRadius: 10).fill(Color.hevySurface3))
                Text(name).font(.hevyExercise).foregroundStyle(Color.hevyBlue)
                Spacer()
                Image(systemName: "ellipsis").foregroundStyle(Color.hevyTextTertiary)
            }
            sets()
            Button(action: onAddSet) {
                Text("+ Add Set")
                    .font(.custom("Inter-Bold", size: 13))
                    .foregroundStyle(Color.hevyTextSecondary)
                    .frame(maxWidth: .infinity).frame(height: 34)
                    .background(RoundedRectangle(cornerRadius: 9).fill(Color.hevyInput))
            }
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.hevyCard))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.hevyDivider, lineWidth: 1))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct HevyTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
            WorkoutView().tabItem { Label("Workout", systemImage: "plus.circle.fill") }
            HistoryView().tabItem { Label("History", systemImage: "calendar") }
            ExercisesView().tabItem { Label("Exercises", systemImage: "chart.bar.fill") }
        }
        .tint(.hevyBlue)   // active is Hevy Blue, no pill indicator
    }
}
```

## 5. Motion

```swift
// Set check: fill + green wash + success haptic + auto-start rest
withAnimation(.easeOut(duration: 0.15)) { done.toggle() }
.sensoryFeedback(.success, trigger: done)

// PR badge: spring scale-in + gold pulse + medium haptic
withAnimation(.spring(response: 0.22, dampingFraction: 0.6)) { shown = true }
.sensoryFeedback(.impact(weight: .medium), trigger: shown)

// Add set: new row slides down
.transition(.move(edge: .top).combined(with: .opacity)) // 200ms ease-out

// Finish → post-workout summary (full-screen sheet)
.fullScreenCover(isPresented: $showSummary) { WorkoutSummaryView() } // 320ms

// Rest timer ring: continuous linear sweep
.animation(.linear(duration: total), value: progress)

// Haptics: .success on set check, .impact(.medium) on PR, .impact(.light) on rest ±/end
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Workout (tab, center) | `plus.circle.fill` | 22pt |
| History (tab) | `calendar` | 22pt |
| Exercises (tab) | `chart.bar.fill` | 22pt |
| Set checkmark | `checkmark` | 13pt |
| Exercise menu | `ellipsis` | 18pt |
| Rest skip / timer | `timer` | 16pt |
| Add set / exercise | `plus` | 16pt |
| PR badge mark | `star.fill` | 10pt |
| Superset link | `link` | 16pt |
| Warm-up set | `flame` | 14pt |
| Reorder | `line.3.horizontal` | 18pt |
| Delete (swipe) | `trash` | 18pt |
| Volume chart | `chart.xyaxis.line` | 18pt |
| Kudos (feed) | `hand.thumbsup` / `.fill` | 18pt |
| Comment (feed) | `bubble.left` | 18pt |
| Share summary | `square.and.arrow.up` | 20pt |

## 7. Dark Mode

```swift
struct HevyTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.hevyCanvas : Color.hevyLightCanvas)
            .foregroundStyle(scheme == .dark ? Color.hevyTextPrimary
                                             : Color(red: 0.067, green: 0.082, blue: 0.110)) // #11151C
            .preferredColorScheme(.dark) // Hevy is dark-first; light is opt-in
    }
}

extension View { func hevyTheme() -> some View { modifier(HevyTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator`; ActivityKit Live Activity for the rest timer requires iOS 16.1+, Dynamic Island iOS 16.2+)
- Bundle Inter TTFs via `Info.plist` `UIAppFonts` — Inter is SIL OFL licensed for free distribution
- Always apply `.monospacedDigit()` on weights, reps, set counts, volume, 1RM, and timers so the set table and timers never reflow
- Dynamic Type: scale screen titles, routine names, exercise names, body, and set-cell values; keep column labels, tab labels, PR/streak badges, and the set-index chip at FIXED sizes (table layout is digit-width-sensitive)
- VoiceOver: label a set row as "Set {n}, previous {prev}, {weight} kilograms, {reps} reps, {completed/not completed}"; expose the checkmark as a toggle action; announce "New personal record, {detail}" when a PR badge appears
- Color contrast: `#F2F4F8` on `#0E1116` and `#1E6FFF` on `#0E1116` both pass WCAG AA; the green row-wash is decorative — the checkmark + VoiceOver state carry completion, not color alone
- Reduce Motion: disable the PR spring/pulse and the add-set slide (use a crossfade); keep the rest-ring sweep (it conveys remaining time)
- Live Activity: mirror the rest countdown to Dynamic Island / Lock Screen with the ring + remaining time; keep digits monospaced there too
- Dark mode is the default (`.preferredColorScheme(.dark)`); a light theme is provided but opt-in via settings
