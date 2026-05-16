# Calm (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Calm's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Night-Sky Gradient
    static let calmGradientTop    = Color(red: 0.165, green: 0.435, blue: 0.839) // #2A6FD6
    static let calmGradientMid    = Color(red: 0.102, green: 0.290, blue: 0.561) // #1A4A8F
    static let calmGradientBottom = Color(red: 0.043, green: 0.118, blue: 0.247) // #0B1E3F
    static let calmScrimNavy      = Color(red: 0.035, green: 0.094, blue: 0.188) // #091830

    // MARK: - Glass Surfaces
    static let calmGlass        = Color.white.opacity(0.08)
    static let calmGlassRaised  = Color.white.opacity(0.12)
    static let calmGlassDivider = Color.white.opacity(0.12)
    static let calmGlassHover   = Color.white.opacity(0.16)

    // MARK: - Text
    static let calmTextPrimary   = Color.white                                  // #FFFFFF
    static let calmTextSecondary = Color(red: 0.722, green: 0.769, blue: 0.847) // #B8C4D8
    static let calmTextTertiary  = Color(red: 0.529, green: 0.580, blue: 0.659) // #8794A8

    // MARK: - Accent
    static let calmBlue         = Color(red: 0.165, green: 0.435, blue: 0.839) // #2A6FD6
    static let calmBluePressed  = Color(red: 0.122, green: 0.341, blue: 0.671) // #1F57AB
    static let calmBlueSoft     = Color(red: 0.290, green: 0.522, blue: 0.878) // #4A85E0
    static let calmStreakGold   = Color(red: 0.949, green: 0.812, blue: 0.298) // #F2C94C
    static let calmGentleError  = Color(red: 0.878, green: 0.541, blue: 0.541) // #E08A8A
    static let calmSuccess      = Color(red: 0.498, green: 0.722, blue: 0.627) // #7FB8A0
}

extension LinearGradient {
    static let calmSky = LinearGradient(
        stops: [
            .init(color: .calmGradientTop,    location: 0.0),
            .init(color: .calmGradientMid,    location: 0.45),
            .init(color: .calmGradientBottom, location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

Lora (serif headlines) and Inter (sans body) are both open-source (SIL OFL). Bundle TTFs via `Info.plist` (`UIAppFonts`).

```swift
extension Font {
    // Lora — serif, the brand voice
    static let calmGreeting   = Font.custom("Lora-SemiBold", size: 30)
    static let calmScreenTitle = Font.custom("Lora-SemiBold", size: 26)
    static let calmDailyTitle = Font.custom("Lora-SemiBold", size: 24)
    static let calmCardTitle  = Font.custom("Lora-SemiBold", size: 19)
    static let calmBreatheCue = Font.custom("Lora-Regular",  size: 22)

    // Inter — sans, supporting
    static let calmSection    = Font.custom("Inter-Bold",     size: 16)
    static let calmRowTitle   = Font.custom("Inter-SemiBold", size: 17)
    static let calmBody       = Font.custom("Inter-Regular",  size: 17)
    static let calmSubtitle   = Font.custom("Inter-Regular",  size: 15)
    static let calmButton     = Font.custom("Inter-SemiBold", size: 17)
    static let calmMeta       = Font.custom("Inter-Medium",   size: 13)
    static let calmTab        = Font.custom("Inter-SemiBold", size: 11)
    static let calmTimer      = Font.custom("Inter-Light",    size: 44)
    static let calmLabelUpper = Font.custom("Inter-Bold",     size: 12)
}
```

## 3. Signature Components

### Night-Sky Background

```swift
struct CalmBackground<Content: View>: View {
    @ViewBuilder var content: Content
    var body: some View {
        ZStack {
            LinearGradient.calmSky.ignoresSafeArea()
            content
        }
    }
}
```

### Breathe Bubble (signature)

```swift
struct BreatheBubble: View {
    @State private var diameter: CGFloat = 120
    @State private var cue = "Breathe in"
    private let base: CGFloat = 120
    private let peak: CGFloat = 220

    var body: some View {
        ZStack {
            Circle()
                .fill(Color.white.opacity(0.9))
                .frame(width: diameter, height: diameter)
                .shadow(color: .calmBlueSoft.opacity(0.6), radius: 40)
            Text(cue)
                .font(.calmBreatheCue)
                .foregroundStyle(Color.calmGradientBottom)
                .animation(.easeInOut(duration: 0.6), value: cue)
        }
        .onAppear { runCycle() }
    }

    private func runCycle() {
        // Inhale 4s
        withAnimation(.easeIn(duration: 4)) { diameter = peak }
        cue = "Breathe in"
        // Hold 7s
        DispatchQueue.main.asyncAfter(deadline: .now() + 4) { cue = "Hold" }
        // Exhale 8s
        DispatchQueue.main.asyncAfter(deadline: .now() + 11) {
            cue = "Breathe out"
            withAnimation(.easeOut(duration: 8)) { diameter = base }
        }
        DispatchQueue.main.asyncAfter(deadline: .now() + 19) { runCycle() }
    }
}
```

### Primary Play Pill

```swift
struct CalmPlayPill: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 10) {
                Image(systemName: "play.fill").font(.system(size: 16))
                Text(title).font(.calmButton)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 16)
            .padding(.horizontal, 40)
            .background(Capsule().fill(Color.calmBlue))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: title)
        .buttonStyle(CalmPressableStyle(scale: 0.97))
    }
}

struct CalmPressableStyle: ButtonStyle {
    var scale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? scale : 1)
            .animation(.easeOut(duration: 0.25), value: configuration.isPressed)
    }
}
```

### Daily Calm Hero Card (signature)

```swift
struct DailyCalmHero: View {
    let title: String
    let subtitle: String
    let photo: Image
    let onPlay: () -> Void

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            photo.resizable().aspectRatio(contentMode: .fill)
            LinearGradient(
                colors: [.clear, .calmScrimNavy.opacity(0.85)],
                startPoint: .top, endPoint: .bottom
            )
            HStack(alignment: .bottom) {
                VStack(alignment: .leading, spacing: 6) {
                    Text("DAILY CALM")
                        .font(.calmLabelUpper).tracking(1)
                        .foregroundStyle(.white)
                    Text(title).font(.calmDailyTitle).foregroundStyle(.white)
                    Text(subtitle).font(.calmSubtitle).foregroundStyle(Color.calmTextSecondary)
                }
                Spacer()
                Button(action: onPlay) {
                    Image(systemName: "play.fill")
                        .font(.system(size: 18)).foregroundStyle(.white)
                        .frame(width: 44, height: 44)
                        .background(Circle().fill(Color.white.opacity(0.16)))
                }
            }
            .padding(20)
        }
        .frame(height: 220)
        .clipShape(RoundedRectangle(cornerRadius: 20))
    }
}
```

### Sleep Story List Row

```swift
struct SleepStoryRow: View {
    let title: String
    let narrator: String
    let duration: String
    let thumb: Image

    var body: some View {
        HStack(spacing: 14) {
            thumb.resizable().aspectRatio(contentMode: .fill)
                .frame(width: 56, height: 56)
                .clipShape(RoundedRectangle(cornerRadius: 12))
            VStack(alignment: .leading, spacing: 4) {
                Text(title).font(.calmRowTitle).foregroundStyle(.white)
                Text("Narrated by \(narrator) · \(duration)")
                    .font(.calmSubtitle).foregroundStyle(Color.calmTextSecondary)
            }
            Spacer()
            Image(systemName: "arrow.down.circle")
                .font(.system(size: 22)).foregroundStyle(.white.opacity(0.8))
        }
        .padding(.horizontal, 20).frame(height: 76)
        .contentShape(Rectangle())
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.calmGlassDivider).frame(height: 1)
        }
    }
}
```

### Glass Card

```swift
struct CalmGlassCard<Content: View>: View {
    @ViewBuilder var content: Content
    var body: some View {
        content
            .padding(20)
            .background(.ultraThinMaterial.opacity(0.6))
            .background(Color.calmGlass)
            .overlay(RoundedRectangle(cornerRadius: 20)
                .strokeBorder(Color.calmGlassDivider, lineWidth: 1))
            .clipShape(RoundedRectangle(cornerRadius: 20))
    }
}
```

## 4. Tab Bar

Use `TabView` with a custom `UITabBarAppearance` matching the deep-navy glass base of the gradient. **Active tint is Calm Blue.**

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        a.backgroundColor = UIColor(Color.calmGradientBottom).withAlphaComponent(0.72)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SleepView().tabItem { Label("Sleep", systemImage: "moon.stars.fill") }
            MeditateView().tabItem { Label("Meditate", systemImage: "figure.mind.and.body") }
            MusicView().tabItem { Label("Music", systemImage: "music.note") }
            MoreView().tabItem { Label("More", systemImage: "ellipsis") }
        }
        .tint(.calmBlue)
    }
}
```

## 5. Motion

```swift
// Breathe bubble: 4s ease-in → hold 7s → 8s ease-out, looping (see BreatheBubble.runCycle)

// Card tap
.buttonStyle(CalmPressableStyle(scale: 0.97))  // 250ms ease-out

// Screen transitions — Calm fades, never hard-slides
.transition(.opacity.animation(.easeInOut(duration: 0.55)))

// Session timer ring — linear sweep over duration
withAnimation(.linear(duration: sessionLength)) { progress = 1.0 }

// Session complete
.sensoryFeedback(.success, trigger: didComplete)  // soft sage check fades in over 600ms
```

Haptics via `.sensoryFeedback` (iOS 17+): `.impact(flexibility: .soft)` on play, `.success` on session complete, `.selection` on mood-chip pick. Keep haptics gentle and rare — Calm is serene.

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 16-24pt |
| Pause | `pause.fill` | 24pt |
| Favorite | `heart` / `heart.fill` | 22pt |
| Download | `arrow.down.circle` / `.fill` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Background sound | `slider.horizontal.3` | 22pt |
| Timer | `timer` | 22pt |
| Search | `magnifyingglass` | 16pt |
| Streak | `flame.fill` | 18pt |
| Settings | `gearshape` | 22pt |
| Home (tab) | `house.fill` | 24pt |
| Sleep (tab) | `moon.stars.fill` | 24pt |
| Meditate (tab) | `figure.mind.and.body` | 24pt |
| Music (tab) | `music.note` | 24pt |
| More (tab) | `ellipsis` | 24pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` needs iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Dynamic Type: scale greetings, titles, body, narrator (both Lora and Inter); pin tab labels (11pt), duration meta (13pt), and cap the session timer at 56pt
- VoiceOver: the breathe bubble is decorative/ambient — mark it `.accessibilityHidden(true)` and instead announce the phase ("Breathe in", "Hold", "Breathe out") via an `.accessibilityLabel` on a hidden live element; label the play pill "Play Daily Calm"
- Reduce Motion: replace the breathe-bubble scale animation with a static circle plus a text-only phase cue; replace cross-dissolves with instant cuts
- Reduce Transparency: when enabled, swap glass surfaces (`rgba(255,255,255,0.08)`) for solid `#13294B` so cards stay legible without blur
- Contrast: `#B8C4D8` on the mid-gradient passes AA at 15pt+; validate the 13pt meta over the lighter top of the gradient and darken the scrim if needed
- Dark-only: do not provide a light `ColorScheme`; lock the app to `.dark`
