# Pluto TV (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pluto TV's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the EPG grid, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only)
    static let plutoCanvas   = Color(red: 0.043, green: 0.059, blue: 0.122) // #0B0F1F
    static let plutoSurface1 = Color(red: 0.078, green: 0.102, blue: 0.180) // #141A2E
    static let plutoSurface2 = Color(red: 0.110, green: 0.141, blue: 0.251) // #1C2440
    static let plutoSurface3 = Color(red: 0.145, green: 0.188, blue: 0.310) // #25304F
    static let plutoDivider  = Color(red: 0.165, green: 0.208, blue: 0.337) // #2A3556

    // MARK: - Brand
    static let plutoBlue        = Color(red: 0.000, green: 0.282, blue: 1.000) // #0048FF
    static let plutoBluePressed = Color(red: 0.000, green: 0.224, blue: 0.800) // #0039CC
    static let plutoBlueBright  = Color(red: 0.173, green: 0.420, blue: 1.000) // #2C6BFF
    static let plutoYellow      = Color(red: 1.000, green: 0.882, blue: 0.000) // #FFE100
    static let plutoCyan        = Color(red: 0.000, green: 0.761, blue: 1.000) // #00C2FF

    // MARK: - Text
    static let plutoTextPrimary   = Color.white                                  // #FFFFFF
    static let plutoTextSecondary = Color(red: 0.616, green: 0.659, blue: 0.780) // #9DA8C7
    static let plutoTextTertiary  = Color(red: 0.420, green: 0.459, blue: 0.584) // #6B7595
    static let plutoOnYellow      = Color(red: 0.043, green: 0.059, blue: 0.122) // #0B0F1F

    // MARK: - Semantic
    static let plutoLive    = Color(red: 1.000, green: 0.231, blue: 0.361) // #FF3B5C
    static let plutoSuccess = Color(red: 0.129, green: 0.816, blue: 0.478) // #21D07A
    static let plutoError   = Color(red: 1.000, green: 0.290, blue: 0.431) // #FF4A6E
}
```

## 2. Typography

Pluto's brand sans is a semi-rounded humanist grotesque; **Manrope** is the closest free analog (SIL OFL). Bundle Manrope via `Info.plist` / `UIAppFonts`. Use tabular figures for channel numbers and times.

```swift
extension Font {
    static let plutoDisplay    = Font.custom("Manrope-ExtraBold", size: 32) // 800
    static let plutoScreenTitle = Font.custom("Manrope-ExtraBold", size: 24) // 800
    static let plutoSection    = Font.custom("Manrope-Bold",      size: 20) // 700
    static let plutoNowPlaying = Font.custom("Manrope-Bold",      size: 17) // 700
    static let plutoBody       = Font.custom("Manrope-Regular",   size: 15) // 400
    static let plutoChannel    = Font.custom("Manrope-Bold",      size: 13) // 700
    static let plutoProgram    = Font.custom("Manrope-SemiBold",  size: 13) // 600
    static let plutoMeta       = Font.custom("Manrope-Medium",    size: 12) // 500
    static let plutoChNumber   = Font.custom("Manrope-ExtraBold", size: 10) // 800 tabular
    static let plutoTimeLabel  = Font.custom("Manrope-Bold",      size: 10) // 700 tabular
    static let plutoBadge      = Font.custom("Manrope-ExtraBold", size: 10) // 800 caps
    static let plutoTab        = Font.custom("Manrope-SemiBold",  size: 10) // 600
}

// Tabular numerals for grid alignment
extension View {
    func plutoTabularDigits() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### EPG Grid (the centerpiece)

```swift
struct EPGProgram: Identifiable {
    let id = UUID()
    let title: String
    let timeLabel: String     // "9:00 – 10:00 PM"
    let widthSlots: CGFloat   // duration in 30-min units
    let isOnNow: Bool
}

struct EPGChannel: Identifiable {
    let id = UUID()
    let number: String        // "612"
    let abbrev: String        // "ACTION"
    let logoGradient: [Color] // per-channel brand gradient
    let programs: [EPGProgram]
}

let slotWidth: CGFloat = 96   // pixel width of a 30-minute slot
let channelColW: CGFloat = 86
let rowH: CGFloat = 64

struct EPGGuide: View {
    let timeLabels: [String]  // ["9:30 PM", "10:00 PM", ...]
    let channels: [EPGChannel]

    var body: some View {
        ScrollView(.vertical, showsIndicators: false) {
            VStack(spacing: 0) {
                // Sticky time-bar
                HStack(spacing: 0) {
                    Color.clear.frame(width: channelColW)
                    ScrollView(.horizontal, showsIndicators: false) {
                        HStack(spacing: 0) {
                            ForEach(timeLabels, id: \.self) { t in
                                Text(t)
                                    .font(.plutoTimeLabel).monospacedDigit()
                                    .foregroundStyle(Color.plutoTextTertiary)
                                    .frame(width: slotWidth, alignment: .leading)
                                    .padding(.vertical, 6).padding(.leading, 8)
                            }
                        }
                    }
                }
                .background(Color.plutoCanvas)
                .overlay(alignment: .bottom) { Rectangle().fill(Color.plutoDivider).frame(height: 0.5) }

                ForEach(channels) { ch in EPGRowView(channel: ch) }
            }
        }
        .background(Color.plutoCanvas)
    }
}

struct EPGRowView: View {
    let channel: EPGChannel

    var body: some View {
        HStack(spacing: 0) {
            // Frozen channel column
            VStack(spacing: 3) {
                Text(channel.number)
                    .font(.plutoChNumber).monospacedDigit()
                    .foregroundStyle(Color.plutoTextTertiary)
                Text(channel.abbrev)
                    .font(.system(size: 9, weight: .heavy))
                    .foregroundStyle(.white)
                    .frame(width: 40, height: 28)
                    .background(
                        LinearGradient(colors: channel.logoGradient,
                                       startPoint: .topLeading, endPoint: .bottomTrailing)
                    )
                    .clipShape(RoundedRectangle(cornerRadius: 5))
            }
            .frame(width: channelColW, height: rowH)
            .background(Color.plutoSurface1)
            .overlay(alignment: .trailing) { Rectangle().fill(Color.plutoDivider).frame(width: 0.5) }

            // Scrolling program timeline
            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 0) {
                    ForEach(channel.programs) { p in
                        VStack(alignment: .leading, spacing: 3) {
                            Text(p.title)
                                .font(.plutoProgram)
                                .foregroundStyle(p.isOnNow ? Color.plutoTextPrimary : Color.white.opacity(0.85))
                                .lineLimit(1)
                            Text(p.timeLabel)
                                .font(.plutoMeta).monospacedDigit()
                                .foregroundStyle(Color.plutoTextTertiary)
                        }
                        .padding(.horizontal, 10).padding(.vertical, 8)
                        .frame(width: slotWidth * p.widthSlots, height: rowH, alignment: .leading)
                        .background(p.isOnNow ? Color.plutoSurface2 : Color.plutoSurface1)
                        .overlay(alignment: .leading) {
                            if p.isOnNow { Rectangle().fill(Color.plutoYellow).frame(width: 3) }
                        }
                        .overlay(alignment: .trailing) { Rectangle().fill(Color.plutoCanvas).frame(width: 1) }
                    }
                }
            }
        }
        .frame(height: rowH)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.plutoDivider).frame(height: 0.5) }
    }
}
```

### Mini-Player (pinned top)

```swift
struct MiniPlayer: View {
    let videoFrame: AnyView   // your AVPlayer layer
    let channelLine: String   // "CH 612 · PLUTO TV ACTION"
    let programTitle: String
    let elapsed: Double       // 0...1

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            videoFrame
                .frame(height: 150)
                .clipped()
                .overlay(
                    LinearGradient(colors: [.clear, .black.opacity(0.55)],
                                   startPoint: .center, endPoint: .bottom)
                )

            VStack(alignment: .leading, spacing: 0) {
                HStack(spacing: 6) {
                    Circle().fill(Color.plutoLive).frame(width: 7, height: 7)
                    Text("LIVE").font(.plutoBadge).foregroundStyle(.white)
                }
                .padding(.horizontal, 9).padding(.vertical, 4)
                .background(Color.black.opacity(0.4))
                .clipShape(RoundedRectangle(cornerRadius: 5))
                .frame(maxWidth: .infinity, alignment: .leading)
                .padding(.bottom, 90)

                Text(channelLine)
                    .font(.system(size: 11, weight: .bold))
                    .foregroundStyle(Color.plutoCyan)
                Text(programTitle)
                    .font(.system(size: 18, weight: .heavy))
                    .foregroundStyle(.white)
                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Capsule().fill(Color.white.opacity(0.2))
                        Capsule().fill(Color.plutoYellow).frame(width: geo.size.width * elapsed)
                    }
                }
                .frame(height: 3)
                .padding(.top, 8)
            }
            .padding(14)
        }
        .frame(height: 150)
    }
}
```

### Category Pill Strip

```swift
struct CategoryPills: View {
    let categories: [String]
    @Binding var selected: String

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(categories, id: \.self) { c in
                    Text(c)
                        .font(.system(size: 11, weight: .bold))
                        .foregroundStyle(c == selected ? .white : Color.plutoTextSecondary)
                        .padding(.horizontal, 12).padding(.vertical, 6)
                        .background(Capsule().fill(c == selected ? Color.plutoBlue : Color.plutoSurface2))
                        .onTapGesture { withAnimation(.easeOut(duration: 0.15)) { selected = c } }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

### Now-Playing Bar

```swift
struct NowPlayingBar: View {
    let title: String
    let channelLine: String
    let gradient: [Color]

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 6)
                .fill(LinearGradient(colors: gradient, startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 56, height: 38)
            VStack(alignment: .leading, spacing: 2) {
                Text(title).font(.system(size: 13, weight: .bold)).foregroundStyle(.white)
                Text(channelLine).font(.system(size: 11)).foregroundStyle(Color.plutoTextSecondary)
            }
            Spacer()
            HStack(spacing: 5) {
                Circle().fill(Color.plutoLive).frame(width: 7, height: 7)
                Text("LIVE").font(.plutoBadge).foregroundStyle(Color.plutoLive)
            }
        }
        .padding(.horizontal, 14).padding(.vertical, 12)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.plutoSurface2))
    }
}
```

### Buttons

```swift
struct PlutoPrimaryButton: View {
    let title: String; let action: () -> Void
    var body: some View {
        Button(action: action) {
            Label(title, systemImage: "play.fill")
                .font(.system(size: 15, weight: .heavy))
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.plutoBlue))
        }
        .buttonStyle(.plain)
    }
}

struct PlutoYellowButton: View {  // high-emphasis "live/now" CTA only
    let title: String; let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.system(size: 15, weight: .heavy))
                .foregroundStyle(Color.plutoOnYellow)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.plutoYellow))
        }
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PlutoTabView: View {
    var body: some View {
        TabView {
            LiveTVView().tabItem  { Label("Live TV",   systemImage: "tv") }
            OnDemandView().tabItem { Label("On Demand", systemImage: "play.rectangle") }
            SearchView().tabItem  { Label("Search",    systemImage: "magnifyingglass") }
            MyPlutoView().tabItem { Label("My Pluto",  systemImage: "bookmark.fill") }
            AccountView().tabItem { Label("Account",   systemImage: "person.crop.circle") }
        }
        .tint(.plutoYellow) // active = yellow, no pill
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.plutoCanvas.opacity(0.96))
            a.shadowColor = UIColor(Color.plutoDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Channel surf: cross-fade mini-player video on tune-in (~300ms)
withAnimation(.easeInOut(duration: 0.3)) { currentChannelID = tappedID }

// Time-bar / timeline scroll: bind both horizontal ScrollViews to the same offset
@State private var timelineOffset: CGFloat = 0   // drive time-bar + each row in sync

// Category filter swap
withAnimation(.easeOut(duration: 0.2)) { visibleChannels = filtered }
withAnimation(.easeOut(duration: 0.15)) { selectedPill = c }

// Mini-player elapsed bar — real-time; reset+animate on channel change
withAnimation(.easeOut(duration: 0.3)) { elapsed = currentElapsed }

// Set Reminder confirm
withAnimation(.spring(response: 0.25, dampingFraction: 0.6)) { reminderSet.toggle() }
.sensoryFeedback(.impact(flexibility: .soft), trigger: reminderSet)

// Optional On Now border breathing (subtle)
.opacity(pulse ? 1.0 : 0.85)
.animation(.easeInOut(duration: 2).repeatForever(autoreverses: true), value: pulse)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Live TV (tab) | `tv` / `tv.fill` | 22pt |
| On Demand (tab) | `play.rectangle` / `.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| My Pluto (tab) | `bookmark` / `bookmark.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Play (CTA) | `play.fill` | 14pt |
| Live dot | `circle.fill` | 7pt |
| Add to watchlist | `plus` / `checkmark` | 18pt |
| Set reminder | `bell` / `bell.fill` | 18pt |
| Share | `square.and.arrow.up` | 22pt |
| Back | `chevron.left` | 24pt |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | 18pt |
| Cast | `airplayvideo` | 22pt |
| Full schedule | `calendar` | 16pt |
| Mute / volume | `speaker.slash.fill` / `speaker.wave.2.fill` | 18pt |

## 7. Dark Mode

Pluto TV is **dark-only** — there is no light scheme. Lock to dark; never derive a light palette.

```swift
struct PlutoTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.plutoCanvas)
            .foregroundStyle(Color.plutoTextPrimary)
            .preferredColorScheme(.dark)   // force dark
    }
}

extension View { func plutoTheme() -> some View { modifier(PlutoTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Manrope TTFs (Regular/Medium/SemiBold/Bold/ExtraBold) via `Info.plist` — SIL OFL, free to ship
- **Tabular figures are mandatory** on channel numbers and EPG times (`.monospacedDigit()`) — otherwise the frozen channel column and the time-bar misalign with the rows
- Dynamic Type: scale screen titles, body, synopsis, now-playing title; keep EPG cell text, channel numbers, time-bar labels, LIVE/ON-NOW badges, tab labels FIXED — the grid is layout-locked and scaling them breaks column alignment
- VoiceOver: EPG cell labeled "{channel} {number}, {program}, {time}" and append ", on now, live" for the On Now cell so the yellow-border state is announced (it's the most important state — never `.accessibilityHidden`); mini-player labeled "Live: {program} on {channel}"
- The "On Now" yellow border is not purely decorative — it conveys "this is live"; always reflect it in the accessibility label and pair it with the "LIVE" text/dot so it isn't color-only
- Color contrast: white on `#0B0F1F`/`#141A2E`/`#1C2440` passes WCAG AA; `#9DA8C7` on canvas passes AA at 12pt+; `#0B0F1F` on `#FFE100` is high-contrast for the yellow CTA
- Reduce Motion: disable the optional On Now border pulse and the mini-player elapsed-bar animation (jump to value); category swap becomes an instant cut
- Reduce Transparency: replace the tab bar's blur with solid `#0B0F1F`
- Keyboard / focus (iPad + remote): the EPG should be focus-navigable cell-by-cell; focused cell gets a `#2C6BFF` ring in addition to any color so focus is not color-only
