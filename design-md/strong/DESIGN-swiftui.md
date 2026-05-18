# Strong (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Strong's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the set-log table.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default)
    static let strongCanvas   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let strongCard     = Color(red: 0.141, green: 0.141, blue: 0.141) // #242424
    static let strongInput    = Color(red: 0.180, green: 0.180, blue: 0.180) // #2E2E2E
    static let strongSurface3 = Color(red: 0.220, green: 0.220, blue: 0.220) // #383838
    static let strongDivider  = Color(red: 0.200, green: 0.200, blue: 0.200) // #333333

    // MARK: - Canvas & Surfaces (Light — optional)
    static let strongLightCanvas  = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let strongLightCard    = Color.white                                   // #FFFFFF
    static let strongLightInput   = Color(red: 0.922, green: 0.922, blue: 0.922) // #EBEBEB
    static let strongLightDivider = Color(red: 0.867, green: 0.867, blue: 0.867) // #DDDDDD

    // MARK: - Brand (single accent)
    static let strongBlue        = Color(red: 0.184, green: 0.502, blue: 0.929) // #2F80ED
    static let strongBluePressed = Color(red: 0.145, green: 0.400, blue: 0.753) // #2566C0
    static let strongBlueSoft    = Color(red: 0.086, green: 0.149, blue: 0.239) // #16263D

    // MARK: - Text
    static let strongTextPrimary   = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let strongTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let strongTextTertiary  = Color(red: 0.388, green: 0.388, blue: 0.388) // #636363

    // MARK: - Semantic
    static let strongDone  = Color(red: 0.153, green: 0.682, blue: 0.376) // #27AE60
    static let strongPR    = Color(red: 0.949, green: 0.788, blue: 0.298) // #F2C94C
    static let strongError = Color(red: 0.922, green: 0.341, blue: 0.341) // #EB5757
    static let strongWarn  = Color(red: 0.949, green: 0.600, blue: 0.290) // #F2994A
}
```

## 2. Typography

Strong ships one face — **Inter** — and uses tabular figures for every number. Bundle the Inter TTFs via `Info.plist` / `UIAppFonts`. Apply `.monospacedDigit()` on every numeric view.

```swift
extension Font {
    static let strongScreenTitle = Font.custom("Inter-ExtraBold", size: 32)               // 800
    static let strongWorkout     = Font.custom("Inter-ExtraBold", size: 26)               // 800
    static let strongSection     = Font.custom("Inter-Bold",      size: 22)               // 700
    static let strongExercise    = Font.custom("Inter-Bold",      size: 18)               // 700
    static let strongMetric      = Font.custom("Inter-Bold",      size: 17).monospacedDigit()
    static let strongBody        = Font.custom("Inter-Regular",   size: 16)               // 400
    static let strongSetCell     = Font.custom("Inter-Bold",      size: 15).monospacedDigit()
    static let strongRowTitle    = Font.custom("Inter-SemiBold",  size: 15)               // 600
    static let strongCaption     = Font.custom("Inter-Medium",    size: 13)               // 500
    static let strongColumn      = Font.custom("Inter-Bold",      size: 11)               // 700 uppercase
    static let strongTab         = Font.custom("Inter-SemiBold",  size: 10)               // 600
    static let strongButton      = Font.custom("Inter-Bold",      size: 16)               // 700
    static let strongFlag        = Font.custom("Inter-ExtraBold", size: 10)               // 800 uppercase
}
```

## 3. Signature Components

### Active-Workout Header (title + stat pills)

```swift
struct StrongWorkoutHeader: View {
    let title: String
    let time: String, volume: String
    let setCount: Int
    let onFinish: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack {
                Text(title).font(.strongWorkout).foregroundStyle(Color.strongTextPrimary)
                Spacer()
                Button(action: onFinish) {
                    Text("Finish")
                        .font(.custom("Inter-Bold", size: 13)).foregroundStyle(.white)
                        .padding(.vertical, 8).padding(.horizontal, 16)
                        .background(RoundedRectangle(cornerRadius: 8).fill(Color.strongBlue))
                }
            }
            HStack(spacing: 8) {
                pill("Time", time); pill("Volume", volume); pill("Sets", "\(setCount)")
            }
        }
        .padding(.horizontal, 18).padding(.bottom, 10)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.strongDivider).frame(height: 0.5)
        }
    }

    private func pill(_ label: String, _ value: String) -> some View {
        HStack(spacing: 4) {
            Text(label).font(.custom("Inter-SemiBold", size: 12)).foregroundStyle(Color.strongTextSecondary)
            Text(value).font(.custom("Inter-Bold", size: 12).monospacedDigit()).foregroundStyle(Color.strongBlue)
        }
        .padding(.vertical, 5).padding(.horizontal, 10)
        .background(RoundedRectangle(cornerRadius: 7).fill(Color.strongInput))
    }
}
```

### Set Row (the core atom)

```swift
struct StrongSetRow: View {
    let label: String          // "1", "W", "D", "F"
    let isWarmup: Bool
    let previous: String       // "95 × 5" or "—"
    @State var weight: String
    @State var reps: String
    @State var done = false
    @FocusState private var focused: Field?
    enum Field { case weight, reps }

    var body: some View {
        HStack(spacing: 8) {
            Text(label)
                .font(.custom("Inter-Bold", size: 14))
                .foregroundStyle(isWarmup ? Color.strongWarn : Color.strongTextSecondary)
                .frame(width: 36)

            Text(previous)
                .font(.custom("Inter-Regular", size: 12).monospacedDigit())
                .foregroundStyle(Color.strongTextTertiary)
                .frame(maxWidth: .infinity)

            cell($weight, .weight)
            cell($reps, .reps)

            Button {
                withAnimation(.easeOut(duration: 0.14)) { done.toggle() }
            } label: {
                ZStack {
                    RoundedRectangle(cornerRadius: 7)
                        .fill(done ? Color.strongDone : .clear)
                        .overlay(RoundedRectangle(cornerRadius: 7)
                            .strokeBorder(done ? Color.strongDone : Color.strongDivider, lineWidth: 1.5))
                    if done { Image(systemName: "checkmark").font(.system(size: 12, weight: .heavy)).foregroundStyle(.white) }
                }
                .frame(width: 24, height: 24)
            }
            .sensoryFeedback(.success, trigger: done)
        }
        .padding(.vertical, 6)
    }

    private func cell(_ text: Binding<String>, _ field: Field) -> some View {
        TextField(previous.components(separatedBy: " ").first ?? "0", text: text)
            .font(.strongSetCell)
            .multilineTextAlignment(.center)
            .foregroundStyle(done ? Color.strongDone : Color.strongTextPrimary)
            .keyboardType(.decimalPad)
            .focused($focused, equals: field)
            .frame(maxWidth: .infinity).frame(height: 34)
            .background(RoundedRectangle(cornerRadius: 7)
                .fill(done ? Color.strongDone.opacity(0.18) : Color.strongInput))
            .overlay(RoundedRectangle(cornerRadius: 7)
                .strokeBorder(focused == field ? Color.strongBlue : .clear, lineWidth: 1.5))
    }
}
```

### Rest-Timer Bar

```swift
struct StrongRestBar: View {
    let remaining: TimeInterval
    let total: TimeInterval
    let onAdjust: (Int) -> Void

    private var fill: Double { total == 0 ? 0 : remaining / total }

    var body: some View {
        GeometryReader { geo in
            ZStack(alignment: .leading) {
                RoundedRectangle(cornerRadius: 8).fill(Color.strongBlueSoft)
                RoundedRectangle(cornerRadius: 8)
                    .fill(Color.strongBlue.opacity(0.22))
                    .frame(width: geo.size.width * fill)
                HStack(spacing: 10) {
                    Image(systemName: "timer").font(.system(size: 16, weight: .bold))
                        .foregroundStyle(Color.strongBlue)
                    Text("Rest Timer · \(format(remaining))")
                        .font(.custom("Inter-Bold", size: 13).monospacedDigit())
                        .foregroundStyle(Color.strongBlue)
                    Spacer()
                    HStack(spacing: 10) {
                        Button("−15") { onAdjust(-15) }
                        Button("+15") { onAdjust(15) }
                    }
                    .font(.custom("Inter-Bold", size: 12))
                    .foregroundStyle(Color.strongTextSecondary)
                }
                .padding(.horizontal, 12)
            }
        }
        .frame(height: 40)
        .padding(.horizontal, 18)
        .animation(.linear(duration: total), value: fill)
    }

    private func format(_ t: TimeInterval) -> String {
        String(format: "%d:%02d", Int(t) / 60, Int(t) % 60)
    }
}
```

### PR Flag

```swift
struct StrongPRFlag: View {
    let text: String   // "▲ PR · Est. 1RM 117 kg"
    @State private var shown = false

    var body: some View {
        Text(text)
            .font(.strongFlag).textCase(.uppercase)
            .foregroundStyle(Color.strongPR)
            .padding(.vertical, 2).padding(.horizontal, 6)
            .background(RoundedRectangle(cornerRadius: 5).fill(Color.strongPR.opacity(0.18)))
            .scaleEffect(shown ? 1 : 0.92)
            .opacity(shown ? 1 : 0)
            .onAppear { withAnimation(.spring(response: 0.20, dampingFraction: 0.7)) { shown = true } }
            .sensoryFeedback(.impact(weight: .light), trigger: shown)
    }
}
```

### Exercise Card

```swift
struct StrongExerciseCard<Sets: View>: View {
    let name: String
    let iconSystemName: String
    let note: String?
    @ViewBuilder let sets: () -> Sets
    let onAddSet: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack(spacing: 10) {
                Image(systemName: iconSystemName)
                    .font(.system(size: 18, weight: .semibold)).foregroundStyle(Color.strongBlue)
                    .frame(width: 34, height: 34)
                    .background(RoundedRectangle(cornerRadius: 9).fill(Color.strongSurface3))
                Text(name).font(.strongExercise).foregroundStyle(Color.strongBlue)
                Spacer()
                Image(systemName: "ellipsis").foregroundStyle(Color.strongTextTertiary)
            }
            if let note { Text(note).font(.strongCaption).foregroundStyle(Color.strongTextSecondary) }
            sets()
            Button(action: onAddSet) {
                Text("+ Add Set")
                    .font(.custom("Inter-Bold", size: 13)).foregroundStyle(Color.strongBlue)
                    .frame(maxWidth: .infinity).frame(height: 34)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.strongInput))
            }
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.strongCard)) // flat, NO border
    }
}
```

## 4. Bottom Tab Bar

```swift
struct StrongTabView: View {
    var body: some View {
        TabView {
            ProfileView().tabItem { Label("Profile", systemImage: "line.3.horizontal") }
            HistoryView().tabItem { Label("History", systemImage: "calendar") }
            WorkoutView().tabItem { Label("Workout", systemImage: "plus.circle.fill") }
            ExercisesView().tabItem { Label("Exercises", systemImage: "chart.bar.fill") }
            SettingsView().tabItem { Label("Settings", systemImage: "gearshape.fill") }
        }
        .tint(.strongBlue)   // active is Strong Blue, no pill indicator
    }
}
```

## 5. Motion

```swift
// Set check: fill box + green cells + success haptic + auto-run rest
withAnimation(.easeOut(duration: 0.14)) { done.toggle() }
.sensoryFeedback(.success, trigger: done)

// PR flag: fade + spring scale-in + light haptic
withAnimation(.spring(response: 0.20, dampingFraction: 0.7)) { shown = true }
.sensoryFeedback(.impact(weight: .light), trigger: shown)

// Add set: new row slides down
.transition(.move(edge: .top).combined(with: .opacity)) // 200ms ease-out

// Finish → post-workout summary (full-screen sheet)
.fullScreenCover(isPresented: $showSummary) { StrongSummaryView() } // 300ms

// Rest bar: inner fill depletes linearly
.animation(.linear(duration: total), value: fill)

// Haptics: .success on set check, .impact(.light) on rest ±/end and PR
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Profile (tab) | `line.3.horizontal` | 22pt |
| History (tab) | `calendar` | 22pt |
| Workout (tab, center) | `plus.circle.fill` | 22pt |
| Exercises (tab) | `chart.bar.fill` | 22pt |
| Settings (tab) | `gearshape.fill` | 22pt |
| Set checkmark | `checkmark` | 12pt |
| Exercise menu | `ellipsis` | 18pt |
| Rest timer | `timer` | 16pt |
| Add set / exercise | `plus` | 16pt |
| PR flag mark | `triangle.fill` | 10pt |
| Superset link | `link` | 16pt |
| Plate calculator | `circle.grid.2x2.fill` | 18pt |
| Reorder | `line.3.horizontal` | 18pt |
| Delete (swipe) | `trash` | 18pt |
| Volume / 1RM chart | `chart.xyaxis.line` | 18pt |
| Body measurements | `ruler` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Share summary | `square.and.arrow.up` | 20pt |

## 7. Dark Mode

```swift
struct StrongTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.strongCanvas : Color.strongLightCanvas)
            .foregroundStyle(scheme == .dark ? Color.strongTextPrimary
                                             : Color(red: 0.102, green: 0.102, blue: 0.102)) // #1A1A1A
            .preferredColorScheme(.dark) // Strong is dark-first; light is opt-in
    }
}

extension View { func strongTheme() -> some View { modifier(StrongTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator`; ActivityKit Live Activity for the rest timer requires iOS 16.1+, Dynamic Island iOS 16.2+)
- Bundle Inter TTFs via `Info.plist` `UIAppFonts` — Inter is SIL OFL licensed for free distribution
- Always apply `.monospacedDigit()` on weights, reps, set counts, volume, 1RM, and timers so the set table and timers never reflow
- Dynamic Type: scale screen titles, workout title, section headers, exercise names, body, and set-cell values; keep column labels, tab labels, the PR flag, and the set-index marker at FIXED sizes (table layout is digit-width-sensitive)
- VoiceOver: label a set row "Set {label}, previous {prev}, {weight} kilograms, {reps} reps, {logged/not logged}"; expose the checkmark as a toggle action; announce "Personal record, {detail}" when a PR flag appears
- Color contrast: `#F5F5F5` on `#1A1A1A` and `#2F80ED` on `#1A1A1A` pass WCAG AA; the green row fill is decorative — the checkmark + VoiceOver state carry completion, not color alone
- Reduce Motion: disable the PR spring/pulse and the add-set slide (use a crossfade); keep the rest-bar fill depletion (it conveys remaining time)
- Live Activity: mirror the rest countdown to Dynamic Island / Lock Screen as a slim progress bar + remaining time; keep digits monospaced there too
- Dark mode is the default (`.preferredColorScheme(.dark)`); a light theme is provided but opt-in via settings
- Exercise cards are intentionally borderless — do not add a stroke for "definition"; the surface lift is the design
