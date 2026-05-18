# Grubhub (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grubhub's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the restaurant list and live order tracking.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (Interactive)
    static let ghRed         = Color(red: 0.965, green: 0.204, blue: 0.251) // #F63440
    static let ghRedPressed  = Color(red: 0.824, green: 0.125, blue: 0.169) // #D2202B
    static let ghOrange      = Color(red: 1.000, green: 0.502, blue: 0.000) // #FF8000
    static let ghOrangePressed = Color(red: 0.878, green: 0.431, blue: 0.000) // #E06E00

    // MARK: - Membership & Status
    static let ghPerksGold   = Color(red: 1.000, green: 0.722, blue: 0.110) // #FFB81C
    static let ghTrackBlue   = Color(red: 0.000, green: 0.627, blue: 0.875) // #00A0DF
    static let ghRatingGreen = Color(red: 0.094, green: 0.663, blue: 0.341) // #18A957

    // MARK: - Canvas & Surfaces (Light)
    static let ghCanvas        = Color.white                                   // #FFFFFF
    static let ghSurface1      = Color(red: 0.965, green: 0.965, blue: 0.957) // #F6F6F4
    static let ghSurface2      = Color(red: 0.925, green: 0.925, blue: 0.918) // #ECECEA
    static let ghDivider       = Color(red: 0.902, green: 0.902, blue: 0.902) // #E6E6E6

    // MARK: - Canvas & Surfaces (Dark)
    static let ghDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let ghDarkSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let ghDarkSurface2  = Color(red: 0.149, green: 0.149, blue: 0.149) // #262626
    static let ghDarkDivider   = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E

    // MARK: - Text
    static let ghTextPrimary    = Color(red: 0.106, green: 0.106, blue: 0.106) // #1B1B1B
    static let ghTextSecondary  = Color(red: 0.439, green: 0.439, blue: 0.439) // #707070
    static let ghTextTertiary   = Color(red: 0.627, green: 0.627, blue: 0.627) // #A0A0A0
    static let ghDarkTextPrimary = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2

    // MARK: - Semantic
    static let ghError       = Color(red: 0.941, green: 0.271, blue: 0.310) // #F0454F
    static let ghSuccess     = Color(red: 0.094, green: 0.663, blue: 0.341) // #18A957
}
```

## 2. Typography

Grubhub's brand face is **Graphik**; the recommended free substitute is **Inter**. Bundle Inter TTFs via `Info.plist` / `UIAppFonts`. Prices, fees and ETAs use tabular figures.

```swift
extension Font {
    static let ghScreenTitle    = Font.custom("Inter-Black",    size: 32) // 900
    static let ghRestaurantHero = Font.custom("Inter-ExtraBold", size: 26) // 800
    static let ghSection        = Font.custom("Inter-Bold",     size: 22)
    static let ghCardTitle      = Font.custom("Inter-Bold",     size: 18)
    static let ghRestaurantName = Font.custom("Inter-Bold",     size: 16)
    static let ghBody           = Font.custom("Inter-Medium",   size: 15)
    static let ghPrice          = Font.custom("Inter-Bold",     size: 15)
    static let ghMeta           = Font.custom("Inter-Regular",  size: 14)
    static let ghDeal           = Font.custom("Inter-SemiBold", size: 13)
    static let ghOverline       = Font.custom("Inter-Bold",     size: 12)
    static let ghButton         = Font.custom("Inter-Bold",     size: 16)
    static let ghTab            = Font.custom("Inter-SemiBold", size: 10)
    static let ghCaption        = Font.custom("Inter-Medium",   size: 12)
}

// Tabular numerals for prices / ETAs / quantities
extension View {
    func ghTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Restaurant Card

```swift
struct RestaurantCard: View {
    let name: String
    let cuisine: String
    let eta: String
    let fee: String
    let rating: Double
    let deal: String?
    let perksBadge: String?       // e.g. "GRUBHUB+ PERKS" or "20% OFF, UP TO $5"
    let imageName: String
    @State private var saved = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topLeading) {
                Image(imageName)
                    .resizable()
                    .aspectRatio(16/9, contentMode: .fill)
                    .clipped()
                    .clipShape(RoundedRectangle(cornerRadius: 14))

                if let perksBadge {
                    Text(perksBadge)
                        .font(.ghOverline)
                        .tracking(0.4)
                        .foregroundStyle(Color(red: 0.102, green: 0.071, blue: 0.024))
                        .padding(.horizontal, 8).padding(.vertical, 4)
                        .background(Color.ghPerksGold, in: RoundedRectangle(cornerRadius: 6))
                        .padding(10)
                }

                Button { saved.toggle() } label: {
                    Image(systemName: saved ? "heart.fill" : "heart")
                        .font(.system(size: 16, weight: .semibold))
                        .foregroundStyle(saved ? Color.ghRed : .white)
                        .frame(width: 30, height: 30)
                        .background(Color.black.opacity(0.45), in: Circle())
                }
                .frame(maxWidth: .infinity, alignment: .trailing)
                .padding(10)
            }

            HStack {
                Text(name).font(.ghRestaurantName).foregroundStyle(Color.ghTextPrimary)
                Spacer()
                HStack(spacing: 4) {
                    Image(systemName: "star.fill").font(.system(size: 12)).foregroundStyle(Color.ghRatingGreen)
                    Text(String(format: "%.1f", rating)).font(.ghDeal).foregroundStyle(Color.ghTextPrimary)
                }
            }
            .padding(.top, 10)

            Text("\(cuisine) · \(eta) · \(fee)")
                .font(.ghMeta).foregroundStyle(Color.ghTextSecondary)
                .padding(.top, 4)

            if let deal {
                Text(deal).font(.ghDeal).foregroundStyle(Color.ghOrange).padding(.top, 5)
            }
        }
        .padding(.bottom, 22)
    }
}
```

### Cuisine Chip Rail

```swift
struct CuisineChips: View {
    let items: [String]
    @State private var selected = 0

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 10) {
                ForEach(Array(items.enumerated()), id: \.offset) { i, label in
                    Text(label)
                        .font(.ghDeal)
                        .foregroundStyle(i == selected ? .white : Color.ghTextSecondary)
                        .padding(.horizontal, 14)
                        .frame(height: 34)
                        .background(i == selected ? Color.ghRed : Color.ghSurface1, in: Capsule())
                        .overlay(Capsule().strokeBorder(i == selected ? Color.ghRed : Color.ghDivider, lineWidth: 0.5))
                        .onTapGesture { withAnimation(.easeOut(duration: 0.15)) { selected = i } }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

### Quantity Stepper

```swift
struct QuantityStepper: View {
    @Binding var quantity: Int

    var body: some View {
        HStack(spacing: 14) {
            stepControl("minus") { if quantity > 1 { quantity -= 1 } }
                .opacity(quantity > 1 ? 1 : 0.35)
            Text("\(quantity)")
                .font(.ghPrice).ghTabular()
                .foregroundStyle(Color.ghTextPrimary)
                .frame(minWidth: 18)
                .contentTransition(.numericText())
            stepControl("plus") { quantity += 1 }
        }
        .padding(.horizontal, 8).padding(.vertical, 6)
        .background(Color.ghSurface1, in: Capsule())
        .overlay(Capsule().strokeBorder(Color.ghDivider, lineWidth: 0.5))
        .sensoryFeedback(.selection, trigger: quantity)
    }

    private func stepControl(_ symbol: String, _ action: @escaping () -> Void) -> some View {
        Button(action: action) {
            Image(systemName: symbol)
                .font(.system(size: 16, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 30, height: 30)
                .background(Color.ghRed, in: Circle())
        }
    }
}
```

### Live Order Tracking Banner

```swift
struct OrderTrackingBanner: View {
    let eta: String
    let restaurant: String
    let status: String
    let progress: Double         // 0...1
    @State private var pulse = false

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(spacing: 8) {
                Circle()
                    .fill(Color.ghTrackBlue)
                    .frame(width: 8, height: 8)
                    .overlay(Circle().stroke(Color.ghTrackBlue.opacity(0.18), lineWidth: 4))
                    .scaleEffect(pulse ? 1.2 : 1.0)
                Text(eta).font(.ghDeal).foregroundStyle(Color.ghDarkTextPrimary)
                Spacer()
                Text(restaurant).font(.ghCaption).foregroundStyle(Color.ghTextSecondary)
            }
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.white.opacity(0.12))
                    Capsule().fill(Color.ghTrackBlue).frame(width: geo.size.width * progress)
                }
            }
            .frame(height: 4)
            Text(status).font(.ghCaption.weight(.semibold)).foregroundStyle(Color.ghTrackBlue)
        }
        .padding(16)
        .background(
            LinearGradient(colors: [Color(red: 0.067, green: 0.212, blue: 0.247),
                                    Color(red: 0.055, green: 0.165, blue: 0.200)],
                           startPoint: .topLeading, endPoint: .bottomTrailing),
            in: RoundedRectangle(cornerRadius: 16)
        )
        .onAppear { withAnimation(.easeInOut(duration: 1.4).repeatForever()) { pulse = true } }
    }
}
```

### Order Tracking Timeline

```swift
struct TrackingTimeline: View {
    struct Step: Identifiable { let id = UUID(); let title: String; let sub: String; let state: State
        enum State { case done, active, pending } }
    let steps: [Step]

    var body: some View {
        VStack(spacing: 0) {
            ForEach(Array(steps.enumerated()), id: \.element.id) { idx, step in
                HStack(alignment: .top, spacing: 12) {
                    VStack(spacing: 0) {
                        Circle()
                            .fill(nodeFill(step.state))
                            .frame(width: 16, height: 16)
                            .overlay(Circle().stroke(nodeStroke(step.state), lineWidth: 3))
                            .overlay(step.state == .active ?
                                Circle().stroke(Color.ghRed.opacity(0.22), lineWidth: 4).scaleEffect(1.6) : nil)
                        if idx < steps.count - 1 {
                            Rectangle()
                                .fill(step.state == .done ? Color.ghTrackBlue : Color.ghDivider)
                                .frame(width: 3, minHeight: 26)
                        }
                    }
                    VStack(alignment: .leading, spacing: 2) {
                        Text(step.title).font(.ghMeta.weight(.bold)).foregroundStyle(Color.ghTextPrimary)
                        Text(step.sub).font(.ghCaption).foregroundStyle(Color.ghTextSecondary)
                    }
                    .padding(.bottom, 22)
                    Spacer()
                }
            }
        }
    }

    private func nodeFill(_ s: Step.State) -> Color {
        switch s { case .done: return .ghTrackBlue; case .active: return .ghRed; case .pending: return .ghCanvas }
    }
    private func nodeStroke(_ s: Step.State) -> Color {
        switch s { case .done: return .ghTrackBlue; case .active: return .ghRed; case .pending: return .ghDivider }
    }
}
```

### Primary Button

```swift
struct GHPrimaryButton: View {
    let title: String
    let action: () -> Void
    var style: Style = .red
    enum Style { case red, orange }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.ghButton)
                .foregroundStyle(style == .red ? .white : Color(red: 0.102, green: 0.071, blue: 0.024))
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(style == .red ? Color.ghRed : Color.ghOrange, in: Capsule())
        }
        .buttonStyle(.plain)
    }
}
// Checkout variant: GHPrimaryButton(title: "Go to checkout · $42.18", action: {})
```

## 4. Bottom Tab Bar

```swift
struct GrubhubTabView: View {
    var body: some View {
        TabView {
            DeliveryView().tabItem { Label("Delivery", systemImage: "house.fill") }
            SearchView().tabItem   { Label("Search",   systemImage: "magnifyingglass") }
            OrdersView().tabItem   { Label("Orders",   systemImage: "list.bullet.rectangle") }
            SavedView().tabItem    { Label("Saved",    systemImage: "heart") }
            AccountView().tabItem  { Label("Account",  systemImage: "person.crop.circle") }
        }
        .tint(.ghRed)   // active tab is Grubhub Red
    }
}
```

## 5. Motion

```swift
// Add-to-bag badge bump
withAnimation(.spring(response: 0.2, dampingFraction: 0.5)) { badgeScale = 1.25 }
// then settle back to 1.0

// Quantity stepper number change
.contentTransition(.numericText())
.sensoryFeedback(.selection, trigger: quantity)

// Tracking progress fill
withAnimation(.easeInOut(duration: 0.6)) { progress = liveProgress }

// Tracking dot pulse
withAnimation(.easeInOut(duration: 1.4).repeatForever()) { pulse = true }

// Step timeline advance (stage completes)
withAnimation(.easeOut(duration: 0.25)) { activeIndex += 1 }
.sensoryFeedback(.impact(weight: .medium), trigger: activeIndex)

// Card → detail: matchedGeometryEffect on the photo, 320ms push
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Delivery (tab) | `house` / `house.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Orders (tab) | `list.bullet.rectangle` | 22pt |
| Saved (tab) | `heart` / `heart.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Saved heart (card) | `heart` / `heart.fill` | 16pt |
| Rating star | `star.fill` | 12pt |
| Address chevron | `chevron.down` | 12pt |
| Bag / cart | `bag` / `bag.fill` | 16pt |
| Stepper minus | `minus` | 16pt |
| Stepper plus | `plus` | 16pt |
| Back | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Driver call | `phone.fill` | 16pt |
| Driver message | `message.fill` | 16pt |
| Tracking pin | `mappin.circle.fill` | 24pt |
| Promo / deal | `tag.fill` | 14pt |

## 7. Dark Mode

```swift
struct GrubhubTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.ghDarkCanvas : Color.ghCanvas)
            .foregroundStyle(scheme == .dark ? Color.ghDarkTextPrimary : Color.ghTextPrimary)
    }
}

extension View {
    func grubhubTheme() -> some View { modifier(GrubhubTheme()) }
}
```

Grubhub Red, Orange, Perks Gold, Track Blue and Rating Green are identical across light and dark — only canvas, surfaces, dividers and text invert. Food photography is never dimmed. Floating surfaces add a 0.5pt `ghDarkDivider` border in dark mode as the elevation cue (shadows fade on dark).

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a manual cross-fade on iOS 16)
- Bundle Inter TTFs via `Info.plist` (`UIAppFonts`) — Inter is SIL OFL, free to distribute. Substitute for Graphik (proprietary)
- Dynamic Type: support on screen titles, restaurant names, body and prices; keep Perks badges, deal lines, tab labels, chip text and the ETA dot label at FIXED sizes
- Tabular numerals: apply `.monospacedDigit()` to all prices, fees, ETAs and quantity counters so they don't jitter
- VoiceOver: label restaurant cards as "{name}, {rating} stars, {cuisine}, {eta}, {fee}"; the saved heart is a toggle ("Save {name}" / "Saved"); the tracking timeline announces each step's state ("Driver picking up, in progress")
- The order-tracking banner should post a VoiceOver announcement when the stage advances ("On the way, arriving 7:45 to 7:55 PM")
- Color contrast: `#1B1B1B` on `#FFFFFF` passes WCAG AAA; orange deal text `#FF8000` on white passes AA at 13pt 700 (large/bold); white on `#F63440` passes AA for button labels
- Reduce Motion: disable the tracking dot pulse and the timeline slide (cross-fade instead); keep the progress fill (it conveys state) but make it instant
- Dark mode: invert to the `ghDark*` palette — warm `#121212`, NOT true black; add a 0.5pt divider border to floating commerce surfaces (bag sheet, sticky checkout, tracking banner) since shadows are barely visible on dark
