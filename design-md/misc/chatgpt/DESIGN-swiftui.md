# ChatGPT (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates ChatGPT's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas
    static let gptCanvas          = Color.white                                    // #FFFFFF
    static let gptDarkCanvas      = Color(red: 0.129, green: 0.129, blue: 0.129)  // #212121

    // MARK: - Sidebar
    static let gptSidebarLight    = Color(red: 0.976, green: 0.976, blue: 0.976) // #F9F9F9
    static let gptSidebarDark     = Color(red: 0.094, green: 0.094, blue: 0.094) // #181818
    static let gptSidebarActive   = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC
    static let gptSidebarActiveDark = Color(red: 0.184, green: 0.184, blue: 0.184) // #2F2F2F
    static let gptDivider         = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let gptDividerDark     = Color(red: 0.259, green: 0.259, blue: 0.259) // #424242

    // MARK: - Text
    static let gptTextPrimary     = Color(red: 0.051, green: 0.051, blue: 0.051) // #0D0D0D
    static let gptTextSecondary   = Color(red: 0.404, green: 0.404, blue: 0.404) // #676767
    static let gptTextTertiary    = Color(red: 0.557, green: 0.557, blue: 0.557) // #8E8E8E
    static let gptDarkTextPrimary = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC
    static let gptDarkTextSecondary = Color(red: 0.706, green: 0.706, blue: 0.706) // #B4B4B4

    // MARK: - User Bubble
    static let gptUserBubbleLight = Color(red: 0.969, green: 0.969, blue: 0.973) // #F7F7F8
    static let gptUserBubbleDark  = Color(red: 0.184, green: 0.184, blue: 0.184) // #2F2F2F

    // MARK: - Code
    static let gptCodeBlockLight  = Color(red: 0.969, green: 0.969, blue: 0.973) // #F7F7F8
    static let gptCodeBlockDark   = Color(red: 0.118, green: 0.118, blue: 0.118) // #1E1E1E
    static let gptCodeInlineLight = Color(red: 0.941, green: 0.941, blue: 0.941) // #F0F0F0
    static let gptCodeInlineDark  = Color(red: 0.259, green: 0.259, blue: 0.259) // #424242

    // MARK: - Send Button
    static let gptSendLight       = Color(red: 0.051, green: 0.051, blue: 0.051) // #0D0D0D
    static let gptSendDark        = Color.white                                    // #FFFFFF
    static let gptSendDisabled    = Color(red: 0.800, green: 0.800, blue: 0.800) // #CCCCCC

    // MARK: - Semantic
    static let gptLinkBlue        = Color(red: 0.165, green: 0.498, blue: 1.000) // #2A7FFF
    static let gptLegacyGreen     = Color(red: 0.063, green: 0.639, blue: 0.498) // #10A37F (legacy, mostly retired)
    static let gptErrorRed        = Color(red: 0.898, green: 0.243, blue: 0.243) // #E53E3E

    // MARK: - Voice Mode Sphere
    static let gptVoiceBlue1      = Color(red: 0.231, green: 0.510, blue: 0.965) // #3B82F6
    static let gptVoiceBlue2      = Color(red: 0.376, green: 0.647, blue: 0.980) // #60A5FA
    static let gptVoiceBlue3      = Color(red: 0.576, green: 0.773, blue: 0.988) // #93C5FD
}
```

## 2. Typography

Söhne is commercially licensed — acquire from Klim Type Foundry if you intend to ship. For prototypes, fall back to Inter (SIL OFL) or SF Pro (system). Bundle TTFs via `Info.plist` / `UIAppFonts` for Söhne and Inter.

```swift
extension Font {
    static let gptBody         = Font.custom("Söhne-Buch",      size: 16).weight(.regular)
    static let gptBodyCompact  = Font.custom("Söhne-Buch",      size: 15).weight(.regular)
    static let gptH1           = Font.custom("Söhne-Halbfett",  size: 24).weight(.semibold)
    static let gptH2           = Font.custom("Söhne-Halbfett",  size: 20).weight(.semibold)
    static let gptH3           = Font.custom("Söhne-Halbfett",  size: 17).weight(.semibold)
    static let gptModelChip    = Font.custom("Söhne-Kraftig",   size: 14).weight(.medium)
    static let gptSidebarTitle = Font.custom("Söhne-Kraftig",   size: 15).weight(.medium)
    static let gptSidebarSection = Font.custom("Söhne-Kraftig", size: 12).weight(.medium)
    static let gptButton       = Font.custom("Söhne-Kraftig",   size: 14).weight(.medium)
    static let gptMeta         = Font.custom("Söhne-Buch",      size: 13).weight(.regular)
    static let gptPlaceholder  = Font.custom("Söhne-Buch",      size: 16).weight(.regular)

    // Code
    static let gptCodeInline   = Font.custom("Menlo-Regular",   size: 14)
    static let gptCodeBlock    = Font.custom("Menlo-Regular",   size: 13)

    // Inter fallback
    static func gptInter(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        Font.custom("Inter-\(weight == .medium ? "Medium" : weight == .semibold ? "SemiBold" : "Regular")", size: size).weight(weight)
    }

    // System fallback (SF Pro)
    static func gptSystem(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Send Button (The Black/White Circle)

```swift
struct SendButton: View {
    let isEnabled: Bool
    let isGenerating: Bool
    let action: () -> Void

    @Environment(\.colorScheme) var scheme

    var circleColor: Color {
        if !isEnabled { return .gptSendDisabled }
        return scheme == .dark ? .gptSendDark : .gptSendLight
    }
    var iconColor: Color {
        scheme == .dark ? .gptSendLight : .gptSendDark
    }

    var body: some View {
        Button(action: action) {
            Image(systemName: isGenerating ? "stop.fill" : "arrow.up")
                .font(.system(size: 16, weight: .bold))
                .foregroundStyle(iconColor)
                .frame(width: 32, height: 32)
                .background(Circle().fill(circleColor))
        }
        .disabled(!isEnabled && !isGenerating)
        .buttonStyle(SendPressStyle())
        .sensoryFeedback(.impact(flexibility: .solid), trigger: isGenerating)
    }
}

struct SendPressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.94 : 1.0)
            .animation(.spring(response: 0.2, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### User Message Bubble

```swift
struct UserMessageBubble: View {
    let text: String
    let attachmentUrl: String?

    @Environment(\.colorScheme) var scheme

    var body: some View {
        HStack {
            Spacer(minLength: 60)  // push to right, max-width ~80%
            VStack(alignment: .trailing, spacing: 8) {
                if let attachmentUrl {
                    AttachmentTile(url: attachmentUrl)
                }
                Text(text)
                    .font(.gptBody)
                    .foregroundStyle(scheme == .dark ? Color.gptDarkTextPrimary : Color.gptTextPrimary)
                    .padding(.horizontal, 14)
                    .padding(.vertical, 10)
                    .background(
                        UnevenRoundedRectangle(
                            topLeadingRadius: 18, bottomLeadingRadius: 18, bottomTrailingRadius: 4, topTrailingRadius: 18
                        )
                        .fill(scheme == .dark ? Color.gptUserBubbleDark : Color.gptUserBubbleLight)
                    )
            }
        }
        .padding(.horizontal, 16)
    }
}

struct AttachmentTile: View {
    let url: String

    var body: some View {
        HStack(spacing: 8) {
            RoundedRectangle(cornerRadius: 4)
                .fill(Color.gptCodeInlineLight)
                .frame(width: 40, height: 40)
                .overlay(Image(systemName: "doc.fill").foregroundStyle(Color.gptTextSecondary))
            Text(URL(string: url)?.lastPathComponent ?? "file")
                .font(.gptMeta)
                .foregroundStyle(Color.gptTextSecondary)
                .lineLimit(1)
        }
        .padding(8)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.gptCodeBlockLight))
    }
}
```

### Assistant Message (No Bubble, with Feedback Row)

```swift
struct AssistantMessage: View {
    let content: String  // markdown-rendered
    let onRegenerate: () -> Void
    let onCopy: () -> Void
    let onThumbUp: () -> Void
    let onThumbDown: () -> Void

    @State private var thumbState: ThumbState = .neutral
    enum ThumbState { case neutral, up, down }

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            // Optional sparkle avatar
            HStack(alignment: .top, spacing: 8) {
                Image(systemName: "sparkles")
                    .font(.system(size: 18))
                    .foregroundStyle(Color.gptTextPrimary)
                    .frame(width: 24, height: 24)

                // Replace this Text with a MarkdownRenderer in production
                Text(LocalizedStringKey(content))
                    .font(.gptBody)
                    .foregroundStyle(Color.gptTextPrimary)
                    .lineSpacing(8)
            }

            // Feedback row
            HStack(spacing: 8) {
                FeedbackIconButton(icon: "arrow.triangle.2.circlepath", action: onRegenerate)
                FeedbackIconButton(icon: "doc.on.doc", action: onCopy)
                FeedbackIconButton(
                    icon: thumbState == .up ? "hand.thumbsup.fill" : "hand.thumbsup",
                    action: { thumbState = .up; onThumbUp() }
                )
                FeedbackIconButton(
                    icon: thumbState == .down ? "hand.thumbsdown.fill" : "hand.thumbsdown",
                    action: { thumbState = .down; onThumbDown() }
                )
            }
            .padding(.leading, 32)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 8)
    }
}

struct FeedbackIconButton: View {
    let icon: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: icon)
                .font(.system(size: 16))
                .foregroundStyle(Color.gptTextSecondary)
                .frame(width: 32, height: 32)
                .background(Circle().fill(Color.clear))
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
    }
}
```

### Code Block (with Copy Button)

```swift
struct CodeBlockView: View {
    let language: String
    let code: String
    @State private var showCopied = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text(language)
                    .font(.system(size: 12, weight: .medium, design: .monospaced))
                    .foregroundStyle(Color.gptTextSecondary)
                Spacer()
                Button {
                    UIPasteboard.general.string = code
                    withAnimation { showCopied = true }
                    DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
                        withAnimation { showCopied = false }
                    }
                } label: {
                    HStack(spacing: 4) {
                        Image(systemName: showCopied ? "checkmark" : "doc.on.doc")
                            .font(.system(size: 12))
                        Text(showCopied ? "Copied!" : "Copy")
                            .font(.gptButton)
                    }
                    .foregroundStyle(Color.gptTextSecondary)
                }
            }
            .padding(.horizontal, 12)
            .padding(.vertical, 8)
            .background(Color.gptCodeBlockLight)
            .overlay(Rectangle().fill(Color.gptDivider).frame(height: 1), alignment: .bottom)

            ScrollView(.horizontal, showsIndicators: false) {
                Text(code)
                    .font(.gptCodeBlock)
                    .foregroundStyle(Color.gptTextPrimary)
                    .padding(12)
            }
        }
        .background(Color.gptCodeBlockLight)
        .clipShape(RoundedRectangle(cornerRadius: 8))
        .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.gptDivider, lineWidth: 1))
        .padding(.horizontal, 16)
    }
}
```

### Model Selector Chip

```swift
struct ModelSelectorChip: View {
    let modelName: String
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 6) {
                Image(systemName: "sparkles")
                    .font(.system(size: 14))
                    .foregroundStyle(Color.gptTextPrimary)
                Text(modelName)
                    .font(.gptModelChip)
                    .foregroundStyle(Color.gptTextPrimary)
                Image(systemName: "chevron.down")
                    .font(.system(size: 10))
                    .foregroundStyle(Color.gptTextSecondary)
            }
            .padding(.horizontal, 10)
            .padding(.vertical, 6)
            .background(Capsule().fill(Color.clear))
            .overlay(Capsule().strokeBorder(Color.gptDivider, lineWidth: 1))
        }
    }
}
```

### Message Composer

```swift
struct Composer: View {
    @Binding var text: String
    let onSend: () -> Void
    let onVoice: () -> Void
    let onAttach: () -> Void
    let onWebSearch: () -> Void

    var isEmpty: Bool { text.trimmingCharacters(in: .whitespaces).isEmpty }

    var body: some View {
        HStack(alignment: .bottom, spacing: 8) {
            Button(action: onAttach) {
                Image(systemName: "plus")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.gptTextSecondary)
                    .frame(width: 40, height: 40)
            }

            TextField("Message ChatGPT…", text: $text, axis: .vertical)
                .font(.gptBody)
                .foregroundStyle(Color.gptTextPrimary)
                .lineLimit(1...6)
                .padding(.vertical, 10)

            if isEmpty {
                HStack(spacing: 4) {
                    Button(action: onWebSearch) {
                        Image(systemName: "globe")
                            .font(.system(size: 18))
                            .foregroundStyle(Color.gptTextSecondary)
                            .frame(width: 40, height: 40)
                    }
                    Button(action: onVoice) {
                        Image(systemName: "mic.fill")
                            .font(.system(size: 18))
                            .foregroundStyle(Color.gptTextSecondary)
                            .frame(width: 32, height: 32)
                            .background(Circle().strokeBorder(Color.gptDivider, lineWidth: 1))
                    }
                }
            } else {
                SendButton(isEnabled: true, isGenerating: false, action: onSend)
            }
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 8)
        .background(
            RoundedRectangle(cornerRadius: 24)
                .fill(Color.gptCanvas)
                .overlay(RoundedRectangle(cornerRadius: 24).strokeBorder(Color.gptDivider, lineWidth: 1))
        )
        .padding(.horizontal, 16)
        .padding(.bottom, 8)
    }
}
```

### Voice Mode (Full-Screen Sphere)

```swift
struct VoiceModeView: View {
    @Binding var isShown: Bool
    @State private var pulseScale: CGFloat = 1.0

    var body: some View {
        ZStack {
            Color.black.ignoresSafeArea()

            Circle()
                .fill(
                    RadialGradient(
                        colors: [.gptVoiceBlue1, .gptVoiceBlue2, .gptVoiceBlue3],
                        center: .center,
                        startRadius: 50,
                        endRadius: 180
                    )
                )
                .frame(width: 280, height: 280)
                .scaleEffect(pulseScale)
                .shadow(color: .gptVoiceBlue1.opacity(0.4), radius: 40)
                .onAppear {
                    withAnimation(.easeInOut(duration: 2).repeatForever(autoreverses: true)) {
                        pulseScale = 1.05
                    }
                }

            VStack {
                Spacer()
                Text("ChatGPT is listening…")
                    .font(.gptMeta)
                    .foregroundStyle(.white.opacity(0.7))

                HStack {
                    Button("Mute") { }
                        .font(.gptButton)
                        .foregroundStyle(.white)

                    Spacer()

                    Button { isShown = false } label: {
                        Image(systemName: "xmark")
                            .font(.system(size: 18, weight: .bold))
                            .foregroundStyle(.white)
                            .frame(width: 44, height: 44)
                            .background(Circle().fill(.white.opacity(0.2)))
                    }
                }
                .padding(.horizontal, 24)
                .padding(.bottom, 40)
            }
        }
        .transition(.opacity.combined(with: .scale(scale: 0.9)))
        .sensoryFeedback(.impact(flexibility: .solid), trigger: isShown)
    }
}
```

### Sidebar (Conversation History)

```swift
struct GPTSidebar: View {
    let sections: [ConversationSection]

    struct ConversationSection: Identifiable {
        let id = UUID()
        let title: String  // "Today", "Yesterday", etc.
        let chats: [ConversationItem]
    }
    struct ConversationItem: Identifiable {
        let id = UUID()
        let title: String
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // New chat button
            Button { } label: {
                HStack(spacing: 10) {
                    Image(systemName: "square.and.pencil").font(.system(size: 18))
                    Text("New chat").font(.gptButton)
                    Spacer()
                }
                .padding(.horizontal, 12)
                .padding(.vertical, 10)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.gptSidebarActive))
            }
            .padding(12)

            // Search
            HStack {
                Image(systemName: "magnifyingglass").foregroundStyle(Color.gptTextSecondary)
                TextField("Search chats", text: .constant(""))
                    .font(.gptMeta)
            }
            .padding(10)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.gptSidebarActive))
            .padding(.horizontal, 12)

            // Conversation list
            ScrollView {
                LazyVStack(alignment: .leading, spacing: 0) {
                    ForEach(sections) { section in
                        Text(section.title)
                            .font(.gptSidebarSection)
                            .foregroundStyle(Color.gptTextSecondary)
                            .padding(.horizontal, 12)
                            .padding(.top, 16)
                            .padding(.bottom, 4)

                        ForEach(section.chats) { chat in
                            ChatSidebarRow(title: chat.title, isActive: false)
                        }
                    }
                }
            }
        }
        .frame(width: 260)
        .background(Color.gptSidebarLight)
    }
}

struct ChatSidebarRow: View {
    let title: String
    let isActive: Bool

    var body: some View {
        HStack {
            Text(title)
                .font(.gptSidebarTitle)
                .foregroundStyle(Color.gptTextPrimary)
                .lineLimit(1)
            Spacer()
        }
        .padding(.horizontal, 12)
        .frame(height: 40)
        .background(isActive ? Color.gptSidebarActive : Color.clear)
    }
}
```

### Typing Indicator (3-dot pulse)

```swift
struct TypingIndicator: View {
    @State private var dot1: CGFloat = 0.3
    @State private var dot2: CGFloat = 0.3
    @State private var dot3: CGFloat = 0.3

    var body: some View {
        HStack(spacing: 6) {
            Circle().fill(Color.gptTextSecondary).frame(width: 8, height: 8).opacity(dot1)
            Circle().fill(Color.gptTextSecondary).frame(width: 8, height: 8).opacity(dot2)
            Circle().fill(Color.gptTextSecondary).frame(width: 8, height: 8).opacity(dot3)
        }
        .onAppear { animate() }
    }

    private func animate() {
        withAnimation(.easeInOut(duration: 0.6).repeatForever(autoreverses: true)) { dot1 = 1.0 }
        withAnimation(.easeInOut(duration: 0.6).repeatForever(autoreverses: true).delay(0.2)) { dot2 = 1.0 }
        withAnimation(.easeInOut(duration: 0.6).repeatForever(autoreverses: true).delay(0.4)) { dot3 = 1.0 }
    }
}
```

## 4. Motion

```swift
// Send button
.sensoryFeedback(.impact(flexibility: .solid), trigger: messageSent)

// Voice mode entry
.sensoryFeedback(.impact(flexibility: .solid), trigger: voiceModeShown)
// .transition(.opacity.combined(with: .scale(scale: 0.9)))

// Voice mode sphere pulse
// withAnimation(.easeInOut(duration: 2).repeatForever(autoreverses: true)) { pulseScale = 1.05 }

// Thumbs feedback
.sensoryFeedback(.impact(flexibility: .soft), trigger: thumbState)

// Copy button
// Swap icon + text for 1 second ("Copied!") then revert

// Streaming — assistant text appears character-by-character
// No animation — just append to string as tokens arrive
```

## 5. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send | `arrow.up` | 16pt |
| Stop generating | `stop.fill` | 16pt |
| Voice (mic) | `mic.fill` / `waveform` | 18pt |
| Attach (+) | `plus` | 20pt |
| Web search | `globe` | 18pt |
| New chat | `square.and.pencil` | 18pt |
| Sidebar toggle | `sidebar.leading` | 24pt |
| Regenerate | `arrow.triangle.2.circlepath` | 16pt |
| Copy | `doc.on.doc` | 16pt |
| Thumbs up | `hand.thumbsup` / `hand.thumbsup.fill` | 16pt |
| Thumbs down | `hand.thumbsdown` / `hand.thumbsdown.fill` | 16pt |
| Sparkle (assistant avatar) | `sparkles` | 18pt |
| Model chip chevron | `chevron.down` | 10pt |
| Close (X) | `xmark` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Settings | `gear` | 22pt |
| More | `ellipsis` | 18pt |
| Share | `square.and.arrow.up` | 18pt |

## 6. Dark Mode Support

```swift
struct GPTTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.gptDarkCanvas : Color.gptCanvas)
            .foregroundStyle(scheme == .dark ? Color.gptDarkTextPrimary : Color.gptTextPrimary)
    }
}

extension View {
    func gptTheme() -> some View { modifier(GPTTheme()) }
}
```

## 7. Markdown Rendering

SwiftUI's built-in `Text(LocalizedStringKey(...))` supports basic markdown (bold, italic, links). For full markdown with headings, code blocks, tables, and LaTeX, use a library like [swift-markdown-ui](https://github.com/gonzalezreal/swift-markdown-ui):

```swift
import MarkdownUI

Markdown(content)
    .markdownTextStyle { FontFamily(.custom("Söhne-Buch")); FontSize(16); ForegroundColor(.gptTextPrimary) }
    .markdownBlockStyle(\.codeBlock) { configuration in
        CodeBlockView(language: configuration.language ?? "", code: configuration.content)
    }
```

For LaTeX, integrate KaTeX or MathJax via WebView for inline rendering.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Söhne requires commercial license from Klim Type Foundry; Inter is Apache/OFL and free; fall back to SF Pro (system) if neither bundled
- Dynamic Type: support on chat body, markdown headings, user bubble text; keep model chip, sidebar section titles, timestamps, code block labels FIXED
- VoiceOver: label user messages as "You: {content}"; assistant as "ChatGPT: {content}"; voice mode as "Voice conversation active, ChatGPT is listening"
- Reduce Motion: skip voice-mode sphere pulse (show static sphere); skip typing indicator animation (show static 3 dots with "Generating..." label); skip send button scale
- High Contrast: boost secondary text from `#676767` to `#3E3E3E` when `UIAccessibility.isDarkerSystemColorsEnabled`
- Audio mode: ensure voice mode works with AVAudioSession correctly set (`.playAndRecord` category); handle mic permission gracefully
- Assistive Touch: add `.accessibilityAction(named: "Regenerate")` etc. on assistant message for rotor
- Content flags: if a message is a refusal or content-policy response, render with slight warning orange tint `#D97706` on a small note
- Dark mode: full support required; the redesign leans into true-black aesthetics on dark
