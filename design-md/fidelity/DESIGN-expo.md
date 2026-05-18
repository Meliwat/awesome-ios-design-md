# Fidelity (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Fidelity's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Core rule: **money uses `fontVariant: ['tabular-nums']`**, and **green/red are gain/loss only**.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  green:        '#368727',
  greenPressed: '#2A6B1F',
  heritage:     '#00754A',
  leaf:         '#4FB23B',

  // Surfaces (light)
  canvas:         '#FFFFFF',
  surface:        '#F4F6F4',
  surfacePressed: '#E8ECE8',
  divider:        '#E1E5E1',

  // Surfaces (dark)
  darkCanvas:   '#0E1411',
  darkSurface1: '#16201B',
  darkSurface2: '#1F2C24',
  darkDivider:  '#26342B',

  // Text
  textPrimary:       '#1A1F1B',
  textSecondary:     '#5A655C',
  textTertiary:      '#8A938C',
  darkTextPrimary:   '#E8EFE9',
  darkTextSecondary: '#9DB0A2',
  darkTextTertiary:  '#6B7E72',

  // Financial semantics (sacred — gain/loss only)
  gainLight: '#15833E',
  gainDark:  '#15B374',
  lossLight: '#D32F2F',
  lossDark:  '#E5544B',

  // System / accent
  warning: '#C8862B',
  info:    '#1E6FB8',
  gold:    '#C8A24B',
} as const;

export type FidColor = keyof typeof colors;

// Resolve a change value -> semantic color (flat falls back to secondary text)
export function changeColor(change: number, dark: boolean) {
  if (change > 0) return dark ? colors.gainDark : colors.gainLight;
  if (change < 0) return dark ? colors.lossDark : colors.lossLight;
  return dark ? colors.darkTextSecondary : colors.textSecondary;
}
```

## 2. Typography

Load **Inter** (SIL OFL) as the free substitute for Fidelity's "Average Sans". The defining rule: every money value gets `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':    require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':     require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':   require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':       require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':  require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

// Apply to ANY currency / price / % / quantity
export const tnum: TextStyle = { fontVariant: ['tabular-nums'] };

export const typography = {
  balanceHero: { fontFamily: 'Inter-ExtraBold', fontSize: 34, lineHeight: 38, letterSpacing: -0.6, ...tnum },
  screenTitle: { fontFamily: 'Inter-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:   { fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 23 },
  body:        { fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  rowValue:    { fontFamily: 'Inter-Bold',      fontSize: 16, lineHeight: 21, ...tnum },
  rowSymbol:   { fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  meta:        { fontFamily: 'Inter-Regular',   fontSize: 14, lineHeight: 19 },
  numericMono: { fontFamily: 'Inter-SemiBold',  fontSize: 13, lineHeight: 17, ...tnum },
  caption:     { fontFamily: 'Inter-Medium',    fontSize: 12, lineHeight: 16 },
  button:      { fontFamily: 'Inter-Bold',      fontSize: 16, lineHeight: 16 },
  tab:         { fontFamily: 'Inter-SemiBold',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  rangeTab:    { fontFamily: 'Inter-SemiBold',  fontSize: 11, lineHeight: 11, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Balance Header (screen hero)

```tsx
// components/BalanceHeader.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors, changeColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function BalanceHeader({
  greeting, title, value, changeAmount, changePercent, change,
}: {
  greeting: string; title: string; value: string;
  changeAmount: string; changePercent: string; change: number;
}) {
  const dark = useColorScheme() === 'dark';
  const primary = dark ? colors.darkTextPrimary : colors.textPrimary;
  const secondary = dark ? colors.darkTextSecondary : colors.textSecondary;
  const c = changeColor(change, dark);

  return (
    <View style={{ paddingHorizontal: 20, paddingBottom: 16 }}>
      <Text style={[typography.caption, { color: secondary }]}>{greeting}</Text>
      <Text style={[typography.screenTitle, { color: primary, marginTop: 2 }]}>{title}</Text>
      <Text style={[typography.caption, { color: secondary, marginTop: 18 }]}>Total account value</Text>
      <Text style={[typography.balanceHero, { color: primary, marginTop: 4 }]}>{value}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', marginTop: 8 }}>
        <Text style={{ color: c, fontSize: 10 }}>{change >= 0 ? '▲ ' : '▼ '}</Text>
        <Text style={[typography.numericMono, { color: c }]}>{changeAmount} </Text>
        <Text style={[typography.numericMono, { color: secondary }]}>({changePercent}) today</Text>
      </View>
    </View>
  );
}
```

### Holding / Watchlist Row

```tsx
// components/HoldingRow.tsx
import { Pressable, Text, View, useColorScheme } from 'react-native';
import { colors, changeColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function HoldingRow({
  symbol, name, value, changePct, change, isBrandFund = false,
}: {
  symbol: string; name: string; value: string;
  changePct: string; change: number; isBrandFund?: boolean;
}) {
  const dark = useColorScheme() === 'dark';
  const primary = dark ? colors.darkTextPrimary : colors.textPrimary;
  const secondary = dark ? colors.darkTextSecondary : colors.textSecondary;
  const chipBg = isBrandFund ? 'rgba(54,135,39,0.16)' : (dark ? colors.darkSurface2 : colors.surface);
  const divider = dark ? colors.darkDivider : colors.divider;

  return (
    <Pressable
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 12,
        paddingHorizontal: 20, height: 64,
        borderBottomWidth: 0.5, borderBottomColor: divider,
        backgroundColor: pressed ? (dark ? colors.darkSurface2 : colors.surfacePressed) : 'transparent',
      })}
    >
      <View style={{ width: 38, height: 38, borderRadius: 10, backgroundColor: chipBg, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 12, color: isBrandFund ? colors.leaf : primary }}>{symbol}</Text>
      </View>
      <View style={{ flex: 1, minWidth: 0 }}>
        <Text style={[typography.rowSymbol, { color: primary }]}>{symbol}</Text>
        <Text style={[typography.caption, { color: secondary }]} numberOfLines={1}>{name}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={[typography.rowValue, { color: primary }]}>{value}</Text>
        <Text style={[typography.numericMono, { color: changeColor(change, dark) }]}>{changePct}</Text>
      </View>
    </Pressable>
  );
}
```

### Sparkline + Range Tabs

```tsx
// components/Sparkline.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Svg, { Path, Defs, LinearGradient, Stop } from 'react-native-svg';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Sparkline({ points, isUp, width = 320, height = 64 }: {
  points: number[]; isUp: boolean; width?: number; height?: number;
}) {
  const stroke = isUp ? colors.leaf : colors.lossDark;
  const step = width / Math.max(points.length - 1, 1);
  const line = points.map((v, i) => `${i === 0 ? 'M' : 'L'}${i * step} ${height - v * height}`).join(' ');
  const area = `${line} L${width} ${height} L0 ${height} Z`;
  return (
    <Svg width="100%" height={height} viewBox={`0 0 ${width} ${height}`} preserveAspectRatio="none">
      <Defs>
        <LinearGradient id="fidFill" x1="0" y1="0" x2="0" y2="1">
          <Stop offset="0" stopColor={stroke} stopOpacity={0.32} />
          <Stop offset="1" stopColor={stroke} stopOpacity={0} />
        </LinearGradient>
      </Defs>
      <Path d={area} fill="url(#fidFill)" />
      <Path d={line} stroke={stroke} strokeWidth={2} fill="none" strokeLinejoin="round" />
    </Svg>
  );
}

export function RangeTabs() {
  const [sel, setSel] = useState(0);
  const ranges = ['1D', '1W', '1M', '1Y', '5Y', 'All'];
  return (
    <View style={{ flexDirection: 'row', gap: 6 }}>
      {ranges.map((r, i) => (
        <Pressable
          key={r}
          onPress={() => { setSel(i); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); }}
          style={{ paddingHorizontal: 9, paddingVertical: 3, borderRadius: 999, backgroundColor: i === sel ? colors.green : 'transparent' }}
        >
          <Text style={[typography.rangeTab, { color: i === sel ? '#FFFFFF' : colors.darkTextSecondary }]}>{r}</Text>
        </Pressable>
      ))}
    </View>
  );
}
```

### Sticky Trade Button

```tsx
// components/StickyTradeBar.tsx
import { Pressable, Text, View } from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StickyTradeBar({ onPress }: { onPress: () => void }) {
  const insets = useSafeAreaInsets();
  return (
    <View style={{ borderTopWidth: 0.5, borderTopColor: colors.darkDivider, backgroundColor: 'rgba(14,20,17,0.94)', paddingBottom: insets.bottom }}>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
        style={({ pressed }) => ({
          marginHorizontal: 16, marginTop: 12, height: 52, borderRadius: 8,
          alignItems: 'center', justifyContent: 'center',
          backgroundColor: pressed ? colors.greenPressed : colors.green,
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}
      >
        <Text style={[typography.button, { color: '#FFFFFF' }]}>Trade</Text>
      </Pressable>
    </View>
  );
}
```

### Performance Pill & Quote Card

```tsx
// components/PerformancePill.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PerformancePill({ kind, text }: { kind: 'gain' | 'loss' | 'flat'; text: string }) {
  const map = {
    gain: { fg: colors.gainDark, bg: 'rgba(21,179,116,0.16)', pre: '▲ ' },
    loss: { fg: colors.lossDark, bg: 'rgba(229,84,75,0.16)', pre: '▼ ' },
    flat: { fg: colors.darkTextSecondary, bg: colors.darkSurface2, pre: '— ' },
  }[kind];
  return (
    <View style={{ alignSelf: 'flex-start', paddingHorizontal: 14, paddingVertical: 8, borderRadius: 8, backgroundColor: map.bg }}>
      <Text style={[typography.numericMono, { color: map.fg }]}>{map.pre}{text}</Text>
    </View>
  );
}
```

```tsx
// components/QuoteCard.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors, changeColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function QuoteCard({ symbol, name, price, pct, change }: {
  symbol: string; name: string; price: string; pct: string; change: number;
}) {
  const dark = useColorScheme() === 'dark';
  const bg = dark ? colors.darkSurface2 : colors.surface;
  const primary = dark ? colors.darkTextPrimary : colors.textPrimary;
  const secondary = dark ? colors.darkTextSecondary : colors.textSecondary;
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', backgroundColor: bg, borderRadius: 12, paddingVertical: 14, paddingHorizontal: 16 }}>
      <View>
        <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 16, color: primary }}>{symbol}</Text>
        <Text style={[typography.caption, { color: secondary, marginTop: 2 }]}>{name}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={[typography.rowValue, { color: primary }]}>{price}</Text>
        <Text style={[typography.caption, { color: changeColor(change, dark), fontFamily: 'Inter-Bold', marginTop: 2 }]}>{pct}</Text>
      </View>
    </View>
  );
}
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
        tabBarActiveTintColor: colors.leaf,
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Summary',  tabBarIcon: ({ color }) => <Ionicons name="home"             size={22} color={color} /> }} />
      <Tabs.Screen name="planning" options={{ title: 'Planning', tabBarIcon: ({ color }) => <Ionicons name="bar-chart"        size={22} color={color} /> }} />
      <Tabs.Screen name="invest"   options={{ title: 'Invest',   tabBarIcon: ({ color }) => <Ionicons name="trending-up"      size={22} color={color} /> }} />
      <Tabs.Screen name="news"     options={{ title: 'News',     tabBarIcon: ({ color }) => <Ionicons name="newspaper"        size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle"    size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Price tick — animate the COLOR only (digits never roll)
import { useSharedValue, withSequence, withTiming } from 'react-native-reanimated';
// flash to gain/loss then settle to primary text over 600ms

// Chart range morph — re-render Path with LayoutAnimation or animate the `d` via reanimated-svg over 280ms ease-out

// Sticky Trade bar — always rendered above the tab bar / safe area; no entrance animation

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // tab / range change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // tap Trade / Place order
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // order filled
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For directional gain/loss glyphs prefer the Unicode triangles `▲ ▼` so the sign is never lost on monochrome.

| Purpose | Ionicons |
|---------|----------|
| Summary | `home` |
| Planning | `bar-chart` |
| Invest | `trending-up` |
| News | `newspaper` |
| Profile | `person-circle` |
| Day change up | `caret-up` (or `▲`) |
| Day change down | `caret-down` (or `▼`) |
| Watchlist toggle | `star-outline` / `star` |
| Search | `search` |
| Back | `chevron-back` |
| Overflow | `ellipsis-horizontal` |
| Trade | `swap-horizontal` |
| Transfer | `arrow-down-circle` |
| Alerts | `notifications-outline` |
| Disclosure | `chevron-forward` |
| Allocation ring | `pie-chart` |
| Documents | `document-text-outline` |

## 7. Platform Notes

- **Font choice**: Inter (SIL OFL) is the closest free substitute for "Average Sans"; bundle Regular→ExtraBold. All money values must carry `fontVariant: ['tabular-nums']`.
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark.
- **Safe area**: wrap screens in `SafeAreaView`; the sticky Trade bar and tab bar both need bottom safe-area padding. During trade-ticket entry, lift the sticky bar with `KeyboardAvoidingView`.
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface*` / `darkDivider`; brand action stays `green`, links/selected brighten to `leaf`. Gain/loss swap to the brighter dark pair but meaning is fixed.
- **Accessibility**: never rely on color alone — keep the `+`/`−` sign and `▲`/`▼` glyph. Set `accessibilityLabel` on rows combining symbol + value + signed change. Set `allowFontScaling={false}` on tab labels, range tabs, and chart-axis labels.
- **Charts**: `react-native-svg` for sparklines; for an interactive scrub crosshair use a `PanGestureHandler` mapping touch X to the nearest data index and rendering a vertical line + tabular value bubble.
- **Number formatting**: format currency/percentages with `Intl.NumberFormat` once, then render the string — keep the tabular style on the `Text`.
- **Reduce Motion**: respect `AccessibilityInfo.isReduceMotionEnabled()` — skip the chart morph and price-tick color flash; set final values instantly while keeping the sign/arrow.
