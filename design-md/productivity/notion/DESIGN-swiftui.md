# Notion (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Notion's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let notionCanvas       = Color.white                                   // #FFFFFF
    static let notionSurfaceGray  = Color(red: 0.969, green: 0.965, blue: 0.953) // #F7F6F3
    static let notionSurfacePressed = Color(red: 0.937, green: 0.933, blue: 0.914) // #EFEEE9
    static let notionDivider      = Color(red: 0.918, green: 0.925, blue: 0.925) // #EAECEC

    // MARK: - Canvas & Surfaces (Dark)
    static let notionDarkCanvas   = Color(red: 0.098, green: 0.098, blue: 0.098) // #191919
    static let notionDarkSurface1 = Color(red: 0.125, green: 0.125, blue: 0.125) // #202020
    static let notionDarkSurface2 = Color(red: 0.184, green: 0.184, blue: 0.184) // #2F2F2F
    static let notionDarkDivider  = Color(red: 0.216, green: 0.216, blue: 0.216) // #373737

    // MARK: - Text
    static let notionTextPrimary    = Color(red: 0.216, green: 0.208, blue: 0.184) // #37352F
    static let notionTextSecondary  = Color(red: 0.608, green: 0.604, blue: 0.592) // #9B9A97
    static let notionTextTertiary   = Color(red: 0.765, green: 0.761, blue: 0.749) // #C3C2BF
    static let notionDarkTextPrimary = Color(red: 0.902, green: 0.902, blue: 0.894) // #E6E6E4

    // MARK: - Brand
    static let notionBlack         = Color.black                                   // #000000
    static let notionLinkBlue      = Color(red: 0.180, green: 0.459, blue: 0.800) // #2E75CC
    static let notionDarkLink      = Color(red: 0.322, green: 0.612, blue: 0.792) // #529CCA

    // MARK: - Page Background Pastels (Light)
    static let notionBgGray        = Color(red: 0.945, green: 0.945, blue: 0.937) // #F1F1EF
    static let notionBgBrown       = Color(red: 0.957, green: 0.933, blue: 0.933) // #F4EEEE
    static let notionBgOrange      = Color(red: 0.980, green: 0.922, blue: 0.867) // #FAEBDD
    static let notionBgYellow      = Color(red: 0.984, green: 0.953, blue: 0.859) // #FBF3DB
    static let notionBgGreen       = Color(red: 0.929, green: 0.953, blue: 0.925) // #EDF3EC
    static let notionBgBlue        = Color(red: 0.906, green: 0.953, blue: 0.973) // #E7F3F8
    static let notionBgPurple      = Color(red: 0.965, green: 0.953, blue: 0.976) // #F6F3F9
    static let notionBgPink        = Color(red: 0.980, green: 0.945, blue: 0.961) // #FAF1F5
    static let notionBgRed         = Color(red: 0.992, green: 0.922, blue: 0.925) // #FDEBEC

    // MARK: - Callout Text Colors (saturated pastels)
    static let notionTextBrown     = Color(red: 0.392, green: 0.278, blue: 0.227) // #64473A
    static let notionTextOrange    = Color(red: 0.851, green: 0.451, blue: 0.051) // #D9730D
    static let notionTextYellow    = Color(red: 0.875, green: 0.671, blue: 0.004) // #DFAB01
    static let notionTextGreen     = Color(red: 0.059, green: 0.482, blue: 0.424) // #0F7B6C
    static let notionTextBlue      = Color(red: 0.043, green: 0.431, blue: 0.600) // #0B6E99
    static let notionTextPurple    = Color(red: 0.412, green: 0.251, blue: 0.647) // #6940A5
    static let notionTextPink      = Color(red: 0.678, green: 0.102, blue: 0.447) // #AD1A72
    static let notionTextRed       = Color(red: 0.878, green: 0.243, blue: 0.243) // #E03E3E
}
```

## 2. Typography

Notion supports three user-switchable families: Inter (default sans), Lora (serif), IBM Plex Mono (mono). Bundle all three via `Info.plist` / `UIAppFonts`. Use an environment value to switch globally.

```swift
enum NotionFontFamily {
    case sans   // Inter
    case serif  // Lora
    case mono   // IBM Plex Mono

    func face(weight: String) -> String {
        switch self {
        case .sans:  return "Inter-\(weight)"
        case .serif: return "Lora-\(weight)"
        case .mono:  return "IBMPlexMono-\(weight)"
        }
    }
}

struct NotionFontKey: EnvironmentKey {
    static let defaultValue: NotionFontFamily = .sans
}

extension EnvironmentValues {
    var notionFontFamily: NotionFontFamily {
        get { self[NotionFontKey.self] }
        set { self[NotionFontKey.self] = newValue }
    }
}

extension Font {
    static func notion(_ size: CGFloat, weight: Font.Weight = .regular, family: NotionFontFamily = .sans) -> Font {
        let weightStr: String = {
            switch weight {
            case .regular:  return "Regular"
            case .medium:   return "Medium"
            case .semibold: return "Semibold"
            case .bold:     return "Bold"
            default:        return "Regular"
            }
        }()
        return Font.custom(family.face(weight: weightStr), size: size)
    }

    // Shorthands for default (Inter)
    static let notionPageTitle = Font.custom("Inter-Bold",    size: 32).weight(.bold)
    static let notionH1        = Font.custom("Inter-Bold",    size: 24).weight(.bold)
    static let notionH2        = Font.custom("Inter-Bold",    size: 20).weight(.bold)
    static let notionH3        = Font.custom("Inter-Bold",    size: 16).weight(.bold)
    static let notionBody      = Font.custom("Inter-Regular", size: 16).weight(.regular)
    static let notionBodyDense = Font.custom("Inter-Regular", size: 15).weight(.regular)
    static let notionCaption   = Font.custom("Inter-Regular", size: 14).weight(.regular)
    static let notionButton    = Font.custom("Inter-Semibold", size: 14).weight(.semibold)
    static let notionMention   = Font.custom("Inter-Medium",  size: 16).weight(.medium)
    static let notionQuote     = Font.custom("Lora-Regular",  size: 17).weight(.regular).italic()
    static let notionCodeInline = Font.custom("IBMPlexMono-Regular", size: 14)
    static let notionCodeBlock  = Font.custom("IBMPlexMono-Regular", size: 13)
    static let notionTab        = Font.custom("Inter-Medium", size: 11).weight(.medium)
    static let notionSidebar    = Font.custom("Inter-Medium", size: 14).weight(.medium)
}
```

## 3. Signature Components

### Page Header (Cover + Emoji + Title)

```swift
struct PageHeader: View {
    let coverUrl: String?
    let emoji: String?
    let title: String
    @Binding var editableTitle: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            if let coverUrl, let url = URL(string: coverUrl) {
                AsyncImage(url: url) { img in
                    img.resizable().aspectRatio(contentMode: .fill)
                } placeholder: {
                    Rectangle().fill(Color.notionSurfaceGray)
                }
                .frame(height: 120)
                .clipped()
            }

            HStack {
                if let emoji {
                    Text(emoji)
                        .font(.system(size: 64))
                        .padding(.leading, 16)
                        .offset(y: coverUrl != nil ? -32 : 0)
                }
            }

            TextField("Untitled", text: $editableTitle)
                .font(.notionPageTitle)
                .foregroundStyle(Color.notionTextPrimary)
                .padding(.horizontal, 16)
                .padding(.top, 8)
                .padding(.bottom, 16)
        }
    }
}
```

### Paragraph Block (with Block Handles)

```swift
struct ParagraphBlock: View {
    @State private var text: String = ""
    @State private var handlesVisible = false
    let onInsertBelow: () -> Void
    let onOpenMenu: () -> Void

    var body: some View {
        HStack(alignment: .top, spacing: 4) {
            // Block handles — appear on long press
            HStack(spacing: 2) {
                Image(systemName: "plus")
                    .font(.system(size: 14))
                    .foregroundStyle(Color.notionTextSecondary.opacity(handlesVisible ? 1.0 : 0))
                    .frame(width: 20, height: 20)
                    .onTapGesture { onInsertBelow() }

                Image(systemName: "ellipsis.circle")  // represents ⋮⋮⋮ drag handle
                    .font(.system(size: 14))
                    .foregroundStyle(Color.notionTextSecondary.opacity(handlesVisible ? 1.0 : 0))
                    .frame(width: 20, height: 20)
                    .onTapGesture { onOpenMenu() }
            }
            .animation(.easeOut(duration: 0.12), value: handlesVisible)
            .frame(width: 44, alignment: .trailing)

            TextEditor(text: $text)
                .font(.notionBody)
                .foregroundStyle(Color.notionTextPrimary)
                .frame(minHeight: 24)
                .overlay(alignment: .topLeading) {
                    if text.isEmpty {
                        Text("Type '/' for commands")
                            .font(.notionBody)
                            .foregroundStyle(Color.notionTextSecondary)
                            .allowsHitTesting(false)
                            .padding(.top, 8)
                            .padding(.leading, 4)
                    }
                }
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 3)
        .contentShape(Rectangle())
        .onLongPressGesture(minimumDuration: 0.4) {
            withAnimation { handlesVisible = true }
        }
    }
}
```

### Callout Block

```swift
struct CalloutBlock: View {
    let emoji: String
    let text: String
    let backgroundColor: Color
    let textColor: Color

    var body: some View {
        HStack(alignment: .top, spacing: 10) {
            Text(emoji).font(.system(size: 20))
            Text(text)
                .font(.notionBody)
                .foregroundStyle(textColor)
        }
        .padding(16)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 4).fill(backgroundColor))
        .padding(.horizontal, 16)
    }
}

// Usage
// CalloutBlock(emoji: "💡", text: "Tip: Type '/' for block commands",
//              backgroundColor: .notionBgYellow, textColor: .notionTextYellow)
```

### Toggle Block

```swift
struct ToggleBlock<Content: View>: View {
    let title: String
    @ViewBuilder let content: () -> Content
    @State private var isOpen = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Button {
                withAnimation(.easeOut(duration: 0.15)) { isOpen.toggle() }
            } label: {
                HStack(spacing: 4) {
                    Image(systemName: "chevron.right")
                        .font(.system(size: 14))
                        .foregroundStyle(Color.notionTextSecondary)
                        .rotationEffect(.degrees(isOpen ? 90 : 0))
                    Text(title)
                        .font(.notionBody.weight(.medium))
                        .foregroundStyle(Color.notionTextPrimary)
                    Spacer()
                }
                .padding(.horizontal, 16)
                .padding(.vertical, 6)
            }
            .buttonStyle(.plain)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: isOpen)

            if isOpen {
                content()
                    .padding(.leading, 24)
                    .transition(.opacity.combined(with: .move(edge: .top)))
            }
        }
    }
}
```

### Code Block

```swift
struct CodeBlock: View {
    let language: String
    let code: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text(language)
                    .font(.notion(12, weight: .regular, family: .mono))
                    .foregroundStyle(Color.notionTextSecondary)
                Spacer()
                Button {
                    UIPasteboard.general.string = code
                } label: {
                    Image(systemName: "doc.on.doc")
                        .font(.system(size: 14))
                        .foregroundStyle(Color.notionTextSecondary)
                }
            }
            .padding(.horizontal, 12)
            .padding(.top, 8)

            ScrollView(.horizontal, showsIndicators: false) {
                Text(code)
                    .font(.notionCodeBlock)
                    .foregroundStyle(Color.notionTextPrimary)
                    .padding(16)
            }
        }
        .background(RoundedRectangle(cornerRadius: 4).fill(Color.notionSurfaceGray))
        .padding(.horizontal, 16)
    }
}
```

### Mention Chip (inline)

```swift
struct MentionChip: View {
    let avatarUrl: String?
    let name: String

    var body: some View {
        HStack(spacing: 4) {
            if let avatarUrl, let url = URL(string: avatarUrl) {
                AsyncImage(url: url) { img in img.resizable() }
                    placeholder: { Circle().fill(Color.notionSurfaceGray) }
                    .frame(width: 16, height: 16)
                    .clipShape(Circle())
            } else {
                Circle().fill(Color.notionLinkBlue.opacity(0.2)).frame(width: 16, height: 16)
            }
            Text("@\(name)")
                .font(.notionMention)
                .foregroundStyle(Color.notionLinkBlue)
        }
        .padding(.vertical, 2)
        .padding(.horizontal, 4)
        .background(RoundedRectangle(cornerRadius: 2).fill(Color.notionLinkBlue.opacity(0.1)))
    }
}
```

### `/` Command Palette

```swift
struct SlashCommandPalette: View {
    @Binding var isShown: Bool
    let options: [BlockOption]
    @State private var selectedIndex = 0

    struct BlockOption: Identifiable {
        let id = UUID()
        let icon: String  // SF Symbol
        let title: String
        let subtitle: String
    }

    var body: some View {
        VStack(spacing: 0) {
            ForEach(Array(options.enumerated()), id: \.offset) { index, option in
                HStack(spacing: 12) {
                    Image(systemName: option.icon)
                        .font(.system(size: 18))
                        .foregroundStyle(Color.notionTextPrimary)
                        .frame(width: 20, height: 20)

                    VStack(alignment: .leading, spacing: 1) {
                        Text(option.title)
                            .font(.notion(14, weight: .medium))
                            .foregroundStyle(Color.notionTextPrimary)
                        Text(option.subtitle)
                            .font(.notion(12))
                            .foregroundStyle(Color.notionTextSecondary)
                    }
                    Spacer()
                }
                .padding(.horizontal, 12)
                .padding(.vertical, 8)
                .frame(height: 40)
                .background(index == selectedIndex ? Color.notionSurfacePressed : Color.clear)
                .onTapGesture {
                    selectedIndex = index
                    // trigger insertion
                    isShown = false
                }
            }
        }
        .padding(.vertical, 6)
        .background(
            RoundedRectangle(cornerRadius: 6)
                .fill(Color.notionCanvas)
                .shadow(color: .black.opacity(0.08), radius: 12, y: 4)
        )
        .overlay(
            RoundedRectangle(cornerRadius: 6)
                .strokeBorder(Color.notionDivider, lineWidth: 1)
        )
        .frame(width: 280)
        .transition(.opacity.combined(with: .move(edge: .bottom)))
    }
}
```

### Sidebar

```swift
struct NotionSidebar: View {
    let sections: [SidebarSection]

    struct SidebarSection: Identifiable {
        let id = UUID()
        let title: String
        let pages: [SidebarPage]
    }

    struct SidebarPage: Identifiable {
        let id = UUID()
        let icon: String   // emoji or SF Symbol
        let title: String
        let children: [SidebarPage]
    }

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                ForEach(sections) { section in
                    VStack(alignment: .leading, spacing: 2) {
                        Text(section.title.uppercased())
                            .font(.notion(11, weight: .medium))
                            .foregroundStyle(Color.notionTextSecondary)
                            .padding(.horizontal, 12)
                            .padding(.top, 8)

                        ForEach(section.pages) { page in
                            SidebarRow(page: page, indent: 0)
                        }
                    }
                }
            }
            .padding(.top, 12)
        }
        .frame(width: 280)
        .background(Color.notionSurfaceGray)
    }
}

struct SidebarRow: View {
    let page: NotionSidebar.SidebarPage
    let indent: Int

    var body: some View {
        HStack(spacing: 8) {
            Image(systemName: "chevron.right").font(.system(size: 10)).foregroundStyle(Color.notionTextSecondary)
            Text(page.icon).font(.system(size: 16))
            Text(page.title).font(.notionSidebar).foregroundStyle(Color.notionTextPrimary)
            Spacer()
        }
        .padding(.horizontal, 12 + CGFloat(indent * 24))
        .frame(height: 32)
        .contentShape(Rectangle())
    }
}
```

## 4. Bottom Tab Bar

```swift
struct NotionTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            UpdatesView().tabItem { Label("Updates", systemImage: "bell") }
            SettingsView().tabItem { Label("Settings", systemImage: "gear") }
        }
        .tint(.notionTextPrimary)  // active is dark text color, no pill indicator
    }
}
```

## 5. Motion

```swift
// Toggle expand/collapse
withAnimation(.easeOut(duration: 0.15)) { isOpen.toggle() }
// Chevron rotation: .rotationEffect(.degrees(isOpen ? 90 : 0))

// Block handle reveal (long-press)
.onLongPressGesture(minimumDuration: 0.4) {
    withAnimation(.easeOut(duration: 0.12)) { handlesVisible = true }
}

// / command palette
.transition(.opacity.combined(with: .move(edge: .bottom)))
// entrance: 150ms ease-out
// dismiss: 150ms ease-in

// Sidebar swipe
// Use DragGesture; open threshold 50% width; spring(response: 0.25, dampingFraction: 0.8)

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: toggleOpen)
.sensoryFeedback(.impact(flexibility: .soft), trigger: blockDragStart)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 24pt |
| Search (tab) | `magnifyingglass` | 24pt |
| Updates (tab) | `bell` / `bell.fill` | 24pt |
| Settings (tab) | `gear` | 24pt |
| Hamburger | `sidebar.leading` | 24pt |
| Back | `chevron.left` | 24pt |
| More (block) | `ellipsis.circle` | 20pt |
| Insert below | `plus` | 20pt |
| Toggle closed | `chevron.right` | 14pt |
| Toggle open | `chevron.down` | 14pt |
| Copy code | `doc.on.doc` | 14pt |
| Share | `square.and.arrow.up` | 22pt |
| Check (checkbox block) | `square` / `checkmark.square.fill` | 18pt |
| Link | `link` | 16pt |
| Page icon (default) | `doc.text` | 16pt |
| Database table | `tablecells` | 18pt |
| Database board | `rectangle.3.group` | 18pt |
| Database calendar | `calendar` | 18pt |
| Database gallery | `square.grid.2x2` | 18pt |

## 7. Dark Mode

```swift
struct NotionTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.notionDarkCanvas : Color.notionCanvas)
            .foregroundStyle(scheme == .dark ? Color.notionDarkTextPrimary : Color.notionTextPrimary)
    }
}

extension View {
    func notionTheme() -> some View { modifier(NotionTheme()) }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Inter, Lora, IBM Plex Mono TTFs via `Info.plist` — all are SIL OFL licensed for free distribution
- Dynamic Type: support on page title, headings, body, captions; keep block handles, tab labels, sidebar labels, mention chips, command palette option text at FIXED sizes
- VoiceOver: label paragraph blocks as "Paragraph: {content}"; label toggle as "Toggle: {title}, expanded" or "collapsed"; label `/` command palette options with both title and subtitle
- Block handles (⋮⋮⋮ + `+`) should be reachable via VoiceOver rotor as "Actions" — use `.accessibilityAction(named:)`
- Color contrast: `#37352F` on `#FFFFFF` passes WCAG AA for body text at 16pt; pastel callout backgrounds have carefully-paired text colors that also pass AA
- Reduce Motion: disable the chevron rotation animation and toggle slide-down (use crossfade instead)
- Dark mode: colors invert per the `notionDark*` palette; shadows become harder to see, so add a 1pt `notionDarkDivider` border to floating panels for an elevation cue
