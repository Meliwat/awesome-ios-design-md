# Snapchat (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Snapchat's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let snapCanvas          = Color.black                                   // #000000
    static let snapSurface1        = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let snapSurface2        = Color(red: 0.173, green: 0.173, blue: 0.173) // #2C2C2C
    static let snapDivider         = Color(red: 0.200, green: 0.200, blue: 0.200) // #333333

    // MARK: - Canvas (Light, limited use)
    static let snapLightCanvas     = Color.white                                   // #FFFFFF
    static let snapLightSurface1   = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2

    // MARK: - Text
    static let snapTextPrimary     = Color.white                                   // #FFFFFF
    static let snapTextPrimaryLight = Color.black                                  // #000000
    static let snapTextSecondary   = Color(red: 0.541, green: 0.541, blue: 0.561) // #8A8A8F
    static let snapTextTertiary    = Color(red: 0.333, green: 0.333, blue: 0.333) // #555555

    // MARK: - Brand
    static let snapYellow          = Color(red: 1.000, green: 0.988, blue: 0.000) // #FFFC00
    static let snapYellowPressed   = Color(red: 0.902, green: 0.890, blue: 0.000) // #E6E300

    // MARK: - Snap Type Colors
    static let snapPhotoRed        = Color(red: 1.000, green: 0.180, blue: 0.239) // #FF2E3D
    static let snapVideoPurple     = Color(red: 0.608, green: 0.318, blue: 1.000) // #9B51FF
    static let snapChatBlue        = Color(red: 0.302, green: 0.655, blue: 1.000) // #4DA7FF
    static let snapAudioGreen      = Color(red: 0.298, green: 0.851, blue: 0.392) // #4CD964

    // MARK: - Semantic
    static let snapErrorRed        = Color(red: 1.000, green: 0.231, blue: 0.188) // #FF3B30
    static let snapSuccessGreen    = Color(red: 0.000, green: 0.847, blue: 0.451) // #00D873
    static let snapLiveRed         = Color(red: 1.000, green: 0.180, blue: 0.239) // #FF2E3D
}
```

## 2. Typography

Avenir Next ships with iOS — no font bundling required. Use `Font.custom("AvenirNext-...", size:)` directly.

```swift
extension Font {
    static let snapSplashTitle    = Font.custom("AvenirNext-Heavy",      size: 48)
    static let snapScreenTitle    = Font.custom("AvenirNext-Bold",       size: 24)
    static let snapSectionHeader  = Font.custom("AvenirNext-Bold",       size: 20)
    static let snapChatRowName    = Font.custom("AvenirNext-Bold",       size: 16)
    static let snapChatMessage    = Font.custom("AvenirNext-Medium",     size: 16)
    static let snapChatStatus     = Font.custom("AvenirNext-Medium",     size: 13)
    static let snapStoryName      = Font.custom("AvenirNext-Bold",       size: 14)
    static let snapTimestamp      = Font.custom("AvenirNext-Medium",     size: 12)
    static let snapStreakCount    = Font.custom("AvenirNext-Bold",       size: 18)
    static let snapHudLabel       = Font.custom("AvenirNext-Bold",       size: 13)
    static let snapButton         = Font.custom("AvenirNext-Bold",       size: 16)
    static let snapLensLabel      = Font.custom("AvenirNext-Bold",       size: 13)
    static let snapBitmojiCallout = Font.custom("AvenirNext-Medium",     size: 14)
    static let snapSpotlightCap   = Font.custom("AvenirNext-Medium",     size: 14)
    static let snapDiscoverTitle  = Font.custom("AvenirNext-Bold",       size: 16)
}
```

## 3. Signature Components

### Snap Capture Button

```swift
struct SnapCaptureButton: View {
    @Binding var isRecording: Bool
    @Binding var recordProgress: Double  // 0...1 over 60s
    let onPhoto: () -> Void
    let onVideoStart: () -> Void
    let onVideoStop: () -> Void
    let onFlip: () -> Void

    @State private var innerScale: CGFloat = 1.0
    @State private var lastTapTime: Date = .distantPast

    var body: some View {
        ZStack {
            // Outer yellow ring
            Circle()
                .stroke(Color.snapYellow, lineWidth: 6)
                .frame(width: 82, height: 82)
                .overlay {
                    if isRecording {
                        Circle()
                            .trim(from: 0, to: recordProgress)
                            .stroke(Color.snapYellow, style: StrokeStyle(lineWidth: 6, lineCap: .round))
                            .frame(width: 82, height: 82)
                            .rotationEffect(.degrees(-90))
                    }
                }

            // Inner white (or yellow when recording) circle
            Circle()
                .fill(isRecording ? Color.snapYellow : Color.white)
                .frame(width: 64, height: 64)
                .scaleEffect(innerScale)
        }
        .frame(width: 82, height: 82)
        .gesture(
            // Tap = photo
            TapGesture(count: 1)
                .onEnded { _ in
                    let now = Date()
                    if now.timeIntervalSince(lastTapTime) < 0.3 {
                        // double tap = flip
                        UIImpactFeedbackGenerator(style: .light).impactOccurred()
                        onFlip()
                    } else {
                        UIImpactFeedbackGenerator(style: .soft).impactOccurred()
                        withAnimation(.spring(response: 0.25)) {
                            innerScale = 0.92
                        }
                        DispatchQueue.main.asyncAfter(deadline: .now() + 0.12) {
                            withAnimation(.spring(response: 0.25)) { innerScale = 1.0 }
                        }
                        onPhoto()
                    }
                    lastTapTime = now
                }
        )
        .simultaneousGesture(
            // Long press = video
            LongPressGesture(minimumDuration: 0.35)
                .onChanged { _ in
                    if !isRecording {
                        UIImpactFeedbackGenerator(style: .medium).impactOccurred()
                        onVideoStart()
                    }
                }
                .onEnded { _ in
                    if isRecording {
                        onVideoStop()
                    }
                }
        )
        .padding(.bottom, 20)
    }
}
```

### Chat Inbox Row

```swift
struct SnapChatRow: View {
    enum SnapType { case photo, video, chat, audio, none }
    enum Direction { case incoming, outgoing }

    let name: String
    let bitmoji: Image
    let status: String           // "Received · 2m ago"
    let timestamp: String        // "2m"
    let snapType: SnapType
    let direction: Direction
    let isUnread: Bool
    let streakDays: Int?

    var body: some View {
        HStack(spacing: 12) {
            bitmoji
                .resizable()
                .frame(width: 48, height: 48)
                .clipShape(Circle())

            VStack(alignment: .leading, spacing: 4) {
                Text(name)
                    .font(.snapChatRowName)
                    .foregroundStyle(.snapTextPrimary)
                HStack(spacing: 6) {
                    if snapType != .none {
                        SnapTypeIndicator(type: snapType, direction: direction, isUnread: isUnread)
                    }
                    Text(status)
                        .font(.snapChatStatus)
                        .foregroundStyle(.snapTextSecondary)
                }
            }

            Spacer()

            VStack(alignment: .trailing, spacing: 4) {
                Text(timestamp)
                    .font(.snapTimestamp)
                    .foregroundStyle(.snapTextSecondary)
                if let days = streakDays {
                    HStack(spacing: 2) {
                        Text("🔥")
                        Text("\(days)")
                            .font(.snapStreakCount)
                            .foregroundStyle(.snapTextPrimary)
                    }
                }
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .background(Color.snapCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.snapDivider).frame(height: 1)
        }
    }
}

struct SnapTypeIndicator: View {
    let type: SnapChatRow.SnapType
    let direction: SnapChatRow.Direction
    let isUnread: Bool

    var color: Color {
        switch type {
        case .photo: .snapPhotoRed
        case .video: .snapVideoPurple
        case .chat:  .snapChatBlue
        case .audio: .snapAudioGreen
        case .none:  .snapTextSecondary
        }
    }

    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 2)
                .fill(isUnread ? color : .clear)
                .overlay(
                    RoundedRectangle(cornerRadius: 2)
                        .stroke(color, lineWidth: 1.5)
                )
                .frame(width: 16, height: 16)
            Image(systemName: direction == .incoming ? "arrow.down" : "arrow.up")
                .font(.system(size: 8, weight: .bold))
                .foregroundStyle(isUnread ? .white : color)
        }
    }
}
```

### Story Thumbnail

```swift
struct SnapStoryThumb: View {
    enum ReadState { case unread, read, live }

    let creatorName: String
    let bitmoji: Image
    let preview: Image
    let state: ReadState
    @State private var ringOpacity: Double = 1.0

    var body: some View {
        ZStack(alignment: .topLeading) {
            preview
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(width: 120, height: 200)
                .clipShape(RoundedRectangle(cornerRadius: 16))
                .overlay(
                    RoundedRectangle(cornerRadius: 16)
                        .stroke(ringColor, lineWidth: 3)
                        .opacity(ringOpacity)
                )

            bitmoji
                .resizable()
                .frame(width: 40, height: 40)
                .clipShape(Circle())
                .overlay(Circle().stroke(Color.white, lineWidth: 2))
                .padding(8)

            VStack {
                Spacer()
                Text(creatorName)
                    .font(.snapStoryName)
                    .foregroundStyle(.white)
                    .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
                    .padding(10)
            }
            .frame(width: 120, height: 200, alignment: .bottomLeading)
        }
        .onAppear {
            if state == .unread {
                withAnimation(.easeInOut(duration: 2).repeatForever(autoreverses: true)) {
                    ringOpacity = 0.7
                }
            }
        }
    }

    private var ringColor: Color {
        switch state {
        case .unread: .snapYellow
        case .read:   .snapTextTertiary
        case .live:   .snapLiveRed
        }
    }
}
```

### Chat Bubble

```swift
struct SnapChatBubble: View {
    enum Sender { case me, them }
    let text: String
    let sender: Sender

    var body: some View {
        HStack {
            if sender == .me { Spacer(minLength: 60) }
            Text(text)
                .font(.snapChatMessage)
                .foregroundStyle(.snapTextPrimary)
                .padding(.vertical, 10)
                .padding(.horizontal, 14)
                .background(
                    RoundedRectangle(cornerRadius: 20)
                        .fill(sender == .me ? Color.snapSurface2 : Color.snapSurface1)
                )
                .frame(maxWidth: UIScreen.main.bounds.width * 0.75, alignment: .leading)
            if sender == .them { Spacer(minLength: 60) }
        }
        .padding(.horizontal, 16)
    }
}
```

## 4. Snapchat-Specific Feature: Camera HUD

```swift
struct SnapCameraHUD: View {
    @State private var isRecording = false
    @State private var recordProgress: Double = 0
    @State private var selectedLens: Int = 2  // center of carousel
    @State private var flashOn = false

    private let lenses = ["😎", "🎭", "🐶", "😂", "✨"]

    var body: some View {
        GeometryReader { geo in
            ZStack {
                // Live camera feed placeholder
                Color.black
                    .overlay(
                        Image(systemName: "camera.viewfinder")
                            .font(.system(size: 80))
                            .foregroundStyle(.white.opacity(0.2))
                    )
                    .ignoresSafeArea()

                // Top HUD
                VStack {
                    HStack {
                        HUDIcon(system: "person.crop.circle.fill")
                        Spacer()
                        HUDIcon(system: flashOn ? "bolt.fill" : "bolt.slash.fill",
                                tint: flashOn ? .snapYellow : .white) {
                            flashOn.toggle()
                        }
                        HUDIcon(system: "arrow.triangle.2.circlepath.camera.fill")
                        HUDIcon(system: "magnifyingglass")
                    }
                    .padding(.horizontal, 16)
                    .padding(.top, 16)

                    Spacer()

                    // Lens carousel
                    LensCarousel(lenses: lenses, selected: $selectedLens)
                        .padding(.bottom, 16)

                    // Bottom row: memories, capture, chat
                    HStack {
                        HUDIcon(system: "photo.stack.fill", size: 32)
                        Spacer()
                        SnapCaptureButton(isRecording: $isRecording, recordProgress: $recordProgress,
                                          onPhoto: {}, onVideoStart: { isRecording = true }, onVideoStop: { isRecording = false }, onFlip: {})
                        Spacer()
                        HUDIcon(system: "bubble.left.fill", size: 32)
                    }
                    .padding(.horizontal, 32)
                    .padding(.bottom, 16)
                }
            }
        }
    }
}

struct HUDIcon: View {
    let system: String
    var size: CGFloat = 28
    var tint: Color = .white
    var action: () -> Void = {}

    var body: some View {
        Button(action: action) {
            Image(systemName: system)
                .font(.system(size: size, weight: .medium))
                .foregroundStyle(tint)
                .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
                .frame(width: 44, height: 44)
        }
    }
}

struct LensCarousel: View {
    let lenses: [String]
    @Binding var selected: Int

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 12) {
                ForEach(Array(lenses.enumerated()), id: \.offset) { i, lens in
                    let isActive = i == selected
                    Button {
                        withAnimation(.spring(response: 0.3)) { selected = i }
                    } label: {
                        ZStack {
                            Circle()
                                .fill(Color.snapSurface2)
                                .frame(width: isActive ? 72 : 60, height: isActive ? 72 : 60)
                                .overlay(
                                    Circle().stroke(Color.snapYellow, lineWidth: isActive ? 3 : 0)
                                )
                            Text(lens)
                                .font(.system(size: isActive ? 32 : 28))
                        }
                    }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

## 5. Tab Bar / Swipe Navigation

Snapchat uses 5-screen horizontal swipe navigation, not a traditional `TabView`. Use `TabView` with `.page` style OR a custom `ScrollView`:

```swift
struct RootSwipeNav: View {
    @State private var currentTab = 2  // Camera center

    var body: some View {
        TabView(selection: $currentTab) {
            MapView()      .tag(0)
            ChatListView() .tag(1)
            SnapCameraHUD().tag(2)  // Center default
            StoriesView()  .tag(3)
            SpotlightView().tag(4)
        }
        .tabViewStyle(.page(indexDisplayMode: .never))
        .ignoresSafeArea()
        .overlay(alignment: .bottom) {
            SnapNavIndicator(selected: currentTab)
                .padding(.bottom, 0)
        }
    }
}

struct SnapNavIndicator: View {
    let selected: Int
    private let icons = ["map.fill", "bubble.left.fill", "camera.fill", "play.rectangle.fill", "diamond.fill"]

    var body: some View {
        HStack(spacing: 0) {
            ForEach(Array(icons.enumerated()), id: \.offset) { i, icon in
                Image(systemName: icon)
                    .font(.system(size: 28))
                    .foregroundStyle(i == selected ? Color.white : Color.snapTextTertiary)
                    .frame(maxWidth: .infinity, minHeight: 56)
            }
        }
        .background(.black.opacity(0.5))
    }
}
```

## 6. Motion

```swift
// Capture tap
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Capture long-press start (video)
UIImpactFeedbackGenerator(style: .medium).impactOccurred()

// Camera flip
UIImpactFeedbackGenerator(style: .light).impactOccurred()

// Screen swipe nav (5-screen)
// TabView .page style handles springs automatically; customize with .animation(.spring())

// Streak save
// Scale flame 1.0 → 1.3 → 1.0 with spring + success haptic
UINotificationFeedbackGenerator().notificationOccurred(.success)

// Story ring pulse (unread)
.onAppear {
    withAnimation(.easeInOut(duration: 2).repeatForever(autoreverses: true)) {
        ringOpacity = 0.7
    }
}

// Bitmoji sticker pop-in
@State private var stickerScale: CGFloat = 0
.onAppear {
    withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) {
        stickerScale = 1.0
    }
}
```

## 7. SF Symbols Used

Snapchat's real app uses custom glyphs extensively, but SF Symbols provide close approximations:

| Component | Symbol | Size |
|-----------|--------|------|
| Camera (tab) | `camera.fill` / `camera` | 28pt |
| Chat (tab) | `bubble.left.fill` | 28pt |
| Map (tab) | `map.fill` | 28pt |
| Stories (tab) | `play.rectangle.fill` | 28pt |
| Spotlight (tab) | `diamond.fill` | 28pt |
| Memories (HUD) | `photo.stack.fill` | 28-32pt |
| Flash on | `bolt.fill` | 28pt |
| Flash off | `bolt.slash.fill` | 28pt |
| Camera flip | `arrow.triangle.2.circlepath.camera.fill` | 28pt |
| Search | `magnifyingglass` | 18-28pt |
| Send | `paperplane.fill` (rotated 45°) | 20pt |
| New chat | `square.and.pencil` | 24pt |
| Profile | `person.crop.circle.fill` (or bitmoji) | 32pt |
| Snap received (incoming) | `arrow.down` in square | 8pt in 16pt sq |
| Snap sent (outgoing) | `arrow.up` in square | 8pt in 16pt sq |
| Streak | 🔥 (emoji) | 18pt |
| BFF | 💛 (emoji) | 14pt |
| Birthday | 🎂 (emoji) | 14pt |
| Microphone (audio) | `mic.fill` | 20pt |
| Sticker | `face.smiling` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (Avenir Next is system-shipped; Snapchat's own features like AR lenses require iOS 15+)
- Support Dynamic Type on chat messages, chat row names, bios, settings — fix timestamps, streak counts, camera HUD labels, lens labels
- VoiceOver: camera HUD needs explicit labels on every floating icon ("Flash, Off", "Camera, switch to front-facing"); capture button announces "Snap camera, double tap to capture photo, press and hold to record video"
- The swipe navigation is disorienting for VoiceOver users — provide a rotor-accessible "Go to screen" menu that lists Map / Chat / Camera / Stories / Spotlight as discrete choices
- Contrast: camera HUD icons rely on drop-shadow legibility over any photographic background; provide a "Always show chrome background" toggle in accessibility settings (blue dot: opaque pill background behind each icon)
- Reduce Motion: respect `@Environment(\.accessibilityReduceMotion)` — disable the story ring pulse, skip the screen-flip animation on camera double-tap, still fire haptics
- Reduce Transparency: the semi-transparent nav indicator should fall back to solid black when enabled
- Camera button: the long-press-for-video gesture is not accessible — provide an explicit "Record Video" mode toggle in accessibility alternatives
- Bitmoji is decorative for VoiceOver — mark bitmoji images with `.accessibilityHidden(true)` and rely on the adjacent name text for identification
- Flash / Live indicators: text labels "Live" and "Recording" must always accompany the red color (not color-alone semantic)
