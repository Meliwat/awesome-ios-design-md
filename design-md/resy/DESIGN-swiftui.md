# Resy (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Resy's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Resy is dark-first — the canvas is pure black.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — primary brand)
    static let resyBlack      = Color.black                                   // #000000
    static let resySurface1   = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let resySurface2   = Color(red: 0.110, green: 0.110, blue: 0.110) // #1C1C1C
    static let resySurface3   = Color(red: 0.141, green: 0.141, blue: 0.141) // #242424
    static let resyDivider    = Color(red: 0.149, green: 0.149, blue: 0.149) // #262626

    // MARK: - Canvas & Surfaces (Light — secondary inversion)
    static let resyCanvasLight   = Color.white                                 // #FFFFFF
    static let resySurfaceGray   = Color(red: 0.957, green: 0.957, blue: 0.957) // #F4F4F4
    static let resySurfacePressed = Color(red: 0.918, green: 0.918, blue: 0.918) // #EAEAEA
    static let resyDividerLight   = Color(red: 0.886, green: 0.886, blue: 0.886) // #E2E2E2

    // MARK: - Text
    static let resyTextPrimary    = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let resyTextSecondary  = Color(red: 0.620, green: 0.620, blue: 0.620) // #9E9E9E
    static let resyTextTertiary   = Color(red: 0.416, green: 0.416, blue: 0.416) // #6A6A6A
    static let resyTextPrimaryLt  = Color(red: 0.063, green: 0.063, blue: 0.063) // #101010
    static let resyTextSecondaryLt = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B

    // MARK: - Brand
    static let resyRed         = Color(red: 0.780, green: 0.243, blue: 0.227) // #C73E3A
    static let resyRedBright   = Color(red: 0.886, green: 0.314, blue: 0.294) // #E2504B
    static let resyRedPressed  = Color(red: 0.659, green: 0.188, blue: 0.176) // #A8302D

    // MARK: - Functional Accents
    static let resyNotifyAmber = Color(red: 0.851, green: 0.604, blue: 0.169) // #D99A2B
    static let resyHitGold     = Color(red: 0.788, green: 0.635, blue: 0.294) // #C9A24B
    static let resyConfirmed   = Color(red: 0.310, green: 0.655, blue: 0.451) // #4FA773

    // MARK: - Semantic
    static let resyError       = Color(red: 0.886, green: 0.314, blue: 0.294) // #E2504B
}
```

## 2. Typography

Resy pairs a high-contrast display serif (Playfair Display, the closest free analog to Resy's brand serif) with Inter for UI. Bundle both via `Info.plist` / `UIAppFonts`. The serif/sans split is the brand.

```swift
extension Font {
    // Editorial / Display — Playfair Display (serif)
    static let resyDisplayTitle = Font.custom("PlayfairDisplay-ExtraBold", size: 34)
    static let resyRestName     = Font.custom("PlayfairDisplay-Bold",      size: 27)
    static func resyEditorial(_ size: CGFloat = 15) -> Font {
        Font.custom("PlayfairDisplay-Italic", size: size)   // italic tagline
    }

    // UI / Body — Inter (sans)
    static let resySection   = Font.custom("Inter-Bold",     size: 22)
    static let resyCardTitle = Font.custom("Inter-SemiBold", size: 18)
    static let resyBody      = Font.custom("Inter-Regular",  size: 16)
    static let resyMeta      = Font.custom("Inter-Regular",  size: 14)
    static let resySlotTime  = Font.custom("Inter-Bold",     size: 14)
    static let resySeating   = Font.custom("Inter-SemiBold", size: 9)
    static let resyEyebrow   = Font.custom("Inter-Bold",     size: 12)
    static let resyButton    = Font.custom("Inter-Bold",     size: 16)
    static let resyTab       = Font.custom("Inter-SemiBold", size: 10)
    static let resyCaption   = Font.custom("Inter-Medium",   size: 12)
}
```

## 3. Signature Components

### Reservation Slot Grid (red-outline system)

```swift
enum SlotKind { case available, primary, notify }

struct ResySlot: Identifiable {
    let id = UUID()
    let label: String      // "6:30" or "Notify"
    let seating: String    // "DINING ROOM" / "7–8 PM"
    let kind: SlotKind
}

struct SlotGrid: View {
    let slots: [ResySlot]
    @Binding var selectedID: UUID?
    let columns = Array(repeating: GridItem(.flexible(), spacing: 9), count: 4)

    var body: some View {
        LazyVGrid(columns: columns, spacing: 9) {
            ForEach(slots) { s in
                let isSel = s.id == selectedID
                let solid = s.kind == .primary || (isSel && s.kind == .available)
                VStack(spacing: 3) {
                    Text(s.label)
                        .font(s.kind == .notify ? .custom("Inter-Bold", size: 12) : .resySlotTime)
                        .foregroundStyle(solid ? .white : (s.kind == .notify ? Color.resyNotifyAmber : Color.resyTextPrimary))
                    Text(s.seating)
                        .font(.resySeating).textCase(.uppercase).tracking(0.3)
                        .foregroundStyle(solid ? .white.opacity(0.82) :
                                         (s.kind == .notify ? Color.resyNotifyAmber : Color.resyTextSecondary))
                }
                .frame(maxWidth: .infinity, minHeight: 56)
                .background(RoundedRectangle(cornerRadius: 10).fill(solid ? Color.resyRed : .clear))
                .overlay(
                    RoundedRectangle(cornerRadius: 10)
                        .strokeBorder(
                            solid ? .clear : (s.kind == .notify ? Color.resyDivider : Color.resyRed),
                            style: StrokeStyle(lineWidth: 1, dash: s.kind == .notify ? [4, 3] : [])
                        )
                )
                .contentShape(Rectangle())
                .onTapGesture {
                    if s.kind != .notify { withAnimation(.easeOut(duration: 0.15)) { selectedID = s.id } }
                }
                .sensoryFeedback(.impact(flexibility: .soft), trigger: selectedID)
            }
        }
    }
}
```

### Reservation Date Strip

```swift
struct DateCell: Identifiable { let id = UUID(); let dow: String; let num: String }

struct DateStrip: View {
    let days: [DateCell]
    @Binding var selectedID: UUID?

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(days) { d in
                    let sel = d.id == selectedID
                    VStack(spacing: 3) {
                        Text(d.dow).font(.custom("Inter-SemiBold", size: 10))
                            .textCase(.uppercase).tracking(0.5)
                            .foregroundStyle(sel ? .white : Color.resyTextSecondary)
                        Text(d.num).font(.custom("Inter-Bold", size: 17))
                            .foregroundStyle(sel ? .white : Color.resyTextPrimary)
                    }
                    .frame(minWidth: 56, minHeight: 60)
                    .background(RoundedRectangle(cornerRadius: 10).fill(sel ? Color.resyRed : Color.resySurface1))
                    .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(sel ? .clear : Color.resyDivider, lineWidth: 1))
                    .onTapGesture { withAnimation(.easeOut(duration: 0.15)) { selectedID = d.id } }
                }
            }
        }
    }
}
```

### Editorial Restaurant Header

```swift
struct RestaurantHeader: View {
    let name: String
    let rating: String      // "9.4"
    let meta: String        // "Italian · $$$ · Williamsburg"
    let tagline: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text(name)
                .font(.resyRestName)
                .foregroundStyle(Color.resyTextPrimary)
            HStack(spacing: 8) {
                Text("★ \(rating)")
                    .font(.custom("Inter-Bold", size: 13))
                    .foregroundStyle(Color.resyHitGold)
                Text("·").foregroundStyle(Color.resyTextTertiary)
                Text(meta).font(.resyMeta).foregroundStyle(Color.resyTextSecondary)
            }
            .padding(.top, 9)
            Text(tagline)
                .font(.resyEditorial(15))
                .foregroundStyle(Color.resyTextSecondary)
                .padding(.top, 12)
        }
    }
}
```

### Notify Chip

```swift
struct NotifyChip: View {
    @Binding var onList: Bool
    var body: some View {
        Button {
            withAnimation(.easeOut(duration: 0.15)) { onList.toggle() }
        } label: {
            HStack(spacing: 7) {
                Image(systemName: "bell.fill").font(.system(size: 12))
                Text(onList ? "On the Notify list" : "Notify when available")
                    .font(.custom("Inter-Bold", size: 13))
            }
            .foregroundStyle(onList ? .white : Color.resyNotifyAmber)
            .padding(.vertical, 8).padding(.horizontal, 16)
            .background(Capsule().fill(onList ? Color.resyNotifyAmber : .clear))
            .overlay(Capsule().strokeBorder(Color.resyNotifyAmber, lineWidth: onList ? 0 : 1))
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(flexibility: .soft), trigger: onList)
    }
}
```

### Primary Button

```swift
struct ResyPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.resyButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 16)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.resyRed))
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.success, trigger: title)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ResyTabView: View {
    var body: some View {
        TabView {
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            HitListView().tabItem { Label("Hit List", systemImage: "list.star") }
            ReservationsView().tabItem { Label("Reservations", systemImage: "calendar") }
            NotifyView().tabItem { Label("Notify", systemImage: "bell") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.resyRedBright)        // bright red for contrast on pure black
        .preferredColorScheme(.dark) // Resy is dark-first
    }
}
```

## 5. Motion

```swift
// Slot select — outlined → solid red + soft haptic; Book button updates
withAnimation(.easeOut(duration: 0.15)) { selectedID = slot.id }
.sensoryFeedback(.impact(flexibility: .soft), trigger: selectedID)

// Date strip — fill red 150ms; slot grid cross-fades to that day
withAnimation(.easeOut(duration: 0.15)) { selectedDay = day.id }

// Slot grid load — staggered fade-in + 4pt slide-up
.transition(.opacity.combined(with: .move(edge: .bottom)))
// .animation(.easeOut(duration: 0.2).delay(Double(i) * 0.03), value: loaded)

// Notify toggle — dashed → amber fill 150ms + haptic
withAnimation(.easeOut(duration: 0.15)) { onList.toggle() }

// Restaurant detail open — iOS push; serif name fades up (matchedGeometryEffect for photo)
@Namespace private var heroNS

// Guest / calendar sheet
.sheet(isPresented: $showGuests) { GuestSheet().presentationDetents([.medium]) }

// Reservation confirm
.sensoryFeedback(.success, trigger: reservationConfirmed)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search (tab) | `magnifyingglass` | 21pt |
| Hit List (tab) | `list.star` / `bookmark.fill` | 21pt |
| Reservations (tab) | `calendar` | 21pt |
| Notify (tab) | `bell` / `bell.fill` | 21pt |
| Account (tab) | `person.crop.circle` | 21pt |
| Back | `chevron.left` | 16pt |
| Hit List (save) | `heart` / `heart.fill` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Rating star | `star.fill` | 13pt |
| Notify bell | `bell.fill` | 12pt |
| Guests | `person.2.fill` | 14pt |
| Date | `calendar` | 14pt |
| Daypart | `sun.max` / `moon.stars` | 14pt |
| Confirmed | `checkmark.circle.fill` | 22pt |
| Menu | `menucard` | 14pt |
| Location | `mappin.and.ellipse` | 14pt |
| Carousel page | `circle.fill` (dots) | 6pt |

## 7. Dark Mode

```swift
// Resy is dark-first — apply .preferredColorScheme(.dark) at the root.
// Light is a secondary inversion (white canvas, #101010 text, #F4F4F4 surfaces).

struct ResyTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme   // .dark is the brand default

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.resyBlack : Color.resyCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.resyTextPrimary : Color.resyTextPrimaryLt)
    }
}

extension View { func resyTheme() -> some View { modifier(ResyTheme()) } }

// On pure black, drop-shadows are invisible — use a 0.5–1pt Color.resyDivider
// border on sheets/floating panels as the elevation cue. Use Color.resyRedBright
// for the active tab so it reads against #000000.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Inter and Playfair Display TTFs via `Info.plist` `UIAppFonts` — both are SIL OFL licensed for free distribution
- Dynamic Type: support on display titles, restaurant names (serif), body, metadata; keep tab labels, seating labels, slot-time text at FIXED sizes (the slot grid is layout-sensitive — let chips wrap)
- VoiceOver: label slot chips "{time}, {seating}, available — double-tap to select" / "{time} primary slot" / "Sold out — Notify me for {window}"; the Notify chip toggles "Add to Notify list" / "On the Notify list"; the rating reads "Rated {n} out of 10"
- Color contrast: `#F5F5F5` on `#000000` passes WCAG AAA; white on `#C73E3A` passes AA for the Book button; the red `#C73E3A` slot border on black and amber `#D99A2B` Notify text are functional — verify any small text size for AA. Active tab uses brighter `#E2504B` for legibility on pure black
- Reduce Motion: disable the slot-select scale bounce and the staggered grid fade-in (snap chips); keep the fill color change (it conveys selection)
- Dark mode: this IS the brand — pure black `#000000`, near-white `#F5F5F5` text, serif restaurant names. The light inversion is secondary; on black, hairline `#262626` borders are the elevation cue since shadows are invisible
