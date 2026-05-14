# Apple Maps (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. Apple Maps is the canonical reference for native iOS map UI. This file translates that visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, themed components for the search card, place card, current-location puck, and map controls.

Targets iOS 17+ for `.sensoryFeedback`, `.presentationDetents`, and native `Map` with `MapKit` integration.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Map Cartography
    static let mapsCream         = Color(red: 0.965, green: 0.945, blue: 0.902) // #F6F1E6 land
    static let mapsWater         = Color(red: 0.725, green: 0.851, blue: 0.922) // #B9D9EB water
    static let mapsParkGreen     = Color(red: 0.839, green: 0.898, blue: 0.788) // #D6E5C9 parks
    static let mapsRoadWhite     = Color(red: 1.00,  green: 1.00,  blue: 1.00)  // #FFFFFF major roads
    static let mapsRoadOutline   = Color(red: 0.835, green: 0.812, blue: 0.753) // #D5CFC0 minor road
    static let mapsHighway       = Color(red: 1.00,  green: 0.851, blue: 0.400) // #FFD966 highway
    static let mapsBuilding2D    = Color(red: 0.937, green: 0.914, blue: 0.867) // #EFE9DD
    static let mapsBuilding3D    = Color(red: 0.902, green: 0.875, blue: 0.816) // #E6DFD0
    static let mapsLabel         = Color(red: 0.349, green: 0.310, blue: 0.247) // #594F3F

    // MARK: - Brand
    static let mapsBlue          = Color(red: 0.039, green: 0.518, blue: 1.00)  // #0A84FF
    static let mapsBluePressed   = Color(red: 0.035, green: 0.404, blue: 0.824) // #0967D2
    static let mapsBlueDark      = Color(red: 0.251, green: 0.612, blue: 1.00)  // #409CFF
    static let mapsRed           = Color(red: 1.00,  green: 0.231, blue: 0.188) // #FF3B30 (iOS system Red)

    // MARK: - Category Colors
    static let mapsFood          = Color(red: 1.00,  green: 0.584, blue: 0.00)  // #FF9500
    static let mapsCoffee        = Color(red: 0.647, green: 0.412, blue: 0.298) // #A5694C
    static let mapsDrinks        = Color(red: 0.353, green: 0.784, blue: 0.980) // #5AC8FA
    static let mapsShopping      = Color(red: 1.00,  green: 0.800, blue: 0.00)  // #FFCC00
    static let mapsParking       = Color(red: 0.686, green: 0.322, blue: 0.871) // #AF52DE
    static let mapsHotel         = Color(red: 0.345, green: 0.337, blue: 0.839) // #5856D6
    static let mapsHealth        = Color(red: 1.00,  green: 0.176, blue: 0.333) // #FF2D55
    static let mapsEVGreen       = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759

    // MARK: - UI Chrome
    static let mapsCardCanvas    = Color(red: 1.00, green: 1.00, blue: 1.00)    // #FFFFFF
    static let mapsSurfaceGray   = Color(red: 0.949, green: 0.949, blue: 0.969) // #F2F2F7
    static let mapsSurfaceGray2  = Color(red: 0.898, green: 0.898, blue: 0.918) // #E5E5EA
    static let mapsDivider       = Color(red: 0.780, green: 0.780, blue: 0.800) // #C7C7CC

    // MARK: - Text (using opacity on a base color)
    static let mapsInk           = Color.black
    static let mapsSecondary     = Color(red: 0.235, green: 0.235, blue: 0.263) // #3C3C43 (apply opacity)
    // Use .opacity(0.60) for secondary, 0.30 for tertiary, 0.18 for placeholder

    // MARK: - Semantic
    static let mapsSuccess       = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759
    static let mapsWarning       = Color(red: 1.00, green: 0.584, blue: 0.00)   // #FF9500
    static let mapsError         = Color(red: 1.00, green: 0.231, blue: 0.188)  // #FF3B30

    // MARK: - Dark mode
    static let mapsDarkMapLand   = Color(red: 0.165, green: 0.157, blue: 0.149) // #2A2826
    static let mapsDarkMapWater  = Color(red: 0.110, green: 0.149, blue: 0.220) // #1C2638
    static let mapsDarkMapPark   = Color(red: 0.173, green: 0.227, blue: 0.165) // #2C3A2A
    static let mapsDarkCardSurf  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let mapsDarkSurface2  = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E
    static let mapsDarkDivider   = Color(red: 0.220, green: 0.220, blue: 0.227) // #38383A
}
```

## 2. Typography

Apple Maps uses SF Pro at the system stack with three optical variants. Use `.system` with the appropriate design parameter.

```swift
extension Font {
    // Place card
    static let mapsPlaceTitle    = Font.system(size: 28, weight: .bold,     design: .default)
    static let mapsPlaceSubtitle = Font.system(size: 15, weight: .regular,  design: .default)

    // Search
    static let mapsSearchPlaceholder = Font.system(size: 17, weight: .regular,  design: .default)
    static let mapsSearchInput   = Font.system(size: 17, weight: .regular,  design: .default)

    // Nav
    static let mapsNavTitle      = Font.system(size: 17, weight: .semibold, design: .default)

    // Section header
    static let mapsSectionHdr    = Font.system(size: 13, weight: .semibold, design: .default)

    // List rows
    static let mapsListTitle     = Font.system(size: 17, weight: .regular,  design: .default)
    static let mapsListSubtitle  = Font.system(size: 13, weight: .regular,  design: .default)

    // ETA / distances
    static let mapsETATime       = Font.system(size: 22, weight: .semibold, design: .default).monospacedDigit()
    static let mapsETADuration   = Font.system(size: 17, weight: .medium,   design: .default).monospacedDigit()

    // Direction steps
    static let mapsStepTitle     = Font.system(size: 22, weight: .semibold, design: .default)
    static let mapsStepDistance  = Font.system(size: 17, weight: .medium,   design: .default).monospacedDigit()

    // Category pills
    static let mapsCategoryLabel = Font.system(size: 14, weight: .medium,   design: .default)

    // Buttons
    static let mapsButton        = Font.system(size: 17, weight: .semibold, design: .default)

    // Map labels (zoom-controlled — render with MapKit, not custom)
    static let mapsCityLabel     = Font.system(size: 14, weight: .semibold, design: .default)
    static let mapsNeighborhood  = Font.system(size: 12, weight: .medium,   design: .default)
    static let mapsStreet        = Font.system(size: 10, weight: .regular,  design: .default)

    // Transit
    static let mapsTransitBadge  = Font.system(size: 12, weight: .bold,     design: .default)

    // Caption
    static let mapsCaption       = Font.system(size: 12, weight: .regular,  design: .default)
}
```

## 3. Signature Components

### Search Card (Sliding Bottom Sheet — Hero Component)

```swift
struct SearchCard: View {
    @State private var searchText = ""
    @State private var detent: PresentationDetent = .small

    static let small  = PresentationDetent.height(88)
    static let medium = PresentationDetent.medium
    static let large  = PresentationDetent.large

    var body: some View {
        VStack(spacing: 0) {
            // Drag indicator (grabber)
            Capsule()
                .fill(Color.mapsSecondary.opacity(0.30))
                .frame(width: 36, height: 5)
                .padding(.top, 8)

            // Search field
            HStack(spacing: 12) {
                Image(systemName: "magnifyingglass")
                    .font(.system(size: 17, weight: .regular))
                    .foregroundStyle(Color.mapsSecondary.opacity(0.60))
                TextField("Search Maps", text: $searchText)
                    .font(.mapsSearchInput)
                    .foregroundStyle(Color.mapsInk)
                Image(systemName: "mic.fill")
                    .font(.system(size: 16))
                    .foregroundStyle(Color.mapsSecondary.opacity(0.60))
            }
            .padding(.horizontal, 16)
            .frame(height: 44)
            .background(
                RoundedRectangle(cornerRadius: 10).fill(Color.mapsSurfaceGray)
            )
            .padding(.horizontal, 16)
            .padding(.top, 12)

            // Category pills row (visible at medium+ detent)
            if detent == Self.medium || detent == Self.large {
                ScrollView(.horizontal, showsIndicators: false) {
                    HStack(spacing: 12) {
                        CategoryPill(icon: "fork.knife",        label: "Food",     color: .mapsFood)
                        CategoryPill(icon: "cup.and.saucer.fill", label: "Coffee",   color: .mapsCoffee)
                        CategoryPill(icon: "wineglass.fill",    label: "Drinks",   color: .mapsDrinks)
                        CategoryPill(icon: "bag.fill",          label: "Shopping", color: .mapsShopping)
                        CategoryPill(icon: "parkingsign.circle.fill", label: "Parking", color: .mapsParking)
                        CategoryPill(icon: "tram.fill",         label: "Transit",  color: .mapsBlue)
                    }
                    .padding(.horizontal, 16)
                }
                .padding(.top, 16)
            }

            Spacer()
        }
        .background(.regularMaterial, in: RoundedRectangle(cornerRadius: 10, style: .continuous))
    }
}
```

### Category Pill

```swift
struct CategoryPill: View {
    let icon: String
    let label: String
    let color: Color

    var body: some View {
        VStack(spacing: 8) {
            Image(systemName: icon)
                .font(.system(size: 28))
                .foregroundStyle(color)
            Text(label)
                .font(.mapsCategoryLabel)
                .foregroundStyle(Color.mapsInk)
        }
        .frame(width: 64, height: 88)
        .background(
            RoundedRectangle(cornerRadius: 14).fill(Color.mapsCardCanvas)
        )
        .shadow(color: .black.opacity(0.12), radius: 12, y: 4)
    }
}
```

### Current-Location Puck (Pulsing Blue Dot with Cone)

```swift
struct CurrentLocationPuck: View {
    let heading: Double?
    @State private var pulse = false

    var body: some View {
        ZStack {
            // Pulsing outer ring
            Circle()
                .fill(Color.mapsBlue.opacity(pulse ? 0.0 : 0.30))
                .frame(width: 30, height: 30)
                .scaleEffect(pulse ? 1.6 : 1.0)
                .animation(.easeOut(duration: 2.0).repeatForever(autoreverses: false), value: pulse)

            // Directional cone (when heading is known)
            if let heading = heading {
                ConeShape()
                    .fill(LinearGradient(
                        colors: [Color.mapsBlue.opacity(0.4), Color.mapsBlue.opacity(0.0)],
                        startPoint: .top,
                        endPoint: .bottom
                    ))
                    .frame(width: 80, height: 60)
                    .offset(y: -30)
                    .rotationEffect(.degrees(heading))
            }

            // Blue dot with white ring
            Circle()
                .fill(Color.mapsBlue)
                .frame(width: 22, height: 22)
                .overlay(Circle().strokeBorder(.white, lineWidth: 3))
                .shadow(color: .black.opacity(0.2), radius: 4, y: 2)
        }
        .onAppear { pulse = true }
    }
}

struct ConeShape: Shape {
    func path(in rect: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: rect.midX, y: rect.maxY))
        p.addLine(to: CGPoint(x: rect.minX, y: rect.minY))
        p.addLine(to: CGPoint(x: rect.maxX, y: rect.minY))
        p.closeSubpath()
        return p
    }
}
```

### Map Controls (Floating Top-Right Stack)

```swift
struct MapControls: View {
    @Binding var mapStyle: MapStyleSelection
    var onLocationTap: () -> Void
    var on3DTap: () -> Void

    enum MapStyleSelection { case standard, satellite, hybrid, transit }

    var body: some View {
        VStack(spacing: 8) {
            MapControlButton(icon: "square.3.layers.3d") { /* open layer picker */ }
            MapControlButton(icon: "location.fill",   action: onLocationTap)
            MapControlButton(icon: "view.3d",         action: on3DTap)
        }
        .padding(.top, 16)
        .padding(.trailing, 16)
        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topTrailing)
    }
}

struct MapControlButton: View {
    let icon: String
    var action: () -> Void = {}

    var body: some View {
        Button(action: action) {
            Image(systemName: icon)
                .font(.system(size: 18, weight: .regular))
                .foregroundStyle(Color.mapsInk)
                .frame(width: 44, height: 44)
                .background(
                    Circle().fill(.regularMaterial)
                )
        }
        .buttonStyle(.plain)
    }
}
```

### Place Card (Detail View)

```swift
struct PlaceCard: View {
    let title: String
    let subtitle: String
    let isOpen: Bool
    let closingTime: String
    var onDirections: () -> Void = {}
    var onCall: () -> Void = {}

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // Top section
            HStack(alignment: .top) {
                VStack(alignment: .leading, spacing: 4) {
                    Text(title)
                        .font(.mapsPlaceTitle)
                        .foregroundStyle(Color.mapsInk)
                    Text(subtitle)
                        .font(.mapsPlaceSubtitle)
                        .foregroundStyle(Color.mapsSecondary.opacity(0.60))
                }
                Spacer()
                Button { /* close */ } label: {
                    Image(systemName: "xmark")
                        .font(.system(size: 14, weight: .semibold))
                        .foregroundStyle(Color.mapsSecondary.opacity(0.80))
                        .frame(width: 30, height: 30)
                        .background(Circle().fill(.regularMaterial))
                }
                .buttonStyle(.plain)
            }
            .padding(.horizontal, 20)
            .padding(.top, 16)

            // Action button row
            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 8) {
                    ActionPill(label: "Directions", systemImage: "arrow.triangle.turn.up.right.diamond.fill",
                               style: .filled,    action: onDirections)
                    ActionPill(label: "Call",       systemImage: "phone.fill",
                               style: .outlined,  action: onCall)
                    ActionPill(label: "Share",      systemImage: "square.and.arrow.up",
                               style: .outlined,  action: {})
                    ActionPill(label: "Save",       systemImage: "heart",
                               style: .outlined,  action: {})
                }
                .padding(.horizontal, 20)
            }
            .padding(.top, 16)

            // Hours row
            HStack {
                Text(isOpen ? "Open" : "Closed")
                    .font(.system(size: 15, weight: .semibold))
                    .foregroundStyle(isOpen ? Color.mapsSuccess : Color.mapsError)
                Text("· \(closingTime)")
                    .font(.mapsPlaceSubtitle)
                    .foregroundStyle(Color.mapsSecondary.opacity(0.60))
                Spacer()
            }
            .padding(.horizontal, 20)
            .padding(.vertical, 16)

            Divider().padding(.horizontal, 20)

            Spacer()
        }
        .background(.regularMaterial, in: RoundedRectangle(cornerRadius: 10, style: .continuous))
    }
}

struct ActionPill: View {
    enum Style { case filled, outlined }
    let label: String
    let systemImage: String
    let style: Style
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: systemImage)
                    .font(.system(size: 14, weight: .semibold))
                Text(label).font(.mapsButton)
            }
            .foregroundStyle(style == .filled ? .white : Color.mapsBlue)
            .padding(.vertical, 12).padding(.horizontal, 20)
            .background(
                Group {
                    if style == .filled {
                        Capsule().fill(Color.mapsBlue)
                    } else {
                        Capsule().strokeBorder(Color.mapsBlue, lineWidth: 1)
                            .background(Capsule().fill(Color.mapsCardCanvas))
                    }
                }
            )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}
```

### Direction Step Card

```swift
struct StepCard: View {
    let arrowSymbol: String     // e.g., "arrow.turn.up.right"
    let arrowRotation: Double   // degrees
    let stepTitle: String       // "Turn right onto Market St"
    let distance: String        // "0.4 mi"

    var body: some View {
        HStack(spacing: 16) {
            Image(systemName: arrowSymbol)
                .font(.system(size: 44, weight: .semibold))
                .foregroundStyle(Color.mapsBlue)
                .rotationEffect(.degrees(arrowRotation))
                .frame(width: 56, height: 56)

            VStack(alignment: .leading, spacing: 4) {
                Text(stepTitle)
                    .font(.mapsStepTitle)
                    .foregroundStyle(Color.mapsInk)
                    .lineLimit(2)
                Text(distance)
                    .font(.mapsStepDistance)
                    .foregroundStyle(Color.mapsSecondary.opacity(0.60))
            }
            Spacer()
        }
        .padding(.horizontal, 20)
        .frame(height: 96)
        .background(.regularMaterial)
    }
}
```

### ETA Bottom Bar

```swift
struct ETABar: View {
    let duration: String        // "32 min"
    let arrival: String         // "1.2 mi · arriving 6:14 PM"
    var onEnd: () -> Void

    var body: some View {
        HStack(alignment: .center) {
            VStack(alignment: .leading, spacing: 4) {
                Text(duration)
                    .font(.mapsETATime)
                    .foregroundStyle(Color.mapsInk)
                Text(arrival)
                    .font(.mapsETADuration)
                    .foregroundStyle(Color.mapsSecondary.opacity(0.60))
            }
            Spacer()
            Button(action: onEnd) {
                Text("End")
                    .font(.mapsButton)
                    .foregroundStyle(Color.mapsError)
                    .padding(.vertical, 12).padding(.horizontal, 20)
                    .background(
                        Capsule().strokeBorder(Color.mapsError, lineWidth: 1)
                            .background(Capsule().fill(Color.mapsCardCanvas))
                    )
            }
            .buttonStyle(.plain)
        }
        .padding(.horizontal, 20)
        .frame(height: 80)
        .background(.regularMaterial)
    }
}
```

### Transit Line Badge

```swift
struct TransitBadge: View {
    let label: String           // "L", "6", "BART"
    let color: Color            // agency-accurate color

    var body: some View {
        Text(label)
            .font(.mapsTransitBadge)
            .foregroundStyle(.white)
            .frame(minWidth: 24, minHeight: 24)
            .padding(.horizontal, 6)
            .background(RoundedRectangle(cornerRadius: 4).fill(color))
    }
}
```

## 4. Sliding Bottom Sheet (Sheet Presentation)

```swift
struct MapScreen: View {
    @State private var showSearchCard = true
    @State private var detent: PresentationDetent = .height(88)

    var body: some View {
        ZStack(alignment: .topTrailing) {
            MapView()
            MapControls(mapStyle: .constant(.standard), onLocationTap: {}, on3DTap: {})
        }
        .sheet(isPresented: $showSearchCard) {
            SearchCard()
                .presentationDetents([.height(88), .medium, .large], selection: $detent)
                .presentationCornerRadius(10)
                .presentationBackgroundInteraction(.enabled)
                .presentationDragIndicator(.visible)
                .interactiveDismissDisabled()
        }
    }
}
```

## 5. Motion & Haptics

```swift
// Pin drop on long-press
.sensoryFeedback(.impact(weight: .medium), trigger: pinDropped)

// Directions tap
.sensoryFeedback(.impact(weight: .medium), trigger: directionsPressed)

// Reach destination
.sensoryFeedback(.success, trigger: arrived)

// Map style picker selection
.sensoryFeedback(.selection, trigger: mapStyle)

// Pin drop animation
withAnimation(.spring(response: 0.4, dampingFraction: 0.65)) { pinDropped = true }

// Current-location lock — pan to user
withAnimation(.easeOut(duration: 0.6)) { camera = userLocation }

// 3D building extrude
withAnimation(.easeOut(duration: 0.4)) { is3D = true }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search | `magnifyingglass` | 17pt |
| Microphone | `mic.fill` | 16pt |
| Close | `xmark` | 14pt |
| Layer picker | `square.3.layers.3d` | 18pt |
| Current location (locked) | `location.fill` | 18pt |
| Current location (unlocked) | `location` | 18pt |
| 3D toggle | `view.3d` | 18pt |
| Compass | `safari` (rotating north arrow) | 22pt |
| Dropped pin | `mappin.and.ellipse` | 32pt |
| Directions | `arrow.triangle.turn.up.right.diamond.fill` | 14pt |
| Call | `phone.fill` | 14pt |
| Share | `square.and.arrow.up` | 14pt |
| Save | `heart` / `heart.fill` | 14pt |
| Look Around | `binoculars.fill` | 18pt |
| Food category | `fork.knife` | 28pt |
| Coffee category | `cup.and.saucer.fill` | 28pt |
| Drinks category | `wineglass.fill` | 28pt |
| Shopping | `bag.fill` | 28pt |
| Parking | `parkingsign.circle.fill` | 28pt |
| Transit | `tram.fill` | 28pt |
| Direction arrow (right) | `arrow.turn.up.right` | 44pt |
| Direction arrow (left) | `arrow.turn.up.left` | 44pt |
| Direction (slight) | `arrow.up.right` / `arrow.up.left` | 44pt |
| Address pin | `mappin.circle.fill` | 18pt |
| Phone | `phone.fill` | 18pt |
| Globe | `globe` | 18pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `.presentationDetents`, modern Map APIs); fall back to `UIImpactFeedbackGenerator` and `UIBezierPath` for the cone on iOS 16
- Support Dynamic Type on place card title, body, list rows, button labels; fix map labels (zoom-controlled), ETA numbers (column alignment), tabular distance/time figures, category pill labels
- VoiceOver: place card reads as `"<title>, <subtitle>, <open status>, <closing time>"`; current-location puck announces as `"Your current location"`; the dropped pin announces the geocoded address
- Contrast: pure black on cream `#F6F1E6` meets WCAG AAA; secondary `#3C3C43`@60% on cream meets AA at 18pt+ Bold or 14pt+ Medium
- Reduce Motion: skip the pulse animation on the current-location puck when `accessibilityReduceMotion` is true; replace with a static blue dot
- Dark mode: implement via `@Environment(\.colorScheme)` — swap the map style, card canvas, divider tokens; Maps Blue brightens to `#409CFF`
- Tabular figures: use `.monospacedDigit()` on every Font that renders distance, time, ETA, or price
- The search card detents use `.presentationDetents` — combine with `.presentationBackgroundInteraction(.enabled)` so the user can still pan the map while the small detent is showing
- Map controls use `.regularMaterial` blur — this requires iOS 15+ and respects light/dark mode automatically
- For the directional cone on the puck, use a custom `Shape` (provided above) — there's no native SF Symbol that matches Apple Maps' exact rendering
