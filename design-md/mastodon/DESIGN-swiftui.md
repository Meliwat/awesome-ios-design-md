# Mastodon (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Mastodon's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Light
    static let mastoCanvas    = Color.white                                  // #FFFFFF
    static let mastoSurface2  = Color(red: 0.949, green: 0.953, blue: 0.973) // #F2F3F8
    static let mastoDivider   = Color(red: 0.753, green: 0.804, blue: 0.851) // #C0CDD9
    static let mastoText      = Color.black                                  // #000000
    static let mastoTextSec   = Color(red: 0.376, green: 0.412, blue: 0.518) // #606984

    // MARK: - Dark (blue-gray, NOT true black)
    static let mastoDarkCanvas   = Color(red: 0.098, green: 0.106, blue: 0.133) // #191B22
    static let mastoDarkSurface1 = Color(red: 0.157, green: 0.173, blue: 0.216) // #282C37
    static let mastoDarkSurface2 = Color(red: 0.192, green: 0.208, blue: 0.263) // #313543
    static let mastoDarkDivider  = Color(red: 0.224, green: 0.247, blue: 0.310) // #393F4F
    static let mastoDarkTextSec  = Color(red: 0.608, green: 0.682, blue: 0.784) // #9BAEC8

    // MARK: - Brand
    static let mastoPurple        = Color(red: 0.388, green: 0.392, blue: 1.0)   // #6364FF
    static let mastoPurplePressed = Color(red: 0.337, green: 0.227, blue: 0.800) // #563ACC
    static let mastoBoostGreen    = Color(red: 0.176, green: 0.808, blue: 0.537) // #2DCE89

    // MARK: - Semantic
    static let mastoFavGold = Color(red: 0.792, green: 0.561, blue: 0.016) // #CA8F04
    static let mastoError   = Color(red: 0.875, green: 0.251, blue: 0.353) // #DF405A
}
```

## 2. Typography

Mastodon uses the system font (SF Pro) at reading sizes; the web client renders Inter. Use `.system` directly — bundle Inter via `UIAppFonts` only if you need pixel parity with the web app.

```swift
extension Font {
    static let mastoTitleLarge   = Font.system(size: 28, weight: .bold)
    static let mastoSection      = Font.system(size: 22, weight: .bold)
    static let mastoDisplayName  = Font.system(size: 16, weight: .semibold)
    static let mastoBody         = Font.system(size: 16, weight: .regular)   // line height 1.45 — see lineSpacing
    static let mastoBodySettings = Font.system(size: 15, weight: .regular)
    static let mastoHandle       = Font.system(size: 14, weight: .regular)
    static let mastoCW           = Font.system(size: 15, weight: .semibold)
    static let mastoCount        = Font.system(size: 13, weight: .medium)
    static let mastoButton       = Font.system(size: 16, weight: .semibold)
    static let mastoShowMore     = Font.system(size: 14, weight: .bold)
    static let mastoTab          = Font.system(size: 10, weight: .semibold)
}

// 16pt body at 1.45 line height → lineSpacing ≈ 16 * 0.45 ≈ 7.2pt
extension View {
    func mastoBodyText() -> some View { self.font(.mastoBody).lineSpacing(7.2) }
}
```

## 3. Signature Components

### Toot Card (the core unit)

```swift
struct TootCard: View {
    let displayName: String
    let handle: String          // "@Gargron@mastodon.social"
    let timestamp: String
    let body: String
    let avatar: Image
    @State private var boosted = false
    @State private var favourited = false

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack(alignment: .top, spacing: 12) {
                avatar.resizable().frame(width: 46, height: 46)
                    .clipShape(RoundedRectangle(cornerRadius: 8))      // rounded-square, NOT circle
                VStack(alignment: .leading, spacing: 2) {
                    Text(displayName).font(.mastoDisplayName).foregroundStyle(Color.mastoText)
                    Text(handle).font(.mastoHandle).foregroundStyle(Color.mastoTextSec)
                }
                Spacer()
                Text(timestamp).font(.mastoCount).foregroundStyle(Color.mastoTextSec)
                Image(systemName: "ellipsis").foregroundStyle(Color.mastoTextSec)
            }
            Text(body).mastoBodyText().foregroundStyle(Color.mastoText)

            HStack {
                actionButton("arrowshape.turn.up.left", count: 12, active: false, tint: .mastoTextSec)
                Spacer()
                BoostButton(boosted: $boosted, count: 34)
                Spacer()
                FavouriteButton(favourited: $favourited, count: 211)
                Spacer()
                actionButton("square.and.arrow.up", count: 0, active: false, tint: .mastoTextSec)
            }
            .padding(.top, 2)
        }
        .padding(16)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.mastoDivider).frame(height: 1)
        }
    }

    func actionButton(_ symbol: String, count: Int, active: Bool, tint: Color) -> some View {
        HStack(spacing: 6) {
            Image(systemName: symbol).font(.system(size: 18))
            if count > 0 { Text("\(count)").font(.mastoCount) }
        }
        .foregroundStyle(tint)
        .frame(minWidth: 44, minHeight: 44, alignment: .leading)
    }
}
```

### Boost Button (the 360° spin)

```swift
struct BoostButton: View {
    @Binding var boosted: Bool
    let count: Int
    @State private var spin: Double = 0

    var body: some View {
        Button {
            boosted.toggle()
            withAnimation(.easeOut(duration: 0.4)) { spin += boosted ? 360 : -360 }
        } label: {
            HStack(spacing: 6) {
                Image(systemName: "arrow.2.squarepath")
                    .font(.system(size: 18))
                    .rotationEffect(.degrees(spin))
                Text("\(boosted ? count + 1 : count)").font(.mastoCount)
            }
            .foregroundStyle(boosted ? Color.mastoBoostGreen : Color.mastoTextSec)
        }
        .frame(minWidth: 44, minHeight: 44)
        .sensoryFeedback(.success, trigger: boosted)
    }
}
```

### Favourite Button (gold bounce)

```swift
struct FavouriteButton: View {
    @Binding var favourited: Bool
    let count: Int
    @State private var scale: CGFloat = 1

    var body: some View {
        Button {
            favourited.toggle()
            withAnimation(.spring(response: 0.3, dampingFraction: 0.5)) { scale = 1.2 }
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.15) {
                withAnimation(.spring()) { scale = 1 }
            }
        } label: {
            HStack(spacing: 6) {
                Image(systemName: favourited ? "star.fill" : "star")
                    .font(.system(size: 18)).scaleEffect(scale)
                Text("\(favourited ? count + 1 : count)").font(.mastoCount)
            }
            .foregroundStyle(favourited ? Color.mastoFavGold : Color.mastoTextSec)
        }
        .frame(minWidth: 44, minHeight: 44)
        .sensoryFeedback(.impact(weight: .light), trigger: favourited)
    }
}
```

### Content-Warning Spoiler Card (the signature)

```swift
struct ContentWarningCard: View {
    let warning: String
    let body: String
    @State private var expanded = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Label(warning, systemImage: "eye.slash")
                    .font(.mastoCW).foregroundStyle(Color.mastoText)
                Spacer()
                Button(expanded ? "SHOW LESS" : "SHOW MORE") {
                    withAnimation(.easeOut(duration: 0.25)) { expanded.toggle() }
                }
                .font(.mastoShowMore).foregroundStyle(Color.mastoPurple)
            }
            .padding(12)
            .background(Color.mastoSurface2, in: RoundedRectangle(cornerRadius: 8))

            if expanded {
                Text(body).mastoBodyText()
                    .foregroundStyle(Color.mastoText)
                    .padding(.top, 10)
                    .transition(.opacity.combined(with: .move(edge: .top)))
            }
        }
    }
}
```

### Primary Button & Compose FAB

```swift
struct MastoPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.mastoButton).foregroundStyle(.white)
                .padding(.vertical, 12).padding(.horizontal, 24)
                .background(Color.mastoPurple, in: RoundedRectangle(cornerRadius: 8))
        }
        .buttonStyle(MastoPressable())
    }
}

struct ComposeFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "square.and.pencil")
                .font(.system(size: 24, weight: .medium))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.mastoPurple))
                .shadow(color: Color.mastoPurple.opacity(0.35), radius: 20, y: 6)
        }
        .buttonStyle(MastoPressable(pressedScale: 0.94))
        .sensoryFeedback(.impact(weight: .soft), trigger: UUID())
    }
}

struct MastoPressable: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

## 4. Distinctive System — Federated Timeline Switcher

```swift
struct TimelineSwitcher: View {
    enum Scope: String, CaseIterable { case home = "Home", local = "Local", federated = "Federated" }
    @Binding var scope: Scope
    @Namespace private var ns

    var body: some View {
        HStack(spacing: 0) {
            ForEach(Scope.allCases, id: \.self) { s in
                VStack(spacing: 8) {
                    Text(s.rawValue)
                        .font(.system(size: 15, weight: scope == s ? .semibold : .regular))
                        .foregroundStyle(scope == s ? Color.mastoPurple : Color.mastoTextSec)
                    ZStack {
                        if scope == s {
                            Capsule().fill(Color.mastoPurple).frame(height: 2)
                                .matchedGeometryEffect(id: "underline", in: ns)
                        } else {
                            Color.clear.frame(height: 2)
                        }
                    }
                }
                .frame(maxWidth: .infinity)
                .contentShape(Rectangle())
                .onTapGesture { withAnimation(.easeOut(duration: 0.25)) { scope = s } }
            }
        }
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        a.backgroundColor = UIColor(Color.mastoCanvas).withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem        { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem      { Label("Search", systemImage: "magnifyingglass") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }
            ProfileView().tabItem     { Label("Profile", systemImage: "person.crop.square") }
        }
        .tint(.mastoPurple)
    }
}
```

## 6. Motion

```swift
// Boost: 360° spin + color → green + success haptic (see BoostButton)
withAnimation(.easeOut(duration: 0.4)) { spin += 360 }
.sensoryFeedback(.success, trigger: boosted)

// Favourite: gold star scale 1.0 → 1.2 → 1.0 over 300ms (see FavouriteButton)

// CW expand/collapse: height + opacity over 0.25s ease-out
withAnimation(.easeOut(duration: 0.25)) { expanded.toggle() }

// Timeline switch: matchedGeometryEffect underline + 0.25s ease-out

// Compose FAB: scale 0.94 on press, soft haptic; sheet via .sheet with spring presentation
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Reply | `arrowshape.turn.up.left` | 18pt |
| Boost | `arrow.2.squarepath` | 18pt |
| Favourite | `star` / `star.fill` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| More | `ellipsis` | 18pt |
| Content warning | `eye.slash` | 15pt |
| Compose (FAB) | `square.and.pencil` | 24pt |
| Home (tab) | `house.fill` | 26pt |
| Search (tab) | `magnifyingglass` | 26pt |
| Notifications (tab) | `bell.fill` | 26pt |
| Profile (tab) | `person.crop.square` | 26pt |
| Visibility public | `globe` | 16pt |
| Visibility followers | `lock.fill` | 16pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` and `matchedGeometryEffect` polish you want iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type generously on toot body, display names, and bios — this is a reading app; only tab labels and "SHOW MORE" stay fixed
- VoiceOver: announce a toot as "<name>, <handle>, <relative time>, <body>"; expose boost/favourite state ("Boosted" / "Favourited") and the CW card as a disclosure ("Content warning, <text>, double-tap to show")
- The CW reveal must be operable by VoiceOver — the "SHOW MORE" button needs a clear label and the expanded body should be announced
- Contrast: `#606984` secondary on `#FFFFFF` passes WCAG AA; in dark mode `#9BAEC8` on `#191B22` passes AA at 14pt+
- Respect Reduce Motion: replace the boost 360° spin with a simple color cross-fade and the CW expansion with an instant reveal
- Honor `prefersCrossFadeTransitions` for the timeline switcher
