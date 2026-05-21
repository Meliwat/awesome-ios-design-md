# Robinhood (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Robinhood's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated/Gesture Handler snippets for the signature portfolio chart scrubber.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `react-native-reanimated` v3, `react-native-gesture-handler`, and `react-native-svg` (for the chart).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (light)
  canvas:        '#FFFFFF',
  surfaceGray:   '#F7F7F7',
  surfaceGray2:  '#EFEFEF',
  divider:       '#E6E6E6',

  // Text (light)
  textPrimary:   '#000000',
  textSecondary: '#5C6166',
  textTertiary:  '#9B9EA3',
  textMuted:     '#C2C5CA',

  // Brand
  rhGreen:        '#00C805',  // up
  rhGreenPressed: '#00A904',
  rhGreenDim:     '#21CE99',  // heritage
  rhGreenBg:      '#E6F9E0',
  rhRed:          '#FF5000',  // down (actually orange)
  rhRedBg:        '#FFEDE5',

  // Semantic
  errorTrue:     '#E62232',
  warning:       '#FFB800',
  info:          '#1D6FF2',

  // Crypto
  bitcoin:       '#F7931A',
  ethereum:      '#627EEA',

  // Dark mode
  darkCanvas:        '#000000',
  darkSurface1:      '#181B1F',
  darkSurface2:      '#23272D',
  darkDivider:       '#2D3138',
  darkTextPrimary:   '#FFFFFF',
  darkTextSecondary: '#A4A8AD',
} as const;

export type RHColor = keyof typeof colors;
```

## 2. Typography

Capsule Sans is proprietary. Bundle via `expo-font`, or fall back to `Inter` from Google Fonts (closest match) with `fontVariant: ['tabular-nums']` for prices.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'CapsuleSansText-Regular':   require('../assets/fonts/CapsuleSansText-Regular.otf'),
    'CapsuleSansText-Medium':    require('../assets/fonts/CapsuleSansText-Medium.otf'),
    'CapsuleSansText-Semibold':  require('../assets/fonts/CapsuleSansText-Semibold.otf'),
    'CapsuleSansText-Bold':      require('../assets/fonts/CapsuleSansText-Bold.otf'),
    'CapsuleSansDisplay-Bold':   require('../assets/fonts/CapsuleSansDisplay-Bold.otf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tabular = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };
const black   = { color: '#000000' } satisfies TextStyle;
const gray    = { color: '#5C6166' } satisfies TextStyle;

export const typography = {
  // Hero (Display variant)
  portfolioHero: { ...black,   ...tabular, fontFamily: 'CapsuleSansDisplay-Bold',  fontSize: 40, lineHeight: 40, letterSpacing: -0.5 },
  orderEntry:    { ...black,   ...tabular, fontFamily: 'CapsuleSansDisplay-Bold',  fontSize: 48, lineHeight: 48 },

  // Titles & body (Text variant)
  screenTitle:   { ...black,             fontFamily: 'CapsuleSansText-Bold',     fontSize: 22, lineHeight: 25, letterSpacing: -0.2 },
  sectionHeader: { ...black,             fontFamily: 'CapsuleSansText-Semibold', fontSize: 18, lineHeight: 22 },
  positionTitle: { ...black,             fontFamily: 'CapsuleSansText-Semibold', fontSize: 16, lineHeight: 21 },
  positionValue: { ...black,   ...tabular, fontFamily: 'CapsuleSansText-Medium',   fontSize: 17, lineHeight: 20 },
  dayChange:     {           ...tabular, fontFamily: 'CapsuleSansText-Medium',   fontSize: 16, lineHeight: 19 },
  body:          { ...black,             fontFamily: 'CapsuleSansText-Regular',  fontSize: 15, lineHeight: 21 },
  bodySmall:     { ...gray,              fontFamily: 'CapsuleSansText-Regular',  fontSize: 13, lineHeight: 18 },

  // Micro
  ticker:        { ...gray,              fontFamily: 'CapsuleSansText-Medium',   fontSize: 13, letterSpacing: 0.3 },
  positionChange:{           ...tabular, fontFamily: 'CapsuleSansText-Medium',   fontSize: 13, lineHeight: 17 },
  chartAxis:     { ...gray,              fontFamily: 'CapsuleSansText-Medium',   fontSize: 11, letterSpacing: 0.2 },
  rangeChip:     { ...gray,              fontFamily: 'CapsuleSansText-Semibold', fontSize: 13, letterSpacing: 0.3 },
  tab:           {                       fontFamily: 'CapsuleSansText-Semibold', fontSize: 10, letterSpacing: 0.2 },
  allCaps:       { ...gray,              fontFamily: 'CapsuleSansText-Bold',     fontSize: 11, letterSpacing: 0.6, textTransform: 'uppercase' },

  button:        { color: '#FFFFFF',     fontFamily: 'CapsuleSansText-Semibold', fontSize: 16, lineHeight: 19 },
  buttonSmall:   { ...black,             fontFamily: 'CapsuleSansText-Medium',   fontSize: 15, lineHeight: 18 },
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
  const isUp = dayChange >= 0;
  const sign = isUp ? '+' : '−';
  const color = isUp ? colors.rhGreen : colors.rhRed;

  return (
    <View style={{ paddingHorizontal: 24, paddingTop: 16, gap: 8 }}>
      <Text style={typography.allCaps}>Buying Power</Text>
      <Text style={typography.portfolioHero}>${value.toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })}</Text>
      <Text style={[typography.dayChange, { color }]}>
        {sign}${Math.abs(dayChange).toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })} ({sign}{Math.abs(dayChangePct * 100).toFixed(2)}%) Today
      </Text>
    </View>
  );
}
```

### Portfolio Chart with Scrubber (SVG + Gesture Handler)

```tsx
// components/PortfolioChart.tsx
import { Dimensions, View, Text } from 'react-native';
import { GestureDetector, Gesture } from 'react-native-gesture-handler';
import Animated, { useSharedValue, useAnimatedProps, runOnJS } from 'react-native-reanimated';
import Svg, { Path, Circle, Line } from 'react-native-svg';
import { useState } from 'react';
import { colors } from '../theme/colors';

const AnimatedLine = Animated.createAnimatedComponent(Line);

type Point = { x: number; y: number; v: number; t: Date };

export function PortfolioChart({ points, isUp, onScrub }: { points: Point[]; isUp: boolean; onScrub: (p: Point | null) => void }) {
  const W = Dimensions.get('window').width;
  const H = 220;
  const stroke = isUp ? colors.rhGreen : colors.rhRed;

  // build SVG path
  const d = points.map((p, i) => (i === 0 ? `M${p.x},${p.y}` : `L${p.x},${p.y}`)).join(' ');

  const dragX = useSharedValue(-1);
  const [dragPt, setDragPt] = useState<Point | null>(null);

  const drag = Gesture.Pan()
    .onChange((e) => {
      dragX.value = e.x;
      const closest = points.reduce((a, b) => (Math.abs(a.x - e.x) < Math.abs(b.x - e.x) ? a : b));
      runOnJS(setDragPt)(closest);
      runOnJS(onScrub)(closest);
    })
    .onEnd(() => {
      dragX.value = -1;
      runOnJS(setDragPt)(null);
      runOnJS(onScrub)(null);
    });

  return (
    <GestureDetector gesture={drag}>
      <View style={{ width: W, height: H }}>
        <Svg width={W} height={H}>
          <Path d={d} stroke={stroke} strokeWidth={2} fill="none" />
          {dragPt && (
            <>
              <Line x1={dragPt.x} y1={0} x2={dragPt.x} y2={H} stroke={colors.textPrimary} strokeWidth={1} />
              <Circle cx={dragPt.x} cy={dragPt.y} r={5} fill={colors.textPrimary} />
            </>
          )}
        </Svg>
      </View>
    </GestureDetector>
  );
}
```

### Chart Range Chips

```tsx
// components/ChartRangeChips.tsx
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import * as Haptics from 'expo-haptics';

const RANGES = ['1D', '1W', '1M', '3M', 'YTD', '1Y', '5Y', 'ALL'] as const;

export function ChartRangeChips({ selected, onSelect, isUp }: { selected: typeof RANGES[number]; onSelect: (r: typeof RANGES[number]) => void; isUp: boolean }) {
  const activeColor = isUp ? colors.rhGreen : colors.rhRed;

  return (
    <View style={{ flexDirection: 'row', justifyContent: 'space-around', paddingHorizontal: 16, paddingVertical: 12 }}>
      {RANGES.map((r) => {
        const isSel = r === selected;
        return (
          <Pressable
            key={r}
            onPress={() => { Haptics.selectionAsync(); onSelect(r); }}
            style={{ alignItems: 'center', minHeight: 32, minWidth: 36 }}
          >
            <Text style={[typography.rangeChip, { color: isSel ? activeColor : colors.textSecondary }]}>{r}</Text>
            <View style={{ height: 2, marginTop: 4, width: '100%', backgroundColor: isSel ? activeColor : 'transparent' }} />
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Position Row

```tsx
// components/PositionRow.tsx
import { View, Text, Image } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  ticker: string;
  company: string;
  shares: number;
  marketValue: number;
  dayChange: number;
  dayChangePct: number;
  logoUri?: string;
};

export function PositionRow({ ticker, company, shares, marketValue, dayChange, dayChangePct, logoUri }: Props) {
  const isUp = dayChange >= 0;
  const sign = isUp ? '+' : '−';
  const color = isUp ? colors.rhGreen : colors.rhRed;

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, height: 56, backgroundColor: colors.canvas, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      {/* Ticker logo */}
      {logoUri ? (
        <Image source={{ uri: logoUri }} style={{ width: 32, height: 32, borderRadius: 8 }} />
      ) : (
        <View style={{ width: 32, height: 32, borderRadius: 8, backgroundColor: colors.surfaceGray, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={{ fontFamily: 'CapsuleSansText-Bold', fontSize: 14 }}>{ticker.charAt(0)}</Text>
        </View>
      )}

      <View style={{ flex: 1 }}>
        <Text style={typography.positionTitle} numberOfLines={1}>{company}</Text>
        <Text style={typography.bodySmall}>
          <Text style={typography.ticker}>{ticker}</Text> · {shares.toLocaleString(undefined, { maximumFractionDigits: 4 })} shares
        </Text>
      </View>

      <View style={{ alignItems: 'flex-end' }}>
        <Text style={typography.positionValue}>${marketValue.toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })}</Text>
        <Text style={[typography.positionChange, { color }]}>
          {sign}${Math.abs(dayChange).toFixed(2)} ({sign}{Math.abs(dayChangePct * 100).toFixed(2)}%)
        </Text>
      </View>
    </View>
  );
}
```

### Trade Button (Sticky Footer)

```tsx
// components/TradeButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TradeButton({ label = 'Trade', onPress }: { label?: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        marginHorizontal: 16, marginBottom: 16, height: 48, borderRadius: 8,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? '#1A1A1A' : colors.textPrimary,
      })}
    >
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Buy / Sell Pair (within Trade modal)

```tsx
// components/BuySellPair.tsx
import { View, Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BuySellPair({ onBuy, onSell }: { onBuy: () => void; onSell: () => void }) {
  return (
    <View style={{ flexDirection: 'row', gap: 12, paddingHorizontal: 16 }}>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onSell(); }}
        style={({ pressed }) => ({ flex: 1, height: 48, borderRadius: 8, alignItems: 'center', justifyContent: 'center', backgroundColor: pressed ? '#D43F00' : colors.rhRed })}
      >
        <Text style={typography.button}>Sell</Text>
      </Pressable>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onBuy(); }}
        style={({ pressed }) => ({ flex: 1, height: 48, borderRadius: 8, alignItems: 'center', justifyContent: 'center', backgroundColor: pressed ? colors.rhGreenPressed : colors.rhGreen })}
      >
        <Text style={typography.button}>Buy</Text>
      </Pressable>
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import MaterialCommunityIcons from '@expo/vector-icons/MaterialCommunityIcons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.textPrimary,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: colors.canvas, height: 56 + 24 },
        tabBarLabelStyle: { fontFamily: 'CapsuleSansText-Semibold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="investing" options={{ title: 'Investing', tabBarIcon: ({ color }) => <Ionicons name="trending-up" size={24} color={color} /> }} />
      <Tabs.Screen name="search"    options={{ title: 'Search',    tabBarIcon: ({ color }) => <Ionicons name="search-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="crypto"    options={{ title: 'Crypto',    tabBarIcon: ({ color }) => <MaterialCommunityIcons name="bitcoin" size={24} color={color} /> }} />
      <Tabs.Screen name="cash"      options={{ title: 'Cash',      tabBarIcon: ({ color }) => <Ionicons name="wallet-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="account"   options={{ title: 'Account',   tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person-circle' : 'person-circle-outline'} size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Chart scrubber — instant pan (no spring), then values update via state
const drag = Gesture.Pan().onChange(...)

// Buy / Sell tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Order placed
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Tab switch
Haptics.selectionAsync();

// Range chip switch
Haptics.selectionAsync();

// Portfolio hero number update during scrub — animate via Reanimated `withTiming` 100ms
// (or use `<Text>` re-render which is fast enough on modern devices)
```

## 6. Icon Library

Use `@expo/vector-icons` — Ionicons and MaterialCommunityIcons cover the Robinhood iconography. For company logos in position rows, prefer fetched logos via the Robinhood API or `clearbit/logos`.

| Purpose | Icon |
|---------|------|
| Investing tab | `Ionicons trending-up` |
| Search tab | `Ionicons search-outline / search` |
| Crypto tab | `MaterialCommunityIcons bitcoin` |
| Cash tab | `Ionicons wallet-outline / wallet` |
| Account tab | `Ionicons person-circle-outline / person-circle` |
| Search icon | `Ionicons search-outline` |
| Set alert | `Ionicons notifications-outline / notifications` |
| Add to list | `Ionicons add-circle-outline` |
| Back | `Ionicons chevron-back` |
| Share | `Ionicons share-outline` |
| Order success | `Ionicons checkmark-circle` |
| Order failed | `Ionicons close-circle` |
| Ethereum | `MaterialCommunityIcons ethereum` |

## 7. Platform Notes

- **iOS-first portrait**: Robinhood is portrait-locked on phones. Use `expo-screen-orientation` to lock it explicitly: `await ScreenOrientation.lockAsync(ScreenOrientation.OrientationLock.PORTRAIT_UP)`.
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` on light-canvas screens, `style="light"` on dark mode.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The sticky Trade footer must respect the home indicator — pull `useSafeAreaInsets().bottom`.
- **Chart performance**: For long history (5Y, ALL), pre-decimate points to 60-100 samples before rendering — `react-native-svg`'s `Path` slows past 200+ points on older devices. Use `react-native-skia` for production if you need 60fps drag on 1000+ point series.
- **Tabular numerals**: Set `style={{ fontVariant: ['tabular-nums'] }}` on every Text rendering a price, percentage, share count. Even when using Capsule Sans (which has tabular by default), specifying it explicitly guarantees consistency on the fallback path.
- **Color-blind accessibility**: Pair the green/orange day-change color with directional symbols ("+"/"−"). Optionally add a small up-arrow / down-arrow icon when `accessibilityDifferentiateWithoutColor` is detected.
- **Dark mode**: Use `useColorScheme()` to switch the token object — `#00C805` and `#FF5000` stay identical. The green line reads more electric on `#000000` than on white.
- **Order confirmation modal**: Use `expo-router`'s `presentation: 'modal'` with a custom dismiss gesture. The final commit is a swipe-to-confirm — implement with `react-native-gesture-handler`'s `Swipeable`.
- **Pull-to-refresh on Investing tab**: Use `RefreshControl` on the outer ScrollView with `tintColor={colors.textPrimary}` for a black spinner on light mode.
