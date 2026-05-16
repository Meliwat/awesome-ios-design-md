# eBay (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates eBay's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:         '#FFFFFF',
  surface:        '#F7F7F7',
  divider:        '#E5E5E5',
  surfacePressed: '#EFEFEF',

  textPrimary:    '#191919',
  textSecondary:  '#707070',
  textTertiary:   '#9B9B9B',

  // Four-color brand
  blue:           '#0064D2',
  bluePressed:    '#0050A8',
  red:            '#E53238',
  yellow:         '#F5AF02',
  green:          '#86B817',

  freeShip:       '#258635',
  errorRed:       '#C7000B',
} as const;

export type EbayColor = keyof typeof colors;
```

## 2. Typography

Load Market Sans via `expo-font`, or substitute Inter. Fall back to `System` (SF Pro on iOS).

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

const ink = { color: '#191919' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  titleLarge: { ...ink, fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  listing:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 29, letterSpacing: -0.2 },
  section:    { ...ink, fontFamily: 'Inter-Bold',     fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  price:      { ...ink, fontFamily: 'Inter-Bold',     fontSize: 24, lineHeight: 26, letterSpacing: -0.3 },
  cardPrice:  { ...ink, fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 20, letterSpacing: -0.1 },
  cardTitle:  { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 20 },
  body:       { ...ink, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 23 },
  button:     { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20 },
  badge:      {          fontFamily: 'Inter-Bold',     fontSize: 12, lineHeight: 14, letterSpacing: 0.2, textTransform: 'uppercase' as const },
  meta:       {          fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 17, color: '#707070' },
  timeLeft:   { ...tnum, fontFamily: 'Inter-Bold',     fontSize: 13, lineHeight: 16 },
  tab:        {          fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  condition:  {          fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.3, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Bid vs Buy-It-Now Badge (the signature)

```tsx
// components/BuyMechanicBadge.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Mechanic = { kind: 'buyItNow' } | { kind: 'auction'; bids: number };

export function BuyMechanicBadge({
  mechanic, bestOffer,
}: { mechanic: Mechanic; bestOffer?: boolean }) {
  const isBin = mechanic.kind === 'buyItNow';
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
      <View style={{
        paddingVertical: 4, paddingHorizontal: 8, borderRadius: 4,
        backgroundColor: isBin ? 'rgba(0,100,210,0.10)' : colors.surface,
      }}>
        <Text style={[typography.badge, { color: isBin ? colors.blue : colors.textPrimary }]}>
          {isBin ? 'Buy It Now' : `${mechanic.bids} bid${mechanic.bids === 1 ? '' : 's'}`}
        </Text>
      </View>
      {bestOffer && (
        <Text style={[typography.meta, { color: colors.green }]}>or Best Offer</Text>
      )}
    </View>
  );
}
```

### "Time Left" Countdown

```tsx
// components/TimeLeft.tsx
import { Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TimeLeft({ secondsRemaining }: { secondsRemaining: number }) {
  const urgent = secondsRemaining < 86_400; // < 24h
  const d = Math.floor(secondsRemaining / 86_400);
  const h = Math.floor((secondsRemaining % 86_400) / 3_600);
  const m = Math.floor((secondsRemaining % 3_600) / 60);
  const s = secondsRemaining % 60;
  const label = d > 0 ? `${d}d ${h}h left` : h > 0 ? `${h}h ${m}m left` : `${m}m ${s}s left`;
  return (
    <Text style={[typography.timeLeft, { color: urgent ? colors.red : colors.textSecondary }]}>
      {label}
    </Text>
  );
}
```

### Watch Heart

```tsx
// components/WatchHeart.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function WatchHeart({
  watching, onToggle, overPhoto = true, size = 24,
}: { watching: boolean; onToggle: () => void; overPhoto?: boolean; size?: number }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const press = () => {
    scale.value = withSequence(withSpring(1.2, { damping: 6 }), withSpring(1, { damping: 10 }));
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    onToggle();
  };
  return (
    <Pressable onPress={press} hitSlop={12}>
      <Animated.View
        style={[
          overPhoto && {
            width: 36, height: 36, borderRadius: 18, backgroundColor: 'rgba(0,0,0,0.40)',
            alignItems: 'center', justifyContent: 'center',
          },
          style,
        ]}
      >
        <Ionicons
          name={watching ? 'heart' : 'heart-outline'}
          size={size}
          color={watching ? colors.red : overPhoto ? '#FFFFFF' : colors.textPrimary}
        />
      </Animated.View>
    </Pressable>
  );
}
```

### Listing Card

```tsx
// components/ListingCard.tsx
import { useState } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { BuyMechanicBadge } from './BuyMechanicBadge';
import { TimeLeft } from './TimeLeft';
import { WatchHeart } from './WatchHeart';

export function ListingCard({
  title, photoUri, price, original, mechanic, bestOffer, freeShipping, secondsLeft, onPress,
}: any) {
  const [watching, setWatching] = useState(false);
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: colors.canvas, borderRadius: 8, padding: 8, gap: 6,
        transform: [{ scale: pressed ? 0.98 : 1 }],
        shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 4, shadowOffset: { width: 0, height: 1 },
      })}
    >
      <View>
        <Image source={{ uri: photoUri }} style={{ width: '100%', aspectRatio: 1, borderRadius: 8 }} />
        <View style={{ position: 'absolute', top: 8, right: 8 }}>
          <WatchHeart watching={watching} onToggle={() => setWatching((w) => !w)} />
        </View>
      </View>
      <Text style={typography.cardTitle} numberOfLines={2}>{title}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 6 }}>
        <Text style={typography.cardPrice}>{price}</Text>
        {original && (
          <Text style={[typography.meta, { textDecorationLine: 'line-through', color: colors.textTertiary }]}>
            {original}
          </Text>
        )}
      </View>
      <BuyMechanicBadge mechanic={mechanic} bestOffer={bestOffer} />
      <View style={{ flexDirection: 'row', gap: 8 }}>
        {freeShipping && <Text style={[typography.meta, { color: colors.freeShip }]}>Free shipping</Text>}
        {secondsLeft != null && <TimeLeft secondsRemaining={secondsLeft} />}
      </View>
    </Pressable>
  );
}
```

### Primary / Bid Buttons

```tsx
// components/EbayButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function EbayButton({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'bidOutline'; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        paddingVertical: variant === 'filled' ? 15 : 13,
        paddingHorizontal: 28,
        borderRadius: 999,
        alignItems: 'center',
        backgroundColor: variant === 'filled'
          ? (pressed ? colors.bluePressed : colors.blue)
          : 'transparent',
        borderWidth: variant === 'bidOutline' ? 1.5 : 0,
        borderColor: colors.blue,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, variant === 'bidOutline' && { color: colors.blue }]}>
        {title}
      </Text>
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
        tabBarActiveTintColor: colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.97)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',          tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="saved"    options={{ title: 'Saved',         tabBarIcon: ({ color }) => <Ionicons name="heart"         size={24} color={color} /> }} />
      <Tabs.Screen name="notifs"   options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={24} color={color} /> }} />
      <Tabs.Screen name="myebay"   options={{ title: 'My eBay',       tabBarIcon: ({ color }) => <Ionicons name="person"        size={24} color={color} /> }} />
      <Tabs.Screen name="selling"  options={{ title: 'Selling',       tabBarIcon: ({ color }) => <Ionicons name="pricetag"      size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Watch-heart toggle — see WatchHeart (withSequence spring 1.2 → 1, success haptic)

// Bid placement — present a confirm modal route; on success tick the bid number + increment count
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// "Time left" tick — drive a 1s interval; fontVariant tabular-nums prevents digit reflow
useEffect(() => {
  const id = setInterval(() => setSeconds((s) => Math.max(0, s - 1)), 1000);
  return () => clearInterval(id);
}, []);

// Card tap — scale 0.98 (Pressable style)

// Photo carousel — light selection haptic per page
Haptics.selectionAsync();
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) mapped to eBay's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Watch | `heart-outline` / `heart` |
| Search | `search` |
| Visual search | `camera-outline` |
| Star (feedback) | `star` |
| Share | `share-outline` |
| Back | `chevron-back` |
| Cart | `cart-outline` / `cart` |
| Filters | `options-outline` |
| Home (tab) | `home-outline` / `home` |
| Saved (tab) | `heart-outline` / `heart` |
| Notifications (tab) | `notifications-outline` / `notifications` |
| My eBay (tab) | `person-outline` / `person` |
| Selling (tab) | `pricetag-outline` / `pricetag` |

## 7. Platform Notes

- **iOS-only feel**: use `expo-blur` (`tint="light"`) for the tab bar `.regularMaterial` equivalent; Android falls back to an opaque white background
- **Status bar**: set `<StatusBar style="dark" />` from `expo-status-bar` — the white canvas requires dark content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the listing photo carousel bleeds full-width while the sticky bar respects the inset
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on the "Time left" countdown so the seconds don't reflow; pin it with `allowFontScaling={false}`
- **Dynamic Type**: React Native respects user font-scaling by default; also set `allowFontScaling={false}` on buy-mechanic badges, condition tags, and tab labels
- **Accessibility**: never rely on color alone for the buy mechanic — give the badge an `accessibilityLabel` (`"Buy It Now"` / `"Auction, 12 bids"`); expose the watch-heart as `accessibilityRole="button"` with a toggle label; read "Time left" as a full phrase
- **Brand lock**: keep the four eBay hues exact; only swap surrounding neutrals for dark mode
- **Performance**: listing photos are 1:1 images in a dense 2-column grid — use `expo-image` with `recyclingKey` for memory efficiency at browse scale
