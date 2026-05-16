# Wise (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Wise's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:        '#FFFFFF',
  surface:       '#F7F7F7',
  surfaceSunken: '#EFEFEF',
  divider:       '#E5E5E5',
  border:        '#D2D2D2',

  textPrimary:   '#0E0F0C',
  textSecondary: '#6B6F66',
  textTertiary:  '#9A9D95',

  bright:        '#9FE870',
  brightPressed: '#8AD45C',
  brightTint:    '#EAF9DC',
  forest:        '#163300',
  forestHover:   '#0E2200',

  success: '#2F8F4E',
  pending: '#B5781E',
  error:   '#D4332B',
} as const;

export type WiseColor = keyof typeof colors;
```

## 2. Typography

Load Wise Sans via `expo-font`. Fall back to `System` (SF Pro on iOS); Inter is the closest webfont substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'WiseSans-Regular':   require('../assets/fonts/WiseSans-Regular.ttf'),
    'WiseSans-Semibold':  require('../assets/fonts/WiseSans-Semibold.ttf'),
    'WiseSans-Extrabold': require('../assets/fonts/WiseSans-Extrabold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#0E0F0C' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  balance:    { color: '#FFFFFF', ...tnum, fontFamily: 'WiseSans-Extrabold', fontSize: 40, lineHeight: 42, letterSpacing: -0.6 },
  titleLarge: { ...base, fontFamily: 'WiseSans-Extrabold', fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  section:    { ...base, fontFamily: 'WiseSans-Extrabold', fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  currency:   { ...base, ...tnum, fontFamily: 'WiseSans-Extrabold', fontSize: 22, lineHeight: 24, letterSpacing: -0.2 },
  subsection: { ...base, fontFamily: 'WiseSans-Semibold',  fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  amount:     { ...base, ...tnum, fontFamily: 'WiseSans-Semibold',  fontSize: 16, lineHeight: 19 },
  title:      { ...base, fontFamily: 'WiseSans-Semibold',  fontSize: 16, lineHeight: 21 },
  body:       { ...base, fontFamily: 'WiseSans-Regular',   fontSize: 15, lineHeight: 22 },
  button:     { color: '#163300', fontFamily: 'WiseSans-Extrabold', fontSize: 16, lineHeight: 20 },
  meta:       { fontFamily: 'WiseSans-Regular', fontSize: 13, lineHeight: 17, color: '#6B6F66' },
  labelUpper: { fontFamily: 'WiseSans-Extrabold', fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  tab:        { fontFamily: 'WiseSans-Semibold', fontSize: 11, lineHeight: 13, letterSpacing: 0.1 },
  caption:    { fontFamily: 'WiseSans-Regular', fontSize: 11, lineHeight: 14, color: '#6B6F66' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary CTA (Bright Green)

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
        minHeight: 52, borderRadius: 16, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.brightPressed : colors.bright,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function ForestButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        minHeight: 52, borderRadius: 16, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.forestHover : colors.forest,
      })}
    >
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Forest Account Hero (with number roll-up)

```tsx
// components/ForestHero.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ForestHero({ total }: { total: string }) {
  return (
    <View style={{
      backgroundColor: colors.forestHover, borderRadius: 20, padding: 24, gap: 12,
      shadowColor: colors.forestHover, shadowOpacity: 0.25, shadowRadius: 16, shadowOffset: { width: 0, height: 12 },
    }}>
      <Text style={[typography.labelUpper, { color: colors.bright }]}>Total balance</Text>
      {/* For a true digit roll-up use a number-flip lib; this is the static target */}
      <Text style={typography.balance}>{total}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <Ionicons name="add" size={16} color={colors.bright} />
        <Text style={[typography.title, { color: colors.bright }]}>Add money</Text>
      </View>
    </View>
  );
}
```

### Currency Balance Row

```tsx
// components/CurrencyRow.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CurrencyRow({
  flag, code, name, balance,
}: { flag: string; code: string; name: string; balance: string }) {
  return (
    <Pressable
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 12, height: 64, paddingHorizontal: 16,
        backgroundColor: pressed ? colors.surface : colors.canvas,
        borderBottomWidth: 1, borderBottomColor: colors.divider,
      })}
    >
      <View style={{ width: 32, height: 32, borderRadius: 16, backgroundColor: colors.surface, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontSize: 18 }}>{flag}</Text>
      </View>
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.title}>{code}</Text>
        <Text style={typography.meta}>{name}</Text>
      </View>
      <Text style={typography.currency}>{balance}</Text>
    </Pressable>
  );
}
```

### Fee-Transparency Card (signature)

```tsx
// components/FeeBreakdownCard.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Line = { label: string; value: string; emphasized?: boolean };

export function FeeBreakdownCard({ lines }: { lines: Line[] }) {
  return (
    <View style={{ backgroundColor: colors.canvas, borderColor: colors.divider, borderWidth: 1, borderRadius: 16, padding: 20 }}>
      {lines.map((l, i) => (
        <View key={i}>
          <View style={{
            flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center',
            paddingVertical: 12,
            paddingHorizontal: l.emphasized ? 12 : 0,
            backgroundColor: l.emphasized ? colors.brightTint : 'transparent',
            borderRadius: l.emphasized ? 10 : 0,
          }}>
            <Text style={[l.emphasized ? typography.title : typography.body, { color: l.emphasized ? colors.forest : colors.textSecondary }]}>
              {l.label}
            </Text>
            <Text style={[l.emphasized ? typography.subsection : typography.amount, { color: l.emphasized ? colors.forest : colors.textPrimary }]}>
              {l.value}
            </Text>
          </View>
          {i < lines.length - 1 && !l.emphasized && (
            <View style={{ height: 1, backgroundColor: colors.divider }} />
          )}
        </View>
      ))}
    </View>
  );
}
```

### Send-Money Stepper (signature)

```tsx
// components/SendStepper.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function SendStepper({ steps, current }: { steps: string[]; current: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center' }}>
      {steps.map((_, i) => {
        const done = i < current;
        const active = i === current;
        return (
          <View key={i} style={{ flexDirection: 'row', alignItems: 'center', flex: i < steps.length - 1 ? 1 : 0 }}>
            <View style={{
              width: 28, height: 28, borderRadius: 14, alignItems: 'center', justifyContent: 'center',
              backgroundColor: done ? colors.bright : active ? colors.forest : colors.divider,
            }}>
              {done
                ? <Ionicons name="checkmark" size={14} color={colors.forest} />
                : <Text style={{ fontFamily: 'WiseSans-Extrabold', fontSize: 13, color: active ? '#FFF' : colors.textSecondary }}>{i + 1}</Text>}
            </View>
            {i < steps.length - 1 && (
              <View style={{ flex: 1, height: 2, backgroundColor: done ? colors.forest : colors.divider }} />
            )}
          </View>
        );
      })}
    </View>
  );
}
```

## 4. Number Roll-Up Helper

```tsx
// A simple animated count-up for the hero total; for true per-digit roll use a
// number-flip component. Keep duration ~500ms ease-out.
import { useEffect, useState } from 'react';
import { Text } from 'react-native';
import { typography } from '../theme/typography';

export function RollingBalance({ target, currency = '£' }: { target: number; currency?: string }) {
  const [v, setV] = useState(0);
  useEffect(() => {
    const start = performance.now();
    let raf: number;
    const tick = (now: number) => {
      const t = Math.min(1, (now - start) / 500);
      const eased = 1 - Math.pow(1 - t, 3);
      setV(target * eased);
      if (t < 1) raf = requestAnimationFrame(tick);
    };
    raf = requestAnimationFrame(tick);
    return () => cancelAnimationFrame(raf);
  }, [target]);
  return <Text style={typography.balance}>{currency}{v.toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })}</Text>;
}
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
        tabBarActiveTintColor:   colors.forest,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'WiseSans-Semibold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"      options={{ title: 'Home',       tabBarIcon: ({ color }) => <Ionicons name="home"        size={24} color={color} /> }} />
      <Tabs.Screen name="card"       options={{ title: 'Card',       tabBarIcon: ({ color }) => <Ionicons name="card"        size={24} color={color} /> }} />
      <Tabs.Screen name="recipients" options={{ title: 'Recipients', tabBarIcon: ({ color }) => <Ionicons name="people"      size={24} color={color} /> }} />
      <Tabs.Screen name="payments"   options={{ title: 'Payments',   tabBarIcon: ({ color }) => <Ionicons name="swap-horizontal" size={24} color={color} /> }} />
      <Tabs.Screen name="account"    options={{ title: 'Account',    tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Number roll-up — see RollingBalance (500ms ease-out)

// CTA press — scale 0.98 + brightPressed fill in the Pressable style

// Stepper advance: animate dot background + rail color with withTiming(200)
import { useSharedValue, withTiming } from 'react-native-reanimated';

// Rate ticker pulse: a small dot whose opacity oscillates with withRepeat
import Animated, { useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
const pulse = useSharedValue(0.3);
useEffect(() => { pulse.value = withRepeat(withTiming(1, { duration: 1000 }), -1, true); }, []);
const dotStyle = useAnimatedStyle(() => ({ opacity: pulse.value }));

// Fee card reveal: stagger row entry with entering={FadeInDown.delay(i*40)}
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Wise's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home-outline` / `home` |
| Card (tab) | `card-outline` / `card` |
| Recipients (tab) | `people-outline` / `people` |
| Payments (tab) | `swap-horizontal` |
| Account (tab) | `person-circle-outline` / `person-circle` |
| Send | `paper-plane` |
| Add money | `add` |
| Request | `arrow-down` |
| Convert | `swap-horizontal` |
| Search | `search` |
| Notifications | `notifications-outline` / `notifications` |
| Statement | `document-text-outline` |
| Step complete | `checkmark` |
| Money in | `arrow-down` |
| Rate up / down | `arrow-up` / `arrow-down` |

## 8. Platform Notes

- **Light-first**: Wise is bright with a single forest anchor; do not invert to dark by default. If you support system dark mode, keep `colors.bright` unchanged (still forest text on fills) and use `colors.forestHover` as the deepest surface
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` on light screens; switch to `light` while the forest hero is the dominant scrolled region if desired
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the send flow's sticky "Continue" must clear the home indicator
- **Tab bar is opaque**: no `expo-blur` — Wise's bar is solid white with a hairline top border for clarity
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on the total, every currency row, and fee lines so decimals align
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on tab labels, uppercase labels, and currency-row figures where layout is rigid
- **Accessibility**: add `accessibilityRole="button"` + labelled `accessibilityLabel` on the CTA; read fee lines as "label, value" and mark the "Recipient gets" line as a header; announce stepper state ("Step 2 of 4, Amount")
