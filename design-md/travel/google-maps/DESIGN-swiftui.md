# Google Maps (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Google Maps' visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let gmCanvas        = Color.white                                   // #FFFFFF
    static let gmSurfaceMuted  = Color(red: 0.945, green: 0.953, blue: 0.957) // #F1F3F4
    static let gmDivider       = Color(red: 0.855, green: 0.863, blue: 0.878) // #DADCE0

    // MARK: - Text
    static let gmTextPrimary   = Color(red: 0.125, green: 0.133, blue: 0.141) // #202124
    static let gmTextSecondary = Color(red: 0.373, green: 0.388, blue: 0.408) // #5F6368
    static let gmTextTertiary  = Color(red: 0.502, green: 0.525, blue: 0.545) // #80868B

    // MARK: - Google Logo Colors (Pin System)
    static let gmBlue          = Color(red: 0.259, green: 0.522, blue: 0.957) // #4285F4
    static let gmBluePressed   = Color(red: 0.102, green: 0.451, blue: 0.910) // #1A73E8
    static let gmBlueDark      = Color(red: 0.090, green: 0.306, blue: 0.651) // #174EA6
    static let gmRed           = Color(red: 0.918, green: 0.263, blue: 0.208) // #EA4335
    static let gmYellow        = Color(red: 0.984, green: 0.737, blue: 0.016) // #FBBC04
    static let gmGreen         = Color(red: 0.204, green: 0.659, blue: 0.325) // #34A853
    static let gmOrange        = Color(red: 0.984, green: 0.549, blue: 0.000) // #FB8C00

    // MARK: - Map Tiles (Light)
    static let gmRoadWhite     = Color.white                                   // #FFFFFF
    static let gmHighwayYellow = Color(red: 0.992, green: 0.965, blue: 0.890) // #FDF6E3
    static let gmWaterBlue     = Color(red: 0.667, green: 0.855, blue: 1.000) // #AADAFF
    static let gmParkGreen     = Color(red: 0.784, green: 0.902, blue: 0.788) // #C8E6C9
    static let gmBuildingFill  = Color(red: 0.941, green: 0.941, blue: 0.941) // #F0F0F0

    // MARK: - Dark Mode
    static let gmDarkCanvas    = Color(red: 0.125, green: 0.133, blue: 0.141) // #202124
    static let gmDarkSurface1  = Color(red: 0.176, green: 0.180, blue: 0.192) // #2D2E31
    static let gmDarkSurface2  = Color(red: 0.235, green: 0.251, blue: 0.263) // #3C4043
    static let gmDarkTextPrim  = Color(red: 0.910, green: 0.918, blue: 0.929) // #E8EAED

    // MARK: - Blue Halo (Accuracy Circle)
    static let gmBlueHalo      = Color(red: 0.259, green: 0.522, blue: 0.957, opacity: 0.18)
    static let gmBlueHaloEdge  = Color(red: 0.259, green: 0.522, blue: 0.957, opacity: 0.4)
}
```

## 2. Typography

Google Sans is restricted to Google's products but falls back gracefully to `.system(design: .default)` which is SF Pro. Roboto is freely available from Google Fonts — bundle via `UIAppFonts`.

```swift
extension Font {
    // Google Sans (headings, CTAs)
    static let gmNavTurn      = Font.custom("GoogleSans-Bold",   size: 36).weight(.bold)
    static let gmScreenTitle  = Font.custom("GoogleSans-Medium", size: 28).weight(.medium)
    static let gmPlaceTitle   = Font.custom("GoogleSans-Medium", size: 20).weight(.medium)
    static let gmSection      = Font.custom("GoogleSans-Medium", size: 16).weight(.medium)
    static let gmButton       = Font.custom("GoogleSans-Medium", size: 16).weight(.medium)
    static let gmButtonSmall  = Font.custom("GoogleSans-Medium", size: 14).weight(.medium)
    static let gmTab          = Font.custom("GoogleSans-Medium", size: 11).weight(.medium)
    static let gmChip         = Font.custom("GoogleSans-Medium", size: 14).weight(.medium)

    // Roboto (body, UI, data)
    static let gmRowTitle     = Font.custom("Roboto-Medium",  size: 16).weight(.medium)
    static let gmBody         = Font.custom("Roboto-Regular", size: 14).weight(.regular)
    static let gmAddress      = Font.custom("Roboto-Regular", size: 14).weight(.regular)
    static let gmMeta         = Font.custom("Roboto-Regular", size: 13).weight(.regular)
    static let gmRating       = Font.custom("Roboto-Medium",  size: 14).weight(.medium)
    static let gmETA          = Font.custom("Roboto-Medium",  size: 16).weight(.medium)
    static let gmDistancePill = Font.custom("Roboto-Medium",  size: 13).weight(.medium)
}

// Tabular numerals modifier for distances, ETAs, speeds
extension View {
    func gmTabularFigures() -> some View {
        self.monospacedDigit()
    }
}
```

## 3. Signature Components

### Floating Top Search Bar

```swift
struct GMSearchBar: View {
    let onTap: () -> Void
    let onMic: () -> Void
    var body: some View {
        HStack(spacing: 12) {
            Circle()
                .fill(Color.gray.opacity(0.3))
                .frame(width: 28, height: 28)
                .overlay(Image(systemName: "person.fill").foregroundStyle(.white).font(.system(size: 14)))
            Text("Search here")
                .font(.gmButton)
                .foregroundStyle(.gmTextSecondary)
            Spacer()
            Button(action: onMic) {
                Image(systemName: "mic.fill")
                    .font(.system(size: 20))
                    .foregroundStyle(.gmTextSecondary)
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 48)
        .background(
            Capsule().fill(Color.gmCanvas)
                .shadow(color: .black.opacity(0.15), radius: 8, y: 2)
        )
        .padding(.horizontal, 16)
        .onTapGesture(perform: onTap)
    }
}
```

### Directions FAB

```swift
struct GMDirectionsFAB: View {
    let action: () -> Void
    @State private var pressed = false
    var body: some View {
        Button(action: action) {
            Image(systemName: "arrow.triangle.turn.up.right.diamond.fill")
                .font(.system(size: 24, weight: .semibold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(pressed ? Color.gmBluePressed : Color.gmBlue))
                .shadow(color: .black.opacity(0.2), radius: 12, y: 4)
        }
        .scaleEffect(pressed ? 0.95 : 1)
        .animation(.spring(response: 0.25, dampingFraction: 0.7), value: pressed)
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded   { _ in pressed = false }
        )
    }
}
```

### Your Location Dot (with accuracy halo + heading cone)

```swift
struct GMLocationDot: View {
    @State private var pulse = false
    var headingDegrees: Double?  // if non-nil, draw a cone in that direction

    var body: some View {
        ZStack {
            // Accuracy circle
            Circle()
                .fill(Color.gmBlueHalo)
                .overlay(Circle().stroke(Color.gmBlueHaloEdge, lineWidth: 1))
                .frame(width: 140, height: 140)

            // Heading cone (optional)
            if let heading = headingDegrees {
                Triangle()
                    .fill(LinearGradient(
                        colors: [Color.gmBlue.opacity(0.55), Color.gmBlue.opacity(0)],
                        startPoint: .top, endPoint: .bottom))
                    .frame(width: 40, height: 48)
                    .offset(y: -48)
                    .rotationEffect(.degrees(heading))
            }

            // Inner blue dot with white stroke ring
            Circle()
                .fill(Color.gmBlue)
                .frame(width: 12, height: 12)
                .overlay(Circle().stroke(.white, lineWidth: 3).frame(width: 18, height: 18))
                .scaleEffect(pulse ? 1.15 : 1.0)
        }
        .onAppear {
            withAnimation(.easeInOut(duration: 1.8).repeatForever(autoreverses: true)) {
                pulse = true
            }
        }
    }
}

struct Triangle: Shape {
    func path(in rect: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: rect.midX, y: rect.minY))
        p.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY))
        p.addLine(to: CGPoint(x: rect.minX, y: rect.maxY))
        p.closeSubpath()
        return p
    }
}
```

### Map Pin (Teardrop)

```swift
struct GMMapPin: View {
    enum Kind { case `default`, saved, homeWork, category(String) }
    let kind: Kind
    var fillColor: Color {
        switch kind {
        case .default:       return .gmRed
        case .saved:         return .gmGreen
        case .homeWork:      return .gmBlue
        case .category:      return .gmOrange
        }
    }
    var body: some View {
        ZStack(alignment: .top) {
            TeardropShape()
                .fill(fillColor)
                .frame(width: 32, height: 40)
                .shadow(color: .black.opacity(0.25), radius: 3, y: 4)

            if case .default = kind {
                Circle()
                    .fill(.white)
                    .frame(width: 10, height: 10)
                    .offset(y: 10)
            } else if case .category(let symbol) = kind {
                Image(systemName: symbol)
                    .font(.system(size: 14, weight: .semibold))
                    .foregroundStyle(.white)
                    .offset(y: 10)
            }
        }
    }
}

struct TeardropShape: Shape {
    func path(in rect: CGRect) -> Path {
        var p = Path()
        let circleD = rect.width
        p.addArc(center: CGPoint(x: rect.midX, y: rect.width / 2),
                 radius: circleD / 2,
                 startAngle: .degrees(0),
                 endAngle: .degrees(360), clockwise: false)
        p.move(to: CGPoint(x: rect.minX + 2, y: rect.width / 2))
        p.addQuadCurve(to: CGPoint(x: rect.midX, y: rect.maxY),
                       control: CGPoint(x: rect.minX, y: rect.width))
        p.addQuadCurve(to: CGPoint(x: rect.maxX - 2, y: rect.width / 2),
                       control: CGPoint(x: rect.maxX, y: rect.width))
        return p
    }
}
```

### Place Sheet Card

```swift
struct GMPlaceCard: View {
    let title: String
    let rating: Double
    let reviewCount: Int
    let category: String
    let distance: String
    let isOpen: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(alignment: .top, spacing: 12) {
                RoundedRectangle(cornerRadius: 12)
                    .fill(Color.gmSurfaceMuted)
                    .frame(width: 72, height: 72)
                VStack(alignment: .leading, spacing: 4) {
                    Text(title).font(.gmPlaceTitle).foregroundStyle(.gmTextPrimary).lineLimit(2)
                    HStack(spacing: 4) {
                        Text(String(format: "%.1f", rating))
                            .font(.gmRating)
                            .foregroundStyle(.gmTextPrimary)
                            .gmTabularFigures()
                        ForEach(0..<5, id: \.self) { i in
                            Image(systemName: Double(i) < rating ? "star.fill" : "star")
                                .font(.system(size: 12))
                                .foregroundStyle(.gmYellow)
                        }
                        Text("(\(reviewCount))")
                            .font(.gmBody)
                            .foregroundStyle(.gmTextSecondary)
                    }
                    Text("\(category) · \(distance)")
                        .font(.gmMeta)
                        .foregroundStyle(.gmTextSecondary)
                    if isOpen {
                        Text("Open now")
                            .font(.gmMeta.weight(.medium))
                            .foregroundStyle(.gmGreen)
                    }
                }
            }
            GMActionRow()
        }
        .padding(16)
        .background(Color.gmCanvas)
    }
}

struct GMActionRow: View {
    var body: some View {
        HStack(spacing: 12) {
            GMPillButton(icon: "arrow.triangle.turn.up.right.diamond.fill", title: "Directions", filled: true)
            GMPillButton(icon: "bookmark", title: "Save", filled: false)
            GMPillButton(icon: "square.and.arrow.up", title: "Share", filled: false)
            GMPillButton(icon: "phone.fill", title: "Call", filled: false)
        }
    }
}

struct GMPillButton: View {
    let icon: String
    let title: String
    let filled: Bool
    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: icon).font(.system(size: 16, weight: .medium))
            Text(title).font(.gmButtonSmall)
        }
        .foregroundStyle(filled ? .white : .gmBlue)
        .padding(.horizontal, 16)
        .frame(height: 36)
        .background(
            Capsule().fill(filled ? Color.gmBlue : Color.clear)
                .overlay(Capsule().stroke(filled ? Color.clear : Color.gmDivider, lineWidth: 1))
        )
    }
}
```

### Navigation Turn Card

```swift
struct GMTurnCard: View {
    let instruction: String
    let distance: String
    let nextInstruction: String?

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack(alignment: .center, spacing: 16) {
                Image(systemName: "arrow.turn.up.right")
                    .font(.system(size: 44, weight: .bold))
                    .foregroundStyle(.white)
                VStack(alignment: .leading, spacing: 4) {
                    Text(instruction)
                        .font(.custom("GoogleSans-Bold", size: 22))
                        .foregroundStyle(.white)
                        .lineLimit(2)
                    Text("in \(distance)")
                        .font(.gmBody)
                        .foregroundStyle(.white.opacity(0.7))
                        .gmTabularFigures()
                }
                Spacer()
            }
            if let next = nextInstruction {
                HStack(spacing: 10) {
                    Image(systemName: "arrow.turn.up.left")
                        .font(.system(size: 22, weight: .medium))
                        .foregroundStyle(.white.opacity(0.8))
                    Text("Then \(next)")
                        .font(.gmBody)
                        .foregroundStyle(.white.opacity(0.8))
                }
                .padding(.top, 6)
                .overlay(Rectangle().frame(height: 1).foregroundStyle(.white.opacity(0.2)), alignment: .top)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.gmBlue))
        .shadow(color: .black.opacity(0.2), radius: 16, y: 4)
        .padding(.horizontal, 16)
    }
}
```

## 4. Route Polyline (MapKit / Overlay)

```swift
import MapKit

final class GMRouteRenderer: MKOverlayRenderer {
    override func draw(_ mapRect: MKMapRect, zoomScale: MKZoomScale, in context: CGContext) {
        guard let polyline = overlay as? MKPolyline else { return }
        let path = CGMutablePath()
        for i in 0..<polyline.pointCount {
            let pt = self.point(for: polyline.points()[i])
            if i == 0 { path.move(to: pt) } else { path.addLine(to: pt) }
        }
        // Casing
        context.setStrokeColor(UIColor(red: 0.09, green: 0.306, blue: 0.651, alpha: 1).cgColor) // #174EA6
        context.setLineWidth(6.5 / zoomScale)
        context.setLineCap(.round)
        context.addPath(path); context.strokePath()

        // Fill
        context.setStrokeColor(UIColor(red: 0.259, green: 0.522, blue: 0.957, alpha: 1).cgColor) // #4285F4
        context.setLineWidth(5 / zoomScale)
        context.setLineCap(.round)
        context.addPath(path); context.strokePath()
    }
}
```

## 5. Tab Bar

```swift
struct GMRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(red: 0.855, green: 0.863, blue: 0.878, alpha: 1)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            ExploreView().tabItem  { Label("Explore",    systemImage: "safari.fill") }
            GoView().tabItem       { Label("Go",         systemImage: "location.north.circle.fill") }
            SavedView().tabItem    { Label("Saved",      systemImage: "bookmark.fill") }
            ContribView().tabItem  { Label("Contribute", systemImage: "plus.circle.fill") }
            UpdatesView().tabItem  { Label("Updates",    systemImage: "newspaper.fill") }
        }
        .tint(Color.gmBlue) // active tab = Google Blue
    }
}
```

## 6. Motion

```swift
// Pin drop
withAnimation(.spring(response: 0.35, dampingFraction: 0.55)) { pinDropped = true }
// paired with .sensoryFeedback(.impact(weight: .medium), trigger: pinDropped)

// Sheet detent change (iOS 16+)
.sheet(isPresented: $showSheet) {
    GMPlaceCard(...)
        .presentationDetents([.height(140), .height(380), .large])
        .presentationDragIndicator(.visible)
        .presentationCornerRadius(16)
}

// Camera flyTo (MapKit)
let camera = MKMapCamera(lookingAtCenter: coord, fromDistance: 1200, pitch: 0, heading: 0)
mapView.setCamera(camera, animated: true)   // 0.6s ease-in-out by default

// Route draw-on animation
// Animate an MKPolyline by appending segments frame-by-frame, or fade in via CAShapeLayer strokeEnd 0→1 in 600ms
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Directions FAB | `arrow.triangle.turn.up.right.diamond.fill` | 24pt |
| Search mic | `mic.fill` | 20pt |
| Location dot | custom (circle with stroke) | 12pt |
| Saved bookmark | `bookmark` / `bookmark.fill` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| Call | `phone.fill` | 20pt |
| Recenter | `location.fill` | 20pt |
| Layers | `square.3.layers.3d` | 20pt |
| Compass | `safari` | 20pt |
| Turn right | `arrow.turn.up.right` | 44pt |
| Turn left | `arrow.turn.up.left` | 44pt |
| Straight | `arrow.up` | 44pt |
| Rating star | `star.fill` | 12pt |
| Home (tab) | `safari.fill` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `presentationDetents`); iOS 17+ for `.sensoryFeedback`
- **VoiceOver**: Location dot should announce "Your location, accuracy 30 feet"; pins should announce category and place name
- **Dynamic Type**: Place titles, addresses, and reviews scale freely; fix distance labels, speed chip, and nav turn instruction at a max of 140%
- **Contrast**: `#5F6368` secondary text on `#FFFFFF` canvas meets WCAG AA at 14pt+; verify `#80868B` tertiary at 13pt
- **Dark mode**: Adopt `@Environment(\.colorScheme)` and swap to dark-tile map palette + `#202124` canvas
- **Reduce Motion**: Replace the pin drop spring with a 200ms ease-out and skip the location-dot pulse
- **Haptics**: medium for pin drop and "Start", light for tab change and chip selection, success for "Arrived at destination"
