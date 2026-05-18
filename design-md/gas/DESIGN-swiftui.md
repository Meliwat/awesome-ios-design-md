# Gas (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Gas's playful purple visual language into paste-ready SwiftUI code: `Color` extensions, the brand & choice gradients, `Font` extensions, and example views for the poll card, flame pill, and progress dots.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let gasIndigo  = Color(red: 0.424, green: 0.361, blue: 0.906) // #6C5CE7
    static let gasPurple  = Color(red: 0.627, green: 0.424, blue: 1.0)   // #A06CFF
    static let gasViolet  = Color(red: 0.482, green: 0.302, blue: 1.0)   // #7B4DFF (on-white tint)
    static let gasIndigoPressed = Color(red: 0.353, green: 0.290, blue: 0.820) // #5A4AD1

    // MARK: - Surfaces
    static let gasCardWhite   = Color.white                                   // #FFFFFF
    static let gasTintSurface = Color(red: 0.957, green: 0.949, blue: 1.0)   // #F4F2FF
    static let gasDivider     = Color(red: 0.925, green: 0.914, blue: 0.984) // #ECE9FB

    // MARK: - Text (on white cards)
    static let gasTextPrimary   = Color(red: 0.102, green: 0.082, blue: 0.188) // #1A1530
    static let gasTextSecondary = Color(red: 0.420, green: 0.396, blue: 0.533) // #6B6588
    static let gasTextTertiary  = Color(red: 0.635, green: 0.616, blue: 0.722) // #A29DB8

    // MARK: - Currency
    static let gasFlame     = Color(red: 1.0, green: 0.478, blue: 0.271) // #FF7A45

    // MARK: - Choice palette (fixed slots)
    static let gasChoice1A = Color(red: 0.424, green: 0.361, blue: 0.906) // #6C5CE7
    static let gasChoice1B = Color(red: 0.545, green: 0.424, blue: 1.0)   // #8B6CFF
    static let gasChoice2A = Color(red: 1.0, green: 0.424, blue: 0.784)   // #FF6CC8
    static let gasChoice2B = Color(red: 1.0, green: 0.612, blue: 0.859)   // #FF9CDB
    static let gasChoice3A = Color(red: 1.0, green: 0.788, blue: 0.235)   // #FFC93C
    static let gasChoice3B = Color(red: 1.0, green: 0.694, blue: 0.235)   // #FFB13C
    static let gasChoice3Text = Color(red: 0.290, green: 0.200, blue: 0.0) // #4A3300
    static let gasChoice4A = Color(red: 0.169, green: 0.769, blue: 0.541) // #2BC48A
    static let gasChoice4B = Color(red: 0.310, green: 0.851, blue: 0.651) // #4FD9A6

    // MARK: - Semantic
    static let gasSuccess = Color(red: 0.169, green: 0.769, blue: 0.541) // #2BC48A
    static let gasError   = Color(red: 1.0,   green: 0.329, blue: 0.439) // #FF5470
}

extension LinearGradient {
    static let gasBrand = LinearGradient(colors: [.gasIndigo, .gasPurple],
                                         startPoint: .top, endPoint: .bottom)
    static let gasChoice1 = LinearGradient(colors: [.gasChoice1A, .gasChoice1B], startPoint: .topLeading, endPoint: .bottomTrailing)
    static let gasChoice2 = LinearGradient(colors: [.gasChoice2A, .gasChoice2B], startPoint: .topLeading, endPoint: .bottomTrailing)
    static let gasChoice3 = LinearGradient(colors: [.gasChoice3A, .gasChoice3B], startPoint: .topLeading, endPoint: .bottomTrailing)
    static let gasChoice4 = LinearGradient(colors: [.gasChoice4A, .gasChoice4B], startPoint: .topLeading, endPoint: .bottomTrailing)
}

let gasPlumShadow = Color(red: 0.157, green: 0.078, blue: 0.353) // rgba(40,20,90)
```

## 2. Typography

Gas's UI face is **Nunito** (SIL OFL — free to bundle), set HEAVY (800–900). Register the TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static func gas(_ size: CGFloat, weight: Font.Weight = .bold) -> Font {
        let face: String = {
            switch weight {
            case .black, .heavy: return "Nunito-Black"
            case .bold:          return "Nunito-ExtraBold"
            case .semibold:      return "Nunito-Bold"
            default:             return "Nunito-SemiBold"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let gasDisplay   = Font.custom("Nunito-Black",    size: 32)
    static let gasScreen    = Font.custom("Nunito-Black",    size: 26)
    static let gasQuestion  = Font.custom("Nunito-Black",    size: 23)
    static let gasCardTitle = Font.custom("Nunito-ExtraBold", size: 18)
    static let gasChoice    = Font.custom("Nunito-ExtraBold", size: 16)
    static let gasBody      = Font.custom("Nunito-Bold",     size: 15)
    static let gasBigNum    = Font.custom("Nunito-Black",    size: 28)
    static let gasMeta      = Font.custom("Nunito-Bold",     size: 13)
    static let gasTag       = Font.custom("Nunito-ExtraBold", size: 12)
    static let gasButton    = Font.custom("Nunito-Black",    size: 16)
    static let gasPill      = Font.custom("Nunito-Black",    size: 15)
    static let gasTab       = Font.custom("Nunito-ExtraBold", size: 10)
}
```

## 3. Signature Components

### Poll Card

```swift
struct PollChoice: Identifiable { let id = UUID(); let name: String }

struct PollCard: View {
    let emoji: String
    let question: String
    let choices: [PollChoice]      // exactly 4
    let onPick: (PollChoice) -> Void
    @State private var pressedID: UUID?

    private let slots: [LinearGradient] = [.gasChoice1, .gasChoice2, .gasChoice3, .gasChoice4]
    private func textColor(_ i: Int) -> Color { i == 2 ? .gasChoice3Text : .white }
    private let cols = [GridItem(.flexible(), spacing: 12), GridItem(.flexible(), spacing: 12)]

    var body: some View {
        VStack(spacing: 0) {
            Text(emoji).font(.system(size: 50))
            Text(question)
                .font(.gasQuestion).foregroundStyle(Color.gasTextPrimary)
                .multilineTextAlignment(.center)
                .padding(.top, 14)
            Text("ANONYMOUS · BE NICE")
                .font(.gasTag).tracking(0.5)
                .foregroundStyle(Color.gasViolet)
                .padding(.top, 8)

            LazyVGrid(columns: cols, spacing: 12) {
                ForEach(Array(choices.enumerated()), id: \.element.id) { i, c in
                    Button {
                        onPick(c)
                    } label: {
                        Text(c.name)
                            .font(.gasChoice).foregroundStyle(textColor(i))
                            .frame(maxWidth: .infinity)
                            .padding(.vertical, 16).padding(.horizontal, 10)
                            .background(RoundedRectangle(cornerRadius: 20).fill(slots[i]))
                    }
                    .buttonStyle(.plain)
                    .scaleEffect(pressedID == c.id ? 1.04 : 1.0)
                    .animation(.spring(response: 0.2, dampingFraction: 0.5), value: pressedID)
                    .simultaneousGesture(
                        DragGesture(minimumDistance: 0)
                            .onChanged { _ in pressedID = c.id }
                            .onEnded { _ in pressedID = nil }
                    )
                }
            }
            .padding(.top, 22)

            Button { } label: {
                Text("🔄 Shuffle names").font(.gas(14, weight: .bold)).foregroundStyle(Color.gasTextTertiary)
            }
            .buttonStyle(.plain)
            .padding(.top, 20)
        }
        .padding(.init(top: 26, leading: 20, bottom: 24, trailing: 20))
        .background(
            RoundedRectangle(cornerRadius: 28).fill(Color.gasCardWhite)
                .shadow(color: gasPlumShadow.opacity(0.55), radius: 25, y: 24)
        )
    }
}
```

### Flame Pill (currency)

```swift
struct FlamePill: View {
    let count: Int
    var onGradient: Bool = true

    var body: some View {
        HStack(spacing: 6) {
            Text("🔥").font(.system(size: 16))
            Text("\(count)").font(.gasPill)
                .contentTransition(.numericText())
        }
        .foregroundStyle(onGradient ? .white : Color.gasFlame)
        .padding(.vertical, 7).padding(.horizontal, 14)
        .background(
            Capsule().fill(onGradient ? Color.white.opacity(0.22) : Color.gasFlame.opacity(0.15))
        )
    }
}
```

### Progress Dots

```swift
struct PollProgress: View {
    let total: Int
    let index: Int
    var body: some View {
        HStack(spacing: 6) {
            ForEach(0..<total, id: \.self) { i in
                Capsule()
                    .fill(i == index ? Color.white : Color.white.opacity(0.35))
                    .frame(width: i == index ? 30 : 22, height: 5)
                    .animation(.spring(response: 0.3, dampingFraction: 0.7), value: index)
            }
        }
    }
}
```

### Primary Button + App Background

```swift
struct GasPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.gasButton).foregroundStyle(.white)
                .padding(.vertical, 15).padding(.horizontal, 30)
                .background(Capsule().fill(LinearGradient.gasBrand))
                .shadow(color: Color.gasIndigo.opacity(0.45), radius: 24, y: 10)
        }
        .buttonStyle(.plain)
    }
}

struct GasBackground: View {
    var body: some View {
        LinearGradient(colors: [.gasIndigo, .gasPurple], startPoint: .top, endPoint: .bottom)
            .ignoresSafeArea()
    }
}
```

## 4. Bottom Tab Bar

```swift
struct GasTabView: View {
    var body: some View {
        TabView {
            PollsView().tabItem { Label("Polls", systemImage: "rectangle.stack.fill") }
            InboxView().tabItem { Label("Inbox", systemImage: "checkmark.seal.fill") }
            AddView().tabItem { Label("", systemImage: "plus") }        // center, raised circle
            SchoolView().tabItem { Label("School", systemImage: "building.2.fill") }
            YouView().tabItem { Label("You", systemImage: "person.fill") }
        }
        .tint(.white) // active = solid white on the gradient
        // Render the center "+" as a custom raised 50pt white circle overlay if not using stock TabView
    }
}

// Center raised action (overlay-style)
struct GasCenterAdd: View {
    var body: some View {
        ZStack {
            Circle().fill(Color.white).frame(width: 50, height: 50)
                .shadow(color: gasPlumShadow.opacity(0.5), radius: 20, y: 8)
            Image(systemName: "plus").font(.system(size: 24, weight: .black))
                .foregroundStyle(Color.gasIndigo)
        }
        .offset(y: -16)
    }
}
```

## 5. Motion

```swift
// Choice press — springy
.scaleEffect(pressed ? 1.04 : 1.0)
.animation(.spring(response: 0.2, dampingFraction: 0.5), value: pressed)

// Card transition (vote): card flies out, next springs in
withAnimation(.easeIn(duration: 0.28)) { cardOut = true }
withAnimation(.spring(response: 0.35, dampingFraction: 0.7)) { nextIn = true }

// Flame count-up
Text("\(count)").contentTransition(.numericText())
withAnimation(.easeOut(duration: 0.6)) { count = newValue } // + per-tick haptic

// Progress dot
.animation(.spring(response: 0.3, dampingFraction: 0.7), value: index)

// Milestone confetti — scale the number
withAnimation(.spring(response: 0.4, dampingFraction: 0.5)) { milestone = true } // 0 → 1.15 → 1.0

// Haptics
.sensoryFeedback(.impact(weight: .medium), trigger: votedChoice)   // vote
.sensoryFeedback(.impact(flexibility: .soft), trigger: flameTick)  // each flame increment
.sensoryFeedback(.success, trigger: milestoneReached)              // milestone
.sensoryFeedback(.selection, trigger: skipped)                     // skip / shuffle
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Polls (tab) | `rectangle.stack` / `.fill` | 23pt |
| Inbox (tab) | `checkmark.seal` / `.fill` | 23pt |
| Add (center) | `plus` | 24pt |
| School (tab) | `building.2` / `.fill` | 23pt |
| You (tab) | `person` / `.fill` | 23pt |
| Flame (currency) | 🔥 emoji (or `flame.fill` tinted `#FF7A45`) | 16pt |
| Shuffle | `shuffle` (or 🔄 emoji) | 14pt |
| Skip | `chevron.right` | 14pt |
| Lock (hint) | `lock.fill` | 16pt |
| Share / Invite | `square.and.arrow.up` | 22pt |
| Settings | `gearshape.fill` | 22pt |
| Back | `chevron.left` | 22pt |
| Close | `xmark` | 20pt |
| Confetti accent | `sparkles` | 18pt |

## 7. Light Mode (Gas is light-by-design)

```swift
struct GasTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(GasBackground())
            .foregroundStyle(.white)
            .preferredColorScheme(.light) // Gas has no dark theme — the brand IS the bright gradient
    }
}
extension View { func gasTheme() -> some View { modifier(GasTheme()) } }
```

> Gas intentionally has **no dark mode**. The identity is the bright indigo→purple gradient with white cards. Force `.light` and design for it; do not invert to a dark canvas.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Nunito TTFs (SemiBold/Bold/ExtraBold/Black) via `Info.plist` — SIL OFL, free to distribute
- Gas is light-by-design: force `.preferredColorScheme(.light)`; never ship a dark inversion — the gradient brand must hold
- Dynamic Type: support on poll questions, card titles, body; keep choice button labels (the 2×2 grid is layout-critical), tab labels, poll tags, progress dots FIXED
- VoiceOver: read a poll as "Poll: {question}. Four choices."; each name button "{name}, button, choice {n} of 4"; the flame pill "{count} flames"; announce flame earns ("Earned 3 flames, total 24")
- Color contrast: `#1A1530` on `#FFFFFF` passes WCAG AA; white on the indigo/pink/green choice gradients passes; the YELLOW slot MUST use `#4A3300` text (white fails on `#FFC93C`) — this pairing is mandatory
- Reduce Motion: replace the card fly-out/spring with a quick crossfade; replace confetti with a static check; keep flame count instant (no per-tick) — but keep the experience cheerful
- Safety/age: this is a teen app — keep all copy positive; never expose real identities in poll context; respect parental-controls / Screen Time
- Haptics are part of the joy — keep medium impact on vote and the soft per-flame tick unless Reduce Motion/low-power is on
