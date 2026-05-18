# Grubhub (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grubhub's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the restaurant list and live order tracking.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (interactive)
  ghRed:          '#F63440',
  ghRedPressed:   '#D2202B',
  ghOrange:       '#FF8000',
  ghOrangePressed:'#E06E00',

  // Membership & status
  perksGold:      '#FFB81C',
  trackBlue:      '#00A0DF',
  ratingGreen:    '#18A957',

  // Surfaces (light)
  canvas:         '#FFFFFF',
  surface1:       '#F6F6F4',
  surface2:       '#ECECEA',
  divider:        '#E6E6E6',

  // Surfaces (dark)
  darkCanvas:     '#121212',
  darkSurface1:   '#1C1C1E',
  darkSurface2:   '#262626',
  darkDivider:    '#2C2C2E',

  // Text
  textPrimary:    '#1B1B1B',
  textSecondary:  '#707070',
  textTertiary:   '#A0A0A0',
  darkTextPrimary:'#F2F2F2',

  // Semantic
  error:          '#F0454F',
  success:        '#18A957',

  // Tracking banner gradient (dark)
  trackBgFrom:    '#11363F',
  trackBgTo:      '#0E2A33',
  perksInkText:   '#1A1206', // near-black on gold/orange
} as const;

export type GrubhubColor = keyof typeof colors;
```

## 2. Typography

Grubhub's brand face is **Graphik**; the recommended free substitute is **Inter**. Load Inter via `expo-font`. Prices/ETAs use tabular figures via `fontVariant`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
    'Inter-Black':     require('../assets/fonts/Inter-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1B1B1B' } satisfies TextStyle;
const tabular = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle:    { ...primary, fontFamily: 'Inter-Black',     fontSize: 32, lineHeight: 37, letterSpacing: -0.5 },
  restaurantHero: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:        { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:      { ...primary, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 23 },
  restaurantName: { ...primary, fontFamily: 'Inter-Bold',      fontSize: 16, lineHeight: 21 },
  body:           { ...primary, fontFamily: 'Inter-Medium',    fontSize: 15, lineHeight: 22 },
  price:          { ...primary, fontFamily: 'Inter-Bold',      fontSize: 15, lineHeight: 20, ...tabular },
  meta:           { color: '#707070', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },
  deal:           { color: '#FF8000', fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 18 },
  overline:       { color: '#1A1206', fontFamily: 'Inter-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.4 },
  button:         { color: '#FFFFFF', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 16 },
  tab:            { color: '#888888', fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  caption:        { color: '#707070', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Restaurant Card

```tsx
// components/RestaurantCard.tsx
import { useState } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RestaurantCard({
  name, cuisine, eta, fee, rating, deal, perksBadge, imageUri,
}: {
  name: string; cuisine: string; eta: string; fee: string;
  rating: number; deal?: string; perksBadge?: string; imageUri: string;
}) {
  const [saved, setSaved] = useState(false);
  return (
    <View style={{ marginBottom: 22 }}>
      <View style={{ borderRadius: 14, overflow: 'hidden', aspectRatio: 16 / 9 }}>
        <Image source={{ uri: imageUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        {perksBadge ? (
          <View style={{
            position: 'absolute', top: 10, left: 10,
            backgroundColor: colors.perksGold, borderRadius: 6,
            paddingHorizontal: 8, paddingVertical: 4,
          }}>
            <Text style={typography.overline}>{perksBadge}</Text>
          </View>
        ) : null}
        <Pressable
          onPress={() => setSaved(s => !s)}
          style={{
            position: 'absolute', top: 10, right: 10,
            width: 30, height: 30, borderRadius: 15,
            backgroundColor: 'rgba(0,0,0,0.45)',
            alignItems: 'center', justifyContent: 'center',
          }}
        >
          <Ionicons name={saved ? 'heart' : 'heart-outline'} size={16}
            color={saved ? colors.ghRed : '#FFFFFF'} />
        </Pressable>
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'center', marginTop: 10 }}>
        <Text style={typography.restaurantName}>{name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginLeft: 'auto' }}>
          <Ionicons name="star" size={12} color={colors.ratingGreen} />
          <Text style={[typography.deal, { color: colors.textPrimary }]}>{rating.toFixed(1)}</Text>
        </View>
      </View>
      <Text style={[typography.meta, { marginTop: 4 }]}>{`${cuisine} · ${eta} · ${fee}`}</Text>
      {deal ? <Text style={[typography.deal, { marginTop: 5 }]}>{deal}</Text> : null}
    </View>
  );
}
```

### Cuisine Chip Rail

```tsx
// components/CuisineChips.tsx
import { useState } from 'react';
import { Pressable, ScrollView, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CuisineChips({ items }: { items: string[] }) {
  const [selected, setSelected] = useState(0);
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ gap: 10, paddingHorizontal: 16 }}>
      {items.map((label, i) => {
        const active = i === selected;
        return (
          <Pressable key={label} onPress={() => setSelected(i)}
            style={{
              height: 34, paddingHorizontal: 14, borderRadius: 999,
              alignItems: 'center', justifyContent: 'center',
              backgroundColor: active ? colors.ghRed : colors.surface1,
              borderWidth: 0.5, borderColor: active ? colors.ghRed : colors.divider,
            }}>
            <Text style={[typography.deal, { color: active ? '#FFFFFF' : colors.textSecondary }]}>{label}</Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

### Quantity Stepper

```tsx
// components/QuantityStepper.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function QuantityStepper({ quantity, onChange }: { quantity: number; onChange: (q: number) => void }) {
  const tick = (next: number) => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    onChange(next);
  };
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      paddingHorizontal: 8, paddingVertical: 6, borderRadius: 999,
      backgroundColor: colors.surface1, borderWidth: 0.5, borderColor: colors.divider,
      alignSelf: 'flex-start',
    }}>
      <Pressable onPress={() => quantity > 1 && tick(quantity - 1)}
        style={{ width: 30, height: 30, borderRadius: 15, backgroundColor: colors.ghRed,
                 alignItems: 'center', justifyContent: 'center', opacity: quantity > 1 ? 1 : 0.35 }}>
        <Ionicons name="remove" size={16} color="#FFFFFF" />
      </Pressable>
      <Text style={[typography.price, { minWidth: 18, textAlign: 'center' }]}>{quantity}</Text>
      <Pressable onPress={() => tick(quantity + 1)}
        style={{ width: 30, height: 30, borderRadius: 15, backgroundColor: colors.ghRed,
                 alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="add" size={16} color="#FFFFFF" />
      </Pressable>
    </View>
  );
}
```

### Live Order Tracking Banner

```tsx
// components/OrderTrackingBanner.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function OrderTrackingBanner({
  eta, restaurant, status, progress,
}: { eta: string; restaurant: string; status: string; progress: number }) {
  const pulse = useSharedValue(1);
  useEffect(() => {
    pulse.value = withRepeat(withTiming(1.2, { duration: 1400 }), -1, true);
  }, []);
  const dotStyle = useAnimatedStyle(() => ({ transform: [{ scale: pulse.value }] }));

  return (
    <LinearGradient colors={[colors.trackBgFrom, colors.trackBgTo]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{ borderRadius: 16, padding: 16, gap: 12 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <Animated.View style={[{ width: 8, height: 8, borderRadius: 4,
          backgroundColor: colors.trackBlue }, dotStyle]} />
        <Text style={[typography.deal, { color: colors.darkTextPrimary }]}>{eta}</Text>
        <Text style={[typography.caption, { marginLeft: 'auto' }]}>{restaurant}</Text>
      </View>
      <View style={{ height: 4, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.12)', overflow: 'hidden' }}>
        <View style={{ height: 4, borderRadius: 2, backgroundColor: colors.trackBlue,
          width: `${Math.round(progress * 100)}%` }} />
      </View>
      <Text style={[typography.caption, { color: colors.trackBlue, fontFamily: 'Inter-SemiBold' }]}>{status}</Text>
    </LinearGradient>
  );
}
```

### Order Tracking Timeline

```tsx
// components/TrackingTimeline.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type StepState = 'done' | 'active' | 'pending';
type Step = { title: string; sub: string; state: StepState };

const nodeFill = (s: StepState) => s === 'done' ? colors.trackBlue : s === 'active' ? colors.ghRed : colors.canvas;
const nodeStroke = (s: StepState) => s === 'done' ? colors.trackBlue : s === 'active' ? colors.ghRed : colors.divider;

export function TrackingTimeline({ steps }: { steps: Step[] }) {
  return (
    <View>
      {steps.map((step, i) => (
        <View key={i} style={{ flexDirection: 'row', alignItems: 'flex-start', gap: 12 }}>
          <View style={{ alignItems: 'center' }}>
            <View style={{
              width: 16, height: 16, borderRadius: 8,
              backgroundColor: nodeFill(step.state),
              borderWidth: 3, borderColor: nodeStroke(step.state),
            }} />
            {i < steps.length - 1 ? (
              <View style={{ width: 3, flex: 1, minHeight: 26,
                backgroundColor: step.state === 'done' ? colors.trackBlue : colors.divider }} />
            ) : null}
          </View>
          <View style={{ paddingBottom: 22 }}>
            <Text style={[typography.meta, { color: colors.textPrimary, fontFamily: 'Inter-Bold' }]}>{step.title}</Text>
            <Text style={typography.caption}>{step.sub}</Text>
          </View>
        </View>
      ))}
    </View>
  );
}
```

### Primary Button

```tsx
// components/GHButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GHButton({
  title, onPress, variant = 'red',
}: { title: string; onPress: () => void; variant?: 'red' | 'orange' }) {
  const bg = variant === 'red' ? colors.ghRed : colors.ghOrange;
  const pressedBg = variant === 'red' ? colors.ghRedPressed : colors.ghOrangePressed;
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 52, borderRadius: 999, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? pressedBg : bg,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={[typography.button, { color: variant === 'red' ? '#FFFFFF' : colors.perksInkText }]}>{title}</Text>
    </Pressable>
  );
}
// Checkout: <GHButton title="Go to checkout · $42.18" onPress={...} />
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
        tabBarActiveTintColor:  colors.ghRed,
        tabBarInactiveTintColor: '#888888',
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Delivery', tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"        size={22} color={color} /> }} />
      <Tabs.Screen name="orders"  options={{ title: 'Orders',   tabBarIcon: ({ color }) => <Ionicons name="receipt-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="saved"   options={{ title: 'Saved',    tabBarIcon: ({ color }) => <Ionicons name="heart-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account',  tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Add-to-bag badge bump — Reanimated spring
import { withSequence, withSpring } from 'react-native-reanimated';
badgeScale.value = withSequence(
  withSpring(1.25, { damping: 6 }),
  withSpring(1.0,  { damping: 12 }),
);

// Quantity stepper — Haptics + LayoutAnimation / numeric crossfade
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Tracking progress fill — animate width
progress.value = withTiming(liveProgress, { duration: 600 });

// Tracking dot pulse
pulse.value = withRepeat(withTiming(1.2, { duration: 1400 }), -1, true);

// Step timeline advance (stage completes)
// activeIndex bump + Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success)

// Card → detail: shared element via expo-router + react-native-shared-element (320ms)
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For bespoke food/cuisine glyphs use `MaterialCommunityIcons` or bundle SVG via `expo-image`.

| Purpose | Ionicons |
|---------|----------|
| Delivery (tab) | `home` |
| Search (tab) | `search` |
| Orders (tab) | `receipt-outline` |
| Saved (tab) | `heart-outline` |
| Account (tab) | `person-circle-outline` |
| Saved heart (card) | `heart` / `heart-outline` |
| Rating star | `star` |
| Address chevron | `chevron-down` |
| Bag / cart | `bag-handle` / `bag-handle-outline` |
| Stepper minus | `remove` |
| Stepper plus | `add` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Driver call | `call` |
| Driver message | `chatbubble` |
| Tracking pin | `location` |
| Promo / deal | `pricetag` |
| Filters | `options-outline` |

## 7. Platform Notes

- **Font choice**: Graphik is proprietary — ship **Inter** (SIL OFL) as the substitute. Bundle Regular → Black weights
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on all prices, fees, ETAs and quantity counters so the figures don't jitter as they change
- **Status bar**: `<StatusBar style="dark" />` on light mode; `"light"` on dark and over the tracking map (with a gradient scrim)
- **Safe area**: wrap screens in `SafeAreaView`; the address bar, bottom tab bar and the sticky checkout pill need safe-area padding
- **Dynamic Type**: React Native respects system scale on `<Text>`; set `allowFontScaling={false}` on Perks badges, deal lines, tab labels, chip text and the ETA dot label
- **Sticky checkout**: place the "Go to checkout · $X" pill in an absolutely-positioned footer with `paddingBottom: insets.bottom`; embed the tabular total in the label
- **Live tracking**: poll/subscribe order status; animate the progress `width` with `withTiming(…, 600)`; fire a success haptic when a stage advances
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkDivider`; keep Red/Orange/Gold/Blue/Green constant; never dim food imagery
- **Maps**: use `react-native-maps` for the courier map; the destination pin uses `ghRed`, the courier marker a custom icon
- **Accessibility**: every restaurant card is one accessible element with a combined label; the saved heart is an `accessibilityRole="button"` toggle; the timeline announces step state via `accessibilityLabel`
