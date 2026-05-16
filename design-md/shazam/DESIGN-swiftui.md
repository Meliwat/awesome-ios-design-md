# Shazam (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Shazam's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the radial gradient, the signature pulsing button + concentric rings, and the result card.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Gradient stops
    static let shazamCore  = Color(red: 0.0,   green: 0.314, blue: 1.0)   // #0050FF
    static let shazamBlue  = Color(red: 0.0,   green: 0.533, blue: 1.0)   // #0088FF
    static let shazamSpace = Color(red: 0.031, green: 0.035, blue: 0.055) // #08090E
    static let shazamBluePressed = Color(red: 0.0, green: 0.435, blue: 0.878) // #006FE0

    // MARK: - Text
    static let shazamTextPrimary   = Color.white                                 // #FFFFFF
    static let shazamTextSecondary = Color(red: 0.722, green: 0.769, blue: 1.0)   // #B8C4FF periwinkle
    static let shazamTextTertiary  = Color(red: 0.722, green: 0.769, blue: 1.0).opacity(0.55)

    // MARK: - Glass
    static let shazamGlass        = Color.white.opacity(0.08)
    static let shazamGlassStrong  = Color.white.opacity(0.14)
    static let shazamDivider      = Color.white.opacity(0.12)

    // MARK: - Semantic
    static let appleMusicPink = Color(red: 0.980, green: 0.141, blue: 0.235) // #FA243C
    static let shazamErrorRed = Color(red: 1.0,   green: 0.271, blue: 0.227) // #FF453A
}

// The signature hero gradient
extension ShapeStyle where Self == RadialGradient {
    static var shazamHero: RadialGradient {
        RadialGradient(
            colors: [.shazamBlue, .shazamCore, .shazamSpace],
            center: UnitPoint(x: 0.5, y: 0.42),
            startRadius: 0,
            endRadius: 520
        )
    }
}
```

## 2. Typography

Montserrat is available on Google Fonts. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to `.system(design: .default)` (SF Pro).

```swift
extension Font {
    static let shazamResultLarge = Font.custom("Montserrat-Bold",   size: 28).weight(.bold)
    static let shazamResult      = Font.custom("Montserrat-Bold",   size: 22).weight(.bold)
    static let shazamSection     = Font.custom("Montserrat-Bold",   size: 20).weight(.bold)
    static let shazamPrompt      = Font.custom("Montserrat-Bold",   size: 18).weight(.bold)
    static let shazamCardTitle   = Font.custom("Montserrat-Bold",   size: 16).weight(.bold)
    static let shazamSubtitle    = Font.custom("Montserrat-Medium", size: 14).weight(.medium)
    static let shazamBody        = Font.custom("Montserrat-Medium", size: 15).weight(.medium)
    static let shazamMeta        = Font.custom("Montserrat-Medium", size: 13).weight(.medium)
    static let shazamLabelUpper  = Font.custom("Montserrat-Bold",   size: 11).weight(.bold)
    static let shazamButton      = Font.custom("Montserrat-Bold",   size: 16).weight(.bold)
    static let shazamButtonSec   = Font.custom("Montserrat-Bold",   size: 14).weight(.bold)
}

extension Font {
    static func shazam(_ size: CGFloat, weight: Font.Weight = .medium) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### The Pulsing Shazam Button + Concentric Rings

```swift
struct ShazamButton: View {
    @Binding var isListening: Bool
    let onTap: () -> Void

    private let size: CGFloat = 132
    @State private var breathe = false

    var body: some View {
        ZStack {
            // Concentric emitting rings (only while listening)
            if isListening {
                ForEach(0..<3) { i in
                    PulseRing(delay: Double(i) * 0.6, base: size)
                }
            }

            // Listening glow
            Circle()
                .fill(Color.shazamBlue.opacity(isListening ? 0.5 : 0))
                .frame(width: size * 1.4, height: size * 1.4)
                .blur(radius: 60)
                .animation(.easeInOut(duration: 0.5), value: isListening)

            // The button
            Button(action: onTap) {
                ZStack {
                    Circle()
                        .fill(
                            RadialGradient(
                                colors: [.white, Color(red: 0.91, green: 0.94, blue: 1.0), Color(red: 0.81, green: 0.88, blue: 1.0)],
                                center: UnitPoint(x: 0.38, y: 0.32),
                                startRadius: 0, endRadius: size * 0.7
                            )
                        )
                        .overlay(Circle().strokeBorder(Color.white.opacity(0.4), lineWidth: 1))
                    ShazamGlyph()
                        .fill(Color.shazamBlue)
                        .frame(width: size * 0.4, height: size * 0.4)
                }
                .frame(width: size, height: size)
                .scaleEffect(breathe && !isListening ? 1.04 : 1.0)
                .shadow(color: Color.shazamCore.opacity(0.45), radius: 48, y: 16)
            }
            .buttonStyle(ShazamPressable())
            .sensoryFeedback(.impact(weight: .heavy), trigger: isListening)
        }
        .onAppear {
            withAnimation(.easeInOut(duration: 2.4).repeatForever(autoreverses: true)) { breathe = true }
        }
    }
}

struct PulseRing: View {
    let delay: Double
    let base: CGFloat
    @State private var animate = false

    var body: some View {
        Circle()
            .strokeBorder(Color.white.opacity(0.22), lineWidth: 2)
            .frame(width: base, height: base)
            .scaleEffect(animate ? 2.6 : 1.0)
            .opacity(animate ? 0 : 0.22)
            .onAppear {
                withAnimation(.easeOut(duration: 1.8).repeatForever(autoreverses: false).delay(delay)) {
                    animate = true
                }
            }
    }
}

struct ShazamPressable: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.94 : 1)
            .animation(.spring(response: 0.3, dampingFraction: 0.8), value: configuration.isPressed)
    }
}

// Stylized Shazam mark — replace with the licensed glyph asset in production
struct ShazamGlyph: Shape {
    func path(in r: CGRect) -> Path {
        var p = Path()
        let w = r.width, h = r.height
        p.move(to: CGPoint(x: w * 0.62, y: h * 0.18))
        p.addCurve(to: CGPoint(x: w * 0.30, y: h * 0.46),
                   control1: CGPoint(x: w * 0.40, y: h * 0.18),
                   control2: CGPoint(x: w * 0.30, y: h * 0.30))
        p.addCurve(to: CGPoint(x: w * 0.70, y: h * 0.62),
                   control1: CGPoint(x: w * 0.30, y: h * 0.62),
                   control2: CGPoint(x: w * 0.70, y: h * 0.50))
        p.addCurve(to: CGPoint(x: w * 0.38, y: h * 0.86),
                   control1: CGPoint(x: w * 0.70, y: h * 0.78),
                   control2: CGPoint(x: w * 0.58, y: h * 0.86))
        return p.strokedPath(.init(lineWidth: w * 0.13, lineCap: .round))
    }
}
```

### Hero Screen (no tab bar)

```swift
struct ShazamHome: View {
    @State private var isListening = false

    var body: some View {
        ZStack {
            Rectangle().fill(.shazamHero).ignoresSafeArea()

            VStack {
                HStack {
                    Image(systemName: "person.crop.circle").font(.system(size: 26))
                    Spacer()
                    Image(systemName: "music.note.list").font(.system(size: 26))
                }
                .foregroundStyle(.white)
                .padding(.horizontal, 20)

                Spacer().frame(height: 40)

                ShazamButton(isListening: $isListening) {
                    isListening.toggle()
                }

                Text(isListening ? "Listening for music…" : "Tap to Shazam")
                    .font(isListening ? .shazamButton : .shazamPrompt)
                    .foregroundStyle(.white)
                    .padding(.top, 24)

                Spacer()
            }
            .padding(.top, 8)
        }
    }
}
```

### Result Card

```swift
struct ShazamResultCard: View {
    let title: String
    let artist: String
    let artwork: Image

    var body: some View {
        VStack(spacing: 20) {
            HStack(spacing: 16) {
                artwork
                    .resizable().aspectRatio(1, contentMode: .fill)
                    .frame(width: 88, height: 88)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
                VStack(alignment: .leading, spacing: 6) {
                    Text(title).font(.shazamResult).foregroundStyle(.white).lineLimit(2)
                    Text(artist).font(.shazamSubtitle).foregroundStyle(.shazamTextSecondary).lineLimit(1)
                }
                Spacer(minLength: 0)
            }

            Button { } label: {
                HStack(spacing: 8) {
                    Image(systemName: "music.note").foregroundStyle(Color.appleMusicPink)
                    Text("Open in Apple Music").font(.shazamButton).foregroundStyle(Color.shazamSpace)
                }
                .frame(maxWidth: .infinity)
                .padding(.vertical, 14)
                .background(Capsule().fill(.white))
            }
            .buttonStyle(ShazamPressable())

            HStack(spacing: 12) {
                GlassAction(icon: "square.and.arrow.up", label: "Share")
                GlassAction(icon: "plus", label: "Add")
                GlassAction(icon: "text.quote", label: "Lyrics")
            }
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 20)
                .fill(Color.shazamGlass)
                .overlay(RoundedRectangle(cornerRadius: 20).strokeBorder(Color.shazamGlassStrong, lineWidth: 1))
        )
        .padding(.horizontal, 20)
        .transition(.scale(scale: 0.85).combined(with: .opacity))
    }
}

struct GlassAction: View {
    let icon: String
    let label: String
    var body: some View {
        VStack(spacing: 6) {
            Image(systemName: icon).font(.system(size: 18)).foregroundStyle(.white)
            Text(label).font(.shazamButtonSec).foregroundStyle(.white)
        }
        .frame(maxWidth: .infinity)
        .padding(.vertical, 12)
        .background(Capsule().fill(Color.shazamGlass))
        .overlay(Capsule().strokeBorder(Color.white.opacity(0.16), lineWidth: 1))
    }
}
```

### Recent-Shazams Sheet

```swift
struct RecentShazamsSheet: View {
    let items: [(title: String, artist: String, time: String, art: Image)]

    var body: some View {
        VStack(spacing: 0) {
            Capsule().fill(Color.white.opacity(0.4)).frame(width: 36, height: 4).padding(.vertical, 12)
            Text("Your Shazams").font(.shazamSection).foregroundStyle(.white)
                .frame(maxWidth: .infinity, alignment: .leading).padding(.horizontal, 16).padding(.bottom, 8)

            ScrollView {
                ForEach(items.indices, id: \.self) { i in
                    let it = items[i]
                    HStack(spacing: 12) {
                        it.art.resizable().aspectRatio(1, contentMode: .fill)
                            .frame(width: 52, height: 52).clipShape(RoundedRectangle(cornerRadius: 10))
                        VStack(alignment: .leading, spacing: 2) {
                            Text(it.title).font(.shazamCardTitle).foregroundStyle(.white).lineLimit(1)
                            Text(it.artist).font(.shazamSubtitle).foregroundStyle(.shazamTextSecondary).lineLimit(1)
                        }
                        Spacer()
                        Text(it.time).font(.shazamMeta).foregroundStyle(.shazamTextSecondary)
                        Image(systemName: "chevron.right").font(.system(size: 14)).foregroundStyle(.shazamTextSecondary)
                    }
                    .padding(.horizontal, 16).frame(height: 68)
                    Divider().overlay(Color.shazamDivider)
                }
            }
        }
        .background(.ultraThinMaterial)
        .presentationDetents([.height(120), .medium, .large])
        .presentationCornerRadius(24)
    }
}
```

## 4. Listening State Machine

Drive the rings and prompt from the recognition lifecycle:

```swift
enum ShazamState { case idle, listening, matched(track: String), noMatch }

@Observable final class ShazamModel {
    var state: ShazamState = .idle
    func tap() {
        state = .listening
        // start ShazamKit SHSession; on result:
        // state = .matched(track:) → reveal card
        // on failure: state = .noMatch → soft shake + error haptic
    }
}
```

## 5. Navigation — No Tab Bar

Shazam deliberately has **no `TabView`**. The hero is the root; the library/history is a sheet:

```swift
struct RootView: View {
    @State private var showLibrary = false
    var body: some View {
        ShazamHome()
            .sheet(isPresented: $showLibrary) {
                RecentShazamsSheet(items: [])
            }
            .preferredColorScheme(.dark)
    }
}
```

## 6. Motion

```swift
// Idle breathing: scale 1.0 → 1.04, 2.4s ease-in-out, repeatForever(autoreverses: true)

// Concentric rings: scale 1.0 → 2.6, opacity 0.22 → 0, 1.8s ease-out, repeatForever, staggered 0.6s

// Tap: ShazamPressable scale 0.94 spring(damping 0.8) + .sensoryFeedback(.impact(weight: .heavy))

// Result reveal:
withAnimation(.spring(response: 0.45, dampingFraction: 0.8)) { /* state = .matched */ }
// card uses .transition(.scale(scale: 0.85).combined(with: .opacity))

// No match: single horizontal shake + .sensoryFeedback(.error, trigger: noMatch)
.offset(x: shake ? -8 : 0).animation(.default.repeatCount(3, autoreverses: true), value: shake)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Profile / Settings | `person.crop.circle` | 26pt |
| Library / History | `music.note.list` | 26pt |
| Open in Apple Music | `music.note` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Add | `plus` | 18pt |
| Lyrics | `text.quote` | 18pt |
| Close result | `xmark` | 18pt |
| Sheet chevron | `chevron.right` | 14pt |
| Search (library) | `magnifyingglass` | 18pt |
| Play / Pause (mini) | `play.fill` / `pause.fill` | 20pt |
| No-match | `waveform.slash` | 22pt |

> The Shazam mark itself is a proprietary logo — ship the licensed asset (`Image("ShazamGlyph")`); `ShazamGlyph` above is a stand-in shape for prototyping only.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`; ShazamKit `SHSession` is iOS 15+)
- Support Dynamic Type on result title, artist, sheet rows, body — pin the centered "Tap to Shazam" prompt and listening label (geometry-sensitive hero)
- VoiceOver: the button's `accessibilityLabel` = "Shazam. Tap to identify the music playing around you."; announce state changes ("Listening", "Match found: <title> by <artist>", "No match found") via `AccessibilityNotification.announcement`
- Respect Reduce Motion: replace concentric ring emission with a static glow + a subtle opacity pulse when `accessibilityReduceMotion` is true
- Contrast: periwinkle `#B8C4FF` on the dark gradient edge passes WCAG AA at 14pt+; validate over the brighter blue core and darken toward `#A6B4FF` if needed
- Force dark: Shazam's hero is dark by design — set `.preferredColorScheme(.dark)` at the root
