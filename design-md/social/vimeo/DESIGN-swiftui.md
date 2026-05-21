# Vimeo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vimeo's cinematic visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the player, Staff Pick badge, and curated watch feed.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — primary)
    static let vimeoCanvas    = Color(red: 0.051, green: 0.055, blue: 0.071) // #0D0E12
    static let vimeoSurface1  = Color(red: 0.086, green: 0.094, blue: 0.122) // #16181F
    static let vimeoSurface2  = Color(red: 0.122, green: 0.133, blue: 0.169) // #1F222B
    static let vimeoDivider   = Color(red: 0.149, green: 0.165, blue: 0.204) // #262A34

    // MARK: - Canvas & Surfaces (Light — rare)
    static let vimeoLightCanvas   = Color(red: 1.0,   green: 1.0,   blue: 1.0)   // #FFFFFF
    static let vimeoLightSurface1 = Color(red: 0.961, green: 0.965, blue: 0.973) // #F5F6F8
    static let vimeoLightDivider  = Color(red: 0.886, green: 0.898, blue: 0.918) // #E2E5EA

    // MARK: - Text
    static let vimeoTextPrimary   = Color.white                                  // #FFFFFF
    static let vimeoTextSecondary = Color(red: 0.682, green: 0.714, blue: 0.761) // #AEB6C2
    static let vimeoTextTertiary  = Color(red: 0.420, green: 0.447, blue: 0.502) // #6B7280
    static let vimeoOnBlue        = Color(red: 0.016, green: 0.071, blue: 0.102) // #04121A

    // MARK: - Brand
    static let vimeoBlue        = Color(red: 0.0,   green: 0.678, blue: 0.937) // #00ADEF
    static let vimeoBlueLegacy  = Color(red: 0.102, green: 0.718, blue: 0.918) // #1AB7EA
    static let vimeoBluePressed = Color(red: 0.0,   green: 0.561, blue: 0.769) // #008FC4

    // MARK: - Accent & Curation
    static let vimeoStaffGold   = Color(red: 1.0,   green: 0.824, blue: 0.298) // #FFD24C
    static let vimeoPlusPurple  = Color(red: 0.545, green: 0.361, blue: 0.965) // #8B5CF6

    // MARK: - Semantic
    static let vimeoSuccess = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let vimeoError   = Color(red: 1.0,   green: 0.302, blue: 0.310) // #FF4D4F
    static let vimeoLive    = Color(red: 1.0,   green: 0.176, blue: 0.333) // #FF2D55
}
```

## 2. Typography

Vimeo's UI face is **Inter** (SIL OFL — free to bundle). Register the TTFs via `Info.plist` / `UIAppFonts`. Always use tabular figures for numeric values (timecodes, play counts).

```swift
extension Font {
    static func vimeo(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .bold, .heavy, .black: return "Inter-Bold"
            case .semibold:             return "Inter-SemiBold"
            case .medium:               return "Inter-Medium"
            default:                    return "Inter-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let vimeoDisplay     = Font.custom("Inter-ExtraBold", size: 32)
    static let vimeoScreenTitle = Font.custom("Inter-Bold",      size: 26)
    static let vimeoSection     = Font.custom("Inter-Bold",      size: 22)
    static let vimeoVideoTitle  = Font.custom("Inter-Bold",      size: 18)
    static let vimeoCardTitle   = Font.custom("Inter-SemiBold",  size: 17)
    static let vimeoBody        = Font.custom("Inter-Medium",    size: 15)
    static let vimeoBodyRead    = Font.custom("Inter-Regular",   size: 15)
    static let vimeoMeta        = Font.custom("Inter-Regular",   size: 13)
    static let vimeoCreator     = Font.custom("Inter-SemiBold",  size: 13)
    static let vimeoButton      = Font.custom("Inter-Bold",      size: 15)
    static let vimeoPill        = Font.custom("Inter-Bold",      size: 12)
    static let vimeoOverline    = Font.custom("Inter-SemiBold",  size: 11)
    static let vimeoTimecode    = Font.custom("Inter-Bold",      size: 11) // apply .monospacedDigit()
    static let vimeoTab         = Font.custom("Inter-Medium",    size: 10)
}

// Apply tabular figures to any numeric Text:
extension View { func vimeoTabular() -> some View { self.monospacedDigit() } }
```

## 3. Signature Components

### Cinematic Video Player

```swift
struct VimeoPlayer: View {
    let elapsed: String      // "1:24"
    let total: String        // "4:07"
    @Binding var progress: Double // 0...1
    @State private var controlsVisible = true
    @State private var idleTask: Task<Void, Never>?

    var body: some View {
        ZStack {
            LinearGradient(colors: [Color(red: 0.086, green: 0.220, blue: 0.290),
                                    Color(red: 0.031, green: 0.082, blue: 0.114)],
                           startPoint: .topLeading, endPoint: .bottomTrailing)

            if controlsVisible {
                Circle()
                    .fill(Color.vimeoCanvas.opacity(0.55))
                    .frame(width: 52, height: 52)
                    .overlay(Circle().strokeBorder(.white.opacity(0.55), lineWidth: 1.5))
                    .overlay(
                        Image(systemName: "play.fill")
                            .font(.system(size: 18)).foregroundStyle(.white)
                            .offset(x: 2)
                    )
                    .transition(.opacity)
            }

            VStack {
                Spacer()
                if controlsVisible {
                    HStack(spacing: 8) {
                        Text(elapsed).font(.vimeoTimecode).vimeoTabular().foregroundStyle(.white)
                        ScrubBar(progress: $progress)
                        Text(total).font(.vimeoTimecode).vimeoTabular().foregroundStyle(.white)
                        Text("HD")
                            .font(.vimeo(9, weight: .bold)).foregroundStyle(.white)
                            .padding(.horizontal, 4).padding(.vertical, 1)
                            .overlay(RoundedRectangle(cornerRadius: 3).strokeBorder(.white.opacity(0.5), lineWidth: 1))
                    }
                    .padding(.horizontal, 12).frame(height: 34)
                    .background(LinearGradient(colors: [Color(red: 0.031, green: 0.082, blue: 0.114).opacity(0.85), .clear],
                                               startPoint: .bottom, endPoint: .top))
                    .transition(.opacity)
                }
            }
        }
        .aspectRatio(16.0/9.0, contentMode: .fit)
        .clipShape(RoundedRectangle(cornerRadius: 14))
        .contentShape(Rectangle())
        .onTapGesture { toggleControls() }
        .onAppear { scheduleIdle() }
    }

    private func toggleControls() {
        withAnimation(.easeOut(duration: 0.2)) { controlsVisible.toggle() }
        if controlsVisible { scheduleIdle() }
    }
    private func scheduleIdle() {
        idleTask?.cancel()
        idleTask = Task {
            try? await Task.sleep(nanoseconds: 3_000_000_000)
            if !Task.isCancelled {
                await MainActor.run { withAnimation(.easeOut(duration: 0.25)) { controlsVisible = false } }
            }
        }
    }
}

struct ScrubBar: View {
    @Binding var progress: Double
    @State private var dragging = false

    var body: some View {
        GeometryReader { geo in
            ZStack(alignment: .leading) {
                Capsule().fill(Color.white.opacity(0.22)).frame(height: 3)
                Capsule().fill(Color.vimeoBlue)
                    .frame(width: geo.size.width * progress, height: 3)
                Circle().fill(.white)
                    .frame(width: dragging ? 16 : 9, height: dragging ? 16 : 9)
                    .shadow(color: .black.opacity(0.6), radius: 4, y: 1)
                    .offset(x: geo.size.width * progress - (dragging ? 8 : 4.5))
            }
            .frame(maxHeight: .infinity, alignment: .center)
            .contentShape(Rectangle())
            .gesture(
                DragGesture(minimumDistance: 0)
                    .onChanged { v in
                        dragging = true
                        progress = min(max(0, v.location.x / geo.size.width), 1)
                    }
                    .onEnded { _ in withAnimation(.easeOut(duration: 0.12)) { dragging = false } }
            )
        }
        .frame(height: 16)
    }
}
```

### Staff Pick Badge

```swift
struct StaffPickBadge: View {
    var compact: Bool = false   // mini circle for thumbnail corner

    var body: some View {
        if compact {
            Circle().fill(Color.vimeoStaffGold)
                .frame(width: 16, height: 16)
                .overlay(Image(systemName: "star.fill").font(.system(size: 9)).foregroundStyle(Color.vimeoOnBlue))
        } else {
            HStack(spacing: 4) {
                Image(systemName: "star.fill").font(.system(size: 10))
                Text("Staff Pick").font(.vimeo(10, weight: .bold))
            }
            .foregroundStyle(Color.vimeoOnBlue)
            .padding(.horizontal, 8).padding(.vertical, 3)
            .background(Capsule().fill(Color.vimeoStaffGold))
        }
    }
}
```

### Curated Watch Feed Row

```swift
struct WatchFeedRow: View {
    let title: String
    let creator: String
    let stats: String
    let duration: String

    var body: some View {
        HStack(alignment: .top, spacing: 11) {
            ZStack(alignment: .topLeading) {
                RoundedRectangle(cornerRadius: 8)
                    .fill(LinearGradient(colors: [Color(red: 0.29, green: 0.42, blue: 0.48),
                                                  Color(red: 0.12, green: 0.20, blue: 0.24)],
                                         startPoint: .topLeading, endPoint: .bottomTrailing))
                    .aspectRatio(16.0/9.0, contentMode: .fit)
                    .frame(width: 124)
                StaffPickBadge(compact: true).padding(5)
                VStack { Spacer(); HStack { Spacer()
                    Text(duration).font(.vimeo(9, weight: .bold)).vimeoTabular()
                        .foregroundStyle(.white)
                        .padding(.horizontal, 4).padding(.vertical, 1)
                        .background(RoundedRectangle(cornerRadius: 3).fill(.black.opacity(0.7)))
                } }.padding(5)
            }
            .frame(width: 124)
            .clipShape(RoundedRectangle(cornerRadius: 8))

            VStack(alignment: .leading, spacing: 4) {
                Text(title).font(.vimeoCreator).foregroundStyle(Color.vimeoTextPrimary).lineLimit(2)
                Text("by \(creator)").font(.vimeo(11)).foregroundStyle(Color.vimeoTextTertiary)
                Text(stats).font(.vimeo(11)).foregroundStyle(Color.vimeoTextTertiary)
            }
            Spacer(minLength: 0)
        }
        .padding(.bottom, 14)
    }
}
```

### Video Meta + Follow

```swift
struct VideoMeta: View {
    let title: String
    let stats: String
    let creator: String
    let followerLine: String
    @State private var following = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text(title).font(.vimeoVideoTitle).foregroundStyle(Color.vimeoTextPrimary)
            Text(stats).font(.vimeoMeta).foregroundStyle(Color.vimeoTextSecondary).padding(.top, 5)

            HStack(spacing: 10) {
                Circle()
                    .fill(LinearGradient(colors: [.vimeoBlue, .vimeoPlusPurple],
                                         startPoint: .topLeading, endPoint: .bottomTrailing))
                    .frame(width: 34, height: 34)
                VStack(alignment: .leading, spacing: 1) {
                    Text(creator).font(.vimeoCreator).foregroundStyle(Color.vimeoTextPrimary)
                    Text(followerLine).font(.vimeo(11)).foregroundStyle(Color.vimeoTextTertiary)
                }
                Spacer()
                Button { withAnimation(.easeOut(duration: 0.2)) { following.toggle() } } label: {
                    Text(following ? "Following" : "Follow")
                        .font(.vimeoPill)
                        .foregroundStyle(following ? Color.vimeoTextPrimary : Color.vimeoOnBlue)
                        .padding(.horizontal, 16).padding(.vertical, 7)
                        .background(
                            Capsule().fill(following ? Color.vimeoSurface2 : Color.vimeoBlue)
                                .overlay(Capsule().strokeBorder(Color.vimeoDivider, lineWidth: following ? 1 : 0))
                        )
                }
                .sensoryFeedback(.impact(flexibility: .soft), trigger: following)
            }
            .padding(.top, 12)
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct VimeoTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            UploadView().tabItem { Label("", systemImage: "play.circle.fill") } // center, emphasized
            InboxView().tabItem { Label("Inbox", systemImage: "envelope.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.vimeoBlue) // active = Vimeo Blue, no tint pill
        .toolbarBackground(Color.vimeoCanvas.opacity(0.94), for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Player controls auto-fade
withAnimation(.easeOut(duration: 0.25)) { controlsVisible = false } // after 3s idle
withAnimation(.easeOut(duration: 0.2))  { controlsVisible = true  } // on tap

// Scrub knob grow
.frame(width: dragging ? 16 : 9, height: dragging ? 16 : 9) // 120ms ease-out

// Follow toggle
withAnimation(.easeOut(duration: 0.2)) { following.toggle() } // label crossfade + bg color

// Like heart spring
withAnimation(.spring(response: 0.26, dampingFraction: 0.6)) { liked.toggle() }

// Thumbnail load — crossfade from gradient placeholder
.transition(.opacity) // 220ms

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: following)
.sensoryFeedback(.selection, trigger: selectedTab)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Upload (center) | `play.circle.fill` | 26pt |
| Inbox (tab) | `envelope` / `envelope.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Play (player) | `play.fill` | 18pt |
| Pause | `pause.fill` | 18pt |
| Staff Pick star | `star.fill` | 9–10pt |
| Notifications | `bell` | 21pt |
| Like | `heart` / `heart.fill` | 21pt |
| Comment | `bubble.left` | 21pt |
| Share | `square.and.arrow.up` | 21pt |
| Watch Later | `clock` / `clock.fill` | 21pt |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | 16pt |
| AirPlay | `airplayvideo` | 18pt |
| Settings (player) | `gearshape` | 18pt |
| More | `ellipsis` | 20pt |

## 7. Dark Mode

```swift
struct VimeoTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.vimeoCanvas : Color.vimeoLightCanvas)
            .foregroundStyle(scheme == .dark ? Color.vimeoTextPrimary : Color(red: 0.063, green: 0.075, blue: 0.102))
            .preferredColorScheme(.dark) // Vimeo is dark-first
    }
}
extension View { func vimeoTheme() -> some View { modifier(VimeoTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Inter TTFs (Regular/Medium/SemiBold/Bold/ExtraBold) via `Info.plist` — SIL OFL, free to distribute
- Vimeo is dark-first: ship `.preferredColorScheme(.dark)`; light mode is a parity fallback only
- Dynamic Type: support on screen titles, video titles, body, descriptions; keep timecodes, tab labels, overlines, duration/quality chips, pill text FIXED (use `.monospacedDigit()` on all numeric values)
- VoiceOver: label the player "Video player, {title}, {elapsed} of {total}"; the scrubber as an adjustable element (`.accessibilityValue` percent, increment/decrement = ±10s); the Staff Pick badge "Staff Pick — editor selected"
- The scrubber must be operable via the VoiceOver rotor — expose `.accessibilityAdjustableAction`
- Color contrast: `#FFFFFF` and `#AEB6C2` on `#0D0E12` pass WCAG AA; `#04121A` on `#00ADEF` and on `#FFD24C` pass AA — never put white text on the gold badge
- Reduce Motion: disable the controls fade (instant show/hide) and the like spring (use a crossfade); keep scrub feedback
- Captions: respect the system "Closed Captions + SDH" setting; surface a CC toggle in player controls
