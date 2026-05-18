# The League (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates The League's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (the only theme — dark-native)
    static let leagueCanvas   = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let leagueSurface1 = Color(red: 0.078, green: 0.078, blue: 0.078) // #141414
    static let leagueSurface2 = Color(red: 0.110, green: 0.102, blue: 0.090) // #1C1A17
    static let leagueCardBase = Color(red: 0.063, green: 0.059, blue: 0.047) // #100F0C
    static let leagueHairline = Color(red: 0.165, green: 0.149, blue: 0.125) // #2A2620

    // MARK: - Text
    static let leagueTextPrimary   = Color(red: 0.929, green: 0.914, blue: 0.886) // #EDE9E2
    static let leagueTextSecondary = Color(red: 0.620, green: 0.596, blue: 0.549) // #9E988C
    static let leagueTextTertiary  = Color(red: 0.420, green: 0.400, blue: 0.361) // #6B665C
    static let leagueOnGold        = Color(red: 0.102, green: 0.078, blue: 0.031) // #1A1408

    // MARK: - Accent (the single accent — gold)
    static let leagueGold       = Color(red: 0.784, green: 0.639, blue: 0.353) // #C8A35A
    static let leagueGoldBright = Color(red: 0.859, green: 0.729, blue: 0.443) // #DBBA71
    static let leagueGoldDeep   = Color(red: 0.659, green: 0.525, blue: 0.247) // #A8863F
    static let leagueChampagne  = Color(red: 0.910, green: 0.863, blue: 0.753) // #E8DCC0

    // MARK: - Semantic
    static let leagueSuccess = Color(red: 0.435, green: 0.682, blue: 0.541) // #6FAE8A
    static let leagueError   = Color(red: 0.780, green: 0.478, blue: 0.478) // #C77A7A
}
```

## 2. Typography

The League pairs a high-contrast display serif (Cormorant Garamond) with a clean geometric sans (Jost). Bundle both (SIL OFL) via `Info.plist` / `UIAppFonts`. Serif = the person & institution; sans = the machine & metadata.

```swift
extension Font {
    // Display serif — names, wordmark, titles, concierge
    static func leagueSerif(_ size: CGFloat, weight: Font.Weight = .bold, italic: Bool = false) -> Font {
        let face: String = italic ? "CormorantGaramond-MediumItalic" : {
            switch weight {
            case .semibold: return "CormorantGaramond-SemiBold"
            case .medium:   return "CormorantGaramond-Medium"
            case .regular:  return "CormorantGaramond-Regular"
            default:        return "CormorantGaramond-Bold"
            }
        }()
        return Font.custom(face, size: size)
    }
    // UI sans — credentials, body, eyebrows, buttons, tabs
    static func leagueSans(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .semibold, .bold: return "Jost-SemiBold"
            case .medium:          return "Jost-Medium"
            case .light:           return "Jost-Light"
            default:               return "Jost-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let leagueWordmark   = Font.custom("CormorantGaramond-Bold",   size: 34)
    static let leagueName       = Font.custom("CormorantGaramond-Bold",   size: 26)
    static let leagueScreenTitle = Font.custom("CormorantGaramond-SemiBold", size: 21)
    static let leagueConcierge  = Font.custom("CormorantGaramond-MediumItalic", size: 16)
    static let leagueStat       = Font.custom("CormorantGaramond-SemiBold", size: 21)
    static let leagueSubtitle   = Font.custom("Jost-Medium",   size: 16)
    static let leagueBody       = Font.custom("Jost-Regular",  size: 15)
    static let leagueCredential = Font.custom("Jost-Regular",  size: 13)
    static let leagueEyebrow    = Font.custom("Jost-SemiBold", size: 11)
    static let leagueButton     = Font.custom("Jost-SemiBold", size: 13)
    static let leagueCaption    = Font.custom("Jost-Regular",  size: 12)
    static let leagueTab        = Font.custom("Jost-Medium",   size: 9)
}

// Tracked-uppercase helper for eyebrows / buttons / tabs
extension View {
    func leagueUppercase(_ tracking: CGFloat) -> some View {
        self.textCase(.uppercase).tracking(tracking)
    }
}
```

## 3. Signature Components

### Prospect Card (the embossed calling card)

```swift
struct ProspectCard: View {
    let photo: String
    let name: String
    let age: Int
    let title: String      // "VP Strategy · Goldman Sachs"
    let school: String     // "Harvard Business School"
    let location: String   // "2 mi · Tribeca, New York"
    let verified: Bool

    var body: some View {
        VStack(spacing: 0) {
            ZStack(alignment: .topTrailing) {
                Image(photo)
                    .resizable().aspectRatio(contentMode: .fill)
                    .frame(maxWidth: .infinity)
                    .frame(height: 280)
                    .clipped()
                if verified {
                    Image(systemName: "star.fill")
                        .font(.system(size: 15))
                        .foregroundStyle(Color.leagueGold)
                        .frame(width: 30, height: 30)
                        .background(Color.black.opacity(0.6), in: Circle())
                        .overlay(Circle().strokeBorder(Color.leagueGold, lineWidth: 1))
                        .padding(14)
                }
            }
            VStack(alignment: .leading, spacing: 0) {
                HStack(alignment: .firstTextBaseline, spacing: 6) {
                    Text(name).font(.leagueName).foregroundStyle(Color.leagueTextPrimary)
                    Text("\(age)").font(.leagueSerif(26, weight: .regular)).foregroundStyle(Color.leagueTextSecondary)
                }
                Rectangle().fill(Color.leagueGold).frame(width: 28, height: 1).padding(.vertical, 8)
                CredentialLine(title)
                CredentialLine(school)
                CredentialLine(location)
            }
            .frame(maxWidth: .infinity, alignment: .leading)
            .padding(.horizontal, 18).padding(.vertical, 14)
        }
        .background(Color.leagueCardBase)
        .clipShape(RoundedRectangle(cornerRadius: 6))
        .overlay(RoundedRectangle(cornerRadius: 6).strokeBorder(Color.leagueHairline, lineWidth: 1))
        // NO shadow — depth is the hairline
    }
}

struct CredentialLine: View {
    let text: String
    init(_ text: String) { self.text = text }
    var body: some View {
        HStack(alignment: .top, spacing: 6) {
            Text("◦").font(.leagueCredential).foregroundStyle(Color.leagueGold)
            Text(text).font(.leagueCredential).foregroundStyle(Color.leagueTextSecondary)
        }
        .padding(.vertical, 2)
    }
}
```

### Card Action Row

```swift
struct CardActions: View {
    var onPass: () -> Void = {}
    var onHeart: () -> Void = {}
    var onMessage: () -> Void = {}

    var body: some View {
        HStack {
            CircleAction(systemName: "xmark", size: 48, tint: .leagueTextSecondary,
                         border: .leagueHairline, action: onPass)
            Spacer()
            CircleAction(systemName: "heart", size: 56, tint: .leagueGold,
                         border: .leagueGold, action: onHeart)  // focal — gold OUTLINE, not fill
            Spacer()
            CircleAction(systemName: "bubble.left", size: 48, tint: .leagueGold,
                         border: .leagueHairline, action: onMessage)
        }
        .padding(.horizontal, 18)
    }
}

struct CircleAction: View {
    let systemName: String
    let size: CGFloat
    let tint: Color
    let border: Color
    let action: () -> Void

    var body: some View {
        Button(action: {
            UIImpactFeedbackGenerator(style: .soft).impactOccurred()
            action()
        }) {
            Image(systemName: systemName)
                .font(.system(size: size * 0.4, weight: .regular))
                .foregroundStyle(tint)
                .frame(width: size, height: size)
                .background(Color.leagueSurface1, in: Circle())
                .overlay(Circle().strokeBorder(border, lineWidth: 1))
        }
        .buttonStyle(.plain)
    }
}
```

### Concierge Note

```swift
struct ConciergeNote: View {
    let message: String
    var onIntroduce: (() -> Void)? = nil

    var body: some View {
        HStack(spacing: 0) {
            Rectangle().fill(Color.leagueGold).frame(width: 2)
            VStack(alignment: .leading, spacing: 8) {
                HStack(spacing: 8) {
                    Image(systemName: "star.fill").font(.system(size: 12)).foregroundStyle(Color.leagueGold)
                    Text("Your Concierge")
                        .font(.leagueEyebrow).foregroundStyle(Color.leagueGold)
                        .leagueUppercase(2)
                }
                Text(message)
                    .font(.leagueConcierge).italic()
                    .foregroundStyle(Color.leagueTextPrimary)
                    .lineSpacing(5)
                if let onIntroduce {
                    Button("Make the introduction →") { onIntroduce() }
                        .font(.leagueButton).foregroundStyle(Color.leagueGold)
                        .leagueUppercase(1.2)
                }
            }
            .padding(18)
        }
        .background(Color.leagueSurface2)
        .clipShape(RoundedRectangle(cornerRadius: 4))
        .overlay(RoundedRectangle(cornerRadius: 4).strokeBorder(Color.leagueHairline, lineWidth: 1))
    }
}
```

### Batch Banner

```swift
struct BatchBanner: View {
    let count: Int
    var body: some View {
        HStack {
            Text("Today's Batch")
                .font(.leagueEyebrow).foregroundStyle(Color.leagueTextSecondary)
                .leagueUppercase(1.5)
            Spacer()
            Text("\(count) Prospects")
                .font(.leagueStat).foregroundStyle(Color.leagueGold)
        }
        .padding(.horizontal, 14).padding(.vertical, 10)
        .background(Color.leagueSurface1)
        .clipShape(RoundedRectangle(cornerRadius: 4))
        .overlay(RoundedRectangle(cornerRadius: 4).strokeBorder(Color.leagueHairline, lineWidth: 1))
    }
}
```

### Primary Button

```swift
struct LeaguePrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.leagueButton).foregroundStyle(Color.leagueOnGold)
                .leagueUppercase(1.6)
                .frame(maxWidth: .infinity).padding(.vertical, 15)
                .background(Color.leagueGold)
                .clipShape(RoundedRectangle(cornerRadius: 3))
        }
        .buttonStyle(.plain)
    }
}

struct LeagueOutlineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.leagueButton).foregroundStyle(Color.leagueGold)
                .leagueUppercase(1.6)
                .frame(maxWidth: .infinity).padding(.vertical, 14)
                .overlay(RoundedRectangle(cornerRadius: 3).strokeBorder(Color.leagueGold, lineWidth: 1))
        }
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct LeagueTabView: View {
    var body: some View {
        TabView {
            ProspectsView().tabItem { Label("Prospects", systemImage: "rectangle.portrait.on.rectangle.portrait") }
            MatchesView().tabItem { Label("Matches", systemImage: "heart") }
            ConciergeView().tabItem { Label("Concierge", systemImage: "bubble.left") }
            ProfileView().tabItem { Label("Profile", systemImage: "person") }
        }
        .tint(.leagueGold) // active = gold; no pill indicator
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(red: 0.039, green: 0.039, blue: 0.039, alpha: 0.96)
            a.shadowColor = UIColor(red: 0.165, green: 0.149, blue: 0.125, alpha: 1) // 1px hairline, not a shadow blur
            let inactive = UIColor(red: 0.420, green: 0.400, blue: 0.361, alpha: 1)
            a.stackedLayoutAppearance.normal.iconColor = inactive
            a.stackedLayoutAppearance.normal.titleTextAttributes = [.foregroundColor: inactive]
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Card advance — restrained fade + drift, never a thrown swipe
withAnimation(.easeOut(duration: 0.28)) {
    cardOpacity = 0; cardOffsetY = -12
}
// then load next prospect and cross-fade in

// Heart send — outline fills gold, gentle pulse
withAnimation(.easeOut(duration: 0.24)) {
    heartFilled = true; heartScale = 1.12
}
// then settle to 1.0; single soft haptic
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Concierge note arrival — gold rule draws, then italic fades in
@State private var ruleHeight: CGFloat = 0
.onAppear {
    withAnimation(.easeOut(duration: 0.2)) { ruleHeight = fullHeight }
    withAnimation(.easeOut(duration: 0.18).delay(0.2)) { textOpacity = 1 }
}

// Batch count — odometer roll
.contentTransition(.numericText())
withAnimation(.easeInOut(duration: 0.32)) { batchCount = newCount }

// Match celebration — wax-seal stroke draw (no confetti)
SealShape().trim(from: 0, to: drawProgress)
    .stroke(Color.leagueChampagne, lineWidth: 2)
withAnimation(.easeInOut(duration: 0.6)) { drawProgress = 1 }

// Tab switch — instant tint cross-fade (no pill slide)
// Field focus — bottom hairline brightens transparent → gold over 160ms

// Haptics — restraint extends to touch
UIImpactFeedbackGenerator(style: .soft).impactOccurred()        // heart send
UINotificationFeedbackGenerator().notificationOccurred(.success) // introduction accepted
UISelectionFeedbackGenerator().selectionChanged()               // tab change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Prospects (tab) | `rectangle.portrait.on.rectangle.portrait` | 21pt |
| Matches (tab) | `heart` / `heart.fill` | 21pt |
| Concierge (tab) | `bubble.left` / `bubble.left.fill` | 21pt |
| Profile (tab) | `person` / `person.fill` | 21pt |
| Heart (card action) | `heart` | 22pt |
| Pass (card action) | `xmark` | 20pt |
| Message (card action) | `bubble.left` | 19pt |
| Verification seal | `star.fill` | 15pt |
| Concierge mark | `star.fill` | 12pt |
| Search | `magnifyingglass` | 22pt |
| Settings | `gearshape` | 22pt |
| Credential mark | `circle` (or text `◦`) | 6pt |
| Membership / premium | `crown` | 16pt |
| Back | `chevron.left` | 20pt |

## 7. Dark Mode

```swift
// The League is dark-native — there is no light mode in spirit.
// Force the dark aesthetic regardless of system setting.
struct LeagueTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.leagueCanvas)
            .foregroundStyle(Color.leagueTextPrimary)
            .tint(Color.leagueGold)
            .environment(\.colorScheme, .dark) // never render a light variant
    }
}
extension View { func leagueTheme() -> some View { modifier(LeagueTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.contentTransition(.numericText())` for the batch odometer requires iOS 16; `.sensoryFeedback` is iOS 17 — examples use `UIImpactFeedbackGenerator` for iOS 16 support)
- Bundle Cormorant Garamond + Jost TTFs via `Info.plist` `UIAppFonts` — both SIL OFL, free to redistribute
- The League is dark-native: force `.environment(\.colorScheme, .dark)`; do not author a light variant — the brand has no light mode
- Dynamic Type: scale serif names, screen titles, body, credentials, concierge notes; keep eyebrows, tab labels, the batch count, and button labels at FIXED sizes (letter-spaced uppercase, layout-sensitive)
- VoiceOver: announce the prospect card as one element — "{name}, {age}, {title}, {school}, {location}, verified member"; card actions get explicit labels ("Pass", "Send heart", "Message"); the concierge note announces "Concierge: {message}"; the batch banner announces "{count} prospects in today's batch"
- Color contrast: `#EDE9E2` on `#0A0A0A` far exceeds WCAG AA; `#C8A35A` on `#0A0A0A` passes AA for the gold UI text; gold-on-gold (CTA: `#1A1408` on `#C8A35A`) passes AA at 13pt bold — verify if you tweak the gold
- The gold hairline (`#2A2620`) is the only depth cue; ensure it is never the *sole* carrier of meaning — pair structural borders with text labels for screen readers
- Reduce Motion: replace the card drift with a straight cross-fade; the concierge rule-draw becomes an instant appear; the wax-seal celebration shows the completed seal without the stroke animation — keep the success haptic
- Hit targets: Heart action 56pt; Pass / Message 48pt; tab icons 21pt glyph in a 44pt hit; concierge inline CTA ≥ 44pt row
- No shadows: never add `.shadow(...)` — separation is the 1px `#2A2620` border plus the tonal surface lift; a drop shadow breaks the engraved, black-tie identity
