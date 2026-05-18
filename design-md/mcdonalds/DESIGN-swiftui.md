# McDonald's (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates McDonald's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views (the MyMcDonald's Rewards card, deal tile, order-mode selector, the elevated Order FAB tab bar).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let mcdYellow        = Color(red: 1.000, green: 0.780, blue: 0.173) // #FFC72C
    static let mcdYellowPressed = Color(red: 0.902, green: 0.690, blue: 0.082) // #E6B015
    static let mcdRed           = Color(red: 0.855, green: 0.161, blue: 0.110) // #DA291C
    static let mcdRedPressed    = Color(red: 0.718, green: 0.122, blue: 0.078) // #B71F14
    static let mcdDarkRed       = Color(red: 0.690, green: 0.129, blue: 0.055) // #B0210E

    // MARK: - Text on Yellow (mandatory — never white)
    static let mcdOnYellow = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A

    // MARK: - Canvas & Surfaces (Light)
    static let mcdCanvas       = Color.white                                    // #FFFFFF
    static let mcdSurface1L    = Color(red: 0.957, green: 0.957, blue: 0.961)  // #F4F4F5
    static let mcdSurface2L    = Color(red: 0.918, green: 0.918, blue: 0.922)  // #EAEAEB
    static let mcdDividerL     = Color(red: 0.890, green: 0.890, blue: 0.898)  // #E3E3E5

    // MARK: - Canvas & Surfaces (Dark)
    static let mcdDarkCanvas   = Color(red: 0.071, green: 0.071, blue: 0.071)  // #121212
    static let mcdDarkSurface1 = Color(red: 0.110, green: 0.110, blue: 0.118)  // #1C1C1E
    static let mcdDarkSurface2 = Color(red: 0.165, green: 0.165, blue: 0.165)  // #2A2A2A
    static let mcdDarkDivider  = Color(red: 0.173, green: 0.173, blue: 0.180)  // #2C2C2E

    // MARK: - Text
    static let mcdTextPrimaryL   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let mcdTextSecondaryL = Color(red: 0.361, green: 0.361, blue: 0.361) // #5C5C5C
    static let mcdTextTertiaryL  = Color(red: 0.541, green: 0.541, blue: 0.541) // #8A8A8A
    static let mcdTextPrimaryD   = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let mcdTextSecondaryD = Color(red: 0.627, green: 0.627, blue: 0.627) // #A0A0A0

    // MARK: - Semantic
    static let mcdSuccess = Color(red: 0.169, green: 0.541, blue: 0.243) // #2B8A3E
    static let mcdWarning = Color(red: 0.902, green: 0.655, blue: 0.000) // #E6A700
    static let mcdInfo    = Color(red: 0.180, green: 0.427, blue: 0.706) // #2E6DB4
}
```

## 2. Typography

McDonald's uses proprietary **Speedee**. Bundle the licensed TTFs via `Info.plist` / `UIAppFonts`; fall back to SF Pro Rounded / SF Pro. Numerals are tabular for points, prices, timers.

```swift
extension Font {
    static let mcdScreenTitle   = Font.custom("Speedee-Bold",    size: 32).weight(.black)
    static let mcdRewardsNumber = Font.custom("Speedee-Bold",    size: 28).weight(.black)
    static let mcdHero          = Font.custom("Speedee-Bold",    size: 26).weight(.heavy)
    static let mcdGreeting      = Font.custom("Speedee-Bold",    size: 22).weight(.heavy)
    static let mcdSection       = Font.custom("Speedee-Bold",    size: 19).weight(.heavy)
    static let mcdCardTitle     = Font.custom("Speedee-Bold",    size: 18).weight(.heavy)
    static let mcdBody          = Font.custom("Speedee-Regular", size: 16).weight(.regular)
    static let mcdDealTitle     = Font.custom("Speedee-Medium",  size: 15).weight(.semibold)
    static let mcdPrice         = Font.custom("Speedee-Bold",    size: 14).weight(.bold)
    static let mcdMeta          = Font.custom("Speedee-Regular", size: 14).weight(.regular)
    static let mcdBadge         = Font.custom("Speedee-Bold",    size: 12).weight(.heavy)
    static let mcdButton        = Font.custom("Speedee-Bold",    size: 15).weight(.heavy)
    static let mcdCaption       = Font.custom("Speedee-Regular", size: 12).weight(.regular)
    static let mcdTab           = Font.custom("Speedee-Bold",    size: 10).weight(.bold)
    static let mcdModeLabel     = Font.custom("Speedee-Bold",    size: 11).weight(.bold)
}

extension View {
    func mcdTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### MyMcDonald's Rewards Card

```swift
struct RewardsCard: View {
    let points: Int            // 2150
    let progress: Double       // 0.62
    let pointsToNext: Int      // 350
    let nextReward: String     // "a free McNuggets"

    @State private var animatedFill: CGFloat = 0
    @State private var shownPoints: Int = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text("MYMCDONALD'S REWARDS")
                .font(.mcdBadge)
                .foregroundStyle(Color.mcdTextSecondaryD)

            HStack(alignment: .firstTextBaseline, spacing: 6) {
                Text("\(shownPoints)")
                    .font(.mcdRewardsNumber)
                    .foregroundStyle(Color.mcdYellow)
                    .mcdTabularNumbers()
                Text("points")
                    .font(.mcdPrice)
                    .foregroundStyle(Color.mcdTextSecondaryD)
            }
            .padding(.top, 6)

            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color(white: 0.23)).frame(height: 8)
                    Capsule()
                        .fill(LinearGradient(colors: [.mcdYellow, Color(red: 1, green: 0.843, blue: 0.369)],
                                             startPoint: .leading, endPoint: .trailing))
                        .frame(width: geo.size.width * animatedFill, height: 8)
                }
            }
            .frame(height: 8)
            .padding(.vertical, 12)

            (Text("Just ") + Text("\(pointsToNext) points").bold().foregroundColor(.mcdTextPrimaryD)
                + Text(" away from \(nextReward)"))
                .font(.mcdCaption)
                .foregroundStyle(Color.mcdTextSecondaryD)
        }
        .padding(16)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.mcdDarkSurface1)
                .overlay(alignment: .topTrailing) {
                    Circle()
                        .fill(RadialGradient(colors: [Color.mcdYellow.opacity(0.18), .clear],
                                             center: .center, startRadius: 0, endRadius: 70))
                        .frame(width: 130, height: 130)
                        .offset(x: 30, y: -30)
                }
                .clipShape(RoundedRectangle(cornerRadius: 16))
        )
        .onAppear {
            withAnimation(.easeOut(duration: 0.6)) { animatedFill = progress }
            // roll the points number up
            let steps = 30
            for i in 0...steps {
                DispatchQueue.main.asyncAfter(deadline: .now() + Double(i) * 0.02) {
                    shownPoints = points * i / steps
                }
            }
        }
    }
}
```

### Deal Tile

```swift
struct DealTile: View {
    let imageURL: String
    let flag: String          // "DEAL" / "FREE" / "NEW"
    let name: String
    let offer: String
    let pointsAlt: String     // "1,900"

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topLeading) {
                AsyncImage(url: URL(string: imageURL)) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Rectangle().fill(Color.mcdDarkSurface2) }
                    .frame(height: 96)
                    .clipped()

                Text(flag)
                    .font(.mcdBadge)
                    .foregroundStyle(.white)
                    .padding(.vertical, 3).padding(.horizontal, 7)
                    .background(RoundedRectangle(cornerRadius: 6).fill(Color.mcdRed))
                    .padding(8)
            }

            VStack(alignment: .leading, spacing: 0) {
                Text(name).font(.mcdCardTitle).foregroundStyle(Color.mcdTextPrimaryD)
                Text(offer).font(.mcdCaption).foregroundStyle(Color.mcdTextSecondaryD).padding(.top, 3)
                HStack(spacing: 4) {
                    Image(systemName: "star.fill").font(.system(size: 11))
                    Text("or \(pointsAlt) pts").font(.mcdBadge)
                }
                .foregroundStyle(Color.mcdYellow)
                .padding(.top, 8)
            }
            .padding(.horizontal, 12)
            .padding(.top, 10).padding(.bottom, 14)
        }
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.mcdDarkSurface1))
        .overlay(RoundedRectangle(cornerRadius: 16).stroke(Color.mcdDarkDivider, lineWidth: 1))
        .clipShape(RoundedRectangle(cornerRadius: 16))
    }
}
```

### Order-Mode Selector

```swift
struct OrderModeSelector: View {
    enum Mode: String, CaseIterable { case pickup = "Pickup", curbside = "Curbside",
                                            driveThru = "Drive Thru", delivery = "Delivery" }
    @State private var selected: Mode = .pickup

    private func icon(_ m: Mode) -> String {
        switch m {
        case .pickup:    return "bag"
        case .curbside:  return "car"
        case .driveThru: return "car.side"
        case .delivery:  return "cart"
        }
    }

    var body: some View {
        HStack(spacing: 8) {
            ForEach(Mode.allCases, id: \.self) { m in
                let active = m == selected
                VStack(spacing: 6) {
                    Image(systemName: icon(m))
                        .font(.system(size: 22, weight: .semibold))
                        .foregroundStyle(active ? Color.mcdYellow : Color.mcdTextPrimaryD)
                    Text(m.rawValue)
                        .font(.mcdModeLabel)
                        .foregroundStyle(active ? Color.mcdYellow : Color.mcdTextPrimaryD)
                }
                .frame(maxWidth: .infinity)
                .padding(.vertical, 12)
                .background(
                    RoundedRectangle(cornerRadius: 12)
                        .fill(active ? Color.mcdYellow.opacity(0.08) : .clear)
                        .overlay(RoundedRectangle(cornerRadius: 12)
                            .stroke(active ? Color.mcdYellow : Color.mcdDarkDivider, lineWidth: 1.5))
                )
                .onTapGesture {
                    withAnimation(.easeOut(duration: 0.15)) { selected = m }
                    UIImpactFeedbackGenerator(style: .light).impactOccurred()
                }
            }
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.mcdDarkSurface1))
    }
}
```

### Buttons

```swift
struct McdPrimaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .font(.mcdButton)
            .foregroundStyle(Color.mcdOnYellow) // mandatory — never white
            .frame(maxWidth: .infinity)
            .padding(.vertical, 14)
            .background(Capsule().fill(configuration.isPressed ? Color.mcdYellowPressed : Color.mcdYellow))
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
    }
}

struct McdSecondaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .font(.mcdButton)
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .padding(.vertical, 14)
            .background(Capsule().fill(configuration.isPressed ? Color.mcdRedPressed : Color.mcdRed))
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
    }
}
```

## 4. Bottom Tab Bar (with elevated Order FAB)

```swift
struct McdTabView: View {
    @State private var selection = 0
    @State private var fabPulse = false

    var body: some View {
        ZStack(alignment: .bottom) {
            TabView(selection: $selection) {
                HomeView().tag(0)
                RewardsView().tag(1)
                Color.clear.tag(2)          // placeholder under the FAB
                MenuView().tag(3)
                MoreView().tag(4)
            }
            .tint(.mcdYellow)

            // Elevated center Order FAB
            Button {
                selection = 2
                withAnimation(.easeInOut(duration: 0.13).repeatCount(2, autoreverses: true)) { fabPulse.toggle() }
            } label: {
                VStack(spacing: 2) {
                    ZStack {
                        Circle().fill(Color.mcdYellow)
                            .frame(width: 50, height: 50)
                            .shadow(color: Color.mcdYellow.opacity(0.40), radius: 16, y: 6)
                        Image(systemName: "cart.fill")
                            .font(.system(size: 22, weight: .bold))
                            .foregroundStyle(Color.mcdOnYellow)
                    }
                    .scaleEffect(fabPulse ? 1.08 : 1.0)
                    Text("Order").font(.mcdTab).foregroundStyle(Color.mcdYellow)
                }
            }
            .offset(y: -6)
        }
    }
}
```

## 5. Motion

```swift
// Add to Mobile Order — fly into FAB + pulse
withAnimation(.easeInOut(duration: 0.13).repeatCount(2, autoreverses: true)) { fabPulse.toggle() }
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Rewards progress fill + number roll
withAnimation(.easeOut(duration: 0.6)) { animatedFill = progress }   // see RewardsCard.onAppear

// Order-mode select
withAnimation(.easeOut(duration: 0.15)) { selected = mode }
UIImpactFeedbackGenerator(style: .light).impactOccurred()

// Deal tile → detail (shared element ~300ms)
.matchedGeometryEffect(id: deal.id, in: namespace)
withAnimation(.easeOut(duration: 0.3)) { showDetail = true }

// Order READY badge flip
withAnimation(.spring(response: 0.3, dampingFraction: 0.5)) { ready = true }
UINotificationFeedbackGenerator().notificationOccurred(.success)

// Bottom sheet (curbside / customise)
.presentationDetents([.medium, .large])
.presentationCornerRadius(20)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Rewards (tab) | `star` / `star.fill` | 22pt |
| Order (center FAB) | `cart.fill` | 22pt |
| Menu (tab) | `square.grid.2x2` / `.fill` | 22pt |
| More (tab) | `person.crop.circle` | 22pt |
| Notification bell | `bell` / `bell.fill` | 18pt |
| Pickup | `bag` | 22pt |
| Curbside | `car` | 22pt |
| Drive Thru | `car.side` | 22pt |
| Delivery | `cart` | 22pt |
| Rewards star | `star.fill` | 11pt |
| Search | `magnifyingglass` | 18pt |
| Back | `chevron.left` | 20pt |
| Store / location | `mappin.and.ellipse` | 18pt |
| Order ready | `checkmark.circle.fill` | 20pt |
| Promo / coupon | `tag.fill` | 16pt |
| Close | `xmark` | 18pt |

## 7. Dark Mode

```swift
struct McdTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.mcdDarkCanvas : Color.mcdCanvas)
            .foregroundStyle(scheme == .dark ? Color.mcdTextPrimaryD : Color.mcdTextPrimaryL)
    }
}

extension View {
    func mcdTheme() -> some View { modifier(McdTheme()) }
}
```

Dark canvas is `#121212` (near-black, never pure black). Cards lift to `#1C1C1E`. Golden Arches Yellow `#FFC72C` and McDonald's Red `#DA291C` are identical across themes. **Text on yellow is always `#1A1A1A`** in both modes — this is a hard brand rule, not a theme decision. Food photography stays full-saturation; the Order FAB's yellow glow is more prominent on dark. Bottom sheets gain a 1pt `#2C2C2E` top border as the dark elevation cue.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`presentationCornerRadius` requires iOS 16.4; spring `response:` is iOS 17 — fall back to `.spring()` defaults on 16)
- Bundle the licensed **Speedee** family via `Info.plist`; fall back to SF Pro Rounded then SF Pro
- Dynamic Type: support on screen titles, section headers, body, deal copy; keep the rewards number, deal flags, status badges, tab labels, and mode-tile labels FIXED (layout-critical)
- VoiceOver: announce the rewards card as "MyMcDonald's Rewards, {points} points, {pointsToNext} points from {reward}"; deal tiles as "{name}, {offer}, or {pts} points"; the Order FAB as "Order, button"; order-mode tiles as "{mode}, {selected/not selected}"
- The Order FAB needs a ≥ 56pt hit area though the visual is 50pt; pill CTAs ≥ 48pt tall
- Color contrast: `#1A1A1A` on `#FFC72C` passes WCAG AA (this is exactly why white-on-yellow is forbidden); white on `#DA291C` passes AA; `#F2F2F2` on `#121212` passes AA
- Use `monospacedDigit()` on the points balance, prices, and the order timer so they don't jitter while rolling
- Reduce Motion: disable the points-roll and progress-fill animation (set final values directly); replace fly-to-FAB with a simple badge increment; keep the READY flip as an instant color change
- Dark mode: invert per the `mcdDark*` palette; never dim food imagery; keep `#1A1A1A` on yellow; add the 1pt divider border to floating sheets
