# Charles Schwab (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Schwab's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Core rules: **money uses `fontVariant: ['tabular-nums']`**, **green/red are gain/loss only**, **dark navy text on Schwab Blue**.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  blue:        '#009DDC',
  bluePressed: '#0080B5',
  navy:        '#003B5C',
  navyDeep:    '#002A42',
  sky:         '#46BEEC',
  skyLight:    '#0078A8',
  onBlue:      '#002233',  // text ON Schwab Blue (white fails contrast)

  // Surfaces (light)
  canvas:         '#FFFFFF',
  surface:        '#F2F6F8',
  surfacePressed: '#E6EDF1',
  divider:        '#DBE3E8',

  // Surfaces (dark)
  darkCanvas:   '#0A1622',
  darkSurface1: '#11212F',
  darkSurface2: '#18303F',
  darkDivider:  '#1F3A4A',

  // Text
  textPrimary:       '#10222E',
  textSecondary:     '#5A707C',
  textTertiary:      '#8597A0',
  darkTextPrimary:   '#E6EEF3',
  darkTextSecondary: '#9DB2BF',
  darkTextTertiary:  '#6A8392',
  onHero:            '#FFFFFF',
  onHeroSub:         '#9DC4D8',

  // Financial semantics (sacred — gain/loss only)
  gainLight:  '#16895F',
  gainDark:   '#18B07B',
  lossLight:  '#C8443D',
  lossDark:   '#E2564E',
  onHeroGain: '#5FE3B0',

  // System / accent
  warning: '#C8862B',
  info:    '#0078A8',
  gold:    '#C8A24B',
} as const;

export type SchwabColor = keyof typeof colors;

export function changeColor(change: number, dark: boolean) {
  if (change > 0) return dark ? colors.gainDark : colors.gainLight;
  if (change < 0) return dark ? colors.lossDark : colors.lossLight;
  return dark ? colors.darkTextSecondary : colors.textSecondary;
}
```

## 2. Typography

Load **Source Sans 3** (SIL OFL) as the free substitute for Schwab's "Charles Modern". Defining rule: every money value gets `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'SourceSans3-Regular':   require('../assets/fonts/SourceSans3-Regular.ttf'),
    'SourceSans3-Medium':    require('../assets/fonts/SourceSans3-Medium.ttf'),
    'SourceSans3-SemiBold':  require('../assets/fonts/SourceSans3-SemiBold.ttf'),
    'SourceSans3-Bold':      require('../assets/fonts/SourceSans3-Bold.ttf'),
    'SourceSans3-ExtraBold': require('../assets/fonts/SourceSans3-ExtraBold.ttf'),
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
  totalValue:  { fontFamily: 'SourceSans3-ExtraBold', fontSize: 34, lineHeight: 38, letterSpacing: -0.6, ...tnum },
  screenTitle: { fontFamily: 'SourceSans3-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { fontFamily: 'SourceSans3-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:   { fontFamily: 'SourceSans3-Bold',      fontSize: 18, lineHeight: 23 },
  body:        { fontFamily: 'SourceSans3-Regular',   fontSize: 16, lineHeight: 24 },
  rowValue:    { fontFamily: 'SourceSans3-ExtraBold', fontSize: 16, lineHeight: 21, ...tnum },
  acctName:    { fontFamily: 'SourceSans3-Bold',      fontSize: 15, lineHeight: 20 },
  meta:        { fontFamily: 'SourceSans3-Regular',   fontSize: 14, lineHeight: 19 },
  numericMono: { fontFamily: 'SourceSans3-Bold',      fontSize: 13, lineHeight: 17, ...tnum },
  caption:     { fontFamily: 'SourceSans3-SemiBold',  fontSize: 12, lineHeight: 16 },
  button:      { fontFamily: 'SourceSans3-ExtraBold', fontSize: 16, lineHeight: 16, letterSpacing: 0.2 },
  tab:         { fontFamily: 'SourceSans3-SemiBold',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  ticketField: { fontFamily: 'SourceSans3-ExtraBold', fontSize: 16, lineHeight: 16, ...tnum },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Account Hero (navy gradient — the lead)

```tsx
// components/AccountHero.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AccountHero({
  title, totalValue, changeAmount, changePercent, change, onBell,
}: {
  title: string; totalValue: string; changeAmount: string;
  changePercent: string; change: number; onBell: () => void;
}) {
  return (
    <LinearGradient
      colors={[colors.navy, colors.navyDeep]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{ paddingHorizontal: 20, paddingTop: 8, paddingBottom: 22 }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={[typography.screenTitle, { color: '#FFFFFF' }]}>{title}</Text>
        <Pressable onPress={onBell} style={{ width: 34, height: 34, borderRadius: 17, backgroundColor: 'rgba(255,255,255,0.10)', alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="notifications-outline" size={16} color="#FFFFFF" />
        </Pressable>
      </View>
      <Text style={[typography.caption, { color: colors.onHeroSub, marginTop: 18, letterSpacing: 0.6 }]}>TOTAL VALUE</Text>
      <Text style={[typography.totalValue, { color: '#FFFFFF', marginTop: 4 }]}>{totalValue}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', marginTop: 8 }}>
        <Text style={{ color: colors.onHeroGain, fontSize: 12 }}>{change >= 0 ? '▲ ' : '▼ '}</Text>
        <Text style={[typography.numericMono, { color: colors.onHeroGain }]}>{changeAmount} </Text>
        <Text style={[typography.numericMono, { color: colors.onHeroSub }]}>({changePercent}) today</Text>
      </View>
    </LinearGradient>
  );
}
```

### Account Card / List Cell

```tsx
// components/AccountCard.tsx
import { Pressable, Text, View, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, changeColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function AccountCard({
  icon, name, mask, value, changePct, change,
}: {
  icon: any; name: string; mask: string; value: string; changePct: string; change: number;
}) {
  const dark = useColorScheme() === 'dark';
  const primary = dark ? colors.darkTextPrimary : colors.textPrimary;
  const secondary = dark ? colors.darkTextSecondary : colors.textSecondary;
  const surface2 = dark ? colors.darkSurface2 : colors.surface;
  const divider = dark ? colors.darkDivider : colors.divider;

  return (
    <Pressable
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 12,
        paddingHorizontal: 20, height: 66,
        borderBottomWidth: 0.5, borderBottomColor: divider,
        backgroundColor: pressed ? surface2 : 'transparent',
      })}
    >
      <View style={{ width: 38, height: 38, borderRadius: 10, backgroundColor: surface2, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name={icon} size={18} color={dark ? colors.sky : colors.skyLight} />
      </View>
      <View style={{ flex: 1, minWidth: 0 }}>
        <Text style={[typography.acctName, { color: primary }]}>{name}</Text>
        <Text style={[typography.caption, { color: secondary }]} numberOfLines={1}>{mask}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={[typography.rowValue, { color: primary }]}>{value}</Text>
        <Text style={[typography.numericMono, { color: changeColor(change, dark) }]}>{changePct}</Text>
      </View>
    </Pressable>
  );
}
```

### Trade Ticket (the centerpiece)

```tsx
// components/TradeTicket.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TradeTicket({ fields, onReview }: {
  fields: [string, string][]; onReview: () => void;
}) {
  const [side, setSide] = useState(0);
  return (
    <View style={{ backgroundColor: colors.darkSurface2, borderRadius: 12, padding: 16 }}>
      <View style={{ flexDirection: 'row', gap: 3, backgroundColor: colors.darkCanvas, borderRadius: 8, padding: 3 }}>
        {['Buy', 'Sell'].map((s, i) => (
          <Pressable
            key={s}
            onPress={() => { setSide(i); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); }}
            style={{ flex: 1, height: 36, borderRadius: 6, alignItems: 'center', justifyContent: 'center', backgroundColor: i === side ? colors.blue : 'transparent' }}
          >
            <Text style={{ fontFamily: 'SourceSans3-Bold', fontSize: 13, color: i === side ? colors.onBlue : colors.darkTextSecondary }}>{s}</Text>
          </Pressable>
        ))}
      </View>

      {fields.map(([label, val], idx) => (
        <View
          key={label}
          style={{
            flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center',
            paddingVertical: 12,
            borderBottomWidth: idx === fields.length - 1 ? 0 : 1,
            borderBottomColor: colors.darkDivider,
          }}
        >
          <Text style={[typography.numericMono, { color: colors.darkTextSecondary }]}>{label}</Text>
          <Text style={[typography.ticketField, { color: colors.darkTextPrimary }]}>{val}</Text>
        </View>
      ))}

      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onReview(); }}
        style={({ pressed }) => ({
          marginTop: 14, height: 52, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
          backgroundColor: pressed ? colors.bluePressed : colors.blue,
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}
      >
        <Text style={[typography.button, { color: colors.onBlue }]}>Review order</Text>
      </Pressable>
    </View>
  );
}
```

### Sticky Trade Button & Balances Tiles

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
    <View style={{ borderTopWidth: 0.5, borderTopColor: colors.darkDivider, backgroundColor: 'rgba(10,22,34,0.94)', paddingBottom: insets.bottom }}>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
        style={({ pressed }) => ({
          marginHorizontal: 16, marginTop: 12, height: 52, borderRadius: 8,
          alignItems: 'center', justifyContent: 'center',
          backgroundColor: pressed ? colors.bluePressed : colors.blue,
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}
      >
        <Text style={[typography.button, { color: colors.onBlue }]}>Trade</Text>
      </Pressable>
    </View>
  );
}
```

```tsx
// components/BalancesTiles.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors, changeColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function BalancesTiles({ tiles }: { tiles: { label: string; value: string; change?: number }[] }) {
  const dark = useColorScheme() === 'dark';
  const bg = dark ? colors.darkSurface2 : colors.surface;
  const primary = dark ? colors.darkTextPrimary : colors.textPrimary;
  const secondary = dark ? colors.darkTextSecondary : colors.textSecondary;
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 12 }}>
      {tiles.map((t) => (
        <View key={t.label} style={{ flexGrow: 1, flexBasis: '47%', backgroundColor: bg, borderRadius: 12, paddingVertical: 14, paddingHorizontal: 16 }}>
          <Text style={[typography.caption, { color: secondary }]}>{t.label}</Text>
          <Text style={{ fontFamily: 'SourceSans3-ExtraBold', fontSize: 19, marginTop: 6, fontVariant: ['tabular-nums'], color: t.change != null ? changeColor(t.change, dark) : primary }}>{t.value}</Text>
        </View>
      ))}
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
        tabBarActiveTintColor: colors.sky,
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'SourceSans3-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Accounts', tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="trade"    options={{ title: 'Trade',    tabBarIcon: ({ color }) => <Ionicons name="trending-up"   size={22} color={color} /> }} />
      <Tabs.Screen name="research" options={{ title: 'Research', tabBarIcon: ({ color }) => <Ionicons name="search"        size={22} color={color} /> }} />
      <Tabs.Screen name="messages" options={{ title: 'Messages', tabBarIcon: ({ color }) => <Ionicons name="mail"          size={22} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',     tabBarIcon: ({ color }) => <Ionicons name="grid"          size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Balance tick — animate the COLOR only (digits never roll); flash to gain/loss then settle over ~600ms

// Trade Buy/Sell — animate the active pill position via reanimated withTiming(…, { duration: 200 })

// Chart range morph — re-render Path / animate `d` over 280ms ease-out

// Sticky Trade bar — always rendered above the tab bar / safe area; no entrance animation

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // tab / segmented / range change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // tap Trade / Place order
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // order accepted
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For directional gain/loss prefer Unicode triangles `▲ ▼` so the sign survives monochrome.

| Purpose | Ionicons |
|---------|----------|
| Accounts | `home` |
| Trade | `trending-up` |
| Research | `search` |
| Messages | `mail` |
| More | `grid` |
| Brokerage account | `trending-up` |
| Retirement account | `time-outline` |
| Bank account | `card-outline` |
| Alerts bell | `notifications-outline` |
| Day change up | `caret-up` (or `▲`) |
| Day change down | `caret-down` (or `▼`) |
| Back | `chevron-back` |
| Overflow | `ellipsis-horizontal` |
| Disclosure | `chevron-forward` |
| Transfer | `swap-horizontal` |
| Documents | `document-text-outline` |
| Order type | `options-outline` |

## 7. Platform Notes

- **Font choice**: Source Sans 3 (SIL OFL) is the closest free substitute for "Charles Modern"; bundle Regular→ExtraBold. All money values carry `fontVariant: ['tabular-nums']`.
- **Schwab Blue contrast**: always render `colors.onBlue` (`#002233`) on `colors.blue` — white text fails WCAG on this bright blue at body sizes.
- **Status bar**: `<StatusBar style="light" />` (the navy hero is dark even in light mode); `"light"` on dark.
- **Safe area**: wrap screens in `SafeAreaView`; the navy hero should extend under the status bar (use `edges={['left','right']}` on the scroll content and let the gradient bleed up). The sticky Trade bar + tab bar need bottom safe-area padding.
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface*` / `darkDivider`; keep the navy hero gradient on both modes; links/selected brighten to `sky`. Gain/loss swap to the brighter dark pair but meaning is fixed.
- **Accessibility**: never rely on color alone — keep `+`/`−` sign and `▲`/`▼`. Set `accessibilityLabel` on rows combining name + mask + value + signed change. Set `allowFontScaling={false}` on tab labels and ticket field values.
- **Charts**: `react-native-svg` for sparkline/quote charts; a `PanGestureHandler` mapping touch X to data index drives a crosshair + tabular value bubble.
- **Number formatting**: format currency/percentages with `Intl.NumberFormat` once, then render the string with the tabular style.
- **Reduce Motion**: respect `AccessibilityInfo.isReduceMotionEnabled()` — snap the Buy/Sell pill, skip chart morph and balance-tick color flash; keep the sign/arrow.
