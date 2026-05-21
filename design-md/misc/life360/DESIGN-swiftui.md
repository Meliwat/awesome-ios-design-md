# Life360 (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Life360's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default)
    static let l3Canvas    = Color(red: 0.086, green: 0.075, blue: 0.145) // #161325
    static let l3Surface1  = Color(red: 0.122, green: 0.106, blue: 0.200) // #1F1B33
    static let l3Surface2  = Color(red: 0.165, green: 0.145, blue: 0.259) // #2A2542
    static let l3Sheet     = Color(red: 0.129, green: 0.114, blue: 0.212) // #211D36
    static let l3Divider   = Color(red: 0.200, green: 0.180, blue: 0.302) // #332E4D
    static let l3Border    = Color(red: 0.239, green: 0.216, blue: 0.349) // #3D3759

    // MARK: - Map Palette (dark-styled)
    static let l3MapBase   = Color(red: 0.102, green: 0.129, blue: 0.220) // #1A2138
    static let l3MapRoad   = Color(red: 0.173, green: 0.212, blue: 0.329) // #2C3654
    static let l3MapPark   = Color(red: 0.106, green: 0.227, blue: 0.180) // #1B3A2E
    static let l3MapWater  = Color(red: 0.086, green: 0.192, blue: 0.290) // #16314A

    // MARK: - Canvas (Light)
    static let l3CanvasLight  = Color.white                                   // #FFFFFF
    static let l3SurfaceLight = Color(red: 0.961, green: 0.953, blue: 0.980) // #F5F3FA
    static let l3MapBaseLight = Color(red: 0.929, green: 0.937, blue: 0.961) // #EDEFF5

    // MARK: - Text
    static let l3TextPrimary   = Color(red: 0.925, green: 0.918, blue: 0.961) // #ECEAF5
    static let l3TextSecondary = Color(red: 0.651, green: 0.627, blue: 0.761) // #A6A0C2
    static let l3TextTertiary  = Color(red: 0.435, green: 0.412, blue: 0.565) // #6F6990
    static let l3TextOnLight   = Color(red: 0.110, green: 0.094, blue: 0.188) // #1C1830

    // MARK: - Brand (Life360 Purple — never a member color)
    static let l3Purple        = Color(red: 0.345, green: 0.173, blue: 0.514) // #582C83
    static let l3PurpleLight   = Color(red: 0.494, green: 0.341, blue: 0.761) // #7E57C2
    static let l3PurpleBright  = Color(red: 0.545, green: 0.361, blue: 0.965) // #8B5CF6
    static let l3PurplePressed = Color(red: 0.290, green: 0.141, blue: 0.439) // #4A2470

    // MARK: - Member Identity Colors (the semantic core — fixed per person)
    static let l3MemberAmber  = Color(red: 0.949, green: 0.639, blue: 0.235) // #F2A33C
    static let l3MemberTeal   = Color(red: 0.176, green: 0.831, blue: 0.749) // #2DD4BF
    static let l3MemberPink   = Color(red: 0.957, green: 0.447, blue: 0.714) // #F472B6
    static let l3MemberBlue   = Color(red: 0.376, green: 0.647, blue: 0.980) // #60A5FA
    static let l3MemberCoral  = Color(red: 0.984, green: 0.443, blue: 0.522) // #FB7185
    static let l3MemberLime   = Color(red: 0.639, green: 0.902, blue: 0.208) // #A3E635
    static let l3MemberSky    = Color(red: 0.220, green: 0.741, blue: 0.973) // #38BDF8

    // MARK: - Safety Semantic (reserved — rationed)
    static let l3Safe    = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759
    static let l3SOS     = Color(red: 1.000, green: 0.420, blue: 0.420) // #FF6B6B
    static let l3Warning = Color(red: 1.000, green: 0.690, blue: 0.125) // #FFB020
}

/// Member identity palette — assign in Circle join order; fixed per person everywhere.
enum MemberColor: Int, CaseIterable {
    case amber, teal, pink, blue, coral, lime, sky
    var color: Color {
        switch self {
        case .amber: .l3MemberAmber; case .teal: .l3MemberTeal; case .pink: .l3MemberPink
        case .blue: .l3MemberBlue; case .coral: .l3MemberCoral; case .lime: .l3MemberLime
        case .sky: .l3MemberSky
        }
    }
}
```

## 2. Typography

Life360's brand face is a friendly rounded-humanist sans; **Plus Jakarta Sans** is the closest free analog. Bundle via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let l3Display    = Font.custom("PlusJakartaSans-ExtraBold", size: 32).weight(.heavy)
    static let l3Title      = Font.custom("PlusJakartaSans-Bold",      size: 26).weight(.bold)
    static let l3Section    = Font.custom("PlusJakartaSans-Bold",      size: 22).weight(.bold)
    static let l3MemberName = Font.custom("PlusJakartaSans-Bold",      size: 18).weight(.bold)
    static let l3Body       = Font.custom("PlusJakartaSans-Regular",   size: 16)
    static let l3RowTitle   = Font.custom("PlusJakartaSans-SemiBold",  size: 15).weight(.semibold)
    static let l3Status     = Font.custom("PlusJakartaSans-SemiBold",  size: 13).weight(.semibold)
    static let l3Meta       = Font.custom("PlusJakartaSans-Regular",   size: 14)
    static let l3Caption    = Font.custom("PlusJakartaSans-SemiBold",  size: 12).weight(.semibold)
    static let l3PinInitial = Font.custom("PlusJakartaSans-Bold",      size: 16).weight(.bold)
    static let l3Tab        = Font.custom("PlusJakartaSans-SemiBold",  size: 10).weight(.semibold)
    static let l3Button     = Font.custom("PlusJakartaSans-Bold",      size: 15).weight(.bold)
    static let l3SOS        = Font.custom("PlusJakartaSans-ExtraBold", size: 15).weight(.heavy)
}
```

> Fallback when the font isn't bundled: `Font.system(size:, weight:, design: .rounded)` keeps the friendly tone.

## 3. Signature Components

### Member Pin (the atomic Life360 element)

```swift
struct MemberPin: View {
    let initial: String
    let color: Color           // the member's FIXED identity color
    let photo: Image?
    var selected: Bool = false

    var body: some View {
        VStack(spacing: 0) {
            ZStack {
                Circle().fill(color)
                if let photo {
                    photo.resizable().scaledToFill()
                        .clipShape(Circle())
                } else {
                    Text(initial).font(.l3PinInitial).foregroundStyle(.white)
                }
            }
            .frame(width: 46, height: 46)
            .overlay(Circle().strokeBorder(.white, lineWidth: selected ? 4 : 3))
            .overlay(
                Circle().strokeBorder(color.opacity(selected ? 0.45 : 0), lineWidth: 8)
                    .blur(radius: 4)
            )
            .shadow(color: .black.opacity(0.5), radius: 12, y: 4)

            // downward pointer
            Triangle().fill(.white)
                .frame(width: 12, height: 8)
                .offset(y: -1)
        }
        .animation(.spring(response: 0.3, dampingFraction: 0.7), value: selected)
    }
}

struct Triangle: Shape {
    func path(in r: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: r.midX, y: r.maxY))
        p.addLine(to: CGPoint(x: r.minX, y: r.minY))
        p.addLine(to: CGPoint(x: r.maxX, y: r.minY))
        p.closeSubpath(); return p
    }
}
```

### Member Sheet Row

```swift
struct MemberRow: View {
    let initial: String
    let color: Color
    let name: String
    let placeText: String      // "At Home · 2 min ago"
    let placeIcon: String      // SF Symbol
    let battery: Int           // 0...100

    var batteryColor: Color { battery <= 20 ? .l3SOS : .l3Safe }

    var body: some View {
        HStack(spacing: 12) {
            ZStack {
                Circle().fill(color)
                Text(initial).font(.l3RowTitle).foregroundStyle(.white)
            }.frame(width: 42, height: 42)

            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.l3MemberName).foregroundStyle(Color.l3TextPrimary)
                HStack(spacing: 5) {
                    Image(systemName: placeIcon).font(.system(size: 11, weight: .semibold))
                    Text(placeText).font(.l3Status)
                }.foregroundStyle(Color.l3TextSecondary)
            }
            Spacer(minLength: 8)

            HStack(spacing: 5) {
                BatteryGlyph(level: battery, color: batteryColor)
                Text("\(battery)%").font(.l3Status).foregroundStyle(batteryColor)
            }
        }
        .padding(.vertical, 10).padding(.horizontal, 18)
        .frame(minHeight: 62)
        .contentShape(Rectangle())
    }
}

struct BatteryGlyph: View {
    let level: Int
    let color: Color
    var body: some View {
        HStack(spacing: 1) {
            RoundedRectangle(cornerRadius: 3)
                .strokeBorder(color, lineWidth: 1.5)
                .frame(width: 22, height: 11)
                .overlay(alignment: .leading) {
                    RoundedRectangle(cornerRadius: 1).fill(color)
                        .frame(width: 19 * CGFloat(level) / 100).padding(1.5)
                }
            RoundedRectangle(cornerRadius: 1).fill(color).frame(width: 2, height: 4)
        }
    }
}
```

### Circle Selector Pill

```swift
struct CircleSelector: View {
    let circleName: String
    let hasAlerts: Bool
    let onSwitch: () -> Void
    let onBell: () -> Void

    var body: some View {
        HStack {
            Button(action: onSwitch) {
                HStack(spacing: 7) {
                    Text(circleName).font(.l3RowTitle.weight(.bold)).foregroundStyle(Color.l3TextPrimary)
                    Image(systemName: "chevron.down").font(.system(size: 12, weight: .bold))
                        .foregroundStyle(Color.l3TextSecondary)
                }
            }.buttonStyle(.plain)
            Spacer()
            Button(action: onBell) {
                Image(systemName: "bell.fill").font(.system(size: 15))
                    .foregroundStyle(Color.l3TextPrimary)
                    .frame(width: 30, height: 30)
                    .background(Circle().fill(Color.l3Surface2))
                    .overlay(alignment: .topTrailing) {
                        if hasAlerts { Circle().fill(Color.l3SOS).frame(width: 8, height: 8) }
                    }
            }.buttonStyle(.plain)
        }
        .padding(.horizontal, 16).padding(.vertical, 10)
        .background(
            Capsule().fill(Color.l3Surface1.opacity(0.92))
                .background(.ultraThinMaterial, in: Capsule())
        )
        .overlay(Capsule().strokeBorder(Color.l3Border, lineWidth: 1))
        .shadow(color: .black.opacity(0.45), radius: 20, y: 6)
    }
}
```

### Primary & SOS Buttons

```swift
struct L3PrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false
    var body: some View {
        Button(action: action) {
            Text(title).font(.l3Button).foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 26)
                .background(Capsule().fill(pressed ? Color.l3PurplePressed : Color.l3PurpleLight))
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .pressEvents { pressed = true } onRelease: { pressed = false }
    }
}

struct SOSButton: View {
    let onTrigger: () -> Void
    @State private var progress: CGFloat = 0
    @GestureState private var holding = false

    var body: some View {
        Text("SOS").font(.l3SOS).tracking(0.5).foregroundStyle(.white)
            .padding(.vertical, 14).padding(.horizontal, 26)
            .background(
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.l3SOS.opacity(0.55))
                    Capsule().fill(Color.l3SOS)
                        .scaleEffect(x: progress, anchor: .leading)
                }
            )
            .clipShape(Capsule())
            .gesture(
                LongPressGesture(minimumDuration: 1.5)
                    .updating($holding) { v, s, _ in s = v }
                    .onChanged { _ in withAnimation(.linear(duration: 1.5)) { progress = 1 } }
                    .onEnded { _ in onTrigger() }
            )
            .onChange(of: holding) { _, active in
                if !active && progress < 1 { withAnimation(.spring()) { progress = 0 } }
            }
            .sensoryFeedback(.impact(weight: .heavy), trigger: progress)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct Life360TabView: View {
    var body: some View {
        TabView {
            MapView().tabItem      { Label("Map",     systemImage: "mappin.and.ellipse") }
            PlacesView().tabItem   { Label("Places",  systemImage: "house.fill") }
            DrivingView().tabItem  { Label("Driving", systemImage: "car.fill") }
            SafetyView().tabItem   { Label("Safety",  systemImage: "shield.fill") }
        }
        .tint(.l3PurpleLight)  // active = Purple Light (brand)
        .toolbarBackground(Color.l3Sheet, for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Pin movement along a path (never teleport) + pulse
withAnimation(.easeInOut(duration: 0.6)) { pinCoordinate = newCoordinate }
.scaleEffect(pulse ? 1.08 : 1.0)
.onChange(of: pinCoordinate) { _, _ in
    withAnimation(.easeInOut(duration: 0.25)) { pulse = true }
    DispatchQueue.main.asyncAfter(deadline: .now() + 0.25) { withAnimation { pulse = false } }
}

// Member sheet detents — native presentationDetents
.presentationDetents([.height(180), .medium, .large])
.presentationBackgroundInteraction(.enabled)
.presentationBackground(Color.l3Sheet)

// Map auto-frame on open / recenter — Map camera ease 400ms
withAnimation(.easeOut(duration: 0.4)) { camera = .region(boundingRegion(allMembers)) }

// Pin tap → focus: ease map 350ms + grow ring + member glow
withAnimation(.easeOut(duration: 0.35)) { focus(member) }

// Geofence entry ripple
.scaleEffect(ripple ? 1.06 : 1.0).opacity(ripple ? 0 : 1)

// Haptics
.sensoryFeedback(.impact(weight: .soft), trigger: detentSnap)   // sheet snap
.sensoryFeedback(.selection, trigger: selectedMember)            // pin tap
.sensoryFeedback(.warning, trigger: newAlert)                    // Alert-red notification
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Map (tab) | `mappin.and.ellipse` | 21pt |
| Places (tab) | `house.fill` | 21pt |
| Driving (tab) | `car.fill` | 21pt |
| Safety (tab) | `shield.fill` | 21pt |
| Member at place | `mappin.circle.fill` | 11pt |
| Member driving | `clock.fill` | 11pt |
| Circle chevron | `chevron.down` | 12pt |
| Notification bell | `bell.fill` | 15pt |
| Battery | custom glyph (see code) | 22×11pt |
| Add place | `plus` | 18pt |
| Recenter map | `location.fill` | 18pt |
| Place — Home | `house.fill` | 14pt |
| Place — School | `graduationcap.fill` | 14pt |
| Place — Work | `briefcase.fill` | 14pt |
| Driving event | `exclamationmark.triangle.fill` | 14pt |
| Crash detected | `car.side.and.exclamationmark` | 16pt |
| Check in | `checkmark.circle.fill` | 16pt |
| Invite | `person.badge.plus` | 16pt |
| Back | `chevron.left` | 17pt |

## 7. Dark Mode

```swift
struct Life360Theme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.l3Canvas : Color.l3CanvasLight)
            .foregroundStyle(scheme == .dark ? Color.l3TextPrimary : Color.l3TextOnLight)
    }
}
extension View { func life360Theme() -> some View { modifier(Life360Theme()) } }
```

Dark mode is a deep violet night (`#161325`) with a dark-styled map (`#1A2138` base, muted roads/parks/water) so member pins stay the brightest objects. In light mode the map switches to a day style (`#EDEFF5` base) but member identity colors, the brand purple, Safe green, and SOS red are **identical** across modes — those carry meaning and must never shift. Pass MapKit a dark `mapStyle` config in dark mode and a standard one in light.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`presentationDetents`, `MapCameraPosition`, and `.sensoryFeedback` (iOS 17) — fall back to `UIImpactFeedbackGenerator` and a custom sheet on iOS 16)
- Bundle Plus Jakarta Sans TTFs via `Info.plist` `UIAppFonts` — SIL OFL licensed for free distribution
- Dynamic Type: support on Display / Title / Section / Member Name / Body / Row Title; keep pin initials, tab labels, map labels, and place-tag text at FIXED sizes (map-layout-sensitive); let member rows grow height to fit scaled text
- VoiceOver: announce a pin as "{name}, at {place}, {time} ago, battery {n} percent"; the identity color is decorative reinforcement — the name always carries identity so color-blind users are never reliant on hue
- Color is not the only signal: member name is always present with the color; battery shows a percentage and a glyph fill, not just red/green; safety state has text ("Left School") not just a red dot
- Contrast: `#ECEAF5` on `#161325` ≈ 14:1 (AAA). Member identity colors are used as fills behind white initials / as pin rings — validated for the white initial; never rely on a member hue as small text on the dark canvas
- The SOS button must be reachable without a precise long-press for motor-impaired users: provide an alternative "Emergency" action in the Safety tab and an accessibility action on the button
- Reduce Motion: disable the pin path animation (snap with a crossfade), the geofence ripple, and the breadcrumb draw; keep the sheet detent snap; never remove the SOS haptic ramp (it's a safety affordance)
- Dark mode: the violet night + dark map is the default; do not auto-switch the map to a bright style at night (colored pins must remain the most salient thing). Safety/brand/member colors are mode-invariant.
