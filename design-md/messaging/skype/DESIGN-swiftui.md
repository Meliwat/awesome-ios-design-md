# Skype (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Skype's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand Skype Blue
    static let skypeBlue        = Color(red: 0.000, green: 0.686, blue: 0.941) // #00AFF0
    static let skypeBlueDeep    = Color(red: 0.000, green: 0.471, blue: 0.831) // #0078D4
    static let skypeBluePressed = Color(red: 0.000, green: 0.569, blue: 0.800) // #0091CC
    static let skypeCyan        = Color(red: 0.204, green: 0.765, blue: 1.000) // #34C3FF
    static let skypeInkOnBright = Color(red: 0.000, green: 0.204, blue: 0.247) // #00343F

    // MARK: - Canvas & Surfaces (Light)
    static let skypeCanvas      = Color.white                                  // #FFFFFF
    static let skypeIncoming    = Color(red: 0.922, green: 0.922, blue: 0.937) // #EBEBEF
    static let skypeSurfaceGray = Color(red: 0.957, green: 0.957, blue: 0.965) // #F4F4F6
    static let skypeRowPressed  = Color(red: 0.910, green: 0.910, blue: 0.925) // #E8E8EC
    static let skypeDivider     = Color(red: 0.886, green: 0.886, blue: 0.902) // #E2E2E6

    // MARK: - Canvas & Surfaces (Dark) — neutral near-black
    static let skypeDarkCanvas   = Color(red: 0.086, green: 0.086, blue: 0.102) // #16161A
    static let skypeDarkSurface1 = Color(red: 0.122, green: 0.122, blue: 0.141) // #1F1F24
    static let skypeDarkSurface2 = Color(red: 0.165, green: 0.165, blue: 0.188) // #2A2A30
    static let skypeDarkDivider  = Color(red: 0.204, green: 0.204, blue: 0.235) // #34343C

    // MARK: - Text
    static let skypeTextPrimary  = Color(red: 0.106, green: 0.106, blue: 0.122) // #1B1B1F
    static let skypeOnBlue       = Color.white
    static let skypeTextSecondary = Color(red: 0.431, green: 0.431, blue: 0.471) // #6E6E78
    static let skypeTextTertiary = Color(red: 0.604, green: 0.604, blue: 0.643) // #9A9AA4
    static let skypeTextPrimaryDark = Color(red: 0.949, green: 0.949, blue: 0.957) // #F2F2F4
    static let skypeTextSecondaryDark = Color(red: 0.643, green: 0.643, blue: 0.682) // #A4A4AE

    // MARK: - Presence & Semantic
    static let skypeGreen  = Color(red: 0.176, green: 0.761, blue: 0.420) // #2DC26B (active / accept)
    static let skypeYellow = Color(red: 1.000, green: 0.769, blue: 0.000) // #FFC400 (away)
    static let skypeRed    = Color(red: 0.910, green: 0.212, blue: 0.310) // #E8364F (end / DND / error)
    static let skypeLink   = Color(red: 0.000, green: 0.471, blue: 0.831) // #0078D4

    static var skypeCanvasAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(red: 0.086, green: 0.086, blue: 0.102, alpha: 1) : .white })
    }
    static var skypeIncomingAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(red: 0.165, green: 0.165, blue: 0.188, alpha: 1)
            : UIColor(red: 0.922, green: 0.922, blue: 0.937, alpha: 1) })
    }
}
```

## 2. Typography

Skype's brand face is Segoe UI (proprietary cross-platform); bundle **Inter** (SIL OFL) as the closest free substitute via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let skypeLargeTitle = Font.custom("Inter-ExtraBold", size: 32)
    static let skypeTitle1     = Font.custom("Inter-Bold",      size: 26)
    static let skypeTitle3     = Font.custom("Inter-Bold",      size: 22)
    static let skypeHeadline   = Font.custom("Inter-Bold",      size: 17)
    static let skypeBody       = Font.custom("Inter-Regular",   size: 15)
    static let skypeBodyEmph   = Font.custom("Inter-SemiBold",  size: 15)
    static let skypePreview    = Font.custom("Inter-Medium",    size: 14)
    static let skypeFootnote   = Font.custom("Inter-SemiBold",  size: 13)
    static let skypeCaption    = Font.custom("Inter-SemiBold",  size: 11)
    static let skypeButton     = Font.custom("Inter-Bold",      size: 16)
    static let skypeTab        = Font.custom("Inter-SemiBold",  size: 11)
    static let skypeBadge      = Font.custom("Inter-Bold",      size: 11)
}
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```swift
struct SkypeBubble: View {
    let text: String
    let outgoing: Bool
    let tailEnd: Bool
    let meta: String   // "9:40 · Delivered"

    var body: some View {
        VStack(alignment: outgoing ? .trailing : .leading, spacing: 4) {
            Text(text)
                .font(.skypeBody)
                .foregroundStyle(outgoing ? Color.skypeOnBlue : Color.skypeTextPrimary)
                .padding(.vertical, 9).padding(.horizontal, 14)
                .background(outgoing ? Color.skypeBlueDeep : Color.skypeIncomingAdaptive)
                .clipShape(.rect(
                    topLeadingRadius: 14,
                    bottomLeadingRadius: outgoing ? 14 : (tailEnd ? 4 : 14),
                    bottomTrailingRadius: outgoing ? (tailEnd ? 4 : 14) : 14,
                    topTrailingRadius: 14))
            if outgoing {
                Text(meta).font(.system(size: 10)).foregroundStyle(.tertiary)
            }
        }
        .frame(maxWidth: 280, alignment: outgoing ? .trailing : .leading)
        .frame(maxWidth: .infinity, alignment: outgoing ? .trailing : .leading)
    }
}
```

### Inline Call Card

```swift
struct CallCard: View {
    let title: String       // "Group video call"
    let subtitle: String    // "12 min · 3 participants"
    let answered: Bool
    @Environment(\.colorScheme) var scheme

    var body: some View {
        HStack(spacing: 10) {
            ZStack {
                Circle().fill((answered ? Color.skypeGreen : Color.skypeRed).opacity(0.18)).frame(width: 30, height: 30)
                Image(systemName: answered ? "phone.fill" : "phone.down.fill")
                    .font(.system(size: 14)).foregroundStyle(answered ? Color.skypeGreen : Color.skypeRed)
            }
            VStack(alignment: .leading, spacing: 1) {
                Text(title).font(.system(size: 13, weight: .bold)).foregroundStyle(.primary)
                Text(subtitle).font(.system(size: 11)).foregroundStyle(.secondary)
            }
        }
        .padding(.vertical, 10).padding(.horizontal, 14)
        .background(scheme == .dark ? Color.skypeDarkSurface2 : Color.skypeSurfaceGray,
                    in: RoundedRectangle(cornerRadius: 14))
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}
```

### Video Call Grid + Control Bar

```swift
struct VideoTile: View {
    let name: String
    let muted: Bool
    let gradient: [Color]
    var body: some View {
        ZStack(alignment: .bottomLeading) {
            LinearGradient(colors: gradient, startPoint: .topLeading, endPoint: .bottomTrailing)
            Text(name).font(.system(size: 11, weight: .semibold)).foregroundStyle(.white)
                .padding(6).background(.black.opacity(0.35), in: Capsule()).padding(8)
            if muted {
                Image(systemName: "mic.slash.fill").font(.system(size: 10)).foregroundStyle(.white)
                    .frame(width: 18, height: 18).background(.black.opacity(0.45), in: Circle())
                    .padding(8).frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topTrailing)
            }
        }
        .clipShape(RoundedRectangle(cornerRadius: 14))
    }
}

struct CallControlBar: View {
    @Binding var muted: Bool
    @Binding var videoOn: Bool
    let onEnd: () -> Void
    var body: some View {
        HStack(spacing: 14) {
            ControlButton(systemName: muted ? "mic.slash.fill" : "mic.fill", bg: .skypeDarkSurface2) { muted.toggle() }
            ControlButton(systemName: videoOn ? "video.fill" : "video.slash.fill", bg: .skypeDarkSurface2) { videoOn.toggle() }
            ControlButton(systemName: "rectangle.on.rectangle", bg: .skypeDarkSurface2) {}
            ControlButton(systemName: "phone.down.fill", bg: .skypeRed, action: onEnd)
        }
        .padding(16)
        .background(.black.opacity(0.4), in: Capsule())
    }
}

struct ControlButton: View {
    let systemName: String
    let bg: Color
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: systemName).font(.system(size: 22)).foregroundStyle(.white)
                .frame(width: 56, height: 56).background(Circle().fill(bg))
        }
    }
}
```

### Presence Dot

```swift
enum Presence { case active, away, dnd, offline, inCall }

struct PresenceDot: View {
    let presence: Presence
    @Environment(\.colorScheme) var scheme
    var body: some View {
        let canvas = scheme == .dark ? Color.skypeDarkCanvas : Color.skypeCanvas
        switch presence {
        case .active:  Circle().fill(Color.skypeGreen)
        case .away:    Circle().fill(Color.skypeYellow)
        case .dnd:     Circle().fill(Color.skypeRed)
        case .inCall:  Circle().fill(Color.skypeBlue)
        case .offline: Circle().strokeBorder(Color.skypeTextTertiary, lineWidth: 2)
        }
        // Apply at call site: .frame(width: 11, height: 11).overlay(Circle().strokeBorder(canvas, lineWidth: 2))
    }
}
```

### Compose Bar

```swift
struct SkypeComposeBar: View {
    @Binding var text: String
    let onSend: () -> Void
    var body: some View {
        HStack(spacing: 10) {
            Button {} label: { Image(systemName: "plus").font(.system(size: 22)).foregroundStyle(.secondary) }
            HStack {
                TextField("Type a message", text: $text, axis: .vertical).font(.skypeBody).lineLimit(1...5)
                Button {} label: { Image(systemName: "face.smiling").font(.system(size: 22)).foregroundStyle(.secondary) }
            }
            .padding(.horizontal, 14).frame(minHeight: 36)
            .background(Color.skypeSurfaceGray, in: RoundedRectangle(cornerRadius: 18))
            Button(action: onSend) {
                Image(systemName: text.isEmpty ? "mic.fill" : "paperplane.fill")
                    .font(.system(size: 16)).foregroundStyle(Color.skypeInkOnBright)
                    .frame(width: 36, height: 36).background(Circle().fill(Color.skypeBlue))
            }
        }
        .padding(.horizontal, 14).padding(.vertical, 8)
    }
}
```

### Chat Header (call-first)

```swift
struct SkypeChatHeader: View {
    let name: String
    let initials: String
    let status: String
    var body: some View {
        HStack(spacing: 10) {
            Button {} label: { Image(systemName: "chevron.left").font(.system(size: 17, weight: .semibold)).foregroundStyle(Color.skypeBlue) }
            ZStack(alignment: .bottomTrailing) {
                Circle().fill(LinearGradient(colors: [.skypeCyan, .skypeBlueDeep], startPoint: .top, endPoint: .bottom))
                    .frame(width: 36, height: 36)
                    .overlay(Text(initials).font(.system(size: 14, weight: .bold)).foregroundStyle(.white))
                Circle().fill(Color.skypeGreen).frame(width: 11, height: 11)
                    .overlay(Circle().strokeBorder(Color.skypeCanvasAdaptive, lineWidth: 2))
            }
            VStack(alignment: .leading, spacing: 1) {
                Text(name).font(.skypeHeadline)
                Text(status).font(.skypeCaption).foregroundStyle(Color.skypeGreen)
            }
            Spacer()
            Button {} label: { Image(systemName: "phone").font(.system(size: 22)).foregroundStyle(Color.skypeBlue) }
            Button {} label: { Image(systemName: "video").font(.system(size: 22)).foregroundStyle(Color.skypeBlue) }
        }
        .padding(.horizontal, 14).padding(.vertical, 8)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SkypeTabView: View {
    var body: some View {
        TabView {
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.fill") }
            CallsView().tabItem { Label("Calls", systemImage: "phone.fill") }
            ContactsView().tabItem { Label("Contacts", systemImage: "person.2.fill") }.badge(5)
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }
        }
        .tint(.skypeBlue) // bright Skype Blue active; standard UITabBar, no custom pill
    }
}
```

## 5. Motion

```swift
// Outgoing bubble — slide up + scale from input
withAnimation(.spring(response: 0.32, dampingFraction: 0.8)) { messages.append(m) }
// .transition(.scale(scale: 0.88).combined(with: .move(edge: .bottom)))

// Reactions strip (long-press ~0.45s)
.onLongPressGesture(minimumDuration: 0.45) {
    withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) { showReactions = true }
}

// Call card insert
.transition(.opacity.combined(with: .move(edge: .leading)))   // 0.25s

// Video tile join / leave
.transition(.scale(scale: 0.9).combined(with: .opacity))      // 0.3s + grid reflow

// Call connect — ringing → grid crossfade
withAnimation(.easeInOut(duration: 0.3)) { callConnected = true }

// Presence change
.animation(.easeInOut(duration: 0.2), value: presence)

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: sentCount)   // send
.sensoryFeedback(.impact(flexibility: .soft), trigger: reaction) // reaction dock
.sensoryFeedback(.success, trigger: callConnected)               // connect
.sensoryFeedback(.warning, trigger: callEnded)                   // end
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Back | `chevron.left` | 17pt |
| Voice call | `phone` / `phone.fill` | 22pt |
| Video call | `video` / `video.fill` | 22pt |
| End call | `phone.down.fill` | 22pt |
| Mute | `mic.fill` / `mic.slash.fill` | 22pt |
| Share screen | `rectangle.on.rectangle` | 22pt |
| Attachment | `plus` | 22pt |
| Emoji | `face.smiling` | 22pt |
| Send | `paperplane.fill` | 16pt |
| Voice message | `mic.fill` | 16pt |
| Chats (tab) | `bubble.left.fill` | 22pt |
| Calls (tab) | `phone.fill` | 22pt |
| Contacts (tab) | `person.2.fill` | 22pt |
| Notifications (tab) | `bell.fill` | 22pt |
| Search | `magnifyingglass` | 15pt |
| Meet Now | `video.badge.plus` | 22pt |
| Reaction (default) | `heart.fill` | 28pt |
| Add people | `person.badge.plus` | 22pt |

## 7. Dark Mode

```swift
struct SkypeTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.skypeDarkCanvas : Color.skypeCanvas)
            .tint(.skypeBlue)
    }
}
extension View { func skypeTheme() -> some View { modifier(SkypeTheme()) } }
```

- Dark canvas is a **neutral near-black** `#16161A` — NOT color-cast; neutral keeps the cloud blue vivid
- Bright `#00AFF0` (buttons/FABs/tabs) and deep `#0078D4` (outgoing bubble) hold across modes
- Incoming bubble inverts `#EBEBEF` → `#2A2A30`; text → `#F2F2F4`
- Presence/semantic greens, yellows, reds are constant; video tile gradients stay full-color
- Floating menus/reactions lose visible shadow on dark — add a 1pt `skypeDarkDivider` border as the elevation cue

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.rect(topLeadingRadius:)` needs iOS 16.4; `.sensoryFeedback`/`TextField(axis:)` need iOS 17 — fall back to a custom `Shape` + `UIImpactFeedbackGenerator`)
- Bundle Inter TTFs via `Info.plist` (SIL OFL) as the Segoe-UI substitute
- Dynamic Type: scale titles, headline, body, list/call-card text; pin timestamps, tab labels, badges, presence, per-tile mic/name labels
- VoiceOver: label outgoing bubbles "You said {text}, {delivery state}"; call cards "{title}, {subtitle}"; presence dots as the avatar's value ("Active"); expose Reactions as a custom action; label video tiles "{name}, {muted?}"
- Color contrast: white on `#0078D4` passes WCAG AA; deep-cyan ink `#00343F` on `#00AFF0` passes AA — do not put white text on the bright blue button; `label` on `#EBEBEF`/`#2A2A30` passes AA
- Differentiate Without Color: presence is conveyed by dot + (DND minus-glyph / offline hollow ring) so it survives color-blindness; call-card outcome by glyph (phone vs phone-down) plus color
- Reduce Motion: disable bubble spring-in (crossfade), tile join/leave scale (fade only), call-connect crossfade simplified
- Reduce Transparency: solid `skypeDarkSurface2`/`skypeSurfaceGray` instead of the translucent call-control scrim
- Dark mode: invert per `skypeDark*` — neutral `#16161A`, never color-cast; add a `skypeDarkDivider` border to floating surfaces as the elevation cue
