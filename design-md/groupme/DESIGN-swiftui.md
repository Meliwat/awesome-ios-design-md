# GroupMe (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates GroupMe's visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, the colored chat nav bar, message bubbles, the signature heart-like pill, the image-gallery block, and the bottom tab bar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (interactive)
    static let gmBlue        = Color(red: 0.000, green: 0.686, blue: 0.941) // #00AFF0
    static let gmBluePressed = Color(red: 0.000, green: 0.569, blue: 0.780) // #0091C7
    static let gmBlueDeep    = Color(red: 0.000, green: 0.467, blue: 0.710) // #0077B5

    // MARK: - Canvas & Surfaces (Light)
    static let gmCanvas        = Color.white                                   // #FFFFFF
    static let gmSurfaceGray   = Color(red: 0.969, green: 0.969, blue: 0.973) // #F7F7F8
    static let gmInboundLight  = Color(red: 0.941, green: 0.941, blue: 0.941) // #F0F0F0
    static let gmDividerLight  = Color(red: 0.894, green: 0.894, blue: 0.902) // #E4E4E6

    // MARK: - Canvas & Surfaces (Dark)
    static let gmDarkCanvas   = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let gmDarkSurface1 = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let gmInboundDark  = Color(red: 0.165, green: 0.165, blue: 0.173) // #2A2A2C
    static let gmDarkDivider  = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E

    // MARK: - Text
    static let gmTextPrimary    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let gmTextSecondary  = Color(red: 0.416, green: 0.416, blue: 0.416) // #6A6A6A
    static let gmTextTertiary   = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let gmDarkTextPrimary   = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC
    static let gmDarkTextSecondary = Color(red: 0.627, green: 0.627, blue: 0.627) // #A0A0A0

    // MARK: - Semantic
    static let gmLikeHeart = Color(red: 1.000, green: 0.231, blue: 0.361) // #FF3B5C
    static let gmError     = Color(red: 0.945, green: 0.369, blue: 0.424) // #F15E6C
    static let gmSuccess   = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let gmWarning   = Color(red: 0.945, green: 0.769, blue: 0.059) // #F1C40F

    // MARK: - Per-Group Theme Accents
    static let gmThemeCoral  = Color(red: 1.000, green: 0.420, blue: 0.420) // #FF6B6B
    static let gmThemeGreen  = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let gmThemePurple = Color(red: 0.608, green: 0.349, blue: 0.714) // #9B59B6
    static let gmThemeTeal   = Color(red: 0.102, green: 0.737, blue: 0.612) // #1ABC9C
}

// Avatar generated gradients (assigned per member)
struct GMAvatarGradient {
    static let warm   = [Color(hex: 0xFF6B6B), Color(hex: 0xFF8E53)]
    static let green  = [Color(hex: 0x2ECC71), Color(hex: 0x16A085)]
    static let purple = [Color(hex: 0x9B59B6), Color(hex: 0x6C5CE7)]
    static let blue   = [Color(hex: 0x4A90D9), Color(hex: 0x1E3A5F)]
    static let amber  = [Color(hex: 0xF39C12), Color(hex: 0xB5651D)]
    static let all    = [warm, green, purple, blue, amber]
    static func forId(_ id: Int) -> [Color] { all[abs(id) % all.count] }
}

extension Color {
    init(hex: UInt32) {
        self.init(
            red:   Double((hex >> 16) & 0xFF) / 255,
            green: Double((hex >> 8)  & 0xFF) / 255,
            blue:  Double(hex & 0xFF) / 255
        )
    }
}
```

## 2. Typography

GroupMe ships no brand typeface — it uses the iOS system face (SF Pro). Use `Font.system` so Dynamic Type and the system face apply automatically.

```swift
extension Font {
    static let gmLargeNav   = Font.system(size: 32, weight: .heavy)   // "Chats"
    static let gmScreen     = Font.system(size: 26, weight: .bold)    // modal titles
    static let gmGroupName   = Font.system(size: 22, weight: .bold)   // nav-bar group name
    static let gmSection    = Font.system(size: 18, weight: .bold)
    static let gmBody       = Font.system(size: 16, weight: .regular) // message text
    static let gmRowTitle   = Font.system(size: 15, weight: .semibold)
    static let gmPreview    = Font.system(size: 14, weight: .regular)
    static let gmMeta       = Font.system(size: 14, weight: .regular)
    static let gmSenderName = Font.system(size: 12, weight: .semibold)
    static let gmLikeCount  = Font.system(size: 10, weight: .bold)
    static let gmTab        = Font.system(size: 10, weight: .medium)
    static let gmUnread     = Font.system(size: 12, weight: .bold)
    static let gmButton     = Font.system(size: 16, weight: .bold)
    static let gmEmojiOnly  = Font.system(size: 40)
}
```

## 3. Signature Components

### Chat Nav Bar (the colored header)

```swift
struct GMChatNavBar: View {
    let groupName: String
    let subline: String
    let avatarGradient: [Color]
    let initials: String
    let onBack: () -> Void
    let onInfo: () -> Void

    var body: some View {
        HStack(spacing: 10) {
            Button(action: onBack) {
                Image(systemName: "chevron.left")
                    .font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(.white)
            }
            Circle()
                .fill(LinearGradient(colors: avatarGradient, startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 34, height: 34)
                .overlay(Text(initials).font(.system(size: 14, weight: .bold)).foregroundStyle(.white))

            VStack(alignment: .leading, spacing: 1) {
                Text(groupName).font(.gmGroupName).foregroundStyle(.white).lineLimit(1)
                Text(subline).font(.system(size: 11)).foregroundStyle(.white.opacity(0.82)).lineLimit(1)
            }
            Spacer()
            Button(action: onInfo) {
                Image(systemName: "info.circle")
                    .font(.system(size: 21))
                    .foregroundStyle(.white)
            }
        }
        .padding(.horizontal, 14)
        .frame(height: 56)
        .background(Color.gmBlue.ignoresSafeArea(edges: .top))
    }
}
```

### Message Bubble + Like Pill

```swift
struct GMMessageBubble: View {
    let text: String
    let isOutbound: Bool
    var likeCount: Int = 0
    @Environment(\.colorScheme) private var scheme

    private var bubbleColor: Color {
        if isOutbound { return .gmBlue }
        return scheme == .dark ? .gmInboundDark : .gmInboundLight
    }
    private var textColor: Color {
        if isOutbound { return .white }
        return scheme == .dark ? .gmDarkTextPrimary : .gmTextPrimary
    }

    var body: some View {
        Text(text)
            .font(.gmBody)
            .foregroundStyle(textColor)
            .padding(.vertical, 9)
            .padding(.horizontal, 13)
            .background(
                BubbleShape(isOutbound: isOutbound).fill(bubbleColor)
            )
            .overlay(alignment: isOutbound ? .bottomTrailing : .bottomLeading) {
                if likeCount > 0 {
                    GMLikePill(count: likeCount)
                        .offset(x: isOutbound ? -8 : 8, y: 10)
                }
            }
            .frame(maxWidth: 260, alignment: isOutbound ? .trailing : .leading)
    }
}

struct BubbleShape: Shape {
    let isOutbound: Bool
    func path(in rect: CGRect) -> Path {
        let r: CGFloat = 18, tail: CGFloat = 5
        return Path(roundedRect: rect,
                    cornerRadii: RectangleCornerRadii(
                        topLeading: r,
                        bottomLeading: isOutbound ? r : tail,
                        bottomTrailing: isOutbound ? tail : r,
                        topTrailing: r))
    }
}

struct GMLikePill: View {
    let count: Int
    @Environment(\.colorScheme) private var scheme
    @State private var pop = false

    var body: some View {
        HStack(spacing: 3) {
            Image(systemName: "heart.fill")
                .font(.system(size: 9))
                .foregroundStyle(Color.gmLikeHeart)
            Text("\(count)")
                .font(.gmLikeCount)
                .foregroundStyle(scheme == .dark ? Color.gmDarkTextSecondary : Color.gmTextSecondary)
        }
        .padding(.vertical, 2)
        .padding(.leading, 5)
        .padding(.trailing, 7)
        .background(
            Capsule().fill(scheme == .dark ? Color.gmDarkSurface1 : Color.gmCanvas)
        )
        .overlay(Capsule().strokeBorder(scheme == .dark ? Color.gmDarkDivider : Color.gmDividerLight, lineWidth: 1))
        .scaleEffect(pop ? 1.0 : 0.6)
        .onAppear {
            withAnimation(.spring(response: 0.22, dampingFraction: 0.7)) { pop = true }
        }
    }
}
```

### Sender Row (avatar + colored name above a run)

```swift
struct GMInboundRow: View {
    let senderName: String
    let avatarGradient: [Color]
    let initials: String
    let accent: Color           // group theme accent
    let bubbles: [String]

    var body: some View {
        HStack(alignment: .bottom, spacing: 8) {
            Circle()
                .fill(LinearGradient(colors: avatarGradient, startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 30, height: 30)
                .overlay(Text(initials).font(.system(size: 12, weight: .bold)).foregroundStyle(.white))

            VStack(alignment: .leading, spacing: 3) {
                Text(senderName)
                    .font(.gmSenderName)
                    .foregroundStyle(accent)
                    .padding(.leading, 4)
                ForEach(bubbles, id: \.self) { GMMessageBubble(text: $0, isOutbound: false) }
            }
            Spacer(minLength: 40)
        }
    }
}
```

### Image Gallery Block

```swift
struct GMGalleryBlock: View {
    let images: [Color]   // placeholder gradients; swap for AsyncImage
    private let cell: CGFloat = 92

    var body: some View {
        let shown = Array(images.prefix(4))
        LazyVGrid(columns: [GridItem(.fixed(cell), spacing: 3), GridItem(.fixed(cell), spacing: 3)], spacing: 3) {
            ForEach(Array(shown.enumerated()), id: \.offset) { idx, c in
                ZStack {
                    RoundedRectangle(cornerRadius: 8).fill(c).frame(width: cell, height: cell)
                    if idx == 3 && images.count > 4 {
                        RoundedRectangle(cornerRadius: 8).fill(Color.black.opacity(0.5))
                        Text("+\(images.count - 4)").font(.system(size: 16, weight: .bold)).foregroundStyle(.white)
                    }
                }
            }
        }
        .padding(4)
        .background(BubbleShape(isOutbound: false).fill(Color.gmInboundLight))
    }
}
```

### Chats List Row

```swift
struct GMChatRow: View {
    let groupName: String
    let preview: String
    let time: String
    let unread: Int
    let avatarGradient: [Color]
    let initials: String

    var body: some View {
        HStack(spacing: 12) {
            Circle()
                .fill(LinearGradient(colors: avatarGradient, startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 52, height: 52)
                .overlay(Text(initials).font(.system(size: 18, weight: .bold)).foregroundStyle(.white))
            VStack(alignment: .leading, spacing: 3) {
                Text(groupName).font(.gmRowTitle)
                Text(preview).font(.gmPreview).foregroundStyle(Color.gmTextSecondary).lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 6) {
                Text(time).font(.system(size: 12)).foregroundStyle(Color.gmTextTertiary)
                if unread > 0 {
                    Text("\(unread)")
                        .font(.gmUnread).foregroundStyle(.white)
                        .padding(.horizontal, 7).padding(.vertical, 3)
                        .background(Capsule().fill(Color.gmBlue))
                }
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
    }
}
```

### Composer

```swift
struct GMComposer: View {
    @Binding var text: String
    let onAttach: () -> Void
    let onSend: () -> Void
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(spacing: 10) {
            Button(action: onAttach) {
                Image(systemName: "plus.circle")
                    .font(.system(size: 26))
                    .foregroundStyle(Color.gmBlue)
            }
            TextField("Send a message", text: $text, axis: .vertical)
                .font(.gmBody)
                .padding(.horizontal, 16)
                .frame(minHeight: 38)
                .background(Capsule().fill(scheme == .dark ? Color.gmInboundDark : Color.gmInboundLight))
            Button(action: onSend) {
                Image(systemName: "paperplane.fill")
                    .font(.system(size: 16))
                    .foregroundStyle(.white)
                    .frame(width: 38, height: 38)
                    .background(Circle().fill(text.isEmpty ? Color(white: 0.78) : Color.gmBlue))
            }
            .disabled(text.isEmpty)
        }
        .padding(.horizontal, 14)
        .padding(.top, 10)
        .padding(.bottom, 8)
        .background((scheme == .dark ? Color.gmDarkSurface1 : Color.gmCanvas)
            .overlay(Rectangle().frame(height: 0.5).foregroundStyle(scheme == .dark ? Color.gmDarkDivider : Color.gmDividerLight), alignment: .top))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct GMTabView: View {
    var body: some View {
        TabView {
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.and.bubble.right.fill") }
            PeopleView().tabItem { Label("People", systemImage: "person.2.fill") }
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.gmBlue)   // active tab is GroupMe Blue
    }
}
```

## 5. Motion

```swift
// Like pill pop
withAnimation(.spring(response: 0.22, dampingFraction: 0.7)) { pop = true }

// Outbound send — slide up + fade from composer
.transition(.move(edge: .bottom).combined(with: .opacity))
withAnimation(.easeOut(duration: 0.20)) { messages.append(newMessage) }

// New inbound — fade + 6pt slide-up
.transition(.opacity.combined(with: .offset(y: 6)))
withAnimation(.easeOut(duration: 0.18)) { /* insert */ }

// Theme change — cross-fade avatar gradients & sender colors
withAnimation(.easeInOut(duration: 0.25)) { groupAccent = newAccent }

// Attachment sheet
.presentationDetents([.medium, .large])   // slides up 300ms ease-out

// Haptics
let soft = UIImpactFeedbackGenerator(style: .soft)   // on like
let light = UIImpactFeedbackGenerator(style: .light) // on send
let sel = UISelectionFeedbackGenerator()             // on tab change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Chats (tab) | `bubble.left.and.bubble.right.fill` | 22pt |
| People (tab) | `person.2.fill` | 22pt |
| Discover (tab) | `magnifyingglass` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Back | `chevron.left` | 22pt |
| Group info | `info.circle` | 21pt |
| Like | `heart.fill` / `heart` | 9–22pt |
| Attach | `plus.circle` | 26pt |
| Send | `paperplane.fill` | 16pt |
| Camera (attach sheet) | `camera.fill` | 22pt |
| Photo library | `photo.on.rectangle` | 22pt |
| GIF | `face.smiling` | 22pt |
| Poll | `chart.bar.fill` | 22pt |
| Event / Calendar | `calendar` | 22pt |
| Location | `mappin.and.ellipse` | 22pt |
| Mute | `bell.slash.fill` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Gallery tab (per group) | `square.grid.2x2.fill` | 18pt |

## 7. Dark Mode

```swift
struct GMTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.gmDarkCanvas : Color.gmCanvas)
            .foregroundStyle(scheme == .dark ? Color.gmDarkTextPrimary : Color.gmTextPrimary)
    }
}
extension View { func gmTheme() -> some View { modifier(GMTheme()) } }
```

GroupMe's dark mode keeps the chat nav bar full `#00AFF0` (it reads well on both themes) and outbound bubbles brand blue. Canvas is `#121212` (warm, not pure black); inbound bubbles lift to `#2A2A2C`. The like pill switches to a `#1C1C1E` fill with a `#2C2C2E` border so it separates from the dark canvas. Shadows are nearly invisible on dark, so floating sheets (attachment, GIF picker) gain a 1pt `#2C2C2E` border as the elevation cue.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`RectangleCornerRadii` in `RoundedRectangle`/`UnevenRoundedRectangle` requires iOS 16; use a manual `Path` fallback for iOS 15)
- No brand font to bundle — system SF Pro is used; Dynamic Type works out of the box for message body, list titles, previews
- Keep tab labels, like-count numbers, and unread pill numbers at fixed sizes (layout-sensitive)
- VoiceOver: label inbound bubbles "{sender} said: {text}"; label the like pill "Liked by {count}, double-tap to see who"; expose double-tap-to-like as an `.accessibilityAction(named: "Like")`
- Color contrast: white on `#00AFF0` passes WCAG AA for the 16pt+ nav title and bubble text; `#6A6A6A` on `#FFFFFF` passes AA for metadata
- The heart-like is the primary reaction — make it reachable via the rotor as a custom action, not only via double-tap gesture
- Reduce Motion: replace the like-pill spring pop and bubble slide-in with a crossfade
- Dark mode: invert per the `gmDark*` palette; never pure black — `#121212` keeps GroupMe warm and friendly
- Respect per-group theme accents on avatars/sender-names but keep the blue chrome constant regardless of theme or color scheme
