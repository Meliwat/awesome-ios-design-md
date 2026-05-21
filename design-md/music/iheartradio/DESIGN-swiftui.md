# iHeartRadio (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates iHeartRadio's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the heart-logomark station tile, the pulsing LIVE badge, the scanning bar (no scrubber), the circular red play/stop button, and the live station row.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — the only real theme for the player)
    static let ihrCanvas    = Color(red: 0.071, green: 0.039, blue: 0.055) // #120A0E
    static let ihrSurface1  = Color(red: 0.118, green: 0.071, blue: 0.086) // #1E1216
    static let ihrSurface2  = Color(red: 0.165, green: 0.090, blue: 0.114) // #2A171D
    static let ihrDivider   = Color(red: 0.204, green: 0.110, blue: 0.141) // #341C24

    // MARK: - Text
    static let ihrTextPrimary   = Color.white                                  // #FFFFFF
    static let ihrTextSecondary = Color(red: 0.722, green: 0.627, blue: 0.659) // #B8A0A8
    static let ihrTextTertiary  = Color(red: 0.486, green: 0.380, blue: 0.408) // #7C6168

    // MARK: - Brand
    static let ihrRed       = Color(red: 0.776, green: 0.000, blue: 0.169) // #C6002B
    static let ihrCoral     = Color(red: 0.949, green: 0.227, blue: 0.184) // #F23A2F  (LIVE)
    static let ihrMagenta   = Color(red: 0.894, green: 0.039, blue: 0.365) // #E40A5D
    static let ihrRedPress  = Color(red: 0.620, green: 0.000, blue: 0.133) // #9E0022
    static let ihrCoralPress = Color(red: 0.800, green: 0.176, blue: 0.141) // #CC2D24

    // MARK: - Semantic
    static let ihrSuccess = Color(red: 0.118, green: 0.843, blue: 0.376) // #1ED760
    static let ihrError   = Color(red: 1.000, green: 0.302, blue: 0.369) // #FF4D5E
    static let ihrWarning = Color(red: 1.000, green: 0.690, blue: 0.180) // #FFB02E
}

// The iHeart system gradient — station logos, brand chips, hero cards
extension LinearGradient {
    static let ihrSystem = LinearGradient(
        colors: [.ihrRed, .ihrMagenta],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
    static let ihrTile = LinearGradient(
        stops: [
            .init(color: .ihrSurface2, location: 0.0),
            .init(color: .ihrSurface1, location: 0.6),
            .init(color: .ihrCanvas,   location: 1.0),
        ],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

iHeartRadio ships **iHeart Sans**; use **Inter** as the faithful fallback. Bundle both via `Info.plist` / `UIAppFonts`. Body/metadata uses Inter at 400/500; titles use the brand face at 700–800; LIVE tags at 900.

```swift
enum IHRFont {
    // Swap "iHeartSans" → "Inter" if the brand face isn't bundled.
    static let brand = "iHeartSans"
    static let read  = "Inter"
}

extension Font {
    static func ihrBrand(_ size: CGFloat, _ weight: Font.Weight = .bold) -> Font {
        Font.custom(IHRFont.brand, size: size).weight(weight)
    }
    static func ihrRead(_ size: CGFloat, _ weight: Font.Weight = .regular) -> Font {
        Font.custom(IHRFont.read, size: size).weight(weight)
    }

    static let ihrScreenTitle = Font.custom(IHRFont.brand, size: 32).weight(.heavy)    // 800
    static let ihrStationName = Font.custom(IHRFont.brand, size: 26).weight(.heavy)    // 800
    static let ihrSection     = Font.custom(IHRFont.brand, size: 22).weight(.bold)     // 700
    static let ihrSubhead     = Font.custom(IHRFont.brand, size: 18).weight(.bold)     // 700
    static let ihrBody        = Font.custom(IHRFont.read,  size: 16).weight(.regular)  // 400
    static let ihrRowTitle    = Font.custom(IHRFont.brand, size: 15).weight(.semibold) // 600
    static let ihrNowAiring   = Font.custom(IHRFont.read,  size: 15).weight(.medium)   // 500
    static let ihrMeta        = Font.custom(IHRFont.read,  size: 14).weight(.regular)  // 400
    static let ihrLiveTag     = Font.custom(IHRFont.brand, size: 12).weight(.black)    // 900, UPPERCASE
    static let ihrFreq        = Font.custom(IHRFont.brand, size: 12).weight(.heavy)    // 800
    static let ihrTabLabel    = Font.custom(IHRFont.brand, size: 10).weight(.semibold) // 600
    static let ihrButton      = Font.custom(IHRFont.brand, size: 16).weight(.bold)     // 700
    static let ihrChip        = Font.custom(IHRFont.brand, size: 13).weight(.bold)     // 700
}
```

## 3. Signature Components

### Heart-Logomark Station Tile (with pulsing LIVE badge + frequency chip)

```swift
struct StationTile: View {
    let frequency: String       // "103.5 FM"
    var isLive: Bool = true

    var body: some View {
        GeometryReader { geo in
            ZStack {
                LinearGradient.ihrTile
                    .overlay(
                        RadialGradient(colors: [Color.ihrRed.opacity(0.35), .clear],
                                       center: .init(x: 0.5, y: 0.42),
                                       startRadius: 0, endRadius: geo.size.width * 0.62)
                    )

                // iHeart heart-check logomark as artwork
                HeartMark()
                    .frame(width: geo.size.width * 0.44, height: geo.size.width * 0.44)
                    .shadow(color: Color.ihrRed.opacity(0.55), radius: 22, x: 0, y: 8)

                if isLive {
                    LiveBadge()
                        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                        .padding(16)
                }

                Text(frequency)
                    .font(.ihrFreq).foregroundStyle(.white)
                    .padding(.vertical, 6).padding(.horizontal, 12)
                    .background(Color.ihrCanvas.opacity(0.7), in: RoundedRectangle(cornerRadius: 8))
                    .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 8))
                    .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .bottomTrailing)
                    .padding(16)
            }
            .clipShape(RoundedRectangle(cornerRadius: 14))
            .shadow(color: Color.ihrRed.opacity(0.45), radius: 24, x: 0, y: 24)
        }
        .aspectRatio(1, contentMode: .fit)
    }
}

struct HeartMark: View {
    var body: some View {
        ZStack {
            // Heart shape (approximation via SF Symbol fill + check overlay)
            Image(systemName: "heart.fill")
                .resizable().scaledToFit()
                .foregroundStyle(Color.ihrRed)
            Image(systemName: "checkmark")
                .font(.system(size: 22, weight: .heavy))
                .foregroundStyle(.white)
        }
    }
}
```

### Pulsing LIVE Badge

```swift
struct LiveBadge: View {
    @State private var pulse = false

    var body: some View {
        HStack(spacing: 7) {
            ZStack {
                Circle().fill(.white).frame(width: 7, height: 7)
                Circle()
                    .stroke(Color.white.opacity(pulse ? 0 : 0.7), lineWidth: 2)
                    .frame(width: pulse ? 21 : 7, height: pulse ? 21 : 7)
            }
            .frame(width: 7, height: 7)
            Text("LIVE").font(.ihrLiveTag).tracking(0.8).foregroundStyle(.white)
        }
        .padding(.vertical, 6).padding(.leading, 10).padding(.trailing, 12)
        .background(Color.ihrCoral, in: Capsule())
        .onAppear {
            withAnimation(.easeOut(duration: 1.6).repeatForever(autoreverses: false)) { pulse = true }
        }
    }
}
```

### Scanning Bar (replaces the scrubber — live radio can't seek)

```swift
struct ScanningBar: View {
    @State private var sweep = false

    var body: some View {
        VStack(spacing: 10) {
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.ihrSurface2).frame(height: 4)
                    LinearGradient(
                        colors: [.clear, .ihrCoral, .clear],
                        startPoint: .leading, endPoint: .trailing
                    )
                    .frame(width: geo.size.width * 0.38, height: 4)
                    .clipShape(Capsule())
                    .offset(x: sweep ? geo.size.width : -geo.size.width * 0.38)
                }
                .frame(height: 4)
                .clipShape(Capsule())
            }
            .frame(height: 4)

            HStack(spacing: 6) {
                Circle().fill(Color.ihrCoral).frame(width: 6, height: 6)
                Text("STREAMING LIVE")
                    .font(.ihrRead(11, .bold))
                    .tracking(0.4)
                    .foregroundStyle(Color.ihrCoral)
                Spacer()
            }
        }
        .onAppear {
            withAnimation(.easeInOut(duration: 2.2).repeatForever(autoreverses: false)) { sweep = true }
        }
    }
}
```

### Circular Red Play/Stop Button (no pause — live)

```swift
struct PlayStopButton: View {
    @Binding var isPlaying: Bool   // true = streaming, false = stopped

    var body: some View {
        Button {
            isPlaying.toggle()
        } label: {
            ZStack {
                Circle().fill(Color.ihrRed)
                Image(systemName: isPlaying ? "stop.fill" : "play.fill")
                    .font(.system(size: 26, weight: .bold))
                    .foregroundStyle(.white)
                    .offset(x: isPlaying ? 0 : 2)
            }
            .frame(width: 68, height: 68)
            .shadow(color: Color.ihrRed.opacity(0.65), radius: 14, x: 0, y: 12)
        }
        .buttonStyle(IHRPressScale())
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)
    }
}

struct IHRPressScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.94 : 1)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Live Station Row

```swift
struct LiveStationRow: View {
    let callSign: String        // "Z100"
    let name: String            // "Z100 New York"
    let genre: String           // "Hit Music Now"

    var body: some View {
        HStack(spacing: 12) {
            ZStack {
                RoundedRectangle(cornerRadius: 8).fill(LinearGradient.ihrSystem)
                Text(callSign).font(.ihrBrand(12, .black)).foregroundStyle(.white)
            }
            .frame(width: 48, height: 48)

            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.ihrRowTitle).foregroundStyle(Color.ihrTextPrimary)
                HStack(spacing: 6) {
                    HStack(spacing: 4) {
                        Circle().fill(Color.ihrCoral).frame(width: 5, height: 5)
                        Text("LIVE").font(.ihrBrand(10, .black)).tracking(0.5)
                            .foregroundStyle(Color.ihrCoral)
                    }
                    Text(genre).font(.ihrMeta).foregroundStyle(Color.ihrTextSecondary)
                }
            }
            Spacer()
            Image(systemName: "play.circle")
                .font(.system(size: 28)).foregroundStyle(Color.ihrCoral)
        }
        .frame(height: 68)
        .contentShape(Rectangle())
    }
}
```

### Live Station Player Top Bar + Primary Button

```swift
struct PlayerTopBar: View {
    var body: some View {
        HStack {
            Image(systemName: "chevron.down").font(.system(size: 22, weight: .bold))
                .foregroundStyle(Color.ihrTextPrimary)
            Spacer()
            VStack(spacing: 2) {
                Text("LIVE STATION").font(.ihrLiveTag).tracking(0.8)
                    .foregroundStyle(Color.ihrTextSecondary)
                Text("For You").font(.ihrRead(13, .bold))
                    .foregroundStyle(Color.ihrTextPrimary)
            }
            Spacer()
            Image(systemName: "ellipsis").font(.system(size: 22, weight: .bold))
                .foregroundStyle(Color.ihrTextPrimary)
        }
    }
}

struct PrimaryButton: View {
    let title: String
    var action: () -> Void = {}
    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 14, weight: .bold))
                Text(title).font(.ihrButton)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 14).padding(.horizontal, 30)
            .background(Color.ihrRed, in: Capsule())
        }
        .buttonStyle(IHRPressScale())
    }
}
```

## 4. Bottom Tab Bar

```swift
struct IHRTabView: View {
    var body: some View {
        TabView {
            ForYouView().tabItem   { Label("For You",  systemImage: "house.fill") }
            RadioView().tabItem    { Label("Radio",    systemImage: "dot.radiowaves.left.and.right") }
            PodcastsView().tabItem { Label("Podcasts", systemImage: "list.bullet.below.rectangle") }
            SearchView().tabItem   { Label("Search",   systemImage: "magnifyingglass") }
            LibraryView().tabItem  { Label("Library",  systemImage: "person.fill") }
        }
        .tint(.ihrCoral)   // active = coral; inactive defaults to tertiary
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.ihrCanvas).withAlphaComponent(0.94)
            a.shadowColor = UIColor(Color.ihrDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
            UITabBar.appearance().unselectedItemTintColor = UIColor(Color.ihrTextTertiary)
        }
    }
}
```

## 5. Motion

```swift
// LIVE badge pulse — continuous while live (ring expands & fades, ~1.6s)
withAnimation(.easeOut(duration: 1.6).repeatForever(autoreverses: false)) { pulse = true }

// Scanning bar sweep — continuous while streaming (~2.2s left→right)
withAnimation(.easeInOut(duration: 2.2).repeatForever(autoreverses: false)) { sweep = true }

// Play/stop — glyph crossfade + scale, soft haptic (NO pause state)
.scaleEffect(isPressed ? 0.94 : 1)
.animation(.easeOut(duration: 0.12), value: isPressed)
.sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)

// Player expand/collapse (mini → full)
withAnimation(.spring(response: 0.32, dampingFraction: 0.82)) { expanded.toggle() }

// Station skip — tile slide + metadata crossfade
.transition(.asymmetric(insertion: .move(edge: .trailing), removal: .opacity))

// Reduce Motion: stop the LIVE pulse + scan sweep (show static badge + static
// coral fill at left edge); keep play/stop crossfade (state-critical)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Collapse player | `chevron.down` | 22pt |
| Overflow menu | `ellipsis` | 22pt |
| Play | `play.fill` | 26pt |
| Stop (live — no pause) | `stop.fill` | 26pt |
| Station skip back | `backward.end.fill` | 22pt |
| Station skip fwd | `forward.end.fill` | 22pt |
| Player settings | `gearshape.fill` | 22pt |
| Favorite (filled) | `heart.fill` | 24pt |
| Heart logomark | `heart.fill` + `checkmark` | tile |
| Row play | `play.circle` | 28pt |
| For You (tab) | `house` / `house.fill` | 22pt |
| Radio (tab) | `dot.radiowaves.left.and.right` | 22pt |
| Podcasts (tab) | `list.bullet.below.rectangle` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Library (tab) | `person` / `person.fill` | 22pt |
| Search field | `magnifyingglass` | 18pt |
| Add to library | `plus` | 18pt |
| Share | `square.and.arrow.up` | 22pt |

## 7. Dark Mode

iHeartRadio's player is dark-native — there is no real light theme for it. Pin the dark palette regardless of system setting; only honor a forced light environment with the minimal fallback in DESIGN.md §2.

```swift
struct IHRTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.ihrCanvas)
            .foregroundStyle(Color.ihrTextPrimary)
            .preferredColorScheme(.dark)   // force dark — iHeartRadio's player identity
    }
}
extension View { func ihrTheme() -> some View { modifier(IHRTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator(style: .soft)`)
- Bundle iHeart Sans if licensed; otherwise ship **Inter** (SIL OFL, free) as the faithful fallback — never silently degrade to system-only for titles
- Dynamic Type: scale screen/station titles, section headers, body, row text; keep tab labels, LIVE tags, frequency chip, overlines, chip text FIXED (layout-sensitive)
- VoiceOver: label the play/stop button "Play" / "Stop" (never "Pause" — it's a live stream); announce the station tile as "{station name}, live, now airing {track}"; the scanning bar is decorative — `.accessibilityHidden(true)` — convey "streaming live" via a label, not the sweep alone
- The LIVE state must be conveyed non-visually: add an `.accessibilityLabel` containing "Live" on the station and rows, not coral color or the pulse alone
- Color contrast: `#FFFFFF` on `#120A0E` and white on `#C6002B` / `#F23A2F` pass WCAG AA; `#B8A0A8` secondary on `#120A0E` passes AA for ≥14pt text
- Reduce Motion: disable the LIVE pulse ring and the scanning sweep (show a static LIVE badge and a static coral fill at the track's left edge) — keep the play/stop crossfade (state-critical)
- Reduce Transparency: replace the frequency-chip / tab-bar blur with solid `#120A0E` / `rgba(18,10,14,0.85)`
- Never show a pause control on the live player and never add timestamps or a seekable scrubber — a live broadcast has no position; this is both a correctness and an accessibility concern (don't announce a progress value)
