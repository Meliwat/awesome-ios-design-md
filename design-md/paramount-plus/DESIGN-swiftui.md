# Paramount+ (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Paramount+'s visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Paramount+ is dark-only; there is no light scheme.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only product)
    static let ppCanvas    = Color(red: 0.039, green: 0.055, blue: 0.176) // #0A0E2D
    static let ppCanvas2   = Color(red: 0.055, green: 0.078, blue: 0.220) // #0E1438
    static let ppSurface1  = Color(red: 0.086, green: 0.110, blue: 0.267) // #161C44
    static let ppSurface2  = Color(red: 0.122, green: 0.149, blue: 0.333) // #1F2655
    static let ppDivider   = Color(red: 0.165, green: 0.196, blue: 0.400) // #2A3266

    // MARK: - Text
    static let ppTextPrimary   = Color.white                                  // #FFFFFF
    static let ppTextSecondary = Color(red: 0.682, green: 0.706, blue: 0.839) // #AEB4D6
    static let ppTextTertiary  = Color(red: 0.435, green: 0.467, blue: 0.651) // #6F77A6

    // MARK: - Brand (single accent)
    static let ppBlue        = Color(red: 0.000, green: 0.392, blue: 1.000) // #0064FF
    static let ppBluePressed = Color(red: 0.000, green: 0.322, blue: 0.800) // #0052CC
    static let ppSky         = Color(red: 0.302, green: 0.616, blue: 1.000) // #4D9DFF

    // MARK: - Semantic
    static let ppLive    = Color(red: 1.000, green: 0.176, blue: 0.275) // #FF2D46
    static let ppSuccess = Color(red: 0.118, green: 0.843, blue: 0.376) // #1ED760
    static let ppError   = Color(red: 1.000, green: 0.271, blue: 0.227) // #FF453A
    static let ppGold    = Color(red: 0.961, green: 0.773, blue: 0.094) // #F5C518
}

extension LinearGradient {
    /// Hero bottom scrim.
    static let ppHeroScrim = LinearGradient(
        stops: [
            .init(color: .clear,                        location: 0.36),
            .init(color: Color.ppCanvas.opacity(0.72),  location: 0.70),
            .init(color: .ppCanvas,                      location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

Paramount Sans is proprietary; bundle **Inter** (SIL OFL) as the closest substitute via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let ppScreenTitle = Font.custom("Inter-Black",     size: 32)
    static let ppHeroTitle   = Font.custom("Inter-ExtraBold", size: 26)
    static let ppRowHeader   = Font.custom("Inter-Bold",      size: 22)
    static let ppCardTitle   = Font.custom("Inter-Bold",      size: 18)
    static let ppBody        = Font.custom("Inter-SemiBold",  size: 16)
    static let ppBodyReg     = Font.custom("Inter-Regular",   size: 16)
    static let ppCaption     = Font.custom("Inter-Medium",    size: 15)
    static let ppMeta        = Font.custom("Inter-Regular",   size: 13)
    static let ppPosterCap   = Font.custom("Inter-Medium",    size: 11)
    static let ppTab         = Font.custom("Inter-SemiBold",  size: 12)
    static let ppEyebrow     = Font.custom("Inter-ExtraBold", size: 11)
    static let ppButton      = Font.custom("Inter-Bold",      size: 15)
    static let ppHubLabel    = Font.custom("Inter-ExtraBold", size: 11)
    static let ppBadge       = Font.custom("Inter-ExtraBold", size: 8)
}

// Eyebrow modifier — 11pt uppercase tracked, sky-blue
struct PPEyebrow: ViewModifier {
    func body(content: Content) -> some View {
        content
            .font(.ppEyebrow)
            .tracking(1.6)
            .textCase(.uppercase)
            .foregroundStyle(Color.ppSky)
    }
}
extension View { func ppEyebrow() -> some View { modifier(PPEyebrow()) } }
```

## 3. Signature Components

### Brand-Hubs Row

```swift
struct Hub: Identifiable {
    let id = UUID()
    let name: String
    let gradient: [Color]
    var labelDark: Bool = false   // Nick/light networks use near-black label
}

struct BrandHubsRow: View {
    let hubs: [Hub]
    let onSelect: (Hub) -> Void

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(hubs) { hub in
                    Button { onSelect(hub) } label: {
                        Text(hub.name)
                            .font(.ppHubLabel)
                            .foregroundStyle(hub.labelDark ? Color(red: 0.106, green: 0.106, blue: 0.106) : .white)
                            .frame(minWidth: 70, minHeight: 40)
                            .padding(.horizontal, 12)
                            .background(
                                LinearGradient(colors: hub.gradient, startPoint: .topLeading, endPoint: .bottomTrailing),
                                in: RoundedRectangle(cornerRadius: 8)
                            )
                    }
                    .buttonStyle(.plain)
                }
            }
            .padding(.horizontal, 18)
        }
        .padding(.bottom, 12)
    }
}
```

### Hero Billboard

```swift
struct HeroBillboard: View {
    let artURL: URL?
    let eyebrow: String
    let title: String
    let meta: String
    let onPlay: () -> Void
    let onAdd: () -> Void

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            AsyncImage(url: artURL) { img in
                img.resizable().aspectRatio(contentMode: .fill)
            } placeholder: {
                Rectangle().fill(Color.ppCanvas2)
            }
            .frame(height: 332)
            .clipped()

            LinearGradient.ppHeroScrim.frame(height: 332)

            VStack(alignment: .leading, spacing: 8) {
                Text(eyebrow).ppEyebrow()
                Text(title).font(.ppHeroTitle).foregroundStyle(Color.ppTextPrimary)
                Text(meta).font(.ppMeta).foregroundStyle(Color.ppTextSecondary)
                HStack(spacing: 10) {
                    Button(action: onPlay) {
                        Label("Play", systemImage: "play.fill")
                            .font(.ppButton)
                            .foregroundStyle(.white)
                            .padding(.vertical, 13).padding(.horizontal, 28)
                            .background(Color.ppBlue, in: RoundedRectangle(cornerRadius: 6))
                    }
                    Button(action: onAdd) {
                        Label("My List", systemImage: "plus")
                            .font(.ppBody)
                            .foregroundStyle(.white)
                            .padding(.vertical, 12).padding(.horizontal, 16)
                            .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 6))
                    }
                }
                .padding(.top, 6)
            }
            .padding(.horizontal, 20)
            .padding(.bottom, 14)
        }
    }
}
```

### Poster Card (with LIVE / NEW)

```swift
struct PosterCard: View {
    let artURL: URL?
    let title: String
    var live: Bool = false
    var isNew: Bool = false
    @State private var pressed = false

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .topLeading) {
                AsyncImage(url: artURL) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Rectangle().fill(Color.ppSurface1) }
                    .frame(width: 116, height: 164)
                    .clipShape(RoundedRectangle(cornerRadius: 8))

                if live {
                    LiveFlag().padding(6)
                } else if isNew {
                    Text("NEW")
                        .font(.ppBadge).tracking(0.4)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 6).padding(.vertical, 3)
                        .background(Color.ppBlue, in: RoundedRectangle(cornerRadius: 4))
                        .padding(6)
                }
            }
            Text(title)
                .font(.ppPosterCap)
                .foregroundStyle(Color.ppTextSecondary)
                .lineLimit(1)
        }
        .frame(width: 116)
        .scaleEffect(pressed ? 0.97 : 1)
        .animation(.easeOut(duration: 0.12), value: pressed)
        ._onButtonGesture { pressed = $0 } perform: {}
    }
}

struct LiveFlag: View {
    @State private var dim = false
    var body: some View {
        HStack(spacing: 4) {
            Circle().fill(.white).frame(width: 5, height: 5)
                .opacity(dim ? 0.4 : 1)
                .animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: dim)
            Text("LIVE").font(.ppBadge).tracking(0.5).foregroundStyle(.white)
        }
        .padding(.horizontal, 6).padding(.vertical, 3)
        .background(Color.ppLive, in: RoundedRectangle(cornerRadius: 4))
        .onAppear { dim = true }
    }
}
```

### Live Badge & Keep-Watching Progress

```swift
struct LiveBadge: View {
    @State private var dim = false
    var body: some View {
        HStack(spacing: 6) {
            Circle().fill(.white).frame(width: 7, height: 7)
                .opacity(dim ? 0.4 : 1)
                .animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: dim)
            Text("LIVE").font(.ppEyebrow).tracking(0.6).foregroundStyle(.white)
        }
        .padding(.horizontal, 10).padding(.vertical, 5)
        .background(Color.ppLive, in: RoundedRectangle(cornerRadius: 4))
        .onAppear { dim = true }
    }
}

struct WatchProgress: View {
    let fraction: Double
    let label: String
    @State private var animated: Double = 0
    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.ppSurface2).frame(height: 4)
                    Capsule().fill(Color.ppBlue)
                        .frame(width: geo.size.width * animated, height: 4)
                }
            }
            .frame(height: 4)
            Text(label).font(.ppPosterCap).foregroundStyle(Color.ppTextSecondary)
        }
        .onAppear { withAnimation(.easeOut(duration: 0.4)) { animated = fraction } }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ParamountTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithOpaqueBackground()
        a.backgroundColor = UIColor(Color.ppCanvas.opacity(0.96))
        a.shadowColor = UIColor(Color.ppDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }

    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            LiveTVView().tabItem { Label("Live TV", systemImage: "tv") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            MyListView().tabItem { Label("My List", systemImage: "plus.rectangle.on.rectangle") }
        }
        .tint(.ppSky)   // active = sky-blue icon; no tint pill
    }
}
```

## 5. Motion

```swift
// Poster press
.scaleEffect(pressed ? 0.97 : 1)
.animation(.easeOut(duration: 0.12), value: pressed)

// Live dot pulse
.opacity(dim ? 0.4 : 1)
.animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: dim)

// Hub re-skin — cross-fade the whole browse into network color over 300ms
.animation(.easeInOut(duration: 0.3), value: activeHubID)

// Hero cross-fade between featured titles (~8s)
.transition(.opacity)
.animation(.easeInOut(duration: 0.4), value: featuredIndex)

// Detail open — shared element style
.matchedGeometryEffect(id: title.id, in: namespace)

// Sheet present
.sheet(isPresented: $showSheet) { /* slides up 300ms ease-out, dim fades */ }

// Haptics
let g = UIImpactFeedbackGenerator(style: .light);  g.impactOccurred() // poster select
let s = UIImpactFeedbackGenerator(style: .soft);   s.impactOccurred() // hub switch
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Live TV (tab) | `tv` / `tv.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| My List (tab) | `plus.rectangle.on.rectangle` | 22pt |
| Play (CTA) | `play.fill` | 14pt |
| Add to My List | `plus` / `checkmark` | 14pt |
| Back | `chevron.left` | 18pt |
| More info | `info.circle` | 18pt |
| Download | `arrow.down.circle` / `arrow.down.circle.fill` | 20pt |
| Cast | `airplayvideo` | 20pt |
| Profile | `person.crop.circle` | 21pt |
| Live indicator | `dot.radiowaves.left.and.right` | 14pt |
| Trailer | `play.rectangle` | 16pt |
| Settings | `gearshape` | 21pt |
| Schedule (Live) | `calendar` | 18pt |
| Share | `square.and.arrow.up` | 20pt |

## 7. Dark Mode

```swift
struct ParamountTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.ppCanvas)
            .foregroundStyle(Color.ppTextPrimary)
            .preferredColorScheme(.dark)   // Paramount+ is dark-only — lock it
    }
}
extension View { func paramountTheme() -> some View { modifier(ParamountTheme()) } }
```

Paramount+ ships **no light theme**. Lock `.preferredColorScheme(.dark)` at the root. The only "elevation" cues on the navy canvas are the subtle `#0E1438` lift under hero art and a 1pt `#2A3266` border on floating sheets/menus — soft shadows are nearly invisible at this luminance.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`AsyncImage`, `.ultraThinMaterial`, `Label` are comfortable; `.sensoryFeedback` needs iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Bundle Inter (Regular / Medium / SemiBold / Bold / ExtraBold / Black) TTFs via `Info.plist` `UIAppFonts` — SIL OFL, free to ship; Paramount Sans is proprietary and not redistributable
- Dynamic Type: scale screen/hero titles, row headers, body, captions; keep tab labels, eyebrows, hub labels, badges, "LIVE" at FIXED sizes (composited over art / layout-sensitive)
- VoiceOver: label hub chips "{network} hub, opens {network} shows"; the blue CTA as "Play {title}"; live cards announce "Live now"; group hero eyebrow+title+meta as one element
- The pulsing live dot is decorative — mark `.accessibilityHidden(true)`; convey "Live" via the flag label text
- Color contrast: white `#FFFFFF` and `#AEB4D6` on `#0A0E2D` pass WCAG AA; white on `#0064FF` passes AA for the CTA; sky-blue `#4D9DFF` on navy passes AA for the active tab; never place body text on the blue accent
- Reduce Motion: disable the hub re-skin cross-fade, hero cross-fade, and live-dot pulse (show a static dot); keep press scale subtle or remove
- Dark mode: there is no alternate scheme — the navy `#0A0E2D` / `#0E1438` system holds in all conditions; do not invert
