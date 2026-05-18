# Vercel (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vercel's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default)
    static let vcCanvas      = Color(red: 0.000, green: 0.000, blue: 0.000) // #000000 TRUE black
    static let vcSurface1    = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let vcSurface2    = Color(red: 0.067, green: 0.067, blue: 0.067) // #111111
    static let vcSurface3    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let vcDivider     = Color(red: 0.180, green: 0.180, blue: 0.180) // #2E2E2E
    static let vcBorder      = Color(red: 0.200, green: 0.200, blue: 0.200) // #333333

    // MARK: - Canvas & Surfaces (Light)
    static let vcCanvasLight   = Color.white                                  // #FFFFFF
    static let vcSurfaceLight  = Color(red: 0.980, green: 0.980, blue: 0.980) // #FAFAFA
    static let vcSurfaceLight2 = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let vcDividerLight  = Color(red: 0.918, green: 0.918, blue: 0.918) // #EAEAEA

    // MARK: - Text
    static let vcTextPrimary    = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED
    static let vcTextSecondary  = Color(red: 0.631, green: 0.631, blue: 0.631) // #A1A1A1
    static let vcTextTertiary   = Color(red: 0.439, green: 0.439, blue: 0.439) // #707070
    static let vcTextOnLight    = Color.black                                  // #000000

    // MARK: - Interactive
    static let vcWhiteFill   = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED primary fill
    static let vcWhitePress  = Color(red: 0.812, green: 0.812, blue: 0.812) // #CFCFCF
    static let vcBlue        = Color(red: 0.000, green: 0.439, blue: 0.953) // #0070F3
    static let vcBlueHover   = Color(red: 0.196, green: 0.569, blue: 1.000) // #3291FF

    // MARK: - Status (deployment state — the systemic accent)
    static let vcReady    = Color(red: 0.047, green: 0.808, blue: 0.420) // #0CCE6B
    static let vcBuilding = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
    static let vcError    = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
    static let vcReadyDim    = Color(red: 0.059, green: 0.239, blue: 0.180) // #0F3D2E
    static let vcErrorDim    = Color(red: 0.227, green: 0.078, blue: 0.086) // #3A1416

    // MARK: - Highlight (decorative only)
    static let vcHlPurple = Color(red: 0.475, green: 0.157, blue: 0.792) // #7928CA
    static let vcHlCyan   = Color(red: 0.314, green: 0.890, blue: 0.761) // #50E3C2
    static let vcHlPink   = Color(red: 1.000, green: 0.000, blue: 0.502) // #FF0080
}
```

## 2. Typography

Vercel uses **Geist Sans** (UI) and **Geist Mono** (machine data). Bundle both via `Info.plist` / `UIAppFonts`. The split is by content type, not user preference.

```swift
extension Font {
    // Sans — human prose & UI
    static let vcDisplay    = Font.custom("Geist-ExtraBold", size: 32).weight(.heavy)
    static let vcTitle      = Font.custom("Geist-Bold",      size: 26).weight(.bold)
    static let vcSection    = Font.custom("Geist-Bold",      size: 22).weight(.bold)
    static let vcSubsection = Font.custom("Geist-SemiBold",  size: 18).weight(.semibold)
    static let vcBody       = Font.custom("Geist-Regular",   size: 16)
    static let vcLabel      = Font.custom("Geist-Medium",    size: 15).weight(.medium)
    static let vcBodyStrong = Font.custom("Geist-SemiBold",  size: 14).weight(.semibold)
    static let vcMeta       = Font.custom("Geist-Regular",   size: 14)
    static let vcCaption    = Font.custom("Geist-Medium",    size: 12).weight(.medium)
    static let vcTab        = Font.custom("Geist-Medium",    size: 10).weight(.medium)
    static let vcButton     = Font.custom("Geist-SemiBold",  size: 14).weight(.semibold)

    // Mono — URLs, branches, commits, env vars, logs
    static let vcMonoURL    = Font.custom("GeistMono-Regular", size: 13)
    static let vcMonoLog    = Font.custom("GeistMono-Regular", size: 12)
    static let vcMonoTag    = Font.custom("GeistMono-Medium",  size: 10).weight(.medium)
    static let vcMonoBadge  = Font.custom("GeistMono-Medium",  size: 12).weight(.medium)
}
```

> Fallback when Geist isn't bundled: `Font.system(size:, weight:, design: .default)` for Sans, `design: .monospaced` for Mono.

## 3. Signature Components

### Deployment Card (the spine of the app)

```swift
enum DeployState { case ready, building, error
    var color: Color {
        switch self { case .ready: .vcReady; case .building: .vcBuilding; case .error: .vcError }
    }
    var label: String {
        switch self { case .ready: "Ready"; case .building: "Building"; case .error: "Error" }
    }
}

struct DeploymentCard: View {
    let state: DeployState
    let env: String          // "Production" / "Preview"
    let url: String
    let branch: String
    let relativeTime: String

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack(spacing: 8) {
                Circle()
                    .fill(state.color)
                    .frame(width: 9, height: 9)
                    .shadow(color: state.color.opacity(0.15), radius: 0, x: 0, y: 0)
                    .overlay(Circle().stroke(state.color.opacity(0.15), lineWidth: 3))
                Text(state.label)
                    .font(.vcBodyStrong)
                    .foregroundStyle(state == .ready ? Color.vcTextPrimary : state.color)
                Spacer()
                EnvironmentTag(env)
            }
            Text(url)
                .font(.vcMonoURL)
                .foregroundStyle(Color.vcTextPrimary)
                .lineLimit(1).truncationMode(.middle)
            HStack(spacing: 6) {
                Image(systemName: "arrow.triangle.branch")
                    .font(.system(size: 11))
                    .foregroundStyle(Color.vcTextTertiary)
                Text(branch)
                    .font(.vcMonoURL)
                    .foregroundStyle(Color.vcTextSecondary)
                Spacer()
                Text(relativeTime)
                    .font(.vcMeta)
                    .foregroundStyle(Color.vcTextTertiary)
            }
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.vcSurface1))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.vcDivider, lineWidth: 1))
    }
}
```

### Environment Tag

```swift
struct EnvironmentTag: View {
    let text: String
    init(_ text: String) { self.text = text }

    var body: some View {
        Text(text)
            .font(.vcMonoTag)
            .foregroundStyle(Color.vcTextSecondary)
            .padding(.vertical, 3).padding(.horizontal, 7)
            .background(RoundedRectangle(cornerRadius: 5).fill(Color.vcSurface3))
            .overlay(RoundedRectangle(cornerRadius: 5).strokeBorder(Color.vcBorder, lineWidth: 1))
    }
}
```

### Status Badge (pill)

```swift
struct StatusBadge: View {
    let state: DeployState
    var body: some View {
        HStack(spacing: 6) {
            Circle().fill(state.color).frame(width: 7, height: 7)
            Text(state.label).font(.vcMonoBadge).foregroundStyle(state.color)
        }
        .padding(.vertical, 5).padding(.horizontal, 10)
        .background(Capsule().fill(state.color.opacity(0.08)))
        .overlay(Capsule().strokeBorder(state.color.opacity(0.4), lineWidth: 1))
    }
}
```

### Primary / Secondary Buttons

```swift
struct VercelPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false
    var body: some View {
        Button(action: action) {
            Text(title).font(.vcButton).foregroundStyle(Color.black)
                .padding(.vertical, 11).padding(.horizontal, 20)
                .background(RoundedRectangle(cornerRadius: 8).fill(pressed ? Color.vcWhitePress : Color.vcWhiteFill))
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .pressEvents { pressed = true } onRelease: { pressed = false }
    }
}

struct VercelSecondaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.vcButton.weight(.medium)).foregroundStyle(Color.vcTextPrimary)
                .padding(.vertical, 10).padding(.horizontal, 18)
                .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.vcBorder, lineWidth: 1))
        }
        .buttonStyle(.plain)
    }
}
```

### Build-Log Viewer

```swift
struct BuildLogViewer: View {
    let lines: [(n: Int, text: String, kind: LogKind)]
    enum LogKind { case normal, error, success, dim }

    func color(_ k: LogKind) -> Color {
        switch k { case .normal: .vcTextPrimary; case .error: .vcError
                   case .success: .vcReady; case .dim: .vcTextTertiary }
    }

    var body: some View {
        ScrollViewReader { proxy in
            ScrollView {
                LazyVStack(alignment: .leading, spacing: 2) {
                    ForEach(lines, id: \.n) { l in
                        HStack(alignment: .top, spacing: 12) {
                            Text("\(l.n)")
                                .font(.vcMonoLog).foregroundStyle(Color.vcTextTertiary)
                                .frame(width: 32, alignment: .trailing)
                            Text(l.text).font(.vcMonoLog).foregroundStyle(color(l.kind))
                            Spacer(minLength: 0)
                        }.id(l.n)
                    }
                }
                .padding(.horizontal, 12).padding(.vertical, 10)
            }
            .background(Color.vcCanvas)
            .onChange(of: lines.count) { _, _ in
                if let last = lines.last { withAnimation { proxy.scrollTo(last.n, anchor: .bottom) } }
            }
        }
    }
}
```

### Analytics Bar Chart (no gridlines)

```swift
struct AnalyticsBars: View {
    let values: [CGFloat]   // 0...1
    var body: some View {
        HStack(alignment: .bottom, spacing: 8) {
            ForEach(values.indices, id: \.self) { i in
                LinearGradient(colors: [.vcBlue, .vcBlue.opacity(0.25)],
                               startPoint: .top, endPoint: .bottom)
                    .frame(maxWidth: .infinity)
                    .frame(height: max(4, values[i] * 110))
                    .clipShape(UnevenRoundedRectangle(topLeadingRadius: 4, topTrailingRadius: 4))
            }
        }
        .padding(12)
        .frame(height: 134)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.vcSurface2))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.vcDivider, lineWidth: 1))
        // intentionally NO gridlines / axis ticks — sparseness is the Vercel signature
    }
}
```

### Triangle Logomark

```swift
struct VercelTriangle: Shape {
    func path(in r: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: r.midX, y: r.minY))
        p.addLine(to: CGPoint(x: r.maxX, y: r.maxY))
        p.addLine(to: CGPoint(x: r.minX, y: r.maxY))
        p.closeSubpath()
        return p
    }
}
// VercelTriangle().fill(Color.vcTextPrimary).frame(width: 22, height: 19)
```

## 4. Bottom Tab Bar

```swift
struct VercelTabView: View {
    var body: some View {
        TabView {
            OverviewView().tabItem  { Label("Overview",    systemImage: "house") }
            DeploysView().tabItem   { Label("Deployments", systemImage: "list.bullet") }
            AnalyticsView().tabItem { Label("Analytics",   systemImage: "chart.line.uptrend.xyaxis") }
            SettingsView().tabItem  { Label("Settings",    systemImage: "gearshape") }
        }
        .tint(.white)  // active is pure white — NO tint pill, Vercel has no accent
        .toolbarBackground(Color.vcCanvas, for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Status transition: Building → Ready (dot color cross-fade)
withAnimation(.easeOut(duration: 0.24)) { state = .ready }
// + soft success haptic
UINotificationFeedbackGenerator().notificationOccurred(.success)

// Building pulse (status-dot halo loop)
.scaleEffect(pulse ? 1.15 : 1.0)
.onAppear { withAnimation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true)) { pulse = true } }

// Button press
.scaleEffect(pressed ? 0.98 : 1).animation(.easeOut(duration: 0.12), value: pressed)

// Sheet present — native .sheet / .presentationDetents
.presentationDetents([.medium, .large])
.presentationBackground(Color.vcSurface1)

// Tab switch — NO slide; instant content swap (Vercel favors immediacy)

// Pull-to-refresh — native .refreshable; a thin #0070F3 line draws while polling
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Overview (tab) | `house` / `house.fill` | 21pt |
| Deployments (tab) | `list.bullet` | 21pt |
| Analytics (tab) | `chart.line.uptrend.xyaxis` | 21pt |
| Settings (tab) | `gearshape` / `gearshape.fill` | 21pt |
| Git branch | `arrow.triangle.branch` | 11pt |
| Commit | `circle.dotted` | 12pt |
| Domain | `globe` | 14pt |
| Redeploy | `arrow.clockwise` | 14pt |
| Visit / open | `arrow.up.right` | 14pt |
| Logs | `terminal` | 16pt |
| Copy | `doc.on.doc` | 14pt |
| Search | `magnifyingglass` | 16pt |
| Reveal env value | `eye` / `eye.slash` | 14pt |
| Back | `chevron.left` | 17pt |
| More | `ellipsis` | 18pt |
| Success toast | `checkmark.circle.fill` | 18pt |
| Error | `exclamationmark.triangle.fill` | 16pt |

## 7. Dark Mode

```swift
struct VercelTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.vcCanvas : Color.vcCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.vcTextPrimary : Color.vcTextOnLight)
            .preferredColorScheme(.dark) // Vercel is dark-native; default to dark
    }
}
extension View { func vercelTheme() -> some View { modifier(VercelTheme()) } }
```

Dark is the default and the brand. The dark canvas is **true `#000000`** — never `#191919`. Light mode is a clean inversion: `#FFFFFF` canvas, `#000000` text, `#FAFAFA` cards, `#EAEAEA` borders. The triangle logomark only swaps fill; status colors are identical across modes.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`UnevenRoundedRectangle` requires iOS 16.4; fall back to a manual `Path` rounded-top rect on earlier)
- Bundle Geist Sans + Geist Mono TTFs via `Info.plist` `UIAppFonts` — both are SIL OFL licensed for free distribution
- Dynamic Type: support on Display / Title / Section / Body / Meta; keep tab labels, env tags, mono badges, and ≤12pt mono captions at FIXED sizes (they are layout-sensitive in dense rows)
- VoiceOver: announce a deployment as "Deployment: {state}, {env}, {branch}, {relativeTime}"; the status dot is decorative — fold its meaning into the card label, don't expose it separately
- Color is not the only signal: every status dot is paired with a text label ("Ready"/"Building"/"Error") so color-blind users are never reliant on hue alone
- Contrast: `#EDEDED` on `#000000` is ~18:1 (AAA). `#A1A1A1` on `#000000` passes AA for secondary text. Status colors on their dim backgrounds (`#0CCE6B` on `rgba(12,206,107,0.08)`) are validated for badge use
- Reduce Motion: disable the Building-pulse loop and the status cross-fade (snap the color instead); keep the success haptic
- Dark mode: the brand default. Do not auto-switch to a charcoal palette — Vercel's identity is true black; the only elevation cue is surface lightness + hairline border, never shadow
