# Klarna (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Klarna's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:         '#FFFFFF',
  surfacePink:    '#FFF2F5',
  surfaceNeutral: '#F6F4F8',
  divider:        '#F0DDE3',
  border:         '#E2D2D8',

  textPrimary:   '#0B051D',
  textSecondary: '#6E6878',
  textTertiary:  '#9B96A3',

  pink:        '#FFB3C7',
  pinkPressed: '#F49CB4',
  pinkTint:    '#FFF2F5',
  black:       '#0B051D',
  blackHover:  '#1A1330',

  success:  '#0E8A4F',
  upcoming: '#A9700E',
  error:    '#C8102E',
} as const;

export type KlarnaColor = keyof typeof colors;
```

## 2. Typography

Load Klarna Text/Display via `expo-font`. Fall back to `System` (SF Pro on iOS); Inter is the closest webfont substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'KlarnaText-Regular': require('../assets/fonts/KlarnaText-Regular.ttf'),
    'KlarnaText-Medium':  require('../assets/fonts/KlarnaText-Medium.ttf'),
    'KlarnaText-Bold':    require('../assets/fonts/KlarnaText-Bold.ttf'),
    'KlarnaDisplay-Bold': require('../assets/fonts/KlarnaDisplay-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#0B051D' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  amountHero: { ...base, ...tnum, fontFamily: 'KlarnaDisplay-Bold', fontSize: 36, lineHeight: 38, letterSpacing: -0.5 },
  titleLarge: { ...base, fontFamily: 'KlarnaText-Bold',    fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  section:    { ...base, fontFamily: 'KlarnaText-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  planAmount: { ...base, ...tnum, fontFamily: 'KlarnaText-Bold',    fontSize: 22, lineHeight: 24, letterSpacing: -0.2 },
  subsection: { ...base, fontFamily: 'KlarnaText-Medium',  fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  amount:     { ...base, ...tnum, fontFamily: 'KlarnaText-Medium',  fontSize: 16, lineHeight: 19 },
  title:      { ...base, fontFamily: 'KlarnaText-Medium',  fontSize: 16, lineHeight: 21 },
  body:       { ...base, fontFamily: 'KlarnaText-Regular', fontSize: 15, lineHeight: 23 },
  button:     { color: '#0B051D', fontFamily: 'KlarnaText-Bold', fontSize: 16, lineHeight: 20 },
  meta:       { fontFamily: 'KlarnaText-Regular', fontSize: 13, lineHeight: 17, color: '#6E6878' },
  labelUpper: { fontFamily: 'KlarnaText-Bold', fontSize: 11, lineHeight: 13, letterSpacing: 0.5, textTransform: 'uppercase' as const },
  tab:        { fontFamily: 'KlarnaText-Medium', fontSize: 11, lineHeight: 13, letterSpacing: 0.1 },
  caption:    { fontFamily: 'KlarnaText-Regular', fontSize: 11, lineHeight: 14, color: '#6E6878' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary CTA (Klarna Pink)

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        minHeight: 56, borderRadius: 28, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.pinkPressed : colors.pink,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function BlackButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 56, borderRadius: 28, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.blackHover : colors.black,
      })}
    >
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Pay-in-4 Schedule Card (signature)

```tsx
// components/PayInFourCard.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type State = 'paid' | 'next' | 'upcoming';
type Installment = { date: string; amount: string; state: State };

function Dot({ state }: { state: State }) {
  if (state === 'paid') {
    return (
      <View style={{ width: 28, height: 28, borderRadius: 14, backgroundColor: colors.pink, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="checkmark" size={14} color={colors.black} />
      </View>
    );
  }
  const ring = state === 'next' ? colors.black : colors.divider;
  return <View style={{ width: 28, height: 28, borderRadius: 14, borderWidth: 2, borderColor: ring, backgroundColor: '#FFF' }} />;
}

export function PayInFourCard({ installments }: { installments: Installment[] }) {
  return (
    <View style={{ backgroundColor: colors.canvas, borderColor: colors.divider, borderWidth: 1, borderRadius: 20, padding: 20 }}>
      <View style={{ flexDirection: 'row', alignItems: 'flex-start' }}>
        {installments.map((it, i) => (
          <View key={i} style={{ flex: 1, alignItems: 'center', position: 'relative' }}>
            {i < installments.length - 1 && (
              <View style={{
                position: 'absolute', top: 13, left: '50%', right: '-50%', height: 2,
                backgroundColor: it.state === 'paid' ? colors.pink : colors.divider,
              }} />
            )}
            <Dot state={it.state} />
            <Text style={[typography.meta, { marginTop: 8 }]}>{it.date}</Text>
            <Text style={[typography.meta, { fontFamily: 'KlarnaText-Bold', color: colors.textPrimary }]}>{it.amount}</Text>
          </View>
        ))}
      </View>
    </View>
  );
}
```

### Black Payment-Summary Hero

```tsx
// components/BlackHero.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BlackHero({ total, nextDate }: { total: string; nextDate: string }) {
  return (
    <View style={{
      backgroundColor: colors.black, borderRadius: 24, padding: 24, gap: 12,
      shadowColor: colors.black, shadowOpacity: 0.18, shadowRadius: 16, shadowOffset: { width: 0, height: 12 },
    }}>
      <Text style={[typography.labelUpper, { color: colors.pink }]}>Total due</Text>
      <Text style={[typography.amountHero, { color: '#FFFFFF' }]}>{total}</Text>
      <Text style={[typography.meta, { color: colors.textTertiary }]}>Next payment {nextDate}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <Ionicons name="card" size={15} color={colors.pink} />
        <Text style={[typography.title, { color: colors.pink }]}>Pay now</Text>
      </View>
    </View>
  );
}
```

### Order Row

```tsx
// components/OrderRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function OrderRow({
  merchant, meta, amount, status, statusColor, logoUri,
}: { merchant: string; meta: string; amount: string; status: string; statusColor: string; logoUri: string }) {
  return (
    <Pressable
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 12, height: 72, paddingHorizontal: 20,
        backgroundColor: pressed ? colors.surfacePink : colors.canvas,
        borderBottomWidth: 1, borderBottomColor: colors.divider,
      })}
    >
      <Image source={{ uri: logoUri }} style={{ width: 44, height: 44, borderRadius: 12, backgroundColor: colors.surfacePink }} />
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.title}>{merchant}</Text>
        <Text style={typography.meta}>{meta}</Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 4 }}>
        <Text style={typography.amount}>{amount}</Text>
        <Text style={[typography.caption, { fontFamily: 'KlarnaText-Bold', color: statusColor }]}>{status}</Text>
      </View>
    </Pressable>
  );
}
```

### In-App Shopping Browser Footer (signature)

```tsx
// components/PayWithKlarnaFooter.tsx
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, Easing } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PayWithKlarnaFooter({ onPress }: { onPress: () => void }) {
  const y = useSharedValue(80);
  useEffect(() => { y.value = withTiming(0, { duration: 320, easing: Easing.out(Easing.cubic) }); }, []);
  const style = useAnimatedStyle(() => ({ transform: [{ translateY: y.value }] }));

  return (
    <Animated.View style={[{ position: 'absolute', left: 0, right: 0, bottom: 0 }, style]}>
      <Pressable
        onPress={onPress}
        style={({ pressed }) => ({
          height: 56, alignItems: 'center', justifyContent: 'center',
          backgroundColor: pressed ? colors.pinkPressed : colors.pink,
        })}
      >
        <Text style={typography.button}>Pay with Klarna</Text>
      </Pressable>
    </Animated.View>
  );
}
```

## 4. Smooth Transition Helper

```tsx
// Klarna's signature glide: unhurried easing on screen / sheet transitions.
import { Easing } from 'react-native-reanimated';

export const KLARNA_GLIDE = { duration: 340, easing: Easing.inOut(Easing.cubic) };

// expo-router screen options for the gliding push:
export const klarnaScreenOptions = {
  animation: 'slide_from_right' as const,
  animationDuration: 340,
};
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
        tabBarActiveTintColor:   colors.black,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'KlarnaText-Medium', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Shop',     tabBarIcon: ({ color }) => <Ionicons name="bag"           size={24} color={color} /> }} />
      <Tabs.Screen name="payments" options={{ title: 'Payments', tabBarIcon: ({ color }) => <Ionicons name="card"          size={24} color={color} /> }} />
      <Tabs.Screen name="rewards"  options={{ title: 'Rewards',  tabBarIcon: ({ color }) => <Ionicons name="gift"          size={24} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Slide transitions — KLARNA_GLIDE (340ms ease-in-out) on stack/sheet

// CTA press — scale 0.98 + pinkPressed fill in the Pressable style, ~160ms

// Schedule fill: animate a dot's backgroundColor outlined→pink and the
// preceding track segment with withTiming(260)

// Footer reveal — PayWithKlarnaFooter: translateY 80→0 over 320ms ease-out

// Amount roll after payment: AnimatedContent-style crossfade, ~300ms
import { useSharedValue, withTiming } from 'react-native-reanimated';
const fade = useSharedValue(1);
const refreshTotal = () => { fade.value = withTiming(0, { duration: 150 }, () => { fade.value = withTiming(1, { duration: 150 }); }); };
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Klarna's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Shop (tab) | `bag-outline` / `bag` |
| Payments (tab) | `card-outline` / `card` |
| Rewards (tab) | `gift-outline` / `gift` |
| Profile (tab) | `person-circle-outline` / `person-circle` |
| Pay now | `card` |
| Browse | `search` |
| Schedule paid | `checkmark` |
| Order delivered | `cube` |
| Search | `search` |
| Notifications | `notifications-outline` / `notifications` |
| Help | `help-circle-outline` |
| Chevron | `chevron-forward` |
| Due reminder | `time-outline` |

## 8. Platform Notes

- **Light-first**: Klarna is bright and soft with a single black anchor; do not invert to dark by default. If you support system dark mode, keep `colors.pink` unchanged (still near-black text on fills) and use `colors.black` as the deepest surface
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` on light screens; switch to `light` while the black hero dominates if desired
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the checkout sheet and pink footer must clear the home indicator
- **Tab bar is opaque**: no `expo-blur` — Klarna's bar is solid white with a hairline warm-grey top border
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on the hero amount, schedule amounts, order totals, and plan lines
- **Soft radii**: keep the CTA at `borderRadius: 28`, cards at `20`, sheets/hero at `24` — the softness is the brand; don't tighten them
- **Unhurried motion**: stack `animationDuration: 340` and ease transitions slightly slower than default — the Klarna glide
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on tab labels, uppercase labels, and schedule date/amount text where layout is rigid
- **Accessibility**: add `accessibilityRole="button"` + labelled `accessibilityLabel` on the CTA; describe the schedule fully ("Payment 2 of 4, next, due 14 June, 32 pounds 10"); mark "TOTAL DUE" as a header
