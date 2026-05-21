# KakaoTalk (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates KakaoTalk's visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, the message bubble with its iconic side-docked unread mark, the friends roster, the gifticon card, and the bottom tab bar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let ktYellow        = Color(red: 0.996, green: 0.898, blue: 0.000) // #FEE500
    static let ktYellowPressed = Color(red: 0.902, green: 0.812, blue: 0.000) // #E6CF00
    static let ktBrown         = Color(red: 0.235, green: 0.118, blue: 0.118) // #3C1E1E
    static let ktBrownSoft     = Color(red: 0.353, green: 0.227, blue: 0.227) // #5A3A3A

    // MARK: - Canvas & Surfaces (Light)
    static let ktCanvas        = Color.white                                   // #FFFFFF
    static let ktChatBgLight   = Color(red: 0.698, green: 0.780, blue: 0.855) // #B2C7DA
    static let ktSurfaceGray   = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let ktInboundLight  = Color.white                                   // #FFFFFF
    static let ktDividerLight  = Color(red: 0.902, green: 0.902, blue: 0.902) // #E6E6E6

    // MARK: - Canvas & Surfaces (Dark)
    static let ktDarkCanvas   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let ktChatBgDark   = Color(red: 0.118, green: 0.165, blue: 0.200) // #1E2A33
    static let ktDarkSurface1 = Color(red: 0.141, green: 0.141, blue: 0.141) // #242424
    static let ktInboundDark  = Color(red: 0.180, green: 0.180, blue: 0.180) // #2E2E2E
    static let ktDarkDivider  = Color(red: 0.200, green: 0.200, blue: 0.200) // #333333

    // MARK: - Text
    static let ktTextPrimary    = Color(red: 0.098, green: 0.098, blue: 0.098) // #191919
    static let ktTextSecondary  = Color(red: 0.416, green: 0.416, blue: 0.416) // #6A6A6A
    static let ktTextTertiary   = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let ktDarkTextPrimary   = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED
    static let ktDarkTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A

    // MARK: - Semantic
    static let ktUnread = Color(red: 0.996, green: 0.898, blue: 0.000) // #FEE500
    static let ktLink   = Color(red: 0.294, green: 0.608, blue: 1.000) // #4B9BFF
    static let ktSuccess = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let ktError   = Color(red: 0.945, green: 0.369, blue: 0.424) // #F15E6C
    static let ktBadge   = Color(red: 1.000, green: 0.231, blue: 0.188) // #FF3B30
}
```

## 2. Typography

KakaoTalk uses the iOS system faces: SF Pro for Latin, Apple SD Gothic Neo for Hangul (the system applies this automatically for Korean text). Big titles go weight `.heavy`/`.black` for the friendly KakaoBig feel. Use `Font.system` so Dynamic Type and the Korean face apply automatically.

```swift
extension Font {
    static let ktLargeTitle  = Font.system(size: 32, weight: .black)    // "친구"
    static let ktScreen      = Font.system(size: 26, weight: .bold)
    static let ktNavTitle    = Font.system(size: 20, weight: .black)    // "Friends"
    static let ktProfileName = Font.system(size: 17, weight: .bold)
    static let ktSection     = Font.system(size: 18, weight: .bold)
    static let ktBody        = Font.system(size: 15, weight: .regular)  // message text
    static let ktRowTitle    = Font.system(size: 15, weight: .semibold)
    static let ktStatus      = Font.system(size: 13, weight: .regular)
    static let ktSender      = Font.system(size: 12, weight: .medium)
    static let ktUnreadMark  = Font.system(size: 11, weight: .bold)
    static let ktBubbleTime  = Font.system(size: 10, weight: .regular)
    static let ktTab         = Font.system(size: 10, weight: .medium)
    static let ktTabBadge    = Font.system(size: 10, weight: .bold)
    static let ktButton      = Font.system(size: 16, weight: .bold)
}
```

## 3. Signature Components

### Rounded-Square Avatar (used everywhere)

```swift
struct KTAvatar: View {
    let gradient: [Color]
    let initials: String
    var size: CGFloat = 44

    var body: some View {
        RoundedRectangle(cornerRadius: size * 0.36, style: .continuous)
            .fill(LinearGradient(colors: gradient, startPoint: .topLeading, endPoint: .bottomTrailing))
            .frame(width: size, height: size)
            .overlay(
                Text(initials)
                    .font(.system(size: size * 0.36, weight: .bold))
                    .foregroundStyle(.white)
            )
    }
}
```

### Message Bubble + iconic side-docked unread mark

```swift
struct KTMessageBubble: View {
    let text: String
    let isOutbound: Bool
    var time: String = ""
    var unread: Int = 0          // recipients who haven't read (outbound only)
    @Environment(\.colorScheme) private var scheme

    private var bubbleColor: Color {
        isOutbound ? .ktYellow : (scheme == .dark ? .ktInboundDark : .ktInboundLight)
    }
    private var textColor: Color {
        isOutbound ? .ktBrown : (scheme == .dark ? .ktDarkTextPrimary : .ktTextPrimary)
    }
    private var metaColor: Color {
        scheme == .dark ? .white.opacity(0.5) : Color.ktTextSecondary
    }

    var body: some View {
        HStack(alignment: .bottom, spacing: 5) {
            if isOutbound { meta }      // meta on the INNER (leading) side of an outbound bubble
            Text(text)
                .font(.ktBody)
                .fontWeight(isOutbound ? .medium : .regular)
                .foregroundStyle(textColor)
                .padding(.vertical, 9)
                .padding(.horizontal, 12)
                .background(KTBubbleShape(isOutbound: isOutbound).fill(bubbleColor))
            if !isOutbound { metaInbound }
        }
        .frame(maxWidth: 270, alignment: isOutbound ? .trailing : .leading)
    }

    private var meta: some View {
        VStack(alignment: .trailing, spacing: 1) {
            if unread > 0 {
                Text("\(unread)").font(.ktUnreadMark).foregroundStyle(Color.ktUnread)
            }
            if !time.isEmpty { Text(time).font(.ktBubbleTime).foregroundStyle(metaColor) }
        }
    }
    private var metaInbound: some View {
        VStack(alignment: .leading, spacing: 1) {
            if !time.isEmpty { Text(time).font(.ktBubbleTime).foregroundStyle(metaColor) }
        }
    }
}

struct KTBubbleShape: Shape {
    let isOutbound: Bool
    func path(in rect: CGRect) -> Path {
        let r: CGFloat = 14, head: CGFloat = 4
        return Path(roundedRect: rect,
                    cornerRadii: RectangleCornerRadii(
                        topLeading: isOutbound ? r : head,
                        bottomLeading: r,
                        bottomTrailing: r,
                        topTrailing: isOutbound ? head : r))
    }
}
```

### Inbound Sender Row (rounded-square avatar + name above a run)

```swift
struct KTInboundRow: View {
    let senderName: String
    let avatarGradient: [Color]
    let initials: String
    let bubbles: [(String, String)]   // (text, time)
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(alignment: .top, spacing: 8) {
            KTAvatar(gradient: avatarGradient, initials: initials, size: 36)
            VStack(alignment: .leading, spacing: 3) {
                Text(senderName)
                    .font(.ktSender)
                    .foregroundStyle(scheme == .dark ? Color.white.opacity(0.78) : Color.ktTextSecondary)
                    .padding(.leading, 2)
                ForEach(Array(bubbles.enumerated()), id: \.offset) { _, b in
                    KTMessageBubble(text: b.0, isOutbound: false, time: b.1)
                }
            }
            Spacer(minLength: 36)
        }
    }
}
```

### Friend Row (with optional now-playing badge)

```swift
struct KTFriendRow: View {
    let name: String
    let status: String
    let nowPlaying: String?      // track title if listening
    let avatarGradient: [Color]
    let initials: String

    var body: some View {
        HStack(spacing: 12) {
            KTAvatar(gradient: avatarGradient, initials: initials, size: 44)
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.ktRowTitle)
                if let track = nowPlaying {
                    HStack(spacing: 4) {
                        Image(systemName: "music.note")
                            .font(.system(size: 11))
                            .foregroundStyle(Color.ktTextTertiary)
                        Text(track).font(.ktStatus).foregroundStyle(Color.ktTextTertiary).lineLimit(1)
                    }
                } else {
                    Text(status).font(.ktStatus).foregroundStyle(Color.ktTextSecondary).lineLimit(1)
                }
            }
            Spacer()
        }
        .padding(.horizontal, 18)
        .frame(height: 64)
    }
}
```

### Gifticon Card

```swift
struct KTGifticonCard: View {
    let brand: String
    let product: String
    let isRecipient: Bool
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        VStack(spacing: 0) {
            ZStack {
                LinearGradient(colors: [Color(red: 1, green: 0.88, blue: 0.54),
                                        Color(red: 0.996, green: 0.78, blue: 0.29)],
                               startPoint: .topLeading, endPoint: .bottomTrailing)
                Image(systemName: "gift.fill").font(.system(size: 42)).foregroundStyle(Color.ktBrown)
            }
            .frame(height: 120)

            VStack(alignment: .leading, spacing: 6) {
                Text(brand).font(.system(size: 11)).foregroundStyle(Color.ktTextTertiary)
                Text(product).font(.system(size: 13, weight: .bold))
                    .foregroundStyle(scheme == .dark ? Color.ktDarkTextPrimary : Color.ktTextPrimary)
                if isRecipient {
                    Text("사용하기")
                        .font(.system(size: 13, weight: .semibold))
                        .foregroundStyle(Color.ktBrown)
                        .padding(.vertical, 8).frame(maxWidth: .infinity)
                        .background(Capsule().fill(Color.ktYellow))
                        .padding(.top, 4)
                }
            }
            .padding(12)
        }
        .frame(width: 188)
        .background(RoundedRectangle(cornerRadius: 16)
            .fill(scheme == .dark ? Color.ktDarkSurface1 : Color.ktCanvas))
        .shadow(color: .black.opacity(0.06), radius: 3, y: 1)
    }
}
```

### Composer

```swift
struct KTComposer: View {
    @Binding var text: String
    let onMenu: () -> Void
    let onEmoticon: () -> Void
    let onSend: () -> Void
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(spacing: 10) {
            Button(action: onMenu) {
                Image(systemName: "plus").font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(Color.ktTextSecondary)
            }
            HStack {
                TextField("", text: $text, axis: .vertical).font(.ktBody)
                if !text.isEmpty {
                    Button(action: onSend) {
                        Image(systemName: "arrow.up.circle.fill")
                            .font(.system(size: 24)).foregroundStyle(Color.ktYellow)
                    }
                }
            }
            .padding(.horizontal, 16)
            .frame(minHeight: 38)
            .background(Capsule().fill(scheme == .dark ? Color.ktInboundDark : Color.ktSurfaceGray))

            Button(action: onEmoticon) {
                Image(systemName: "face.smiling").font(.system(size: 22))
                    .foregroundStyle(Color.ktTextSecondary)
            }
        }
        .padding(.horizontal, 14)
        .padding(.top, 10)
        .padding(.bottom, 8)
        .background((scheme == .dark ? Color.ktDarkCanvas : Color.ktCanvas)
            .overlay(Rectangle().frame(height: 0.5)
                .foregroundStyle(scheme == .dark ? Color.ktDarkDivider : Color.ktDividerLight), alignment: .top))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct KTTabView: View {
    var body: some View {
        TabView {
            FriendsView().tabItem { Label("Friends", systemImage: "person.2.fill") }
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.fill") }
                .badge(5)
            OpenChatView().tabItem { Label("Open", systemImage: "globe") }
            ShoppingView().tabItem { Label("Shopping", systemImage: "bag.fill") }
            MoreView().tabItem { Label("More", systemImage: "line.3.horizontal") }
        }
        .tint(Color.ktTextPrimary)   // active = primary text color, NOT yellow
    }
}
```

## 5. Motion

```swift
// Message send — pop up + fade from composer
.transition(.move(edge: .bottom).combined(with: .opacity))
withAnimation(.easeOut(duration: 0.20)) { messages.append(newMessage) }

// Unread countdown — cross-fade the number
withAnimation(.easeInOut(duration: 0.2)) { unread -= 1 }

// New inbound — fade + 6pt slide-up
.transition(.opacity.combined(with: .offset(y: 6)))
withAnimation(.easeOut(duration: 0.18)) { /* insert */ }

// Emoticon send — scale 0.7 → 1.0 spring
withAnimation(.spring(response: 0.22, dampingFraction: 0.7)) { stickerScale = 1.0 }

// Action sheet / emoticon keyboard — slide up
.presentationDetents([.medium, .large])   // ~280ms ease-out

// Haptics
let light = UIImpactFeedbackGenerator(style: .light) // on send
let soft  = UIImpactFeedbackGenerator(style: .soft)  // on emoticon send
let sel   = UISelectionFeedbackGenerator()           // on tab change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Friends (tab) | `person.2.fill` | 24pt |
| Chats (tab) | `bubble.left.fill` | 24pt |
| Open Chat (tab) | `globe` | 24pt |
| Shopping (tab) | `bag.fill` | 24pt |
| More (tab) | `line.3.horizontal` | 24pt |
| Back | `chevron.left` | 22pt |
| Search | `magnifyingglass` | 21pt |
| Add friend | `person.badge.plus` | 21pt |
| Settings / menu | `ellipsis` / `line.3.horizontal` | 21pt |
| Composer menu (+) | `plus` | 22pt |
| Emoticon keyboard | `face.smiling` | 22pt |
| Send | `arrow.up.circle.fill` | 24pt |
| Now-playing badge | `music.note` | 11pt |
| Gifticon | `gift.fill` | 42pt |
| Album / photo | `photo.on.rectangle` | 22pt |
| Camera | `camera.fill` | 22pt |
| Pay / 송금 | `wonsign.circle.fill` | 22pt |
| Location | `mappin.and.ellipse` | 22pt |
| Schedule | `calendar` | 22pt |
| Voice | `mic.fill` | 22pt |

## 7. Dark Mode

```swift
struct KTTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    let isChatThread: Bool
    func body(content: Content) -> some View {
        content
            .background(
                isChatThread
                ? (scheme == .dark ? Color.ktChatBgDark : Color.ktChatBgLight)
                : (scheme == .dark ? Color.ktDarkCanvas : Color.ktCanvas)
            )
            .foregroundStyle(scheme == .dark ? Color.ktDarkTextPrimary : Color.ktTextPrimary)
    }
}
extension View { func ktTheme(chatThread: Bool = false) -> some View { modifier(KTTheme(isChatThread: chatThread)) } }
```

KakaoTalk's dark mode keeps outbound bubbles Kakao Yellow `#FEE500` with Kakao Brown `#3C1E1E` text (the contrast reads on both themes). The chat thread backdrop shifts to `#1E2A33` (the dark equivalent of the signature blue-gray); inbound bubbles sit at `#2E2E2E`. The friends/list canvas is `#1A1A1A` (warm, not pure black). The yellow unread mark and timestamp dock to the bubble's inner side. Shadows nearly vanish on dark, so the gifticon card switches to a `#242424` fill and floating panels gain a 1pt `#333333` border as the elevation cue.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`RectangleCornerRadii` requires iOS 16; use a manual `Path` for iOS 15)
- No brand font to bundle — system SF Pro (Latin) + Apple SD Gothic Neo (Hangul) are used; Dynamic Type works out of the box for message body, list names, status lines
- Keep tab labels, unread marks, bubble times, and tab badges at fixed sizes (layout-sensitive in run grouping)
- Korean text: do not constrain line height below ~1.35 — `Font.system` already sets appropriate Hangul metrics; avoid forcing tight `lineSpacing`
- VoiceOver: label outbound bubbles "You said: {text}, read by all" or "{n} unread"; label the unread mark "{n} people have not read this"; label inbound "{sender} said: {text}"; label gifticon "{brand} {product} gift, double-tap to use"
- Color contrast: `#3C1E1E` on `#FEE500` passes WCAG AA comfortably; `#6A6A6A` on `#FFFFFF` passes AA for metadata; verify any text placed on the blue-gray thread backdrop
- The unread mark and emoticon picker are core — expose the emoticon keyboard and the `+` action sheet via accessible buttons with clear labels
- Reduce Motion: replace the emoticon scale-pop and bubble slide-in with a crossfade; keep the unread-count cross-fade (it conveys state)
- Dark mode: invert per the `ktDark*` palette; never pure black — `#1A1A1A` canvas + `#1E2A33` thread keep KakaoTalk warm; outbound bubbles stay yellow regardless of scheme
- Active tab uses the primary text color (filled icon), never a yellow tint — keep `.tint(Color.ktTextPrimary)`
