# Binance (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Binance's dark trading-terminal language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default)
  canvas:    '#0B0E11',
  surface1:  '#181A20',
  surface2:  '#1E2026',
  surface3:  '#2B3139',
  divider:   '#2B3139',

  // Surfaces (light — secondary)
  canvasLight:   '#FFFFFF',
  surface1Light: '#F5F5F5',
  dividerLight:  '#EAECEF',

  // Brand (single accent)
  yellow:        '#F0B90B',
  yellowPressed: '#C99400',
  yellowTint:    'rgba(240,185,11,0.12)',

  // Market semantics (never invert)
  up:          '#0ECB81',
  down:        '#F6465D',
  upPressed:   '#0BA572',
  downPressed: '#D93849',
  askFill:     'rgba(246,70,93,0.14)',
  bidFill:     'rgba(14,203,129,0.14)',

  // Text
  textPrimary:    '#EAECEF',
  textSecondary:  '#848E9C',
  textTertiary:   '#5E6673',
  textPrimaryLt:  '#1E2329',

  // Coin brand colors
  btc: '#F7931A',
  eth: '#627EEA',
  bnb: '#F0B90B',
  usdt:'#26A17B',
  sol: '#14F195',

  // Info
  info: '#3375BB',
} as const;

export type BinanceColor = keyof typeof colors;
```

## 2. Typography

Load **IBM Plex Sans** (UI) + **IBM Plex Mono** (numbers) via `expo-font`. Rule: words use the sans, all numbers use the mono with tabular figures (`fontVariant: ['tabular-nums']`).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'IBMPlexSans-Regular':  require('../assets/fonts/IBMPlexSans-Regular.ttf'),
    'IBMPlexSans-Medium':   require('../assets/fonts/IBMPlexSans-Medium.ttf'),
    'IBMPlexSans-SemiBold': require('../assets/fonts/IBMPlexSans-SemiBold.ttf'),
    'IBMPlexSans-Bold':     require('../assets/fonts/IBMPlexSans-Bold.ttf'),
    'IBMPlexMono-Medium':   require('../assets/fonts/IBMPlexMono-Medium.ttf'),
    'IBMPlexMono-SemiBold': require('../assets/fonts/IBMPlexMono-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tabular = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  // UI sans
  screenTitle: { fontFamily: 'IBMPlexSans-Bold',     fontSize: 32, lineHeight: 38, letterSpacing: -0.6, color: '#EAECEF' },
  section:     { fontFamily: 'IBMPlexSans-Bold',     fontSize: 22, lineHeight: 28, letterSpacing: -0.2, color: '#EAECEF' },
  rowTitle:    { fontFamily: 'IBMPlexSans-SemiBold', fontSize: 18, lineHeight: 23, color: '#EAECEF' },
  body:        { fontFamily: 'IBMPlexSans-Regular',  fontSize: 16, lineHeight: 24, color: '#EAECEF' },
  listLabel:   { fontFamily: 'IBMPlexSans-SemiBold', fontSize: 14, lineHeight: 18, color: '#EAECEF' },
  meta:        { fontFamily: 'IBMPlexSans-Regular',  fontSize: 14, lineHeight: 19, color: '#848E9C' },
  pill:        { fontFamily: 'IBMPlexSans-SemiBold', fontSize: 13, lineHeight: 13, color: '#FFFFFF' },
  caption:     { fontFamily: 'IBMPlexSans-Medium',   fontSize: 12, lineHeight: 16, color: '#848E9C' },
  tab:         { fontFamily: 'IBMPlexSans-Medium',   fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:      { fontFamily: 'IBMPlexSans-SemiBold', fontSize: 15, lineHeight: 15 },

  // Numeric mono (always tabular)
  balance:     { ...tabular, fontFamily: 'IBMPlexMono-SemiBold', fontSize: 28, lineHeight: 32, letterSpacing: -0.5, color: '#EAECEF' },
  price:       { ...tabular, fontFamily: 'IBMPlexMono-SemiBold', fontSize: 15, lineHeight: 20, color: '#EAECEF' },
  number:      { ...tabular, fontFamily: 'IBMPlexMono-SemiBold', fontSize: 14, lineHeight: 18, color: '#EAECEF' },
  monoCaption: { ...tabular, fontFamily: 'IBMPlexMono-Medium',   fontSize: 12, lineHeight: 16, color: '#848E9C' },
  monoSmall:   { ...tabular, fontFamily: 'IBMPlexMono-Medium',   fontSize: 10, lineHeight: 14, color: '#848E9C' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Markets List Row

```tsx
// components/MarketRow.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MarketRow({
  symbol, quote, volume, price, usd, changePct, iconColor = colors.btc,
}: {
  symbol: string; quote: string; volume: string;
  price: string; usd: string; changePct: number; iconColor?: string;
}) {
  const up = changePct >= 0;
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10,
      paddingHorizontal: 16, paddingVertical: 12,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{ width: 30, height: 30, borderRadius: 15, backgroundColor: iconColor,
        alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'IBMPlexSans-Bold', fontSize: 13, color: '#0B0E11' }}>
          {symbol[0]}
        </Text>
      </View>

      <View style={{ flex: 1 }}>
        <Text>
          <Text style={typography.listLabel}>{symbol}</Text>
          <Text style={{ fontFamily: 'IBMPlexSans-Medium', fontSize: 11, color: colors.textTertiary }}>
            {' '}/{quote}
          </Text>
        </Text>
        <Text style={{ fontFamily: 'IBMPlexSans-Regular', fontSize: 11, color: colors.textSecondary }}>
          Vol {volume}
        </Text>
      </View>

      <View style={{ alignItems: 'flex-end' }}>
        <Text style={typography.number}>{price}</Text>
        <Text style={typography.monoSmall}>{usd}</Text>
      </View>

      <View style={{
        minWidth: 64, paddingVertical: 6, borderRadius: 4, alignItems: 'center',
        backgroundColor: up ? colors.up : colors.down, marginLeft: 8,
      }}>
        <Text style={typography.pill}>{up ? '+' : ''}{changePct.toFixed(2)}%</Text>
      </View>
    </View>
  );
}
```

### Order Book (depth-shaded)

```tsx
// components/OrderBookRow.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function OrderBookRow({
  price, qty, depthRatio, isAsk,
}: { price: string; qty: string; depthRatio: number; isAsk: boolean }) {
  return (
    <View style={{ height: 22, justifyContent: 'center', overflow: 'hidden' }}>
      <View style={{
        position: 'absolute', right: 0, top: 0, bottom: 0,
        width: `${depthRatio * 100}%`,
        backgroundColor: isAsk ? colors.askFill : colors.bidFill,
      }} />
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', paddingHorizontal: 8 }}>
        <Text style={[typography.monoCaption, { color: isAsk ? colors.down : colors.up }]}>{price}</Text>
        <Text style={[typography.monoCaption, { color: colors.textPrimary }]}>{qty}</Text>
      </View>
    </View>
  );
}

export function SpreadRow({ last, up }: { last: string; up: boolean }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 8, paddingVertical: 6 }}>
      <Text style={[typography.price, { fontSize: 16, color: up ? colors.up : colors.down }]}>{last}</Text>
      <Text style={{ color: up ? colors.up : colors.down, fontSize: 12 }}>{up ? '↑' : '↓'}</Text>
    </View>
  );
}
```

### Balance Hero

```tsx
// components/BalanceHero.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BalanceHero({
  value, currency, pnl, gain,
}: { value: string; currency: string; pnl: string; gain: boolean }) {
  return (
    <View style={{ paddingHorizontal: 16, gap: 4 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Text style={typography.caption}>Est. Total Value</Text>
        <Ionicons name="eye-outline" size={12} color={colors.textSecondary} />
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 4 }}>
        <Text style={typography.balance}>{value}</Text>
        <Text style={{ fontFamily: 'IBMPlexSans-Medium', fontSize: 14, color: colors.textSecondary, marginBottom: 4 }}>
          {currency}
        </Text>
      </View>
      <Text style={[typography.monoCaption, { color: gain ? colors.up : colors.down }]}>
        {pnl} Today
      </Text>
    </View>
  );
}
```

### Buy / Sell Trade Ticket

```tsx
// components/TradeTicket.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TradeTicket() {
  const [isBuy, setIsBuy] = useState(true);
  const [pct, setPct] = useState(0);
  const accent = isBuy ? colors.up : colors.down;

  return (
    <View style={{ padding: 16, backgroundColor: colors.surface1, gap: 12 }}>
      <View style={{ flexDirection: 'row', borderRadius: 8, overflow: 'hidden' }}>
        {[true, false].map((buy) => (
          <Pressable key={String(buy)} onPress={() => setIsBuy(buy)}
            style={{ flex: 1, height: 36, alignItems: 'center', justifyContent: 'center',
              backgroundColor: isBuy === buy ? (buy ? colors.up : colors.down) : colors.surface3 }}>
            <Text style={[typography.button, { color: isBuy === buy ? '#FFF' : colors.textSecondary }]}>
              {buy ? 'Buy' : 'Sell'}
            </Text>
          </Pressable>
        ))}
      </View>

      <View style={{ flexDirection: 'row', gap: 8 }}>
        {[25, 50, 75, 100].map((p) => (
          <Pressable key={p} onPress={() => setPct(p)}
            style={{ flex: 1, paddingVertical: 7, borderRadius: 500, alignItems: 'center',
              backgroundColor: pct === p ? colors.yellowTint : colors.surface3 }}>
            <Text style={{ fontFamily: 'IBMPlexSans-SemiBold', fontSize: 13,
              color: pct === p ? colors.yellow : colors.textSecondary }}>{p}%</Text>
          </Pressable>
        ))}
      </View>

      <Pressable style={{ height: 48, borderRadius: 8, alignItems: 'center', justifyContent: 'center', backgroundColor: accent }}>
        <Text style={[typography.button, { color: '#FFF' }]}>{isBuy ? 'Buy BTC' : 'Sell BTC'}</Text>
      </Pressable>
    </View>
  );
}
```

### Primary (Yellow) Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        height: 48, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.yellowPressed : colors.yellow,
      })}>
      <Text style={[typography.button, { color: colors.canvas }]}>{title}</Text>
    </Pressable>
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
        tabBarActiveTintColor: colors.yellow,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopWidth: 1,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'IBMPlexSans-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="markets" options={{ title: 'Markets', tabBarIcon: ({ color }) => <Ionicons name="stats-chart"   size={22} color={color} /> }} />
      <Tabs.Screen name="trade"   options={{ title: 'Trade',   tabBarIcon: ({ color }) => <Ionicons name="swap-horizontal" size={22} color={color} /> }} />
      <Tabs.Screen name="futures" options={{ title: 'Futures', tabBarIcon: ({ color }) => <Ionicons name="trending-up"    size={22} color={color} /> }} />
      <Tabs.Screen name="wallets" options={{ title: 'Wallets', tabBarIcon: ({ color }) => <Ionicons name="wallet"         size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSequence } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Price tick flash — call on every price update
const flash = useSharedValue(0);
function tick(up: boolean) {
  flash.value = withSequence(withTiming(1, { duration: 40 }), withTiming(0, { duration: 150 }));
}
// const flashStyle = useAnimatedStyle(() => ({
//   backgroundColor: `rgba(${up ? '14,203,129' : '246,70,93'},${flash.value * 0.1})`,
// }));

// Markets tab underline slide — easeOut 200ms (no spring)
underlineX.value = withTiming(targetX, { duration: 200 });

// Buy/Sell toggle color-morph — 180ms
segmentX.value = withTiming(isBuy ? 0 : 1, { duration: 180 });

// Order book reflow — quick, NO spring (data must feel stable)
// Use LayoutAnimation.easeInEaseOut() or withTiming(120) on row positions

// Convert swap — 250ms vertical flip + soft haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Haptics
Haptics.selectionAsync();                                   // percentage chip snap
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // order filled
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);     // order submit
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Coin glyphs are best rendered as bundled SVGs via `react-native-svg`.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Markets | `stats-chart` |
| Trade | `swap-horizontal` |
| Futures | `trending-up` |
| Wallets | `wallet` |
| Search | `search` |
| Notifications | `notifications-outline` |
| Hide balance | `eye-outline` / `eye-off-outline` |
| Favorite | `star` / `star-outline` |
| Back | `chevron-back` |
| Convert swap | `swap-vertical` |
| Up tick | `arrow-up` |
| Down tick | `arrow-down` |
| Deposit | `arrow-down-circle-outline` |
| Withdraw | `arrow-up-circle-outline` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Chart type | `bar-chart-outline` |
| Settings | `settings-outline` |
| Filter | `options-outline` |

## 7. Platform Notes

- **Fonts**: IBM Plex Sans + IBM Plex Mono are SIL OFL — free to bundle. Substitute Binance's custom `BinancePlex` if you have a license; the Plex pair is a faithful open stand-in
- **Tabular numbers**: set `fontVariant: ['tabular-nums']` on every numeric `<Text>` (already in the `tabular` typography tokens). Without it, markets prices and the order book visibly jitter as digits change
- **Status bar**: `<StatusBar style="light" />` — Binance is dark-native; only switch to `"dark"` if you ship the light theme
- **Safe area**: wrap screens in `SafeAreaView`; the sticky trade ticket needs bottom safe-area padding so it clears the home indicator
- **Dynamic Type**: set `allowFontScaling={false}` on numeric mono columns, % pills, order-book rows, and tab labels to preserve tabular column alignment; allow scaling on titles/body/labels
- **Live data**: throttle price-tick re-renders (e.g. 4–10 Hz) and use `React.memo` on `MarketRow` keyed by symbol so a list of 100 pairs stays at 60fps; only flash rows whose price actually changed
- **Keyboard**: use `KeyboardAvoidingView` on the trade ticket so the percentage slider and submit button stay visible above the keyboard
- **Charts**: use `react-native-wagmi-charts` or a custom `react-native-skia` candlestick renderer; bullish `#0ECB81`, bearish `#F6465D`, grid `#2B3139` @ 40%
- **Dark mode**: default to dark; if supporting light, swap only `canvas`→`#FFFFFF` and `textPrimary`→`#1E2329` via `useColorScheme()` — never invert `up`/`down`/`yellow`
- **Accessibility**: pair every colored % with a `+`/`−` and an arrow glyph; set `accessibilityLabel` on market rows ("Bitcoin, 67,284 dollars, up 2.34 percent"); avoid announcing every tick to VoiceOver
