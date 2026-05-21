# Peloton (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Peloton's studio-black visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, `ViewModifier`s, and the signature class-browse + in-class components.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — the only mode)
    static let plCanvas   = Color.black                                       // #000000
    static let plSurface1 = Color(red: 0.071, green: 0.071, blue: 0.071)      // #121212
    static let plSurface2 = Color(red: 0.110, green: 0.110, blue: 0.118)      // #1C1C1E
    static let plSurface3 = Color(red: 0.149, green: 0.149, blue: 0.161)      // #262629
    static let plDivider  = Color(red: 0.173, green: 0.173, blue: 0.180)      // #2C2C2E

    // MARK: - Text
    static let plTextPrimary   = Color.white                                  // #FFFFFF
    static let plTextSecondary = Color(red: 0.710, green: 0.710, blue: 0.729) // #B5B5BA
    static let plTextTertiary  = Color(red: 0.478, green: 0.478, blue: 0.502) // #7A7A80

    // MARK: - Brand (single accent)
    static let plRed        = Color(red: 0.875, green: 0.118, blue: 0.180)    // #DF1E2E
    static let plRedOnDark  = Color(red: 1.0,   green: 0.294, blue: 0.341)    // #FF4B57
    static let plRedPressed = Color(red: 0.722, green: 0.094, blue: 0.145)    // #B81825

    // MARK: - Functional / In-Class Metrics (fixed meaning, theme-invariant)
    static let plCadence    = Color(red: 0.898, green: 0.251, blue: 0.165)    // #E5402A
    static let plResistance = Color(red: 0.941, green: 0.627, blue: 0.188)    // #F0A030
    static let plOutput     = Color(red: 0.875, green: 0.118, blue: 0.180)    // #DF1E2E
    static let plHeartRate  = Color(red: 1.0,   green: 0.294, blue: 0.341)    // #FF4B57
    static let plStrive     = Color(red: 0.239, green: 0.722, blue: 0.878)    // #3DB8E0
    static let plPR         = Color(red: 0.180, green: 0.800, blue: 0.443)    // #2ECC71

    // MARK: - Semantic
    static let plSuccess = Color(red: 0.180, green: 0.800, blue: 0.443)       // #2ECC71
    static let plWarning = Color(red: 0.941, green: 0.627, blue: 0.188)       // #F0A030
    static let plError   = Color(red: 0.875, green: 0.118, blue: 0.180)       // #DF1E2E
    static let plTrack   = Color(red: 0.149, green: 0.149, blue: 0.161)       // #262629
}
```

## 2. Typography

Peloton sets **Inter** heavy and tight. Bundle Inter via `Info.plist` / `UIAppFonts` (SIL OFL — free to redistribute). Numerals use tabular figures.

```swift
extension Font {
    static let plOutputNumber = Font.custom("Inter-Black",     size: 44)   // weight 900
    static let plScreenTitle  = Font.custom("Inter-Black",     size: 32)
    static let plHeroTitle    = Font.custom("Inter-ExtraBold", size: 26)
    static let plSection      = Font.custom("Inter-ExtraBold", size: 22)
    static let plClassTitle   = Font.custom("Inter-ExtraBold", size: 17)
    static let plMetricValue  = Font.custom("Inter-ExtraBold", size: 18)
    static let plLbOutput     = Font.custom("Inter-ExtraBold", size: 14)
    static let plBody         = Font.custom("Inter-SemiBold",  size: 15)
    static let plMeta         = Font.custom("Inter-Regular",   size: 14)
    static let plEyebrow      = Font.custom("Inter-Bold",      size: 12)   // UPPERCASE +0.6
    static let plMetricLabel  = Font.custom("Inter-SemiBold",  size: 11)   // UPPERCASE +0.4
    static let plCaption      = Font.custom("Inter-SemiBold",  size: 11)
    static let plButton       = Font.custom("Inter-Bold",      size: 15)
    static let plTab          = Font.custom("Inter-SemiBold",  size: 10)
    static let plBadge        = Font.custom("Inter-Black",      size: 10)   // UPPERCASE +0.6
}

extension View {
    func plTabular() -> some View { self.monospacedDigit() }
    func plEyebrowStyle() -> some View {
        self.font(.plEyebrow).textCase(.uppercase).tracking(0.6)
            .foregroundStyle(Color.plRedOnDark)
    }
}
```

## 3. Signature Components

### Class Card

```swift
struct ClassCard: View {
    let discipline: String     // "Power Zone Ride"
    let title: String          // "45 min Power Zone Max"
    let instructor: String     // "Olivia Amato"
    let duration: String       // "45 min"
    let isLive: Bool
    let rating: String         // "4.9 · 12,408 taken"

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topLeading) {
                LinearGradient(colors: [Color(hex: 0x4A1014), Color(hex: 0x160508)],
                               startPoint: .topLeading, endPoint: .bottomTrailing)
                    .frame(height: 150)

                if isLive { LiveBadge().padding(14) }

                Text(duration)
                    .font(.plCaption).foregroundStyle(.white)
                    .padding(.horizontal, 9).padding(.vertical, 5)
                    .background(Color.black.opacity(0.6))
                    .clipShape(RoundedRectangle(cornerRadius: 5))
                    .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topTrailing)
                    .padding(14)
            }

            VStack(alignment: .leading, spacing: 3) {
                Text(discipline.uppercased())
                    .font(.plEyebrow).tracking(0.6).foregroundStyle(Color.plRedOnDark)
                Text(title).font(.plClassTitle).foregroundStyle(.white).padding(.top, 1)
                Text(instructor).font(.plMeta.weight(.semibold)).foregroundStyle(Color.plTextSecondary)
                HStack(spacing: 4) {
                    Image(systemName: "star.fill").font(.system(size: 11)).foregroundStyle(Color.plResistance)
                    Text(rating).font(.system(size: 12)).foregroundStyle(Color.plTextTertiary)
                }.padding(.top, 6)
            }
            .padding(.horizontal, 16).padding(.vertical, 14)
        }
        .background(Color.plSurface1)
        .clipShape(RoundedRectangle(cornerRadius: 14))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.plDivider, lineWidth: 1))
    }
}

extension Color {
    init(hex: UInt) {
        self.init(red: Double((hex >> 16) & 0xFF) / 255,
                  green: Double((hex >> 8) & 0xFF) / 255,
                  blue: Double(hex & 0xFF) / 255)
    }
}
```

### Live Badge (pulsing dot)

```swift
struct LiveBadge: View {
    @State private var pulse = false
    var body: some View {
        HStack(spacing: 6) {
            Circle().fill(.white).frame(width: 6, height: 6)
                .opacity(pulse ? 0.4 : 1)
            Text("LIVE").font(.plBadge).tracking(0.6).foregroundStyle(.white)
        }
        .padding(.horizontal, 9).padding(.vertical, 5)
        .background(Color.plRed)
        .clipShape(RoundedRectangle(cornerRadius: 5))
        .onAppear {
            withAnimation(.easeInOut(duration: 0.7).repeatForever(autoreverses: true)) {
                pulse = true
            }
        }
    }
}
```

### Output Ring

```swift
struct OutputRing: View {
    let progress: Double      // 0...1
    let totalKJ: Int
    @State private var fill: Double = 0

    var body: some View {
        ZStack {
            Circle().stroke(Color.plTrack, lineWidth: 9)
            Circle()
                .trim(from: 0, to: fill)
                .stroke(Color.plRed, style: StrokeStyle(lineWidth: 9, lineCap: .round))
                .rotationEffect(.degrees(-90))
            VStack(spacing: 3) {
                Text("\(totalKJ)").font(.system(size: 26, weight: .black))
                    .foregroundStyle(.white).plTabular()
                Text("TOTAL kJ").font(.system(size: 9, weight: .bold))
                    .tracking(0.5).foregroundStyle(Color.plTextTertiary)
            }
        }
        .frame(width: 96, height: 96)
        .onAppear { withAnimation(.easeOut(duration: 0.6)) { fill = progress } }
    }
}
```

### In-Class Metric Column

```swift
struct Metric: Identifiable { let id = UUID(); let label, value, unit: String; let color: Color }

struct MetricColumn: View {
    let metrics: [Metric]
    var body: some View {
        VStack(spacing: 0) {
            ForEach(Array(metrics.enumerated()), id: \.element.id) { i, m in
                HStack(alignment: .firstTextBaseline) {
                    Text(m.label.uppercased())
                        .font(.plMetricLabel).tracking(0.4)
                        .foregroundStyle(Color.plTextSecondary)
                    Spacer()
                    HStack(alignment: .firstTextBaseline, spacing: 3) {
                        Text(m.value).font(.plMetricValue).foregroundStyle(m.color).plTabular()
                        if !m.unit.isEmpty {
                            Text(m.unit).font(.system(size: 11)).foregroundStyle(Color.plTextTertiary)
                        }
                    }
                }
                .padding(.vertical, 7)
                if i < metrics.count - 1 {
                    Rectangle().fill(Color.plDivider).frame(height: 1)
                }
            }
        }
    }
}
```

### Real-Time Leaderboard

```swift
struct Rider: Identifiable { let id = UUID(); let rank: Int; let name: String; let output: Int; let isMe: Bool }

struct Leaderboard: View {
    let riders: [Rider]
    let totalRiders: Int

    var body: some View {
        VStack(spacing: 0) {
            Text("LEADERBOARD · \(totalRiders) RIDERS")
                .font(.system(size: 11, weight: .bold)).tracking(0.6)
                .foregroundStyle(Color.plTextSecondary)
                .frame(maxWidth: .infinity, alignment: .leading)
                .padding(.horizontal, 16).padding(.top, 14).padding(.bottom, 10)

            ForEach(riders) { r in
                HStack(spacing: 12) {
                    Text("\(r.rank)")
                        .font(.plLbOutput).plTabular()
                        .foregroundStyle(r.isMe ? Color.plRedOnDark : Color.plTextSecondary)
                        .frame(width: 26, alignment: .leading)
                    Circle()
                        .fill(LinearGradient(colors: [Color(hex: 0x5A2B82), Color.plRed],
                                             startPoint: .topLeading, endPoint: .bottomTrailing))
                        .frame(width: 30, height: 30)
                    Text(r.name).font(.plMeta.weight(.semibold)).foregroundStyle(.white)
                    Spacer()
                    Text("\(r.output)").font(.plLbOutput).plTabular().foregroundStyle(.white)
                }
                .padding(.horizontal, 16).padding(.vertical, 10)
                .background(r.isMe ? Color.plRed.opacity(0.12) : .clear)
                .overlay(Rectangle().fill(Color.plDivider).frame(height: 1), alignment: .top)
                .animation(.easeOut(duration: 0.25), value: r.rank)
            }
        }
        .background(Color.plSurface1)
        .clipShape(RoundedRectangle(cornerRadius: 14))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.plDivider, lineWidth: 1))
    }
}
```

### Filter Pill (white-chip)

```swift
struct FilterPill: View {
    let title: String
    let selected: Bool
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.system(size: 13, weight: .semibold))
                .foregroundStyle(selected ? Color.black : Color.plTextSecondary)
                .padding(.horizontal, 16).padding(.vertical, 8)
        }
        .background(selected ? Color.white : Color.plSurface2)
        .overlay(Capsule().strokeBorder(selected ? .clear : Color.plDivider, lineWidth: 1))
        .clipShape(Capsule())
        .buttonStyle(.plain)
        .animation(.easeOut(duration: 0.15), value: selected)
    }
}
```

### Primary Button

```swift
struct PLPrimaryButton: View {
    let title: String
    var variant: Variant = .red
    let action: () -> Void
    enum Variant { case red, white, outline }

    var body: some View {
        Button(action: action) {
            Text(title).font(.plButton)
                .foregroundStyle(variant == .white ? Color.black : .white)
                .frame(maxWidth: .infinity).padding(.vertical, 15)
        }
        .background(bg)
        .overlay(Capsule().strokeBorder(
            variant == .outline ? Color.white.opacity(0.4) : .clear, lineWidth: 1.5))
        .clipShape(Capsule())
        .buttonStyle(.plain)
    }
    private var bg: Color {
        switch variant { case .red: .plRed; case .white: .white; case .outline: .clear }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PLTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            ClassesView().tabItem { Label("Classes", systemImage: "play.rectangle.fill") }
            ScheduleView().tabItem { Label("Schedule", systemImage: "chart.bar.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle.fill") }
        }
        .tint(.white)                                     // active is white, no pill
        .toolbarBackground(Color.plSurface1, for: .tabBar)
        .toolbarColorScheme(.dark, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// LIVE dot pulse — the brand heartbeat
withAnimation(.easeInOut(duration: 0.7).repeatForever(autoreverses: true)) { pulse = true }

// Output ring fill from 0 at class start
.onAppear { withAnimation(.easeOut(duration: 0.6)) { fill = progress } }
// Live tracking after start
withAnimation(.easeOut(duration: 0.3)) { fill = liveProgress }

// Card tap scale
.scaleEffect(pressed ? 0.98 : 1).animation(.easeOut(duration: 0.12), value: pressed)

// Leaderboard reorder
.animation(.easeOut(duration: 0.25), value: rider.rank)

// Pill select
.animation(.easeOut(duration: 0.15), value: selected)

// PR burst
withAnimation(.spring(response: 0.4, dampingFraction: 0.6)) { showPR = true }

// Haptics
.sensoryFeedback(.selection, trigger: selectedPill)
.sensoryFeedback(.impact(weight: .medium), trigger: didTapTakeClass)
.sensoryFeedback(.success, trigger: didHitPR)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Classes (tab) | `play.rectangle` / `play.rectangle.fill` | 22pt |
| Schedule (tab) | `chart.bar` / `chart.bar.fill` | 22pt |
| Profile (tab) | `person.crop.circle` / `.fill` | 22pt |
| Search | `magnifyingglass` | 20pt |
| Filter | `line.3.horizontal.decrease` | 20pt |
| Play (thumbnail) | `play.fill` | 18pt |
| Rating star | `star.fill` | 11–12pt |
| Add to Stack | `plus.circle` | 18pt |
| Cadence | `metronome.fill` | 14pt |
| Resistance | `dial.medium.fill` | 14pt |
| Output / power | `bolt.fill` | 14pt |
| Heart rate | `heart.fill` | 14pt |
| Leaderboard | `list.number` | 18pt |
| PR / trophy | `trophy.fill` | 18pt |
| Music | `music.note` | 16pt |

(Peloton uses custom metric glyphs; SF Symbols above are the closest first-party mappings.)

## 7. Dark Mode

```swift
struct PLTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.plCanvas)            // always studio-black; no light scheme
            .foregroundStyle(Color.plTextPrimary)
            .tint(.white)
            .preferredColorScheme(.dark)
    }
}
extension View { func plTheme() -> some View { modifier(PLTheme()) } }
```

Peloton has **no light mode** — pin `.preferredColorScheme(.dark)` and always render the studio-black palette. Cards lift via surface-lightness steps (`#000000 → #121212 → #1C1C1E`). **In-class metric colors (Cadence `#E5402A`, Resistance `#F0A030`, Strive/HR `#3DB8E0`, Output red) are fixed semantics — never recolor.** Brand-red text/active on black uses brightened `#FF4B57`; fills use `#DF1E2E`.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Inter (all weights through Black) via `Info.plist` `UIAppFonts` — SIL OFL, free to redistribute
- Always `.monospacedDigit()` on the output number, in-class metric values, and leaderboard outputs so live values and reordering don't shift layout
- Dynamic Type: scale screen/hero/section titles, body, meta; keep the output number, metric values/labels, leaderboard, tab labels, and badges FIXED (tight live-updating layouts)
- VoiceOver: announce the LIVE badge as "Live class"; metric rows as "Cadence 94 RPM"; leaderboard rows as "Rank 3, You, 312 output"; the output ring as "Total energy 312 kilojoules"
- Color is never the only signal: the LIVE badge carries the word "LIVE"; metrics carry text labels beside the colored value; your leaderboard row is named "You" plus the red wash
- Contrast: white on `#000000` is maximal; `#FF4B57` on `#000000` passes AA — never render small text in raw `#DF1E2E` on black; the white-chip pill is black-on-white (AA+)
- Reduce Motion: LIVE dot stays solid (no pulse), output ring sets final value with a 150ms crossfade, no card scale, leaderboard reorders instantly, PR burst becomes a static badge
- Reduce Transparency: replace the blurred tab-bar background with solid `#121212`
- Dark mode: the only mode — surfaces step `#000000 → #121212 → #1C1C1E`; floating layers (filter sheet, dialogs) get `#1C1C1E` + `rgba(0,0,0,0.7) 0 8px 28px`
