# Things 3 (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Things 3's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (light — primary)
    static let thingsCanvas   = Color.white                                  // #FFFFFF
    static let thingsSurface1 = Color(red: 0.961, green: 0.965, blue: 0.973) // #F5F6F8
    static let thingsSurface2 = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC
    static let thingsDivider  = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC

    // MARK: - Text
    static let thingsTextPrimary   = Color(red: 0.114, green: 0.114, blue: 0.122) // #1D1D1F
    static let thingsTextSecondary = Color(red: 0.541, green: 0.541, blue: 0.557) // #8A8A8E
    static let thingsTextTertiary  = Color(red: 0.780, green: 0.780, blue: 0.800) // #C7C7CC

    // MARK: - Brand
    static let thingsBlue        = Color(red: 0.310, green: 0.592, blue: 1.0)   // #4F97FF
    static let thingsBluePressed = Color(red: 0.239, green: 0.498, blue: 0.878) // #3D7FE0
    static let thingsBlueTint    = Color(red: 0.310, green: 0.592, blue: 1.0).opacity(0.10)

    // MARK: - Today & Semantic
    static let thingsToday    = Color(red: 1.0, green: 0.839, blue: 0.039)      // #FFD60A
    static let thingsDeadline = Color(red: 1.0, green: 0.231, blue: 0.188)      // #FF3B30

    // MARK: - Dark mode (secondary)
    static let thingsDarkCanvas  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let thingsDarkSurface = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E
}
```

## 2. Typography

Things uses SF Pro (the system face). Use `.system(...)` directly so Dynamic Type and optical sizing come for free; substitute Inter only if matching a non-Apple platform.

```swift
extension Font {
    static let thingsListTitle    = Font.system(size: 28, weight: .bold,     design: .default) // SF Pro Display
    static let thingsProjectTitle = Font.system(size: 24, weight: .bold,     design: .default)
    static let thingsHeading      = Font.system(size: 17, weight: .bold,     design: .default)
    static let thingsTaskTitle    = Font.system(size: 17, weight: .regular,  design: .default)
    static let thingsBody         = Font.system(size: 15, weight: .regular,  design: .default)
    static let thingsSubtitle     = Font.system(size: 14, weight: .regular,  design: .default)
    static let thingsMetadata     = Font.system(size: 13, weight: .regular,  design: .default)
    static let thingsButton       = Font.system(size: 17, weight: .semibold, design: .default)
    static let thingsSidebar      = Font.system(size: 16, weight: .regular,  design: .default)
    static let thingsDatePill     = Font.system(size: 13, weight: .semibold, design: .default)
    static let thingsCount        = Font.system(size: 15, weight: .regular,  design: .default)
    static let thingsTinyUpper    = Font.system(size: 12, weight: .bold,     design: .default)
}
```

## 3. Signature Components

### Circular Checkbox + Fill Animation (the signature delight)

```swift
struct ThingsCheckbox: View {
    @Binding var done: Bool
    var size: CGFloat = 22

    var body: some View {
        ZStack {
            Circle()
                .strokeBorder(Color.thingsTextTertiary, lineWidth: 1.5)
                .opacity(done ? 0 : 1)
            Circle()
                .fill(Color.thingsBlue)
                .scaleEffect(done ? 1 : 0.001)         // fills center-out
            Image(systemName: "checkmark")
                .font(.system(size: size * 0.5, weight: .bold))
                .foregroundStyle(.white)
                .scaleEffect(done ? 1 : 0.3)
                .opacity(done ? 1 : 0)
        }
        .frame(width: size, height: size)
        .contentShape(Circle())
        .onTapGesture {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { done.toggle() }
        }
        .sensoryFeedback(.impact(weight: .soft), trigger: done)
        .animation(.easeOut(duration: 0.18), value: done) // the fill timing
    }
}
```

### To-Do Row

```swift
struct ToDoRow: View {
    @State private var done = false
    let title: String
    var tag: String? = nil
    var datePill: String? = nil
    var isSelected: Bool = false

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            ThingsCheckbox(done: $done)
                .padding(.top, 1)

            VStack(alignment: .leading, spacing: 4) {
                Text(title)
                    .font(.thingsTaskTitle)
                    .foregroundStyle(done ? Color.thingsTextTertiary : .thingsTextPrimary)
                    .strikethrough(done, color: .thingsTextTertiary)

                if tag != nil || datePill != nil {
                    HStack(spacing: 6) {
                        if let tag {
                            Text(tag)
                                .font(.thingsMetadata)
                                .foregroundStyle(.thingsTextSecondary)
                                .padding(.horizontal, 8).padding(.vertical, 3)
                                .background(Capsule().fill(Color.thingsSurface1))
                        }
                        if let datePill {
                            Text(datePill)
                                .font(.thingsDatePill)
                                .foregroundStyle(.thingsBlue)
                        }
                    }
                }
            }
            Spacer(minLength: 0)
        }
        .padding(.horizontal, 20)
        .padding(.vertical, 12)
        .frame(minHeight: 44, alignment: .top)
        .background(isSelected ? Color.thingsBlueTint : .clear)
        .clipShape(RoundedRectangle(cornerRadius: 8))
        .opacity(done ? 0 : 1)        // gentle fade-out after completion
        .animation(.easeInOut(duration: 0.25).delay(done ? 0.25 : 0), value: done)
        .contentShape(Rectangle())
    }
}
```

### Magic-Plus Button (the signature control)

```swift
struct MagicPlusButton: View {
    let onTap: () -> Void
    @GestureState private var dragging = false

    var body: some View {
        Image(systemName: "plus")
            .font(.system(size: 24, weight: .semibold))
            .foregroundStyle(.white)
            .frame(width: 56, height: 56)
            .background(Circle().fill(Color.thingsBlue))
            .shadow(color: Color.thingsBlue.opacity(0.35), radius: 20, y: 8)
            .scaleEffect(dragging ? 1.05 : 1.0)
            .gesture(
                LongPressGesture(minimumDuration: 0.15)
                    .sequenced(before: DragGesture())
                    .updating($dragging) { _, state, _ in state = true }
                // On change: compute nearest row gap, show a #4F97FF insertion line.
                // On end: insert an editable to-do at that gap, raise the keyboard.
            )
            .onTapGesture { onTap() }
            .sensoryFeedback(.impact(weight: .medium), trigger: dragging)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: dragging)
    }
}
```

### List Title Header

```swift
struct ListTitleHeader: View {
    let title: String
    var isToday: Bool = false

    var body: some View {
        HStack(spacing: 8) {
            if isToday {
                Image(systemName: "star.fill")
                    .font(.system(size: 22))
                    .foregroundStyle(Color.thingsToday)
            }
            Text(title)
                .font(.thingsListTitle)
                .foregroundStyle(.thingsTextPrimary)
                .tracking(0.3)
            Spacer()
        }
        .padding(.horizontal, 20)
        .padding(.top, 24)
        .padding(.bottom, 8)
    }
}
```

### Section Heading + Hairline Divider

```swift
struct SectionHeading: View {
    let title: String
    @Binding var collapsed: Bool

    var body: some View {
        VStack(spacing: 0) {
            Button { withAnimation(.easeInOut(duration: 0.2)) { collapsed.toggle() } } label: {
                HStack(spacing: 6) {
                    Text(title).font(.thingsHeading).foregroundStyle(.thingsTextPrimary)
                    Image(systemName: "chevron.down")
                        .font(.system(size: 12, weight: .semibold))
                        .foregroundStyle(.thingsTextSecondary)
                        .rotationEffect(.degrees(collapsed ? -90 : 0))
                    Spacer()
                }
                .padding(.horizontal, 20)
                .padding(.top, 24)
                .padding(.bottom, 8)
            }
            Divider().overlay(Color.thingsDivider).padding(.horizontal, 20)
        }
    }
}
```

### Project Pie-Progress Ring

```swift
struct PieProgress: View {
    let fraction: Double // 0…1
    var size: CGFloat = 20

    var body: some View {
        ZStack {
            Circle().stroke(Color.thingsDivider, lineWidth: 2)
            Circle()
                .trim(from: 0, to: fraction)
                .stroke(Color.thingsBlue, style: StrokeStyle(lineWidth: 2, lineCap: .round))
                .rotationEffect(.degrees(-90))
            if fraction >= 1 {
                Image(systemName: "checkmark")
                    .font(.system(size: size * 0.45, weight: .bold))
                    .foregroundStyle(Color.thingsBlue)
            }
        }
        .frame(width: size, height: size)
        .animation(.easeInOut(duration: 0.3), value: fraction)
    }
}
```

## 4. Sidebar Navigation (no bottom tab bar)

Things has no tab bar. On iPad use `NavigationSplitView`; on iPhone present the sidebar as a leading slide-over with a soft scrim.

```swift
struct RootView: View {
    @State private var showSidebar = false
    var body: some View {
        ZStack(alignment: .leading) {
            TodayListView()
            if showSidebar {
                Color.black.opacity(0.2).ignoresSafeArea()
                    .onTapGesture { withAnimation { showSidebar = false } }
                Sidebar()
                    .frame(width: 300)
                    .background(Color.thingsCanvas)
                    .transition(.move(edge: .leading))
            }
        }
        .animation(.spring(response: 0.35, dampingFraction: 0.9), value: showSidebar)
    }
}

struct Sidebar: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 2) {
            SidebarItem(icon: "tray.fill",        label: "Inbox",    count: 2,  active: false, tint: .thingsTextSecondary)
            SidebarItem(icon: "star.fill",        label: "Today",    count: 5,  active: true,  tint: .thingsToday)
            SidebarItem(icon: "calendar",         label: "Upcoming", count: nil, active: false, tint: .thingsTextSecondary)
            SidebarItem(icon: "square.stack",     label: "Anytime",  count: nil, active: false, tint: .thingsTextSecondary)
            SidebarItem(icon: "archivebox",       label: "Someday",  count: nil, active: false, tint: .thingsTextSecondary)
        }
        .padding(12)
        .frame(maxHeight: .infinity, alignment: .top)
    }
}

struct SidebarItem: View {
    let icon: String; let label: String; let count: Int?; let active: Bool; let tint: Color
    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: icon).font(.system(size: 18))
                .foregroundStyle(active ? Color.thingsBlue : tint)
                .frame(width: 22)
            Text(label).font(.system(size: 16, weight: active ? .semibold : .regular))
                .foregroundStyle(.thingsTextPrimary)
            Spacer()
            if let count { Text("\(count)").font(.thingsCount).foregroundStyle(.thingsTextSecondary).monospacedDigit() }
        }
        .padding(.horizontal, 10)
        .frame(height: 40)
        .background(active ? Color.thingsBlueTint : .clear)
        .clipShape(RoundedRectangle(cornerRadius: 8))
    }
}
```

## 5. Motion

```swift
// Checkbox completion: spring check + center-out fill, soft haptic, then gentle fade-out
withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { done.toggle() }
.sensoryFeedback(.impact(weight: .soft), trigger: done)

// Magic-Plus tap: spring scale + medium haptic
.sensoryFeedback(.impact(weight: .medium), trigger: tapped)
.animation(.spring(response: 0.25, dampingFraction: 0.7), value: pressed)

// Heading collapse/expand: child height ease 200ms + chevron rotate
.animation(.easeInOut(duration: 0.2), value: collapsed)

// Pie-progress: arc forward over 300ms on completion
.animation(.easeInOut(duration: 0.3), value: fraction)

// Sidebar slide-over: spring 240ms with a soft 0.2 scrim
.animation(.spring(response: 0.35, dampingFraction: 0.9), value: showSidebar)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Unchecked checkbox | drawn `Circle` stroke | 22pt |
| Completed check | `checkmark` (in filled circle) | 11pt |
| Magic-Plus | `plus` | 24pt |
| Today / scheduled | `star.fill` | 18–22pt |
| Inbox (sidebar) | `tray.fill` | 18pt |
| Upcoming (sidebar) | `calendar` | 18pt |
| Anytime (sidebar) | `square.stack` | 18pt |
| Someday (sidebar) | `archivebox` | 18pt |
| Logbook (sidebar) | `book.closed` | 18pt |
| Heading chevron | `chevron.down` | 12pt |
| Deadline flag | `flag.fill` | 14pt |
| Search | `magnifyingglass` | 16pt |
| More | `ellipsis` | 22pt |
| Tag | `tag` | 14pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Things is a reading surface: honor Dynamic Type fully on list titles, task titles, and notes — only pin the 22pt checkbox (interaction target) and clamp the tiny "THIS EVENING" caps so the divider rhythm holds
- VoiceOver: the checkbox needs `.accessibilityAddTraits(done ? [.isButton, .isSelected] : .isButton)` and a label like "Email the design feedback, to-do, not completed"; announce completion ("Completed")
- Communicate "scheduled today" with the star *and* an accessibility label — never color alone
- Contrast: `#8A8A8E` secondary on `#FFFFFF` passes WCAG AA at 13pt+; `#C7C7CC` is intentionally low-contrast (completed/placeholder) — don't use it for essential text
- Respect `accessibilityReduceMotion`: keep the checkbox state change but drop the spring overshoot and the row fade-collapse; settle instantly instead
- The Magic-Plus drag gesture must have a tap fallback (add at top) for users who can't perform a press-and-drag
