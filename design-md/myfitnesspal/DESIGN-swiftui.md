# MyFitnessPal (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates MyFitnessPal's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the calorie ring, macro donut, diary row, FAB, and haptics.

Targets iOS 17+. Modern `.sensoryFeedback`, `Path`-based ring drawing, and `matchedGeometryEffect` on the FAB-to-sheet transition all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let mfpCanvas       = Color(red: 1.00, green: 1.00, blue: 1.00)   // #FFFFFF
    static let mfpSurfaceGray  = Color(red: 0.961, green: 0.969, blue: 0.980) // #F5F7FA
    static let mfpSurfaceGray2 = Color(red: 0.898, green: 0.914, blue: 0.941) // #E5E9F0
    static let mfpDivider      = Color(red: 0.898, green: 0.906, blue: 0.922) // #E5E7EB
    static let mfpHairline     = Color(red: 0.937, green: 0.945, blue: 0.961) // #EEF1F5

    // MARK: - Text
    static let mfpInk          = Color(red: 0.122, green: 0.161, blue: 0.216) // #1F2937 primary
    static let mfpSlate        = Color(red: 0.294, green: 0.333, blue: 0.388) // #4B5563 secondary
    static let mfpMute         = Color(red: 0.612, green: 0.639, blue: 0.686) // #9CA3AF tertiary
    static let mfpHeroNumber   = Color(red: 0.067, green: 0.094, blue: 0.153) // #111827 hero

    // MARK: - Brand
    static let mfpBlue         = Color(red: 0.00, green: 0.365, blue: 0.667)  // #005DAA heritage
    static let mfpLakeBlue     = Color(red: 0.00, green: 0.447, blue: 0.808)  // #0072CE action
    static let mfpLakePressed  = Color(red: 0.00, green: 0.373, blue: 0.659)  // #005FA8
    static let mfpSkyBlue      = Color(red: 0.906, green: 0.941, blue: 1.00)  // #E7F0FF tint

    // MARK: - Macro trio (do not reorder)
    static let mfpCarbs        = Color(red: 1.00, green: 0.624, blue: 0.110)  // #FF9F1C orange
    static let mfpFat          = Color(red: 0.643, green: 0.388, blue: 0.949) // #A463F2 purple
    static let mfpProtein      = Color(red: 0.098, green: 0.765, blue: 0.490) // #19C37D green

    // MARK: - Semantic
    static let mfpUnderGoal    = Color(red: 0.098, green: 0.765, blue: 0.490) // #19C37D
    static let mfpApproaching  = Color(red: 0.961, green: 0.620, blue: 0.043) // #F59E0B
    static let mfpOverLimit    = Color(red: 0.937, green: 0.267, blue: 0.267) // #EF4444
    static let mfpStreak       = Color(red: 0.984, green: 0.573, blue: 0.235) // #FB923C
    static let mfpPremiumGold  = Color(red: 0.831, green: 0.643, blue: 0.216) // #D4A437

    // MARK: - Dark mode
    static let mfpDarkCanvas   = Color(red: 0.059, green: 0.078, blue: 0.098) // #0F1419
    static let mfpDarkSurface1 = Color(red: 0.102, green: 0.122, blue: 0.149) // #1A1F26
    static let mfpDarkSurface2 = Color(red: 0.145, green: 0.169, blue: 0.200) // #252B33
    static let mfpDarkDivider  = Color(red: 0.165, green: 0.184, blue: 0.216) // #2A2F37
    static let mfpDarkText     = Color(red: 0.953, green: 0.957, blue: 0.965) // #F3F4F6
    static let mfpDarkTextSec  = Color(red: 0.612, green: 0.639, blue: 0.686) // #9CA3AF
    static let mfpLakeDark     = Color(red: 0.231, green: 0.561, blue: 0.875) // #3B8FDF
}
```

## 2. Typography

SF Pro is the system default on iOS. Tabular numerals must be enabled on every number that summarizes nutrition — calories, grams, percentages, weight, water ounces.

```swift
extension Font {
    // Calorie ring hero — the giant number inside the donut
    static let mfpCalorieHero  = Font.system(size: 56, weight: .bold,    design: .default)
                                     .monospacedDigit()
    static let mfpCalorieLabel = Font.system(size: 13, weight: .semibold, design: .default)

    // Large titles / sections
    static let mfpLargeNav     = Font.system(size: 28, weight: .bold,    design: .default)
    static let mfpSection      = Font.system(size: 22, weight: .bold,    design: .default)
    static let mfpMeal         = Font.system(size: 18, weight: .semibold,design: .default)

    // Diary rows
    static let mfpFoodName     = Font.system(size: 16, weight: .medium,  design: .default)
    static let mfpFoodSub      = Font.system(size: 13, weight: .regular, design: .default)
    static let mfpCalorie      = Font.system(size: 16, weight: .semibold,design: .default)
                                     .monospacedDigit()

    // Body
    static let mfpBody         = Font.system(size: 15, weight: .regular, design: .default)
    static let mfpBodySmall    = Font.system(size: 13, weight: .regular, design: .default)

    // Macros
    static let mfpMacroNumber  = Font.system(size: 22, weight: .bold,    design: .default)
                                     .monospacedDigit()
    static let mfpMacroLabel   = Font.system(size: 11, weight: .semibold,design: .default)

    // Buttons & misc
    static let mfpButton       = Font.system(size: 16, weight: .semibold,design: .default)
    static let mfpTab          = Font.system(size: 10, weight: .semibold,design: .default)
    static let mfpStreak       = Font.system(size: 12, weight: .bold,    design: .default)
                                     .monospacedDigit()
}
```

## 3. Signature Components

### Calorie Ring (the hero of Dashboard)

```swift
struct CalorieRing: View {
    let goal: Int
    let consumed: Int
    let exercise: Int
    let diameter: CGFloat = 220
    let stroke: CGFloat = 18

    var remaining: Int { goal - consumed + exercise }
    var progress: Double { min(Double(consumed) / Double(max(goal, 1)), 1.0) }

    var ringColor: Color {
        let used = Double(consumed) / Double(max(goal, 1))
        switch used {
        case ..<0.6:   return .mfpUnderGoal
        case ..<0.95:  return .mfpLakeBlue
        case ..<1.0:   return .mfpApproaching
        default:       return .mfpOverLimit
        }
    }

    var body: some View {
        ZStack {
            // Background ring
            Circle()
                .stroke(Color.mfpSurfaceGray2, lineWidth: stroke)

            // Progress ring
            Circle()
                .trim(from: 0, to: progress)
                .stroke(ringColor, style: StrokeStyle(lineWidth: stroke, lineCap: .round))
                .rotationEffect(.degrees(-90))
                .animation(.easeOut(duration: 0.6), value: progress)

            // Center stack
            VStack(spacing: 4) {
                Text("REMAINING")
                    .font(.mfpCalorieLabel)
                    .tracking(0.6)
                    .foregroundStyle(Color.mfpSlate)
                Text(remaining.formatted())
                    .font(.mfpCalorieHero)
                    .tracking(-1.0)
                    .foregroundStyle(Color.mfpHeroNumber)
                    .contentTransition(.numericText(value: Double(remaining)))
                Text("calories")
                    .font(.mfpBodySmall)
                    .foregroundStyle(Color.mfpSlate)
            }
        }
        .frame(width: diameter, height: diameter)
    }
}
```

### Macro Donut (Carbs / Fat / Protein)

```swift
struct MacroDonut: View {
    let carbs: Int        // grams
    let fat: Int          // grams
    let protein: Int      // grams
    let carbsGoal: Int
    let fatGoal: Int
    let proteinGoal: Int
    let diameter: CGFloat = 96
    let stroke: CGFloat = 10

    var total: Int { carbs + fat + protein }
    var carbsFrac:   Double { total == 0 ? 0 : Double(carbs)   / Double(total) }
    var fatFrac:     Double { total == 0 ? 0 : Double(fat)     / Double(total) }
    var proteinFrac: Double { total == 0 ? 0 : Double(protein) / Double(total) }

    var body: some View {
        VStack(spacing: 16) {
            ZStack {
                // Carbs slice
                Circle()
                    .trim(from: 0, to: carbsFrac)
                    .stroke(Color.mfpCarbs, style: StrokeStyle(lineWidth: stroke, lineCap: .butt))
                    .rotationEffect(.degrees(-90))

                // Fat slice
                Circle()
                    .trim(from: carbsFrac, to: carbsFrac + fatFrac)
                    .stroke(Color.mfpFat, style: StrokeStyle(lineWidth: stroke, lineCap: .butt))
                    .rotationEffect(.degrees(-90))

                // Protein slice
                Circle()
                    .trim(from: carbsFrac + fatFrac, to: carbsFrac + fatFrac + proteinFrac)
                    .stroke(Color.mfpProtein, style: StrokeStyle(lineWidth: stroke, lineCap: .butt))
                    .rotationEffect(.degrees(-90))

                Text("\(Int(Double(total) / Double(max(carbsGoal + fatGoal + proteinGoal, 1)) * 100))%")
                    .font(.mfpMacroNumber)
                    .foregroundStyle(Color.mfpInk)
            }
            .frame(width: diameter, height: diameter)

            HStack(spacing: 24) {
                MacroLegend(color: .mfpCarbs,   label: "CARBS",   grams: carbs,   goal: carbsGoal)
                MacroLegend(color: .mfpFat,     label: "FAT",     grams: fat,     goal: fatGoal)
                MacroLegend(color: .mfpProtein, label: "PROTEIN", grams: protein, goal: proteinGoal)
            }
        }
    }
}

struct MacroLegend: View {
    let color: Color
    let label: String
    let grams: Int
    let goal: Int
    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            HStack(spacing: 6) {
                RoundedRectangle(cornerRadius: 2).fill(color).frame(width: 8, height: 8)
                Text(label).font(.mfpMacroLabel).tracking(0.8).foregroundStyle(Color.mfpSlate)
            }
            Text("\(grams)g · \(Int(Double(grams) / Double(max(goal,1)) * 100))%")
                .font(.mfpBodySmall)
                .monospacedDigit()
                .foregroundStyle(Color.mfpInk)
        }
    }
}
```

### Diary Food Row

```swift
struct FoodRow: View {
    let name: String
    let subtitle: String
    let calories: Int

    var body: some View {
        HStack(spacing: 12) {
            VStack(alignment: .leading, spacing: 2) {
                Text(name)
                    .font(.mfpFoodName)
                    .foregroundStyle(Color.mfpInk)
                    .lineLimit(1)
                Text(subtitle)
                    .font(.mfpFoodSub)
                    .foregroundStyle(Color.mfpSlate)
                    .lineLimit(1)
            }
            Spacer(minLength: 8)
            Text(calories.formatted())
                .font(.mfpCalorie)
                .foregroundStyle(Color.mfpInk)
            Image(systemName: "chevron.right")
                .font(.system(size: 12, weight: .semibold))
                .foregroundStyle(Color.mfpMute)
        }
        .padding(.horizontal, 16)
        .frame(minHeight: 64)
        .background(Color.mfpCanvas)
        .contentShape(Rectangle())
    }
}
```

### Meal Section ("Breakfast", "Lunch", "Dinner", "Snacks")

```swift
struct MealSection<Content: View>: View {
    let title: String
    let totalCalories: Int
    @ViewBuilder var content: () -> Content
    @State private var isExpanded = true

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Button { withAnimation(.easeOut(duration: 0.25)) { isExpanded.toggle() } } label: {
                HStack {
                    Text(title)
                        .font(.mfpMeal)
                        .foregroundStyle(Color.mfpInk)
                    Spacer()
                    Text(totalCalories.formatted())
                        .font(.mfpCalorie)
                        .foregroundStyle(Color.mfpInk)
                }
                .padding(.horizontal, 16)
                .frame(height: 48)
            }
            .buttonStyle(.plain)

            Rectangle().fill(Color.mfpDivider).frame(height: 0.5)

            if isExpanded {
                content()

                // "+ Add Food" row
                Button {} label: {
                    HStack(spacing: 8) {
                        Image(systemName: "plus.circle.fill")
                            .font(.system(size: 22))
                            .foregroundStyle(Color.mfpLakeBlue)
                        Text("Add Food")
                            .font(.system(size: 15, weight: .semibold))
                            .foregroundStyle(Color.mfpLakeBlue)
                        Spacer()
                    }
                    .padding(.horizontal, 16)
                    .frame(height: 56)
                }
                .buttonStyle(.plain)
            }
        }
        .background(Color.mfpCanvas)
    }
}
```

### Diary FAB (centered Scan/Add button above the tab bar)

```swift
struct DiaryFAB: View {
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            ZStack {
                Circle()
                    .fill(Color.mfpLakeBlue)
                    .shadow(color: Color.mfpLakeBlue.opacity(0.35), radius: 16, y: 6)
                Image(systemName: "plus")
                    .font(.system(size: 22, weight: .bold))
                    .foregroundStyle(.white)
            }
            .frame(width: 56, height: 56)
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}
```

### Primary CTA

```swift
struct MFPPrimaryButton: View {
    let label: String
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.mfpButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(
                    RoundedRectangle(cornerRadius: 12)
                        .fill(pressed ? Color.mfpLakePressed : Color.mfpLakeBlue)
                )
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: 0, perform: {}, onPressingChanged: { pressed = $0 })
        .sensoryFeedback(.impact(weight: .light), trigger: pressed)
    }
}
```

### Streak Flame Badge

```swift
struct StreakBadge: View {
    let days: Int
    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: "flame.fill")
                .font(.system(size: 14))
                .foregroundStyle(Color.mfpStreak)
            Text("\(days)")
                .font(.mfpStreak)
                .foregroundStyle(Color.mfpInk)
        }
        .padding(.horizontal, 10)
        .padding(.vertical, 6)
        .background(Capsule().fill(Color.mfpSurfaceGray))
    }
}
```

### Dashboard Card

```swift
struct DashboardCard<Content: View>: View {
    let label: String
    let icon: String
    @ViewBuilder var content: () -> Content

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack {
                Text(label)
                    .font(.mfpMacroLabel)
                    .tracking(0.8)
                    .foregroundStyle(Color.mfpSlate)
                Spacer()
                Image(systemName: icon)
                    .font(.system(size: 14))
                    .foregroundStyle(Color.mfpSlate)
            }
            content()
        }
        .padding(16)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.mfpCanvas)
                .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.mfpDivider, lineWidth: 1))
        )
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        appearance.backgroundColor = UIColor.white.withAlphaComponent(0.92)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            DashboardView().tabItem { Label("Dashboard", systemImage: "square.grid.2x2") }
            DiaryView()    .tabItem { Label("Diary",     systemImage: "book.closed") }
            PlansView()    .tabItem { Label("Plans",     systemImage: "list.clipboard") }
            CommunityView().tabItem { Label("Community", systemImage: "person.3") }
            MoreView()     .tabItem { Label("More",      systemImage: "ellipsis") }
        }
        .tint(Color.mfpLakeBlue)
    }
}
```

## 5. Motion & Haptics

```swift
// Ring fill on food add
.animation(.easeOut(duration: 0.6), value: consumed)
.contentTransition(.numericText(value: Double(remaining)))

// FAB tap
.sensoryFeedback(.impact(weight: .medium), trigger: fabPressed)

// Primary CTA tap
.sensoryFeedback(.impact(weight: .light), trigger: ctaPressed)

// Diary completion confirmation
.sensoryFeedback(.success, trigger: diaryCompleted)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Barcode scan capture
.sensoryFeedback(.success, trigger: barcodeRecognized)

// Streak milestone (7/30/100/365)
.sensoryFeedback(.success, trigger: streakMilestone)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| FAB add | `plus` | 22pt |
| Add food row | `plus.circle.fill` | 22pt |
| Barcode scan | `barcode.viewfinder` | 24pt |
| Search | `magnifyingglass` | 16pt |
| Streak | `flame.fill` | 14pt |
| Steps | `figure.walk` | 14pt |
| Weight | `scalemass` | 14pt |
| Water | `drop.fill` | 14pt |
| Dashboard tab | `square.grid.2x2` / `.fill` | 22pt |
| Diary tab | `book.closed` / `.fill` | 22pt |
| Plans tab | `list.clipboard` | 22pt |
| Community tab | `person.3` / `.fill` | 22pt |
| More tab | `ellipsis` | 22pt |
| Premium gate | `lock.fill` | 14pt |
| Chevron (row) | `chevron.right` | 12pt |
| Date picker (Diary) | `chevron.down` | 12pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `.contentTransition(.numericText)` on the calorie number). On iOS 16, fall back to `UIImpactFeedbackGenerator` and a manual number tween.
- Support Dynamic Type on food names, meal titles, body. Fix the calorie hero number, macro numbers, tab labels, and streak badge.
- VoiceOver: read the calorie ring as `"1,247 calories remaining out of a 2,000 calorie goal; 753 consumed, 0 from exercise"`; macro donut as `"187 grams of carbs (65 percent), 64 grams of fat (88 percent), 92 grams of protein (74 percent)"`. Group food rows as a single accessibility element.
- Contrast: Ink (`#1F2937`) on white meets WCAG AAA at all sizes; Slate (`#4B5563`) meets AA at 14pt+; Mute (`#9CA3AF`) is only for placeholder and disabled text.
- Reduce Motion: skip the ring fill animation; flip the stroke instantly. Disable streak confetti when `accessibilityReduceMotion` is true.
- Dark mode: use `@Environment(\.colorScheme)` — swap canvas, text, divider, surface tokens; Lake Blue lifts to `#3B8FDF` for OLED legibility.
- Tabular numerals: applied via `.monospacedDigit()` on every numeric value — this is non-negotiable.
