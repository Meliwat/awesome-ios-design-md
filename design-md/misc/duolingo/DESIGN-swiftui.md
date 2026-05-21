# Duolingo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Duolingo's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Targets iOS 17+.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand Greens
    static let duoFeatherGreen     = Color(red: 0.345, green: 0.800, blue: 0.008) // #58CC02
    static let duoMaskGreen        = Color(red: 0.537, green: 0.886, blue: 0.098) // #89E219
    static let duoButtonGreenLedge = Color(red: 0.345, green: 0.655, blue: 0.000) // #58A700

    // MARK: - Gamification
    static let duoCardinalRed      = Color(red: 1.000, green: 0.294, blue: 0.294) // #FF4B4B
    static let duoCardinalLedge    = Color(red: 0.898, green: 0.188, blue: 0.188) // #E53030
    static let duoFoxOrange        = Color(red: 1.000, green: 0.588, blue: 0.000) // #FF9600
    static let duoBeeYellow        = Color(red: 1.000, green: 0.784, blue: 0.000) // #FFC800
    static let duoMacawBlue        = Color(red: 0.110, green: 0.690, blue: 0.965) // #1CB0F6
    static let duoBeetlePurple     = Color(red: 0.808, green: 0.510, blue: 1.000) // #CE82FF

    // MARK: - Canvas (Light)
    static let duoSnow             = Color.white                                  // #FFFFFF
    static let duoPolar            = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let duoSwan             = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let duoSwanLedge        = Color(red: 0.769, green: 0.769, blue: 0.769) // #C4C4C4

    // MARK: - Canvas (Dark)
    static let duoDarkCanvas       = Color(red: 0.075, green: 0.122, blue: 0.141) // #131F24
    static let duoDarkSurface1     = Color(red: 0.122, green: 0.173, blue: 0.204) // #1F2C34
    static let duoDarkSurface2     = Color(red: 0.216, green: 0.275, blue: 0.310) // #37464F

    // MARK: - Text
    static let duoEel              = Color(red: 0.294, green: 0.294, blue: 0.294) // #4B4B4B
    static let duoHare             = Color(red: 0.686, green: 0.686, blue: 0.686) // #AFAFAF
    static let duoWolf             = Color(red: 0.467, green: 0.467, blue: 0.467) // #777777

    // MARK: - Tile States
    static let duoTileSelectedFill   = Color(red: 0.867, green: 0.957, blue: 1.000) // #DDF4FF
    static let duoTileSelectedBorder = Color(red: 0.518, green: 0.847, blue: 1.000) // #84D8FF
    static let duoTileCorrectFill    = Color(red: 0.843, green: 1.000, blue: 0.722) // #D7FFB8
    static let duoTileWrongFill      = Color(red: 1.000, green: 0.875, blue: 0.878) // #FFDFE0
}
```

## 2. Typography

Feather Bold and DIN Next Rounded Pro are proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to SF Pro Rounded — its rounded terminals come closest.

```swift
extension Font {
    // Feather Bold — headlines and numbers
    static let duoWordmark       = Font.custom("Feather-Bold", size: 34).weight(.bold)
    static let duoHeroNumber     = Font.custom("Feather-Bold", size: 56).weight(.bold)
    static let duoScreenTitle    = Font.custom("Feather-Bold", size: 28).weight(.bold)
    static let duoQuestion       = Font.custom("Feather-Bold", size: 22).weight(.bold)
    static let duoHudNumber      = Font.custom("Feather-Bold", size: 18).weight(.bold)

    // DIN Next Rounded Pro — UI
    static let duoSectionHeader  = Font.custom("DINNextRoundedPro-Bold", size: 20).weight(.heavy)
    static let duoSkillLabel     = Font.custom("DINNextRoundedPro-Bold", size: 16).weight(.heavy)
    static let duoButton         = Font.custom("DINNextRoundedPro-Bold", size: 17).weight(.heavy)
    static let duoTile           = Font.custom("DINNextRoundedPro-Bold", size: 17).weight(.bold)
    static let duoBody           = Font.custom("DINNextRoundedPro-Regular", size: 16).weight(.regular)
    static let duoHelper         = Font.custom("DINNextRoundedPro-Medium", size: 14).weight(.medium)
    static let duoMeta           = Font.custom("DINNextRoundedPro-Medium", size: 13).weight(.medium)
    static let duoTab            = Font.custom("DINNextRoundedPro-Bold", size: 11).weight(.bold)
    static let duoLabelUpper     = Font.custom("DINNextRoundedPro-Bold", size: 12).weight(.heavy)
}

// Rounded SF Pro fallback if custom faces are unavailable:
extension Font {
    static func duoRounded(_ size: CGFloat, weight: Font.Weight = .bold) -> Font {
        .system(size: size, weight: weight, design: .rounded)
    }
}
```

## 3. Signature Components

### The 3D Primary Button (The Green Slab)

```swift
struct DuoPrimaryButton: View {
    let title: String
    var variant: Variant = .primary
    let action: () -> Void

    enum Variant {
        case primary, neutral, destructive
        var topColor: Color {
            switch self { case .primary: .duoFeatherGreen; case .neutral: .duoSnow; case .destructive: .duoCardinalRed }
        }
        var ledgeColor: Color {
            switch self { case .primary: .duoButtonGreenLedge; case .neutral: .duoSwan; case .destructive: .duoCardinalLedge }
        }
        var textColor: Color {
            switch self { case .primary, .destructive: .white; case .neutral: .duoEel }
        }
    }

    @State private var isPressed = false

    var body: some View {
        Button {
            action()
        } label: {
            Text(title.uppercased())
                .font(.duoButton)
                .tracking(0.4)
                .foregroundStyle(variant.textColor)
                .frame(maxWidth: .infinity, minHeight: 56)
                .background(
                    ZStack(alignment: .bottom) {
                        RoundedRectangle(cornerRadius: 16)
                            .fill(variant.ledgeColor)
                            .offset(y: 4)
                        RoundedRectangle(cornerRadius: 16)
                            .fill(variant.topColor)
                            .overlay(
                                variant == .neutral
                                    ? RoundedRectangle(cornerRadius: 16).stroke(Color.duoSwan, lineWidth: 2)
                                    : nil
                            )
                            .offset(y: isPressed ? 4 : 0)
                    }
                )
        }
        .buttonStyle(DuoSlabStyle(isPressed: $isPressed))
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isPressed)
    }
}

struct DuoSlabStyle: ButtonStyle {
    @Binding var isPressed: Bool
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .onChange(of: configuration.isPressed) { _, pressed in
                withAnimation(pressed ? .linear(duration: 0.08) : .spring(response: 0.25, dampingFraction: 0.7)) {
                    isPressed = pressed
                }
            }
    }
}
```

### Skill Node (Path Circle)

```swift
struct SkillNode: View {
    let iconSymbol: String
    var state: State = .locked
    var crownCount: Int = 0
    let action: () -> Void

    enum State { case locked, available, current, completed, gold, legendary }

    var body: some View {
        Button(action: action) {
            ZStack(alignment: .top) {
                Circle()
                    .fill(ledgeColor)
                    .offset(y: 4)
                    .frame(width: size, height: size)
                Circle()
                    .fill(topColor)
                    .frame(width: size, height: size)
                    .overlay(
                        Image(systemName: state == .locked ? "lock.fill" : iconSymbol)
                            .font(.system(size: size * 0.4, weight: .heavy))
                            .foregroundStyle(state == .locked ? Color.duoHare : .white)
                    )
                    .overlay(
                        state == .current
                            ? Circle().stroke(Color.duoMaskGreen, lineWidth: 4).scaleEffect(1.08).opacity(0.6)
                            : nil
                    )
                if crownCount > 0 && state != .locked {
                    HStack(spacing: 2) {
                        Image(systemName: "crown.fill").foregroundStyle(Color.duoBeeYellow)
                        Text("\(crownCount)").font(.duoMeta).foregroundStyle(.white)
                    }
                    .padding(.horizontal, 8).padding(.vertical, 3)
                    .background(Capsule().fill(Color.duoEel))
                    .offset(y: -14)
                }
            }
        }
        .buttonStyle(DuoScaleStyle(pressedScale: 0.96))
        .sensoryFeedback(.impact(flexibility: .soft), trigger: state)
    }

    private var size: CGFloat { state == .current ? 96 : 80 }
    private var topColor: Color {
        switch state {
        case .locked: .duoSwan
        case .available, .current, .completed: .duoFeatherGreen
        case .gold: .duoBeeYellow
        case .legendary: .duoBeetlePurple
        }
    }
    private var ledgeColor: Color {
        switch state {
        case .locked: .duoSwanLedge
        case .available, .current, .completed: .duoButtonGreenLedge
        case .gold: Color(red: 0.886, green: 0.624, blue: 0.012)
        case .legendary: Color(red: 0.627, green: 0.357, blue: 0.847)
        }
    }
}

struct DuoScaleStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.96
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.2, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Answer Tile

```swift
struct AnswerTile: View {
    let word: String
    @Binding var state: TileState
    let onTap: () -> Void

    enum TileState { case idle, selected, correct, wrong }
    @State private var shakeOffset: CGFloat = 0

    var body: some View {
        Button {
            onTap()
        } label: {
            Text(word)
                .font(.duoTile)
                .foregroundStyle(textColor)
                .padding(.vertical, 12).padding(.horizontal, 20)
                .frame(minHeight: 48)
                .background(
                    ZStack(alignment: .bottom) {
                        RoundedRectangle(cornerRadius: 12).fill(ledgeColor).offset(y: 2)
                        RoundedRectangle(cornerRadius: 12).fill(fillColor)
                            .overlay(RoundedRectangle(cornerRadius: 12).stroke(borderColor, lineWidth: 2))
                    }
                )
        }
        .offset(x: shakeOffset)
        .onChange(of: state) { _, newValue in
            if newValue == .wrong {
                withAnimation(.default.repeatCount(4, autoreverses: true).speed(3)) { shakeOffset = 6 }
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.4) { shakeOffset = 0 }
            }
        }
        .sensoryFeedback(.success, trigger: state == .correct)
        .sensoryFeedback(.warning, trigger: state == .wrong)
    }

    private var fillColor: Color {
        switch state { case .idle: .duoSnow; case .selected: .duoTileSelectedFill; case .correct: .duoTileCorrectFill; case .wrong: .duoTileWrongFill }
    }
    private var borderColor: Color {
        switch state { case .idle: .duoSwan; case .selected: .duoTileSelectedBorder; case .correct: .duoFeatherGreen; case .wrong: .duoCardinalRed }
    }
    private var ledgeColor: Color { state == .idle ? .duoSwan : borderColor }
    private var textColor: Color {
        switch state { case .idle, .selected: .duoEel; case .correct: .duoButtonGreenLedge; case .wrong: .duoCardinalRed }
    }
}
```

### HUD Chip (Streak / Gems / Hearts)

```swift
struct DuoHudChip: View {
    enum Kind { case streak, gems, hearts }
    let kind: Kind
    let value: Int

    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: iconName).font(.system(size: 20, weight: .bold)).foregroundStyle(tint)
            Text("\(value)").font(.duoHudNumber).foregroundStyle(.primary)
        }
        .padding(.horizontal, 10).padding(.vertical, 4)
        .frame(minHeight: 32)
    }

    private var iconName: String {
        switch kind { case .streak: "flame.fill"; case .gems: "diamond.fill"; case .hearts: "heart.fill" }
    }
    private var tint: Color {
        switch kind { case .streak: .duoFoxOrange; case .gems: .duoMacawBlue; case .hearts: .duoCardinalRed }
    }
}
```

### Celebration Takeover

```swift
struct DuoCelebration: View {
    let xpEarned: Int
    let onContinue: () -> Void

    var body: some View {
        ZStack {
            Color.duoFeatherGreen.ignoresSafeArea()
            VStack(spacing: 24) {
                Image("duo-cheering") // bundled illustration asset
                    .resizable().aspectRatio(contentMode: .fit)
                    .frame(maxHeight: 280)
                Text("LESSON COMPLETE!")
                    .font(.duoScreenTitle).tracking(0.4)
                    .foregroundStyle(.white)
                Text("+\(xpEarned) XP")
                    .font(.duoQuestion)
                    .foregroundStyle(.white)
                    .padding(.horizontal, 20).padding(.vertical, 8)
                    .background(Capsule().fill(Color.duoBeeYellow.opacity(0.25)))
                    .overlay(Capsule().stroke(Color.duoBeeYellow, lineWidth: 2))
                Spacer()
                DuoPrimaryButton(title: "Continue", variant: .neutral, action: onContinue)
                    .padding(.horizontal, 16)
            }
            .padding(.top, 80)
            ConfettiLayer() // see §6
        }
        .sensoryFeedback(.success, trigger: xpEarned)
    }
}
```

## 4. The Learning Path

```swift
struct LearningPath: View {
    let nodes: [PathNode]

    struct PathNode: Identifiable {
        let id = UUID()
        let icon: String
        let title: String
        let state: SkillNode.State
        let crowns: Int
    }

    var body: some View {
        ScrollView {
            VStack(spacing: 32) {
                ForEach(Array(nodes.enumerated()), id: \.element.id) { index, node in
                    HStack {
                        if index.isMultiple(of: 2) { Spacer() }
                        VStack(spacing: 8) {
                            SkillNode(iconSymbol: node.icon, state: node.state, crownCount: node.crowns) {}
                            Text(node.title)
                                .font(.duoSkillLabel).foregroundStyle(.primary)
                        }
                        .offset(x: offset(for: index))
                        if !index.isMultiple(of: 2) { Spacer() }
                    }
                }
            }
            .padding(.vertical, 32).padding(.horizontal, 16)
        }
        .background(Color.duoSnow)
    }

    private func offset(for index: Int) -> CGFloat {
        // Alternating ±60pt wave pattern
        let phase = Double(index) * .pi / 3
        return CGFloat(sin(phase)) * 60
    }
}
```

## 5. Tab Bar

```swift
struct DuoRootTabView: View {
    var body: some View {
        TabView {
            LearnView().tabItem {
                Label("Learn", systemImage: "house.fill")
            }
            LeaderboardView().tabItem {
                Label("Leaderboard", systemImage: "trophy.fill")
            }
            QuestsView().tabItem {
                Label("Quests", systemImage: "scroll.fill")
            }
            ShopView().tabItem {
                Label("Shop", systemImage: "cart.fill")
            }
            ProfileView().tabItem {
                Label("Profile", systemImage: "person.fill")
            }
        }
        .tint(.duoFeatherGreen)
    }
}

// For Duolingo's signature "pill background behind active tab", use a custom
// UITabBarAppearance or migrate to a custom tab bar on iOS 18+ using the
// .tabViewStyle(.tabBarOnly) modifier with custom TabView backgrounds.
```

## 6. Motion & Effects

### Confetti Layer

```swift
struct ConfettiLayer: View {
    @State private var particles: [Particle] = []

    struct Particle: Identifiable {
        let id = UUID()
        let x: CGFloat
        let color: Color
        let delay: Double
    }

    private let palette: [Color] = [.duoFeatherGreen, .duoFoxOrange, .duoBeeYellow, .duoMacawBlue, .duoCardinalRed, .duoBeetlePurple]

    var body: some View {
        GeometryReader { geo in
            ZStack {
                ForEach(particles) { p in
                    RoundedRectangle(cornerRadius: 2)
                        .fill(p.color)
                        .frame(width: 8, height: 14)
                        .position(x: p.x * geo.size.width, y: -20)
                        .modifier(FallModifier(height: geo.size.height, delay: p.delay))
                }
            }
            .onAppear { seed() }
        }
        .allowsHitTesting(false)
    }

    private func seed() {
        particles = (0..<12).map { _ in
            Particle(x: CGFloat.random(in: 0.05...0.95), color: palette.randomElement()!, delay: .random(in: 0...0.3))
        }
    }
}

struct FallModifier: ViewModifier {
    let height: CGFloat
    let delay: Double
    @State private var y: CGFloat = 0
    @State private var rotate: Double = 0

    func body(content: Content) -> some View {
        content
            .offset(y: y)
            .rotationEffect(.degrees(rotate))
            .onAppear {
                withAnimation(.easeIn(duration: 1.2).delay(delay)) {
                    y = height + 40
                    rotate = .random(in: -360...360)
                }
            }
    }
}
```

### Streak Flame Pulse

```swift
struct StreakFlame: View {
    let count: Int
    @State private var scale: CGFloat = 1.0

    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: "flame.fill")
                .font(.system(size: 20, weight: .heavy))
                .foregroundStyle(Color.duoFoxOrange)
                .scaleEffect(scale)
                .onAppear {
                    withAnimation(.easeInOut(duration: 2).repeatForever(autoreverses: true)) {
                        scale = 1.05
                    }
                }
            Text("\(count)")
                .font(.duoHudNumber)
                .foregroundStyle(Color.duoFoxOrange)
        }
    }
}
```

### Haptics Summary
```swift
// Correct answer
.sensoryFeedback(.success, trigger: isCorrect)
// Wrong answer
.sensoryFeedback(.warning, trigger: isWrong)
// Button press
.sensoryFeedback(.impact(flexibility: .soft), trigger: isPressed)
// Level up / streak save / celebration
.sensoryFeedback(.success, trigger: celebrationTrigger)
// Skill node unlock
.sensoryFeedback(.impact(weight: .medium), trigger: nodeUnlocked)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Streak flame | `flame.fill` | 20pt |
| Gems | `diamond.fill` | 20pt |
| Hearts | `heart.fill` | 20pt |
| Locked skill | `lock.fill` | 32pt |
| Completed skill | `star.fill` / `checkmark` | 32pt |
| Speaking exercise | `waveform` | 24pt |
| Listening exercise | `ear.fill` | 24pt |
| Close lesson | `xmark` | 20pt |
| Continue | `arrow.right` | 20pt |
| Learn tab | `house.fill` | 28pt |
| Leaderboard tab | `trophy.fill` | 28pt |
| Quests tab | `scroll.fill` | 28pt |
| Shop tab | `cart.fill` | 28pt |
| Profile tab | `person.fill` | 28pt |
| Crown (skill mastery) | `crown.fill` | 16pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`; for iOS 16 fall back to `UIImpactFeedbackGenerator` + `UINotificationFeedbackGenerator`)
- Reduce Motion: disable the confetti layer, the flame pulse, and the node-current ring when `UIAccessibility.isReduceMotionEnabled`
- Dynamic Type: scale the question prompt, body copy, and lesson explanations; pin button labels, HUD numbers, tab labels at fixed sizes
- VoiceOver: label answer tiles with full text and state ("Answer tile: casa, selected"). On correct/wrong announce the toast: "Correct! +10 XP"
- Contrast: Feather Green on white clears WCAG AA only at 14pt+ bold — use white text on green surfaces for button labels
- Sound Effects: pair every haptic with an SFX asset via `AVAudioPlayer` — correct chime, wrong buzz, level-up cheer, streak-save whoosh, gem-earn coin
- Color blindness: never use red/green alone to signal correctness — pair with an icon (checkmark / X) and an animation
