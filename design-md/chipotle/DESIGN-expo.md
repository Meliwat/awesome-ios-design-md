# Chipotle (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Chipotle's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  cream:     '#FFF5E1',
  surface:   '#FFFFFF',
  creamDeep: '#F4E8D0',
  divider:   '#E8DCC4',

  textPrimary:   '#451400',
  textSecondary: '#8A6B4F',
  textTertiary:  '#B49A82',

  red:        '#A81612',
  redPressed: '#8C1210',
  redTint:    '#F4E2E1',

  tan:     '#AC8C5B',
  success: '#3C7A3C',
  spice:   '#E07B26',
} as const;

export type CMGColor = keyof typeof colors;
```

## 2. Typography

Headers use Archivo (ALL CAPS); body uses Avenir Next (sentence case). Load Archivo via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Archivo-Bold':       require('../assets/fonts/Archivo-Bold.ttf'),
    'Archivo-ExtraBold':  require('../assets/fonts/Archivo-ExtraBold.ttf'),
    'AvenirNext-Regular': require('../assets/fonts/AvenirNext-Regular.ttf'),
    'AvenirNext-Bold':    require('../assets/fonts/AvenirNext-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const caps = { textTransform: 'uppercase' as const };

export const typography = {
  hero:        { ...caps, fontFamily: 'Archivo-ExtraBold',  fontSize: 32, lineHeight: 34, letterSpacing: 0.4, color: '#451400' },
  screenTitle: { ...caps, fontFamily: 'Archivo-ExtraBold',  fontSize: 26, lineHeight: 29, letterSpacing: 0.3, color: '#451400' },
  section:     { ...caps, fontFamily: 'Archivo-Bold',       fontSize: 18, lineHeight: 21, letterSpacing: 0.6, color: '#451400' },
  button:      { ...caps, fontFamily: 'Archivo-ExtraBold',  fontSize: 16, lineHeight: 18, letterSpacing: 0.8, color: '#FFFFFF' },
  points:      { fontFamily: 'Archivo-ExtraBold',  fontSize: 34, lineHeight: 36, color: '#A81612', fontVariant: ['tabular-nums'] as const },
  badge:       { ...caps, fontFamily: 'Archivo-Bold',       fontSize: 11, lineHeight: 13, letterSpacing: 0.6 },

  itemName:    { fontFamily: 'AvenirNext-Bold',    fontSize: 17, lineHeight: 22, color: '#451400' },
  price:       { fontFamily: 'AvenirNext-Bold',    fontSize: 16, lineHeight: 19, color: '#451400', fontVariant: ['tabular-nums'] as const },
  body:        { fontFamily: 'AvenirNext-Regular', fontSize: 15, lineHeight: 22, color: '#451400' },
  meta:        { fontFamily: 'AvenirNext-Regular', fontSize: 13, lineHeight: 17, color: '#8A6B4F' },
  tab:         { fontFamily: 'AvenirNext-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  caption:     { fontFamily: 'AvenirNext-Regular', fontSize: 11, lineHeight: 14, color: '#8A6B4F' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Section Header (ALL CAPS)

```tsx
// components/SectionHeader.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SectionHeader({ title }: { title: string }) {
  return (
    <View style={{ paddingTop: 24 }}>
      <Text style={typography.section}>{title}</Text>
      <View style={{ height: 1, backgroundColor: colors.divider, marginTop: 8 }} />
    </View>
  );
}
```

### Build-Your-Burrito Ingredient Row

```tsx
// components/IngredientRow.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withSpring } from 'react-native-reanimated';
import { useEffect } from 'react';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function IngredientRow({
  name, meta, selected, multiSelect = false, onPress,
}: { name: string; meta?: string; selected: boolean; multiSelect?: boolean; onPress: () => void }) {
  const s = useSharedValue(selected ? 1 : 0);
  useEffect(() => { s.value = withSpring(selected ? 1 : 0, { damping: 12 }); }, [selected]);
  const checkStyle = useAnimatedStyle(() => ({ transform: [{ scale: s.value }], opacity: s.value }));

  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 12,
        paddingHorizontal: 16, minHeight: 60,
        backgroundColor: selected ? colors.redTint : colors.surface,
        borderLeftWidth: selected ? 1.5 : 0, borderLeftColor: colors.red,
      }}>
      <View style={{ flex: 1 }}>
        <Text style={typography.itemName}>{name}</Text>
        {meta && <Text style={typography.meta}>{meta}</Text>}
      </View>
      <View style={{
        width: multiSelect ? 26 : 28, height: multiSelect ? 26 : 28,
        borderRadius: multiSelect ? 6 : 14, borderWidth: 2,
        borderColor: selected ? colors.red : colors.divider,
        backgroundColor: selected ? colors.red : 'transparent',
        alignItems: 'center', justifyContent: 'center',
      }}>
        <Animated.View style={checkStyle}>
          <Ionicons name="checkmark" size={16} color="#fff" />
        </Animated.View>
      </View>
    </Pressable>
  );
}
```

### Primary CTA (ALL CAPS)

```tsx
// components/ChipotleCTA.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChipotleCTA({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 54, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.redPressed : colors.red,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Rewards Points Ring (SVG)

```tsx
// components/RewardsRing.tsx
import { View, Text } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RewardsRing({ points, goal }: { points: number; goal: number }) {
  const size = 120, stroke = 10, r = (size - stroke) / 2, c = 2 * Math.PI * r;
  const pct = Math.min(1, points / goal);
  return (
    <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={size} height={size} style={{ position: 'absolute' }}>
        <Circle cx={size / 2} cy={size / 2} r={r} stroke={colors.divider} strokeWidth={stroke} fill="none" />
        <Circle cx={size / 2} cy={size / 2} r={r} stroke={colors.red} strokeWidth={stroke} fill="none"
          strokeDasharray={c} strokeDashoffset={c * (1 - pct)} strokeLinecap="round"
          transform={`rotate(-90 ${size / 2} ${size / 2})`} />
      </Svg>
      <Text style={typography.points}>{points.toLocaleString()}</Text>
      <Text style={[typography.badge, { color: colors.textSecondary }]}>POINTS</Text>
    </View>
  );
}
// Animate strokeDashoffset 0 → target over 700ms with a Reanimated shared value for the fill-in.
```

### Step Progress Bar

```tsx
// components/StepProgress.tsx
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function StepProgress({ total, current }: { total: number; current: number }) {
  return (
    <View style={{ flexDirection: 'row', gap: 2, paddingHorizontal: 16 }}>
      {Array.from({ length: total }).map((_, i) => (
        <View key={i} style={{
          flex: 1, height: 4, borderRadius: 500,
          backgroundColor: i <= current ? colors.red : colors.divider,
        }} />
      ))}
    </View>
  );
}
```

## 4. Build-Flow State

```ts
type OrderBuild = {
  rice?: string;            // single-select
  beans?: string;           // single-select
  protein?: string;         // single-select
  toppings: Set<string>;    // multi-select
  salsas: Set<string>;      // multi-select
  extras: Set<string>;      // each adds a "+$2.00" meta tag
};
// runningTotal = base + proteinDelta + extras.size * 2 — surface it in the persistent CTA.
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.red,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.surface, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'AvenirNext-Bold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Order',    tabBarIcon: ({ color }) => <Ionicons name="restaurant-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="scan"     options={{ title: 'Scan',     tabBarIcon: ({ color }) => <Ionicons name="qr-code-outline"    size={24} color={color} /> }} />
      <Tabs.Screen name="delivery" options={{ title: 'Delivery', tabBarIcon: ({ color }) => <Ionicons name="bicycle-outline"   size={24} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',     tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Ingredient select: check scale 0→1
s.value = withSpring(selected ? 1 : 0, { damping: 12 });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Step progress slide: animate segment color via withTiming(250) on `current`

// Rewards ring fill: shared value 0→1 with withTiming(700, Easing.out(Easing.quad)),
// drive strokeDashoffset; number counts via a derived value

// Add to Bag: Haptics.notificationAsync(Success); bag badge withSequence(withSpring(1.2), withSpring(1))

// Sheet present: expo-router modal presentation; warm scrim color rgba(69,20,0,0.40)
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Chipotle's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Selected check | `checkmark` |
| Order | `restaurant-outline` / `restaurant` |
| Scan | `qr-code-outline` / `qr-code` |
| Delivery | `bicycle-outline` / `bicycle` |
| More | `ellipsis-horizontal` |
| Bag | `bag-outline` / `bag` |
| Search | `search` |
| Location chevron | `chevron-down` |
| Quantity minus / plus | `remove` / `add` |
| Rewards / star | `star` |
| Spicy | `flame` |
| Back | `chevron-back` |

## 8. Platform Notes

- **Light-only feel**: the cream-paper warmth is core — do not auto-invert; if you mirror system dark, keep Chipotle Red identical
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` — the cream canvas requires dark content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the persistent "ADD TO BAG" footer must clear the home indicator (`useSafeAreaInsets().bottom`)
- **ALL-CAPS via style**: use `textTransform: 'uppercase'` on header/button styles so the source stays sentence-case and `letterSpacing` does the breathing
- **Tabular figures**: pass `fontVariant: ['tabular-nums']` on prices, calories, and points
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on tab labels and step segments where layout breaks; let item names and body scale
- **Accessibility**: ingredient rows get `accessibilityRole="checkbox"` (multi) or `"radio"` (single) with `accessibilityState={{ checked/selected }}`; the CTA announces "Add to bag, total $X"
