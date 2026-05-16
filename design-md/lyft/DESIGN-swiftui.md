# Lyft (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Lyft's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views — including the full-screen map + rounded bottom sheet model and the ride-type selector.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Light Canvas & Surfaces
    static let lyftCanvas      = Color.white                                  // #FFFFFF
    static let lyftSurface     = Color(red: 0.957, green: 0.957, blue: 0.969) // #F4F4F7
    static let lyftSurfaceDeep = Color(red: 0.914, green: 0.914, blue: 0.937) // #E9E9EF
    static let lyftDivider     = Color(red: 0.898, green: 0.898, blue: 0.918) // #E5E5EA

    static let lyftTextPrimary   = Color(red: 0.067, green: 0.067, blue: 0.122) // #11111F
    static let lyftTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.482) // #6B6B7B
    static let lyftTextTertiary  = Color(red: 0.604, green: 0.604, blue: 0.659) // #9A9AA8

    // MARK: - Dark Canvas & Surfaces
    static let lyftCanvasDark      = Color(red: 0.067, green: 0.067, blue: 0.122) // #11111F
    static let lyftSurfaceDark     = Color(red: 0.110, green: 0.110, blue: 0.169) // #1C1C2B
    static let lyftSurfaceDeepDark = Color(red: 0.149, green: 0.149, blue: 0.227) // #26263A
    static let lyftDividerDark     = Color(red: 0.173, green: 0.173, blue: 0.251) // #2C2C40
    static let lyftTextPrimaryDark = Color.white                                  // #FFFFFF
    static let lyftTextSecDark     = Color(red: 0.627, green: 0.627, blue: 0.706) // #A0A0B4

    // MARK: - Brand
    static let lyftPink        = Color(red: 1.0,   green: 0.0,   blue: 0.749) // #FF00BF
    static let lyftPinkPressed = Color(red: 0.835, green: 0.0,   blue: 0.627) // #D500A0
    static let lyftPinkTint    = Color(red: 1.0,   green: 0.898, blue: 0.969) // #FFE5F7
    static let lyftPinkTintDk  = Color(red: 0.200, green: 0.063, blue: 0.169) // #33102B

    // MARK: - Semantic
    static let lyftSuccess = Color(red: 0.0,   green: 0.659, blue: 0.384) // #00A862
    static let lyftGold    = Color(red: 1.0,   green: 0.706, blue: 0.0)   // #FFB400
    static let lyftError   = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}
```

## 2. Typography

Lyft uses Inter with a friendly, rounded character. Bundle Inter via `Info.plist` (`UIAppFonts`); fall back to `.system(design: .rounded)` to preserve the soft personality.

```swift
extension Font {
    static let lyftSheetTitle  = Font.custom("Inter-Bold",      size: 22)
    static let lyftWhereTo     = Font.custom("Inter-SemiBold",  size: 20)
    static let lyftRideName    = Font.custom("Inter-Bold",      size: 17)
    static let lyftPrice       = Font.custom("Inter-Bold",      size: 17)
    static let lyftBody        = Font.custom("Inter-Regular",   size: 15)
    static let lyftButton      = Font.custom("Inter-Bold",      size: 17)
    static let lyftDriverName  = Font.custom("Inter-Bold",      size: 18)
    static let lyftMeta        = Font.custom("Inter-Regular",   size: 14)
    static let lyftRideSub     = Font.custom("Inter-Regular",   size: 13)
    static let lyftBadge       = Font.custom("Inter-Bold",      size: 11)
    static let lyftCaption     = Font.custom("Inter-Regular",   size: 12)
    static let lyftPinLabel    = Font.custom("Inter-Bold",      size: 13)
}

extension Font {
    static func lyft(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .rounded) // rounded fallback
    }
}
```

Prices, ETAs, and fare breakdowns use tabular figures — apply `.monospacedDigit()`.

## 3. Signature Components

### Full-Screen Map + Rounded Bottom Sheet

```swift
struct RideHomeView: View {
    @State private var detent: PresentationDetent = .fraction(0.45)

    var body: some View {
        MapView()                                  // full-bleed, edge to edge
            .ignoresSafeArea()
            .overlay(alignment: .topLeading) {
                MapIconButton(system: "line.3.horizontal") {}
                    .padding(16)
            }
            .sheet(isPresented: .constant(true)) {
                ChooseRideSheet()
                    .presentationDetents([.height(140), .fraction(0.45), .large], selection: $detent)
                    .presentationCornerRadius(28)               // very rounded sheet
                    .presentationBackgroundInteraction(.enabled) // map stays interactive
                    .presentationDragIndicator(.visible)
                    .interactiveDismissDisabled()
            }
    }
}
```

> The system `.sheet` already springs and snaps between detents. For full control over the overshoot, build a custom sheet with a `DragGesture` + `withAnimation(.spring(response: 0.45, dampingFraction: 0.85))` snapping to the nearest detent height.

### Ride-Type Selector Row

```swift
struct RideTypeRow: View {
    let icon: Image
    let name: String
    let subtitle: String
    let capacityEta: String   // "4 seats · 3 min away"
    let price: String
    let isSelected: Bool
    let tap: () -> Void

    var body: some View {
        Button(action: tap) {
            HStack(spacing: 12) {
                icon.resizable().scaledToFit().frame(width: 56, height: 40)
                VStack(alignment: .leading, spacing: 2) {
                    Text(name)
                        .font(.lyftRideName)
                        .foregroundStyle(Color.lyftTextPrimary)
                    Text(subtitle)
                        .font(.lyftRideSub)
                        .foregroundStyle(Color.lyftTextSecondary)
                    Text(capacityEta)
                        .font(.lyftMeta)
                        .foregroundStyle(Color.lyftTextSecondary)
                }
                Spacer()
                Text(price)
                    .font(.lyftPrice).monospacedDigit()
                    .foregroundStyle(Color.lyftTextPrimary)
            }
            .padding(.horizontal, 16)
            .padding(.vertical, 14)
            .background(
                RoundedRectangle(cornerRadius: 16)
                    .fill(isSelected ? Color.lyftPinkTint : .clear)
            )
            .overlay(
                RoundedRectangle(cornerRadius: 16)
                    .strokeBorder(isSelected ? Color.lyftPink : .clear, lineWidth: 2)
            )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.selection, trigger: isSelected)
        .animation(.spring(response: 0.25, dampingFraction: 0.8), value: isSelected)
    }
}
```

### Primary CTA

```swift
struct LyftCTA: View {
    let label: String     // "Select Lyft"
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.lyftButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 56)
                .background(RoundedRectangle(cornerRadius: 16).fill(Color.lyftPink))
        }
        .buttonStyle(LyftPressableStyle())
    }
}

struct LyftPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .brightness(configuration.isPressed ? -0.05 : 0)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Pickup Pin (with drop)

```swift
struct PickupPin: View {
    @State private var dropped = false

    var body: some View {
        ZStack {
            // teardrop body
            PinShape()
                .fill(Color.lyftPink)
                .frame(width: 28, height: 38)
                .shadow(color: Color.lyftTextPrimary.opacity(0.30),
                        radius: dropped ? 8 : 3, y: dropped ? 6 : 2)
            Circle().fill(.white).frame(width: 10, height: 10).offset(y: -6)
        }
        .scaleEffect(dropped ? 1 : 0.6)
        .offset(y: dropped ? 0 : -40)
        .onAppear {
            withAnimation(.spring(response: 0.4, dampingFraction: 0.6)) { dropped = true }
        }
    }
}

struct PinShape: Shape {
    func path(in r: CGRect) -> Path {           // simple teardrop with a precise tip
        var p = Path()
        p.addArc(center: CGPoint(x: r.midX, y: r.width / 2),
                 radius: r.width / 2, startAngle: .degrees(180), endAngle: .degrees(0), clockwise: false)
        p.addLine(to: CGPoint(x: r.midX, y: r.maxY))
        p.closeSubpath()
        return p
    }
}
```

### "Where to?" Search Bar

```swift
struct WhereToBar: View {
    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 3)
                .fill(Color.lyftPink).frame(width: 10, height: 10)
            Text("Where to?")
                .font(.lyftWhereTo)
                .foregroundStyle(Color.lyftTextSecondary)
            Spacer()
        }
        .padding(.horizontal, 18)
        .frame(height: 56)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.lyftCanvas))
        .shadow(color: Color.lyftTextPrimary.opacity(0.16), radius: 12, y: 4)
        .padding(.horizontal, 20)
    }
}
```

## 4. Sheet State Progression

```swift
enum TripState { case idle, chooseRide, confirmPickup, enRoute, rate }
// Drive sheet content + detent from this single state:
// idle → height(140) "Where to?"; chooseRide → fraction(0.45) ride list;
// confirmPickup → fraction(0.35) address + Confirm; enRoute → fraction(0.4) driver card;
// rate → fraction(0.5) rating + tip. Animate state changes inside withAnimation(.spring).
```

## 5. Navigation (No Tab Bar)

Lyft has **no `TabView`**. The root is a `MapView` with a `.sheet`; navigation between trip phases is state-driven, not tabbed. A floating menu button (top-left) presents account/history/payment as a separate sheet or `NavigationStack` push.

```swift
struct MapIconButton: View {
    let system: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: system)
                .font(.system(size: 20, weight: .semibold))
                .foregroundStyle(Color.lyftTextPrimary)
                .frame(width: 44, height: 44)
                .background(Circle().fill(Color.lyftCanvas))
                .shadow(color: Color.lyftTextPrimary.opacity(0.16), radius: 12, y: 4)
        }
    }
}
```

## 6. Motion

```swift
// Sheet detent snap (custom sheet): slight overshoot then settle
withAnimation(.spring(response: 0.45, dampingFraction: 0.85)) { detentHeight = nearest }

// Ride-type select
.sensoryFeedback(.selection, trigger: selectedRideID)
.animation(.spring(response: 0.25, dampingFraction: 0.8), value: selectedRideID)

// Pickup pin drop (bounce + shadow scale)
withAnimation(.spring(response: 0.4, dampingFraction: 0.6)) { dropped = true }
.sensoryFeedback(.impact(weight: .soft), trigger: dropped)

// CTA press
.scaleEffect(isPressed ? 0.98 : 1)

// Map recenter: animate MKMapCamera / MapCameraPosition with .easeInOut(duration: 0.4)
// ETA bubble: cross-fade the number (tabular figures prevent layout jump)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Menu (map) | `line.3.horizontal` | 20pt |
| Back / dismiss | `chevron.left` | 20pt |
| Recenter / locate | `location.fill` | 20pt |
| Layers | `square.3.layers.3d` | 20pt |
| Where-to dot | (drawn pink square) | 10pt |
| Search | `magnifyingglass` | 18pt |
| Saved: Home | `house.fill` | 18pt |
| Saved: Work | `briefcase.fill` | 18pt |
| Rating star | `star.fill` | 14pt |
| Contact driver | `phone.fill` / `message.fill` | 18pt |
| Share trip | `square.and.arrow.up` | 18pt |
| Safety | `shield.fill` | 18pt |
| Destination pin | (drawn near-black teardrop) | — |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16.4 (for `.presentationCornerRadius` / `.presentationBackgroundInteraction`); `.sensoryFeedback` needs iOS 17 (fall back to `UIImpactFeedbackGenerator(style: .soft)` / `UISelectionFeedbackGenerator`)
- Support Dynamic Type on sheet titles, ride-type names, body; keep prices/ETAs tabular and right-aligned; pin badges and pin labels
- VoiceOver: announce ride-type rows as "Standard, 4 seats, 3 minutes away, 18 dollars 40, selected" with `.accessibilityAddTraits(.isSelected)`; the CTA reads "Select Lyft"; the map exposes a summary ("Map, pickup set, driver 3 minutes away")
- Respect `@Environment(\.colorScheme)` — provide the full dark palette; the pink stays identical on both
- Reduce Motion: replace the sheet overshoot and pin bounce with a quick fade/slide when `accessibilityReduceMotion` is on
- Contrast: `#6B6B7B` on `#FFFFFF` and `#A0A0B4` on `#11111F` pass WCAG AA at 14pt+. Lyft Pink `#FF00BF` on white passes AA for bold 17pt+ — keep the CTA bold; for white-on-pink verify the pressed `#D500A0` state too
