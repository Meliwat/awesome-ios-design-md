# KAYAK (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates KAYAK's visual language into paste-ready Expo / React Native: a design-token module, themed components (fare card, price-calendar strip, forecast banner, fare-compare matrix), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F4F5F7',
  surfacePressed: '#E9EBEE',
  divider:        '#E2E5E9',

  // Surfaces (dark) — near-neutral, NO brand tint
  darkCanvas:     '#101214',
  darkSurface1:   '#181B1E',
  darkSurface2:   '#21262B',
  darkDivider:    '#2C3338',

  // Text
  textPrimary:    '#16191C',
  textSecondary:  '#5C656E',
  textTertiary:   '#8B949C',
  darkTextPrimary:   '#E9ECEF',
  darkTextSecondary: '#9BA3AB',

  // Brand (single accent)
  orange:        '#FF690F',
  orangePressed: '#E0560A',
  orangeSoft:    '#FF8A42',
  link:          '#2E7CF6',

  // Functional semantic (NOT brand)
  priceLow:  '#1E9E5A',
  priceHigh: '#E5484D',
  wait:      '#E8A317',
} as const;

export type KayakColor = keyof typeof colors;
export type PriceBand = 'low' | 'mid' | 'high';
export type Advice = 'buy' | 'wait' | 'rise';

export function priceColor(band: PriceBand, dark = false): string {
  if (band === 'low') return colors.priceLow;
  if (band === 'high') return colors.priceHigh;
  return dark ? colors.darkTextPrimary : colors.textPrimary;
}

export function adviceColor(a: Advice): string {
  if (a === 'buy') return colors.priceLow;
  if (a === 'wait') return colors.wait;
  return colors.priceHigh;
}
```

## 2. Typography

KAYAK's product face is proprietary — bundle it via `expo-font` only if licensed; otherwise fall back to system / Inter. Numeric `Text` uses `fontVariant: ['tabular-nums']`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const F = { reg: 'Inter-Regular', semi: 'Inter-SemiBold', bold: 'Inter-Bold', heavy: 'Inter-ExtraBold' };
const primary = { color: '#16191C' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  display:  { ...primary, fontFamily: F.heavy, fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  route:    { ...primary, fontFamily: F.heavy, fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:  { ...primary, fontFamily: F.bold,  fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  body:     { ...primary, fontFamily: F.reg,   fontSize: 16, lineHeight: 24 },
  label:    { ...primary, fontFamily: F.semi,  fontSize: 14, lineHeight: 19 },
  meta:     { color: '#5C656E', fontFamily: F.reg,  fontSize: 13, lineHeight: 18 },
  calDay:   { color: '#5C656E', fontFamily: F.semi, fontSize: 11, lineHeight: 12 },
  tag:      { color: '#FFFFFF', fontFamily: F.bold, fontSize: 11, lineHeight: 12, letterSpacing: 0.3 },
  button:   { color: '#FFFFFF', fontFamily: F.bold, fontSize: 16, lineHeight: 16 },
  tab:      { fontFamily: F.semi, fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  price:    { ...primary, fontFamily: F.heavy, fontSize: 20, lineHeight: 24, ...tnum },
  legTime:  { ...primary, fontFamily: F.heavy, fontSize: 18, lineHeight: 22, ...tnum },
  calPrice: { fontFamily: F.heavy, fontSize: 13, lineHeight: 14, ...tnum },
  matrixNum:{ fontFamily: F.heavy, fontSize: 12, lineHeight: 14, ...tnum },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Fare Card

```tsx
// components/FareCard.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FareCard({
  airlineCode, airlineName, isHackerFare,
  departTime, departCode, arriveTime, arriveCode,
  duration, stopsText, nonstop, provider, price,
}: {
  airlineCode: string; airlineName: string; isHackerFare?: boolean;
  departTime: string; departCode: string; arriveTime: string; arriveCode: string;
  duration: string; stopsText: string; nonstop: boolean; provider: string; price: number;
}) {
  return (
    <View style={{
      backgroundColor: colors.canvas, borderRadius: 14,
      borderWidth: 0.5, borderColor: colors.divider, padding: 14, marginBottom: 12,
      shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 8, shadowOffset: { width: 0, height: 2 }, elevation: 2,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
          <View style={{
            width: 26, height: 26, borderRadius: 7, backgroundColor: colors.surfaceGray,
            alignItems: 'center', justifyContent: 'center',
          }}>
            <Text style={{ fontFamily: 'Inter-ExtraBold', fontSize: 10, color: colors.textSecondary }}>{airlineCode}</Text>
          </View>
          <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 12, color: colors.textSecondary }}>{airlineName}</Text>
        </View>
        {isHackerFare ? (
          <View style={{ backgroundColor: colors.orange, borderRadius: 5, paddingVertical: 3, paddingHorizontal: 7 }}>
            <Text style={typography.tag}>HACKER FARE</Text>
          </View>
        ) : null}
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, marginTop: 12 }}>
        <View style={{ alignItems: 'center' }}>
          <Text style={typography.legTime}>{departTime}</Text>
          <Text style={{ fontSize: 11, color: colors.textSecondary, marginTop: 2 }}>{departCode}</Text>
        </View>
        <View style={{ flex: 1, alignItems: 'center' }}>
          <Text style={{ fontSize: 11, color: colors.textSecondary }}>{duration}</Text>
          <View style={{ height: 1, backgroundColor: colors.divider, alignSelf: 'stretch', marginVertical: 6 }} />
          <Text style={{ fontSize: 11, fontFamily: 'Inter-Bold', color: nonstop ? colors.textSecondary : colors.orange }}>{stopsText}</Text>
        </View>
        <View style={{ alignItems: 'center' }}>
          <Text style={typography.legTime}>{arriveTime}</Text>
          <Text style={{ fontSize: 11, color: colors.textSecondary, marginTop: 2 }}>{arriveCode}</Text>
        </View>
      </View>

      <View style={{
        flexDirection: 'row', alignItems: 'flex-end', justifyContent: 'space-between',
        marginTop: 14, paddingTop: 12, borderTopWidth: 0.5, borderTopColor: colors.divider,
      }}>
        <Text style={{ fontSize: 11, color: colors.textSecondary }}>{provider}</Text>
        <View style={{ alignItems: 'flex-end' }}>
          <Text style={typography.price}>${price}</Text>
          <Text style={{ fontSize: 11, color: colors.textSecondary }}>round-trip</Text>
        </View>
      </View>
    </View>
  );
}
```

### Price-Calendar Strip

```tsx
// components/PriceCalendarStrip.tsx
import { ScrollView, Pressable, Text } from 'react-native';
import { colors, priceColor, PriceBand } from '../theme/colors';
import { typography } from '../theme/typography';

type Day = { label: string; price: number; band: PriceBand };

export function PriceCalendarStrip({
  days, selected, onSelect,
}: { days: Day[]; selected: number; onSelect: (i: number) => void }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ gap: 6, paddingHorizontal: 16 }}>
      {days.map((d, i) => {
        const sel = i === selected;
        return (
          <Pressable
            key={i}
            onPress={() => onSelect(i)}
            style={{
              width: 50, alignItems: 'center', paddingVertical: 8, borderRadius: 10,
              backgroundColor: sel ? colors.orange : colors.canvas,
              borderWidth: sel ? 0 : 0.5, borderColor: colors.divider,
            }}
          >
            <Text style={[typography.calDay, sel && { color: '#FFFFFF' }]}>{d.label}</Text>
            <Text style={[typography.calPrice, { marginTop: 4, color: sel ? '#FFFFFF' : priceColor(d.band) }]}>
              ${d.price}
            </Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

### Forecast Banner

```tsx
// components/ForecastBanner.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, adviceColor, Advice } from '../theme/colors';

const GLYPH: Record<Advice, any> = { buy: 'checkmark-circle', wait: 'time', rise: 'trending-up' };

export function ForecastBanner({ advice, title, subtitle }: { advice: Advice; title: string; subtitle: string }) {
  const tint = advice === 'rise' ? colors.orange : adviceColor(advice);
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10,
      marginHorizontal: 16, paddingVertical: 11, paddingHorizontal: 13, borderRadius: 10,
      backgroundColor: `${tint}1F`, borderWidth: 0.5, borderColor: `${tint}66`,
    }}>
      <Ionicons name={GLYPH[advice]} size={18} color={tint} />
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'Inter-Bold', fontSize: 12, color: colors.textPrimary }}>{title}</Text>
        <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.textSecondary, marginTop: 1 }}>{subtitle}</Text>
      </View>
    </View>
  );
}
```

### Fare-Compare Matrix

```tsx
// components/FareMatrix.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Row = { airline: string; cells: (number | null)[]; bestIndex: number | null };

export function FareMatrix({ columns, rows }: { columns: string[]; rows: Row[] }) {
  return (
    <View style={{ borderWidth: 0.5, borderColor: colors.divider, borderRadius: 10, overflow: 'hidden' }}>
      <View style={{ flexDirection: 'row', backgroundColor: colors.surfaceGray, paddingVertical: 10, paddingHorizontal: 8 }}>
        <Text style={[typography.label, { flex: 1.4 }]}>Airline</Text>
        {columns.map((c) => (
          <Text key={c} style={{ flex: 1, textAlign: 'center', fontFamily: 'Inter-Regular', fontSize: 12, color: colors.textSecondary }}>{c}</Text>
        ))}
      </View>
      {rows.map((row, ri) => (
        <View key={ri} style={{
          flexDirection: 'row', paddingVertical: 10, paddingHorizontal: 8,
          borderTopWidth: 0.5, borderTopColor: colors.divider,
        }}>
          <Text style={[typography.label, { flex: 1.4 }]}>{row.airline}</Text>
          {row.cells.map((v, ci) => {
            const best = row.bestIndex === ci;
            return (
              <Text
                key={ci}
                style={[
                  typography.matrixNum,
                  { flex: 1, textAlign: 'center',
                    color: v == null ? colors.textTertiary : best ? colors.priceLow : colors.textPrimary },
                ]}
              >
                {v == null ? '—' : `$${v}`}
              </Text>
            );
          })}
        </View>
      ))}
    </View>
  );
}
```

### Primary Button

```tsx
// components/ViewDealButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ViewDealButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.orangePressed : colors.orange,
        borderRadius: 10, paddingVertical: 14, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
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
        tabBarActiveTintColor: colors.orange,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Search',       tabBarIcon: ({ color }) => <Ionicons name="search"        size={22} color={color} /> }} />
      <Tabs.Screen name="trends"  options={{ title: 'Trends',       tabBarIcon: ({ color }) => <Ionicons name="trending-up"   size={22} color={color} /> }} />
      <Tabs.Screen name="trips"   options={{ title: 'Trips',        tabBarIcon: ({ color }) => <Ionicons name="bookmark-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="alerts"  options={{ title: 'Price Alerts', tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile',      tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Calendar day select — orange fill is immediate; cross-dissolve the fare list
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
// <Animated.View key={selectedDate} entering={FadeIn.duration(220)} exiting={FadeOut.duration(120)}>
// show a skeleton shimmer while re-pricing

// Forecast banner reveal
// entering={FadeIn.duration(240)} (+ translateY 0 from -8 via withTiming)

// Matrix cell tap — orange tint flash
bg.value = withSequence(withTiming(1, { duration: 140 }), withTiming(0, { duration: 140 }));

// Price-drop pulse (Price Alerts)
scale.value = withRepeat(withTiming(1.08, { duration: 180 }), 2, true);

// Sort/filter sheet — @gorhom/bottom-sheet or expo-router modal, ~300ms

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);  // calendar select, segment, filter
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // price-drop captured
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). KAYAK's iconography is a clean utilitarian set.

| Purpose | Ionicons |
|---------|----------|
| Search (tab) | `search` |
| Trends (tab) | `trending-up` |
| Trips (tab) | `bookmark-outline` |
| Price Alerts (tab) | `notifications-outline` |
| Profile (tab) | `person-circle-outline` |
| Route arrow | `arrow-forward` |
| Forecast rise | `trending-up` |
| Forecast buy | `checkmark-circle` |
| Forecast wait | `time` |
| Swap origin/dest | `swap-vertical` |
| Filter | `options-outline` |
| Sort | `funnel-outline` |
| Flight | `airplane` |
| Stay | `bed-outline` |
| Car | `car-outline` |
| Calendar | `calendar-outline` |
| Travelers | `people-outline` |
| Price up | `arrow-up` |
| Price down | `arrow-down` |
| Share | `share-outline` |

## 7. Platform Notes

- **Font**: KAYAK's product face is proprietary — bundle only if licensed; otherwise ship Inter and let SF Pro/Roboto fall back.
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every price/time/duration/matrix `Text` — the calendar and matrix only align with tabular digits.
- **Status bar**: `<StatusBar style="dark" />` light, `"light"` dark.
- **Safe area**: wrap screens in `SafeAreaView`; sticky sort/filter bar and bottom tab need safe-area padding; calendar/filter sheets respect the bottom inset.
- **Dynamic Type**: `<Text>` respects system scale; set `allowFontScaling={false}` on calendar day/price, the Hacker Fare tag, tab labels, and matrix cells (layout-sensitive).
- **Don't rely on color alone for price band**: pair the color with an `accessibilityLabel` ("lowest price these dates") so the cheapness signal is conveyed non-visually.
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` (near-neutral, no tint) / `darkSurface1`; pass `dark` into `priceColor` so the `mid` band resolves correctly; keep orange + functional semantics fixed.
- **Maps / Explore**: the Trends/Explore tab uses `react-native-maps`; render destination price bubbles as custom markers.
- **Accessibility**: give the fare card an `accessibilityLabel` summarizing airline/leg/price; the forecast banner reads title + subtitle; the Hacker Fare tag is announced as "two separate one-way tickets".
