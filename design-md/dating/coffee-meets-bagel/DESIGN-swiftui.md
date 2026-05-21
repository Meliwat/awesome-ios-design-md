# Coffee Meets Bagel (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Coffee Meets Bagel's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Primary action (Bagel Orange)
    static let cmbBagel       = Color(red: 0.957, green: 0.384, blue: 0.227) // #F4623A
    static let cmbBagelDeep   = Color(red: 0.851, green: 0.306, blue: 0.165) // #D94E2A

    // MARK: - Secondary brand (Brew Brown)
    static let cmbBrew        = Color(red: 0.627, green: 0.322, blue: 0.176) // #A0522D
    static let cmbBrewDeep    = Color(red: 0.494, green: 0.247, blue: 0.133) // #7E3F22
    static let cmbBrewPressed = Color(red: 0.412, green: 0.200, blue: 0.094) // #693318

    // MARK: - Cream (replaces white)
    static let cmbCream       = Color(red: 0.953, green: 0.894, blue: 0.812) // #F3E4CF
    static let cmbCreamSoft   = Color(red: 0.910, green: 0.831, blue: 0.722) // #E8D4B8

    // MARK: - Canvas & Surfaces (Light)
    static let cmbCanvas      = Color(red: 0.984, green: 0.965, blue: 0.937) // #FBF6EF
    static let cmbSurface1L   = Color.white                                   // #FFFFFF
    static let cmbSurface2L   = Color(red: 0.953, green: 0.925, blue: 0.882) // #F3ECE1
    static let cmbDividerL    = Color(red: 0.910, green: 0.871, blue: 0.812) // #E8DECF

    // MARK: - Canvas & Surfaces (Dark)
    static let cmbDarkCanvas  = Color(red: 0.078, green: 0.063, blue: 0.051) // #14100D
    static let cmbDarkSurface1 = Color(red: 0.122, green: 0.094, blue: 0.075) // #1F1813
    static let cmbDarkSurface2 = Color(red: 0.165, green: 0.125, blue: 0.090) // #2A2017
    static let cmbDarkDivider  = Color(red: 0.200, green: 0.157, blue: 0.114) // #33281D

    // MARK: - Text
    static let cmbTextPrimaryL   = Color(red: 0.227, green: 0.165, blue: 0.094) // #3A2A18
    static let cmbTextSecondaryL = Color(red: 0.541, green: 0.455, blue: 0.345) // #8A7458
    static let cmbTextPrimaryD   = Color(red: 0.937, green: 0.902, blue: 0.855) // #EFE6DA
    static let cmbTextSecondaryD = Color(red: 0.718, green: 0.643, blue: 0.557) // #B7A48E
    static let cmbTextTertiaryD  = Color(red: 0.486, green: 0.420, blue: 0.341) // #7C6B57

    // MARK: - Status
    static let cmbMatchGreen  = Color(red: 0.298, green: 0.765, blue: 0.541) // #4CC38A
    static let cmbPremiumGold = Color(red: 0.878, green: 0.659, blue: 0.180) // #E0A82E
}
```

## 2. Typography

CMB's brand face is Brandon Grotesque / Gotham-family; **Poppins** is the closest free face. Bundle Poppins via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let cmbScreenTitle  = Font.custom("Poppins-ExtraBold", size: 32) // -0.6 tracking
    static let cmbProfileName  = Font.custom("Poppins-Bold",      size: 26)
    static let cmbSection      = Font.custom("Poppins-Bold",      size: 22)
    static let cmbCardTitle    = Font.custom("Poppins-SemiBold",  size: 18)
    static let cmbBody         = Font.custom("Poppins-Regular",   size: 16)
    static let cmbPromptAns    = Font.custom("Poppins-SemiBold",  size: 15)
    static let cmbMeta         = Font.custom("Poppins-Regular",   size: 14)
    static let cmbChip         = Font.custom("Poppins-SemiBold",  size: 13)
    static let cmbBadge        = Font.custom("Poppins-Bold",      size: 12) // uppercase, 0.4 tracking
    static let cmbNumber       = Font.custom("Poppins-Bold",      size: 16)
    static let cmbTab          = Font.custom("Poppins-SemiBold",  size: 11)
    static let cmbButton       = Font.custom("Poppins-Bold",      size: 16)
}
```

## 3. Signature Components

### Today's Bagel Card (the core atom)

```swift
struct BagelCard: View {
    let imageName: String
    let name: String
    let age: Int
    let meta: String
    let badge: String
    let interests: [String]

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            Image(imageName)
                .resizable()
                .aspectRatio(3.0/4.0, contentMode: .fill)

            // Bottom gradient
            LinearGradient(
                colors: [.clear, Color(red: 0.078, green: 0.063, blue: 0.051).opacity(0.95)],
                startPoint: .center, endPoint: .bottom
            )

            // Badge (top-left)
            VStack {
                HStack {
                    HStack(spacing: 6) {
                        Image(systemName: "clock.fill").font(.system(size: 12))
                        Text(badge.uppercased()).font(.cmbBadge).tracking(0.4)
                    }
                    .foregroundStyle(Color.cmbCream)
                    .padding(.vertical, 6).padding(.horizontal, 12)
                    .background(.ultraThinMaterial.opacity(0.8), in: Capsule())
                    Spacer()
                }
                Spacer()
            }
            .padding(16)

            // Info block
            VStack(alignment: .leading, spacing: 4) {
                (Text(name).font(.cmbProfileName) + Text(" \(age)").font(.cmbProfileName.weight(.medium)))
                    .foregroundStyle(.white)
                Text(meta).font(.cmbMeta).foregroundStyle(Color.cmbCream)
                HStack(spacing: 8) {
                    ForEach(interests, id: \.self) { chip in
                        Text(chip)
                            .font(.cmbChip)
                            .foregroundStyle(Color.cmbCream)
                            .padding(.vertical, 5).padding(.horizontal, 12)
                            .background(Color.cmbCream.opacity(0.18), in: Capsule())
                            .overlay(Capsule().strokeBorder(Color.cmbCream.opacity(0.28), lineWidth: 1))
                    }
                }
                .padding(.top, 8)
            }
            .padding(20)
        }
        .clipShape(RoundedRectangle(cornerRadius: 24))
        .shadow(color: .black.opacity(0.7), radius: 40, x: 0, y: 16)
    }
}
```

### Action Trio (Pass / Like / Send a Bagel)

```swift
struct ActionTrio: View {
    let onPass: () -> Void
    let onLike: () -> Void
    let onSend: () -> Void
    @State private var heartScale: CGFloat = 1.0

    var body: some View {
        HStack(spacing: 28) {
            // Pass
            Button(action: onPass) {
                Image(systemName: "xmark")
                    .font(.system(size: 24, weight: .bold))
                    .foregroundStyle(Color.cmbTextSecondaryD)
                    .frame(width: 60, height: 60)
                    .background(Circle().fill(Color.cmbDarkSurface2))
                    .overlay(Circle().strokeBorder(Color.cmbDarkDivider, lineWidth: 1.5))
            }
            .buttonStyle(.plain)
            .shadow(color: .black.opacity(0.6), radius: 20, y: 8)

            // Like (largest)
            Button {
                let h = UINotificationFeedbackGenerator(); h.notificationOccurred(.success)
                withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { heartScale = 1.15 }
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.14) {
                    withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { heartScale = 1.0 }
                }
                onLike()
            } label: {
                Image(systemName: "heart.fill")
                    .font(.system(size: 32))
                    .foregroundStyle(.white)
                    .scaleEffect(heartScale)
                    .frame(width: 72, height: 72)
                    .background(Circle().fill(Color.cmbBagel))
            }
            .buttonStyle(.plain)
            .shadow(color: .black.opacity(0.6), radius: 20, y: 8)

            // Send a Bagel
            Button(action: onSend) {
                Image(systemName: "star.fill")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.cmbCream)
                    .frame(width: 60, height: 60)
                    .background(Circle().fill(Color.cmbBrew))
            }
            .buttonStyle(.plain)
            .shadow(color: .black.opacity(0.6), radius: 20, y: 8)
        }
        .padding(.vertical, 16)
    }
}
```

### Curated Batch Header

```swift
struct BatchHeader: View {
    let title: String
    let countText: String   // "6 fresh picks · expires in 23h"
    let onBeans: () -> Void

    var body: some View {
        HStack(alignment: .top) {
            VStack(alignment: .leading, spacing: 1) {
                Text(title).font(.cmbCardTitle).foregroundStyle(Color.cmbTextPrimaryD)
                Text(countText).font(.cmbBadge.weight(.semibold)).foregroundStyle(Color.cmbTextSecondaryD)
            }
            Spacer()
            Button(action: onBeans) {
                Image(systemName: "leaf.fill")
                    .font(.system(size: 18))
                    .foregroundStyle(Color.cmbCream)
                    .frame(width: 38, height: 38)
                    .background(Circle().fill(Color.cmbDarkSurface2))
            }
            .buttonStyle(.plain)
        }
        .padding(.horizontal, 20).padding(.bottom, 12)
    }
}
```

### Interest Chip

```swift
struct InterestChip: View {
    let label: String
    var selected: Bool = false

    var body: some View {
        Text(label)
            .font(.cmbChip)
            .foregroundStyle(selected ? Color.cmbCream : Color.cmbTextPrimaryD)
            .padding(.vertical, 8).padding(.horizontal, 14)
            .background(Capsule().fill(selected ? Color.cmbBrew : Color.cmbDarkSurface2))
            .overlay(Capsule().strokeBorder(selected ? Color.cmbBrew : Color.cmbDarkDivider, lineWidth: 1))
    }
}
```

### Match Takeover

```swift
struct MatchTakeover: View {
    let onSayHi: () -> Void
    let onKeep: () -> Void
    @State private var appear = false

    var body: some View {
        ZStack {
            LinearGradient(colors: [.cmbBagel, .cmbBrew], startPoint: .top, endPoint: .bottom)
                .ignoresSafeArea()
            VStack(spacing: 20) {
                Text("It's a match!")
                    .font(.cmbScreenTitle)
                    .foregroundStyle(.white)
                Image(systemName: "checkmark.circle.fill")
                    .font(.system(size: 44))
                    .foregroundStyle(Color.cmbMatchGreen)
                Button(action: onSayHi) {
                    Text("Say hi")
                        .font(.cmbButton).foregroundStyle(.white)
                        .padding(.vertical, 14).padding(.horizontal, 40)
                        .background(Capsule().fill(Color.cmbBagel))
                }
                .buttonStyle(.plain)
                Button("Keep browsing", action: onKeep)
                    .font(.cmbChip).foregroundStyle(Color.cmbCream)
            }
            .scaleEffect(appear ? 1.0 : 0.9)
            .opacity(appear ? 1 : 0)
        }
        .onAppear {
            let h = UIImpactFeedbackGenerator(style: .medium); h.impactOccurred()
            withAnimation(.easeOut(duration: 0.4)) { appear = true }
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct CMBTabView: View {
    var body: some View {
        TabView {
            SuggestedView().tabItem { Label("Suggested", systemImage: "circle.circle.fill") } // bagel logomark
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.fill") }
                .badge(3) // orange unread dot
            ProfileView().tabItem { Label("Profile", systemImage: "person.fill") }
        }
        .tint(.cmbBagel) // active icon fills + label orange; no pill
    }
}
```

## 5. Motion

```swift
// Like heart pop
withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { heartScale = 1.15 }   // then back to 1.0
// Just-liked card wash: orange overlay opacity 0.12 → 0 over 0.5s
withAnimation(.easeOut(duration: 0.5)) { likeFlash = 0 }

// Card advance (like flies up-right)
withAnimation(.easeIn(duration: 0.32)) { cardOffset = CGSize(width: 320, height: -120); cardRotation = 12 }
// Next card cross-fade + scale
withAnimation(.easeOut(duration: 0.26)) { nextScale = 1.0; nextOpacity = 1.0 }

// Match takeover scale-in
withAnimation(.easeOut(duration: 0.4)) { appear = true }

// Chip select fill
withAnimation(.easeInOut(duration: 0.18)) { selected.toggle() }

// Haptics
UINotificationFeedbackGenerator().notificationOccurred(.success)        // Like
UIImpactFeedbackGenerator(style: .light).impactOccurred()               // Pass / chip
UIImpactFeedbackGenerator(style: .medium).impactOccurred()              // Match takeover
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Suggested (tab) | `circle.circle.fill` (stand-in for bagel logomark) | 22pt |
| Discover (tab) | `magnifyingglass` | 22pt |
| Chats (tab) | `bubble.left` / `bubble.left.fill` | 22pt |
| Profile (tab) | `person` / `person.fill` | 22pt |
| Pass | `xmark` | 24pt |
| Like | `heart.fill` | 32pt |
| Send a Bagel | `star.fill` | 20pt |
| Beans / currency | `leaf.fill` (stand-in for coffee bean) | 18pt |
| Active badge | `clock.fill` | 12pt |
| Match check | `checkmark.circle.fill` | 44pt |
| Back | `chevron.left` | 22pt |
| Send message | `arrow.up` | 18pt |
| Filter | `slider.horizontal.3` | 18pt |
| Report / overflow | `ellipsis` | 20pt |
| Premium / Boost | `bolt.fill` | 18pt |

## 7. Dark Mode

```swift
struct CMBTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.cmbDarkCanvas : Color.cmbCanvas)
            .foregroundStyle(scheme == .dark ? Color.cmbTextPrimaryD : Color.cmbTextPrimaryL)
    }
}
extension View { func cmbTheme() -> some View { modifier(CMBTheme()) } }
```

CMB dark mode is a cozy coffee-house: canvas `#14100D` (warm brown-black, NOT grey), cards `#1F1813`. Bagel Orange and Brew Brown are unchanged across themes. Cream replaces white for on-photo content. On dark, cards add a 1pt `cmbDarkDivider` warm border since the deepened shadow alone is hard to read.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UINotificationFeedbackGenerator` / `UIImpactFeedbackGenerator` as shown)
- Bundle Poppins TTFs via `Info.plist` (SIL OFL — free to distribute); Brandon Grotesque is licensed and should not be redistributed
- Dynamic Type: scale profile name, section, body, prompt answer; keep chips, badges, bean numbers, tab labels FIXED
- VoiceOver: label the card "Profile: {name}, {age}, {meta}"; the action trio buttons must read "Pass on {name}", "Like {name}", "Send a Bagel to {name}"; expose Like/Pass as custom actions on the card so users need not reach the buttons
- Color is not the only signal — Like is also the *largest* control and uses a heart glyph; Pass uses ×; Send uses ★ — shape distinguishes them for color-blind users
- Contrast: white/cream text over the bottom gradient passes WCAG AA at the rendered sizes; `#3A2A18` on `#FBF6EF` and `#EFE6DA` on `#14100D` pass AA for 16pt body; white on `#F4623A` passes AA for button text
- Reduce Motion: replace the card fly-out with a cross-fade, disable the heart pop (snap), keep the orange like-flash but shorten to 250ms; match takeover fades instead of scaling
- Dark mode: invert via the `cmbDark*` palette — warm brown-black `#14100D`, NOT neutral grey; text becomes warm cream `#EFE6DA`
