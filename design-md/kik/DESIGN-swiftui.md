# Kik (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Kik's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand Kik Blue (modern) + Green (heritage)
    static let kikBlue        = Color(red: 0.000, green: 0.690, blue: 0.941) // #00B0F0
    static let kikBlueDeep    = Color(red: 0.000, green: 0.576, blue: 0.784) // #0093C8
    static let kikBluePressed = Color(red: 0.000, green: 0.525, blue: 0.722) // #0086B8
    static let kikCyan        = Color(red: 0.310, green: 0.796, blue: 0.969) // #4FCBF7
    static let kikGreenLegacy = Color(red: 0.510, green: 0.737, blue: 0.137) // #82BC23 (heritage only)
    static let kikInkOnBlue   = Color(red: 0.000, green: 0.165, blue: 0.212) // #002A36

    // MARK: - Canvas & Surfaces (Light)
    static let kikCanvas      = Color.white                                  // #FFFFFF
    static let kikIncoming    = Color(red: 0.914, green: 0.918, blue: 0.925) // #E9EAEC
    static let kikSurfaceGray = Color(red: 0.953, green: 0.957, blue: 0.965) // #F3F4F6
    static let kikRowPressed  = Color(red: 0.906, green: 0.910, blue: 0.922) // #E7E8EB
    static let kikDivider     = Color(red: 0.882, green: 0.886, blue: 0.898) // #E1E2E5

    // MARK: - Canvas & Surfaces (Dark) — neutral near-black
    static let kikDarkCanvas   = Color(red: 0.071, green: 0.075, blue: 0.086) // #121316
    static let kikDarkSurface1 = Color(red: 0.106, green: 0.114, blue: 0.129) // #1B1D21
    static let kikDarkSurface2 = Color(red: 0.145, green: 0.157, blue: 0.176) // #25282D
    static let kikDarkDivider  = Color(red: 0.192, green: 0.204, blue: 0.227) // #31343A

    // MARK: - Text
    static let kikTextPrimary  = Color(red: 0.086, green: 0.094, blue: 0.106) // #16181B
    static let kikTextSecondary = Color(red: 0.424, green: 0.435, blue: 0.467) // #6C6F77
    static let kikTextTertiary = Color(red: 0.604, green: 0.616, blue: 0.647) // #9A9DA5
    static let kikTextPrimaryDark = Color(red: 0.945, green: 0.949, blue: 0.957) // #F1F2F4
    static let kikTextSecondaryDark = Color(red: 0.612, green: 0.627, blue: 0.659) // #9CA0A8

    // MARK: - Semantic
    static let kikError = Color(red: 0.941, green: 0.278, blue: 0.243) // #F0473E
    static let kikLink  = Color(red: 0.000, green: 0.576, blue: 0.784) // #0093C8

    static var kikCanvasAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(red: 0.071, green: 0.075, blue: 0.086, alpha: 1) : .white })
    }
    static var kikIncomingAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(red: 0.145, green: 0.157, blue: 0.176, alpha: 1)
            : UIColor(red: 0.914, green: 0.918, blue: 0.925, alpha: 1) })
    }
}
```

## 2. Typography

Kik's brand face is proprietary; bundle **Inter** (SIL OFL) as the closest free substitute via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let kikLargeTitle = Font.custom("Inter-ExtraBold", size: 32)
    static let kikScreenTitle = Font.custom("Inter-ExtraBold", size: 26)
    static let kikTitle3     = Font.custom("Inter-Bold",      size: 22)
    static let kikHeadline   = Font.custom("Inter-Bold",      size: 17)
    static let kikRowTitle   = Font.custom("Inter-Bold",      size: 15)
    static let kikBody       = Font.custom("Inter-Regular",   size: 14)
    static let kikBodyOut    = Font.custom("Inter-Medium",    size: 14) // outgoing — crisper on Blue
    static let kikBodyEmph   = Font.custom("Inter-SemiBold",  size: 14)
    static let kikPreview    = Font.custom("Inter-Medium",    size: 13)
    static let kikFootnote   = Font.custom("Inter-SemiBold",  size: 13)
    static let kikCaption    = Font.custom("Inter-SemiBold",  size: 11)
    static let kikButton     = Font.custom("Inter-Bold",      size: 16)
    static let kikReceipt    = Font.custom("Inter-Bold",      size: 11)
    static let kikTab        = Font.custom("Inter-SemiBold",  size: 11)
}
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```swift
struct KikBubble: View {
    let text: String
    let outgoing: Bool
    let tailEnd: Bool
    let timestamp: String
    let receipt: SDR

    var body: some View {
        VStack(alignment: outgoing ? .trailing : .leading, spacing: 3) {
            Text(text)
                .font(outgoing ? .kikBodyOut : .kikBody)
                .foregroundStyle(outgoing ? Color.kikInkOnBlue : Color.kikTextPrimary)
                .padding(.vertical, 8).padding(.horizontal, 13)
                .background(outgoing ? Color.kikBlue : Color.kikIncomingAdaptive)
                .clipShape(.rect(
                    topLeadingRadius: 16,
                    bottomLeadingRadius: outgoing ? 16 : (tailEnd ? 5 : 16),
                    bottomTrailingRadius: outgoing ? (tailEnd ? 5 : 16) : 16,
                    topTrailingRadius: 16))
            if outgoing {
                HStack(spacing: 5) {
                    Text(timestamp).font(.system(size: 10)).foregroundStyle(.tertiary)
                    SDRReceipt(state: receipt)
                }
            }
        }
        .frame(maxWidth: 280, alignment: outgoing ? .trailing : .leading)
        .frame(maxWidth: .infinity, alignment: outgoing ? .trailing : .leading)
    }
}
```

### S / D / R Receipt

```swift
enum SDR { case sending, sent, delivered, read, failed }

struct SDRReceipt: View {
    let state: SDR
    @Environment(\.colorScheme) var scheme

    private var letter: String {
        switch state {
        case .sending:   return "…"
        case .sent:      return "S"
        case .delivered: return "D"
        case .read:      return "R"
        case .failed:    return "!"
        }
    }
    private var color: Color {
        switch state {
        case .sending, .sent: return scheme == .dark ? Color(red: 0.41, green: 0.42, blue: 0.45) : .kikTextTertiary
        case .delivered:      return scheme == .dark ? .kikTextSecondaryDark : .kikTextSecondary
        case .read:           return .kikBlue   // the one color moment
        case .failed:         return .kikError
        }
    }

    var body: some View {
        Text(letter)
            .font(.kikReceipt)
            .tracking(0.2)
            .foregroundStyle(color)
            // No animation — S/D/R swap is instant; only the R recolor reads as an event
    }
}
```

### Conversation Row (person vs bot)

```swift
struct KikConvRow: View {
    let name: String
    let preview: String
    let time: String
    let isBot: Bool
    let unread: Int?
    let receipt: SDR?
    let gradient: [Color]

    var body: some View {
        HStack(spacing: 12) {
            Group {
                if isBot {
                    RoundedRectangle(cornerRadius: 14)
                        .fill(LinearGradient(colors: gradient, startPoint: .top, endPoint: .bottom))
                } else {
                    Circle().fill(LinearGradient(colors: gradient, startPoint: .top, endPoint: .bottom))
                }
            }
            .frame(width: 48, height: 48)
            .overlay(Text(String(name.prefix(1))).font(.system(size: 16, weight: .bold)).foregroundStyle(.white))

            VStack(alignment: .leading, spacing: 2) {
                HStack(spacing: 5) {
                    Text(name).font(.kikRowTitle).foregroundStyle(.primary).lineLimit(1)
                    if isBot {
                        Text("BOT").font(.system(size: 9, weight: .bold))
                            .padding(.horizontal, 5).padding(.vertical, 1)
                            .background(Color(.tertiarySystemFill), in: RoundedRectangle(cornerRadius: 4))
                            .foregroundStyle(.secondary)
                    }
                }
                Text(preview).font(.kikPreview).foregroundStyle(.secondary).lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 5) {
                Text(time).font(.system(size: 11)).foregroundStyle(.tertiary)
                if let unread {
                    Text("\(unread)").font(.system(size: 11, weight: .bold))
                        .foregroundStyle(Color.kikInkOnBlue)
                        .frame(minWidth: 18, minHeight: 18)
                        .background(Capsule().fill(Color.kikBlue))
                } else if let receipt {
                    SDRReceipt(state: receipt)
                }
            }
        }
        .padding(.horizontal, 16).padding(.vertical, 12)
    }
}
```

### Kik Code

```swift
struct KikCode: View {
    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 24).fill(Color.kikBlue)
            Circle()
                .strokeBorder(style: StrokeStyle(lineWidth: 6, dash: [2, 6]))
                .foregroundStyle(.white)
                .frame(width: 96, height: 96)
            RoundedRectangle(cornerRadius: 12).fill(.white).frame(width: 44, height: 44)
        }
        .frame(width: 120, height: 120)
        // Flat, high-contrast — NO shadow (must scan reliably)
    }
}
```

### Compose Bar (text "Send" label)

```swift
struct KikComposeBar: View {
    @Binding var text: String
    let onSend: () -> Void
    var body: some View {
        HStack(spacing: 10) {
            Button {} label: { Image(systemName: "plus").font(.system(size: 22)).foregroundStyle(.secondary) }
            HStack {
                TextField("Type a message", text: $text, axis: .vertical).font(.kikBody).lineLimit(1...5)
            }
            .padding(.horizontal, 14).frame(minHeight: 36)
            .background(Color.kikSurfaceGray, in: RoundedRectangle(cornerRadius: 18))
            Button(action: onSend) {
                Text("Send").font(.kikReceipt.weight(.bold))
                    .foregroundStyle(text.isEmpty ? Color(.tertiaryLabel) : Color.kikBlue)
            }
            .disabled(text.isEmpty)
        }
        .padding(.horizontal, 14).padding(.vertical, 8)
    }
}
```

### Chat Header (no call icons)

```swift
struct KikChatHeader: View {
    let username: String
    var body: some View {
        HStack(spacing: 10) {
            Button {} label: { Image(systemName: "chevron.left").font(.system(size: 17, weight: .semibold)).foregroundStyle(Color.kikBlue) }
            Spacer()
            Text(username).font(.kikHeadline)
            Spacer()
            Circle().fill(LinearGradient(colors: [.kikCyan, .kikBlueDeep], startPoint: .top, endPoint: .bottom))
                .frame(width: 32, height: 32)  // tap → profile / Kik Code
        }
        .padding(.horizontal, 14).padding(.vertical, 8)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct KikTabView: View {
    var body: some View {
        TabView {
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.fill") }
            PeopleView().tabItem { Label("People", systemImage: "person.2.fill") }
            DiscoverView().tabItem { Label("Discover", systemImage: "sparkles") }
            KikCodeView().tabItem { Label("Kik Code", systemImage: "qrcode") }
        }
        .tint(.kikBlue) // bright Kik Blue active; standard UITabBar, no custom pill, no call tab
    }
}
```

## 5. Motion

```swift
// Outgoing bubble — slide up + scale from input
withAnimation(.spring(response: 0.32, dampingFraction: 0.8)) { messages.append(m) }
// .transition(.scale(scale: 0.88).combined(with: .move(edge: .bottom)))

// S/D/R receipt — NO animation; swap the letter instantly. The R recolor to Blue is the only "event".

// List row reorder on new message
withAnimation(.easeOut(duration: 0.25)) { conversations.sortToTop(id) }

// Bot suggested-reply chips
.transition(.opacity.combined(with: .move(edge: .bottom)))   // 0.2s

// Kik Code match — plate pulse, then push to chat
withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { codePulse.toggle() }

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: sentCount)     // send
.sensoryFeedback(.impact(flexibility: .soft), trigger: botTapped) // bot button
.sensoryFeedback(.success, trigger: kikCodeMatched)               // Kik Code match / friend added
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Back | `chevron.left` | 17pt |
| New chat | `plus` | 22pt |
| Scan / camera | `qrcode.viewfinder` / `camera` | 22pt |
| Attachment | `plus` | 22pt |
| Search | `magnifyingglass` | 15pt |
| Chats (tab) | `bubble.left.fill` | 22pt |
| People (tab) | `person.2.fill` | 22pt |
| Discover (tab) | `sparkles` | 22pt |
| Kik Code (tab) | `qrcode` | 22pt |
| Bot avatar marker | `cpu` / rounded-square frame | n/a |
| GIF | `photo.stack` | 22pt |
| Web content | `globe` | 22pt |
| Failed message | `exclamationmark.circle.fill` | 11pt |
| Group | `person.3.fill` | 22pt |
| Block / report | `hand.raised.fill` | 18pt |
| Settings | `gearshape.fill` | 22pt |
| Share Kik Code | `square.and.arrow.up` | 22pt |

## 7. Dark Mode

```swift
struct KikTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.kikDarkCanvas : Color.kikCanvas)
            .tint(.kikBlue)
    }
}
extension View { func kikTheme() -> some View { modifier(KikTheme()) } }
```

- Dark canvas is a **neutral near-black** `#121316` — NOT color-cast; neutral keeps Kik Blue vivid
- Kik Blue `#00B0F0` (outgoing bubble / buttons / tabs / Kik Code) holds across modes
- Incoming bubble inverts `#E9EAEC` → `#25282D`; text → `#F1F2F4`
- The S/D/R receipt: S/D stay gray (mode-adjusted), **R stays Kik Blue** in both modes
- Kik Green `#82BC23` heritage accent is identical across modes (rarely used)
- Floating menus lose visible shadow on dark — add a 1pt `kikDarkDivider` border as the elevation cue; the Kik Code plate stays flat & high-contrast

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.rect(topLeadingRadius:)` needs iOS 16.4; `.sensoryFeedback`/`TextField(axis:)` need iOS 17 — fall back to a custom `Shape` + `UIImpactFeedbackGenerator`)
- Bundle Inter TTFs via `Info.plist` (SIL OFL) as the Kik-brand substitute
- Dynamic Type: scale titles, headline, row title, body, previews; pin timestamps, tab labels, the S/D/R glyph, "BOT" tag, and the Kik Code plate
- VoiceOver: spell the receipt out as a value — "Sent" / "Delivered" / "Read" (not the bare letter); label bot rows "{name}, bot"; label the Kik Code "Kik Code, double-tap to share"; usernames read with the leading "at"
- Color contrast: dark-cyan ink `#002A36`/`#00323F` on `#00B0F0` passes WCAG AA — never white on the bright Blue; `label` on `#E9EAEC`/`#25282D` passes AA; the Read-Blue receipt is paired with the explicit letter "R" so it survives color-blindness
- Differentiate Without Color: receipt meaning is carried by the letter (S/D/R) itself, not just color; bots by the rounded-square avatar + "BOT" text
- Reduce Motion: disable the bubble spring-in (crossfade) and the Kik Code pulse; the receipt has no animation to disable
- Reduce Transparency: solid `kikDarkSurface1`/`kikSurfaceGray` bars (no blur used by default)
- Dark mode: invert per `kikDark*` — neutral `#121316`, never color-cast; add a `kikDarkDivider` border to floating menus as the elevation cue; keep the Kik Code plate flat & high-contrast for reliable scanning
