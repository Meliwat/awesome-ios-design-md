# Canva (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Canva's visual language into paste-ready SwiftUI code: `Color` / `LinearGradient` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let canvaWhite        = Color.white                                  // #FFFFFF
    static let canvaWorkspace    = Color(red: 0.969, green: 0.973, blue: 0.980) // #F7F8FA
    static let canvaPressedLight = Color(red: 0.933, green: 0.941, blue: 0.957) // #EEF0F4
    static let canvaDividerLight = Color(red: 0.902, green: 0.910, blue: 0.925) // #E6E8EC

    // MARK: - Canvas & Surfaces (Dark)
    static let canvaCanvasDark = Color(red: 0.094, green: 0.098, blue: 0.106) // #18191B
    static let canvaSurface1   = Color(red: 0.122, green: 0.125, blue: 0.137) // #1F2023
    static let canvaSurface2   = Color(red: 0.153, green: 0.157, blue: 0.173) // #27282C
    static let canvaDivider    = Color(red: 0.188, green: 0.192, blue: 0.208) // #303135

    // MARK: - Text
    static let canvaTextPrimary    = Color(red: 0.925, green: 0.925, blue: 0.933) // #ECECEE (dark)
    static let canvaTextPrimaryLt  = Color(red: 0.051, green: 0.055, blue: 0.063) // #0D0E10 (light)
    static let canvaTextSecondary  = Color(red: 0.643, green: 0.651, blue: 0.678) // #A4A6AD
    static let canvaTextTertiary   = Color(red: 0.435, green: 0.443, blue: 0.471) // #6F7178

    // MARK: - Brand
    static let canvaCyan        = Color(red: 0.000, green: 0.769, blue: 0.800) // #00C4CC
    static let canvaPurple      = Color(red: 0.490, green: 0.165, blue: 0.910) // #7D2AE8
    static let canvaBlue        = Color(red: 0.231, green: 0.361, blue: 1.000) // #3B5CFF
    static let canvaPurplePress = Color(red: 0.416, green: 0.122, blue: 0.816) // #6A1FD0

    // MARK: - Semantic
    static let canvaProGold = Color(red: 1.000, green: 0.761, blue: 0.294) // #FFC24B
    static let canvaSuccess = Color(red: 0.122, green: 0.780, blue: 0.486) // #1FC77C
    static let canvaError   = Color(red: 1.000, green: 0.318, blue: 0.388) // #FF5163
}

extension LinearGradient {
    /// The Canva brand gradient — logo, create FAB, primary CTA, hero rails.
    static let canvaBrand = LinearGradient(
        colors: [.canvaCyan, .canvaPurple],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
    static func tile(_ a: Color, _ b: Color) -> LinearGradient {
        LinearGradient(colors: [a, b], startPoint: .topLeading, endPoint: .bottomTrailing)
    }
    // Recurring design-type tile gradients
    static let tileInstagram   = tile(.canvaCyan, .canvaBlue)                                  // #00C4CC → #3B5CFF
    static let tilePresentation = tile(.canvaPurple, Color(red: 0.757, green: 0.227, blue: 0.878)) // #7D2AE8 → #C13AE0
    static let tileDoc         = tile(Color(red: 1, green: 0.478, blue: 0.349), .canvaProGold) // #FF7A59 → #FFC24B
    static let tileWhiteboard  = tile(.canvaSuccess, .canvaCyan)                               // #1FC77C → #00C4CC
}
```

## 2. Typography

Canva Sans stand-in: **Plus Jakarta Sans** (SIL OFL). Bundle the TTFs via `Info.plist` / `UIAppFonts`. App chrome always uses this family; the hundreds of in-editor fonts apply only to user content text.

```swift
extension Font {
    static func canva(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .regular:  return "PlusJakartaSans-Regular"
            case .medium:   return "PlusJakartaSans-Medium"
            case .semibold: return "PlusJakartaSans-SemiBold"
            case .bold:     return "PlusJakartaSans-Bold"
            case .heavy, .black: return "PlusJakartaSans-ExtraBold"
            default:        return "PlusJakartaSans-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let canvaDisplay   = Font.custom("PlusJakartaSans-ExtraBold", size: 32)
    static let canvaGreeting  = Font.custom("PlusJakartaSans-ExtraBold", size: 26)
    static let canvaSection   = Font.custom("PlusJakartaSans-Bold",      size: 22)
    static let canvaSubsection = Font.custom("PlusJakartaSans-Bold",     size: 18)
    static let canvaBody      = Font.custom("PlusJakartaSans-Regular",   size: 16)
    static let canvaLabel     = Font.custom("PlusJakartaSans-SemiBold",  size: 15)
    static let canvaButton    = Font.custom("PlusJakartaSans-Bold",      size: 16)
    static let canvaMeta      = Font.custom("PlusJakartaSans-Regular",   size: 14)
    static let canvaChip      = Font.custom("PlusJakartaSans-SemiBold",  size: 12)
    static let canvaTab       = Font.custom("PlusJakartaSans-SemiBold",  size: 10)
    static let canvaProBadge  = Font.custom("PlusJakartaSans-ExtraBold", size: 9)
    static let canvaTool      = Font.custom("PlusJakartaSans-SemiBold",  size: 11)
}
```

## 3. Signature Components

### Home Header (Greeting + Avatar)

```swift
struct HomeHeader: View {
    let name: String
    let initial: String

    var body: some View {
        HStack {
            Text("Hi, \(name) 👋")
                .font(.canvaGreeting)
                .foregroundStyle(Color.canvaTextPrimary)
            Spacer()
            Text(initial)
                .font(.canva(14, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 34, height: 34)
                .background(Circle().fill(LinearGradient.canvaBrand))
        }
        .padding(.horizontal, 18)
        .padding(.vertical, 4)
    }
}
```

### Search Field

```swift
struct CanvaSearchField: View {
    @State private var query = ""
    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "magnifyingglass")
                .font(.system(size: 17))
                .foregroundStyle(Color.canvaTextSecondary)
            TextField("Search templates, photos…", text: $query)
                .font(.canva(15))
                .foregroundStyle(Color.canvaTextPrimary)
        }
        .padding(.horizontal, 14)
        .frame(height: 46)
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.canvaSurface2))
        .padding(.horizontal, 18)
    }
}
```

### Design-Type Tile

```swift
struct DesignTypeTile: View {
    let icon: String
    let label: String
    let gradient: LinearGradient

    var body: some View {
        VStack(spacing: 7) {
            Image(systemName: icon)
                .font(.system(size: 24, weight: .semibold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(RoundedRectangle(cornerRadius: 16).fill(gradient))
            Text(label)
                .font(.canvaChip)
                .foregroundStyle(Color.canvaTextSecondary)
        }
        .frame(width: 76)
    }
}
```

### Template Thumbnail (with PRO badge)

```swift
struct TemplateThumb: View {
    let gradient: LinearGradient
    let isPro: Bool

    var body: some View {
        RoundedRectangle(cornerRadius: 12)
            .fill(gradient)
            .aspectRatio(3.0/4.0, contentMode: .fit)
            .overlay(alignment: .topTrailing) {
                if isPro {
                    Text("PRO")
                        .font(.canvaProBadge)
                        .foregroundStyle(Color(red: 0.102, green: 0.071, blue: 0.020))
                        .padding(.vertical, 2).padding(.horizontal, 6)
                        .background(RoundedRectangle(cornerRadius: 6).fill(Color.canvaProGold))
                        .padding(7)
                }
            }
            .shadow(color: Color.canvaTextPrimaryLt.opacity(0.06), radius: 8, y: 2)
    }
}
```

### Buttons

```swift
struct CanvaPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.canvaButton)
                .foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 28)
                .background(Capsule().fill(LinearGradient.canvaBrand))
        }
        .buttonStyle(.plain)
    }
}

struct CanvaSolidButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.canva(15, weight: .bold))
                .foregroundStyle(.white)
                .padding(.vertical, 12).padding(.horizontal, 22)
                .background(Capsule().fill(Color.canvaPurple))
        }
        .buttonStyle(.plain)
    }
}

struct CanvaProButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: "crown.fill").font(.system(size: 12))
                Text(title).font(.canva(14, weight: .heavy))
            }
            .foregroundStyle(Color(red: 0.102, green: 0.071, blue: 0.020))
            .padding(.vertical, 10).padding(.horizontal, 18)
            .background(Capsule().fill(Color.canvaProGold))
        }
        .buttonStyle(.plain)
    }
}
```

### Create FAB (center tab)

```swift
struct CreateFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 46, height: 46)
                .background(RoundedRectangle(cornerRadius: 16).fill(LinearGradient.canvaBrand))
                .shadow(color: Color.canvaPurple.opacity(0.45), radius: 16, y: 6)
        }
        .buttonStyle(.plain)
        .offset(y: -4)
    }
}
```

### Create-Design Bottom Sheet

```swift
struct CreateDesignSheet: View {
    let types: [(icon: String, label: String, grad: LinearGradient)]

    var body: some View {
        VStack(alignment: .leading, spacing: 18) {
            Capsule().fill(Color.canvaDivider).frame(width: 40, height: 5)
                .frame(maxWidth: .infinity)
            Text("Create a design")
                .font(.canvaSubsection)
                .foregroundStyle(Color.canvaTextPrimary)
            LazyVGrid(columns: Array(repeating: GridItem(.flexible()), count: 3), spacing: 18) {
                ForEach(types.indices, id: \.self) { i in
                    DesignTypeTile(icon: types[i].icon, label: types[i].label, gradient: types[i].grad)
                }
            }
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 24)
                .fill(Color.canvaSurface1)
                .shadow(color: .black.opacity(0.12), radius: 28, y: -6)
        )
    }
}
```

## 4. Bottom Tab Bar

The center slot is the gradient create FAB — lifted and shadowed (the one elevated element). Other tabs have no tint pill; active is the primary text color.

```swift
struct CanvaTabBar: View {
    @Binding var selection: Int
    let onCreate: () -> Void

    var body: some View {
        HStack {
            tabItem(0, "house.fill", "Home")
            tabItem(1, "square.grid.2x2.fill", "Templates")
            CreateFAB(action: onCreate)
            tabItem(3, "list.bullet.rectangle", "Projects")
            tabItem(4, "person.crop.circle", "You")
        }
        .padding(.horizontal, 4)
        .padding(.top, 8)
        .padding(.bottom, 16)
        .background(.ultraThinMaterial)
        .overlay(Rectangle().fill(Color.canvaDivider).frame(height: 0.5), alignment: .top)
    }

    @ViewBuilder private func tabItem(_ i: Int, _ icon: String, _ label: String) -> some View {
        let active = selection == i
        VStack(spacing: 4) {
            Image(systemName: icon).font(.system(size: 22))
            Text(label).font(.canvaTab)
        }
        .foregroundStyle(active ? Color.canvaTextPrimary : Color.canvaTextSecondary)
        .frame(maxWidth: .infinity)
        .onTapGesture { selection = i }
    }
}
```

## 5. Motion

```swift
// Create FAB tap → "Create a design" sheet
withAnimation(.easeOut(duration: 0.32)) { showCreateSheet = true }
// FAB press: .scaleEffect(pressed ? 0.92 : 1)

// Design-type tile tap
.scaleEffect(pressed ? 0.96 : 1)
.animation(.easeOut(duration: 0.12), value: pressed)

// Bottom sheet — drag-to-dismiss
.presentationDetents([.medium, .large])
.presentationDragIndicator(.visible)
// spring release damping ≈ 0.85

// Editor element select — contextual toolbar
.transition(.move(edge: .bottom).combined(with: .opacity)) // 0.22s ease-out

// Tab active pop
.scaleEffect(active ? 1.05 : 1).animation(.easeOut(duration: 0.12), value: active)

// Gradient button press
.brightness(pressed ? -0.07 : 0).scaleEffect(pressed ? 0.98 : 1)

// Skeleton shimmer (template grid)
// LinearGradient sweep, .repeatForever(autoreverses: false) over 1.2s

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: fabTapped)
.sensoryFeedback(.impact(weight: .light), trigger: tileTapped)
.sensoryFeedback(.success, trigger: exportDone)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Templates (tab) | `square.grid.2x2` / `.fill` | 22pt |
| Create FAB (tab center) | `plus` | 24pt |
| Projects (tab) | `list.bullet.rectangle` | 22pt |
| You (tab) | `person.crop.circle` | 22pt |
| Search | `magnifyingglass` | 17pt |
| Pro / premium | `crown.fill` | 12–14pt |
| Instagram type | `camera.fill` / `photo` | 24pt |
| Presentation type | `play.rectangle` | 24pt |
| Doc type | `doc.text` | 24pt |
| Whiteboard type | `square.dashed` | 24pt |
| Video type | `play.circle.fill` | 24pt |
| Editor — back | `chevron.left` | 22pt |
| Editor — undo / redo | `arrow.uturn.backward` / `.forward` | 20pt |
| Editor — share | `square.and.arrow.up` | 22pt |
| Tool — Elements | `square.on.circle` | 24pt |
| Tool — Text | `textformat` | 24pt |
| Tool — Uploads | `arrow.up.circle` | 24pt |
| Tool — Photos | `photo.on.rectangle` | 24pt |
| Tool — Draw | `pencil.tip` | 24pt |
| Element — color | `paintpalette.fill` | 22pt |
| Element — animate | `sparkles` | 22pt |
| Element — delete | `trash` | 22pt |

## 7. Dark Mode

```swift
struct CanvaTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.canvaCanvasDark : Color.canvaWorkspace)
            .foregroundStyle(scheme == .dark ? Color.canvaTextPrimary : Color.canvaTextPrimaryLt)
            .tint(Color.canvaCyan)
    }
}

extension View {
    func canvaTheme() -> some View { modifier(CanvaTheme()) }
}
```

Canva's dark canvas is warm-neutral `#18191B`, never pure black; cards lift to `#1F2023`. The cyan→purple brand gradient and Pro gold (`#FFC24B`) are identical across themes. Template thumbnails and user designs stay full-color — only the chrome dims.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Plus Jakarta Sans TTFs via `Info.plist` `UIAppFonts` — SIL OFL, free to distribute. App chrome always uses this family
- Dynamic Type: scale greeting, section titles, body, meta; keep tab labels (10pt), chip labels (12pt), the Pro badge (9pt), tool labels (11pt) at FIXED sizes (layout-sensitive)
- VoiceOver: label the create FAB "Create a design"; label design-type tiles "{Label} template, button"; label template thumbnails "{Template name}, {Pro if applicable}, button"; the Pro badge needs an accessibility label "Pro"
- Color contrast: `#0D0E10` on `#FFFFFF` and `#ECECEE` on `#18191B` pass WCAG AA; verify white text over the lighter end of tile gradients (cyan `#00C4CC`) — add a subtle dark scrim behind label text if a thumbnail is light
- The brand gradient must keep ≥4.5:1 against white text — verify at the cyan end; the FAB `+` is large/bold so AA-large applies
- Reduce Motion: disable the FAB/tile scale pops and the skeleton shimmer; keep sheet slide as a simple fade
- Dark mode: warm-neutral `#18191B` — NOT pure black; do not desaturate content thumbnails, only chrome dims; sheets add a faint 1pt `#303135` top border since shadows nearly vanish
- Touch targets: create FAB 46pt, design-type tile 76pt tap, top-bar icons 44pt hit, tool-rail items 56pt hit
