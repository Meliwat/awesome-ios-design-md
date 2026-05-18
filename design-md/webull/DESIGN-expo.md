# Webull (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Webull's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Core rules: **dark-native**, **every numeric uses `fontVariant: ['tabular-nums']`**, **up `#00C076` / down `#FA5252` everywhere**, **blue→cyan gradient = brand/action only**.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, `react-native-gesture-handler`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  blue:        '#1B9EFB',
  cyan:        '#20D5C4',
  bluePressed: '#1684D6',
  onBrand:     '#04121C',
  gradient:    ['#1B9EFB', '#20D5C4'] as const,

  // Canvas & surfaces (dark — the only mode)
  canvas:   '#0B0E11',
  surface1: '#14181D',
  surface2: '#1C2127',
  divider:  '#232931',
  gridLine: '#1A1F26',

  // Text
  textPrimary:   '#EAEEF2',
  textSecondary: '#8B95A1',
  textTertiary:  '#5A636E',

  // Market semantics (loud & pervasive)
  up:       '#00C076',
  down:     '#FA5252',
  onUp:     '#03150D',
  onDown:   '#1B0606',
  upFill:   'rgba(0,192,118,0.14)',
  downFill: 'rgba(250,82,82,0.14)',

  // System / accent
  amber: '#F7A600',
} as const;

export type WBColor = keyof typeof colors;

// US default: up=green, down=red. Region setting may invert (Asia markets).
export function wbColor(change: number, upIsGreen = true) {
  if (change === 0) return colors.textSecondary;
  const positive = change > 0;
  const green = upIsGreen ? positive : !positive;
  return green ? colors.up : colors.down;
}
```

## 2. Typography

Load **Inter** (SIL OFL) with tabular figures. The defining rule: every numeric gets `fontVariant: ['tabular-nums']` so the ladder and option chain align to the decimal.

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
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

// Apply to ALL numeric values
export const tnum: TextStyle = { fontVariant: ['tabular-nums'] };

export const typography = {
  lastPrice:   { fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 36, letterSpacing: -0.6, ...tnum },
  screenTitle: { fontFamily: 'Inter-ExtraBold', fontSize: 24, lineHeight: 29, letterSpacing: -0.3 },
  section:     { fontFamily: 'Inter-Bold',      fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  symbol:      { fontFamily: 'Inter-Bold',      fontSize: 17, lineHeight: 22 },
  body:        { fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 23 },
  rowSymbol:   { fontFamily: 'Inter-SemiBold',  fontSize: 14, lineHeight: 18 },
  rowValue:    { fontFamily: 'Inter-Bold',      fontSize: 15, lineHeight: 20, ...tnum },
  meta:        { fontFamily: 'Inter-Regular',   fontSize: 13, lineHeight: 18 },
  numericMono: { fontFamily: 'Inter-Bold',      fontSize: 12, lineHeight: 16, ...tnum },
  ladder:      { fontFamily: 'Inter-Bold',      fontSize: 13, lineHeight: 17, ...tnum },
  label:       { fontFamily: 'Inter-SemiBold',  fontSize: 11, lineHeight: 11, letterSpacing: 0.4 },
  button:      { fontFamily: 'Inter-ExtraBold', fontSize: 15, lineHeight: 15, letterSpacing: 0.3 },
  tab:         { fontFamily: 'Inter-SemiBold',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  timeframe:   { fontFamily: 'Inter-Bold',      fontSize: 11, lineHeight: 11, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Quote Header

```tsx
// components/QuoteHeader.tsx
import { Text, View } from 'react-native';
import { colors, wbColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function QuoteHeader({
  symbol, name, price, delta, change, isPaper,
}: {
  symbol: string; name: string; price: string; delta: string; change: number; isPaper?: boolean;
}) {
  const c = wbColor(change);
  return (
    <View style={{ backgroundColor: colors.canvas, paddingHorizontal: 18, paddingTop: 4, paddingBottom: 10 }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'flex-start' }}>
        <View>
          <Text style={{ ...typography.section, fontSize: 20, color: colors.textPrimary }}>{symbol}</Text>
          <Text style={[typography.meta, { color: colors.textSecondary, marginTop: 1 }]}>{name}</Text>
        </View>
        {isPaper && (
          <View style={{ backgroundColor: colors.cyan, borderRadius: 4, paddingHorizontal: 8, paddingVertical: 3 }}>
            <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 10, color: colors.onBrand }}>PAPER</Text>
          </View>
        )}
      </View>
      <Text style={[typography.lastPrice, { color: c, marginTop: 12 }]}>{price}</Text>
      <Text style={[typography.rowValue, { color: c, marginTop: 4 }]}>{delta} {change >= 0 ? '▲' : '▼'}</Text>
    </View>
  );
}
```

### Candlestick Chart + Timeframe Strip

```tsx
// components/CandleChart.tsx
import Svg, { Line, Rect } from 'react-native-svg';
import { colors } from '../theme/colors';

type Candle = { o: number; h: number; l: number; c: number };

export function CandleChart({
  candles, prevClose, min, max, width = 320, height = 158,
}: {
  candles: Candle[]; prevClose: number; min: number; max: number; width?: number; height?: number;
}) {
  const step = width / candles.length;
  const y = (v: number) => height - ((v - min) / (max - min)) * height;
  return (
    <Svg width="100%" height={height} viewBox={`0 0 ${width} ${height}`} preserveAspectRatio="none">
      {[1, 2, 3].map((i) => (
        <Line key={i} x1={0} y1={(height * i) / 4} x2={width} y2={(height * i) / 4} stroke={colors.gridLine} strokeWidth={1} />
      ))}
      <Line x1={0} y1={y(prevClose)} x2={width} y2={y(prevClose)} stroke={colors.blue} strokeWidth={1} strokeDasharray="3 3" />
      {candles.map((c, i) => {
        const x = step * (i + 0.5);
        const up = c.c >= c.o;
        const col = up ? colors.up : colors.down;
        return (
          <React.Fragment key={i}>
            <Line x1={x} y1={y(c.h)} x2={x} y2={y(c.l)} stroke={col} strokeWidth={1.5} />
            <Rect x={x - step * 0.3} y={Math.min(y(c.o), y(c.c))} width={step * 0.6} height={Math.max(Math.abs(y(c.o) - y(c.c)), 1)} fill={col} />
          </React.Fragment>
        );
      })}
    </Svg>
  );
}
```

```tsx
// components/TimeframeStrip.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TimeframeStrip() {
  const [sel, setSel] = useState(2);
  const tfs = ['1m', '5m', '1D', '5D', '1M', '1Y'];
  return (
    <View style={{ flexDirection: 'row', gap: 4, paddingHorizontal: 14, paddingTop: 10 }}>
      {tfs.map((t, i) => (
        <Pressable
          key={t}
          onPress={() => { setSel(i); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); }}
          style={{ paddingHorizontal: 10, paddingVertical: 5, borderRadius: 4, backgroundColor: i === sel ? 'rgba(27,158,251,0.16)' : 'transparent' }}
        >
          <Text style={[typography.timeframe, { color: i === sel ? colors.blue : colors.textSecondary }]}>{t}</Text>
        </Pressable>
      ))}
    </View>
  );
}
```

### Order-Book Ladder

```tsx
// components/OrderLadder.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Level = { price: string; size: string; frac: number };

function LadderRow({ level, side }: { level: Level; side: 'bid' | 'ask' }) {
  return (
    <View style={{ height: 22, justifyContent: 'center' }}>
      <View style={{ position: 'absolute', right: 0, top: 3, bottom: 3, width: `${level.frac * 100}%`, backgroundColor: side === 'bid' ? colors.upFill : colors.downFill, borderRadius: 2 }} />
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}>
        <Text style={[typography.numericMono, { width: 64, color: side === 'bid' ? colors.up : colors.down }]}>{level.price}</Text>
        <Text style={[typography.numericMono, { color: colors.textSecondary }]}>{level.size}</Text>
      </View>
    </View>
  );
}

export function OrderLadder({ asks, bids, mid, spread }: {
  asks: Level[]; bids: Level[]; mid: string; spread: string;
}) {
  return (
    <View style={{ paddingHorizontal: 18, paddingTop: 12 }}>
      {asks.map((l, i) => <LadderRow key={`a${i}`} level={l} side="ask" />)}
      <View style={{ borderTopWidth: 0.5, borderBottomWidth: 0.5, borderColor: colors.divider, marginVertical: 4, paddingVertical: 6, alignItems: 'center' }}>
        <Text style={[typography.ladder, { color: colors.textPrimary }]}>{mid}  ·  Spread {spread}</Text>
      </View>
      {bids.map((l, i) => <LadderRow key={`b${i}`} level={l} side="bid" />)}
    </View>
  );
}
```

### Docked Buy / Sell Pair + Watchlist Row

```tsx
// components/BuySellBar.tsx
import { Pressable, Text, View } from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BuySellBar({ onBuy, onSell }: { onBuy: () => void; onSell: () => void }) {
  const insets = useSafeAreaInsets();
  const tap = () => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
  return (
    <View style={{ flexDirection: 'row', gap: 10, paddingHorizontal: 18, paddingTop: 12, paddingBottom: insets.bottom + 12, backgroundColor: 'rgba(11,14,17,0.94)' }}>
      <Pressable
        onPress={() => { tap(); onBuy(); }}
        style={({ pressed }) => ({ flex: 1, height: 48, borderRadius: 6, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.up, opacity: pressed ? 0.9 : 1, transform: [{ scale: pressed ? 0.98 : 1 }] })}
      >
        <Text style={[typography.button, { color: colors.onUp }]}>Buy</Text>
      </Pressable>
      <Pressable
        onPress={() => { tap(); onSell(); }}
        style={({ pressed }) => ({ flex: 1, height: 48, borderRadius: 6, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.down, opacity: pressed ? 0.9 : 1, transform: [{ scale: pressed ? 0.98 : 1 }] })}
      >
        <Text style={[typography.button, { color: colors.onDown }]}>Sell</Text>
      </Pressable>
    </View>
  );
}
```

```tsx
// components/WatchlistRow.tsx
import { Text, View } from 'react-native';
import Svg, { Path } from 'react-native-svg';
import { colors, wbColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchlistRow({ symbol, name, price, pct, change, spark }: {
  symbol: string; name: string; price: string; pct: string; change: number; spark: number[];
}) {
  const up = change >= 0;
  const step = 56 / Math.max(spark.length - 1, 1);
  const d = spark.map((v, i) => `${i === 0 ? 'M' : 'L'}${i * step} ${28 - v * 28}`).join(' ');
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 12, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View style={{ flex: 1, minWidth: 0 }}>
        <Text style={[typography.rowSymbol, { color: colors.textPrimary }]}>{symbol}</Text>
        <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.textSecondary, marginTop: 2 }} numberOfLines={1}>{name}</Text>
      </View>
      <Svg width={56} height={28} viewBox="0 0 56 28" preserveAspectRatio="none">
        <Path d={d} stroke={up ? colors.up : colors.down} strokeWidth={2} fill="none" />
      </Svg>
      <View style={{ alignItems: 'flex-end', minWidth: 78 }}>
        <Text style={[typography.rowValue, { color: colors.textPrimary }]}>{price}</Text>
        <View style={{ marginTop: 4, paddingHorizontal: 7, paddingVertical: 3, borderRadius: 4, backgroundColor: wbColor(change) }}>
          <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 11, fontVariant: ['tabular-nums'], color: up ? colors.onUp : colors.onDown }}>{pct}</Text>
        </View>
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
        tabBarActiveTintColor: colors.blue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="markets"   options={{ title: 'Markets',   tabBarIcon: ({ color }) => <Ionicons name="search"          size={22} color={color} /> }} />
      <Tabs.Screen name="index"     options={{ title: 'Quotes',    tabBarIcon: ({ color }) => <Ionicons name="stats-chart"     size={22} color={color} /> }} />
      <Tabs.Screen name="trade"     options={{ title: 'Trade',     tabBarIcon: ({ color }) => <Ionicons name="cash-outline"    size={22} color={color} /> }} />
      <Tabs.Screen name="portfolio" options={{ title: 'Portfolio', tabBarIcon: ({ color }) => <Ionicons name="bar-chart"       size={22} color={color} /> }} />
      <Tabs.Screen name="menu"      options={{ title: 'Menu',      tabBarIcon: ({ color }) => <Ionicons name="menu"            size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Live price tick — flash bright then settle to steady up/down color (NO digit roll)
// useSharedValue + withSequence(withTiming(1,{duration:250}), withTiming(0,{duration:250}))

// Chart timeframe switch — re-render candles; LayoutAnimation or reanimated over 250ms ease-out

// Crosshair scrub — PanGestureHandler mapping touch X → candle index; vertical+horizontal #8B95A1 lines + OHLC tooltip, 1:1, no easing

// Buy/Sell → ticket — present a bottom sheet (~280ms slide-up); confirm button inherits green/red

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // timeframe / tab
Haptics.selectionAsync();                                  // crosshair crossing key levels
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // order submit
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // fill
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For up/down direction prefer Unicode triangles `▲ ▼` so the sign survives monochrome (critical — the up/down→color map can invert by region).

| Purpose | Ionicons |
|---------|----------|
| Markets | `search` |
| Quotes | `stats-chart` |
| Trade | `cash-outline` |
| Portfolio | `bar-chart` |
| Menu | `menu` |
| Watchlist star | `star-outline` / `star` |
| Search | `search` |
| Back | `chevron-back` |
| Overflow | `ellipsis-horizontal` |
| Up tick | `caret-up` (or `▲`) |
| Down tick | `caret-down` (or `▼`) |
| Alerts | `notifications-outline` |
| Indicators | `options-outline` |
| Chart fullscreen | `expand-outline` |
| Order type | `list-outline` |
| Quantity +/− | `add` / `remove` |
| Depth / level 2 | `bar-chart-outline` |

## 7. Platform Notes

- **Font choice**: Inter (SIL OFL) with tabular figures; bundle Regular→ExtraBold. The ladder and option chain MUST be tabular (`fontVariant: ['tabular-nums']`).
- **Dark-native**: force dark — set `userInterfaceStyle: 'dark'` in `app.json` and never offer a primary light theme. Canvas `#0B0E11` is intentional for chart contrast.
- **Status bar**: `<StatusBar style="light" />` always.
- **Safe area**: wrap screens in `SafeAreaView`; the chart is full-bleed; the docked Buy/Sell bar + tab bar need bottom safe-area padding.
- **Charts**: `react-native-svg` for candles; for live data throttle ticks to ~10Hz. Crosshair via `react-native-gesture-handler` `PanGestureHandler` mapping X→candle index; OHLC tooltip follows 1:1. Landscape full-screen chart is a core feature — use `expo-screen-orientation` to allow rotation on the chart screen only.
- **Accessibility**: never rely on color alone for up/down — keep `+`/`−` and `▲`/`▼` (essential because region settings can invert up/down color). Set `accessibilityLabel` on rows/ladder combining all parts. Set `allowFontScaling={false}` on ladder, option chain, timeframe chips, and tab labels.
- **Region setting**: provide a `upIsGreen` preference (default true US; many Asia markets use up=red). Pass it through `wbColor()` everywhere — never hardcode.
- **Number formatting**: format with `Intl.NumberFormat` once, render the string with the tabular style.
- **Reduce Motion**: respect `AccessibilityInfo.isReduceMotionEnabled()` — skip the timeframe re-layout and price flash; keep the sign/arrow and crosshair.
