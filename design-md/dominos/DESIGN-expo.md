# Domino's (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Domino's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets — including the signature five-stage pizza tracker.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:      '#FFFFFF',
  surface:     '#F4F4F4',
  surfaceDeep: '#EAEAEA',
  divider:     '#E2E2E2',

  textPrimary:   '#1F1F1F',
  textSecondary: '#6E6E6E',
  textTertiary:  '#9A9A9A',

  red:         '#E31837',
  redPressed:  '#C2122E',
  redTint:     '#FCE7EB',
  blue:        '#006491',
  bluePressed: '#00547A',
  blueTint:    '#E0EEF4',

  success: '#1E8E3E',
  warning: '#F5A623',
} as const;

export type DPZColor = keyof typeof colors;
```

## 2. Typography

Domino's uses Archivo. Load via `expo-font`; fall back to `System`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Archivo-Regular': require('../assets/fonts/Archivo-Regular.ttf'),
    'Archivo-Bold':    require('../assets/fonts/Archivo-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#1F1F1F' } satisfies TextStyle;
const tnum = ['tabular-nums'] as const;

export const typography = {
  screenTitle:  { ...ink, fontFamily: 'Archivo-Bold',    fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  trackerStage: { ...ink, fontFamily: 'Archivo-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  section:      { ...ink, fontFamily: 'Archivo-Bold',    fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  itemName:     { ...ink, fontFamily: 'Archivo-Bold',    fontSize: 17, lineHeight: 22 },
  price:        { ...ink, fontFamily: 'Archivo-Bold',    fontSize: 16, lineHeight: 19, fontVariant: tnum },
  body:         { ...ink, fontFamily: 'Archivo-Regular', fontSize: 15, lineHeight: 22 },
  button:       { color: '#FFFFFF', fontFamily: 'Archivo-Bold', fontSize: 16, lineHeight: 20, letterSpacing: 0.3 },
  dealPrice:    { color: '#E31837', fontFamily: 'Archivo-Bold', fontSize: 28, lineHeight: 30, letterSpacing: -0.4, fontVariant: tnum },
  meta:         { fontFamily: 'Archivo-Regular', fontSize: 13, lineHeight: 17, color: '#6E6E6E' },
  badge:        { fontFamily: 'Archivo-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.4 },
  tab:          { fontFamily: 'Archivo-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  caption:      { fontFamily: 'Archivo-Regular', fontSize: 11, lineHeight: 14, color: '#6E6E6E' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Five-Stage Pizza Tracker

```tsx
// components/PizzaTracker.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { useEffect } from 'react';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const STAGES = ['Prep', 'Bake', 'Box', 'Quality Check', 'Out for delivery'];
const HEADLINES = [
  "We're prepping your order", 'Your pizza is in the oven!', 'Boxing it up',
  'Quality check in progress', 'Out for delivery!',
];

export function PizzaTracker({ current, eta }: { current: number; eta: string }) {
  return (
    <View style={{ backgroundColor: colors.canvas, borderRadius: 12, padding: 16,
                    shadowColor: colors.textPrimary, shadowOpacity: 0.12, shadowRadius: 14,
                    shadowOffset: { width: 0, height: 4 } }}>
      <Text style={typography.trackerStage}>{HEADLINES[current]}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', marginVertical: 16 }}>
        {STAGES.map((label, i) => (
          <View key={label} style={{ flexDirection: 'row', alignItems: 'center', flex: i < 4 ? 1 : 0 }}>
            <Node label={label} completed={i < current} active={i === current} />
            {i < 4 && (
              <View style={{ flex: 1, height: 4,
                backgroundColor: i < current ? colors.blue : i === current ? colors.red : colors.divider }} />
            )}
          </View>
        ))}
      </View>
      <Text style={typography.meta}>Estimated delivery {eta}</Text>
      <Pressable hitSlop={8}>
        <Text style={{ ...typography.meta, color: colors.blue, fontFamily: 'Archivo-Bold', marginTop: 8 }}>
          Track on the map
        </Text>
      </Pressable>
    </View>
  );
}

function Node({ label, completed, active }: { label: string; completed: boolean; active: boolean }) {
  const pulse = useSharedValue(1);
  useEffect(() => {
    if (active) pulse.value = withRepeat(withTiming(1.06, { duration: 800 }), -1, true);
    else pulse.value = 1;
  }, [active]);
  const dot = useAnimatedStyle(() => ({ transform: [{ scale: pulse.value }] }));
  const fill = completed ? colors.blue : active ? colors.red : 'transparent';
  const stroke = completed ? colors.blue : active ? colors.red : colors.divider;

  return (
    <View style={{ alignItems: 'center', width: 56 }}>
      <Animated.View style={[{ width: 24, height: 24, borderRadius: 12, borderWidth: 2,
        borderColor: stroke, backgroundColor: fill, alignItems: 'center', justifyContent: 'center' }, dot]}>
        {completed && <Ionicons name="checkmark" size={13} color="#fff" />}
      </Animated.View>
      <Text numberOfLines={1} style={{ ...typography.caption, marginTop: 6,
        fontFamily: completed || active ? 'Archivo-Bold' : 'Archivo-Regular',
        color: active ? colors.red : completed ? colors.textPrimary : colors.textTertiary }}>
        {label}
      </Text>
    </View>
  );
}
```

### Primary CTA

```tsx
// components/DominosCTA.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function DominosCTA({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 52, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.redPressed : colors.red,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Deal Card

```tsx
// components/DealCard.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function DominoMark() {
  return (
    <View style={{ flexDirection: 'row', width: 22, height: 22, borderRadius: 4, overflow: 'hidden' }}>
      <View style={{ flex: 1, backgroundColor: colors.red }} />
      <View style={{ width: 1 }} />
      <View style={{ flex: 1, backgroundColor: colors.blue }} />
    </View>
  );
}

export function DealCard({
  price, name, finePrint, onAdd,
}: { price: string; name: string; finePrint: string; onAdd: () => void }) {
  return (
    <View style={{ height: 150, backgroundColor: colors.canvas, borderRadius: 8,
                    borderWidth: 2, borderColor: colors.red, padding: 16 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={typography.dealPrice}>{price}</Text>
        <DominoMark />
      </View>
      <Text style={[typography.itemName, { marginTop: 6 }]}>{name}</Text>
      <Text style={typography.meta}>{finePrint}</Text>
      <View style={{ flex: 1 }} />
      <Pressable onPress={onAdd} style={{ alignSelf: 'flex-end', backgroundColor: colors.red,
        borderRadius: 8, paddingVertical: 8, paddingHorizontal: 16 }}>
        <Text style={{ ...typography.badge, color: '#fff' }}>ADD DEAL</Text>
      </Pressable>
    </View>
  );
}
```

### Build-Your-Pizza Live Preview

```tsx
// components/PizzaPreview.tsx
import Animated, { LinearTransition } from 'react-native-reanimated';
import { View } from 'react-native';

export function PizzaPreview({
  crustWidth, sauceColor, hasCheese,
}: { crustWidth: number; sauceColor: string; hasCheese: boolean }) {
  return (
    <Animated.View layout={LinearTransition.duration(250)}
      style={{ width: 160, height: 160, borderRadius: 80, backgroundColor: '#EDD9A8',
               borderWidth: crustWidth, borderColor: '#CC9E5C', overflow: 'hidden',
               alignItems: 'center', justifyContent: 'center' }}>
      <View style={{ position: 'absolute', inset: crustWidth + 6, borderRadius: 80,
                      backgroundColor: sauceColor }} />
      {hasCheese && (
        <View style={{ position: 'absolute', inset: crustWidth + 10, borderRadius: 80,
                        backgroundColor: 'rgba(250,220,115,0.6)' }} />
      )}
      {/* topping garnishes scattered as small absolutely-positioned circles */}
    </Animated.View>
  );
}
```

## 4. Tracker State Progression

```ts
// Hold `current` (0-4) in state. Advancing recomputes node/rail colors;
// wrap the setState in a LayoutAnimation or animate the rail width with a
// shared value (withTiming 600, Easing.out) for the red fill hand-off,
// then Haptics.impactAsync(Soft).
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
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'Archivo-Bold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Order',   tabBarIcon: ({ color }) => <Ionicons name="restaurant-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="tracker" options={{ title: 'Tracker', tabBarIcon: ({ color }) => <Ionicons name="location-outline"   size={24} color={color} /> }} />
      <Tabs.Screen name="deals"   options={{ title: 'Deals',   tabBarIcon: ({ color }) => <Ionicons name="pricetag-outline"   size={24} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color }) => <Ionicons name="person-outline"     size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Tracker stage advance: animate rail-fill shared value withTiming(600, Easing.out(Easing.quad))
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Active node pulse: pulse.value = withRepeat(withTiming(1.06, { duration: 800 }), -1, true)

// Build-pizza layer add: Animated.View layout={LinearTransition.duration(250)}

// Add to Order: Haptics.notificationAsync(Success); cart badge withSequence(withSpring(1.2), withSpring(1))

// Sheet present: expo-router modal; scrim rgba(31,31,31,0.45)
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Domino's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Completed stage check | `checkmark` |
| Order | `restaurant-outline` / `restaurant` |
| Tracker | `location-outline` / `location` |
| Deals | `pricetag-outline` / `pricetag` |
| Account | `person-outline` / `person` |
| Cart | `cart-outline` / `cart` |
| Search | `search` |
| Store chevron | `chevron-down` |
| Quantity minus / plus | `remove` / `add` |
| Map / track | `map-outline` |
| Time / ETA | `time-outline` |
| Back | `chevron-back` |

## 8. Platform Notes

- **Light-only feel**: the bright catalog + tracker depend on white — do not auto-invert; if you mirror system dark, keep red and blue identical
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` — the white canvas requires dark content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the persistent order footer must clear the home indicator (`useSafeAreaInsets().bottom`)
- **Tabular figures**: pass `fontVariant: ['tabular-nums']` on prices, ETAs, deal callouts, and totals
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on tab labels and tracker node labels (abbreviate on small widths); let names and body scale
- **Accessibility**: expose the tracker as a single `accessibilityLabel` ("Order status: Bake, stage 2 of 5, ETA 7:45 PM") with `accessibilityRole="progressbar"`; the CTA announces "Add to order, total $X"
- **Tracker is the hero**: ensure the tracker card renders above the fold on the active-order screen and is not collapsible
