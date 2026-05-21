# Postman (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Postman's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default)
    static let pmCanvas    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let pmSurface1  = Color(red: 0.129, green: 0.129, blue: 0.129) // #212121
    static let pmSurface2  = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let pmSurface3  = Color(red: 0.200, green: 0.200, blue: 0.200) // #333333
    static let pmCodeSurface = Color(red: 0.086, green: 0.086, blue: 0.086) // #161616
    static let pmDivider   = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A
    static let pmBorder    = Color(red: 0.267, green: 0.267, blue: 0.267) // #444444

    // MARK: - Canvas & Surfaces (Light)
    static let pmCanvasLight  = Color.white                                  // #FFFFFF
    static let pmSurfaceLight = Color(red: 0.957, green: 0.961, blue: 0.961) // #F4F5F5
    static let pmCodeLight    = Color(red: 0.980, green: 0.980, blue: 0.980) // #FAFAFA
    static let pmDividerLight = Color(red: 0.882, green: 0.890, blue: 0.890) // #E1E3E3

    // MARK: - Text
    static let pmTextPrimary   = Color(red: 0.910, green: 0.910, blue: 0.910) // #E8E8E8
    static let pmTextSecondary = Color(red: 0.651, green: 0.651, blue: 0.651) // #A6A6A6
    static let pmTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E
    static let pmTextOnLight   = Color(red: 0.129, green: 0.129, blue: 0.129) // #212121

    // MARK: - Brand (the single accent — actions only)
    static let pmOrange        = Color(red: 1.000, green: 0.424, blue: 0.216) // #FF6C37
    static let pmOrangePressed = Color(red: 0.898, green: 0.353, blue: 0.169) // #E55A2B
    static let pmOrangeDim     = Color(red: 0.239, green: 0.141, blue: 0.090) // #3D2417

    // MARK: - HTTP Method Colors (dark — semantic core)
    static let pmGet    = Color(red: 0.420, green: 0.867, blue: 0.604) // #6BDD9A
    static let pmPost   = Color(red: 1.000, green: 0.894, blue: 0.478) // #FFE47A
    static let pmPut    = Color(red: 0.455, green: 0.682, blue: 0.965) // #74AEF6
    static let pmPatch  = Color(red: 0.753, green: 0.659, blue: 0.882) // #C0A8E1
    static let pmDelete = Color(red: 0.969, green: 0.565, blue: 0.565) // #F79090
    static let pmOptions = Color(red: 0.651, green: 0.651, blue: 0.651) // #A6A6A6

    // MARK: - JSON Syntax
    static let pmJsonKey   = Color(red: 0.455, green: 0.682, blue: 0.965) // #74AEF6
    static let pmJsonStr   = Color(red: 0.420, green: 0.867, blue: 0.604) // #6BDD9A
    static let pmJsonNum   = Color(red: 0.753, green: 0.659, blue: 0.882) // #C0A8E1
    static let pmJsonPunc  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E

    // MARK: - Semantic
    static let pmSuccess = pmGet
    static let pmError   = pmDelete
    static let pmWarning = pmPost
    static let pmInfo    = pmPut
}
```

## 2. Typography

Postman pairs **Inter** (UI chrome) with **JetBrains Mono** (all request/response data). Bundle both via `Info.plist` / `UIAppFonts`. The split is by content type, not user preference.

```swift
extension Font {
    // Sans — chrome
    static let pmDisplay    = Font.custom("Inter-ExtraBold", size: 32).weight(.heavy)
    static let pmTitle      = Font.custom("Inter-Bold",      size: 26).weight(.bold)
    static let pmSection    = Font.custom("Inter-Bold",      size: 22).weight(.bold)
    static let pmSubsection = Font.custom("Inter-SemiBold",  size: 18).weight(.semibold)
    static let pmBody       = Font.custom("Inter-Regular",   size: 16)
    static let pmLabel      = Font.custom("Inter-Medium",    size: 15).weight(.medium)
    static let pmMeta       = Font.custom("Inter-Regular",   size: 14)
    static let pmCaption    = Font.custom("Inter-Medium",    size: 12).weight(.medium)
    static let pmTab        = Font.custom("Inter-Medium",    size: 10).weight(.medium)
    static let pmButton     = Font.custom("Inter-Bold",      size: 14).weight(.bold)

    // Mono — URLs, headers, params, JSON, status, timing
    static let pmMethod     = Font.custom("JetBrainsMono-Bold",    size: 13).weight(.bold)
    static let pmMonoData   = Font.custom("JetBrainsMono-Regular", size: 13)
    static let pmMonoJson   = Font.custom("JetBrainsMono-Regular", size: 12)
    static let pmStatusCode = Font.custom("JetBrainsMono-Bold",    size: 12).weight(.bold)
}
```

> Fallback when fonts aren't bundled: `Font.system(size:, weight:)` for Inter, `design: .monospaced` for JetBrains Mono.

## 3. Signature Components

### Method-Colored URL / Request Bar

```swift
enum HTTPMethod: String, CaseIterable {
    case GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS
    var color: Color {
        switch self {
        case .GET: .pmGet; case .POST: .pmPost; case .PUT: .pmPut
        case .PATCH: .pmPatch; case .DELETE: .pmDelete
        case .HEAD, .OPTIONS: .pmOptions
        }
    }
}

struct RequestBar: View {
    @Binding var method: HTTPMethod
    @Binding var url: String
    let onSend: () -> Void
    let onPickMethod: () -> Void

    var body: some View {
        HStack(spacing: 0) {
            Button(action: onPickMethod) {
                Text(method.rawValue)
                    .font(.pmMethod).tracking(0.3)
                    .foregroundStyle(method.color)
                    .padding(.horizontal, 12).frame(maxHeight: .infinity)
            }
            .buttonStyle(.plain)
            .overlay(Rectangle().frame(width: 1).foregroundStyle(Color.pmBorder), alignment: .trailing)

            TextField("Enter request URL", text: $url)
                .font(.pmMonoData)
                .foregroundStyle(Color.pmTextPrimary)
                .textInputAutocapitalization(.never)
                .autocorrectionDisabled()
                .padding(.horizontal, 12)

            Button(action: onSend) {
                Text("Send").font(.pmButton).foregroundStyle(.white)
                    .padding(.horizontal, 16).frame(maxHeight: .infinity)
                    .background(Color.pmOrange)
            }
            .buttonStyle(.plain)
        }
        .frame(height: 44)
        .background(RoundedRectangle(cornerRadius: 10).fill(Color.pmSurface1))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.pmBorder, lineWidth: 1))
        .clipShape(RoundedRectangle(cornerRadius: 10))
    }
}
```

### HTTP Method Chip

```swift
struct MethodChip: View {
    let method: HTTPMethod
    var body: some View {
        Text(method.rawValue)
            .font(.pmMethod).tracking(0.3)
            .foregroundStyle(method.color)
            .padding(.vertical, 6).padding(.horizontal, 12)
            .background(RoundedRectangle(cornerRadius: 6).fill(method.color.opacity(0.12)))
    }
}
```

### Status Code Chip

```swift
struct StatusChip: View {
    let code: Int
    let label: String   // "OK", "Not Found"
    var cls: Color {
        switch code {
        case 200..<300: .pmSuccess; case 300..<400: .pmInfo
        case 400..<500: .pmWarning; default: .pmError
        }
    }
    var body: some View {
        Text("\(code) \(label)")
            .font(.pmStatusCode).tracking(0.2)
            .foregroundStyle(cls)
            .padding(.vertical, 5).padding(.horizontal, 10)
            .background(Capsule().fill(cls.opacity(0.08)))
            .overlay(Capsule().strokeBorder(cls.opacity(0.4), lineWidth: 1))
    }
}
```

### Key-Value Editor Row

```swift
struct KeyValueRow: View {
    @Binding var key: String
    @Binding var value: String
    @Binding var enabled: Bool

    var body: some View {
        HStack(spacing: 8) {
            Button { enabled.toggle() } label: {
                Image(systemName: enabled ? "checkmark.square.fill" : "square")
                    .foregroundStyle(enabled ? Color.pmOrange : Color.pmTextTertiary)
            }.buttonStyle(.plain)

            TextField("key", text: $key)
                .font(.pmMonoData).foregroundStyle(Color.pmJsonKey)
                .padding(9).frame(maxWidth: .infinity)
                .background(RoundedRectangle(cornerRadius: 6).fill(Color.pmSurface1))
                .overlay(RoundedRectangle(cornerRadius: 6).strokeBorder(Color.pmDivider, lineWidth: 1))
                .frame(maxWidth: 130)

            TextField("value", text: $value)
                .font(.pmMonoData).foregroundStyle(Color.pmTextPrimary)
                .padding(9).frame(maxWidth: .infinity)
                .background(RoundedRectangle(cornerRadius: 6).fill(Color.pmSurface1))
                .overlay(RoundedRectangle(cornerRadius: 6).strokeBorder(Color.pmDivider, lineWidth: 1))
        }
    }
}
```

### Response Viewer with Syntax-Highlighted JSON

```swift
struct ResponseViewer: View {
    let code: Int
    let label: String
    let durationMs: Int
    let sizeText: String
    let json: AttributedString   // pre-colored via the JSON tokenizer below

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 10) {
                StatusChip(code: code, label: label)
                Text("\(durationMs) ms").font(.pmMonoJson).foregroundStyle(Color.pmTextSecondary)
                Text(sizeText).font(.pmMonoJson).foregroundStyle(Color.pmTextSecondary)
                Spacer()
            }
            .padding(.horizontal, 18).padding(.vertical, 8)

            ScrollView {
                Text(json)
                    .font(.pmMonoJson)
                    .frame(maxWidth: .infinity, alignment: .leading)
                    .padding(12)
            }
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.pmCodeSurface)) // data sinks darker
            .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.pmDivider, lineWidth: 1))
            .padding(.horizontal, 14).padding(.bottom, 14)
        }
    }
}

// Minimal JSON colorizer → AttributedString
func colorizeJSON(_ raw: String) -> AttributedString {
    var out = AttributedString(raw)
    // Keys: "..." followed by ':'
    for m in raw.ranges(of: try! Regex("\"[^\"]+\"(?=\\s*:)")) {
        if let r = Range(m, in: out) { out[r].foregroundColor = .pmJsonKey }
    }
    // Strings (values)
    for m in raw.ranges(of: try! Regex("(?<=:\\s)\"[^\"]*\"")) {
        if let r = Range(m, in: out) { out[r].foregroundColor = .pmJsonStr }
    }
    // Numbers / booleans / null
    for m in raw.ranges(of: try! Regex("(?<=:\\s)(-?\\d+\\.?\\d*|true|false|null)")) {
        if let r = Range(m, in: out) { out[r].foregroundColor = .pmJsonNum }
    }
    return out
}
```

### Postman Roundel Logomark

```swift
struct PostmanRoundel: View {
    var size: CGFloat = 26
    var body: some View {
        ZStack {
            Circle().fill(Color.pmOrange)
            // simplified "satellite" tick
            RoundedRectangle(cornerRadius: 1.5)
                .fill(.white)
                .frame(width: size * 0.5, height: size * 0.14)
                .rotationEffect(.degrees(-45))
        }
        .frame(width: size, height: size)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PostmanTabView: View {
    var body: some View {
        TabView {
            RequestView().tabItem      { Label("Request",      systemImage: "arrow.up.arrow.down") }
            CollectionsView().tabItem  { Label("Collections",  systemImage: "folder") }
            HistoryView().tabItem      { Label("History",      systemImage: "clock") }
            EnvironmentsView().tabItem { Label("Environments", systemImage: "list.bullet.indent") }
        }
        .tint(.pmOrange)  // active = Postman Orange (the one accent)
        .toolbarBackground(Color.pmCanvas, for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Send → spinner → response
@State private var sending = false
Button { sending = true; fire() } label: {
    if sending { ProgressView().tint(.white) } else { Text("Send").font(.pmButton) }
}
// On completion: slide the response up + pop the status chip
withAnimation(.easeOut(duration: 0.22)) { showResponse = true }
// status chip pop
.scaleEffect(chipIn ? 1.0 : 0.9).animation(.spring(response: 0.25, dampingFraction: 0.7), value: chipIn)

// Tab-strip underline slide
.matchedGeometryEffect(id: "tabUnderline", in: ns) // 200ms ease-out via withAnimation

// Method picker — bottom sheet + color cross-fade on the URL pill
.animation(.easeOut(duration: 0.18), value: method)

// JSON node collapse
.rotationEffect(.degrees(open ? 90 : 0)).animation(.easeOut(duration: 0.15), value: open)

// Haptics
UIImpactFeedbackGenerator(style: .light).impactOccurred()                       // Send tap
UINotificationFeedbackGenerator().notificationOccurred(code < 400 ? .success : .error) // response
UISelectionFeedbackGenerator().selectionChanged()                              // method / tab change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Request (tab) | `arrow.up.arrow.down` | 21pt |
| Collections (tab) | `folder` / `folder.fill` | 21pt |
| History (tab) | `clock` / `clock.fill` | 21pt |
| Environments (tab) | `list.bullet.indent` | 21pt |
| Send | `paperplane.fill` | 16pt |
| Save | `square.and.arrow.down` | 16pt |
| Add request | `plus` | 18pt |
| Method picker chevron | `chevron.down` | 12pt |
| JSON node | `chevron.right` (rotate) | 12pt |
| Copy | `doc.on.doc` | 14pt |
| Enable row | `checkmark.square.fill` / `square` | 16pt |
| Delete row | `trash` | 16pt |
| Search | `magnifyingglass` | 16pt |
| Environment | `globe` | 14pt |
| Test pass | `checkmark.circle.fill` | 14pt |
| Test fail | `xmark.circle.fill` | 14pt |
| Back | `chevron.left` | 17pt |
| More | `ellipsis` | 18pt |

## 7. Dark Mode

```swift
struct PostmanTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.pmCanvas : Color.pmCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.pmTextPrimary : Color.pmTextOnLight)
    }
}
extension View { func postmanTheme() -> some View { modifier(PostmanTheme()) } }
```

Dark mode is a **neutral grey** (`#1A1A1A`), not pure black and not charcoal-blue. Postman Orange `#FF6C37` is identical in both modes. The HTTP-method hues are the softened dark-mode set (`#6BDD9A` etc.); in light mode they shift to the saturated variants (`#0CBB52` GET, `#1A73E8` PUT, `#7D4FC4` PATCH, `#E5484D` DELETE) but keep their hue identity. The response code block stays *darker* than the canvas in both modes (data sinks).

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`AttributedString` regex colorizing and `Regex` literals require iOS 16; on iOS 15 fall back to an `NSAttributedString` tokenizer)
- Bundle Inter + JetBrains Mono TTFs via `Info.plist` `UIAppFonts` — both are SIL OFL licensed for free distribution
- Dynamic Type: support on Display / Title / Section / Body / Label / Meta; keep method pills, status chips, tab labels, JSON body, and ≤12pt mono FIXED (column-alignment-sensitive in key-value grids and JSON)
- VoiceOver: announce a request row as "{method} request, {name}"; the method color is decorative — fold the method word into the label so color-blind users get the same info; announce a response as "Status {code} {label}, {duration} milliseconds, {size}"
- Color is not the only signal: method is always shown as text (`GET`/`POST`), status as the numeric code — never hue alone
- Contrast: `#E8E8E8` on `#1A1A1A` ≈ 13:1 (AAA). Method hues on their 12% chip fills are validated for the chip text; `#FFE47A` POST on dark needs the chip fill (not the bare canvas) to pass — always render the method as a chip, not loose text on canvas
- Reduce Motion: disable the response slide-up and status-chip pop (cross-fade instead); keep the underline color change without the slide
- Dark mode: neutral grey is the brand. Do not substitute pure black or a blue-tinted charcoal; the response block must remain darker than the canvas in both modes (the depth inversion is a Postman tell)
