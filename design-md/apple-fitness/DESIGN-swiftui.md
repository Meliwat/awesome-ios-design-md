# Apple Fitness (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Apple Fitness's rings-on-black visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions (system SF Pro), `ViewModifier`s, and the signature Activity-rings + Fitness+ components.

> Apple Fitness *is* an Apple app, so this guide leans on system primitives — `Color.primary`/`.secondary`, `.thinMaterial`, the system grouped background — wherever Apple would. The three ring colors and the chrome accent are the only custom tokens.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - The Three Activity Rings (the brand — immutable)
    static let afMove        = Color(red: 0.980, green: 0.067, blue: 0.310)   // #FA114F
    static let afMoveLabel   = Color(red: 1.0,   green: 0.216, blue: 0.373)   // #FF375F (brightened on dark)
    static let afExercise    = Color(red: 0.573, green: 0.910, blue: 0.165)   // #92E82A
    static let afExerciseHi  = Color(red: 0.400, green: 1.0,   blue: 0.0)     // #66FF00
    static let afStand       = Color(red: 0.118, green: 0.894, blue: 0.886)   // #1EE4E1
    static let afStandHi     = Color(red: 0.0,   green: 0.941, blue: 1.0)     // #00F0FF

    // Ring track = ring color @ 22% opacity
    static var afMoveTrack:     Color { afMove.opacity(0.22) }
    static var afExerciseTrack: Color { afExercise.opacity(0.22) }
    static var afStandTrack:    Color { afStand.opacity(0.22) }

    // MARK: - Chrome Accent (single)
    static let afAccent        = Color(red: 1.0, green: 0.216, blue: 0.373)   // #FF375F
    static let afAccentPressed = Color(red: 0.847, green: 0.055, blue: 0.271) // #D80E45
    static let afFitnessPlus   = Color(red: 0.788, green: 0.412, blue: 0.878) // #C969E0

    // MARK: - Surfaces (dark — iPhone primary)
    static let afCanvas    = Color.black                                      // #000000
    static let afGrouped1  = Color(red: 0.110, green: 0.110, blue: 0.118)     // #1C1C1E
    static let afGrouped2  = Color(red: 0.173, green: 0.173, blue: 0.180)     // #2C2C2E
    static let afGrouped3  = Color(red: 0.227, green: 0.227, blue: 0.235)     // #3A3A3C
    static let afSeparator = Color(red: 0.220, green: 0.220, blue: 0.227)     // #38383A
    static let afFill      = Color(red: 0.463, green: 0.463, blue: 0.502).opacity(0.24) // system fill

    // MARK: - Semantic
    static let afSuccess  = Color(red: 0.188, green: 0.820, blue: 0.345)      // #30D158
    static let afError    = Color(red: 1.0,   green: 0.271, blue: 0.227)      // #FF453A
    static let afAwardGold = Color(red: 1.0,  green: 0.839, blue: 0.039)      // #FFD60A
}

// Text: use system label opacities — do NOT hardcode grays
// Color.primary  → label primary
// Color.secondary → secondaryLabel (≈ white @ 60% on dark)
// Color.secondary.opacity(0.5) or a tertiary helper for tertiaryLabel
```

## 2. Typography

Apple Fitness uses the **system font (SF Pro)** via Apple's text styles — no bundling. Numerals tabular.

```swift
extension Font {
    // Built on Apple's text styles; weights tuned to Fitness's bolder display
    static let afLargeTitle = Font.system(size: 40, weight: .heavy,    design: .default)
    static let afDate       = Font.system(size: 32, weight: .heavy,    design: .default)
    static let afHeader     = Font.system(size: 26, weight: .heavy,    design: .default)
    static let afSection    = Font.system(size: 22, weight: .heavy,    design: .default)
    static let afTitle3     = Font.system(size: 20, weight: .bold,     design: .default)
    static let afBody       = Font.system(size: 17, weight: .semibold, design: .default)
    static let afBodyReg    = Font.system(size: 17, weight: .regular,  design: .default)
    static let afRingValue  = Font.system(size: 19, weight: .heavy,    design: .default)
    static let afTileValue  = Font.system(size: 22, weight: .heavy,    design: .default)
    static let afCardTitle  = Font.system(size: 15, weight: .semibold, design: .default)
    static let afFootnote   = Font.system(size: 13, weight: .regular,  design: .default)
    static let afEyebrow    = Font.system(size: 12, weight: .bold,     design: .default)
    static let afButton     = Font.system(size: 17, weight: .semibold, design: .default)
    static let afTab        = Font.system(size: 10, weight: .medium,   design: .default)
    static let afBadge      = Font.system(size: 10, weight: .bold,     design: .default)
}

extension View {
    func afTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Activity Rings

```swift
struct ActivityRings: View {
    var move: Double      // 0...1 (can exceed 1 for overshoot)
    var exercise: Double
    var stand: Double
    var lineWidth: CGFloat = 14
    @State private var animate = false

    var body: some View {
        ZStack {
            ring(progress: animate ? move : 0,
                 color: .afMove, track: .afMoveTrack, inset: 0)
            ring(progress: animate ? exercise : 0,
                 color: .afExercise, track: .afExerciseTrack, inset: lineWidth + 3)
            ring(progress: animate ? stand : 0,
                 color: .afStand, track: .afStandTrack, inset: (lineWidth + 3) * 2)
        }
        .onAppear {
            withAnimation(.easeOut(duration: 1.0)) { animate = true }
        }
    }

    @ViewBuilder
    private func ring(progress: Double, color: Color, track: Color, inset: CGFloat) -> some View {
        ZStack {
            Circle()
                .stroke(track, style: StrokeStyle(lineWidth: lineWidth, lineCap: .round))
            Circle()
                .trim(from: 0, to: min(progress, 1.0))
                .stroke(color, style: StrokeStyle(lineWidth: lineWidth, lineCap: .round))
                .rotationEffect(.degrees(-90))
                .shadow(color: color.opacity(0.55), radius: 5)   // soft "lit" glow, not a drop shadow
        }
        .padding(inset)
    }
}
```

### Ring Legend

```swift
struct RingLegend: View {
    struct Item: Identifiable {
        let id = UUID()
        let name: String          // "Move"
        let value: String         // "486"
        let goal: String          // "/620 KCAL"
        let color: Color          // label-brightened ring color
    }
    let items: [Item]

    var body: some View {
        VStack(alignment: .leading, spacing: 14) {
            ForEach(items) { it in
                VStack(alignment: .leading, spacing: 2) {
                    Text(it.name.uppercased())
                        .font(.afEyebrow).tracking(0.4)
                        .foregroundStyle(it.color)
                    HStack(alignment: .firstTextBaseline, spacing: 4) {
                        Text(it.value).font(.afRingValue).foregroundStyle(.primary).afTabular()
                        Text(it.goal).font(.system(size: 12, weight: .semibold))
                            .foregroundStyle(.secondary)
                    }
                }
            }
        }
    }
}
```

### Activity Rings Hero Card

```swift
struct RingHeroCard: View {
    var body: some View {
        HStack(spacing: 20) {
            ActivityRings(move: 0.80, exercise: 1.27, stand: 0.75)
                .frame(width: 130, height: 130)
            RingLegend(items: [
                .init(name: "Move", value: "486", goal: "/620 KCAL", color: .afMoveLabel),
                .init(name: "Exercise", value: "38", goal: "/30 MIN", color: .afExercise),
                .init(name: "Stand", value: "9", goal: "/12 HR", color: .afStand),
            ])
            Spacer()
        }
        .padding(EdgeInsets(top: 22, leading: 18, bottom: 22, trailing: 18))
        .background(Color.afGrouped1, in: RoundedRectangle(cornerRadius: 18, style: .continuous))
        .padding(.horizontal, 16)
    }
}
```

### Metric Tile

```swift
struct MetricTile: View {
    let symbol: String       // SF Symbol
    let tint: Color
    let name: String
    let value: String
    let unit: String
    let sub: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 6) {
                Image(systemName: symbol).font(.system(size: 16, weight: .bold)).foregroundStyle(tint)
                Text(name).font(.system(size: 12, weight: .semibold)).foregroundStyle(.secondary)
            }
            HStack(alignment: .firstTextBaseline, spacing: 2) {
                Text(value).font(.afTileValue).foregroundStyle(.primary).afTabular()
                if !unit.isEmpty {
                    Text(unit).font(.system(size: 13, weight: .semibold)).foregroundStyle(.secondary)
                }
            }
            .padding(.top, 8)
            Text(sub).font(.system(size: 12)).foregroundStyle(.tertiary).padding(.top, 2)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(14)
        .background(Color.afGrouped1, in: RoundedRectangle(cornerRadius: 14, style: .continuous))
    }
}
```

### Fitness+ Card

```swift
struct FitnessPlusCard: View {
    let badge: String          // "New"
    let type: String           // "HIIT"
    let title: String          // "20 min HIIT"
    let meta: String           // "Bakari · Pop Anthems"
    let gradient: [Color]

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topLeading) {
                LinearGradient(colors: gradient, startPoint: .topLeading, endPoint: .bottomTrailing)
                    .frame(width: 168, height: 200)
                    .clipShape(RoundedRectangle(cornerRadius: 16, style: .continuous))

                Text(badge.uppercased())
                    .font(.afBadge).tracking(0.5).foregroundStyle(.white)
                    .padding(.horizontal, 8).padding(.vertical, 4)
                    .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 6))
                    .padding(12)

                Image(systemName: "play.fill")
                    .font(.system(size: 14, weight: .bold)).foregroundStyle(.white)
                    .frame(width: 34, height: 34)
                    .background(.ultraThinMaterial, in: Circle())
                    .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .bottomLeading)
                    .padding(14)
            }
            Text(type.uppercased()).font(.afEyebrow).tracking(0.4)
                .foregroundStyle(Color.afMoveLabel).padding(.top, 10)
            Text(title).font(.afCardTitle).foregroundStyle(.primary).padding(.top, 3)
            Text(meta).font(.afFootnote).foregroundStyle(.secondary).padding(.top, 2)
        }
        .frame(width: 168)
    }
}
```

### Buttons

```swift
struct AFPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.afButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 15)
        }
        .background(Color.afMove, in: RoundedRectangle(cornerRadius: 14, style: .continuous))
        .buttonStyle(.plain)
    }
}

struct AFTintedButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.system(size: 15, weight: .semibold))
                .foregroundStyle(Color.afAccent)
                .padding(.vertical, 12).padding(.horizontal, 22)
        }
        .background(Color.afMove.opacity(0.18), in: RoundedRectangle(cornerRadius: 12, style: .continuous))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct AFTabView: View {
    var body: some View {
        TabView {
            SummaryView().tabItem { Label("Summary", systemImage: "circle.circle.fill") }
            FitnessPlusView().tabItem { Label("Fitness+", systemImage: "play.rectangle.fill") }
            SharingView().tabItem { Label("Sharing", systemImage: "person.2.fill") }
        }
        .tint(.afAccent)                                       // Move-pink accent, system tint (no pill)
        .toolbarBackground(.thinMaterial, for: .tabBar)        // Apple blur material
        .toolbarColorScheme(.dark, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Rings sweep from 0 (staggered Move → Exercise → Stand)
withAnimation(.easeOut(duration: 1.0)) { animateMove = true }
withAnimation(.easeOut(duration: 1.0).delay(0.08)) { animateExercise = true }
withAnimation(.easeOut(duration: 1.0).delay(0.16)) { animateStand = true }

// Ring close celebration + haptic
if ringProgress >= 1.0 {
    // particle burst in the ring color (e.g. a TimelineView/Canvas emitter)
}
.sensoryFeedback(.success, trigger: ringDidClose)

// Card press
.scaleEffect(pressed ? 0.97 : 1).animation(.spring(response: 0.3, dampingFraction: 0.7), value: pressed)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Start Workout
.sensoryFeedback(.impact(weight: .medium), trigger: didStartWorkout)

// Live HUD updates (in-workout) — smooth ease as metrics change
withAnimation(.easeInOut(duration: 0.3)) { liveMove = newValue }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Summary (tab) | `circle.circle` / `circle.circle.fill` | 24pt |
| Fitness+ (tab) | `play.rectangle` / `.fill` | 24pt |
| Sharing (tab) | `person.2` / `person.2.fill` | 24pt |
| Steps | `figure.walk` / `chart.bar.fill` | 16pt |
| Distance | `point.topleft.down.curvedto.point.bottomright.up` | 16pt |
| Heart Rate | `heart.fill` | 16pt |
| Workouts | `figure.run` / `clock.fill` | 16pt |
| Play (card) | `play.fill` | 14pt |
| Move ring detail | `flame.fill` | 18pt |
| Exercise ring detail | `figure.run` | 18pt |
| Stand ring detail | `figure.stand` | 18pt |
| Award | `medal.fill` / `trophy.fill` | 18pt |
| Share | `square.and.arrow.up` | 20pt |
| See All / disclosure | `chevron.right` | 13pt |
| Search (Fitness+) | `magnifyingglass` | 15pt |
| Trends up / down | `arrow.up.right` / `arrow.down.right` | 14pt |

## 7. Dark Mode

```swift
struct AFTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.afCanvas)            // true black on iPhone
            .tint(.afAccent)
            .preferredColorScheme(.dark)           // iPhone Fitness is dark-first
    }
}
extension View { func afTheme() -> some View { modifier(AFTheme()) } }
```

Apple Fitness is **dark-first on iPhone** — pin `.preferredColorScheme(.dark)` and render true black. On iPad it can use the light system grouped background. **Critically: the three ring colors (Move `#FA114F`, Exercise `#92E82A`, Stand `#1EE4E1`) and their 22%-opacity tracks NEVER change between modes — they are the brand.** Use system semantic colors (`Color.primary`/`.secondary`/`.tertiary`, `.thinMaterial`, the grouped background) for everything else so the rest adapts correctly while the rings stay constant.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UINotificationFeedbackGenerator` / `UIImpactFeedbackGenerator`; `.continuous` RoundedRectangle and `.thinMaterial` are fine at 16)
- Uses the **system font** — no font bundling. For a cross-platform port, substitute `Inter` (SIL OFL)
- Always `.monospacedDigit()` on the ring legend, metric tiles, and in-workout HUD so values don't jitter
- Dynamic Type: support all titles/body/footnotes through accessibility sizes; pin the ring legend numerals, in-workout HUD, tab labels, and frosted badges; ring diameter scales with width, not type
- VoiceOver: announce rings as "Move ring, 486 of 620 kilocalories, 78 percent"; metric tiles "Steps, 8,214, average 7,540"; the rings card should be a single grouped element with a combined summary; Fitness+ cards "20 minute HIIT with Bakari, Pop Anthems playlist"
- Color is never the only signal: ring legend rows carry the text label + value beside the colored ring; trend arrows pair color with an up/down glyph
- Contrast: white on `#000000` is maximal; `#FF375F` on `#000000` passes AA for the accent; ring colors on black are vivid by design — keep ring *labels* at the brightened variants for text contrast
- Differentiate Without Color: the three rings are also distinguishable by radius/position; never rely on hue alone for the activity meaning
- Reduce Motion: rings set their final value with a 250ms crossfade (no sweep), no particle burst (use a static "closed" check ring), no card scale, no shelf parallax, awards crossfade instead of 3D flip
- Reduce Transparency: replace `.thinMaterial`/`.ultraThinMaterial` with solid `#1C1C1E` (tab bar) and a solid dark scrim (frosted overlays)
- Dark mode: surfaces step `#000000 → #1C1C1E → #2C2C2E`; modal layers use `.regularMaterial` + a `rgba(0,0,0,0.4)` scrim — never custom drop shadows on grouped cards
