# Flickr (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Flickr's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, a justified-mosaic layout, the photo detail page, the EXIF table, and the favorite-star animation.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default for gallery viewing)
    static let flickrCanvas    = Color(red: 0.047, green: 0.051, blue: 0.055) // #0C0D0E
    static let flickrSurface1  = Color(red: 0.102, green: 0.106, blue: 0.114) // #1A1B1D
    static let flickrSurface2  = Color(red: 0.137, green: 0.141, blue: 0.153) // #232427
    static let flickrDivider   = Color(red: 0.165, green: 0.173, blue: 0.188) // #2A2C30

    // MARK: - Canvas & Surfaces (Light)
    static let flickrCanvasLight   = Color.white                                  // #FFFFFF
    static let flickrSurfaceLight  = Color(red: 0.961, green: 0.965, blue: 0.969) // #F5F6F7
    static let flickrSurfacePressed = Color(red: 0.918, green: 0.925, blue: 0.937) // #EAECEF
    static let flickrDividerLight  = Color(red: 0.886, green: 0.894, blue: 0.910) // #E2E4E8

    // MARK: - Text
    static let flickrTextPrimary    = Color.white                                  // #FFFFFF
    static let flickrTextSecondary  = Color(red: 0.690, green: 0.702, blue: 0.722) // #B0B3B8
    static let flickrTextTertiary   = Color(red: 0.431, green: 0.443, blue: 0.463) // #6E7176
    static let flickrTextPrimaryLt  = Color(red: 0.110, green: 0.122, blue: 0.137) // #1C1F23
    static let flickrTextSecondaryLt = Color(red: 0.420, green: 0.439, blue: 0.467) // #6B7077

    // MARK: - Brand (twin dots)
    static let flickrPink         = Color(red: 1.000, green: 0.000, blue: 0.518) // #FF0084
    static let flickrPinkPressed  = Color(red: 0.839, green: 0.000, blue: 0.439) // #D60070
    static let flickrBlue         = Color(red: 0.000, green: 0.388, blue: 0.863) // #0063DC
    static let flickrBluePressed  = Color(red: 0.000, green: 0.322, blue: 0.722) // #0052B8

    // MARK: - Accent & Semantic
    static let flickrProGold      = Color(red: 1.000, green: 0.698, blue: 0.000) // #FFB200
    static let flickrSuccess      = Color(red: 0.000, green: 0.780, blue: 0.506) // #00C781
    static let flickrError        = Color(red: 1.000, green: 0.231, blue: 0.361) // #FF3B5C
}
```

## 2. Typography

Bundle **Proza Libre** (titles/headers) and **Inter** (all UI) plus **IBM Plex Mono** (EXIF values) via `Info.plist` / `UIAppFonts`. All three are SIL OFL.

```swift
extension Font {
    // Editorial serif — titles, headers, photo titles
    static let flickrScreenTitle  = Font.custom("ProzaLibre-Bold",     size: 32) // -0.4 tracking
    static let flickrSectionHead  = Font.custom("ProzaLibre-Bold",     size: 26)
    static let flickrNavTitle     = Font.custom("ProzaLibre-SemiBold", size: 22)
    static let flickrPhotoTitle   = Font.custom("ProzaLibre-SemiBold", size: 18)

    // UI sans — everything functional
    static let flickrBody      = Font.custom("Inter-Regular",  size: 16)
    static let flickrCardTitle = Font.custom("Inter-SemiBold", size: 15)
    static let flickrButton    = Font.custom("Inter-Bold",     size: 15)
    static let flickrMeta      = Font.custom("Inter-Regular",  size: 13)
    static let flickrFaveCount = Font.custom("Inter-SemiBold", size: 11)
    static let flickrExifKey   = Font.custom("Inter-Regular",  size: 12)
    static let flickrTagChip   = Font.custom("Inter-SemiBold", size: 12)
    static let flickrTab       = Font.custom("Inter-Medium",   size: 10)
    static let flickrCaption   = Font.custom("Inter-Regular",  size: 12)

    // Monospace — EXIF readouts
    static let flickrExifValue = Font.custom("IBMPlexMono-SemiBold", size: 11)
}
```

## 3. Signature Components

### Justified Photo Mosaic

The heritage layout — a row-packing algorithm that preserves native aspect ratios while every row fills the width at a target height.

```swift
struct Photo: Identifiable {
    let id = UUID()
    let aspect: CGFloat   // width / height
    let gradient: [Color] // stand-in for the image
    let faves: Int?
}

struct JustifiedMosaic: View {
    let photos: [Photo]
    let targetRowHeight: CGFloat = 115
    let gutter: CGFloat = 3

    private func packRows(width: CGFloat) -> [[Photo]] {
        var rows: [[Photo]] = []
        var current: [Photo] = []
        var aspectSum: CGFloat = 0
        for p in photos {
            current.append(p)
            aspectSum += p.aspect
            let rowHeight = (width - gutter * CGFloat(current.count - 1)) / aspectSum
            if rowHeight <= targetRowHeight {
                rows.append(current); current = []; aspectSum = 0
            }
        }
        if !current.isEmpty { rows.append(current) }
        return rows
    }

    var body: some View {
        GeometryReader { geo in
            ScrollView {
                let rows = packRows(width: geo.size.width)
                VStack(spacing: gutter) {
                    ForEach(Array(rows.enumerated()), id: \.offset) { _, row in
                        let aspectSum = row.reduce(0) { $0 + $1.aspect }
                        let h = (geo.size.width - gutter * CGFloat(row.count - 1)) / aspectSum
                        HStack(spacing: gutter) {
                            ForEach(row) { p in
                                MosaicTile(photo: p)
                                    .frame(width: h * p.aspect, height: h)
                            }
                        }
                    }
                }
            }
            .background(Color.flickrCanvas)
        }
    }
}

struct MosaicTile: View {
    let photo: Photo
    var body: some View {
        ZStack(alignment: .bottomLeading) {
            LinearGradient(colors: photo.gradient, startPoint: .topLeading, endPoint: .bottomTrailing)
            if let faves = photo.faves {
                LinearGradient(colors: [.clear, .black.opacity(0.45)],
                               startPoint: .center, endPoint: .bottom)
                HStack(spacing: 4) {
                    Image(systemName: "star.fill").font(.system(size: 12)).foregroundStyle(.white)
                    Text("\(faves)").font(.flickrFaveCount).foregroundStyle(.white)
                }
                .shadow(color: .black.opacity(0.7), radius: 3, y: 1)
                .padding(8)
            }
        }
        .clipped() // no corner radius — full-bleed
    }
}
```

### Photo Detail Page

```swift
struct PhotoDetailView: View {
    let photo: Photo
    @State private var isFaved = false

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 0) {
                LinearGradient(colors: photo.gradient, startPoint: .top, endPoint: .bottom)
                    .aspectRatio(photo.aspect, contentMode: .fit)

                Text("Aurora over Vestrahorn")
                    .font(.flickrPhotoTitle)
                    .foregroundStyle(Color.flickrTextPrimary)
                    .padding(.horizontal, 16).padding(.top, 16)

                HStack(spacing: 8) {
                    Circle()
                        .fill(LinearGradient(colors: [.flickrPink, .flickrBlue], startPoint: .topLeading, endPoint: .bottomTrailing))
                        .frame(width: 30, height: 30)
                    VStack(alignment: .leading, spacing: 1) {
                        Text("Sofia Marent").font(.flickrMeta.weight(.semibold)).foregroundStyle(Color.flickrTextPrimary)
                        Text("312 faves · 2d ago").font(.flickrCaption).foregroundStyle(Color.flickrTextSecondary)
                    }
                    Spacer()
                    FollowPill()
                }
                .padding(.horizontal, 16).padding(.top, 12)

                PhotoActionBar(isFaved: $isFaved)
                    .padding(.horizontal, 16).padding(.vertical, 12)
                Divider().overlay(Color.flickrDivider)

                Text("Shot on a 30-second exposure just after the storm cleared the ridge.")
                    .font(.flickrBody)
                    .foregroundStyle(Color.flickrTextPrimary)
                    .padding(16)

                ExifTable()
            }
        }
        .background(Color.flickrCanvas)
    }
}

struct FollowPill: View {
    @State private var following = false
    var body: some View {
        Button { following.toggle() } label: {
            Text(following ? "Following" : "Follow")
                .font(.custom("Inter-Bold", size: 13))
                .foregroundStyle(following ? Color.flickrTextSecondary : .white)
                .padding(.vertical, 8).padding(.horizontal, 18)
                .background(
                    Capsule().fill(following ? Color.clear : Color.flickrPink)
                )
                .overlay(Capsule().strokeBorder(Color.flickrTextSecondary, lineWidth: following ? 1 : 0))
        }
    }
}
```

### Favorite Star (the like primitive)

```swift
struct FavoriteStar: View {
    @Binding var isFaved: Bool
    @State private var scale: CGFloat = 1.0

    var body: some View {
        Button {
            isFaved.toggle()
            if isFaved {
                withAnimation(.spring(response: 0.28, dampingFraction: 0.6)) { scale = 1.25 }
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.14) {
                    withAnimation(.spring(response: 0.28, dampingFraction: 0.6)) { scale = 1.0 }
                }
            }
        } label: {
            Image(systemName: isFaved ? "star.fill" : "star")
                .font(.system(size: 22))
                .foregroundStyle(isFaved ? Color.flickrPink : Color.flickrTextPrimary)
                .scaleEffect(scale)
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isFaved)
        .frame(width: 44, height: 44)
    }
}
```

### Photo Action Bar

```swift
struct PhotoActionBar: View {
    @Binding var isFaved: Bool
    var body: some View {
        HStack(spacing: 22) {
            actionItem(systemName: isFaved ? "star.fill" : "star", label: "Fave",
                       tint: isFaved ? .flickrPink : .flickrTextSecondary) { isFaved.toggle() }
            actionItem(systemName: "bubble.right", label: "Comment", tint: .flickrTextSecondary) {}
            actionItem(systemName: "square.and.arrow.up", label: "Share", tint: .flickrTextSecondary) {}
            actionItem(systemName: "rectangle.stack.badge.plus", label: "Add", tint: .flickrTextSecondary) {}
            Spacer()
        }
    }

    private func actionItem(systemName: String, label: String, tint: Color, action: @escaping () -> Void) -> some View {
        Button(action: action) {
            VStack(spacing: 6) {
                Image(systemName: systemName).font(.system(size: 18))
                Text(label).font(.flickrCaption)
            }
            .foregroundStyle(tint)
        }
    }
}
```

### EXIF / Camera Detail Table

```swift
struct ExifTable: View {
    struct Row: Identifiable { let id = UUID(); let key: String; let value: String; let tappable: Bool }
    let rows: [Row] = [
        .init(key: "Camera",       value: "Sony ILCE-7M4",  tappable: true),
        .init(key: "Lens",         value: "FE 35mm F1.4 GM", tappable: true),
        .init(key: "Exposure",     value: "1/250 sec",      tappable: false),
        .init(key: "Aperture",     value: "f/2.8",          tappable: false),
        .init(key: "Focal length", value: "35 mm",          tappable: false),
        .init(key: "ISO",          value: "100",            tappable: false),
        .init(key: "Date taken",   value: "Jan 14, 2026",   tappable: false),
    ]

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text("CAMERA DETAILS")
                .font(.custom("Inter-Bold", size: 10))
                .tracking(0.8)
                .foregroundStyle(Color.flickrTextTertiary)
                .padding(.bottom, 10).padding(.horizontal, 16).padding(.top, 16)

            ForEach(rows) { row in
                HStack {
                    Text(row.key).font(.flickrExifKey).foregroundStyle(Color.flickrTextSecondary)
                    Spacer()
                    Text(row.value).font(.flickrExifValue).foregroundStyle(Color.flickrTextPrimary)
                }
                .padding(.horizontal, 16).padding(.vertical, 7)
                Divider().overlay(Color.flickrDivider).padding(.leading, 16)
            }
        }
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.flickrSurface1))
        .padding(16)
    }
}
```

### Camera / Lens Chip Row

```swift
struct GearChipRow: View {
    let chips: [(String, Bool)] // (label, isMono)
    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 10) {
                ForEach(Array(chips.enumerated()), id: \.offset) { _, c in
                    Text(c.0)
                        .font(c.1 ? .flickrExifValue : .flickrTagChip)
                        .foregroundStyle(Color.flickrTextPrimary)
                        .padding(.vertical, 7).padding(.horizontal, 14)
                        .background(Capsule().fill(Color.flickrSurface2))
                        .overlay(Capsule().strokeBorder(Color.flickrDivider, lineWidth: 1))
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct FlickrTabView: View {
    var body: some View {
        TabView {
            PhotostreamView().tabItem { Label("Feed", systemImage: "house") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            CameraView().tabItem { Label("", systemImage: "camera") }    // center, emphasized
            NotifyView().tabItem { Label("Notify", systemImage: "bell") }
            ProfileView().tabItem { Label("You", systemImage: "person.crop.circle") }
        }
        .tint(.flickrTextPrimary)
        .toolbarBackground(.ultraThinMaterial, for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
        // Camera tab glyph: render in .flickrPink via a custom UITabBarItem image (template off)
    }
}
```

## 5. Motion

```swift
// Fave star burst
withAnimation(.spring(response: 0.28, dampingFraction: 0.6)) { scale = 1.25 }
// ...then back to 1.0 after 0.14s

// Grid -> detail shared element (matched geometry)
@Namespace private var photoNS
// grid tile:  .matchedGeometryEffect(id: photo.id, in: photoNS)
// detail img: .matchedGeometryEffect(id: photo.id, in: photoNS)
// transition .animation(.easeOut(duration: 0.32))

// Lightbox open: zoom from grid position over 300ms ease-out; chrome -> rgba(0,0,0,0.55)
// Photo detail parallax: image .offset(y: scrollOffset * 0.5)

// New justified rows fade-in
.transition(.opacity).animation(.easeOut(duration: 0.20), value: rows.count)

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: isFaved)        // fave
.sensoryFeedback(.impact(weight: .medium), trigger: addedToAlbum)      // add to album
.sensoryFeedback(.selection, trigger: selectedTab)                     // tab change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Feed (tab) | `house` / `house.fill` | 23pt |
| Search (tab) | `magnifyingglass` | 23pt |
| Camera (tab, center) | `camera` | 27pt (tinted `#FF0084`) |
| Notify (tab) | `bell` / `bell.fill` | 23pt |
| You (tab) | `person.crop.circle` | 23pt |
| Favorite (inactive) | `star` | 22pt |
| Favorite (active) | `star.fill` | 22pt (`#FF0084`) |
| Comment | `bubble.right` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Add to album | `rectangle.stack.badge.plus` | 18pt |
| Back | `chevron.left` | 22pt |
| Overflow | `ellipsis` | 20pt |
| Pro badge | `star.circle.fill` | 12pt (`#FFB200`) |
| Download | `arrow.down.circle` | 18pt |
| Camera (nav) | `camera` | 21pt |

## 7. Dark Mode

```swift
struct FlickrTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.flickrCanvas : Color.flickrCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.flickrTextPrimary : Color.flickrTextPrimaryLt)
    }
}
extension View { func flickrTheme() -> some View { modifier(FlickrTheme()) } }
```

Flickr defaults to dark for gallery viewing. **Photos never dim** across themes — only chrome, EXIF tables, and dividers invert. Flickr Pink (`#FF0084`) and Flickr Blue (`#0063DC`) are identical in both modes. In light mode, canvas flips to `#FFFFFF`, text to `#1C1F23`, EXIF container to `#F5F6F7`.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `matchedGeometryEffect` shared transitions are smoothest on iOS 17; fall back to `UIImpactFeedbackGenerator` on 16)
- Bundle Proza Libre, Inter, IBM Plex Mono TTFs via `Info.plist` — all SIL OFL, free to ship
- Dynamic Type: scale photo titles, body, comments, metadata; keep EXIF values, fave-count overlays, and tab labels at FIXED sizes (layout-sensitive in the justified grid and on photo scrims)
- VoiceOver: label grid tiles "Photo by {author}, {n} favorites"; the fave button as "Favorite, {n} faves, button" with selected trait when faved; EXIF rows as "{key}: {value}"; Camera/Lens rows expose a custom action "See more shot with this {camera/lens}"
- Color contrast: `#FFFFFF` on `#0C0D0E` is maximal; `#B0B3B8` secondary on canvas passes AA; the fave-count overlay always sits on the `rgba(0,0,0,0.45)` scrim for legibility over any photo
- Reduce Motion: disable the fave star spring/particle burst (use a simple crossfade fill), disable the grid->detail zoom (cross-dissolve instead); keep selection states
- Reduce Transparency: replace the `.ultraThinMaterial` tab/nav with solid `#0C0D0E` (dark) / `#FFFFFF` (light)
- The five-point star is the favorite primitive — do not substitute a heart even when mirroring iOS conventions
