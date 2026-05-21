# Crypto.com (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Crypto.com's premium navy financial language into paste-ready Expo / React Native code: a design-token module, themed components (including the metallic Visa card), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-local-authentication`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default)
  canvas:    '#03060F',
  surface1:  '#0B1426',
  surface2:  '#103F68', // Navy 2
  surface3:  '#15263F',
  divider:   '#1C3354',

  // Surfaces (light — secondary)
  canvasLight:   '#FFFFFF',
  surface1Light: '#F4F7FB',
  dividerLight:  '#E3E9F2',

  // Brand (decorative)
  navy:  '#002D74',
  navy2: '#103F68',

  // Accent (interactive)
  accent:        '#1199FA',
  accentPressed: '#0B7AD1',
  accentTint:    'rgba(17,153,250,0.12)',

  // Market semantics (never invert)
  up:   '#00C08B',
  down: '#F6485D',

  // Card metals
  gold: '#C8A24A',

  // Text
  textPrimary:    '#F4F7FB',
  textSecondary:  '#8DA0BD',
  textTertiary:   '#5B6E8C',
  textPrimaryLt:  '#0A1F44',

  // Coin brand colors
  btc: '#F7931A',
  eth: '#627EEA',
  cro: '#1199FA',
  sol: '#14F195',

  // Semantic
  warning: '#F5A623',
} as const;

// Card-tier gradient stops (for expo-linear-gradient)
export const cardGradients = {
  midnight: ['#1A1A1E', '#2A2A30'],
  ruby:     ['#5A1020', '#8A1830'],
  jade:     ['#0E3A30', '#14564A'],
  obsidian: ['#0A0A0C', '#1A1A1E', '#26262C'],
} as const;

export const brandNavyGradient = ['#002D74', '#103F68'] as const;

export type CryptoColor = keyof typeof colors;
```

## 2. Typography

Load **Manrope** (UI) + **Roboto Mono** (money) via `expo-font`. All money/PAN/APR uses tabular figures (`fontVariant: ['tabular-nums']`).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Manrope-Regular':    require('../assets/fonts/Manrope-Regular.ttf'),
    'Manrope-Medium':     require('../assets/fonts/Manrope-Medium.ttf'),
    'Manrope-SemiBold':   require('../assets/fonts/Manrope-SemiBold.ttf'),
    'Manrope-Bold':       require('../assets/fonts/Manrope-Bold.ttf'),
    'Manrope-ExtraBold':  require('../assets/fonts/Manrope-ExtraBold.ttf'),
    'RobotoMono-Medium':  require('../assets/fonts/RobotoMono-Medium.ttf'),
    'RobotoMono-SemiBold':require('../assets/fonts/RobotoMono-SemiBold.ttf'),
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
  screenTitle: { fontFamily: 'Manrope-ExtraBold', fontSize: 32, lineHeight: 38, letterSpacing: -0.6, color: '#F4F7FB' },
  section:     { fontFamily: 'Manrope-ExtraBold', fontSize: 22, lineHeight: 28, letterSpacing: -0.2, color: '#F4F7FB' },
  rowTitle:    { fontFamily: 'Manrope-Bold',      fontSize: 18, lineHeight: 23, color: '#F4F7FB' },
  body:        { fontFamily: 'Manrope-Regular',   fontSize: 16, lineHeight: 24, color: '#F4F7FB' },
  listLabel:   { fontFamily: 'Manrope-Bold',      fontSize: 15, lineHeight: 20, color: '#F4F7FB' },
  cardTier:    { fontFamily: 'Manrope-Bold',      fontSize: 13, lineHeight: 13, letterSpacing: 1.5, color: '#C8A24A' },
  meta:        { fontFamily: 'Manrope-Regular',   fontSize: 14, lineHeight: 19, color: '#8DA0BD' },
  chip:        { fontFamily: 'Manrope-SemiBold',  fontSize: 13, lineHeight: 13, color: '#8DA0BD' },
  caption:     { fontFamily: 'Manrope-SemiBold',  fontSize: 12, lineHeight: 16, color: '#8DA0BD' },
  tab:         { fontFamily: 'Manrope-SemiBold',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:      { fontFamily: 'Manrope-Bold',      fontSize: 15, lineHeight: 15, color: '#FFFFFF' },

  // Numeric mono (always tabular)
  balance: { ...tabular, fontFamily: 'RobotoMono-SemiBold', fontSize: 30, lineHeight: 34, letterSpacing: -0.5, color: '#F4F7FB' },
  price:   { ...tabular, fontFamily: 'RobotoMono-SemiBold', fontSize: 15, lineHeight: 20, color: '#F4F7FB' },
  pan:     { ...tabular, fontFamily: 'RobotoMono-Medium',   fontSize: 15, lineHeight: 20, letterSpacing: 2, color: 'rgba(255,255,255,0.92)' },
  pctSm:   { ...tabular, fontFamily: 'RobotoMono-SemiBold', fontSize: 12, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Metallic Visa Card

```tsx
// components/VisaCard.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, cardGradients } from '../theme/colors';
import { typography } from '../theme/typography';

type Tier = keyof typeof cardGradients;
const tierName: Record<Tier, string> = {
  midnight: 'Midnight Blue', ruby: 'Ruby Steel', jade: 'Jade Green', obsidian: 'Obsidian',
};

export function VisaCard({ tier, pan, holder }: { tier: Tier; pan: string; holder: string }) {
  return (
    <View style={{
      borderRadius: 18, aspectRatio: 1.586, overflow: 'hidden',
      shadowColor: '#000', shadowOpacity: 0.8, shadowRadius: 30, shadowOffset: { width: 0, height: 14 },
      elevation: 12,
    }}>
      <LinearGradient colors={cardGradients[tier] as unknown as string[]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1, padding: 20 }}>
        {/* gloss sheen */}
        <LinearGradient colors={['rgba(255,255,255,0.08)', 'transparent']}
          start={{ x: 0, y: 0 }} end={{ x: 0.5, y: 0.5 }}
          style={{ position: 'absolute', inset: 0 }} pointerEvents="none" />

        <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
          <Text style={typography.cardTier}>{tierName[tier].toUpperCase()}</Text>
          <Text style={{ fontSize: 20, color: '#FFF' }}>◆</Text>
        </View>

        <View style={{ width: 36, height: 26, borderRadius: 5, marginTop: 18, overflow: 'hidden' }}>
          <LinearGradient colors={['#D9C079', '#A88D45']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
            style={{ flex: 1 }} />
        </View>

        <View style={{ flex: 1, justifyContent: 'flex-end' }}>
          <Text style={typography.pan}>{pan}</Text>
          <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', marginTop: 6 }}>
            <Text style={{ fontFamily: 'Manrope-SemiBold', fontSize: 12, letterSpacing: 1,
              color: 'rgba(255,255,255,0.75)' }}>
              {holder.toUpperCase()}
            </Text>
            <Text style={{ fontFamily: 'Manrope-ExtraBold', fontSize: 18, fontStyle: 'italic', color: '#FFF' }}>
              VISA
            </Text>
          </View>
        </View>
      </LinearGradient>
    </View>
  );
}
```

### Total Balance Hero

```tsx
// components/BalanceHero.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BalanceHero({ value, pnl, gain }: { value: string; pnl: string; gain: boolean }) {
  const [hidden, setHidden] = useState(false);
  return (
    <View style={{ paddingHorizontal: 20, gap: 4 }}>
      <Text style={typography.caption}>Total Balance (USD)</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <Text style={typography.balance}>{hidden ? '••••••' : value}</Text>
        <Pressable onPress={() => setHidden((h) => !h)} hitSlop={8}>
          <Ionicons name={hidden ? 'eye-off-outline' : 'eye-outline'} size={14} color={colors.textSecondary} />
        </Pressable>
      </View>
      <Text style={[typography.pctSm, { color: gain ? colors.up : colors.down }]}>{pnl} Today</Text>
    </View>
  );
}
```

### Watchlist Row

```tsx
// components/WatchRow.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchRow({
  symbol, name, price, pct, iconColor = colors.btc,
}: { symbol: string; name: string; price: string; pct: number; iconColor?: string }) {
  const up = pct >= 0;
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 20, paddingVertical: 11 }}>
      <View style={{ width: 34, height: 34, borderRadius: 17, backgroundColor: iconColor,
        alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Manrope-Bold', fontSize: 14, color: '#FFF' }}>{symbol[0]}</Text>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={typography.listLabel}>{symbol}</Text>
        <Text style={{ fontFamily: 'Manrope-Regular', fontSize: 12, color: colors.textSecondary }}>{name}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={typography.price}>{price}</Text>
        <Text style={[typography.pctSm, { color: up ? colors.up : colors.down }]}>
          {up ? '+' : ''}{pct.toFixed(2)}%
        </Text>
      </View>
    </View>
  );
}
```

### Primary Pill Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress?: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        height: 52, borderRadius: 999, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.accentPressed : colors.accent,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Quick-Action Circle Row

```tsx
// components/QuickActions.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

const items = [
  { icon: 'add', label: 'Deposit' },
  { icon: 'arrow-up', label: 'Send' },
  { icon: 'swap-horizontal', label: 'Trade' },
  { icon: 'pie-chart-outline', label: 'Earn' },
] as const;

export function QuickActions() {
  return (
    <View style={{ flexDirection: 'row', paddingHorizontal: 20 }}>
      {items.map((it) => (
        <View key={it.label} style={{ flex: 1, alignItems: 'center', gap: 6 }}>
          <View style={{ width: 46, height: 46, borderRadius: 23, backgroundColor: colors.surface1,
            alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name={it.icon} size={20} color={colors.accent} />
          </View>
          <Text style={{ fontFamily: 'Manrope-SemiBold', fontSize: 11, color: colors.textSecondary }}>
            {it.label}
          </Text>
        </View>
      ))}
    </View>
  );
}
```

## 4. Bottom Tab Bar (floating center Trade)

`expo-router` `Tabs` with a custom center button:

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

function TradeButton({ onPress }: { onPress?: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      top: -22, width: 44, height: 44, borderRadius: 22, backgroundColor: colors.accent,
      alignItems: 'center', justifyContent: 'center',
      shadowColor: colors.accent, shadowOpacity: 0.4, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
      elevation: 8,
    }}>
      <Ionicons name="swap-horizontal" size={18} color="#FFF" />
    </Pressable>
  );
}

export default function TabsLayout() {
  return (
    <Tabs screenOptions={{
      tabBarActiveTintColor: colors.accent,
      tabBarInactiveTintColor: colors.textTertiary,
      tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 1, borderTopColor: colors.divider },
      tabBarLabelStyle: { fontFamily: 'Manrope-SemiBold', fontSize: 10, letterSpacing: 0.1 },
    }}>
      <Tabs.Screen name="index"  options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home" size={20} color={color} /> }} />
      <Tabs.Screen name="prices" options={{ title: 'Prices', tabBarIcon: ({ color }) => <Ionicons name="stats-chart" size={20} color={color} /> }} />
      <Tabs.Screen name="trade"  options={{ title: '', tabBarButton: (p) => <TradeButton onPress={p.onPress as any} /> }} />
      <Tabs.Screen name="earn"   options={{ title: 'Earn',   tabBarIcon: ({ color }) => <Ionicons name="pie-chart-outline" size={20} color={color} /> }} />
      <Tabs.Screen name="card"   options={{ title: 'Card',   tabBarIcon: ({ color }) => <Ionicons name="card" size={20} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Card flip (reveal CVV) — 400ms, requires biometric first
const rot = useSharedValue(0);
const frontStyle = useAnimatedStyle(() => ({ transform: [{ perspective: 800 }, { rotateY: `${rot.value}deg` }] }));
function flip() { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); rot.value = withTiming(rot.value ? 0 : 180, { duration: 400 }); }

// Balance reveal — cross-dissolve (Animated opacity 200ms)

// Floating Trade press-spring 1 → 0.92 → 1
scale.value = withSpring(0.92, { damping: 8 }, () => { scale.value = withSpring(1); });

// Buy/Sell toggle color-morph — 200ms
segX.value = withTiming(isBuy ? 0 : 1, { duration: 200 });

// Price tick color pulse — 150ms (text color only, no bg flash)

// Card carousel focus scale 0.94 → 1.0 (interpolate from scroll offset)

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                 // card flip
Haptics.selectionAsync();                                              // term/segment
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);   // transaction done
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The Crypto.com brand mark and coin glyphs are best bundled as SVG via `react-native-svg`.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Prices | `stats-chart` |
| Trade (center) | `swap-horizontal` |
| Earn | `pie-chart-outline` |
| Card | `card` |
| Notifications | `notifications-outline` |
| Menu | `menu` |
| Hide balance | `eye-outline` / `eye-off-outline` |
| Deposit | `add` |
| Send | `arrow-up` |
| Receive | `arrow-down` |
| Search | `search` |
| Back | `chevron-back` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Up tick | `arrow-up` |
| Down tick | `arrow-down` |
| Stake / reward | `star-outline` |
| Settings | `settings-outline` |
| Lock card | `lock-closed-outline` |

## 7. Platform Notes

- **Fonts**: Manrope (SIL OFL) + Roboto Mono (Apache 2.0) — free to bundle. Substitute Crypto.com's custom grotesque if licensed; Manrope is a faithful open stand-in
- **Tabular numbers**: set `fontVariant: ['tabular-nums']` on every numeric `<Text>` (already in tokens). Without it the watchlist prices and the card PAN don't align
- **Card gradients**: use `expo-linear-gradient`; layer a faint white sheen gradient on top for the metallic gloss. Add platform shadow (`shadowOpacity: 0.8` iOS / `elevation: 12` Android) for the card's physical weight
- **Status bar**: `<StatusBar style="light" />` — Crypto.com is dark-native; switch to `"dark"` only for the light theme
- **Safe area**: wrap screens in `SafeAreaView`; the floating Trade button must sit above the home indicator (give the tab bar bottom safe-area padding and offset the button accordingly)
- **Dynamic Type**: set `allowFontScaling={false}` on card text (tier/PAN), numeric mono columns, and tab labels; allow scaling on titles/body/labels
- **Card security**: gate the card-flip / reveal behind `expo-local-authentication` (Face ID); never persist or log the full PAN/CVV
- **Keyboard**: `KeyboardAvoidingView` on amount screens so the MAX button and confirm pill stay visible
- **Charts**: use `react-native-wagmi-charts` or `react-native-skia`; up `#00C08B`, down `#F6485D`
- **Dark mode**: default to dark; if supporting light, swap only `canvas`→`#FFFFFF` and `textPrimary`→`#0A1F44` via `useColorScheme()` — never invert `up`/`down`, never change `accent`/`navy`/card gradients
- **Accessibility**: the Visa card should be a single accessible element ("Obsidian Visa card, ending 7310, 5 percent cashback"); pair every colored % with `+`/`−` + an arrow glyph
