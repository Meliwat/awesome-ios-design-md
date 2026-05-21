# Charles Schwab (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Schwab's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Recurring rules: **money is tabular**, **green/red mean gain/loss only**, and **dark navy text on Schwab Blue**.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let schwabBlue        = Color(red: 0.000, green: 0.616, blue: 0.863) // #009DDC
    static let schwabBluePressed = Color(red: 0.000, green: 0.502, blue: 0.710) // #0080B5
    static let schwabNavy        = Color(red: 0.000, green: 0.231, blue: 0.361) // #003B5C
    static let schwabNavyDeep    = Color(red: 0.000, green: 0.165, blue: 0.259) // #002A42
    static let schwabSky         = Color(red: 0.275, green: 0.745, blue: 0.925) // #46BEEC
    static let schwabSkyLight    = Color(red: 0.000, green: 0.471, blue: 0.659) // #0078A8
    static let schwabOnBlue      = Color(red: 0.000, green: 0.133, blue: 0.200) // #002233

    // MARK: - Canvas & Surfaces (Light)
    static let schwabCanvas        = Color.white                                    // #FFFFFF
    static let schwabSurface       = Color(red: 0.949, green: 0.965, blue: 0.973)  // #F2F6F8
    static let schwabSurfacePressed = Color(red: 0.902, green: 0.929, blue: 0.945) // #E6EDF1
    static let schwabDivider       = Color(red: 0.859, green: 0.890, blue: 0.910)  // #DBE3E8

    // MARK: - Canvas & Surfaces (Dark)
    static let schwabDarkCanvas   = Color(red: 0.039, green: 0.086, blue: 0.133) // #0A1622
    static let schwabDarkSurface1 = Color(red: 0.067, green: 0.129, blue: 0.184) // #11212F
    static let schwabDarkSurface2 = Color(red: 0.094, green: 0.188, blue: 0.247) // #18303F
    static let schwabDarkDivider  = Color(red: 0.122, green: 0.227, blue: 0.290) // #1F3A4A

    // MARK: - Text
    static let schwabTextPrimary    = Color(red: 0.063, green: 0.133, blue: 0.180) // #10222E
    static let schwabTextSecondary  = Color(red: 0.353, green: 0.439, blue: 0.486) // #5A707C
    static let schwabTextTertiary   = Color(red: 0.522, green: 0.592, blue: 0.627) // #8597A0
    static let schwabDarkTextPrimary   = Color(red: 0.902, green: 0.933, blue: 0.953) // #E6EEF3
    static let schwabDarkTextSecondary = Color(red: 0.616, green: 0.698, blue: 0.749) // #9DB2BF
    static let schwabDarkTextTertiary  = Color(red: 0.416, green: 0.514, blue: 0.573) // #6A8392
    static let schwabOnHero      = Color.white                                       // #FFFFFF
    static let schwabOnHeroSub   = Color(red: 0.616, green: 0.769, blue: 0.847)     // #9DC4D8

    // MARK: - Financial Semantics (sacred — gain/loss only)
    static let schwabGainLight = Color(red: 0.086, green: 0.537, blue: 0.373) // #16895F
    static let schwabGainDark  = Color(red: 0.094, green: 0.690, blue: 0.482) // #18B07B
    static let schwabLossLight = Color(red: 0.784, green: 0.267, blue: 0.239) // #C8443D
    static let schwabLossDark  = Color(red: 0.886, green: 0.337, blue: 0.306) // #E2564E
    static let schwabOnHeroGain = Color(red: 0.373, green: 0.890, blue: 0.690) // #5FE3B0

    // MARK: - System / Accent
    static let schwabWarning = Color(red: 0.784, green: 0.525, blue: 0.169) // #C8862B
    static let schwabInfo    = Color(red: 0.000, green: 0.471, blue: 0.659) // #0078A8
    static let schwabGold    = Color(red: 0.784, green: 0.635, blue: 0.294) // #C8A24B
}

extension Color {
    static func schwabGain(_ s: ColorScheme) -> Color { s == .dark ? .schwabGainDark : .schwabGainLight }
    static func schwabLoss(_ s: ColorScheme) -> Color { s == .dark ? .schwabLossDark : .schwabLossLight }
}

/// Resolve a change value to its semantic color (flat -> secondary text).
func schwabChangeColor(_ change: Double, scheme: ColorScheme) -> Color {
    if change > 0 { return .schwabGain(scheme) }
    if change < 0 { return .schwabLoss(scheme) }
    return scheme == .dark ? .schwabDarkTextSecondary : .schwabTextSecondary
}
```

## 2. Typography

Schwab's brand face is "Charles Modern"; fall back to SF Pro or bundle **Source Sans 3** (SIL OFL) as the closest free match. The defining rule: every monetary figure uses `.monospacedDigit()`.

```swift
extension Font {
    static let schwabTotalValue = Font.custom("SourceSans3-ExtraBold", size: 34).weight(.heavy)
    static let schwabScreenTitle = Font.custom("SourceSans3-ExtraBold", size: 26).weight(.heavy)
    static let schwabSection    = Font.custom("SourceSans3-Bold",      size: 22).weight(.bold)
    static let schwabCardTitle  = Font.custom("SourceSans3-Bold",      size: 18).weight(.bold)
    static let schwabBody       = Font.custom("SourceSans3-Regular",   size: 16).weight(.regular)
    static let schwabRowValue   = Font.custom("SourceSans3-ExtraBold", size: 16).weight(.heavy)
    static let schwabAcctName   = Font.custom("SourceSans3-Bold",      size: 15).weight(.bold)
    static let schwabMeta       = Font.custom("SourceSans3-Regular",   size: 14).weight(.regular)
    static let schwabNumericMono = Font.custom("SourceSans3-Bold",     size: 13).weight(.bold)
    static let schwabCaption    = Font.custom("SourceSans3-SemiBold",  size: 12).weight(.semibold)
    static let schwabButton     = Font.custom("SourceSans3-ExtraBold", size: 16).weight(.heavy)
    static let schwabTab        = Font.custom("SourceSans3-SemiBold",  size: 10).weight(.semibold)
    static let schwabTicketField = Font.custom("SourceSans3-ExtraBold", size: 16).weight(.heavy)
}

/// Money modifier — ALWAYS apply to currency / price / % / quantity.
struct SchwabMoney: ViewModifier {
    func body(content: Content) -> some View { content.monospacedDigit() }
}
extension View { func schwabMoney() -> some View { modifier(SchwabMoney()) } }
```

## 3. Signature Components

### Account Hero (navy gradient — the screen lead)

```swift
struct AccountHero: View {
    let title: String
    let totalValue: String     // "$612,408.55"
    let changeAmount: String   // "+$5,204.18"
    let changePercent: String  // "+0.86%"
    let change: Double
    let onBell: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text(title).font(.schwabScreenTitle).foregroundStyle(.white)
                Spacer()
                Button(action: onBell) {
                    Image(systemName: "bell")
                        .font(.system(size: 16, weight: .semibold))
                        .foregroundStyle(.white)
                        .frame(width: 34, height: 34)
                        .background(Circle().fill(Color.white.opacity(0.10)))
                }
            }
            Text("TOTAL VALUE")
                .font(.schwabCaption).tracking(0.6)
                .foregroundStyle(Color.schwabOnHeroSub)
                .padding(.top, 18)
            Text(totalValue)
                .font(.schwabTotalValue).foregroundStyle(.white)
                .schwabMoney().padding(.top, 4)
            HStack(spacing: 6) {
                Text(change >= 0 ? "▲" : "▼")
                Text("\(changeAmount) ").schwabMoney()
                + Text("(\(changePercent)) today").foregroundColor(Color.schwabOnHeroSub)
            }
            .font(.schwabNumericMono)
            .foregroundStyle(Color.schwabOnHeroGain)
            .padding(.top, 8)
        }
        .padding(.horizontal, 20)
        .padding(.top, 8).padding(.bottom, 22)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            LinearGradient(colors: [.schwabNavy, .schwabNavyDeep],
                           startPoint: .topLeading, endPoint: .bottomTrailing)
        )
    }
}
```

### Account Card / List Cell

```swift
struct AccountCard: View {
    @Environment(\.colorScheme) var scheme
    let icon: String       // SF Symbol
    let name: String
    let mask: String       // "Brokerage ••4471"
    let value: String      // "$428,910.22"
    let changePct: String  // "+0.94%"
    let change: Double

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: icon)
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(scheme == .dark ? Color.schwabSky : Color.schwabSkyLight)
                .frame(width: 38, height: 38)
                .background(RoundedRectangle(cornerRadius: 10).fill(surface2))

            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.schwabAcctName).foregroundStyle(primary)
                Text(mask).font(.schwabCaption).foregroundStyle(secondary)
            }
            Spacer(minLength: 8)
            VStack(alignment: .trailing, spacing: 2) {
                Text(value).font(.schwabRowValue).foregroundStyle(primary).schwabMoney()
                Text(changePct).font(.schwabNumericMono)
                    .foregroundStyle(schwabChangeColor(change, scheme: scheme)).schwabMoney()
            }
        }
        .padding(.horizontal, 20)
        .frame(height: 66)
        .overlay(Rectangle().fill(divider).frame(height: 0.5), alignment: .bottom)
    }

    private var primary: Color { scheme == .dark ? .schwabDarkTextPrimary : .schwabTextPrimary }
    private var secondary: Color { scheme == .dark ? .schwabDarkTextSecondary : .schwabTextSecondary }
    private var surface2: Color { scheme == .dark ? .schwabDarkSurface2 : .schwabSurface }
    private var divider: Color { scheme == .dark ? .schwabDarkDivider : .schwabDivider }
}
```

### Trade Ticket (the centerpiece)

```swift
struct TradeTicket: View {
    @State private var side = 0  // 0 = Buy, 1 = Sell
    let fields: [(String, String)]  // [("Symbol","AAPL"), ("Quantity","25"), …]
    let onReview: () -> Void

    var body: some View {
        VStack(spacing: 0) {
            // Segmented Buy/Sell
            HStack(spacing: 3) {
                ForEach(["Buy", "Sell"].indices, id: \.self) { i in
                    Text(["Buy", "Sell"][i])
                        .font(.system(size: 13, weight: .bold))
                        .frame(maxWidth: .infinity).frame(height: 36)
                        .foregroundStyle(i == side ? Color.schwabOnBlue : Color.schwabDarkTextSecondary)
                        .background(RoundedRectangle(cornerRadius: 6)
                            .fill(i == side ? Color.schwabBlue : .clear))
                        .onTapGesture {
                            withAnimation(.easeOut(duration: 0.2)) { side = i }
                            UIImpactFeedbackGenerator(style: .light).impactOccurred()
                        }
                }
            }
            .padding(3)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.schwabDarkCanvas))

            ForEach(fields.indices, id: \.self) { idx in
                HStack {
                    Text(fields[idx].0).font(.schwabNumericMono)
                        .foregroundStyle(Color.schwabDarkTextSecondary)
                    Spacer()
                    Text(fields[idx].1).font(.schwabTicketField)
                        .foregroundStyle(Color.schwabDarkTextPrimary).schwabMoney()
                }
                .padding(.vertical, 12)
                .overlay(idx == fields.count - 1 ? nil :
                    Rectangle().fill(Color.schwabDarkDivider).frame(height: 1), alignment: .bottom)
            }

            Button(action: onReview) {
                Text("Review order").font(.schwabButton)
                    .foregroundStyle(Color.schwabOnBlue)
                    .frame(maxWidth: .infinity).frame(height: 52)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.schwabBlue))
            }
            .buttonStyle(SchwabPressStyle())
            .padding(.top, 14)
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.schwabDarkSurface2))
    }
}

struct SchwabPressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.05 : 0)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Sticky Trade Button & Balances Tiles

```swift
struct StickyTradeBar: View {
    let action: () -> Void
    var body: some View {
        VStack(spacing: 0) {
            Divider().background(Color.schwabDarkDivider)
            Button(action: {
                UIImpactFeedbackGenerator(style: .medium).impactOccurred(); action()
            }) {
                Text("Trade").font(.schwabButton).foregroundStyle(Color.schwabOnBlue)
                    .frame(maxWidth: .infinity).frame(height: 52)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.schwabBlue))
            }
            .buttonStyle(SchwabPressStyle())
            .padding(.horizontal, 16).padding(.top, 12)
        }
        .background(.ultraThinMaterial)
    }
}

struct BalancesTiles: View {
    @Environment(\.colorScheme) var scheme
    let tiles: [(String, String, Double?)] // label, value, optional change

    let cols = [GridItem(.flexible(), spacing: 12), GridItem(.flexible(), spacing: 12)]

    var body: some View {
        LazyVGrid(columns: cols, spacing: 12) {
            ForEach(tiles.indices, id: \.self) { i in
                VStack(alignment: .leading, spacing: 6) {
                    Text(tiles[i].0).font(.schwabCaption)
                        .foregroundStyle(scheme == .dark ? Color.schwabDarkTextSecondary : Color.schwabTextSecondary)
                    Text(tiles[i].1)
                        .font(.system(size: 19, weight: .heavy)).schwabMoney()
                        .foregroundStyle(tiles[i].2.map { schwabChangeColor($0, scheme: scheme) }
                            ?? (scheme == .dark ? Color.schwabDarkTextPrimary : Color.schwabTextPrimary))
                }
                .frame(maxWidth: .infinity, alignment: .leading)
                .padding(.vertical, 14).padding(.horizontal, 16)
                .background(RoundedRectangle(cornerRadius: 12)
                    .fill(scheme == .dark ? Color.schwabDarkSurface2 : Color.schwabSurface))
            }
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SchwabTabView: View {
    var body: some View {
        TabView {
            AccountsView().tabItem  { Label("Accounts",  systemImage: "house.fill") }
            TradeView().tabItem     { Label("Trade",     systemImage: "chart.line.uptrend.xyaxis") }
            ResearchView().tabItem  { Label("Research",  systemImage: "magnifyingglass") }
            MessagesView().tabItem  { Label("Messages",  systemImage: "envelope.fill") }
            MoreView().tabItem      { Label("More",      systemImage: "square.grid.2x2.fill") }
        }
        .tint(.schwabSky) // active sky-blue; inactive system tertiary
    }
}
```

## 5. Motion

```swift
// Balance tick — animate COLOR only (digits never roll)
withAnimation(.easeOut(duration: 0.6)) { tickColor = schwabChangeColor(delta, scheme: scheme) }

// Trade Buy/Sell segmented — active pill slide
withAnimation(.easeOut(duration: 0.2)) { side = i }

// Chart range morph
.animation(.easeOut(duration: 0.28), value: rangePoints)

// Push navigation: standard NavigationStack push (300ms iOS)

// Sticky Trade/Review button: always visible; top Divider appears as content scrolls beneath

// Haptics
UIImpactFeedbackGenerator(style: .light).impactOccurred()   // tab / segmented / range change
UIImpactFeedbackGenerator(style: .medium).impactOccurred()  // tap Trade / Place order
UINotificationFeedbackGenerator().notificationOccurred(.success) // order accepted
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Accounts (tab) | `house` / `house.fill` | 22pt |
| Trade (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Research (tab) | `magnifyingglass` | 22pt |
| Messages (tab) | `envelope` / `envelope.fill` | 22pt |
| More (tab) | `square.grid.2x2` / `.fill` | 22pt |
| Brokerage account | `chart.line.uptrend.xyaxis` | 18pt |
| Retirement account | `clock` | 18pt |
| Bank account | `creditcard` | 18pt |
| Alerts bell | `bell` / `bell.fill` | 16pt |
| Day change up | `arrowtriangle.up.fill` | 11pt |
| Day change down | `arrowtriangle.down.fill` | 11pt |
| Back | `chevron.left` | 22pt |
| Overflow | `ellipsis` | 20pt |
| Disclosure | `chevron.right` | 14pt |
| Transfer | `arrow.left.arrow.right` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Documents / statements | `doc.text` | 18pt |
| Order type | `slider.horizontal.3` | 16pt |

## 7. Dark Mode

```swift
struct SchwabTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.schwabDarkCanvas : Color.schwabCanvas)
            .foregroundStyle(scheme == .dark ? Color.schwabDarkTextPrimary : Color.schwabTextPrimary)
            .tint(scheme == .dark ? Color.schwabSky : Color.schwabSkyLight)
    }
}
extension View { func schwabTheme() -> some View { modifier(SchwabTheme()) } }
```

Dark canvas is the maritime navy `#0A1622`, never pure black. The account hero keeps its `#003B5C → #002A42` gradient on both modes — it carries the brand. Schwab Blue `#009DDC` stays the action color (always with `#002233` on-color text); links/selected tints brighten to `#46BEEC` Sky on dark. Gain/loss shift to the brighter dark pair (`#18B07B` / `#E2564E`) but meaning never changes. Cards get a 1pt `#1F3A4A` border on dark since shadows vanish against the navy.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`NavigationStack`, `.ultraThinMaterial`). `UIImpactFeedbackGenerator` works back to iOS 13.
- Bundle Source Sans 3 (SIL OFL) as the free brand substitute; register all weights via `Info.plist` `UIAppFonts`. If using SF Pro, apply `.monospacedDigit()` to all money.
- Dynamic Type: scale total value, titles, body, meta; pin tab labels, ticket field values, and table headers at fixed sizes. Tabular figures must remain tabular at every size.
- Do NOT rely on color alone for gain/loss: always pair green/red with a `+`/`−` sign and a ▲/▼ glyph (components above do). Hard requirement for a finance app.
- Contrast: Schwab Blue `#009DDC` with **dark navy** text `#002233` passes WCAG AA at 16pt bold (white-on-blue does NOT — never use it). `#18B07B`/`#E2564E` on `#0A1622` pass AA at the bold numeric weights. `#5FE3B0` on the navy hero passes AA.
- VoiceOver: announce an account row as "Roth IRA, Retirement ending 8820, 158,330 dollars 7 cents, up 0.71 percent"; combine name + mask + value + signed change in one label; never expose color as the only state.
- Reduce Motion: disable the segmented pill slide (snap instead), the chart morph (cross-fade), and the balance-tick color animation (set final color instantly); keep the static sign/arrow.
- Dark mode: invert via the `schwabDark*` palette — `#0A1622`, NOT true black; add the 1pt `#1F3A4A` border to cards, the ticket, and sheets as the elevation cue. Keep the navy hero gradient on dark.
