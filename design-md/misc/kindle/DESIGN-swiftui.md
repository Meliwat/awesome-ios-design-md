# Kindle (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Kindle's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, a reading-theme model, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand & Interactive
    static let kdlOrange        = Color(red: 1.000, green: 0.600, blue: 0.000) // #FF9900
    static let kdlOrangePressed = Color(red: 0.910, green: 0.545, blue: 0.000) // #E88B00
    static let kdlBlack         = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let kdlLink          = Color(red: 0.102, green: 0.596, blue: 1.000) // #1A98FF
    static let kdlBlue          = Color(red: 0.310, green: 0.702, blue: 0.851) // #4FB3D9

    // MARK: - Reading Themes (page / ink)
    static let kdlWhitePage  = Color.white                                     // #FFFFFF
    static let kdlWhiteInk   = Color(red: 0.102, green: 0.102, blue: 0.102)    // #1A1A1A
    static let kdlSepiaPage  = Color(red: 0.984, green: 0.941, blue: 0.851)    // #FBF0D9
    static let kdlSepiaInk   = Color(red: 0.373, green: 0.294, blue: 0.196)    // #5F4B32
    static let kdlGreenPage  = Color(red: 0.773, green: 0.882, blue: 0.773)    // #C5E1C5
    static let kdlGreenInk   = Color(red: 0.200, green: 0.286, blue: 0.184)    // #33492F
    static let kdlDarkPage   = Color(red: 0.165, green: 0.165, blue: 0.165)    // #2A2A2A
    static let kdlDarkInk    = Color(red: 0.847, green: 0.847, blue: 0.847)    // #D8D8D8
    static let kdlBlackPage  = Color.black                                     // #000000
    static let kdlBlackInk   = Color(red: 0.784, green: 0.784, blue: 0.784)    // #C8C8C8

    // MARK: - App Chrome (Light)
    static let kdlChromeCanvas  = Color.white                                  // #FFFFFF
    static let kdlSurfaceSubtle = Color(red: 0.957, green: 0.949, blue: 0.933) // #F4F2EE
    static let kdlDivider       = Color(red: 0.894, green: 0.886, blue: 0.867) // #E4E2DD

    // MARK: - App Chrome (Dark)
    static let kdlDarkCanvas   = Color(red: 0.055, green: 0.055, blue: 0.055)  // #0E0E0E
    static let kdlDarkSurface1 = Color(red: 0.102, green: 0.102, blue: 0.102)  // #1A1A1A
    static let kdlDarkSurface2 = Color(red: 0.141, green: 0.141, blue: 0.141)  // #242424
    static let kdlDarkDivider  = Color(red: 0.180, green: 0.180, blue: 0.180)  // #2E2E2E

    // MARK: - Text (Chrome)
    static let kdlTextPrimary    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let kdlTextSecondary  = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B
    static let kdlDarkTextPrimary = Color(red: 0.910, green: 0.910, blue: 0.910) // #E8E8E8
    static let kdlDarkTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A

    // MARK: - Semantic
    static let kdlError   = Color(red: 0.878, green: 0.325, blue: 0.239) // #E0533D
    static let kdlSuccess = Color(red: 0.184, green: 0.682, blue: 0.373) // #2FAE5F
}

// Reading theme model — the user's choice, OS-independent
enum KindleReadingTheme: String, CaseIterable, Identifiable {
    case white, sepia, green, dark, black
    var id: String { rawValue }
    var page: Color {
        switch self { case .white: .kdlWhitePage; case .sepia: .kdlSepiaPage
        case .green: .kdlGreenPage; case .dark: .kdlDarkPage; case .black: .kdlBlackPage }
    }
    var ink: Color {
        switch self { case .white: .kdlWhiteInk; case .sepia: .kdlSepiaInk
        case .green: .kdlGreenInk; case .dark: .kdlDarkInk; case .black: .kdlBlackInk }
    }
}
```

## 2. Typography

Kindle uses a reading serif (**Bookerly**; ship **Bitter** as a free analog) for book text and **Amazon Ember** for chrome. The reading face/size/spacing/margins are user controls — bind them to settings, never hard-code.

```swift
extension Font {
    // Reading — Bitter (Bookerly analog)
    static func kdlReading(_ size: CGFloat) -> Font { .custom("Bitter-Regular", size: size) }
    static let kdlChapterTitle = Font.custom("Bitter-Bold", size: 24)
    static let kdlDropCap      = Font.custom("Bitter-Bold", size: 52)

    // Chrome — Amazon Ember (fallback: SF Pro)
    static let kdlScreenTitle = Font.custom("AmazonEmber-Bold",    size: 30)
    static let kdlBookTitle   = Font.custom("AmazonEmber-Bold",    size: 20)
    static let kdlSubtitle    = Font.custom("AmazonEmber-Medium",  size: 17)
    static let kdlListTitle   = Font.custom("AmazonEmber-Medium",  size: 15)
    static let kdlMeta        = Font.custom("AmazonEmber-Regular", size: 14)
    static let kdlCaption     = Font.custom("AmazonEmber-Medium",  size: 12)
    static let kdlEyebrow     = Font.custom("AmazonEmber-Bold",    size: 11)
    static let kdlTab         = Font.custom("AmazonEmber-Medium",  size: 10)
    static let kdlNote        = Font.custom("AmazonEmber-Regular", size: 13)
}

// User reading settings (bound to a Settings store)
struct KindleReadingSettings {
    var fontName: String = "Bitter-Regular"
    var size: CGFloat = 15.5
    var lineSpacingMultiple: CGFloat = 1.72
    var margin: CGFloat = 26   // Narrow 16 / Standard 26 / Wide 40
    var theme: KindleReadingTheme = .sepia
}
```

## 3. Signature Components

### Reading Page

```swift
struct ReadingPage: View {
    let chapter: String        // "CHAPTER SEVEN"
    let title: String          // "The Lighthouse at Dawn"
    let paragraphs: [String]
    let percent: Int
    let minsLeft: Int
    let settings: KindleReadingSettings
    @Binding var chromeShown: Bool

    var body: some View {
        let theme = settings.theme
        ZStack(alignment: .bottom) {
            theme.page.ignoresSafeArea()

            ScrollView {
                VStack(alignment: .leading, spacing: 16) {
                    Text(chapter)
                        .font(.kdlEyebrow).kerning(1.5)
                        .foregroundStyle(theme.ink.opacity(0.55))
                        .padding(.bottom, 4)
                    Text(title)
                        .font(.kdlChapterTitle)
                        .foregroundStyle(theme.ink)
                        .padding(.bottom, 6)
                    ForEach(Array(paragraphs.enumerated()), id: \.offset) { i, p in
                        Text(p)
                            .font(.custom(settings.fontName, size: settings.size))
                            .foregroundStyle(theme.ink)
                            .lineSpacing(settings.size * (settings.lineSpacingMultiple - 1))
                            .multilineTextAlignment(.leading) // justified: see note
                    }
                }
                .padding(.horizontal, settings.margin)
                .padding(.vertical, 20)
            }
            .scrollIndicators(.hidden)

            // Footer whisper + 2pt orange progress hairline
            VStack(spacing: 8) {
                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Rectangle().fill(theme.ink.opacity(0.18)).frame(height: 2)
                        Rectangle().fill(Color.kdlOrange)
                            .frame(width: geo.size.width * CGFloat(percent) / 100, height: 2)
                    }
                }.frame(height: 2)
                HStack {
                    Text("\(percent)%")
                    Spacer()
                    Text("\(minsLeft) min left in chapter")
                }
                .font(.kdlEyebrow).foregroundStyle(theme.ink.opacity(0.55))
            }
            .padding(.horizontal, 24).padding(.bottom, 14)
        }
        .contentShape(Rectangle())
        .onTapGesture { withAnimation(.easeInOut(duration: 0.2)) { chromeShown.toggle() } }
    }
}
// NOTE: SwiftUI Text has no native justified alignment. For true Kindle
// justified+hyphenated body, render the page with TextKit 2 (UIViewRepresentable
// over NSTextView/UITextView) and set .justified + .byWordWrapping + hyphenationFactor 1.
```

### Aa Typography Panel

```swift
struct AaPanel: View {
    @Binding var settings: KindleReadingSettings

    var body: some View {
        VStack(alignment: .leading, spacing: 18) {
            // Size row
            HStack {
                Text("Aa").font(.system(size: 14))
                Slider(value: $settings.size, in: 12...26, step: 0.5).tint(.kdlOrange)
                Text("Aa").font(.system(size: 24))
            }
            // Theme swatches
            HStack(spacing: 12) {
                ForEach(KindleReadingTheme.allCases) { theme in
                    Circle()
                        .fill(theme.page)
                        .frame(width: 30, height: 30)
                        .overlay(Circle().strokeBorder(.black.opacity(0.18), lineWidth: 1))
                        .overlay(
                            Circle().strokeBorder(Color.kdlOrange, lineWidth: 2)
                                .padding(-4)
                                .opacity(settings.theme == theme ? 1 : 0)
                        )
                        .scaleEffect(settings.theme == theme ? 1.0 : 1.0)
                        .onTapGesture {
                            withAnimation(.easeInOut(duration: 0.15)) { settings.theme = theme }
                            UIImpactFeedbackGenerator(style: .soft).impactOccurred()
                        }
                }
            }
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 12, style: .continuous)
                .fill(settings.theme.page)
        )
    }
}
```

### Library Cover Cell

```swift
struct LibraryCover: View {
    let imageUrl: String?
    let progress: Double      // 0...1
    let author: String

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .bottom) {
                AsyncImage(url: URL(string: imageUrl ?? "")) { img in
                    img.resizable().aspectRatio(contentMode: .fill)
                } placeholder: {
                    Rectangle().fill(Color.kdlDarkSurface2)
                }
                .aspectRatio(2.0/3.0, contentMode: .fit)
                .clipShape(RoundedRectangle(cornerRadius: 4))
                .shadow(color: .black.opacity(0.4), radius: 10, y: 4)

                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Rectangle().fill(.white.opacity(0.2))
                        Rectangle().fill(Color.kdlOrange)
                            .frame(width: geo.size.width * progress)
                    }
                }
                .frame(height: 3)
            }
            Text(progress >= 1 ? "Finished · \(author)" : "\(Int(progress*100))% · \(author)")
                .font(.kdlCaption).foregroundStyle(Color.kdlTextSecondary)
        }
    }
}
```

### Reading Progress Bar

```swift
struct KindleProgress: View {
    let fraction: Double
    let caption: String
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        HStack(spacing: 12) {
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(scheme == .dark ? Color.kdlDarkSurface2 : Color.kdlDivider)
                    Capsule().fill(Color.kdlOrange)
                        .frame(width: geo.size.width * fraction)
                }
            }
            .frame(height: 4)
            Text(caption).font(.kdlCaption)
                .foregroundStyle(scheme == .dark ? Color.kdlDarkTextSecondary : Color.kdlTextSecondary)
                .frame(width: 96, alignment: .leading)
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct KindleTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            LibraryView().tabItem { Label("Library", systemImage: "books.vertical.fill") }
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            MoreView().tabItem { Label("More", systemImage: "ellipsis") }
        }
        .tint(.kdlOrange)   // single accent; no tint pill
    }
}
```

## 5. Motion

```swift
// Chrome fade on book open / center-tap
withAnimation(.easeInOut(duration: 0.2)) { chromeShown.toggle() }
// (on book open: animate out over 0.25s)

// Theme swatch select — ring pop + page cross-fade
withAnimation(.easeInOut(duration: 0.15)) { settings.theme = theme }
UIImpactFeedbackGenerator(style: .soft).impactOccurred()
// ring scale 1.0→1.15→1.0 via a keyframed scaleEffect if desired

// Library progress fill on appear
.onAppear { withAnimation(.easeOut(duration: 0.5)) { shownProgress = progress } }

// Continue-reading card scale-in
.onAppear { withAnimation(.easeOut(duration: 0.25)) { coverScale = 1.0 } } // from 0.96

// Highlight commit — fill selected range 120ms + soft haptic
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Page turn — slide
.transition(.asymmetric(insertion: .move(edge: .trailing), removal: .move(edge: .leading)))
// or a page-curl via UIPageViewController(.pageCurl) bridged in
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Library (tab) | `books.vertical` / `.fill` | 22pt |
| Discover (tab) | `magnifyingglass` | 22pt |
| More (tab) | `ellipsis` | 22pt |
| Back to library | `chevron.left` | 18pt |
| Aa panel | (text "Aa") | 20pt |
| Brightness | `sun.max` | 20pt |
| Bookmark | `bookmark` / `bookmark.fill` | 18pt |
| Search in book | `magnifyingglass` | 18pt |
| Contents / Go To | `list.bullet` | 18pt |
| X-Ray | `person.text.rectangle` | 18pt |
| Sync furthest | `arrow.triangle.2.circlepath` | 16pt |
| Downloaded | `checkmark.circle.fill` | 14pt |
| Not downloaded | `arrow.down.circle` | 14pt |
| Highlight | `highlighter` | 18pt |
| Note | `note.text` | 18pt |
| Dictionary / Lookup | `character.book.closed` | 18pt |
| Share | `square.and.arrow.up` | 18pt |

## 7. Dark Mode

```swift
// Two distinct contexts:
// (1) Reading surface — obeys the USER's KindleReadingTheme, NOT the OS.
// (2) App chrome — follows system colorScheme.

struct KindleChromeTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.kdlDarkCanvas : Color.kdlChromeCanvas)
            .foregroundStyle(scheme == .dark ? Color.kdlDarkTextPrimary : Color.kdlTextPrimary)
            .tint(.kdlOrange)
    }
}
extension View { func kindleChrome() -> some View { modifier(KindleChromeTheme()) } }
```

The reading view never reads `colorScheme` — it renders `settings.theme.page` / `.ink`. On the Black theme, ink is the dimmed `#C8C8C8` (never pure white) to cut halation. App chrome canvas is `#0E0E0E` in system dark, surfaces lift to `#1A1A1A`. Amazon Orange `#FF9900` is identical in every theme — it is the progress and CTA signal. Sheets gain a 1pt theme-divider border on Dark/Black themes since shadows are invisible there. Cover art is never desaturated.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` is iOS 17; fall back to `UIImpactFeedbackGenerator` shown above)
- Bundle Bitter (Regular/Bold) for the Bookerly-analog reading face and Amazon Ember (Regular/Medium/Bold) for chrome — Bitter is SIL OFL (free); Amazon Ember requires Amazon's license, so for non-Amazon builds fall back chrome to SF Pro
- True justified + hyphenated reading body requires TextKit 2 (UITextView/NSTextView bridged) — SwiftUI `Text` cannot justify; provide it as a `UIViewRepresentable`
- The reading size control is **in-app**, but honor system Dynamic Type as a floor when the user enables "Match system text size"
- VoiceOver: the reading page should expose paragraphs as accessible text with reading order; the footer whisper as "38 percent, 14 minutes left in chapter"; theme swatches labeled "Sepia theme, selected"
- Cover art: `.accessibilityLabel("\(title) by \(author), \(Int(progress*100)) percent read")`
- Color contrast: every theme's ink/page pair is tuned to pass WCAG AA at reading size; the Black theme deliberately uses `#C8C8C8` not white to balance contrast vs halation
- Reduce Motion: disable the chrome fade animation (instant toggle), the theme ring pop, and progress fill — keep the state changes
- Reduce Transparency: make the reader top bar and tab bar opaque theme-tinted instead of translucent
- VoiceOver page turn: provide explicit "Next page" / "Previous page" custom actions in addition to the tap zones
- OpenDyslexic: ship it as a selectable reading face for accessibility, matching Kindle's own option
