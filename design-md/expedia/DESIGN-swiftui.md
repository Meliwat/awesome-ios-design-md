# Expedia (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Expedia's visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views (the property result card, segmented mode switch, One Key strip).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let expCanvas        = Color.white                                    // #FFFFFF
    static let expSurfaceGray   = Color(red: 0.961, green: 0.969, blue: 0.980)  // #F5F7FA
    static let expSurfacePressed = Color(red: 0.925, green: 0.937, blue: 0.957) // #ECEFF4
    static let expDivider       = Color(red: 0.890, green: 0.906, blue: 0.929)  // #E3E7ED

    // MARK: - Canvas & Surfaces (Dark)
    static let expDarkCanvas    = Color(red: 0.055, green: 0.067, blue: 0.086)  // #0E1116
    static let expDarkSurface1  = Color(red: 0.086, green: 0.106, blue: 0.133)  // #161B22
    static let expDarkSurface2  = Color(red: 0.122, green: 0.149, blue: 0.188)  // #1F2630
    static let expDarkDivider   = Color(red: 0.165, green: 0.196, blue: 0.243)  // #2A323E

    // MARK: - Text
    static let expTextPrimary   = Color(red: 0.102, green: 0.122, blue: 0.149)  // #1A1F26
    static let expTextSecondary = Color(red: 0.353, green: 0.396, blue: 0.451)  // #5A6573
    static let expTextTertiary  = Color(red: 0.541, green: 0.584, blue: 0.639)  // #8A95A3
    static let expDarkTextPrimary   = Color(red: 0.910, green: 0.922, blue: 0.937) // #E8EBEF
    static let expDarkTextSecondary = Color(red: 0.604, green: 0.643, blue: 0.698) // #9AA4B2

    // MARK: - Brand & Interactive
    static let expYellow        = Color(red: 1.000, green: 0.788, blue: 0.302)  // #FFC94D
    static let expYellowDeep    = Color(red: 1.000, green: 0.702, blue: 0.102)  // #FFB31A
    static let expActionBlue    = Color(red: 0.086, green: 0.408, blue: 0.890)  // #1668E3
    static let expActionPressed = Color(red: 0.059, green: 0.310, blue: 0.690)  // #0F4FB0
    static let expNavy          = Color(red: 0.000, green: 0.208, blue: 0.373)  // #00355F
    static let expNavySoft      = Color(red: 0.078, green: 0.255, blue: 0.420)  // #14416B
    static let expOneKeyGold    = Color(red: 0.961, green: 0.773, blue: 0.094)  // #F5C518

    // MARK: - Semantic
    static let expSuccess       = Color(red: 0.102, green: 0.545, blue: 0.294)  // #1A8B4B
    static let expError         = Color(red: 0.851, green: 0.227, blue: 0.227)  // #D93A3A
    static let expWarning       = Color(red: 0.910, green: 0.514, blue: 0.047)  // #E8830C
}

// Review-score badge color for a 0–10 guest score
func expReviewBadgeColor(_ score: Double) -> Color {
    switch score {
    case 9.0...:   return .expSuccess     // Wonderful
    case 8.0..<9.0: return .expActionBlue // Excellent
    case 7.0..<8.0: return .expNavySoft   // Good
    case 6.0..<7.0: return .expTextSecondary
    default:        return .expTextTertiary
    }
}
```

## 2. Typography

Expedia Sans is proprietary; bundle it via `Info.plist` / `UIAppFonts` if licensed, otherwise the system falls back to SF Pro. Inter is the closest free substitute. Prices use **monospaced (tabular) digits** so columns align.

```swift
extension Font {
    // Expedia Sans → SF Pro fallback (swap "ExpediaSans-*" if the licensed face is bundled)
    static func expedia(_ size: CGFloat, _ weight: Font.Weight = .regular) -> Font {
        Font.system(size: size, weight: weight, design: .default)
    }

    static let expDisplay      = Font.system(size: 32, weight: .heavy)
    static let expScreenTitle  = Font.system(size: 26, weight: .heavy)
    static let expSection      = Font.system(size: 22, weight: .bold)
    static let expCardTitle    = Font.system(size: 18, weight: .bold)
    static let expBody         = Font.system(size: 16, weight: .regular)
    static let expCardSubtitle = Font.system(size: 15, weight: .semibold)
    static let expMeta         = Font.system(size: 14, weight: .regular)
    static let expStrike       = Font.system(size: 13, weight: .regular)
    static let expBadge        = Font.system(size: 12, weight: .semibold)
    static let expOneKeyLine   = Font.system(size: 11, weight: .bold)
    static let expButton       = Font.system(size: 16, weight: .bold)
    static let expTab          = Font.system(size: 10, weight: .semibold)

    // Price figures — always tabular
    static let expPriceNow   = Font.system(size: 18, weight: .heavy).monospacedDigit()
    static let expScoreNum   = Font.system(size: 15, weight: .heavy).monospacedDigit()
}
```

## 3. Signature Components

### Property Result Card

```swift
struct PropertyCard: View {
    let imageName: String
    let dealFlag: String?         // "−24% Bundle" / "Member price"
    let title: String
    let location: String
    let score: Double             // 9.2
    let scoreWord: String         // "Wonderful"
    let reviewCount: Int
    let strikePrice: Int?
    let nightlyPrice: Int
    let oneKeyEarn: Int
    @State private var saved = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topLeading) {
                Image(imageName)
                    .resizable().aspectRatio(16.0/10.0, contentMode: .fill)
                    .clipped()

                if let dealFlag {
                    Text(dealFlag)
                        .font(.expBadge).foregroundStyle(Color.expNavy)
                        .padding(.vertical, 4).padding(.horizontal, 9)
                        .background(RoundedRectangle(cornerRadius: 6).fill(Color.expYellow))
                        .padding(12)
                }

                Button { withAnimation(.spring(response: 0.22, dampingFraction: 0.55)) { saved.toggle() } } label: {
                    Image(systemName: saved ? "heart.fill" : "heart")
                        .font(.system(size: 18, weight: .semibold))
                        .foregroundStyle(saved ? Color.expActionBlue : .white)
                        .padding(7)
                        .background(Circle().fill(Color.black.opacity(0.4)))
                }
                .frame(maxWidth: .infinity, alignment: .trailing)
                .padding(10)
                .scaleEffect(saved ? 1.0 : 1.0)
            }

            VStack(alignment: .leading, spacing: 0) {
                Text(title).font(.expCardTitle).foregroundStyle(Color.expTextPrimary)
                Text(location).font(.expCardSubtitle).foregroundStyle(Color.expTextSecondary)
                    .padding(.top, 3)

                HStack(spacing: 6) {
                    Text(String(format: "%.1f", score))
                        .font(.expScoreNum).foregroundStyle(.white)
                        .padding(.vertical, 3).padding(.horizontal, 7)
                        .background(RoundedRectangle(cornerRadius: 6).fill(expReviewBadgeColor(score)))
                    Text(scoreWord).font(.expBadge.weight(.bold)).foregroundStyle(Color.expTextPrimary)
                    Text("· \(reviewCount.formatted()) reviews")
                        .font(.expBadge).foregroundStyle(Color.expTextSecondary)
                }
                .padding(.top, 8)

                HStack(alignment: .firstTextBaseline, spacing: 6) {
                    if let strikePrice {
                        Text("$\(strikePrice)")
                            .font(.expStrike).strikethrough()
                            .foregroundStyle(Color.expTextTertiary)
                    }
                    Text("$\(nightlyPrice)").font(.expPriceNow).foregroundStyle(Color.expTextPrimary)
                    Text("/ night").font(.expBadge).foregroundStyle(Color.expTextSecondary)
                }
                .padding(.top, 10)

                HStack(spacing: 5) {
                    Circle().fill(Color.expOneKeyGold).frame(width: 8, height: 8)
                    Text("Earn \(oneKeyEarn.formatted()) One Key cash")
                        .font(.expOneKeyLine).foregroundStyle(Color.expOneKeyGold)
                }
                .padding(.top, 7)
            }
            .padding(.horizontal, 14).padding(.top, 12).padding(.bottom, 14)
        }
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.expCanvas))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.expDivider, lineWidth: 0.5))
        .clipShape(RoundedRectangle(cornerRadius: 16))
        .shadow(color: .black.opacity(0.08), radius: 12, y: 4)
        .sensoryFeedback(.impact(flexibility: .soft), trigger: saved)
    }
}
```

### Segmented Mode Switch (Stays / Flights / Cars / Bundle)

```swift
struct ModeSwitch: View {
    @Binding var selection: Int
    let modes = ["Stays", "Flights", "Cars", "Bundle"]

    var body: some View {
        HStack(spacing: 8) {
            ForEach(modes.indices, id: \.self) { i in
                let active = selection == i
                Text(modes[i])
                    .font(.expBadge.weight(.semibold))
                    .foregroundStyle(active ? Color.expNavy : Color.expTextSecondary)
                    .frame(maxWidth: .infinity)
                    .padding(.vertical, 9)
                    .background(
                        RoundedRectangle(cornerRadius: 10)
                            .fill(active ? Color.expYellow : Color.expSurfaceGray)
                    )
                    .overlay(
                        RoundedRectangle(cornerRadius: 10)
                            .strokeBorder(active ? Color.clear : Color.expDivider, lineWidth: 0.5)
                    )
                    .onTapGesture {
                        withAnimation(.easeOut(duration: 0.2)) { selection = i }
                    }
            }
        }
        .padding(.horizontal, 16)
    }
}
```

### One Key Rewards Strip

```swift
struct OneKeyStrip: View {
    let balance: String   // "$124.80"

    var body: some View {
        HStack(spacing: 12) {
            Text("1K")
                .font(.system(size: 13, weight: .heavy))
                .foregroundStyle(Color(red: 0.106, green: 0.106, blue: 0.106))
                .frame(width: 36, height: 36)
                .background(Circle().fill(Color.expOneKeyGold))

            VStack(alignment: .leading, spacing: 2) {
                Text("One Key cash available")
                    .font(.system(size: 13, weight: .bold))
                    .foregroundStyle(Color.expTextPrimary)
                Text("Apply at checkout on any stay, flight, or car")
                    .font(.system(size: 11)).foregroundStyle(Color.expTextSecondary)
            }
            Spacer()
            Text(balance).font(.system(size: 16, weight: .heavy))
                .foregroundStyle(Color.expOneKeyGold)
        }
        .padding(.vertical, 14).padding(.horizontal, 16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.expSurfaceGray))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.expDivider, lineWidth: 0.5))
    }
}
```

### Search Pill

```swift
struct SearchPill: View {
    let destination: String
    let detail: String

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "magnifyingglass")
                .font(.system(size: 17, weight: .semibold))
                .foregroundStyle(Color.expTextSecondary)
            VStack(alignment: .leading, spacing: 2) {
                Text(destination).font(.system(size: 14, weight: .bold))
                    .foregroundStyle(Color.expTextPrimary)
                Text(detail).font(.system(size: 11)).foregroundStyle(Color.expTextSecondary)
            }
            Spacer()
        }
        .padding(.horizontal, 16).frame(height: 52)
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.expCanvas))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.expDivider, lineWidth: 0.5))
        .shadow(color: .black.opacity(0.08), radius: 12, y: 4)
        .padding(.horizontal, 16)
    }
}
```

### Buttons

```swift
struct ReserveButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.expButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 14)
        }
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.expActionBlue))
        .buttonStyle(.plain)
    }
}

struct BundleButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.expButton).foregroundStyle(Color.expNavy)
                .frame(maxWidth: .infinity).padding(.vertical, 14)
        }
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.expYellow))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ExpediaTabView: View {
    var body: some View {
        TabView {
            SearchScreen().tabItem { Label("Search", systemImage: "magnifyingglass") }
            SavedScreen().tabItem { Label("Saved", systemImage: "heart") }
            TripsScreen().tabItem { Label("Trips", systemImage: "suitcase") }
            SupportScreen().tabItem { Label("Support", systemImage: "questionmark.circle") }
            AccountScreen().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.expActionBlue) // active = Action Blue, no pill indicator
    }
}
```

## 5. Motion

```swift
// Save heart — scale bounce + soft haptic
withAnimation(.spring(response: 0.22, dampingFraction: 0.55)) { saved.toggle() }
.sensoryFeedback(.impact(flexibility: .soft), trigger: saved)

// Segmented mode switch — color crossfade
withAnimation(.easeOut(duration: 0.2)) { selection = i }

// Price drop — brief yellow flash
withAnimation(.easeOut(duration: 0.18)) { priceColor = .expYellow }
DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
    withAnimation { priceColor = .expTextPrimary }
}

// Card → detail — shared element hero
.matchedGeometryEffect(id: "hero-\(card.id)", in: namespace)
// push transition ~320ms ease-out (navigation default)

// Bundle reveal — savings ribbon
.transition(.move(edge: .top).combined(with: .scale(scale: 0.96)))
// withAnimation(.easeOut(duration: 0.24))

// Sticky CTA bar
.transition(.move(edge: .bottom).combined(with: .opacity)) // 200ms
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search (tab) | `magnifyingglass` | 22pt |
| Saved (tab) | `heart` / `heart.fill` | 22pt |
| Trips (tab) | `suitcase` / `suitcase.fill` | 22pt |
| Support (tab) | `questionmark.circle` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Save heart (card) | `heart` / `heart.fill` | 18pt |
| Back | `chevron.left` | 22pt |
| Share | `square.and.arrow.up` | 20pt |
| Filter | `slider.horizontal.3` | 18pt |
| Sort | `arrow.up.arrow.down` | 18pt |
| Flight | `airplane` | 18pt |
| Stay / hotel | `bed.double` | 18pt |
| Car | `car` | 18pt |
| Bundle | `shippingbox` | 18pt |
| Date / calendar | `calendar` | 18pt |
| Travelers | `person.2` | 18pt |
| Map | `map` | 18pt |
| One Key | `key.fill` | 14pt |
| Price trend up | `chart.line.uptrend.xyaxis` | 16pt |
| Free cancellation | `checkmark.shield` | 14pt |

## 7. Dark Mode

```swift
struct ExpediaTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.expDarkCanvas : Color.expSurfaceGray)
            .foregroundStyle(scheme == .dark ? Color.expDarkTextPrimary : Color.expTextPrimary)
    }
}
extension View { func expediaTheme() -> some View { modifier(ExpediaTheme()) } }
```

- Dark canvas is `#0E1116` (navy-biased), NOT pure black; cards lift to `#161B22`.
- Expedia Yellow `#FFC94D`, Action Blue `#1668E3`, One Key gold `#F5C518` are identical across themes.
- Shadows deepen to 0.4–0.6 opacity on dark to register over the canvas.
- Property photography stays full-color; only chrome dims.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` on 16).
- Expedia Sans is proprietary — only bundle it if you hold a license; otherwise SF Pro fallback is the safe default. Inter is the closest free substitute for parity testing.
- Dynamic Type: scale display, titles, body, prices; pin tab labels, deal flags/badges, One Key lines, and the review-score number (layout-sensitive).
- Prices and review scores must use `.monospacedDigit()` so columns align across rows.
- VoiceOver: label the card as "{title}, {scoreWord} {score} out of 10, {reviewCount} reviews, {nightlyPrice} dollars per night"; the save heart as "Save {title}" / "Saved"; the One Key line as "Earn {n} One Key cash".
- Color contrast: navy `#00355F` on Expedia Yellow `#FFC94D` and white on Action Blue `#1668E3` both pass WCAG AA at button text sizes; verify any custom yellow pairing.
- Reduce Motion: replace the save-heart spring with a crossfade; disable the bundle ribbon scale; keep the price-drop color flash (conveys state).
- Dark mode: invert via the `expDark*` palette; keep brand yellow/blue/gold fixed; add a 0.5pt `expDarkDivider` border to floating sheets as an elevation cue.
