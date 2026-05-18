# OpenTable (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates OpenTable's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let otCanvas        = Color.white                                   // #FFFFFF
    static let otSurfaceGray   = Color(red: 0.965, green: 0.965, blue: 0.969) // #F6F6F7
    static let otSurfaceRaised = Color(red: 0.984, green: 0.984, blue: 0.988) // #FBFBFC
    static let otSurfacePressed = Color(red: 0.925, green: 0.925, blue: 0.933) // #ECECEE
    static let otDivider       = Color(red: 0.894, green: 0.894, blue: 0.906) // #E4E4E7

    // MARK: - Canvas & Surfaces (Dark)
    static let otDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let otDarkSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let otDarkSurface2  = Color(red: 0.149, green: 0.149, blue: 0.161) // #262629
    static let otDarkDivider   = Color(red: 0.180, green: 0.180, blue: 0.192) // #2E2E31

    // MARK: - Text
    static let otTextPrimary    = Color(red: 0.102, green: 0.102, blue: 0.110) // #1A1A1C
    static let otTextSecondary  = Color(red: 0.420, green: 0.420, blue: 0.439) // #6B6B70
    static let otTextTertiary   = Color(red: 0.604, green: 0.604, blue: 0.624) // #9A9A9F
    static let otDarkTextPrimary = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED
    static let otDarkTextSecondary = Color(red: 0.635, green: 0.635, blue: 0.651) // #A2A2A6

    // MARK: - Brand
    static let otRed         = Color(red: 0.855, green: 0.216, blue: 0.263) // #DA3743
    static let otRedBright   = Color(red: 0.949, green: 0.329, blue: 0.357) // #F2545B
    static let otRedPressed  = Color(red: 0.725, green: 0.173, blue: 0.216) // #B92C37
    static let otRedTint     = Color(red: 0.988, green: 0.922, blue: 0.925) // #FCEBEC

    // MARK: - Functional Accents
    static let otGoldStar    = Color(red: 0.910, green: 0.639, blue: 0.239) // #E8A33D
    static let otPointsTeal  = Color(red: 0.122, green: 0.541, blue: 0.541) // #1F8A8A
    static let otDinerGreen  = Color(red: 0.184, green: 0.659, blue: 0.416) // #2FA86A
    static let otNotifyAmber = Color(red: 0.878, green: 0.541, blue: 0.184) // #E08A2F

    // MARK: - Semantic
    static let otSuccess     = Color(red: 0.184, green: 0.659, blue: 0.416) // #2FA86A
    static let otError       = Color(red: 0.839, green: 0.204, blue: 0.184) // #D6342F
    static let otErrorDark   = Color(red: 0.949, green: 0.329, blue: 0.357) // #F2545B
}
```

## 2. Typography

OpenTable uses a custom grotesque sans ("OpenTable Sans"); Inter is the closest free analog. Bundle Inter via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let otScreenTitle = Font.custom("Inter-ExtraBold", size: 32)
    static let otRestName    = Font.custom("Inter-ExtraBold", size: 26)
    static let otSection     = Font.custom("Inter-Bold",      size: 22)
    static let otCardTitle   = Font.custom("Inter-Bold",      size: 18)
    static let otBody        = Font.custom("Inter-Regular",   size: 16)
    static let otListSub     = Font.custom("Inter-SemiBold",  size: 15)
    static let otMeta        = Font.custom("Inter-Regular",   size: 14)
    static let otSlotTime    = Font.custom("Inter-Bold",      size: 14)
    static let otPointsTag   = Font.custom("Inter-Bold",      size: 12)
    static let otButton      = Font.custom("Inter-Bold",      size: 16)
    static let otPill        = Font.custom("Inter-SemiBold",  size: 13)
    static let otTab         = Font.custom("Inter-SemiBold",  size: 10)
    static let otCaption     = Font.custom("Inter-Medium",    size: 12)
}
```

## 3. Signature Components

### Reservation Time-Slot Grid

```swift
struct TimeSlot: Identifiable {
    let id = UUID()
    let time: String      // "7:00"
    let points: Int?      // 1000 -> "+1,000"
    let recommended: Bool
}

struct SlotGrid: View {
    let slots: [TimeSlot]
    @Binding var selectedID: UUID?
    let columns = [GridItem(.flexible()), GridItem(.flexible()),
                   GridItem(.flexible()), GridItem(.flexible())]

    var body: some View {
        LazyVGrid(columns: columns, spacing: 9) {
            ForEach(slots) { slot in
                let selected = slot.id == selectedID || (selectedID == nil && slot.recommended)
                Button {
                    withAnimation(.easeOut(duration: 0.15)) { selectedID = slot.id }
                } label: {
                    VStack(spacing: 2) {
                        Text(slot.time)
                            .font(.otSlotTime)
                            .foregroundStyle(selected ? .white : Color.otTextPrimary)
                        if let pts = slot.points {
                            Text("+\(pts.formatted())")
                                .font(.custom("Inter-Bold", size: 9))
                                .foregroundStyle(selected ? .white.opacity(0.85) : Color.otPointsTeal)
                        }
                    }
                    .frame(maxWidth: .infinity, minHeight: 52)
                    .background(
                        RoundedRectangle(cornerRadius: 10)
                            .fill(selected ? Color.otRed : Color.otSurfaceGray)
                    )
                    .overlay(
                        RoundedRectangle(cornerRadius: 10)
                            .strokeBorder(selected ? .clear : Color.otDivider, lineWidth: 1)
                    )
                }
                .buttonStyle(.plain)
                .scaleEffect(selected ? 1.0 : 1.0)
                .sensoryFeedback(.impact(flexibility: .soft), trigger: selectedID)
            }
        }
    }
}
```

### Star Rating

```swift
struct StarRating: View {
    let rating: Double      // 4.7
    let count: Int          // 412

    var body: some View {
        HStack(spacing: 4) {
            HStack(spacing: 1) {
                ForEach(0..<5) { i in
                    Image(systemName: Double(i) + 0.5 < rating ? "star.fill" :
                          (Double(i) < rating ? "star.leadinghalf.filled" : "star"))
                        .font(.system(size: 13))
                        .foregroundStyle(Color.otGoldStar)
                }
            }
            Text(String(format: "%.1f", rating))
                .font(.otMeta.weight(.semibold)).foregroundStyle(Color.otTextPrimary)
            Text("(\(count))")
                .font(.otMeta).foregroundStyle(Color.otTextSecondary)
        }
    }
}
```

### Social-Proof Pill

```swift
struct BookedPill: View {
    let count: Int
    var body: some View {
        HStack(spacing: 5) {
            Text("🔥")
            Text("Booked \(count) times today")
                .font(.custom("Inter-Bold", size: 11))
        }
        .foregroundStyle(Color.otDinerGreen)
        .padding(.vertical, 4).padding(.horizontal, 10)
        .background(Capsule().fill(Color.otDinerGreen.opacity(0.16)))
    }
}
```

### Party-Size / Date Selector Chip

```swift
struct SelectorChip: View {
    let systemImage: String
    let label: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: systemImage)
                    .font(.system(size: 14))
                    .foregroundStyle(Color.otTextSecondary)
                Text(label).font(.otPill).foregroundStyle(Color.otTextPrimary)
            }
            .frame(maxWidth: .infinity, minHeight: 40)
            .background(Capsule().fill(Color.otSurfaceGray))
            .overlay(Capsule().strokeBorder(Color.otDivider, lineWidth: 1))
        }
        .buttonStyle(.plain)
    }
}
```

### Sticky Reserve Bar

```swift
struct ReserveBar: View {
    let timeLabel: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text("Reserve for \(timeLabel)")
                .font(.otButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).frame(height: 50)
                .background(Capsule().fill(Color.otRed))
        }
        .buttonStyle(.plain)
        .padding(.horizontal, 16).padding(.vertical, 12)
        .background(.ultraThinMaterial)
        .overlay(alignment: .top) { Rectangle().fill(Color.otDivider).frame(height: 0.5) }
        .shadow(color: .black.opacity(0.10), radius: 16, y: -2)
        .sensoryFeedback(.success, trigger: timeLabel)
    }
}
```

### Points / Rewards Chip

```swift
struct PointsChip: View {
    let text: String
    var body: some View {
        HStack(spacing: 7) {
            Image(systemName: "star.fill").font(.system(size: 12))
            Text(text).font(.custom("Inter-Bold", size: 13))
        }
        .foregroundStyle(Color.otPointsTeal)
        .padding(.vertical, 7).padding(.horizontal, 14)
        .background(Capsule().fill(Color.otPointsTeal.opacity(0.16)))
        .overlay(Capsule().strokeBorder(Color.otPointsTeal.opacity(0.4), lineWidth: 1))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct OpenTableTabView: View {
    var body: some View {
        TabView {
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            SavedView().tabItem { Label("Saved", systemImage: "heart") }
            ReservationsView().tabItem { Label("Reservations", systemImage: "calendar") }
            RewardsView().tabItem { Label("Rewards", systemImage: "star") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.otRed)   // active red; bright variant in dark mode via asset color
    }
}
```

## 5. Motion

```swift
// Slot select — fill to red + soft haptic; Reserve bar label updates
withAnimation(.easeOut(duration: 0.15)) { selectedID = slot.id }
.sensoryFeedback(.impact(flexibility: .soft), trigger: selectedID)

// Slot grid load — staggered fade-in + 4pt slide-up
.transition(.opacity.combined(with: .move(edge: .bottom)))
// .animation(.easeOut(duration: 0.2).delay(Double(index) * 0.03), value: loaded)

// Filter chip toggle — cross-fade border/fill 150ms
withAnimation(.easeOut(duration: 0.15)) { isSelected.toggle() }

// Restaurant detail open — iOS push (matchedGeometryEffect for hero photo)
@Namespace private var heroNS

// Party/date sheet
.sheet(isPresented: $showPartySheet) { PartySheet().presentationDetents([.medium]) }

// Reserve confirm
.sensoryFeedback(.success, trigger: reservationConfirmed)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Discover (tab) | `magnifyingglass` | 21pt |
| Saved (tab) | `heart` / `heart.fill` | 21pt |
| Reservations (tab) | `calendar` | 21pt |
| Rewards (tab) | `star` / `star.fill` | 21pt |
| Profile (tab) | `person.crop.circle` | 21pt |
| Back | `chevron.left` | 16pt |
| Save (heart) | `heart` / `heart.fill` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Review star | `star.fill` / `star.leadinghalf.filled` | 13pt |
| Points | `star.fill` | 12–13pt |
| Party size | `person.2.fill` | 14pt |
| Date | `calendar` | 14pt |
| Time | `clock` | 14pt |
| Notify / waitlist | `bell` | 16pt |
| Location | `mappin.and.ellipse` | 14pt |
| Menu | `menucard` | 14pt |
| Confirmed | `checkmark.circle.fill` | 22pt |

## 7. Dark Mode

```swift
struct OpenTableTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.otDarkCanvas : Color.otCanvas)
            .foregroundStyle(scheme == .dark ? Color.otDarkTextPrimary : Color.otTextPrimary)
    }
}

extension View { func openTableTheme() -> some View { modifier(OpenTableTheme()) } }

// Use Color.otRedBright for the Reserve CTA / active tab / selected slot in dark mode —
// Color.otRed is slightly muted on #121212. Define an asset-catalog Color
// "OTAction" = #DA3743 (light) / #F2545B (dark) and use it for all booking actions.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Inter TTFs (or licensed OpenTable Sans) via `Info.plist` `UIAppFonts` — Inter is SIL OFL licensed for free distribution
- Dynamic Type: support on screen titles, restaurant names, body, metadata; keep tab labels, points tags, slot-time text at FIXED sizes (the slot grid is layout-sensitive — let chips wrap, not stretch)
- VoiceOver: label slot chips as "{time}, earns {points} points, double-tap to select"; label the Reserve bar "Reserve a table for {time}"; the star rating reads "{rating} stars, {count} reviews"; the booked pill reads "Booked {count} times today"
- Color contrast: `#1A1A1C` on `#FFFFFF` passes WCAG AA; white text on `#DA3743` passes AA for the Reserve button; teal `#1F8A8A` on its 16% tint and gold `#E8A33D` star are decorative/iconographic — pair points text with sufficient contrast. On dark, use `#F2545B` so the CTA holds contrast on `#121212`
- Reduce Motion: disable the slot-select scale bounce and the staggered grid fade-in (snap chips in); keep the fill color change (it conveys selection state)
- Dark mode: invert per the `otDark*` palette; clean charcoal `#121212`, NOT pure black; the action color becomes `#F2545B`; restaurant photography stays full-color
