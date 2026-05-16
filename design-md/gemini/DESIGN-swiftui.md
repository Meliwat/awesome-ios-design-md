# Google Gemini (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Gemini's visual language into paste-ready SwiftUI code: `Color` extensions, the gradient, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let gemCanvas   = Color.white                                     // #FFFFFF
    static let gemSurface  = Color(red: 0.941, green: 0.957, blue: 0.976)    // #F0F4F9
    static let gemDivider  = Color(red: 0.890, green: 0.890, blue: 0.890)    // #E3E3E3

    // MARK: - Canvas & Surfaces (Dark)
    static let gemDarkCanvas  = Color(red: 0.118, green: 0.118, blue: 0.118) // #1E1E1E
    static let gemDarkSurface = Color(red: 0.157, green: 0.165, blue: 0.173) // #282A2C
    static let gemDarkDivider = Color(red: 0.235, green: 0.235, blue: 0.235) // #3C3C3C

    // MARK: - Text
    static let gemTextPrimary    = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let gemTextSecondary  = Color(red: 0.373, green: 0.388, blue: 0.408) // #5F6368
    static let gemTextTertiary   = Color(red: 0.604, green: 0.627, blue: 0.651) // #9AA0A6
    static let gemDarkTextPrimary = Color(red: 0.890, green: 0.890, blue: 0.890) // #E3E3E3

    // MARK: - Brand
    static let gemBlue        = Color(red: 0.259, green: 0.522, blue: 0.957) // #4285F4
    static let gemBluePressed = Color(red: 0.200, green: 0.404, blue: 0.839) // #3367D6
    static let gemDarkBlue    = Color(red: 0.541, green: 0.706, blue: 0.973) // #8AB4F8
    static let gemViolet      = Color(red: 0.608, green: 0.447, blue: 0.796) // #9B72CB
    static let gemCoral       = Color(red: 0.851, green: 0.396, blue: 0.439) // #D96570

    // MARK: - Semantic
    static let gemSuccess = Color(red: 0.118, green: 0.557, blue: 0.243)     // #1E8E3E
    static let gemWarning = Color(red: 0.976, green: 0.671, blue: 0.0)       // #F9AB00
    static let gemError   = Color(red: 0.851, green: 0.188, blue: 0.145)     // #D93025
}

// The single brand gesture — used for sparkle, streaming edge, focus ring, chip accents.
extension LinearGradient {
    static let gemini = LinearGradient(
        colors: [.gemBlue, .gemViolet, .gemCoral],
        startPoint: .leading, endPoint: .trailing
    )
}
```

## 2. Typography

Google Sans is Google's product typeface. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. The closest free substitute is **Inter**; use Roboto Mono (or SF Mono) for code.

```swift
extension Font {
    static let gemGreeting    = Font.custom("GoogleSans-Medium",  size: 28).weight(.medium)
    static let gemTitle       = Font.custom("GoogleSans-Medium",  size: 22).weight(.medium)
    static let gemSection     = Font.custom("GoogleSans-Medium",  size: 18).weight(.medium)
    static let gemAnswerH2    = Font.custom("GoogleSans-Medium",  size: 20).weight(.medium)
    static let gemAnswerH3    = Font.custom("GoogleSans-Medium",  size: 17).weight(.medium)
    static let gemAnswerBody  = Font.custom("GoogleSansText-Regular", size: 16).weight(.regular)
    static let gemUserTurn    = Font.custom("GoogleSansText-Regular", size: 16).weight(.regular)
    static let gemPromptInput = Font.custom("GoogleSansText-Regular", size: 16).weight(.regular)
    static let gemChip        = Font.custom("GoogleSansText-Medium",  size: 14).weight(.medium)
    static let gemMeta        = Font.custom("GoogleSansText-Regular", size: 13).weight(.regular)
    static let gemCode        = Font.custom("RobotoMono-Regular",     size: 14).weight(.regular)
    static let gemLabelUpper  = Font.custom("GoogleSans-Medium",  size: 11).weight(.medium)
    static let gemButton      = Font.custom("GoogleSans-Medium",  size: 15).weight(.medium)
}

extension Font {
    static func gem(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Gradient Sparkle

```swift
struct GemSparkle: View {
    var size: CGFloat = 20

    var body: some View {
        Image(systemName: "sparkle")
            .font(.system(size: size, weight: .semibold))
            .foregroundStyle(LinearGradient.gemini) // gradient-filled glyph
            .frame(width: size, height: size)
    }
}
```

### User Turn Chip

```swift
struct GemUserTurn: View {
    let text: String

    var body: some View {
        HStack {
            Spacer(minLength: 48)
            Text(text)
                .font(.gemUserTurn)
                .foregroundStyle(Color.gemTextPrimary)
                .padding(.vertical, 12)
                .padding(.horizontal, 16)
                .background(
                    UnevenRoundedRectangle(
                        topLeadingRadius: 20, bottomLeadingRadius: 20,
                        bottomTrailingRadius: 4, topTrailingRadius: 20
                    )
                    .fill(Color.gemSurface)
                )
        }
        .padding(.horizontal, 16)
    }
}
```

### Assistant Turn (plain text, no bubble)

```swift
struct GemAssistantTurn: View {
    let markdown: AttributedString
    let isStreaming: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(alignment: .top, spacing: 10) {
                GemSparkle(size: 20)
                Text(markdown)
                    .font(.gemAnswerBody)
                    .foregroundStyle(Color.gemTextPrimary)
                    .lineSpacing(8) // ≈ 1.55 line-height at 16pt
                    .textSelection(.enabled)
            }
            if !isStreaming {
                HStack(spacing: 20) {
                    actionIcon("doc.on.doc")     // Copy
                    actionIcon("arrow.clockwise") // Regenerate
                    actionIcon("square.and.arrow.up") // Share
                    actionIcon("ellipsis")
                }
                .padding(.leading, 30)
            }
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(.horizontal, 16)
    }

    private func actionIcon(_ name: String) -> some View {
        Image(systemName: name)
            .font(.system(size: 18))
            .foregroundStyle(Color.gemTextSecondary)
            .frame(width: 44, height: 44)
            .contentShape(Rectangle())
    }
}
```

### Streaming Shimmer Overlay

```swift
struct GemStreamingText: View {
    let text: String
    @State private var phase: CGFloat = -0.3

    var body: some View {
        Text(text)
            .font(.gemAnswerBody)
            .foregroundStyle(Color.gemTextPrimary)
            .lineSpacing(8)
            .overlay(
                LinearGradient(
                    colors: [.clear, Color.gemViolet.opacity(0.28), .clear],
                    startPoint: .init(x: phase, y: 0.5),
                    endPoint: .init(x: phase + 0.3, y: 0.5)
                )
                .blendMode(.plusLighter)
                .allowsHitTesting(false)
            )
            .onAppear {
                withAnimation(.linear(duration: 1.1).repeatForever(autoreverses: false)) {
                    phase = 1.3
                }
            }
    }
}

// Thinking indicator (before first token)
struct GemThinking: View {
    @State private var t = false
    var body: some View {
        HStack(spacing: 6) {
            ForEach(0..<3) { i in
                Circle()
                    .fill(LinearGradient.gemini)
                    .frame(width: 7, height: 7)
                    .scaleEffect(t ? 1 : 0.5)
                    .animation(.easeInOut(duration: 0.5).repeatForever().delay(Double(i) * 0.15), value: t)
            }
        }
        .onAppear { t = true }
    }
}
```

### Prompt Bar (with gradient focus ring)

```swift
struct GemPromptBar: View {
    @Binding var text: String
    @FocusState private var focused: Bool
    var isStreaming: Bool = false
    let onSend: () -> Void
    let onStop: () -> Void

    var body: some View {
        HStack(spacing: 10) {
            Button { /* attach */ } label: {
                Image(systemName: "plus")
                    .font(.system(size: 22)).foregroundStyle(Color.gemTextSecondary)
            }
            TextField("Ask Gemini", text: $text, axis: .vertical)
                .font(.gemPromptInput)
                .focused($focused)
                .lineLimit(1...5)
            Button { /* mic */ } label: {
                Image(systemName: "mic")
                    .font(.system(size: 22)).foregroundStyle(Color.gemTextSecondary)
            }
            sendButton
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 8)
        .frame(minHeight: 52)
        .background(Capsule(style: .continuous).fill(Color.gemSurface))
        .overlay(
            Capsule(style: .continuous)
                .strokeBorder(LinearGradient.gemini, lineWidth: focused ? 1.5 : 0)
                .animation(.easeOut(duration: 0.18), value: focused)
        )
        .padding(.horizontal, 16)
    }

    @ViewBuilder private var sendButton: some View {
        if isStreaming {
            Button(action: onStop) {
                Image(systemName: "stop.fill")
                    .font(.system(size: 14, weight: .bold))
                    .foregroundStyle(.white)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(Color.gemBlue))
            }
        } else {
            let active = !text.isEmpty
            Button(action: onSend) {
                Image(systemName: "arrow.up")
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(active ? .white : Color.gemTextTertiary)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(active ? Color.gemBlue : .clear))
            }
            .disabled(!active)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: active)
        }
    }
}
```

### Suggestion Chip

```swift
struct GemSuggestionChip: View {
    let label: String
    var featured: Bool = false
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                if featured { GemSparkle(size: 12) }
                Text(label).font(.gemChip).foregroundStyle(Color.gemTextPrimary)
            }
            .padding(.vertical, 10)
            .padding(.horizontal, 16)
            .background(Capsule().fill(Color.gemSurface))
            .overlay(
                Capsule().strokeBorder(
                    featured ? AnyShapeStyle(LinearGradient.gemini)
                             : AnyShapeStyle(Color.gemDivider),
                    lineWidth: 1)
            )
        }
        .buttonStyle(GemPressableStyle())
    }
}

struct GemPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.spring(response: 0.2, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

## 4. Side Drawer

```swift
struct GemDrawer: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            // New chat pill
            HStack { Image(systemName: "plus"); Text("New chat").font(.gemButton) }
                .padding(.vertical, 12).padding(.horizontal, 16)
                .frame(maxWidth: .infinity, alignment: .leading)
                .background(Capsule().fill(Color.gemSurface))

            Text("TODAY").font(.gemLabelUpper).foregroundStyle(Color.gemTextSecondary)
                .padding(.top, 16).padding(.leading, 8)

            ForEach(recents) { r in
                HStack(spacing: 12) {
                    Image(systemName: "bubble.left").font(.system(size: 18))
                    Text(r.title).font(.gemUserTurn).lineLimit(1)
                }
                .padding(.vertical, 12).padding(.horizontal, 16)
                .frame(maxWidth: .infinity, alignment: .leading)
                .background(
                    Capsule().fill(r.active ? Color.gemSurface : .clear))
            }
            Spacer()
        }
        .padding(16)
        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
        .background(Color.gemCanvas)
    }
}
```

## 5. Navigation (no tab bar — top bar + drawer)

```swift
struct GemRoot: View {
    @State private var drawerOpen = false
    var body: some View {
        ZStack {
            ConversationView()
                .safeAreaInset(edge: .top) {
                    HStack {
                        Button { drawerOpen = true } label: {
                            Image(systemName: "line.3.horizontal").font(.system(size: 22))
                        }
                        Spacer()
                        Button { } label: {
                            HStack(spacing: 4) {
                                Text("Gemini").font(.gemButton)
                                Image(systemName: "chevron.down").font(.system(size: 12))
                            }
                        }
                        Spacer()
                        Image(systemName: "person.crop.circle").font(.system(size: 28))
                    }
                    .padding(.horizontal, 16).padding(.vertical, 8)
                    .background(Color.gemCanvas)
                }
            if drawerOpen {
                Color.black.opacity(0.4).ignoresSafeArea()
                    .onTapGesture { drawerOpen = false }
                GemDrawer().frame(width: UIScreen.main.bounds.width * 0.82)
                    .frame(maxWidth: .infinity, alignment: .leading)
                    .transition(.move(edge: .leading))
            }
        }
        .animation(.easeOut(duration: 0.28), value: drawerOpen)
    }
}
```

## 6. Motion

```swift
// Streaming shimmer — GemStreamingText sweeps a low-opacity gradient over the leading edge
// Thinking dots — GemThinking, gradient pulse before the first token
// Send ⇄ Stop morph
.animation(.spring(response: 0.2, dampingFraction: 0.7), value: isStreaming)
// Prompt focus ring
.animation(.easeOut(duration: 0.18), value: focused)
// Drawer
.animation(.easeOut(duration: 0.28), value: drawerOpen)
// Copy confirm — a brief gradient-edged toast (.transition(.move(edge: .bottom).combined(with: .opacity)))
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Sparkle (brand) | `sparkle` | 12-56pt |
| Send | `arrow.up` | 18pt |
| Stop streaming | `stop.fill` | 14pt |
| Mic | `mic` | 22pt |
| Add content | `plus` | 22pt |
| Copy | `doc.on.doc` | 18pt |
| Regenerate | `arrow.clockwise` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| More | `ellipsis` | 18pt |
| Drawer toggle | `line.3.horizontal` | 22pt |
| Model chevron | `chevron.down` | 12pt |
| New chat | `square.and.pencil` / `plus` | 22pt |
| Search (drawer) | `magnifyingglass` | 18pt |
| Conversation row | `bubble.left` | 18pt |
| Account | `person.crop.circle` | 28pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on the greeting, answer body/headings, user turn, and chips — pin the prompt-bar base height and drawer day labels
- VoiceOver: announce the assistant turn as "Gemini response"; the send button as "Send" / "Stop generating"; the sparkle is decorative (`.accessibilityHidden(true)`)
- The gradient is brand, not state — never rely on it alone to convey meaning; pair the focus ring with a VoiceOver focus announcement
- Contrast: `#5F6368` on `#FFFFFF` passes WCAG AA at 13pt+; on dark use `#9AA0A6` on `#1E1E1E`; use `#8AB4F8` for blue links/send on dark
- Respect Reduce Motion: stop the idle sparkle shimmer and the streaming highlight sweep (still append tokens, just without the moving gradient); cross-fade the drawer instead of sliding
- Full light + dark parity: drive surface/text via the environment `colorScheme`; keep the gradient stops identical
