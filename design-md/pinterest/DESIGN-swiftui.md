# Pinterest (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pinterest's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the masonry grid, Save button, and pin detail.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let pinterestRed        = Color(red: 0.902, green: 0.0,   blue: 0.137)  // #E60023
    static let pinterestRedPressed = Color(red: 0.678, green: 0.031, blue: 0.106)  // #AD081B
    static let pinterestRedHover   = Color(red: 0.8,   green: 0.0,   blue: 0.125)  // #CC0020

    // MARK: - Canvas (Light)
    static let pinterestCanvasLight   = Color.white                                 // #FFFFFF
    static let pinterestSurface1Light = Color(red: 0.973, green: 0.973, blue: 0.973) // #F8F8F8
    static let pinterestInputLight    = Color(red: 0.937, green: 0.937, blue: 0.937) // #EFEFEF
    static let pinterestDividerLight  = Color(red: 0.914, green: 0.914, blue: 0.914) // #E9E9E9

    // MARK: - Canvas (Dark)
    static let pinterestCanvasDark   = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let pinterestSurface1Dark = Color(red: 0.118, green: 0.118, blue: 0.118) // #1E1E1E
    static let pinterestSurface2Dark = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let pinterestDividerDark  = Color(red: 0.180, green: 0.180, blue: 0.180) // #2E2E2E

    // MARK: - Text
    static let pinterestTextPrimaryLight   = Color(red: 0.067, green: 0.067, blue: 0.067) // #111111
    static let pinterestTextSecondaryLight = Color(red: 0.463, green: 0.463, blue: 0.463) // #767676
    static let pinterestTextTertiaryLight  = Color(red: 0.710, green: 0.710, blue: 0.710) // #B5B5B5
    static let pinterestTextPrimaryDark    = Color.white                                   // #FFFFFF
    static let pinterestTextSecondaryDark  = Color(red: 0.667, green: 0.667, blue: 0.667) // #AAAAAA

    // MARK: - Semantic
    static let pinterestSuccess = Color(red: 0.0, green: 0.541, blue: 0.235) // #008A3C
    static let pinterestInfo    = Color(red: 0.0, green: 0.455, blue: 0.910) // #0074E8
}
```

## 2. Typography

Pinterest Sans is proprietary. Bundle TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to SF Pro — geometric humanist sans is the closest free substitute.

```swift
extension Font {
    static let pinterestPinDetailTitle = Font.custom("PinterestSans-Bold",     size: 28).weight(.bold)
    static let pinterestLargeTitle     = Font.custom("PinterestSans-Bold",     size: 24).weight(.bold)
    static let pinterestSectionHeader  = Font.custom("PinterestSans-Bold",     size: 20).weight(.bold)
    static let pinterestBoardName      = Font.custom("PinterestSans-Bold",     size: 17).weight(.bold)
    static let pinterestPinTitle       = Font.custom("PinterestSans-Medium",   size: 14).weight(.medium)
    static let pinterestUsername       = Font.custom("PinterestSans-Semibold", size: 14).weight(.semibold)
    static let pinterestBody           = Font.custom("PinterestSans-Regular",  size: 15).weight(.regular)
    static let pinterestComment        = Font.custom("PinterestSans-Regular",  size: 14).weight(.regular)
    static let pinterestMeta           = Font.custom("PinterestSans-Regular",  size: 12).weight(.regular)
    static let pinterestButton         = Font.custom("PinterestSans-Bold",     size: 16).weight(.bold)
    static let pinterestButtonSmall    = Font.custom("PinterestSans-Semibold", size: 14).weight(.semibold)
    static let pinterestTabLabel       = Font.custom("PinterestSans-Semibold", size: 10).weight(.semibold)
    static let pinterestMicro          = Font.custom("PinterestSans-Bold",     size: 11).weight(.bold)

    // System fallback if Pinterest Sans isn't bundled
    static func pinterest(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Save Button (The Red → Black Morph)

```swift
struct PinterestSaveButton: View {
    @Binding var isSaved: Bool
    var onTap: () -> Void = {}

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.25, dampingFraction: 0.8)) {
                isSaved.toggle()
            }
            onTap()
        } label: {
            Text(isSaved ? "Saved" : "Save")
                .font(.pinterestButton)
                .foregroundStyle(.white)
                .padding(.vertical, 10)
                .padding(.horizontal, 20)
                .background(
                    Capsule().fill(isSaved ? Color.pinterestTextPrimaryLight : Color.pinterestRed)
                )
        }
        .sensoryFeedback(.success, trigger: isSaved)
        .buttonStyle(PinterestPressableStyle(pressedScale: 0.97))
    }
}

struct PinterestPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.2, dampingFraction: 0.75),
                       value: configuration.isPressed)
    }
}
```

### Primary Pill (Follow / Sign up)

```swift
struct PinterestPillButton: View {
    let title: String
    var style: Style = .primary
    let action: () -> Void

    enum Style { case primary, secondary }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.pinterestButton)
                .foregroundStyle(style == .primary ? .white : Color.pinterestTextPrimaryLight)
                .padding(.vertical, 12)
                .padding(.horizontal, 24)
                .background(
                    Capsule().fill(style == .primary ? Color.pinterestRed : Color.pinterestInputLight)
                )
        }
        .buttonStyle(PinterestPressableStyle())
    }
}
```

### Pin Tile (for Masonry Grid)

```swift
struct PinTile: View {
    let imageURL: URL
    let title: String
    let creatorName: String
    let creatorAvatarURL: URL
    let aspectRatio: CGFloat  // width / height — e.g. 0.75 for a 3:4 pin
    let width: CGFloat
    @State private var isSaved = false

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            ZStack(alignment: .topTrailing) {
                AsyncImage(url: imageURL) { image in
                    image.resizable().scaledToFill()
                } placeholder: {
                    Rectangle().fill(Color.pinterestSurface1Light)
                }
                .frame(width: width, height: width / aspectRatio)
                .clipShape(RoundedRectangle(cornerRadius: 16))

                PinterestSaveButton(isSaved: $isSaved)
                    .padding(12)
            }

            Text(title)
                .font(.pinterestPinTitle)
                .foregroundStyle(Color.pinterestTextPrimaryLight)
                .lineLimit(2)
                .padding(.top, 4)

            HStack(spacing: 6) {
                AsyncImage(url: creatorAvatarURL) { image in
                    image.resizable().scaledToFill()
                } placeholder: {
                    Circle().fill(Color.pinterestSurface1Light)
                }
                .frame(width: 20, height: 20)
                .clipShape(Circle())

                Text(creatorName)
                    .font(.pinterestMeta)
                    .foregroundStyle(Color.pinterestTextSecondaryLight)
                    .lineLimit(1)
            }
        }
    }
}
```

### Masonry Two-Column Layout

SwiftUI has no built-in masonry. The idiomatic solution is to split items into two columns based on their cumulative heights and render each column as a `VStack`.

```swift
struct Pin: Identifiable {
    let id = UUID()
    let imageURL: URL
    let aspectRatio: CGFloat
    let title: String
    let creatorName: String
    let creatorAvatarURL: URL
}

struct MasonryGrid: View {
    let pins: [Pin]
    let columnGap: CGFloat = 8
    let horizontalMargin: CGFloat = 16

    var body: some View {
        GeometryReader { geo in
            let columnWidth = (geo.size.width - horizontalMargin * 2 - columnGap) / 2
            let (left, right) = distribute(pins: pins, columnWidth: columnWidth)

            ScrollView {
                HStack(alignment: .top, spacing: columnGap) {
                    column(pins: left, width: columnWidth)
                    column(pins: right, width: columnWidth)
                }
                .padding(.horizontal, horizontalMargin)
                .padding(.vertical, 12)
            }
        }
    }

    private func column(pins: [Pin], width: CGFloat) -> some View {
        VStack(spacing: 8) {
            ForEach(pins) { pin in
                PinTile(
                    imageURL: pin.imageURL,
                    title: pin.title,
                    creatorName: pin.creatorName,
                    creatorAvatarURL: pin.creatorAvatarURL,
                    aspectRatio: pin.aspectRatio,
                    width: width
                )
            }
        }
    }

    // Split pins into two columns by cumulative height so the shorter column gets the next pin.
    private func distribute(pins: [Pin], columnWidth: CGFloat) -> ([Pin], [Pin]) {
        var leftHeight: CGFloat = 0, rightHeight: CGFloat = 0
        var left: [Pin] = [], right: [Pin] = []
        for pin in pins {
            let h = columnWidth / pin.aspectRatio + 60 // ~60pt for caption + creator row
            if leftHeight <= rightHeight {
                left.append(pin); leftHeight += h + 8
            } else {
                right.append(pin); rightHeight += h + 8
            }
        }
        return (left, right)
    }
}
```

### Floating Search Bar

```swift
struct FloatingSearchBar: View {
    @Binding var query: String
    @State private var isVisible = true

    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "magnifyingglass")
                .font(.system(size: 20, weight: .semibold))
                .foregroundStyle(Color.pinterestTextPrimaryLight)
            TextField("Search for ideas", text: $query)
                .font(.system(size: 16, weight: .regular))
                .foregroundStyle(Color.pinterestTextPrimaryLight)
        }
        .padding(.horizontal, 18)
        .frame(height: 48)
        .background(
            Capsule().fill(Color.white)
                .shadow(color: .black.opacity(0.08), radius: 8, y: 2)
        )
        .padding(.horizontal, 16)
    }
}
```

### Pin Detail Hero

```swift
struct PinDetailHero: View {
    let imageURL: URL
    let aspectRatio: CGFloat
    let title: String
    let description: String
    let creatorName: String
    let creatorAvatarURL: URL

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                ZStack(alignment: .topTrailing) {
                    AsyncImage(url: imageURL) { img in
                        img.resizable().scaledToFill()
                    } placeholder: { Color.pinterestSurface1Light }
                        .aspectRatio(aspectRatio, contentMode: .fit)
                        .clipShape(RoundedRectangle(cornerRadius: 16))

                    PinterestSaveButton(isSaved: .constant(false))
                        .padding(12)
                }
                .padding(.horizontal, 16)

                HStack(spacing: 12) {
                    AsyncImage(url: creatorAvatarURL) { img in
                        img.resizable().scaledToFill()
                    } placeholder: { Color.pinterestSurface1Light }
                        .frame(width: 44, height: 44)
                        .clipShape(Circle())

                    Text(creatorName)
                        .font(.pinterestBoardName)
                        .foregroundStyle(Color.pinterestTextPrimaryLight)
                    Spacer()
                    PinterestPillButton(title: "Follow") {}
                }
                .padding(.horizontal, 16)

                Text(title)
                    .font(.pinterestPinDetailTitle)
                    .foregroundStyle(Color.pinterestTextPrimaryLight)
                    .padding(.horizontal, 16)

                Text(description)
                    .font(.pinterestBody)
                    .foregroundStyle(Color.pinterestTextPrimaryLight)
                    .lineSpacing(3)
                    .padding(.horizontal, 16)
            }
        }
    }
}
```

## 4. Tab Bar

Pinterest uses an icon-only tab bar with a **distinctive red center Create button**. A custom overlay is cleaner than wrestling `TabView`.

```swift
struct PinterestTabBar: View {
    @Binding var selected: Tab

    enum Tab { case home, search, create, notifications, profile }

    var body: some View {
        HStack(spacing: 0) {
            tabButton(.home, icon: "house", filledIcon: "house.fill")
            tabButton(.search, icon: "magnifyingglass", filledIcon: "magnifyingglass")
            createButton
            tabButton(.notifications, icon: "bell", filledIcon: "bell.fill")
            tabButton(.profile, icon: "person", filledIcon: "person.fill")
        }
        .padding(.horizontal, 8)
        .frame(height: 56)
        .background(
            Color.pinterestCanvasLight
                .overlay(Rectangle().fill(Color.pinterestDividerLight).frame(height: 1), alignment: .top)
        )
    }

    private func tabButton(_ tab: Tab, icon: String, filledIcon: String) -> some View {
        Button {
            selected = tab
            UIImpactFeedbackGenerator(style: .light).impactOccurred()
        } label: {
            Image(systemName: selected == tab ? filledIcon : icon)
                .font(.system(size: 26, weight: .regular))
                .foregroundStyle(selected == tab ? Color.pinterestTextPrimaryLight : Color.pinterestTextSecondaryLight)
                .frame(maxWidth: .infinity, maxHeight: .infinity)
        }
    }

    private var createButton: some View {
        Button {
            selected = .create
            UIImpactFeedbackGenerator(style: .medium).impactOccurred()
        } label: {
            Image(systemName: "plus")
                .font(.system(size: 22, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 48, height: 48)
                .background(RoundedRectangle(cornerRadius: 14).fill(Color.pinterestRed))
        }
        .frame(maxWidth: .infinity)
    }
}
```

## 5. Pull-to-Refresh with Red "P" Spinner

```swift
struct PinterestRefreshSpinner: View {
    @State private var rotation: Double = 0

    var body: some View {
        Image("PinterestLogoP") // your asset — the red 'P' logomark
            .resizable()
            .scaledToFit()
            .frame(width: 28, height: 28)
            .rotationEffect(.degrees(rotation))
            .onAppear {
                withAnimation(.linear(duration: 1).repeatForever(autoreverses: false)) {
                    rotation = 360
                }
            }
    }
}
```

## 6. Motion

```swift
// Save button morph — described in PinterestSaveButton above. Key: 0.25s spring damping 0.8 + .sensoryFeedback(.success)

// Shared-element pin tap → detail
// Use matchedGeometryEffect on the pin image between the grid tile and the detail hero
struct SharedPinImage: View {
    let imageURL: URL
    let namespace: Namespace.ID
    let id: UUID
    var body: some View {
        AsyncImage(url: imageURL) { img in img.resizable().scaledToFill() } placeholder: { Color.gray }
            .clipShape(RoundedRectangle(cornerRadius: 16))
            .matchedGeometryEffect(id: id, in: namespace)
    }
}

// Fade-up entrance on newly loaded pins
// Apply on each PinTile via .transition(.move(edge: .bottom).combined(with: .opacity))
// With insertion: .spring(response: 0.4, dampingFraction: 0.85)

// Idea Pin advance
// Use a TabView(selection:) with PageTabViewStyle and custom onTap logic for tap-left/right
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search | `magnifyingglass` | 18–20pt |
| Home (tab) | `house` / `house.fill` | 26pt |
| Notifications | `bell` / `bell.fill` | 26pt |
| Profile | `person` / `person.fill` | 26pt |
| Create (center) | `plus` | 22pt |
| Heart (react) | `heart` / `heart.fill` | 24pt |
| Comment | `bubble.right` / `bubble.right.fill` | 24pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 20pt |
| Back | `chevron.left` | 24pt |
| Shop | `tag.fill` | 16pt |
| Idea Pin | `sparkles` | 16pt |
| Save confirm | `checkmark` | 16pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; on iOS 16 use `UIImpactFeedbackGenerator` and `UINotificationFeedbackGenerator`)
- Dynamic Type: enable on pin detail title, description, and body copy; keep masonry-grid pin captions at a fixed size so the height calculation stays valid
- VoiceOver: group pin tile contents so the user hears "Save {title} by {creator}" as one tap target, with the Save button as a secondary action
- Color contrast: `#767676` on `#FFFFFF` meets WCAG AA for 14pt+; dark-mode `#AAAAAA` on `#121212` also passes. Validate at 12pt metadata sizes.
- Image placeholders: render a colored rectangle matching the average pin aspect so the grid doesn't shift as images load
- Masonry critical path: you MUST know the image aspect ratio before rendering. If you have to measure, use a PreferenceKey with `GeometryReader` and cache the measurement per pin id
