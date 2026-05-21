# Coinbase (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Coinbase's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated/Gesture Handler snippets for the signature portfolio chart, asset row, and 4-up action quad.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `react-native-reanimated` v3, `react-native-gesture-handler`, and `react-native-svg` (for chart and sparklines).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  cbBlue:        '#0052FF',
  cbBluePressed: '#0040CC',
  cbBlueTint:    '#E5EDFF',
  cbBlueDark:    '#3B6CFF',
  cbBlack:       '#0A0B0D',
  cbCharcoal:    '#1A1C1F',

  // Crypto asset colors
  bitcoin:       '#F7931A',
  ethereum:      '#627EEA',
  usdc:          '#2775CA',
  solana:        '#9945FF',
  cardano:       '#0033AD',
  tether:        '#26A17B',

  // Canvas & surfaces (light)
  canvas:        '#FFFFFF',
  surfaceGray:   '#F7F8FA',
  surfaceGray2:  '#EEF0F3',
  divider:       '#E1E4E8',

  // Text (light)
  textPrimary:   '#0A0B0D',
  textSecondary: '#5B616E',
  textTertiary:  '#80868F',
  textMuted:     '#A0A4AA',

  // Semantic
  success:       '#05B169',
  successTint:   '#E6F7EF',
  loss:          '#CF202F',
  lossTint:      '#FCE7E9',
  warning:       '#F5A623',

  // Dark mode
  darkCanvas:    '#0A0B0D',
  darkSurface1:  '#13151A',
  darkSurface2:  '#1E2026',
  darkDivider:   '#2A2E36',
  darkTextPri:   '#FFFFFF',
  darkTextSec:   '#A0A4AA',
} as const;

export type CBColor = keyof typeof colors;
```

## 2. Typography

Coinbase Sans / Display / Mono are proprietary (Frere-Jones Type, ~2021). Bundle via `expo-font`; fall back to `Inter` + `JetBrains Mono` from Google Fonts with `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'CoinbaseSans-Regular':   require('../assets/fonts/CoinbaseSans-Regular.otf'),
    'CoinbaseSans-Medium':    require('../assets/fonts/CoinbaseSans-Medium.otf'),
    'CoinbaseSans-Semibold':  require('../assets/fonts/CoinbaseSans-Semibold.otf'),
    'CoinbaseSans-Bold':      require('../assets/fonts/CoinbaseSans-Bold.otf'),
    'CoinbaseDisplay-Bold':   require('../assets/fonts/CoinbaseDisplay-Bold.otf'),
    'CoinbaseMono-Regular':   require('../assets/fonts/CoinbaseMono-Regular.otf'),
    'CoinbaseMono-Medium':    require('../assets/fonts/CoinbaseMono-Medium.otf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tabular = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };
const black   = { color: '#0A0B0D' } satisfies TextStyle;
const gray    = { color: '#5B616E' } satisfies TextStyle;

export const typography = {
  // Hero (Display variant)
  portfolioHero: { ...black, ...tabular, fontFamily: 'CoinbaseDisplay-Bold', fontSize: 40, lineHeight: 40, letterSpacing: -0.5 },
  buyAmount:     { ...black, ...tabular, fontFamily: 'CoinbaseDisplay-Bold', fontSize: 56, lineHeight: 56, letterSpacing: -1 },
  screenTitle:   { ...black,             fontFamily: 'CoinbaseDisplay-Bold', fontSize: 28, lineHeight: 32, letterSpacing: -0.3 },

  // Sections & titles (Sans)
  sectionHeader: { ...black,             fontFamily: 'CoinbaseSans-Bold',     fontSize: 20, lineHeight: 23, letterSpacing: -0.2 },
  assetTitle:    { ...black,             fontFamily: 'CoinbaseSans-Semibold', fontSize: 16, lineHeight: 21 },

  // Prices & deltas
  assetPrice:    { ...black, ...tabular, fontFamily: 'CoinbaseSans-Medium',   fontSize: 16, lineHeight: 19 },
  assetChange:   {           ...tabular, fontFamily: 'CoinbaseSans-Medium',   fontSize: 13, lineHeight: 17 },

  // Body
  body:          { ...black, fontFamily: 'CoinbaseSans-Regular', fontSize: 15, lineHeight: 22 },
  bodySmall:     { ...black, fontFamily: 'CoinbaseSans-Regular', fontSize: 13, lineHeight: 18 },

  // Micro
  ticker:        { ...gray,  fontFamily: 'CoinbaseSans-Medium',   fontSize: 13, letterSpacing: 0.3 },
  tab:           {           fontFamily: 'CoinbaseSans-Semibold', fontSize: 10, letterSpacing: 0.1 },
  allCaps:       { ...gray,  fontFamily: 'CoinbaseSans-Bold',     fontSize: 11, letterSpacing: 0.6, textTransform: 'uppercase' },
  rangeChip:     { ...gray,  fontFamily: 'CoinbaseSans-Semibold', fontSize: 13, letterSpacing: 0.3 },

  // Buttons
  button:        { color: '#FFFFFF',     fontFamily: 'CoinbaseSans-Semibold', fontSize: 16, lineHeight: 19 },
  buttonSmall:   { ...black,             fontFamily: 'CoinbaseSans-Medium',   fontSize: 13, lineHeight: 16 },

  // Mono
  walletAddr:    { ...black,             fontFamily: 'CoinbaseMono-Medium',  fontSize: 13, lineHeight: 18 },
  txHash:        { ...black,             fontFamily: 'CoinbaseMono-Regular', fontSize: 12, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Portfolio Hero

```tsx
// components/PortfolioHero.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = { value: number; dayChange: number; dayChangePct: number };

export function PortfolioHero({ value, dayChange, dayChangePct }: Props) {
  const isUp  = dayChange >= 0;
  const sign  = isUp ? '+' : '−';
  const color = isUp ? colors.success : colors.loss;

  return (
    <View style={{ paddingHorizontal: 16, paddingTop: 16, gap: 8 }}>
      <Text style={typography.allCaps}>Portfolio Balance</Text>
      <Text style={typography.portfolioHero}>${value.toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })}</Text>
      <Text style={[typography.assetPrice, { color }]}>
        {sign}${Math.abs(dayChange).toLocaleString(undefined, { minimumFractionDigits: 2 })} ({sign}{Math.abs(dayChangePct * 100).toFixed(2)}%)
      </Text>
    </View>
  );
}
```

### Asset Row with Mini Sparkline

```tsx
// components/AssetRow.tsx
import { View, Text } from 'react-native';
import Svg, { Path } from 'react-native-svg';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  assetName: string;
  ticker: string;
  holdings: string;
  price: number;
  dayChange: number;
  dayChangePct: number;
  iconColor: string;
  glyph: string;
  sparkline: number[]; // 24h price series
};

export function AssetRow({ assetName, ticker, holdings, price, dayChange, dayChangePct, iconColor, glyph, sparkline }: Props) {
  const isUp  = dayChange >= 0;
  const color = isUp ? colors.success : colors.loss;
  const sign  = isUp ? '+' : '−';

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, height: 64, backgroundColor: colors.canvas, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      {/* Icon */}
      <View style={{ width: 40, height: 40, borderRadius: 20, backgroundColor: iconColor, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#FFFFFF', fontFamily: 'CoinbaseSans-Bold', fontSize: 18 }}>{glyph}</Text>
      </View>

      <View style={{ flex: 1 }}>
        <Text style={typography.assetTitle} numberOfLines={1}>{assetName}</Text>
        <Text style={typography.bodySmall}>
          <Text style={typography.ticker}>{ticker}</Text> · {holdings}
        </Text>
      </View>

      <MiniSparkline points={sparkline} color={color} width={56} height={20} />

      <View style={{ alignItems: 'flex-end' }}>
        <Text style={typography.assetPrice}>${price.toLocaleString(undefined, { minimumFractionDigits: 2 })}</Text>
        <Text style={[typography.assetChange, { color }]}>
          {sign}{Math.abs(dayChangePct * 100).toFixed(2)}%
        </Text>
      </View>
    </View>
  );
}

function MiniSparkline({ points, color, width, height }: { points: number[]; color: string; width: number; height: number }) {
  if (points.length < 2) return null;
  const min = Math.min(...points);
  const max = Math.max(...points);
  const range = max - min || 1;
  const step = width / (points.length - 1);
  const d = points
    .map((p, i) => {
      const x = i * step;
      const y = height - ((p - min) / range) * height;
      return `${i === 0 ? 'M' : 'L'}${x.toFixed(2)},${y.toFixed(2)}`;
    })
    .join(' ');
  return (
    <Svg width={width} height={height}>
      <Path d={d} stroke={color} strokeWidth={1.5} fill="none" strokeLinecap="round" strokeLinejoin="round" />
    </Svg>
  );
}
```

### 4-Up Action Row (Buy / Sell / Send / Receive)

```tsx
// components/AssetActionRow.tsx
import { View, Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Action = { icon: keyof typeof Ionicons.glyphMap; label: string; onPress: () => void };

export function AssetActionRow({ actions }: { actions: [Action, Action, Action, Action] }) {
  return (
    <View style={{ flexDirection: 'row', gap: 8, paddingHorizontal: 16 }}>
      {actions.map((a, i) => (
        <Pressable
          key={i}
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); a.onPress(); }}
          style={({ pressed }) => ({
            flex: 1, paddingVertical: 16, alignItems: 'center', gap: 8,
            borderRadius: 12, backgroundColor: pressed ? colors.surfaceGray2 : colors.surfaceGray,
          })}
        >
          <Ionicons name={a.icon} size={24} color={colors.cbBlue} />
          <Text style={typography.buttonSmall}>{a.label}</Text>
        </Pressable>
      ))}
    </View>
  );
}

// usage
// <AssetActionRow actions={[
//   { icon: 'arrow-up',    label: 'Buy',     onPress: ... },
//   { icon: 'arrow-down',  label: 'Sell',    onPress: ... },
//   { icon: 'paper-plane', label: 'Send',    onPress: ... },
//   { icon: 'qr-code',     label: 'Receive', onPress: ... },
// ]} />
```

### Primary CTA

```tsx
// components/CBButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Variant = 'primary' | 'secondary';

export function CBButton({ label, variant = 'primary', onPress }: { label: string; variant?: Variant; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        height: 48, borderRadius: 12, alignItems: 'center', justifyContent: 'center',
        backgroundColor: variant === 'primary'
          ? (pressed ? colors.cbBluePressed : colors.cbBlue)
          : (pressed ? colors.divider : colors.surfaceGray2),
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={variant === 'primary' ? typography.button : { ...typography.button, color: colors.textPrimary }}>{label}</Text>
    </Pressable>
  );
}
```

### Coinbase C-Mark Logomark

```tsx
// components/CoinbaseCMark.tsx
import { View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';

export function CoinbaseCMark({ size = 28, color = colors.cbBlue }: { size?: number; color?: string }) {
  return (
    <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      <View style={{
        position: 'absolute',
        width: size, height: size,
        borderRadius: size / 2,
        borderWidth: size * 0.16,
        borderColor: color,
      }} />
      <View style={{
        width: size * 0.42,
        height: size * 0.16,
        backgroundColor: color,
      }} />
    </View>
  );
}

export function CoinbaseCMarkLoading({ size = 28 }: { size?: number }) {
  const rotation = useSharedValue(0);
  useEffect(() => {
    rotation.value = withRepeat(withTiming(360, { duration: 1500, easing: Easing.linear }), -1);
  }, []);
  const style = useAnimatedStyle(() => ({ transform: [{ rotate: `${rotation.value}deg` }] }));
  return <Animated.View style={style}><CoinbaseCMark size={size} /></Animated.View>;
}
```

### Wallet Address Display

```tsx
// components/WalletAddress.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Clipboard from 'expo-clipboard';
import * as Haptics from 'expo-haptics';
import { useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WalletAddress({ address }: { address: string }) {
  const [copied, setCopied] = useState(false);
  const truncated = address.length > 14 ? `${address.slice(0, 10)}...${address.slice(-8)}` : address;

  const copy = async () => {
    await Clipboard.setStringAsync(address);
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    setCopied(true);
    setTimeout(() => setCopied(false), 1500);
  };

  return (
    <Pressable onPress={copy} style={{ flexDirection: 'row', alignItems: 'center', backgroundColor: colors.surfaceGray, padding: 12, borderRadius: 8, gap: 8 }}>
      <Text style={[typography.walletAddr, { flex: 1 }]}>{truncated}</Text>
      <Ionicons name={copied ? 'checkmark' : 'copy-outline'} size={16} color={copied ? colors.success : colors.textSecondary} />
    </Pressable>
  );
}
```

### Chart Range Chips

```tsx
// components/ChartRangeChips.tsx
import { View, Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const RANGES = ['1H', '1D', '1W', '1M', '1Y', 'ALL'] as const;

export function ChartRangeChips({ selected, onSelect }: { selected: typeof RANGES[number]; onSelect: (r: typeof RANGES[number]) => void }) {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'space-around', paddingHorizontal: 16 }}>
      {RANGES.map((r) => {
        const isSel = r === selected;
        return (
          <Pressable
            key={r}
            onPress={() => { Haptics.selectionAsync(); onSelect(r); }}
            style={{
              minWidth: 44, height: 32, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
              backgroundColor: isSel ? colors.surfaceGray2 : 'transparent',
            }}
          >
            <Text style={[typography.rangeChip, { color: isSel ? colors.textPrimary : colors.textSecondary }]}>{r}</Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.cbBlue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider, height: 56 + 24 },
        tabBarLabelStyle: { fontFamily: 'CoinbaseSans-Semibold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="home"   options={{ title: 'Home',   tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'home' : 'home-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="trade"  options={{ title: 'Trade',  tabBarIcon: ({ color }) => <Ionicons name="swap-horizontal" size={24} color={color} /> }} />
      <Tabs.Screen name="cards"  options={{ title: 'Cards',  tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'card' : 'card-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="earn"   options={{ title: 'Earn',   tabBarIcon: ({ color }) => <Ionicons name="trending-up" size={24} color={color} /> }} />
      <Tabs.Screen name="wallet" options={{ title: 'Wallet', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'wallet' : 'wallet-outline'} size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Buy/Sell tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Order placed
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// 4-up action tap (light)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Wallet address copied
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Tab switch
Haptics.selectionAsync();

// Range chip switch
Haptics.selectionAsync();

// Chart scrubber — instant gesture (no spring)
const drag = Gesture.Pan().onChange(...);

// C-mark loading rotation
rotation.value = withRepeat(withTiming(360, { duration: 1500, easing: Easing.linear }), -1);

// Order confirmation checkmark scale-in
scale.value = withSpring(1, { damping: 12 });
```

## 6. Icon Library

Use `@expo/vector-icons` — Ionicons covers most Coinbase iconography. For asset icons (Bitcoin, Ethereum, etc.) use brand-color circles with a Unicode glyph or a custom SVG.

| Purpose | Icon |
|---------|------|
| Home tab | `Ionicons home-outline / home` |
| Trade tab | `Ionicons swap-horizontal` |
| Cards tab | `Ionicons card-outline / card` |
| Earn tab | `Ionicons trending-up` |
| Wallet tab | `Ionicons wallet-outline / wallet` |
| 4-up Buy | `Ionicons arrow-up` (inside Surface Gray card) |
| 4-up Sell | `Ionicons arrow-down` |
| 4-up Send | `Ionicons paper-plane` |
| 4-up Receive | `Ionicons qr-code` |
| Search | `Ionicons search-outline` |
| Notifications | `Ionicons notifications-outline / notifications` |
| Watchlist star | `Ionicons star-outline / star` |
| Settings | `Ionicons settings-outline` |
| Back | `Ionicons chevron-back` |
| Share | `Ionicons share-outline` |
| Success | `Ionicons checkmark-circle` |
| Failed | `Ionicons close-circle` |
| Pending | `Ionicons time` |
| Copy | `Ionicons copy-outline` |
| Bitcoin glyph | render "₿" as Text in white inside #F7931A circle |
| Ethereum glyph | render "Ξ" or "◆" as Text in white inside #627EEA circle |
| USDC glyph | render "$" as Text in white inside #2775CA circle |

## 7. Platform Notes

- **iOS-first portrait**: Coinbase is portrait-locked on phones; lock via `expo-screen-orientation`.
- **Status bar**: `<StatusBar style="dark" />` on light mode, `style="light"` on dark.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The sticky Buy/Sell footer must respect `useSafeAreaInsets().bottom`.
- **Tabular numerals**: every Text rendering a price, percentage, or quantity must include `style={{ fontVariant: ['tabular-nums'] }}` — the institutional asset list look depends on it.
- **Coinbase Mono**: REQUIRED for wallet addresses and transaction hashes. Never render an address in proportional Sans. Use `JetBrains Mono` as the Google Fonts fallback.
- **Sparkline performance**: For a list of 50+ asset rows, memoize the sparkline `Path` `d` string. For 200+ rows, pre-render sparklines as Image bitmaps server-side and cache via `expo-image`.
- **Asset icon library**: Maintain a static map of asset → brand color + glyph. Don't fetch icons from external services in production; cache them locally.
- **Accessibility**: Asset row VoiceOver should announce `"Bitcoin, B T C, you own 0.1842, $12,389.42, up 1.92 percent"`. Use `accessibilityLabel` to expand the truncated wallet address back to full when copying.
- **Dark mode**: use `useColorScheme()` to switch tokens. Coinbase Blue shifts to `#3B6CFF` on dark for AA contrast.
- **Chart performance**: Use `react-native-svg` for the main portfolio chart up to ~200 points; switch to `react-native-skia` for 1000+ point series and 60fps scrubbing on older devices.
- **Color-blind users**: pair green/red 24h-change color with directional arrows (`arrow-up` / `arrow-down`) and "+"/"−" sign prefixes when `accessibilityDifferentiateWithoutColor` is detected.
