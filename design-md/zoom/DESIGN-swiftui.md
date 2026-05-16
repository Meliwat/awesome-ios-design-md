# Zoom (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zoom's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark / in-call)
    static let zoomCanvas    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let zoomSurface1  = Color(red: 0.176, green: 0.176, blue: 0.176) // #2D2D2D
    static let zoomSurface2  = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A
    static let zoomDivider   = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A

    // MARK: - Light surfaces (outside call)
    static let zoomLightCanvas  = Color.white                                // #FFFFFF
    static let zoomLightSurface = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let zoomLightDivider = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5

    // MARK: - Text (dark)
    static let zoomTextPrimary   = Color.white                                // #FFFFFF
    static let zoomTextSecondary = Color(red: 0.690, green: 0.690, blue: 0.690) // #B0B0B0
    static let zoomTextTertiary  = Color(red: 0.478, green: 0.478, blue: 0.478) // #7A7A7A

    // MARK: - Brand & Semantic
    static let zoomBlue        = Color(red: 0.176, green: 0.549, blue: 1.0)   // #2D8CFF
    static let zoomBluePressed = Color(red: 0.122, green: 0.435, blue: 0.800) // #1F6FCC
    static let zoomRed         = Color(red: 0.878, green: 0.157, blue: 0.157) // #E02828
    static let zoomRedPressed  = Color(red: 0.725, green: 0.122, blue: 0.122) // #B91F1F
    static let zoomHandYellow  = Color(red: 0.961, green: 0.773, blue: 0.094) // #F5C518
    static let zoomSuccess     = Color(red: 0.055, green: 0.541, blue: 0.271) // #0E8A45
}
```

## 2. Typography

Zoom uses a custom grotesque; Lato (or Inter) is the closest free substitute. Bundle the TTFs via `Info.plist` (`UIAppFonts`). The call timer and meeting IDs need tabular figures.

```swift
extension Font {
    static let zoomTitleLarge   = Font.custom("Lato-Bold",     size: 28)
    static let zoomMeetingTopic = Font.custom("Lato-Bold",     size: 22)
    static let zoomSection      = Font.custom("Lato-Bold",     size: 17)
    static let zoomListTitle    = Font.custom("Lato-Semibold", size: 16)
    static let zoomBody         = Font.custom("Lato-Regular",  size: 15)
    static let zoomButton       = Font.custom("Lato-Bold",     size: 17)
    static let zoomControlLabel = Font.custom("Lato-Semibold", size: 11)
    static let zoomMetadata     = Font.custom("Lato-Regular",  size: 13)
    static let zoomTileName     = Font.custom("Lato-Semibold", size: 13)
    static let zoomTimer        = Font.custom("Lato-Semibold", size: 14)
    static let zoomTab          = Font.custom("Lato-Semibold", size: 10)
    static let zoomTinyUpper    = Font.custom("Lato-Bold",     size: 11)
}

// System fallback if Lato is unavailable
extension Font {
    static func zoom(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}

extension View {
    func zoomTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Gallery Video Tile (the signature element)

```swift
struct GalleryTile: View {
    let name: String
    let isMuted: Bool
    let isActiveSpeaker: Bool
    var hasVideo: Bool = false

    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 8).fill(Color.zoomSurface2)

            if !hasVideo {
                Circle()
                    .fill(Color.zoomBlue.opacity(0.25))
                    .frame(width: 56, height: 56)
                    .overlay(Text(initials(name))
                        .font(.zoom(20, weight: .semibold))
                        .foregroundStyle(.white))
            }

            VStack {
                Spacer()
                HStack(spacing: 6) {
                    if isMuted {
                        Image(systemName: "mic.slash.fill")
                            .font(.system(size: 12))
                            .foregroundStyle(.white)
                            .frame(width: 22, height: 22)
                            .background(Circle().fill(Color.zoomRed))
                    }
                    Text(name)
                        .font(.zoomTileName)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 6).padding(.vertical, 3)
                        .background(RoundedRectangle(cornerRadius: 4).fill(Color.black.opacity(0.45)))
                    Spacer()
                }
                .padding(8)
            }
        }
        .clipShape(RoundedRectangle(cornerRadius: 8))
        .overlay(
            RoundedRectangle(cornerRadius: 8)
                .strokeBorder(Color.zoomBlue, lineWidth: isActiveSpeaker ? 3 : 0)
        )
        .animation(.easeInOut(duration: 0.22), value: isActiveSpeaker)
    }

    private func initials(_ s: String) -> String {
        s.split(separator: " ").prefix(2).compactMap { $0.first }.map(String.init).joined()
    }
}
```

### Primary "Join" Button (the signature CTA)

```swift
struct JoinButton: View {
    var title: String = "Join"
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.zoomButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.zoomBlue))
        }
        .buttonStyle(ZoomPressable(pressedColor: .zoomBluePressed))
        .sensoryFeedback(.impact(weight: .medium), trigger: title)
    }
}

struct ZoomPressable: ButtonStyle {
    var pressedColor: Color
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .overlay(configuration.isPressed
                ? RoundedRectangle(cornerRadius: 8).fill(pressedColor).blendMode(.multiply)
                : nil)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### In-Call Control Bar

```swift
struct ControlBar: View {
    @Binding var micOn: Bool
    @Binding var videoOn: Bool
    let onLeave: () -> Void

    var body: some View {
        HStack(spacing: 0) {
            ControlButton(icon: micOn ? "mic.fill" : "mic.slash.fill",
                          label: micOn ? "Mute" : "Unmute",
                          tint: micOn ? .white : .zoomRed) { micOn.toggle() }
            ControlButton(icon: videoOn ? "video.fill" : "video.slash.fill",
                          label: videoOn ? "Stop Video" : "Start Video",
                          tint: .white) { videoOn.toggle() }
            ControlButton(icon: "square.and.arrow.up", label: "Share", tint: .white) {}
            ControlButton(icon: "person.2.fill", label: "Participants", tint: .white) {}
            ControlButton(icon: "face.smiling", label: "React", tint: .white) {}

            Button(action: onLeave) {
                Text("Leave")
                    .font(.zoom(15, weight: .bold))
                    .foregroundStyle(.white)
                    .padding(.horizontal, 18).padding(.vertical, 8)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.zoomRed))
            }
            .padding(.leading, 4)
        }
        .padding(.horizontal, 12)
        .frame(height: 72)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.zoomSurface1.opacity(0.96)))
        .shadow(color: .black.opacity(0.4), radius: 24, y: 8)
        .padding(.horizontal, 12)
    }
}

struct ControlButton: View {
    let icon: String; let label: String; let tint: Color; let action: () -> Void
    var body: some View {
        Button(action: action) {
            VStack(spacing: 4) {
                Image(systemName: icon).font(.system(size: 24)).foregroundStyle(tint)
                Text(label).font(.zoomControlLabel).foregroundStyle(tint)
            }
            .frame(maxWidth: .infinity)
        }
    }
}
```

### Meeting List Row

```swift
struct MeetingRow: View {
    let time: String
    let topic: String
    let subtitle: String
    let onJoin: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            Text(time)
                .font(.zoomMetadata)
                .foregroundStyle(.zoomTextSecondary)
                .frame(width: 64, alignment: .leading)
                .zoomTabular()

            VStack(alignment: .leading, spacing: 4) {
                Text(topic).font(.zoomListTitle).foregroundStyle(.white)
                Text(subtitle).font(.zoomMetadata).foregroundStyle(.zoomTextSecondary)
            }

            Spacer()

            Button(action: onJoin) {
                Text("Join")
                    .font(.zoom(14, weight: .bold))
                    .foregroundStyle(.white)
                    .padding(.horizontal, 18)
                    .frame(height: 32)
                    .background(Capsule().fill(Color.zoomBlue))
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .background(Color.zoomSurface1)
    }
}
```

### Recording Indicator

```swift
struct RecordingIndicator: View {
    @State private var pulse = false
    var body: some View {
        HStack(spacing: 6) {
            Circle()
                .fill(Color.zoomRed)
                .frame(width: 10, height: 10)
                .scaleEffect(pulse ? 0.7 : 1.0)
                .animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: pulse)
            Text("RECORDING")
                .font(.zoomTinyUpper)
                .foregroundStyle(.white)
                .tracking(0.4)
        }
        .padding(.horizontal, 10).padding(.vertical, 6)
        .background(Capsule().fill(Color.black.opacity(0.45)))
        .onAppear { pulse = true }
    }
}
```

## 4. Gallery Grid Layout

The grid recomputes columns from participant count. Use a `LazyVGrid` with adaptive layouts; pin the active speaker to a stage in speaker view.

```swift
struct GalleryGrid: View {
    let participants: [Participant]
    var body: some View {
        let columns = gridColumns(for: participants.count)
        LazyVGrid(columns: columns, spacing: 4) {
            ForEach(participants) { p in
                GalleryTile(name: p.name, isMuted: p.isMuted, isActiveSpeaker: p.isSpeaking)
                    .aspectRatio(16/9, contentMode: .fit)
            }
        }
        .padding(4)
        .animation(.easeInOut(duration: 0.3), value: participants.count)
    }

    private func gridColumns(for n: Int) -> [GridItem] {
        let cols = n <= 1 ? 1 : n <= 4 ? 2 : 3
        return Array(repeating: GridItem(.flexible(), spacing: 4), count: cols)
    }
}

struct Participant: Identifiable { let id = UUID(); let name: String; let isMuted: Bool; let isSpeaking: Bool }
```

## 5. Tab Bar

Use `TabView` with a `UITabBarAppearance` that adapts to light/dark. The active tint is Zoom Blue.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor { $0.userInterfaceStyle == .dark
            ? UIColor(Color.zoomSurface1) : UIColor.white }
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            MeetingsView().tabItem  { Label("Meetings",  systemImage: "video.fill") }
            ChatView().tabItem      { Label("Team Chat", systemImage: "bubble.left.and.bubble.right.fill") }
            MailView().tabItem      { Label("Mail",      systemImage: "envelope.fill") }
            PhoneView().tabItem     { Label("Phone",     systemImage: "phone.fill") }
            MoreView().tabItem      { Label("More",      systemImage: "ellipsis") }
        }
        .tint(.zoomBlue)
    }
}
```

## 6. Motion

```swift
// Join button tap
.sensoryFeedback(.impact(weight: .medium), trigger: didJoin)

// Active-speaker border (debounced upstream by ~300ms)
.animation(.easeInOut(duration: 0.22), value: isActiveSpeaker)

// Control bar auto-hide
.offset(y: barHidden ? 16 : 0)
.opacity(barHidden ? 0 : 1)
.animation(.easeOut(duration: 0.25), value: barHidden)
// Restore on stage tap:
.onTapGesture { withAnimation(.spring(response: 0.2, dampingFraction: 0.8)) { barHidden = false } }

// Reactions float: animate offset y: 0 → -120 + opacity 1 → 0 over ~3s

// Recording dot: repeatForever scale 1.0 ↔ 0.7 over 1.2s
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Mic on / off | `mic.fill` / `mic.slash.fill` | 24pt |
| Video on / off | `video.fill` / `video.slash.fill` | 24pt |
| Share | `square.and.arrow.up` | 24pt |
| Participants | `person.2.fill` | 24pt |
| React | `face.smiling` | 24pt |
| Leave (red) | `phone.down.fill` | 20pt |
| Raise hand | `hand.raised.fill` | 22pt |
| Camera flip | `arrow.triangle.2.circlepath.camera` | 20pt |
| Search | `magnifyingglass` | 16pt |
| Meetings (tab) | `video.fill` | 24pt |
| Team Chat (tab) | `bubble.left.and.bubble.right.fill` | 24pt |
| Mail (tab) | `envelope.fill` | 24pt |
| Phone (tab) | `phone.fill` | 24pt |
| More (tab) | `ellipsis` | 24pt |
| Send (chat) | `arrow.up` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on meeting titles, chat body, and metadata — clamp tile name labels at 15pt and pin the call timer / meeting IDs (tabular, layout-critical) and 11pt control labels
- VoiceOver: announce mic/video state changes ("Microphone muted"); label the active-speaker tile ("Alex Rivera, speaking"); the Leave button must read "Leave meeting, button"
- The control bar must remain reachable for assistive tech even while auto-hidden — keep it in the accessibility tree and disable auto-hide when `accessibilityReduceMotion` or VoiceOver is on
- Contrast: `#B0B0B0` on `#1A1A1A` passes WCAG AA at 13pt+; validate the 10pt tab and 11pt control labels and bump toward `#C4C4C4` if targeting strict compliance
- Respect `accessibilityReduceMotion`: drop the recording-dot pulse and the active-speaker scale; keep a static border instead
