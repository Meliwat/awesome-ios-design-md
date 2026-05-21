# Plex (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Plex's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Plex's core media experience is **dark-first** on a cool charcoal canvas.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-first, cool charcoal — NOT true black)
    static let plexCanvas   = Color(red: 0.122, green: 0.137, blue: 0.149) // #1F2326
    static let plexSurface1 = Color(red: 0.157, green: 0.173, blue: 0.188) // #282C30
    static let plexSurface2 = Color(red: 0.196, green: 0.216, blue: 0.231) // #32373B
    static let plexSurface3 = Color(red: 0.235, green: 0.255, blue: 0.275) // #3C4146
    static let plexDivider  = Color(red: 0.204, green: 0.224, blue: 0.239) // #34393D

    // MARK: - Text
    static let plexTextPrimary   = Color(red: 0.949, green: 0.953, blue: 0.957) // #F2F3F4
    static let plexTextSecondary = Color(red: 0.608, green: 0.627, blue: 0.643) // #9BA0A4
    static let plexTextTertiary  = Color(red: 0.420, green: 0.439, blue: 0.459) // #6B7075

    // MARK: - Brand & Status
    static let plexYellow        = Color(red: 0.898, green: 0.627, blue: 0.051) // #E5A00D — the only accent
    static let plexYellowPressed = Color(red: 0.788, green: 0.541, blue: 0.035) // #C98A09
    static let plexYellowInk     = Color(red: 0.102, green: 0.075, blue: 0.016) // #1A1304 — text on yellow (mandatory)
    static let plexOnline        = Color(red: 0.298, green: 0.686, blue: 0.314) // #4CAF50
    static let plexOffline       = Color(red: 0.420, green: 0.439, blue: 0.459) // #6B7075
    static let plexError         = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}
```

## 2. Typography

Plex's product face is **Inter** — neutral, legible, functional. Bundle Inter TTFs via `Info.plist` / `UIAppFonts`. Falls back to SF Pro 1:1 when unbundled.

```swift
extension Font {
    static func plexInter(_ size: CGFloat, _ weight: Weight = .regular) -> Font {
        let face: String
        switch weight {
        case .heavy, .black:    face = "Inter-ExtraBold"
        case .bold:             face = "Inter-Bold"
        case .semibold:         face = "Inter-SemiBold"
        case .medium:           face = "Inter-Medium"
        default:                face = "Inter-Regular"
        }
        return .custom(face, size: size)
    }

    static let plexScreenTitle   = Font.custom("Inter-ExtraBold", size: 32)
    static let plexHeroTitle     = Font.custom("Inter-ExtraBold", size: 26)
    static let plexSectionHeader = Font.custom("Inter-Bold",      size: 22)
    static let plexSubsection    = Font.custom("Inter-Bold",      size: 18)
    static let plexBody          = Font.custom("Inter-Regular",   size: 16)
    static let plexCardTitle     = Font.custom("Inter-SemiBold",  size: 15)
    static let plexMeta          = Font.custom("Inter-Regular",   size: 14)
    static let plexButtonLabel   = Font.custom("Inter-Bold",      size: 16)
    static let plexServerName    = Font.custom("Inter-SemiBold",  size: 14)
    static let plexServerMeta    = Font.custom("Inter-Regular",   size: 12)
    static let plexTab           = Font.custom("Inter-SemiBold",  size: 10)
    static let plexTechChip      = Font.custom("Inter-SemiBold",  size: 12)
}
```

## 3. Signature Components

### Server Picker Pill + Sheet

```swift
struct ServerPill: View {
    let name: String
    let isOnline: Bool
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 8) {
                Circle()
                    .fill(isOnline ? Color.plexOnline : Color.plexOffline)
                    .frame(width: 8, height: 8)
                Text(name)
                    .font(.plexInter(13, .semibold))
                    .foregroundStyle(Color.plexTextPrimary)
                Image(systemName: "chevron.down")
                    .font(.system(size: 11, weight: .semibold))
                    .foregroundStyle(Color.plexTextSecondary)
            }
            .padding(.vertical, 7).padding(.horizontal, 14)
            .background(Color.plexSurface1)
            .overlay(Capsule().strokeBorder(Color.plexDivider, lineWidth: 1))
            .clipShape(Capsule())
        }
        .buttonStyle(.plain)
    }
}

struct ServerSheetRow: View {
    let name: String
    let meta: String           // "Online · 2,481 items · LAN"
    let isOnline: Bool
    let isSelected: Bool

    var body: some View {
        HStack(spacing: 12) {
            Circle()
                .fill(isOnline ? Color.plexOnline : Color.plexOffline)
                .frame(width: 9, height: 9)
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.plexServerName).foregroundStyle(Color.plexTextPrimary)
                Text(meta).font(.plexServerMeta).foregroundStyle(Color.plexTextSecondary)
            }
            Spacer()
            if isSelected {
                Image(systemName: "checkmark")
                    .font(.system(size: 16, weight: .bold))
                    .foregroundStyle(Color.plexYellow)
            }
        }
        .padding(.vertical, 13).padding(.horizontal, 14)
        .background(isSelected ? Color.plexSurface3 : Color.plexSurface2)
        .clipShape(RoundedRectangle(cornerRadius: 8))
    }
}
```

### On Deck Tile (resume-aware)

```swift
struct OnDeckTile: View {
    let artUrl: String
    let title: String
    let sub: String            // "S2 · E5 · 22 min left"
    let progress: Double       // 0.0 ... 1.0

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ZStack {
                AsyncImage(url: URL(string: artUrl)) { img in
                    img.resizable().aspectRatio(contentMode: .fill)
                } placeholder: { Rectangle().fill(Color.plexSurface2) }
                .frame(width: 220, height: 124)
                .clipShape(RoundedRectangle(cornerRadius: 6))

                Circle()
                    .fill(Color.black.opacity(0.55))
                    .frame(width: 42, height: 42)
                    .overlay(
                        Image(systemName: "play.fill")
                            .font(.system(size: 16))
                            .foregroundStyle(.white)
                    )

                VStack {
                    Spacer()
                    GeometryReader { g in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.22))
                            Rectangle().fill(Color.plexYellow)
                                .frame(width: g.size.width * progress)
                        }
                    }
                    .frame(height: 3)
                }
                .frame(width: 220, height: 124)
            }

            Text(title)
                .font(.plexCardTitle)
                .foregroundStyle(Color.plexTextPrimary)
                .lineLimit(1)
            Text(sub)
                .font(.plexMeta)
                .foregroundStyle(Color.plexTextSecondary)
                .lineLimit(1)
        }
        .frame(width: 220)
    }
}
```

### Primary Play Button (dark-on-yellow)

```swift
struct PlayButton: View {
    let action: () -> Void
    @GestureState private var pressed = false

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 16))
                Text("Play").font(.plexButtonLabel)
            }
            .foregroundStyle(Color.plexYellowInk)          // dark ink — NEVER white
            .frame(maxWidth: .infinity)
            .padding(.vertical, 14)
            .background(pressed ? Color.plexYellowPressed : Color.plexYellow)
            .clipShape(RoundedRectangle(cornerRadius: 8))
            .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0).updating($pressed) { _, s, _ in s = true }
        )
    }
}
```

### Library Poster + Unwatched Dot

```swift
struct LibraryPoster: View {
    let artUrl: String
    let unwatched: Bool

    var body: some View {
        AsyncImage(url: URL(string: artUrl)) { img in
            img.resizable().aspectRatio(contentMode: .fill)
        } placeholder: { Rectangle().fill(Color.plexSurface2) }
        .aspectRatio(2.0/3.0, contentMode: .fill)
        .clipShape(RoundedRectangle(cornerRadius: 6))
        .overlay(alignment: .topTrailing) {
            if unwatched {
                Circle()
                    .fill(Color.plexYellow)
                    .frame(width: 9, height: 9)
                    .shadow(color: .black.opacity(0.5), radius: 1.5, y: 1)
                    .padding(7)
            }
        }
    }
}
```

### Tech / Quality Chip

```swift
struct TechChip: View {
    let text: String           // "1080p · 5.1"
    var body: some View {
        Text(text)
            .font(.plexTechChip)
            .tracking(0.2)
            .foregroundStyle(Color.plexTextSecondary)
            .padding(.vertical, 4).padding(.horizontal, 8)
            .background(Color.plexSurface2)
            .clipShape(RoundedRectangle(cornerRadius: 4))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PlexTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem     { Label("Home",     systemImage: "house.fill") }
            LibraryView().tabItem  { Label("Library",  systemImage: "square.grid.2x2.fill") }
            DiscoverView().tabItem { Label("Discover", systemImage: "play.circle") }
            LiveTVView().tabItem   { Label("Live TV",  systemImage: "tv") }
            YouView().tabItem      { Label("You",      systemImage: "person.crop.circle") }
        }
        .tint(.plexYellow)                       // active = Plex Yellow, no tint pill
        .toolbarBackground(Color.plexCanvas.opacity(0.96), for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
        .preferredColorScheme(.dark)
    }
}
```

## 5. Motion

```swift
// Server switch — check fades in, sheet dismisses, content cross-fades
withAnimation(.easeOut(duration: 0.15)) { selectedServerID = id }
// then dismiss sheet (0.25s) + cross-fade Home content (0.25s)

// On Deck resume — tile press then progress fill on return
.scaleEffect(pressed ? 0.97 : 1).animation(.easeOut(duration: 0.15), value: pressed)
.onAppear { withAnimation(.easeOut(duration: 0.35)) { fill = watched } }

// Mark as Watched — unwatched dot fades out
withAnimation(.easeOut(duration: 0.2)) { unwatched = false }

// Status dot online — calm gray → green (no bounce)
withAnimation(.easeInOut(duration: 0.2)) { isOnline = true }

// Sheet present
.presentationDetents([.medium, .large])      // server sheet

// Haptics — light only; Plex motion is deliberately quiet
let soft = UIImpactFeedbackGenerator(style: .light)  // server select, tile resume, mark watched
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Library (tab) | `square.grid.2x2` / `.fill` | 22pt |
| Discover (tab) | `play.circle` | 22pt |
| Live TV (tab) | `tv` | 22pt |
| You (tab) | `person.crop.circle` | 22pt |
| Server chevron | `chevron.down` | 11pt |
| Selected server | `checkmark` | 16pt |
| Play (CTA / overlay) | `play.fill` | 16pt |
| Mark as Watched | `checkmark.circle` | 18pt |
| Add to Playlist | `text.badge.plus` | 18pt |
| Search | `magnifyingglass` | 20pt |
| Settings | `gearshape` | 20pt |
| Cast | `airplayvideo` | 20pt |
| Back | `chevron.left` | 20pt |
| Online/Offline dot | (filled `Circle`, not a symbol) | 8–9pt |
| Download | `arrow.down.circle` | 20pt |

## 7. Dark Mode

Plex's library/browse experience is **dark-first** on the cool charcoal canvas — not true black, and not a light mode. Lock the media surfaces to the charcoal palette.

```swift
struct PlexTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.plexCanvas)
            .foregroundStyle(Color.plexTextPrimary)
            .preferredColorScheme(.dark)         // media experience stays dark
            .tint(.plexYellow)
    }
}

extension View {
    func plexTheme() -> some View { modifier(PlexTheme()) }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`AsyncImage`, `.presentationDetents`, `.toolbarBackground` available; `.sensoryFeedback` needs iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Bundle Inter (Regular / Medium / SemiBold / Bold / ExtraBold) TTFs via `Info.plist` `UIAppFonts` — Inter is SIL OFL, free to redistribute; falls back to SF Pro 1:1
- Lock the media experience to `preferredColorScheme(.dark)` on the cool charcoal canvas — pure-white light mode is not the Plex identity
- **Dark-on-yellow is an accessibility requirement**: every yellow fill must use `#1A1304` ink — white-on-yellow (`#E5A00D`) fails WCAG AA. This is both brand and contrast.
- Dynamic Type: scale screen/hero titles, section headers, body, summaries, meta; keep tab labels, tech chips, progress-bar geometry, status dots, and the unwatched corner dot at FIXED sizes
- VoiceOver: label the server pill "Server: {name}, {online/offline}"; server rows "{name}, {meta}, {selected}"; On Deck tiles "{title}, {sub}, {percent}% watched" with progress exposed via `.accessibilityValue`
- Status dots carry meaning by color alone — pair them with the text labels ("Online" / "Offline") in the meta line so the state is not color-only
- Contrast: `#F2F3F4` on `#1F2326` passes AA; `#9BA0A4` meta passes AA at 14pt; the yellow check `#E5A00D` on `#3C4146` is an icon, supported by the row's selected background step
- Reduce Motion: replace the Home cross-fade on server switch with an instant swap; keep the progress bar static at the watched value (no fill animation)
- Respect the safe area / Dynamic Island: Plex keeps a clean top inset (server pill / nav) — do not extend art under the status bar
