# Apple Wallet (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Wallet's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, themed components, and the signature card-stack animation.

Targets iOS 17+. The modern `.sensoryFeedback`, `matchedGeometryEffect` for card expand, and `Material` blur all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let walletCanvas      = Color(red: 0.00, green: 0.00, blue: 0.00)     // #000000 true black
    static let walletSurface1    = Color(red: 0.110, green: 0.110, blue: 0.118)  // #1C1C1E
    static let walletSurface2    = Color(red: 0.173, green: 0.173, blue: 0.180)  // #2C2C2E
    static let walletGlass       = Color.white.opacity(0.12)                     // rgba(255,255,255,0.12)
    static let walletHairline    = Color(red: 0.149, green: 0.149, blue: 0.161)  // #262629

    // MARK: - Text
    static let walletTextPrimary   = Color(red: 1.00, green: 1.00, blue: 1.00)   // #FFFFFF
    static let walletTextSecondary = Color(red: 0.627, green: 0.627, blue: 0.647) // #A0A0A5
    static let walletTextTertiary  = Color(red: 0.388, green: 0.388, blue: 0.408) // #636368

    // MARK: - Apple Card titanium
    static let walletTitaniumHi  = Color(red: 0.910, green: 0.910, blue: 0.922)  // #E8E8EB
    static let walletTitaniumMid = Color(red: 0.659, green: 0.659, blue: 0.678)  // #A8A8AD
    static let walletTitaniumLo  = Color(red: 0.239, green: 0.239, blue: 0.247)  // #3D3D3F
    static let walletChipGold    = Color(red: 0.780, green: 0.675, blue: 0.451)  // #C7AC73
    static let walletDailyCash   = Color(red: 1.00, green: 0.231, blue: 0.188)   // #FF3B30

    // MARK: - Semantic (HIG dark mode tints)
    static let walletSystemBlue  = Color(red: 0.039, green: 0.518, blue: 1.00)   // #0A84FF
    static let walletSuccess     = Color(red: 0.188, green: 0.820, blue: 0.345)  // #30D158
    static let walletWarning     = Color(red: 1.00, green: 0.624, blue: 0.039)   // #FF9F0A
    static let walletError       = Color(red: 1.00, green: 0.271, blue: 0.227)   // #FF453A

    // MARK: - Common card brand hints
    static let walletChaseBlue   = Color(red: 0.102, green: 0.176, blue: 0.310)  // #1A2D4F
    static let walletAmexSilver  = Color(red: 0.655, green: 0.690, blue: 0.718)  // #A7B0B7
    static let walletVisaNavy    = Color(red: 0.102, green: 0.122, blue: 0.443)  // #1A1F71
}
```

## 2. Typography

```swift
extension Font {
    // SF Pro Display — titles ≥20pt
    static let walletTitle       = Font.system(size: 34, weight: .bold,    design: .default)  // "Wallet"
    static let walletSheetTitle  = Font.system(size: 28, weight: .bold,    design: .default)
    static let walletCardIssuer  = Font.system(size: 17, weight: .semibold, design: .default)  // card face
    static let walletBalanceHero = Font.system(size: 40, weight: .bold,    design: .default).monospacedDigit()
    static let walletDailyCash   = Font.system(size: 22, weight: .bold,    design: .default).monospacedDigit()

    // SF Pro Text — body ≤17pt
    static let walletBody        = Font.system(size: 17, weight: .regular, design: .default)
    static let walletBodyMedium  = Font.system(size: 17, weight: .medium,  design: .default).monospacedDigit()
    static let walletAction      = Font.system(size: 17, weight: .semibold, design: .default)
    static let walletSectionHdr  = Font.system(size: 13, weight: .semibold, design: .default)  // UPPERCASE
    static let walletFootnote    = Font.system(size: 13, weight: .regular, design: .default)
    static let walletCaption     = Font.system(size: 11, weight: .regular, design: .default)

    // Monospace — card face last4 only
    static let walletCardLast4   = Font.system(size: 17, weight: .medium,  design: .monospaced)
    static let walletCardHolder  = Font.system(size: 13, weight: .medium,  design: .default)
}
```

## 3. Signature Components

### Apple Card (the signature object)

```swift
struct AppleCardFace: View {
    let cardholder: String
    let dailyCashBalance: String

    var body: some View {
        ZStack {
            // Titanium gradient
            LinearGradient(
                colors: [.walletTitaniumHi, .walletTitaniumMid, .walletTitaniumLo],
                startPoint: .topLeading,
                endPoint: .bottomTrailing
            )

            // Subtle brushed-metal noise
            Rectangle()
                .fill(.ultraThinMaterial)
                .opacity(0.06)
                .blendMode(.overlay)

            VStack(alignment: .leading) {
                HStack(alignment: .top) {
                    // Chip
                    RoundedRectangle(cornerRadius: 4)
                        .fill(Color.walletChipGold)
                        .frame(width: 26, height: 20)
                    Spacer()
                    // Apple logo
                    Image(systemName: "applelogo")
                        .font(.system(size: 24, weight: .regular))
                        .foregroundStyle(.white)
                }
                Spacer()
                HStack(alignment: .bottom) {
                    Text(cardholder.uppercased())
                        .font(.walletCardHolder)
                        .foregroundStyle(Color(red: 0.10, green: 0.10, blue: 0.10))
                    Spacer()
                    ZStack {
                        Circle().fill(Color.walletDailyCash)
                        Text("$").foregroundStyle(.white).font(.system(size: 16, weight: .bold))
                    }
                    .frame(width: 28, height: 28)
                }
            }
            .padding(16)
        }
        .frame(height: 220)
        .clipShape(RoundedRectangle(cornerRadius: 10))
        .overlay(
            RoundedRectangle(cornerRadius: 10)
                .strokeBorder(Color.white.opacity(0.08), lineWidth: 0.5)
        )
        .shadow(color: .black.opacity(0.5), radius: 12, y: 8)
    }
}
```

### Standard Credit/Debit Card

```swift
struct CreditCardFace: View {
    let issuer: String
    let last4: String
    let cardholder: String
    let networkLogo: String       // e.g. "visa" image asset
    let background: LinearGradient

    var body: some View {
        ZStack {
            background

            VStack(alignment: .leading) {
                HStack {
                    Text(issuer)
                        .font(.walletCardIssuer)
                        .foregroundStyle(.white)
                    Spacer()
                    Image(networkLogo).resizable().scaledToFit().frame(height: 22)
                }
                Spacer()
                HStack {
                    Text(cardholder.uppercased())
                        .font(.walletCardHolder)
                        .foregroundStyle(.white.opacity(0.9))
                    Spacer()
                    Text("•••• \(last4)")
                        .font(.walletCardLast4)
                        .foregroundStyle(.white)
                        .tracking(0.5)
                }
            }
            .padding(16)
        }
        .frame(height: 220)
        .clipShape(RoundedRectangle(cornerRadius: 10))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.white.opacity(0.08), lineWidth: 0.5))
        .shadow(color: .black.opacity(0.5), radius: 12, y: 8)
    }
}
```

### Card Stack (the home screen)

```swift
struct CardStack<Card: View>: View {
    let cards: [Card]
    @State private var expandedIndex: Int? = nil
    @Namespace private var ns

    var body: some View {
        ScrollView {
            ZStack(alignment: .top) {
                ForEach(Array(cards.enumerated()), id: \.offset) { idx, card in
                    if expandedIndex == nil || expandedIndex == idx {
                        card
                            .matchedGeometryEffect(id: "card-\(idx)", in: ns)
                            .offset(y: expandedIndex == nil ? CGFloat(idx) * 80 : 0)
                            .onTapGesture {
                                withAnimation(.spring(response: 0.5, dampingFraction: 0.8)) {
                                    expandedIndex = expandedIndex == nil ? idx : nil
                                }
                            }
                    }
                }
            }
            .padding(.horizontal, 16)
        }
        .background(Color.walletCanvas.ignoresSafeArea())
        .sensoryFeedback(.impact(weight: .medium), trigger: expandedIndex)
    }
}
```

### Wallet Home (title + add button + stack)

```swift
struct WalletHome: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text("Wallet")
                    .font(.walletTitle)
                    .foregroundStyle(.white)
                Spacer()
                Button {} label: {
                    Image(systemName: "plus")
                        .font(.system(size: 16, weight: .bold))
                        .foregroundStyle(.white)
                        .frame(width: 32, height: 32)
                        .background(Circle().fill(Color.walletGlass))
                }
            }
            .padding(.horizontal, 16)
            .padding(.top, 8)

            CardStack(cards: /* array of card views */)
        }
        .background(Color.walletCanvas.ignoresSafeArea())
    }
}
```

### Transaction Row

```swift
struct TransactionRow: View {
    let merchant: String
    let date: String
    let amount: String
    let dailyCash: String?

    var body: some View {
        HStack(alignment: .center, spacing: 12) {
            Circle()
                .fill(Color.walletSurface2)
                .frame(width: 32, height: 32)
                .overlay(Image(systemName: "cart.fill").foregroundStyle(.white).font(.system(size: 14)))
            VStack(alignment: .leading, spacing: 2) {
                Text(merchant).font(.walletBody).foregroundStyle(.white)
                Text(date).font(.walletFootnote).foregroundStyle(.walletTextSecondary)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 2) {
                Text(amount).font(.walletBodyMedium).foregroundStyle(.white)
                if let dc = dailyCash {
                    Text("Daily Cash \(dc)")
                        .font(.walletCaption)
                        .foregroundStyle(.walletDailyCash)
                }
            }
        }
        .padding(.vertical, 12)
        .padding(.horizontal, 16)
        .background(Color.walletSurface1)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.walletHairline).frame(height: 0.5).padding(.leading, 16)
        }
    }
}
```

### Action Toolbar (bottom of an expanded card)

```swift
struct ActionToolbar: View {
    var body: some View {
        HStack(spacing: 24) {
            ToolbarButton(icon: "creditcard.fill")
            ToolbarButton(icon: "list.bullet.rectangle")
            ToolbarButton(icon: "ellipsis")
        }
        .padding(.vertical, 12)
        .padding(.horizontal, 16)
        .frame(maxWidth: .infinity)
        .background(.regularMaterial)
        .overlay(alignment: .top) {
            Rectangle().fill(Color.walletHairline).frame(height: 0.5)
        }
    }
}

struct ToolbarButton: View {
    let icon: String
    var body: some View {
        Button {} label: {
            Image(systemName: icon)
                .font(.system(size: 18, weight: .regular))
                .foregroundStyle(.white)
                .frame(width: 44, height: 44)
                .background(Circle().fill(Color.walletGlass))
        }
        .sensoryFeedback(.selection, trigger: UUID())
    }
}
```

### Pay Now Button (Apple Card payment flow)

```swift
struct PayNowButton: View {
    var onPayNow: () -> Void
    var body: some View {
        Button(action: onPayNow) {
            Text("Pay Now")
                .font(.walletAction)
                .foregroundStyle(.black)
                .frame(maxWidth: .infinity)
                .frame(height: 54)
                .background(RoundedRectangle(cornerRadius: 14).fill(.white))
        }
        .padding(.horizontal, 16)
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}
```

## 4. Apple Pay Invocation

```swift
struct ApplePayOverlay: View {
    @State private var authenticated = false
    var body: some View {
        ZStack {
            Color.walletCanvas.ignoresSafeArea()
            VStack(spacing: 32) {
                // The card slammed to center (use matchedGeometryEffect from origin)
                Image("appleCardFace") // placeholder
                    .resizable().scaledToFit()
                    .frame(maxWidth: 358)
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                    .shadow(color: .black.opacity(0.5), radius: 24, y: 12)

                if !authenticated {
                    VStack(spacing: 12) {
                        Image(systemName: "faceid")
                            .font(.system(size: 64))
                            .foregroundStyle(.white)
                        Text("Use Face ID to confirm")
                            .font(.walletBody)
                            .foregroundStyle(.white)
                    }
                } else {
                    Image(systemName: "checkmark.circle.fill")
                        .font(.system(size: 64))
                        .foregroundStyle(Color.walletSuccess)
                        .transition(.scale.combined(with: .opacity))
                }

                Spacer()
                Text("Hold near reader")
                    .font(.walletSheetTitle)
                    .foregroundStyle(.white)
                    .padding(.bottom, 48)
            }
            .padding(.top, 64)
        }
        .sensoryFeedback(.success, trigger: authenticated)
    }
}
```

## 5. Motion & Haptics

```swift
// Card tap → expand
withAnimation(.spring(response: 0.5, dampingFraction: 0.8)) {
    expandedIndex = idx
}
.sensoryFeedback(.impact(weight: .medium), trigger: expandedIndex)

// Card collapse on drag-down
.sensoryFeedback(.impact(flexibility: .soft), trigger: dismissed)

// Apple Pay success
.sensoryFeedback(.success, trigger: authenticated)

// Stack scroll detents
.sensoryFeedback(.selection, trigger: visibleCardIndex)

// Card stack offsets (declarative)
.offset(y: expandedIndex == nil ? CGFloat(idx) * 80 : 0)

// Toolbar button tap
.sensoryFeedback(.selection, trigger: tappedAction)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Add card "+" | `plus` | 16pt bold |
| Apple logo | `applelogo` | 24pt |
| Card details | `creditcard.fill` | 18pt |
| Transactions list | `list.bullet.rectangle` | 18pt |
| More menu | `ellipsis` | 18pt |
| Disclosure | `chevron.right` | 13pt |
| Apple Pay confirmation | `faceid` | 64pt |
| Success check | `checkmark.circle.fill` | 64pt |
| Transit NFC waves | `wave.3.right` | 24pt |
| Merchant default | `cart.fill` / `bag.fill` | 14pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `Material`, and `matchedGeometryEffect`); iOS 16 fallback uses `UIImpactFeedbackGenerator` and `UIVisualEffectView`
- Use the real `PKAddPassButton` and `PKPaymentButton` from PassKit for Apple Pay flows — never recreate them custom
- VoiceOver: each card on the stack is its own accessibility element with label `"Apple Card, ending in 4521, $1,284.20 balance, expanded"` / `"Apple Card, ending in 4521, collapsed"`
- Contrast: white text on titanium gradient meets WCAG AA at the top-left highlight but borderline at the bottom-right shadow zone — apply a 30% black overlay behind any text positioned over the dark end of the gradient for safety
- Reduce Motion: skip the spring bounce on card expand — fall back to a 0.25s linear cross-fade instead
- Dark mode: Wallet IS dark mode; it does not respect Light Mode — even when the system is set to Light, Wallet stays on its `#000000` canvas because the cards depend on it
- Dynamic Type: Wallet title and detail body scale up to xxxLarge; card-face typography is FIXED because the card is art, not text
- Tabular numerals: enforce `.monospacedDigit()` on every balance, last4, transaction amount — dollar columns must align
