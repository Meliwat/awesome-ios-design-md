# Evernote (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Evernote's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (Interactive)
    static let everGreen        = Color(red: 0.000, green: 0.659, blue: 0.176) // #00A82D
    static let everGreenBright  = Color(red: 0.176, green: 0.745, blue: 0.376) // #2DBE60
    static let everGreenPressed = Color(red: 0.000, green: 0.478, blue: 0.125) // #007A20
    static let everGreenDeep    = Color(red: 0.063, green: 0.541, blue: 0.000) // #108A00
    static let everInk          = Color(red: 0.110, green: 0.169, blue: 0.200) // #1C2B33

    // MARK: - Canvas & Surfaces (Light)
    static let everCanvas      = Color.white                                   // #FFFFFF
    static let everOffWhite    = Color(red: 0.980, green: 0.980, blue: 0.980) // #FAFAFA
    static let everPressed     = Color(red: 0.945, green: 0.949, blue: 0.949) // #F1F2F2
    static let everDivider     = Color(red: 0.894, green: 0.898, blue: 0.898) // #E4E5E5
    static let everBorder      = Color(red: 0.863, green: 0.871, blue: 0.871) // #DCDEDE

    // MARK: - Canvas & Surfaces (Dark) — true near-black
    static let everDarkCanvas   = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let everDarkSurface1 = Color(red: 0.141, green: 0.141, blue: 0.149) // #242426
    static let everDarkSurface2 = Color(red: 0.180, green: 0.180, blue: 0.188) // #2E2E30
    static let everDarkSurface3 = Color(red: 0.227, green: 0.227, blue: 0.235) // #3A3A3C
    static let everDarkDivider  = Color(red: 0.220, green: 0.220, blue: 0.227) // #38383A

    // MARK: - Text
    static let everTextPrimary    = Color(red: 0.110, green: 0.169, blue: 0.200) // #1C2B33
    static let everTextSecondary  = Color(red: 0.361, green: 0.412, blue: 0.439) // #5C6970
    static let everTextTertiary   = Color(red: 0.549, green: 0.588, blue: 0.612) // #8C969C
    static let everDarkTextPrimary  = Color(red: 0.894, green: 0.894, blue: 0.902) // #E4E4E6
    static let everDarkTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.620) // #9A9A9E
    static let everDarkTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.447) // #6E6E72

    // MARK: - Tag chips
    static let everTagBgLight   = Color(red: 0.902, green: 0.957, blue: 0.918) // #E6F4EA
    static let everTagTextLight = Color(red: 0.122, green: 0.478, blue: 0.200) // #1F7A33
    static let everTagBgDark    = Color(red: 0.110, green: 0.227, blue: 0.141) // #1C3A24
    static let everTagTextDark  = Color(red: 0.373, green: 0.839, blue: 0.541) // #5FD68A

    // MARK: - Semantic
    static let everBlue    = Color(red: 0.184, green: 0.502, blue: 0.929) // #2F80ED
    static let everWarn    = Color(red: 0.965, green: 0.651, blue: 0.035) // #F6A609
    static let everError   = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
    static let everYellow  = Color(red: 0.965, green: 0.773, blue: 0.267) // #F6C544
    static let everHiLight = Color(red: 1.000, green: 0.953, blue: 0.690) // #FFF3B0
    static let everHiDark  = Color(red: 0.353, green: 0.290, blue: 0.071) // #5A4A12

    // MARK: - Notebook accents
    static let everNbBlue   = Color(red: 0.184, green: 0.502, blue: 0.929) // #2F80ED
    static let everNbPurple = Color(red: 0.478, green: 0.353, blue: 0.973) // #7A5AF8
    static let everNbTeal   = Color(red: 0.102, green: 0.682, blue: 0.624) // #1AAE9F
}
```

## 2. Typography

Evernote uses the iOS system humanist stack (SF Pro); **Inter** (SIL OFL) is the closest free analog. Body is 16pt at 1.6 line-height — reading comfort first.

```swift
extension Font {
    // Default = SF Pro (system). Substitute "Inter" if bundled for cross-platform parity.
    static func ever(_ size: CGFloat, _ weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }

    static let everScreenTitle = Font.system(size: 30, weight: .bold)
    static let everNoteTitle   = Font.system(size: 24, weight: .bold)
    static let everNoteH1      = Font.system(size: 20, weight: .bold)
    static let everNoteH2      = Font.system(size: 17, weight: .bold)
    static let everBody        = Font.system(size: 16, weight: .regular)
    static let everListTitle   = Font.system(size: 15, weight: .semibold)
    static let everChecklist   = Font.system(size: 15, weight: .regular)
    static let everSnippet     = Font.system(size: 14, weight: .regular)
    static let everTag         = Font.system(size: 12, weight: .semibold)
    static let everMeta        = Font.system(size: 12, weight: .regular)
    static let everButton      = Font.system(size: 15, weight: .semibold)
    static let everTab         = Font.system(size: 10, weight: .medium)
    static let everCode        = Font.system(size: 14, weight: .regular, design: .monospaced)
}

// Body line-height helper (SwiftUI: lineSpacing adds to default; ~9.6pt ≈ 1.6 at 16pt)
extension View {
    func everBodyLeading() -> some View { self.lineSpacing(9.6) }
}
```

## 3. Signature Components

### Tag Pill

```swift
struct TagPill: View {
    let text: String
    @Environment(\.colorScheme) var scheme

    var body: some View {
        Text(text)
            .font(.everTag)
            .foregroundStyle(scheme == .dark ? Color.everTagTextDark : Color.everTagTextLight)
            .padding(.vertical, 3)
            .padding(.horizontal, 9)
            .background(Capsule().fill(scheme == .dark ? Color.everTagBgDark : Color.everTagBgLight))
    }
}
```

### Checklist Item

```swift
struct ChecklistItem: View {
    @State var done: Bool
    let text: String
    @Environment(\.colorScheme) var scheme

    var body: some View {
        HStack(alignment: .top, spacing: 10) {
            Button {
                withAnimation(.spring(response: 0.15, dampingFraction: 0.6)) { done.toggle() }
                UIImpactFeedbackGenerator(style: .light).impactOccurred()
            } label: {
                ZStack {
                    RoundedRectangle(cornerRadius: 5)
                        .stroke(done ? Color.clear : Color.everTextTertiary, lineWidth: 2)
                        .background(RoundedRectangle(cornerRadius: 5)
                            .fill(done ? (scheme == .dark ? Color.everGreenBright : Color.everGreen) : .clear))
                        .frame(width: 19, height: 19)
                    if done {
                        Image(systemName: "checkmark")
                            .font(.system(size: 11, weight: .bold))
                            .foregroundStyle(.white)
                    }
                }
            }
            .frame(width: 44, height: 44, alignment: .topLeading)

            Text(text)
                .font(.everChecklist)
                .strikethrough(done)
                .foregroundStyle(done ? Color.everTextTertiary
                                      : (scheme == .dark ? Color.everDarkTextPrimary : Color.everTextPrimary))
        }
    }
}
```

### Note List Row

```swift
struct NoteRow: View {
    let title: String
    let snippet: String
    let date: String
    let notebook: String
    let hasThumb: Bool
    @Environment(\.colorScheme) var scheme

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            VStack(alignment: .leading, spacing: 3) {
                Text(title).font(.everListTitle).lineLimit(1)
                    .foregroundStyle(scheme == .dark ? Color.everDarkTextPrimary : Color.everTextPrimary)
                Text(snippet).font(.everSnippet).lineLimit(2)
                    .foregroundStyle(scheme == .dark ? Color.everDarkTextSecondary : Color.everTextSecondary)
                Text("\(date) · \(notebook)").font(.everMeta)
                    .foregroundStyle(scheme == .dark ? Color.everDarkTextTertiary : Color.everTextTertiary)
                    .padding(.top, 3)
            }
            Spacer(minLength: 0)
            if hasThumb {
                RoundedRectangle(cornerRadius: 8)
                    .fill(scheme == .dark ? Color.everDarkSurface3 : Color.everPressed)
                    .frame(width: 52, height: 52)
            }
        }
        .padding(.vertical, 14)
        .overlay(Divider().background(scheme == .dark ? Color.everDarkDivider : Color.everDivider), alignment: .bottom)
        .swipeActions(edge: .trailing) {
            Button(role: .destructive) {} label: { Label("Delete", systemImage: "trash") }
            Button {} label: { Label("Tag", systemImage: "tag") }.tint(.everGreen)
        }
        .swipeActions(edge: .leading) {
            Button {} label: { Label("Pin", systemImage: "pin") }.tint(.everGreen)
        }
    }
}
```

### Note Editor Scaffold

```swift
struct NoteEditor: View {
    @State var title: String = ""
    let tags: [String]
    @Environment(\.colorScheme) var scheme

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 0) {
                TextField("Title", text: $title)
                    .font(.everNoteTitle)
                    .foregroundStyle(scheme == .dark ? Color.everDarkTextPrimary : Color.everTextPrimary)

                Text("Updated May 16 · Work notebook")
                    .font(.everMeta)
                    .foregroundStyle(Color.everTextTertiary)
                    .padding(.top, 8)

                if !tags.isEmpty {
                    HStack(spacing: 6) { ForEach(tags, id: \.self) { TagPill(text: $0) } }
                        .padding(.top, 12)
                }

                // … rich body blocks: paragraph .everBody.everBodyLeading(), H1/H2, ChecklistItem …
            }
            .padding(.horizontal, 22)
            .padding(.top, 4)
        }
        .background(scheme == .dark ? Color.everDarkCanvas : Color.everCanvas)
        .safeAreaInset(edge: .bottom) { FormatToolbar() }
    }
}
```

### Format Toolbar (keyboard accessory)

```swift
struct FormatToolbar: View {
    @Environment(\.colorScheme) var scheme
    let items = ["bold", "italic", "list.bullet", "checklist", "textformat.size", "paperclip", "link"]

    var body: some View {
        HStack { ForEach(items, id: \.self) { sym in
            Spacer()
            Image(systemName: sym)
                .font(.system(size: 18, weight: .medium))
                .foregroundStyle(Color.everTextSecondary)
            Spacer()
        }}
        .padding(.vertical, 12)
        .background(scheme == .dark ? Color.everDarkSurface2 : Color.everCanvas)
        .overlay(Divider().background(scheme == .dark ? Color.everDarkDivider : Color.everDivider), alignment: .top)
    }
}
```

### Floating Action Button

```swift
struct EvernoteFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .semibold))
                .foregroundStyle(.white)
                .frame(width: 52, height: 52)
                .background(Circle().fill(Color.everGreen))
                .shadow(color: Color.everGreen.opacity(0.40), radius: 16, y: 6)
        }
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct EvernoteTabView: View {
    var body: some View {
        ZStack(alignment: .bottom) {
            TabView {
                HomeView().tabItem { Label("Home", systemImage: "house") }
                NotesView().tabItem { Label("Notes", systemImage: "note.text") }
                Color.clear.tabItem { Text("") }            // FAB slot
                NotebooksView().tabItem { Label("Notebooks", systemImage: "books.vertical") }
                SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            }
            .tint(.everGreen)                                // active = brand green

            EvernoteFAB {}.offset(y: -6)                     // raised above the strip
        }
    }
}
```

## 5. Motion

```swift
// Checkbox toggle — spring fill + strikethrough draw
withAnimation(.spring(response: 0.15, dampingFraction: 0.6)) { done.toggle() }
UIImpactFeedbackGenerator(style: .light).impactOccurred()

// Note open (list → editor): iOS native push, 300ms horizontal slide

// FAB → capture sheet
.sheet(isPresented: $showCapture) { CaptureSheet() }        // 300ms ease-out slide-up
// FAB press feedback: .scaleEffect(pressed ? 0.96 : 1.0).animation(.easeOut(duration: 0.12), value: pressed)

// Sync toast
.transition(.opacity.combined(with: .move(edge: .top)))     // in; auto-dismiss 2s fade-out

// Stack expand
withAnimation(.easeOut(duration: 0.2)) { expanded.toggle() }
// chevron: .rotationEffect(.degrees(expanded ? 90 : 0))

// Tag add
.transition(.scale(scale: 0.8).combined(with: .opacity))    // 150ms ease-out

// Haptics
UIImpactFeedbackGenerator(style: .light).impactOccurred()   // checkbox, tab change
UIImpactFeedbackGenerator(style: .medium).impactOccurred()  // swipe commit, delete
UIImpactFeedbackGenerator(style: .soft).impactOccurred()    // FAB long-press
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Notes (tab) | `note.text` | 22pt |
| FAB / new note | `plus` | 24pt |
| Notebooks (tab) | `books.vertical` / `.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Back | `chevron.left` | 18pt |
| Overflow | `ellipsis` | 19pt |
| Note info | `info.circle` | 19pt |
| Checkbox empty | `square` | 19pt |
| Checkbox done | `checkmark.square.fill` | 19pt |
| Bold | `bold` | 18pt |
| Italic | `italic` | 18pt |
| Bullet list | `list.bullet` | 18pt |
| Checklist | `checklist` | 18pt |
| Heading | `textformat.size` | 18pt |
| Attach | `paperclip` | 18pt |
| Camera / scan | `camera` / `doc.viewfinder` | 20pt |
| Audio | `mic` | 20pt |
| Link | `link` | 18pt |
| Highlight | `highlighter` | 18pt |
| Table | `tablecells` | 18pt |
| Tag | `tag` / `tag.fill` | 16pt |
| Pin | `pin` / `pin.fill` | 16pt |
| Reminder | `clock` / `bell` | 16pt |
| Notebook | `book.closed` | 22pt |
| Stack | `square.stack` | 22pt |

## 7. Dark Mode

```swift
struct EvernoteTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.everDarkCanvas : Color.everOffWhite)
            .foregroundStyle(scheme == .dark ? Color.everDarkTextPrimary : Color.everTextPrimary)
            .tint(scheme == .dark ? Color.everGreenBright : Color.everGreen)
    }
}

extension View { func evernoteTheme() -> some View { modifier(EvernoteTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`safeAreaInset`, `swipeActions`, modern transitions all available)
- Evernote uses the system font; ship **Inter** (SIL OFL) only if you need cross-platform visual parity with the Expo/Compose guides
- Body line-height: keep ≈1.6 (`lineSpacing(9.6)` at 16pt) — never tighten note body below 1.5; reading comfort is the brand
- Dynamic Type: scale screen titles, note title, headings, body, snippets, list titles, checklist text; keep tab labels, tag pills, and sync-toast text FIXED
- VoiceOver: label note rows "{title}, {snippet}, {date}, {notebook}"; checklist items "{text}, checked/unchecked, toggles on activate"; the FAB "New note, button"; tag pills "Tag: {name}, filters notes"
- Color contrast: `#1C2B33` on `#FFFFFF` and `#E4E4E6` on `#1C1C1E` pass WCAG AA for body; brighten interactive green to `#2DBE60` on dark so checkbox fill, links, and the active tab clear AA — never use `#00A82D` for small text/icons on the dark canvas
- Don't encode state by color alone — completed checklist items also get strikethrough; the FAB has a distinct shape
- Reduce Motion: replace push/slide and FAB scale with crossfades; toggle checkboxes without the spring; keep instant state changes
- Dark mode: invert via `everDark*`; the dark canvas is a true near-black `#1C1C1E` (NOT charcoal-gray); shadows nearly vanish, so signal elevation via the surface step (`#242426`→`#2E2E30`→`#3A3A3C`) + a 1px border on menus/sheets; the FAB keeps its green-tinted glow
- Hit targets: note row full-row ≥64pt; checkbox 19pt glyph in 44pt hit; FAB 52pt; tab/toolbar icons 44pt hit
