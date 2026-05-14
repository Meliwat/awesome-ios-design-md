# Hinge (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hinge's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the signature prompt card, the heart-tap, the Rose send sheet, and the match celebration.

Targets iOS 17+. The modern `.sensoryFeedback`, `matchedGeometryEffect` on the comment sheet, and `phaseAnimator` for confetti all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (the cream paper)
    static let hingeCream        = Color(red: 0.992, green: 0.973, blue: 0.949) // #FDF8F2 canvas
    static let hingePaper        = Color(red: 0.980, green: 0.965, blue: 0.941) // #FAF6F0 cards
    static let hingeSand         = Color(red: 0.949, green: 0.922, blue: 0.878) // #F2EBE0 chips/inputs
    static let hingeSand2        = Color(red: 0.910, green: 0.875, blue: 0.816) // #E8DFD0 pressed
    static let hingeDividerBone  = Color(red: 0.878, green: 0.839, blue: 0.773) // #E0D6C5

    // MARK: - Text (warm-tinted greys, never neutral)
    static let hingeBlack        = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A primary
    static let hingeBlackPressed = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let hingeGraphite     = Color(red: 0.290, green: 0.259, blue: 0.224) // #4A4239 secondary
    static let hingeStone        = Color(red: 0.478, green: 0.447, blue: 0.408) // #7A7268 tertiary
    static let hingeBone         = Color(red: 0.690, green: 0.659, blue: 0.612) // #B0A89C disabled

    // MARK: - Rose Gold (the single accent — Standouts, Roses, premium)
    static let hingeRose         = Color(red: 0.910, green: 0.627, blue: 0.302) // #E8A04D
    static let hingeRoseDeep     = Color(red: 0.773, green: 0.494, blue: 0.180) // #C57E2E pressed
    static let hingeRoseLight    = Color(red: 0.961, green: 0.851, blue: 0.659) // #F5D9A8 halo

    // MARK: - Semantic
    static let hingeMatchGreen   = Color(red: 0.176, green: 0.478, blue: 0.294) // #2D7A4B
    static let hingeWarning      = Color(red: 0.847, green: 0.545, blue: 0.180) // #D88B2E
    static let hingeError        = Color(red: 0.702, green: 0.227, blue: 0.184) // #B33A2F
    static let hingeInfo         = Color(red: 0.353, green: 0.384, blue: 0.451) // #5A6273

    // MARK: - Dark mode (warm dark — preserves paper feel)
    static let hingeDarkCanvas   = Color(red: 0.086, green: 0.075, blue: 0.055) // #16130E
    static let hingeDarkSurface  = Color(red: 0.118, green: 0.102, blue: 0.078) // #1E1A14
    static let hingeDarkSurface2 = Color(red: 0.165, green: 0.145, blue: 0.125) // #2A2520
    static let hingeDarkDivider  = Color(red: 0.184, green: 0.165, blue: 0.133) // #2F2A22
    static let hingeDarkText     = Color(red: 0.937, green: 0.910, blue: 0.855) // #EFE8DA
    static let hingeDarkTextSec  = Color(red: 0.659, green: 0.620, blue: 0.557) // #A89E8E
    static let hingeRoseDark     = Color(red: 0.941, green: 0.690, blue: 0.361) // #F0B05C OLED-brightened
}
```

## 2. Typography

Sailec is the Hinge brand face. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Use Inter for UI chrome at compact sizes (12-15pt). Fall back to SF Pro Display/Text when Sailec isn't available — it's the warmest system substitute.

```swift
extension Font {
    // Display / hero
    static let hingeDisplay      = Font.custom("Sailec-Bold",   size: 36) // "It's a match!"
    static let hingeName         = Font.custom("Sailec-Bold",   size: 28) // "Sigrún, 28"
    static let hingePromptQ      = Font.custom("Sailec-Medium", size: 22) // "Two truths and a lie"
    static let hingePromptA      = Font.custom("Sailec-Bold",   size: 24) // The user's answer

    // Sections / body
    static let hingeSection      = Font.custom("Sailec-Bold",   size: 18) // "Vitals", "My Vices"
    static let hingeButton       = Font.custom("Sailec-Bold",   size: 16) // Primary CTA

    // Chrome via Inter
    static let hingeBody         = Font.custom("Inter-Regular", size: 16) // Card body
    static let hingeBodyBold     = Font.custom("Inter-SemiBold", size: 16)
    static let hingeChip         = Font.custom("Inter-Medium",  size: 14) // Attribute chips
    static let hingeMeta         = Font.custom("Inter-Regular", size: 13) // "Liked 2h ago"
    static let hingeCaption      = Font.custom("Inter-Regular", size: 12)
    static let hingeTab          = Font.custom("Sailec-Medium", size: 10) // Tab labels
    static let hingeCommentInput = Font.custom("Inter-Regular", size: 16)
    static let hingeMatchBanner  = Font.custom("Sailec-Bold",   size: 14)
}

// If Sailec/Inter aren't bundled, this fallback keeps the warm system substitute:
extension Font {
    static func hinge(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Heart Tap (under every prompt and photo — the most-pressed button)

```swift
struct HeartTap: View {
    @Binding var isFilled: Bool
    var onTap: () -> Void

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.25, dampingFraction: 0.6)) {
                isFilled.toggle()
            }
            onTap()
        } label: {
            ZStack {
                Circle()
                    .fill(isFilled ? Color.hingeBlack : Color.hingePaper)
                    .overlay(
                        Circle().strokeBorder(Color.hingeBlack, lineWidth: 1)
                    )
                    .frame(width: 44, height: 44)
                Image(systemName: "heart.fill")
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(isFilled ? Color.hingePaper : Color.hingeBlack)
                    .scaleEffect(isFilled ? 1.0 : 0.92)
            }
        }
        .sensoryFeedback(.impact(weight: .light), trigger: isFilled)
    }
}
```

### Prompt Card (The Hero Component)

```swift
struct PromptCard: View {
    let question: String
    let answer: String
    @State private var isLiked = false
    var onCommentTap: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text(question)
                .font(.hingePromptQ)
                .foregroundStyle(Color.hingeGraphite)
                .italic() // Hinge italicizes certain templates
                .padding(.bottom, 12)

            Text(answer)
                .font(.hingePromptA)
                .foregroundStyle(Color.hingeBlack)
                .lineSpacing(4)
                .padding(.bottom, 16)

            HStack {
                Spacer()
                HeartTap(isFilled: $isLiked, onTap: onCommentTap)
            }
        }
        .padding(24)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.hingePaper)
                .overlay(
                    RoundedRectangle(cornerRadius: 16).strokeBorder(Color.hingeDividerBone, lineWidth: 0.5)
                )
        )
        .shadow(color: Color(red: 0.110, green: 0.078, blue: 0.039).opacity(0.06), radius: 8, y: 2)
        .onTapGesture { onCommentTap() }
    }
}
```

### Photo Card (4:5 portrait)

```swift
struct PhotoCard: View {
    let image: Image
    @State private var isLiked = false
    var onCommentTap: () -> Void

    var body: some View {
        ZStack(alignment: .bottomTrailing) {
            image
                .resizable()
                .aspectRatio(4/5, contentMode: .fill)
                .clipShape(RoundedRectangle(cornerRadius: 16))
            HeartTap(isFilled: $isLiked, onTap: onCommentTap)
                .padding(16)
        }
        .shadow(color: Color(red: 0.110, green: 0.078, blue: 0.039).opacity(0.06), radius: 8, y: 2)
    }
}
```

### Attribute Chip Row (Vitals)

```swift
struct AttributeChip: View {
    let glyph: String          // SF Symbol
    let label: String
    var isVerified: Bool = false

    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: glyph)
                .font(.system(size: 13, weight: .medium))
                .foregroundStyle(Color.hingeBlack)
            Text(label)
                .font(.hingeChip)
                .foregroundStyle(Color.hingeBlack)
            if isVerified {
                Image(systemName: "checkmark.seal.fill")
                    .font(.system(size: 12))
                    .foregroundStyle(Color.hingeMatchGreen)
            }
        }
        .padding(.vertical, 8)
        .padding(.horizontal, 14)
        .background(Capsule().fill(Color.hingeSand))
    }
}
```

### Standouts Hero Card

```swift
struct StandoutsCard: View {
    let photo: Image
    let answer: String
    var onSendRose: () -> Void

    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 6) {
                Image(systemName: "star.fill")
                    .font(.system(size: 14))
                    .foregroundStyle(Color.hingeRose)
                Text("Standout")
                    .font(.custom("Sailec-Bold", size: 12))
                    .foregroundStyle(Color.hingeBlack)
                Spacer()
            }
            .frame(height: 32)
            .padding(.horizontal, 16)
            .background(Color.hingeRoseLight)

            photo
                .resizable()
                .aspectRatio(1, contentMode: .fill)
                .clipped()

            VStack(alignment: .leading, spacing: 16) {
                Text(answer)
                    .font(.hingePromptA)
                    .foregroundStyle(Color.hingeBlack)
                RoseCTA(label: "Send a Rose", action: onSendRose)
            }
            .padding(16)
        }
        .background(
            RoundedRectangle(cornerRadius: 20)
                .fill(Color.hingePaper)
                .overlay(
                    RoundedRectangle(cornerRadius: 20).strokeBorder(
                        LinearGradient(
                            colors: [Color.hingeRose, Color.hingeRoseLight, Color.hingeRose],
                            startPoint: .topLeading, endPoint: .bottomTrailing
                        ),
                        lineWidth: 1
                    )
                )
        )
        .clipShape(RoundedRectangle(cornerRadius: 20))
        .shadow(color: Color(red: 0.110, green: 0.078, blue: 0.039).opacity(0.10), radius: 12, y: 4)
    }
}
```

### Rose CTA

```swift
struct RoseCTA: View {
    var label: String = "Send a Rose"
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 10) {
                Image(systemName: "leaf.fill") // Rose glyph stand-in via SF Symbols
                    .font(.system(size: 16))
                    .foregroundStyle(Color.hingeRose)
                Text(label)
                    .font(.hingeButton)
                    .foregroundStyle(Color.hingePaper)
            }
            .frame(maxWidth: .infinity)
            .frame(height: 56)
            .background(
                Capsule()
                    .fill(Color.hingeBlack)
                    .overlay(Capsule().strokeBorder(Color.hingeRose, lineWidth: 1))
            )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .heavy), trigger: UUID())
    }
}
```

### Primary CTA (Send Like / Match / Continue)

```swift
struct HingePrimaryButton: View {
    let label: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.hingeButton)
                .foregroundStyle(Color.hingePaper)
                .frame(maxWidth: .infinity)
                .frame(height: 56)
                .background(Capsule().fill(Color.hingeBlack))
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}
```

### Comment Sheet (tap-to-comment pattern)

```swift
struct CommentSheet: View {
    let source: AnyView                // The pinned prompt card or photo
    @State private var comment: String = ""
    var onSend: (String) -> Void

    var body: some View {
        VStack(spacing: 16) {
            // Pinned source — the tapped prompt or photo
            source
                .frame(maxHeight: .infinity, alignment: .top)

            HStack(spacing: 10) {
                TextField("Add a comment about her response", text: $comment, axis: .vertical)
                    .font(.hingeCommentInput)
                    .foregroundStyle(Color.hingeBlack)
                    .padding(.vertical, 14)
                    .padding(.horizontal, 20)
                    .background(Capsule().fill(Color.hingeSand))
                    .lineLimit(1...4)

                Button {
                    onSend(comment)
                } label: {
                    Image(systemName: "arrow.up")
                        .font(.system(size: 16, weight: .bold))
                        .foregroundStyle(Color.hingePaper)
                        .frame(width: 44, height: 44)
                        .background(Circle().fill(comment.isEmpty ? Color.hingeBone : Color.hingeBlack))
                }
                .disabled(comment.isEmpty)
                .sensoryFeedback(.impact(weight: .medium), trigger: comment.isEmpty == false)
            }
            .padding(.horizontal, 16)
            .padding(.bottom, 24)
        }
        .padding(.top, 24)
        .background(
            RoundedRectangle(cornerRadius: 24)
                .fill(Color.hingePaper)
                .ignoresSafeArea(edges: .bottom)
        )
    }
}
```

### Match Celebration

```swift
struct MatchCelebration: View {
    let myAvatar: Image
    let theirName: String
    let theirAvatar: Image
    var onMessage: () -> Void
    var onKeepBrowsing: () -> Void

    @State private var confettiTrigger = false

    var body: some View {
        ZStack {
            Color.hingeCream.ignoresSafeArea()

            // Confetti layer (Rose Gold particles)
            ForEach(0..<24, id: \.self) { i in
                Circle()
                    .fill(Color.hingeRose)
                    .frame(width: CGFloat.random(in: 4...10), height: CGFloat.random(in: 4...10))
                    .offset(x: CGFloat.random(in: -160...160), y: confettiTrigger ? 600 : -300)
                    .opacity(confettiTrigger ? 0 : 1)
                    .animation(.easeIn(duration: 1.8).delay(Double(i) * 0.04), value: confettiTrigger)
            }

            VStack(spacing: 32) {
                HStack(spacing: 24) {
                    myAvatar
                        .resizable().aspectRatio(contentMode: .fill)
                        .frame(width: 120, height: 120).clipShape(Circle())
                    theirAvatar
                        .resizable().aspectRatio(contentMode: .fill)
                        .frame(width: 120, height: 120).clipShape(Circle())
                }

                VStack(spacing: 8) {
                    Text("It's a match!")
                        .font(.hingeDisplay)
                        .foregroundStyle(Color.hingeBlack)
                        .tracking(-0.6)
                    Text("You and \(theirName) liked each other")
                        .font(.hingeBody)
                        .foregroundStyle(Color.hingeGraphite)
                }

                VStack(spacing: 12) {
                    HingePrimaryButton(label: "Send a message", action: onMessage)
                    Button("Keep browsing", action: onKeepBrowsing)
                        .font(.custom("Inter-Medium", size: 14))
                        .foregroundStyle(Color.hingeGraphite)
                }
                .padding(.horizontal, 24)
            }
        }
        .onAppear {
            confettiTrigger = true
        }
        .sensoryFeedback(.success, trigger: confettiTrigger)
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        appearance.backgroundColor = UIColor(Color.hingeCream).withAlphaComponent(0.92)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            DiscoverView()  .tabItem { Label("Discover",   systemImage: "safari") }
            LikesYouView()  .tabItem { Label("Likes You",  systemImage: "heart") }
            StandoutsView() .tabItem { Label("Standouts",  systemImage: "star") }
            MatchesView()   .tabItem { Label("Matches",    systemImage: "bubble.left") }
            ProfileView()   .tabItem { Label("Profile",    systemImage: "person") }
        }
        .tint(Color.hingeBlack) // Hinge does not use color on active tab — it uses fill + warm black
    }
}
```

## 5. Motion & Haptics

```swift
// Heart-tap (most-repeated motion in the app)
.sensoryFeedback(.impact(weight: .light), trigger: isLiked)
withAnimation(.spring(response: 0.25, dampingFraction: 0.6)) { isLiked.toggle() }

// Send Like CTA
.sensoryFeedback(.impact(weight: .medium), trigger: sendPressed)

// Rose send (premium / currency)
.sensoryFeedback(.impact(weight: .heavy), trigger: rosePressed)

// Match celebration
.sensoryFeedback(.success, trigger: matchTriggered)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Comment sheet shared transition
.matchedGeometryEffect(id: "commentSource", in: commentNS)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Heart-tap (filled) | `heart.fill` | 18pt |
| Heart-tap container outline | n/a (`Circle().strokeBorder`) | 44pt |
| X decline | `xmark` | 22pt |
| Send arrow (comment) | `arrow.up` | 16pt |
| Standouts star | `star.fill` | 14pt |
| Rose currency | `leaf.fill` (placeholder for proprietary rose glyph) | 16pt |
| Verified attribute | `checkmark.seal.fill` (Match Green) | 12pt |
| Attribute — height | `ruler` | 13pt |
| Attribute — job | `briefcase.fill` | 13pt |
| Attribute — location | `mappin.and.ellipse` | 13pt |
| Attribute — education | `graduationcap.fill` | 13pt |
| Attribute — religion | `book.closed.fill` | 13pt |
| Discover tab | `safari` / `safari.fill` | 24pt |
| Likes You tab | `heart` / `heart.fill` | 24pt |
| Standouts tab | `star` / `star.fill` | 24pt |
| Matches tab | `bubble.left` / `bubble.left.fill` | 24pt |
| Profile tab | `person` / `person.fill` | 24pt |
| Back | `chevron.left` | 18pt |
| Settings | `gearshape` | 22pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `matchedGeometryEffect` on comment-sheet shared transition); fall back to `UIImpactFeedbackGenerator` on iOS 16
- Support Dynamic Type on prompt question, prompt answer, name, body, metadata, comment input — fix attribute chips, tab labels, and the "H" mark (layout-sensitive)
- VoiceOver: group prompt card as a single accessibility element with label `"Prompt: Two truths and a lie. Answer: I have never seen Star Wars. Double-tap to comment."`; the heart-tap is a separate trait `"Like, button"` / `"Liked, button"`
- Contrast: Hinge Black (`#1A1A1A`) on cream (`#FDF8F2`) meets WCAG AAA at all sizes; Hinge Graphite (`#4A4239`) on cream meets AAA at body sizes and above
- Reduce Motion: skip the confetti animation on match celebration when `accessibilityReduceMotion` is true — fall back to a 200ms cross-fade with the success haptic preserved
- Dark mode: implement via `@Environment(\.colorScheme)` — swap all canvas/text/surface tokens; Rose Gold brightens slightly to `#F0B05C` to compensate for OLED dimming
- Roses currency screen reader: announce remaining count after a Rose send — `"Rose sent. You have 2 Roses remaining."`
