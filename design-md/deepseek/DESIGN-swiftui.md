# DeepSeek (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates DeepSeek's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. DeepSeek's signature is the **recessed reasoning trace** with a blue left-bar and dimmed-italic chain-of-thought, subordinate to an upright answer.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark)
    static let dsCanvas     = Color(red: 0.055, green: 0.055, blue: 0.063) // #0E0E10
    static let dsSurface1   = Color(red: 0.102, green: 0.106, blue: 0.118) // #1A1B1E
    static let dsSurface2   = Color(red: 0.137, green: 0.141, blue: 0.157) // #232428
    static let dsSurface3   = Color(red: 0.173, green: 0.176, blue: 0.192) // #2C2D31
    static let dsReasoningBG = Color(red: 0.086, green: 0.090, blue: 0.102) // #16171A (recessed)
    static let dsDivider    = Color(red: 0.180, green: 0.184, blue: 0.200) // #2E2F33

    // MARK: - Canvas & Surfaces (Light)
    static let dsCanvasLight   = Color.white                                  // #FFFFFF
    static let dsSurface1Light = Color(red: 0.961, green: 0.965, blue: 0.973) // #F5F6F8
    static let dsReasoningBGLight = Color(red: 0.969, green: 0.973, blue: 0.980) // #F7F8FA
    static let dsDividerLight  = Color(red: 0.894, green: 0.902, blue: 0.922) // #E4E6EB

    // MARK: - Text
    static let dsTextPrimary   = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC
    static let dsTextSecondary = Color(red: 0.604, green: 0.608, blue: 0.627) // #9A9BA0
    static let dsTextTertiary  = Color(red: 0.416, green: 0.420, blue: 0.439) // #6A6B70
    static let dsReasoningText = Color(red: 0.541, green: 0.545, blue: 0.565) // #8A8B90
    static let dsTextPrimaryLight = Color(red: 0.102, green: 0.106, blue: 0.118) // #1A1B1E

    // MARK: - Brand (the single accent)
    static let dsBlue        = Color(red: 0.302, green: 0.420, blue: 0.996) // #4D6BFE
    static let dsBluePressed = Color(red: 0.231, green: 0.341, blue: 0.878) // #3B57E0
    static let dsBlueSoft    = Color(red: 0.118, green: 0.133, blue: 0.251) // #1E2240
    static let dsBlueSoftLight = Color(red: 0.922, green: 0.937, blue: 1.000) // #EBEFFF
    static let dsBlueToken   = Color(red: 0.773, green: 0.804, blue: 1.000) // #C5CDFF

    // MARK: - Semantic
    static let dsSuccess = Color(red: 0.180, green: 0.741, blue: 0.522) // #2EBD85
    static let dsError   = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}
```

## 2. Typography

DeepSeek uses the system sans (SF Pro on device; Inter as the open fallback). The reasoning trace is the only italic in the system.

```swift
extension Font {
    static let dsScreenTitle  = Font.system(size: 32, weight: .heavy)
    static let dsEmptyHeadline = Font.system(size: 26, weight: .bold)
    static let dsSection      = Font.system(size: 18, weight: .bold)
    static let dsBody         = Font.system(size: 15, weight: .regular)
    static let dsAction       = Font.system(size: 15, weight: .semibold)
    static let dsCardTitle    = Font.system(size: 15, weight: .semibold)
    static let dsReasoning    = Font.system(size: 13, weight: .regular).italic()   // chain-of-thought
    static let dsMeta         = Font.system(size: 14, weight: .regular)
    static let dsCaption      = Font.system(size: 12, weight: .medium)
    static let dsTab          = Font.system(size: 10, weight: .medium)
    static let dsPlaceholder  = Font.system(size: 15, weight: .regular)

    static let dsCodeInline = Font.system(size: 13, weight: .regular, design: .monospaced)
    static let dsCodeBlock  = Font.system(size: 13, weight: .regular, design: .monospaced)
    static let dsCitation   = Font.system(size: 11, weight: .semibold, design: .monospaced)
}
```

## 3. Signature Components

### Reasoning Trace (the signature)

```swift
struct ReasoningTrace: View {
    let durationSeconds: Int
    let thought: String
    @State private var collapsed = false

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Button {
                withAnimation(.easeOut(duration: 0.2)) { collapsed.toggle() }
            } label: {
                HStack(spacing: 8) {
                    Image(systemName: "brain")
                        .font(.system(size: 14))
                        .foregroundStyle(Color.dsBlue)
                    Text("Thought for \(durationSeconds) seconds")
                        .font(.dsCaption)
                        .foregroundStyle(Color.dsTextSecondary)
                    Text(collapsed ? "· tap to expand" : "· tap to collapse")
                        .font(.dsCaption)
                        .foregroundStyle(Color.dsTextTertiary)
                    Spacer()
                    Image(systemName: "chevron.down")
                        .font(.system(size: 11))
                        .foregroundStyle(Color.dsTextTertiary)
                        .rotationEffect(.degrees(collapsed ? -90 : 0))
                }
            }
            .buttonStyle(.plain)

            if !collapsed {
                Text(thought)
                    .font(.dsReasoning)                       // dimmed ITALIC
                    .foregroundStyle(Color.dsReasoningText)
                    .lineSpacing(4)
                    .transition(.opacity.combined(with: .move(edge: .top)))
            }
        }
        .padding(.vertical, 12)
        .padding(.horizontal, 14)
        .background(
            Color.dsReasoningBG                              // DARKER than canvas
                .clipShape(RoundedRectangle(cornerRadius: 10))
        )
        .overlay(alignment: .leading) {
            Rectangle().fill(Color.dsBlue).frame(width: 2)   // 2pt blue left-bar
                .clipShape(RoundedRectangle(cornerRadius: 1))
        }
    }
}
```

### DeepThink / Search Toggle Pill

```swift
struct TogglePill: View {
    let title: String
    let systemImage: String
    @Binding var isOn: Bool

    var body: some View {
        Button {
            UIImpactFeedbackGenerator(style: .soft).impactOccurred()
            withAnimation(.easeInOut(duration: 0.15)) { isOn.toggle() }
        } label: {
            HStack(spacing: 6) {
                Image(systemName: systemImage).font(.system(size: 14))
                Text(title).font(.dsAction.weight(.semibold))
            }
            .foregroundStyle(isOn ? Color.dsBlue : Color.dsTextSecondary)
            .padding(.vertical, 7).padding(.horizontal, 13)
            .background(
                Capsule().fill(isOn ? Color.dsBlueSoft : Color.dsSurface1)
            )
            .overlay(
                Capsule().strokeBorder(isOn ? Color.dsBlue : Color.dsDivider, lineWidth: 1)
            )
        }
        .buttonStyle(.plain)
    }
}
```

### User Bubble & Assistant Message

```swift
struct UserBubble: View {
    let text: String
    var body: some View {
        HStack {
            Spacer(minLength: 40)
            Text(text)
                .font(.dsBody)
                .foregroundStyle(Color.dsTextPrimary)
                .lineSpacing(3)
                .padding(.vertical, 12).padding(.horizontal, 16)
                .background(Color.dsBlueSoft)
                .clipShape(.rect(topLeadingRadius: 18, bottomLeadingRadius: 18,
                                 bottomTrailingRadius: 4, topTrailingRadius: 18))
                .overlay(
                    UnevenRoundedRectangle(topLeadingRadius: 18, bottomLeadingRadius: 18,
                                           bottomTrailingRadius: 4, topTrailingRadius: 18)
                        .strokeBorder(Color(red: 0.165, green: 0.200, blue: 0.376), lineWidth: 1))
        }
    }
}

struct AssistantMessage<Trace: View>: View {
    let model: String              // "DeepSeek-R1"
    @ViewBuilder let trace: () -> Trace?
    let answer: AttributedString

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack(spacing: 7) {
                Image(systemName: "circle.hexagongrid.fill")    // whale stand-in glyph
                    .font(.system(size: 18)).foregroundStyle(Color.dsBlue)
                Text(model).font(.dsCaption).foregroundStyle(Color.dsTextSecondary)
            }
            if let t = trace() { t }
            Text(answer)
                .font(.dsBody)
                .foregroundStyle(Color.dsTextPrimary)
                .lineSpacing(4)
            HStack(spacing: 16) {
                ForEach(["doc.on.doc", "hand.thumbsup", "hand.thumbsdown", "arrow.clockwise"], id: \.self) {
                    Image(systemName: $0).font(.system(size: 16))
                }
            }
            .foregroundStyle(Color.dsTextTertiary)
        }
    }
}
```

### Composer (toggle row + input pill)

```swift
struct Composer: View {
    @Binding var text: String
    @Binding var deepThink: Bool
    @Binding var search: Bool
    @Binding var isStreaming: Bool
    let onSend: () -> Void
    let onStop: () -> Void

    var body: some View {
        VStack(spacing: 8) {
            HStack(spacing: 8) {
                TogglePill(title: "DeepThink (R1)", systemImage: "brain", isOn: $deepThink)
                TogglePill(title: "Search", systemImage: "magnifyingglass", isOn: $search)
                Spacer()
            }
            HStack(spacing: 10) {
                TextField("", text: $text, prompt:
                    Text("Message DeepSeek").foregroundStyle(Color.dsTextTertiary))
                    .font(.dsBody).foregroundStyle(Color.dsTextPrimary)
                    .padding(.leading, 18)
                Button {
                    if isStreaming { onStop() } else {
                        UIImpactFeedbackGenerator(style: .soft).impactOccurred(); onSend()
                    }
                } label: {
                    Image(systemName: isStreaming ? "stop.fill" : "arrow.up")
                        .font(.system(size: 16, weight: .bold))
                        .foregroundStyle(.white)
                        .frame(width: 36, height: 36)
                        .background(Circle().fill(isStreaming ? Color.dsError : Color.dsBlue))
                }
                .padding(.trailing, 7)
            }
            .frame(height: 50)
            .background(
                Capsule().fill(Color.dsSurface1)
                    .overlay(Capsule().strokeBorder(Color.dsDivider, lineWidth: 1))
            )
        }
        .padding(.horizontal, 14)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct DSTabView: View {
    var body: some View {
        TabView {
            ChatView().tabItem     { Label("Chat",     systemImage: "bubble.left.and.bubble.right") }
            HistoryView().tabItem  { Label("History",  systemImage: "clock.arrow.circlepath") }
            SettingsView().tabItem { Label("Settings", systemImage: "gearshape") }
        }
        .tint(.dsBlue)                                  // active = the single accent, no pill
        .toolbarBackground(Color.dsCanvas, for: .tabBar)
        .toolbarColorScheme(.dark, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Toggle on/off — fill + border + content crossfade
withAnimation(.easeInOut(duration: 0.15)) { isOn.toggle() }

// Trace collapse/expand — chevron rotate + height
withAnimation(.easeOut(duration: 0.2)) { collapsed.toggle() }
// .rotationEffect(.degrees(collapsed ? -90 : 0)) on the chevron

// Reasoning stream — type token-by-token, live duration counter
// Append to `thought` on a Timer/AsyncStream; blink a #4D6BFE caret

// Send → stop morph
// Swap "arrow.up" → "stop.fill" and fill .dsBlue → .dsError on isStreaming

// Message append — new user bubble slide-up + fade
.transition(.opacity.combined(with: .move(edge: .bottom)))

// Citation tap — source sheet
.sheet(isPresented: $showSources) { SourceSheet() }   // 300ms ease-out default

// Tab change — instant color swap (no slide)

// Haptics
UIImpactFeedbackGenerator(style: .soft).impactOccurred()      // toggle, send
UINotificationFeedbackGenerator().notificationOccurred(.success) // copy
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Chat (tab) | `bubble.left.and.bubble.right` | 22pt |
| History (tab) | `clock.arrow.circlepath` | 22pt |
| Settings (tab) | `gearshape` | 22pt |
| DeepThink toggle | `brain` | 14pt |
| Search toggle | `magnifyingglass` | 14pt |
| Whale / model glyph | `circle.hexagongrid.fill` (custom whale asset preferred) | 18–22pt |
| Send | `arrow.up` | 16pt |
| Stop streaming | `stop.fill` | 16pt |
| New chat | `square.and.pencil` / `plus` | 22pt |
| Hamburger / history | `line.3.horizontal` | 22pt |
| Copy | `doc.on.doc` | 16pt |
| Thumbs up / down | `hand.thumbsup` / `hand.thumbsdown` | 16pt |
| Regenerate | `arrow.clockwise` | 16pt |
| Share | `square.and.arrow.up` | 16pt |
| Trace collapse | `chevron.down` | 11pt |
| Citation source | `link` / `text.book.closed` | 14pt |
| Attach | `paperclip` / `plus` | 20pt |

## 7. Dark Mode

DeepSeek ships both modes; dark is primary. The defining inversion: the reasoning trace background is *darker* than the canvas in both modes (recession, not elevation).

```swift
struct DSTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.dsCanvas : Color.dsCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.dsTextPrimary : Color.dsTextPrimaryLight)
            .tint(.dsBlue)
    }
}
extension View { func dsTheme() -> some View { modifier(DSTheme()) } }

// Reasoning panel per-scheme:
// dark  → bg #16171A, text #8A8B90, left-bar #4D6BFE
// light → bg #F7F8FA, text #6E6F74, left-bar #4D6BFE
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`UnevenRoundedRectangle` and `.rect(topLeadingRadius:...)` require iOS 16.4 / 17 — for iOS 16.0 use a custom `Path` for the asymmetric user-bubble corner; `UIImpactFeedbackGenerator` works back to iOS 13).
- No custom font license needed on device — system SF Pro. Ship Inter (SIL OFL) only if you need brand parity on web/cross-platform.
- Dynamic Type: scale message body, headings, and reasoning text; pin tab labels, citation chips, and toggle text at FIXED sizes (layout-sensitive composer).
- VoiceOver: announce the trace as "Reasoning, thought for {N} seconds, {collapsed/expanded}, double-tap to toggle"; read the chain-of-thought as a single block prefixed "Model reasoning:"; the answer as the primary content; toggles as "DeepThink, {on/off}" / "Search, {on/off}".
- Italic + dimmed contrast: `#8A8B90` on `#16171A` is intentionally low-emphasis but still passes WCAG AA at 13pt; never rely on italic alone to convey "this is reasoning" — the VoiceOver prefix and the visual panel/left-bar carry it too.
- Single accent: `#4D6BFE` is the only chrome color; never introduce a second accent or tint the active tab with a pill (active = `#4D6BFE` glyph+label).
- Reduce Motion: disable the trace height animation and chevron rotation (use a crossfade); keep the token-by-token stream but drop the caret blink; the send→stop morph becomes an instant swap.
- Reduce Transparency: replace the tab bar's `rgba(14,14,16,0.94)` blur with solid `#0E0E10`.
- Streaming state: while the answer streams, the send button is a `#E5484D` stop control with an accessible label "Stop generating"; the live duration counter updates an `accessibilityValue`, not a barrage of announcements.
