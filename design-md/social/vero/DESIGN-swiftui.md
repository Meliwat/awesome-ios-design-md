# Vero (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vero's calm, true-black visual language into paste-ready SwiftUI code: `Color` extensions, the brand gradient, `Font` extensions, and example views for the 7 post-type selector, the chronological feed post, and typed inner cards.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — primary)
    static let veroCanvas   = Color.black                                       // #000000
    static let veroSurface1 = Color(red: 0.055, green: 0.055, blue: 0.063)     // #0E0E10
    static let veroSurface2 = Color(red: 0.086, green: 0.086, blue: 0.094)     // #161618
    static let veroDivider  = Color(red: 0.137, green: 0.137, blue: 0.145)     // #232325

    // MARK: - Canvas & Surfaces (Light — rare)
    static let veroLightCanvas   = Color.white                                  // #FFFFFF
    static let veroLightSurface1 = Color(red: 0.965, green: 0.965, blue: 0.969) // #F6F6F7
    static let veroLightDivider  = Color(red: 0.894, green: 0.894, blue: 0.902) // #E4E4E6

    // MARK: - Text
    static let veroTextPrimary   = Color.white                                  // #FFFFFF
    static let veroTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.627) // #9A9AA0
    static let veroTextTertiary  = Color(red: 0.369, green: 0.369, blue: 0.392) // #5E5E64

    // MARK: - Brand
    static let veroTeal      = Color(red: 0.0,   green: 0.761, blue: 0.722) // #00C2B8
    static let veroBlue      = Color(red: 0.0,   green: 0.596, blue: 0.902) // #0098E6
    static let veroGradientA = Color(red: 0.0,   green: 0.820, blue: 0.757) // #00D1C1
    static let veroGradientB = Color(red: 0.0,   green: 0.475, blue: 0.827) // #0079D3
    static let veroTealPressed = Color(red: 0.0, green: 0.659, blue: 0.624) // #00A89F

    // MARK: - Ratings
    static let veroAmber   = Color(red: 0.910, green: 0.698, blue: 0.227) // #E8B23A
    static let veroOnAmber = Color(red: 0.102, green: 0.071, blue: 0.024) // #1A1206

    // MARK: - Semantic
    static let veroSuccess = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let veroError   = Color(red: 1.0,   green: 0.302, blue: 0.310) // #FF4D4F
}

// The single brand expression — use sparingly.
extension LinearGradient {
    static let veroBrand = LinearGradient(
        colors: [.veroGradientA, .veroGradientB],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Vero's UI face is **Manrope** (SIL OFL — free to bundle). Register the TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static func vero(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .heavy, .black:  return "Manrope-ExtraBold"
            case .bold:           return "Manrope-Bold"
            case .semibold:       return "Manrope-SemiBold"
            case .medium:         return "Manrope-Medium"
            default:              return "Manrope-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let veroDisplay     = Font.custom("Manrope-ExtraBold", size: 32)
    static let veroScreenTitle = Font.custom("Manrope-Bold",      size: 26)
    static let veroSection     = Font.custom("Manrope-Bold",      size: 22)
    static let veroAuthor      = Font.custom("Manrope-Bold",      size: 18)
    static let veroAuthorSm    = Font.custom("Manrope-Bold",      size: 14)
    static let veroTitle       = Font.custom("Manrope-SemiBold",  size: 15)
    static let veroCaption     = Font.custom("Manrope-Medium",    size: 14)
    static let veroBodyRead    = Font.custom("Manrope-Regular",   size: 14)
    static let veroMeta        = Font.custom("Manrope-Regular",   size: 13)
    static let veroButton      = Font.custom("Manrope-Bold",      size: 15)
    static let veroTypeLabel   = Font.custom("Manrope-SemiBold",  size: 11)
    static let veroTab         = Font.custom("Manrope-Medium",    size: 10)
}
```

## 3. Signature Components

### 7 Post-Type Selector

```swift
enum VeroPostType: String, CaseIterable, Identifiable {
    case photo, video, link, music, film, book, place
    var id: String { rawValue }
    var label: String { rawValue.capitalized }
    var symbol: String {
        switch self {
        case .photo: return "photo"
        case .video: return "video"
        case .link:  return "link"
        case .music: return "music.note"
        case .film:  return "film"
        case .book:  return "book.closed"
        case .place: return "mappin.and.ellipse"
        }
    }
}

struct PostTypeSelector: View {
    @Binding var selected: VeroPostType
    private let cols = [GridItem(.adaptive(minimum: 64), spacing: 10)]

    var body: some View {
        LazyVGrid(columns: cols, spacing: 10) {
            ForEach(VeroPostType.allCases) { type in
                let on = type == selected
                VStack(spacing: 7) {
                    Image(systemName: type.symbol)
                        .font(.system(size: 20))
                    Text(type.label).font(.veroTypeLabel)
                }
                .foregroundStyle(on ? Color.veroTeal : Color.veroTextTertiary)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 12).padding(.horizontal, 4)
                .background(
                    RoundedRectangle(cornerRadius: 12)
                        .fill(on ? Color.veroTeal.opacity(0.08) : Color.veroSurface1)
                )
                .overlay(
                    RoundedRectangle(cornerRadius: 12)
                        .strokeBorder(on ? Color.veroTeal : Color.veroDivider, lineWidth: 1)
                )
                .contentShape(Rectangle())
                .onTapGesture {
                    withAnimation(.easeOut(duration: 0.16)) { selected = type }
                }
            }
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: selected)
    }
}
```

### Chronological Feed Post

```swift
struct FeedPost: View {
    let author: String
    let type: VeroPostType
    let timeAgo: String
    let caption: String
    let likes: Int
    let comments: Int
    @State private var liked = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // Head
            HStack(spacing: 10) {
                Circle().fill(LinearGradient.veroBrand).frame(width: 38, height: 38)
                VStack(alignment: .leading, spacing: 2) {
                    Text(author).font(.veroAuthorSm).foregroundStyle(Color.veroTextPrimary)
                    HStack(spacing: 5) {
                        HStack(spacing: 4) {
                            Image(systemName: type.symbol).font(.system(size: 11))
                            Text(type.label).font(.vero(10, weight: .bold))
                        }
                        .foregroundStyle(Color.veroTeal)
                        Text("· \(timeAgo)").font(.veroMeta).foregroundStyle(Color.veroTextTertiary)
                    }
                }
                Spacer()
                Image(systemName: "ellipsis").foregroundStyle(Color.veroTextTertiary)
            }

            // Media (4:5 default)
            RoundedRectangle(cornerRadius: 10)
                .fill(LinearGradient(colors: [Color(red: 0.11, green: 0.29, blue: 0.32),
                                              Color(red: 0.03, green: 0.09, blue: 0.11)],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
                .aspectRatio(4.0/5.0, contentMode: .fit)
                .padding(.top, 12)

            Text(caption).font(.veroCaption).foregroundStyle(Color.veroTextSecondary)
                .lineSpacing(4).padding(.top, 12)

            // Actions
            HStack(spacing: 22) {
                Button { withAnimation(.spring(response: 0.24, dampingFraction: 0.6)) { liked.toggle() } } label: {
                    HStack(spacing: 6) {
                        Image(systemName: liked ? "heart.fill" : "heart").font(.system(size: 19))
                        Text("\(likes + (liked ? 1 : 0))").font(.vero(12, weight: .semibold))
                    }
                    .foregroundStyle(liked ? Color.veroTeal : Color.veroTextSecondary)
                }
                .sensoryFeedback(.impact(flexibility: .soft), trigger: liked)
                HStack(spacing: 6) {
                    Image(systemName: "bubble.left").font(.system(size: 19))
                    Text("\(comments)").font(.vero(12, weight: .semibold))
                }.foregroundStyle(Color.veroTextSecondary)
                Image(systemName: "square.and.arrow.up").font(.system(size: 19))
                    .foregroundStyle(Color.veroTextSecondary)
                Spacer()
            }
            .padding(.top, 14)
        }
        .padding(.horizontal, 16).padding(.top, 16).padding(.bottom, 14)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.veroDivider).frame(height: 0.5)
        }
    }
}
```

### Book / Film Inner Card

```swift
struct BookCard: View {
    let title: String
    let author: String
    let rating: Int   // 0...5

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 4)
                .fill(LinearGradient(colors: [Color(red: 0.76, green: 0.60, blue: 0.23),
                                              Color(red: 0.48, green: 0.35, blue: 0.09)],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 56, height: 80)
            VStack(alignment: .leading, spacing: 3) {
                Text(title).font(.veroTitle).foregroundStyle(Color.veroTextPrimary)
                Text(author).font(.vero(12)).foregroundStyle(Color.veroTextSecondary)
                Text(String(repeating: "★", count: rating) + String(repeating: "☆", count: 5 - rating))
                    .font(.vero(11, weight: .semibold))
                    .foregroundStyle(Color.veroAmber)
                    .tracking(1)
                    .padding(.top, 4)
            }
            Spacer()
        }
        .padding(12)
        .background(RoundedRectangle(cornerRadius: 10).fill(Color.veroSurface1))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.veroDivider, lineWidth: 1))
    }
}
```

### Primary "Post" Button + Wordmark

```swift
struct VeroPostButton: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text("Post")
                .font(.veroButton).foregroundStyle(.white)
                .padding(.vertical, 13).padding(.horizontal, 28)
                .background(Capsule().fill(LinearGradient.veroBrand))
        }
        .buttonStyle(.plain)
    }
}

struct VeroWordmark: View {
    var body: some View {
        Text("VERO")
            .font(.vero(26, weight: .heavy))
            .tracking(2)
            .foregroundStyle(LinearGradient.veroBrand)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct VeroTabView: View {
    var body: some View {
        TabView {
            FeedView().tabItem { Label("Feed", systemImage: "house.fill") }
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            ComposeView().tabItem { Label("", systemImage: "plus.circle") } // center, gradient ring
            InboxView().tabItem { Label("Inbox", systemImage: "envelope.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.veroTeal) // active = flat teal, no tint pill
        .toolbarBackground(Color.veroCanvas.opacity(0.96), for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
    }
}

// The center compose ring with the brand gradient stroke
struct ComposeRing: View {
    var body: some View {
        ZStack {
            Circle().strokeBorder(LinearGradient.veroBrand, lineWidth: 2).frame(width: 27, height: 27)
            Image(systemName: "plus").font(.system(size: 13, weight: .bold))
                .foregroundStyle(LinearGradient.veroBrand)
        }
    }
}
```

## 5. Motion

```swift
// Post-type select — tint over 160ms
withAnimation(.easeOut(duration: 0.16)) { selected = type }

// Like heart — gentle spring
withAnimation(.spring(response: 0.24, dampingFraction: 0.6)) { liked.toggle() }

// Compose sheet — slide up 300ms
.sheet(isPresented: $composing) { ComposeSheet() } // default detent slide

// New posts insert at top — fade only, never reshuffle existing
.transition(.opacity)

// Tab switch — crossfade content 200ms; tint changes instantly

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: selected) // post-type
.sensoryFeedback(.impact(flexibility: .soft), trigger: liked)    // like
.sensoryFeedback(.selection, trigger: selectedTab)               // tab
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Feed (tab) | `house` / `house.fill` | 22pt |
| Discover (tab) | `magnifyingglass` | 22pt |
| Compose (center) | `plus.circle` (custom gradient ring) | 27pt |
| Inbox (tab) | `envelope` / `envelope.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Post-type · Photo | `photo` | 19–20pt |
| Post-type · Video | `video` | 19–20pt |
| Post-type · Link | `link` | 19–20pt |
| Post-type · Music | `music.note` | 19–20pt |
| Post-type · Film | `film` | 19–20pt |
| Post-type · Book | `book.closed` | 19–20pt |
| Post-type · Place | `mappin.and.ellipse` | 19–20pt |
| Like | `heart` / `heart.fill` | 19pt |
| Comment | `bubble.left` | 19pt |
| Share | `square.and.arrow.up` | 19pt |
| More | `ellipsis` | 18pt |
| Notifications | `bell` | 21pt |
| Rating star | `star.fill` (or text ★) | 11pt |

## 7. Dark Mode

```swift
struct VeroTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.veroCanvas : Color.veroLightCanvas)
            .foregroundStyle(scheme == .dark ? Color.veroTextPrimary : Color(red: 0.043, green: 0.043, blue: 0.047))
            .preferredColorScheme(.dark) // Vero is true-black-first
    }
}
extension View { func veroTheme() -> some View { modifier(VeroTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Manrope TTFs (Regular/Medium/SemiBold/Bold/ExtraBold) via `Info.plist` — SIL OFL, free to distribute
- Vero is true-black-first: ship `.preferredColorScheme(.dark)`; the light mode is an accessibility/parity fallback only
- Dynamic Type: support on screen titles, author names, captions, body; keep post-type labels, tab labels, star ratings, type chips FIXED (layout-critical)
- VoiceOver: label a post "{author}, {type} post, {timeAgo}, {caption}"; the post-type selector cells as "Photo, button, selected/not selected" — all seven reachable; the like control toggles "Liked / Not liked"
- The 7 post-type selector must read as a single radio group (`.accessibilityElement(children: .contain)` with `.isSelected` traits)
- Color contrast: `#FFFFFF` and `#9A9AA0` on `#000000` pass WCAG AA; teal `#00C2B8` on black passes for icons/large text — pair small teal text carefully; `#1A1206` on `#E8B23A` passes for amber chips
- Reduce Motion: disable the like spring (instant fill) and post-type tint animation (instant); keep the new-post fade subtle
- True-black is intentional for OLED — do not substitute a charcoal in dark mode; only inset cards use `#0E0E10`
