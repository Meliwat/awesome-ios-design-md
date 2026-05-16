# Grok (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grok's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let grokCanvas    = Color.black                                  // #000000
    static let grokSurface1  = Color(red: 0.086, green: 0.094, blue: 0.110) // #16181C
    static let grokSurface2  = Color(red: 0.118, green: 0.129, blue: 0.149) // #1E2126
    static let grokSurface3  = Color(red: 0.153, green: 0.165, blue: 0.180) // #272A2E
    static let grokDivider   = Color(red: 0.184, green: 0.200, blue: 0.212) // #2F3336

    // MARK: - Text
    static let grokTextPrimary   = Color(red: 0.906, green: 0.914, blue: 0.918) // #E7E9EA
    static let grokTextSecondary = Color(red: 0.443, green: 0.463, blue: 0.482) // #71767B
    static let grokTextTertiary  = Color(red: 0.302, green: 0.318, blue: 0.337) // #4D5156

    // MARK: - Functional / Brand
    static let grokAccentWhite   = Color.white                                  // #FFFFFF
    static let grokPressedWhite  = Color(red: 0.843, green: 0.859, blue: 0.863) // #D7DBDC
    static let grokLinkBlue      = Color(red: 0.114, green: 0.608, blue: 0.941) // #1D9BF0
    static let grokLinkPressed   = Color(red: 0.102, green: 0.549, blue: 0.847) // #1A8CD8
    static let grokSuccess       = Color(red: 0.000, green: 0.729, blue: 0.486) // #00BA7C
    static let grokError         = Color(red: 0.957, green: 0.129, blue: 0.180) // #F4212E
}
```

## 2. Typography

Inter is open-source (SIL OFL). Bundle the TTFs via `Info.plist` (`UIAppFonts`) with the slashed-zero stylistic feature enabled. Fall back to `.system(design: .default)`.

```swift
extension Font {
    static let grokScreenTitle  = Font.custom("Inter-Bold",     size: 28)
    static let grokSectionHeader = Font.custom("Inter-Bold",    size: 20)
    static let grokConvoTitle   = Font.custom("Inter-SemiBold", size: 17)
    static let grokBody         = Font.custom("Inter-Regular",  size: 16)
    static let grokPromptInput  = Font.custom("Inter-Regular",  size: 16)
    static let grokModePill     = Font.custom("Inter-SemiBold", size: 14)
    static let grokCiteAuthor   = Font.custom("Inter-Bold",     size: 14)
    static let grokCiteMeta     = Font.custom("Inter-Regular",  size: 13)
    static let grokBodySmall    = Font.custom("Inter-Regular",  size: 14)
    static let grokCode         = Font.system(size: 13.5, design: .monospaced)
    static let grokButton       = Font.custom("Inter-SemiBold", size: 15)
    static let grokCaption      = Font.custom("Inter-Regular",  size: 12)
    static let grokLabelUpper   = Font.custom("Inter-Bold",     size: 11)
}

// Enable the slashed zero on numeral-bearing Text (technical identity)
extension View {
    func grokSlashedZero() -> some View {
        self.font(.grokBody).fontFeature("zero")
    }
}

// Convenience until Inter is registered:
extension Font {
    static func grok(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### User Message Bubble

```swift
struct GrokUserBubble: View {
    let text: String

    var body: some View {
        HStack {
            Spacer(minLength: 48)
            Text(text)
                .font(.grokBody)
                .foregroundStyle(Color.grokTextPrimary)
                .padding(.vertical, 12)
                .padding(.horizontal, 16)
                .background(
                    UnevenRoundedRectangle(
                        cornerRadii: .init(topLeading: 20, bottomLeading: 20,
                                           bottomTrailing: 6, topTrailing: 20)
                    )
                    .fill(Color.grokSurface1)
                )
        }
        .frame(maxWidth: .infinity, alignment: .trailing)
    }
}
```

### Assistant Response (plain — no bubble) with Streaming Cursor

```swift
struct GrokAssistantResponse: View {
    let text: String
    let isStreaming: Bool
    @State private var cursorVisible = true

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            Image("grok-glyph") // 24pt mark, once per response
                .resizable().frame(width: 24, height: 24)
                .foregroundStyle(Color.grokTextPrimary)

            (Text(text)
                + (isStreaming
                   ? Text("▍").foregroundColor(cursorVisible ? .grokTextPrimary : .clear)
                   : Text("")))
                .font(.grokBody)
                .foregroundStyle(Color.grokTextPrimary)
                .lineSpacing(6) // ~1.55 line-height at 16pt
                .fixedSize(horizontal: false, vertical: true)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(.horizontal, 16)
        .onAppear { startBlink() }
    }

    private func startBlink() {
        guard isStreaming else { return }
        withAnimation(.easeInOut(duration: 0).repeatForever(autoreverses: true)) {}
        Timer.scheduledTimer(withTimeInterval: 0.53, repeats: true) { t in
            if !isStreaming { t.invalidate(); cursorVisible = false; return }
            cursorVisible.toggle()
        }
    }
}
```

### X Post Citation Card (signature)

```swift
struct XCitationCard: View {
    let author: String
    let handle: String
    let timeAgo: String
    let isVerified: Bool
    let postText: String
    let replies: Int
    let reposts: Int
    let likes: Int
    let onOpen: () -> Void

    @State private var pressed = false

    var body: some View {
        Button(action: onOpen) {
            VStack(alignment: .leading, spacing: 10) {
                HStack(spacing: 8) {
                    Circle().fill(Color.grokSurface3).frame(width: 28, height: 28)
                    Text(author).font(.grokCiteAuthor).foregroundStyle(Color.grokTextPrimary)
                    if isVerified {
                        Image(systemName: "checkmark.seal.fill")
                            .font(.system(size: 14)).foregroundStyle(Color.grokLinkBlue)
                    }
                    Text("@\(handle) · \(timeAgo)")
                        .font(.grokCiteMeta).foregroundStyle(Color.grokTextSecondary)
                    Spacer()
                    Image("x-glyph").resizable().frame(width: 16, height: 16)
                        .foregroundStyle(Color.grokTextSecondary)
                }
                Text(postText)
                    .font(.grokBodySmall).foregroundStyle(Color.grokTextPrimary)
                    .lineLimit(4)
                HStack(spacing: 20) {
                    metric("bubble.left", replies)
                    metric("arrow.2.squarepath", reposts)
                    metric("heart", likes)
                }
                .font(.grokCaption).foregroundStyle(Color.grokTextSecondary)
            }
            .padding(14)
            .background(RoundedRectangle(cornerRadius: 16)
                .fill(pressed ? Color.grokSurface3 : Color.grokSurface2))
            .overlay(RoundedRectangle(cornerRadius: 16)
                .strokeBorder(pressed ? Color.grokLinkBlue : Color.grokDivider, lineWidth: 1))
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: 0, pressing: { pressed = $0 }, perform: {})
    }

    private func metric(_ symbol: String, _ n: Int) -> some View {
        HStack(spacing: 4) {
            Image(systemName: symbol).font(.system(size: 14))
            Text("\(n)")
        }
    }
}
```

### Send Button (state machine)

```swift
struct GrokSendButton: View {
    enum State { case disabled, enabled, streaming }
    let state: State
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Group {
                switch state {
                case .streaming:
                    Image(systemName: "stop.fill")
                        .foregroundStyle(Color.grokTextPrimary)
                default:
                    Image(systemName: "arrow.up")
                        .foregroundStyle(state == .enabled ? Color.black : Color.grokTextSecondary)
                }
            }
            .font(.system(size: 16, weight: .semibold))
            .frame(width: 32, height: 32)
            .background(Circle().fill(fill))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: state)
        .buttonStyle(GrokPressableStyle(scale: 0.92))
        .disabled(state == .disabled)
    }

    private var fill: Color {
        switch state {
        case .disabled:  return .grokSurface3
        case .enabled:   return .grokAccentWhite
        case .streaming: return .grokSurface3
        }
    }
}

struct GrokPressableStyle: ButtonStyle {
    var scale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? scale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Mode Toggle (Regular / Fun)

```swift
struct GrokModeToggle: View {
    @Binding var isFun: Bool
    @Namespace private var ns

    var body: some View {
        HStack(spacing: 0) {
            segment("Regular", active: !isFun) { isFun = false }
            segment("Fun",     active:  isFun) { isFun = true }
        }
        .padding(3)
        .background(Capsule().fill(Color.grokSurface1))
        .overlay(Capsule().strokeBorder(Color.grokDivider, lineWidth: 1))
        .sensoryFeedback(.selection, trigger: isFun)
    }

    private func segment(_ label: String, active: Bool, tap: @escaping () -> Void) -> some View {
        Text(label)
            .font(.grokModePill)
            .foregroundStyle(active ? (label == "Fun" ? Color.grokLinkBlue : .black)
                                    : Color.grokTextSecondary)
            .padding(.vertical, 7).padding(.horizontal, 14)
            .background {
                if active {
                    Capsule()
                        .fill(label == "Fun" ? Color.grokSurface1 : Color.grokAccentWhite)
                        .matchedGeometryEffect(id: "pill", in: ns)
                }
            }
            .contentShape(Rectangle())
            .onTapGesture { withAnimation(.spring(response: 0.2, dampingFraction: 0.8)) { tap() } }
    }
}
```

### Prompt Bar

```swift
struct GrokPromptBar: View {
    @Binding var text: String
    @FocusState private var focused: Bool
    let sendState: GrokSendButton.State
    let onSend: () -> Void

    var body: some View {
        HStack(alignment: .bottom, spacing: 8) {
            Image(systemName: "paperclip")
                .font(.system(size: 18)).foregroundStyle(Color.grokTextSecondary)
            TextField("Ask Grok anything", text: $text, axis: .vertical)
                .font(.grokPromptInput)
                .foregroundStyle(Color.grokTextPrimary)
                .tint(Color.grokTextPrimary)
                .lineLimit(1...5)
                .focused($focused)
            GrokSendButton(state: sendState, action: onSend)
        }
        .padding(.horizontal, 12).padding(.vertical, 8)
        .background(RoundedRectangle(cornerRadius: 24).fill(Color.grokSurface1))
        .overlay(RoundedRectangle(cornerRadius: 24)
            .strokeBorder(focused ? Color(red: 0.243, green: 0.255, blue: 0.275) : Color.grokDivider,
                          lineWidth: 1))
        .padding(.horizontal, 16)
    }
}
```

## 4. Top Bar (no tab bar)

Grok is single-surface — there is no `TabView`. Use a `ZStack`/`Scaffold`-style root with the mode toggle centered in the bar.

```swift
struct GrokRoot: View {
    @State private var isFun = false
    @State private var showHistory = false

    var body: some View {
        VStack(spacing: 0) {
            HStack {
                Button { showHistory = true } label: {
                    Image(systemName: "line.3.horizontal")
                        .font(.system(size: 20)).foregroundStyle(Color.grokTextPrimary)
                }
                Spacer()
                GrokModeToggle(isFun: $isFun)
                Spacer()
                Button { /* new chat */ } label: {
                    Image(systemName: "square.and.pencil")
                        .font(.system(size: 20)).foregroundStyle(Color.grokTextPrimary)
                }
            }
            .padding(.horizontal, 16).frame(height: 44)
            .background(Color.grokCanvas)

            ConversationScroll() // user bubbles + assistant responses + citations
        }
        .background(Color.grokCanvas.ignoresSafeArea())
        .sheet(isPresented: $showHistory) { GrokHistoryPanel() }
    }
}
```

## 5. Motion

```swift
// Streaming cursor — drive a Bool with a 0.53s repeating timer (see GrokAssistantResponse)

// Send → Stop morph
.contentTransition(.symbolEffect(.replace))   // arrow.up ↔ stop.fill

// Mode toggle slide
withAnimation(.spring(response: 0.2, dampingFraction: 0.8)) { isFun.toggle() }

// Copy confirm
.symbolEffect(.bounce, value: didCopy)        // doc.on.doc → checkmark, tint .grokSuccess

// "Searching X…" pulse
.opacity(searching ? pulse : 1)
.animation(.easeInOut(duration: 0.9).repeatForever(autoreverses: true), value: pulse)
```

Haptics via the modern `.sensoryFeedback` API (iOS 17+): `.impact(flexibility: .soft)` on send, `.selection` on the mode toggle, `.success` on copy-confirmed.

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send (enabled/disabled) | `arrow.up` | 16pt |
| Stop generation | `stop.fill` | 16pt |
| Attach | `paperclip` | 18pt |
| New chat | `square.and.pencil` | 20pt |
| History / menu | `line.3.horizontal` | 20pt |
| Verified (citation) | `checkmark.seal.fill` | 14pt |
| Reply (citation) | `bubble.left` | 14pt |
| Repost (citation) | `arrow.2.squarepath` | 14pt |
| Like (citation) | `heart` | 14pt |
| Copy | `doc.on.doc` → `checkmark` | 18pt |
| Regenerate | `arrow.clockwise` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Thumbs up / down | `hand.thumbsup` / `hand.thumbsdown` | 18pt |
| Searching X | `globe` | 16pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `UnevenRoundedRectangle` need iOS 17; fall back to `UIImpactFeedbackGenerator` + a manual masked shape on 16)
- Slashed zero: register Inter with the `zero` OpenType feature; expose `.fontFeature("zero")` on numeral-bearing text
- Dynamic Type: scale assistant body, user message, conversation titles; pin citation meta (13pt), the disclaimer caption (12pt), the mode-pill label, and the Send glyph
- VoiceOver: label the Send button by state ("Send message" / "Stop generating"); merge citation-card text into one element with action "Opens post on X"; announce streaming completion via `.accessibilityLabel` update
- Reduce Motion: replace the token-by-token reveal with a single fade-in of the completed response and disable the cursor blink
- Contrast: `#71767B` on `#000000` is borderline at 13pt — bump secondary text toward `#8B8F94` when "Increase Contrast" is on
- Dark-only: do not provide a light `ColorScheme`; lock the app to `.dark`
