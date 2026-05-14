# Headspace (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Headspace's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, themed components, and the signature breathing sphere animation.

Targets iOS 17+. `.sensoryFeedback`, `.containerRelativeFrame`, and continuous spring animations all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let hsButterCream   = Color(red: 1.00,  green: 0.969, blue: 0.906)  // #FFF7E7
    static let hsOffCream      = Color(red: 0.984, green: 0.945, blue: 0.855)  // #FBF1DA
    static let hsWarmGray      = Color(red: 0.910, green: 0.875, blue: 0.788)  // #E8DFC9
    static let hsSurfaceWhite  = Color(red: 1.00,  green: 1.00,  blue: 1.00)

    // MARK: - Brand
    static let hsMarigold        = Color(red: 1.00,  green: 0.420, blue: 0.208)  // #FF6B35
    static let hsMarigoldPressed = Color(red: 0.898, green: 0.353, blue: 0.169)  // #E55A2B
    static let hsMarigoldLight   = Color(red: 1.00,  green: 0.722, blue: 0.620)  // #FFB89E

    // MARK: - Aurora gradient stops
    static let hsAurora1         = Color(red: 1.00,  green: 0.722, blue: 0.620)  // #FFB89E
    static let hsAurora2         = Color(red: 1.00,  green: 0.824, blue: 0.361)  // #FFD25C
    static let hsAurora3         = Color(red: 1.00,  green: 0.420, blue: 0.208)  // #FF6B35

    // MARK: - Secondary accents
    static let hsButter          = Color(red: 1.00,  green: 0.824, blue: 0.361)  // #FFD25C
    static let hsSageMoss        = Color(red: 0.494, green: 0.624, blue: 0.294)  // #7E9F4B
    static let hsSageLight       = Color(red: 0.706, green: 0.776, blue: 0.561)  // #B4C68F
    static let hsCoralFlush      = Color(red: 0.957, green: 0.529, blue: 0.494)  // #F4877E
    static let hsSkyLavender     = Color(red: 0.718, green: 0.690, blue: 0.863)  // #B7B0DC

    // MARK: - Text (Ink Brown)
    static let hsInkBrown        = Color(red: 0.180, green: 0.102, blue: 0.278)  // #2E1A47
    static let hsInkBrownSoft    = Color(red: 0.349, green: 0.275, blue: 0.459)  // #594675
    static let hsInkBrownMute    = Color(red: 0.557, green: 0.490, blue: 0.647)  // #8E7DA5

    // MARK: - Dusk (Sleep mode)
    static let hsDuskCanvas      = Color(red: 0.102, green: 0.078, blue: 0.188)  // #1A1430
    static let hsDuskSurface1    = Color(red: 0.165, green: 0.125, blue: 0.275)  // #2A2046
    static let hsDuskSurface2    = Color(red: 0.227, green: 0.184, blue: 0.361)  // #3A2F5C
    static let hsDuskDivider     = Color(red: 0.247, green: 0.204, blue: 0.376)  // #3F3460

    // MARK: - Semantic
    static let hsSuccess         = Color(red: 0.494, green: 0.624, blue: 0.294)  // #7E9F4B (same as Sage)
    static let hsWarning         = Color(red: 1.00,  green: 0.624, blue: 0.290)  // #FF9F4A
    static let hsErrorCoral      = Color(red: 0.878, green: 0.275, blue: 0.275)  // #E04646

    // MARK: - Milestone
    static let hsMilestoneGold   = Color(red: 0.898, green: 0.722, blue: 0.361)  // #E5B85C
}
```

## 2. Typography

Apercu is licensed via Colophon Foundry. Bundle via `Info.plist`. Fall back to `Nunito` from Google Fonts (closest soft humanist sans) or system rounded.

```swift
extension Font {
    // Apercu — Headspace's house face
    static let hsGreetingHero   = Font.custom("Apercu-Bold",    size: 32)
    static let hsScreenTitle    = Font.custom("Apercu-Bold",    size: 28)
    static let hsMedTitle       = Font.custom("Apercu-Bold",    size: 22)
    static let hsSectionHdr     = Font.custom("Apercu-Bold",    size: 18)
    static let hsCardTitle      = Font.custom("Apercu-Medium",  size: 17)

    // Body — generous line height
    static let hsBody           = Font.custom("Apercu-Regular", size: 16)
    static let hsBodyBold       = Font.custom("Apercu-Medium",  size: 16)
    static let hsGuidance       = Font.custom("Apercu-Regular", size: 22)   // play-screen guidance text

    // Meta / captions
    static let hsMeta           = Font.custom("Apercu-Regular", size: 13)
    static let hsTagPill        = Font.custom("Apercu-Medium",  size: 13)
    static let hsCaption        = Font.custom("Apercu-Regular", size: 11)
    static let hsTab            = Font.custom("Apercu-Medium",  size: 11)

    // Buttons
    static let hsButton         = Font.custom("Apercu-Medium",  size: 17)   // Medium, not Bold

    // Timer / streaks (monospaced digit)
    static let hsTimerLg        = Font.custom("Apercu-Bold",    size: 56).monospacedDigit()
    static let hsStreakNum      = Font.custom("Apercu-Bold",    size: 22).monospacedDigit()

    // System fallback (when Apercu isn't bundled)
    static func headspace(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .rounded)   // SF Pro Rounded is the warmest system substitute
    }
}
```

## 3. Signature Components

### Breathing Sphere (the hero component)

```swift
struct BreathingSphere: View {
    let diameter: CGFloat
    @State private var scale: CGFloat = 1.0

    var body: some View {
        Circle()
            .fill(
                RadialGradient(
                    colors: [.hsAurora1, .hsMarigold, .hsMarigoldPressed],
                    center: .topLeading,
                    startRadius: 0,
                    endRadius: diameter
                )
            )
            .overlay(Circle().strokeBorder(Color.hsInkBrown.opacity(0.08), lineWidth: 1))
            .frame(width: diameter, height: diameter)
            .scaleEffect(scale)
            .onAppear { startBreath() }
    }

    func startBreath() {
        // 12-second breath cycle, infinite repeat:
        //   4s inhale: 1.0 → 1.04
        //   2s hold
        //   4s exhale: 1.04 → 0.96
        //   2s hold (returns to 1.0 next inhale)
        withAnimation(.easeInOut(duration: 4).repeatForever(autoreverses: false)) {
            scale = 1.04
        }
        // For more accurate 4-2-4-2 timing, drive via a Timer instead — see notes
    }
}
```

For an accurate 4-2-4-2 breath cadence, drive scale changes via a `Timer.publish` rather than `repeatForever`:

```swift
struct AccurateBreathingSphere: View {
    let diameter: CGFloat
    @State private var scale: CGFloat = 1.0
    @State private var phase = 0   // 0=inhale, 1=hold, 2=exhale, 3=hold-low

    var body: some View {
        Circle()
            .fill(RadialGradient(colors: [.hsAurora1, .hsMarigold, .hsMarigoldPressed], center: .topLeading, startRadius: 0, endRadius: diameter))
            .frame(width: diameter, height: diameter)
            .scaleEffect(scale)
            .task { await runBreath() }
    }

    @MainActor func runBreath() async {
        while !Task.isCancelled {
            withAnimation(.easeInOut(duration: 4)) { scale = 1.04 }
            try? await Task.sleep(for: .seconds(4))
            try? await Task.sleep(for: .seconds(2))    // hold high
            withAnimation(.easeInOut(duration: 4)) { scale = 0.96 }
            try? await Task.sleep(for: .seconds(4))
            withAnimation(.easeInOut(duration: 0)) { scale = 1.0 }
            try? await Task.sleep(for: .seconds(2))    // hold neutral
        }
    }
}
```

### Aurora Gradient Background (Today hero)

```swift
struct AuroraGradient: View {
    var body: some View {
        LinearGradient(
            colors: [.hsAurora1, .hsAurora2, .hsAurora3],
            startPoint: .top,
            endPoint: .bottom
        )
    }
}
```

### Today Hero

```swift
struct TodayHero: View {
    var body: some View {
        ZStack(alignment: .topLeading) {
            AuroraGradient()
                .frame(height: 280)
                .ignoresSafeArea(edges: .top)
            VStack(alignment: .leading, spacing: 6) {
                Text("Good morning, Sam")
                    .font(.hsGreetingHero)
                    .foregroundStyle(Color.hsInkBrown)
                Text("Today's vibe: Mindful Moment")
                    .font(.hsBody)
                    .foregroundStyle(Color.hsInkBrown.opacity(0.8))
            }
            .padding(.horizontal, 24)
            .padding(.top, 16)
        }
    }
}
```

### Primary Action Button

```swift
struct HeadspaceCTAButton: View {
    let title: String
    var onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            Text(title)
                .font(.hsButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 56)
                .background(
                    Capsule()
                        .fill(Color.hsMarigold)
                        .shadow(color: Color.hsMarigold.opacity(0.25), radius: 16, y: 6)
                )
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
        .padding(.horizontal, 24)
    }
}
```

### Meditation Session Card

```swift
struct MeditationCard: View {
    let illustration: Image
    let tag: String
    let title: String
    let host: String
    let duration: String

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            illustration
                .resizable()
                .scaledToFill()
                .frame(height: 200)
                .clipped()

            LinearGradient(
                colors: [.clear, Color.hsInkBrown.opacity(0.6)],
                startPoint: .top,
                endPoint: .bottom
            )

            VStack(alignment: .leading, spacing: 6) {
                Text(tag)
                    .font(.hsTagPill)
                    .foregroundStyle(.white)
                    .padding(.vertical, 4).padding(.horizontal, 12)
                    .background(Capsule().fill(Color.white.opacity(0.25)))
                Text(title)
                    .font(.hsMedTitle)
                    .foregroundStyle(.white)
                Text("\(host) · \(duration)")
                    .font(.hsMeta)
                    .foregroundStyle(.white.opacity(0.85))
            }
            .padding(16)
        }
        .frame(height: 200)
        .clipShape(RoundedRectangle(cornerRadius: 24))
        .shadow(color: Color.hsInkBrown.opacity(0.08), radius: 16, y: 4)
        .padding(.horizontal, 24)
    }
}
```

### Quick Action Tile

```swift
struct QuickActionTile: View {
    let tileColor: Color
    let illustration: Image
    let title: String
    let subtitle: String

    var body: some View {
        VStack(alignment: .center, spacing: 8) {
            illustration
                .resizable()
                .scaledToFit()
                .frame(width: 64, height: 64)
            Text(title)
                .font(.hsCardTitle)
                .foregroundStyle(Color.hsInkBrown)
                .multilineTextAlignment(.center)
            Text(subtitle)
                .font(.hsMeta)
                .foregroundStyle(Color.hsInkBrown.opacity(0.7))
        }
        .padding(16)
        .frame(width: 140, height: 180)
        .background(RoundedRectangle(cornerRadius: 20).fill(tileColor))
        .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
    }
}
```

### Player Controls

```swift
struct PlayerControls: View {
    @Binding var playing: Bool
    var onSkipBack: () -> Void
    var onSkipForward: () -> Void

    var body: some View {
        HStack(spacing: 32) {
            CircleControl(icon: "gobackward.15", action: onSkipBack)
            Button {
                playing.toggle()
            } label: {
                Image(systemName: playing ? "pause.fill" : "play.fill")
                    .font(.system(size: 28, weight: .bold))
                    .foregroundStyle(.white)
                    .frame(width: 72, height: 72)
                    .background(Circle().fill(Color.hsMarigold))
                    .shadow(color: Color.hsMarigold.opacity(0.25), radius: 16, y: 6)
            }
            .sensoryFeedback(.impact(flexibility: .soft), trigger: playing)
            CircleControl(icon: "goforward.15", action: onSkipForward)
        }
    }
}

struct CircleControl: View {
    let icon: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: icon)
                .font(.system(size: 22, weight: .medium))
                .foregroundStyle(Color.hsInkBrown)
                .frame(width: 48, height: 48)
                .background(Circle().fill(Color.hsOffCream))
        }
    }
}
```

### Streak Ring (Profile)

```swift
struct StreakRing: View {
    let currentStreak: Int
    let goal: Int = 30
    @State private var animatedProgress: Double = 0

    var body: some View {
        let progress = min(Double(currentStreak) / Double(goal), 1.0)
        ZStack {
            Circle()
                .stroke(Color.hsOffCream, style: StrokeStyle(lineWidth: 8))
            Circle()
                .trim(from: 0, to: animatedProgress)
                .stroke(Color.hsMarigold, style: StrokeStyle(lineWidth: 8, lineCap: .round))
                .rotationEffect(.degrees(-90))
            VStack(spacing: 0) {
                Text("\(currentStreak)")
                    .font(.hsTimerLg)
                    .foregroundStyle(Color.hsInkBrown)
                Text("days")
                    .font(.hsMeta)
                    .foregroundStyle(Color.hsInkBrownSoft)
            }
        }
        .frame(width: 120, height: 120)
        .onAppear {
            withAnimation(.easeOut(duration: 1.2)) {
                animatedProgress = progress
            }
        }
        .sensoryFeedback(.success, trigger: animatedProgress > 0)
    }
}
```

### Mood Tag Row

```swift
struct MoodTag: View {
    let emoji: String
    let label: String
    @Binding var selected: Bool

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) {
                selected.toggle()
            }
        } label: {
            VStack(spacing: 6) {
                Text(emoji).font(.system(size: 32))
                Text(label).font(.hsMeta).foregroundStyle(Color.hsInkBrown)
            }
            .frame(width: 80, height: 88)
            .background(
                RoundedRectangle(cornerRadius: 16)
                    .fill(selected ? Color.hsButter : Color.hsOffCream)
            )
            .scaleEffect(selected ? 1.05 : 1.0)
        }
        .sensoryFeedback(.selection, trigger: selected)
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    @State private var tab = 0
    var body: some View {
        TabView(selection: $tab) {
            TodayView()    .tabItem { Label("Today",    systemImage: tab == 0 ? "sun.max.fill" : "sun.max") }.tag(0)
            MeditateView() .tabItem { Label("Meditate", systemImage: tab == 1 ? "leaf.fill" : "leaf") }.tag(1)
            SleepView()    .tabItem { Label("Sleep",    systemImage: tab == 2 ? "moon.fill" : "moon") }.tag(2)
            MoveView()     .tabItem { Label("Move",     systemImage: tab == 3 ? "figure.walk.motion" : "figure.walk") }.tag(3)
            ProfileView()  .tabItem { Label("Profile",  systemImage: tab == 4 ? "person.fill" : "person") }.tag(4)
        }
        .tint(tab == 2 ? Color.hsSageMoss : Color.hsMarigold)   // Sage on Sleep tab, Marigold elsewhere
        .sensoryFeedback(.selection, trigger: tab)
        .background(Color.hsButterCream)
    }
}
```

## 5. Motion & Haptics

```swift
// Breathing sphere — 12s cycle, pure ease-in-out
withAnimation(.easeInOut(duration: 4)) { scale = 1.04 }

// Primary CTA tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: tapped)
.scaleEffect(pressed ? 0.98 : 1.0)

// Mood tag select
.sensoryFeedback(.selection, trigger: selected)
withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) { selected = true }

// Streak ring reveal
.sensoryFeedback(.success, trigger: streakSeen)
withAnimation(.easeOut(duration: 1.2)) { progress = currentProgress }

// Tab switch
.sensoryFeedback(.selection, trigger: tab)

// Meditation completion
.sensoryFeedback(.success, trigger: completed)
withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) { celebrationScale = 1.0 }

// Sleep mode transition
withAnimation(.easeInOut(duration: 0.6)) {
    canvasColor = .hsDuskCanvas
}
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Today tab | `sun.max` / `sun.max.fill` | 24pt |
| Meditate tab | `leaf` / `leaf.fill` | 24pt |
| Sleep tab | `moon` / `moon.fill` | 24pt |
| Move tab | `figure.walk` / `figure.walk.motion` | 24pt |
| Profile tab | `person` / `person.fill` | 24pt |
| Play | `play.fill` | 28pt |
| Pause | `pause.fill` | 28pt |
| Skip back 15s | `gobackward.15` | 22pt |
| Skip forward 15s | `goforward.15` | 22pt |
| Settings | `gearshape` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Favorite | `heart` / `heart.fill` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| Streak flame | `flame.fill` (Marigold) | 14pt |
| Bell / notifications | `bell` | 22pt |
| Milestone trophy | `trophy.fill` (Milestone Gold) | 24pt |
| Andy Puddicombe (host) | `mic.fill` (or avatar image) | 14pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 for `.sensoryFeedback` and refined `task` closures; iOS 16 fallback uses `UIImpactFeedbackGenerator` and `onAppear { Task { ... } }`
- Apercu is proprietary — if unlicensed, use SF Pro Rounded (`.system(size:, design: .rounded)`) as the system substitute or `Nunito` from Google Fonts. Both preserve the warm-humanist feel
- Dynamic Type: scale greeting, body, guidance, meditation titles; FIX tab labels, tag pills, mood labels, streak number (small, layout-sensitive)
- VoiceOver: breathing sphere is a single accessibility element with label `"Breathing meditation. Currently exhaling. 10 minutes remaining."`. The label updates with each phase change so VoiceOver users can follow the breath cadence
- Contrast: Marigold `#FF6B35` on Butter Cream `#FFF7E7` passes WCAG AA at 17pt+ Medium; Marigold on white passes at all sizes. Ink Brown `#2E1A47` on Butter Cream meets AAA at all sizes
- Reduce Motion: skip the breathing sphere scale animation — render it static at scale 1.0. Replace the streak ring animate-in with a 200ms cross-fade
- Sleep mode: Headspace's dusk canvas should respect the iOS Sleep Focus mode where possible — switch automatically when the system Focus is set to Sleep
- Audio: the meditation play screen should respect `AVAudioSession` interruptions (calls, Siri) and resume smoothly
- The illustration assets are bitmap PNG @2x/@3x — bundled via asset catalog. For the breathing sphere, prefer the radial gradient SwiftUI shape over a static illustration so it animates smoothly
