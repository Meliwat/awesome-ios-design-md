# Character.AI (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Character.AI's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the asymmetric tuck bubble, the typing indicator, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — primary)
    static let caiCanvas    = Color(red: 0.059, green: 0.059, blue: 0.063) // #0F0F10
    static let caiSurface1  = Color(red: 0.086, green: 0.086, blue: 0.094) // #161618
    static let caiSurface2  = Color(red: 0.118, green: 0.118, blue: 0.129) // #1E1E21
    static let caiBubbleAI  = Color(red: 0.110, green: 0.110, blue: 0.122) // #1C1C1F
    static let caiBubbleUser = Color(red: 0.149, green: 0.149, blue: 0.165) // #26262A
    static let caiDivider   = Color(red: 0.165, green: 0.165, blue: 0.180) // #2A2A2E

    // MARK: - Canvas & Surfaces (Light)
    static let caiCanvasLight   = Color.white                                 // #FFFFFF
    static let caiBubbleAILight = Color(red: 0.945, green: 0.945, blue: 0.953) // #F1F1F3
    static let caiBubbleUserLight = Color(red: 0.894, green: 0.910, blue: 0.949) // #E4E8F2

    // MARK: - Brand
    static let caiAccent      = Color(red: 0.227, green: 0.482, blue: 0.992) // #3A7BFD
    static let caiAccentPress = Color(red: 0.180, green: 0.388, blue: 0.839) // #2E63D6
    static let caiAccentSoft  = Color(red: 0.106, green: 0.165, blue: 0.290) // #1B2A4A
    static let caiLilac       = Color(red: 0.616, green: 0.482, blue: 1.000) // #9D7BFF

    // MARK: - Text
    static let caiTextPrimary   = Color(red: 0.925, green: 0.925, blue: 0.933) // #ECECEE
    static let caiTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.635) // #9A9AA2
    static let caiTextTertiary  = Color(red: 0.400, green: 0.400, blue: 0.431) // #66666E
    static let caiOnAccent      = Color.white                                  // #FFFFFF

    // MARK: - Semantic
    static let caiSuccess = Color(red: 0.306, green: 0.851, blue: 0.604) // #4ED99A
    static let caiError   = Color(red: 1.000, green: 0.420, blue: 0.420) // #FF6B6B
}

// Character avatar gradients (the single warm focal point)
extension RadialGradient {
    static let caiAvatarBlue = RadialGradient(
        colors: [Color(hex: 0x6FA0FF), .caiAccent, Color(hex: 0x274FB0)],
        center: UnitPoint(x: 0.36, y: 0.30), startRadius: 2, endRadius: 60
    )
    static let caiAvatarLilac = RadialGradient(
        colors: [Color(hex: 0xC7A8FF), .caiLilac, Color(hex: 0x6A47C9)],
        center: UnitPoint(x: 0.36, y: 0.30), startRadius: 2, endRadius: 60
    )
}

extension Color {
    init(hex: UInt) {
        self.init(red: Double((hex >> 16) & 0xFF)/255, green: Double((hex >> 8) & 0xFF)/255, blue: Double(hex & 0xFF)/255)
    }
}
```

## 2. Typography

Character.AI pairs **Sora** (identity: wordmark, titles, names, buttons) with **Inter** (reading comfort: all message text, descriptions, body). Bundle both via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    // Sora — identity
    static let caiDisplay     = Font.custom("Sora-Bold",     size: 32) // -0.5 tracking
    static let caiScreenTitle = Font.custom("Sora-SemiBold", size: 24)
    static let caiGreetingName = Font.custom("Sora-SemiBold", size: 20)
    static let caiHeaderName  = Font.custom("Sora-SemiBold", size: 17)
    static let caiCardName    = Font.custom("Sora-SemiBold", size: 15)
    static let caiButton      = Font.custom("Sora-SemiBold", size: 15)
    static let caiTab         = Font.custom("Sora-SemiBold", size: 10)

    // Inter — reading comfort
    static let caiMessage     = Font.custom("Inter-Regular", size: 15)
    static let caiMessageDense = Font.custom("Inter-Regular", size: 14)
    static let caiCardDesc    = Font.custom("Inter-Regular", size: 13)
    static let caiBody        = Font.custom("Inter-Regular", size: 16)
    static let caiMeta        = Font.custom("Inter-Medium",  size: 11)
}
```

## 3. Signature Components

### Asymmetric Tuck Bubble

```swift
/// AI tucks the TOP-LEADING corner (points back at the avatar); user mirrors it.
struct TuckBubbleShape: Shape {
    enum Speaker { case ai, user }
    let speaker: Speaker
    var tuck: CGFloat = 4
    var round: CGFloat = 18

    func path(in r: CGRect) -> Path {
        let tl = speaker == .ai   ? tuck : round
        let tr = speaker == .user ? tuck : round
        return Path(
            roundedRect: r,
            cornerRadii: .init(topLeading: tl, bottomLeading: round, bottomTrailing: round, topTrailing: tr),
            style: .continuous
        )
    }
}

/// Renders message text with `*roleplay actions*` italic + secondary — load-bearing signature.
func caiAttributed(_ raw: String) -> AttributedString {
    var out = AttributedString()
    var italic = false
    raw.split(separator: "*", omittingEmptySubsequences: false).forEach { seg in
        var run = AttributedString(String(seg))
        run.font = .caiMessage
        run.foregroundColor = italic ? .caiTextSecondary : .caiTextPrimary
        if italic { run.font = Font.custom("Inter-Italic", size: 15) }
        out += run
        italic.toggle()
    }
    return out
}

struct MessageBubble: View {
    enum Speaker { case ai, user }
    let speaker: Speaker
    let text: String
    let avatar: RadialGradient

    var body: some View {
        HStack(alignment: .top, spacing: 9) {
            if speaker == .ai {
                Circle().fill(avatar).frame(width: 26, height: 26).padding(.top, 2)
            }
            Text(caiAttributed(text))
                .lineSpacing(4)               // ≈ line-height 1.5 at 15pt
                .padding(.vertical, 11).padding(.horizontal, 14)
                .background(
                    TuckBubbleShape(speaker: speaker == .ai ? .ai : .user)
                        .fill(speaker == .ai ? Color.caiBubbleAI : Color.caiBubbleUser)
                )
                .overlay(
                    speaker == .ai
                    ? AnyView(TuckBubbleShape(speaker: .ai).strokeBorder(Color.caiDivider, lineWidth: 1))
                    : AnyView(EmptyView())
                )
        }
        .frame(maxWidth: .infinity, alignment: speaker == .ai ? .leading : .trailing)
        .padding(.horizontal, 14)
        .frame(maxWidth: .infinity)
        .modifier(BubbleMaxWidth())
    }
}

struct BubbleMaxWidth: ViewModifier {
    func body(content: Content) -> some View {
        content.frame(maxWidth: UIScreen.main.bounds.width * 0.86, alignment: .leading)
    }
}
```

### Typing Indicator

```swift
struct TypingIndicator: View {
    @State private var phase = 0.0
    let avatar: RadialGradient

    var body: some View {
        HStack(alignment: .top, spacing: 9) {
            Circle().fill(avatar).frame(width: 26, height: 26).padding(.top, 2)
            HStack(spacing: 5) {
                ForEach(0..<3) { i in
                    Circle()
                        .fill(Color.caiTextTertiary)
                        .frame(width: 6, height: 6)
                        .opacity(0.4 + 0.6 * abs(sin(phase + Double(i) * 0.52)))
                }
            }
            .padding(.vertical, 14).padding(.horizontal, 16)
            .background(TuckBubbleShape(speaker: .ai).fill(Color.caiBubbleAI))
            .overlay(TuckBubbleShape(speaker: .ai).strokeBorder(Color.caiDivider, lineWidth: 1))
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(.horizontal, 14)
        .onAppear {
            withAnimation(.linear(duration: 1.2).repeatForever(autoreverses: false)) { phase = .pi * 2 }
        }
    }
}
```

### Greeting Header

```swift
struct GreetingHeader: View {
    let avatar: RadialGradient
    let name: String
    let tagline: String       // "A curious starship navigator · by @lumen"
    @State private var shown = false

    var body: some View {
        VStack(spacing: 8) {
            Circle().fill(avatar).frame(width: 64, height: 64)
                .scaleEffect(shown ? 1 : 0.92).opacity(shown ? 1 : 0)
            Text(name).font(.caiGreetingName).foregroundStyle(Color.caiTextPrimary)
                .opacity(shown ? 1 : 0)
            Text(tagline).font(.caiCardDesc).foregroundStyle(Color.caiTextTertiary)
                .opacity(shown ? 1 : 0)
        }
        .frame(maxWidth: .infinity)
        .padding(.vertical, 8)
        .onAppear { withAnimation(.easeOut(duration: 0.26)) { shown = true } }
    }
}
```

### Character Card (Discover)

```swift
struct CharacterCard: View {
    let avatar: RadialGradient
    let name: String
    let desc: String
    let chats: String          // "2.4M chats"

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Circle().fill(avatar).frame(width: 48, height: 48)
            Text(name).font(.caiCardName).foregroundStyle(Color.caiTextPrimary)
            Text(desc).font(.caiCardDesc).foregroundStyle(Color.caiTextSecondary)
                .lineLimit(2).fixedSize(horizontal: false, vertical: true)
            HStack(spacing: 5) {
                Image(systemName: "bubble.left.and.bubble.right")
                    .font(.system(size: 11)).foregroundStyle(Color.caiTextTertiary)
                Text(chats).font(.caiMeta).foregroundStyle(Color.caiTextTertiary)
            }
        }
        .padding(16)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.caiSurface2))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.caiDivider, lineWidth: 1))
    }
}
```

### Composer

```swift
struct Composer: View {
    @Binding var text: String
    let characterName: String
    let streaming: Bool
    let onSend: () -> Void
    let onStop: () -> Void

    var body: some View {
        HStack(spacing: 10) {
            TextField("Message \(characterName)…", text: $text, axis: .vertical)
                .font(.caiMessageDense)
                .foregroundStyle(Color.caiTextPrimary)
                .lineLimit(1...5)
                .padding(.horizontal, 16)
                .frame(minHeight: 44)
                .background(Capsule().fill(Color.caiSurface2))
                .overlay(Capsule().strokeBorder(Color.caiDivider, lineWidth: 1))

            Button(action: streaming ? onStop : onSend) {
                Image(systemName: streaming ? "stop.fill" : "arrow.up")
                    .font(.system(size: 18, weight: .bold))
                    .foregroundStyle(text.isEmpty && !streaming ? Color.caiTextTertiary : Color.caiOnAccent)
                    .frame(width: 40, height: 40)
                    .background(Circle().fill(text.isEmpty && !streaming ? Color.caiBubbleUser : Color.caiAccent))
            }
            .animation(.easeInOut(duration: 0.15), value: streaming)
        }
        .padding(.horizontal, 14).padding(.top, 10).padding(.bottom, 16)
        .overlay(Divider().background(Color.caiDivider), alignment: .top)
    }
}
```

### Buttons

```swift
struct CAIPrimaryButton: View {
    let title: String; let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.caiButton).foregroundStyle(Color.caiOnAccent)
                .padding(.vertical, 13).padding(.horizontal, 26)
                .background(Capsule().fill(Color.caiAccent))
        }
        .buttonStyle(PressScale())
    }
}

struct CAISoftButton: View {
    let title: String; let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.caiButton).foregroundStyle(Color(hex: 0x9FC0FF))
                .padding(.vertical, 13).padding(.horizontal, 26)
                .background(Capsule().fill(Color.caiAccentSoft))
        }
        .buttonStyle(PressScale())
    }
}

struct PressScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label.scaleEffect(configuration.isPressed ? 0.98 : 1.0)
            .animation(.easeOut(duration: 0.08), value: configuration.isPressed)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct CAITabView: View {
    var body: some View {
        TabView {
            DiscoverView().tabItem { Label("Discover", systemImage: "sparkles") }
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.and.bubble.right") }
            CreateView().tabItem { Label("Create", systemImage: "plus.circle") }
            ProfileView().tabItem { Label("Profile", systemImage: "person") }
        }
        .tint(.caiAccent)                  // active = blue icon + blue label; no pill
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.caiCanvas)
            a.shadowColor = UIColor(Color.caiDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Message send — user bubble fade + 8pt slide up, list autoscroll
.transition(.opacity.combined(with: .move(edge: .bottom)))
withAnimation(.easeOut(duration: 0.12)) { messages.append(userMsg) }
withAnimation(.easeOut(duration: 0.25)) { scrollProxy.scrollTo(bottomID, anchor: .bottom) }

// AI streaming — append tokens to the bound string; bubble height grows with no jump.
// Blink a tail caret while streaming:
@State private var caretOn = true
.onReceive(Timer.publish(every: 0.5, on: .main, in: .common).autoconnect()) { _ in
    if isStreaming { caretOn.toggle() }
}

// Typing indicator — 1.2s repeatForever, 0.52rad stagger (see TypingIndicator)

// Greeting entrance — avatar scale 0.92 → 1.0 + fade, 260ms ease-out (see GreetingHeader)

// Send ↔ Stop morph — 150ms ease-in-out (see Composer)

// Haptics — NONE during streaming (would buzz continuously)
UISelectionFeedbackGenerator().selectionChanged()                 // send tap, tab switch
UIImpactFeedbackGenerator(style: .light).impactOccurred()         // long-press bubble actions
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Discover (tab) | `sparkles` | 23pt |
| Chats (tab) | `bubble.left.and.bubble.right` / `.fill` | 23pt |
| Create (tab) | `plus.circle` / `.fill` | 23pt |
| Profile (tab) | `person` / `person.fill` | 23pt |
| Send | `arrow.up` | 18pt |
| Stop generation | `stop.fill` | 18pt |
| Back | `chevron.left` | 18pt |
| Overflow / chat menu | `ellipsis` (vertical) | 18pt |
| Chat-count (card) | `bubble.left.and.bubble.right` | 11pt |
| New chat | `square.and.pencil` | 18pt |
| Retry message | `arrow.clockwise` | 16pt |
| Copy message | `doc.on.doc` | 16pt |
| Rate message | `hand.thumbsup` / `hand.thumbsdown` | 16pt |
| Search | `magnifyingglass` | 16pt |
| Settings | `gearshape` | 18pt |

## 7. Dark Mode

Character.AI is dark-first; a light theme exists. Drive canvas/surface/text by `colorScheme`. The accent blue, lilac, and avatar gradients are **identical** across modes.

```swift
struct CAITheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.caiCanvas : Color.caiCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.caiTextPrimary : Color(red: 0.086, green: 0.086, blue: 0.102))
            .tint(.caiAccent)
    }
}

extension View { func caiTheme() -> some View { modifier(CAITheme()) } }
```

In light mode only the bubble greys swap (`#F1F1F3` AI / `#E4E8F2` user) — the 4pt tuck still does speaker differentiation, and the accent blue `#3A7BFD` is unchanged. Never let the accent become a bubble fill in either mode.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`TextField(axis: .vertical)` auto-grow, `AttributedString` runs, `RoundedRectangle` per-corner radii)
- Bundle Sora + Inter (incl. Inter-Italic) TTFs via `Info.plist` `UIAppFonts` — both SIL OFL, free to distribute
- Dynamic Type: scale display, titles, names, message text, body; pin tab labels and "by @creator · N chats" meta at fixed sizes
- Message text: allow generous scaling; the bubble grows with it while the 86% max-width constant holds — never truncate AI replies
- VoiceOver: label AI bubbles "Aria said: {speech}; action: {roleplay text}" so screen readers convey the *narration vs speech* distinction the italics encode visually; label the typing indicator "Aria is typing"; label the send button "Send" / "Stop generating"
- Roleplay `*actions*` are meaning, not decoration — expose them to VoiceOver distinctly (e.g., prefixed "action:") rather than dropping the asterisk semantics
- Color contrast: `#ECECEE` on `#0F0F10` is strong; `#FFFFFF` on `#3A7BFD` passes AA for the send button/CTAs; `#9A9AA2` italic on `#1C1C1F` passes AA for the roleplay text at 15pt — keep the size
- Reduce Motion: disable the typing-dot wave (show three static dots), the greeting scale, and the send↔stop morph (instant swap); keep token streaming (it's content, not decoration)
- Streaming + haptics: never fire haptics during streaming — it would be continuous; only on send tap and discrete actions
- The avatar gradient is the only warm element — ensure character identity is also conveyed by the name label for users who can't perceive color (do not rely on blue-vs-lilac alone to tell characters apart)
