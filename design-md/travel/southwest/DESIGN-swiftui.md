# Southwest (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Southwest's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and the signature boarding-position + check-in components.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Heart Tri-Color (Brand) — strict role system
    static let swBlue        = Color(red: 0.188, green: 0.298, blue: 0.698) // #304CB2  structure
    static let swBlueBright  = Color(red: 0.145, green: 0.404, blue: 0.910) // #2567E8  link
    static let swBlueDeep    = Color(red: 0.141, green: 0.235, blue: 0.557) // #243C8E  hero gradient end
    static let swRed         = Color(red: 0.898, green: 0.114, blue: 0.137) // #E51D23  ALERTS ONLY
    static let swRedPressed  = Color(red: 0.753, green: 0.086, blue: 0.106) // #C0161B
    static let swYellow      = Color(red: 0.976, green: 0.714, blue: 0.071) // #F9B612  THE CTA
    static let swYellowPressed = Color(red: 0.878, green: 0.639, blue: 0.0) // #E0A300

    // MARK: - Canvas & Surfaces (Light)
    static let swCanvas      = Color.white                                   // #FFFFFF
    static let swWash        = Color(red: 0.949, green: 0.961, blue: 0.984) // #F2F5FB
    static let swSurface     = Color.white                                   // #FFFFFF (card)
    static let swPressed     = Color(red: 0.906, green: 0.925, blue: 0.965) // #E7ECF6
    static let swDivider     = Color(red: 0.863, green: 0.886, blue: 0.933) // #DCE2EE

    // MARK: - Canvas & Surfaces (Dark)
    static let swDarkCanvas  = Color(red: 0.055, green: 0.090, blue: 0.149) // #0E1726
    static let swDarkSurface1 = Color(red: 0.086, green: 0.133, blue: 0.227) // #16223A
    static let swDarkSurface2 = Color(red: 0.122, green: 0.180, blue: 0.290) // #1F2E4A
    static let swDarkDivider = Color(red: 0.165, green: 0.227, blue: 0.341) // #2A3A57

    // MARK: - Text
    static let swTextPrimary    = Color(red: 0.102, green: 0.133, blue: 0.200) // #1A2233
    static let swTextSecondary  = Color(red: 0.353, green: 0.404, blue: 0.514) // #5A6783
    static let swTextTertiary   = Color(red: 0.549, green: 0.600, blue: 0.702) // #8C99B3
    static let swDarkTextPrimary   = Color(red: 0.918, green: 0.941, blue: 0.980) // #EAF0FA
    static let swDarkTextSecondary = Color(red: 0.616, green: 0.667, blue: 0.769) // #9DAAC4
    static let swDarkTextTertiary  = Color(red: 0.404, green: 0.455, blue: 0.561) // #67748F
    static let swOnYellow  = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let swOnBlue    = Color.white                                   // #FFFFFF

    // MARK: - Semantic
    static let swSuccess      = Color(red: 0.118, green: 0.557, blue: 0.306) // #1E8E4E light
    static let swSuccessDark  = Color(red: 0.122, green: 0.682, blue: 0.369) // #1FAE5E dark
    // Warning == swYellow, Error == swRed (alerts only), Info == swBlue / swBlueBright
}
```

## 2. Typography

Southwest's brand face is **Southwest Sans** (proprietary, Monotype). Bundle it via `Info.plist` / `UIAppFonts` if licensed; otherwise ship **Inter** (SIL OFL) as the fallback. Numbers are tabular everywhere they update.

```swift
extension Font {
    // Brand face name — swap "Inter" for "SouthwestSans" if licensed
    private static let brand = "Inter"

    static let swBoardingGroup = Font.custom("\(brand)-Black",      size: 88).weight(.black)
    static let swBoardingNum   = Font.custom("\(brand)-ExtraBold",  size: 52).weight(.heavy)
    static let swScreenTitle   = Font.custom("\(brand)-Black",      size: 32).weight(.black)
    static let swAirportCode   = Font.custom("\(brand)-ExtraBold",  size: 26).weight(.heavy)
    static let swSection       = Font.custom("\(brand)-ExtraBold",  size: 22).weight(.heavy)
    static let swSubsection    = Font.custom("\(brand)-Bold",       size: 18).weight(.bold)
    static let swBody          = Font.custom("\(brand)-Regular",    size: 16).weight(.regular)
    static let swBodyEmphasis  = Font.custom("\(brand)-SemiBold",   size: 16).weight(.semibold)
    static let swCardTitle     = Font.custom("\(brand)-SemiBold",   size: 15).weight(.semibold)
    static let swFarePrice     = Font.custom("\(brand)-ExtraBold",  size: 19).weight(.heavy)
    static let swEyebrow       = Font.custom("\(brand)-Bold",       size: 13).weight(.bold)
    static let swMeta          = Font.custom("\(brand)-Medium",     size: 12).weight(.medium)
    static let swButton        = Font.custom("\(brand)-ExtraBold",  size: 16).weight(.heavy)
    static let swTab           = Font.custom("\(brand)-SemiBold",   size: 10).weight(.semibold)
    static let swClock         = Font.custom("\(brand)-ExtraBold",  size: 11).weight(.heavy)
    static let swClockLarge    = Font.custom("\(brand)-ExtraBold",  size: 34).weight(.heavy)
}

// Tabular figures for anything that updates (clock, fares, points)
extension View {
    func swTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Boarding Position Card

```swift
struct BoardingPositionCard: View {
    let group: String        // "A"
    let position: Int        // 23
    let boardingStarts: String  // "4:25 PM"
    let confirmation: String    // "K9F2Q2"

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text("BOARDING POSITION")
                .font(.swEyebrow)
                .tracking(1.2)
                .foregroundStyle(.white.opacity(0.78))

            HStack(alignment: .lastTextBaseline, spacing: 4) {
                Text(group)
                    .font(.swBoardingGroup)
                    .foregroundStyle(Color.swYellow)
                Text("\(position)")
                    .font(.swBoardingNum)
                    .foregroundStyle(.white)
                    .swTabularNumbers()
            }
            .padding(.vertical, 6)

            Text("Board in group \(group), position \(position)")
                .font(.swCardTitle)
                .foregroundStyle(.white.opacity(0.85))

            Divider().overlay(Color.white.opacity(0.18)).padding(.vertical, 16)

            HStack {
                footCol(label: "BOARDING STARTS", value: boardingStarts)
                Spacer()
                footCol(label: "CONFIRMATION", value: confirmation)
            }
        }
        .padding(22)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            LinearGradient(colors: [Color.swBlue, Color.swBlueDeep],
                           startPoint: .topLeading, endPoint: .bottomTrailing)
        )
        .clipShape(RoundedRectangle(cornerRadius: 20))
        .shadow(color: Color.swBlue.opacity(0.30), radius: 15, x: 0, y: 14)
    }

    private func footCol(label: String, value: String) -> some View {
        VStack(alignment: .leading, spacing: 2) {
            Text(label).font(.swMeta.weight(.semibold)).tracking(0.5)
                .foregroundStyle(.white.opacity(0.7))
            Text(value).font(.swSubsection).foregroundStyle(.white)
        }
    }
}
```

### Check-In Countdown

```swift
struct CheckInCountdown: View {
    var progress: Double        // 0…1 toward T-0
    let clock: String           // "23:41"
    var isOpen: Bool = false
    let onAction: () -> Void

    var body: some View {
        HStack(spacing: 14) {
            ZStack {
                Circle().stroke(Color.swDarkDivider, lineWidth: 5)
                Circle()
                    .trim(from: 0, to: progress)
                    .stroke(Color.swYellow, style: StrokeStyle(lineWidth: 5, lineCap: .round))
                    .rotationEffect(.degrees(-90))
                Text(clock).font(.swClock).foregroundStyle(Color.swYellow).swTabularNumbers()
            }
            .frame(width: 54, height: 54)

            VStack(alignment: .leading, spacing: 3) {
                Text(isOpen ? "Check-in is open" : "Check-in opens soon")
                    .font(.swCardTitle).foregroundStyle(Color.swDarkTextPrimary)
                Text("Opens 24 hrs before departure to lock your spot")
                    .font(.swMeta).foregroundStyle(Color.swDarkTextSecondary)
            }

            Spacer(minLength: 8)

            Button(action: onAction) {
                Text(isOpen ? "Check in" : "Notify")
                    .font(.swButton).foregroundStyle(Color.swOnYellow)
                    .padding(.horizontal, 16).padding(.vertical, 10)
                    .background(Color.swYellow, in: RoundedRectangle(cornerRadius: 8))
            }
        }
        .padding(18)
        .background(Color.swDarkSurface1, in: RoundedRectangle(cornerRadius: 16))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.swDarkDivider, lineWidth: 1))
    }
}
```

### Flight Strip

```swift
struct FlightStrip: View {
    let origin: String, destination: String   // "DAL", "MDW"
    let durationLabel: String                  // "Nonstop · 2h 35m"
    let flightLine: String                     // "WN 2418 · Mon, Jun 9"
    let gateLine: String                       // "Gate B14 · 4:55 PM"

    var body: some View {
        VStack(spacing: 10) {
            HStack {
                Text(origin).font(.swAirportCode).foregroundStyle(Color.swDarkTextPrimary)
                VStack(spacing: 4) {
                    RouteArrow().stroke(Color.swYellow, lineWidth: 1.6).frame(width: 60, height: 14)
                    Text(durationLabel.uppercased()).font(.system(size: 10, weight: .semibold))
                        .tracking(0.6).foregroundStyle(Color.swDarkTextSecondary)
                }.frame(maxWidth: .infinity)
                Text(destination).font(.swAirportCode).foregroundStyle(Color.swDarkTextPrimary)
            }
            HStack {
                Text(flightLine).font(.swMeta).foregroundStyle(Color.swDarkTextSecondary)
                Spacer()
                Text(gateLine).font(.swMeta).foregroundStyle(Color.swDarkTextSecondary)
            }
        }
        .padding(18)
        .background(Color.swDarkSurface1, in: RoundedRectangle(cornerRadius: 16))
    }
}

struct RouteArrow: Shape {
    func path(in r: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: 2, y: r.midY)); p.addLine(to: CGPoint(x: r.maxX - 12, y: r.midY))
        p.move(to: CGPoint(x: r.maxX - 16, y: r.midY - 4))
        p.addLine(to: CGPoint(x: r.maxX, y: r.midY))
        p.addLine(to: CGPoint(x: r.maxX - 16, y: r.midY + 4))
        return p
    }
}
```

### Wanna Get Away Fare Ladder

```swift
struct FareCard: View {
    let name: String, price: String, points: String
    let selected: Bool

    var body: some View {
        VStack(spacing: 8) {
            Text(name.uppercased()).font(.system(size: 11, weight: .bold)).tracking(0.4)
                .foregroundStyle(Color.swDarkTextSecondary)
            VStack(spacing: 2) {
                Text(price).font(.swFarePrice).foregroundStyle(Color.swDarkTextPrimary).swTabularNumbers()
                Text(points).font(.system(size: 11, weight: .semibold))
                    .foregroundStyle(Color.swDarkTextSecondary).swTabularNumbers()
            }
        }
        .frame(maxWidth: .infinity)
        .padding(.vertical, 14).padding(.horizontal, 12)
        .background(Color.swDarkSurface2, in: RoundedRectangle(cornerRadius: 12))
        .overlay(
            RoundedRectangle(cornerRadius: 12)
                .strokeBorder(selected ? Color.swYellow : Color.swDarkDivider, lineWidth: selected ? 2 : 1)
        )
        .background(selected ? Color.swYellow.opacity(0.10) : .clear, in: RoundedRectangle(cornerRadius: 12))
    }
}
```

### Rapid Rewards Points Pill

```swift
struct PointsPill: View {
    let text: String           // "54,820 pts"
    var tier: Bool = false      // true → A-List badge style

    var body: some View {
        HStack(spacing: 8) {
            Image(systemName: "star.fill")
                .font(.system(size: 13))
                .foregroundStyle(tier ? Color.swBlue : Color.swYellow)
            Text(text).font(.system(size: 14, weight: .heavy))
                .foregroundStyle(tier ? Color.swOnYellow : Color.swOnBlue)
                .swTabularNumbers()
        }
        .padding(.horizontal, 16).padding(.vertical, 10)
        .background(tier ? Color.swYellow : Color.swBlue, in: Capsule())
    }
}
```

### Primary Button

```swift
struct SWPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title).font(.swButton).foregroundStyle(Color.swOnYellow)
                .frame(maxWidth: .infinity).frame(height: 52)
                .background(pressed ? Color.swYellowPressed : Color.swYellow,
                            in: RoundedRectangle(cornerRadius: 8))
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }.onEnded { _ in pressed = false })
        .animation(.easeOut(duration: 0.12), value: pressed)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SWTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            BookView().tabItem { Label("Book", systemImage: "paperplane.fill") }
            TripsView().tabItem { Label("Trips", systemImage: "suitcase.fill") }
            RewardsView().tabItem { Label("Rapid Rewards", systemImage: "star.fill") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.swYellow)   // active = Warm Yellow; iOS draws no pill — matches Southwest
    }
}
```

## 5. Motion

```swift
// Boarding-position reveal — the payoff moment
withAnimation(.easeOut(duration: 0.28)) { revealScale = 1.0 }   // from 0.96
// group letter: .transition(.opacity.combined(with: .offset(y: 6)))
.sensoryFeedback(.success, trigger: didCheckIn)

// Check-in clock digit roll
.contentTransition(.numericText())                              // iOS 17 digit roll
withAnimation(.easeInOut(duration: 0.12)) { clock = nextClock }

// At T-0: CTA cross-fades Notify → full-width "Check in"
withAnimation(.easeOut(duration: 0.24)) { isOpen = true }

// Primary CTA press: scale 1.0 → 0.98 over 120ms + Yellow → Pressed Yellow
// Fare selection: border animates Yellow in over 150ms
withAnimation(.easeOut(duration: 0.15)) { selectedFare = idx }

// Bottom sheet present: 320ms easeOut + scrim fade (use .presentationDetents)
// Cancellation banner (the only red motion): slide down 220ms + warning haptic
.sensoryFeedback(.warning, trigger: isCancelled)

// Haptics
.sensoryFeedback(.success, trigger: positionRevealed)   // check-in / reveal
.sensoryFeedback(.impact(weight: .light), trigger: ctaTap)
.sensoryFeedback(.selection, trigger: selectedFare)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house.fill` | 22pt |
| Book (tab) | `paperplane.fill` | 22pt |
| Trips (tab) | `suitcase.fill` | 22pt |
| Rapid Rewards (tab) | `star.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Back | `chevron.left` | 17pt |
| Help / clock | `clock` | 18pt |
| Boarding pass | `barcode` / `qrcode` | — |
| Bags fly free | `bag.fill` | 20pt |
| Flight / route | `airplane` | 16pt |
| On time / success | `checkmark.circle.fill` | 16pt |
| Delay / warning | `exclamationmark.triangle.fill` | 16pt |
| Cancelled (red only) | `xmark.octagon.fill` | 16pt |
| Points / rewards | `star.fill` | 13pt |
| Companion Pass | `person.2.fill` | 16pt |
| Add a bag | `plus.circle` | 18pt |
| Calendar | `calendar` | 18pt |
| Notification bell | `bell.fill` | 16pt |

## 7. Dark Mode

```swift
struct SWTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.swDarkCanvas : Color.swWash)
            .foregroundStyle(scheme == .dark ? Color.swDarkTextPrimary : Color.swTextPrimary)
            .tint(.swYellow)  // CTA + active tab color is identical across themes
    }
}

extension View { func swTheme() -> some View { modifier(SWTheme()) } }
```

The dark canvas is navy `#0E1726` (a darkened Southwest Blue), never neutral black. Warm Yellow `#F9B612` and Bold Red `#E51D23` are identical across light and dark — the Heart must read the same in any light. Link blue brightens to `#2567E8` on dark for WCAG AA on the navy canvas. Cards use a 1pt `#2A3A57` border on dark since shadows read poorly on navy; the boarding card keeps its blue-glow shadow regardless.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback`, `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a fade for the clock roll)
- Bundle Southwest Sans via `Info.plist` `UIAppFonts` if licensed; otherwise ship Inter (SIL OFL) — keep the named ramp identical
- Tabular numerals: apply `.monospacedDigit()` to the countdown clock, fares, and points balance so digits never jitter
- Dynamic Type: scale screen titles, sections, body, card titles, fare prices; keep boarding group/number, countdown clock, tab labels, eyebrow labels, airport codes FIXED (layout-critical) — derive their sizes outside the scaled metric
- VoiceOver: announce the boarding card as "Boarding position: group A, position 23. Boarding starts 4:25 PM. Confirmation K9F2Q2." Announce the countdown as "Check-in opens in 23 hours 41 minutes." The check-in CTA must read as a button with a clear action label
- Color is never the only signal: pair the red cancellation state with an icon + text ("Cancelled"), never color alone
- Contrast: `#1A1A1A` on `#F9B612` and `#FFFFFF` on `#304CB2` both pass WCAG AA; on the navy canvas use `#EAF0FA` text and the brightened `#2567E8` link
- Reduce Motion: disable the position-reveal scale and the clock digit roll (use a crossfade); keep the success haptic
- Reduce Transparency: replace the tab-bar blur with a solid `#0E1726` / `#FFFFFF` fill
- Hit targets: primary CTA 52pt; tab icons 22pt glyph in ≥48pt; fare cards full-card tappable ≥64pt; icon buttons 34pt visible / 44pt hit
