# Feeld (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Feeld's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Feeld ships dark-only; this guide builds dark-native.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-native, only mode)
    static let feeldCanvas    = Color.black                                      // #000000
    static let feeldSurface1  = Color(red: 0.071, green: 0.071, blue: 0.071)    // #121212
    static let feeldSurface2  = Color(red: 0.110, green: 0.110, blue: 0.118)    // #1C1C1E
    static let feeldSurface3  = Color(red: 0.149, green: 0.149, blue: 0.149)    // #262626
    static let feeldDivider   = Color(red: 0.165, green: 0.165, blue: 0.165)    // #2A2A2A

    // MARK: - Brand
    static let feeldAcid       = Color(red: 0.910, green: 1.000, blue: 0.388)   // #E8FF63
    static let feeldAcidPress  = Color(red: 0.831, green: 0.922, blue: 0.310)   // #D4EB4F
    static let feeldPink       = Color(red: 1.000, green: 0.361, blue: 0.541)   // #FF5C8A
    static let feeldPinkDeep   = Color(red: 0.878, green: 0.267, blue: 0.478)   // #E0447A
    static let feeldLilac      = Color(red: 0.788, green: 0.722, blue: 1.000)   // #C9B8FF

    // MARK: - Text
    static let feeldTextPrimary   = Color.white                                  // #FFFFFF
    static let feeldTextSecondary = Color(red: 0.659, green: 0.659, blue: 0.659) // #A8A8A8
    static let feeldTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E
    static let feeldOnAcid        = Color.black                                  // #000000

    // MARK: - Semantic
    static let feeldSuccess = Color(red: 0.400, green: 0.878, blue: 0.639)       // #66E0A3
    static let feeldError   = Color(red: 1.000, green: 0.420, blue: 0.420)       // #FF6B6B
}
```

## 2. Typography

Feeld uses Space Grotesk for personality (display, names, sections, buttons) and Inter for legibility (body, metadata, snippets). Bundle both via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    // Space Grotesk — personality
    static let feeldDisplay  = Font.custom("SpaceGrotesk-Bold",     size: 32)   // -0.5 tracking
    static let feeldName     = Font.custom("SpaceGrotesk-Bold",     size: 26)
    static let feeldSection  = Font.custom("SpaceGrotesk-SemiBold", size: 22)
    static let feeldSubsection = Font.custom("SpaceGrotesk-SemiBold", size: 18)
    static let feeldListTitle = Font.custom("SpaceGrotesk-SemiBold", size: 15)
    static let feeldButton   = Font.custom("SpaceGrotesk-SemiBold", size: 16)

    // Inter — legibility
    static let feeldBody     = Font.custom("Inter-Regular",  size: 16)
    static let feeldMeta     = Font.custom("Inter-Regular",  size: 14)
    static let feeldSnippet  = Font.custom("Inter-Regular",  size: 13)
    static let feeldChip     = Font.custom("Inter-SemiBold", size: 12)
    static let feeldCaption  = Font.custom("Inter-Regular",  size: 12)
    static let feeldTab      = Font.custom("Inter-SemiBold", size: 10)
}

// Apply negative tracking on display/section
extension View {
    func feeldDisplayTracking() -> some View { self.tracking(-0.5) }
    func feeldSectionTracking() -> some View { self.tracking(-0.3) }
}
```

## 3. Signature Components

### Discover Profile Card (Couple/Group-aware)

```swift
struct DiscoverCard: View {
    let photo: Image
    let names: [String]          // ["Robin", "Sky"] for a couple
    let ages: [Int]
    let distanceKm: Int
    let kind: String             // "Couple" / "Single" / "Group"
    let desires: [DesireChipModel]
    let verified: Bool

    private var nameText: String { names.joined(separator: " & ") }
    private var ageText: String  { ages.map(String.init).joined(separator: " & ") }

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            photo
                .resizable()
                .aspectRatio(contentMode: .fill)

            LinearGradient(
                colors: [.clear, .black.opacity(0.72), .black.opacity(0.92)],
                startPoint: .top, endPoint: .bottom
            )
            .frame(maxHeight: .infinity, alignment: .bottom)

            if verified {
                HStack(spacing: 5) {
                    Image(systemName: "checkmark")
                        .font(.system(size: 12, weight: .bold))
                        .foregroundStyle(Color.feeldAcid)
                    Text("Verified")
                        .font(.feeldCaption.weight(.semibold))
                        .foregroundStyle(Color.feeldTextPrimary)
                }
                .padding(.horizontal, 10).padding(.vertical, 6)
                .background(.ultraThinMaterial, in: Capsule())
                .padding(18)
                .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topTrailing)
            }

            VStack(alignment: .leading, spacing: 4) {
                Text(nameText)
                    .font(.feeldName).feeldSectionTracking()
                    .foregroundStyle(.white)
                    .lineLimit(2)                       // never truncate a couple's 2nd name
                Text("\(kind) · \(ageText) · \(distanceKm) km away")
                    .font(.feeldMeta)
                    .foregroundStyle(.white.opacity(0.78))
                FlowChips(desires: desires)
                    .padding(.top, 8)
            }
            .padding(.horizontal, 22).padding(.bottom, 18)
        }
        .clipShape(RoundedRectangle(cornerRadius: 28, style: .continuous))
        .padding(.horizontal, 16)
    }
}
```

### Action Row (Pass · Like · Wink)

```swift
struct ActionRow: View {
    let onPass: () -> Void
    let onLike: () -> Void
    let onWink: () -> Void
    @State private var likeScale: CGFloat = 1.0

    var body: some View {
        HStack(spacing: 22) {
            Button(action: onPass) {
                Image(systemName: "xmark")
                    .font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(Color.feeldTextSecondary)
                    .frame(width: 52, height: 52)
                    .background(Circle().fill(Color.feeldSurface2))
                    .overlay(Circle().strokeBorder(Color.feeldDivider, lineWidth: 1))
            }

            Button {
                likeScale = 0.92
                withAnimation(.easeOut(duration: 0.18)) { likeScale = 1.0 }
                UIImpactFeedbackGenerator(style: .soft).impactOccurred()
                onLike()
            } label: {
                Image(systemName: "heart.fill")
                    .font(.system(size: 28))
                    .foregroundStyle(Color.feeldOnAcid)
                    .frame(width: 64, height: 64)
                    .background(Circle().fill(Color.feeldAcid))
                    .shadow(color: Color.feeldAcid.opacity(0.5), radius: 15, x: 0, y: 12)
                    .scaleEffect(likeScale)
            }

            Button(action: onWink) {
                Image(systemName: "star.fill")
                    .font(.system(size: 22))
                    .foregroundStyle(Color.feeldLilac)
                    .frame(width: 52, height: 52)
                    .background(Circle().fill(Color.feeldSurface2))
                    .overlay(Circle().strokeBorder(Color.feeldDivider, lineWidth: 1))
            }
        }
        .padding(.vertical, 8)
    }
}
```

### Desire Chip + Flow Layout

```swift
struct DesireChipModel: Identifiable {
    let id = UUID()
    let label: String
    enum State { case selected, unselected, emotional }
    let state: State
}

struct DesireChip: View {
    let model: DesireChipModel

    var body: some View {
        Text(model.label)
            .font(.feeldChip)
            .padding(.horizontal, 15).padding(.vertical, 9)
            .background(background)
            .overlay(Capsule().strokeBorder(border, lineWidth: 1))
            .foregroundStyle(foreground)
            .clipShape(Capsule())
    }

    private var background: Color {
        switch model.state {
        case .selected:   return .feeldAcid
        case .unselected: return .feeldSurface2
        case .emotional:  return Color.feeldPink.opacity(0.16)
        }
    }
    private var foreground: Color {
        switch model.state {
        case .selected:   return .feeldOnAcid
        case .unselected: return .feeldTextPrimary
        case .emotional:  return .feeldPink
        }
    }
    private var border: Color {
        switch model.state {
        case .selected:   return .clear
        case .unselected: return .feeldDivider
        case .emotional:  return Color.feeldPink.opacity(0.4)
        }
    }
}

struct FlowChips: View {
    let desires: [DesireChipModel]
    var body: some View {
        // Use a wrapping layout (iOS 16 Layout or a LazyVGrid). Chips wrap, never truncate.
        FlexibleWrap(spacing: 7) {
            ForEach(desires) { DesireChip(model: $0) }
        }
    }
}
```

### Connections Row

```swift
struct ConnectionRow: View {
    let avatar: LinearGradient
    let name: String              // supports "Jordan & Lee"
    let snippet: String
    let time: String
    let unread: Bool

    var body: some View {
        HStack(spacing: 14) {
            Circle().fill(avatar).frame(width: 48, height: 48)
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.feeldListTitle).foregroundStyle(Color.feeldTextPrimary)
                Text(snippet).font(.feeldSnippet).foregroundStyle(Color.feeldTextSecondary).lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 6) {
                Text(time).font(.feeldCaption).foregroundStyle(Color.feeldTextTertiary)
                if unread { Circle().fill(Color.feeldAcid).frame(width: 6, height: 6) }
            }
        }
        .padding(.vertical, 12)
        .overlay(Divider().background(Color.feeldDivider), alignment: .bottom)
    }
}
```

### Buttons

```swift
struct FeeldPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.feeldButton)
                .foregroundStyle(Color.feeldOnAcid)
                .padding(.vertical, 15).padding(.horizontal, 30)
                .frame(maxWidth: .infinity)
                .background(Capsule().fill(Color.feeldAcid))
        }
        .buttonStyle(PressScale())
    }
}

struct PressScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label.scaleEffect(configuration.isPressed ? 0.98 : 1.0)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct FeeldTabView: View {
    @State private var selection = 0
    var body: some View {
        TabView(selection: $selection) {
            DiscoverView().tag(0).tabItem { Image(systemName: "magnifyingglass") }
            LikesView().tag(1).tabItem { Image(systemName: "heart") }
            ConnectionsView().tag(2).tabItem { Image(systemName: "bubble.left.and.bubble.right") }
            ProfileView().tag(3).tabItem { Image(systemName: "person") }
        }
        .tint(.feeldAcid)            // active icon = acid; no pill, no colored label block
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithTransparentBackground()
            a.backgroundColor = UIColor.black.withAlphaComponent(0.92)
            a.shadowColor = UIColor(Color.feeldDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Card swipe — 1:1 drag, rotation ±8°, spring commit
.gesture(
    DragGesture()
        .onChanged { v in
            offset = v.translation
            rotation = Double(v.translation.width / cardWidth) * 8
        }
        .onEnded { v in
            if abs(v.translation.width) > cardWidth * 0.35 {
                withAnimation(.spring(response: 0.22, dampingFraction: 0.8)) {
                    offset.width = v.translation.width > 0 ? 800 : -800
                }
            } else {
                withAnimation(.spring(response: 0.3, dampingFraction: 0.8)) {
                    offset = .zero; rotation = 0
                }
            }
        }
)

// Like tap — scale 0.92 → 1.0 in 180ms ease-out + soft haptic (see ActionRow)

// Wink bounce — scale 1.0 → 1.25 → 1.0 over 260ms
withAnimation(.spring(response: 0.26, dampingFraction: 0.5)) { winkScale = 1.25 }

// Connection made — full-screen takeover, avatars slide from edges 320ms ease-out + acid burst
withAnimation(.easeOut(duration: 0.32)) { avatarsTogether = true }

// Haptics
UIImpactFeedbackGenerator(style: .soft).impactOccurred()   // Like commit
UIImpactFeedbackGenerator(style: .light).impactOccurred()  // Wink
UISelectionFeedbackGenerator().selectionChanged()          // chip toggle
UINotificationFeedbackGenerator().notificationOccurred(.success) // Connection
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Discover (tab) | `magnifyingglass` | 23pt |
| Likes (tab) | `heart` / `heart.fill` | 23pt |
| Connections (tab) | `bubble.left.and.bubble.right` | 23pt |
| Profile (tab) | `person` / `person.fill` | 23pt |
| Pass | `xmark` | 22pt |
| Like | `heart.fill` | 28pt |
| Wink / super-interest | `star.fill` | 22pt |
| Verified | `checkmark` | 12pt |
| Filters (top bar) | `slider.horizontal.3` | 17pt |
| Settings | `gearshape` | 17pt |
| Back | `chevron.left` | 20pt |
| Send message | `arrow.up.circle.fill` | 28pt |
| Block / report | `exclamationmark.triangle` | 18pt |
| Add photo | `plus` | 20pt |

## 7. Dark Mode

Feeld ships **dark-only** — there is no light theme to toggle. Force dark and never read `colorScheme` to swap palettes.

```swift
struct FeeldRoot: View {
    var body: some View {
        FeeldTabView()
            .preferredColorScheme(.dark)   // lock dark — Feeld is dark-native
            .background(Color.feeldCanvas.ignoresSafeArea())
            .tint(.feeldAcid)
    }
}
```

If a system surface (share sheet, mail composer) appears in light, that is the OS chrome — keep accents `#E8FF63` / `#FF5C8A` identical; do not build an in-app light theme.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (iOS 16 `Layout` protocol enables the flowing Desire chip wrap; below that, use a measured `LazyVGrid`)
- Bundle Space Grotesk + Inter TTFs via `Info.plist` `UIAppFonts` — both are SIL OFL, free to distribute
- Dynamic Type: scale display, name, section, body, snippet; pin Desire-chip text, tab labels, verified-pill text at fixed sizes (they are layout-sensitive pills)
- Couple/group names: set `.lineLimit(2)` and `.minimumScaleFactor(0.85)` on the name so a two-name couple never truncates mid-name
- VoiceOver: label the Discover card "Profile: {names}, {kind}, {ages}, {distance} kilometers away, {n} desires"; label Like as "Like", Pass as "Pass", Wink as "Send a Wink"; expose the three actions via `.accessibilityAction` so the card is operable without swiping
- Color contrast: `#FFFFFF` on `#000000` is maximal; `#000000` on `#E8FF63` passes AA for the acid button; `#FF5C8A` on `#000000` passes AA for large text — verify pink on dark for small text and bump weight if needed
- Reduce Motion: disable card rotation and the Connection avatar-slide; substitute a crossfade; keep the chip fill swap (conveys state)
- Reduce Transparency: replace `.ultraThinMaterial` verified pill with solid `Color.black.opacity(0.85)`
- The acid Like glow is decorative — ensure the heart icon itself carries the meaning for users who can't perceive the halo
