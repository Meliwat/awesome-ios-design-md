# Microsoft Teams (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Teams' visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Teams is dual-identity, so colors are resolved against the environment color scheme.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // Resolve light/dark at the call site
    static func teams(_ light: Color, _ dark: Color, _ scheme: ColorScheme) -> Color {
        scheme == .dark ? dark : light
    }

    // MARK: - Canvas & Surfaces (light)
    static let teamsLightCanvas   = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let teamsLightSurface1 = Color.white                                  // #FFFFFF
    static let teamsLightSurface2 = Color(red: 0.941, green: 0.941, blue: 0.941) // #F0F0F0
    static let teamsLightDivider  = Color(red: 0.882, green: 0.882, blue: 0.882) // #E1E1E1
    static let teamsLightText1    = Color(red: 0.145, green: 0.141, blue: 0.137) // #252423
    static let teamsLightText2    = Color(red: 0.380, green: 0.380, blue: 0.380) // #616161

    // MARK: - Canvas & Surfaces (dark)
    static let teamsDarkCanvas   = Color(red: 0.122, green: 0.122, blue: 0.122)  // #1F1F1F
    static let teamsDarkSurface1 = Color(red: 0.176, green: 0.173, blue: 0.173)  // #2D2C2C
    static let teamsDarkSurface2 = Color(red: 0.239, green: 0.235, blue: 0.235)  // #3D3C3C
    static let teamsDarkDivider  = Color(red: 0.239, green: 0.235, blue: 0.235)  // #3D3C3C
    static let teamsDarkText1    = Color.white                                   // #FFFFFF
    static let teamsDarkText2    = Color(red: 0.678, green: 0.678, blue: 0.678)  // #ADADAD

    // MARK: - Brand
    static let teamsPurpleLight = Color(red: 0.384, green: 0.392, blue: 0.655)   // #6264A7
    static let teamsPurpleDark  = Color(red: 0.357, green: 0.373, blue: 0.780)   // #5B5FC7
    static let teamsPurplePress = Color(red: 0.310, green: 0.322, blue: 0.698)   // #4F52B2

    // MARK: - Presence (loaded semantic color)
    static let teamsAvailable = Color(red: 0.420, green: 0.718, blue: 0.0)       // #6BB700
    static let teamsBusy      = Color(red: 0.769, green: 0.192, blue: 0.294)     // #C4314B
    static let teamsAway      = Color(red: 1.0,   green: 0.667, blue: 0.267)     // #FFAA44
    static let teamsOffline   = Color(red: 0.541, green: 0.533, blue: 0.525)     // #8A8886
}
```

## 2. Typography

Teams uses Segoe UI; Inter is the closest free substitute on iOS. Bundle the TTFs via `Info.plist` (`UIAppFonts`).

```swift
extension Font {
    static let teamsTitleLarge = Font.custom("SegoeUI-Bold",     size: 28)
    static let teamsSection    = Font.custom("SegoeUI-Bold",     size: 20)
    static let teamsTeamName   = Font.custom("SegoeUI-Bold",     size: 16)
    static let teamsListTitle  = Font.custom("SegoeUI-Semibold", size: 16)
    static let teamsAuthor     = Font.custom("SegoeUI-Semibold", size: 15)
    static let teamsBody       = Font.custom("SegoeUI-Regular",  size: 15)
    static let teamsButton     = Font.custom("SegoeUI-Semibold", size: 16)
    static let teamsMetadata   = Font.custom("SegoeUI-Regular",  size: 13)
    static let teamsReaction   = Font.custom("SegoeUI-Semibold", size: 12)
    static let teamsTab        = Font.custom("SegoeUI-Semibold", size: 10)
    static let teamsTinyUpper  = Font.custom("SegoeUI-Bold",     size: 11)
}

extension Font {
    static func teamsSys(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Presence Dot (the loaded semantic color)

```swift
enum Presence { case available, busy, dnd, away, offline }

struct PresenceDot: View {
    let presence: Presence
    var size: CGFloat = 10

    var body: some View {
        ZStack {
            switch presence {
            case .available: Circle().fill(Color.teamsAvailable)
            case .busy:      Circle().fill(Color.teamsBusy)
            case .dnd:
                Circle().fill(Color.teamsBusy)
                Capsule().fill(.white).frame(width: size * 0.5, height: size * 0.18)
            case .away:      Circle().fill(Color.teamsAway)
            case .offline:   Circle().strokeBorder(Color.teamsOffline, lineWidth: 1.5)
            }
        }
        .frame(width: size, height: size)
        .overlay(Circle().strokeBorder(Color(.systemBackground), lineWidth: 2))
        .animation(.easeInOut(duration: 0.25), value: presence)
    }
}

struct AvatarWithPresence: View {
    let initials: String
    let presence: Presence
    var size: CGFloat = 32
    var body: some View {
        Circle()
            .fill(Color.teamsPurpleLight.opacity(0.25))
            .frame(width: size, height: size)
            .overlay(Text(initials).font(.teamsSys(size * 0.4, weight: .semibold)))
            .overlay(alignment: .bottomTrailing) {
                PresenceDot(presence: presence, size: size * 0.32)
            }
    }
}
```

### Teams → Channels Tree (the signature element)

```swift
struct Team: Identifiable { let id = UUID(); let name: String; let channels: [Channel] }
struct Channel: Identifiable { let id = UUID(); let name: String; var unread: Bool }

struct TeamTreeRow: View {
    let team: Team
    @State private var expanded = true
    @Binding var activeChannel: UUID?
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        VStack(spacing: 0) {
            Button { withAnimation(.easeInOut(duration: 0.2)) { expanded.toggle() } } label: {
                HStack(spacing: 12) {
                    RoundedRectangle(cornerRadius: 8)
                        .fill(Color.teamsPurpleLight.opacity(0.3))
                        .frame(width: 32, height: 32)
                        .overlay(Text(String(team.name.prefix(1))).font(.teamsSys(14, weight: .bold)))
                    Text(team.name).font(.teamsTeamName)
                        .foregroundStyle(Color.teams(.teamsLightText1, .teamsDarkText1, scheme))
                    Spacer()
                    Image(systemName: "chevron.right")
                        .font(.system(size: 12, weight: .semibold))
                        .foregroundStyle(Color.teams(.teamsLightText2, .teamsDarkText2, scheme))
                        .rotationEffect(.degrees(expanded ? 90 : 0))
                }
                .padding(.horizontal, 16)
                .frame(height: 56)
            }

            if expanded {
                ForEach(team.channels) { ch in
                    ChannelRow(channel: ch, isActive: activeChannel == ch.id)
                        .onTapGesture { activeChannel = ch.id }
                }
            }
        }
    }
}

struct ChannelRow: View {
    let channel: Channel
    let isActive: Bool
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(spacing: 8) {
            Text("#")
                .font(.teamsSys(16, weight: .semibold))
                .foregroundStyle(Color.teams(.teamsLightText2, .teamsDarkText2, scheme))
            Text(channel.name)
                .font(.teamsListTitle)
                .fontWeight(channel.unread ? .bold : .semibold)
                .foregroundStyle(Color.teams(.teamsLightText1, .teamsDarkText1, scheme))
            Spacer()
            if channel.unread {
                Circle().fill(Color.teamsPurpleLight).frame(width: 8, height: 8)
            }
        }
        .padding(.leading, 44)
        .padding(.trailing, 16)
        .frame(height: 44)
        .background(isActive ? Color.teamsPurpleLight.opacity(0.12) : .clear)
        .overlay(alignment: .leading) {
            if isActive { Rectangle().fill(Color.teamsPurpleLight).frame(width: 3) }
        }
    }
}
```

### Message Card

```swift
struct MessageCard: View {
    let author: String
    let initials: String
    let presence: Presence
    let timestamp: String
    let body: String
    let replyCount: Int
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            AvatarWithPresence(initials: initials, presence: presence)
            VStack(alignment: .leading, spacing: 4) {
                HStack(spacing: 8) {
                    Text(author).font(.teamsAuthor)
                        .foregroundStyle(Color.teams(.teamsLightText1, .teamsDarkText1, scheme))
                    Text(timestamp).font(.teamsMetadata)
                        .foregroundStyle(Color.teams(.teamsLightText2, .teamsDarkText2, scheme))
                }
                Text(body).font(.teamsBody)
                    .foregroundStyle(Color.teams(.teamsLightText1, .teamsDarkText1, scheme))

                HStack(spacing: 6) {
                    ReactionChip(emoji: "👍", count: 3, mine: true)
                    ReactionChip(emoji: "❤️", count: 1, mine: false)
                }
                if replyCount > 0 {
                    Text("💬 \(replyCount) replies · Last reply 2h ago")
                        .font(.teamsMetadata)
                        .foregroundStyle(Color.teams(.teamsLightText2, .teamsDarkText2, scheme))
                }
            }
            Spacer(minLength: 0)
        }
        .padding(12)
        .background(RoundedRectangle(cornerRadius: 8)
            .fill(Color.teams(.teamsLightSurface1, .teamsDarkSurface1, scheme)))
    }
}

struct ReactionChip: View {
    let emoji: String; let count: Int; let mine: Bool
    @Environment(\.colorScheme) private var scheme
    var body: some View {
        HStack(spacing: 4) {
            Text(emoji).font(.system(size: 12))
            Text("\(count)").font(.teamsReaction)
                .foregroundStyle(Color.teams(.teamsLightText2, .teamsDarkText2, scheme))
        }
        .padding(.horizontal, 8).padding(.vertical, 4)
        .background(Capsule().fill(mine
            ? Color.teamsPurpleLight.opacity(0.12)
            : Color.teams(.teamsLightSurface2, .teamsDarkSurface2, scheme)))
    }
}
```

### Meeting Join Bar

```swift
struct MeetingJoinBar: View {
    let title: String
    let onJoin: () -> Void
    @Environment(\.colorScheme) private var scheme
    @State private var pulse = false

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "video.fill").foregroundStyle(.white).font(.system(size: 18))
            Text(title).font(.teamsSys(15, weight: .semibold)).foregroundStyle(.white)
            Spacer()
            Button(action: onJoin) {
                Text("Join")
                    .font(.teamsSys(14, weight: .semibold))
                    .foregroundStyle(scheme == .dark ? Color.teamsPurpleDark : Color.teamsPurpleLight)
                    .padding(.horizontal, 18).frame(height: 30)
                    .background(Capsule().fill(.white))
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
        .background(RoundedRectangle(cornerRadius: 12)
            .fill(scheme == .dark ? Color.teamsPurpleDark : Color.teamsPurpleLight))
        .shadow(color: Color.teamsPurpleLight.opacity(0.35), radius: 16, y: 4)
        .opacity(pulse ? 0.85 : 1.0)
        .animation(.easeInOut(duration: 2).repeatForever(autoreverses: true), value: pulse)
        .padding(.horizontal, 12)
        .onAppear { pulse = true }
    }
}
```

### Primary Button

```swift
struct TeamsPrimaryButton: View {
    let title: String
    let action: () -> Void
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.teamsButton)
                .foregroundStyle(.white)
                .padding(.vertical, 12).padding(.horizontal, 24)
                .frame(height: 44)
                .background(RoundedRectangle(cornerRadius: 8)
                    .fill(scheme == .dark ? Color.teamsPurpleDark : Color.teamsPurpleLight))
        }
        .buttonStyle(TeamsPressable())
        .sensoryFeedback(.impact(weight: .light), trigger: title)
    }
}

struct TeamsPressable: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .overlay(configuration.isPressed
                ? RoundedRectangle(cornerRadius: 8).fill(Color.teamsPurplePress).blendMode(.multiply) : nil)
            .animation(.spring(response: 0.25, dampingFraction: 0.85), value: configuration.isPressed)
    }
}
```

## 4. Navigation — Bottom Tab Bar

```swift
struct RootTabView: View {
    @Environment(\.colorScheme) private var scheme
    var body: some View {
        TabView {
            ActivityView().tabItem { Label("Activity", systemImage: "bell.fill") }
                .badge(3)
            ChatView().tabItem     { Label("Chat",     systemImage: "bubble.left.and.bubble.right.fill") }
            TeamsView().tabItem    { Label("Teams",    systemImage: "person.3.fill") }
            CalendarView().tabItem { Label("Calendar", systemImage: "calendar") }
            CallsView().tabItem    { Label("Calls",    systemImage: "phone.fill") }
        }
        .tint(scheme == .dark ? .teamsPurpleDark : .teamsPurpleLight)
    }
}
```

## 5. Motion

```swift
// Primary button tap
.sensoryFeedback(.impact(weight: .light), trigger: didTap)

// Tree expand/collapse: chevron rotation + child height
.rotationEffect(.degrees(expanded ? 90 : 0))
.animation(.easeInOut(duration: 0.2), value: expanded)

// Reaction picker (long-press): spring scale + opacity
.scaleEffect(showPicker ? 1 : 0.9).opacity(showPicker ? 1 : 0)
.animation(.spring(response: 0.3, dampingFraction: 0.8), value: showPicker)

// Presence change: color cross-fade 250ms
.animation(.easeInOut(duration: 0.25), value: presence)

// Join bar: subtle 2s opacity pulse (1.0 ↔ 0.85), slide+fade on appear
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Activity (tab) | `bell.fill` | 24pt |
| Chat (tab) | `bubble.left.and.bubble.right.fill` | 24pt |
| Teams (tab) | `person.3.fill` | 24pt |
| Calendar (tab) | `calendar` | 24pt |
| Calls (tab) | `phone.fill` | 24pt |
| Channel marker | `number` (or text `#`) | 16pt |
| Expand chevron | `chevron.right` | 12pt |
| Meeting / join | `video.fill` | 18pt |
| Send | `paperplane.fill` | 16pt |
| Attach | `paperclip` | 22pt |
| Emoji | `face.smiling` | 22pt |
| Search | `magnifyingglass` | 16pt |
| New chat | `square.and.pencil` | 22pt |
| Reply / thread | `arrowshape.turn.up.left` | 14pt |
| More | `ellipsis` | 22pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Teams is dual-identity: resolve every token against `@Environment(\.colorScheme)` and verify contrast in both
- Support Dynamic Type on names, message body, channel titles; pin presence dots (10pt status) and tabular timestamps/durations and 10pt tab labels
- VoiceOver: presence must be announced as text, not color alone ("Priya Anand, Available"); the unread dot needs an "unread" trait; combine message-card text with `.accessibilityElement(children: .combine)`
- The Teams tree must expose expand/collapse state to assistive tech (`.accessibilityAddTraits(.isButton)`, announce "expanded"/"collapsed")
- Contrast: validate `#616161` on `#F5F5F5` (light) and `#ADADAD` on `#1F1F1F` (dark) at 13pt; both pass WCAG AA at 13pt+
- Respect `accessibilityReduceMotion`: drop the join-bar pulse and reaction-picker scale; keep static states
