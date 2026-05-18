# Clubhouse (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Clubhouse's visual language into paste-ready SwiftUI code: `Color` extensions, the serif/sans `Font` pairing, `ViewModifier`s, and example views for the signature speaking-pulse avatar, room stage, and raise-hand bar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light — the Hallway)
    static let chCream        = Color(red: 0.949, green: 0.937, blue: 0.894) // #F2EFE4
    static let chCreamCard    = Color(red: 0.984, green: 0.976, blue: 0.945) // #FBF9F1
    static let chCreamPressed = Color(red: 0.918, green: 0.902, blue: 0.847) // #EAE6D8
    static let chDividerLight = Color(red: 0.867, green: 0.847, blue: 0.784) // #DDD8C8

    // MARK: - Canvas & Surfaces (Dark — the Room; warm near-black, NOT slate)
    static let chRoom         = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let chDarkSurface1 = Color(red: 0.141, green: 0.133, blue: 0.125) // #242220
    static let chDarkSurface2 = Color(red: 0.180, green: 0.169, blue: 0.153) // #2E2B27
    static let chDividerDark  = Color(red: 0.227, green: 0.216, blue: 0.184) // #3A372F

    // MARK: - Text
    static let chInk          = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let chInkSecondary = Color(red: 0.420, green: 0.392, blue: 0.325) // #6B6453
    static let chInkTertiary  = Color(red: 0.604, green: 0.580, blue: 0.514) // #9A9483
    static let chCreamText    = Color(red: 0.949, green: 0.937, blue: 0.894) // #F2EFE4
    static let chCreamText2   = Color(red: 0.722, green: 0.698, blue: 0.627) // #B8B2A0
    static let chCreamText3   = Color(red: 0.514, green: 0.490, blue: 0.427) // #837D6D

    // MARK: - Brand (the single accent)
    static let chEmerald      = Color(red: 0.220, green: 0.710, blue: 0.412) // #38B569
    static let chEmeraldDeep  = Color(red: 0.180, green: 0.604, blue: 0.345) // #2E9A58
    static let chEmeraldSoft  = Color(red: 0.220, green: 0.710, blue: 0.412).opacity(0.18) // pulse halo
    static let chEmeraldInk   = Color(red: 0.027, green: 0.129, blue: 0.059) // #07210F (text on emerald)

    // MARK: - Semantic
    static let chGold         = Color(red: 0.914, green: 0.769, blue: 0.416) // #E9C46A moderator
    static let chLeave        = Color(red: 0.898, green: 0.341, blue: 0.361) // #E5575C

    // MARK: - Avatar Gradient Palette (content color)
    static let chPeachA  = Color(red: 0.957, green: 0.780, blue: 0.482); static let chPeachB  = Color(red: 0.878, green: 0.635, blue: 0.294)
    static let chSageA   = Color(red: 0.624, green: 0.847, blue: 0.706); static let chSageB   = Color(red: 0.373, green: 0.706, blue: 0.518)
    static let chLavA    = Color(red: 0.788, green: 0.714, blue: 0.910); static let chLavB    = Color(red: 0.612, green: 0.498, blue: 0.816)
    static let chCoralA  = Color(red: 0.949, green: 0.663, blue: 0.627); static let chCoralB  = Color(red: 0.851, green: 0.467, blue: 0.420)
    static let chSkyA    = Color(red: 0.663, green: 0.780, blue: 0.910); static let chSkyB    = Color(red: 0.431, green: 0.612, blue: 0.816)
}

enum ChAvatarTint: CaseIterable {
    case peach, sage, lavender, coral, sky
    var gradient: LinearGradient {
        let pair: (Color, Color) = switch self {
        case .peach:    (.chPeachA, .chPeachB)
        case .sage:     (.chSageA, .chSageB)
        case .lavender: (.chLavA, .chLavB)
        case .coral:    (.chCoralA, .chCoralB)
        case .sky:      (.chSkyA, .chSkyB)
        }
        return LinearGradient(colors: [pair.0, pair.1], startPoint: .topLeading, endPoint: .bottomTrailing)
    }
    static func forID(_ id: Int) -> ChAvatarTint { allCases[abs(id) % allCases.count] }
}
```

## 2. Typography

Clubhouse pairs a humanist serif display for titles with a sans for UI. Bundle **DM Serif Display** (titles) + **Inter** (UI) — both SIL OFL.

```swift
extension Font {
    // Serif display — titles, topics, greetings, pull-quotes
    static let chRoomTitle   = Font.custom("DMSerifDisplay-Regular", size: 28)
    static let chScreenTitle = Font.custom("DMSerifDisplay-Regular", size: 24)
    static let chQuote       = Font.custom("DMSerifDisplay-Italic",  size: 20)

    // Sans (Inter) — every piece of UI text
    static let chSection   = Font.custom("Inter-Bold",     size: 18)
    static let chSubsection = Font.custom("Inter-Bold",    size: 16)
    static let chBody      = Font.custom("Inter-Regular",  size: 16)
    static let chName      = Font.custom("Inter-SemiBold", size: 15)
    static let chButton    = Font.custom("Inter-Bold",     size: 16)
    static let chMeta      = Font.custom("Inter-Regular",  size: 14)
    static let chRoleTag   = Font.custom("Inter-SemiBold", size: 12)
    static let chOverline  = Font.custom("Inter-Bold",     size: 11)   // UPPERCASE + .tracking(0.8)
    static let chTab       = Font.custom("Inter-SemiBold", size: 10)
}
```

## 3. Signature Components

### Speaking-Pulse Avatar (the core atom)

```swift
struct ChSpeakerAvatar: View {
    let initials: String
    let tint: ChAvatarTint
    let name: String
    var role: String? = nil
    var isSpeaking: Bool = false
    var isMuted: Bool = false
    var isHost: Bool = false
    var size: CGFloat = 60

    @State private var pulse = false

    var body: some View {
        VStack(spacing: 7) {
            ZStack {
                // Breathing halo (a glow, not a drop-shadow)
                if isSpeaking {
                    Circle()
                        .fill(Color.chEmeraldSoft)
                        .frame(width: size + 16, height: size + 16)
                        .scaleEffect(pulse ? 1.12 : 1.0)
                        .animation(.easeInOut(duration: 0.9).repeatForever(autoreverses: true), value: pulse)
                }

                Circle()
                    .fill(tint.gradient)
                    .frame(width: size, height: size)
                    .overlay(
                        Text(initials)
                            .font(.custom("Inter-Bold", size: size * 0.33))
                            .foregroundStyle(Color(red: 0.165, green: 0.149, blue: 0.125))
                    )
                    .overlay(
                        Circle().strokeBorder(Color.chEmerald, lineWidth: isSpeaking ? 3 : 0)
                    )
                    .opacity(isMuted ? 0.55 : 1.0)

                if isMuted {
                    Image(systemName: "mic.slash.fill")
                        .font(.system(size: 11, weight: .bold))
                        .foregroundStyle(Color.chCreamText2)
                        .frame(width: 22, height: 22)
                        .background(Circle().fill(Color.chDarkSurface2))
                        .overlay(Circle().strokeBorder(Color.chRoom, lineWidth: 2))
                        .offset(x: size * 0.34, y: size * 0.34)
                }
                if isHost {
                    Text("🎙️")
                        .font(.system(size: 11))
                        .frame(width: 20, height: 20)
                        .background(Circle().fill(Color.chDarkSurface2))
                        .overlay(Circle().strokeBorder(Color.chRoom, lineWidth: 2))
                        .offset(x: size * 0.34, y: -size * 0.34)
                }
            }
            .frame(width: size + 16, height: size + 16)

            VStack(spacing: 1) {
                Text(name)
                    .font(.chName)
                    .foregroundStyle(role == "Moderator" ? Color.chGold : Color.chCreamText)
                    .lineLimit(1)
                if let role {
                    Text(role)
                        .font(.custom("Inter-Medium", size: 11))
                        .foregroundStyle(Color.chCreamText3)
                }
            }
        }
        .onAppear { pulse = isSpeaking }
        .onChange(of: isSpeaking) { _, v in pulse = v }
    }
}
```

### Room Stage (grouped speaker grid)

```swift
struct ChRoomStage: View {
    struct Group: Identifiable { let id = UUID(); let label: String; let speakers: [SpeakerVM] }
    struct SpeakerVM: Identifiable { let id: Int; let initials, name: String; let role: String?; let speaking, muted, host: Bool }

    let groups: [Group]
    private let cols = Array(repeating: GridItem(.flexible(), spacing: 6), count: 4)

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 4) {
                ForEach(groups) { group in
                    Text(group.label.uppercased())
                        .font(.chOverline)
                        .tracking(0.8)
                        .foregroundStyle(Color.chCreamText3)
                        .padding(.horizontal, 6)
                        .padding(.top, 14)
                        .padding(.bottom, 10)

                    LazyVGrid(columns: cols, spacing: 14) {
                        ForEach(group.speakers) { s in
                            ChSpeakerAvatar(initials: s.initials, tint: .forID(s.id),
                                            name: s.name, role: s.role,
                                            isSpeaking: s.speaking, isMuted: s.muted, isHost: s.host)
                        }
                    }
                }
            }
            .padding(.horizontal, 16)
        }
        .background(Color.chRoom)
    }
}
```

### Raise-Hand Bar (footer)

```swift
struct ChRaiseHandBar: View {
    @State private var raised = false
    @State private var wave: Double = 0

    var body: some View {
        HStack {
            Button {
                if !raised {
                    withAnimation(.easeInOut(duration: 0.15).repeatCount(4, autoreverses: true)) { wave = 15 }
                }
                withAnimation(.easeOut(duration: 0.2)) { raised.toggle() }
            } label: {
                HStack(spacing: 8) {
                    Text("✋").font(.system(size: 18)).rotationEffect(.degrees(raised ? 0 : wave))
                    Text(raised ? "Hand raised" : "Raise hand")
                        .font(.chButton)
                        .foregroundStyle(raised ? Color.chEmerald : Color.chCreamText)
                }
                .padding(.vertical, 12).padding(.horizontal, 20)
                .background(Capsule().fill(raised ? Color.chEmeraldSoft : Color.chDarkSurface2))
            }
            .buttonStyle(.plain)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: raised)

            Spacer()

            HStack(spacing: 22) {
                Circle().fill(Color.chEmerald).frame(width: 14, height: 14)
                    .overlay(Circle().fill(Color.chEmeraldSoft).frame(width: 24, height: 24))
                Image(systemName: "mic.fill")
                    .font(.system(size: 22))
                    .foregroundStyle(Color.chCreamText2)
            }
        }
        .padding(.horizontal, 20).padding(.vertical, 16)
        .background(Color.chRoom)
        .overlay(alignment: .top) { Rectangle().fill(Color.chDividerDark).frame(height: 0.5) }
    }
}
```

### Room Header (serif title)

```swift
struct ChRoomHeader: View {
    let club: String
    let status: String      // "Open" / "Social" / "Closed"
    let title: String

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            HStack(spacing: 6) {
                Circle().fill(Color.chEmerald).frame(width: 6, height: 6)
                Text("\(club) · \(status)")
                    .font(.custom("Inter-SemiBold", size: 12))
                    .foregroundStyle(Color.chCreamText2)
            }
            Text(title)
                .font(.chRoomTitle)
                .foregroundStyle(Color.chCreamText)
                .lineSpacing(2)
        }
        .padding(.horizontal, 22)
        .padding(.bottom, 14)
    }
}
```

### Hallway Room Card

```swift
struct ChHallwayCard: View {
    let club: String
    let isLive: Bool
    let title: String
    let avatars: [(String, ChAvatarTint)]
    let count: Int

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack {
                Text(club).font(.custom("Inter-SemiBold", size: 14)).foregroundStyle(Color.chInkSecondary)
                Spacer()
                Text(isLive ? "● Open" : "Upcoming")
                    .font(.chRoleTag)
                    .foregroundStyle(isLive ? Color.chEmerald : Color.chInkSecondary)
                    .padding(.vertical, 5).padding(.horizontal, 10)
                    .background(Capsule().fill(isLive ? Color.chEmeraldSoft : Color.chCreamPressed))
            }
            Text(title).font(.custom("DMSerifDisplay-Regular", size: 20)).foregroundStyle(Color.chInk)
            HStack(spacing: -10) {
                ForEach(Array(avatars.prefix(5).enumerated()), id: \.offset) { _, a in
                    Circle().fill(a.1.gradient)
                        .frame(width: 32, height: 32)
                        .overlay(Text(a.0).font(.custom("Inter-Bold", size: 11)).foregroundStyle(.white))
                        .overlay(Circle().strokeBorder(Color.chCreamCard, lineWidth: 2))
                }
                Text("\(count) in the room")
                    .font(.chMeta).foregroundStyle(Color.chInkSecondary)
                    .padding(.leading, 16)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.chCreamCard))
        .shadow(color: .black.opacity(0.05), radius: 10, y: 2)
    }
}
```

### Primary Pill Button

```swift
struct ChPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.chButton)
                .foregroundStyle(Color.chEmeraldInk)
                .padding(.vertical, 14).padding(.horizontal, 28)
                .background(Capsule().fill(pressed ? Color.chEmeraldDeep : Color.chEmerald))
        }
        .buttonStyle(.plain)
        .scaleEffect(pressed ? 0.98 : 1)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }.onEnded { _ in pressed = false })
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ChTabView: View {
    var body: some View {
        TabView {
            HallwayView().tabItem  { Label("Hallway",  systemImage: "house") }
            ExploreView().tabItem  { Label("Explore",  systemImage: "safari") }
            ActivityView().tabItem { Label("Activity", systemImage: "bell") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person.circle") }
        }
        .tint(.chInk)              // active = ink/cream, no colored pill
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.chCream)
            a.shadowColor = UIColor(Color.chDividerLight)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Speaking pulse — the signature breath (loop while audio detected)
.scaleEffect(pulse ? 1.12 : 1.0)
.animation(.easeInOut(duration: 0.9).repeatForever(autoreverses: true), value: pulse)

// New speaker joins the stage
.transition(.scale(scale: 0.8).combined(with: .opacity))   // .animation(.easeOut(duration: 0.2))

// Raise hand — emoji wave
withAnimation(.easeInOut(duration: 0.15).repeatCount(4, autoreverses: true)) { wave = 15 }

// Invited to speak — celebratory sheet + medium haptic
.sensoryFeedback(.impact(weight: .medium), trigger: invitedToSpeak)

// Room enter / collapse
// enter: full-screen sheet slide-up 300ms ease-out; title fade + rise 8pt
// collapse: slide-down to docked mini-bar 280ms ease-in (pulse continues)

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: handRaised)     // raise hand, ping, new speaker
.sensoryFeedback(.impact(weight: .medium), trigger: invitedToSpeak)
.sensoryFeedback(.selection, trigger: audiencePill)                     // Open/Social/Closed change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Collapse room | `chevron.down` | 18pt |
| Add people | `person.badge.plus` | 20pt |
| Share / more | `square.and.arrow.up` / `ellipsis` | 20pt |
| Mic on | `mic.fill` | 22pt |
| Mic off / muted badge | `mic.slash.fill` | 11pt (badge) / 22pt (self) |
| Hallway (tab) | `house` / `house.fill` | 24pt |
| Explore (tab) | `safari` / `safari.fill` | 24pt |
| Activity (tab) | `bell` / `bell.fill` | 24pt |
| Profile (tab) | `person.circle` / `.fill` | 24pt |
| Search | `magnifyingglass` | 16pt |
| Reminder | `bell.badge` | 16pt |
| Start a room (+) | `plus` | 18pt |
| Leave | `xmark` | 16pt |
| Host pin | 🎙️ (emoji, not a symbol) | 11pt |
| Speaking | (no icon — the emerald ring + halo) | — |

## 7. Dark Mode

Clubhouse's dark mode is the **Room**: a *warm* near-black (`#1A1A1A`), never a cold slate. The cream hallway inverts to a warm dark; the Speaking Emerald is identical in both modes.

```swift
struct ChTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.chRoom : Color.chCream)
            .foregroundStyle(scheme == .dark ? Color.chCreamText : Color.chInk)
    }
}
extension View {
    func chTheme() -> some View { modifier(ChTheme()) }
}

// Warm scrim behind sheets (NOT pure black)
extension Color {
    static let chScrim = Color(red: 0.078, green: 0.071, blue: 0.063).opacity(0.5) // rgba(20,18,16,0.5)
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UISelectionFeedbackGenerator`)
- Bundle DM Serif Display + Inter TTFs via `Info.plist` `UIAppFonts` — both SIL OFL, free to ship
- Dynamic Type: scale serif titles, section headers, body, speaker names; keep role tags, group overlines, tab labels, and the "+N" cluster chip FIXED (layout-critical); names truncate to 1 line, role line hides at the largest sizes
- VoiceOver: label a speaking avatar "{name}, speaking" / muted "{name}, muted" / host "{name}, host"; the raise-hand pill announces "Raise hand, button" → "Hand raised"; group overlines are headers; the speaking pulse must NOT be the only signal — keep the VoiceOver state string
- Color contrast: `#1A1A1A` on `#F2EFE4` and `#F2EFE4` on `#1A1A1A` both pass AA; `#07210F` on `#38B569` passes AA for the CTA; the emerald ring at 3pt is supplemented by the VoiceOver "speaking" string for non-color users
- Reduce Motion: replace the breathing speaking pulse with a static 3pt `#38B569` ring (no scale loop); sheet slide transitions become cross-fades; skip the hand-wave (just cross-fade the pill)
- Reduce Transparency: the warm scrim becomes a solid `#141210`; sheets are already opaque warm surfaces
- Warmth is non-negotiable: never let dark mode drift to a neutral/blue `#191919` — Clubhouse's near-black is warm-tinted (`#1A1A1A` with warm surfaces `#242220` / `#2E2B27`)
- The emerald is the most important pixel — never tint it per-theme, never use it for decoration; it means "this person has the floor"
