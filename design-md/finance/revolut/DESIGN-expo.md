# Revolut (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Revolut's visual language into paste-ready Expo / React Native code: a design-token module, the brand gradient, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#0A0A0F',
  surface1: '#16161F',
  surface2: '#1E1E2A',
  surface3: '#28283A',
  divider:  '#2A2A38',
  border:   '#33334A',

  textPrimary:   '#FFFFFF',
  textSecondary: '#9A9AAA',
  textTertiary:  '#6A6A7E',

  gradStart:    '#5B6BFF',
  gradEnd:      '#9C6BFF',
  brand:        '#6B5BFF',
  brandPressed: '#5648D6',
  brandTint:    '#1C1B33',

  income: '#1FD17B',
  spend:  '#FF5A6A',
  warn:   '#FFB23F',
  crypto: '#F7C948',
} as const;

export const BRAND_GRADIENT = ['#5B6BFF', '#9C6BFF'] as const;

export type RevolutColor = keyof typeof colors;
```

## 2. Typography

Load Aeonik via `expo-font`. Fall back to `System` (SF Pro on iOS); Inter is the closest webfont substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Aeonik-Regular': require('../assets/fonts/Aeonik-Regular.ttf'),
    'Aeonik-Medium':  require('../assets/fonts/Aeonik-Medium.ttf'),
    'Aeonik-Bold':    require('../assets/fonts/Aeonik-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  balance:     { ...base, ...tnum, fontFamily: 'Aeonik-Bold',   fontSize: 40, lineHeight: 42, letterSpacing: -0.6 },
  titleLarge:  { ...base, fontFamily: 'Aeonik-Bold',   fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  section:     { ...base, fontFamily: 'Aeonik-Bold',   fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  tileBalance: { ...base, ...tnum, fontFamily: 'Aeonik-Bold',   fontSize: 22, lineHeight: 24, letterSpacing: -0.2 },
  subsection:  { ...base, fontFamily: 'Aeonik-Medium', fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  amount:      { ...base, ...tnum, fontFamily: 'Aeonik-Medium', fontSize: 16, lineHeight: 19 },
  merchant:    { ...base, fontFamily: 'Aeonik-Medium', fontSize: 16, lineHeight: 21 },
  body:        { ...base, fontFamily: 'Aeonik-Regular', fontSize: 15, lineHeight: 22 },
  button:      { color: '#FFFFFF', fontFamily: 'Aeonik-Medium', fontSize: 16, lineHeight: 20 },
  meta:        { fontFamily: 'Aeonik-Regular', fontSize: 13, lineHeight: 17, color: '#9A9AAA' },
  labelUpper:  { ...base, fontFamily: 'Aeonik-Bold',   fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  tab:         { fontFamily: 'Aeonik-Medium', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  caption:     { fontFamily: 'Aeonik-Regular', fontSize: 11, lineHeight: 14, color: '#9A9AAA' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Gradient CTA

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import * as Haptics from 'expo-haptics';
import { BRAND_GRADIENT, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        transform: [{ scale: pressed ? 0.98 : 1 }],
        opacity: pressed ? 0.85 : 1,
        borderRadius: 16,
        shadowColor: colors.brand, shadowOpacity: 0.3, shadowRadius: 14, shadowOffset: { width: 0, height: 8 },
      })}
    >
      <LinearGradient
        colors={BRAND_GRADIENT as unknown as string[]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ minHeight: 52, borderRadius: 16, alignItems: 'center', justifyContent: 'center' }}
      >
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}

export function SecondaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 52, borderRadius: 16, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.surface3 : colors.surface2,
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Currency Balance Tile

```tsx
// components/CurrencyTile.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CurrencyTile({
  flag, code, name, balance,
}: { flag: string; code: string; name: string; balance: string }) {
  return (
    <Pressable
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 12, height: 72, paddingHorizontal: 16,
        backgroundColor: pressed ? colors.surface2 : colors.surface1,
        borderColor: colors.divider, borderWidth: 1, borderRadius: 16,
      })}
    >
      <View style={{ width: 28, height: 28, borderRadius: 14, backgroundColor: colors.surface2, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontSize: 16 }}>{flag}</Text>
      </View>
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.merchant}>{code}</Text>
        <Text style={typography.meta}>{name}</Text>
      </View>
      <Text style={typography.tileBalance}>{balance}</Text>
    </Pressable>
  );
}
```

### Transaction Row

```tsx
// components/TransactionRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TransactionRow({
  merchant, meta, amount, incoming, logoUri,
}: { merchant: string; meta: string; amount: string; incoming: boolean; logoUri: string }) {
  return (
    <Pressable
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 12, height: 64, paddingHorizontal: 16,
        backgroundColor: pressed ? colors.surface2 : 'transparent',
      })}
    >
      <Image source={{ uri: logoUri }} style={{ width: 40, height: 40, borderRadius: 20, backgroundColor: colors.surface2 }} />
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.merchant} numberOfLines={1}>{merchant}</Text>
        <Text style={typography.meta} numberOfLines={1}>{meta}</Text>
      </View>
      <Text style={[typography.amount, incoming && { color: colors.income }]}>{amount}</Text>
    </Pressable>
  );
}
```

### Spend Analytics Donut

```tsx
// components/SpendDonut.tsx
import { View, Text } from 'react-native';
import Svg, { Circle, Defs, LinearGradient as SvgGradient, Stop } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);
const R = 80, C = 2 * Math.PI * R;

export function SpendDonut({ total, progress }: { total: string; progress: number }) {
  const p = useSharedValue(0);
  useEffect(() => { p.value = withTiming(progress, { duration: 700, easing: Easing.out(Easing.cubic) }); }, [progress]);
  const animatedProps = useAnimatedProps(() => ({ strokeDashoffset: C * (1 - p.value) }));

  return (
    <View style={{ backgroundColor: colors.surface1, borderRadius: 20, padding: 20, alignItems: 'center' }}>
      <Svg width={180} height={180} style={{ transform: [{ rotate: '-90deg' }] }}>
        <Defs>
          <SvgGradient id="g" x1="0" y1="0" x2="1" y2="1">
            <Stop offset="0" stopColor={colors.gradStart} />
            <Stop offset="1" stopColor={colors.gradEnd} />
          </SvgGradient>
        </Defs>
        <Circle cx={90} cy={90} r={R} stroke={colors.surface3} strokeWidth={14} fill="none" />
        <AnimatedCircle cx={90} cy={90} r={R} stroke="url(#g)" strokeWidth={14} fill="none"
          strokeLinecap="round" strokeDasharray={C} animatedProps={animatedProps} />
      </Svg>
      <View style={{ position: 'absolute', top: 0, bottom: 0, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={typography.section}>{total}</Text>
        <Text style={typography.meta}>this month</Text>
      </View>
    </View>
  );
}
```

### Metal Card Hero (flip + sheen)

```tsx
// components/MetalCardHero.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withRepeat } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { useEffect, useState } from 'react';
import { typography } from '../theme/typography';

export function MetalCardHero() {
  const [flipped, setFlipped] = useState(false);
  const rot = useSharedValue(0);
  const sheen = useSharedValue(-1);

  useEffect(() => { sheen.value = withTiming(1, { duration: 1200 }); }, []);

  const cardStyle = useAnimatedStyle(() => ({ transform: [{ perspective: 800 }, { rotateY: `${rot.value}deg` }] }));
  const sheenStyle = useAnimatedStyle(() => ({ transform: [{ translateX: sheen.value * 260 }] }));

  const flip = () => {
    const next = !flipped;
    setFlipped(next);
    rot.value = withTiming(next ? 180 : 0, { duration: 450 });
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
  };

  return (
    <Pressable onPress={flip}>
      <Animated.View style={[{ aspectRatio: 1.586, borderRadius: 16, overflow: 'hidden',
        shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 24, shadowOffset: { width: 0, height: 8 } }, cardStyle]}>
        <LinearGradient colors={['#2E2E33', '#101014']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ flex: 1, padding: 20, justifyContent: 'space-between' }}>
          <Text style={[typography.subsection, { opacity: 0.9 }]}>Revolut</Text>
          <Text style={typography.amount}>{flipped ? 'CVV 042' : '•••• 4821'}</Text>
        </LinearGradient>
        <Animated.View style={[{ position: 'absolute', top: 0, bottom: 0, width: 80 }, sheenStyle]}>
          <LinearGradient colors={['transparent', 'rgba(255,255,255,0.18)', 'transparent']}
            start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }} />
        </Animated.View>
      </Animated.View>
    </Pressable>
  );
}
```

## 4. Brand Gradient Helper

```tsx
// Apply the brand gradient as a tint behind a glyph by masking with expo's MaskedView,
// or use a solid colors.brand for small icons / focus rings where a gradient is impractical.
import MaskedView from '@react-native-masked-view/masked-view';
import { LinearGradient } from 'expo-linear-gradient';
import { BRAND_GRADIENT } from '../theme/colors';

export function GradientGlyph({ children }: { children: React.ReactNode }) {
  return (
    <MaskedView maskElement={<>{children}</>}>
      <LinearGradient colors={BRAND_GRADIENT as unknown as string[]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ width: 24, height: 24 }} />
    </MaskedView>
  );
}
```

## 5. Tab Bar

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
        tabBarActiveTintColor:   colors.brand,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(10,10,15,0.6)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Aeonik-Medium', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="invest"    options={{ title: 'Invest',    tabBarIcon: ({ color }) => <Ionicons name="trending-up"   size={24} color={color} /> }} />
      <Tabs.Screen name="crypto"    options={{ title: 'Crypto',    tabBarIcon: ({ color }) => <Ionicons name="logo-bitcoin" size={24} color={color} /> }} />
      <Tabs.Screen name="lifestyle" options={{ title: 'Lifestyle', tabBarIcon: ({ color }) => <Ionicons name="sparkles"      size={24} color={color} /> }} />
      <Tabs.Screen name="cards"     options={{ title: 'Cards',     tabBarIcon: ({ color }) => <Ionicons name="card"          size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Card flip + sheen — see MetalCardHero above (rotateY 450ms + sheen sweep 1200ms + medium haptic)

// Gradient CTA press — scale 0.98 + opacity 0.85 in the Pressable style, ~150ms

// Donut draw — animate strokeDashoffset 0→value over 700ms ease-out on mount

// Balance hide/reveal: animate a blur overlay opacity, 250ms
import { useSharedValue, withTiming } from 'react-native-reanimated';
const blur = useSharedValue(0);
const toggleBalance = (hide: boolean) => { blur.value = withTiming(hide ? 1 : 0, { duration: 250 }); };

// Segmented thumb: animate a gradient capsule's translateX, 220ms ease-in-out
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Revolut's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home-outline` / `home` |
| Invest (tab) | `trending-up` |
| Crypto (tab) | `logo-bitcoin` |
| Lifestyle (tab) | `sparkles` |
| Cards (tab) | `card-outline` / `card` |
| Add money | `add` |
| Exchange | `swap-horizontal` |
| Freeze card | `snow` |
| Send / pay | `paper-plane` |
| Contactless | `wifi` (rotated) |
| Search | `search` |
| Notifications | `notifications-outline` / `notifications` |
| Statement | `document-text-outline` |
| Income arrow | `arrow-down` |
| Outgoing arrow | `arrow-up` |

## 8. Platform Notes

- **Dark-first**: Revolut is a dark cockpit; do not invert to light by default. If you support system light mode, use `colors.brandPressed` solid and drop gradient ghosting
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` — light content on the near-black canvas
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the absolute tab bar needs bottom inset so content clears it
- **Tab bar blur**: `expo-blur` gives the `.regularMaterial` feel; if Reduce Transparency is on, swap to a solid `rgba(10,10,15,0.98)`
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on every balance, amount, and currency tile so decimals align
- **Gradient performance**: prefer `expo-linear-gradient` over re-rendering SVG gradients; the card sheen is a single translating band, not a per-frame redraw
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on tab labels, uppercase labels, and currency-tile figures where layout is rigid
- **Accessibility**: add `accessibilityRole="button"` + a labelled `accessibilityLabel` on the CTA and card hero; group transaction-row text and state direction in the label
