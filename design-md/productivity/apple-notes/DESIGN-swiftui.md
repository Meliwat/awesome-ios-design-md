# Apple Notes (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Apple Notes's paper-soft aesthetic into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the iconic yellow folder glyph as a Path, note rows, the FAB, and haptics.

Targets iOS 17+. Modern `.sensoryFeedback`, `Path`-based folder glyph, and `.scrollDismissesKeyboard` all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light — warm cream paper)
    static let notesCream       = Color(red: 1.000, green: 0.984, blue: 0.929) // #FFFBED
    static let notesCreamSurf1  = Color(red: 0.980, green: 0.965, blue: 0.890) // #FAF6E3
    static let notesCreamSurf2  = Color(red: 0.949, green: 0.929, blue: 0.839) // #F2EDD6
    static let notesDividerLight= Color(red: 0.929, green: 0.918, blue: 0.847) // #EDEAD8

    // MARK: - Canvas & Surfaces (Dark)
    static let notesDarkPaper   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let notesDarkSurf1   = Color(red: 0.149, green: 0.149, blue: 0.149) // #262626
    static let notesDarkSurf2   = Color(red: 0.200, green: 0.200, blue: 0.200) // #333333
    static let notesDarkDivider = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A

    // MARK: - Brand
    static let notesOrange        = Color(red: 0.941, green: 0.604, blue: 0.220) // #F09A38
    static let notesOrangePressed = Color(red: 0.847, green: 0.494, blue: 0.122) // #D87E1F
    static let notesOrangeTint    = Color(red: 1.000, green: 0.945, blue: 0.867) // #FFF1DD
    static let notesFolderYellow  = Color(red: 0.961, green: 0.843, blue: 0.451) // #F5D773
    static let notesFolderHighlight = Color(red: 0.980, green: 0.910, blue: 0.627) // #FAE8A0
    static let notesHighlightYellow = Color(red: 1.000, green: 0.922, blue: 0.471) // #FFEB78

    // MARK: - Text
    static let notesInk         = Color(red: 0.110, green: 0.110, blue: 0.118)    // #1C1C1E
    static let notesSlate       = Color(red: 0.557, green: 0.557, blue: 0.576)    // #8E8E93
    static let notesMute        = Color(red: 0.780, green: 0.780, blue: 0.800)    // #C7C7CC
    static let notesSoftWhite   = Color(red: 0.949, green: 0.949, blue: 0.949)    // #F2F2F2

    // MARK: - Semantic
    static let notesSuccess     = Color(red: 0.204, green: 0.780, blue: 0.349)    // #34C759
    static let notesWarning     = Color(red: 1.000, green: 0.800, blue: 0.000)    // #FFCC00
    static let notesError       = Color(red: 1.000, green: 0.231, blue: 0.188)    // #FF3B30
    static let notesInfoBlue    = Color(red: 0.039, green: 0.518, blue: 1.000)    // #0A84FF
    static let notesLockYellow  = Color(red: 1.000, green: 0.800, blue: 0.000)    // #FFCC00

    // MARK: - Highlight colors (text selection palette)
    static let notesHighlightGreen  = Color(red: 0.702, green: 0.894, blue: 0.482) // #B3E47B
    static let notesHighlightBlue   = Color(red: 0.627, green: 0.847, blue: 1.000) // #A0D8FF
    static let notesHighlightPink   = Color(red: 1.000, green: 0.702, blue: 0.859) // #FFB3DB
    static let notesHighlightPurple = Color(red: 0.831, green: 0.702, blue: 1.000) // #D4B3FF
}
```

## 2. Typography

SF Pro is the system default on iOS. Notes uses SF Pro Display Heavy (800) at 34pt for the large nav title — one of the few iOS apps that does.

```swift
extension Font {
    // Large titles
    static let notesLargeNav   = Font.system(size: 34, weight: .heavy,    design: .default) // SF Pro Display 800
    static let notesFolderSec  = Font.system(size: 22, weight: .bold,     design: .default)

    // Note row
    static let notesRowTitle   = Font.system(size: 16, weight: .semibold, design: .default)
    static let notesRowPreview = Font.system(size: 14, weight: .regular,  design: .default)
    static let notesRowDate    = Font.system(size: 12, weight: .regular,  design: .default)

    // Folder row
    static let notesFolderRow  = Font.system(size: 17, weight: .regular,  design: .default)

    // Note body
    static let notesBodyTitle    = Font.system(size: 20, weight: .semibold, design: .default)
    static let notesBodyHeading  = Font.system(size: 17, weight: .semibold, design: .default)
    static let notesBody         = Font.system(size: 17, weight: .regular,  design: .default)
    static let notesBodyMono     = Font.system(size: 15, weight: .regular,  design: .monospaced)

    // Misc
    static let notesTagChip     = Font.system(size: 14, weight: .medium,   design: .default)
    static let notesToolbar     = Font.system(size: 11, weight: .medium,   design: .default)
    static let notesButton      = Font.system(size: 17, weight: .semibold, design: .default)
}
```

## 3. Signature Components

### Yellow Folder Glyph (the iconic mark)

```swift
struct FolderGlyph: View {
    var size: CGFloat = 24

    var body: some View {
        Canvas { ctx, canvasSize in
            let w = canvasSize.width
            let h = canvasSize.height
            let tabHeight: CGFloat = h * 0.18
            let tabWidth:  CGFloat = w * 0.45
            let bodyTopOffset = tabHeight

            // Folder body path with a tab on top-left
            var path = Path()
            path.move(to: CGPoint(x: 0, y: bodyTopOffset))
            path.addLine(to: CGPoint(x: 0, y: h))
            path.addLine(to: CGPoint(x: w, y: h))
            path.addLine(to: CGPoint(x: w, y: bodyTopOffset - 2))
            path.addLine(to: CGPoint(x: tabWidth + 6, y: bodyTopOffset - 2))
            path.addCurve(
                to: CGPoint(x: tabWidth, y: 0),
                control1: CGPoint(x: tabWidth + 4, y: 0),
                control2: CGPoint(x: tabWidth + 2, y: 0)
            )
            path.addLine(to: CGPoint(x: 4, y: 0))
            path.addCurve(
                to: CGPoint(x: 0, y: 4),
                control1: CGPoint(x: 2, y: 0),
                control2: CGPoint(x: 0, y: 2)
            )
            path.closeSubpath()

            // Fill
            ctx.fill(path, with: .color(Color.notesFolderYellow))
            // Stroke
            ctx.stroke(path, with: .color(Color.notesOrange), lineWidth: 1)
            // Top edge highlight
            var highlight = Path()
            highlight.move(to: CGPoint(x: 6, y: bodyTopOffset + 1))
            highlight.addLine(to: CGPoint(x: w - 6, y: bodyTopOffset + 1))
            ctx.stroke(highlight, with: .color(Color.notesFolderHighlight), lineWidth: 0.5)
        }
        .frame(width: size, height: size * 0.78)
        .rotationEffect(.degrees(-2))   // subtle forward tilt for depth
    }
}
```

### Note Row

```swift
struct NoteRow: View {
    let title: String
    let preview: String
    let date: String
    let isPinned: Bool = false

    var body: some View {
        HStack(alignment: .top, spacing: 0) {
            VStack(alignment: .leading, spacing: 4) {
                HStack(spacing: 6) {
                    if isPinned {
                        Image(systemName: "pin.fill")
                            .font(.system(size: 11))
                            .foregroundStyle(Color.notesOrange)
                    }
                    Text(title)
                        .font(.notesRowTitle)
                        .foregroundStyle(Color.notesInk)
                        .lineLimit(1)
                }
                Text(preview)
                    .font(.notesRowPreview)
                    .foregroundStyle(Color.notesSlate)
                    .lineLimit(1)
                    .padding(.top, 2)
                Text(date)
                    .font(.notesRowDate)
                    .foregroundStyle(Color.notesSlate)
                    .padding(.top, 2)
            }
            Spacer()
        }
        .padding(.horizontal, 16)
        .frame(height: 84, alignment: .top)
        .padding(.top, 16)
        .background(Color.notesCream)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.notesDividerLight).frame(height: 0.5)
        }
        .contentShape(Rectangle())
    }
}
```

### Folder Row

```swift
struct FolderRow: View {
    let name: String
    let count: Int
    var isSmart: Bool = false

    var body: some View {
        HStack(spacing: 12) {
            if isSmart {
                Image(systemName: "sparkles")
                    .font(.system(size: 14))
                    .foregroundStyle(Color.notesOrange)
            }
            FolderGlyph(size: 24)
            Text(name)
                .font(.notesFolderRow)
                .foregroundStyle(Color.notesInk)
            Spacer()
            Text("\(count)")
                .font(.notesFolderRow)
                .foregroundStyle(Color.notesSlate)
            Image(systemName: "chevron.right")
                .font(.system(size: 13, weight: .semibold))
                .foregroundStyle(Color.notesSlate)
        }
        .padding(.horizontal, 16)
        .frame(height: 44)
        .background(Color.notesCream)
    }
}
```

### New Note FAB

```swift
struct NewNoteFAB: View {
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            ZStack {
                Circle()
                    .fill(Color.notesOrange)
                    .shadow(color: Color.notesOrange.opacity(0.30), radius: 12, y: 4)
                Image(systemName: "square.and.pencil")
                    .font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(.white)
                    .offset(x: -1, y: -1)   // visual centering on the pencil glyph
            }
            .frame(width: 56, height: 56)
            .scaleEffect(pressed ? 0.95 : 1)
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: 0, perform: {}, onPressingChanged: { pressed = $0 })
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
    }
}
```

### Primary CTA

```swift
struct NotesPrimaryButton: View {
    let label: String
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.notesButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 44)
                .background(
                    RoundedRectangle(cornerRadius: 12)
                        .fill(pressed ? Color.notesOrangePressed : Color.notesOrange)
                )
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: 0, perform: {}, onPressingChanged: { pressed = $0 })
        .sensoryFeedback(.impact(weight: .light), trigger: pressed)
    }
}
```

### Tag Chip

```swift
struct TagChip: View {
    let name: String
    let isSelected: Bool
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 4) {
                Text("#")
                    .font(.notesTagChip)
                    .foregroundStyle(isSelected ? .white : Color.notesOrange)
                Text(name)
                    .font(.notesTagChip)
                    .foregroundStyle(isSelected ? .white : Color.notesOrange)
            }
            .padding(.horizontal, 12)
            .padding(.vertical, 6)
            .background(
                Capsule()
                    .fill(isSelected ? Color.notesOrange : Color.notesOrangeTint)
            )
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.selection, trigger: isSelected)
    }
}
```

### Search Bar

```swift
struct NotesSearchBar: View {
    @Binding var text: String
    @FocusState private var focused: Bool

    var body: some View {
        HStack(spacing: 8) {
            Image(systemName: "magnifyingglass")
                .font(.system(size: 14))
                .foregroundStyle(Color.notesSlate)
            TextField("Search", text: $text)
                .font(.system(size: 17, weight: .regular))
                .foregroundStyle(Color.notesInk)
                .focused($focused)
            if !text.isEmpty {
                Button { text = "" } label: {
                    Image(systemName: "xmark.circle.fill")
                        .font(.system(size: 14))
                        .foregroundStyle(Color.notesSlate)
                }
            } else {
                Image(systemName: "mic.fill")
                    .font(.system(size: 14))
                    .foregroundStyle(Color.notesSlate)
            }
        }
        .padding(.horizontal, 12)
        .frame(height: 36)
        .background(
            RoundedRectangle(cornerRadius: 10)
                .fill(Color.notesCreamSurf1)
                .overlay(
                    RoundedRectangle(cornerRadius: 10)
                        .strokeBorder(focused ? Color.notesOrange : .clear, lineWidth: 2)
                )
        )
    }
}
```

### Checklist Item

```swift
struct ChecklistItem: View {
    let text: String
    @State var checked: Bool

    var body: some View {
        HStack(spacing: 12) {
            Button {
                withAnimation(.easeOut(duration: 0.2)) { checked.toggle() }
            } label: {
                ZStack {
                    Circle()
                        .strokeBorder(Color.notesSlate, lineWidth: 1.5)
                        .frame(width: 24, height: 24)
                    if checked {
                        Circle()
                            .fill(Color.notesOrange)
                            .frame(width: 24, height: 24)
                        Image(systemName: "checkmark")
                            .font(.system(size: 12, weight: .bold))
                            .foregroundStyle(.white)
                    }
                }
                .scaleEffect(checked ? 1.0 : 1.0)
            }
            .buttonStyle(.plain)
            .sensoryFeedback(.success, trigger: checked)

            Text(text)
                .font(.notesBody)
                .foregroundStyle(checked ? Color.notesSlate : Color.notesInk)
                .strikethrough(checked, color: Color.notesSlate)
                .opacity(checked ? 0.6 : 1.0)
                .animation(.easeOut(duration: 0.3), value: checked)
        }
    }
}
```

### Pinned Card (horizontal scroll)

```swift
struct PinnedCard: View {
    let title: String
    let preview: String

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            HStack {
                Text(title)
                    .font(.system(size: 15, weight: .semibold))
                    .foregroundStyle(Color.notesInk)
                    .lineLimit(1)
                Spacer()
                Image(systemName: "pin.fill")
                    .font(.system(size: 11))
                    .foregroundStyle(Color.notesOrange)
            }
            Text(preview)
                .font(.system(size: 12, weight: .regular))
                .foregroundStyle(Color.notesSlate)
                .lineLimit(3)
        }
        .padding(12)
        .frame(width: 160, height: 96, alignment: .topLeading)
        .background(
            RoundedRectangle(cornerRadius: 10)
                .fill(Color.notesCreamSurf1)
        )
    }
}
```

## 4. Navigation (no tab bar — hierarchical)

```swift
struct NotesRootView: View {
    var body: some View {
        NavigationStack {
            FoldersView()
        }
        .tint(Color.notesOrange)  // every nav-bar button gets Notes Orange
    }
}
```

## 5. Motion & Haptics

```swift
// FAB tap
.sensoryFeedback(.impact(weight: .medium), trigger: fabPressed)

// Primary CTA tap
.sensoryFeedback(.impact(weight: .light), trigger: ctaPressed)

// Checklist toggle
.sensoryFeedback(.success, trigger: checklistToggled)

// Pin / Unpin swipe
.sensoryFeedback(.impact(weight: .light), trigger: pinAction)

// Tag chip selection
.sensoryFeedback(.selection, trigger: selectedTag)

// Note row tap — slides to editor
// .navigationDestination(for: Note.self) { editor }

// Cursor blink — UITextView's native blink is correct
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| New Note FAB | `square.and.pencil` | 22pt |
| Pin indicator | `pin.fill` | 11pt |
| Smart folder | `sparkles` | 14pt |
| Search | `magnifyingglass` | 14pt |
| Voice search | `mic.fill` | 14pt |
| Clear search | `xmark.circle.fill` | 14pt |
| Checklist (empty) | custom circle (1.5pt stroke) | 24pt |
| Checklist (filled) | `checkmark` over filled circle | 12pt |
| Share | `square.and.arrow.up` | 18pt |
| More menu | `ellipsis.circle` | 22pt |
| Back chevron | `chevron.left` | 18pt |
| Row chevron | `chevron.right` | 13pt |
| Photo (toolbar) | `camera` | 22pt |
| Checklist (toolbar) | `checklist` | 22pt |
| Table (toolbar) | `tablecells` | 22pt |
| Format (toolbar) | `textformat` | 22pt |
| Lock | `lock.fill` | 14pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`). On iOS 16, fall back to `UIImpactFeedbackGenerator`.
- Support Dynamic Type on **everything** — Notes skews older-demographic; this is the most Dynamic-Type-friendly app on iOS. Even the tag chips should scale (with horizontal wrapping).
- VoiceOver: read a note row as `"Grocery list, Milk eggs butter sourdough bread, Yesterday"`; a folder row as `"Recipes folder, 12 notes, double-tap to open"`; a checklist item as `"Unchecked: Milk, double-tap to toggle"`.
- Contrast: Ink `#1C1C1E` on Notes Cream `#FFFBED` meets AAA. Slate `#8E8E93` on cream meets AA only at 18pt+ — for tiny dates and previews, raise weight to Medium (500) if AA is critical.
- Reduce Motion: skip the FAB scale bounce, the checklist fill scale, and the folder glyph tilt when `accessibilityReduceMotion` is true.
- Dark mode: use `@Environment(\.colorScheme)` — swap canvas (`#FFFBED` → `#1A1A1A`), surfaces, text. Notes Orange and Folder Yellow stay identical across light and dark.
- Locked notes: implement biometric (Face ID / Touch ID) via `LocalAuthentication.framework` with `LAContext`. Fall back to passcode after 3 failed biometric attempts.
- Body text scaling: when Dynamic Type is at Accessibility XL, the body remains at 17pt × Dynamic-Type-scale × 1.5 line-height — the calmness must scale up, not break.
