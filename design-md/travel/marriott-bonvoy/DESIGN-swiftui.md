# Marriott Bonvoy (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Marriott Bonvoy's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature Bonvoy points panel, points-aware rate cards, the hotel hero, the Mobile Key, and the bottom tab bar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let mbCanvas        = Color.white                                   // #FFFFFF
    static let mbSurfaceCream  = Color(red: 0.965, green: 0.957, blue: 0.937) // #F6F4EF
    static let mbSurfacePressed = Color(red: 0.925, green: 0.914, blue: 0.882) // #ECE9E1
    static let mbDivider       = Color(red: 0.890, green: 0.882, blue: 0.855) // #E3E1DA

    // MARK: - Canvas & Surfaces (Dark)
    static let mbDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.078) // #121214
    static let mbDarkSurface1  = Color(red: 0.102, green: 0.102, blue: 0.114) // #1A1A1D
    static let mbDarkSurface2  = Color(red: 0.137, green: 0.137, blue: 0.153) // #232327
    static let mbDarkDivider   = Color(red: 0.180, green: 0.180, blue: 0.200) // #2E2E33

    // MARK: - Structure (Navy) — constant across schemes
    static let mbNavy      = Color(red: 0.086, green: 0.149, blue: 0.290) // #16264A
    static let mbNearBlack = Color(red: 0.110, green: 0.110, blue: 0.110) // #1C1C1C
    static let mbNavyLine  = Color(red: 0.141, green: 0.227, blue: 0.400) // #243A66
    static let mbNavyPressed = Color(red: 0.122, green: 0.192, blue: 0.341) // #1F3157

    // MARK: - Member Value (Gold)
    static let mbGold        = Color(red: 0.702, green: 0.522, blue: 0.165) // #B3852A
    static let mbGoldBright  = Color(red: 0.824, green: 0.635, blue: 0.243) // #D2A23E
    static let mbGoldPressed = Color(red: 0.561, green: 0.416, blue: 0.122) // #8F6A1F
    static let mbOnGold      = Color(red: 0.110, green: 0.071, blue: 0.024) // #1C1206

    // MARK: - Text
    static let mbTextPrimary   = Color(red: 0.102, green: 0.102, blue: 0.110) // #1A1A1C
    static let mbTextSecondary = Color(red: 0.361, green: 0.361, blue: 0.388) // #5C5C63
    static let mbTextTertiary  = Color(red: 0.557, green: 0.557, blue: 0.588) // #8E8E96
    static let mbDarkTextPrimary   = Color(red: 0.929, green: 0.929, blue: 0.937) // #EDEDEF
    static let mbDarkTextSecondary = Color(red: 0.627, green: 0.627, blue: 0.659) // #A0A0A8

    // MARK: - Semantic
    static let mbSuccess = Color(red: 0.180, green: 0.545, blue: 0.341) // #2E8B57
    static let mbError   = Color(red: 0.780, green: 0.271, blue: 0.231) // #C7453B
    static let mbWarning = Color(red: 0.784, green: 0.565, blue: 0.110) // #C8901C
}

enum BonvoyTier: CaseIterable {
    case member, silver, gold, platinum, titanium, ambassador
    var label: String {
        switch self {
        case .member: return "MEMBER"; case .silver: return "SILVER ELITE"
        case .gold: return "GOLD ELITE"; case .platinum: return "PLATINUM ELITE"
        case .titanium: return "TITANIUM ELITE"; case .ambassador: return "AMBASSADOR ELITE"
        }
    }
}
```

## 2. Typography

Marriott Bonvoy ships **no custom typeface** — it uses SF Pro with Dynamic Type. Sub-brand and tier eyebrows are letter-spaced uppercase small-caps; the points balance is an 800-weight numeral.

```swift
extension Font {
    static let mbLargeTitle    = Font.system(size: 32, weight: .heavy,    design: .default)
    static let mbPointsBalance = Font.system(size: 26, weight: .heavy,    design: .default)
    static let mbHotelName     = Font.system(size: 22, weight: .bold,     design: .default)
    static let mbSection       = Font.system(size: 18, weight: .bold,     design: .default)
    static let mbBody          = Font.system(size: 16, weight: .regular,  design: .default)
    static let mbRateTitle     = Font.system(size: 15, weight: .semibold, design: .default)
    static let mbPrice         = Font.system(size: 17, weight: .heavy,    design: .default)
    static let mbMeta          = Font.system(size: 14, weight: .regular,  design: .default)
    static let mbEyebrow       = Font.system(size: 11, weight: .bold,     design: .default) // + .tracking(1.2)
    static let mbPointsEarn    = Font.system(size: 11, weight: .bold,     design: .default) // + .tracking(0.3)
    static let mbButton        = Font.system(size: 16, weight: .bold,     design: .default)
    static let mbTab           = Font.system(size: 10, weight: .medium,   design: .default)
    static let mbCaption       = Font.system(size: 12, weight: .medium,   design: .default)
}

// Eyebrow helper — uppercase + tracked small-caps
extension Text {
    func bonvoyEyebrow(_ color: Color = .mbGoldBright, tracking: CGFloat = 1.2) -> some View {
        self.font(.mbEyebrow).tracking(tracking).foregroundStyle(color).textCase(.uppercase)
    }
}
```

## 3. Signature Components

### Bonvoy Points Panel (the core component)

```swift
struct BonvoyPointsPanel: View {
    let tier: BonvoyTier
    let memberName: String
    let points: Int
    let progress: Double          // 0...1 toward next free night
    let footnote: String          // "21,550 points to your next free night..."
    @State private var animatedPoints = 0
    @State private var barWidth: CGFloat = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 14) {
            HStack(alignment: .top) {
                VStack(alignment: .leading, spacing: 2) {
                    Text(tier.label).bonvoyEyebrow()
                    Text(memberName)
                        .font(.system(size: 13))
                        .foregroundStyle(.white.opacity(0.7))
                }
                Spacer()
                VStack(alignment: .trailing, spacing: 1) {
                    Text(animatedPoints, format: .number)
                        .font(.mbPointsBalance).foregroundStyle(.white)
                    Text("POINTS").font(.system(size: 11, weight: .semibold))
                        .tracking(0.4).foregroundStyle(.white.opacity(0.6))
                }
            }
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.white.opacity(0.14))
                    Capsule()
                        .fill(LinearGradient(colors: [.mbGold, .mbGoldBright],
                                             startPoint: .leading, endPoint: .trailing))
                        .frame(width: barWidth)
                }
                .onAppear {
                    withAnimation(.easeOut(duration: 0.5)) { barWidth = geo.size.width * progress }
                }
            }
            .frame(height: 6)
            Text(footnote)
                .font(.system(size: 11)).foregroundStyle(.white.opacity(0.6))
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.mbNavy))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.mbNavyLine, lineWidth: 1))
        .onAppear {
            withAnimation(.easeOut(duration: 0.6)) { animatedPoints = points }
        }
    }
}
```

### Points-Aware Rate Card

```swift
struct RateCard: View {
    let name: String
    let detail: String
    let priceText: String         // "$545" or "68,000"
    let pointsLine: String        // "+ 2,180 pts" or "points / night"
    let selected: Bool
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack {
                VStack(alignment: .leading, spacing: 3) {
                    Text(name).font(.mbRateTitle).foregroundStyle(Color.mbDarkTextPrimary)
                    Text(detail).font(.system(size: 12)).foregroundStyle(Color.mbDarkTextSecondary)
                }
                Spacer()
                VStack(alignment: .trailing, spacing: 2) {
                    Text(priceText).font(.mbPrice).foregroundStyle(Color.mbDarkTextPrimary)
                    Text(pointsLine).font(.mbPointsEarn).tracking(0.3)
                        .foregroundStyle(Color.mbGoldBright)
                }
            }
            .padding(14)
            .background(RoundedRectangle(cornerRadius: 10)
                .fill(selected ? Color.mbGold.opacity(0.1) : Color.mbDarkSurface1))
            .overlay(RoundedRectangle(cornerRadius: 10)
                .strokeBorder(selected ? Color.mbGold : Color.mbDarkDivider,
                              lineWidth: selected ? 1 : 1))
            .animation(.easeOut(duration: 0.18), value: selected)
        }
        .buttonStyle(.plain)
    }
}
```

### Hotel Hero

```swift
struct HotelHero: View {
    let imageURL: String
    let subBrand: String          // "THE RITZ-CARLTON"
    let name: String
    let location: String
    let onBack: () -> Void
    let onShare: () -> Void
    @Binding var saved: Bool

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            AsyncImage(url: URL(string: imageURL)) { img in
                img.resizable().aspectRatio(contentMode: .fill)
            } placeholder: { Color.mbNavy }
            .frame(height: 256).clipped()

            LinearGradient(colors: [.clear, Color.mbDarkCanvas.opacity(0.7)],
                           startPoint: .center, endPoint: .bottom)
                .frame(height: 145).allowsHitTesting(false)

            VStack(alignment: .leading, spacing: 5) {
                Text(subBrand).bonvoyEyebrow(.mbGoldBright, tracking: 1.4)
                Text(name).font(.system(size: 23, weight: .heavy))
                    .foregroundStyle(.white)
                Text(location).font(.system(size: 13))
                    .foregroundStyle(.white.opacity(0.82))
            }
            .padding(18)
        }
        .overlay(alignment: .top) {
            HStack {
                HeroButton(system: "chevron.left", action: onBack)
                Spacer()
                HStack(spacing: 10) {
                    HeroButton(system: "square.and.arrow.up", action: onShare)
                    HeroButton(system: saved ? "heart.fill" : "heart") { saved.toggle() }
                }
            }
            .padding(.horizontal, 16).padding(.top, 54)
        }
    }
}

struct HeroButton: View {
    let system: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: system)
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(.white)
                .frame(width: 36, height: 36)
                .background(.ultraThinMaterial, in: Circle())
        }
    }
}
```

### Mobile Key

```swift
struct MobileKeyCard: View {
    let room: String              // "1408"
    let checkIn: String           // "3:00 PM"
    let onUnlock: () -> Void
    @State private var pulse = false

    var body: some View {
        Button(action: { pulse.toggle(); onUnlock() }) {
            HStack(spacing: 14) {
                ZStack {
                    RoundedRectangle(cornerRadius: 12)
                        .fill(Color.mbGoldBright.opacity(0.16))
                        .frame(width: 48, height: 48)
                    Image(systemName: "lock.fill")
                        .font(.system(size: 22))
                        .foregroundStyle(Color.mbGoldBright)
                        .scaleEffect(pulse ? 1.12 : 1)
                }
                VStack(alignment: .leading, spacing: 4) {
                    Text("Mobile Key ready")
                        .font(.system(size: 14, weight: .bold)).foregroundStyle(.white)
                    Text("Skip the front desk · Tap your phone to unlock")
                        .font(.system(size: 12)).foregroundStyle(.white.opacity(0.65))
                    Text("ROOM \(room) · CHECK-IN \(checkIn)")
                        .font(.mbEyebrow).tracking(0.5).foregroundStyle(Color.mbGoldBright)
                }
                Spacer()
            }
            .padding(16)
            .background(RoundedRectangle(cornerRadius: 14).fill(Color.mbNavy))
            .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.mbNavyLine, lineWidth: 1))
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.success, trigger: pulse)
    }
}
```

### Elite Tier Badge + Booking Bar

```swift
struct TierBadge: View {
    let tier: BonvoyTier
    var body: some View {
        Text(tier.label)
            .font(.mbEyebrow).tracking(0.6)
            .foregroundStyle(fg).textCase(.uppercase)
            .padding(.horizontal, 12).padding(.vertical, 6)
            .background(Capsule().fill(bg))
            .overlay(Capsule().strokeBorder(border, lineWidth: 1))
    }
    private var fg: Color {
        switch tier {
        case .member: return .mbTextSecondary
        case .silver: return Color(red: 0.78, green: 0.78, blue: 0.81)
        case .gold, .titanium, .ambassador: return .mbGoldBright
        case .platinum: return Color(red: 0.62, green: 0.71, blue: 0.88)
        }
    }
    private var bg: Color {
        switch tier {
        case .member: return .mbDarkSurface2
        case .silver: return Color(red: 0.165, green: 0.165, blue: 0.180)
        case .gold: return .mbGold.opacity(0.16)
        case .platinum: return .mbNavy.opacity(0.4)
        case .titanium, .ambassador: return .mbNavy
        }
    }
    private var border: Color {
        tier == .ambassador ? .mbGold : Color.white.opacity(0.06)
    }
}

struct BonvoyBookingBar: View {
    let price: String
    let pointsEarned: String
    let onBook: () -> Void
    var body: some View {
        HStack {
            VStack(alignment: .leading, spacing: 2) {
                HStack(alignment: .firstTextBaseline, spacing: 4) {
                    Text(price).font(.system(size: 19, weight: .heavy))
                        .foregroundStyle(Color.mbDarkTextPrimary)
                    Text("/ night").font(.system(size: 13))
                        .foregroundStyle(Color.mbDarkTextSecondary)
                }
                Text(pointsEarned).font(.mbPointsEarn)
                    .foregroundStyle(Color.mbGoldBright)
            }
            Spacer()
            Button(action: onBook) {
                Text("Book").font(.mbButton)
                    .foregroundStyle(Color.mbOnGold)
                    .padding(.horizontal, 26).padding(.vertical, 13)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.mbGold))
            }
            .sensoryFeedback(.impact(flexibility: .soft), trigger: price)
        }
        .padding(.horizontal, 18)
        .frame(height: 76)
        .background(.ultraThinMaterial)
        .overlay(Rectangle().fill(Color.mbDarkDivider).frame(height: 1), alignment: .top)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct BonvoyTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            TripsView().tabItem { Label("Trips", systemImage: "suitcase") }
            MobileKeyView().tabItem { Label("Mobile Key", systemImage: "key") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.mbGoldBright)   // active tab = gold; loyalty is the spine of the app
    }
}
```

## 5. Motion

```swift
// Points balance count-up + bar fill (member panel)
withAnimation(.easeOut(duration: 0.6)) { animatedPoints = points }
withAnimation(.easeOut(duration: 0.5)) { barWidth = fullWidth * progress }

// Rate select — gold border + tint, then booking-bar cross-dissolve
.animation(.easeOut(duration: 0.18), value: selected)
Text(price).contentTransition(.opacity)
withAnimation(.easeInOut(duration: 0.25)) { price = newPrice; pointsEarned = newPts }

// Mobile Key unlock — ring pulse + success haptic
.sensoryFeedback(.success, trigger: keyTapped)

// Hero parallax
.offset(y: scrollOffset * 0.5)   // 0.5x scroll speed

// Card appear
.transition(.opacity.combined(with: .move(edge: .bottom)))

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: bookTapped)   // Book
.sensoryFeedback(.success, trigger: bookingConfirmed)                // confirmed
.sensoryFeedback(.selection, trigger: selectedRate)                  // rate select
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Trips (tab) | `suitcase` / `suitcase.fill` | 22pt |
| Mobile Key (tab) | `key` / `key.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Back (hero) | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Save | `heart` / `heart.fill` | 18pt |
| Rating star | `star.fill` | 14pt |
| Mobile Key glyph | `lock.fill` / `key.fill` | 22pt |
| Points / rewards | `star.circle.fill` | 16pt |
| Free night | `moon.stars.fill` | 14pt |
| Member tier | `crown.fill` | 14pt |
| Calendar / dates | `calendar` | 16pt |
| Room & guests | `person.2` | 16pt |
| Location | `mappin.and.ellipse` | 14pt |
| Amenities | `checkmark.circle` | 14pt |
| Filters | `slider.horizontal.3` | 16pt |
| Confirmed | `checkmark.seal.fill` | 18pt |

## 7. Dark Mode

```swift
struct BonvoyTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.mbDarkCanvas : Color.mbCanvas)
            .foregroundStyle(scheme == .dark ? Color.mbDarkTextPrimary : Color.mbTextPrimary)
            .tint(scheme == .dark ? Color.mbGoldBright : Color.mbGold)
    }
}

extension View {
    func bonvoyTheme() -> some View { modifier(BonvoyTheme()) }
}

// Navy is constant across schemes (panels, secondary CTAs, the brand frame).
// Gold brightens on dark so points / tier / Book still glow.
extension Color {
    static func mbValue(_ scheme: ColorScheme) -> Color {
        scheme == .dark ? .mbGoldBright : .mbGold
    }
    static func mbLink(_ scheme: ColorScheme) -> Color {
        scheme == .dark ? .mbGoldBright : .mbNavy
    }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition` require iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator` and a plain crossfade for price/points)
- No custom fonts to bundle — Marriott Bonvoy uses SF Pro (system); enable Dynamic Type everywhere
- Dynamic Type: scale large titles, points balance, hotel name, section headers, body; keep eyebrows (sub-brand / tier small-caps), tab labels, points-earn lines, and progress legends at FIXED sizes (layout-sensitive)
- VoiceOver: announce the member panel "Titanium Elite, 128,450 points, 64% toward your next free night"; rate cards "{name}, {detail}, {price}, earn {points} points, double-tap to select"; the Mobile Key "Mobile Key ready, Room 1408, double-tap to unlock"
- Color contrast: `#1A1A1C` on `#FFFFFF` and `#EDEDEF` on `#121214` pass WCAG AA; gold-on-navy and the `#1C1206`-on-gold CTA are carefully paired; tier badges include text labels, not color alone
- Reduce Motion: disable the points count-up and bar-fill animations (set final values directly); cross-dissolve rate/price changes; remove hero parallax
- Dark mode: navy `#16264A` stays constant (panels, secondary CTA, brand frame); gold brightens to `#D2A23E` for points/tier/Book; canvas is `#121214`, NOT pure black; shadows nearly vanish so floating panels add a 1pt `mbDarkDivider` border
- Photography: hero images are full-bleed and never carry a shadow; provide descriptive alt text for VoiceOver; the gold sub-brand eyebrow must remain legible (it carries the property's luxury identity)
