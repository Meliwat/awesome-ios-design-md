# OkCupid (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates OkCupid's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let okcCanvas   = Color.white                                     // #FFFFFF
    static let okcSurface  = Color(red: 0.949, green: 0.949, blue: 0.949)    // #F2F2F2
    static let okcDivider  = Color(red: 0.898, green: 0.898, blue: 0.898)    // #E5E5E5

    // MARK: - Text
    static let okcTextPrimary   = Color(red: 0.078, green: 0.090, blue: 0.102) // #14171A
    static let okcTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B
    static let okcTextTertiary  = Color(red: 0.608, green: 0.608, blue: 0.608) // #9B9B9B

    // MARK: - Brand
    static let okcMagenta        = Color(red: 0.886, green: 0.008, blue: 0.310) // #E2024F
    static let okcMagentaPressed = Color(red: 0.753, green: 0.008, blue: 0.267) // #C00244
    static let okcMagentaTint    = Color(red: 0.988, green: 0.890, blue: 0.922) // #FCE3EB
    static let okcIndigo         = Color(red: 0.020, green: 0.0,   blue: 1.0)   // #0500FF
    static let okcIndigoTint     = Color(red: 0.898, green: 0.894, blue: 1.0)   // #E5E4FF

    // MARK: - Match tiers
    static let okcMatchHigh = Color(red: 0.886, green: 0.008, blue: 0.310)    // #E2024F
    static let okcMatchMid  = Color(red: 0.941, green: 0.341, blue: 0.541)    // #F0578A
    static let okcMatchLow  = Color(red: 0.608, green: 0.608, blue: 0.608)    // #9B9B9B

    // MARK: - Semantic
    static let okcSuccess = Color(red: 0.110, green: 0.667, blue: 0.416)      // #1CAA6A
    static let okcWarning = Color(red: 0.961, green: 0.651, blue: 0.137)      // #F5A623
    static let okcError   = Color(red: 0.816, green: 0.008, blue: 0.106)      // #D0021B
}
```

## 2. Typography

Larsseit is OkCupid's licensed brand typeface. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. The closest free substitute is **Inter** — a friendly grotesque (avoid hard industrial sans).

```swift
extension Font {
    static let okcTitleLarge   = Font.custom("Larsseit-Bold",     size: 30).weight(.bold)
    static let okcProfileName  = Font.custom("Larsseit-Bold",     size: 26).weight(.bold)
    static let okcSection      = Font.custom("Larsseit-Bold",     size: 22).weight(.bold)
    static let okcMatchPct      = Font.custom("Larsseit-Bold",     size: 20).weight(.bold)
    static let okcQuestion     = Font.custom("Larsseit-Semibold", size: 20).weight(.semibold)
    static let okcCardTitle    = Font.custom("Larsseit-Semibold", size: 17).weight(.semibold)
    static let okcBody         = Font.custom("Larsseit-Regular",  size: 16).weight(.regular)
    static let okcAnswer       = Font.custom("Larsseit-Semibold", size: 16).weight(.semibold)
    static let okcMeta         = Font.custom("Larsseit-Regular",  size: 14).weight(.regular)
    static let okcCaption      = Font.custom("Larsseit-Regular",  size: 13).weight(.regular)
    static let okcLabelUpper   = Font.custom("Larsseit-Bold",     size: 11).weight(.bold)
    static let okcButton       = Font.custom("Larsseit-Bold",     size: 17).weight(.bold)
    static let okcTab          = Font.custom("Larsseit-Semibold", size: 10).weight(.semibold)
}

extension Font {
    static func okc(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .rounded) // rounded ≈ Larsseit warmth
    }
}
```

## 3. Signature Components

### Match-% Circular Badge

```swift
struct OkcMatchBadge: View {
    let percent: Int
    var diameter: CGFloat = 48

    private var tier: Color {
        switch percent {
        case 90...:  return .okcMatchHigh
        case 70..<90: return .okcMatchMid
        default:     return .okcMatchLow
        }
    }

    @State private var display = 0

    var body: some View {
        ZStack {
            Circle().fill(Color.white.opacity(0.96))
            Circle().strokeBorder(tier, lineWidth: 3)
            Text("\(display)%")
                .font(.okcMatchPct)
                .foregroundStyle(tier)
                .minimumScaleFactor(0.6)
        }
        .frame(width: diameter, height: diameter)
        .onAppear {
            withAnimation(.easeOut(duration: 0.6)) { display = percent }
            // For a true count-up, drive `display` with a TimelineView or a Timer.
        }
    }
}
```

### Like Button (Magenta Heart)

```swift
struct OkcLikeButton: View {
    @Binding var liked: Bool
    var size: CGFloat = 64
    let action: () -> Void

    var body: some View {
        Button {
            action()
            liked = true
        } label: {
            ZStack {
                Circle()
                    .fill(liked ? Color.okcMagentaTint : Color.white)
                    .overlay(Circle().strokeBorder(Color.okcDivider, lineWidth: 1))
                    .shadow(color: Color.okcTextPrimary.opacity(0.16), radius: 14, y: 4)
                Image(systemName: liked ? "heart.fill" : "heart")
                    .font(.system(size: size * 0.44, weight: .bold))
                    .foregroundStyle(Color.okcMagenta)
                    .scaleEffect(liked ? 1.0 : 1.0)
            }
            .frame(width: size, height: size)
        }
        .sensoryFeedback(.success, trigger: liked)
        .buttonStyle(OkcPressableStyle(pressedScale: 0.90))
    }
}

struct OkcPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.6), value: configuration.isPressed)
    }
}
```

### Pass Button (Indigo X)

```swift
struct OkcPassButton: View {
    var size: CGFloat = 56
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            ZStack {
                Circle()
                    .fill(Color.white)
                    .overlay(Circle().strokeBorder(Color.okcDivider, lineWidth: 1))
                    .shadow(color: Color.okcTextPrimary.opacity(0.16), radius: 14, y: 4)
                Image(systemName: "xmark")
                    .font(.system(size: size * 0.42, weight: .bold))
                    .foregroundStyle(Color.okcIndigo)
            }
            .frame(width: size, height: size)
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
        .buttonStyle(OkcPressableStyle(pressedScale: 0.90))
    }
}
```

### Primary Pill

```swift
struct OkcPrimaryPill: View {
    let title: String
    var enabled: Bool = true
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.okcButton)
                .tracking(0.2)
                .foregroundStyle(.white)
                .padding(.vertical, 15)
                .padding(.horizontal, 32)
                .frame(maxWidth: .infinity)
                .background(Capsule().fill(Color.okcMagenta.opacity(enabled ? 1 : 0.35)))
        }
        .disabled(!enabled)
        .buttonStyle(OkcPressableStyle(pressedScale: 0.97))
    }
}
```

### Match-Question Card

```swift
struct OkcQuestionCard: View {
    let prompt: String
    let options: [String]
    @State private var selected: Int? = nil
    @State private var importance = 1 // 0 a little, 1 somewhat, 2 very
    let onAnswer: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            Text(prompt).font(.okcQuestion).foregroundStyle(Color.okcTextPrimary)

            VStack(spacing: 12) {
                ForEach(options.indices, id: \.self) { i in
                    Button { selected = i } label: {
                        Text(options[i])
                            .font(.okcAnswer)
                            .foregroundStyle(selected == i ? .white : Color.okcTextPrimary)
                            .frame(maxWidth: .infinity)
                            .padding(.vertical, 16)
                            .background(
                                Capsule().fill(selected == i ? Color.okcMagenta : .white)
                            )
                            .overlay(
                                Capsule().strokeBorder(
                                    selected == i ? .clear : Color.okcDivider, lineWidth: 1.5)
                            )
                    }
                }
            }

            Picker("", selection: $importance) {
                Text("A little").tag(0); Text("Somewhat").tag(1); Text("Very").tag(2)
            }
            .pickerStyle(.segmented)

            OkcPrimaryPill(title: "Answer", enabled: selected != nil, action: onAnswer)
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 20)
                .fill(Color.white)
                .shadow(color: Color.okcTextPrimary.opacity(0.06), radius: 8, y: 2)
        )
    }
}
```

## 4. DoubleTake Profile Card

```swift
struct OkcProfileCard: View {
    let name: String
    let age: Int
    let percent: Int
    let hero: Image

    var body: some View {
        VStack(spacing: 0) {
            ZStack(alignment: .bottomLeading) {
                hero.resizable().aspectRatio(4/5, contentMode: .fill)
                LinearGradient(colors: [.clear, .black.opacity(0.45)],
                               startPoint: .center, endPoint: .bottom)
                HStack(alignment: .bottom) {
                    Text("\(name), \(age)")
                        .font(.okcProfileName).foregroundStyle(.white)
                    Spacer()
                    OkcMatchBadge(percent: percent)
                }
                .padding(16)
            }
            // Internally scrollable bio / questions go below (omitted for brevity)
        }
        .clipShape(RoundedRectangle(cornerRadius: 24))
        .shadow(color: Color.okcTextPrimary.opacity(0.12), radius: 24, y: 8)
        .padding(.horizontal, 8)
    }
}
```

## 5. Tab Bar

```swift
struct OkcRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = UIColor(Color.okcCanvas).withAlphaComponent(0.96)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            DiscoverView().tabItem { Label("Discover", systemImage: "rectangle.stack.fill") }
            LikesView().tabItem    { Label("Likes",    systemImage: "heart.fill") }
            MatchesView().tabItem  { Label("Matches",  systemImage: "sparkles") }
            MessagesView().tabItem { Label("Messages", systemImage: "bubble.left.fill") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person.fill") }
        }
        .tint(.okcMagenta) // active = OkCupid Magenta
    }
}
```

## 6. Motion

```swift
// Like — success haptic + scale spring + heart-fill bounce
.sensoryFeedback(.success, trigger: liked)
// liked heart: .scaleEffect with keyframes 1.0 → 1.2 → 1.0 via withAnimation chains

// Pass — soft impact, then slide the card out
withAnimation(.easeOut(duration: 0.3)) { cardOffset = -screenW; cardRotation = -8 }

// Match-% count-up — drive `display` with a TimelineView(.animation) or a Timer over 0.6s

// It's a Match — full-screen .transition(.scale(scale: 0.85).combined(with: .opacity))
.animation(.spring(response: 0.5, dampingFraction: 0.7), value: isMatch)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Like | `heart` / `heart.fill` | 28pt |
| Pass | `xmark` | 24pt |
| Star / Boost | `star.fill` | 24pt |
| Match (tab) | `sparkles` | 26pt |
| Message send | `arrow.up.circle.fill` | 28pt |
| Filter | `slider.horizontal.3` | 22pt |
| Settings | `gearshape.fill` | 22pt |
| Verified | `checkmark.seal.fill` | 18pt |
| Discover (tab) | `rectangle.stack.fill` | 26pt |
| Likes (tab) | `heart.fill` | 26pt |
| Messages (tab) | `bubble.left.fill` | 26pt |
| Profile (tab) | `person.fill` | 26pt |
| Question | `text.bubble.fill` | 22pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UINotificationFeedbackGenerator` / `UIImpactFeedbackGenerator`)
- Support Dynamic Type on titles, question prompts, body, and list text — pin the match-% badge number and tab labels (fixed ornaments)
- VoiceOver: announce the match badge as "94 percent match"; the Like button as "Like {name}"; the Pass button as "Pass"; group question prompt + options
- Contrast: `#6B6B6B` secondary on `#FFFFFF` passes WCAG AA at 14pt+; the match-low gray `#9B9B9B` is intentionally de-emphasized — keep high-match magenta for emphasis
- Use `.system(design: .rounded)` as the substitute design when Larsseit/Inter is unavailable — it preserves the friendly tone
- Respect Reduce Motion: skip the count-up (set the number directly) and the card-slide; cross-fade the "It's a Match" takeover instead of scaling
