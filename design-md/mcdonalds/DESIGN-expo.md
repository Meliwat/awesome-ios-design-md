# McDonald's (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates McDonald's visual language into paste-ready Expo / React Native code: a design-token module, themed components (MyMcDonald's Rewards card, deal tile, order-mode selector, the elevated Order FAB tab bar), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  mcdYellow:        '#FFC72C',
  mcdYellowPressed: '#E6B015',
  mcdRed:           '#DA291C',
  mcdRedPressed:    '#B71F14',
  mcdDarkRed:       '#B0210E',

  // Text on yellow — mandatory, never white
  onYellow: '#1A1A1A',

  // Surfaces (light)
  canvas:    '#FFFFFF',
  surface1L: '#F4F4F5',
  surface2L: '#EAEAEB',
  dividerL:  '#E3E3E5',

  // Surfaces (dark)
  darkCanvas:   '#121212',
  darkSurface1: '#1C1C1E',
  darkSurface2: '#2A2A2A',
  darkDivider:  '#2C2C2E',

  // Text
  textPrimaryL:   '#1A1A1A',
  textSecondaryL: '#5C5C5C',
  textTertiaryL:  '#8A8A8A',
  textPrimaryD:   '#F2F2F2',
  textSecondaryD: '#A0A0A0',

  // Semantic
  success: '#2B8A3E',
  warning: '#E6A700',
  info:    '#2E6DB4',
} as const;

export type McdColor = keyof typeof colors;
```

## 2. Typography

Load proprietary **Speedee** via `expo-font`; fall back to system. Tabular numerals for points, prices, timers.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Speedee-Regular': require('../assets/fonts/Speedee-Regular.ttf'),
    'Speedee-Medium':  require('../assets/fonts/Speedee-Medium.ttf'),
    'Speedee-Bold':    require('../assets/fonts/Speedee-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#F2F2F2' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle:   { ...primary, fontFamily: 'Speedee-Bold', fontSize: 32, lineHeight: 38, letterSpacing: -0.4 },
  rewardsNumber: { color: '#FFC72C', ...tnum, fontFamily: 'Speedee-Bold', fontSize: 28, lineHeight: 30, letterSpacing: -0.5 },
  hero:          { ...primary, fontFamily: 'Speedee-Bold', fontSize: 26, lineHeight: 33, letterSpacing: -0.3 },
  greeting:      { ...primary, fontFamily: 'Speedee-Bold', fontSize: 22, lineHeight: 28, letterSpacing: -0.4 },
  section:       { ...primary, fontFamily: 'Speedee-Bold', fontSize: 19, lineHeight: 25, letterSpacing: -0.3 },
  cardTitle:     { ...primary, fontFamily: 'Speedee-Bold', fontSize: 18, lineHeight: 23, letterSpacing: -0.3 },
  body:          { ...primary, fontFamily: 'Speedee-Regular', fontSize: 16, lineHeight: 24 },
  dealTitle:     { ...primary, fontFamily: 'Speedee-Medium', fontSize: 15, lineHeight: 21 },
  price:         { ...primary, ...tnum, fontFamily: 'Speedee-Bold', fontSize: 14, lineHeight: 20 },
  meta:          { color: '#A0A0A0', fontFamily: 'Speedee-Regular', fontSize: 14, lineHeight: 20 },
  badge:         { color: '#FFFFFF', fontFamily: 'Speedee-Bold', fontSize: 12, lineHeight: 13, letterSpacing: 0.3, textTransform: 'uppercase' as const },
  button:        { color: '#1A1A1A', fontFamily: 'Speedee-Bold', fontSize: 15, lineHeight: 15, letterSpacing: 0.1 },
  caption:       { color: '#A0A0A0', fontFamily: 'Speedee-Regular', fontSize: 12, lineHeight: 17 },
  tab:           { color: '#A0A0A0', fontFamily: 'Speedee-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  modeLabel:     { ...primary, fontFamily: 'Speedee-Bold', fontSize: 11, lineHeight: 13 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### MyMcDonald's Rewards Card

```tsx
// components/RewardsCard.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RewardsCard({
  points, progress, pointsToNext, nextReward,
}: { points: number; progress: number; pointsToNext: number; nextReward: string }) {
  const fill = useSharedValue(0);
  const fillStyle = useAnimatedStyle(() => ({ width: `${fill.value * 100}%` }));

  useEffect(() => {
    fill.value = withTiming(progress, { duration: 600, easing: Easing.out(Easing.cubic) });
  }, [progress]);

  return (
    <View style={{
      marginHorizontal: 18, padding: 16, borderRadius: 16,
      backgroundColor: colors.darkSurface1, overflow: 'hidden',
    }}>
      {/* corner glow */}
      <View style={{
        position: 'absolute', right: -30, top: -30, width: 130, height: 130, borderRadius: 65,
        backgroundColor: 'rgba(255,199,44,0.15)',
      }} />
      <Text style={[typography.badge, { color: colors.textSecondaryD }]}>MYMCDONALD'S REWARDS</Text>
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 6, marginTop: 6 }}>
        <Text style={typography.rewardsNumber}>{points.toLocaleString()}</Text>
        <Text style={[typography.price, { color: colors.textSecondaryD, marginBottom: 4 }]}>points</Text>
      </View>

      <View style={{ height: 8, borderRadius: 4, backgroundColor: '#3A3A3A', marginVertical: 12, overflow: 'hidden' }}>
        <Animated.View style={fillStyle}>
          <LinearGradient
            colors={[colors.mcdYellow, '#FFD75E']}
            start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
            style={{ height: 8, borderRadius: 4 }}
          />
        </Animated.View>
      </View>

      <Text style={typography.caption}>
        Just <Text style={{ color: colors.textPrimaryD, fontFamily: 'Speedee-Bold' }}>{pointsToNext} points</Text> away from {nextReward}
      </Text>
    </View>
  );
}
```

### Deal Tile

```tsx
// components/DealTile.tsx
import { Text, View } from 'react-native';
import { Image } from 'expo-image';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function DealTile({
  imageUri, flag, name, offer, pointsAlt,
}: { imageUri: string; flag: string; name: string; offer: string; pointsAlt: string }) {
  return (
    <View style={{
      flex: 1, borderRadius: 16, overflow: 'hidden',
      backgroundColor: colors.darkSurface1, borderWidth: 1, borderColor: colors.darkDivider,
    }}>
      <View style={{ height: 96 }}>
        <Image source={{ uri: imageUri }} style={{ flex: 1 }} contentFit="cover" />
        <View style={{
          position: 'absolute', top: 8, left: 8,
          backgroundColor: colors.mcdRed, borderRadius: 6,
          paddingHorizontal: 7, paddingVertical: 3,
        }}>
          <Text style={typography.badge}>{flag}</Text>
        </View>
      </View>
      <View style={{ paddingHorizontal: 12, paddingTop: 10, paddingBottom: 14 }}>
        <Text style={typography.cardTitle}>{name}</Text>
        <Text style={[typography.caption, { marginTop: 3 }]}>{offer}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 8 }}>
          <Ionicons name="star" size={11} color={colors.mcdYellow} />
          <Text style={[typography.badge, { color: colors.mcdYellow }]}>or {pointsAlt} pts</Text>
        </View>
      </View>
    </View>
  );
}
```

### Order-Mode Selector

```tsx
// components/OrderModeSelector.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const MODES = [
  { key: 'pickup',   label: 'Pickup',     icon: 'bag-outline' },
  { key: 'curbside', label: 'Curbside',   icon: 'car-outline' },
  { key: 'drive',    label: 'Drive Thru', icon: 'car-sport-outline' },
  { key: 'delivery', label: 'Delivery',   icon: 'bicycle-outline' },
] as const;

export function OrderModeSelector() {
  const [sel, setSel] = useState<string>('pickup');
  return (
    <View style={{ marginHorizontal: 18, padding: 14, borderRadius: 16, backgroundColor: colors.darkSurface1 }}>
      <View style={{ flexDirection: 'row', gap: 8 }}>
        {MODES.map((m) => {
          const active = sel === m.key;
          return (
            <Pressable
              key={m.key}
              onPress={() => { setSel(m.key); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); }}
              style={{
                flex: 1, alignItems: 'center', gap: 6, paddingVertical: 12,
                borderRadius: 12, borderWidth: 1.5,
                borderColor: active ? colors.mcdYellow : colors.darkDivider,
                backgroundColor: active ? 'rgba(255,199,44,0.08)' : 'transparent',
              }}
            >
              <Ionicons name={m.icon as any} size={22}
                color={active ? colors.mcdYellow : colors.textPrimaryD} />
              <Text style={[typography.modeLabel, active && { color: colors.mcdYellow }]}>{m.label}</Text>
            </Pressable>
          );
        })}
      </View>
    </View>
  );
}
```

### Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        borderRadius: 999, paddingVertical: 14, alignItems: 'center',
        backgroundColor: pressed ? colors.mcdYellowPressed : colors.mcdYellow,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button /* #1A1A1A — never white */}>{title}</Text>
    </Pressable>
  );
}

export function SecondaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        borderRadius: 999, paddingVertical: 14, alignItems: 'center',
        backgroundColor: pressed ? colors.mcdRedPressed : colors.mcdRed,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar (with elevated Order FAB)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

function OrderFab({ focused }: { focused: boolean }) {
  return (
    <View style={{
      width: 50, height: 50, borderRadius: 25, marginTop: -18,
      backgroundColor: colors.mcdYellow, alignItems: 'center', justifyContent: 'center',
      shadowColor: colors.mcdYellow, shadowOpacity: 0.4, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
      elevation: 10,
    }}>
      <Ionicons name="cart" size={22} color={colors.onYellow} />
    </View>
  );
}

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  colors.mcdYellow,
        tabBarInactiveTintColor: '#888888',
        tabBarStyle: {
          backgroundColor: colors.darkCanvas,
          borderTopWidth: 0.5, borderTopColor: colors.darkDivider, height: 70,
        },
        tabBarLabelStyle: { fontFamily: 'Speedee-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home" size={22} color={color} /> }} />
      <Tabs.Screen name="rewards" options={{ title: 'Rewards', tabBarIcon: ({ color }) => <Ionicons name="star" size={22} color={color} /> }} />
      <Tabs.Screen name="order"   options={{ title: 'Order', tabBarIcon: ({ focused }) => <OrderFab focused={focused} />, tabBarLabelStyle: { color: colors.mcdYellow, fontFamily: 'Speedee-Bold', fontSize: 10 } }} />
      <Tabs.Screen name="menu"    options={{ title: 'Menu',    tabBarIcon: ({ color }) => <Ionicons name="grid" size={22} color={color} /> }} />
      <Tabs.Screen name="more"    options={{ title: 'More',    tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Add to Mobile Order — FAB pulse + soft haptic
import Animated, { useSharedValue, withSequence, withTiming } from 'react-native-reanimated';
// fabScale.value = withSequence(withTiming(1.08, { duration: 130 }), withTiming(1, { duration: 130 }));
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Rewards progress fill — withTiming 600ms ease-out (see RewardsCard)
// Points roll — useEffect interval incrementing a state value over ~600ms

// Order-mode select — border/fill swap + light haptic (instant or 150ms color tween)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Deal tile → detail — expo-router shared transition / Reanimated layout, ~300ms

// Order READY badge flip — scale bounce + success haptic
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Bottom sheet (curbside / customise) — @gorhom/bottom-sheet, 20px top radius
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Bundle the Golden Arches as an SVG via `expo-image` (never recolor it).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` / `home-outline` |
| Rewards | `star` / `star-outline` |
| Order (FAB) | `cart` |
| Menu | `grid` / `grid-outline` |
| More | `person-circle` / `-outline` |
| Notification bell | `notifications-outline` |
| Pickup | `bag-outline` |
| Curbside | `car-outline` |
| Drive Thru | `car-sport-outline` |
| Delivery | `bicycle-outline` |
| Rewards star | `star` |
| Search | `search` |
| Back | `chevron-back` |
| Store / location | `location-outline` |
| Order ready | `checkmark-circle` |
| Promo / coupon | `pricetag` |
| Close | `close` |

## 7. Platform Notes

- **Fonts**: bundle the licensed **Speedee** family; never download at runtime — fall back to `System` if absent
- **Text on yellow**: hard-code `#1A1A1A` on every yellow surface (button label, points on a yellow chip, FAB glyph); white-on-yellow is forbidden by the brand
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on the points balance, prices, and the order timer so they don't reflow while rolling
- **Status bar**: `<StatusBar style="light" />` (dark canvas); deal-detail hero may bleed under it
- **Safe area**: wrap screens in `SafeAreaView`; the Order FAB must clear the home indicator — use `useSafeAreaInsets()` and the negative `marginTop` shown above; sticky CTA bars pad above the tab bar
- **expo-image** for all product imagery (memory-disk cache, `contentFit="cover"`, blurhash placeholders) — food sells the deal
- **Dynamic Type**: `<Text>` respects system scale; set `allowFontScaling={false}` on the rewards number, deal flags, status badges, tab labels, and mode-tile labels (layout-critical)
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkSurface2`; never desaturate food images; keep `#1A1A1A` on yellow regardless of scheme
- **Order FAB**: it's a real route under the center tab; render the elevated circle as the `tabBarIcon` with negative margin so it lifts above the bar; ensure a ≥ 56pt hit area
- **Accessibility**: label the rewards card, deal tiles, FAB ("Order"), and mode tiles (see DESIGN.md §9 / SwiftUI §8); pill CTAs need a 48pt min height
