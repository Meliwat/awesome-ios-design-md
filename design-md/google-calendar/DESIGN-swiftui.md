# Google Calendar (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Google Calendar's Material-on-iOS visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, themed components for the event card, Schedule day banner, FAB, and Month grid.

Targets iOS 17+ for `.sensoryFeedback` and `matchedGeometryEffect`. The Material Level 6 dual-shadow uses two stacked `.shadow` modifiers.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let gcalCanvas       = Color(red: 1.00, green: 1.00, blue: 1.00)    // #FFFFFF
    static let gcalSurfaceGray  = Color(red: 0.945, green: 0.953, blue: 0.957) // #F1F3F4
    static let gcalSurfaceGray2 = Color(red: 0.973, green: 0.976, blue: 0.980) // #F8F9FA
    static let gcalDivider      = Color(red: 0.855, green: 0.863, blue: 0.878) // #DADCE0

    // MARK: - Text
    static let gcalInk          = Color(red: 0.125, green: 0.129, blue: 0.141) // #202124
    static let gcalSecondary    = Color(red: 0.373, green: 0.388, blue: 0.408) // #5F6368
    static let gcalTertiary     = Color(red: 0.502, green: 0.525, blue: 0.545) // #80868B

    // MARK: - Brand & Actions
    static let gcalBlue         = Color(red: 0.102, green: 0.451, blue: 0.910) // #1A73E8 primary
    static let gcalBluePressed  = Color(red: 0.082, green: 0.341, blue: 0.690) // #1557B0
    static let gcalBlueTint     = Color(red: 0.910, green: 0.941, blue: 0.996) // #E8F0FE selected
    static let gcalBlueDark     = Color(red: 0.541, green: 0.706, blue: 0.973) // #8AB4F8 dark mode

    // MARK: - Event Colors (Material Primary Set)
    static let gcalEventBlue    = Color(red: 0.102, green: 0.451, blue: 0.910) // #1A73E8
    static let gcalEventRed     = Color(red: 0.851, green: 0.188, blue: 0.145) // #D93025
    static let gcalEventYellow  = Color(red: 0.976, green: 0.671, blue: 0.00)  // #F9AB00
    static let gcalEventGreen   = Color(red: 0.094, green: 0.502, blue: 0.220) // #188038

    // MARK: - 24-Color Calendar Palette (user-selectable)
    static let gcalTomato       = Color(red: 0.835, green: 0.00,  blue: 0.00)  // #D50000
    static let gcalFlamingo     = Color(red: 0.902, green: 0.486, blue: 0.451) // #E67C73
    static let gcalTangerine    = Color(red: 0.957, green: 0.318, blue: 0.118) // #F4511E
    static let gcalBanana       = Color(red: 0.965, green: 0.749, blue: 0.149) // #F6BF26
    static let gcalSage         = Color(red: 0.200, green: 0.714, blue: 0.475) // #33B679
    static let gcalBasil        = Color(red: 0.043, green: 0.502, blue: 0.263) // #0B8043
    static let gcalPeacock      = Color(red: 0.012, green: 0.608, blue: 0.898) // #039BE5
    static let gcalBlueberry    = Color(red: 0.247, green: 0.318, blue: 0.710) // #3F51B5
    static let gcalLavender     = Color(red: 0.475, green: 0.525, blue: 0.796) // #7986CB
    static let gcalGrape        = Color(red: 0.557, green: 0.141, blue: 0.667) // #8E24AA
    static let gcalGraphite     = Color(red: 0.380, green: 0.380, blue: 0.380) // #616161

    // MARK: - Dark mode
    static let gcalDarkCanvas   = Color(red: 0.125, green: 0.129, blue: 0.141) // #202124
    static let gcalDarkSurface  = Color(red: 0.176, green: 0.180, blue: 0.188) // #2D2E30
    static let gcalDarkSurface2 = Color(red: 0.235, green: 0.251, blue: 0.263) // #3C4043
    static let gcalDarkText     = Color(red: 0.910, green: 0.918, blue: 0.929) // #E8EAED
    static let gcalDarkTextSec  = Color(red: 0.604, green: 0.627, blue: 0.651) // #9AA0A6
}
```

## 2. Typography

Google Sans is publicly available via Google Fonts (Google Sans family). Bundle the TTFs via `Info.plist` `UIAppFonts`. On iOS, body text falls back to SF Pro Text by deliberate design choice.

```swift
extension Font {
    // Nav titles (Google Sans Display)
    static let gcalNavTitle      = Font.custom("GoogleSans-Medium",  size: 22)
    static let gcalDayNumberLg   = Font.custom("GoogleSans-Regular", size: 36)
    static let gcalSectionHdr    = Font.custom("GoogleSans-Medium",  size: 14)
    static let gcalDayLabel      = Font.custom("GoogleSans-Medium",  size: 13)

    // Event detail
    static let gcalEventDetail   = Font.custom("GoogleSans-Regular", size: 22)

    // Event card body (SF Pro Text on iOS — system fallback)
    static let gcalEventTitle    = Font.system(size: 14, weight: .medium, design: .default)
    static let gcalEventTime     = Font.system(size: 13, weight: .regular, design: .default).monospacedDigit()
    static let gcalEventLocation = Font.system(size: 13, weight: .regular, design: .default)

    // Tinted block (Day/Week view)
    static let gcalBlockTitle    = Font.system(size: 12, weight: .medium, design: .default)
    static let gcalBlockTime     = Font.system(size: 11, weight: .regular, design: .default).monospacedDigit()

    // Month grid
    static let gcalDayNumber     = Font.custom("GoogleSans-Regular", size: 14).monospacedDigit()
    static let gcalTodayNumber   = Font.custom("GoogleSans-Medium",  size: 14)
    static let gcalWeekdayHdr    = Font.system(size: 12, weight: .medium, design: .default)

    // Time gutter
    static let gcalTimeGutter    = Font.system(size: 11, weight: .regular, design: .default).monospacedDigit()

    // Sidebar / Drawer
    static let gcalSidebar       = Font.system(size: 14, weight: .medium, design: .default)

    // Buttons (Google Sans Medium, tracked)
    static let gcalButton        = Font.custom("GoogleSans-Medium", size: 14)

    // Misc
    static let gcalTabLabel      = Font.system(size: 10, weight: .medium, design: .default)
    static let gcalCaption       = Font.system(size: 12, weight: .regular, design: .default)
}
```

## 3. Signature Components

### Event Card (Schedule View — Hero Component)

```swift
struct EventCard: View {
    let title: String
    let timeRange: String       // "9:00 – 9:30 AM"
    let location: String?
    let calendarColor: Color    // e.g., .gcalBlueberry
    var onTap: () -> Void = {}

    var body: some View {
        Button(action: onTap) {
            HStack(alignment: .top, spacing: 0) {
                // 4pt left color bar
                Rectangle()
                    .fill(calendarColor)
                    .frame(width: 4)

                VStack(alignment: .leading, spacing: 4) {
                    Text(title)
                        .font(.gcalEventTitle)
                        .foregroundStyle(Color.gcalInk)
                        .lineLimit(2)

                    Text(timeRange)
                        .font(.gcalEventTime)
                        .foregroundStyle(Color.gcalSecondary)

                    if let location = location {
                        HStack(spacing: 4) {
                            Image(systemName: "mappin.and.ellipse")
                                .font(.system(size: 11))
                                .foregroundStyle(Color.gcalSecondary)
                            Text(location)
                                .font(.gcalEventLocation)
                                .foregroundStyle(Color.gcalSecondary)
                                .lineLimit(1)
                        }
                    }
                }
                .padding(.horizontal, 16)
                .padding(.vertical, 12)

                Spacer()
            }
            .frame(minHeight: 56)
            .background(
                RoundedRectangle(cornerRadius: 4, style: .continuous)
                    .fill(Color.gcalCanvas)
            )
            .shadow(color: Color(red: 0.235, green: 0.251, blue: 0.263).opacity(0.10), radius: 1, y: 1)
            .shadow(color: Color(red: 0.235, green: 0.251, blue: 0.263).opacity(0.15), radius: 3, y: 1)
        }
        .buttonStyle(.plain)
    }
}
```

### Schedule Day Banner

```swift
struct DayBanner: View {
    let dayNumber: String       // "14"
    let dayLabel: String        // "THU · MAY"
    let weatherSymbol: String?  // "sun.max.fill"
    let temperature: String?    // "72°"
    let isToday: Bool

    var body: some View {
        HStack(spacing: 16) {
            // Day number + label
            VStack(alignment: .leading, spacing: 4) {
                Text(dayLabel)
                    .font(.gcalDayLabel)
                    .tracking(0.8)
                    .foregroundStyle(Color.gcalSecondary)

                if isToday {
                    ZStack {
                        Circle()
                            .fill(Color.gcalBlue)
                            .frame(width: 56, height: 56)
                        Text(dayNumber)
                            .font(.gcalDayNumberLg)
                            .foregroundStyle(.white)
                    }
                } else {
                    Text(dayNumber)
                        .font(.gcalDayNumberLg)
                        .foregroundStyle(Color.gcalInk)
                }
            }

            Spacer()

            if let symbol = weatherSymbol, let temp = temperature {
                HStack(spacing: 8) {
                    Image(systemName: symbol)
                        .font(.system(size: 20))
                        .foregroundStyle(Color.gcalEventYellow)
                    Text(temp)
                        .font(.system(size: 14, weight: .medium))
                        .foregroundStyle(Color.gcalInk)
                }
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 80)
    }
}
```

### Floating Action Button (Material Level 6)

```swift
struct GcalFAB: View {
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: {
            action()
        }) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .regular))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(
                    Circle().fill(Color.gcalBlue)
                )
                // Material Level 6 dual shadow
                .shadow(color: Color(red: 0.235, green: 0.251, blue: 0.263).opacity(pressed ? 0.30 : 0.15),
                        radius: pressed ? 6 : 4, y: pressed ? 6 : 4)
                .shadow(color: Color(red: 0.235, green: 0.251, blue: 0.263).opacity(pressed ? 0.40 : 0.30),
                        radius: pressed ? 2 : 1, y: pressed ? 2 : 1)
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .pressEvents(onPress: { pressed = true }, onRelease: { pressed = false })
    }
}

extension View {
    func pressEvents(onPress: @escaping () -> Void, onRelease: @escaping () -> Void) -> some View {
        simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in onPress() }
                .onEnded   { _ in onRelease() }
        )
    }
}
```

### Month Grid Cell

```swift
struct MonthCell: View {
    let day: Int
    let isToday: Bool
    let isCurrentMonth: Bool
    let events: [Color] // up to 3 event-source colors as small dots

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            HStack {
                if isToday {
                    ZStack {
                        Circle().fill(Color.gcalBlue).frame(width: 28, height: 28)
                        Text("\(day)")
                            .font(.gcalTodayNumber)
                            .foregroundStyle(.white)
                    }
                } else {
                    Text("\(day)")
                        .font(.gcalDayNumber)
                        .foregroundStyle(isCurrentMonth ? Color.gcalInk : Color.gcalTertiary)
                        .padding(.leading, 4)
                        .padding(.top, 4)
                }
                Spacer()
            }

            HStack(spacing: 2) {
                ForEach(events.prefix(3), id: \.description) { color in
                    Circle().fill(color).frame(width: 4, height: 4)
                }
                if events.count > 3 {
                    Text("+\(events.count - 3)")
                        .font(.system(size: 9, weight: .medium))
                        .foregroundStyle(Color.gcalTertiary)
                }
            }
            .padding(.leading, 4)

            Spacer(minLength: 0)
        }
        .frame(maxWidth: .infinity, minHeight: 52, alignment: .topLeading)
        .background(Color.gcalCanvas)
        .overlay(alignment: .top) {
            Rectangle().fill(Color.gcalDivider).frame(height: 0.5)
        }
    }
}
```

### Day / Week Tinted Event Block

```swift
struct TintedEventBlock: View {
    let title: String
    let timeRange: String
    let calendarColor: Color
    let heightPt: CGFloat   // determined by event duration

    var body: some View {
        HStack(spacing: 0) {
            Rectangle()
                .fill(calendarColor)
                .frame(width: 3)

            VStack(alignment: .leading, spacing: 2) {
                Text(title)
                    .font(.gcalBlockTitle)
                    .foregroundStyle(calendarColor)
                    .lineLimit(1)
                if heightPt > 32 {
                    Text(timeRange)
                        .font(.gcalBlockTime)
                        .foregroundStyle(calendarColor.opacity(0.85))
                }
            }
            .padding(.horizontal, 6)
            .padding(.vertical, 4)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .frame(height: heightPt, alignment: .top)
        .background(
            RoundedRectangle(cornerRadius: 4)
                .fill(calendarColor.opacity(0.20))
        )
    }
}
```

### Drawer (Sidebar)

```swift
struct DrawerSection: View {
    let title: String?
    let items: [DrawerItem]
    @Binding var selected: String

    struct DrawerItem {
        let id: String
        let icon: String
        let label: String
        let color: Color?    // for calendar swatches
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            if let title = title {
                Text(title)
                    .font(.system(size: 12, weight: .medium))
                    .tracking(0.4)
                    .foregroundStyle(Color.gcalSecondary)
                    .padding(.horizontal, 16)
                    .padding(.vertical, 8)
            }
            ForEach(items, id: \.id) { item in
                DrawerRow(item: item, isSelected: selected == item.id) {
                    selected = item.id
                }
            }
        }
    }
}

struct DrawerRow: View {
    let item: DrawerSection.DrawerItem
    let isSelected: Bool
    var onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 16) {
                if let color = item.color {
                    RoundedRectangle(cornerRadius: 2)
                        .fill(color)
                        .frame(width: 18, height: 18)
                } else {
                    Image(systemName: item.icon)
                        .font(.system(size: 20))
                        .foregroundStyle(isSelected ? Color.gcalBlue : Color.gcalSecondary)
                        .frame(width: 24)
                }
                Text(item.label)
                    .font(.gcalSidebar)
                    .foregroundStyle(isSelected ? Color.gcalBlue : Color.gcalInk)
                Spacer()
            }
            .padding(.horizontal, 16)
            .frame(height: 48)
            .background(isSelected ? Color.gcalBlueTint : Color.clear)
        }
        .buttonStyle(.plain)
    }
}
```

### Material Buttons

```swift
struct MaterialTextButton: View {
    let title: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title.uppercased())
                .font(.gcalButton)
                .tracking(0.4)
                .foregroundStyle(Color.gcalBlue)
                .padding(.vertical, 8)
                .padding(.horizontal, 12)
        }
        .buttonStyle(.plain)
        .contentShape(Rectangle())
    }
}

struct MaterialFilledButton: View {
    let title: String
    var systemImage: String? = nil
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                if let symbol = systemImage {
                    Image(systemName: symbol)
                        .font(.system(size: 14, weight: .medium))
                }
                Text(title.uppercased())
                    .font(.gcalButton)
                    .tracking(0.4)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 10)
            .padding(.horizontal, 24)
            .background(RoundedRectangle(cornerRadius: 4).fill(Color.gcalBlue))
        }
        .buttonStyle(.plain)
    }
}
```

### RSVP Pills (Event Detail)

```swift
struct RSVPPills: View {
    @State private var selection: RSVPOption = .yes
    enum RSVPOption: String, CaseIterable { case yes = "Yes", no = "No", maybe = "Maybe" }

    var body: some View {
        HStack(spacing: 8) {
            ForEach(RSVPOption.allCases, id: \.self) { option in
                let isSelected = selection == option
                Button {
                    withAnimation(.easeOut(duration: 0.2)) { selection = option }
                } label: {
                    Text(option.rawValue)
                        .font(.system(size: 14, weight: .medium))
                        .foregroundStyle(isSelected ? .white : Color.gcalSecondary)
                        .frame(maxWidth: .infinity)
                        .frame(height: 40)
                        .background(
                            RoundedRectangle(cornerRadius: 500)
                                .fill(isSelected ? Color.gcalBlue : Color.clear)
                        )
                        .overlay(
                            RoundedRectangle(cornerRadius: 500)
                                .strokeBorder(isSelected ? Color.clear : Color.gcalDivider, lineWidth: 1)
                        )
                }
                .buttonStyle(.plain)
            }
        }
    }
}
```

## 4. Day Timeline Current-Time Indicator

```swift
struct CurrentTimeIndicator: View {
    let yOffset: CGFloat // computed from current time

    var body: some View {
        HStack(spacing: 0) {
            Circle().fill(Color.gcalEventRed).frame(width: 10, height: 10)
            Rectangle().fill(Color.gcalEventRed).frame(height: 2)
        }
        .offset(y: yOffset)
        .allowsHitTesting(false)
    }
}
```

## 5. Motion & Haptics

```swift
// FAB tap
.sensoryFeedback(.impact(weight: .medium), trigger: fabPressed)

// Event saved
.sensoryFeedback(.success, trigger: eventSaved)

// RSVP toggle
.sensoryFeedback(.selection, trigger: rsvpChoice)

// Month-day tap (transition to Day view)
.sensoryFeedback(.impact(weight: .light), trigger: dayTapped)

// FAB ripple (concentric expand)
withAnimation(.easeOut(duration: 0.3)) {
    rippleScale = 1.5
    rippleOpacity = 0
}

// Event card → Detail shared transition
.matchedGeometryEffect(id: event.id, in: cardNamespace)
```

## 6. Tab Bar (iPad — Drawer replaces this on iPhone)

```swift
struct RootTabView: View {
    var body: some View {
        TabView {
            ScheduleView() .tabItem { Label("Schedule", systemImage: "list.bullet") }
            DayView()      .tabItem { Label("Day",      systemImage: "calendar.day.timeline.left") }
            WeekView()     .tabItem { Label("Week",     systemImage: "calendar") }
            MonthView()    .tabItem { Label("Month",    systemImage: "calendar") }
        }
        .tint(Color.gcalBlue)
    }
}
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| FAB plus | `plus` | 24pt |
| Schedule view | `list.bullet` | 22pt |
| Day view | `calendar.day.timeline.left` | 22pt |
| Week view | `calendar` | 22pt |
| Month view | `calendar` | 22pt |
| Location pin | `mappin.and.ellipse` | 11pt |
| Reminder | `bell.fill` | 14pt |
| Drawer menu | `line.horizontal.3` | 22pt |
| Settings | `gearshape` | 20pt |
| Search | `magnifyingglass` | 20pt |
| Today | `calendar.badge.clock` (the floating "Today" button) | 22pt |
| Weather sun | `sun.max.fill` | 20pt |
| Google Meet | `video.fill` | 18pt |
| Add guest | `person.badge.plus` | 20pt |
| Add description | `text.alignleft` | 20pt |
| All-day toggle | (use `Toggle`) | — |
| Calendar swatch (drawer) | filled `square.fill` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `matchedGeometryEffect`); fall back to `UIImpactFeedbackGenerator` on iOS 16
- Support Dynamic Type on event titles, body, large nav titles, sidebar items; fix day numbers in Month grid, time gutter labels, weekday headers, tab labels (layout-sensitive)
- VoiceOver: event card reads as `"<title>, <time range>, <location>, in <calendar name>"` — e.g., `"Stand-up, 9 to 9:30 AM, Conference Room B, in Work calendar"`; the card is a single accessibility element
- Contrast: Ink `#202124` on white meets WCAG AAA; Secondary `#5F6368` meets AA at 14pt+ Medium (event time text is exactly at this boundary — verify with audit)
- Reduce Motion: skip the FAB ripple and Day-grid zoom animations when `accessibilityReduceMotion` is true — fall back to instant transitions
- Dark mode: implement via `@Environment(\.colorScheme)` — swap canvas, text, surface tokens; Google Blue brightens to `#8AB4F8` in dark mode for FAB and links
- Tabular figures: use `.monospacedDigit()` on all `Font` instances that render time or date — this is layout-critical
- The Material dual-shadow may not render identically across iOS versions; verify on iOS 17 and iOS 18 — if shadow stacking degrades, fall back to a single shadow at `radius: 4, y: 2`
