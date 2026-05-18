# Plenty of Fish (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Plenty of Fish's visual language into paste-ready SwiftUI code: `Color` / `LinearGradient` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let pofWhite        = Color.white                                  // #FFFFFF
    static let pofBackground   = Color(red: 0.957, green: 0.961, blue: 0.969) // #F4F5F7
    static let pofPressedLight = Color(red: 0.914, green: 0.922, blue: 0.937) // #E9EBEF
    static let pofDividerLight = Color(red: 0.894, green: 0.902, blue: 0.918) // #E4E6EA

    // MARK: - Canvas & Surfaces (Dark)
    static let pofCanvasDark = Color(red: 0.071, green: 0.078, blue: 0.090) // #121417
    static let pofSurface1   = Color(red: 0.102, green: 0.114, blue: 0.129) // #1A1D21
    static let pofSurface2   = Color(red: 0.137, green: 0.153, blue: 0.173) // #23272C
    static let pofDivider    = Color(red: 0.173, green: 0.188, blue: 0.212) // #2C3036

    // MARK: - Text
    static let pofTextPrimary    = Color(red: 0.929, green: 0.937, blue: 0.949) // #EDEFF2 (dark)
    static let pofTextPrimaryLt  = Color(red: 0.086, green: 0.094, blue: 0.110) // #16181C (light)
    static let pofTextSecondary  = Color(red: 0.608, green: 0.631, blue: 0.671) // #9BA1AB
    static let pofTextTertiary   = Color(red: 0.420, green: 0.439, blue: 0.475) // #6B7079

    // MARK: - Brand
    static let pofBlue        = Color(red: 0.000, green: 0.596, blue: 0.859) // #0098DB
    static let pofBlueLight   = Color(red: 0.000, green: 0.651, blue: 0.886) // #00A6E2
    static let pofBluePressed = Color(red: 0.000, green: 0.475, blue: 0.690) // #0079B0

    // MARK: - Accent / Status
    static let pofTeal  = Color(red: 0.000, green: 0.788, blue: 0.718) // #00C9B7 — online
    static let pofPink  = Color(red: 1.000, green: 0.310, blue: 0.545) // #FF4F8B — unread
    static let pofGold  = Color(red: 1.000, green: 0.698, blue: 0.243) // #FFB23E — upgrade

    // MARK: - Semantic
    static let pofSuccess = Color(red: 0.125, green: 0.788, blue: 0.592) // #20C997
    static let pofError   = Color(red: 1.000, green: 0.322, blue: 0.404) // #FF5267
}

extension LinearGradient {
    /// The POF brand gradient — logo, primary CTA, the Meet Me "yes" button.
    static let pofBrand = LinearGradient(
        colors: [.pofBlue, .pofBlueLight],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
    /// Bottom-of-card scrim for white name/meta over photos.
    static let pofScrim = LinearGradient(
        colors: [.clear, .black.opacity(0.78)],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

POF's UI sans stand-in: **Nunito Sans** (SIL OFL). Bundle the TTFs via `Info.plist` / `UIAppFonts`. Heavy rounded weights (800/900) for names and titles.

```swift
extension Font {
    static func pof(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .regular:  return "NunitoSans-Regular"
            case .medium:   return "NunitoSans-SemiBold"   // 600 ≈ medium emphasis
            case .semibold: return "NunitoSans-SemiBold"
            case .bold:     return "NunitoSans-Bold"
            case .heavy:    return "NunitoSans-ExtraBold"
            case .black:    return "NunitoSans-Black"
            default:        return "NunitoSans-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let pofDisplay   = Font.custom("NunitoSans-Black",     size: 32)
    static let pofTitle     = Font.custom("NunitoSans-ExtraBold", size: 26)
    static let pofSection   = Font.custom("NunitoSans-ExtraBold", size: 22)
    static let pofProfile   = Font.custom("NunitoSans-ExtraBold", size: 18)
    static let pofBody      = Font.custom("NunitoSans-Regular",   size: 16)
    static let pofCardName  = Font.custom("NunitoSans-Bold",      size: 15)
    static let pofButton    = Font.custom("NunitoSans-ExtraBold", size: 16)
    static let pofMeta      = Font.custom("NunitoSans-Regular",   size: 14)
    static let pofCardMeta  = Font.custom("NunitoSans-SemiBold",  size: 12)
    static let pofChip      = Font.custom("NunitoSans-Bold",      size: 12)
    static let pofTab       = Font.custom("NunitoSans-ExtraBold", size: 10)
    static let pofBadge     = Font.custom("NunitoSans-ExtraBold", size: 9)
    static let pofBubble    = Font.custom("NunitoSans-SemiBold",  size: 15)
}
```

## 3. Signature Components

### Profile Grid Card

```swift
struct ProfileGridCard: View {
    let photo: LinearGradient   // swap for AsyncImage in production
    let name: String
    let age: Int
    let meta: String
    let isOnline: Bool
    let onLike: () -> Void

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            photo.aspectRatio(3.0/4.0, contentMode: .fill)

            if isOnline {
                HStack(spacing: 5) {
                    Circle().fill(Color.pofTeal).frame(width: 7, height: 7)
                    Text("Online").font(.pofChip).foregroundStyle(.white)
                }
                .padding(.vertical, 4).padding(.horizontal, 9)
                .background(Capsule().fill(Color.black.opacity(0.42)))
                .padding(10)
                .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
            }

            LinearGradient.pofScrim.frame(height: 90)
                .frame(maxHeight: .infinity, alignment: .bottom)

            VStack(alignment: .leading, spacing: 1) {
                Text("\(name), \(age)").font(.pofCardName).foregroundStyle(.white)
                Text(meta).font(.pofCardMeta).foregroundStyle(.white.opacity(0.85))
            }
            .padding(12)

            Button(action: onLike) {
                Image(systemName: "heart.fill")
                    .font(.system(size: 16))
                    .foregroundStyle(.white)
                    .frame(width: 36, height: 36)
                    .background(Circle().fill(Color.pofBlue))
                    .shadow(color: Color.pofBlue.opacity(0.5), radius: 12, y: 4)
            }
            .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .bottomTrailing)
            .padding(12)
        }
        .clipShape(RoundedRectangle(cornerRadius: 16))
    }
}
```

### Match Grid

```swift
struct MatchGrid: View {
    let cards: [ProfileVM]
    private let cols = [GridItem(.flexible(), spacing: 10), GridItem(.flexible(), spacing: 10)]

    var body: some View {
        ScrollView {
            LazyVGrid(columns: cols, spacing: 10) {
                ForEach(cards) { vm in
                    ProfileGridCard(photo: vm.photo, name: vm.name, age: vm.age,
                                    meta: vm.meta, isOnline: vm.isOnline, onLike: vm.like)
                }
            }
            .padding(14)
        }
        .background(Color.pofCanvasDark)
    }
}
```

### Meet Me Card

```swift
struct MeetMeCard: View {
    let photo: LinearGradient
    let name: String
    let age: Int
    let onNo: () -> Void
    let onMaybe: () -> Void
    let onYes: () -> Void

    var body: some View {
        VStack(spacing: 14) {
            photo
                .aspectRatio(1, contentMode: .fill)
                .clipShape(RoundedRectangle(cornerRadius: 16))
            Text("\(name), \(age)").font(.pofProfile).foregroundStyle(Color.pofTextPrimary)
            Text("Would you like to meet \(name)?").font(.pof(13, weight: .bold)).foregroundStyle(Color.pofTextSecondary)

            HStack(spacing: 14) {
                roundButton("xmark", border: Color.pofDivider, tint: Color.pofTextSecondary, action: onNo)
                roundButton("questionmark", border: Color.pofGold, tint: Color.pofGold, action: onMaybe)
                Button(action: onYes) {
                    Image(systemName: "heart.fill")
                        .font(.system(size: 24, weight: .bold))
                        .foregroundStyle(.white)
                        .frame(width: 56, height: 56)
                        .background(Circle().fill(Color.pofBlue))
                        .shadow(color: Color.pofBlue.opacity(0.5), radius: 16, y: 6)
                }
                .buttonStyle(.plain)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 20).fill(Color.pofSurface1))
    }

    @ViewBuilder private func roundButton(_ sym: String, border: Color, tint: Color, action: @escaping () -> Void) -> some View {
        Button(action: action) {
            Image(systemName: sym)
                .font(.system(size: 22, weight: .bold))
                .foregroundStyle(tint)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.pofSurface1))
                .overlay(Circle().strokeBorder(border, lineWidth: 1.5))
        }
        .buttonStyle(.plain)
    }
}
```

### Buttons

```swift
struct POFPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.pofButton)
                .foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 28)
                .background(Capsule().fill(LinearGradient.pofBrand))
        }
        .buttonStyle(.plain)
    }
}

struct POFOutlineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.pof(15, weight: .bold))
                .foregroundStyle(Color.pofBlue)
                .padding(.vertical, 11).padding(.horizontal, 20)
                .overlay(Capsule().strokeBorder(Color.pofBlue, lineWidth: 1.5))
        }
        .buttonStyle(.plain)
    }
}

struct POFUpgradeButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: "crown.fill").font(.system(size: 12))
                Text(title).font(.pof(14, weight: .heavy))
            }
            .foregroundStyle(Color(red: 0.165, green: 0.106, blue: 0.020))
            .padding(.vertical, 10).padding(.horizontal, 18)
            .background(Capsule().fill(Color.pofGold))
        }
        .buttonStyle(.plain)
    }
}
```

### "Online" Presence Pill

```swift
struct OnlinePill: View {
    @State private var pulse = false
    var body: some View {
        HStack(spacing: 5) {
            Circle()
                .fill(Color.pofTeal)
                .frame(width: 7, height: 7)
                .opacity(pulse ? 0.5 : 1)
                .animation(.easeInOut(duration: 2).repeatForever(autoreverses: true), value: pulse)
            Text("Online").font(.pofChip).foregroundStyle(.white)
        }
        .padding(.vertical, 4).padding(.horizontal, 9)
        .background(Capsule().fill(Color.black.opacity(0.42)))
        .onAppear { pulse = true }
    }
}
```

## 4. Bottom Tab Bar

5 slots; active is POF Blue (no Material pill — just tint + filled icon). The Messages tab carries a pink unread badge.

```swift
struct POFTabBar: View {
    @Binding var selection: Int
    let unread: Int

    var body: some View {
        HStack {
            tab(0, "square.grid.2x2", "Matches")
            tab(1, "heart", "Meet Me")
            tab(2, "magnifyingglass", "Search")
            tab(3, "bubble.left.and.bubble.right", "Messages", badge: unread)
            tab(4, "person.crop.circle", "Me")
        }
        .padding(.horizontal, 4)
        .padding(.top, 8)
        .padding(.bottom, 16)
        .background(.ultraThinMaterial)
        .overlay(Rectangle().fill(Color.pofDivider).frame(height: 0.5), alignment: .top)
    }

    @ViewBuilder private func tab(_ i: Int, _ icon: String, _ label: String, badge: Int = 0) -> some View {
        let active = selection == i
        VStack(spacing: 4) {
            ZStack(alignment: .topTrailing) {
                Image(systemName: active ? "\(icon).fill" : icon).font(.system(size: 23))
                if badge > 0 {
                    Text("\(badge)").font(.pofBadge).foregroundStyle(.white)
                        .padding(.horizontal, 4).frame(minWidth: 16, minHeight: 16)
                        .background(Capsule().fill(Color.pofPink))
                        .offset(x: 10, y: -4)
                }
            }
            Text(label).font(.pofTab)
        }
        .foregroundStyle(active ? Color.pofBlue : Color.pofTextSecondary)
        .frame(maxWidth: .infinity)
        .onTapGesture { selection = i }
    }
}
```

## 5. Motion

```swift
// Like (grid card) — heart pop
withAnimation(.spring(response: 0.22, dampingFraction: 0.5)) { liked = true }
// scaleEffect 1 → 1.25 → 1; light haptic

// Meet Me decision — card off-screen
withAnimation(.easeIn(duration: 0.26)) { offset = decision == .yes ? 600 : -600 }
// "maybe": .offset(y: -600) over 0.24s

// Meet Me drag
DragGesture()
    .onChanged { value in dragX = value.translation.width; rotation = Double(dragX / 18) }
    .onEnded { _ in /* commit > 35% width else spring back, damping 0.8 */ }

// Tab active pop
.scaleEffect(active ? 1.05 : 1).animation(.easeOut(duration: 0.12), value: active)

// Online dot pulse
.opacity(pulse ? 0.5 : 1)
.animation(.easeInOut(duration: 2).repeatForever(autoreverses: true), value: pulse)

// Profile open — shared element zoom (matchedGeometryEffect), 0.32s

// Message send — bubble scale-in
.transition(.scale(scale: 0.6).combined(with: .opacity)) // 0.18s ease-out

// Skeleton shimmer — 1.2s repeating sweep over rounded placeholders

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: liked)
.sensoryFeedback(.impact(weight: .light), trigger: meetMeDecision)
.sensoryFeedback(.success, trigger: matchMade)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Matches (tab) | `square.grid.2x2` / `.fill` | 23pt |
| Meet Me (tab) | `heart` / `heart.fill` | 23pt |
| Search (tab) | `magnifyingglass` | 23pt |
| Messages (tab) | `bubble.left.and.bubble.right` / `.fill` | 23pt |
| Me (tab) | `person.crop.circle` / `.fill` | 23pt |
| Like (card / yes) | `heart.fill` | 16–24pt |
| Meet Me — no | `xmark` | 22pt |
| Meet Me — maybe | `questionmark` | 22pt |
| Filter | `slider.horizontal.3` | 22pt |
| Upgrade / premium | `crown.fill` | 12–14pt |
| Online dot | (filled `Circle`, teal) | 7pt |
| Send message | `arrow.up.circle.fill` | 28pt |
| Photo (composer) | `photo` | 22pt |
| Block / report | `exclamationmark.shield` | 20pt |
| Back | `chevron.left` | 22pt |
| More | `ellipsis` | 22pt |
| Distance | `location.fill` | 12pt |
| Carousel dots | `circle.fill` | 6pt |
| Verified | `checkmark.seal.fill` | 16pt |

## 7. Dark Mode

```swift
struct POFTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.pofCanvasDark : Color.pofBackground)
            .foregroundStyle(scheme == .dark ? Color.pofTextPrimary : Color.pofTextPrimaryLt)
            .tint(Color.pofBlue)
    }
}

extension View {
    func pofTheme() -> some View { modifier(POFTheme()) }
}
```

POF's dark canvas is cool near-black `#121417`, never pure black; cards lift to `#1A1D21`. POF Blue, online teal (`#00C9B7`), unread pink (`#FF4F8B`) and upgrade gold (`#FFB23E`) are identical across themes. Profile photos stay full-color over the dark grid — only the chrome dims.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Nunito Sans TTFs via `Info.plist` `UIAppFonts` — SIL OFL, free to distribute
- Dynamic Type: scale display, titles, body, meta; keep card-scrim name/meta, tab labels (10pt), unread badge (9pt), status chips (12pt) at FIXED sizes — they sit over imagery and must not break the photo composition
- VoiceOver: each grid card is one element — "Profile, {name}, {age}, {meta}, {Online if present}"; expose the like button as a separate action "Like {name}"; Meet Me buttons labeled "No", "Maybe", "Yes, like {name}"
- The like/yes heart needs a state change announced ("Liked"); the unread badge needs an accessibility value ("{n} unread messages")
- Color contrast: white name/meta on the `rgba(0,0,0,0.78)` scrim passes WCAG AA; `#16181C` on `#FFFFFF` and `#EDEFF2` on `#121417` pass AA; verify POF Blue keeps ≥4.5:1 behind white button text (it does at 16pt bold — AA-large)
- Reduce Motion: disable the heart pop, the online-dot pulse, and Meet Me card-fly; substitute a crossfade; keep selection state changes
- Dark mode: cool near-black `#121417` — NOT pure black; do not desaturate profile photos, only chrome dims; sheets add a faint 1pt `#2C3036` top border since shadows nearly vanish
- Touch targets: tab icons 44pt hit, grid-card like 44pt hit (36pt visual), Meet Me buttons 56pt (52pt SE), full card tappable to open profile
