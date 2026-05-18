# Threema (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Threema's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature trust-level indicator, the QR / verify surface, message bubbles, and the bottom tab bar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let trCanvas        = Color.white                                   // #FFFFFF
    static let trSurfaceGray   = Color(red: 0.957, green: 0.957, blue: 0.965) // #F4F4F6
    static let trSurfacePressed = Color(red: 0.910, green: 0.910, blue: 0.922) // #E8E8EB
    static let trDivider       = Color(red: 0.886, green: 0.886, blue: 0.898) // #E2E2E5
    static let trBubbleIn      = Color(red: 0.922, green: 0.922, blue: 0.929) // #EBEBED
    static let trBubbleOut     = Color(red: 0.839, green: 0.941, blue: 0.863) // #D6F0DC

    // MARK: - Canvas & Surfaces (Dark)
    static let trDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let trDarkSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let trDarkSurface2  = Color(red: 0.149, green: 0.149, blue: 0.157) // #262628
    static let trDarkDivider   = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E
    static let trBubbleOutDark = Color(red: 0.047, green: 0.369, blue: 0.133) // #0C5E22

    // MARK: - Text
    static let trTextPrimary   = Color(red: 0.067, green: 0.067, blue: 0.075) // #111113
    static let trTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.439) // #6B6B70
    static let trTextTertiary  = Color(red: 0.651, green: 0.651, blue: 0.671) // #A6A6AB
    static let trDarkTextPrimary   = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC
    static let trDarkTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.624) // #9A9A9F

    // MARK: - Brand
    static let trGreen        = Color(red: 0.031, green: 0.541, blue: 0.161) // #088A29
    static let trGreenPressed = Color(red: 0.024, green: 0.420, blue: 0.125) // #066B20
    static let trGreenBright  = Color(red: 0.122, green: 0.647, blue: 0.235) // #1FA53C
    static let trLink         = Color(red: 0.310, green: 0.706, blue: 0.467) // #4FB477

    // MARK: - Trust Levels (the signature roles)
    static let trTrustRed    = Color(red: 0.898, green: 0.271, blue: 0.227) // #E5453A — Level 1
    static let trTrustOrange = Color(red: 0.937, green: 0.545, blue: 0.173) // #EF8B2C — Level 2
    static let trTrustGreen  = Color(red: 0.082, green: 0.639, blue: 0.227) // #15A33A — Level 3
    static let trTrustTrack  = Color(red: 0.824, green: 0.824, blue: 0.843) // #D2D2D7

    // MARK: - Semantic
    static let trError   = Color(red: 0.898, green: 0.271, blue: 0.227) // #E5453A
    static let trSuccess = Color(red: 0.082, green: 0.639, blue: 0.227) // #15A33A
    static let trWarning = Color(red: 0.937, green: 0.545, blue: 0.173) // #EF8B2C
}

enum TrustLevel: Int, CaseIterable {
    case unknown = 1, serverMatched, verified

    var color: Color {
        switch self {
        case .unknown:       return .trTrustRed
        case .serverMatched: return .trTrustOrange
        case .verified:      return .trTrustGreen
        }
    }
    var label: String {
        switch self {
        case .unknown:       return "Unknown"
        case .serverMatched: return "Server-matched"
        case .verified:      return "Verified"
        }
    }
    var voiceOver: String { "Trust level: \(label.lowercased())" }
}
```

## 2. Typography

Threema ships **no custom typeface** — it uses SF Pro with Dynamic Type. The Threema ID and key fingerprint use SF Mono so characters are unambiguous when read aloud during verification.

```swift
extension Font {
    // SF Pro (system) — respects Dynamic Type via relativeTo
    static let trLargeTitle   = Font.system(size: 32, weight: .heavy,    design: .default)
    static let trScreenTitle  = Font.system(size: 26, weight: .bold,     design: .default)
    static let trSection      = Font.system(size: 22, weight: .bold,     design: .default)
    static let trNavName      = Font.system(size: 18, weight: .semibold, design: .default)
    static let trBody         = Font.system(size: 16, weight: .regular,  design: .default)
    static let trListTitle    = Font.system(size: 15, weight: .medium,   design: .default)
    static let trSecondary    = Font.system(size: 14, weight: .regular,  design: .default)
    static let trCaption      = Font.system(size: 12, weight: .medium,   design: .default)
    static let trButton       = Font.system(size: 16, weight: .semibold, design: .default)
    static let trTab          = Font.system(size: 10, weight: .medium,   design: .default)
    static let trBadge        = Font.system(size: 12, weight: .bold,     design: .default)

    // SF Mono — identity strings only (Threema ID + fingerprint)
    static let trID           = Font.system(size: 14, weight: .semibold, design: .monospaced)
    static let trIDLarge      = Font.system(size: 16, weight: .bold,     design: .monospaced)
}

// Dynamic Type friendly variants (scale relative to a text style)
extension Font {
    static func trScaled(_ size: CGFloat, _ weight: Font.Weight, relativeTo style: Font.TextStyle = .body) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Trust-Level Indicator (the core atom)

```swift
struct TrustDots: View {
    let level: TrustLevel
    var dotSize: CGFloat = 8
    var showLabel: Bool = true

    var body: some View {
        HStack(spacing: 4) {
            HStack(spacing: 3) {
                ForEach(0..<3, id: \.self) { _ in
                    Circle()
                        .fill(level.color)
                        .frame(width: dotSize, height: dotSize)
                }
            }
            if showLabel {
                Text(level.label)
                    .font(.trSecondary)
                    .foregroundStyle(Color.trDarkTextSecondary)
            }
        }
        .animation(.easeOut(duration: 0.2), value: level)
        .accessibilityElement(children: .ignore)
        .accessibilityLabel(level.voiceOver)
    }
}
```

### Threema ID Chip

```swift
struct ThreemaIDChip: View {
    let id: String           // e.g. "ECHOX9P2"
    let onCopy: () -> Void

    var body: some View {
        HStack(spacing: 8) {
            Text(id)
                .font(.trIDLarge)
                .tracking(1)
                .foregroundStyle(Color.trDarkTextPrimary)
            Button(action: onCopy) {
                Image(systemName: "doc.on.doc")
                    .font(.system(size: 15))
                    .foregroundStyle(Color.trGreenBright)
            }
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 8)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.trDarkSurface2))
    }
}
```

### QR Verify Card

```swift
struct QRVerifyCard: View {
    let threemaID: String
    let qrImage: Image          // pre-rendered QR (modules in trGreen on white)

    var body: some View {
        VStack(spacing: 16) {
            qrImage
                .resizable()
                .interpolation(.none)
                .scaledToFit()
                .frame(width: 220, height: 220)
                .padding(16)
                .background(RoundedRectangle(cornerRadius: 12).fill(.white))

            Text(threemaID)
                .font(.trIDLarge)
                .tracking(1)
                .foregroundStyle(Color.trDarkTextPrimary)

            Text("Scan in person to reach three green dots — the only fully-verified state.")
                .font(.trSecondary)
                .foregroundStyle(Color.trDarkTextSecondary)
                .multilineTextAlignment(.center)
                .padding(.horizontal, 24)
        }
    }
}

struct QRScannerReticle: View {
    @State private var pulse = false
    var body: some View {
        RoundedRectangle(cornerRadius: 16)
            .stroke(pulse ? Color.trTrustGreen : .white, lineWidth: 2.5)
            .frame(width: 240, height: 240)
            .onAppear {
                withAnimation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true)) {
                    pulse = true
                }
            }
    }
}
```

### Message Bubbles

```swift
struct MessageBubble: View {
    enum Direction { case incoming, outgoing }
    let text: String
    let time: String
    let direction: Direction
    var isRead: Bool = false

    var body: some View {
        HStack {
            if direction == .outgoing { Spacer(minLength: 60) }
            VStack(alignment: .trailing, spacing: 4) {
                Text(text)
                    .font(.trBody)
                    .foregroundStyle(direction == .outgoing
                        ? Color(red: 0.918, green: 0.984, blue: 0.937)  // #EAFBEF
                        : Color.trDarkTextPrimary)
                HStack(spacing: 4) {
                    Text(time)
                        .font(.system(size: 10, weight: .regular))
                        .foregroundStyle(direction == .outgoing
                            ? Color.white.opacity(0.55)
                            : Color.trDarkTextSecondary.opacity(0.7))
                    if direction == .outgoing {
                        Image(systemName: "checkmark")
                            .font(.system(size: 9, weight: .bold))
                            .foregroundStyle(isRead
                                ? Color(red: 0.435, green: 0.839, blue: 0.549) // #6FD68C
                                : Color.white.opacity(0.55))
                    }
                }
            }
            .padding(.horizontal, 13)
            .padding(.vertical, 8)
            .background(bubbleBackground)
            .clipShape(BubbleShape(direction: direction))
            if direction == .incoming { Spacer(minLength: 60) }
        }
        .transition(.opacity.combined(with: .move(edge: .bottom)))
    }

    private var bubbleBackground: Color {
        direction == .outgoing ? .trBubbleOutDark : .trDarkSurface2
    }
}

struct BubbleShape: Shape {
    let direction: MessageBubble.Direction
    func path(in rect: CGRect) -> Path {
        let tail: UIRectCorner = direction == .outgoing ? .bottomRight : .bottomLeft
        let rounded: UIRectCorner = [.topLeft, .topRight, .bottomLeft, .bottomRight].subtracting(tail)
        var p = Path(UIBezierPath(roundedRect: rect, byRoundingCorners: rounded,
                                  cornerRadii: CGSize(width: 16, height: 16)).cgPath)
        p.addPath(Path(UIBezierPath(roundedRect: rect, byRoundingCorners: tail,
                                    cornerRadii: CGSize(width: 4, height: 4)).cgPath))
        return p
    }
}
```

### Chat Navigation Bar

```swift
struct ChatNavBar: View {
    let name: String
    let threemaID: String
    let level: TrustLevel
    let onBack: () -> Void
    let onCall: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            Button(action: onBack) {
                Image(systemName: "chevron.left")
                    .font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(Color.trGreenBright)
            }
            Circle()
                .fill(LinearGradient(colors: [.trGreenBright, .trGreen],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 34, height: 34)
                .overlay(Text(String(name.prefix(2)).uppercased())
                    .font(.system(size: 14, weight: .bold)).foregroundStyle(.white))
            VStack(alignment: .leading, spacing: 1) {
                Text(name).font(.trNavName).foregroundStyle(Color.trDarkTextPrimary)
                HStack(spacing: 5) {
                    HStack(spacing: 3) {
                        ForEach(0..<3, id: \.self) { _ in
                            Circle().fill(level.color).frame(width: 7, height: 7)
                        }
                    }
                    Text("\(level.label) · \(threemaID)")
                        .font(.system(size: 11)).foregroundStyle(Color.trDarkTextSecondary)
                }
            }
            Spacer()
            Button(action: onCall) {
                Image(systemName: "phone")
                    .font(.system(size: 21))
                    .foregroundStyle(Color.trGreenBright)
            }
        }
        .padding(.horizontal, 14)
        .frame(height: 54)
        .background(Color.trDarkCanvas.opacity(0.96))
        .overlay(Rectangle().fill(Color.trDarkDivider).frame(height: 0.5), alignment: .bottom)
    }
}
```

### Primary Button

```swift
struct ThreemaPrimaryButton: View {
    let title: String
    let action: () -> Void
    @GestureState private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.trButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 14)
                .background(RoundedRectangle(cornerRadius: 10)
                    .fill(pressed ? Color.trGreenPressed : Color.trGreen))
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .updating($pressed) { _, s, _ in s = true })
        .sensoryFeedback(.impact(flexibility: .soft), trigger: pressed)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ThreemaTabView: View {
    var body: some View {
        TabView {
            ChatsView()
                .tabItem { Label("Chats", systemImage: "bubble.left.fill") }
                .badge(3)
            ContactsView().tabItem { Label("Contacts", systemImage: "person.2") }
            CallsView().tabItem { Label("Calls", systemImage: "phone") }
            MyIDView().tabItem { Label("My ID", systemImage: "qrcode") }
            SettingsView().tabItem { Label("Settings", systemImage: "gearshape") }
        }
        .tint(.trGreenBright)   // active tab = brand green (bright on dark)
    }
}
```

## 5. Motion

```swift
// Trust upgrade — crossfade red/orange → green on verification success
withAnimation(.easeOut(duration: 0.2)) { level = .verified }
// pair with: .sensoryFeedback(.success, trigger: level)

// QR reticle pulse (scanning loop)
withAnimation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true)) { pulse = true }

// Send message — bubble scale-in
withAnimation(.easeOut(duration: 0.18)) { appended = true }
// MessageBubble uses .transition(.opacity.combined(with: .move(edge: .bottom)))

// Sheet present (share ID / verify options)
.sheet(isPresented: $showShare) { ShareIDSheet() }   // native 300ms slide-up

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: sendTapped)   // send
.sensoryFeedback(.success, trigger: verifiedLevel)                   // verify success
.sensoryFeedback(.selection, trigger: selectedTab)                   // tab change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Chats (tab) | `bubble.left` / `bubble.left.fill` | 22pt |
| Contacts (tab) | `person.2` / `person.2.fill` | 22pt |
| Calls (tab) | `phone` / `phone.fill` | 22pt |
| My ID (tab) | `qrcode` | 22pt |
| Settings (tab) | `gearshape` / `gearshape.fill` | 22pt |
| Back | `chevron.left` | 22pt |
| Call (nav) | `phone` | 21pt |
| Send | `paperplane.fill` | 17pt |
| Attach (+) | `plus.circle` | 24pt |
| Copy ID | `doc.on.doc` | 15pt |
| Scan QR | `qrcode.viewfinder` | 22pt |
| Verified badge | `checkmark.seal.fill` | 18pt |
| Delivered / read | `checkmark` (single/double) | 9pt |
| Encrypted | `lock.fill` | 14pt |
| Block / revoke (destructive) | `nosign` / `trash` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Add contact | `person.badge.plus` | 20pt |

## 7. Dark Mode

```swift
struct ThreemaTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.trDarkCanvas : Color.trCanvas)
            .foregroundStyle(scheme == .dark ? Color.trDarkTextPrimary : Color.trTextPrimary)
            .tint(scheme == .dark ? Color.trGreenBright : Color.trGreen)
    }
}

extension View {
    func threemaTheme() -> some View { modifier(ThreemaTheme()) }
}

// Brand green resolves brighter on dark so it stays legible.
extension Color {
    static func trBrand(_ scheme: ColorScheme) -> Color {
        scheme == .dark ? .trGreenBright : .trGreen
    }
}
// Trust dots NEVER shift between schemes — verification state must read identically.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UINotificationFeedbackGenerator().notificationOccurred(.success)` for the verify haptic)
- No custom fonts to bundle — Threema uses SF Pro / SF Mono (system); enable Dynamic Type everywhere via `.system(size:weight:design:)` paired with `relativeTo:` text styles
- Dynamic Type: scale large titles, names, message body, captions; keep the trust-dot indicator, 10pt tab labels, and the monospaced Threema ID string at FIXED sizes (layout-sensitive)
- VoiceOver: trust state must never be color-only — `TrustDots` exposes a combined label ("Trust level: verified"); label bubbles "You said: {text}, read" / "{name} said: {text}"; label the QR card "Your Threema ID {id}, scan to verify"
- Color contrast: `#111113` on `#FFFFFF` and `#ECECEC` on `#121212` pass WCAG AA; the trust palette (red/orange/green) is always accompanied by a text label so it is not the sole information channel
- Reduce Motion: disable the QR reticle pulse and bubble slide; use a plain crossfade for the trust upgrade (keep the success haptic)
- Dark mode: invert via the `trDark*` palette — `#121212`, NOT true black; brand green brightens to `#1FA53C`; shadows nearly vanish so floating panels add a 0.5pt `trDarkDivider` border
- Privacy: the Threema ID is the only identifier — never request or display a phone number or email as account identity; the monospaced ID + fingerprint must remain legible at the user's chosen text size
