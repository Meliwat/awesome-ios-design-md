# ClickUp (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates ClickUp's visual language into paste-ready SwiftUI code: `Color` extensions, the brand `Gradient`, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let cuCanvas        = Color.white                                   // #FFFFFF
    static let cuSurfaceGray   = Color(red: 0.969, green: 0.969, blue: 0.984) // #F7F7FB
    static let cuSurfacePressed = Color(red: 0.929, green: 0.929, blue: 0.957) // #EDEDF4
    static let cuDivider       = Color(red: 0.906, green: 0.906, blue: 0.937) // #E7E7EF
    static let cuCardBorder    = Color(red: 0.871, green: 0.871, blue: 0.914) // #DEDEE9

    // MARK: - Canvas & Surfaces (Dark)
    static let cuDarkCanvas    = Color(red: 0.106, green: 0.106, blue: 0.180) // #1B1B2E
    static let cuDarkSurface1  = Color(red: 0.137, green: 0.137, blue: 0.220) // #232338
    static let cuDarkSurface2  = Color(red: 0.173, green: 0.173, blue: 0.267) // #2C2C44
    static let cuDarkDivider   = Color(red: 0.220, green: 0.220, blue: 0.322) // #383852
    static let cuDarkCardBorder = Color(red: 0.259, green: 0.259, blue: 0.361) // #42425C

    // MARK: - Text
    static let cuTextPrimary    = Color(red: 0.106, green: 0.106, blue: 0.180) // #1B1B2E
    static let cuTextSecondary  = Color(red: 0.369, green: 0.369, blue: 0.478) // #5E5E7A
    static let cuTextTertiary   = Color(red: 0.573, green: 0.573, blue: 0.659) // #9292A8
    static let cuDarkTextPrimary = Color(red: 0.945, green: 0.945, blue: 0.965) // #F1F1F6
    static let cuDarkTextSecondary = Color(red: 0.651, green: 0.651, blue: 0.737) // #A6A6BC
    static let cuDarkTextTertiary = Color(red: 0.431, green: 0.431, blue: 0.533) // #6E6E88

    // MARK: - Brand
    static let cuPurple        = Color(red: 0.482, green: 0.408, blue: 0.933) // #7B68EE
    static let cuPurplePressed = Color(red: 0.416, green: 0.341, blue: 0.867) // #6A57DD
    static let cuPink          = Color(red: 0.992, green: 0.443, blue: 0.686) // #FD71AF
    static let cuBlue          = Color(red: 0.286, green: 0.800, blue: 0.976) // #49CCF9

    // MARK: - Functional Palette
    static let cuGreen  = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let cuRed    = Color(red: 0.957, green: 0.306, blue: 0.431) // #F44E6E
    static let cuOrange = Color(red: 1.000, green: 0.624, blue: 0.102) // #FF9F1A
    static let cuYellow = Color(red: 1.000, green: 0.800, blue: 0.000) // #FFCC00
    static let cuTeal   = Color(red: 0.118, green: 0.796, blue: 0.882) // #1ECBE1
    static let cuGray   = Color(red: 0.357, green: 0.357, blue: 0.475) // #5B5B79

    // MARK: - Semantic
    static let cuSuccess = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let cuError   = Color(red: 0.957, green: 0.306, blue: 0.431) // #F44E6E
    static let cuWarning = Color(red: 1.000, green: 0.624, blue: 0.102) // #FF9F1A
}

// The 3-stop brand gradient — the hero. FAB / primary CTA / AI ONLY.
extension LinearGradient {
    static let cuBrand = LinearGradient(
        colors: [.cuPurple, .cuPink, .cuBlue],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}

// On-solid text helper: blue / yellow / teal flip to dark
func cuOnColor(_ bg: Color) -> Color {
    [Color.cuBlue, .cuYellow, .cuTeal].contains(bg) ? .cuTextPrimary : .white
}
```

## 2. Typography

ClickUp uses **Plus Jakarta Sans**. Bundle the TTFs via `Info.plist` / `UIAppFonts`; falls back to SF Pro when absent.

```swift
extension Font {
    static func cu(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .regular:  return "PlusJakartaSans-Regular"
            case .medium:   return "PlusJakartaSans-Medium"
            case .semibold: return "PlusJakartaSans-SemiBold"
            case .bold:     return "PlusJakartaSans-Bold"
            case .heavy, .black: return "PlusJakartaSans-ExtraBold"
            default:        return "PlusJakartaSans-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let cuScreenTitle = Font.custom("PlusJakartaSans-ExtraBold", size: 32)
    static let cuListTitle   = Font.custom("PlusJakartaSans-ExtraBold", size: 26)
    static let cuSection     = Font.custom("PlusJakartaSans-Bold",      size: 22)
    static let cuSubsection  = Font.custom("PlusJakartaSans-Bold",      size: 17)
    static let cuBody        = Font.custom("PlusJakartaSans-Regular",   size: 15)
    static let cuTaskName    = Font.custom("PlusJakartaSans-SemiBold",  size: 14)
    static let cuStatusLabel = Font.custom("PlusJakartaSans-ExtraBold", size: 10)
    static let cuMeta        = Font.custom("PlusJakartaSans-Regular",   size: 13)
    static let cuTag         = Font.custom("PlusJakartaSans-Bold",      size: 10)
    static let cuButton      = Font.custom("PlusJakartaSans-Bold",      size: 15)
    static let cuTextAction  = Font.custom("PlusJakartaSans-SemiBold",  size: 14)
    static let cuAssignee    = Font.custom("PlusJakartaSans-Bold",      size: 9)
    static let cuTab         = Font.custom("PlusJakartaSans-SemiBold",  size: 10)
}
```

## 3. Signature Components

### Task Row (dense — status box + flag + tags + assignee)

```swift
struct Priority { let color: Color }   // .cuRed urgent / .cuOrange high / .cuBlue normal / gray low

struct TaskRow: View {
    let name: String
    let statusColor: Color
    let done: Bool
    let priority: Priority?
    let tags: [(text: String, hue: Color)]
    let due: (text: String, overdue: Bool)?
    let assigneeInitials: String
    let assigneeColor: Color
    let onCycleStatus: () -> Void

    var body: some View {
        HStack(spacing: 10) {
            Button(action: onCycleStatus) {
                ZStack {
                    RoundedRectangle(cornerRadius: 5)
                        .strokeBorder(done ? Color.cuGreen : statusColor, lineWidth: 2)
                        .background(RoundedRectangle(cornerRadius: 5).fill(done ? Color.cuGreen : .clear))
                        .frame(width: 18, height: 18)
                    if done {
                        Image(systemName: "checkmark")
                            .font(.system(size: 10, weight: .heavy))
                            .foregroundStyle(Color.cuDarkCanvas)
                    }
                }
            }
            .buttonStyle(.plain)

            VStack(alignment: .leading, spacing: 6) {
                Text(name)
                    .font(.cuTaskName)
                    .strikethrough(done)
                    .foregroundStyle(done ? Color.cuTextTertiary : Color.cuTextPrimary)
                    .lineLimit(1)

                HStack(spacing: 6) {
                    if let priority {
                        Triangle().fill(priority.color).frame(width: 12, height: 10)
                    }
                    ForEach(Array(tags.enumerated()), id: \.offset) { _, tag in
                        Text(tag.text)
                            .font(.cuTag)
                            .foregroundStyle(tag.hue)
                            .padding(.vertical, 2).padding(.horizontal, 7)
                            .background(Capsule().fill(tag.hue.opacity(0.22)))
                    }
                    if let due {
                        Text(due.text)
                            .font(.cuMeta)
                            .foregroundStyle(due.overdue ? Color.cuRed : Color.cuTextSecondary)
                    }
                }
            }
            Spacer(minLength: 8)
            Text(assigneeInitials)
                .font(.cuAssignee)
                .foregroundStyle(.white)
                .frame(width: 22, height: 22)
                .background(Circle().fill(assigneeColor))
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 11)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.cuDivider).frame(height: 1) }
    }
}

struct Triangle: Shape {
    func path(in r: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: r.midX, y: r.minY))
        p.addLine(to: CGPoint(x: r.maxX, y: r.maxY))
        p.addLine(to: CGPoint(x: r.minX, y: r.maxY))
        p.closeSubpath()
        return p
    }
}
```

### Custom-Status Group Header

```swift
struct StatusGroupHeader: View {
    let label: String
    let color: Color
    let count: Int
    @Binding var expanded: Bool

    var body: some View {
        HStack(spacing: 8) {
            Button { withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() } } label: {
                HStack(spacing: 8) {
                    Image(systemName: "chevron.right")
                        .font(.system(size: 11, weight: .bold))
                        .foregroundStyle(Color.cuTextTertiary)
                        .rotationEffect(.degrees(expanded ? 90 : 0))
                    Text(label.uppercased())
                        .font(.cuStatusLabel)
                        .tracking(0.6)
                        .foregroundStyle(cuOnColor(color))
                        .padding(.vertical, 4).padding(.horizontal, 9)
                        .background(RoundedRectangle(cornerRadius: 5).fill(color))
                    Text("\(count)").font(.cuMeta).foregroundStyle(Color.cuTextTertiary)
                }
            }
            .buttonStyle(.plain)
            Spacer()
            Image(systemName: "plus").font(.system(size: 13, weight: .bold)).foregroundStyle(Color.cuTextTertiary)
        }
        .padding(.horizontal, 16)
        .padding(.top, 10).padding(.bottom, 6)
    }
}
```

### Brand-Gradient FAB (squircle)

```swift
struct CreateFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .heavy))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(RoundedRectangle(cornerRadius: 18).fill(LinearGradient.cuBrand))
                .shadow(color: Color.cuPurple.opacity(0.50), radius: 22, y: 8)
        }
        .buttonStyle(.plain)
    }
}
```

### Primary Gradient Button

```swift
struct PrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.cuButton)
                .foregroundStyle(.white)
                .padding(.vertical, 13).padding(.horizontal, 26)
                .background(RoundedRectangle(cornerRadius: 10).fill(LinearGradient.cuBrand))
        }
        .buttonStyle(.plain)
    }
}
```

### ClickUp AI Bar

```swift
struct AIBar: View {
    let action: String
    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "sparkles")
                .font(.system(size: 16, weight: .bold))
                .foregroundStyle(Color.cuPurple)
            (Text("ClickUp AI").font(.cu(12, weight: .bold)).foregroundColor(.cuTextPrimary)
                + Text(" · \(action)").font(.cu(12, weight: .semibold)).foregroundColor(.cuTextSecondary))
            Spacer()
        }
        .padding(.vertical, 10).padding(.horizontal, 14)
        .background(
            RoundedRectangle(cornerRadius: 10)
                .fill(LinearGradient(colors: [Color.cuPurple.opacity(0.18), Color.cuPink.opacity(0.16), Color.cuBlue.opacity(0.16)],
                                     startPoint: .leading, endPoint: .trailing))
        )
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.cuPurple.opacity(0.4), lineWidth: 1))
    }
}
```

### Board Card

```swift
struct BoardCard: View {
    let statusColor: Color
    let name: String
    let priority: Priority?
    let assigneeInitials: String
    let assigneeColor: Color

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Rectangle().fill(statusColor).frame(height: 3) // status-color top accent
            VStack(alignment: .leading, spacing: 10) {
                Text(name).font(.cuTaskName).foregroundStyle(Color.cuTextPrimary)
                HStack {
                    if let priority { Triangle().fill(priority.color).frame(width: 12, height: 10) }
                    Spacer()
                    Text(assigneeInitials).font(.cuAssignee).foregroundStyle(.white)
                        .frame(width: 22, height: 22).background(Circle().fill(assigneeColor))
                }
            }
            .padding(12)
        }
        .background(RoundedRectangle(cornerRadius: 10).fill(Color.cuCanvas))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.cuCardBorder, lineWidth: 1))
        .shadow(color: .black.opacity(0.07), radius: 4, y: 1)
        .clipShape(RoundedRectangle(cornerRadius: 10))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ClickUpTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house") }
            TasksView().tabItem { Label("Tasks", systemImage: "list.bullet") }
            InboxView().tabItem { Label("Inbox", systemImage: "bell") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
        }
        .tint(.cuPurple) // active = brand purple, no pill indicator
    }
}
```

## 5. Motion

```swift
// Status change — checkbox cycles + color crossfade + soft haptic
withAnimation(.easeInOut(duration: 0.18)) { statusColor = next }
.sensoryFeedback(.impact(flexibility: .soft), trigger: statusColor)
// long-press → status picker sheet (fade + 4pt slide-up 150ms)

// Status group collapse/expand
withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() }
// chevron: .rotationEffect(.degrees(expanded ? 90 : 0))

// FAB → create sheet
.sheet(isPresented: $showCreate) { CreateTaskSheet() }   // 280ms ease-out rise

// Drag task / board card
// DragGesture 1:1; neighbors .easeOut(0.2); drop .easeOut(0.15); status recolors on cross-column drop
.sensoryFeedback(.impact(flexibility: .soft), trigger: dragStarted)

// AI generation — gradient shimmer while generating
// animate a 1.2s phase on a LinearGradient mask; result .opacity transition over 200ms

// View tab underline slide
withAnimation(.easeOut(duration: 0.2)) { selectedView = tapped }
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Tasks (tab) | `list.bullet` / `list.bullet.indent` | 22pt |
| Inbox (tab) | `bell` / `bell.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Add / FAB | `plus` | 24pt |
| AI / sparkle | `sparkles` | 16pt |
| Status group chevron | `chevron.right` (rotate 90°) | 11pt |
| Task complete | `checkmark` (in status square) | 10pt |
| Priority flag | custom `Triangle` shape | 12pt |
| Due date | `calendar` | 13pt |
| Assignee (empty) | `person.crop.circle` | 22pt |
| Subtask | `arrow.turn.down.right` | 13pt |
| Comment | `bubble.left` | 14pt |
| More actions | `ellipsis` | 20pt |
| Back | `chevron.left` | 24pt |
| Breadcrumb sep | `chevron.right` | 11pt |
| Filter | `line.3.horizontal.decrease` | 16pt |
| List view | `list.bullet` | 16pt |
| Board view | `rectangle.split.3x1` | 16pt |
| Calendar view | `calendar` | 16pt |
| Gantt view | `chart.bar.xaxis` | 16pt |
| Doc view | `doc.text` | 16pt |
| Slash menu | `slash.circle` | 16pt |

## 7. Dark Mode

```swift
struct ClickUpTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.cuDarkCanvas : Color.cuCanvas)
            .foregroundStyle(scheme == .dark ? Color.cuDarkTextPrimary : Color.cuTextPrimary)
    }
}

extension View { func clickUpTheme() -> some View { modifier(ClickUpTheme()) } }
```

Dark mode uses the deep blue-violet `#1B1B2E` (chosen to complement the gradient, NOT pure black). The 3-stop brand gradient and the functional palette are **identical in both themes** — the gradient FAB/CTA/AI must stay vivid, and status colors carry meaning. Text inverts to `#F1F1F6`; surfaces lift in tight steps. Gray shadows vanish on dark, so board cards use a 1pt `cuDarkCardBorder` + the 3pt status-color top accent; the FAB keeps its purple glow.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator(style: .soft)` for status-change/drag haptics)
- Bundle Plus Jakarta Sans TTFs via `Info.plist` `UIAppFonts` — SIL OFL licensed for free distribution
- Dynamic Type: support on screen/list/section titles, body, task names, comments, Docs; keep status pills, tags, tab labels, assignee initials, status group labels at FIXED sizes (dense-layout-sensitive)
- VoiceOver: announce the status checkbox as "Status: {status name}, double tap to change"; announce the priority flag as "Priority: {Urgent/High/Normal/Low}" (never color alone); announce status group pills with the full status word; label AI surfaces "ClickUp AI: {action}"
- The dense row exposes many attributes — group them into a single accessible element with a combined label, plus custom actions (change status, set priority, open task)
- Color contrast: white on `#7B68EE` / `#2ECC71` / `#F44E6E` / `#FF9F1A` passes WCAG AA at 10pt/800; blue `#49CCF9`, yellow `#FFCC00`, teal `#1ECBE1` MUST use dark text — the `cuOnColor` helper enforces this
- The gradient FAB: ensure the white `plus` has sufficient contrast across all three stops (it does — purple/pink/blue are all dark enough at this saturation)
- Reduce Motion: disable the status color crossfade and the AI gradient shimmer (snap / static tint instead); keep the soft haptic
- Dark mode: invert chrome via `cuDark*` but keep the gradient + functional palette identical; add 1pt `cuDarkCardBorder` + status accent to cards where shadows vanish
