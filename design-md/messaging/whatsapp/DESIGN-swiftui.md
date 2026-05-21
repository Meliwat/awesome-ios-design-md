# WhatsApp (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates WhatsApp's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let waGreen         = Color(red: 0.145, green: 0.827, blue: 0.400) // #25D366
    static let waGreenPressed  = Color(red: 0.118, green: 0.745, blue: 0.365) // #1EBE5D
    static let waTeal          = Color(red: 0.027, green: 0.369, blue: 0.329) // #075E54
    static let waMidTeal       = Color(red: 0.071, green: 0.549, blue: 0.494) // #128C7E
    static let waDarkTeal      = Color(red: 0.020, green: 0.302, blue: 0.267) // #054D44

    // MARK: - Bubbles
    static let waOutgoingLight = Color(red: 0.851, green: 0.992, blue: 0.827) // #D9FDD3
    static let waOutgoingDark  = Color(red: 0.000, green: 0.361, blue: 0.294) // #005C4B
    static let waIncomingLight = Color.white
    static let waIncomingDark  = Color(red: 0.122, green: 0.173, blue: 0.204) // #1F2C34

    // MARK: - Canvas
    static let waWallpaperLight = Color(red: 0.925, green: 0.898, blue: 0.867) // #ECE5DD
    static let waWallpaperDark  = Color(red: 0.043, green: 0.078, blue: 0.102) // #0B141A
    static let waCanvasLight    = Color.white                                   // #FFFFFF
    static let waCanvasDark     = Color(red: 0.067, green: 0.106, blue: 0.129) // #111B21
    static let waSurface1Light  = Color(red: 0.969, green: 0.973, blue: 0.980) // #F7F8FA
    static let waSurface1Dark   = Color(red: 0.125, green: 0.173, blue: 0.200) // #202C33
    static let waSurface2Dark   = Color(red: 0.165, green: 0.224, blue: 0.259) // #2A3942
    static let waDividerLight   = Color(red: 0.914, green: 0.929, blue: 0.937) // #E9EDEF
    static let waDividerDark    = Color(red: 0.133, green: 0.176, blue: 0.204) // #222D34

    // MARK: - Text
    static let waTextPrimary    = Color(red: 0.067, green: 0.106, blue: 0.129) // #111B21
    static let waTextSecondary  = Color(red: 0.400, green: 0.467, blue: 0.506) // #667781
    static let waTextTertiary   = Color(red: 0.525, green: 0.588, blue: 0.627) // #8696A0
    static let waTextPrimaryDark   = Color(red: 0.914, green: 0.929, blue: 0.937) // #E9EDEF
    static let waTextSecondaryDark = Color(red: 0.525, green: 0.588, blue: 0.627) // #8696A0

    // MARK: - Semantic
    static let waReadBlue      = Color(red: 0.325, green: 0.741, blue: 0.922) // #53BDEB
    static let waErrorRed      = Color(red: 0.945, green: 0.361, blue: 0.427) // #F15C6D
}
```

## 2. Typography

WhatsApp ships no proprietary font — rely on SF Pro via `.system()`. These helpers keep hierarchy tokens inline so consumers don't need to remember sizes.

```swift
extension Font {
    static let waLargeTitle    = Font.system(size: 34, weight: .bold,     design: .default)
    static let waNavTitle      = Font.system(size: 17, weight: .semibold, design: .default)
    static let waContactName   = Font.system(size: 17, weight: .semibold, design: .default)
    static let waMessagePreview = Font.system(size: 15, weight: .regular, design: .default)
    static let waBubbleBody    = Font.system(size: 17, weight: .regular,  design: .default)
    static let waGroupSender   = Font.system(size: 13, weight: .semibold, design: .default)
    static let waTimestampList = Font.system(size: 12, weight: .regular,  design: .default)
    static let waTimestampBubble = Font.system(size: 11, weight: .regular, design: .default)
    static let waSectionHeader = Font.system(size: 13, weight: .semibold, design: .default)
    static let waSystemMessage = Font.system(size: 12, weight: .medium,   design: .default)
    static let waTabLabel      = Font.system(size: 10, weight: .medium,   design: .default)
    static let waButton        = Font.system(size: 17, weight: .semibold, design: .default)
    static let waInputPlaceholder = Font.system(size: 16, weight: .regular, design: .default)
}
```

## 3. Signature Components

### Send / Mic Morphing Button

```swift
struct WASendButton: View {
    let hasText: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: hasText ? "paperplane.fill" : "mic.fill")
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(.white)
                .frame(width: 36, height: 36)
                .background(Circle().fill(Color.waGreen))
                .contentTransition(.symbolEffect(.replace))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: hasText)
        .buttonStyle(WAPressableStyle(pressedScale: 0.92))
    }
}

struct WAPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.22, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Chat Bubble (Outgoing)

```swift
struct WAOutgoingBubble: View {
    let text: String
    let timestamp: String
    let readState: ReadState

    enum ReadState { case sent, delivered, read }

    var body: some View {
        HStack {
            Spacer(minLength: UIScreen.main.bounds.width * 0.2)
            VStack(alignment: .trailing, spacing: 4) {
                Text(text)
                    .font(.waBubbleBody)
                    .foregroundStyle(Color.waTextPrimary)
                HStack(spacing: 3) {
                    Text(timestamp)
                        .font(.waTimestampBubble)
                        .foregroundStyle(Color.waTextSecondary)
                    tickView
                }
            }
            .padding(.vertical, 8)
            .padding(.horizontal, 12)
            .background(
                UnevenRoundedRectangle(
                    topLeadingRadius: 12,
                    bottomLeadingRadius: 12,
                    bottomTrailingRadius: 0,
                    topTrailingRadius: 12
                )
                .fill(Color.waOutgoingLight)
            )
        }
        .padding(.trailing, 8)
    }

    @ViewBuilder
    private var tickView: some View {
        switch readState {
        case .sent:
            Image(systemName: "checkmark")
                .font(.system(size: 12, weight: .bold))
                .foregroundStyle(Color.waTextTertiary)
        case .delivered:
            HStack(spacing: -4) {
                Image(systemName: "checkmark")
                Image(systemName: "checkmark")
            }
            .font(.system(size: 12, weight: .bold))
            .foregroundStyle(Color.waTextTertiary)
        case .read:
            HStack(spacing: -4) {
                Image(systemName: "checkmark")
                Image(systemName: "checkmark")
            }
            .font(.system(size: 12, weight: .bold))
            .foregroundStyle(Color.waReadBlue)
        }
    }
}
```

### Chat List Row

```swift
struct WAChatListRow: View {
    let avatar: Image
    let name: String
    let preview: String
    let timestamp: String
    let unreadCount: Int
    let hasStatusRing: Bool

    var body: some View {
        HStack(spacing: 12) {
            ZStack {
                if hasStatusRing {
                    Circle()
                        .strokeBorder(Color.waGreen, lineWidth: 2.5)
                        .frame(width: 54, height: 54)
                }
                avatar
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(width: 48, height: 48)
                    .clipShape(Circle())
            }

            VStack(alignment: .leading, spacing: 3) {
                Text(name)
                    .font(.waContactName)
                    .foregroundStyle(Color.waTextPrimary)
                    .lineLimit(1)
                Text(preview)
                    .font(.waMessagePreview)
                    .foregroundStyle(Color.waTextSecondary)
                    .lineLimit(1)
            }

            Spacer()

            VStack(alignment: .trailing, spacing: 4) {
                Text(timestamp)
                    .font(.waTimestampList)
                    .foregroundStyle(unreadCount > 0 ? Color.waGreen : Color.waTextSecondary)
                if unreadCount > 0 {
                    Text("\(unreadCount)")
                        .font(.system(size: 12, weight: .semibold))
                        .foregroundStyle(.white)
                        .padding(.horizontal, 7)
                        .padding(.vertical, 2)
                        .background(Capsule().fill(Color.waGreen))
                }
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .contentShape(Rectangle())
    }
}
```

### Voice Message Waveform Bubble

```swift
struct WAVoiceWaveformBubble: View {
    let duration: String
    @State private var isPlaying = false
    @State private var progress: Double = 0.0
    // Amplitude samples normalized 0..1
    let amplitudes: [CGFloat] = (0..<48).map { _ in CGFloat.random(in: 0.2...1.0) }

    var body: some View {
        HStack(spacing: 10) {
            Button {
                isPlaying.toggle()
            } label: {
                Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                    .font(.system(size: 16, weight: .bold))
                    .foregroundStyle(.white)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(Color.waGreen))
            }
            .sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)

            GeometryReader { geo in
                HStack(spacing: 2) {
                    ForEach(0..<amplitudes.count, id: \.self) { i in
                        let playedFraction = Double(i) / Double(amplitudes.count)
                        Capsule()
                            .fill(playedFraction <= progress ? Color.waGreen : Color.waTextTertiary)
                            .frame(width: 2, height: max(4, amplitudes[i] * 20))
                    }
                }
                .frame(height: 24)
            }
            .frame(height: 24)

            Text(duration)
                .font(.waTimestampBubble)
                .foregroundStyle(Color.waTextTertiary)
        }
        .padding(.vertical, 10)
        .padding(.horizontal, 12)
        .background(
            UnevenRoundedRectangle(
                topLeadingRadius: 12,
                bottomLeadingRadius: 12,
                bottomTrailingRadius: 0,
                topTrailingRadius: 12
            )
            .fill(Color.waOutgoingLight)
        )
    }
}
```

### Compose Bar

```swift
struct WAComposeBar: View {
    @State private var text: String = ""

    var body: some View {
        HStack(spacing: 8) {
            HStack(spacing: 10) {
                Button { } label: {
                    Image(systemName: "face.smiling")
                        .font(.system(size: 22))
                        .foregroundStyle(Color.waTextTertiary)
                }
                TextField("Message", text: $text, axis: .vertical)
                    .font(.waInputPlaceholder)
                    .lineLimit(1...5)
                Button { } label: {
                    Image(systemName: "paperclip")
                        .font(.system(size: 20))
                        .foregroundStyle(Color.waTextTertiary)
                        .rotationEffect(.degrees(-45))
                }
                Button { } label: {
                    Image(systemName: "camera.fill")
                        .font(.system(size: 20))
                        .foregroundStyle(Color.waTextTertiary)
                }
            }
            .padding(.vertical, 8)
            .padding(.horizontal, 14)
            .background(Capsule().fill(Color.white))

            WASendButton(hasText: !text.isEmpty) {
                text = ""
            }
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 8)
        .background(Color.waSurface1Light)
    }
}
```

## 4. Chat Screen Assembly

```swift
struct WAChatScreen: View {
    var body: some View {
        VStack(spacing: 0) {
            // Top nav handled by NavigationStack
            ScrollView {
                LazyVStack(spacing: 2) {
                    WAEncryptionBanner()
                    WAOutgoingBubble(
                        text: "Hey are you around?",
                        timestamp: "10:24",
                        readState: .read
                    )
                    WAIncomingBubble(text: "Yes, about to head out")
                    WAVoiceWaveformBubble(duration: "0:23")
                }
                .padding(.vertical, 8)
            }
            .background(
                Color.waWallpaperLight
                    .overlay(WADoodleWallpaper().opacity(0.08))
            )
            WAComposeBar()
        }
    }
}

struct WAEncryptionBanner: View {
    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: "lock.fill").font(.system(size: 10))
            Text("Messages are end-to-end encrypted. No one outside this chat, not even WhatsApp, can read or listen to them.")
                .font(.waSystemMessage)
                .multilineTextAlignment(.center)
        }
        .foregroundStyle(Color.waTextSecondary)
        .padding(.vertical, 8)
        .padding(.horizontal, 12)
        .background(Capsule().fill(Color(red: 1.0, green: 0.96, blue: 0.77)))
        .padding(.horizontal, 24)
    }
}

struct WAIncomingBubble: View {
    let text: String
    var body: some View {
        HStack {
            VStack(alignment: .leading) {
                Text(text).font(.waBubbleBody).foregroundStyle(Color.waTextPrimary)
            }
            .padding(.vertical, 8)
            .padding(.horizontal, 12)
            .background(
                UnevenRoundedRectangle(
                    topLeadingRadius: 12,
                    bottomLeadingRadius: 0,
                    bottomTrailingRadius: 12,
                    topTrailingRadius: 12
                )
                .fill(Color.white)
                .shadow(color: .black.opacity(0.08), radius: 2, y: 1)
            )
            Spacer(minLength: UIScreen.main.bounds.width * 0.2)
        }
        .padding(.leading, 8)
    }
}

struct WADoodleWallpaper: View {
    // Decorative doodle tile — use an Image asset in production
    var body: some View { Color.clear }
}
```

## 5. Tab Bar

```swift
struct WARootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            UpdatesView().tabItem { Label("Updates", systemImage: "circle.dashed") }
            CallsView().tabItem { Label("Calls", systemImage: "phone.fill") }
            CommunitiesView().tabItem { Label("Communities", systemImage: "person.3.fill") }
            ChatsView().tabItem { Label("Chats", systemImage: "message.fill") }
                .badge(3)
            SettingsView().tabItem { Label("Settings", systemImage: "gearshape.fill") }
        }
        .tint(.waGreen)
    }
}
```

## 6. Motion

```swift
// Send button press
.sensoryFeedback(.impact(flexibility: .soft), trigger: sentCount)

// Long-press reactions
.sensoryFeedback(.impact(weight: .medium), trigger: reactionShown)

// Swipe-to-reply threshold
.sensoryFeedback(.selection, trigger: swipeThresholdCrossed)

// Read receipt turning blue
.animation(.easeInOut(duration: 0.2), value: readState)

// Voice bubble progress
withAnimation(.linear(duration: 0.1)) { progress += 0.02 }

// Mic record pulsing ring (outer ring grows when decibel peaks)
.scaleEffect(recording ? 1.0 + amplitude * 0.2 : 1.0)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send | `paperplane.fill` | 18-22pt |
| Microphone | `mic.fill` | 18-22pt |
| Attach | `paperclip` (rotated -45deg) | 20pt |
| Camera | `camera.fill` | 20-22pt |
| Emoji | `face.smiling` | 22pt |
| Read ticks | `checkmark` | 12pt |
| Phone | `phone.fill` / `phone.down.fill` | 22pt |
| Video | `video.fill` | 22pt |
| Lock | `lock.fill` | 12-14pt |
| Chats tab | `message.fill` | 24pt |
| Calls tab | `phone.fill` | 24pt |
| Communities tab | `person.3.fill` | 24pt |
| Updates tab | `circle.dashed` | 24pt |
| Settings tab | `gearshape.fill` | 24pt |
| New chat | `square.and.pencil` | 22pt |
| Search | `magnifyingglass` | 17pt |
| Ellipsis | `ellipsis` | 20pt |
| Play / Pause (voice) | `play.fill` / `pause.fill` | 16pt |
| Status ring | N/A (Circle strokeBorder) | 2.5pt lineWidth |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- `UnevenRoundedRectangle` requires iOS 16.0+
- Dynamic Type: respect on bubble body, contact name, message preview; fix inline timestamps (11-12pt)
- VoiceOver: label the send/mic button dynamically ("Send message" vs "Record voice message"); group bubble text with timestamp and read-state via `.accessibilityElement(children: .combine)`
- Contrast: `#D9FDD3` outgoing bubble + `#111B21` text easily exceeds WCAG AA; validate dark-mode `#005C4B` bubble with `#E9EDEF` text (also AA compliant)
- Right-to-left languages: bubble tail corner flips automatically via SwiftUI layout direction — test with Arabic/Hebrew locale
- End-to-end encryption banner: mark as `.accessibilityAddTraits(.isStaticText)` and do not hide from VoiceOver
