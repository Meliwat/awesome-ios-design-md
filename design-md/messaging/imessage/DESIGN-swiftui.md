# iMessage (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates iMessage's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. iMessage is system-native; favor `Color`-asset semantic colors and `.system` fonts so it tracks Apple's appearance automatically.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand Bubble Colors (identical light & dark)
    static let imsgBlue        = Color(red: 0.000, green: 0.478, blue: 1.000) // #007AFF
    static let imsgBluePressed = Color(red: 0.000, green: 0.384, blue: 0.800) // #0062CC
    static let imsgGreen       = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759
    static let imsgGreenPressed = Color(red: 0.141, green: 0.541, blue: 0.239) // #248A3D

    // MARK: - Canvas & Surfaces (Light)
    static let imsgCanvas       = Color.white                                  // #FFFFFF
    static let imsgIncoming     = Color(red: 0.914, green: 0.914, blue: 0.922) // #E9E9EB
    static let imsgGroupedBg    = Color(red: 0.949, green: 0.949, blue: 0.969) // #F2F2F7

    // MARK: - Canvas & Surfaces (Dark)
    static let imsgDarkCanvas   = Color.black                                  // #000000
    static let imsgIncomingDark = Color(red: 0.149, green: 0.149, blue: 0.165) // #26262A
    static let imsgDarkSurface1 = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let imsgDarkSurface2 = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E

    // MARK: - Text
    static let imsgTextPrimary  = Color.primary                                // label
    static let imsgOnAccent     = Color.white                                  // text on blue/green
    static let imsgTextSecondary = Color.secondary                             // secondaryLabel
    static let imsgTextTertiary = Color(.tertiaryLabel)                        // Delivered / placeholder

    // MARK: - Semantic
    static let imsgRed     = Color(red: 1.000, green: 0.231, blue: 0.188)      // #FF3B30 systemRed
    static let imsgRedDark = Color(red: 1.000, green: 0.271, blue: 0.227)      // #FF453A
    static let imsgLink    = Color(red: 0.000, green: 0.478, blue: 1.000)      // #007AFF

    // Adaptive helpers
    static var imsgIncomingAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(red: 0.149, green: 0.149, blue: 0.165, alpha: 1)
            : UIColor(red: 0.914, green: 0.914, blue: 0.922, alpha: 1) })
    }
    static var imsgCanvasAdaptive: Color {
        Color(UIColor { $0.userInterfaceStyle == .dark ? .black : .white })
    }
}
```

## 2. Typography

iMessage uses the system San Francisco face at Dynamic Type sizes. Use `.system` text styles — never a bundled font — so it tracks Apple exactly.

```swift
extension Font {
    static let imsgLargeTitle = Font.system(.largeTitle, design: .default).weight(.bold)   // 34 / 700
    static let imsgTitle1     = Font.system(.title,      design: .default).weight(.bold)   // 28 / 700
    static let imsgTitle3     = Font.system(.title3,     design: .default).weight(.semibold) // 20 / 600
    static let imsgHeadline   = Font.system(.headline,   design: .default)                 // 17 / 600
    static let imsgBody       = Font.system(.body,       design: .default)                 // 17 / 400 (bubble)
    static let imsgCallout    = Font.system(.callout,    design: .default)                 // 16 / 400
    static let imsgSubhead    = Font.system(.subheadline, design: .default)                // 15 / 400
    static let imsgFootnote   = Font.system(.footnote,   design: .default).weight(.semibold) // 13 / 600
    static let imsgCaption1   = Font.system(.caption,    design: .default)                 // 12 / 400
    static let imsgCaption2   = Font.system(.caption2,   design: .default).weight(.semibold) // 11 / 600

    // Fixed (non-scaling) chrome
    static let imsgReceipt    = Font.system(size: 13, weight: .semibold)
    static let imsgChannel    = Font.system(size: 11, weight: .semibold)
}
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```swift
enum BubbleKind { case incoming, iMessage, sms }

struct ChatBubble: View {
    let text: String
    let kind: BubbleKind
    let isTailEnd: Bool  // last bubble in a same-sender run shows the tail

    private var bg: Color {
        switch kind {
        case .incoming: return .imsgIncomingAdaptive
        case .iMessage: return .imsgBlue
        case .sms:      return .imsgGreen
        }
    }
    private var fg: Color { kind == .incoming ? .imsgTextPrimary : .imsgOnAccent }
    private var isOutgoing: Bool { kind != .incoming }

    var body: some View {
        Text(text)
            .font(.imsgBody)
            .foregroundStyle(fg)
            .padding(.vertical, 8)
            .padding(.horizontal, 14)
            .background(bg, in: BubbleShape(isOutgoing: isOutgoing, tail: isTailEnd))
            .frame(maxWidth: 280, alignment: isOutgoing ? .trailing : .leading)
            .frame(maxWidth: .infinity, alignment: isOutgoing ? .trailing : .leading)
    }
}

/// 19pt rounded bubble with one pinched 6pt corner (tail) on the sender's edge.
struct BubbleShape: Shape {
    let isOutgoing: Bool
    let tail: Bool
    func path(in rect: CGRect) -> Path {
        let big: CGFloat = 19, small: CGFloat = 6
        let tl = big
        let tr = big
        let bl = (!isOutgoing && tail) ? small : big
        let br = ( isOutgoing && tail) ? small : big
        return Path { p in
            p.move(to: CGPoint(x: rect.minX + tl, y: rect.minY))
            p.addLine(to: CGPoint(x: rect.maxX - tr, y: rect.minY))
            p.addArc(center: CGPoint(x: rect.maxX - tr, y: rect.minY + tr), radius: tr, startAngle: .degrees(-90), endAngle: .degrees(0), clockwise: false)
            p.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY - br))
            p.addArc(center: CGPoint(x: rect.maxX - br, y: rect.maxY - br), radius: br, startAngle: .degrees(0), endAngle: .degrees(90), clockwise: false)
            p.addLine(to: CGPoint(x: rect.minX + bl, y: rect.maxY))
            p.addArc(center: CGPoint(x: rect.minX + bl, y: rect.maxY - bl), radius: bl, startAngle: .degrees(90), endAngle: .degrees(180), clockwise: false)
            p.addLine(to: CGPoint(x: rect.minX, y: rect.minY + tl))
            p.addArc(center: CGPoint(x: rect.minX + tl, y: rect.minY + tl), radius: tl, startAngle: .degrees(180), endAngle: .degrees(270), clockwise: false)
        }
    }
}
```

### Tapback Reaction Strip

```swift
struct TapbackStrip: View {
    let onPick: (String) -> Void
    private let glyphs = ["❤️", "👍", "👎", "😂", "‼️", "❓"]

    var body: some View {
        HStack(spacing: 10) {
            ForEach(glyphs, id: \.self) { g in
                Text(g).font(.system(size: 28))
                    .frame(width: 44, height: 44)
                    .contentShape(Rectangle())
                    .onTapGesture { onPick(g) }
            }
        }
        .padding(.horizontal, 14).padding(.vertical, 8)
        .background(
            Capsule().fill(Color(.systemBackground))
                .shadow(color: .black.opacity(0.12), radius: 20, y: 6)
        )
        .overlay(Capsule().strokeBorder(Color(.separator), lineWidth: 0.5))
        .transition(.scale(scale: 0.7).combined(with: .opacity))
    }
}

/// Reaction chip docked over a bubble's owner-near top corner.
struct TapbackChip: View {
    let glyph: String
    var body: some View {
        Text(glyph).font(.system(size: 12))
            .padding(6)
            .background(Circle().fill(Color(.systemBackground)))
            .overlay(Circle().strokeBorder(Color(.separator), lineWidth: 0.5))
            .offset(y: -16)
    }
}
```

### Typing Indicator

```swift
struct TypingIndicator: View {
    @State private var phase = 0.0
    var body: some View {
        HStack(spacing: 4) {
            ForEach(0..<3) { i in
                Circle()
                    .fill(Color(.tertiaryLabel))
                    .frame(width: 7, height: 7)
                    .scaleEffect(scale(for: i))
            }
        }
        .padding(.vertical, 12).padding(.horizontal, 16)
        .background(Color.imsgIncomingAdaptive, in: BubbleShape(isOutgoing: false, tail: true))
        .onAppear {
            withAnimation(.easeInOut(duration: 1.4).repeatForever(autoreverses: false)) { phase = 1 }
        }
    }
    private func scale(for i: Int) -> CGFloat {
        let t = (phase + Double(i) * 0.2).truncatingRemainder(dividingBy: 1)
        return 0.6 + 0.4 * (sin(t * .pi))
    }
}
```

### Delivery Receipt

```swift
struct DeliveryReceipt: View {
    let label: String   // "Delivered" or "Read 9:41 AM"
    var body: some View {
        Text(label)
            .font(.imsgReceipt)
            .foregroundStyle(.secondary)
            .frame(maxWidth: .infinity, alignment: .trailing)
            .padding(.trailing, 6)
            .padding(.top, 2)
            .transition(.opacity)
    }
}
```

### Compose Bar

```swift
struct ComposeBar: View {
    @Binding var text: String
    let isIMessage: Bool
    let onSend: () -> Void

    var body: some View {
        HStack(spacing: 10) {
            Button {} label: {
                Image(systemName: "plus")
                    .font(.system(size: 16, weight: .semibold))
                    .foregroundStyle(.secondary)
                    .frame(width: 30, height: 30)
                    .background(Circle().fill(Color(.systemGray5)))
            }

            HStack {
                TextField(isIMessage ? "iMessage" : "Text Message", text: $text, axis: .vertical)
                    .font(.imsgBody)
                    .lineLimit(1...5)
                    .padding(.horizontal, 14)
            }
            .frame(minHeight: 36)
            .overlay(RoundedRectangle(cornerRadius: 18).strokeBorder(Color(.separator), lineWidth: 1))

            Button(action: onSend) {
                Image(systemName: "arrow.up")
                    .font(.system(size: 15, weight: .semibold))
                    .foregroundStyle(.white)
                    .frame(width: 30, height: 30)
                    .background(Circle().fill(text.isEmpty ? Color(.systemGray3) : (isIMessage ? Color.imsgBlue : Color.imsgGreen)))
            }
            .disabled(text.isEmpty)
            .scaleEffect(text.isEmpty ? 1 : 1)
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 8)
        .background(.ultraThinMaterial)
    }
}
```

### Chat Nav Bar (centered avatar + name)

```swift
struct ChatNavTitle: View {
    let name: String
    let initials: String
    var body: some View {
        VStack(spacing: 3) {
            Circle()
                .fill(LinearGradient(colors: [Color(red: 0.37, green: 0.36, blue: 0.90), .imsgBlue],
                                     startPoint: .top, endPoint: .bottom))
                .frame(width: 34, height: 34)
                .overlay(Text(initials).font(.system(size: 14, weight: .semibold)).foregroundStyle(.white))
            HStack(spacing: 4) {
                Text(name).font(.system(size: 13, weight: .semibold))
                Image(systemName: "chevron.down").font(.system(size: 9, weight: .semibold)).foregroundStyle(.secondary)
            }
        }
    }
}
```

## 4. Bottom Tab Bar

iMessage itself is single-tab (a thread). At the app shell level the Messages app is one tab in iOS; if you build a tabbed host:

```swift
struct MessagesTabHost: View {
    var body: some View {
        TabView {
            ConversationListView()
                .tabItem { Label("Messages", systemImage: "message.fill") }
        }
        .tint(.imsgBlue) // systemBlue active — no tint pill, standard UITabBar
    }
}
```

The signature in-app navigation is the centered nav title above + the blur-backed compose bar below — not a custom tab bar. Use a standard `NavigationStack` + `.toolbar` with `.principal` placement for `ChatNavTitle`.

## 5. Motion

```swift
// Outgoing bubble send — spring up from input field
withAnimation(.spring(response: 0.35, dampingFraction: 0.75)) { messages.append(newMessage) }
// New bubble: .transition(.scale(scale: 0.6).combined(with: .move(edge: .bottom)))

// Tapback present (long-press ~0.5s)
.onLongPressGesture(minimumDuration: 0.5) {
    withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) { showTapback = true }
}

// Tapback dock
withAnimation(.spring(response: 0.25, dampingFraction: 0.6)) { reaction = glyph }

// Typing dots — continuous
withAnimation(.easeInOut(duration: 1.4).repeatForever(autoreverses: false)) { phase = 1 }

// Delivered → Read crossfade
withAnimation(.easeInOut(duration: 0.2)) { receipt = "Read 9:41 AM" }

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: sentCount)        // send
.sensoryFeedback(.impact(flexibility: .soft), trigger: reaction)     // tapback dock
.sensoryFeedback(.success, trigger: effectTriggered)                 // message effect
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Back | `chevron.left` | 17pt |
| Contact disclosure | `chevron.down` | 9pt |
| FaceTime | `video` / `video.fill` | 22pt |
| Compose (list) | `square.and.pencil` | 22pt |
| Attachment | `plus` | 16pt |
| Send | `arrow.up` | 15pt |
| Search | `magnifyingglass` | 15pt |
| Failed message | `exclamationmark.circle.fill` | 17pt |
| Unread badge | `circle.fill` | 10pt |
| Pin (swipe) | `pin.fill` | 20pt |
| Mute (swipe) | `bell.slash.fill` | 20pt |
| Delete (swipe) | `trash.fill` | 20pt |
| Camera (tray) | `camera.fill` | 22pt |
| Photos (tray) | `photo.on.rectangle` | 22pt |
| Audio message | `waveform` / `mic.fill` | 20pt |
| App Store strip | `circle.grid.3x3.fill` | 20pt |

## 7. Dark Mode

```swift
struct IMessageTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.imsgDarkCanvas : Color.imsgCanvas)
            .tint(.imsgBlue)
    }
}
extension View { func imessageTheme() -> some View { modifier(IMessageTheme()) } }
```

- Canvas goes **true black** (`#000000`) in dark — OLED, not charcoal
- Outgoing bubbles stay `#007AFF` / `#34C759` — identical across modes (brand-defining)
- Incoming bubble inverts `#E9E9EB` → `#26262A`; text inverts to white
- Surfaces (`+` button, tapback strip) use `systemGray5` dark `#2C2C2E`
- The floating tapback/context popover loses its visible shadow on black — add a 0.5pt `separator` border as the elevation cue

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `TextField(axis:)` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a `ZStack` growing editor on iOS 16)
- No bundled fonts — system San Francisco via `.system` text styles tracks Apple automatically and is license-free
- Dynamic Type: scale message body, headline, list previews (xSmall → AX5); pin timestamps, "Delivered/Read", tab/toolbar labels, typing dots at fixed sizes
- VoiceOver: label bubbles "You said {text}" / "{Name} said {text}"; announce reactions "{Name} reacted {glyph} to {text}"; expose Tapback as a custom action ("React") so it's reachable without long-press; announce "Delivered"/"Read" as the bubble's value
- Color contrast: white on `#007AFF` and `#34C759` passes WCAG AA; `label` on `#E9E9EB`/`#26262A` passes AA — never put low-contrast text on bubbles
- Differentiate Without Color: the blue/green/gray bubble distinction is reinforced by left/right alignment and the tail direction, so meaning survives color-blindness
- Reduce Motion: disable bubble spring-in (use crossfade), disable screen effects (show a static end-state), keep typing-dot loop subtle or replace with "typing…" text
- Reduce Transparency: swap `.ultraThinMaterial` nav/compose bars for opaque `systemBackground`
- Dark mode: invert per the `imsgDark*` palette — true black `#000000`, never charcoal; add a `separator` border to floating popovers as the dark elevation cue
