# Microsoft Copilot (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Copilot's visual language into paste-ready SwiftUI code: `Color` extensions, the flourish gradient, acrylic materials, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let cpCanvas   = Color.white                                      // #FFFFFF
    static let cpSurface  = Color(red: 0.953, green: 0.953, blue: 0.953)     // #F3F3F3
    static let cpDivider  = Color(red: 0.878, green: 0.878, blue: 0.878)     // #E0E0E0

    // MARK: - Canvas & Surfaces (Dark)
    static let cpDarkCanvas  = Color(red: 0.125, green: 0.125, blue: 0.125)  // #202020
    static let cpDarkSurface = Color(red: 0.176, green: 0.176, blue: 0.176)  // #2D2D2D
    static let cpDarkDivider = Color(red: 0.227, green: 0.227, blue: 0.227)  // #3A3A3A

    // MARK: - Text
    static let cpTextPrimary    = Color(red: 0.141, green: 0.141, blue: 0.141) // #242424
    static let cpTextSecondary  = Color(red: 0.380, green: 0.380, blue: 0.380) // #616161
    static let cpTextTertiary   = Color(red: 0.569, green: 0.569, blue: 0.569) // #919191
    static let cpDarkTextPrimary = Color.white                                 // #FFFFFF

    // MARK: - Brand
    static let cpBlue        = Color(red: 0.0,   green: 0.471, blue: 0.831)   // #0078D4
    static let cpBluePressed = Color(red: 0.0,   green: 0.353, blue: 0.620)   // #005A9E
    static let cpBlueTint    = Color(red: 0.871, green: 0.925, blue: 0.976)   // #DEECF9
    static let cpDarkBlue    = Color(red: 0.302, green: 0.639, blue: 0.878)   // #4DA3E0
    static let cpCoral       = Color(red: 1.0,   green: 0.435, blue: 0.380)   // #FF6F61
    static let cpGold        = Color(red: 1.0,   green: 0.725, blue: 0.0)     // #FFB900

    // MARK: - Semantic
    static let cpSuccess = Color(red: 0.063, green: 0.486, blue: 0.063)       // #107C10
    static let cpWarning = Color(red: 0.969, green: 0.388, blue: 0.047)       // #F7630C
    static let cpError   = Color(red: 0.773, green: 0.059, blue: 0.122)       // #C50F1F
}

// The single warm brand gesture — flourish mark, streaming edge, focus emphasis.
extension LinearGradient {
    static let copilotFlourish = LinearGradient(
        colors: [.cpCoral, .cpGold],
        startPoint: .leading, endPoint: .trailing
    )
}
```

## 2. Typography

Segoe UI is Microsoft's product typeface (not bundled with iOS). Register the TTFs via `Info.plist` (`UIAppFonts`), or use **Inter** (closest free humanist-grotesque). Use Cascadia Code / SF Mono for code.

```swift
extension Font {
    static let cpGreeting    = Font.custom("SegoeUI-Bold",     size: 28).weight(.bold)
    static let cpTitle       = Font.custom("SegoeUI-Bold",     size: 22).weight(.bold)
    static let cpSection     = Font.custom("SegoeUI-Semibold", size: 18).weight(.semibold)
    static let cpAnswerH2    = Font.custom("SegoeUI-Semibold", size: 20).weight(.semibold)
    static let cpAnswerH3    = Font.custom("SegoeUI-Semibold", size: 17).weight(.semibold)
    static let cpAnswerBody  = Font.custom("SegoeUI-Regular",  size: 16).weight(.regular)
    static let cpUserTurn    = Font.custom("SegoeUI-Regular",  size: 16).weight(.regular)
    static let cpPromptInput = Font.custom("SegoeUI-Regular",  size: 16).weight(.regular)
    static let cpChip        = Font.custom("SegoeUI-Semibold", size: 14).weight(.semibold)
    static let cpTone        = Font.custom("SegoeUI-Semibold", size: 13).weight(.semibold)
    static let cpMeta        = Font.custom("SegoeUI-Regular",  size: 13).weight(.regular)
    static let cpCode        = Font.custom("CascadiaCode-Regular", size: 14).weight(.regular)
    static let cpLabelUpper  = Font.custom("SegoeUI-Semibold", size: 11).weight(.semibold)
    static let cpButton      = Font.custom("SegoeUI-Semibold", size: 15).weight(.semibold)
}

extension Font {
    static func cp(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Acrylic Surface Modifier

```swift
// Fluent acrylic ≈ a frosted material + a faint tint + a hairline + a blur-in on appear.
struct CopilotAcrylic: ViewModifier {
    var cornerRadius: CGFloat = 16
    var appears: Bool = true
    @State private var shown = false

    func body(content: Content) -> some View {
        content
            .background(.regularMaterial) // the frosted layer (dims content behind)
            .background(Color.cpSurface.opacity(0.5)) // faint Fluent tint
            .clipShape(RoundedRectangle(cornerRadius: cornerRadius, style: .continuous))
            .overlay(
                RoundedRectangle(cornerRadius: cornerRadius, style: .continuous)
                    .strokeBorder(Color.cpDivider.opacity(0.6), lineWidth: 1)
            )
            .opacity(shown || !appears ? 1 : 0)
            .blur(radius: shown || !appears ? 0 : 8)
            .onAppear { withAnimation(.easeOut(duration: 0.22)) { shown = true } }
    }
}
extension View {
    func copilotAcrylic(cornerRadius: CGFloat = 16, appears: Bool = true) -> some View {
        modifier(CopilotAcrylic(cornerRadius: cornerRadius, appears: appears))
    }
}
```

### Flourish Mark

```swift
struct CopilotFlourish: View {
    var size: CGFloat = 22
    var body: some View {
        Image(systemName: "sparkles") // substitute for the swirl flourish glyph
            .font(.system(size: size, weight: .semibold))
            .foregroundStyle(LinearGradient.copilotFlourish)
            .frame(width: size, height: size)
    }
}
```

### Answer Card

```swift
struct CopilotAnswerCard: View {
    let markdown: AttributedString
    let isStreaming: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(alignment: .top, spacing: 10) {
                CopilotFlourish(size: 22)
                Text(markdown)
                    .font(.cpAnswerBody)
                    .foregroundStyle(Color.cpTextPrimary)
                    .lineSpacing(6) // ≈ 1.5 line-height at 16pt
                    .textSelection(.enabled)
            }
            if !isStreaming {
                HStack(spacing: 20) {
                    actionIcon("doc.on.doc")        // Copy
                    actionIcon("hand.thumbsup")     // Like
                    actionIcon("hand.thumbsdown")   // Dislike
                    actionIcon("arrow.clockwise")   // Regenerate
                    actionIcon("square.and.arrow.up")
                    actionIcon("ellipsis")
                }
                .padding(.leading, 32)
            }
        }
        .padding(16)
        .frame(maxWidth: .infinity, alignment: .leading)
        .copilotAcrylic(cornerRadius: 16)
        .shadow(color: Color.cpTextPrimary.opacity(0.06), radius: 10, y: 2)
        .padding(.horizontal, 16)
    }

    private func actionIcon(_ name: String) -> some View {
        Image(systemName: name)
            .font(.system(size: 18))
            .foregroundStyle(Color.cpTextSecondary)
            .frame(width: 44, height: 44)
            .contentShape(Rectangle())
    }
}
```

### User Turn

```swift
struct CopilotUserTurn: View {
    let text: String
    var neutral: Bool = false

    var body: some View {
        HStack {
            Spacer(minLength: 48)
            Text(text)
                .font(.cpUserTurn)
                .foregroundStyle(neutral ? Color.cpTextPrimary : .white)
                .padding(.vertical, 12)
                .padding(.horizontal, 16)
                .background(
                    UnevenRoundedRectangle(
                        topLeadingRadius: 16, bottomLeadingRadius: 16,
                        bottomTrailingRadius: 4, topTrailingRadius: 16
                    )
                    .fill(neutral ? Color.cpSurface : Color.cpBlue)
                )
        }
        .padding(.horizontal, 16)
    }
}
```

### Tone Selector

```swift
struct CopilotToneSelector: View {
    @Binding var selection: Int // 0 Creative, 1 Balanced, 2 Precise
    private let tones = ["Creative", "Balanced", "Precise"]

    var body: some View {
        HStack(spacing: 0) {
            ForEach(tones.indices, id: \.self) { i in
                Text(tones[i])
                    .font(.cpTone)
                    .foregroundStyle(selection == i ? .white : Color.cpTextSecondary)
                    .frame(maxWidth: .infinity)
                    .padding(.vertical, 9)
                    .background(
                        Capsule().fill(selection == i ? Color.cpBlue : .clear)
                    )
                    .contentShape(Rectangle())
                    .onTapGesture {
                        withAnimation(.spring(response: 0.2, dampingFraction: 0.7)) { selection = i }
                    }
            }
        }
        .padding(3)
        .background(Capsule().fill(.regularMaterial))
        .overlay(Capsule().strokeBorder(Color.cpDivider.opacity(0.6), lineWidth: 1))
    }
}
```

### Prompt Bar (acrylic, focus accent)

```swift
struct CopilotPromptBar: View {
    @Binding var text: String
    @FocusState private var focused: Bool
    var isStreaming: Bool = false
    let onSend: () -> Void
    let onStop: () -> Void

    var body: some View {
        HStack(spacing: 10) {
            Button { } label: {
                Image(systemName: "plus")
                    .font(.system(size: 22)).foregroundStyle(Color.cpTextSecondary)
            }
            TextField("Message Copilot", text: $text, axis: .vertical)
                .font(.cpPromptInput)
                .focused($focused)
                .lineLimit(1...5)
            Button { } label: {
                Image(systemName: "mic")
                    .font(.system(size: 22)).foregroundStyle(Color.cpTextSecondary)
            }
            sendButton
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 8)
        .frame(minHeight: 52)
        .background(.regularMaterial)
        .background(Color.cpSurface.opacity(0.5))
        .clipShape(RoundedRectangle(cornerRadius: 24, style: .continuous))
        .overlay(
            RoundedRectangle(cornerRadius: 24, style: .continuous)
                .strokeBorder(focused ? Color.cpBlue : Color.cpDivider.opacity(0.6),
                              lineWidth: focused ? 1.5 : 1)
                .animation(.easeOut(duration: 0.18), value: focused)
        )
        .padding(.horizontal, 16)
    }

    @ViewBuilder private var sendButton: some View {
        if isStreaming {
            Button(action: onStop) {
                Image(systemName: "stop.fill")
                    .font(.system(size: 14, weight: .bold)).foregroundStyle(.white)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(Color.cpBlue))
            }
        } else {
            let active = !text.isEmpty
            Button(action: onSend) {
                Image(systemName: "arrow.up")
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(active ? .white : Color.cpTextSecondary)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(active ? Color.cpBlue : .clear))
            }
            .disabled(!active)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: active)
        }
    }
}
```

### Suggestion Chip

```swift
struct CopilotSuggestionChip: View {
    let label: String
    var featured: Bool = false
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                if featured { CopilotFlourish(size: 12) }
                Text(label).font(.cpChip).foregroundStyle(Color.cpTextPrimary)
            }
            .padding(.vertical, 10)
            .padding(.horizontal, 16)
            .background(.regularMaterial)
            .clipShape(Capsule())
            .overlay(Capsule().strokeBorder(Color.cpDivider.opacity(0.6), lineWidth: 1))
        }
        .buttonStyle(CopilotPressableStyle())
    }
}

struct CopilotPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.spring(response: 0.2, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

## 4. Streaming Shimmer + Thinking

```swift
struct CopilotStreamingText: View {
    let text: String
    @State private var phase: CGFloat = -0.3
    var body: some View {
        Text(text)
            .font(.cpAnswerBody)
            .foregroundStyle(Color.cpTextPrimary)
            .lineSpacing(6)
            .overlay(
                LinearGradient(
                    colors: [.clear, Color.cpGold.opacity(0.28), Color.cpCoral.opacity(0.20), .clear],
                    startPoint: .init(x: phase, y: 0.5),
                    endPoint: .init(x: phase + 0.3, y: 0.5)
                )
                .blendMode(.plusLighter)
                .allowsHitTesting(false)
            )
            .onAppear {
                withAnimation(.linear(duration: 1.1).repeatForever(autoreverses: false)) { phase = 1.3 }
            }
    }
}

struct CopilotThinking: View {
    @State private var t = false
    var body: some View {
        HStack(spacing: 6) {
            ForEach(0..<3) { i in
                Circle()
                    .fill(LinearGradient.copilotFlourish)
                    .frame(width: 7, height: 7)
                    .scaleEffect(t ? 1 : 0.5)
                    .animation(.easeInOut(duration: 0.5).repeatForever().delay(Double(i) * 0.15), value: t)
            }
        }
        .onAppear { t = true }
    }
}
```

## 5. Navigation (no tab bar — top bar + sidebar)

```swift
struct CopilotRoot: View {
    @State private var sidebarOpen = false
    var body: some View {
        ZStack {
            ConversationView()
                .safeAreaInset(edge: .top) {
                    HStack {
                        Button { sidebarOpen = true } label: {
                            Image(systemName: "line.3.horizontal").font(.system(size: 22))
                        }
                        Spacer()
                        Text("Copilot").font(.cpButton)
                        Spacer()
                        Image(systemName: "person.crop.circle").font(.system(size: 28))
                    }
                    .padding(.horizontal, 16).padding(.vertical, 8)
                    .background(.regularMaterial)
                }
            if sidebarOpen {
                Color.black.opacity(0.4).ignoresSafeArea()
                    .onTapGesture { sidebarOpen = false }
                CopilotSidebar()
                    .frame(width: UIScreen.main.bounds.width * 0.80)
                    .frame(maxWidth: .infinity, alignment: .leading)
                    .transition(.move(edge: .leading))
            }
        }
        .animation(.easeOut(duration: 0.28), value: sidebarOpen)
    }
}

struct CopilotSidebar: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack { Image(systemName: "plus"); Text("New chat").font(.cpButton) }
                .padding(.vertical, 12).padding(.horizontal, 16)
                .frame(maxWidth: .infinity, alignment: .leading)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.cpBlue))
                .foregroundStyle(.white)
            Text("TODAY").font(.cpLabelUpper).foregroundStyle(Color.cpTextSecondary)
                .padding(.top, 16).padding(.leading, 4)
            // recents rows: 48pt; active row gets acrylic highlight + 3pt leading cpBlue bar
            Spacer()
        }
        .padding(16)
        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
        .background(.regularMaterial)
    }
}
```

## 6. Motion

```swift
// Acrylic blur-in — CopilotAcrylic ramps opacity + blur 8→0 over 0.22s on appear
// Streaming shimmer — CopilotStreamingText sweeps a warm low-opacity gradient
// Thinking dots — CopilotThinking, flourish pulse before the first token
// Send ⇄ Stop morph
.animation(.spring(response: 0.2, dampingFraction: 0.7), value: isStreaming)
// Tone pill slide
.animation(.spring(response: 0.2, dampingFraction: 0.7), value: selection)
// Prompt focus accent
.animation(.easeOut(duration: 0.18), value: focused)
// Sidebar
.animation(.easeOut(duration: 0.28), value: sidebarOpen)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Flourish (brand) | `sparkles` (substitute for swirl) | 12-56pt |
| Send | `arrow.up` | 18pt |
| Stop streaming | `stop.fill` | 14pt |
| Voice / Mic | `mic` | 20-22pt |
| Add content | `plus` | 22pt |
| Copy | `doc.on.doc` | 18pt |
| Like / Dislike | `hand.thumbsup` / `hand.thumbsdown` | 18pt |
| Regenerate | `arrow.clockwise` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| More | `ellipsis` | 18pt |
| Sidebar toggle | `line.3.horizontal` | 22pt |
| Selector chevron | `chevron.down` | 12pt |
| New chat | `square.and.pencil` / `plus` | 22pt |
| Search (sidebar) | `magnifyingglass` | 18pt |
| Conversation row | `bubble.left` | 18pt |
| Account | `person.crop.circle` | 28pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`). `.regularMaterial` (acrylic) is iOS 15+
- Support Dynamic Type on the greeting, answer body/headings, user turn, and chips — pin the prompt-bar base height, sidebar day labels, and tone labels
- VoiceOver: announce the answer card as "Copilot response"; the send button as "Send" / "Stop generating"; the flourish is decorative (`.accessibilityHidden(true)`); the tone selector exposes a "Tone, Balanced" picker semantic
- The flourish gradient is brand, not state — never rely on it alone; pair the focus accent with a VoiceOver focus announcement
- Acrylic + Reduce Transparency: when `accessibilityReduceTransparency` is on, swap `.regularMaterial` for a solid `Color.cpSurface` so text contrast is preserved
- Contrast: `#616161` on `#FFFFFF` passes WCAG AA at 13pt+; on dark use `#A6A6A6` on `#202020`; use `#4DA3E0` for blue links/send on dark
- Respect Reduce Motion: stop the idle flourish shimmer and the streaming sweep (still append tokens), and cross-fade the sidebar / skip the acrylic blur-in
- Full light + dark parity: drive surface/text via the environment `colorScheme`; keep the flourish stops identical
