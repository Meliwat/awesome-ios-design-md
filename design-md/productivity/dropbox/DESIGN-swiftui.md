# Dropbox (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Dropbox's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let dbxCanvas     = Color.white                                   // #FFFFFF
    static let dbxSurface    = Color(red: 0.969, green: 0.961, blue: 0.949)   // #F7F5F2
    static let dbxDivider    = Color(red: 0.902, green: 0.882, blue: 0.855)   // #E6E1DA

    // MARK: - Text
    static let dbxTextPrimary   = Color(red: 0.118, green: 0.098, blue: 0.098) // #1E1919
    static let dbxTextSecondary = Color(red: 0.435, green: 0.416, blue: 0.396) // #6F6A65
    static let dbxTextTertiary  = Color(red: 0.639, green: 0.620, blue: 0.596) // #A39E98

    // MARK: - Brand
    static let dbxBlue        = Color(red: 0.0,   green: 0.380, blue: 1.0)     // #0061FF
    static let dbxBluePressed = Color(red: 0.0,   green: 0.314, blue: 0.816)   // #0050D0
    static let dbxBlueTint    = Color(red: 0.902, green: 0.941, blue: 1.0)     // #E6F0FF

    // MARK: - Dark
    static let dbxDarkCanvas  = Color(red: 0.118, green: 0.098, blue: 0.098)   // #1E1919
    static let dbxDarkSurface = Color(red: 0.165, green: 0.141, blue: 0.141)   // #2A2424
    static let dbxDarkDivider = Color(red: 0.227, green: 0.200, blue: 0.192)   // #3A3331
    static let dbxDarkBlue    = Color(red: 0.239, green: 0.545, blue: 1.0)     // #3D8BFF

    // MARK: - File-Type Icons
    static let dbxPdfRed    = Color(red: 0.980, green: 0.333, blue: 0.118)     // #FA551E
    static let dbxSheetGreen = Color(red: 0.102, green: 0.529, blue: 0.329)    // #1A8754
    static let dbxImageTeal = Color(red: 0.0,   green: 0.698, blue: 0.663)     // #00B2A9
    static let dbxFolderSlate = Color(red: 0.549, green: 0.592, blue: 0.659)   // #8C97A8

    // MARK: - Semantic
    static let dbxSuccess = Color(red: 0.102, green: 0.529, blue: 0.329)       // #1A8754
    static let dbxWarning = Color(red: 1.0,   green: 0.686, blue: 0.0)         // #FFAF00
    static let dbxError   = Color(red: 0.820, green: 0.094, blue: 0.043)       // #D1180B
}
```

## 2. Typography

Sharp Grotesk is Dropbox's licensed brand typeface. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. The closest free substitute is **Inter** — a clean grotesque with a similar tone.

```swift
extension Font {
    static let dbxTitleLarge  = Font.custom("SharpGrotesk-Bold",     size: 28).weight(.bold)
    static let dbxSection     = Font.custom("SharpGrotesk-Bold",     size: 22).weight(.bold)
    static let dbxSheetTitle  = Font.custom("SharpGrotesk-Bold",     size: 20).weight(.bold)
    static let dbxCardTitle   = Font.custom("SharpGrotesk-Semibold", size: 17).weight(.semibold)
    static let dbxRowName     = Font.custom("SharpGrotesk-Semibold", size: 16).weight(.semibold)
    static let dbxBody        = Font.custom("SharpGrotesk-Regular",  size: 15).weight(.regular)
    static let dbxMeta        = Font.custom("SharpGrotesk-Regular",  size: 14).weight(.regular)
    static let dbxCaption     = Font.custom("SharpGrotesk-Regular",  size: 12).weight(.regular)
    static let dbxLabelUpper  = Font.custom("SharpGrotesk-Bold",     size: 11).weight(.bold)
    static let dbxButton      = Font.custom("SharpGrotesk-Semibold", size: 16).weight(.semibold)
    static let dbxTab         = Font.custom("SharpGrotesk-Semibold", size: 11).weight(.semibold)
}

// If Sharp Grotesk / Inter is unavailable, fall back to the system grotesque:
extension Font {
    static func dbx(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}

// Tabular figures for file sizes / dates / counts
extension View {
    func dbxTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Primary Button

```swift
struct DbxPrimaryButton: View {
    let title: String
    var enabled: Bool = true
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.dbxButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 14)
                .background(
                    RoundedRectangle(cornerRadius: 8)
                        .fill(Color.dbxBlue.opacity(enabled ? 1 : 0.4))
                )
        }
        .disabled(!enabled)
        .buttonStyle(DbxPressableStyle(pressedScale: 0.98))
    }
}

struct DbxPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Upload / Create FAB

```swift
struct DbxUploadFAB: View {
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .semibold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.dbxBlue))
                .shadow(color: Color.dbxBlue.opacity(0.32), radius: 20, y: 8)
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
        .buttonStyle(DbxPressableStyle(pressedScale: 0.92))
        .padding(.trailing, 16)
        .padding(.bottom, 16) // sits above the tab bar
    }
}
```

### File / Folder Row

```swift
enum DbxFileKind { case pdf, doc, sheet, image, folder }

struct DbxFileRow: View {
    let name: String
    let meta: String
    let kind: DbxFileKind
    var isSelected: Bool = false
    let onTap: () -> Void

    private var iconColor: Color {
        switch kind {
        case .pdf:    return .dbxPdfRed
        case .doc:    return .dbxBlue
        case .sheet:  return .dbxSheetGreen
        case .image:  return .dbxImageTeal
        case .folder: return .dbxFolderSlate
        }
    }
    private var iconSymbol: String {
        switch kind {
        case .pdf:    return "doc.richtext.fill"
        case .doc:    return "doc.text.fill"
        case .sheet:  return "tablecells.fill"
        case .image:  return "photo.fill"
        case .folder: return "folder.fill"
        }
    }

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 12) {
                if isSelected {
                    Image(systemName: "checkmark.circle.fill")
                        .font(.system(size: 28))
                        .foregroundStyle(Color.dbxBlue)
                        .frame(width: 40, height: 40)
                } else {
                    RoundedRectangle(cornerRadius: 8)
                        .fill(iconColor.opacity(0.14))
                        .frame(width: 40, height: 40)
                        .overlay(
                            Image(systemName: iconSymbol)
                                .font(.system(size: 18, weight: .semibold))
                                .foregroundStyle(iconColor)
                        )
                }

                VStack(alignment: .leading, spacing: 2) {
                    Text(name)
                        .font(.dbxRowName)
                        .foregroundStyle(Color.dbxTextPrimary)
                        .lineLimit(1)
                    Text(meta)
                        .font(.dbxMeta)
                        .foregroundStyle(Color.dbxTextSecondary)
                        .dbxTabularNumbers()
                        .lineLimit(1)
                }

                Spacer()

                Image(systemName: kind == .folder ? "chevron.right" : "ellipsis")
                    .font(.system(size: 18, weight: .semibold))
                    .foregroundStyle(Color.dbxTextSecondary)
            }
            .padding(.horizontal, 16)
            .frame(height: 60)
            .background(isSelected ? Color.dbxBlueTint : Color.dbxCanvas)
            .contentShape(Rectangle())
        }
        .buttonStyle(.plain)
    }
}
```

### Recent File Card

```swift
struct DbxRecentCard: View {
    let name: String
    let meta: String
    let thumbnail: Image?

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ZStack {
                RoundedRectangle(cornerRadius: 4).fill(Color.dbxSurface)
                if let thumbnail {
                    thumbnail.resizable().aspectRatio(contentMode: .fill)
                        .clipShape(RoundedRectangle(cornerRadius: 4))
                } else {
                    Image(systemName: "doc.fill")
                        .font(.system(size: 28)).foregroundStyle(Color.dbxBlue)
                }
            }
            .frame(width: 140, height: 96)

            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.dbxCardTitle)
                    .foregroundStyle(Color.dbxTextPrimary).lineLimit(2)
                Text(meta).font(.dbxCaption)
                    .foregroundStyle(Color.dbxTextSecondary).dbxTabularNumbers()
            }
        }
        .padding(12)
        .frame(width: 140, height: 160, alignment: .top)
        .background(
            RoundedRectangle(cornerRadius: 12)
                .fill(Color.dbxCanvas)
                .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.dbxDivider, lineWidth: 1))
        )
    }
}
```

### Upload Progress Bar

```swift
struct DbxUploadBar: View {
    let label: String
    let progress: Double // 0...1, real byte ratio
    var done: Bool = false

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            HStack {
                Text(label).font(.dbxMeta.weight(.semibold))
                    .foregroundStyle(Color.dbxTextPrimary)
                Spacer()
                if done {
                    Image(systemName: "checkmark.circle.fill")
                        .foregroundStyle(Color.dbxSuccess)
                }
            }
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.dbxDivider)
                    Capsule().fill(Color.dbxBlue)
                        .frame(width: geo.size.width * progress)
                        .animation(.linear(duration: 0.2), value: progress)
                }
            }
            .frame(height: 3)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 12)
        .background(Color.dbxCanvas)
    }
}
```

## 4. Tab Bar

```swift
struct DbxRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = UIColor(Color.dbxCanvas).withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem    { Label("Home",    systemImage: "house") }
            FilesView().tabItem   { Label("Files",   systemImage: "folder") }
            PhotosView().tabItem  { Label("Photos",  systemImage: "photo.on.rectangle") }
            OfflineView().tabItem { Label("Offline", systemImage: "arrow.down.circle") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.dbxBlue) // active = Dropbox Blue
    }
}
```

## 5. Photo Grid (contact-sheet)

```swift
struct DbxPhotoGrid: View {
    let photos: [Image]
    @State private var selected: Set<Int> = []

    private let columns = Array(repeating: GridItem(.flexible(), spacing: 2), count: 3)

    var body: some View {
        ScrollView {
            LazyVGrid(columns: columns, spacing: 2) {
                ForEach(photos.indices, id: \.self) { i in
                    photos[i]
                        .resizable().aspectRatio(1, contentMode: .fill)
                        .frame(maxWidth: .infinity)
                        .clipped()
                        .overlay(alignment: .topTrailing) {
                            if selected.contains(i) {
                                Image(systemName: "checkmark.circle.fill")
                                    .foregroundStyle(Color.dbxBlue, .white)
                                    .padding(6)
                            }
                        }
                        .overlay(
                            Rectangle().strokeBorder(
                                selected.contains(i) ? Color.dbxBlue : .clear, lineWidth: 4)
                        )
                        .onLongPressGesture { toggle(i) }
                }
            }
        }
    }
    private func toggle(_ i: Int) {
        if selected.contains(i) { selected.remove(i) } else { selected.insert(i) }
    }
}
```

## 6. Motion

```swift
// FAB tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: fabTapped)

// Star toggle: scale bounce 1.0 → 1.12 → 1.0
@State private var starred = false
.scaleEffect(starred ? 1.0 : 1.0)
.animation(.spring(response: 0.24, dampingFraction: 0.55), value: starred)
.sensoryFeedback(.success, trigger: starred)

// Upload progress — drive `progress` from URLSession bytes; .linear(0.2) keeps it honest
// Sheet present — use `.presentationDetents` + default spring; scrim is system default
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Upload / Create FAB | `plus` | 24pt |
| PDF file | `doc.richtext.fill` | 18pt |
| Doc file | `doc.text.fill` | 18pt |
| Sheet file | `tablecells.fill` | 18pt |
| Image file | `photo.fill` | 18pt |
| Folder | `folder.fill` | 18pt |
| Selected | `checkmark.circle.fill` | 28pt |
| More | `ellipsis` | 18pt |
| Folder chevron | `chevron.right` | 18pt |
| Star | `star` / `star.fill` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Download / Offline | `arrow.down.circle` | 22-24pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house` / `house.fill` | 24pt |
| Files (tab) | `folder` / `folder.fill` | 24pt |
| Photos (tab) | `photo.on.rectangle` | 24pt |
| Account (tab) | `person.crop.circle` | 24pt |
| Success | `checkmark.circle.fill` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on file names, metadata, body, and empty-state copy — pin tab labels and the progress-bar height
- Use `.monospacedDigit()` on file sizes, dates, and counts so columns stay aligned at any type size
- VoiceOver: label the FAB ("Upload or create"), and merge file-row name + metadata while exposing the ellipsis as a separate "More actions" button
- Contrast: `#6F6A65` secondary on `#FFFFFF` passes WCAG AA at 14pt+; on the dark canvas use `#3D8BFF` for blue text/icons to keep AA contrast over `#1E1919`
- Respect `prefersCrossFadeTransitions` — keep the thumbnail decode fade short (200ms) and skip it under Reduce Motion
