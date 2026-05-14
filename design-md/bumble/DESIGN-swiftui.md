# Bumble (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bumble's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the swipe card, the heart action button with yellow glow, the It's a Match celebration, and the 24-hour countdown chip.

Targets iOS 17+. The modern `.sensoryFeedback`, `phaseAnimator` for confetti hexes, and `matchedGeometryEffect` on the match celebration all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let bumbleYellow      = Color(red: 1.000, green: 0.776, blue: 0.161) // #FFC629
    static let bumbleHoneyDeep   = Color(red: 0.961, green: 0.714, blue: 0.086) // #F5B616 pressed
    static let bumbleYellowLight = Color(red: 1.000, green: 0.914, blue: 0.631) // #FFE9A1 soft chip

    // MARK: - Mode colors (Date/BFF/Bizz)
    static let bumbleBFFTeal     = Color(red: 0.067, green: 0.667, blue: 0.659) // #11AAA8
    static let bumbleBizzOrange  = Color(red: 1.000, green: 0.502, blue: 0.000) // #FF8000

    // MARK: - Canvas & Surfaces
    static let bumbleCanvas      = Color(red: 1.000, green: 1.000, blue: 1.000) // #FFFFFF
    static let bumbleBFFCream    = Color(red: 1.000, green: 0.988, blue: 0.949) // #FFFCF2
    static let bumbleSurface1    = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let bumbleSurface2    = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED
    static let bumbleDivider     = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5

    // MARK: - Text
    static let bumbleBlack       = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F primary
    static let bumbleSlate       = Color(red: 0.353, green: 0.353, blue: 0.353) // #5A5A5A secondary
    static let bumbleMist        = Color(red: 0.612, green: 0.612, blue: 0.612) // #9C9C9C tertiary

    // MARK: - Semantic
    static let bumbleMatchPink   = Color(red: 0.914, green: 0.294, blue: 0.482) // #E94B7B
    static let bumbleVerified    = Color(red: 0.000, green: 0.400, blue: 1.000) // #0066FF
    static let bumbleError       = Color(red: 0.843, green: 0.149, blue: 0.220) // #D72638
    static let bumbleSuccess     = Color(red: 0.000, green: 0.659, blue: 0.420) // #00A86B
    static let bumbleWarning     = Color(red: 1.000, green: 0.584, blue: 0.000) // #FF9500

    // MARK: - Dark mode
    static let bumbleDarkCanvas  = Color(red: 0.059, green: 0.059, blue: 0.059) // #0F0F0F
    static let bumbleDarkSurface = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let bumbleDarkSurface2 = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let bumbleDarkDivider = Color(red: 0.184, green: 0.184, blue: 0.184) // #2F2F2F
    static let bumbleDarkText    = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
}
```

## 2. Typography

Brando is the Bumble brand face. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to SF Pro Display/Text — the warmest system substitute when Brando is unavailable.

```swift
extension Font {
    // Display / hero
    static let bumbleMatchHero    = Font.custom("Brando-Black",  size: 44) // "It's a Match!"
    static let bumbleDisplay      = Font.custom("Brando-Black",  size: 32) // "Make the first move"
    static let bumbleScreenTitle  = Font.custom("Brando-Black",  size: 24) // "Chats"
    static let bumbleCardName     = Font.custom("Brando-Bold",   size: 28) // "Sigrún, 28"
    static let bumbleSection      = Font.custom("Brando-Bold",   size: 18) // "About me"

    // Body
    static let bumbleBody         = Font.custom("Brando-Medium", size: 16) // Bio text
    static let bumbleBodyBold     = Font.custom("Brando-Bold",   size: 16)
    static let bumbleBodySmall    = Font.custom("Brando-Medium", size: 14)

    // Buttons & labels
    static let bumbleButton       = Font.custom("Brando-Bold",   size: 16)
    static let bumbleButtonLarge  = Font.custom("Brando-Bold",   size: 18)
    static let bumbleTab          = Font.custom("Brando-Bold",   size: 10)
    static let bumbleChip         = Font.custom("Brando-Medium", size: 13)
    static let bumbleMeta         = Font.custom("Brando-Medium", size: 13)
    static let bumbleCounter      = Font.custom("Brando-Bold",   size: 11) // "1" / "5" tab badge
    static let bumbleCompliment   = Font.custom("Brando-Black",  size: 22) // "She sent you a Compliment"
}

// Fallback when Brando isn't bundled — SF Pro is the warmest system substitute
extension Font {
    static func bumble(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Yellow Primary CTA

```swift
struct BumblePrimaryButton: View {
    let label: String
    var hasGlow: Bool = false
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.bumbleButton)
                .foregroundStyle(Color.black) // Pure black on yellow — WCAG AA requires it
                .frame(maxWidth: .infinity)
                .frame(height: 56)
                .background(Capsule().fill(Color.bumbleYellow))
                .shadow(
                    color: hasGlow ? Color.bumbleYellow.opacity(0.4) : .clear,
                    radius: 24, y: 8
                )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}
```

### Swipe Action Row (under the swipe card)

```swift
struct SwipeActionRow: View {
    var onRewind: () -> Void
    var onPass:   () -> Void
    var onLike:   () -> Void
    var onSuper:  () -> Void
    var onCompliment: () -> Void

    var body: some View {
        HStack(spacing: 24) {
            // Rewind — small
            ActionCircle(diameter: 48, fill: Color.bumbleSurface1, stroke: nil) {
                Image(systemName: "arrow.uturn.backward").font(.system(size: 20)).foregroundStyle(Color.bumbleMist)
            } action: { onRewind() }

            // X Pass — medium
            ActionCircle(diameter: 56, fill: Color.bumbleCanvas, stroke: Color.bumbleBlack) {
                Image(systemName: "xmark").font(.system(size: 22, weight: .heavy)).foregroundStyle(Color.bumbleBlack)
            } action: {
                UIImpactFeedbackGenerator(style: .light).impactOccurred()
                onPass()
            }

            // Heart Yes — large with yellow glow
            ZStack {
                Circle()
                    .fill(Color.bumbleYellow)
                    .frame(width: 64, height: 64)
                    .shadow(color: Color.bumbleYellow.opacity(0.5), radius: 16, y: 6)
                Image(systemName: "heart.fill")
                    .font(.system(size: 28, weight: .heavy))
                    .foregroundStyle(Color.black)
            }
            .onTapGesture {
                UIImpactFeedbackGenerator(style: .medium).impactOccurred()
                onLike()
            }

            // Star SuperSwipe — medium yellow
            ActionCircle(diameter: 56, fill: Color.bumbleYellow, stroke: nil) {
                Image(systemName: "star.fill").font(.system(size: 22, weight: .heavy)).foregroundStyle(Color.black)
            } action: {
                UINotificationFeedbackGenerator().notificationOccurred(.success)
                onSuper()
            }

            // Bee Compliment — small
            ActionCircle(diameter: 56, fill: Color.bumbleCanvas, stroke: Color.bumbleYellow) {
                Image(systemName: "ant.fill") // bee placeholder
                    .font(.system(size: 22))
                    .foregroundStyle(Color.bumbleYellow)
            } action: { onCompliment() }
        }
    }
}

private struct ActionCircle<Content: View>: View {
    let diameter: CGFloat
    let fill: Color
    let stroke: Color?
    @ViewBuilder var content: Content
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            ZStack {
                Circle()
                    .fill(fill)
                    .overlay(stroke.map { Circle().strokeBorder($0, lineWidth: 1.5) })
                content
            }
            .frame(width: diameter, height: diameter)
        }
        .buttonStyle(.plain)
    }
}
```

### Swipe Card (the hero component)

```swift
struct SwipeCard: View {
    let photos: [Image]
    let name: String
    let age: Int
    let bio: String
    let isVerified: Bool
    @State private var currentPhoto = 0

    var body: some View {
        ZStack {
            // Photo
            photos[currentPhoto]
                .resizable()
                .aspectRatio(3/4, contentMode: .fill)
                .clipShape(RoundedRectangle(cornerRadius: 12))

            // Photo progress bar (4 segments)
            VStack {
                HStack(spacing: 4) {
                    ForEach(0..<photos.count, id: \.self) { idx in
                        Capsule()
                            .fill(idx == currentPhoto ? Color.white : Color.white.opacity(0.4))
                            .frame(height: 3)
                    }
                }
                .padding(.horizontal, 12)
                .padding(.top, 12)
                Spacer()
            }

            // Bottom gradient overlay
            VStack {
                Spacer()
                LinearGradient(
                    colors: [.clear, .black.opacity(0.7)],
                    startPoint: .top, endPoint: .bottom
                )
                .frame(height: 200)
            }
            .clipShape(RoundedRectangle(cornerRadius: 12))

            // Name+age + bio
            VStack(alignment: .leading, spacing: 4) {
                Spacer()
                HStack(alignment: .firstTextBaseline, spacing: 8) {
                    Text("\(name), \(age)")
                        .font(.bumbleCardName)
                        .foregroundStyle(.white)
                    if isVerified {
                        Image(systemName: "checkmark.seal.fill")
                            .font(.system(size: 18))
                            .foregroundStyle(Color.bumbleVerified)
                    }
                }
                Text(bio).font(.bumbleBody).foregroundStyle(.white).lineLimit(1)
            }
            .padding(.horizontal, 20)
            .padding(.bottom, 20)
        }
        .frame(maxWidth: .infinity)
        .aspectRatio(3/4, contentMode: .fit)
        .shadow(color: .black.opacity(0.12), radius: 16, y: 4)
        .gesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in /* track drag */ }
                .onEnded { value in
                    if abs(value.translation.width) > 100 {
                        // commit swipe
                    }
                }
        )
    }
}
```

### 24-Hour Countdown Chip

```swift
struct CountdownChip: View {
    let remaining: TimeInterval        // seconds
    @State private var pulse = false

    var formatted: String {
        let h = Int(remaining) / 3600
        let m = (Int(remaining) % 3600) / 60
        if remaining <= 0 { return "Time's up — extend?" }
        return "Your turn: \(h)h \(m)m"
    }

    var isExpired: Bool { remaining <= 0 }

    var body: some View {
        Text(formatted)
            .font(.bumbleMeta)
            .fontWeight(.bold)
            .foregroundStyle(Color.black)
            .padding(.horizontal, 16)
            .padding(.vertical, 8)
            .frame(maxWidth: .infinity)
            .frame(height: 32)
            .background(
                Capsule()
                    .fill(isExpired ? Color.bumbleError : Color.bumbleYellow)
                    .overlay(Capsule().strokeBorder(Color.bumbleHoneyDeep, lineWidth: 1))
            )
            .opacity(pulse ? 1.0 : 0.95)
            .animation(.easeInOut(duration: 1.5).repeatForever(autoreverses: true), value: pulse)
            .onAppear { pulse = true }
    }
}
```

### Match Celebration ("It's a Match!")

```swift
struct MatchCelebration: View {
    let myAvatar: Image
    let theirAvatar: Image
    let theirName: String
    var onSendMessage: () -> Void
    var onKeepSwiping: () -> Void

    @State private var heartScale: CGFloat = 0.2

    var body: some View {
        ZStack {
            Color.bumbleYellow.ignoresSafeArea()

            VStack(spacing: 24) {
                Spacer()

                ZStack {
                    HStack(spacing: 32) {
                        HexAvatar(image: myAvatar)
                        HexAvatar(image: theirAvatar)
                    }
                    Image(systemName: "heart.fill")
                        .font(.system(size: 48, weight: .heavy))
                        .foregroundStyle(Color.bumbleMatchPink)
                        .scaleEffect(heartScale)
                        .shadow(color: .black.opacity(0.15), radius: 8, y: 4)
                }

                VStack(spacing: 12) {
                    Text("It's a Match!")
                        .font(.bumbleMatchHero)
                        .foregroundStyle(Color.bumbleBlack)
                        .tracking(-0.8)
                    Text("You and \(theirName) want to chat")
                        .font(.bumbleBody)
                        .foregroundStyle(Color.bumbleBlack)
                }

                Text("She has 24 hours to make the first move")
                    .font(.bumbleMeta)
                    .foregroundStyle(Color.bumbleBlack.opacity(0.8))

                Spacer()

                VStack(spacing: 16) {
                    Button(action: onSendMessage) {
                        Text("Send a Message")
                            .font(.bumbleButtonLarge)
                            .foregroundStyle(Color.bumbleBlack)
                            .frame(maxWidth: .infinity)
                            .frame(height: 56)
                            .background(Capsule().fill(Color.white))
                    }
                    .buttonStyle(.plain)

                    Button("Keep Swiping", action: onKeepSwiping)
                        .font(.bumbleButton)
                        .foregroundStyle(Color.bumbleBlack)
                }
                .padding(.horizontal, 24)
                .padding(.bottom, 40)
            }
        }
        .onAppear {
            withAnimation(.spring(response: 0.4, dampingFraction: 0.55)) {
                heartScale = 1.0
            }
            UINotificationFeedbackGenerator().notificationOccurred(.success)
        }
    }
}

struct HexAvatar: View {
    let image: Image
    var size: CGFloat = 120
    var stroke: Color = .white

    var body: some View {
        image
            .resizable()
            .aspectRatio(contentMode: .fill)
            .frame(width: size, height: size)
            .clipShape(Hexagon())
            .overlay(Hexagon().strokeBorder(stroke, lineWidth: 4))
    }
}

struct Hexagon: Shape {
    func path(in rect: CGRect) -> Path {
        var path = Path()
        let center = CGPoint(x: rect.midX, y: rect.midY)
        let radius = min(rect.width, rect.height) / 2
        for i in 0..<6 {
            let angle = CGFloat.pi / 3 * CGFloat(i) - CGFloat.pi / 2
            let point = CGPoint(
                x: center.x + radius * cos(angle),
                y: center.y + radius * sin(angle)
            )
            if i == 0 { path.move(to: point) } else { path.addLine(to: point) }
        }
        path.closeSubpath()
        return path
    }
}
```

### Chat Input with Send Button

```swift
struct BumbleChatInput: View {
    @Binding var text: String
    var onSend: () -> Void

    var canSend: Bool { !text.isEmpty }

    var body: some View {
        HStack(spacing: 10) {
            TextField("Type your message…", text: $text, axis: .vertical)
                .font(.bumbleBody)
                .padding(.vertical, 14)
                .padding(.horizontal, 20)
                .background(Capsule().fill(Color.bumbleSurface1))
                .lineLimit(1...4)

            Button {
                onSend()
            } label: {
                Image(systemName: "arrow.up")
                    .font(.system(size: 18, weight: .bold))
                    .foregroundStyle(canSend ? Color.black : Color.bumbleMist)
                    .frame(width: 40, height: 40)
                    .background(Circle().fill(canSend ? Color.bumbleYellow : Color.bumbleSurface1))
            }
            .disabled(!canSend)
            .sensoryFeedback(.impact(weight: .medium), trigger: canSend == false)
        }
        .padding(.horizontal, 16)
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    @State private var selected: Int = 0

    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = .white
        appearance.shadowColor = UIColor(Color.bumbleDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView(selection: $selected) {
            PeopleView()  .tabItem { Label("People",  systemImage: "person.2") }.tag(0)
            HivesView()   .tabItem { Label("Hives",   systemImage: "hexagon.fill") }.tag(1)
            MatchesView() .tabItem { Label("Matches", systemImage: "heart.fill") }.tag(2)
            ChatsView()   .tabItem { Label("Chats",   systemImage: "bubble.left.fill") }.tag(3)
            ProfileView() .tabItem { Label("Profile", systemImage: "person.crop.circle") }.tag(4)
        }
        .tint(Color.bumbleBlack) // active = warm Bumble Black; indicator dot in yellow below
    }
}
```

## 5. Motion & Haptics

```swift
// Heart action button tap (the most-pressed action)
.sensoryFeedback(.impact(weight: .medium), trigger: likePressed)

// SuperSwipe (star) — heavier moment
.sensoryFeedback(.success, trigger: superSwipePressed)

// Compliment (bee) — softer, premium
.sensoryFeedback(.impact(weight: .soft), trigger: complimentPressed)

// Match celebration
.sensoryFeedback(.success, trigger: matchTriggered)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Heart bounce
withAnimation(.spring(response: 0.25, dampingFraction: 0.6)) { heartScale = 1.2 }
withAnimation(.spring(response: 0.25, dampingFraction: 0.6).delay(0.1)) { heartScale = 1.0 }

// Swipe commit (card rotates and slides off)
withAnimation(.spring(response: 0.35, dampingFraction: 0.7)) {
    cardRotation = .degrees(8)
    cardOffset = CGSize(width: 500, height: -50)
}

// SuperSwipe yellow flash
withAnimation(.easeOut(duration: 0.2)) { flashOpacity = 1.0 }
withAnimation(.easeIn(duration: 0.2).delay(0.2)) { flashOpacity = 0.0 }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Heart action (filled) | `heart.fill` | 28pt |
| X Pass | `xmark` | 22pt |
| Star SuperSwipe | `star.fill` | 22pt |
| Rewind | `arrow.uturn.backward` | 20pt |
| Bee Compliment | `ant.fill` (placeholder for proprietary bee) | 22pt |
| Match Pink heart | `heart.fill` | 48pt |
| Send (chat input) | `arrow.up` | 18pt |
| Photo info | `info.circle` | 16pt |
| Verified check | `checkmark.seal.fill` (Verified Blue) | 18pt |
| People tab | `person.2` / `person.2.fill` | 24pt |
| Hives tab | `hexagon` / `hexagon.fill` | 24pt |
| Matches tab | `heart` / `heart.fill` | 24pt |
| Chats tab | `bubble.left` / `bubble.left.fill` | 24pt |
| Profile tab | `person.crop.circle` / `person.crop.circle.fill` | 24pt |
| Filters | `slider.horizontal.3` | 24pt |
| Back | `chevron.left` | 18pt |
| Settings | `gearshape` | 22pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `phaseAnimator` on the hex confetti); fall back to `UIImpactFeedbackGenerator` and conditional rendering on iOS 16
- Support Dynamic Type on match hero, display, screen titles, body, bio, section headers — fix tab labels, badge counters, chip labels, and the 24-hour countdown (layout-sensitive)
- VoiceOver: announce match celebration as `"It's a Match. You and Sigrún want to chat. She has 24 hours to make the first move. Send a Message button."` Cards: combined label `"Sigrún, 28, 2 miles away, verified profile. Swipe right to like, left to pass."`
- Contrast: Bumble Yellow `#FFC629` requires **pure black** (`#000000`) text — Bumble Black `#1F1F1F` on yellow fails WCAG AA at small sizes. Use pure black on every yellow surface, no exceptions
- Reduce Motion: skip the hex confetti burst on swipe commits and the heart scale-bounce on match when `accessibilityReduceMotion` is true — preserve the haptics
- Dark mode: implement via `@Environment(\.colorScheme)` — swap canvas/surface/text tokens; Bumble Yellow stays identical (`#FFC629`), Honey Deep brightens slightly to `#FFD45C` for OLED pressed states
- The 24-hour countdown chip: announce "Time remaining: 23 hours, 14 minutes" every time the value crosses a 30-minute boundary, or on focus
- Swipe gestures: provide an accessibility action set on the card (`"Like"`, `"Pass"`, `"SuperSwipe"`, `"Send Compliment"`) so VoiceOver users can act without dragging
