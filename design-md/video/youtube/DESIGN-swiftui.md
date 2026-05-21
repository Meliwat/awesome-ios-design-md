# YouTube (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates YouTube's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, Subscribe button, video card, mini-player.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let ytRed        = Color(red: 1.0,   green: 0.0,   blue: 0.0)   // #FF0000
    static let ytRedPressed = Color(red: 0.8,   green: 0.0,   blue: 0.0)   // #CC0000
    static let ytRedHover   = Color(red: 0.902, green: 0.0,   blue: 0.0)   // #E60000

    // MARK: - Light Canvas
    static let ytCanvasLight   = Color.white                                  // #FFFFFF
    static let ytSurface1Light = Color(red: 0.976, green: 0.976, blue: 0.976) // #F9F9F9
    static let ytSurface2Light = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let ytDividerLight  = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5

    // MARK: - Dark Canvas
    static let ytCanvasDark   = Color(red: 0.059, green: 0.059, blue: 0.059) // #0F0F0F
    static let ytSurface1Dark = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let ytSurface2Dark = Color(red: 0.153, green: 0.153, blue: 0.153) // #272727
    static let ytSurface3Dark = Color(red: 0.247, green: 0.247, blue: 0.247) // #3F3F3F
    static let ytDividerDark  = Color(red: 0.188, green: 0.188, blue: 0.188) // #303030

    // MARK: - Text (Light)
    static let ytTextPrimaryLight   = Color(red: 0.059, green: 0.059, blue: 0.059) // #0F0F0F
    static let ytTextSecondaryLight = Color(red: 0.376, green: 0.376, blue: 0.376) // #606060
    static let ytTextTertiaryLight  = Color(red: 0.565, green: 0.565, blue: 0.565) // #909090

    // MARK: - Text (Dark)
    static let ytTextPrimaryDark   = Color.white
    static let ytTextSecondaryDark = Color(red: 0.667, green: 0.667, blue: 0.667) // #AAAAAA
    static let ytTextTertiaryDark  = Color(red: 0.443, green: 0.443, blue: 0.443) // #717171

    // MARK: - Semantic
    static let ytInfoBlue = Color(red: 0.243, green: 0.651, blue: 1.0) // #3EA6FF
}
```

## 2. Typography

YouTube Sans (proprietary) + Roboto. Bundle via `Info.plist` `UIAppFonts`. Fall back to SF Pro.

```swift
extension Font {
    // Display (YouTube Sans)
    static let ytScreenTitle     = Font.custom("YouTubeSans-Bold",       size: 20).weight(.bold)
    static let ytShortsCaption   = Font.custom("YouTubeSans-Medium",     size: 15).weight(.medium)

    // Body (Roboto)
    static let ytVideoDetailTitle = Font.custom("Roboto-Medium",   size: 18).weight(.semibold)
    static let ytVideoTitle       = Font.custom("Roboto-Medium",   size: 16).weight(.medium)
    static let ytChannelName      = Font.custom("Roboto-Medium",   size: 14).weight(.medium)
    static let ytMetadata         = Font.custom("Roboto-Regular",  size: 13).weight(.regular)
    static let ytCommentBody      = Font.custom("Roboto-Regular",  size: 14).weight(.regular)
    static let ytCommentAuthor    = Font.custom("Roboto-Medium",   size: 13).weight(.medium)
    static let ytBody             = Font.custom("Roboto-Regular",  size: 14).weight(.regular)
    static let ytButton           = Font.custom("Roboto-Medium",   size: 14).weight(.medium)
    static let ytChip             = Font.custom("Roboto-Regular",  size: 14).weight(.regular)
    static let ytTabLabel         = Font.custom("Roboto-Medium",   size: 10).weight(.medium)
    static let ytDurationTag      = Font.custom("Roboto-Medium",   size: 11).weight(.medium)
    static let ytTimestamp        = Font.custom("Roboto-Regular",  size: 12).weight(.regular)

    static func yt(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Subscribe Button

```swift
struct SubscribeButton: View {
    @Binding var isSubscribed: Bool
    @State private var bellOn = false

    var body: some View {
        HStack(spacing: 0) {
            Button {
                withAnimation(.spring(response: 0.25, dampingFraction: 0.8)) {
                    isSubscribed.toggle()
                }
                UIImpactFeedbackGenerator(style: .light).impactOccurred()
            } label: {
                Text(isSubscribed ? "Subscribed" : "Subscribe")
                    .font(.ytButton)
                    .foregroundStyle(isSubscribed ? Color.ytTextPrimaryLight : .white)
                    .padding(.vertical, 8)
                    .padding(.horizontal, 16)
                    .background(
                        RoundedRectangle(cornerRadius: 18)
                            .fill(isSubscribed ? Color.ytSurface2Light : Color.ytRed)
                    )
            }
            .buttonStyle(.plain)

            if isSubscribed {
                Button {
                    bellOn.toggle()
                    UIImpactFeedbackGenerator(style: .light).impactOccurred()
                } label: {
                    Image(systemName: bellOn ? "bell.fill" : "bell")
                        .font(.system(size: 16))
                        .foregroundStyle(Color.ytTextPrimaryLight)
                        .padding(.horizontal, 12)
                        .padding(.vertical, 8)
                        .background(
                            RoundedRectangle(cornerRadius: 18)
                                .fill(Color.ytSurface2Light)
                        )
                }
                .buttonStyle(.plain)
                .padding(.leading, 4)
                .transition(.move(edge: .trailing).combined(with: .opacity))
            }
        }
    }
}
```

### Video Card (Feed)

```swift
struct VideoCard: View {
    let thumbnailURL: URL
    let duration: String         // "12:34"
    let isLive: Bool
    let title: String
    let channelName: String
    let channelAvatarURL: URL
    let viewCount: String        // "1.2M views"
    let uploadedAgo: String      // "3 days ago"

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // Thumbnail with duration tag
            ZStack(alignment: .bottomTrailing) {
                AsyncImage(url: thumbnailURL) { img in
                    img.resizable().scaledToFill()
                } placeholder: { Color.ytSurface2Light }
                    .aspectRatio(16/9, contentMode: .fit)
                    .clipped()

                if isLive {
                    HStack(spacing: 4) {
                        Circle().fill(Color.white).frame(width: 6, height: 6)
                        Text("LIVE")
                            .font(.custom("Roboto-Bold", size: 11))
                            .foregroundStyle(.white)
                    }
                    .padding(.horizontal, 6).padding(.vertical, 4)
                    .background(RoundedRectangle(cornerRadius: 4).fill(Color.ytRed))
                    .padding(6)
                    .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                } else {
                    Text(duration)
                        .font(.ytDurationTag)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 6).padding(.vertical, 4)
                        .background(RoundedRectangle(cornerRadius: 4).fill(Color.black.opacity(0.75)))
                        .padding(6)
                }
            }

            HStack(alignment: .top, spacing: 12) {
                AsyncImage(url: channelAvatarURL) { img in
                    img.resizable().scaledToFill()
                } placeholder: { Color.ytSurface2Light }
                    .frame(width: 28, height: 28)
                    .clipShape(Circle())

                VStack(alignment: .leading, spacing: 4) {
                    Text(title)
                        .font(.ytVideoTitle)
                        .foregroundStyle(Color.ytTextPrimaryLight)
                        .lineLimit(2)
                    Text("\(channelName) · \(viewCount) · \(uploadedAgo)")
                        .font(.ytMetadata)
                        .foregroundStyle(Color.ytTextSecondaryLight)
                        .lineLimit(1)
                }

                Spacer(minLength: 8)

                Button { /* overflow */ } label: {
                    Image(systemName: "ellipsis")
                        .font(.system(size: 20))
                        .foregroundStyle(Color.ytTextPrimaryLight)
                }
            }
            .padding(.horizontal, 16)
            .padding(.top, 12)
            .padding(.bottom, 16)
        }
    }
}
```

### Action Pill (Like / Dislike / Share)

```swift
struct ActionPill: View {
    let systemIcon: String
    let label: String?
    let isActive: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: systemIcon)
                    .font(.system(size: 20))
                if let label {
                    Text(label)
                        .font(.ytMetadata)
                        .fontWeight(.medium)
                }
            }
            .foregroundStyle(Color.ytTextPrimaryLight)
            .padding(.horizontal, 12)
            .padding(.vertical, 8)
            .background(RoundedRectangle(cornerRadius: 18).fill(Color.ytSurface2Light))
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isActive)
    }
}
```

### Mini-Player

```swift
struct MiniPlayer: View {
    let thumbnailURL: URL
    let title: String
    let channelName: String
    @Binding var isPlaying: Bool
    let onExpand: () -> Void
    let onDismiss: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            AsyncImage(url: thumbnailURL) { img in
                img.resizable().scaledToFill()
            } placeholder: { Color.ytSurface2Light }
                .frame(width: 128, height: 72)
                .clipped()

            VStack(alignment: .leading, spacing: 2) {
                Text(title)
                    .font(.ytChannelName)
                    .foregroundStyle(Color.ytTextPrimaryLight)
                    .lineLimit(1)
                Text(channelName)
                    .font(.system(size: 12, weight: .regular))
                    .foregroundStyle(Color.ytTextSecondaryLight)
                    .lineLimit(1)
            }
            Spacer()

            Button {
                isPlaying.toggle()
                UIImpactFeedbackGenerator(style: .light).impactOccurred()
            } label: {
                Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.ytTextPrimaryLight)
            }
            Button(action: onDismiss) {
                Image(systemName: "xmark")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.ytTextPrimaryLight)
            }
            .padding(.trailing, 8)
        }
        .frame(height: 72)
        .background(Color.ytCanvasLight)
        .contentShape(Rectangle())
        .onTapGesture { onExpand() }
    }
}
```

### Filter Chip Row (Home)

```swift
struct FilterChip: View {
    let label: String
    let isSelected: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.ytChip)
                .foregroundStyle(isSelected ? Color.ytCanvasLight : Color.ytTextPrimaryLight)
                .padding(.vertical, 8)
                .padding(.horizontal, 12)
                .background(
                    Capsule().fill(isSelected ? Color.ytTextPrimaryLight : Color.ytSurface2Light)
                )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.selection, trigger: isSelected)
    }
}

struct FilterChipRow: View {
    let chips: [String]
    @State private var selected: String = "All"

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(chips, id: \.self) { chip in
                    FilterChip(label: chip, isSelected: selected == chip) {
                        withAnimation(.easeInOut(duration: 0.2)) { selected = chip }
                    }
                }
            }
            .padding(.horizontal, 12)
        }
    }
}
```

### Shorts Right-Rail

```swift
struct ShortsActionRail: View {
    let creatorAvatarURL: URL
    let likeCount: String
    let commentCount: String
    @Binding var isLiked: Bool

    var body: some View {
        VStack(spacing: 20) {
            ZStack(alignment: .bottomTrailing) {
                AsyncImage(url: creatorAvatarURL) { img in
                    img.resizable().scaledToFill()
                } placeholder: { Color.gray }
                    .frame(width: 44, height: 44)
                    .clipShape(Circle())
                    .overlay(Circle().stroke(.white, lineWidth: 2))
                Image(systemName: "plus.circle.fill")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.ytRed, .white)
                    .offset(x: 4, y: 4)
            }

            VStack(spacing: 4) {
                Button {
                    isLiked.toggle()
                    UIImpactFeedbackGenerator(style: .light).impactOccurred()
                } label: {
                    Image(systemName: isLiked ? "hand.thumbsup.fill" : "hand.thumbsup")
                        .font(.system(size: 28))
                        .foregroundStyle(.white)
                        .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
                }
                Text(likeCount)
                    .font(.system(size: 13, weight: .medium))
                    .foregroundStyle(.white)
                    .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
            }

            VStack(spacing: 4) {
                Image(systemName: "hand.thumbsdown")
                    .font(.system(size: 28))
                    .foregroundStyle(.white)
                    .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
            }

            VStack(spacing: 4) {
                Image(systemName: "bubble.right")
                    .font(.system(size: 28))
                    .foregroundStyle(.white)
                    .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
                Text(commentCount)
                    .font(.system(size: 13, weight: .medium))
                    .foregroundStyle(.white)
                    .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
            }

            Image(systemName: "arrowshape.turn.up.right")
                .font(.system(size: 28))
                .foregroundStyle(.white)
                .shadow(color: .black.opacity(0.4), radius: 2, y: 1)
        }
    }
}
```

## 4. Tab Bar

```swift
struct YouTubeTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.systemBackground
        appearance.shadowColor = UIColor(Color.ytDividerLight)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()
                .tabItem { Label("Home", systemImage: "house.fill") }
            ShortsView()
                .tabItem { Label("Shorts", systemImage: "play.rectangle.fill") }
            CreateSheet()
                .tabItem { Label("", systemImage: "plus.circle.fill") }
            SubscriptionsView()
                .tabItem { Label("Subscriptions", systemImage: "play.square.stack.fill") }
            YouView()
                .tabItem { Label("You", systemImage: "person.crop.circle.fill") }
        }
        .tint(Color.ytTextPrimaryLight)
    }
}
```

## 5. Motion

```swift
// Subscribe morph
.sensoryFeedback(.impact(flexibility: .soft), trigger: isSubscribed)
.animation(.spring(response: 0.25, dampingFraction: 0.8), value: isSubscribed)

// Mini-player ← → full player
@Namespace private var playerNS
// In mini-player: .matchedGeometryEffect(id: "playerThumb", in: playerNS)
// In full player: same .matchedGeometryEffect id
// Wrap the change in withAnimation(.spring(response: 0.35, dampingFraction: 0.8))

// Like bounce
@State private var likeScale: CGFloat = 1.0
Button(action: {
    withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) {
        likeScale = 1.15
    }
    DispatchQueue.main.asyncAfter(deadline: .now() + 0.15) {
        withAnimation(.spring(response: 0.3, dampingFraction: 0.8)) {
            likeScale = 1.0
        }
    }
}) { /* ... */ }.scaleEffect(likeScale)

// Double-tap seek (left = -10s, right = +10s)
// Use DragGesture with tap recognition + a custom overlay that fades in/out
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play / Pause (player) | `play.fill` / `pause.fill` | 32pt (main) / 20pt (mini) |
| Next / Previous | `forward.end.fill` / `backward.end.fill` | 24pt |
| Like | `hand.thumbsup` / `hand.thumbsup.fill` | 20pt (pill) / 28pt (Shorts) |
| Dislike | `hand.thumbsdown` | same sizes |
| Share | `arrowshape.turn.up.right` | 20pt |
| Download | `arrow.down.circle` / `arrow.down.circle.fill` | 20pt |
| Save (to playlist) | `plus.rectangle.on.folder` | 20pt |
| Comment | `bubble.right` | 20pt / 28pt on Shorts |
| Subscribe bell | `bell` / `bell.fill` | 16pt |
| Cast | `airplayvideo` | 22pt |
| Notifications | `bell` | 24pt |
| Search | `magnifyingglass` | 22pt |
| Home (tab) | `house` / `house.fill` | 24pt |
| Shorts (tab) | `play.rectangle` / `play.rectangle.fill` | 24pt |
| Create (tab) | `plus.circle.fill` | 28pt |
| Subscriptions (tab) | `play.square.stack` / `play.square.stack.fill` | 24pt |
| You (tab) | `person.crop.circle` / `person.crop.circle.fill` | 24pt |
| Back | `chevron.left` | 24pt |
| More | `ellipsis` | 20pt |
| Captions | `captions.bubble` | 20pt |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | 20pt |
| Close | `xmark` | 20pt |
| Live dot | custom (6pt `Circle()`) | — |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (.sensoryFeedback requires iOS 17; otherwise use `UIImpactFeedbackGenerator`)
- Dynamic Type: on for video titles, descriptions, comments; fixed for duration tag, Subscribe button text, tab labels, and Shorts captions
- VoiceOver: group video card into one tappable element; expose Subscribe and overflow as separate actions
- Color contrast: `#606060` on `#FFFFFF` meets WCAG AA for 13pt+; `#AAAAAA` on `#0F0F0F` passes. Validate at smaller sizes.
- Motion: respect `reduceMotion` — disable the double-tap-seek ripple, the mini-player spring, and the Subscribe morph spring (fall back to cross-fade)
- Autoplay: on Shorts, add a subtle audio-on indicator so users can tell the sound state; respect the device mute switch
