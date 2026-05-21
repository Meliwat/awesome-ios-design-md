# Strava (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Strava's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, themed components, and the signature feed-card layout with route polyline.

Targets iOS 17+. `.sensoryFeedback`, `MapKit` overlays, and `matchedGeometryEffect` all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Strava Orange — the only accent
    static let stravaOrange        = Color(red: 0.988, green: 0.298, blue: 0.008)  // #FC4C02
    static let stravaOrangePressed = Color(red: 0.831, green: 0.251, blue: 0.008)  // #D44002
    static let stravaOrangeHalo    = Color(red: 0.988, green: 0.298, blue: 0.008).opacity(0.3)

    // MARK: - Canvas, Surface, Divider (Light)
    static let stravaCanvas       = Color(red: 1.00, green: 1.00, blue: 1.00)     // #FFFFFF
    static let stravaSurfaceWarm  = Color(red: 0.961, green: 0.957, blue: 0.949)  // #F5F4F2
    static let stravaSurfaceCool  = Color(red: 0.941, green: 0.941, blue: 0.941)  // #F0F0F0
    static let stravaDivider      = Color(red: 0.898, green: 0.898, blue: 0.898)  // #E5E5E5
    static let stravaMapTile      = Color(red: 0.910, green: 0.898, blue: 0.867)  // #E8E5DD

    // MARK: - Text (Light)
    static let stravaInkPrimary   = Color(red: 0.055, green: 0.055, blue: 0.055)  // #0E0E0E
    static let stravaInkSecondary = Color(red: 0.400, green: 0.400, blue: 0.400)  // #666666
    static let stravaInkTertiary  = Color(red: 0.604, green: 0.604, blue: 0.604)  // #9A9A9A

    // MARK: - Dark mode
    static let stravaDarkCanvas   = Color(red: 0.059, green: 0.059, blue: 0.059)  // #0F0F0F coal
    static let stravaDarkSurface  = Color(red: 0.102, green: 0.102, blue: 0.102)  // #1A1A1A
    static let stravaDarkSurface2 = Color(red: 0.149, green: 0.149, blue: 0.149)  // #262626
    static let stravaDarkDivider  = Color(red: 0.165, green: 0.165, blue: 0.165)  // #2A2A2A
    static let stravaDarkText     = Color(red: 0.949, green: 0.949, blue: 0.949)  // #F2F2F2
    static let stravaDarkTextSec  = Color(red: 0.627, green: 0.627, blue: 0.627)  // #A0A0A0
    static let stravaDarkMapTile  = Color(red: 0.106, green: 0.106, blue: 0.106)  // #1B1B1B

    // MARK: - Achievement
    static let stravaPRGold       = Color(red: 0.961, green: 0.761, blue: 0.290)  // #F5C24A
    static let stravaSilver       = Color(red: 0.776, green: 0.776, blue: 0.776)  // #C6C6C6
    static let stravaBronze       = Color(red: 0.804, green: 0.498, blue: 0.196)  // #CD7F32
    static let stravaKOMCrown     = Color(red: 1.00, green: 0.843, blue: 0.00)    // #FFD700

    // MARK: - Chart & Semantic
    static let stravaSuccess      = Color(red: 0.133, green: 0.773, blue: 0.369)  // #22C55E
    static let stravaHeartRed     = Color(red: 0.906, green: 0.298, blue: 0.235)  // #E74C3C
    static let stravaPaceBlue     = Color(red: 0.231, green: 0.510, blue: 0.965)  // #3B82F6
    static let stravaElevBrown    = Color(red: 0.545, green: 0.435, blue: 0.278)  // #8B6F47
}
```

## 2. Typography

Use SF Pro Display ≥ 20pt and SF Pro Text ≤ 17pt — Apple's documented switchover. Every numeric style uses `.monospacedDigit()`.

```swift
extension Font {
    // Hero stats (activity detail) — SF Pro Display Black
    static let stravaHeroStat   = Font.system(size: 44, weight: .black,    design: .default).monospacedDigit()
    static let stravaHeroUnit   = Font.system(size: 13, weight: .semibold, design: .default)

    // Large nav title
    static let stravaLargeNav   = Font.system(size: 28, weight: .bold,     design: .default)

    // Activity card titles & athlete names
    static let stravaActivityTitle = Font.system(size: 17, weight: .semibold, design: .default)
    static let stravaAthlete    = Font.system(size: 15, weight: .semibold, design: .default)

    // Section / stat label — UPPERCASE 11pt Semibold
    static let stravaStatLabel  = Font.system(size: 11, weight: .semibold, design: .default)
    static let stravaSectionHdr = Font.system(size: 13, weight: .bold,     design: .default)

    // Stat values (the 3-up grid)
    static let stravaStatValue  = Font.system(size: 17, weight: .bold,     design: .default).monospacedDigit()

    // Body
    static let stravaBody       = Font.system(size: 15, weight: .regular,  design: .default)
    static let stravaBodySmall  = Font.system(size: 13, weight: .regular,  design: .default)

    // Kudos / counts / metadata
    static let stravaKudosCount = Font.system(size: 13, weight: .semibold, design: .default).monospacedDigit()
    static let stravaMeta       = Font.system(size: 13, weight: .regular,  design: .default)

    // Buttons
    static let stravaButton     = Font.system(size: 17, weight: .semibold, design: .default)
    static let stravaButtonSm   = Font.system(size: 15, weight: .semibold, design: .default)

    // Tab & rank
    static let stravaTab        = Font.system(size: 10, weight: .semibold, design: .default)
    static let stravaRank       = Font.system(size: 13, weight: .black,    design: .default)
}
```

## 3. Signature Components

### Stat Cell (the 3-up grid primitive)

```swift
struct StatCell: View {
    let label: String
    let value: String

    var body: some View {
        VStack(spacing: 4) {
            Text(label.uppercased())
                .font(.stravaStatLabel)
                .tracking(0.6)
                .foregroundStyle(Color.stravaInkSecondary)
            Text(value)
                .font(.stravaStatValue)
                .foregroundStyle(Color.stravaInkPrimary)
        }
        .frame(maxWidth: .infinity)
        .padding(.vertical, 16)
    }
}

struct StatGrid: View {
    let stats: [(String, String)]   // up to 3
    var body: some View {
        HStack(spacing: 0) {
            ForEach(Array(stats.enumerated()), id: \.offset) { idx, s in
                StatCell(label: s.0, value: s.1)
                if idx < stats.count - 1 {
                    Rectangle().fill(Color.stravaSurfaceCool).frame(width: 1).padding(.vertical, 12)
                }
            }
        }
    }
}
```

### Route Map Snapshot with Polyline Halo

```swift
import MapKit

struct RouteMapSnapshot: View {
    let coordinates: [CLLocationCoordinate2D]

    var body: some View {
        ZStack {
            Map {
                // Halo polyline drawn first
                MapPolyline(coordinates: coordinates)
                    .stroke(Color.stravaOrangeHalo, style: StrokeStyle(lineWidth: 6, lineCap: .round, lineJoin: .round))
                // Main polyline drawn on top
                MapPolyline(coordinates: coordinates)
                    .stroke(Color.stravaOrange, style: StrokeStyle(lineWidth: 4, lineCap: .round, lineJoin: .round))

                // Start / end markers
                if let start = coordinates.first {
                    Annotation("", coordinate: start) {
                        Circle().fill(.white).overlay(Circle().strokeBorder(Color.stravaOrange, lineWidth: 2)).frame(width: 12, height: 12)
                    }
                }
                if let end = coordinates.last {
                    Annotation("", coordinate: end) {
                        Circle().fill(Color.stravaOrange).overlay(Circle().strokeBorder(.white, lineWidth: 2)).frame(width: 12, height: 12)
                    }
                }
            }
            .mapStyle(.standard(elevation: .flat))
        }
        .aspectRatio(16/9, contentMode: .fit)
        .clipShape(RoundedRectangle(cornerRadius: 6))
    }
}
```

### Kudos Button (with spring + confetti)

```swift
struct KudosButton: View {
    @Binding var kudosCount: Int
    @State private var hasGivenKudos = false
    @State private var emitConfetti = false

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) {
                hasGivenKudos.toggle()
                kudosCount += hasGivenKudos ? 1 : -1
            }
            emitConfetti = hasGivenKudos
        } label: {
            HStack(spacing: 6) {
                Image(systemName: hasGivenKudos ? "hand.thumbsup.fill" : "hand.thumbsup")
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(hasGivenKudos ? Color.stravaOrange : Color.stravaInkSecondary)
                    .scaleEffect(hasGivenKudos ? 1.0 : 1.0)
                Text("\(kudosCount)")
                    .font(.stravaKudosCount)
                    .foregroundStyle(Color.stravaInkPrimary)
            }
            .padding(.horizontal, 4)
            .padding(.vertical, 8)
            .contentShape(Rectangle())
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: hasGivenKudos)
        .overlay {
            if emitConfetti { ConfettiBurst().allowsHitTesting(false) }
        }
    }
}

struct ConfettiBurst: View {
    @State private var rise = false
    var body: some View {
        ZStack {
            ForEach(0..<8) { i in
                Circle().fill(Color.stravaOrange)
                    .frame(width: 4, height: 4)
                    .offset(x: cos(Double(i) * .pi / 4) * (rise ? 30 : 0),
                            y: sin(Double(i) * .pi / 4) * (rise ? -30 : 0))
                    .opacity(rise ? 0 : 1)
            }
        }
        .onAppear {
            withAnimation(.easeOut(duration: 0.6)) { rise = true }
        }
    }
}
```

### Activity Feed Card (the hero component)

```swift
struct ActivityCard: View {
    let athleteAvatar: Image
    let athleteName: String
    let timestamp: String
    let activityTitle: String
    let routeCoords: [CLLocationCoordinate2D]
    let distance: String
    let elapsed: String
    let pace: String
    @State private var kudosCount: Int

    init(athleteAvatar: Image, athleteName: String, timestamp: String, activityTitle: String,
         routeCoords: [CLLocationCoordinate2D], distance: String, elapsed: String, pace: String,
         kudosCount: Int) {
        self.athleteAvatar = athleteAvatar
        self.athleteName = athleteName
        self.timestamp = timestamp
        self.activityTitle = activityTitle
        self.routeCoords = routeCoords
        self.distance = distance
        self.elapsed = elapsed
        self.pace = pace
        self._kudosCount = State(initialValue: kudosCount)
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            // Header
            HStack(spacing: 12) {
                athleteAvatar.resizable().scaledToFill()
                    .frame(width: 40, height: 40).clipShape(Circle())
                VStack(alignment: .leading, spacing: 2) {
                    Text(athleteName).font(.stravaAthlete).foregroundStyle(Color.stravaInkPrimary)
                    Text(timestamp).font(.stravaMeta).foregroundStyle(Color.stravaInkSecondary)
                }
                Spacer()
                Image(systemName: "figure.run").font(.system(size: 18)).foregroundStyle(Color.stravaOrange)
            }

            // Title
            Text(activityTitle).font(.stravaActivityTitle).foregroundStyle(Color.stravaInkPrimary)

            // Map snapshot
            RouteMapSnapshot(coordinates: routeCoords)

            // 3-up stat grid
            StatGrid(stats: [("Distance", distance), ("Time", elapsed), ("Pace", pace)])

            Divider().background(Color.stravaDivider)

            // Kudos + comments + view
            HStack(spacing: 16) {
                KudosButton(kudosCount: $kudosCount)
                HStack(spacing: 6) {
                    Image(systemName: "bubble.left").font(.system(size: 16)).foregroundStyle(Color.stravaInkSecondary)
                    Text("12").font(.stravaKudosCount).foregroundStyle(Color.stravaInkSecondary)
                }
                Spacer()
                Text("View Activity").font(.stravaButtonSm).foregroundStyle(Color.stravaOrange)
            }
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 16)
        .background(Color.stravaCanvas)
    }
}
```

### Record Button (the center tab)

```swift
struct RecordButton: View {
    var onRecord: () -> Void
    var body: some View {
        Button {
            onRecord()
        } label: {
            Image(systemName: "record.circle")
                .font(.system(size: 22, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.stravaOrange))
                .shadow(color: Color.stravaOrange.opacity(0.4), radius: 8, y: 8)
        }
        .sensoryFeedback(.impact(weight: .heavy), trigger: UUID())
        .offset(y: -8)  // protrudes 8pt above the tab bar
    }
}
```

### Segment Leaderboard Row

```swift
struct LeaderboardRow: View {
    let rank: Int
    let avatar: Image
    let name: String
    let date: String
    let time: String
    let gap: String

    var rankBg: Color {
        switch rank {
        case 1: return .stravaPRGold
        case 2: return .stravaSilver
        case 3: return .stravaBronze
        default: return .stravaSurfaceCool
        }
    }
    var rankFg: Color {
        rank <= 3 ? .white : .stravaInkPrimary
    }

    var body: some View {
        HStack(spacing: 12) {
            Text("\(rank)")
                .font(.stravaRank)
                .foregroundStyle(rankFg)
                .frame(width: 32, height: 32)
                .background(Circle().fill(rankBg))
            avatar.resizable().scaledToFill().frame(width: 36, height: 36).clipShape(Circle())
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.stravaAthlete).foregroundStyle(Color.stravaInkPrimary)
                Text(date).font(.stravaMeta).foregroundStyle(Color.stravaInkSecondary)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 2) {
                Text(time).font(.stravaStatValue).foregroundStyle(Color.stravaInkPrimary)
                Text(gap).font(.stravaMeta).foregroundStyle(Color.stravaInkSecondary)
            }
        }
        .padding(.horizontal, 16).frame(height: 56)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.stravaDivider).frame(height: 0.5)
        }
    }
}
```

### Achievement Badge

```swift
struct AchievementBadge: View {
    let icon: String     // e.g. "trophy.fill" / "figure.run.circle.fill"
    let text: String     // e.g. "1 PR"
    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: icon).font(.system(size: 14)).foregroundStyle(Color.stravaOrange)
            Text(text).font(.stravaKudosCount).foregroundStyle(Color.stravaInkPrimary)
        }
        .padding(.vertical, 4).padding(.horizontal, 10)
        .background(Capsule().fill(Color.stravaOrange.opacity(0.08)))
    }
}
```

## 4. Tab Bar (with Center Record Button)

```swift
struct RootTabView: View {
    @State private var tab = 0
    var body: some View {
        ZStack(alignment: .bottom) {
            TabView(selection: $tab) {
                FeedView()    .tabItem { Label("Home", systemImage: "house") }.tag(0)
                MapsView()    .tabItem { Label("Maps", systemImage: "map") }.tag(1)
                Color.clear   .tabItem { Label("",     systemImage: "") }.tag(2) // placeholder for Record
                GroupsView()  .tabItem { Label("Groups", systemImage: "person.3") }.tag(3)
                ProfileView() .tabItem { Label("You",  systemImage: "person.crop.circle") }.tag(4)
            }
            .tint(Color.stravaOrange)
            .sensoryFeedback(.selection, trigger: tab)

            RecordButton(onRecord: { /* present record sheet */ })
        }
    }
}
```

## 5. Motion & Haptics

```swift
// Kudos tap
.sensoryFeedback(.impact(weight: .medium), trigger: hasGivenKudos)
withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { hasGivenKudos.toggle() }

// Record button tap
.sensoryFeedback(.impact(weight: .heavy), trigger: didRecord)

// Tab switch
.sensoryFeedback(.selection, trigger: tab)

// Card tap → activity detail
.sensoryFeedback(.selection, trigger: cardTapped)

// PR celebration (on first view of an activity with a PR)
.sensoryFeedback(.success, trigger: prSeen)
withAnimation(.spring(response: 0.4, dampingFraction: 0.7).delay(0.2)) { trophyScale = 1.1 }
withAnimation(.spring(response: 0.4, dampingFraction: 0.7).delay(0.4)) { trophyScale = 1.0 }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Kudos (un-given) | `hand.thumbsup` | 18pt |
| Kudos (given) | `hand.thumbsup.fill` | 18pt |
| Comment | `bubble.left` | 16pt |
| Activity type — Run | `figure.run` | 18pt |
| Activity type — Ride | `figure.outdoor.cycle` | 18pt |
| Activity type — Swim | `figure.pool.swim` | 18pt |
| Activity type — Hike | `figure.hiking` | 18pt |
| Activity type — Walk | `figure.walk` | 18pt |
| Record (center tab) | `record.circle` | 22pt |
| Trophy / PR | `trophy.fill` | 14pt |
| Crown / KOM | `crown.fill` | 14pt |
| Maps tab | `map` | 24pt |
| Groups tab | `person.3` | 24pt |
| Profile tab | `person.crop.circle` | 24pt |
| Search | `magnifyingglass` | 18pt |
| Filter | `slider.horizontal.3` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Heart rate chart | `heart.fill` | 12pt |
| Elevation | `mountain.2.fill` | 14pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `MapPolyline`); iOS 16 fallback uses `UIImpactFeedbackGenerator` and `MKPolylineRenderer`
- Dynamic Type: scale activity titles, body, and metadata; FIX the hero stats (44pt Black), stat labels, stat values, tab labels, and rank chips
- VoiceOver: group activity card as a single element with label `"Casey Reardon's run, Tuesday Morning Run, 8.2 miles in 1 hour 14 minutes 23 seconds, 9:03 per mile pace, 27 kudos, 12 comments, 2 hours ago"`
- Kudos button: separate element `"Give kudos, 27, button"` / `"Kudos given, 28, button"`
- Contrast: Strava Orange `#FC4C02` on white passes WCAG AA at 17pt+ Semibold; do not use it for small body text
- Reduce Motion: skip the kudos confetti and scale bounce — fall back to instant color change
- Tabular numerals: `.monospacedDigit()` everywhere there's a stat — pace columns, splits, times
- Dark mode: switch to coal canvas `#0F0F0F` and dark map style; Strava Orange remains identical and glows
- MapKit polyline halo: render two polylines on the same `Map` view — the halo first (line width 6, color `stravaOrangeHalo`), then the main stroke (line width 4, color `stravaOrange`). Z-order is implicit (later annotations render on top)
