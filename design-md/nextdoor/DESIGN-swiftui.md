# Nextdoor (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Nextdoor's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (light only — warm cream)
    static let ndCream     = Color(red: 0.980, green: 0.976, blue: 0.965) // #FAF9F6
    static let ndSurface1  = Color.white                                  // #FFFFFF
    static let ndSurface2  = Color(red: 0.949, green: 0.941, blue: 0.922) // #F2F0EB
    static let ndDivider   = Color(red: 0.894, green: 0.886, blue: 0.867) // #E4E2DD

    // MARK: - Text (warm ink)
    static let ndText      = Color(red: 0.133, green: 0.118, blue: 0.122) // #221E1F
    static let ndTextSec   = Color(red: 0.420, green: 0.408, blue: 0.392) // #6B6864
    static let ndTextTer   = Color(red: 0.612, green: 0.596, blue: 0.576) // #9C9893

    // MARK: - Brand
    static let ndGreen        = Color(red: 0.0,   green: 0.698, blue: 0.275) // #00B246
    static let ndGreenPressed = Color(red: 0.0,   green: 0.478, blue: 0.188) // #007A30
    static let ndLinkForest   = Color(red: 0.0,   green: 0.420, blue: 0.235) // #006B3C

    // MARK: - Semantic / Map categories
    static let ndAlertAmber = Color(red: 0.898, green: 0.541, blue: 0.0)   // #E58A00
    static let ndError      = Color(red: 0.851, green: 0.227, blue: 0.169) // #D93A2B
    static let ndEventBlue  = Color(red: 0.106, green: 0.435, blue: 0.702) // #1B6FB3
    static let ndForSale    = Color(red: 0.431, green: 0.310, blue: 0.639) // #6E4FA3
}
```

## 2. Typography

Nextdoor uses **Lato**. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to `.system` (SF Pro) at the same sizes/weights if Lato is unavailable.

```swift
extension Font {
    static let ndTitleLarge  = Font.custom("Lato-Bold",    size: 28)
    static let ndSection     = Font.custom("Lato-Bold",    size: 22)
    static let ndPostTitle   = Font.custom("Lato-Bold",    size: 18)
    static let ndName        = Font.custom("Lato-Bold",    size: 16)
    static let ndBody        = Font.custom("Lato-Regular", size: 16)   // 1.45 line height via lineSpacing
    static let ndBodySettings = Font.custom("Lato-Regular", size: 15)
    static let ndMeta        = Font.custom("Lato-Regular", size: 13)
    static let ndChip        = Font.custom("Lato-Bold",    size: 14)
    static let ndActionLabel = Font.custom("Lato-Bold",    size: 14)
    static let ndButton      = Font.custom("Lato-Bold",    size: 16)
    static let ndTab         = Font.custom("Lato-Bold",    size: 10)
    static let ndPinLabel    = Font.custom("Lato-Bold",    size: 12)

    // System fallback at aligned sizes if Lato is missing
    static func nd(_ size: CGFloat, bold: Bool = false) -> Font {
        .system(size: size, weight: bold ? .bold : .regular)
    }
}

// 16pt body at 1.45 → lineSpacing ≈ 16 * 0.45 ≈ 7pt
extension View {
    func ndBodyText() -> some View { self.font(.ndBody).lineSpacing(7) }
}
```

## 3. Signature Components

### Neighborhood Feed Card (the core unit)

```swift
struct FeedCard: View {
    let name: String
    let verified: Bool
    let location: String        // "0.3 mi · Maple Heights · 2h"
    let body: String
    let avatar: Image

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack(alignment: .top, spacing: 12) {
                avatar.resizable().frame(width: 44, height: 44).clipShape(Circle())
                VStack(alignment: .leading, spacing: 2) {
                    HStack(spacing: 4) {
                        Text(name).font(.ndName).foregroundStyle(Color.ndText)
                        if verified {
                            Image(systemName: "checkmark.seal.fill")
                                .font(.system(size: 14)).foregroundStyle(Color.ndGreen)
                        }
                    }
                    Text(location).font(.ndMeta).foregroundStyle(Color.ndTextSec)
                }
                Spacer()
                Image(systemName: "ellipsis").foregroundStyle(Color.ndTextSec)
            }

            Text(body).ndBodyText().foregroundStyle(Color.ndText)

            Divider().overlay(Color.ndDivider)

            HStack {
                CardAction(symbol: "hand.thumbsup", label: "React")
                Spacer()
                CardAction(symbol: "bubble.left", label: "Reply")
                Spacer()
                CardAction(symbol: "square.and.arrow.up", label: "Share")
            }
        }
        .padding(16)
        .background(Color.ndSurface1)
        .clipShape(RoundedRectangle(cornerRadius: 16))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.ndDivider, lineWidth: 1))
        .shadow(color: Color.ndText.opacity(0.06), radius: 3, y: 1)
        .padding(.horizontal, 16)
        .padding(.bottom, 12)
    }
}

struct CardAction: View {
    let symbol: String; let label: String
    @State private var active = false
    @State private var scale: CGFloat = 1
    var body: some View {
        Button {
            active.toggle()
            withAnimation(.easeOut(duration: 0.12)) { scale = 1.12 }
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.12) {
                withAnimation(.easeOut) { scale = 1 }
            }
        } label: {
            HStack(spacing: 6) {
                Image(systemName: symbol).font(.system(size: 20)).scaleEffect(scale)
                Text(label).font(.ndActionLabel)
            }
            .foregroundStyle(active ? Color.ndGreen : Color.ndTextSec)
        }
        .frame(minHeight: 44)
        .sensoryFeedback(.impact(weight: .light), trigger: active)
    }
}
```

### Primary Button & Center Post FAB

```swift
struct NDPrimaryButton: View {
    let title: String
    var style: Style = .filled
    let action: () -> Void
    enum Style { case filled, outline }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.ndButton)
                .foregroundStyle(style == .filled ? .white : Color.ndGreen)
                .padding(.vertical, style == .filled ? 12 : 10)
                .padding(.horizontal, style == .filled ? 24 : 20)
                .background(RoundedRectangle(cornerRadius: 24).fill(style == .filled ? Color.ndGreen : .clear))
                .overlay(RoundedRectangle(cornerRadius: 24)
                    .strokeBorder(style == .outline ? Color.ndGreen : .clear, lineWidth: 1.5))
        }
        .buttonStyle(NDPressable())
    }
}

struct CenterPostFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 26, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.ndGreen))
                .shadow(color: Color.ndGreen.opacity(0.32), radius: 18, y: 6)
        }
        .buttonStyle(NDPressable(pressedScale: 0.94))
        .sensoryFeedback(.impact(weight: .soft), trigger: UUID())
    }
}

struct NDPressable: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.easeOut(duration: 0.2), value: configuration.isPressed)
    }
}
```

### Group Chip Row

```swift
struct GroupChipRow: View {
    let groups: [String]
    @Binding var selected: String

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(groups, id: \.self) { g in
                    Text(g)
                        .font(.ndChip)
                        .foregroundStyle(selected == g ? .white : Color.ndText)
                        .padding(.vertical, 8).padding(.horizontal, 16)
                        .background(Capsule().fill(selected == g ? Color.ndGreen : Color.ndSurface2))
                        .onTapGesture { withAnimation(.easeInOut(duration: 0.2)) { selected = g } }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

## 4. Distinctive System — Hyperlocal Map Pin

```swift
struct MapPin: View {
    enum Category { case recommendation, event, forSale, alert
        var color: Color {
            switch self {
            case .recommendation: return .ndGreen
            case .event:          return .ndEventBlue
            case .forSale:        return .ndForSale
            case .alert:          return .ndAlertAmber
            }
        }
    }
    let category: Category
    let label: String           // "$45" or a glyph
    let selected: Bool

    var body: some View {
        Text(label)
            .font(.ndPinLabel)
            .foregroundStyle(.white)
            .padding(.vertical, 6).padding(.horizontal, 10)
            .background(Capsule().fill(category.color))
            .overlay(
                Capsule().strokeBorder(.white, lineWidth: selected ? 2 : 0)
            )
            .overlay(
                Capsule().strokeBorder(Color.ndGreen, lineWidth: selected ? 2 : 0)
                    .padding(-3)
            )
            .scaleEffect(selected ? 1.15 : 1)
            .shadow(color: Color.ndText.opacity(0.25), radius: 6, y: 2)
            .animation(.easeOut(duration: 0.2), value: selected)
    }
}
```

Drop these as `MapAnnotation` content in a SwiftUI `Map` (`MapKit`). Tapping a pin slides up a peek sheet (`.sheet` with `.presentationDetents([.height(220), .large])`).

## 5. Tab Bar

Nextdoor's tab bar is opaque white on cream with a raised green center Post button. A plain `TabView` can't host a center FAB cleanly — use a custom bar overlaying the content.

```swift
struct RootView: View {
    @State private var tab = 0
    var body: some View {
        ZStack(alignment: .bottom) {
            Group {
                switch tab {
                case 0: HomeView()
                case 1: MapView()
                case 3: NotificationsView()
                default: InboxView()
                }
            }
            HStack {
                tabButton(0, "house", "Home")
                tabButton(1, "map", "Map")
                CenterPostFAB { /* present composer */ }.offset(y: -14)
                tabButton(3, "bell", "Notifications")
                tabButton(4, "envelope", "Inbox")
            }
            .padding(.horizontal, 12).padding(.top, 8).padding(.bottom, 0)
            .background(Color.ndSurface1)
            .overlay(Rectangle().fill(Color.ndDivider).frame(height: 0.5), alignment: .top)
        }
    }
    func tabButton(_ i: Int, _ sys: String, _ label: String) -> some View {
        Button { tab = i } label: {
            VStack(spacing: 3) {
                Image(systemName: sys).font(.system(size: 24))
                Text(label).font(.ndTab)
            }
            .foregroundStyle(tab == i ? Color.ndGreen : Color.ndTextSec)
            .frame(maxWidth: .infinity)
        }
    }
}
```

## 6. Motion

```swift
// FAB press: scale 0.94, #007A30, soft haptic; sheet rises 0.3s ease-out
.buttonStyle(NDPressable(pressedScale: 0.94))
.sensoryFeedback(.impact(weight: .soft), trigger: tapped)

// Reaction tap: fade to #00B246 + subtle 1.0 → 1.12 → 1.0 over 250ms, light haptic (see CardAction)

// Card appear: fade + 8pt rise over 0.25s ease-out
.transition(.opacity.combined(with: .move(edge: .bottom)))

// Map pin select: scale 1.0 → 1.15 + green ring (see MapPin); peek sheet 0.3s ease-out

// Chip switch: 0.2s cross-fade of the feed
withAnimation(.easeInOut(duration: 0.2)) { selected = group }
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| React | `hand.thumbsup` / `hand.thumbsup.fill` | 20pt |
| Reply | `bubble.left` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| More | `ellipsis` | 20pt |
| Verified neighbor | `checkmark.seal.fill` | 14pt |
| Post (center FAB) | `plus` | 26pt |
| Home (tab) | `house` / `house.fill` | 26pt |
| Map (tab) | `map` / `map.fill` | 26pt |
| Notifications (tab) | `bell` / `bell.fill` | 26pt |
| Inbox (tab) | `envelope` / `envelope.fill` | 26pt |
| Search | `magnifyingglass` | 22pt |
| Map recenter | `location.fill` | 20pt |
| Event pin | `calendar` | 12pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` prefer iOS 17; fall back to `UIImpactFeedbackGenerator`). `MapKit` SwiftUI `Map` annotations are comfortable on iOS 16+.
- Bundle Lato via `UIAppFonts`; if it fails to load, the `Font.nd(_:bold:)` system fallback keeps sizes/weights aligned
- Support Dynamic Type generously on post body, names, and headlines — Nextdoor is a reading app; pin only tab labels and map-pin labels
- VoiceOver: announce a card as "<name>, verified neighbor, <location line>, <body>"; the verified badge needs an explicit label ("Verified neighbor"); map pins should read category + label ("For sale, $45, double-tap to view")
- Contrast: `#6B6864` secondary on `#FAF9F6` cream passes WCAG AA at 13pt+; verify `#006B3C` link green on cream meets AA (it does at body sizes)
- This is light-only — do not implement a dark scheme; if the system is in dark mode, keep the cream canvas (it is the brand)
- Respect Reduce Motion: replace the reaction scale and card rise with instant changes; keep the map-pin selection as a color/ring change without scaling
