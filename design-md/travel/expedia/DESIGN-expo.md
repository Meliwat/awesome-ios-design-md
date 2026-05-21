# Expedia (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Expedia's visual language into paste-ready Expo / React Native: a design-token module, themed components (property card, mode switch, One Key strip), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F5F7FA',
  surfacePressed: '#ECEFF4',
  divider:        '#E3E7ED',

  // Surfaces (dark)
  darkCanvas:     '#0E1116',
  darkSurface1:   '#161B22',
  darkSurface2:   '#1F2630',
  darkDivider:    '#2A323E',

  // Text
  textPrimary:    '#1A1F26',
  textSecondary:  '#5A6573',
  textTertiary:   '#8A95A3',
  darkTextPrimary:   '#E8EBEF',
  darkTextSecondary: '#9AA4B2',

  // Brand & interactive
  yellow:        '#FFC94D',
  yellowDeep:    '#FFB31A',
  actionBlue:    '#1668E3',
  actionPressed: '#0F4FB0',
  navy:          '#00355F',
  navySoft:      '#14416B',
  oneKeyGold:    '#F5C518',

  // Semantic
  success: '#1A8B4B',
  error:   '#D93A3A',
  warning: '#E8830C',
} as const;

export type ExpediaColor = keyof typeof colors;

// Review-score badge color for a 0–10 guest score
export function reviewBadgeColor(score: number): string {
  if (score >= 9.0) return colors.success;     // Wonderful
  if (score >= 8.0) return colors.actionBlue;  // Excellent
  if (score >= 7.0) return colors.navySoft;    // Good
  if (score >= 6.0) return colors.textSecondary;
  return colors.textTertiary;
}
```

## 2. Typography

Expedia Sans is proprietary. Bundle it via `expo-font` only if licensed; otherwise fall back to system / Inter (closest free substitute). Prices use `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // If licensed:
    // 'ExpediaSans-Regular':  require('../assets/fonts/ExpediaSans-Regular.ttf'),
    // 'ExpediaSans-SemiBold': require('../assets/fonts/ExpediaSans-SemiBold.ttf'),
    // 'ExpediaSans-Bold':     require('../assets/fonts/ExpediaSans-Bold.ttf'),
    // 'ExpediaSans-Heavy':    require('../assets/fonts/ExpediaSans-Heavy.ttf'),
    // Free substitute fallback:
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const F = {
  reg:   'Inter-Regular',
  semi:  'Inter-SemiBold',
  bold:  'Inter-Bold',
  heavy: 'Inter-ExtraBold',
};
const primary = { color: '#1A1F26' } satisfies TextStyle;
const tab = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  display:      { ...primary, fontFamily: F.heavy, fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  screenTitle:  { ...primary, fontFamily: F.heavy, fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:      { ...primary, fontFamily: F.bold,  fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:    { ...primary, fontFamily: F.bold,  fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  body:         { ...primary, fontFamily: F.reg,   fontSize: 16, lineHeight: 24 },
  cardSubtitle: { color: '#5A6573', fontFamily: F.semi, fontSize: 15, lineHeight: 20 },
  meta:         { color: '#5A6573', fontFamily: F.reg,  fontSize: 14, lineHeight: 19 },
  strike:       { color: '#8A95A3', fontFamily: F.reg,  fontSize: 13, lineHeight: 16, textDecorationLine: 'line-through' as const, ...tab },
  badge:        { fontFamily: F.semi, fontSize: 12, lineHeight: 13, letterSpacing: 0.2 },
  oneKeyLine:   { color: '#F5C518', fontFamily: F.bold, fontSize: 11, lineHeight: 14, letterSpacing: 0.1 },
  button:       { fontFamily: F.bold, fontSize: 16, lineHeight: 16 },
  tab:          { fontFamily: F.semi, fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  priceNow:     { ...primary, fontFamily: F.heavy, fontSize: 18, lineHeight: 22, ...tab },
  scoreNum:     { color: '#FFFFFF', fontFamily: F.heavy, fontSize: 15, lineHeight: 16, ...tab },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Property Result Card

```tsx
// components/PropertyCard.tsx
import { useState } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors, reviewBadgeColor } from '../theme/colors';
import { typography } from '../theme/typography';

const AHeart = Animated.createAnimatedComponent(Pressable);

export function PropertyCard({
  image, dealFlag, title, location, score, scoreWord, reviewCount,
  strikePrice, nightlyPrice, oneKeyEarn,
}: {
  image: string; dealFlag?: string; title: string; location: string;
  score: number; scoreWord: string; reviewCount: number;
  strikePrice?: number; nightlyPrice: number; oneKeyEarn: number;
}) {
  const [saved, setSaved] = useState(false);
  const s = useSharedValue(1);
  const heartStyle = useAnimatedStyle(() => ({ transform: [{ scale: s.value }] }));

  const toggleSave = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    setSaved((v) => !v);
    s.value = withSequence(withTiming(1.2, { duration: 110 }), withTiming(1, { duration: 110 }));
  };

  return (
    <View style={{
      backgroundColor: colors.canvas, borderRadius: 16,
      borderWidth: 0.5, borderColor: colors.divider, overflow: 'hidden',
      shadowColor: '#000', shadowOpacity: 0.08, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
      elevation: 3, marginBottom: 16,
    }}>
      <View style={{ aspectRatio: 16 / 10, width: '100%' }}>
        <Image source={{ uri: image }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        {dealFlag ? (
          <View style={{
            position: 'absolute', top: 12, left: 12,
            backgroundColor: colors.yellow, borderRadius: 6,
            paddingVertical: 4, paddingHorizontal: 9,
          }}>
            <Text style={[typography.badge, { color: colors.navy }]}>{dealFlag}</Text>
          </View>
        ) : null}
        <AHeart
          onPress={toggleSave}
          style={[{
            position: 'absolute', top: 10, right: 10,
            width: 30, height: 30, borderRadius: 15,
            alignItems: 'center', justifyContent: 'center',
            backgroundColor: 'rgba(0,0,0,0.4)',
          }, heartStyle]}
        >
          <Ionicons name={saved ? 'heart' : 'heart-outline'} size={18} color={saved ? colors.actionBlue : '#FFFFFF'} />
        </AHeart>
      </View>

      <View style={{ paddingHorizontal: 14, paddingTop: 12, paddingBottom: 14 }}>
        <Text style={typography.cardTitle}>{title}</Text>
        <Text style={[typography.cardSubtitle, { marginTop: 3 }]}>{location}</Text>

        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 8 }}>
          <View style={{
            backgroundColor: reviewBadgeColor(score), borderRadius: 6,
            paddingVertical: 3, paddingHorizontal: 7,
          }}>
            <Text style={typography.scoreNum}>{score.toFixed(1)}</Text>
          </View>
          <Text style={[typography.badge, { fontFamily: 'Inter-Bold', color: colors.textPrimary }]}>{scoreWord}</Text>
          <Text style={[typography.badge, { color: colors.textSecondary }]}>· {reviewCount.toLocaleString()} reviews</Text>
        </View>

        <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 6, marginTop: 10 }}>
          {strikePrice ? <Text style={typography.strike}>${strikePrice}</Text> : null}
          <Text style={typography.priceNow}>${nightlyPrice}</Text>
          <Text style={[typography.badge, { color: colors.textSecondary }]}>/ night</Text>
        </View>

        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, marginTop: 7 }}>
          <View style={{ width: 8, height: 8, borderRadius: 4, backgroundColor: colors.oneKeyGold }} />
          <Text style={typography.oneKeyLine}>Earn {oneKeyEarn.toLocaleString()} One Key cash</Text>
        </View>
      </View>
    </View>
  );
}
```

### Segmented Mode Switch

```tsx
// components/ModeSwitch.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const MODES = ['Stays', 'Flights', 'Cars', 'Bundle'];

export function ModeSwitch({ value, onChange }: { value: number; onChange: (i: number) => void }) {
  return (
    <View style={{ flexDirection: 'row', gap: 8, paddingHorizontal: 16 }}>
      {MODES.map((m, i) => {
        const active = value === i;
        return (
          <Pressable
            key={m}
            onPress={() => onChange(i)}
            style={{
              flex: 1, alignItems: 'center', paddingVertical: 9, borderRadius: 10,
              backgroundColor: active ? colors.yellow : colors.surfaceGray,
              borderWidth: active ? 0 : 0.5, borderColor: colors.divider,
            }}
          >
            <Text style={[typography.badge, { color: active ? colors.navy : colors.textSecondary }]}>{m}</Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

### One Key Rewards Strip

```tsx
// components/OneKeyStrip.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';

export function OneKeyStrip({ balance }: { balance: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      backgroundColor: colors.surfaceGray, borderRadius: 12,
      borderWidth: 0.5, borderColor: colors.divider,
      paddingVertical: 14, paddingHorizontal: 16,
    }}>
      <View style={{
        width: 36, height: 36, borderRadius: 18,
        backgroundColor: colors.oneKeyGold, alignItems: 'center', justifyContent: 'center',
      }}>
        <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 13, color: '#1B1B1B' }}>1K</Text>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: colors.textPrimary }}>One Key cash available</Text>
        <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.textSecondary, marginTop: 2 }}>
          Apply at checkout on any stay, flight, or car
        </Text>
      </View>
      <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 16, color: colors.oneKeyGold }}>{balance}</Text>
    </View>
  );
}
```

### Search Pill

```tsx
// components/SearchPill.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function SearchPill({ destination, detail, onPress }: { destination: string; detail: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 12,
        height: 52, paddingHorizontal: 16, marginHorizontal: 16,
        backgroundColor: colors.canvas, borderRadius: 14,
        borderWidth: 0.5, borderColor: colors.divider,
        shadowColor: '#000', shadowOpacity: 0.08, shadowRadius: 12, shadowOffset: { width: 0, height: 4 }, elevation: 3,
      }}
    >
      <Ionicons name="search" size={17} color={colors.textSecondary} />
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 14, color: colors.textPrimary }}>{destination}</Text>
        <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.textSecondary, marginTop: 2 }}>{detail}</Text>
      </View>
    </Pressable>
  );
}
```

### Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export const ReserveButton = ({ title, onPress }: { title: string; onPress: () => void }) => (
  <Pressable
    onPress={onPress}
    style={({ pressed }) => ({
      backgroundColor: pressed ? colors.actionPressed : colors.actionBlue,
      borderRadius: 8, paddingVertical: 14, alignItems: 'center',
      transform: [{ scale: pressed ? 0.98 : 1 }],
    })}
  >
    <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
  </Pressable>
);

export const BundleButton = ({ title, onPress }: { title: string; onPress: () => void }) => (
  <Pressable
    onPress={onPress}
    style={({ pressed }) => ({
      backgroundColor: pressed ? colors.yellowDeep : colors.yellow,
      borderRadius: 8, paddingVertical: 14, alignItems: 'center',
      transform: [{ scale: pressed ? 0.98 : 1 }],
    })}
  >
    <Text style={[typography.button, { color: colors.navy }]}>{title}</Text>
  </Pressable>
);
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.actionBlue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"               size={22} color={color} /> }} />
      <Tabs.Screen name="saved"   options={{ title: 'Saved',   tabBarIcon: ({ color }) => <Ionicons name="heart-outline"        size={22} color={color} /> }} />
      <Tabs.Screen name="trips"   options={{ title: 'Trips',   tabBarIcon: ({ color }) => <Ionicons name="briefcase-outline"    size={22} color={color} /> }} />
      <Tabs.Screen name="support" options={{ title: 'Support', tabBarIcon: ({ color }) => <Ionicons name="help-circle-outline"  size={22} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Save heart — scale bounce + soft haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
s.value = withSequence(withTiming(1.2, { duration: 110 }), withTiming(1, { duration: 110 }));

// Mode switch — color is immediate; wrap in LayoutAnimation for a soft crossfade if desired
// import { LayoutAnimation } from 'react-native'; LayoutAnimation.easeInEaseOut();

// Price drop — yellow flash
priceColor.value = withSequence(
  withTiming(1, { duration: 180 }),  // 0→1 maps interpolated color to yellow
  withDelay(320, withTiming(0, { duration: 180 })),
);

// Bundle reveal — savings ribbon
entering={FadeIn.duration(240)} // + scale 0.96 → 1 via withTiming on a shared value

// Card → detail — use a shared element lib (react-native-shared-element) or
// expo-router's animated transitions; ~320ms ease-out push

// Haptics: Soft impact on save, mode switch, date-range commit; Success on booking confirm
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Expedia's iconography is a clean travel set.

| Purpose | Ionicons |
|---------|----------|
| Search (tab) | `search` |
| Saved (tab) | `heart-outline` / `heart` |
| Trips (tab) | `briefcase-outline` |
| Support (tab) | `help-circle-outline` |
| Account (tab) | `person-circle-outline` |
| Save heart (card) | `heart-outline` / `heart` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Filter | `options-outline` |
| Sort | `swap-vertical` |
| Flight | `airplane` |
| Stay | `bed-outline` |
| Car | `car-outline` |
| Bundle | `cube-outline` |
| Calendar | `calendar-outline` |
| Travelers | `people-outline` |
| Map | `map-outline` |
| One Key | `key` |
| Price trend up | `trending-up` |
| Free cancellation | `shield-checkmark-outline` |

## 7. Platform Notes

- **Font**: Expedia Sans is proprietary — bundle only if licensed; otherwise ship Inter as the parity substitute and let SF Pro/Roboto fall back.
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every price and review-score `Text` so vertical lists align.
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark; transparent over photo heroes.
- **Safe area**: wrap screens in `SafeAreaView`; sticky CTA bar and bottom tab need safe-area padding; map result carousel floats above the bottom inset.
- **Dynamic Type**: `<Text>` respects system scale; set `allowFontScaling={false}` on tab labels, deal flags/badges, One Key lines, and the review-score number.
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1` / `darkDivider`; keep brand yellow/blue/gold fixed; add a 0.5px `darkDivider` border to floating sheets.
- **Images**: use `expo-image` for property photos (better caching/placeholder); keep 16:10 aspect ratio.
- **Accessibility**: give the card an `accessibilityLabel` summarizing title/score/price; the save heart its own `accessibilityRole="button"` with state; the One Key line is informative text.
- **Maps**: use `react-native-maps`; render price pins as custom markers (pill, 12px radius, selected = `#1668E3` bg / white text).
