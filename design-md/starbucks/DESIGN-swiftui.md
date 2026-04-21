# Starbucks (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Starbucks' visual language into paste-ready SwiftUI code: `Color` / `Font` extensions, the Stars progress ring, size selector, category card, and the drink-builder customization rows.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas (Light)
    static let sbxCanvas        = Color(red: 0.976, green: 0.976, blue: 0.976) // #F9F9F9
    static let sbxCardSurface   = Color.white                                   // #FFFFFF
    static let sbxSurfaceMuted  = Color(red: 0.945, green: 0.945, blue: 0.945) // #F1F1F1
    static let sbxDivider       = Color(red: 0.910, green: 0.910, blue: 0.910) // #E8E8E8

    // MARK: - Text
    static let sbxTextPrimary   = Color.black                                   // #000000
    static let sbxTextSecondary = Color(red: 0.349, green: 0.349, blue: 0.349) // #595959
    static let sbxTextTertiary  = Color(red: 0.557, green: 0.557, blue: 0.557) // #8E8E8E

    // MARK: - Brand
    static let sbxSirenGreen    = Color(red: 0.000, green: 0.439, blue: 0.290) // #00704A
    static let sbxGreenPressed  = Color(red: 0.000, green: 0.337, blue: 0.227) // #00563A
    static let sbxHouseGreen    = Color(red: 0.118, green: 0.224, blue: 0.196) // #1E3932
    static let sbxMint          = Color(red: 0.831, green: 0.914, blue: 0.886) // #D4E9E2

    // MARK: - Gold (Rewards)
    static let sbxGoldStar      = Color(red: 0.796, green: 0.635, blue: 0.345) // #CBA258
    static let sbxGoldAlt       = Color(red: 0.757, green: 0.635, blue: 0.396) // #C1A265
    static let sbxGoldLight     = Color(red: 0.894, green: 0.784, blue: 0.588) // #E4C896

    // MARK: - Semantic
    static let sbxSuccessGreen  = Color(red: 0.000, green: 0.459, blue: 0.290) // #00754A
    static let sbxAlertRed      = Color(red: 0.867, green: 0.200, blue: 0.200) // #DD3333
    static let sbxInfoBlue      = Color(red: 0.000, green: 0.435, blue: 0.812) // #006FCF
    static let sbxWarningAmber  = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623

    // MARK: - Dark
    static let sbxDarkCanvas    = Color(red: 0.118, green: 0.224, blue: 0.196) // #1E3932 (House Green)
    static let sbxDarkSurface1  = Color(red: 0.176, green: 0.290, blue: 0.263) // #2D4A43
    static let sbxDarkSurface2  = Color(red: 0.231, green: 0.345, blue: 0.322) // #3B5852
}

extension LinearGradient {
    static let sbxGoldRing = LinearGradient(
        colors: [.sbxGoldStar, .sbxGoldLight, .sbxGoldStar],
        startPoint: .leading, endPoint: .trailing
    )
}
```

## 2. Typography

SoDo Sans is proprietary (licensed from Dalton Maag). Fall back to Lato or SF Pro.

```swift
extension Font {
    static let sbxStarsHero    = Font.custom("SoDoSans-ExtraBold", size: 48).weight(.heavy)
    static let sbxScreenTitle  = Font.custom("SoDoSans-Bold",      size: 28).weight(.bold)
    static let sbxSheetTitle   = Font.custom("SoDoSans-Bold",      size: 22).weight(.bold)
    static let sbxSection      = Font.custom("SoDoSans-Bold",      size: 18).weight(.bold)
    static let sbxCardTitle    = Font.custom("SoDoSans-Semibold",  size: 16).weight(.semibold)
    static let sbxDrinkTitle   = Font.custom("SoDoSans-Bold",      size: 22).weight(.bold)
    static let sbxBody         = Font.custom("SoDoSans-Regular",   size: 15).weight(.regular)
    static let sbxMeta         = Font.custom("SoDoSans-Regular",   size: 13).weight(.regular)
    static let sbxPrice        = Font.custom("SoDoSans-Bold",      size: 17).weight(.bold)
    static let sbxStarsInline  = Font.custom("SoDoSans-ExtraBold", size: 22).weight(.heavy)
    static let sbxButton       = Font.custom("SoDoSans-Bold",      size: 16).weight(.bold)
    static let sbxButtonSmall  = Font.custom("SoDoSans-Semibold",  size: 15).weight(.semibold)
    static let sbxSize         = Font.custom("SoDoSans-Semibold",  size: 13).weight(.semibold)
    static let sbxTab          = Font.custom("SoDoSans-Semibold",  size: 11).weight(.semibold)
    static let sbxChip         = Font.custom("SoDoSans-Bold",      size: 12).weight(.bold)
}
```

## 3. Signature Components

### Stars Progress Ring

```swift
struct StarbucksStarsRing: View {
    let currentStars: Int
    let targetStars: Int
    @State private var animatedProgress: CGFloat = 0

    var targetProgress: CGFloat {
        CGFloat(currentStars) / CGFloat(targetStars)
    }

    var body: some View {
        VStack(spacing: 16) {
            ZStack {
                Circle()
                    .stroke(Color.sbxDivider, lineWidth: 12)
                    .frame(width: 180, height: 180)
                Circle()
                    .trim(from: 0, to: animatedProgress)
                    .stroke(
                        LinearGradient.sbxGoldRing,
                        style: StrokeStyle(lineWidth: 12, lineCap: .round)
                    )
                    .frame(width: 180, height: 180)
                    .rotationEffect(.degrees(-90))
                VStack(spacing: 4) {
                    Text("\(currentStars)")
                        .font(.sbxStarsHero)
                        .foregroundStyle(.sbxGoldStar)
                        .monospacedDigit()
                        .tracking(-0.5)
                    Text("Stars")
                        .font(.sbxMeta.weight(.semibold))
                        .foregroundStyle(.sbxTextSecondary)
                }
            }
            Text("\(targetStars - currentStars) Stars until free reward")
                .font(.sbxBody)
                .foregroundStyle(.sbxTextSecondary)
        }
        .onAppear {
            withAnimation(.easeOut(duration: 1.5)) {
                animatedProgress = targetProgress
            }
        }
    }
}
```

### Size Selector (Short / Tall / Grande / Venti / Trenta)

```swift
enum CoffeeSize: String, CaseIterable, Identifiable {
    case short = "Short", tall = "Tall", grande = "Grande", venti = "Venti", trenta = "Trenta"
    var id: String { rawValue }
    var cupScale: CGFloat {
        switch self {
        case .short:  return 0.55
        case .tall:   return 0.7
        case .grande: return 0.85
        case .venti:  return 1.0
        case .trenta: return 1.15
        }
    }
}

struct StarbucksSizeSelector: View {
    @Binding var selected: CoffeeSize

    var body: some View {
        HStack(spacing: 8) {
            ForEach(CoffeeSize.allCases) { size in
                SizePill(
                    size: size,
                    isSelected: selected == size,
                    onTap: {
                        withAnimation(.easeOut(duration: 0.25)) { selected = size }
                    }
                )
            }
        }
    }
}

private struct SizePill: View {
    let size: CoffeeSize
    let isSelected: Bool
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            VStack(spacing: 4) {
                Image(systemName: "cup.and.saucer.fill")
                    .font(.system(size: 20 * size.cupScale))
                Text(size.rawValue)
                    .font(.sbxSize)
                    .tracking(0.2)
            }
            .foregroundStyle(isSelected ? .white : .sbxTextPrimary)
            .padding(.horizontal, 14)
            .frame(minHeight: 36)
            .background(
                Capsule()
                    .fill(isSelected ? Color.sbxSirenGreen : Color.clear)
                    .overlay(
                        Capsule()
                            .strokeBorder(
                                isSelected ? Color.clear : Color.sbxDivider,
                                lineWidth: 1.5
                            )
                    )
            )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.selection, trigger: isSelected)
    }
}
```

### Primary CTA (Siren Green Pill)

```swift
struct StarbucksPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.sbxButton)
                .foregroundStyle(.white)
                .tracking(0.2)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(
                    Capsule().fill(pressed ? Color.sbxGreenPressed : Color.sbxSirenGreen)
                )
                .shadow(color: Color.sbxSirenGreen.opacity(0.2), radius: 12, y: 4)
        }
        .buttonStyle(.plain)
        .scaleEffect(pressed ? 0.98 : 1)
        .animation(.spring(response: 0.2, dampingFraction: 0.8), value: pressed)
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded   { _ in pressed = false }
        )
    }
}
```

### Category Card (Order Grid)

```swift
struct StarbucksCategoryCard: View {
    let title: String
    let imageUrl: URL?
    let onTap: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: onTap) {
            ZStack(alignment: .bottomLeading) {
                AsyncImage(url: imageUrl) { img in
                    img.resizable().scaledToFill()
                } placeholder: {
                    Color.sbxSurfaceMuted
                }

                LinearGradient(
                    colors: [.clear, .black.opacity(0.5)],
                    startPoint: UnitPoint(x: 0.5, y: 0.5),
                    endPoint: .bottom
                )

                Text(title)
                    .font(.sbxCardTitle)
                    .foregroundStyle(.white)
                    .padding(16)
            }
            .aspectRatio(2/3, contentMode: .fit)
            .clipShape(RoundedRectangle(cornerRadius: 16))
            .shadow(color: .black.opacity(0.08), radius: 8, y: 2)
        }
        .buttonStyle(.plain)
        .scaleEffect(pressed ? 0.98 : 1)
        .animation(.spring(response: 0.2, dampingFraction: 0.8), value: pressed)
        .sensoryFeedback(.impact(weight: .light), trigger: pressed)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded   { _ in pressed = false }
        )
    }
}
```

### Syrup Pump Stepper

```swift
struct StarbucksPumpStepper: View {
    let label: String
    @Binding var count: Int

    var body: some View {
        HStack {
            Text(label)
                .font(.sbxButtonSmall)
                .foregroundStyle(.sbxTextPrimary)
            Spacer()
            HStack(spacing: 0) {
                StepButton(symbol: "minus") { if count > 0 { count -= 1 } }
                Text("\(count)")
                    .font(.sbxPrice)
                    .foregroundStyle(.sbxTextPrimary)
                    .frame(minWidth: 44)
                    .monospacedDigit()
                StepButton(symbol: "plus") { count += 1 }
            }
        }
        .padding(.vertical, 12)
        .overlay(Rectangle().frame(height: 1).foregroundStyle(.sbxDivider), alignment: .bottom)
    }
}

private struct StepButton: View {
    let symbol: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Image(systemName: symbol)
                .font(.system(size: 14, weight: .bold))
                .foregroundStyle(.sbxTextPrimary)
                .frame(width: 36, height: 36)
                .background(
                    Circle()
                        .strokeBorder(Color.sbxDivider, lineWidth: 1.5)
                )
        }
        .scaleEffect(pressed ? 0.9 : 1)
        .animation(.spring(response: 0.2), value: pressed)
        .sensoryFeedback(.impact(weight: .light), trigger: pressed)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded   { _ in pressed = false }
        )
    }
}
```

### Scan to Pay Barcode Card

```swift
struct StarbucksScanPayCard: View {
    let stars: Int
    let balance: Double
    let barcodeNumber: String  // "6241 1234 5678 9012"

    var body: some View {
        VStack(spacing: 20) {
            // Stars summary
            HStack {
                Image(systemName: "star.fill")
                    .foregroundStyle(.sbxGoldStar)
                Text("\(stars) Stars")
                    .font(.sbxStarsInline)
                    .foregroundStyle(.sbxGoldStar)
                    .monospacedDigit()
                Spacer()
                Text("Gold Member")
                    .font(.sbxChip)
                    .foregroundStyle(.sbxGoldStar)
                    .tracking(0.3)
            }
            .padding(16)
            .background(
                RoundedRectangle(cornerRadius: 16)
                    .fill(LinearGradient.sbxGoldRing.opacity(0.15))
            )

            // Barcode (placeholder — use a barcode generator in real implementation)
            VStack(spacing: 12) {
                Rectangle()
                    .fill(.black)
                    .mask(barcodeMask)  // render actual Code128 barcode
                    .frame(height: 80)
                Text(barcodeNumber)
                    .font(.sbxMeta)
                    .foregroundStyle(.sbxTextPrimary)
                    .monospacedDigit()
                    .tracking(2)
            }
            .padding(20)
            .background(RoundedRectangle(cornerRadius: 20).fill(Color.sbxCardSurface))

            // Balance + Reload
            HStack {
                VStack(alignment: .leading, spacing: 4) {
                    Text("Balance")
                        .font(.sbxMeta)
                        .foregroundStyle(.sbxTextSecondary)
                    Text("$\(balance, specifier: "%.2f")")
                        .font(.sbxDrinkTitle)
                        .foregroundStyle(.sbxTextPrimary)
                        .monospacedDigit()
                }
                Spacer()
                StarbucksPrimaryButton(title: "Reload") { /* reload */ }
                    .fixedSize()
            }
            .padding(.horizontal, 16)
        }
        .padding(.horizontal, 16)
    }

    // Placeholder mask — swap for CIFilter-generated Code128
    var barcodeMask: some View {
        HStack(spacing: 1) {
            ForEach(0..<60, id: \.self) { i in
                Rectangle().frame(width: CGFloat(Int.random(in: 1...3)))
            }
        }
    }
}
```

## 4. Tab Bar

```swift
struct StarbucksRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(red: 0.910, green: 0.910, blue: 0.910, alpha: 1)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem  { Label("Home",   systemImage: "house.fill") }
            ScanView().tabItem  { Label("Scan",   systemImage: "qrcode") }
            OrderView().tabItem { Label("Order",  systemImage: "cup.and.saucer.fill") }
            GiftView().tabItem  { Label("Gift",   systemImage: "gift.fill") }
            OffersView().tabItem { Label("Offers", systemImage: "tag.fill") }
        }
        .tint(Color.sbxSirenGreen)
    }
}
```

## 5. Motion Specs

```swift
// Stars ring fill
withAnimation(.easeOut(duration: 1.5)) { animatedProgress = targetProgress }

// Size pill slide — animate selection change with easeOut 250ms
withAnimation(.easeOut(duration: 0.25)) { selected = newSize }

// Category card tap
.scaleEffect(pressed ? 0.98 : 1)
.animation(.spring(response: 0.2, dampingFraction: 0.8), value: pressed)

// Primary CTA press
.scaleEffect(pressed ? 0.98 : 1)
.animation(.spring(response: 0.2, dampingFraction: 0.8), value: pressed)
.sensoryFeedback(.impact(weight: .medium), trigger: pressed)

// Stars earned (tick-up animation)
for i in 0..<starsEarned {
    DispatchQueue.main.asyncAfter(deadline: .now() + Double(i) * 0.08) {
        currentStars += 1
    }
}

// Order placed checkmark
withAnimation(.spring(response: 0.4, dampingFraction: 0.55)) { checkScale = 1.0 }
.sensoryFeedback(.success, trigger: orderPlaced)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Cup / size icon | `cup.and.saucer.fill` | 20pt |
| Star (rewards) | `star.fill` | 16-20pt |
| QR / Scan | `qrcode` | 24pt |
| Plus / Minus (stepper) | `plus` / `minus` | 14pt |
| Cart | `cart.fill` | 24pt |
| Store / pin | `mappin.circle.fill` | 24pt |
| Home (tab) | `house.fill` | 24pt |
| Order (tab) | `cup.and.saucer.fill` | 24pt |
| Gift (tab) | `gift.fill` | 24pt |
| Offers (tab) | `tag.fill` | 24pt |
| Checkmark (order placed) | `checkmark.circle.fill` | 48pt |
| Info | `info.circle` | 20pt |
| Clock (ready in) | `clock.fill` | 16pt |

## 7. Accessibility Notes

- Minimum target: iOS 16 (iOS 17 for `.sensoryFeedback`)
- **VoiceOver**:
  - Stars ring: `accessibilityLabel("47 Stars earned, 75 more Stars until free reward")`
  - Size selector: each pill announces "[Size] selected" / "[Size], double-tap to select"
  - Barcode: announce balance and that the barcode is being displayed for scanning
- **Dynamic Type**: Drink names, body, meta scale freely; Stars count fixed at 48pt; size selector fixed at 13pt; tab labels fixed at 11pt
- **Contrast**:
  - Gold Stars (`#CBA258`) on Warm White (`#F9F9F9`) — passes AA at 22pt+ weight 800; for 14pt use a darker gold alt
  - Text secondary `#595959` on warm white passes WCAG AA at 13pt+
- **Reduce Motion**: Skip the 1.5s Stars ring fill animation — render at target progress immediately; skip the size-pill slide animation
- **Dark mode**: Canvas becomes `#1E3932` (House Green, NOT black); surfaces lift to `#2D4A43` and `#3B5852`
- **Haptics**:
  - `.selection` on size-pill tap
  - `.impact(.light)` on stepper +/− and category card
  - `.impact(.medium)` on primary CTA
  - `.success` on Stars-earned reveal and order-placed confirmation
