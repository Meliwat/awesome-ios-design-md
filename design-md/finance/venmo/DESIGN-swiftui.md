# Venmo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Venmo's visual language into paste-ready SwiftUI code: `Color` / `Font` extensions, the signature feed row, Pay/Request split pill, and the payment-sent confirmation.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas (Light)
    static let vnmCanvas        = Color.white                                    // #FFFFFF
    static let vnmSurfaceMuted  = Color(red: 0.969, green: 0.969, blue: 0.969)  // #F7F7F7
    static let vnmDivider       = Color(red: 0.949, green: 0.949, blue: 0.949)  // #F2F2F2
    static let vnmDividerStrong = Color(red: 0.878, green: 0.878, blue: 0.878)  // #E0E0E0

    // MARK: - Text
    static let vnmTextPrimary   = Color(red: 0.184, green: 0.188, blue: 0.200)  // #2F3033
    static let vnmTextSecondary = Color(red: 0.420, green: 0.431, blue: 0.463)  // #6B6E76
    static let vnmTextTertiary  = Color(red: 0.604, green: 0.627, blue: 0.659)  // #9AA0A8

    // MARK: - Brand
    static let vnmBlue          = Color(red: 0.000, green: 0.549, blue: 1.000)  // #008CFF
    static let vnmBlueDeep      = Color(red: 0.000, green: 0.471, blue: 0.871)  // #0078DE
    static let vnmBlueLight     = Color(red: 0.239, green: 0.584, blue: 0.808)  // #3D95CE
    static let vnmBlueTint      = Color(red: 0.902, green: 0.957, blue: 1.000)  // #E6F4FF

    // MARK: - Semantic
    static let vnmReceivedGreen = Color(red: 0.294, green: 0.710, blue: 0.263)  // #4BB543
    static let vnmChargeRed     = Color(red: 0.827, green: 0.180, blue: 0.180)  // #D32E2E
    static let vnmPendingOrange = Color(red: 0.961, green: 0.651, blue: 0.137)  // #F5A623

    // MARK: - Dark
    static let vnmDarkCanvas    = Color(red: 0.102, green: 0.102, blue: 0.102)  // #1A1A1A
    static let vnmDarkSurface1  = Color(red: 0.141, green: 0.141, blue: 0.141)  // #242424
    static let vnmDarkSurface2  = Color(red: 0.180, green: 0.180, blue: 0.180)  // #2E2E2E
}

extension LinearGradient {
    static let vnmBrand = LinearGradient(
        colors: [.vnmBlue, .vnmBlueDeep],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

Venmo Sans and Venmo Display are proprietary. If unavailable, Source Sans Pro and Brown Std are the closest free/near-free substitutes; SF Pro works as a final fallback.

```swift
extension Font {
    // Display (numbers + hero moments)
    static let vnmBalanceDisplay  = Font.custom("VenmoDisplay-Bold", size: 56).weight(.bold)
    static let vnmConfirmDisplay  = Font.custom("VenmoDisplay-Bold", size: 44).weight(.bold)
    static let vnmSignupDisplay   = Font.custom("VenmoDisplay-Bold", size: 36).weight(.bold)
    static let vnmAmountEntry     = Font.custom("VenmoDisplay-Bold", size: 72).weight(.bold)

    // Sans
    static let vnmScreenTitle     = Font.custom("VenmoSans-Bold",     size: 22).weight(.bold)
    static let vnmFeedName        = Font.custom("VenmoSans-Semibold", size: 15).weight(.semibold)
    static let vnmFeedBody        = Font.custom("VenmoSans-Regular",  size: 15).weight(.regular)
    static let vnmFeedMemo        = Font.custom("VenmoSans-Regular",  size: 15).weight(.regular)
    static let vnmTimestamp       = Font.custom("VenmoSans-Regular",  size: 13).weight(.regular)
    static let vnmComment         = Font.custom("VenmoSans-Regular",  size: 13).weight(.regular)
    static let vnmAmount          = Font.custom("VenmoSans-Bold",     size: 17).weight(.bold)
    static let vnmButton          = Font.custom("VenmoSans-Bold",     size: 16).weight(.bold)
    static let vnmButtonSmall     = Font.custom("VenmoSans-Semibold", size: 15).weight(.semibold)
    static let vnmInput           = Font.custom("VenmoSans-Regular",  size: 16).weight(.regular)
    static let vnmTab             = Font.custom("VenmoSans-Semibold", size: 11).weight(.semibold)
    static let vnmMeta            = Font.custom("VenmoSans-Regular",  size: 12).weight(.regular)
}
```

## 3. Signature Components

### Feed Transaction Row

```swift
struct VenmoFeedRow: View {
    let actorName: String
    let verb: String   // "paid" or "charged"
    let recipientName: String
    let timestamp: String
    let memo: String
    let avatarURL: URL?
    let likeCount: Int
    let commentCount: Int
    @State private var liked = false

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack(alignment: .top, spacing: 12) {
                AsyncImage(url: avatarURL) { img in
                    img.resizable().scaledToFill()
                } placeholder: {
                    Circle().fill(Color.vnmSurfaceMuted)
                }
                .frame(width: 40, height: 40)
                .clipShape(Circle())

                VStack(alignment: .leading, spacing: 4) {
                    // Mixed-weight transaction line
                    (
                        Text(actorName).font(.vnmFeedName) +
                        Text(" \(verb) ").font(.vnmFeedBody) +
                        Text(recipientName).font(.vnmFeedName) +
                        Text(" · \(timestamp)").font(.vnmTimestamp).foregroundColor(.vnmTextSecondary)
                    )
                    .foregroundStyle(.vnmTextPrimary)

                    Text(memo)
                        .font(.vnmFeedMemo)
                        .foregroundStyle(.vnmTextPrimary)
                        .lineLimit(3)
                }
            }

            // Like / Comment row
            HStack(spacing: 16) {
                Button { liked.toggle() } label: {
                    HStack(spacing: 4) {
                        Image(systemName: liked ? "heart.fill" : "heart")
                            .foregroundStyle(liked ? .vnmBlue : .vnmTextSecondary)
                        Text("\(likeCount + (liked ? 1 : 0))")
                            .font(.vnmComment)
                            .foregroundStyle(.vnmTextSecondary)
                    }
                }
                .sensoryFeedback(.impact(weight: .light), trigger: liked)

                HStack(spacing: 4) {
                    Image(systemName: "bubble.left")
                        .foregroundStyle(.vnmTextSecondary)
                    Text("\(commentCount)")
                        .font(.vnmComment)
                        .foregroundStyle(.vnmTextSecondary)
                }
            }
            .padding(.leading, 52)   // align under the avatar column
        }
        .padding(.horizontal, 16).padding(.vertical, 12)
        .background(Color.vnmCanvas)
        .overlay(Rectangle().frame(height: 1).foregroundStyle(.vnmDivider), alignment: .bottom)
    }
}
```

### Pay / Request Split Pill

```swift
struct VenmoPayRequestPill: View {
    let onPay: () -> Void
    let onRequest: () -> Void

    var body: some View {
        HStack(spacing: 0) {
            SplitHalf(title: "Pay", icon: "arrow.up", action: onPay)
            Rectangle().fill(.white.opacity(0.6)).frame(width: 1, height: 30)
            SplitHalf(title: "Request", icon: "arrow.down", action: onRequest)
        }
        .frame(height: 50)
        .background(Capsule().fill(Color.vnmBlue))
        .shadow(color: Color.vnmBlue.opacity(0.25), radius: 12, y: 4)
        .padding(.horizontal, 16)
    }
}

private struct SplitHalf: View {
    let title: String
    let icon: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: icon)
                    .font(.system(size: 16, weight: .bold))
                Text(title)
                    .font(.vnmButton)
                    .tracking(0.2)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity, maxHeight: .infinity)
            .background(pressed ? Color.vnmBlueDeep : Color.clear)
            .clipShape(Capsule())
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded   { _ in pressed = false }
        )
    }
}
```

### Balance Card (Me Tab Hero)

```swift
struct VenmoBalanceCard: View {
    let balance: Double
    let onTransfer: () -> Void
    let onAddMoney: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 20) {
            Text("Venmo balance")
                .font(.vnmButtonSmall)
                .foregroundStyle(.white.opacity(0.8))
            Text(formatCurrency(balance))
                .font(.vnmBalanceDisplay)
                .foregroundStyle(.white)
                .monospacedDigit()
                .tracking(-0.8)
            HStack(spacing: 12) {
                WhitePill(title: "Transfer to bank", action: onTransfer)
                WhitePill(title: "Add money", action: onAddMoney)
            }
        }
        .padding(20)
        .frame(maxWidth: .infinity, alignment: .leading)
        .frame(height: 180)
        .background(RoundedRectangle(cornerRadius: 20).fill(LinearGradient.vnmBrand))
        .padding(.horizontal, 16)
    }
}

private struct WhitePill: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.vnmButtonSmall)
                .foregroundStyle(.vnmBlue)
                .padding(.horizontal, 16).padding(.vertical, 10)
                .background(Capsule().fill(Color.white))
        }
    }
}

func formatCurrency(_ n: Double) -> String {
    let formatter = NumberFormatter()
    formatter.numberStyle = .currency
    formatter.currencyCode = "USD"
    formatter.maximumFractionDigits = 2
    return formatter.string(from: NSNumber(value: n)) ?? "$0.00"
}
```

### Amount Input Screen

```swift
struct VenmoAmountEntryView: View {
    @State private var amount: String = "0"
    @State private var memo: String = ""
    let onPay: () -> Void

    var displayAmount: String {
        // Format "1234" → "$12.34" live
        let cents = Int(amount) ?? 0
        let dollars = Double(cents) / 100
        return formatCurrency(dollars)
    }

    let quickEmojis = ["🍕","🍺","🚗","💰","🎉","☕","🏠","🎁","🛒","✈️","🎬","💵"]

    var body: some View {
        VStack(spacing: 20) {
            Spacer()
            Text(displayAmount)
                .font(.vnmAmountEntry)
                .foregroundStyle(.vnmTextPrimary)
                .monospacedDigit()
                .tracking(-1.0)

            TextField("What's it for?", text: $memo)
                .font(.vnmInput)
                .multilineTextAlignment(.center)
                .padding(.horizontal, 40)

            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 8) {
                    ForEach(quickEmojis, id: \.self) { emoji in
                        Button { memo += emoji } label: {
                            Text(emoji)
                                .font(.system(size: 22))
                                .frame(width: 40, height: 32)
                                .background(Capsule().fill(Color.vnmSurfaceMuted))
                        }
                    }
                }
                .padding(.horizontal, 16)
            }

            Spacer()

            Button(action: onPay) {
                Text("Pay \(displayAmount)")
                    .font(.vnmButton)
                    .foregroundStyle(.white)
                    .frame(maxWidth: .infinity, minHeight: 52)
                    .background(Capsule().fill(Color.vnmBlue))
            }
            .padding(.horizontal, 16)
            .padding(.bottom, 24)
        }
    }
}
```

### Payment-Sent Confirmation (Animated Checkmark)

```swift
struct VenmoSentConfirmationView: View {
    let amount: Double
    let recipientName: String
    @State private var checkProgress: CGFloat = 0
    @State private var circleScale: CGFloat = 0

    var body: some View {
        ZStack {
            LinearGradient.vnmBrand.ignoresSafeArea()

            VStack(spacing: 24) {
                ZStack {
                    Circle()
                        .fill(.white.opacity(0.15))
                        .frame(width: 160, height: 160)
                    Circle()
                        .fill(Color.vnmBlue)
                        .frame(width: 80, height: 80)
                        .scaleEffect(circleScale)

                    CheckmarkShape()
                        .trim(from: 0, to: checkProgress)
                        .stroke(Color.white, style: StrokeStyle(lineWidth: 6, lineCap: .round, lineJoin: .round))
                        .frame(width: 44, height: 32)
                }

                VStack(spacing: 8) {
                    Text("Sent")
                        .font(.vnmSignupDisplay)
                        .foregroundStyle(.white)
                    Text(formatCurrency(amount))
                        .font(.vnmConfirmDisplay)
                        .foregroundStyle(.white)
                        .monospacedDigit()
                    Text("To \(recipientName)")
                        .font(.vnmInput)
                        .foregroundStyle(.white.opacity(0.8))
                }

                WhitePill(title: "Done", action: { /* dismiss */ })
                    .padding(.top, 16)
            }
        }
        .onAppear {
            withAnimation(.spring(response: 0.35, dampingFraction: 0.7)) {
                circleScale = 1.0
            }
            withAnimation(.easeOut(duration: 0.35).delay(0.2)) {
                checkProgress = 1.0
            }
        }
        .sensoryFeedback(.success, trigger: true)
    }
}

struct CheckmarkShape: Shape {
    func path(in rect: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: rect.minX, y: rect.midY))
        p.addLine(to: CGPoint(x: rect.minX + rect.width * 0.35, y: rect.maxY))
        p.addLine(to: CGPoint(x: rect.maxX, y: rect.minY))
        return p
    }
}
```

## 4. Tab Bar (with Pay Pill overlay)

```swift
struct VenmoRootTabView: View {
    var body: some View {
        ZStack(alignment: .bottom) {
            TabView {
                HomeFeedView().tabItem  { Label("Home",     systemImage: "house.fill") }
                IncompleteView().tabItem { Label("Incomplete", systemImage: "clock.fill") }
                Color.clear.tabItem     { Label("", systemImage: "") }       // spacer for pill
                CardsView().tabItem     { Label("Cards",    systemImage: "creditcard.fill") }
                MeView().tabItem        { Label("Me",       systemImage: "person.fill") }
            }
            .tint(Color.vnmBlue)

            VenmoPayRequestPill(onPay: {}, onRequest: {})
                .padding(.bottom, 4)
        }
    }
}
```

## 5. Motion Specs

```swift
// Pay/Request half press
withAnimation(.spring(response: 0.2, dampingFraction: 0.8)) { pressed = true }

// Confirmation checkmark
withAnimation(.spring(response: 0.35, dampingFraction: 0.7)) { circleScale = 1.0 }
withAnimation(.easeOut(duration: 0.35).delay(0.2))          { checkProgress = 1.0 }

// Balance "tick up" on mount
@State private var displayValue = 0.0
.onAppear {
    withAnimation(.easeOut(duration: 0.4)) { displayValue = actualBalance }
}

// Feed row like
@State private var likeScale: CGFloat = 1
onLikeTap:
    withAnimation(.spring(response: 0.2, dampingFraction: 0.6)) {
        likeScale = 1.2
    }
    // reset
    DispatchQueue.main.asyncAfter(deadline: .now() + 0.2) {
        withAnimation { likeScale = 1.0 }
    }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Pay arrow | `arrow.up` | 16pt |
| Request arrow | `arrow.down` | 16pt |
| Like | `heart` / `heart.fill` | 18pt |
| Comment | `bubble.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Send (comment) | `arrow.up.circle.fill` | 28pt |
| Search | `magnifyingglass` | 16pt |
| QR scan | `qrcode.viewfinder` | 24pt |
| Home (tab) | `house.fill` | 24pt |
| Cards (tab) | `creditcard.fill` | 24pt |
| Me (tab) | `person.fill` | 24pt |
| Checkmark animation | custom Shape | 44x32pt |
| Verified badge | `checkmark.seal.fill` | 14pt |

## 7. Accessibility Notes

- Minimum target: iOS 16 (iOS 17 for `.sensoryFeedback`)
- **VoiceOver**:
  - Feed row: build a single accessibility string — "Alex paid Jordan, 2 hours ago. Memo: pizza and beer Pizza Friday. 3 likes, 1 comment"
  - Pay/Request split: two children, each with `accessibilityLabel("Pay")` / `accessibilityLabel("Request")`
  - Balance: `accessibilityLabel("Venmo balance, $124.50")`
  - Payment sent: announce "Payment sent successfully" after the animation completes
- **Dynamic Type**: Feed body, memo, comment scale freely; balance Display caps at 72pt, amount-entry Display stays fixed at 72pt for layout
- **Contrast**: `#6B6E76` secondary on `#FFFFFF` meets WCAG AA at 13pt+; `#9AA0A8` tertiary is decorative only
- **Reduce Motion**: Skip the checkmark stroke animation (just show the filled check), skip confetti, replace balance tick-up with the final value
- **Dark mode**: Canvas `#1A1A1A`, surfaces `#242424`/`#2E2E2E`, text primary white, Venmo Blue unchanged
- **Haptics**:
  - `.selection` on emoji chip tap
  - `.impact(.light)` on feed row like
  - `.impact(.medium)` on Pay/Request tap
  - `.success` on payment confirmation
- **Emoji rendering**: Rely on the system emoji font stack — don't re-render emojis with custom art, because Venmo's playful emoji-memo culture depends on native OS emojis
