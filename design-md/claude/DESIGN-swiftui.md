# Claude (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Claude's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the asterisk-star logomark `Shape`, themed components for the assistant message block, the chat input, code blocks, and the streaming cursor.

Targets iOS 17+. The modern `.sensoryFeedback`, `phaseAnimator` for streaming cursor, and `matchedGeometryEffect` on artifact expansion all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas (the Claude paper)
    static let claudeCream     = Color(red: 0.973, green: 0.957, blue: 0.929) // #F8F4ED canvas
    static let claudePaper     = Color(red: 0.984, green: 0.976, blue: 0.957) // #FBF9F4 elevated surfaces
    static let claudeSurface1  = Color(red: 0.941, green: 0.918, blue: 0.878) // #F0EAE0 user pill, callouts
    static let claudeSurface2  = Color(red: 0.910, green: 0.878, blue: 0.824) // #E8E0D2 pressed/chips
    static let claudeSand      = Color(red: 0.867, green: 0.824, blue: 0.741) // #DDD2BD divider

    // MARK: - Text (warm-tinted ink)
    static let claudeInk       = Color(red: 0.176, green: 0.145, blue: 0.125) // #2D2520 primary
    static let claudeGraphite  = Color(red: 0.353, green: 0.310, blue: 0.267) // #5A4F44 secondary
    static let claudeStone     = Color(red: 0.541, green: 0.494, blue: 0.447) // #8A7E72 tertiary
    static let claudeBone      = Color(red: 0.710, green: 0.671, blue: 0.620) // #B5AB9E disabled

    // MARK: - Claude Orange (the signature accent)
    static let claudeOrange       = Color(red: 0.851, green: 0.467, blue: 0.341) // #D97757
    static let claudeOrangePressed = Color(red: 0.745, green: 0.384, blue: 0.259) // #BE6242
    static let claudeOrangeSoft   = Color(red: 0.949, green: 0.867, blue: 0.816) // #F2DDD0 active chip

    // MARK: - Code & Syntax (warm palette)
    static let claudeCodeBg       = Color(red: 0.122, green: 0.106, blue: 0.086) // #1F1B16
    static let claudeCodeFg       = Color(red: 0.910, green: 0.878, blue: 0.824) // #E8E0D2
    static let claudeSyntaxKey    = Color(red: 0.851, green: 0.467, blue: 0.341) // #D97757 keywords
    static let claudeSyntaxString = Color(red: 0.498, green: 0.690, blue: 0.412) // #7FB069 strings
    static let claudeSyntaxNum    = Color(red: 0.910, green: 0.725, blue: 0.435) // #E8B96F numbers
    static let claudeSyntaxFunc   = Color(red: 0.616, green: 0.643, blue: 0.949) // #9DA4F2 functions
    static let claudeSyntaxCmt    = Color(red: 0.541, green: 0.494, blue: 0.447) // #8A7E72 comments

    // MARK: - Semantic
    static let claudeSuccess   = Color(red: 0.420, green: 0.616, blue: 0.369) // #6B9D5E sage
    static let claudeWarning   = Color(red: 0.831, green: 0.600, blue: 0.322) // #D49952
    static let claudeError     = Color(red: 0.757, green: 0.400, blue: 0.329) // #C16654 terracotta
    static let claudeInfo      = Color(red: 0.353, green: 0.384, blue: 0.451) // #5A6273

    // MARK: - Dark mode (warm dark — preserves paper feel)
    static let claudeDarkCanvas   = Color(red: 0.122, green: 0.106, blue: 0.086) // #1F1B16
    static let claudeDarkSurface  = Color(red: 0.165, green: 0.145, blue: 0.125) // #2A2520
    static let claudeDarkSurface2 = Color(red: 0.227, green: 0.200, blue: 0.173) // #3A332C
    static let claudeDarkDivider  = Color(red: 0.227, green: 0.200, blue: 0.173) // #3A332C
    static let claudeDarkText     = Color(red: 0.910, green: 0.878, blue: 0.824) // #E8E0D2
    static let claudeDarkTextSec  = Color(red: 0.710, green: 0.671, blue: 0.620) // #B5AB9E
    static let claudeOrangeSoftDk = Color(red: 0.290, green: 0.208, blue: 0.165) // #4A352A
}
```

## 2. Typography

Tiempos and Styrene are proprietary (Klim Type Foundry). Bundle via `Info.plist` (`UIAppFonts`). If unavailable, fall back to Source Serif Pro and SF Pro — the closest open-source pairing.

```swift
extension Font {
    // Display
    static let claudeDisplay      = Font.custom("TiemposHeadline-Semibold", size: 32) // "Settings"
    static let claudeConvTitle    = Font.custom("Styrene-Semibold",         size: 18) // Conversation title

    // Headings inside assistant messages
    static let claudeH1           = Font.custom("TiemposHeadline-Semibold", size: 24)
    static let claudeH2           = Font.custom("TiemposHeadline-Semibold", size: 20)
    static let claudeH3           = Font.custom("TiemposHeadline-Semibold", size: 17)

    // Assistant body — the most-rendered text in the app
    static let claudeBody         = Font.custom("TiemposText-Regular",      size: 16)
    static let claudeBodyBold     = Font.custom("TiemposText-Semibold",     size: 16)
    static let claudeBodyItalic   = Font.custom("TiemposText-Italic",       size: 16)
    static let claudeBlockquote   = Font.custom("TiemposText-Italic",       size: 16)

    // User messages & UI chrome
    static let claudeUser         = Font.custom("Styrene-Regular",          size: 16)
    static let claudeAction       = Font.custom("Styrene-Semibold",         size: 15)
    static let claudeChip         = Font.custom("Styrene-Medium",           size: 13)
    static let claudeMeta         = Font.custom("Styrene-Regular",          size: 12)
    static let claudeCaption      = Font.custom("Styrene-Regular",          size: 11)
    static let claudeSenderLabel  = Font.custom("Styrene-Medium",           size: 13)
    static let claudeGroupHeader  = Font.custom("Styrene-Bold",             size: 11) // SIDEBAR GROUPS

    // Code
    static let claudeCodeInline   = Font.custom("JetBrainsMono-Medium",     size: 14)
    static let claudeCodeBlock    = Font.custom("JetBrainsMono-Regular",    size: 14)
    static let claudeCodeBlockSm  = Font.custom("JetBrainsMono-Regular",    size: 13)
    static let claudeCodeLang     = Font.custom("JetBrainsMono-Regular",    size: 11)
}

// Fallback when Tiempos isn't bundled — Source Serif Pro / SF Pro is the closest pairing
extension Font {
    static func claude(_ size: CGFloat, weight: Font.Weight = .regular, serif: Bool = false) -> Font {
        .system(size: size, weight: weight, design: serif ? .serif : .default)
    }
}
```

## 3. The Asterisk-Star Logomark

```swift
struct ClaudeMark: Shape {
    func path(in rect: CGRect) -> Path {
        var path = Path()
        let center = CGPoint(x: rect.midX, y: rect.midY)
        let radius = min(rect.width, rect.height) / 2
        let innerRadius = radius * 0.18

        // 6 narrow petals radiating from center, 60° apart
        for i in 0..<6 {
            let angle = CGFloat.pi / 3 * CGFloat(i) - CGFloat.pi / 2
            let tip = CGPoint(
                x: center.x + radius * cos(angle),
                y: center.y + radius * sin(angle)
            )
            let leftAngle = angle + CGFloat.pi / 2
            let leftBase = CGPoint(
                x: center.x + innerRadius * cos(leftAngle),
                y: center.y + innerRadius * sin(leftAngle)
            )
            let rightBase = CGPoint(
                x: center.x - innerRadius * cos(leftAngle),
                y: center.y - innerRadius * sin(leftAngle)
            )
            path.move(to: leftBase)
            path.addQuadCurve(to: tip, control: CGPoint(
                x: (leftBase.x + tip.x) / 2 + 1, y: (leftBase.y + tip.y) / 2 + 1))
            path.addQuadCurve(to: rightBase, control: CGPoint(
                x: (tip.x + rightBase.x) / 2 - 1, y: (tip.y + rightBase.y) / 2 - 1))
            path.closeSubpath()
        }
        return path
    }
}

struct ClaudeAvatar: View {
    var size: CGFloat = 18
    var color: Color = .claudeOrange

    var body: some View {
        ClaudeMark()
            .fill(color)
            .frame(width: size, height: size)
    }
}
```

## 4. Signature Components

### Assistant Message Block (the conversation hero)

```swift
struct AssistantMessage: View {
    let modelName: String          // "Claude Opus 4.5"
    let content: AttributedString  // Pre-rendered markdown
    let isStreaming: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            // Header strip
            HStack(spacing: 8) {
                ClaudeAvatar(size: 18)
                HStack(spacing: 6) {
                    Text("Claude")
                        .font(.claudeSenderLabel)
                        .foregroundStyle(Color.claudeGraphite)
                    Text("·")
                        .foregroundStyle(Color.claudeStone)
                    Text(modelName)
                        .font(.claudeChip)
                        .foregroundStyle(Color.claudeStone)
                }
            }
            .padding(.leading, 4)

            // Body — markdown-rendered Tiempos prose
            Text(content)
                .font(.claudeBody)
                .foregroundStyle(Color.claudeInk)
                .lineSpacing(8) // Approximates 1.55 line-height

            if isStreaming {
                StreamingCursor()
            }
        }
        .padding(.horizontal, 4)
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}
```

### Streaming Cursor (the orange caret)

```swift
struct StreamingCursor: View {
    @State private var visible = true

    var body: some View {
        Rectangle()
            .fill(Color.claudeOrange)
            .frame(width: 8, height: 18)
            .cornerRadius(1)
            .opacity(visible ? 1 : 0)
            .onAppear {
                withAnimation(.linear(duration: 0.3).repeatForever()) {
                    visible = false
                }
            }
            .accessibilityLabel("Claude is responding")
    }
}
```

### User Message Pill

```swift
struct UserMessage: View {
    let text: String

    var body: some View {
        HStack {
            Spacer(minLength: 40)
            Text(text)
                .font(.claudeUser)
                .foregroundStyle(Color.claudeInk)
                .padding(.vertical, 12)
                .padding(.horizontal, 16)
                .background(
                    RoundedRectangle(cornerRadius: 18)
                        .fill(Color.claudeSurface1)
                )
                .frame(maxWidth: UIScreen.main.bounds.width * 0.8, alignment: .trailing)
        }
        .padding(.horizontal, 4)
    }
}
```

### Chat Input

```swift
struct ChatInput: View {
    @Binding var text: String
    @FocusState private var focused: Bool
    var onSend: () -> Void
    var onAttach: () -> Void

    var canSend: Bool { !text.trimmingCharacters(in: .whitespaces).isEmpty }

    var body: some View {
        VStack(spacing: 0) {
            HStack(alignment: .bottom, spacing: 8) {
                // Paperclip / attach
                Button(action: onAttach) {
                    Image(systemName: "plus.circle")
                        .font(.system(size: 24))
                        .foregroundStyle(Color.claudeGraphite)
                }
                .padding(.bottom, 14)

                // Auto-growing text field
                TextField("Reply to Claude…", text: $text, axis: .vertical)
                    .focused($focused)
                    .font(.claudeUser)
                    .foregroundStyle(Color.claudeInk)
                    .padding(.vertical, 14)
                    .lineLimit(1...8)

                // Send circle
                Button(action: {
                    if canSend {
                        onSend()
                        text = ""
                    }
                }) {
                    Image(systemName: "arrow.up")
                        .font(.system(size: 16, weight: .bold))
                        .foregroundStyle(canSend ? Color.claudePaper : Color.claudeStone)
                        .frame(width: 40, height: 40)
                        .background(
                            Circle().fill(canSend ? Color.claudeOrange : Color.claudeSurface2)
                        )
                }
                .disabled(!canSend)
                .padding(.bottom, 6)
                .sensoryFeedback(.impact(weight: .medium), trigger: canSend)
            }
            .padding(.horizontal, 16)
            .padding(.vertical, 6)
            .background(
                RoundedRectangle(cornerRadius: 24)
                    .fill(Color.claudePaper)
                    .overlay(
                        RoundedRectangle(cornerRadius: 24)
                            .strokeBorder(
                                focused ? Color.claudeOrange : Color.claudeSand,
                                lineWidth: focused ? 1.5 : 1
                            )
                    )
            )
            .padding(.horizontal, 16)
            .padding(.bottom, 8)
        }
    }
}
```

### Model Picker Chip

```swift
struct ModelPickerChip: View {
    let modelName: String
    var onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 6) {
                ClaudeAvatar(size: 14)
                Text(modelName)
                    .font(.claudeChip)
                    .foregroundStyle(Color.claudeInk)
                Image(systemName: "chevron.down")
                    .font(.system(size: 10, weight: .semibold))
                    .foregroundStyle(Color.claudeStone)
            }
            .padding(.vertical, 6)
            .padding(.horizontal, 12)
            .background(
                RoundedRectangle(cornerRadius: 16)
                    .fill(Color.claudeSurface1)
            )
        }
        .buttonStyle(.plain)
    }
}
```

### Model Picker Sheet

```swift
struct ModelPickerSheet: View {
    @Binding var selectedModel: String
    let models: [ModelOption]
    var onClose: () -> Void

    struct ModelOption: Identifiable {
        var id: String { name }
        let name: String
        let subtitle: String
        let available: Bool
    }

    var body: some View {
        VStack(spacing: 0) {
            HStack {
                Text("Select Model").font(.claudeAction).foregroundStyle(Color.claudeInk)
                Spacer()
                Button(action: onClose) {
                    Image(systemName: "xmark.circle.fill")
                        .font(.system(size: 22))
                        .foregroundStyle(Color.claudeStone)
                }
            }
            .padding(.horizontal, 20)
            .padding(.vertical, 16)

            ForEach(models) { model in
                Button {
                    if model.available {
                        selectedModel = model.name
                        onClose()
                    }
                } label: {
                    HStack(spacing: 16) {
                        ClaudeAvatar(size: 24, color: model.available ? .claudeOrange : .claudeBone)
                        VStack(alignment: .leading, spacing: 2) {
                            Text(model.name).font(.system(size: 16, weight: .medium)).foregroundStyle(Color.claudeInk)
                            Text(model.subtitle).font(.claudeChip).foregroundStyle(Color.claudeGraphite)
                        }
                        Spacer()
                        if model.name == selectedModel {
                            Circle().fill(Color.claudeOrange).frame(width: 12, height: 12)
                        }
                    }
                    .padding(.horizontal, 20)
                    .padding(.vertical, 14)
                }
                .buttonStyle(.plain)
                Rectangle().fill(Color.claudeSurface1).frame(height: 1).padding(.leading, 60)
            }
        }
        .background(
            RoundedRectangle(cornerRadius: 24, style: .continuous)
                .fill(Color.claudePaper)
        )
        .sensoryFeedback(.selection, trigger: selectedModel)
    }
}
```

### Code Block

```swift
struct CodeBlock: View {
    let language: String
    let code: String                  // Pre-syntax-highlighted via AttributedString
    @State private var copied = false

    var body: some View {
        VStack(spacing: 0) {
            // Header
            HStack {
                Text(language).font(.claudeCodeLang).foregroundStyle(Color.claudeStone)
                Spacer()
                Button {
                    UIPasteboard.general.string = code
                    withAnimation(.easeInOut(duration: 0.15)) { copied = true }
                    DispatchQueue.main.asyncAfter(deadline: .now() + 1.2) {
                        withAnimation { copied = false }
                    }
                } label: {
                    Image(systemName: copied ? "checkmark" : "doc.on.doc")
                        .font(.system(size: 14, weight: .medium))
                        .foregroundStyle(Color.claudeCodeFg)
                        .frame(width: 32, height: 32)
                        .background(Color.white.opacity(0.05).clipShape(Circle()))
                }
                .sensoryFeedback(.impact(weight: .light), trigger: copied)
            }
            .padding(.horizontal, 12)
            .padding(.vertical, 8)
            .background(Rectangle().fill(Color.claudeDarkSurface2.opacity(0.4)))

            // Body
            ScrollView(.horizontal, showsIndicators: false) {
                Text(code)
                    .font(.claudeCodeBlock)
                    .foregroundStyle(Color.claudeCodeFg)
                    .padding(16)
            }
        }
        .background(Color.claudeCodeBg)
        .clipShape(RoundedRectangle(cornerRadius: 12))
        .overlay(
            RoundedRectangle(cornerRadius: 12).strokeBorder(Color.claudeSand, lineWidth: 1)
        )
    }
}
```

### "Thinking..." Indicator

```swift
struct ThinkingIndicator: View {
    let elapsedSeconds: Int?
    @State private var pulse = false

    var body: some View {
        HStack(spacing: 8) {
            ClaudeAvatar(size: 14)
                .scaleEffect(pulse ? 1.15 : 1.0)
                .animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: pulse)
            Text(elapsedSeconds.map { "Thought for \($0)s" } ?? "Thinking…")
                .font(.claudeChip)
                .foregroundStyle(Color.claudeGraphite)
        }
        .padding(.vertical, 6)
        .padding(.horizontal, 12)
        .background(Capsule().fill(Color.claudeOrangeSoft))
        .onAppear { pulse = true }
    }
}
```

### Artifact Card

```swift
struct ArtifactCard: View {
    let title: String
    let kind: ArtifactKind             // .document / .code / .chart
    let preview: String
    var onOpen: () -> Void

    enum ArtifactKind { case document, code, chart }

    var icon: String {
        switch kind {
        case .document: return "doc.text"
        case .code:     return "chevron.left.forwardslash.chevron.right"
        case .chart:    return "chart.bar"
        }
    }

    var body: some View {
        Button(action: onOpen) {
            VStack(alignment: .leading, spacing: 12) {
                HStack(spacing: 8) {
                    Image(systemName: icon).font(.system(size: 14)).foregroundStyle(Color.claudeGraphite)
                    Text(title).font(.system(size: 17, weight: .medium)).foregroundStyle(Color.claudeInk)
                    Spacer()
                    Image(systemName: "arrow.up.right").font(.system(size: 12)).foregroundStyle(Color.claudeStone)
                }

                Text(preview)
                    .font(.claudeCodeBlockSm)
                    .foregroundStyle(Color.claudeGraphite)
                    .lineLimit(6)
                    .multilineTextAlignment(.leading)
                    .padding(.horizontal, 8).padding(.vertical, 6)
                    .background(
                        RoundedRectangle(cornerRadius: 8).fill(Color.claudeSurface1)
                    )
            }
            .padding(16)
            .frame(maxWidth: .infinity, alignment: .leading)
            .background(
                RoundedRectangle(cornerRadius: 12)
                    .fill(Color.claudePaper)
                    .overlay(
                        RoundedRectangle(cornerRadius: 12).strokeBorder(Color.claudeSand, lineWidth: 1)
                    )
            )
            .shadow(color: Color.black.opacity(0.06), radius: 8, y: 2)
        }
        .buttonStyle(.plain)
    }
}
```

## 5. Conversation View Composition

```swift
struct ConversationView: View {
    @State private var input = ""
    @State private var modelName = "Claude Opus 4.5"
    @State private var showingModelPicker = false

    var body: some View {
        VStack(spacing: 0) {
            // Top header
            HStack {
                Button { /* open sidebar */ } label: {
                    Image(systemName: "line.3.horizontal").font(.system(size: 18)).foregroundStyle(Color.claudeInk)
                }
                Spacer()
                Text("Untitled chat").font(.claudeConvTitle).foregroundStyle(Color.claudeInk)
                Spacer()
                Button { /* ... */ } label: {
                    Image(systemName: "ellipsis").font(.system(size: 18)).foregroundStyle(Color.claudeInk)
                }
            }
            .padding(.horizontal, 16)
            .frame(height: 52)
            .background(Color.claudeCream)

            // Model chip
            HStack {
                ModelPickerChip(modelName: modelName) { showingModelPicker = true }
                Spacer()
            }
            .padding(.horizontal, 20).padding(.bottom, 8)

            ScrollView {
                VStack(spacing: 32) {
                    UserMessage(text: "Explain Bayesian inference like I'm a curious 15-year-old.")
                    AssistantMessage(
                        modelName: modelName,
                        content: AttributedString("Imagine you're trying to figure out…"),
                        isStreaming: true
                    )
                }
                .padding(.horizontal, 16).padding(.vertical, 16)
            }
            .background(Color.claudeCream)

            ChatInput(text: $input, onSend: {}, onAttach: {})
        }
        .background(Color.claudeCream.ignoresSafeArea())
        .sheet(isPresented: $showingModelPicker) {
            ModelPickerSheet(
                selectedModel: $modelName,
                models: [
                    .init(name: "Claude Opus 4.5",   subtitle: "Most intelligent",     available: true),
                    .init(name: "Claude Sonnet 4.5", subtitle: "Fast and capable",     available: true),
                    .init(name: "Claude Haiku 4.5",  subtitle: "Quickest responses",   available: true),
                ],
                onClose: { showingModelPicker = false }
            )
            .presentationDetents([.medium])
        }
    }
}
```

## 6. Motion & Haptics

```swift
// Send tap
.sensoryFeedback(.impact(weight: .medium), trigger: sendPressed)

// Copy code
.sensoryFeedback(.impact(weight: .light), trigger: copied)

// Model picker select
.sensoryFeedback(.selection, trigger: selectedModel)

// Streaming cursor blink — 600ms interval
withAnimation(.linear(duration: 0.3).repeatForever()) { cursorVisible.toggle() }

// "Thinking…" asterisk pulse — 1200ms
withAnimation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true)) { pulseScale = 1.15 }

// Artifact card → modal expansion (shared element)
.matchedGeometryEffect(id: artifactID, in: artifactNS)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send (arrow) | `arrow.up` | 16pt |
| Attach (plus) | `plus.circle` | 24pt |
| Copy | `doc.on.doc` | 14pt |
| Copied confirmation | `checkmark` | 14pt |
| Hamburger / sidebar | `line.3.horizontal` | 18pt |
| Overflow menu | `ellipsis` | 18pt |
| Model picker chevron | `chevron.down` | 10pt |
| Close modal | `xmark.circle.fill` | 22pt |
| Artifact — document | `doc.text` | 14pt |
| Artifact — code | `chevron.left.forwardslash.chevron.right` | 14pt |
| Artifact — chart | `chart.bar` | 14pt |
| Artifact — open arrow | `arrow.up.right` | 12pt |
| Settings | `gearshape` | 22pt |
| New chat | `plus` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `matchedGeometryEffect` on artifact expansion); fall back to `UIImpactFeedbackGenerator` on iOS 16
- Support Dynamic Type on assistant body, all markdown headings, user messages, list items, blockquotes, settings — fix model chip, timestamps, send icon, code language label (layout-sensitive). Code block font scales with an upper bound at 18pt to prevent overflow
- VoiceOver: assistant messages are a single accessibility container with the model name announced first, then the body content. The streaming cursor announces "Claude is responding" and updates rotor labels as new content arrives. Code blocks should be readable; the copy button has `accessibilityLabel("Copy code")`
- Contrast: Ink (`#2D2520`) on Cream (`#F8F4ED`) meets WCAG AAA at all sizes; Claude Orange (`#D97757`) on cream meets AA at 16pt+ Medium — avoid Claude Orange on small body text
- Reduce Motion: skip the streaming cursor blink and the asterisk pulse when `accessibilityReduceMotion` is true; the cursor stays solid and the "Thinking…" indicator shows a static dot pattern
- Dark mode: implement via `@Environment(\.colorScheme)` — the warm dark canvas `#1F1B16` preserves the paper feel; Claude Orange stays identical because it reads well on warm dark
- Word-by-word streaming should use `.contentTransition(.numericText())` per word or animate via SwiftUI's `Text` interpolation rather than character-by-character — readability over typewriter drama
- Long code blocks: prefer SwiftUI's `ScrollView(.horizontal)` for code, not text wrapping. Wrapping breaks code semantics
- The asterisk-star logomark: never tint it any color other than `#D97757` Claude Orange (or `#B5AB9E` Bone Warm for the disabled state in model picker)
