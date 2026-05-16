# Lyft (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Lyft's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets — including the full-screen map + rounded bottom sheet model and the ride-type selector.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, and `@gorhom/bottom-sheet` v4 (or a custom Reanimated sheet).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Light
  canvas:      '#FFFFFF',
  surface:     '#F4F4F7',
  surfaceDeep: '#E9E9EF',
  divider:     '#E5E5EA',
  textPrimary:   '#11111F',
  textSecondary: '#6B6B7B',
  textTertiary:  '#9A9AA8',

  // Dark
  canvasDark:      '#11111F',
  surfaceDark:     '#1C1C2B',
  surfaceDeepDark: '#26263A',
  dividerDark:     '#2C2C40',
  textPrimaryDark: '#FFFFFF',
  textSecondaryDark: '#A0A0B4',

  // Brand
  pink:        '#FF00BF',
  pinkPressed: '#D500A0',
  pinkTint:    '#FFE5F7',
  pinkTintDark:'#33102B',

  // Semantic
  success: '#00A862',
  gold:    '#FFB400',
  error:   '#E5484D',
} as const;

export type LyftColor = keyof typeof colors;
```

## 2. Typography

Lyft uses Inter with a friendly, rounded character. Load via `expo-font`; fall back to `System` (`SF Pro Rounded` on iOS via `fontFamily: undefined` + rounded weight where possible).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#11111F' } satisfies TextStyle;
const tnum = ['tabular-nums'] as const;

export const typography = {
  sheetTitle: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  whereTo:    { fontFamily: 'Inter-SemiBold', fontSize: 20, lineHeight: 25, letterSpacing: -0.2, color: '#6B6B7B' },
  rideName:   { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 21, letterSpacing: -0.1 },
  price:      { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 20, fontVariant: tnum },
  body:       { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 22 },
  button:     { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 17, lineHeight: 20 },
  driverName: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 18, lineHeight: 22, letterSpacing: -0.1 },
  meta:       { fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 18, color: '#6B6B7B' },
  rideSub:    { fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 17, color: '#6B6B7B' },
  badge:      { fontFamily: 'Inter-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.3 },
  caption:    { fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 15, color: '#6B6B7B' },
  pinLabel:   { fontFamily: 'Inter-Bold',    fontSize: 13, lineHeight: 15 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Full-Screen Map + Rounded Bottom Sheet

```tsx
// app/index.tsx
import MapView from 'react-native-maps';
import BottomSheet from '@gorhom/bottom-sheet';
import { useMemo, useRef } from 'react';
import { View } from 'react-native';
import { colors } from '../theme/colors';
import { ChooseRideSheet } from '../components/ChooseRideSheet';
import { MapIconButton } from '../components/MapIconButton';

export default function RideHome() {
  const sheetRef = useRef<BottomSheet>(null);
  const snapPoints = useMemo(() => [140, '45%', '90%'], []);

  return (
    <View style={{ flex: 1 }}>
      <MapView style={{ ...StyleSheet.absoluteFillObject }} showsUserLocation />
      <MapIconButton system="menu" style={{ position: 'absolute', top: 56, left: 16 }} onPress={() => {}} />
      <BottomSheet
        ref={sheetRef}
        index={1}
        snapPoints={snapPoints}
        handleIndicatorStyle={{ backgroundColor: colors.divider, width: 36, height: 4 }}
        backgroundStyle={{ backgroundColor: colors.canvas, borderRadius: 28 }}
        // @gorhom/bottom-sheet already springs + snaps between snap points
        animationConfigs={{ damping: 18, stiffness: 220 }}>
        <ChooseRideSheet />
      </BottomSheet>
    </View>
  );
}
```

> `@gorhom/bottom-sheet` snaps with a spring out of the box. For a custom sheet, drive a shared `translateY` with `withSpring(target, { damping: 14, stiffness: 180 })` for the signature slight overshoot.

### Ride-Type Selector Row

```tsx
// components/RideTypeRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withSpring } from 'react-native-reanimated';
import { useEffect } from 'react';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RideTypeRow({
  iconUri, name, subtitle, capacityEta, price, selected, onPress,
}: {
  iconUri: string; name: string; subtitle: string;
  capacityEta: string; price: string; selected: boolean; onPress: () => void;
}) {
  const k = useSharedValue(selected ? 1 : 0);
  useEffect(() => { k.value = withSpring(selected ? 1 : 0, { damping: 14 }); }, [selected]);
  const ring = useAnimatedStyle(() => ({
    borderColor: selected ? colors.pink : 'transparent',
    backgroundColor: selected ? colors.pinkTint : 'transparent',
  }));

  return (
    <Pressable
      onPress={() => { Haptics.selectionAsync(); onPress(); }}>
      <Animated.View style={[{
        flexDirection: 'row', alignItems: 'center', gap: 12,
        paddingHorizontal: 16, paddingVertical: 14,
        borderRadius: 16, borderWidth: 2,
      }, ring]}>
        <Image source={{ uri: iconUri }} style={{ width: 56, height: 40 }} resizeMode="contain" />
        <View style={{ flex: 1 }}>
          <Text style={typography.rideName}>{name}</Text>
          <Text style={typography.rideSub}>{subtitle}</Text>
          <Text style={typography.meta}>{capacityEta}</Text>
        </View>
        <Text style={typography.price}>{price}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Primary CTA

```tsx
// components/LyftCTA.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LyftCTA({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 56, borderRadius: 16, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.pinkPressed : colors.pink,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Pickup Pin (with drop)

```tsx
// components/PickupPin.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function PickupPin() {
  const drop = useSharedValue(0); // 0 = up/small, 1 = landed
  useEffect(() => {
    drop.value = withSpring(1, { damping: 7, stiffness: 160 });
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
  }, []);
  const pin = useAnimatedStyle(() => ({
    transform: [{ scale: 0.6 + 0.4 * drop.value }, { translateY: -40 * (1 - drop.value) }],
  }));
  return (
    <Animated.View style={pin}>
      <View style={{ width: 28, height: 28, borderRadius: 14, backgroundColor: colors.pink,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: colors.textPrimary, shadowOpacity: 0.3, shadowRadius: 8,
        shadowOffset: { width: 0, height: 6 } }}>
        <View style={{ width: 10, height: 10, borderRadius: 5, backgroundColor: '#fff' }} />
      </View>
      <View style={{ width: 2, height: 10, backgroundColor: colors.pink, alignSelf: 'center' }} />
    </Animated.View>
  );
}
```

### "Where to?" Search Bar

```tsx
// components/WhereToBar.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WhereToBar() {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      height: 56, borderRadius: 16, paddingHorizontal: 18, marginHorizontal: 20,
      backgroundColor: colors.canvas,
      shadowColor: colors.textPrimary, shadowOpacity: 0.16, shadowRadius: 12,
      shadowOffset: { width: 0, height: 4 },
    }}>
      <View style={{ width: 10, height: 10, borderRadius: 3, backgroundColor: colors.pink }} />
      <Text style={typography.whereTo}>Where to?</Text>
    </View>
  );
}
```

## 4. Sheet State Progression

```ts
type TripState = 'idle' | 'chooseRide' | 'confirmPickup' | 'enRoute' | 'rate';
// Map each state to a snap index + sheet content:
// idle → snap 0 (140) "Where to?"; chooseRide → snap 1 (45%) ride list;
// confirmPickup → snap to ~35% address + Confirm; enRoute → ~40% driver card;
// rate → ~50% rating + tip. Call sheetRef.current?.snapToIndex(...) on state change.
```

## 5. Navigation (No Tab Bar)

Lyft has **no `Tabs`**. `app/index.tsx` is the full-screen map + sheet; trip phases are state-driven. The floating menu button opens account/history via a modal route (`expo-router` `presentation: 'modal'`), not a tab.

```tsx
// components/MapIconButton.tsx
import { Pressable, View, ViewStyle } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

const MAP = { menu: 'menu', back: 'chevron-back', locate: 'locate', layers: 'layers' } as const;

export function MapIconButton({
  system, style, onPress,
}: { system: keyof typeof MAP; style?: ViewStyle; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={[{ width: 44, height: 44, borderRadius: 22,
      backgroundColor: colors.canvas, alignItems: 'center', justifyContent: 'center',
      shadowColor: colors.textPrimary, shadowOpacity: 0.16, shadowRadius: 12,
      shadowOffset: { width: 0, height: 4 } }, style]}>
      <Ionicons name={MAP[system]} size={20} color={colors.textPrimary} />
    </Pressable>
  );
}
```

## 6. Motion

```tsx
// Sheet snap (custom): translateY.value = withSpring(target, { damping: 14, stiffness: 180 }) // slight overshoot

// Ride-type select
Haptics.selectionAsync();
k.value = withSpring(selected ? 1 : 0, { damping: 14 });

// Pickup pin drop
drop.value = withSpring(1, { damping: 7, stiffness: 160 });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// CTA press: transform scale 0.98 via Pressable pressed state

// Map recenter: animate the camera with mapRef.animateCamera({ ... }, { duration: 400 })
// ETA bubble: cross-fade the number; tabular figures keep layout stable
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Lyft's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Menu (map) | `menu` |
| Back / dismiss | `chevron-back` |
| Recenter / locate | `locate` |
| Layers | `layers-outline` |
| Search | `search` |
| Saved: Home | `home` |
| Saved: Work | `briefcase` |
| Rating star | `star` |
| Contact driver | `call` / `chatbubble` |
| Share trip | `share-outline` |
| Safety | `shield-checkmark` |
| Where-to dot | (drawn pink square) |

## 8. Platform Notes

- **Map-first, no tabs**: `app/index.tsx` is a full-bleed `MapView`; all controls float or live in the sheet — never add an `expo-router` `Tabs` layout
- **Status bar**: `<StatusBar style="auto" />` so it adapts — light content over the map in dark mode, dark over light map
- **Safe area**: the map renders edge-to-edge; inset floating controls with `useSafeAreaInsets()`; the sheet's content respects the bottom inset
- **Dark mode**: read `useColorScheme()` and swap the canvas/surface/text tokens; keep `colors.pink` identical on both themes
- **Tabular figures**: pass `fontVariant: ['tabular-nums']` on prices, ETAs, and fare breakdowns so the right-aligned price column aligns
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` and swap sheet/pin springs for quick timing animations
- **Accessibility**: ride-type rows get `accessibilityRole="radio"` + `accessibilityState={{ selected }}`; the CTA announces "Select Lyft"; expose a map summary via `accessibilityLabel`
- **Sheet library**: prefer `@gorhom/bottom-sheet` for correct gesture + detent physics; ensure `backgroundStyle.borderRadius` is 28 for the very-rounded top corners
