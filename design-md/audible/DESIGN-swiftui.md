# Audible (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Audible's visual language into paste-ready SwiftUI code: `Color` extensions, the serif/sans `Font` pairing, the signature speed-dial + 30s-skip player, the cover progress ring, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let audCanvas   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let audSurface1 = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let audSurface2 = Color(red: 0.204, green: 0.204, blue: 0.204) // #343434
    static let audDivider  = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A

    // MARK: - Text
    static let audTextPrimary   = Color.white                                // #FFFFFF
    static let audTextSecondary = Color(red: 0.690, green: 0.690, blue: 0.690) // #B0B0B0
    static let audTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E

    // MARK: - Brand
    static let audOrange        = Color(red: 1.0,   green: 0.600, blue: 0.0)  // #FF9900
    static let audOrangePressed = Color(red: 0.902, green: 0.541, blue: 0.0)  // #E68A00
    static let audErrorRed      = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}

extension ShapeStyle where Self == Color {
    static var audOrangeGlow: Color { Color(red: 1.0, green: 0.6, blue: 0.0).opacity(0.28) }
}
```

## 2. Typography

Two families, strict roles. **Playfair Display** for headings/titles only; **Inter** for body/UI. Both are on Google Fonts — bundle the TTFs via `Info.plist` (`UIAppFonts`). Fall back to `.system(design: .serif)` and `.system(design: .default)` respectively.

```swift
extension Font {
    // Headings — Playfair Display (serif) ONLY
    static let audTitleLarge = Font.custom("PlayfairDisplay-Bold", size: 30).weight(.bold)
    static let audBookTitle  = Font.custom("PlayfairDisplay-Bold", size: 26).weight(.bold)
    static let audSection    = Font.custom("PlayfairDisplay-Bold", size: 22).weight(.bold)
    static let audCardTitle  = Font.custom("PlayfairDisplay-Bold", size: 17).weight(.bold)
    static let audMiniTitle  = Font.custom("PlayfairDisplay-Bold", size: 14).weight(.bold)

    // Body / UI — Inter (sans)
    static let audAuthor     = Font.custom("Inter-Regular",  size: 14).weight(.regular)
    static let audNarrator   = Font.custom("Inter-SemiBold", size: 13).weight(.semibold)
    static let audChapter    = Font.custom("Inter-SemiBold", size: 16).weight(.semibold)
    static let audBody       = Font.custom("Inter-Regular",  size: 15).weight(.regular)
    static let audCaptions   = Font.custom("Inter-Regular",  size: 18).weight(.regular)
    static let audMeta       = Font.custom("Inter-Regular",  size: 13).weight(.regular)
    static let audLabelUpper = Font.custom("Inter-Bold",     size: 11).weight(.bold)
    static let audButton     = Font.custom("Inter-Bold",     size: 16).weight(.bold)
    static let audButtonSec  = Font.custom("Inter-SemiBold", size: 14).weight(.semibold)
    static let audSpeed      = Font.custom("Inter-Bold",     size: 15).weight(.bold)
    static let audTab        = Font.custom("Inter-SemiBold", size: 10).weight(.semibold)
}

extension Font {
    static func audSerif(_ size: CGFloat) -> Font { .system(size: size, weight: .bold, design: .serif) }
    static func audSans(_ size: CGFloat, weight: Font.Weight = .regular) -> Font { .system(size: size, weight: weight, design: .default) }
}
```

Apply `.monospacedDigit()` to time-remaining and the speed value.

## 3. Signature Components

### Cover with Orange Progress Ring

```swift
struct CoverProgressRing: View {
    let artwork: Image
    let progress: Double          // 0...1
    var size: CGFloat = 280
    var ring: CGFloat = 4

    var body: some View {
        ZStack {
            Circle()
                .stroke(Color.audDivider, lineWidth: ring)
            Circle()
                .trim(from: 0, to: progress)
                .stroke(Color.audOrange, style: StrokeStyle(lineWidth: ring, lineCap: .round))
                .rotationEffect(.degrees(-90))
            artwork
                .resizable().aspectRatio(1, contentMode: .fill)
                .frame(width: size - ring * 6, height: size - ring * 6)
                .clipShape(RoundedRectangle(cornerRadius: 10))
                .shadow(color: .black.opacity(0.5), radius: 36, y: 14)
        }
        .frame(width: size, height: size)
    }
}
```

### Speed-Dial + 30s-Skip Transport (Signature)

```swift
struct AudiblePlayButton: View {
    let isPlaying: Bool
    var size: CGFloat = 72
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                .font(.system(size: size * 0.44, weight: .bold))
                .foregroundStyle(Color.audCanvas) // dark glyph on orange
                .frame(width: size, height: size)
                .background(Circle().fill(Color.audOrange))
                .shadow(color: .audOrangeGlow, radius: 22, y: 6)
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: isPlaying)
        .buttonStyle(AudPressable(pressedScale: 0.93))
    }
}

struct SkipButton: View {
    enum Dir { case back, forward }
    let dir: Dir
    let action: () -> Void
    @State private var flash = false

    var body: some View {
        Button {
            withAnimation(.easeOut(duration: 0.18)) { flash = true }
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.2) { flash = false }
            action()
        } label: {
            Image(systemName: dir == .back ? "gobackward.30" : "goforward.30")
                .font(.system(size: 30, weight: .regular))
                .foregroundStyle(flash ? Color.audOrange : .white)
                .frame(width: 44, height: 44)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: flash)
        .buttonStyle(AudPressable(pressedScale: 0.9))
    }
}

struct AudPressable: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.72), value: configuration.isPressed)
    }
}
```

### Speed Dial Sheet

```swift
struct SpeedDialSheet: View {
    @Binding var speed: Double      // 0.5 ... 3.5
    private let presets: [Double] = [1.0, 1.25, 1.5, 2.0]

    var body: some View {
        VStack(spacing: 24) {
            Capsule().fill(Color.audTextTertiary).frame(width: 36, height: 4).padding(.top, 10)

            Text(String(format: "%.2f×", speed).replacingOccurrences(of: ".00", with: ".0"))
                .font(.custom("Inter-Bold", size: 22)).monospacedDigit()
                .foregroundStyle(Color.audOrange)
                .contentTransition(.numericText())

            Slider(value: $speed, in: 0.5...3.5, step: 0.05) { _ in }
                .tint(.audOrange)
                .onChange(of: speed) { _, new in
                    if (new * 4).rounded() == new * 4 { // .25 detent
                        UISelectionFeedbackGenerator().selectionChanged()
                    }
                }

            HStack(spacing: 10) {
                ForEach(presets, id: \.self) { p in
                    Button {
                        withAnimation(.snappy) { speed = p }
                    } label: {
                        Text(String(format: "%.2g×", p))
                            .font(.audSpeed)
                            .foregroundStyle(speed == p ? Color.audCanvas : .white)
                            .padding(.vertical, 8).padding(.horizontal, 16)
                            .background(RoundedRectangle(cornerRadius: 8)
                                .fill(speed == p ? Color.audOrange : Color.audSurface2))
                    }
                }
            }
            Spacer()
        }
        .padding(.horizontal, 20)
        .background(Color.audSurface1)
        .presentationDetents([.height(280)])
        .presentationCornerRadius(16)
    }
}
```

### Player Hero

```swift
struct AudiblePlayer: View {
    let title: String
    let author: String
    let narrator: String
    let artwork: Image
    @State private var progress = 0.34
    @State private var isPlaying = true
    @State private var speed = 1.5
    @State private var showSpeed = false

    var body: some View {
        VStack(spacing: 32) {
            Spacer()
            CoverProgressRing(artwork: artwork, progress: progress)

            VStack(spacing: 6) {
                Text(title).font(.audTitleLarge).foregroundStyle(.white).multilineTextAlignment(.center)
                Text(author).font(.audAuthor).foregroundStyle(.audTextSecondary)
                Text(narrator).font(.audNarrator).foregroundStyle(.audTextSecondary)
            }

            HStack(spacing: 36) {
                SkipButton(dir: .back) { progress = max(0, progress - 0.01) }
                AudiblePlayButton(isPlaying: isPlaying) { isPlaying.toggle() }
                SkipButton(dir: .forward) { progress = min(1, progress + 0.01) }
            }

            Text("8 hrs 14 min left").font(.audMeta).foregroundStyle(.audTextSecondary).monospacedDigit()

            HStack(spacing: 24) {
                Button { showSpeed = true } label: {
                    Text(String(format: "%.2g×", speed)).font(.audSpeed).foregroundStyle(Color.audOrange)
                        .padding(.vertical, 8).padding(.horizontal, 14)
                        .background(Capsule().fill(Color.audSurface2))
                }
                Image(systemName: "moon.zzz").foregroundStyle(.audTextSecondary)
                Image(systemName: "bookmark").foregroundStyle(.audTextSecondary)
                Image(systemName: "list.bullet").foregroundStyle(.audTextSecondary)
                Image(systemName: "car").foregroundStyle(.audTextSecondary)
            }
            .font(.system(size: 22))
            Spacer()
        }
        .padding(.horizontal, 24)
        .background(Color.audCanvas.ignoresSafeArea())
        .sheet(isPresented: $showSpeed) { SpeedDialSheet(speed: $speed) }
    }
}
```

### Continue-Listening Row

```swift
struct ContinueRow: View {
    let title: String
    let author: String
    let remaining: String
    let artwork: Image
    let progress: Double

    var body: some View {
        HStack(spacing: 14) {
            CoverProgressRing(artwork: artwork, progress: progress, size: 72, ring: 3)
            VStack(alignment: .leading, spacing: 3) {
                Text(title).font(.audCardTitle).foregroundStyle(.white).lineLimit(1)
                Text(author).font(.custom("Inter-Regular", size: 13)).foregroundStyle(.audTextSecondary).lineLimit(1)
                Text(remaining).font(.custom("Inter-Regular", size: 12)).foregroundStyle(.audTextSecondary)
            }
            Spacer()
            Image(systemName: "play.fill")
                .font(.system(size: 18, weight: .bold)).foregroundStyle(Color.audCanvas)
                .frame(width: 56, height: 56).background(Circle().fill(Color.audOrange))
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.audSurface1))
    }
}
```

### Chapter List Sheet

```swift
struct ChapterListSheet: View {
    let chapters: [(n: Int, title: String, dur: String, state: ChapterState)]
    enum ChapterState { case playing, finished, upcoming }

    var body: some View {
        VStack(spacing: 0) {
            Capsule().fill(Color.audTextTertiary).frame(width: 36, height: 4).padding(.vertical, 10)
            Text("Chapters").font(.audSection).foregroundStyle(.white)
                .frame(maxWidth: .infinity, alignment: .leading).padding(.horizontal, 16).padding(.bottom, 8)
            ScrollView {
                ForEach(chapters, id: \.n) { c in
                    HStack(spacing: 12) {
                        if c.state == .playing {
                            Rectangle().fill(Color.audOrange).frame(width: 3, height: 28)
                        } else {
                            Color.clear.frame(width: 3, height: 28)
                        }
                        Text("\(c.n).").font(.audChapter).foregroundStyle(.audTextSecondary)
                        Text(c.title)
                            .font(.audChapter)
                            .foregroundStyle(c.state == .playing ? Color.audOrange : .white)
                        Spacer()
                        if c.state == .finished {
                            Image(systemName: "checkmark").font(.system(size: 12, weight: .bold)).foregroundStyle(Color.audOrange)
                        }
                        Text(c.dur).font(.audMeta).foregroundStyle(.audTextSecondary).monospacedDigit()
                    }
                    .padding(.horizontal, 16).frame(height: 56)
                    Divider().overlay(Color.audDivider)
                }
            }
        }
        .background(Color.audSurface1)
        .presentationDetents([.medium, .large])
        .presentationCornerRadius(16)
    }
}
```

## 4. Captions (Synced Text)

```swift
struct CaptionsPanel: View {
    let line: String
    let activeWordIndex: Int
    var body: some View {
        let words = line.split(separator: " ").map(String.init)
        FlowLayout(spacing: 4) {
            ForEach(words.indices, id: \.self) { i in
                Text(words[i])
                    .font(.audCaptions)
                    .foregroundStyle(i == activeWordIndex ? .white : Color.audTextSecondary)
                    .animation(.easeOut(duration: 0.18), value: activeWordIndex)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.audSurface1))
    }
}
// FlowLayout: a simple wrapping Layout (omitted for brevity).
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        a.backgroundColor = UIColor(Color.audCanvas).withAlphaComponent(0.96)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            LibraryView().tabItem { Label("Library", systemImage: "books.vertical.fill") }
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.audOrange)
    }
}
```

## 6. Motion

```swift
// Play tap: AudPressable scale 0.93 spring(damping 0.72) + .sensoryFeedback(.impact(weight: .medium))

// 30s skip: scale 0.9 + orange flash 0.2s + .sensoryFeedback(.impact(weight: .light)); ring jumps 30s

// Speed dial: each .25 tick → UISelectionFeedbackGenerator().selectionChanged();
// big number uses .contentTransition(.numericText())

// Cover progress ring: animate .trim linearly with playback position (no easing)

// Chapter change: the orange leading bar moves with .animation(.easeInOut(duration: 0.22))

// Mini-bar → Full player: matchedGeometryEffect on the cover, .spring(response: 0.32)

// Captions: each line/word .animation(.easeOut(duration: 0.18))
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 32pt |
| Pause | `pause.fill` | 32pt |
| Skip back 30s | `gobackward.30` | 30pt |
| Skip forward 30s | `goforward.30` | 30pt |
| Sleep timer | `moon.zzz` | 22pt |
| Bookmark | `bookmark` / `bookmark.fill` | 22pt |
| Chapters | `list.bullet` | 22pt |
| Car mode | `car` | 22pt |
| Captions | `captions.bubble` / `captions.bubble.fill` | 22pt |
| Finished check | `checkmark` | 12pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Library (tab) | `books.vertical.fill` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` + `.contentTransition(.numericText())` need iOS 17 — fall back to `UISelectionFeedbackGenerator` and a cross-fade)
- Support Dynamic Type on titles, authors, body, chapter titles, and **captions especially** (readability while listening is the priority — let captions scale generously); pin the speed value and tab labels
- VoiceOver: the Play button label = "Play <title>"; the skip buttons = "Skip back 30 seconds" / "Skip forward 30 seconds"; expose speed as an adjustable value ("Playback speed, 1.5×, adjustable")
- The cover progress ring: mirror progress with `.accessibilityValue("34 percent, 8 hours 14 minutes remaining")`
- Contrast: `#B0B0B0` secondary on `#1A1A1A` passes WCAG AA at 14pt+; validate the 12-13pt meta and lighten toward `#BDBDBD` if targeting strict compliance
- Serif headings: Playfair Display's high stroke contrast can thin out at small Dynamic Type sizes — keep the serif at 17pt+ and never use it for captions or metadata
- Force dark for the player/library: set `.preferredColorScheme(.dark)` on those flows — the warm charcoal is core to the identity
