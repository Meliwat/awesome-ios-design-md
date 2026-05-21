# Skyscanner (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Skyscanner's visual language into paste-ready Expo / React Native: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:        '#FFFFFF',
  surface1:      '#F5F7FA',
  surface2:      '#E9EDF2',
  divider:       '#E2E7EC',

  // Surfaces (dark)
  darkCanvas:    '#0B0F14',
  darkSurface1:  '#141A22',
  darkSurface2:  '#1C242E',
  darkDivider:   '#25303C',

  // Text
  textPrimary:    '#0E1B2C',
  textSecondary:  '#5C6B7A',
  textTertiary:   '#8B97A3',
  darkTextPrim:   '#E8EDF2',
  darkTextSec:    '#9AA7B4',

  // Brand
  skyBlue:        '#0770E3',
  skyBluePressed: '#0A5BBA',
  skyBright:      '#05A8FA',
  deepNavy:       '#05203C',

  // Price traffic-light (theme-invariant — never swap for dark)
  priceLow:       '#00A698',
  priceAvg:       '#FFB81C',
  priceHigh:      '#E5392E',

  // Tints
  skyBlueTint:    'rgba(7,112,227,0.08)',
  lowTint:        'rgba(0,166,152,0.16)',
  avgTint:        'rgba(255,184,28,0.16)',
  highTint:       'rgba(229,57,46,0.16)',
} as const;

export type SkyColor = keyof typeof colors;

// Pair a fare band to its color + tint
export const priceBands = {
  low:  { color: colors.priceLow,  tint: colors.lowTint,  label: 'Cheap' },
  avg:  { color: colors.priceAvg,  tint: colors.avgTint,  label: 'Average' },
  high: { color: colors.priceHigh, tint: colors.highTint, label: 'Expensive' },
} as const;
```

## 2. Typography

Load Skyscanner Relative via `expo-font` (fall back to System).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Relative-Regular':  require('../assets/fonts/SkyscannerRelative-Regular.ttf'),
    'Relative-Medium':   require('../assets/fonts/SkyscannerRelative-Medium.ttf'),
    'Relative-Bold':     require('../assets/fonts/SkyscannerRelative-Bold.ttf'),
    'Relative-Black':    require('../assets/fonts/SkyscannerRelative-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#0E1B2C' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  display:     { ...primary, fontFamily: 'Relative-Black',   fontSize: 32, lineHeight: 37, letterSpacing: -0.5 },
  screenTitle: { ...primary, fontFamily: 'Relative-Bold',    fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: 'Relative-Bold',    fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:   { ...primary, fontFamily: 'Relative-Bold',    fontSize: 18, lineHeight: 23 },
  fareLarge:   { ...primary, ...tnum, fontFamily: 'Relative-Black', fontSize: 19, lineHeight: 23 },
  body:        { ...primary, fontFamily: 'Relative-Regular', fontSize: 16, lineHeight: 23 },
  priceInline: { ...primary, ...tnum, fontFamily: 'Relative-Medium', fontSize: 15, lineHeight: 20 },
  time:        { ...primary, ...tnum, fontFamily: 'Relative-Bold',   fontSize: 17, lineHeight: 20 },
  meta:        { color: '#5C6B7A', fontFamily: 'Relative-Regular', fontSize: 14, lineHeight: 20 },
  airport:     { color: '#5C6B7A', fontFamily: 'Relative-Medium', fontSize: 12, lineHeight: 16, letterSpacing: 0.4 },
  fieldLabel:  { color: '#8B97A3', fontFamily: 'Relative-Medium', fontSize: 12, lineHeight: 16, letterSpacing: 0.2 },
  button:      { color: '#FFFFFF', fontFamily: 'Relative-Bold',   fontSize: 16, lineHeight: 16 },
  chip:        { fontFamily: 'Relative-Bold', fontSize: 13, lineHeight: 13 },
  tab:         { color: '#8B97A3', fontFamily: 'Relative-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  calDay:      { ...primary, ...tnum, fontFamily: 'Relative-Bold', fontSize: 11, lineHeight: 12 },
  calPrice:    { ...tnum, fontFamily: 'Relative-Medium', fontSize: 8, lineHeight: 9 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Stacked Search Card (From / To / Dates + Swap)

```tsx
// components/SearchCard.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function Field({ icon, label, value }: { icon: any; label: string; value: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 14, paddingHorizontal: 16 }}>
      <Ionicons name={icon} size={18} color={colors.skyBlue} />
      <View>
        <Text style={[typography.fieldLabel, { textTransform: 'uppercase' }]}>{label}</Text>
        <Text style={typography.priceInline}>{value}</Text>
      </View>
    </View>
  );
}

export function SearchCard({ from, to, dates, onSwap }:
  { from: string; to: string; dates: string; onSwap: () => void }) {
  return (
    <View style={{ marginHorizontal: 18 }}>
      <View style={{ backgroundColor: colors.canvas, borderRadius: 16, borderWidth: 1, borderColor: colors.divider, overflow: 'hidden' }}>
        <Field icon="location-outline" label="From" value={from} />
        <View style={{ height: 1, backgroundColor: colors.divider }} />
        <Field icon="location-outline" label="To" value={to} />
        <View style={{ height: 1, backgroundColor: colors.divider }} />
        <Field icon="calendar-outline" label="Depart · Return" value={dates} />
      </View>
      {/* swap button centered on the From–To divider */}
      <Pressable
        onPress={onSwap}
        style={{ position: 'absolute', right: 16, top: 56, width: 30, height: 30, borderRadius: 15,
          backgroundColor: colors.canvas, borderWidth: 1, borderColor: colors.divider,
          alignItems: 'center', justifyContent: 'center' }}
      >
        <Ionicons name="swap-vertical" size={14} color={colors.textSecondary} />
      </Pressable>
    </View>
  );
}
```

### Month Price Grid (signature surface)

```tsx
// components/PriceCalendar.tsx
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Band = 'low' | 'avg' | 'high';
type Day = { number: number; fare: number | null; band: Band | null };

const bandColor: Record<Band, string> = { low: colors.priceLow, avg: colors.priceAvg, high: colors.priceHigh };

export function PriceCalendar({ days, selected, onSelect }:
  { days: Day[]; selected: number | null; onSelect: (i: number) => void }) {
  return (
    <View style={{ paddingHorizontal: 18 }}>
      <Text style={typography.section}>Cheapest month — October</Text>
      <Text style={[typography.meta, { marginBottom: 12 }]}>Green is a good deal · prices per person</Text>
      <View style={{ flexDirection: 'row', flexWrap: 'wrap' }}>
        {days.map((d, i) => {
          const sel = selected === i;
          return (
            <Pressable
              key={i}
              disabled={d.fare == null}
              onPress={() => onSelect(i)}
              style={{
                width: `${100 / 7}%`,
                aspectRatio: 1,
                padding: 2.5,
                opacity: d.fare == null ? 0.35 : 1,
              }}
            >
              <View style={{
                flex: 1, alignItems: 'center', justifyContent: 'center', borderRadius: 7,
                backgroundColor: sel ? colors.skyBlue : colors.surface1,
                borderWidth: sel ? 0 : 1, borderColor: colors.divider,
              }}>
                <Text style={[typography.calDay, sel && { color: '#FFF' }]}>{d.number}</Text>
                {d.fare != null && (
                  <Text style={[typography.calPrice, { color: sel ? '#FFF' : bandColor[d.band!] }]}>
                    £{d.fare}
                  </Text>
                )}
              </View>
            </Pressable>
          );
        })}
      </View>
    </View>
  );
}
```

### Flight Result Row

```tsx
// components/FlightRow.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FlightRow(p: {
  depTime: string; depCode: string; arrTime: string; arrCode: string;
  duration: string; stops: string; fare: string; stopColor?: string;
}) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12, padding: 16,
      backgroundColor: colors.surface1, borderRadius: 14, borderWidth: 1, borderColor: colors.divider,
    }}>
      <View>
        <Text style={typography.time}>{p.depTime}</Text>
        <Text style={typography.airport}>{p.depCode}</Text>
      </View>
      <View style={{ flex: 1, alignItems: 'center' }}>
        <Text style={[typography.meta, { fontSize: 11 }]}>{p.duration}</Text>
        <View style={{ height: 1, alignSelf: 'stretch', backgroundColor: colors.divider, marginVertical: 6 }}>
          <View style={{ position: 'absolute', right: 0, top: -2, width: 5, height: 5, borderRadius: 2.5, backgroundColor: colors.skyBlue }} />
        </View>
        <Text style={{ fontFamily: 'Relative-Medium', fontSize: 10, color: p.stopColor ?? colors.priceLow }}>{p.stops}</Text>
      </View>
      <View>
        <Text style={typography.time}>{p.arrTime}</Text>
        <Text style={typography.airport}>{p.arrCode}</Text>
      </View>
      <View style={{ alignItems: 'flex-end' }}>
        <Text style={typography.fareLarge}>{p.fare}</Text>
        <Text style={typography.airport}>return</Text>
      </View>
    </View>
  );
}
```

### Best / Cheapest / Fastest Tabs

```tsx
// components/SortTabs.tsx
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Sort = 'Best' | 'Cheapest' | 'Fastest';

export function SortTabs({ value, fares, onChange }:
  { value: Sort; fares: Record<Sort, string>; onChange: (s: Sort) => void }) {
  const tabs: Sort[] = ['Best', 'Cheapest', 'Fastest'];
  return (
    <View style={{ flexDirection: 'row' }}>
      {tabs.map((s) => {
        const active = value === s;
        return (
          <Pressable key={s} onPress={() => onChange(s)} style={{ flex: 1, alignItems: 'center' }}>
            <Text style={[typography.chip, { color: active ? colors.textPrimary : colors.textSecondary }]}>{s}</Text>
            <Text style={[typography.priceInline, { color: active ? colors.textPrimary : colors.textSecondary }]}>{fares[s]}</Text>
            <View style={{ height: 2, alignSelf: 'stretch', marginTop: 4, backgroundColor: active ? colors.skyBlue : 'transparent' }} />
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Price Hint Chip & Everywhere Pill

```tsx
// components/PriceChip.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, priceBands } from '../theme/colors';
import { typography } from '../theme/typography';

export function PriceChip({ fare, band }: { fare: string; band: 'low' | 'avg' | 'high' }) {
  const b = priceBands[band];
  return (
    <View style={{ backgroundColor: b.tint, borderRadius: 8, paddingHorizontal: 14, paddingVertical: 7 }}>
      <Text style={[typography.chip, { color: b.color }]}>{fare} · {b.label}</Text>
    </View>
  );
}

export function EverywherePill({ onPress }: { onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 18 }}>
      <Ionicons name="globe-outline" size={14} color={colors.skyBright} />
      <Text style={{ fontFamily: 'Relative-Medium', fontSize: 12, color: colors.skyBright }}>
        Explore Everywhere — find the cheapest destination
      </Text>
    </Pressable>
  );
}
```

### Primary Search Button

```tsx
// components/SearchButton.tsx
import { Pressable, Text, useColorScheme } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SearchButton({ title, onPress }: { title: string; onPress: () => void }) {
  const dark = useColorScheme() === 'dark';
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        marginHorizontal: 18, height: 50, borderRadius: 12,
        backgroundColor: pressed ? colors.skyBluePressed : colors.skyBlue,
        alignItems: 'center', justifyContent: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
        shadowColor: colors.skyBlue, shadowOpacity: dark ? 0 : 0.45, shadowRadius: 18,
        shadowOffset: { width: 0, height: 8 }, elevation: dark ? 0 : 6,
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
        tabBarActiveTintColor:  colors.skyBlue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Relative-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Explore', tabBarIcon: ({ color }) => <Ionicons name="paper-plane" size={22} color={color} /> }} />
      <Tabs.Screen name="trips"   options={{ title: 'Trips',   tabBarIcon: ({ color }) => <Ionicons name="list" size={22} color={color} /> }} />
      <Tabs.Screen name="saved"   options={{ title: 'Saved',   tabBarIcon: ({ color }) => <Ionicons name="heart-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, FadeOut, useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';

// Calendar day select — animate background via withTiming on a progress value (150ms)
// selectionProgress.value = withTiming(1, { duration: 150 });

// Sort tab underline — animate left/width with withTiming (200ms ease-out)

// Swap From/To — rotate the swap glyph 180°
// rotation.value = withTiming(swapped ? 180 : 0, { duration: 250 });

// Live price streaming — staggered row reveal
// entering={FadeIn.duration(200).delay(index * 40)}

// Price-alert bell — quick scale bounce
// scale.value = withSequence(withTiming(1.15, { duration: 100 }), withTiming(1, { duration: 100 }));

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);          // calendar select, tab switch
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // price alert created
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Carrier logos: bundle as monochrome-tolerant SVG via `react-native-svg`.

| Purpose | Ionicons |
|---------|----------|
| Explore (tab) | `paper-plane` |
| Trips (tab) | `list` |
| Saved (tab) | `heart-outline` / `heart` |
| Profile (tab) | `person-circle-outline` |
| From / To | `location-outline` |
| Dates | `calendar-outline` |
| Swap | `swap-vertical` |
| Everywhere | `globe-outline` |
| Price alert | `notifications-outline` / `notifications` |
| Back | `chevron-back` |
| Filters | `options-outline` |
| Sort | `swap-vertical-outline` |
| Stop dot | `ellipse` |
| Refresh fares | `refresh` |
| Share | `share-outline` |
| Info / price banner | `information-circle-outline` |

## 7. Platform Notes

- **Font choice**: Skyscanner Relative is proprietary — for a clone, fall back to System (SF Pro on iOS); the Inter family is the closest free substitute for web previews
- **Tabular numbers**: set `fontVariant: ['tabular-nums']` on every fare/time/calendar-price `<Text>` so columns align — critical for the price grid
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the search card and tab bar need safe-area padding; bottom sheets inset above the home indicator
- **Dynamic Type**: keep `allowFontScaling={false}` on tab labels, airport codes, field labels, and the calendar day/price text (the 7-col grid is layout-sensitive); allow scaling on titles/body/fares
- **Keyboard**: use `KeyboardAvoidingView` so the From/To autocomplete dropdown floats above the keyboard
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkDivider`; **do not** swap `priceLow` / `priceAvg` / `priceHigh` — the traffic-light meaning is theme-invariant; drop the Search button shadow on dark
- **Color-blind safety**: always pair the price color with text ("Cheap"/"Average"/"Expensive") in chips and announce the band in `accessibilityLabel` on calendar cells
- **Live fares**: stream result rows into a `FlatList` as data arrives; use `FadeIn` with a per-index delay; debounce fare "tick" updates with a 150ms color crossfade
