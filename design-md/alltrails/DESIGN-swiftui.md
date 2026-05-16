# AllTrails (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates AllTrails' visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let atCanvas        = Color.white                                  // #FFFFFF
    static let atSurfaceSage   = Color(red: 0.949, green: 0.957, blue: 0.945) // #F2F4F1
    static let atSurfaceSunken = Color(red: 0.922, green: 0.933, blue: 0.910) // #EBEEE8
    static let atDivider       = Color(red: 0.886, green: 0.902, blue: 0.875) // #E2E6DF

    // MARK: - Text
    static let atTextPrimary   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let atTextSecondary = Color(red: 0.420, green: 0.435, blue: 0.408) // #6B6F68
    static let atTextTertiary  = Color(red: 0.604, green: 0.620, blue: 0.588) // #9A9E96

    // MARK: - Brand
    static let atGreen        = Color(red: 0.259, green: 0.502, blue: 0.000) // #428000
    static let atGreenPressed = Color(red: 0.200, green: 0.388, blue: 0.000) // #336300
    static let atGreenSoft    = Color(red: 0.910, green: 0.941, blue: 0.875) // #E8F0DF

    // MARK: - Difficulty Scale (color = meaning)
    static let atEasy     = Color(red: 0.259, green: 0.502, blue: 0.000) // #428000
    static let atModerate = Color(red: 0.780, green: 0.467, blue: 0.000) // #C77700
    static let atHard     = Color(red: 0.702, green: 0.149, blue: 0.118) // #B3261E
    static let atEasyBg     = Color(red: 0.910, green: 0.941, blue: 0.875) // #E8F0DF
    static let atModerateBg = Color(red: 0.984, green: 0.933, blue: 0.867) // #FBEEDD
    static let atHardBg     = Color(red: 0.969, green: 0.878, blue: 0.871) // #F7E0DE

    // MARK: - Semantic
    static let atStarGold = Color(red: 0.949, green: 0.663, blue: 0.231) // #F2A93B
}

enum Difficulty {
    case easy, moderate, hard
    var color: Color { switch self { case .easy: .atEasy; case .moderate: .atModerate; case .hard: .atHard } }
    var bg: Color { switch self { case .easy: .atEasyBg; case .moderate: .atModerateBg; case .hard: .atHardBg } }
    var label: String { switch self { case .easy: "EASY"; case .moderate: "MODERATE"; case .hard: "HARD" } }
}
```

## 2. Typography

Inter is open-source (SIL OFL). Bundle the TTFs via `Info.plist` (`UIAppFonts`). Use tabular figures on stats and ratings.

```swift
extension Font {
    static let atScreenTitle = Font.custom("Inter-Bold",     size: 28)
    static let atDetailTitle = Font.custom("Inter-Bold",     size: 24)
    static let atSection     = Font.custom("Inter-Bold",     size: 20)
    static let atCardTitle   = Font.custom("Inter-SemiBold", size: 17)
    static let atBody        = Font.custom("Inter-Regular",  size: 16)
    static let atStatValue   = Font.custom("Inter-Bold",     size: 15)
    static let atSubtitle    = Font.custom("Inter-Regular",  size: 14)
    static let atDifficulty  = Font.custom("Inter-Bold",     size: 13)
    static let atStatLabel   = Font.custom("Inter-Regular",  size: 13)
    static let atButton      = Font.custom("Inter-SemiBold", size: 16)
    static let atRating      = Font.custom("Inter-Bold",     size: 14)
    static let atMeta        = Font.custom("Inter-Regular",  size: 13)
    static let atTab         = Font.custom("Inter-SemiBold", size: 11)
    static let atPinLabel    = Font.custom("Inter-Bold",     size: 12)
    static let atLabelUpper  = Font.custom("Inter-Bold",     size: 12)
}

extension View {
    func atTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Difficulty Pill

```swift
struct DifficultyPill: View {
    let difficulty: Difficulty
    var solid: Bool = false  // solid variant for the detail hero

    var body: some View {
        Text(difficulty.label)
            .font(.atDifficulty).tracking(0.3)
            .foregroundStyle(solid ? .white : difficulty.color)
            .padding(.vertical, 6).padding(.horizontal, 12)
            .background(Capsule().fill(solid ? difficulty.color : difficulty.bg))
    }
}
```

### Trail Card (signature)

```swift
struct TrailCard: View {
    let name: String
    let location: String
    let difficulty: Difficulty
    let rating: Double
    let reviews: Int
    let length: String
    let time: String
    let elevation: String
    let photo: Image
    @Binding var saved: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            ZStack(alignment: .topTrailing) {
                photo
                    .resizable().aspectRatio(4/3, contentMode: .fill)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
                Button { saved.toggle() } label: {
                    Image(systemName: saved ? "heart.fill" : "heart")
                        .font(.system(size: 18))
                        .foregroundStyle(saved ? Color.atGreen : .white)
                        .padding(8)
                        .background(Circle().fill(.black.opacity(0.25)))
                }
                .padding(12)
                .sensoryFeedback(.success, trigger: saved)
            }
            HStack {
                DifficultyPill(difficulty: difficulty)
                Spacer()
                HStack(spacing: 4) {
                    Image(systemName: "star.fill")
                        .font(.system(size: 12)).foregroundStyle(Color.atStarGold)
                    Text(String(format: "%.1f", rating)).font(.atRating).atTabular()
                    Text("(\(reviews))").font(.atMeta).foregroundStyle(Color.atTextSecondary)
                }
            }
            Text(name).font(.atCardTitle).foregroundStyle(Color.atTextPrimary).lineLimit(1)
            Text(location).font(.atSubtitle).foregroundStyle(Color.atTextSecondary)
            HStack(spacing: 6) {
                stat("Length", length)
                Text("·").foregroundStyle(Color.atTextTertiary)
                stat("Est.", time)
                Text("·").foregroundStyle(Color.atTextTertiary)
                stat("↑", elevation)
            }
        }
    }

    private func stat(_ label: String, _ value: String) -> some View {
        HStack(spacing: 4) {
            Text(label).font(.atStatLabel).foregroundStyle(Color.atTextSecondary)
            Text(value).font(.atStatValue).foregroundStyle(Color.atTextPrimary).atTabular()
        }
    }
}
```

### Record Button (signature)

```swift
struct RecordButton: View {
    @Binding var recording: Bool
    @State private var pulse = false

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) { recording.toggle() }
        } label: {
            ZStack {
                Circle()
                    .fill(recording ? Color.atHard : Color.atGreen)
                    .frame(width: 60, height: 60)
                    .shadow(color: (recording ? Color.atHard : Color.atGreen).opacity(0.30),
                            radius: 18, y: 6)
                Image(systemName: recording ? "stop.fill" : "record.circle")
                    .font(.system(size: 26)).foregroundStyle(.white)
            }
            .scaleEffect(recording && pulse ? 1.06 : 1.0)
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: recording)
        .onChange(of: recording) { _, on in
            if on { withAnimation(.easeInOut(duration: 0.8).repeatForever(autoreverses: true)) { pulse = true } }
            else { pulse = false }
        }
    }
}
```

### Primary / Outline Buttons

```swift
struct ATPrimaryButton: View {
    let title: String
    var systemImage: String? = nil
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                if let s = systemImage { Image(systemName: s).font(.system(size: 18)) }
                Text(title).font(.atButton)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 14).padding(.horizontal, 28)
            .frame(maxWidth: .infinity)
            .background(Capsule().fill(Color.atGreen))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: title)
        .buttonStyle(ATPressableStyle())
    }
}

struct ATOutlineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.atButton).foregroundStyle(Color.atGreen)
                .padding(.vertical, 13).padding(.horizontal, 24)
                .background(Capsule().strokeBorder(Color.atGreen, lineWidth: 1.5))
        }
        .buttonStyle(ATPressableStyle())
    }
}

struct ATPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.97 : 1)
            .animation(.easeOut(duration: 0.2), value: configuration.isPressed)
    }
}
```

### Route Trace (signature)

```swift
struct RouteTrace: Shape {
    var points: [CGPoint]
    var progress: CGFloat // 0...1, animatable

    var animatableData: CGFloat {
        get { progress } set { progress = newValue }
    }

    func path(in rect: CGRect) -> Path {
        var p = Path()
        guard points.count > 1 else { return p }
        p.move(to: points[0])
        for pt in points.dropFirst() { p.addLine(to: pt) }
        return p.trimmedPath(from: 0, to: progress)
    }
}

struct RouteTraceView: View {
    let points: [CGPoint]
    @State private var progress: CGFloat = 0
    var body: some View {
        RouteTrace(points: points, progress: progress)
            .stroke(Color.atGreen, style: StrokeStyle(lineWidth: 4, lineCap: .round, lineJoin: .round))
            .onAppear { withAnimation(.easeOut(duration: 1.0)) { progress = 1 } }
    }
}
```

## 4. Tab Bar

`TabView` with a custom `UITabBarAppearance` — near-opaque white glass with a sage hairline. **Active tint is AllTrails Green.**

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterialLight)
        a.backgroundColor = UIColor.white.withAlphaComponent(0.96)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            ExploreView().tabItem { Label("Explore", systemImage: "map.fill") }
            SavedView().tabItem { Label("Saved", systemImage: "bookmark.fill") }
            NavigatorView().tabItem { Label("Navigator", systemImage: "location.north.circle.fill") }
            CommunityView().tabItem { Label("Community", systemImage: "person.2.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle.fill") }
        }
        .tint(.atGreen)
    }
}
```

## 5. Motion

```swift
// Route trace draw — polyline trims 0 → 1 over 1000ms ease-out (see RouteTraceView)
withAnimation(.easeOut(duration: 1.0)) { progress = 1 }

// Map pin select — pin scales ~1.2× + white ring; mini card slides up 300ms ease-out
withAnimation(.easeOut(duration: 0.3)) { selectedPin = pin }

// Card tap
.buttonStyle(ATPressableStyle())  // 200ms ease-out, scale 0.98

// Map/List toggle — active fill slides 250ms
withAnimation(.easeInOut(duration: 0.25)) { isMap.toggle() }

// Record start — green circle → red stop, firm haptic, banner expands
.sensoryFeedback(.impact(weight: .medium), trigger: recording)

// Save heart — fill + bounce 1.0 → 1.15 → 1.0 over 300ms
.sensoryFeedback(.success, trigger: saved)
```

Haptics via `.sensoryFeedback` (iOS 17+): `.impact(flexibility: .soft)` on primary CTAs, `.impact(weight: .medium)` on Record start/stop, `.success` on save.

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Record | `record.circle` | 26pt |
| Stop recording | `stop.fill` | 26pt |
| Save | `heart` / `heart.fill` | 18-20pt |
| Star (rating) | `star.fill` | 12pt |
| Directions | `arrow.triangle.turn.up.right.diamond.fill` | 18pt |
| Share | `square.and.arrow.up` | 20pt |
| Filter | `slider.horizontal.3` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Elevation | `arrow.up.forward` | 14pt |
| Distance | `point.topleft.down.curvedto.point.bottomright.up` | 14pt |
| Explore (tab) | `map.fill` | 24pt |
| Saved (tab) | `bookmark.fill` | 24pt |
| Navigator (tab) | `location.north.circle.fill` | 24pt |
| Community (tab) | `person.2.fill` | 24pt |
| Profile (tab) | `person.crop.circle.fill` | 24pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` needs iOS 17; fall back to `UIImpactFeedbackGenerator`); `MapKit`'s SwiftUI `Map` is richest on iOS 17+
- Tabular figures: register Inter with the tabular numeral feature or rely on `.monospacedDigit()` so a scrolling list of trail cards keeps its stat columns aligned
- Dynamic Type: scale titles, trail names, body, reviews; let the stat row wrap to two lines at the largest sizes; pin the difficulty pill, tab labels, map-pin labels
- VoiceOver: trail card is one element — "Eagle Peak Trail, moderate, 4.7 stars, 1,284 reviews, 5.2 miles, 1,200 feet elevation gain"; never communicate difficulty by color alone — the pill always carries the word
- Color is information, not the only signal: the difficulty pill text and a legend make green/amber/red decodable for color-blind users
- Reduce Motion: replace the route-trace draw with the full route shown immediately; disable the pin-scale + banner animations
- Outdoor legibility: support Increase Contrast — bump `#6B6F68` toward `#54584F`; ensure the difficulty pill remains AA at 13pt; respect higher brightness use (the high-contrast light theme is intentional — do not auto-dim content)
- Light-first: a dark mode exists for OS parity / night navigation but the light topo-paper experience is primary
