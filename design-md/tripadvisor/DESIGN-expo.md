# Tripadvisor (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tripadvisor's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:         '#FFFFFF',
  surface:        '#F2F2F2',
  divider:        '#E0E0E0',
  surfacePressed: '#E8E8E8',

  textPrimary:    '#000000',
  textSecondary:  '#6B6B6B',
  textTertiary:   '#9B9B9B',

  green:          '#34E0A1',
  greenPressed:   '#21C589',
  owlBlack:       '#000000',

  emptyBubble:    '#D9D9D9',
  errorRed:       '#D6122E',
} as const;

export type TAColor = keyof typeof colors;
```

## 2. Typography

Load Trip Sans via `expo-font`, or substitute Inter. Fall back to `System` (SF Pro on iOS).

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

const ink = { color: '#000000' } satisfies TextStyle;

export const typography = {
  titleLarge: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  placeHero:  {          fontFamily: 'Inter-Bold',     fontSize: 24, lineHeight: 29, color: '#FFFFFF' },
  section:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  placeName:  { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 21 },
  cardTitle:  { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 21 },
  reviewBody: { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 22 },
  button:     { ...ink, fontFamily: 'Inter-Bold',     fontSize: 16, lineHeight: 20 },
  buttonSec:  { ...ink, fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 19 },
  meta:       {          fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#6B6B6B' },
  tab:        {          fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  badge:      { ...ink, fontFamily: 'Inter-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  caption:    {          fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 16, color: '#6B6B6B' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Five-Circle Bubble Rating

```tsx
// components/BubbleRating.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BubbleRating({
  value, size = 16, reviewCount,
}: { value: number; size?: number; reviewCount?: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
      <View style={{ flexDirection: 'row', gap: 2 }}>
        {[0, 1, 2, 3, 4].map((i) => {
          const fill = Math.min(Math.max(value - i, 0), 1); // 0 | 0.5 | 1
          return (
            <View
              key={i}
              style={{ width: size, height: size, borderRadius: size / 2,
                       backgroundColor: colors.emptyBubble, overflow: 'hidden' }}
            >
              <View
                style={{ width: size * fill, height: size,
                         backgroundColor: colors.green }}
              />
            </View>
          );
        })}
      </View>
      {reviewCount != null && (
        <Text style={typography.meta}>
          {value.toFixed(1)} ({reviewCount.toLocaleString()})
        </Text>
      )}
    </View>
  );
}
```

### Interactive Bubble Picker (Review Write)

```tsx
// components/BubblePicker.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function BubblePicker({
  rating, onChange, size = 32,
}: { rating: number; onChange: (v: number) => void; size?: number }) {
  return (
    <View style={{ flexDirection: 'row', gap: 8 }}>
      {[1, 2, 3, 4, 5].map((i) => {
        const scale = useSharedValue(1);
        const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
        return (
          <Pressable
            key={i}
            onPress={() => {
              Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
              scale.value = withSpring(1.15, {}, () => (scale.value = withSpring(1)));
              onChange(i);
            }}
          >
            <Animated.View
              style={[
                { width: size, height: size, borderRadius: size / 2,
                  backgroundColor: i <= rating ? colors.green : colors.emptyBubble },
                style,
              ]}
            />
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Primary CTA Pill

```tsx
// components/TAPillButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TAPillButton({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'outline'; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: variant === 'filled' ? 14 : 12,
        paddingHorizontal: variant === 'filled' ? 28 : 24,
        borderRadius: 999,
        alignItems: 'center',
        backgroundColor: variant === 'filled'
          ? (pressed ? colors.greenPressed : colors.green)
          : 'transparent',
        borderWidth: variant === 'outline' ? 1 : 0,
        borderColor: colors.owlBlack,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={variant === 'filled' ? typography.button : typography.buttonSec}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Place Card

```tsx
// components/PlaceCard.tsx
import { useState } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { BubbleRating } from './BubbleRating';

export function PlaceCard({
  name, photoUri, rating, reviews, category, priceTier, distance, awarded, onPress,
}: {
  name: string; photoUri: string; rating: number; reviews: number;
  category: string; priceTier: string; distance: string; awarded?: boolean;
  onPress: () => void;
}) {
  const [saved, setSaved] = useState(false);
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.98 : 1 }], gap: 8 })}>
      <View>
        <Image source={{ uri: photoUri }} style={{ width: '100%', aspectRatio: 3 / 2, borderRadius: 16 }} />
        <Pressable
          onPress={() => setSaved((s) => !s)}
          hitSlop={12}
          style={{ position: 'absolute', top: 12, right: 12, width: 36, height: 36,
                   borderRadius: 18, backgroundColor: 'rgba(0,0,0,0.30)',
                   alignItems: 'center', justifyContent: 'center' }}
        >
          <Ionicons name={saved ? 'heart' : 'heart-outline'} size={20}
                    color={saved ? colors.green : '#FFFFFF'} />
        </Pressable>
      </View>

      {awarded && (
        <View style={{ alignSelf: 'flex-start', backgroundColor: colors.green,
                       paddingVertical: 4, paddingHorizontal: 10, borderRadius: 999 }}>
          <Text style={typography.badge}>TRAVELERS' CHOICE</Text>
        </View>
      )}

      <Text style={typography.placeName}>{name}</Text>
      <BubbleRating value={rating} size={16} reviewCount={reviews} />
      <Text style={typography.meta}>{category} · {priceTier} · {distance}</Text>
    </Pressable>
  );
}
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.green,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.92)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Explore', tabBarIcon: ({ color }) => <Ionicons name="compass"        size={24} color={color} /> }} />
      <Tabs.Screen name="search" options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"         size={24} color={color} /> }} />
      <Tabs.Screen name="trips"  options={{ title: 'Trips',   tabBarIcon: ({ color }) => <Ionicons name="briefcase"      size={24} color={color} /> }} />
      <Tabs.Screen name="review" options={{ title: 'Review',  tabBarIcon: ({ color }) => <Ionicons name="create"         size={24} color={color} /> }} />
      <Tabs.Screen name="more"   options={{ title: 'More',    tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Save-heart bounce
const scale = useSharedValue(1);
const onSave = () => {
  scale.value = withSequence(withSpring(1.2), withSpring(1));
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
};

// Bubble selection — light impact per tap (see BubblePicker)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Hero parallax — drive an Animated.ScrollView onScroll, apply translateY 0.5× + scale to the hero Image
const scrollY = useSharedValue(0);
const heroStyle = useAnimatedStyle(() => ({
  transform: [
    { translateY: scrollY.value * 0.5 },
    { scale: scrollY.value < 0 ? 1 - scrollY.value / 400 : 1 },
  ],
}));
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) mapped to Tripadvisor's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Search | `search` |
| Save heart | `heart-outline` / `heart` |
| Share | `share-outline` |
| Back | `chevron-back` |
| Filters | `options-outline` |
| Location pin | `location-outline` |
| Directions | `navigate-outline` |
| Helpful vote | `thumbs-up-outline` / `thumbs-up` |
| Explore (tab) | `compass-outline` / `compass` |
| Trips (tab) | `briefcase-outline` / `briefcase` |
| Review (tab) | `create-outline` / `create` |
| More (tab) | `ellipsis-horizontal` |

## 7. Platform Notes

- **iOS-only feel**: use `expo-blur` (`tint="light"`) for the tab bar `.regularMaterial` equivalent; Android falls back to an opaque white background
- **Status bar**: set `<StatusBar style="dark" />` from `expo-status-bar` — the white canvas requires dark content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the detail hero bleeds full-width but the sticky bar respects the inset
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` on the bubble rating (a fixed geometric unit), tab labels, and the photo-count pill
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on the save heart; expose the bubble rating as a single combined label, and the review picker as `accessibilityRole="adjustable"`
- **Performance**: the place card photo is a large 3:2 image — use `expo-image` with `recyclingKey` in long lists for memory efficiency
