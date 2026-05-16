# Uber Eats (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Uber Eats' visual language into paste-ready SwiftUI code: `Color` extensions (light + dark), `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light — default)
    static let ueCanvas    = Color.white                                       // #FFFFFF
    static let ueSurface   = Color(red: 0.953, green: 0.953, blue: 0.953)      // #F3F3F3
    static let ueSurface2  = Color(red: 0.933, green: 0.933, blue: 0.933)      // #EEEEEE
    static let ueDivider   = Color(red: 0.910, green: 0.910, blue: 0.910)      // #E8E8E8

    // MARK: - Text (Light)
    static let ueTextPrimary   = Color.black                                   // #000000
    static let ueTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.420)  // #6B6B6B
    static let ueTextTertiary  = Color(red: 0.651, green: 0.651, blue: 0.651)  // #A6A6A6

    // MARK: - Dark Mode
    static let ueDarkCanvas    = Color.black                                   // #000000
    static let ueDarkSurface   = Color(red: 0.110, green: 0.110, blue: 0.118)  // #1C1C1E
    static let ueDarkSurface2  = Color(red: 0.173, green: 0.173, blue: 0.180)  // #2C2C2E

    // MARK: - Brand (identical in light & dark)
    static let ueGreen        = Color(red: 0.024, green: 0.757, blue: 0.404)   // #06C167
    static let ueGreenPressed = Color(red: 0.020, green: 0.651, blue: 0.345)   // #05A658
    static let ueGreenTint    = Color(red: 0.906, green: 0.973, blue: 0.937)   // #E7F8EF
    static let ueErrorRed     = Color(red: 0.882, green: 0.098, blue: 0.000)   // #E11900
    static let ueBusyAmber    = Color(red: 1.000, green: 0.541, blue: 0.000)   // #FF8A00
}

// Dynamic (light/dark) helpers — use these in views
extension Color {
    static func ueBackground(_ s: ColorScheme) -> Color { s == .dark ? .ueDarkCanvas  : .ueCanvas }
    static func ueCard(_ s: ColorScheme)       -> Color { s == .dark ? .ueDarkSurface : .ueSurface }
    static func ueText(_ s: ColorScheme)       -> Color { s == .dark ? .white         : .black }
}
```

## 2. Typography

Uber Move is a licensed brand typeface. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to Inter (or `.system`) — a tight geometric grotesque is the closest free substitute.

```swift
extension Font {
    static let ueRestaurantHeader = Font.custom("UberMove-Bold",   size: 30).weight(.bold)
    static let ueTitleLarge       = Font.custom("UberMove-Bold",   size: 28).weight(.bold)
    static let ueSectionHeader    = Font.custom("UberMove-Bold",   size: 22).weight(.bold)
    static let ueSubsection       = Font.custom("UberMove-Bold",   size: 20).weight(.bold)
    static let ueRestaurantName   = Font.custom("UberMove-Bold",   size: 17).weight(.bold)
    static let ueMenuItemTitle    = Font.custom("UberMove-Medium", size: 16).weight(.medium)
    static let ueBody             = Font.custom("UberMove-Regular", size: 15).weight(.regular)
    static let uePrice            = Font.custom("UberMove-Bold",   size: 16).weight(.bold)
    static let ueMeta             = Font.custom("UberMove-Regular", size: 14).weight(.regular)
    static let uePill             = Font.custom("UberMove-Medium", size: 14).weight(.medium)
    static let ueCaption          = Font.custom("UberMove-Regular", size: 13).weight(.regular)
    static let ueLabelUpper       = Font.custom("UberMove-Bold",   size: 11).weight(.bold)
    static let ueButton           = Font.custom("UberMove-Bold",   size: 16).weight(.bold)
    static let ueTab              = Font.custom("UberMove-Medium", size: 10).weight(.medium)
    static let ueCartBadge        = Font.custom("UberMove-Bold",   size: 12).weight(.bold)
}

extension Font {
    static func ue(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Primary Green Button

```swift
struct UEPrimaryButton: View {
    let title: String
    var trailing: String? = nil   // e.g. price "$12.49"
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack {
                Text(title).font(.ueButton).tracking(0.2)
                if let trailing { Spacer(); Text(trailing).font(.ueButton) }
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .padding(.horizontal, trailing == nil ? 0 : 20)
            .background(RoundedRectangle(cornerRadius: 12).fill(Color.ueGreen))
        }
        .sensoryFeedback(.impact(weight: .light), trigger: title)
        .buttonStyle(UEPressableStyle(pressedScale: 0.98))
    }
}

struct UEPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Photo-First Restaurant Card

```swift
struct UERestaurantCard: View {
    let name: String
    let rating: String   // "4.8"
    let eta: String      // "25 min"
    let fee: String      // "$0.99 Delivery Fee"
    var freeDelivery: Bool = false
    let photo: Image
    @Environment(\.colorScheme) private var scheme
    @State private var pressed = false

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ZStack(alignment: .topLeading) {
                photo
                    .resizable()
                    .aspectRatio(16/9, contentMode: .fill)
                    .frame(maxWidth: .infinity)
                    .frame(height: 180)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
                    .scaleEffect(pressed ? 0.98 : 1)
                if freeDelivery {
                    Text("$0 Delivery Fee")
                        .font(.ueCaption.weight(.bold))
                        .foregroundStyle(.white)
                        .padding(.horizontal, 8).padding(.vertical, 4)
                        .background(Capsule().fill(Color.ueGreen))
                        .padding(10)
                }
                HStack { Spacer()
                    Image(systemName: "heart")
                        .font(.system(size: 16, weight: .bold))
                        .foregroundStyle(.black)
                        .frame(width: 36, height: 36)
                        .background(Circle().fill(.white.opacity(0.9)))
                }.padding(10)
            }
            Text(name).font(.ueRestaurantName).foregroundStyle(Color.ueText(scheme))
            HStack(spacing: 6) {
                Image(systemName: "star.fill").font(.system(size: 12))
                    .foregroundStyle(Color.ueText(scheme))   // monochrome, not yellow
                Text(rating).font(.ueMeta).foregroundStyle(Color.ueText(scheme))
                Text("· \(eta) ·").font(.ueMeta).foregroundStyle(.ueTextSecondary)
                Text(fee).font(.ueMeta).foregroundStyle(freeDelivery ? Color.ueGreen : .ueTextSecondary)
            }
        }
        .onLongPressGesture(minimumDuration: 0, pressing: { p in
            withAnimation(.easeOut(duration: 0.15)) { pressed = p }
        }, perform: {})
    }
}
```

### Sticky Cart Bar

```swift
struct UEStickyCartBar: View {
    let count: Int
    let total: String
    let onView: () -> Void

    var body: some View {
        Button(action: onView) {
            HStack {
                Text("\(count)")
                    .font(.ueCartBadge).foregroundStyle(Color.ueGreen)
                    .frame(width: 28, height: 28)
                    .background(Circle().fill(.white))
                Spacer()
                Text("View cart").font(.ueButton).foregroundStyle(.white)
                Spacer()
                Text(total).font(.ueButton).foregroundStyle(.white)
                    .contentTransition(.numericText())
            }
            .padding(.horizontal, 16)
            .frame(height: 56)
            .background(RoundedRectangle(cornerRadius: 12).fill(Color.ueGreen))
            .padding(.horizontal, 16)
            .shadow(color: .black.opacity(0.12), radius: 12, y: -2)
        }
        .buttonStyle(UEPressableStyle())
        .transition(.move(edge: .bottom).combined(with: .opacity))
    }
}
```

### Menu Item Row + Cart-Count Bump

```swift
struct UEMenuItemRow: View {
    let name: String
    let desc: String
    let price: String
    let photo: Image
    let onAdd: () -> Void
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            VStack(alignment: .leading, spacing: 4) {
                Text(name).font(.ueMenuItemTitle).foregroundStyle(Color.ueText(scheme))
                Text(desc).font(.ueMeta).foregroundStyle(.ueTextSecondary).lineLimit(2)
                Text(price).font(.ueBody).foregroundStyle(Color.ueText(scheme)).padding(.top, 2)
            }
            Spacer(minLength: 12)
            ZStack(alignment: .bottomTrailing) {
                photo.resizable().aspectRatio(1, contentMode: .fill)
                    .frame(width: 88, height: 88)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
                Button(action: onAdd) {
                    Image(systemName: "plus")
                        .font(.system(size: 16, weight: .bold))
                        .foregroundStyle(.white)
                        .frame(width: 28, height: 28)
                        .background(Circle().fill(Color.ueGreen))
                }
                .sensoryFeedback(.impact(weight: .light), trigger: price)
                .offset(x: 6, y: 6)
            }
        }
        .padding(.vertical, 12)
        .overlay(Divider().background(Color.ueDivider), alignment: .bottom)
    }
}

// Reusable cart-count "bump"
struct CartCountBadge: View {
    let count: Int
    @State private var bump = false
    var body: some View {
        Text("\(count)")
            .font(.ueCartBadge).foregroundStyle(.white)
            .frame(width: 18, height: 18)
            .background(Circle().fill(Color.ueGreen))
            .scaleEffect(bump ? 1.25 : 1)
            .onChange(of: count) { _, _ in
                withAnimation(.spring(response: 0.28, dampingFraction: 0.5)) { bump = true }
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.28) {
                    withAnimation { bump = false }
                }
            }
    }
}
```

## 4. Live Map Order Tracking

```swift
import MapKit

struct UEOrderTrackingView: View {
    @State private var courier = CLLocationCoordinate2D(latitude: 37.78, longitude: -122.41)
    let route: [CLLocationCoordinate2D]
    let etaText: String

    var body: some View {
        ZStack(alignment: .bottom) {
            Map {
                MapPolyline(coordinates: route).stroke(.black, lineWidth: 4)   // near-black route
                Annotation("Courier", coordinate: courier) {
                    Circle().fill(Color.ueGreen)
                        .frame(width: 22, height: 22)
                        .overlay(Circle().strokeBorder(.white, lineWidth: 3))
                }
            }
            .mapStyle(.standard(elevation: .flat))
            .ignoresSafeArea()

            // Draggable tracking sheet
            VStack(alignment: .leading, spacing: 16) {
                Capsule().fill(Color.ueDivider).frame(width: 40, height: 5)
                    .frame(maxWidth: .infinity)
                Text(etaText).font(.ueSectionHeader)            // "Arriving in 12 min"
                UEProgressStepper(steps: ["Preparing", "Picked up", "On the way", "Delivered"],
                                  current: 2)
                // Courier card omitted for brevity
            }
            .padding(20)
            .frame(maxWidth: .infinity, alignment: .leading)
            .background(Color.ueCanvas)
            .clipShape(.rect(topLeadingRadius: 16, topTrailingRadius: 16))
            .shadow(color: .black.opacity(0.16), radius: 28, y: -8)
        }
        .task {
            // Ease the courier marker between location updates
            for point in route {
                withAnimation(.easeInOut(duration: 1.0)) { courier = point }
                try? await Task.sleep(for: .seconds(3))
            }
        }
    }
}

struct UEProgressStepper: View {
    let steps: [String]
    let current: Int
    var body: some View {
        HStack(spacing: 6) {
            ForEach(steps.indices, id: \.self) { i in
                RoundedRectangle(cornerRadius: 2)
                    .fill(i <= current ? Color.ueGreen : Color.ueSurface2)
                    .frame(height: 4)
            }
        }
    }
}
```

## 5. Tab Bar

Use `TabView` with a custom `UITabBarAppearance`. **Active tint is near-black/white, NOT green** — green is reserved for action surfaces.

```swift
struct RootTabView: View {
    @Environment(\.colorScheme) private var scheme
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem    { Label("Home",    systemImage: "house.fill") }
            BrowseView().tabItem  { Label("Browse",  systemImage: "square.grid.2x2.fill") }
            SearchView().tabItem  { Label("Search",  systemImage: "magnifyingglass") }
            CartView().tabItem    { Label("Cart",    systemImage: "cart.fill") }
                .badge(3) // green badge styling applied via tint where shown
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle.fill") }
        }
        .tint(scheme == .dark ? .white : .black) // active = near-black/white, NOT green
    }
}
```

## 6. Motion

Use `SwiftUI` spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// Cart-count bump — scale 1.0 → 1.25 → 1.0 on count change (see §3)

// Sticky cart bar appear
.transition(.move(edge: .bottom).combined(with: .opacity))
// withAnimation(.spring(response: 0.35, dampingFraction: 0.75)) { cartVisible = true }

// Map courier marker — withAnimation(.easeInOut(duration: 1.0)) { courier = nextPoint } (see §4)

// Pill select — withAnimation(.easeOut(duration: 0.15)) { selected = id }

// Add to cart haptic
.sensoryFeedback(.impact(weight: .light), trigger: didAdd)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Add | `plus` | 16pt |
| Quantity − / + | `minus` / `plus` | 18pt |
| Rating star | `star.fill` | 12pt (monochrome) |
| Favorite | `heart` / `heart.fill` | 16-22pt |
| Share | `square.and.arrow.up` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Address chevron | `chevron.down` | 12pt |
| Courier message | `message.fill` | 18pt |
| Courier call | `phone.fill` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Browse (tab) | `square.grid.2x2.fill` | 24pt |
| Search (tab) | `magnifyingglass` | 24pt |
| Cart (tab) | `cart.fill` | 24pt |
| Account (tab) | `person.crop.circle.fill` | 24pt |
| See all | `chevron.right` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (the new SwiftUI `Map`, `.sensoryFeedback`, and `.contentTransition(.numericText())` used here are iOS 17; on iOS 16 use `UIImpactFeedbackGenerator`, the older `Map(coordinateRegion:)`, and a manual count transition)
- Support Dynamic Type on restaurant names, menu items, descriptions, ETA headline — pin tab labels, cart badge, and the map ETA chip
- VoiceOver: announce restaurant cards as "Sunrise Diner, 4.8 stars, 25 minute delivery, 99 cent delivery fee"; the cart bar as "View cart, 3 items, total $28.40, button"; on the map expose ETA + courier status as a live region
- Contrast: `#6B6B6B` secondary on `#FFFFFF` passes WCAG AA at 14pt+; white on `#06C167` passes AA at 16pt bold (the CTA size) — validate any smaller green text
- Dark mode: keep `#06C167` unchanged; only neutrals invert — re-verify green-on-dark-surface contrast for any green text/icons
- Reduce Motion: disable the cart-count bump (instant update), the cart-bar slide (fade only), and ease the map marker with a shorter linear move or snap between updates
