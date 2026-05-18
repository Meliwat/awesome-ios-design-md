# Viber (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Viber's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand Purple
    static let viberPurple        = Color(red: 0.451, green: 0.376, blue: 0.949) // #7360F2
    static let viberPurpleDeep    = Color(red: 0.400, green: 0.361, blue: 0.675) // #665CAC
    static let viberPurplePressed = Color(red: 0.357, green: 0.290, blue: 0.800) // #5B4ACC
    static let viberViolet        = Color(red: 0.561, green: 0.490, blue: 0.969) // #8F7DF7
    static let viberBannerDeep    = Color(red: 0.349, green: 0.149, blue: 0.486) // #59267C

    // MARK: - Canvas & Surfaces (Light)
    static let viberCanvas      = Color.white                                  // #FFFFFF
    static let viberIncoming    = Color(red: 0.929, green: 0.922, blue: 0.961) // #EDEBF5
    static let viberSurfaceGray = Color(red: 0.957, green: 0.953, blue: 0.973) // #F4F3F8
    static let viberRowPressed  = Color(red: 0.914, green: 0.906, blue: 0.949) // #E9E7F2
    static let viberDivider     = Color(red: 0.894, green: 0.886, blue: 0.925) // #E4E2EC

    // MARK: - Canvas & Surfaces (Dark) — aubergine-tinted, NOT neutral
    static let viberDarkCanvas   = Color(red: 0.071, green: 0.067, blue: 0.094) // #121118
    static let viberDarkSurface1 = Color(red: 0.110, green: 0.102, blue: 0.141) // #1C1A24
    static let viberDarkSurface2 = Color(red: 0.149, green: 0.137, blue: 0.184) // #26232F
    static let viberDarkDivider  = Color(red: 0.196, green: 0.184, blue: 0.235) // #322F3C

    // MARK: - Text
    static let viberTextPrimary  = Color(red: 0.102, green: 0.094, blue: 0.145) // #1A1825
    static let viberOnPurple     = Color.white
    static let viberTextSecondary = Color(red: 0.490, green: 0.478, blue: 0.549) // #7D7A8C
    static let viberTextTertiary = Color(red: 0.659, green: 0.647, blue: 0.710) // #A8A5B5
    static let viberTextPrimaryDark = Color(red: 0.953, green: 0.945, blue: 0.973) // #F3F1F8
    static let viberTextSecondaryDark = Color(red: 0.616, green: 0.596, blue: 0.675) // #9D98AC

    // MARK: - Semantic
    static let viberGreen = Color(red: 0.275, green: 0.761, blue: 0.416)  // #46C26A
    static let viberRed   = Color(red: 0.941, green: 0.314, blue: 0.431)  // #F0506E
    static let viberLink  = Color(red: 0.451, green: 0.376, blue: 0.949)  // #7360F2

    static var viberCanvasAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(red: 0.071, green: 0.067, blue: 0.094, alpha: 1)
            : .white })
    }
    static var viberIncomingAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(red: 0.149, green: 0.137, blue: 0.184, alpha: 1)
            : UIColor(red: 0.929, green: 0.922, blue: 0.961, alpha: 1) })
    }
}
```

## 2. Typography

Viber's brand face is proprietary; bundle **Manrope** (SIL OFL) as the closest free substitute via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let viberLargeTitle = Font.custom("Manrope-ExtraBold", size: 32) // 800
    static let viberTitle1     = Font.custom("Manrope-ExtraBold", size: 26)
    static let viberTitle3     = Font.custom("Manrope-Bold",      size: 22)
    static let viberHeadline   = Font.custom("Manrope-Bold",      size: 17)
    static let viberBody       = Font.custom("Manrope-Regular",   size: 15)
    static let viberBodyEmph   = Font.custom("Manrope-SemiBold",  size: 15)
    static let viberPreview    = Font.custom("Manrope-Medium",    size: 14)
    static let viberFootnote   = Font.custom("Manrope-SemiBold",  size: 13)
    static let viberCaption    = Font.custom("Manrope-SemiBold",  size: 11)
    static let viberButton     = Font.custom("Manrope-Bold",      size: 16)
    static let viberTab        = Font.custom("Manrope-SemiBold",  size: 11)
    static let viberBadge      = Font.custom("Manrope-Bold",      size: 11)
}
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```swift
struct ViberBubble: View {
    let text: String
    let outgoing: Bool
    let tailEnd: Bool
    let timestamp: String
    let receipt: ReceiptState

    var body: some View {
        VStack(alignment: outgoing ? .trailing : .leading, spacing: 3) {
            Text(text)
                .font(.viberBody)
                .foregroundStyle(outgoing ? Color.viberOnPurple : Color.viberTextPrimary)
                .padding(.vertical, 9)
                .padding(.horizontal, 14)
                .background(
                    (outgoing ? Color.viberPurple : Color.viberIncomingAdaptive),
                    in: ViberBubbleShape(outgoing: outgoing, tail: tailEnd)
                )
            if outgoing {
                HStack(spacing: 4) {
                    Text(timestamp).font(.system(size: 10)).foregroundStyle(.secondary)
                    ReceiptCheck(state: receipt)
                }
            }
        }
        .frame(maxWidth: 280, alignment: outgoing ? .trailing : .leading)
        .frame(maxWidth: .infinity, alignment: outgoing ? .trailing : .leading)
    }
}

struct ViberBubbleShape: Shape {
    let outgoing: Bool
    let tail: Bool
    func path(in r: CGRect) -> Path {
        let big: CGFloat = 16, small: CGFloat = 5
        let bl = (!outgoing && tail) ? small : big
        let br = ( outgoing && tail) ? small : big
        return Path { p in
            p.addRoundedRect(in: r, cornerSize: .init(width: big, height: big))
            // Approximate per-corner: use UnevenRoundedRectangle on iOS 16.4+
        }
    }
}

// Prefer (iOS 16.4+):
// .clipShape(.rect(topLeadingRadius: 16, bottomLeadingRadius: outgoing ? 16 : (tail ? 5 : 16),
//                   bottomTrailingRadius: outgoing ? (tail ? 5 : 16) : 16, topTrailingRadius: 16))
```

### Three-State Check Receipt

```swift
enum ReceiptState { case sending, sent, delivered, seen, failed }

struct ReceiptCheck: View {
    let state: ReceiptState
    @Environment(\.colorScheme) var scheme

    private var color: Color {
        switch state {
        case .sending, .sent: return scheme == .dark ? Color(red: 0.42, green: 0.40, blue: 0.47) : .viberTextTertiary
        case .delivered:      return scheme == .dark ? .viberTextSecondaryDark : .viberTextSecondary
        case .seen:           return .viberViolet
        case .failed:         return .viberRed
        }
    }

    var body: some View {
        Group {
            switch state {
            case .sending: Image(systemName: "clock")
            case .sent:    Image(systemName: "checkmark")
            case .delivered, .seen: Image(systemName: "checkmark").overlay(
                Image(systemName: "checkmark").offset(x: 4))
            case .failed:  Image(systemName: "exclamationmark.circle.fill")
            }
        }
        .font(.system(size: 11, weight: .bold))
        .foregroundStyle(color)
        .animation(.easeInOut(duration: 0.2), value: state)
    }
}
```

### Free Viber Call Banner

```swift
struct FreeCallBanner: View {
    let name: String
    let onCall: () -> Void
    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "phone.fill").font(.system(size: 18)).foregroundStyle(.white)
            VStack(alignment: .leading, spacing: 1) {
                Text("Free Viber Call").font(.system(size: 13, weight: .bold)).foregroundStyle(.white)
                Text("Call \(name) free over Viber-to-Viber")
                    .font(.system(size: 11)).foregroundStyle(.white.opacity(0.8))
            }
            Spacer()
            Button(action: onCall) {
                Text("Call").font(.system(size: 12, weight: .bold)).foregroundStyle(.white)
                    .padding(.horizontal, 12).padding(.vertical, 6)
                    .background(Capsule().fill(.white.opacity(0.22)))
            }
        }
        .padding(12)
        .background(
            LinearGradient(colors: [.viberPurple, .viberBannerDeep],
                           startPoint: .topLeading, endPoint: .bottomTrailing),
            in: RoundedRectangle(cornerRadius: 14)
        )
        .shadow(color: Color.viberPurple.opacity(0.10), radius: 8, y: 2)
    }
}
```

### In-Thread Sticker

```swift
struct StickerMessage: View {
    let outgoing: Bool
    let timestamp: String
    let receipt: ReceiptState
    var body: some View {
        VStack(alignment: outgoing ? .trailing : .leading, spacing: 3) {
            // Replace with Lottie/APNG animated sticker; placeholder shape here
            RoundedRectangle(cornerRadius: 20)
                .fill(RadialGradient(colors: [.viberViolet, .viberBannerDeep], center: .top, startRadius: 4, endRadius: 96))
                .frame(width: 96, height: 96)
            if outgoing {
                HStack(spacing: 4) {
                    Text(timestamp).font(.system(size: 10)).foregroundStyle(.secondary)
                    ReceiptCheck(state: receipt)
                }
            }
        }
        .frame(maxWidth: .infinity, alignment: outgoing ? .trailing : .leading)
    }
}
```

### Compose Bar

```swift
struct ViberComposeBar: View {
    @Binding var text: String
    let onSend: () -> Void
    var body: some View {
        HStack(spacing: 10) {
            Button {} label: { Image(systemName: "plus").font(.system(size: 22)).foregroundStyle(.secondary) }
            HStack {
                TextField("Type a message", text: $text, axis: .vertical)
                    .font(.viberBody).lineLimit(1...5)
                Button {} label: { Image(systemName: "face.smiling").font(.system(size: 22)).foregroundStyle(.secondary) }
            }
            .padding(.horizontal, 14).frame(minHeight: 36)
            .background(Color.viberSurfaceGray, in: RoundedRectangle(cornerRadius: 18))
            Button(action: onSend) {
                Image(systemName: text.isEmpty ? "mic.fill" : "paperplane.fill")
                    .font(.system(size: 16)).foregroundStyle(.white)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(Color.viberPurple))
            }
        }
        .padding(.horizontal, 14).padding(.vertical, 8)
        .background(Color.viberDarkSurface1.opacity(0)) // use surface per scheme
    }
}
```

### Chat Header (call-first)

```swift
struct ViberChatHeader: View {
    let name: String
    let initials: String
    let online: Bool
    var body: some View {
        HStack(spacing: 10) {
            Button {} label: { Image(systemName: "chevron.left").font(.system(size: 17, weight: .semibold)).foregroundStyle(Color.viberPurple) }
            Circle()
                .fill(LinearGradient(colors: [.viberViolet, .viberPurpleDeep], startPoint: .top, endPoint: .bottom))
                .frame(width: 36, height: 36)
                .overlay(Text(initials).font(.system(size: 14, weight: .bold)).foregroundStyle(.white))
            VStack(alignment: .leading, spacing: 1) {
                Text(name).font(.viberHeadline.weight(.bold))
                if online { Text("online").font(.viberCaption).foregroundStyle(Color.viberGreen) }
            }
            Spacer()
            Button {} label: { Image(systemName: "phone").font(.system(size: 22)).foregroundStyle(Color.viberPurple) }
            Button {} label: { Image(systemName: "video").font(.system(size: 22)).foregroundStyle(Color.viberPurple) }
        }
        .padding(.horizontal, 14).padding(.vertical, 8)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ViberTabView: View {
    var body: some View {
        TabView {
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.fill") }
            CallsView().tabItem { Label("Calls", systemImage: "phone.fill") }
            ExploreView().tabItem { Label("Explore", systemImage: "smiley") }.badge(3)
            MoreView().tabItem { Label("More", systemImage: "person.crop.circle") }
        }
        .tint(.viberPurple) // active = brand purple; standard UITabBar (no custom pill)
    }
}
```

## 5. Motion

```swift
// Outgoing bubble — slide up + scale from input field
withAnimation(.spring(response: 0.32, dampingFraction: 0.78)) { messages.append(m) }
// .transition(.scale(scale: 0.85).combined(with: .move(edge: .bottom)))

// Receipt sent → delivered → seen
.animation(.easeInOut(duration: 0.2), value: receipt)   // recolor + crossfade
// On .seen, add a subtle pulse: scaleEffect(seenPulse ? 1.15 : 1) then back

// Reactions strip (long-press ~0.45s)
.onLongPressGesture(minimumDuration: 0.45) {
    withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) { showReactions = true }
}

// Sticker market open
.transition(.move(edge: .bottom))   // 0.3s ease-out

// Incoming call screen
withAnimation(.easeOut(duration: 0.3)) { showCall = true }   // gradient fade + buttons spring

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: sentCount)   // send
.sensoryFeedback(.impact(flexibility: .soft), trigger: reaction) // reaction dock
.sensoryFeedback(.success, trigger: callConnected)               // call connect
.sensoryFeedback(.warning, trigger: callDeclined)                // decline
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Back | `chevron.left` | 17pt |
| Voice call | `phone` / `phone.fill` | 22pt |
| Video call | `video` / `video.fill` | 22pt |
| Attachment | `plus` | 22pt |
| Emoji / sticker | `face.smiling` | 22pt |
| Send | `paperplane.fill` | 16pt |
| Voice message | `mic.fill` | 16pt |
| Sent | `checkmark` | 11pt |
| Delivered / Seen | `checkmark` ×2 | 11pt |
| Sending | `clock` | 11pt |
| Failed | `exclamationmark.circle.fill` | 11pt |
| Chats (tab) | `bubble.left` / `.fill` | 22pt |
| Calls (tab) | `phone` / `.fill` | 22pt |
| Explore (tab) | `smiley` / `.fill` | 22pt |
| More (tab) | `person.crop.circle` | 22pt |
| Search | `magnifyingglass` | 15pt |
| Reaction (default) | `hand.thumbsup.fill` | 28pt |
| Secret message | `timer` | 14pt |

## 7. Dark Mode

```swift
struct ViberTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.viberDarkCanvas : Color.viberCanvas)
            .tint(.viberPurple)
    }
}
extension View { func viberTheme() -> some View { modifier(ViberTheme()) } }
```

- Dark canvas is **aubergine-tinted** `#121118` — NOT neutral gray; the purple cast is intentional
- Outgoing bubble stays `#7360F2`; incoming inverts `#EDEBF5` → `#26232F`; text → `#F3F1F8`
- Seen receipt stays violet `#8F7DF7` in both modes; online dot stays green `#46C26A`
- Floating menus/reactions lose visible shadow on dark — add a 1pt `viberDarkDivider` border as the elevation cue
- The free-call banner keeps its `#7360F2 → #59267C` gradient unchanged

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`UnevenRoundedRectangle` needs iOS 16.4; `.sensoryFeedback`/`TextField(axis:)` need iOS 17 — fall back to a custom `Shape` and `UIImpactFeedbackGenerator` on earlier)
- Bundle Manrope TTFs via `Info.plist` (SIL OFL, free to redistribute) as the Viber-brand substitute
- Dynamic Type: scale titles, headline, body, list previews; pin timestamps, receipt glyphs, tab labels, badges, presence text, sticker artwork
- VoiceOver: label outgoing bubbles "You said {text}, {receipt state}"; announce receipt as the value ("Seen"); label stickers by pack/emoji name; expose Reactions as a custom action
- Color contrast: white on `#7360F2` passes WCAG AA; `label` on `#EDEBF5`/`#26232F` passes AA; the violet seen-check `#8F7DF7` is paired with the double-check shape so meaning survives color-blindness
- Differentiate Without Color: receipt state is conveyed by glyph shape (clock / 1 check / 2 checks) plus color — not color alone
- Reduce Motion: disable bubble spring-in (crossfade), stop sticker auto-loop (show first frame, play on tap), disable banner shimmer
- Reduce Transparency: solid `viberDarkSurface1`/`viberSurfaceGray` bars instead of any blur
- Dark mode: invert per `viberDark*` — aubergine `#121118`, never neutral gray; add a `viberDarkDivider` border to floating surfaces as the elevation cue
