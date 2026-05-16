# Trello (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Trello's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Action & Board
    static let trlAction        = Color(red: 0.047, green: 0.400, blue: 0.894) // #0C66E4
    static let trlActionPressed = Color(red: 0.000, green: 0.333, blue: 0.800) // #0055CC
    static let trlActionTint    = Color(red: 0.914, green: 0.949, blue: 1.000) // #E9F2FF
    static let trlBoardBlue     = Color(red: 0.000, green: 0.475, blue: 0.749) // #0079BF (default backdrop)

    // MARK: - Surfaces & Text
    static let trlCard      = Color.white                                  // #FFFFFF
    static let trlList      = Color(red: 0.945, green: 0.949, blue: 0.957) // #F1F2F4
    static let trlSunken    = Color(red: 0.922, green: 0.925, blue: 0.941) // #EBECF0
    static let trlDivider   = Color(red: 0.875, green: 0.882, blue: 0.902) // #DFE1E6
    static let trlBorder    = Color(red: 0.757, green: 0.780, blue: 0.816) // #C1C7D0
    static let trlTextPrimary   = Color(red: 0.090, green: 0.169, blue: 0.302) // #172B4D
    static let trlTextSecondary = Color(red: 0.369, green: 0.424, blue: 0.518) // #5E6C84
    static let trlTextTertiary  = Color(red: 0.537, green: 0.576, blue: 0.643) // #8993A4

    // MARK: - Card Label Palette (functional)
    static let trlLabelGreen  = Color(red: 0.294, green: 0.808, blue: 0.592) // #4BCE97
    static let trlLabelYellow = Color(red: 0.961, green: 0.804, blue: 0.278) // #F5CD47
    static let trlLabelOrange = Color(red: 0.996, green: 0.639, blue: 0.384) // #FEA362
    static let trlLabelRed    = Color(red: 0.973, green: 0.443, blue: 0.408) // #F87168
    static let trlLabelPurple = Color(red: 0.624, green: 0.561, blue: 0.937) // #9F8FEF
    static let trlLabelBlue   = Color(red: 0.341, green: 0.616, blue: 1.000) // #579DFF

    // MARK: - Semantic
    static let trlSuccess = Color(red: 0.122, green: 0.518, blue: 0.353) // #1F845A
    static let trlDueSoon = Color(red: 0.714, green: 0.361, blue: 0.008) // #B65C02
    static let trlOverdue = Color(red: 0.788, green: 0.216, blue: 0.173) // #C9372C
}

// Trello's signature navy-tinted shadow
extension Color {
    static func trlShadow(_ opacity: Double) -> Color {
        Color(red: 0.035, green: 0.118, blue: 0.259).opacity(opacity) // rgba(9,30,66,a)
    }
}
```

## 2. Typography

Trello uses the system font deliberately (legibility on any backdrop). Use SF Pro via `.system`; Inter is the closest web substitute.

```swift
extension Font {
    static let trlSheetTitle  = Font.system(size: 24, weight: .bold)
    static let trlBoardName   = Font.system(size: 20, weight: .bold)
    static let trlSection     = Font.system(size: 17, weight: .bold)
    static let trlListTitle   = Font.system(size: 15, weight: .semibold)
    static let trlCardTitle   = Font.system(size: 15, weight: .semibold)
    static let trlBody        = Font.system(size: 15, weight: .regular)
    static let trlButton      = Font.system(size: 16, weight: .semibold)
    static let trlBadge       = Font.system(size: 12, weight: .semibold)
    static let trlSubtitle    = Font.system(size: 13, weight: .regular)
    static let trlLabelUpper  = Font.system(size: 11, weight: .bold)
    static let trlComposer    = Font.system(size: 15, weight: .regular)
    static let trlCaption     = Font.system(size: 11, weight: .regular)
}
```

## 3. Signature Components

### Card (the core object, with drag lift)

```swift
struct TrelloCard: View {
    let title: String
    let labels: [Color]
    let due: String?
    let checklist: String? // e.g. "3/8"
    var isDragging: Bool = false

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            if !labels.isEmpty {
                HStack(spacing: 4) {
                    ForEach(labels.indices, id: \.self) { i in
                        RoundedRectangle(cornerRadius: 4)
                            .fill(labels[i])
                            .frame(width: 36, height: 8)
                    }
                }
            }
            Text(title)
                .font(.trlCardTitle)
                .foregroundStyle(.trlTextPrimary)
                .lineLimit(4)
            if due != nil || checklist != nil {
                HStack(spacing: 8) {
                    if let due {
                        HStack(spacing: 4) {
                            Image(systemName: "clock").font(.system(size: 11))
                            Text(due).font(.trlBadge)
                        }
                        .foregroundStyle(.trlDueSoon)
                    }
                    if let checklist {
                        HStack(spacing: 4) {
                            Image(systemName: "checklist").font(.system(size: 11))
                            Text(checklist).font(.trlBadge)
                        }
                        .foregroundStyle(.trlTextSecondary)
                    }
                }
            }
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 8)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.trlCard))
        .scaleEffect(isDragging ? 1.03 : 1)
        .shadow(
            color: .trlShadow(isDragging ? 0.25 : 0.13),
            radius: isDragging ? 24 : 1,
            y: isDragging ? 12 : 1
        )
        .animation(.spring(response: 0.22, dampingFraction: 0.8), value: isDragging)
    }
}
```

### List / Column

```swift
struct TrelloList: View {
    let title: String
    let count: Int
    let cards: [TrelloCardModel]   // your model
    @State private var composing = false
    @State private var draft = ""

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack {
                Text(title).font(.trlListTitle).foregroundStyle(.trlTextPrimary)
                Text("\(count)").font(.trlBadge).foregroundStyle(.trlTextSecondary)
                Spacer()
                Image(systemName: "ellipsis").font(.system(size: 16)).foregroundStyle(.trlTextSecondary)
            }
            .padding(.horizontal, 8)
            .padding(.top, 8)

            ScrollView(showsIndicators: false) {
                VStack(spacing: 8) {
                    ForEach(cards) { card in
                        TrelloCard(title: card.title, labels: card.labels, due: card.due, checklist: card.checklist)
                    }
                }
                .padding(.horizontal, 8)
            }

            if composing {
                VStack(spacing: 8) {
                    TextField("Enter a title for this card…", text: $draft, axis: .vertical)
                        .font(.trlComposer)
                        .padding(8)
                        .background(RoundedRectangle(cornerRadius: 8).fill(Color.trlCard)
                            .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.trlAction, lineWidth: 2)))
                    HStack {
                        Button("Add card") { /* add */ }
                            .font(.trlButton).foregroundStyle(.white)
                            .padding(.horizontal, 16).frame(height: 36)
                            .background(RoundedRectangle(cornerRadius: 8).fill(Color.trlAction))
                        Button { composing = false } label: { Image(systemName: "xmark").foregroundStyle(.trlTextSecondary) }
                    }
                }
                .padding(8)
            } else {
                Button { composing = true } label: {
                    HStack { Image(systemName: "plus"); Text("Add a card") }
                        .font(.trlBody).foregroundStyle(.trlTextSecondary)
                        .padding(8)
                }
            }
        }
        .frame(width: 272, alignment: .top)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.trlList))
        .shadow(color: .trlShadow(0.08), radius: 2, y: 1)
    }
}
```

### Board (horizontal scroll, backdrop)

```swift
struct TrelloBoard: View {
    let boardName: String
    let lists: [ListModel]
    var backdrop: Color = .trlBoardBlue   // or an Image-backed background

    var body: some View {
        ZStack(alignment: .top) {
            backdrop.ignoresSafeArea()
            // optional scrim for photo backdrops:
            // Color.black.opacity(0.16).ignoresSafeArea()

            VStack(spacing: 0) {
                // On-board header
                HStack(spacing: 12) {
                    Image(systemName: "chevron.left").foregroundStyle(.white)
                    Text(boardName).font(.trlBoardName).foregroundStyle(.white)
                    Spacer()
                    OnBoardChip(label: "Filter", systemImage: "line.3.horizontal.decrease")
                    Image(systemName: "ellipsis").foregroundStyle(.white)
                }
                .padding(.horizontal, 12)
                .frame(height: 44)

                ScrollView(.horizontal, showsIndicators: false) {
                    HStack(alignment: .top, spacing: 8) {
                        ForEach(lists) { list in
                            TrelloList(title: list.title, count: list.cards.count, cards: list.cards)
                        }
                        AddListChip()
                    }
                    .padding(8)
                }
            }
        }
    }
}

struct OnBoardChip: View {
    let label: String
    let systemImage: String
    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: systemImage)
            Text(label).font(.trlBadge)
        }
        .foregroundStyle(.white)
        .padding(.horizontal, 12)
        .frame(height: 32)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.white.opacity(0.16)))
    }
}
```

### Card Detail Sheet

```swift
struct CardDetailSheet: View {
    let title: String

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 20) {
                Text(title).font(.trlSheetTitle).foregroundStyle(.trlTextPrimary)

                Text("CHECKLIST").font(.trlLabelUpper).foregroundStyle(.trlTextSecondary)
                ProgressView(value: 0.375)
                    .tint(.trlSuccess)

                Text("ACTIVITY").font(.trlLabelUpper).foregroundStyle(.trlTextSecondary)
                // comment rows…
            }
            .padding(16)
        }
        .background(Color.trlCard)
        .presentationDetents([.large])
        .presentationCornerRadius(16)
    }
}
```

## 4. Drag-and-Drop Lift

```swift
// Use .onLongPressGesture to enter drag, drive isDragging on the card.
// SwiftUI's draggable/dropDestination (iOS 16+) or a manual DragGesture both work;
// the visual contract is: scale 1.03 + Level-3 navy shadow + a placeholder gap.

struct DropPlaceholder: View {
    var body: some View {
        RoundedRectangle(cornerRadius: 8)
            .fill(Color.trlShadow(0.14)) // rgba(9,30,66,0.14)-ish gap
            .frame(height: 48)
            .transition(.opacity.combined(with: .scale))
    }
}
```

## 5. Navigation (no bottom tab)

```swift
// Trello has NO bottom TabView. The board is the primary surface.
// App-level navigation is a top bar / drawer:

struct RootView: View {
    var body: some View {
        NavigationStack {
            BoardsListView()           // Boards / Home reached here
                .navigationTitle("Boards")
                .toolbar {
                    ToolbarItem(placement: .topBarTrailing) {
                        Button { /* search */ } label: { Image(systemName: "magnifyingglass") }
                    }
                }
        }
        // Pushing a board makes the board own the screen with horizontal scroll.
    }
}
```

## 6. Motion

```swift
// Card lift on drag — TrelloCard: scale 1.03 + shadow ramp, spring(response: 0.22)

// Card drop — settle spring ~0.22s, shadow returns to rest

// Card detail present — .presentationDetents + default sheet slide (~300ms)

// Composer expand
withAnimation(.easeInOut(duration: 0.18)) { composing = true }

// Checklist progress
ProgressView(value: progress).tint(.trlSuccess)
    .animation(.easeInOut(duration: 0.2), value: progress)
```

Haptics: `.sensoryFeedback(.impact(weight: .medium), trigger: isDragging)` when a card is picked up; `.selection` when it snaps into a new slot.

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Back | `chevron.left` | 18pt |
| Board overflow | `ellipsis` | 16–20pt |
| Filter (on board) | `line.3.horizontal.decrease` | 16pt |
| Search | `magnifyingglass` | 18pt |
| Add card / list | `plus` | 16pt |
| Close composer | `xmark` | 16pt |
| Due date badge | `clock` | 11pt |
| Checklist badge | `checklist` | 11pt |
| Comment badge | `text.bubble` | 11pt |
| Member | `person.crop.circle.fill` | 24pt |
| Attachment | `paperclip` | 16pt |
| Move card | `arrow.right.square` | 16pt |
| Checklist item | `checkmark.square` / `square` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.draggable`/`.dropDestination`, `.presentationDetents`, `.sensoryFeedback` is iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on card titles, body, and sheet content — cards grow taller to fit; keep badges and label chips fixed
- VoiceOver: a card announces "Card, <title>, 2 labels, due tomorrow, checklist 3 of 8"; expose drag as a custom action ("Move card") since drag-and-drop is hard for VoiceOver; the board name reads with `.accessibilityAddTraits(.isHeader)`
- Contrast: navy ink `#172B4D` on white cards passes WCAG AAA; on-board white text requires the scrim — never place white text on a light photo without `rgba(0,0,0,0.16+)` behind it. `#5E6C84` secondary on white passes AA at 12pt+ (validate badges)
- Color independence: label colors must not be the *only* signal — show label text in expanded mode for color-blind users
- Reduce Motion: when `accessibilityReduceMotion` is on, replace the drag scale with an opacity change and skip the placeholder scale transition
- Touch targets: whole card is the drag/tap target (≥44pt); on-board 32pt chips get a 44pt hit area; checklist boxes get 44pt
