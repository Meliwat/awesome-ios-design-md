# Signal (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Signal's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the message bubbles, and the slide-up send button.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (light / dark via asset or scheme)
    static let sigCanvas       = Color(red: 1, green: 1, blue: 1)                // #FFFFFF
    static let sigCanvasDark   = Color(red: 0.106, green: 0.106, blue: 0.106)    // #1B1B1B
    static let sigSurface      = Color(red: 0.961, green: 0.961, blue: 0.961)    // #F5F5F5
    static let sigSurfaceDark  = Color(red: 0.165, green: 0.165, blue: 0.165)    // #2A2A2A
    static let sigDivider      = Color(red: 0.898, green: 0.898, blue: 0.898)    // #E5E5E5
    static let sigDividerDark  = Color(red: 0.227, green: 0.227, blue: 0.227)    // #3A3A3A

    // MARK: - Text
    static let sigTextPrimary   = Color.black                                    // #000000
    static let sigTextPrimaryD  = Color.white                                    // #FFFFFF
    static let sigTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.420)   // #6B6B6B
    static let sigTextTertiary  = Color(red: 0.604, green: 0.604, blue: 0.604)   // #9A9A9A

    // MARK: - Brand
    static let sigBlue        = Color(red: 0.227, green: 0.463, blue: 0.941)     // #3A76F0
    static let sigBluePressed = Color(red: 0.184, green: 0.373, blue: 0.800)     // #2F5FCC
    static let sigBlueTint    = Color(red: 0.906, green: 0.933, blue: 0.992)     // #E7EEFD

    // MARK: - Message
    static let sigIncoming     = Color(red: 0.914, green: 0.914, blue: 0.922)    // #E9E9EB
    static let sigIncomingDark = Color(red: 0.165, green: 0.165, blue: 0.165)    // #2A2A2A
    static let sigOutMeta      = Color(red: 0.796, green: 0.851, blue: 0.976)    // #CBD9F9

    // MARK: - Semantic
    static let sigError   = Color(red: 0.843, green: 0.149, blue: 0.239)         // #D7263D
    static let sigSuccess = Color(red: 0.227, green: 0.710, blue: 0.290)         // #3AB54A
}
```

## 2. Typography

Signal uses **Inter** everywhere at weights 400/500/700. Bundle the TTFs via `Info.plist` (`UIAppFonts`); the 60-digit safety number uses SF Mono.

```swift
extension Font {
    static let sigLargeTitle    = Font.custom("Inter-Bold",    size: 28)
    static let sigConvoName     = Font.custom("Inter-Medium",  size: 17)
    static let sigThreadTitle   = Font.custom("Inter-SemiBold",size: 17)
    static let sigMessageBody   = Font.custom("Inter-Regular", size: 16)
    static let sigPreview       = Font.custom("Inter-Regular", size: 15)
    static let sigSectionHeader = Font.custom("Inter-SemiBold",size: 13)
    static let sigTimestamp     = Font.custom("Inter-Regular", size: 13)
    static let sigBubbleMeta    = Font.custom("Inter-Regular", size: 12)
    static let sigButton        = Font.custom("Inter-SemiBold",size: 16)
    static let sigButtonText    = Font.custom("Inter-Regular", size: 16)
    static let sigTab           = Font.custom("Inter-Medium",  size: 10)
    static let sigTimerChip     = Font.custom("Inter-Medium",  size: 12)
    static let sigSystemNote    = Font.custom("Inter-Regular", size: 13)
    static let sigSafetyNumber  = Font.system(size: 15, weight: .regular, design: .monospaced)
}

extension Font {
    static func signal(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Message Bubble (Outgoing + Incoming with same-sender tail)

```swift
struct MessageBubble: View {
    let text: String
    let time: String
    let isOutgoing: Bool
    let isLastInRun: Bool   // tighten the tail corner when true

    private var tail: RoundedCorner {
        RoundedCorner(radius: 18,
                      tightCorner: isOutgoing ? .bottomRight : .bottomLeft,
                      tightRadius: isLastInRun ? 6 : 18)
    }

    var body: some View {
        HStack {
            if isOutgoing { Spacer(minLength: 0) }
            VStack(alignment: .trailing, spacing: 2) {
                Text(text)
                    .font(.sigMessageBody)
                    .foregroundStyle(isOutgoing ? .white : Color.sigTextPrimary)
                HStack(spacing: 3) {
                    Text(time).font(.sigBubbleMeta)
                    if isOutgoing {
                        Image(systemName: "checkmark").font(.system(size: 10, weight: .bold))
                    }
                }
                .foregroundStyle(isOutgoing ? Color.sigOutMeta : Color.sigTextSecondary)
            }
            .padding(.vertical, 9)
            .padding(.horizontal, 13)
            .background(isOutgoing ? Color.sigBlue : Color.sigIncoming)
            .clipShape(tail)
            .frame(maxWidth: 280, alignment: isOutgoing ? .trailing : .leading)
            if !isOutgoing { Spacer(minLength: 0) }
        }
        .padding(.horizontal, 12)
    }
}

// Per-corner radius shape for the same-sender tail
struct RoundedCorner: Shape {
    var radius: CGFloat
    var tightCorner: UIRectCorner
    var tightRadius: CGFloat
    func path(in rect: CGRect) -> Path {
        let corners: UIRectCorner = [.topLeft, .topRight, .bottomLeft, .bottomRight]
        var path = Path()
        for c in [UIRectCorner.topLeft, .topRight, .bottomLeft, .bottomRight] where corners.contains(c) {
            _ = c
        }
        // Simplified: use a UIBezierPath with mixed radii
        let p = UIBezierPath(roundedRect: rect, byRoundingCorners: corners,
                             cornerRadii: CGSize(width: radius, height: radius))
        path = Path(p.cgPath)
        // Re-round the tight corner smaller
        let tp = UIBezierPath(roundedRect: rect, byRoundingCorners: tightCorner,
                              cornerRadii: CGSize(width: tightRadius, height: tightRadius))
        return Path(tp.cgPath).intersection(path).isEmpty ? Path(p.cgPath) : path
    }
}
```

### Slide-Up Send Button (Signature)

```swift
struct ComposerBar: View {
    @State private var text = ""

    private var hasText: Bool { !text.trimmingCharacters(in: .whitespaces).isEmpty }

    var body: some View {
        HStack(spacing: 8) {
            HStack(spacing: 8) {
                Image(systemName: "plus")
                    .font(.system(size: 22)).foregroundStyle(Color.sigTextSecondary)
                TextField("Signal message", text: $text, axis: .vertical)
                    .font(.sigMessageBody)
                    .lineLimit(1...5)
                Image(systemName: "camera")
                    .font(.system(size: 20)).foregroundStyle(Color.sigTextSecondary)
            }
            .padding(.horizontal, 12)
            .frame(minHeight: 36)
            .background(Capsule().fill(Color.sigSurface))

            // Send circle: only when text exists; mic when empty
            SendCircle(showSend: hasText) {
                text = ""
            }
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 8)
        .animation(.spring(response: 0.18, dampingFraction: 0.8), value: hasText)
    }
}

struct SendCircle: View {
    let showSend: Bool
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button {
            action()
        } label: {
            ZStack {
                Circle().fill(pressed ? Color.sigBluePressed : Color.sigBlue)
                Image(systemName: showSend ? "arrow.up" : "mic.fill")
                    .font(.system(size: 16, weight: .bold))
                    .foregroundStyle(.white)
            }
            .frame(width: 32, height: 32)
            .scaleEffect(pressed ? 0.90 : 1)
            .opacity(showSend ? 1 : 0.92)
            .offset(y: showSend ? 0 : 4) // slides up into place when text appears
        }
        .sensoryFeedback(.impact(weight: .light), trigger: showSend)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }
            .onEnded { _ in pressed = false })
    }
}
```

### Conversation Row

```swift
struct ConversationRow: View {
    let name: String
    let preview: String
    let time: String
    let unread: Int
    let disappearing: Bool

    var body: some View {
        HStack(spacing: 12) {
            Circle().fill(Color.sigSurface).frame(width: 48, height: 48)
            VStack(alignment: .leading, spacing: 3) {
                HStack(spacing: 5) {
                    Text(name).font(.sigConvoName).foregroundStyle(Color.sigTextPrimary)
                    if disappearing {
                        Image(systemName: "timer").font(.system(size: 12))
                            .foregroundStyle(Color.sigTextSecondary)
                    }
                }
                Text(preview).font(.sigPreview).foregroundStyle(Color.sigTextSecondary)
                    .lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 6) {
                Text(time).font(.sigTimestamp).foregroundStyle(Color.sigTextSecondary)
                if unread > 0 {
                    Text("\(unread)")
                        .font(.sigBubbleMeta).foregroundStyle(.white)
                        .frame(minWidth: 20, minHeight: 20)
                        .background(Circle().fill(Color.sigBlue))
                }
            }
        }
        .padding(.vertical, 12)
        .padding(.horizontal, 16)
        .frame(height: 72)
        .contentShape(Rectangle())
    }
}
```

### Disappearing-Message Timer Chip

```swift
struct TimerChip: View {
    let duration: String  // "1w", "8h", "Off"
    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: "timer").font(.system(size: 12))
            Text(duration).font(.sigTimerChip)
        }
        .foregroundStyle(Color.sigTextSecondary) // privacy is quiet — neutral, never blue/red
    }
}
```

## 4. Encryption System Note

```swift
struct EncryptionNote: View {
    var body: some View {
        HStack(spacing: 5) {
            Image(systemName: "lock.fill").font(.system(size: 11))
            Text("Messages and calls are end-to-end encrypted.")
                .font(.sigSystemNote)
        }
        .foregroundStyle(Color.sigTextSecondary)
        .frame(maxWidth: .infinity)
        .padding(.vertical, 12)
    }
}
```

## 5. Tab Bar

`TabView` with an opaque canvas appearance; active tint is Signal Blue.

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithOpaqueBackground()
        a.backgroundColor = UIColor.systemBackground
        a.shadowColor = UIColor(Color.sigDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            ChatsView().tabItem { Label("Chats", systemImage: "message.fill") }
            CallsView().tabItem { Label("Calls", systemImage: "phone.fill") }
            StoriesView().tabItem { Label("Stories", systemImage: "circle.dashed") }
            SettingsView().tabItem { Label("Settings", systemImage: "gearshape.fill") }
        }
        .tint(.sigBlue)
    }
}
```

## 6. Motion

```swift
// Send button slide-up + fade
.animation(.spring(response: 0.18, dampingFraction: 0.8), value: hasText)

// Send circle press
.sensoryFeedback(.impact(weight: .light), trigger: showSend)

// Outgoing bubble enters from composer
.transition(.move(edge: .bottom).combined(with: .opacity))
.animation(.easeOut(duration: 0.25), value: messages.count)

// Reaction picker scales in above the bubble
.scaleEffect(showReactions ? 1 : 0.6)
.animation(.spring(response: 0.2, dampingFraction: 0.7), value: showReactions)

// Disappearing timer ring: animate trim 1 → 0 over the message lifetime
Circle().trim(from: 0, to: progress)
    .stroke(Color.sigTextSecondary, lineWidth: 1.5)
    .animation(.linear(duration: lifetime), value: progress)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send | `arrow.up` | 16pt |
| Voice (empty composer) | `mic.fill` | 16pt |
| Attach | `plus` | 22pt |
| Camera | `camera` | 20-22pt |
| Audio call | `phone` | 22pt |
| Video call | `video` | 22pt |
| Compose | `square.and.pencil` | 22pt |
| Disappearing timer | `timer` | 12pt |
| Sealed sender | `lock.fill` | 11-13pt |
| Safety shield | `checkmark.shield.fill` | 18pt |
| Read check | `checkmark` | 10pt |
| Search | `magnifyingglass` | 16pt |
| Chats (tab) | `message.fill` | 24pt |
| Calls (tab) | `phone.fill` | 24pt |
| Stories (tab) | `circle.dashed` | 24pt |
| Settings (tab) | `gearshape.fill` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`); `TextField(axis: .vertical)` needs iOS 16
- Support Dynamic Type on conversation names, message bodies, previews — cap body at ~22pt; pin bubble meta, timestamps, tab labels
- VoiceOver: announce bubble direction ("You said" vs "<Name> said"), then text, then time and read state; expose the timer chip as "Disappearing messages, 1 week"
- The send button must be announced only when actionable — hide it from accessibility when the composer is empty (it's a mic then)
- Contrast: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 13pt+; outgoing `#FFFFFF` on `#3A76F0` passes AA; verify `#CBD9F9` meta on `#3A76F0` (it's intentionally low-emphasis — bump to `#DCE6FB` if compliance-critical)
- Keep the privacy-minimal stance for assistive tech too — no decorative elements that add VoiceOver noise
