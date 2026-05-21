# ESPN (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates ESPN's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. ESPN's scoreboard identity is **dark-first** on a near-black canvas, with **tabular numerals on every score**.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-first, near-black — NOT pure black)
    static let espnCanvas   = Color(red: 0.055, green: 0.059, blue: 0.067) // #0E0F11
    static let espnSurface1 = Color(red: 0.094, green: 0.098, blue: 0.106) // #18191B
    static let espnSurface2 = Color(red: 0.129, green: 0.137, blue: 0.153) // #212327
    static let espnSurface3 = Color(red: 0.169, green: 0.176, blue: 0.192) // #2B2D31
    static let espnDivider  = Color(red: 0.165, green: 0.173, blue: 0.188) // #2A2C30

    // MARK: - Text
    static let espnTextPrimary   = Color.white                              // #FFFFFF
    static let espnTextSecondary = Color(red: 0.604, green: 0.627, blue: 0.651) // #9AA0A6
    static let espnTextTertiary  = Color(red: 0.420, green: 0.439, blue: 0.459) // #6B7075

    // MARK: - Brand & Status
    static let espnRed       = Color(red: 0.835, green: 0.039, blue: 0.039) // #D50A0A
    static let espnRedBright = Color(red: 0.800, green: 0.000, blue: 0.000) // #CC0000
    static let espnRedPress  = Color(red: 0.647, green: 0.031, blue: 0.031) // #A50808
    static let espnLive      = Color(red: 1.000, green: 0.102, blue: 0.102) // #FF1A1A
    static let espnWin       = Color(red: 0.122, green: 0.667, blue: 0.349) // #1FAA59
    static let espnRankGold  = Color(red: 0.949, green: 0.761, blue: 0.000) // #F2C200
    static let espnError     = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}
```

## 2. Typography

ESPN's brand face is a proprietary condensed sans; the closest free analog is **Archivo**. Bundle Archivo TTFs via `Info.plist` / `UIAppFonts`. Scores/clocks **must** be tabular — use `.monospacedDigit()` (works with Archivo and the SF Pro fallback).

```swift
extension Font {
    static func espnArchivo(_ size: CGFloat, _ weight: Weight = .regular) -> Font {
        let face: String
        switch weight {
        case .black, .heavy: face = "Archivo-Black"
        case .bold:          face = "Archivo-Bold"
        case .semibold:      face = "Archivo-SemiBold"
        case .medium:        face = "Archivo-Medium"
        default:             face = "Archivo-Regular"
        }
        return .custom(face, size: size)
    }

    static let espnScreenTitle   = Font.custom("Archivo-Black",    size: 32)
    static let espnHeadline      = Font.custom("Archivo-Black",    size: 26)
    static let espnSectionHeader = Font.custom("Archivo-Bold",     size: 22)  // ~weight 800
    static let espnTeamName      = Font.custom("Archivo-Bold",     size: 18)
    static let espnBody          = Font.custom("Archivo-Regular",  size: 16)
    static let espnStoryTitle    = Font.custom("Archivo-Bold",     size: 15)
    static let espnMeta          = Font.custom("Archivo-Regular",  size: 14)
    static let espnRecord        = Font.custom("Archivo-Regular",  size: 12)
    static let espnButtonLabel   = Font.custom("Archivo-Bold",     size: 16)
    static let espnStatusBadge   = Font.custom("Archivo-Black",    size: 11)
    static let espnCategoryTag   = Font.custom("Archivo-Black",    size: 10)
    static let espnTab           = Font.custom("Archivo-Bold",     size: 10)

    // Scores — always tabular. Apply `.monospacedDigit()` at the call site.
    static let espnScoreLarge    = Font.custom("Archivo-Black",    size: 26)
    static let espnScoreTicker   = Font.custom("Archivo-Bold",     size: 13)
}
```

## 3. Signature Components

### Game Card (the core atom)

```swift
struct GameCard: View {
    struct Team { let logo: Color; let name: String; let record: String; let score: Int; let winning: Bool }
    let league: String          // "NBA · WESTERN FINALS · GAME 5"
    let isLive: Bool
    let isFinal: Bool
    let clock: String           // "Q3 · 4:12"
    let home: Team
    let away: Team

    var body: some View {
        VStack(spacing: 0) {
            // Header
            HStack {
                Text(league)
                    .font(.espnStatusBadge)
                    .tracking(0.5)
                    .foregroundStyle(Color.espnTextSecondary)
                Spacer()
                StatusBadge(isLive: isLive, isFinal: isFinal, label: isLive ? "LIVE" : (isFinal ? "FINAL" : clock))
            }
            .padding(.horizontal, 14).padding(.vertical, 10)
            .background(Color.espnSurface2)

            // Team rows
            VStack(spacing: 0) {
                teamRow(home)
                Rectangle().fill(Color.espnDivider).frame(height: 1)
                teamRow(away)
            }
            .padding(14)

            // Footer
            HStack {
                Text(clock).font(.espnArchivo(13, .bold)).foregroundStyle(Color.espnTextPrimary)
                Spacer()
                HStack(spacing: 6) {
                    Image(systemName: "play.fill").font(.system(size: 12))
                    Text("Watch on ESPN+").font(.espnArchivo(12, .bold))
                }
                .foregroundStyle(Color.espnRedBright)
            }
            .padding(.horizontal, 14).padding(.vertical, 10)
            .overlay(alignment: .top) { Rectangle().fill(Color.espnDivider).frame(height: 1) }
        }
        .background(Color.espnSurface1)
        .clipShape(RoundedRectangle(cornerRadius: 12))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.espnDivider, lineWidth: 1))
    }

    private func teamRow(_ t: GameCard.Team) -> some View {
        HStack(spacing: 12) {
            Circle().fill(t.logo).frame(width: 30, height: 30)
            VStack(alignment: .leading, spacing: 1) {
                Text(t.name).font(.espnTeamName).foregroundStyle(Color.espnTextPrimary)
                Text(t.record).font(.espnRecord).foregroundStyle(Color.espnTextSecondary)
            }
            Spacer()
            Text("\(t.score)")
                .font(.espnScoreLarge)
                .monospacedDigit()                 // tabular — non-negotiable
                .foregroundStyle(
                    isFinal ? (t.winning ? Color.espnWin : Color.espnTextSecondary)
                            : (t.winning ? Color.espnTextPrimary : Color.espnTextSecondary)
                )
        }
        .padding(.vertical, 8)
    }
}
```

### Scores Ticker

```swift
struct ScoresTicker: View {
    struct Chip: Identifiable {
        let id = UUID()
        let league: String
        let status: String        // "LIVE Q3" / "FINAL" / "7:05 ET"
        let isLive: Bool
        let isFinal: Bool
        let a: (abbr: String, logo: Color, score: String, win: Bool)
        let b: (abbr: String, logo: Color, score: String, win: Bool)
    }
    let chips: [Chip]

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(chips) { c in
                    VStack(alignment: .leading, spacing: 6) {
                        HStack {
                            Text(c.league).font(.espnArchivo(10, .bold)).foregroundStyle(Color.espnTextSecondary)
                            Spacer()
                            StatusBadge(isLive: c.isLive, isFinal: c.isFinal, label: c.status, compact: true)
                        }
                        tickerLine(c.a)
                        tickerLine(c.b)
                    }
                    .padding(10)
                    .frame(width: 116, alignment: .leading)
                    .background(Color.espnSurface2)
                    .clipShape(RoundedRectangle(cornerRadius: 8))
                }
            }
            .padding(.horizontal, 12)
        }
        .padding(.bottom, 12)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.espnDivider).frame(height: 1) }
    }

    private func tickerLine(_ t: (abbr: String, logo: Color, score: String, win: Bool)) -> some View {
        HStack {
            HStack(spacing: 6) {
                Circle().fill(t.logo).frame(width: 16, height: 16)
                Text(t.abbr).font(.espnArchivo(13, .bold))
            }
            Spacer()
            Text(t.score).font(.espnScoreTicker).monospacedDigit()
        }
        .foregroundStyle(t.win ? Color.espnTextPrimary : Color.espnTextSecondary)
    }
}
```

### Live Status Badge (with pulsing dot)

```swift
struct StatusBadge: View {
    let isLive: Bool
    let isFinal: Bool
    let label: String
    var compact: Bool = false
    @State private var pulse = false

    var body: some View {
        HStack(spacing: 5) {
            if isLive {
                Circle()
                    .fill(.white)
                    .frame(width: 6, height: 6)
                    .opacity(pulse ? 0.4 : 1)
                    .onAppear {
                        withAnimation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true)) { pulse = true }
                    }
            }
            Text(label.uppercased())
                .font(compact ? .espnArchivo(9, .black) : .espnStatusBadge)
                .tracking(0.5)
        }
        .padding(.horizontal, compact ? 5 : 9)
        .padding(.vertical, compact ? 2 : 4)
        .background(isLive ? Color.espnLive : (isFinal ? Color.espnSurface3 : Color.clear))
        .foregroundStyle(isFinal ? Color.espnTextSecondary : Color.white)
        .overlay(
            RoundedRectangle(cornerRadius: 3)
                .strokeBorder(Color.white.opacity(isLive || isFinal ? 0 : 0.4), lineWidth: 1)
        )
        .clipShape(RoundedRectangle(cornerRadius: 3))
    }
}
```

### Primary CTA ("Watch Live")

```swift
struct WatchLiveButton: View {
    let action: () -> Void
    @GestureState private var pressed = false

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 16))
                Text("Watch Live").font(.espnButtonLabel).tracking(0.3)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .padding(.vertical, 14)
            .background(pressed ? Color.espnRedPress : Color.espnRed)
            .clipShape(RoundedRectangle(cornerRadius: 6))
            .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0).updating($pressed) { _, s, _ in s = true }
        )
    }
}
```

### SportsCenter Story Row

```swift
struct StoryRow: View {
    let thumb: Color
    let category: String        // "BREAKING"
    let title: String
    let meta: String            // "NBA · 14m ago · 3 min read"

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            RoundedRectangle(cornerRadius: 8).fill(thumb).frame(width: 96, height: 72)
            VStack(alignment: .leading, spacing: 4) {
                Text(category.uppercased())
                    .font(.espnCategoryTag).tracking(0.5)
                    .foregroundStyle(Color.espnRedBright)
                Text(title)
                    .font(.espnStoryTitle).foregroundStyle(Color.espnTextPrimary)
                    .lineLimit(2)
                Text(meta)
                    .font(.espnArchivo(12, .regular)).foregroundStyle(Color.espnTextSecondary)
                    .padding(.top, 2)
            }
            Spacer(minLength: 0)
        }
        .padding(.vertical, 14)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.espnDivider).frame(height: 1) }
    }
}
```

## 4. Bottom Tab Bar

ESPN marks the active tab with a small red dot under a white icon — not a tint pill. A custom bar makes the dot explicit.

```swift
struct ESPNTabBar: View {
    @Binding var selection: Int
    private let tabs: [(String, String)] = [
        ("Home", "house.fill"), ("Scores", "sportscourt.fill"),
        ("Watch", "play.rectangle.fill"), ("ESPN BET", "chart.bar.fill"), ("More", "ellipsis"),
    ]

    var body: some View {
        HStack(spacing: 0) {
            ForEach(tabs.indices, id: \.self) { i in
                VStack(spacing: 3) {
                    Image(systemName: tabs[i].1).font(.system(size: 22))
                    Text(tabs[i].0).font(.espnTab)
                    Circle()
                        .fill(Color.espnRed)
                        .frame(width: 4, height: 4)
                        .opacity(i == selection ? 1 : 0)
                }
                .foregroundStyle(i == selection ? Color.espnTextPrimary : Color.espnTextTertiary)
                .frame(maxWidth: .infinity)
                .contentShape(Rectangle())
                .onTapGesture { selection = i }
            }
        }
        .padding(.top, 8).padding(.bottom, 14)
        .background(.ultraThinMaterial)
        .overlay(alignment: .top) { Rectangle().fill(Color.espnDivider).frame(height: 0.5) }
        .preferredColorScheme(.dark)
    }
}
```

## 5. Motion

```swift
// Live pulse — the signature "happening now" cue
withAnimation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true)) { pulse = true }
// dot .opacity(pulse ? 0.4 : 1)

// Score update — cross-fade + 1.06 pop
withAnimation(.easeOut(duration: 0.2)) { score = newScore }   // + .scaleEffect on change

// Game ends — LIVE → FINAL, winner score → green
withAnimation(.easeInOut(duration: 0.25)) { isFinal = true }
withAnimation(.easeOut(duration: 0.30)) { winnerColor = .espnWin }

// Tabbed underline — red bar slide
.matchedGeometryEffect(id: "underline", in: ns)               // tween ~200ms ease-out

// Card press
.scaleEffect(pressed ? 0.98 : 1).animation(.easeOut(duration: 0.15), value: pressed)

// Haptics
let soft = UIImpactFeedbackGenerator(style: .light)   // follow toggle, tab change
let med  = UIImpactFeedbackGenerator(style: .medium)  // tracked team's score change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Scores (tab) | `sportscourt` / `.fill` | 22pt |
| Watch (tab) | `play.rectangle` / `.fill` | 22pt |
| ESPN BET (tab) | `chart.bar` / `.fill` | 22pt |
| More (tab) | `ellipsis` | 22pt |
| Watch / play (CTA) | `play.fill` | 16pt |
| Follow team | `plus` / `checkmark` (following) | 18pt |
| Alerts / bell | `bell` / `bell.fill` | 22pt |
| Search | `magnifyingglass` | 22pt |
| Share | `square.and.arrow.up` | 20pt |
| Back | `chevron.left` | 20pt |
| Live dot | (filled `Circle`, not a symbol) | 6–7pt |
| Box score | `tablecells` | 18pt |
| Stats / chart | `chart.xyaxis.line` | 18pt |
| Cast | `airplayvideo` | 20pt |

## 7. Dark Mode

ESPN's scoreboard identity (near-black ground, red accent, green-winner, live pulse) is defined in **dark**. Lock the scoreboard surfaces to the near-black palette.

```swift
struct ESPNTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.espnCanvas)
            .foregroundStyle(Color.espnTextPrimary)
            .preferredColorScheme(.dark)         // scoreboard identity is dark-first
            .tint(.espnRed)
    }
}

extension View {
    func espnTheme() -> some View { modifier(ESPNTheme()) }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`matchedGeometryEffect`, `.ultraThinMaterial`, `.monospacedDigit()` available; `.sensoryFeedback` needs iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Bundle Archivo (Regular / Medium / SemiBold / Bold / Black) TTFs via `Info.plist` `UIAppFonts` — Archivo is SIL OFL, free to redistribute; without it, use SF Pro `.black` + `.monospacedDigit()`
- **Tabular numerals are mandatory on scores/clocks/records** — always apply `.monospacedDigit()`; proportional digits shift columns and break the scoreboard (this is a correctness, not a style, requirement)
- Lock the scoreboard to `preferredColorScheme(.dark)` on the near-black canvas — pure black breaks card contrast
- Dynamic Type: scale headlines, section headers, body, story titles, meta; keep ticker scores/chips, status badges, tab labels, the pulse dot, and the game-card score size FIXED (the card is a fixed-rhythm scoreboard)
- VoiceOver: label the game card "{away} {awayScore}, {home} {homeScore}, {status}"; the ticker chip the same compactly; the LIVE badge as "Live, {period}"; expose the winner via the label ("final, {winner} wins") so the green isn't color-only
- Color-only meaning: the winning-score green and the live red must be backed by text ("FINAL", "LIVE {period}") so state is not conveyed by color alone
- Contrast: `#FFFFFF` on `#0E0F11` is maximal; `#9AA0A6` meta passes AA at 14pt; the LIVE pill uses white on `#FF1A1A` (passes AA); win green `#1FAA59` on near-black passes AA for the large score
- Reduce Motion: disable the live pulse (show a static red dot), the score pop, and the win-color animation (snap to green); keep the tabbed underline as a crossfade
- Respect the safe area / Dynamic Island: the top bar + pinned ticker sit below the island; the persistent watch mini-bar (if playing) sits above the tab bar
