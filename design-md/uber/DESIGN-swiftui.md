# Uber (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Uber's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Brand
    static let uberBlack          = Color.black                                 // #000000
    static let uberWhite          = Color.white                                 // #FFFFFF
    static let uberCanvasDark     = Color(red: 0.047, green: 0.047, blue: 0.047) // #0C0C0C

    // MARK: - Gray Ramp
    static let uberGray50   = Color(red: 0.965, green: 0.965, blue: 0.965) // #F6F6F6
    static let uberGray100  = Color(red: 0.933, green: 0.933, blue: 0.933) // #EEEEEE
    static let uberGray200  = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let uberGray400  = Color(red: 0.686, green: 0.686, blue: 0.686) // #AFAFAF
    static let uberGray600  = Color(red: 0.459, green: 0.459, blue: 0.459) // #757575
    static let uberGray700  = Color(red: 0.329, green: 0.329, blue: 0.329) // #545454
    static let uberGray900  = Color(red: 0.184, green: 0.184, blue: 0.184) // #2F2F2F
    static let uberGray950  = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A

    // MARK: - Functional
    static let uberGreen    = Color(red: 0.020, green: 0.639, blue: 0.341) // #05A357
    static let uberRed      = Color(red: 0.843, green: 0.129, blue: 0.075) // #D72113
    static let uberBlue     = Color(red: 0.039, green: 0.278, blue: 1.000) // #0A47FF
    static let uberAmber    = Color(red: 1.000, green: 0.796, blue: 0.000) // #FFCB00

    // MARK: - Semantic tokens (auto light/dark)
    static let uberSurface      = Color("UberSurface")      // #FFFFFF / #1A1A1A
    static let uberSurfaceAlt   = Color("UberSurfaceAlt")   // #F6F6F6 / #2F2F2F
    static let uberTextPrimary  = Color("UberTextPrimary")  // #000000 / #FFFFFF
    static let uberTextSecondary = Color("UberTextSecondary") // #757575 / #AFAFAF
    static let uberDivider      = Color("UberDivider")      // #E5E5E5 / #3A3A3A
}
```

## 2. Typography

Uber Move is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fallback to `.system(design: .default)` — SF Pro's geometric lineage is the closest free substitute. For numeric UI use `.monospacedDigit()` on SF Pro when Uber Move Mono is unavailable.

```swift
extension Font {
    // Uber Move (Display) — 20pt+
    static let uberHero         = Font.custom("UberMove-Bold",   size: 36).weight(.bold)
    static let uberSheetTitle   = Font.custom("UberMove-Bold",   size: 24).weight(.bold)
    static let uberNavTitle     = Font.custom("UberMove-Medium", size: 18).weight(.medium)

    // Uber Move Text — below 20pt UI
    static let uberWhereTo      = Font.custom("UberMoveText-Medium",  size: 18).weight(.medium)
    static let uberRowTitle     = Font.custom("UberMoveText-Medium",  size: 16).weight(.medium)
    static let uberBody         = Font.custom("UberMoveText-Regular", size: 15).weight(.regular)
    static let uberMeta         = Font.custom("UberMoveText-Regular", size: 14).weight(.regular)
    static let uberLabelUpper   = Font.custom("UberMoveText-Bold",    size: 11).weight(.bold)
    static let uberButton       = Font.custom("UberMoveText-Medium",  size: 17).weight(.medium)
    static let uberButtonSmall  = Font.custom("UberMoveText-Medium",  size: 15).weight(.medium)
    static let uberTab          = Font.custom("UberMoveText-Medium",  size: 11).weight(.medium)
    static let uberCaption      = Font.custom("UberMoveText-Regular", size: 12).weight(.regular)

    // Uber Move Mono — ETAs, fares, addresses
    static let uberPrice        = Font.custom("UberMoveMono-Medium",  size: 16).weight(.medium)
    static let uberETA          = Font.custom("UberMoveMono-Medium",  size: 14).weight(.medium)
    static let uberETABadge     = Font.custom("UberMoveMono-Medium",  size: 18).weight(.medium)
    static let uberAddress      = Font.custom("UberMoveMono-Regular", size: 13).weight(.regular)
}

// System fallback when Uber Move isn't bundled:
extension Font {
    static func uber(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
    static func uberMono(_ size: CGFloat, weight: Font.Weight = .medium) -> Font {
        .system(size: size, weight: weight, design: .monospaced)
    }
}
```

## 3. Signature Components

### Primary Black CTA

```swift
struct UberPrimaryButton: View {
    let title: String
    var isLoading: Bool = false
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            ZStack {
                if isLoading {
                    ProgressView().tint(.white)
                } else {
                    Text(title)
                        .font(.uberButton)
                        .foregroundStyle(.white)
                }
            }
            .frame(maxWidth: .infinity)
            .frame(height: 56)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.uberBlack))
        }
        .buttonStyle(UberPressableStyle(pressedFill: .uberGray900, pressedScale: 0.98))
        .sensoryFeedback(.impact(weight: .medium), trigger: isLoading)
    }
}

struct UberPressableStyle: ButtonStyle {
    var pressedFill: Color? = nil
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.2, dampingFraction: 0.85), value: configuration.isPressed)
    }
}
```

### "Where To?" Bottom Sheet Input

```swift
struct WhereToInput: View {
    var body: some View {
        HStack(spacing: 14) {
            VStack(spacing: 4) {
                Rectangle().fill(Color.uberBlack).frame(width: 8, height: 8)
                ForEach(0..<3) { _ in
                    Rectangle().fill(Color.uberGray400).frame(width: 1, height: 3)
                }
                Rectangle().fill(Color.uberBlack).frame(width: 8, height: 8)
            }
            .padding(.leading, 16)

            Text("Where to?")
                .font(.uberWhereTo)
                .foregroundStyle(.uberTextPrimary)

            Spacer()

            HStack(spacing: 6) {
                Image(systemName: "clock")
                    .font(.system(size: 14, weight: .medium))
                Text("Later")
                    .font(.uberButtonSmall)
            }
            .foregroundStyle(.uberTextPrimary)
            .padding(.horizontal, 12)
            .padding(.vertical, 8)
            .background(Capsule().fill(Color.uberWhite))
            .overlay(Capsule().strokeBorder(Color.uberGray200, lineWidth: 1))
            .padding(.trailing, 8)
        }
        .frame(height: 56)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.uberGray50))
    }
}
```

### Ride Option Card

```swift
struct RideOptionCard: View {
    let name: String
    let eta: String
    let capacity: Int
    let price: String
    let isSelected: Bool
    let carImage: Image
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 12) {
                carImage
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .frame(width: 56, height: 56)

                VStack(alignment: .leading, spacing: 4) {
                    HStack(spacing: 6) {
                        Text(name).font(.uberRowTitle).foregroundStyle(.uberTextPrimary)
                        HStack(spacing: 2) {
                            Image(systemName: "person.fill").font(.system(size: 10))
                            Text("\(capacity)").font(.uberCaption)
                        }
                        .foregroundStyle(.uberTextSecondary)
                    }
                    Text("\(eta) away")
                        .font(.uberAddress)
                        .foregroundStyle(.uberTextSecondary)
                }

                Spacer()

                Text(price)
                    .font(.uberPrice)
                    .foregroundStyle(.uberTextPrimary)
            }
            .padding(.horizontal, 16)
            .frame(height: 72)
            .background(
                RoundedRectangle(cornerRadius: 8)
                    .fill(isSelected ? Color.uberGray50 : Color.uberWhite)
            )
            .overlay(
                RoundedRectangle(cornerRadius: 8)
                    .strokeBorder(
                        isSelected ? Color.uberBlack : Color.uberGray200,
                        lineWidth: isSelected ? 2 : 1
                    )
            )
        }
        .buttonStyle(UberPressableStyle())
        .sensoryFeedback(.selection, trigger: isSelected)
    }
}
```

### Active Trip Card

```swift
struct ActiveTripCard: View {
    let driverName: String
    let rating: Double
    let carModel: String
    let plate: String
    let driverPhoto: Image

    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            HStack(spacing: 12) {
                driverPhoto
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(width: 48, height: 48)
                    .clipShape(Circle())

                VStack(alignment: .leading, spacing: 2) {
                    Text(driverName)
                        .font(.uberRowTitle)
                        .foregroundStyle(.uberTextPrimary)
                    HStack(spacing: 4) {
                        Image(systemName: "star.fill").font(.system(size: 11))
                        Text(String(format: "%.2f", rating)).font(.uberETA)
                    }
                    .foregroundStyle(.uberTextSecondary)
                }

                Spacer()

                Text(plate)
                    .font(.uberETA)
                    .padding(.horizontal, 10)
                    .padding(.vertical, 4)
                    .background(Capsule().fill(Color.uberGray50))
            }

            Text(carModel)
                .font(.uberMeta)
                .foregroundStyle(Color.uberGray700)

            Divider().background(Color.uberDivider)

            HStack(spacing: 24) {
                ForEach([("message", "Message"), ("phone", "Call"), ("square.and.arrow.up", "Share")], id: \.1) { icon, label in
                    VStack(spacing: 6) {
                        ZStack {
                            Circle().fill(Color.uberGray50).frame(width: 44, height: 44)
                            Image(systemName: icon).font(.system(size: 20, weight: .medium))
                                .foregroundStyle(.uberTextPrimary)
                        }
                        Text(label).font(.uberCaption).foregroundStyle(.uberTextSecondary)
                    }
                }
                Spacer()
            }
        }
        .padding(16)
    }
}
```

### Bottom Sheet with Detents

```swift
struct UberBottomSheet<Content: View>: View {
    @State private var selectedDetent: PresentationDetent = .medium
    @ViewBuilder var content: () -> Content

    var body: some View {
        VStack(spacing: 0) { /* map content */ }
            .sheet(isPresented: .constant(true)) {
                VStack(spacing: 0) {
                    RoundedRectangle(cornerRadius: 2)
                        .fill(Color.uberGray200)
                        .frame(width: 36, height: 4)
                        .padding(.top, 6)
                        .padding(.bottom, 14)
                    content()
                }
                .presentationDetents(
                    [.height(140), .medium, .large],
                    selection: $selectedDetent
                )
                .presentationDragIndicator(.hidden)
                .presentationBackground(Color.uberSurface)
                .presentationCornerRadius(16)
                .interactiveDismissDisabled()
            }
    }
}
```

## 4. Map Integration

Uber uses its own map renderer in production, but MapKit is the idiomatic SwiftUI choice.

```swift
import MapKit

struct UberMapView: View {
    @State private var camera: MapCameraPosition = .automatic

    var body: some View {
        Map(position: $camera) {
            Marker("Pickup", systemImage: "circle.fill", coordinate: pickup)
                .tint(.uberBlack)
            Annotation("Destination", coordinate: destination) {
                DestinationPin()
            }
            MapPolyline(coordinates: route)
                .stroke(Color.uberBlack, style: StrokeStyle(lineWidth: 5, lineCap: .round, lineJoin: .round))
        }
        .mapStyle(.standard(pointsOfInterest: .excludingAll))
        .mapControlVisibility(.hidden)
    }
}

struct DestinationPin: View {
    var body: some View {
        ZStack(alignment: .bottom) {
            Ellipse().fill(Color.black.opacity(0.25))
                .frame(width: 28, height: 8)
                .offset(y: 4)
                .blur(radius: 3)
            RoundedRectangle(cornerRadius: 4)
                .fill(Color.uberBlack)
                .frame(width: 32, height: 32)
        }
    }
}
```

### Floating Map Control

```swift
struct FloatingMapButton: View {
    let systemImage: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: systemImage)
                .font(.system(size: 20, weight: .medium))
                .foregroundStyle(.uberTextPrimary)
                .frame(width: 44, height: 44)
                .background(Circle().fill(Color.uberSurface))
                .shadow(color: .black.opacity(0.12), radius: 8, y: 2)
        }
    }
}
```

### Driver Arrival Beacon

```swift
struct DriverBeacon: View {
    @State private var animate = false

    var body: some View {
        ZStack {
            ForEach(0..<3) { i in
                Circle()
                    .fill(Color.uberGreen.opacity(0.3 - Double(i) * 0.1))
                    .frame(width: 48, height: 48)
                    .scaleEffect(animate ? 2.4 : 1)
                    .opacity(animate ? 0 : 1)
                    .animation(
                        .easeInOut(duration: 1.8)
                            .repeatForever(autoreverses: false)
                            .delay(Double(i) * 0.6),
                        value: animate
                    )
            }
            Image(systemName: "car.fill")
                .font(.system(size: 14))
                .foregroundStyle(.black)
                .frame(width: 24, height: 24)
                .background(Circle().fill(Color.uberWhite))
        }
        .onAppear { animate = true }
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = UIColor.systemBackground
        appearance.shadowColor = UIColor(Color.uberDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            ServicesView().tabItem { Label("Services", systemImage: "square.grid.2x2.fill") }
            ActivityView().tabItem { Label("Activity", systemImage: "clock.fill") }
            AccountView().tabItem { Label("Account", systemImage: "person.fill") }
        }
        .tint(.uberBlack)
    }
}
```

## 6. Motion & Haptics

```swift
// Confirm CTA — medium haptic on tap
.sensoryFeedback(.impact(weight: .medium), trigger: confirmTrigger)

// Driver arrived — success haptic
.sensoryFeedback(.success, trigger: driverArrivedAt)

// Cancel ride — warning haptic (on the destructive confirmation)
.sensoryFeedback(.warning, trigger: cancelTrigger)

// Ride option select — selection haptic
.sensoryFeedback(.selection, trigger: selectedOption)

// Sheet detent snap — SwiftUI's default spring via PresentationDetents
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Recenter | `location.fill` | 20pt |
| Zoom In / Out | `plus` / `minus` | 18pt |
| Back | `chevron.left` | 20pt |
| Close | `xmark` | 18pt |
| Search | `magnifyingglass` | 18pt |
| Clock (Later) | `clock` | 14pt |
| Home (saved) | `house.fill` | 16pt |
| Work (saved) | `briefcase.fill` | 16pt |
| Star (saved) | `star.fill` | 16pt |
| Person (capacity) | `person.fill` | 10pt |
| Message | `message.fill` | 20pt |
| Call | `phone.fill` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| Home tab | `house.fill` | 24pt |
| Services tab | `square.grid.2x2.fill` | 24pt |
| Activity tab | `clock.fill` | 24pt |
| Account tab | `person.fill` | 24pt |
| Payment card | `creditcard.fill` | 20pt |
| Car | `car.fill` | 14-22pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `PresentationDetents`, `Map` view improvements, `mapStyle(...)`). For iOS 16 fallback use `UIImpactFeedbackGenerator` and `MKMapView` via `UIViewRepresentable`.
- Dynamic Type on row titles, sheet titles, body. Fix the 56pt CTA height even as label scales — clip to 120% max.
- VoiceOver: add `.accessibilityLabel` on map pins ("Pickup at 212 Market Street"), group the driver card into a single accessibility element, read the pulse beacon as "Driver has arrived".
- Contrast: `#757575` on `#FFFFFF` passes WCAG AA for 14pt+ Regular. Validate at 11pt (use `#545454` instead). Green on white passes at 14pt+ Medium.
- Reduced motion: disable the driver pulse beacon (stays as a static green ring) and skip the polyline stroke animation — swap for instant draw.
- Safe area: map extends beneath the status bar; floating buttons respect `.safeAreaInset(edge: .top)`. The bottom sheet's smallest detent must sit above the home indicator.
