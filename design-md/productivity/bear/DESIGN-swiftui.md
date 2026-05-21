# Bear (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bear's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the live-Markdown attributed-string renderer, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let bearCanvas        = Color.white                                  // #FFFFFF
    static let bearSurfaceLight  = Color(red: 0.965, green: 0.965, blue: 0.957) // #F6F6F4
    static let bearPressedLight  = Color(red: 0.925, green: 0.925, blue: 0.910) // #ECECE8
    static let bearDividerLight  = Color(red: 0.902, green: 0.902, blue: 0.886) // #E6E6E2

    // MARK: - Canvas & Surfaces (Dark — "Charcoal")
    static let bearCanvasDark    = Color(red: 0.129, green: 0.145, blue: 0.169) // #21252B
    static let bearSurface1      = Color(red: 0.157, green: 0.173, blue: 0.204) // #282C34
    static let bearSurface2      = Color(red: 0.184, green: 0.204, blue: 0.239) // #2F343D
    static let bearDivider       = Color(red: 0.208, green: 0.227, blue: 0.259) // #353A42

    // MARK: - Text
    static let bearTextPrimary    = Color(red: 0.910, green: 0.918, blue: 0.929) // #E8EAED (dark)
    static let bearTextPrimaryLt  = Color(red: 0.165, green: 0.173, blue: 0.200) // #2A2C33 (light)
    static let bearTextSecondary  = Color(red: 0.616, green: 0.639, blue: 0.678) // #9DA3AD
    static let bearTextTertiary   = Color(red: 0.420, green: 0.447, blue: 0.502) // #6B7280

    // MARK: - Brand
    static let bearRed       = Color(red: 0.878, green: 0.337, blue: 0.435) // #E0566F
    static let bearOrange    = Color(red: 1.000, green: 0.541, blue: 0.396) // #FF8A65
    static let bearRedPressed = Color(red: 0.784, green: 0.282, blue: 0.369) // #C8485E
    static let bearLink      = Color(red: 0.353, green: 0.686, blue: 0.937) // #5AAFEF

    // MARK: - Tag / Highlight Accents
    static let bearTagBlue   = Color(red: 0.353, green: 0.686, blue: 0.937) // #5AAFEF
    static let bearTagGreen  = Color(red: 0.341, green: 0.788, blue: 0.545) // #57C98B
    static let bearTagYellow = Color(red: 0.949, green: 0.757, blue: 0.306) // #F2C14E
    static let bearTagPurple = Color(red: 0.690, green: 0.549, blue: 0.941) // #B08CF0
    static let bearHighlight = Color(red: 0.290, green: 0.263, blue: 0.122) // #4A431F (dark)

    // MARK: - Semantic
    static let bearError     = Color(red: 0.949, green: 0.329, blue: 0.357) // #F2545B
    static let bearSuccess   = Color(red: 0.341, green: 0.788, blue: 0.545) // #57C98B
}

extension LinearGradient {
    /// The single Bear brand accent — icon, FAB, checkbox fill, selection.
    static let bearBrand = LinearGradient(
        colors: [.bearRed, .bearOrange],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Bear's type family is user-switchable: sans (Inter, default), serif (Lora), mono (JetBrains Mono). Bundle all three via `Info.plist` / `UIAppFonts`. Code blocks always render JetBrains Mono regardless of the user's choice.

```swift
enum BearFontFamily {
    case sans   // Inter
    case serif  // Lora
    case mono   // JetBrains Mono

    func face(weight: String) -> String {
        switch self {
        case .sans:  return "Inter-\(weight)"
        case .serif: return "Lora-\(weight)"
        case .mono:  return "JetBrainsMono-\(weight)"
        }
    }
}

struct BearFontKey: EnvironmentKey {
    static let defaultValue: BearFontFamily = .sans
}

extension EnvironmentValues {
    var bearFontFamily: BearFontFamily {
        get { self[BearFontKey.self] }
        set { self[BearFontKey.self] = newValue }
    }
}

extension Font {
    static func bear(_ size: CGFloat, weight: Font.Weight = .regular, family: BearFontFamily = .sans) -> Font {
        let w: String = {
            switch weight {
            case .regular:  return "Regular"
            case .medium:   return "Medium"
            case .semibold: return "SemiBold"
            case .bold:     return "Bold"
            case .heavy:    return "ExtraBold"
            default:        return "Regular"
            }
        }()
        return Font.custom(family.face(weight: w), size: size)
    }

    // Shorthands (default = Inter; quote = Lora; code = JetBrains Mono)
    static let bearLargeTitle = Font.custom("Inter-ExtraBold", size: 32)
    static let bearNoteTitle  = Font.custom("Inter-Bold",      size: 24)
    static let bearH2         = Font.custom("Inter-Bold",      size: 19)
    static let bearH3         = Font.custom("Inter-Bold",      size: 17)
    static let bearSubhead    = Font.custom("Inter-SemiBold",  size: 17)
    static let bearBody       = Font.custom("Inter-Regular",   size: 16)
    static let bearBodyDense  = Font.custom("Inter-Regular",   size: 15)
    static let bearQuote      = Font.custom("Lora-Italic",     size: 17)
    static let bearPreview    = Font.custom("Inter-Regular",   size: 14)
    static let bearMeta       = Font.custom("Inter-Regular",   size: 14)
    static let bearTag        = Font.custom("Inter-Medium",    size: 14)
    static let bearCodeInline = Font.custom("JetBrainsMono-Regular", size: 14)
    static let bearCodeBlock  = Font.custom("JetBrainsMono-Regular", size: 13)
    static let bearCaption    = Font.custom("Inter-Medium",    size: 12)
    static let bearTabLabel   = Font.custom("Inter-Medium",    size: 13)
}
```

## 3. Signature Components

### Live Markdown Renderer

Bear's signature: render Markdown in-place, dimming the syntax marker rather than hiding structure.

```swift
struct MarkdownLine: View {
    let raw: String  // e.g. "# Sprint Planning", "- [x] Ship", "> Focus", "**bold**"

    var body: some View {
        if raw.hasPrefix("# ") {
            HStack(spacing: 4) {
                Text("#").font(.bearNoteTitle).foregroundStyle(Color.bearTextTertiary)
                Text(raw.dropFirst(2)).font(.bearNoteTitle).foregroundStyle(Color.bearTextPrimary)
            }
        } else if raw.hasPrefix("## ") {
            HStack(spacing: 4) {
                Text("##").font(.bearH2).foregroundStyle(Color.bearTextTertiary)
                Text(raw.dropFirst(3)).font(.bearH2).foregroundStyle(Color.bearTextPrimary)
            }
        } else if raw.hasPrefix("> ") {
            QuoteBlock(text: String(raw.dropFirst(2)))
        } else if raw.hasPrefix("- [") {
            CheckboxRow(checked: raw.hasPrefix("- [x]"),
                        label: String(raw.drop(while: { $0 != "]" }).dropFirst(2)))
        } else {
            Text(inlineStyled(raw))
                .font(.bearBody)
                .foregroundStyle(Color.bearTextPrimary)
                .lineSpacing(8) // ≈1.55 line height at 16pt
        }
    }

    /// Renders **bold**, `code`, #hashtags, and [links] with dimmed markers.
    private func inlineStyled(_ s: String) -> AttributedString {
        var out = AttributedString(s)
        // Tag tokens
        for range in s.ranges(of: #/#[\w/]+/#) {
            if let r = Range(range, in: out) { out[r].foregroundColor = .bearRed }
        }
        // Links
        for range in s.ranges(of: #/\[\[[^\]]+\]\]/#) {
            if let r = Range(range, in: out) {
                out[r].foregroundColor = .bearLink
                out[r].underlineStyle = .single
            }
        }
        return out
    }
}
```

### Note Editor (Title + Body)

```swift
struct NoteEditor: View {
    @Binding var title: String
    let lines: [String]

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                TextField("Title", text: $title)
                    .font(.bearNoteTitle)
                    .foregroundStyle(Color.bearTextPrimary)

                ForEach(lines.indices, id: \.self) { i in
                    MarkdownLine(raw: lines[i])
                }
            }
            .padding(.horizontal, 22)
            .padding(.top, 4)
        }
        .background(Color.bearCanvasDark)
    }
}
```

### Checkbox Row

```swift
struct CheckboxRow: View {
    @State var checked: Bool
    let label: String

    var body: some View {
        HStack(spacing: 10) {
            ZStack {
                RoundedRectangle(cornerRadius: 5)
                    .strokeBorder(Color.bearTextTertiary, lineWidth: 2)
                    .frame(width: 19, height: 19)
                if checked {
                    RoundedRectangle(cornerRadius: 5)
                        .fill(LinearGradient.bearBrand)
                        .frame(width: 19, height: 19)
                    Image(systemName: "checkmark")
                        .font(.system(size: 11, weight: .heavy))
                        .foregroundStyle(.white)
                }
            }
            Text(label)
                .font(.bearBody)
                .foregroundStyle(checked ? Color.bearTextTertiary : Color.bearTextPrimary)
                .strikethrough(checked, color: .bearTextTertiary)
            Spacer()
        }
        .contentShape(Rectangle())
        .onTapGesture {
            withAnimation(.easeInOut(duration: 0.15)) { checked.toggle() }
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: checked)
    }
}
```

### Quote Block

```swift
struct QuoteBlock: View {
    let text: String
    var body: some View {
        HStack(spacing: 0) {
            Rectangle().fill(Color.bearOrange).frame(width: 3)
            Text(text)
                .font(.bearQuote)
                .italic()
                .foregroundStyle(Color.bearTextSecondary)
                .padding(.leading, 14)
                .padding(.vertical, 2)
            Spacer()
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
            if !language.isEmpty {
                Text(language)
                    .font(.bearCaption)
                    .foregroundStyle(Color.bearTextTertiary)
                    .frame(maxWidth: .infinity, alignment: .trailing)
                    .padding(.horizontal, 14)
                    .padding(.top, 8)
            }
            Text(code)
                .font(.bearCodeBlock)
                .foregroundStyle(Color.bearTextPrimary)
                .lineSpacing(7)
                .padding(14)
        }
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.bearSurface1))
    }
}
```

### Tag Sidebar Row

```swift
struct TagRow: View {
    let icon: String        // SF Symbol or "number"
    let title: String
    let indent: Int
    let active: Bool

    var body: some View {
        HStack(spacing: 8) {
            Image(systemName: icon)
                .font(.system(size: 16))
                .foregroundStyle(Color.bearRed)
            Text(title)
                .font(.bearTag)
                .foregroundStyle(Color.bearTextPrimary)
            Spacer()
        }
        .padding(.leading, 14 + CGFloat(indent) * 18)
        .padding(.trailing, 12)
        .frame(height: 36)
        .background(active ? Color.bearSurface2 : .clear)
        .contentShape(Rectangle())
    }
}
```

### Compose FAB

```swift
struct ComposeFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "square.and.pencil")
                .font(.system(size: 22, weight: .semibold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(LinearGradient.bearBrand))
                .shadow(color: Color.bearRed.opacity(0.35), radius: 16, y: 6)
        }
        .buttonStyle(.plain)
    }
}
```

### Buttons

```swift
struct BearPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.bear(16, weight: .semibold))
                .foregroundStyle(.white)
                .padding(.vertical, 13).padding(.horizontal, 26)
                .background(RoundedRectangle(cornerRadius: 12).fill(LinearGradient.bearBrand))
        }
        .buttonStyle(.plain)
    }
}

struct BearOutlineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.bear(15, weight: .medium))
                .foregroundStyle(Color.bearOrange)
                .padding(.vertical, 11).padding(.horizontal, 20)
                .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.bearOrange, lineWidth: 1))
        }
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar / Info Bar

Bear's editor has no UITabBar — it has a bottom **info bar** with the live word count and actions. The app shell uses pushed navigation (sidebar → list → editor).

```swift
struct EditorInfoBar: View {
    let words: Int
    let chars: Int

    var body: some View {
        HStack {
            Text("\(words) words · \(chars) chars")
                .font(.bearCaption)
                .foregroundStyle(Color.bearTextTertiary)
            Spacer()
            HStack(spacing: 22) {
                ForEach(["tablecells", "eye", "square.and.arrow.up", "info.circle"], id: \.self) { sym in
                    Image(systemName: sym)
                        .font(.system(size: 20))
                        .foregroundStyle(Color.bearOrange)
                }
            }
        }
        .padding(.horizontal, 22)
        .frame(height: 60)
        .background(.ultraThinMaterial)
        .overlay(Rectangle().fill(Color.bearDivider).frame(height: 0.5), alignment: .top)
    }
}
```

## 5. Motion

```swift
// Checkbox toggle — gradient cross-fade + strike-through
withAnimation(.easeInOut(duration: 0.15)) { checked.toggle() }

// Markdown syntax-marker fade (opacity 1 → dimmed); never a layout jump
.animation(.easeInOut(duration: 0.12), value: rendered)

// Tag tap → note-list filter
withAnimation(.easeInOut(duration: 0.20)) { filterTag = tag }

// Nested tag disclosure
.rotationEffect(.degrees(expanded ? 90 : 0))
.animation(.easeOut(duration: 0.15), value: expanded)
// children: .transition(.opacity.combined(with: .move(edge: .top)))  // 0.20s

// Compose FAB
.scaleEffect(pressed ? 0.92 : 1)
// then push new note: .easeOut(duration: 0.28)

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: checked)   // checkbox
.sensoryFeedback(.impact(flexibility: .soft), trigger: fabTapped) // FAB
.sensoryFeedback(.success, trigger: exportDone)                   // export complete
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Back ("Notes") | `chevron.left` | 18pt |
| Info (top nav) | `info.circle` | 19pt |
| Format / Markdown | `textformat` | 19pt |
| More | `ellipsis` | 19pt |
| Compose FAB | `square.and.pencil` | 22pt |
| Checkbox (unchecked) | `square` | 19pt |
| Checkbox (checked) | `checkmark` (on gradient) | 11pt |
| Tag / #hashtag | `number` | 16pt |
| Smart filter — Notes | `note.text` | 18pt |
| Smart filter — Todo | `checkmark.circle` | 18pt |
| Smart filter — Today | `calendar` | 18pt |
| Smart filter — Archive | `archivebox` | 18pt |
| Smart filter — Trash | `trash` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Info-bar — Table | `tablecells` | 20pt |
| Info-bar — Preview | `eye` | 20pt |
| Info-bar — Export | `square.and.arrow.up` | 20pt |
| Disclosure (nested tag) | `chevron.right` | 14pt |
| Pin note | `pin` | 20pt |
| Link / wiki-link | `link` | 16pt |

## 7. Dark Mode

```swift
struct BearTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.bearCanvasDark : Color.bearCanvas)
            .foregroundStyle(scheme == .dark ? Color.bearTextPrimary : Color.bearTextPrimaryLt)
            .tint(Color.bearOrange) // every interactive glyph is Bear Orange
    }
}

extension View {
    func bearTheme() -> some View { modifier(BearTheme()) }
}
```

The default dark theme ("Charcoal") is `#21252B`, never true black. Surfaces lift to `#282C34`; the red→orange brand gradient is identical across light and dark. Bear ships multiple full themes — model each as a struct of `(canvas, surface1, surface2, divider, syntaxKeyword, syntaxString)`; the brand gradient is the one constant across all of them.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Inter, Lora, JetBrains Mono TTFs via `Info.plist` `UIAppFonts` — all SIL OFL, free to distribute
- Dynamic Type: scale note title, headings, body, quote, preview, meta; keep tag tokens, 12pt info-bar caption, sidebar filter labels, tab labels at FIXED sizes (layout-sensitive)
- VoiceOver: label checkbox rows "Task: {label}, completed" / "not completed"; expose the toggle as the default action. Label #hashtag tokens "Tag: {name}, double-tap to filter". Label the note title as a heading via `.accessibilityAddTraits(.isHeader)`
- The live-Markdown renderer must keep semantic structure: headings get `.isHeader`; the syntax marker dim is *visual only* — VoiceOver should read the styled text, not the raw `#`/`**`
- Color contrast: `#2A2C33` on `#FFFFFF` and `#E8EAED` on `#21252B` both pass WCAG AA for body at 16pt; `#E0566F` hashtags on canvas pass AA; verify any custom tag color against its surface
- Reduce Motion: disable the disclosure-chevron rotation and the checkbox cross-fade (use an instant state change); keep the selection highlight
- Dark mode: the "Charcoal" palette is `#21252B` — NOT pure black; `#2A2C33` text becomes `#E8EAED`. Floating panels add a 1pt `#353A42` border because shadows nearly vanish on the dark canvas
- Respect the user's chosen font family (`bearFontFamily`) everywhere via the environment; only fenced code blocks force JetBrains Mono
