# Grammarly (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grammarly's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (single accent)
    static let gmGreen        = Color(red: 0.082, green: 0.765, blue: 0.604) // #15C39A
    static let gmGreenDeep    = Color(red: 0.067, green: 0.651, blue: 0.514) // #11A683
    static let gmGreenPressed = Color(red: 0.055, green: 0.541, blue: 0.427) // #0E8A6D
    static let gmGreenOnBtn   = Color(red: 0.024, green: 0.157, blue: 0.122) // #06281F (text on green)

    // MARK: - Suggestion Categories (underlines/dots only)
    static let gmCorrectness  = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
    static let gmClarity      = Color(red: 0.231, green: 0.510, blue: 0.965) // #3B82F6
    static let gmEngagement   = Color(red: 0.086, green: 0.639, blue: 0.290) // #16A34A
    static let gmDelivery     = Color(red: 0.545, green: 0.361, blue: 0.965) // #8B5CF6

    // MARK: - Premium
    static let gmPremiumGold  = Color(red: 0.878, green: 0.659, blue: 0.180) // #E0A82E

    // MARK: - Canvas & Surfaces (Light)
    static let gmCanvas       = Color.white                                   // #FFFFFF
    static let gmSurface1     = Color(red: 0.969, green: 0.973, blue: 0.973) // #F7F8F8
    static let gmSurface2     = Color(red: 0.933, green: 0.941, blue: 0.941) // #EEF0F0
    static let gmDivider      = Color(red: 0.894, green: 0.902, blue: 0.902) // #E4E6E6

    // MARK: - Canvas & Surfaces (Dark)
    static let gmDarkCanvas   = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let gmDarkSurface1 = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let gmDarkSurface2 = Color(red: 0.149, green: 0.149, blue: 0.161) // #262629
    static let gmDarkDivider  = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E

    // MARK: - Text
    static let gmTextPrimary    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let gmTextSecondary  = Color(red: 0.420, green: 0.420, blue: 0.439) // #6B6B70
    static let gmTextTertiary   = Color(red: 0.604, green: 0.604, blue: 0.624) // #9A9A9F
    static let gmDarkTextPrimary = Color(red: 0.894, green: 0.894, blue: 0.894) // #E4E4E4
}
```

## 2. Typography

Grammarly's brand face is Apercu; on iOS the product uses SF Pro. Inter is the closest free face — bundle it via `Info.plist` / `UIAppFonts`, or drop the `.notion`-style helper and use `.system(...)` for an SF Pro build.

```swift
extension Font {
    static let gmScreenTitle  = Font.custom("Inter-ExtraBold", size: 32) // -0.6 tracking
    static let gmDocHeading   = Font.custom("Inter-Bold",      size: 26)
    static let gmDocSubhead   = Font.custom("Inter-Bold",      size: 22)
    static let gmCardTitle    = Font.custom("Inter-Bold",      size: 18)
    static let gmBody         = Font.custom("Inter-Regular",   size: 16) // line-height 1.65 via spacing
    static let gmSuggestTitle = Font.custom("Inter-SemiBold",  size: 15)
    static let gmSwap         = Font.custom("Inter-SemiBold",  size: 16)
    static let gmExplain      = Font.custom("Inter-Regular",   size: 14)
    static let gmAction       = Font.custom("Inter-SemiBold",  size: 13)
    static let gmCategoryTag  = Font.custom("Inter-Bold",      size: 12) // uppercase, 0.4 tracking
    static let gmTonePill     = Font.custom("Inter-SemiBold",  size: 12)
    static let gmScoreNum     = Font.custom("Inter-Bold",      size: 17)
    static let gmBarLabel     = Font.custom("Inter-SemiBold",  size: 11)
    static let gmButton       = Font.custom("Inter-Bold",      size: 16)
}

// Body uses generous line-height — apply via .lineSpacing
extension View {
    func gmBodyLeading() -> some View { self.lineSpacing(16 * 0.65) } // ~1.65
}
```

## 3. Signature Components

### Suggestion Category Model

```swift
enum SuggestionCategory: String {
    case correctness = "Correctness"
    case clarity     = "Clarity"
    case engagement  = "Engagement"
    case delivery    = "Delivery"

    var color: Color {
        switch self {
        case .correctness: return .gmCorrectness
        case .clarity:     return .gmClarity
        case .engagement:  return .gmEngagement
        case .delivery:    return .gmDelivery
        }
    }
}
```

### Suggestion Card (the core atom)

```swift
struct SuggestionCard: View {
    let category: SuggestionCategory
    let oldText: String
    let newText: String
    let explanation: String
    let onAccept: () -> Void
    let onDismiss: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 8) {
                Circle().fill(category.color).frame(width: 10, height: 10)
                Text(category.rawValue.uppercased())
                    .font(.gmCategoryTag)
                    .tracking(0.4)
                    .foregroundStyle(Color.gmTextSecondary)
            }
            .padding(.bottom, 10)

            HStack(spacing: 6) {
                Text(oldText)
                    .font(.gmSwap)
                    .strikethrough()
                    .foregroundStyle(category.color)
                Text("→").font(.gmSwap).foregroundStyle(Color.gmTextTertiary)
                Text(newText)
                    .font(.gmSwap)
                    .foregroundStyle(Color.gmGreen)
            }
            .padding(.bottom, 6)

            Text(explanation)
                .font(.gmExplain)
                .foregroundStyle(Color.gmTextSecondary)
                .lineLimit(2)
                .padding(.bottom, 14)

            HStack(spacing: 10) {
                Button(action: onAccept) {
                    Text("Accept")
                        .font(.gmAction)
                        .foregroundStyle(Color.gmGreenOnBtn)
                        .frame(maxWidth: .infinity)
                        .padding(.vertical, 10)
                        .background(Capsule().fill(Color.gmGreen))
                }
                .buttonStyle(.plain)

                Button(action: onDismiss) {
                    Image(systemName: "xmark")
                        .font(.system(size: 16, weight: .semibold))
                        .foregroundStyle(Color.gmTextSecondary)
                        .frame(width: 44, height: 40)
                        .overlay(Capsule().strokeBorder(Color.gmDarkDivider, lineWidth: 1))
                }
                .buttonStyle(.plain)
            }
        }
        .padding(16)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.gmDarkSurface1)
                .shadow(color: .black.opacity(0.5), radius: 24, y: 8)
        )
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.gmDarkDivider, lineWidth: 1))
        .padding(.horizontal, 16)
        .transition(.move(edge: .bottom).combined(with: .opacity))
    }
}
```

### Document Score Ring

```swift
struct ScoreRing: View {
    let score: Int          // 0...100
    var diameter: CGFloat = 38
    @State private var animated: CGFloat = 0

    private var tint: Color {
        score >= 90 ? .gmGreen : (score >= 60 ? .gmPremiumGold : .gmCorrectness)
    }

    var body: some View {
        ZStack {
            Circle().stroke(Color.gmDarkDivider, lineWidth: 3)
            Circle()
                .trim(from: 0, to: animated)
                .stroke(tint, style: StrokeStyle(lineWidth: 3, lineCap: .round))
                .rotationEffect(.degrees(-90))
            Text("\(score)")
                .font(diameter > 48 ? .gmScoreNum : .gmAction)
                .foregroundStyle(tint)
        }
        .frame(width: diameter, height: diameter)
        .onAppear { withAnimation(.easeOut(duration: 0.6)) { animated = CGFloat(score) / 100 } }
        .onChange(of: score) { _, v in withAnimation(.easeOut(duration: 0.4)) { animated = CGFloat(v) / 100 } }
    }
}
```

### Colored Underline Modifier

```swift
struct CategoryUnderline: ViewModifier {
    let category: SuggestionCategory

    func body(content: Content) -> some View {
        switch category {
        case .clarity:
            content
                .padding(.horizontal, 1)
                .background(category.color.opacity(0.18))
                .overlay(Rectangle().frame(height: 2).foregroundStyle(category.color), alignment: .bottom)
                .clipShape(RoundedRectangle(cornerRadius: 2))
        case .engagement:
            content.overlay(
                Rectangle().frame(height: 2)
                    .foregroundStyle(category.color)
                    .overlay(Color.clear) // dotted: use a DashedLine shape in production
                , alignment: .bottom)
        default: // correctness (wavy in production) / delivery
            content.overlay(Rectangle().frame(height: 2).foregroundStyle(category.color), alignment: .bottom)
        }
    }
}

extension View {
    func categoryUnderline(_ c: SuggestionCategory) -> some View { modifier(CategoryUnderline(category: c)) }
}
```

### Tone Detector Row

```swift
struct ToneRow: View {
    let emoji: String        // 😊 🙂 😐 😟
    let tones: [String]      // ["Confident", "Formal"]

    var body: some View {
        HStack(spacing: 8) {
            Text(emoji).font(.system(size: 18))
            Text("Tone sounds").font(.gmTonePill).foregroundStyle(Color.gmTextSecondary)
            ForEach(tones, id: \.self) { t in
                Text(t)
                    .font(.gmTonePill)
                    .foregroundStyle(Color.gmDarkTextPrimary)
                    .padding(.vertical, 5).padding(.horizontal, 12)
                    .background(Capsule().fill(Color.gmDarkSurface2))
                    .overlay(Capsule().strokeBorder(Color.gmDarkDivider, lineWidth: 1))
            }
            Spacer()
        }
        .padding(.horizontal, 20).padding(.vertical, 10)
    }
}
```

### Assistant Bar

```swift
struct AssistantBar: View {
    let total: Int
    let breakdown: String   // "3 Correctness · 6 Clarity · 5 Engagement"
    let onReview: () -> Void

    var body: some View {
        HStack(spacing: 14) {
            ZStack {
                Circle().fill(Color.gmGreen).frame(width: 36, height: 36)
                Image(systemName: "g.circle.fill") // stand-in for rotated-G logomark
                    .font(.system(size: 20))
                    .foregroundStyle(.white)
            }
            VStack(alignment: .leading, spacing: 1) {
                Text(total == 0 ? "All clear" : "\(total) suggestions")
                    .font(.gmBarLabel).fontWeight(.bold)
                    .foregroundStyle(Color.gmDarkTextPrimary)
                if total > 0 {
                    Text(breakdown).font(.gmBarLabel).foregroundStyle(Color.gmTextTertiary)
                }
            }
            Spacer()
            if total > 0 {
                Button(action: onReview) {
                    Text("Review")
                        .font(.gmAction)
                        .foregroundStyle(Color.gmGreenOnBtn)
                        .padding(.vertical, 9).padding(.horizontal, 16)
                        .background(Capsule().fill(Color.gmGreen))
                }
                .buttonStyle(.plain)
            }
        }
        .padding(.horizontal, 18)
        .frame(height: 64)
        .background(.ultraThinMaterial)
        .overlay(Rectangle().frame(height: 0.5).foregroundStyle(Color.gmDarkDivider), alignment: .top)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct GrammarlyTabView: View {
    var body: some View {
        TabView {
            DocumentsView().tabItem { Label("Documents", systemImage: "doc.text") }
            AssistantView().tabItem { Label("Assistant", systemImage: "g.circle.fill") }
            AccountView().tabItem { Label("Account", systemImage: "person.circle") }
        }
        .tint(.gmGreen) // active icon + label go green; no pill indicator
    }
}
```

## 5. Motion

```swift
// Suggestion card present / dismiss
withAnimation(.spring(response: 0.4, dampingFraction: 0.85)) { activeCard = card }   // present (~280ms)
withAnimation(.easeIn(duration: 0.2)) { activeCard = nil }                            // dismiss

// Accept: crossfade old→new text, flash green wash
withAnimation(.easeInOut(duration: 0.2)) { spanText = newText }
withAnimation(.easeOut(duration: 0.6)) { acceptFlash = false } // start true, fade to false

// Score ring fill
.onAppear { withAnimation(.easeOut(duration: 0.6)) { animated = target } }
.onChange(of: score) { withAnimation(.easeOut(duration: 0.4)) { animated = target } }

// Haptics
let soft = UIImpactFeedbackGenerator(style: .soft); soft.impactOccurred()  // Accept
let tick = UIImpactFeedbackGenerator(style: .light); tick.impactOccurred() // score-ring complete
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Documents (tab) | `doc.text` / `doc.text.fill` | 24pt |
| Assistant (tab / orb) | `g.circle.fill` (stand-in for logomark) | 24pt |
| Account (tab) | `person.circle` / `person.circle.fill` | 24pt |
| Back | `chevron.left` | 22pt |
| Dismiss suggestion | `xmark` | 16pt |
| Premium lock | `lock.fill` | 14pt |
| Search | `magnifyingglass` | 16pt |
| Share | `square.and.arrow.up` | 22pt |
| Settings | `gearshape` | 22pt |
| New document | `plus` | 20pt |
| Tone sentiment | use emoji glyphs (😊🙂😐😟), not SF Symbols | 18pt |
| Score ring | drawn with `Circle().trim` | — |
| Insights productivity | `chart.bar.fill` | 18pt |
| Plagiarism flag | `exclamationmark.triangle.fill` | 16pt |

## 7. Dark Mode

```swift
struct GrammarlyTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.gmDarkCanvas : Color.gmCanvas)
            .foregroundStyle(scheme == .dark ? Color.gmDarkTextPrimary : Color.gmTextPrimary)
    }
}
extension View { func grammarlyTheme() -> some View { modifier(GrammarlyTheme()) } }
```

In dark mode the category underline hues brighten slightly (`#F05A5F` / `#5A95F8` / `#22B85A` / `#A07CF8`) so they read under body text; Grammarly Green is unchanged. Floating cards add a 1pt `gmDarkDivider` border because the deepened shadow alone is hard to read on `#121212`.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` as shown)
- Bundle Inter TTFs via `Info.plist` (SIL OFL — free to distribute) or build an SF-Pro variant with `.system(...)`; the brand face Apercu is licensed and should not be redistributed
- Dynamic Type: scale document heading, editor body, suggestion title, explanation; keep category tags, tone pills, score number, bar/tab labels FIXED
- VoiceOver: announce a marked span as "{category} suggestion: replace {old} with {new}. {explanation}"; the card's Accept/Dismiss are first-class buttons; expose "Accept suggestion" / "Dismiss suggestion" as custom actions on the span itself so users need not open the card
- The score ring needs an accessibility label: "Document score {n} out of 100, {good/fair/needs work}"
- Color is never the only signal — each category also has a distinct underline *style* (wavy / highlight / dotted / solid) and a text label in the card, so color-blind users can still differentiate
- Contrast: `#1A1A1A` on `#FFFFFF` and `#E4E4E4` on `#121212` pass WCAG AA for 16pt body; `#06281F` on `#15C39A` passes AA for button text
- Reduce Motion: replace the card slide with a crossfade, disable the score-ring sweep (snap to value), keep the green accept-flash (it conveys state) but shorten to 200ms
- Dark mode: invert via the `gmDark*` palette — warm charcoal `#121212`, NOT true black; body text `#E4E4E4`
