# Domino's (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Domino's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views — including the signature five-stage pizza tracker.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let dpzCanvas      = Color.white                                  // #FFFFFF
    static let dpzSurface     = Color(red: 0.957, green: 0.957, blue: 0.957) // #F4F4F4
    static let dpzSurfaceDeep = Color(red: 0.918, green: 0.918, blue: 0.918) // #EAEAEA
    static let dpzDivider     = Color(red: 0.886, green: 0.886, blue: 0.886) // #E2E2E2

    // MARK: - Text
    static let dpzTextPrimary   = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let dpzTextSecondary = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E
    static let dpzTextTertiary  = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A

    // MARK: - Brand
    static let dpzRed         = Color(red: 0.890, green: 0.094, blue: 0.216) // #E31837
    static let dpzRedPressed  = Color(red: 0.761, green: 0.071, blue: 0.180) // #C2122E
    static let dpzRedTint     = Color(red: 0.988, green: 0.906, blue: 0.922) // #FCE7EB
    static let dpzBlue        = Color(red: 0.0,   green: 0.392, blue: 0.569) // #006491
    static let dpzBluePressed = Color(red: 0.0,   green: 0.329, blue: 0.478) // #00547A
    static let dpzBlueTint    = Color(red: 0.878, green: 0.933, blue: 0.957) // #E0EEF4

    // MARK: - Semantic
    static let dpzSuccess = Color(red: 0.118, green: 0.557, blue: 0.243) // #1E8E3E
    static let dpzWarning = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
}
```

## 2. Typography

Domino's uses Archivo. Bundle the TTFs via `Info.plist` (`UIAppFonts`); fall back to Helvetica Neue / `.system(design: .default)`.

```swift
extension Font {
    static let dpzScreenTitle  = Font.custom("Archivo-Bold",    size: 26)
    static let dpzTrackerStage = Font.custom("Archivo-Bold",    size: 22)
    static let dpzSection      = Font.custom("Archivo-Bold",    size: 18)
    static let dpzItemName     = Font.custom("Archivo-Bold",    size: 17)
    static let dpzPrice        = Font.custom("Archivo-Bold",    size: 16)
    static let dpzBody         = Font.custom("Archivo-Regular", size: 15)
    static let dpzButton       = Font.custom("Archivo-Bold",    size: 16)
    static let dpzDealPrice    = Font.custom("Archivo-Bold",    size: 28)
    static let dpzMeta         = Font.custom("Archivo-Regular", size: 13)
    static let dpzBadge        = Font.custom("Archivo-Bold",    size: 11)
    static let dpzTab          = Font.custom("Archivo-Bold",    size: 11)
    static let dpzCaption      = Font.custom("Archivo-Regular", size: 11)
}

extension Font {
    static func dpz(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Prices, ETAs, and deal callouts use tabular figures — apply `.monospacedDigit()`.

## 3. Signature Components

### Five-Stage Pizza Tracker

```swift
enum TrackerStage: Int, CaseIterable {
    case prep, bake, box, quality, out
    var label: String {
        switch self {
        case .prep: "Prep"; case .bake: "Bake"; case .box: "Box"
        case .quality: "Quality Check"; case .out: "Out for delivery"
        }
    }
    var headline: String {
        switch self {
        case .prep: "We're prepping your order"
        case .bake: "Your pizza is in the oven!"
        case .box: "Boxing it up"
        case .quality: "Quality check in progress"
        case .out: "Out for delivery!"
        }
    }
}

struct PizzaTracker: View {
    let current: TrackerStage
    let eta: String

    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            Text(current.headline)
                .font(.dpzTrackerStage)
                .foregroundStyle(Color.dpzTextPrimary)

            HStack(spacing: 0) {
                ForEach(TrackerStage.allCases, id: \.rawValue) { stage in
                    node(for: stage)
                    if stage != .out {
                        Rectangle()
                            .fill(stage.rawValue < current.rawValue ? Color.dpzBlue :
                                  stage.rawValue == current.rawValue ? Color.dpzRed : Color.dpzDivider)
                            .frame(height: 4)
                    }
                }
            }

            Text("Estimated delivery \(eta)")
                .font(.dpzMeta)
                .foregroundStyle(Color.dpzTextSecondary)

            Button("Track on the map") {}
                .font(.dpz(14, weight: .bold))
                .foregroundStyle(Color.dpzBlue)
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.dpzCanvas))
        .shadow(color: Color.dpzTextPrimary.opacity(0.12), radius: 14, y: 4)
    }

    @ViewBuilder private func node(for stage: TrackerStage) -> some View {
        let completed = stage.rawValue < current.rawValue
        let active = stage.rawValue == current.rawValue
        VStack(spacing: 6) {
            ZStack {
                Circle()
                    .fill(completed ? Color.dpzBlue : active ? Color.dpzRed : .clear)
                    .frame(width: 24, height: 24)
                Circle()
                    .strokeBorder(completed ? Color.dpzBlue : active ? Color.dpzRed : Color.dpzDivider,
                                  lineWidth: 2)
                    .frame(width: 24, height: 24)
                if completed {
                    Image(systemName: "checkmark")
                        .font(.system(size: 11, weight: .bold))
                        .foregroundStyle(.white)
                }
            }
            .scaleEffect(active ? 1.06 : 1)
            .animation(active ?
                .easeInOut(duration: 0.8).repeatForever(autoreverses: true) : .default,
                value: active)
            Text(stage.label)
                .font(.dpz(11, weight: completed || active ? .bold : .regular))
                .foregroundStyle(active ? Color.dpzRed :
                                 completed ? Color.dpzTextPrimary : Color.dpzTextTertiary)
                .fixedSize()
        }
        .frame(width: 56)
    }
}
```

### Primary CTA

```swift
struct DominosCTA: View {
    let label: String     // "ADD TO ORDER · $13.99"
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.dpzButton)
                .tracking(0.3)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 52)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.dpzRed))
        }
        .buttonStyle(DPZPressableStyle())
    }
}

struct DPZPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .brightness(configuration.isPressed ? -0.04 : 0)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Deal Card

```swift
struct DealCard: View {
    let price: String       // "$7.99"
    let name: String
    let finePrint: String
    let add: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack {
                Text(price).font(.dpzDealPrice).monospacedDigit().foregroundStyle(Color.dpzRed)
                Spacer()
                DominoMark()      // small red/blue square motif
            }
            Text(name).font(.dpzItemName).foregroundStyle(Color.dpzTextPrimary)
            Text(finePrint).font(.dpzMeta).foregroundStyle(Color.dpzTextSecondary)
            Spacer()
            Button(action: add) {
                Text("ADD DEAL")
                    .font(.dpz(13, weight: .bold)).foregroundStyle(.white)
                    .padding(.vertical, 8).padding(.horizontal, 16)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.dpzRed))
            }
            .frame(maxWidth: .infinity, alignment: .trailing)
        }
        .padding(16)
        .frame(height: 150)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.dpzCanvas))
        .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.dpzRed, lineWidth: 2))
    }
}

struct DominoMark: View {  // red/blue split square
    var body: some View {
        HStack(spacing: 1) {
            Rectangle().fill(Color.dpzRed)
            Rectangle().fill(Color.dpzBlue)
        }
        .frame(width: 22, height: 22)
        .clipShape(RoundedRectangle(cornerRadius: 4))
    }
}
```

### Build-Your-Pizza Live Preview

```swift
struct PizzaPreview: View {
    let crustThickness: CGFloat   // ring width by crust
    let sauceColor: Color
    let hasCheese: Bool
    let toppings: [String]

    var body: some View {
        ZStack {
            Circle().fill(Color(red: 0.93, green: 0.85, blue: 0.66))           // crust base
            Circle().strokeBorder(Color(red: 0.80, green: 0.62, blue: 0.36),
                                   lineWidth: crustThickness)                  // crust ring
            Circle().fill(sauceColor).padding(crustThickness + 6)              // sauce wash
            if hasCheese {
                Circle().fill(Color(red: 0.98, green: 0.86, blue: 0.45).opacity(0.6))
                    .padding(crustThickness + 10)
            }
            // toppings scattered as small circles around the pie (omitted for brevity)
        }
        .frame(width: 160, height: 160)
        .animation(.easeOut(duration: 0.25), value: toppings)
        .animation(.easeOut(duration: 0.25), value: hasCheese)
    }
}
```

## 4. Tracker State Progression

```swift
// Advancing a stage: the prior node settles blue+check, the new node turns red,
// the rail segment fills red. Drive with a single @State current stage and let the
// node/rail color closures recompute; wrap the change in withAnimation for the
// ~600ms ease-out hand-off and fire a soft haptic.
withAnimation(.easeOut(duration: 0.6)) { current = next }
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(Color.dpzDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            OrderView().tabItem   { Label("Order",   systemImage: "menucard") }
            TrackerView().tabItem { Label("Tracker", systemImage: "location.circle") }
            DealsView().tabItem   { Label("Deals",   systemImage: "tag") }
            AccountView().tabItem { Label("Account", systemImage: "person") }
        }
        .tint(.dpzRed) // active = Domino's Red
    }
}
```

## 6. Motion

```swift
// Tracker stage advance (~600ms ease-out hand-off) + soft haptic
withAnimation(.easeOut(duration: 0.6)) { current = next }
.sensoryFeedback(.impact(weight: .soft), trigger: current)

// Active node pulse (1.0 → 1.06 → 1.0 loop)
.animation(.easeInOut(duration: 0.8).repeatForever(autoreverses: true), value: isActive)

// Build-pizza layer add (~250ms)
.animation(.easeOut(duration: 0.25), value: toppings)

// Add to Order
.sensoryFeedback(.success, trigger: didAddToOrder)
// then bump cart badge scale 1.0 → 1.2 → 1.0 with .spring

// Sheet present: .presentationDetents + default slide
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Completed stage check | `checkmark` | 11pt |
| Order (tab) | `menucard` | 24pt |
| Tracker (tab) | `location.circle` / `.fill` | 24pt |
| Deals (tab) | `tag` / `tag.fill` | 24pt |
| Account (tab) | `person` / `person.fill` | 24pt |
| Cart | `cart` / `cart.fill` | 24pt |
| Search | `magnifyingglass` | 18pt |
| Store chevron | `chevron.down` | 14pt |
| Quantity minus / plus | `minus` / `plus` | 14pt |
| Map / track | `map` | 16pt |
| Time / ETA | `clock` | 13pt |
| Back | `chevron.left` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator(style: .soft)` and `UINotificationFeedbackGenerator`)
- Support Dynamic Type on item names, body, descriptions; the tracker headline scales to a max and wraps to at most 2 lines; pin tab labels and tracker node labels (abbreviate on small widths)
- VoiceOver: expose the tracker as a single status — `.accessibilityLabel("Order status: Bake, stage 2 of 5, estimated delivery 7:45 PM")` — and hide the decorative nodes/rail
- The CTA reads "Add to order, total $13.99"; deal cards read "Mix and Match deal, $7.99, add"
- Contrast: `#1F1F1F` on `#FFFFFF` is maximal. `#6E6E6E` secondary on white passes WCAG AA at 13pt+. Domino's Red `#E31837` text on white passes AA for bold 16pt+ — keep small red text bold
- Don't rely on color alone for tracker state — completed nodes carry a checkmark glyph and labels switch to bold, in addition to the blue/red/gray coding
