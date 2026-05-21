# MyFitnessPal (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates MyFitnessPal's visual language into paste-ready Expo / React Native code: a design-token module, themed components, ring/donut drawn with `react-native-svg`, and Reanimated transitions on the calorie number.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `react-native-reanimated` v3, and `react-native-svg`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces
  canvas:        '#FFFFFF',
  surfaceGray:   '#F5F7FA',
  surfaceGray2:  '#E5E9F0',
  divider:       '#E5E7EB',
  hairline:      '#EEF1F5',

  // Text
  ink:           '#1F2937',
  slate:         '#4B5563',
  mute:          '#9CA3AF',
  heroNumber:    '#111827',

  // Brand
  mfpBlue:       '#005DAA',  // heritage
  lakeBlue:      '#0072CE',  // primary action
  lakePressed:   '#005FA8',
  skyBlue:       '#E7F0FF',

  // Macro trio (locked order)
  carbs:         '#FF9F1C',  // orange
  fat:           '#A463F2',  // purple
  protein:       '#19C37D',  // green

  // Semantic
  underGoal:     '#19C37D',
  approaching:   '#F59E0B',
  overLimit:     '#EF4444',
  streak:        '#FB923C',
  premiumGold:   '#D4A437',

  // Dark mode
  darkCanvas:    '#0F1419',
  darkSurface1:  '#1A1F26',
  darkSurface2:  '#252B33',
  darkDivider:   '#2A2F37',
  darkText:      '#F3F4F6',
  darkTextSec:   '#9CA3AF',
  lakeDark:      '#3B8FDF',
} as const;

export type MFPColor = keyof typeof colors;
```

## 2. Typography

SF Pro is the default system face on iOS. Tabular numerals must be applied to every numeric value via `fontVariant: ['tabular-nums']`.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const SYS = 'System'; // resolves to SF Pro on iOS
const TNUM: TextStyle = { fontVariant: ['tabular-nums'] };

const ink   = { color: '#1F2937' };
const slate = { color: '#4B5563' };

export const typography = {
  // Calorie ring
  calorieHero:  { ...TNUM, color: '#111827', fontFamily: SYS, fontSize: 56, fontWeight: '700', letterSpacing: -1.0, lineHeight: 56 },
  calorieLabel: {          color: '#4B5563', fontFamily: SYS, fontSize: 13, fontWeight: '600', letterSpacing: 0.6,  lineHeight: 13 },

  // Titles
  largeNav:     { ...ink, fontFamily: SYS, fontSize: 28, fontWeight: '700', letterSpacing: -0.4, lineHeight: 32 },
  section:      { ...ink, fontFamily: SYS, fontSize: 22, fontWeight: '700', letterSpacing: -0.3, lineHeight: 26 },
  meal:         { ...ink, fontFamily: SYS, fontSize: 18, fontWeight: '600', letterSpacing: -0.2, lineHeight: 22 },

  // Diary rows
  foodName:     { ...ink,   fontFamily: SYS, fontSize: 16, fontWeight: '500', letterSpacing: -0.1, lineHeight: 20 },
  foodSub:      { ...slate, fontFamily: SYS, fontSize: 13, fontWeight: '400', lineHeight: 17 },
  calorie:      { ...ink, ...TNUM, fontFamily: SYS, fontSize: 16, fontWeight: '600', lineHeight: 18 },

  // Body
  body:         { ...ink,   fontFamily: SYS, fontSize: 15, fontWeight: '400', lineHeight: 21 },
  bodySmall:    { ...ink,   fontFamily: SYS, fontSize: 13, fontWeight: '400', lineHeight: 18 },

  // Macros
  macroNumber:  { ...ink, ...TNUM, fontFamily: SYS, fontSize: 22, fontWeight: '700', letterSpacing: -0.3, lineHeight: 24 },
  macroLabel:   { ...slate, fontFamily: SYS, fontSize: 11, fontWeight: '600', letterSpacing: 0.8, lineHeight: 13 },

  // Buttons & misc
  button:       { color: '#FFFFFF', fontFamily: SYS, fontSize: 16, fontWeight: '600', lineHeight: 18 },
  buttonTinted: { color: '#0072CE', fontFamily: SYS, fontSize: 16, fontWeight: '600', lineHeight: 18 },
  tab:          { fontFamily: SYS, fontSize: 10, fontWeight: '600', letterSpacing: 0.2, lineHeight: 12 },
  streak:       { ...ink, ...TNUM, fontFamily: SYS, fontSize: 12, fontWeight: '700', lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Calorie Ring (the hero of Dashboard)

```tsx
// components/CalorieRing.tsx
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import Svg, { Circle, G } from 'react-native-svg';
import { View, Text } from 'react-native';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);

function ringColor(consumed: number, goal: number) {
  const used = consumed / Math.max(goal, 1);
  if (used < 0.6)   return colors.underGoal;
  if (used < 0.95)  return colors.lakeBlue;
  if (used < 1.0)   return colors.approaching;
  return colors.overLimit;
}

type Props = { goal: number; consumed: number; exercise: number; diameter?: number; stroke?: number };

export function CalorieRing({ goal, consumed, exercise, diameter = 220, stroke = 18 }: Props) {
  const remaining = goal - consumed + exercise;
  const radius = (diameter - stroke) / 2;
  const circumference = 2 * Math.PI * radius;
  const target = Math.min(consumed / Math.max(goal, 1), 1);
  const animatedFrac = useSharedValue(0);

  useEffect(() => {
    animatedFrac.value = withTiming(target, { duration: 600, easing: Easing.out(Easing.cubic) });
  }, [target]);

  const animatedProps = useAnimatedProps(() => ({
    strokeDashoffset: circumference * (1 - animatedFrac.value),
  }));

  return (
    <View style={{ width: diameter, height: diameter, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={diameter} height={diameter}>
        <G rotation={-90} origin={`${diameter / 2}, ${diameter / 2}`}>
          <Circle cx={diameter / 2} cy={diameter / 2} r={radius} stroke={colors.surfaceGray2} strokeWidth={stroke} fill="none" />
          <AnimatedCircle
            cx={diameter / 2}
            cy={diameter / 2}
            r={radius}
            stroke={ringColor(consumed, goal)}
            strokeWidth={stroke}
            fill="none"
            strokeLinecap="round"
            strokeDasharray={circumference}
            animatedProps={animatedProps}
          />
        </G>
      </Svg>
      <View style={{ position: 'absolute', alignItems: 'center', gap: 4 }}>
        <Text style={typography.calorieLabel}>REMAINING</Text>
        <Text style={typography.calorieHero}>{remaining.toLocaleString()}</Text>
        <Text style={typography.bodySmall}>calories</Text>
      </View>
    </View>
  );
}
```

### Macro Donut

```tsx
// components/MacroDonut.tsx
import { View, Text } from 'react-native';
import Svg, { Circle, G } from 'react-native-svg';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { carbs: number; fat: number; protein: number; carbsGoal: number; fatGoal: number; proteinGoal: number };

export function MacroDonut({ carbs, fat, protein, carbsGoal, fatGoal, proteinGoal }: Props) {
  const total = carbs + fat + protein;
  const diameter = 96;
  const stroke = 10;
  const r = (diameter - stroke) / 2;
  const C = 2 * Math.PI * r;

  const carbsFrac   = total === 0 ? 0 : carbs   / total;
  const fatFrac     = total === 0 ? 0 : fat     / total;
  const proteinFrac = total === 0 ? 0 : protein / total;

  const totalGoal = carbsGoal + fatGoal + proteinGoal;
  const pctOfGoal = Math.round((total / Math.max(totalGoal, 1)) * 100);

  return (
    <View style={{ alignItems: 'center', gap: 16 }}>
      <View style={{ width: diameter, height: diameter, alignItems: 'center', justifyContent: 'center' }}>
        <Svg width={diameter} height={diameter}>
          <G rotation={-90} origin={`${diameter / 2}, ${diameter / 2}`}>
            <Circle cx={diameter / 2} cy={diameter / 2} r={r} stroke={colors.carbs}   strokeWidth={stroke} fill="none" strokeDasharray={`${carbsFrac * C} ${C}`} />
            <Circle cx={diameter / 2} cy={diameter / 2} r={r} stroke={colors.fat}     strokeWidth={stroke} fill="none" strokeDasharray={`${fatFrac * C} ${C}`}   strokeDashoffset={-carbsFrac * C} />
            <Circle cx={diameter / 2} cy={diameter / 2} r={r} stroke={colors.protein} strokeWidth={stroke} fill="none" strokeDasharray={`${proteinFrac * C} ${C}`} strokeDashoffset={-(carbsFrac + fatFrac) * C} />
          </G>
        </Svg>
        <Text style={[typography.macroNumber, { position: 'absolute' }]}>{pctOfGoal}%</Text>
      </View>

      <View style={{ flexDirection: 'row', gap: 24 }}>
        <MacroLegend color={colors.carbs}   label="CARBS"   grams={carbs}   goal={carbsGoal} />
        <MacroLegend color={colors.fat}     label="FAT"     grams={fat}     goal={fatGoal} />
        <MacroLegend color={colors.protein} label="PROTEIN" grams={protein} goal={proteinGoal} />
      </View>
    </View>
  );
}

function MacroLegend({ color, label, grams, goal }: { color: string; label: string; grams: number; goal: number }) {
  return (
    <View style={{ gap: 4 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <View style={{ width: 8, height: 8, borderRadius: 2, backgroundColor: color }} />
        <Text style={typography.macroLabel}>{label}</Text>
      </View>
      <Text style={[typography.bodySmall, { fontVariant: ['tabular-nums'] }]}>
        {grams}g · {Math.round((grams / Math.max(goal, 1)) * 100)}%
      </Text>
    </View>
  );
}
```

### Diary Food Row

```tsx
// components/FoodRow.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FoodRow({ name, subtitle, calories, onPress }: { name: string; subtitle: string; calories: number; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      paddingHorizontal: 16, minHeight: 64, flexDirection: 'row', alignItems: 'center',
      backgroundColor: pressed ? colors.surfaceGray : colors.canvas,
    })}>
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.foodName} numberOfLines={1}>{name}</Text>
        <Text style={typography.foodSub} numberOfLines={1}>{subtitle}</Text>
      </View>
      <Text style={typography.calorie}>{calories.toLocaleString()}</Text>
      <Ionicons name="chevron-forward" size={14} color={colors.mute} style={{ marginLeft: 12 }} />
    </Pressable>
  );
}
```

### Meal Section

```tsx
// components/MealSection.tsx
import { useState } from 'react';
import { Pressable, View, Text, LayoutAnimation } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { title: string; totalCalories: number; children: React.ReactNode };

export function MealSection({ title, totalCalories, children }: Props) {
  const [expanded, setExpanded] = useState(true);

  return (
    <View style={{ backgroundColor: colors.canvas }}>
      <Pressable
        onPress={() => {
          LayoutAnimation.configureNext(LayoutAnimation.Presets.easeInEaseOut);
          setExpanded((e) => !e);
        }}
        style={{ height: 48, paddingHorizontal: 16, flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}
      >
        <Text style={typography.meal}>{title}</Text>
        <Text style={typography.calorie}>{totalCalories.toLocaleString()}</Text>
      </Pressable>
      <View style={{ height: 0.5, backgroundColor: colors.divider }} />
      {expanded && (
        <>
          {children}
          <Pressable style={({ pressed }) => ({
            paddingHorizontal: 16, height: 56, flexDirection: 'row', alignItems: 'center', gap: 8,
            backgroundColor: pressed ? colors.surfaceGray : colors.canvas,
          })}>
            <Ionicons name="add-circle" size={22} color={colors.lakeBlue} />
            <Text style={{ fontFamily: 'System', fontSize: 15, fontWeight: '600', color: colors.lakeBlue }}>Add Food</Text>
          </Pressable>
        </>
      )}
    </View>
  );
}
```

### Diary FAB (the centered scan/add button)

```tsx
// components/DiaryFAB.tsx
import { Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function DiaryFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        width: 56, height: 56, borderRadius: 28,
        backgroundColor: pressed ? colors.lakePressed : colors.lakeBlue,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: colors.lakeBlue, shadowOpacity: 0.35, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
        elevation: 8,
        transform: [{ scale: pressed ? 0.95 : 1 }],
      })}
    >
      <Ionicons name="add" size={26} color="#FFFFFF" />
    </Pressable>
  );
}
```

### Primary CTA

```tsx
// components/MFPPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MFPPrimaryButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        height: 48, borderRadius: 12, paddingHorizontal: 28, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.lakePressed : colors.lakeBlue,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Streak Flame Badge

```tsx
// components/StreakBadge.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StreakBadge({ days }: { days: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, backgroundColor: colors.surfaceGray, paddingHorizontal: 10, paddingVertical: 6, borderRadius: 500 }}>
      <Ionicons name="flame" size={14} color={colors.streak} />
      <Text style={typography.streak}>{days}</Text>
    </View>
  );
}
```

### Dashboard Card

```tsx
// components/DashboardCard.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { label: string; icon: keyof typeof Ionicons.glyphMap; children: React.ReactNode };

export function DashboardCard({ label, icon, children }: Props) {
  return (
    <View style={{
      padding: 16, borderRadius: 16, backgroundColor: colors.canvas,
      borderWidth: 1, borderColor: colors.divider, gap: 8,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={typography.macroLabel}>{label}</Text>
        <Ionicons name={icon} size={14} color={colors.slate} />
      </View>
      {children}
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.lakeBlue,
        tabBarInactiveTintColor: colors.mute,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.92)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'System', fontSize: 10, fontWeight: '600', letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Dashboard', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'apps' : 'apps-outline'}                 size={22} color={color} /> }} />
      <Tabs.Screen name="diary"     options={{ title: 'Diary',     tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'book' : 'book-outline'}                 size={22} color={color} /> }} />
      <Tabs.Screen name="plans"     options={{ title: 'Plans',     tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'clipboard' : 'clipboard-outline'}       size={22} color={color} /> }} />
      <Tabs.Screen name="community" options={{ title: 'Community', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'people' : 'people-outline'}             size={22} color={color} /> }} />
      <Tabs.Screen name="more"      options={{ title: 'More',      tabBarIcon: ({ color })          => <Ionicons name="ellipsis-horizontal"                              size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Ring fill on food add — see CalorieRing.tsx (Reanimated withTiming over 600ms ease-out)

// FAB tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Primary CTA tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Diary completion
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Barcode capture
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Tab switch
Haptics.selectionAsync();

// Streak milestone (7/30/100/365)
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

## 6. Icon Library

`@expo/vector-icons` — Ionicons. Mapping:

| Purpose | Ionicons |
|---------|----------|
| FAB add | `add` |
| Add food row | `add-circle` |
| Barcode scan | `barcode` / `barcode-outline` |
| Search | `search` |
| Streak flame | `flame` / `flame-outline` |
| Steps | `walk` |
| Weight | `fitness` |
| Water | `water` |
| Dashboard tab | `apps` / `apps-outline` |
| Diary tab | `book` / `book-outline` |
| Plans tab | `clipboard` / `clipboard-outline` |
| Community tab | `people` / `people-outline` |
| More tab | `ellipsis-horizontal` |
| Premium lock | `lock-closed` |
| Row chevron | `chevron-forward` |
| Date picker dropdown | `chevron-down` |

## 7. Platform Notes

- **iOS-first blur**: Use `expo-blur` `tint="light"` at intensity 80 for the tab bar background. Android falls back to `rgba(255,255,255,0.96)`.
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` on light-canvas screens.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The Diary FAB must sit 88pt above the safe-area bottom inset.
- **Tabular numerals**: React Native supports `fontVariant: ['tabular-nums']` natively on iOS. Apply to every calorie/macro/weight/water number.
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` — when true, set ring `withTiming` duration to 0 and disable streak confetti.
- **Ring drawing**: `react-native-svg` `Circle` + Reanimated `useAnimatedProps` is the right path; do not try to draw the ring with CSS `border-radius` tricks — the gradient ring needs SVG stroke.
- **Number flip**: For the giant calorie number animation, wrap in `Animated.Text` and use `useAnimatedReaction` to morph value strings, or use the cross-platform `react-native-animateable-text` library on production.
- **Dark mode**: `useColorScheme()` swaps the token object; Lake Blue shifts from `#0072CE` → `#3B8FDF` for OLED legibility.
