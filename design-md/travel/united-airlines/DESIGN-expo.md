# United (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates United's visual language into paste-ready Expo / React Native code: a design-token module, themed components (flight detail → boarding pass + interactive seat map), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-brightness`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  uaBlue:         '#002244',
  uaBlueMid:      '#1B3D6E',
  rhapsody:       '#1414FF',
  rhapsodyPress:  '#0E0EC8',
  rhapsodyBright: '#4A4AFF',
  premierGold:    '#C2A14D',
  premierGoldBright: '#D8B863',

  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F3F5FA',
  surfacePressed: '#E5EAF3',
  dividerLight:   '#D4DCE9',

  // Surfaces (dark)
  darkCanvas:     '#0A1424',
  darkSurface1:   '#111E33',
  darkSurface2:   '#1A2A45',
  darkDivider:    '#243652',

  // Text
  textPrimary:      '#0C1F38',
  textSecondary:    '#566884',
  textTertiary:     '#8493AC',
  darkTextPrimary:  '#E9EEF6',
  darkTextSecondary:'#97A6BE',
  darkTextTertiary: '#5E7090',

  // Flight status (functional, constant)
  onTime:        '#1E8E5A',
  onTimeDark:    '#4FD18C',
  boarding:      '#1414FF',
  boardingDark:  '#7A7AFF',
  delayed:       '#C98A1E',
  delayedDark:   '#E0A943',
  canceled:      '#D8434F',
  canceledDark:  '#F0808A',
} as const;

export type UAColor = keyof typeof colors;

export const statusPairs = {
  onTime:   { fg: colors.onTimeDark,   bg: 'rgba(30,142,90,0.18)' },
  boarding: { fg: colors.boardingDark, bg: 'rgba(20,20,255,0.22)' },
  delayed:  { fg: colors.delayedDark,  bg: 'rgba(201,138,30,0.18)' },
  canceled: { fg: colors.canceledDark, bg: 'rgba(216,67,79,0.18)' },
} as const;
```

## 2. Typography

Load Open Sans (Apache 2.0, the closest free analog to United's licensed corporate face) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'OpenSans-Regular':   require('../assets/fonts/OpenSans-Regular.ttf'),
    'OpenSans-Medium':    require('../assets/fonts/OpenSans-Medium.ttf'),
    'OpenSans-SemiBold':  require('../assets/fonts/OpenSans-SemiBold.ttf'),
    'OpenSans-Bold':      require('../assets/fonts/OpenSans-Bold.ttf'),
    'OpenSans-ExtraBold': require('../assets/fonts/OpenSans-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#E9EEF6' } satisfies TextStyle;

export const typography = {
  routeIATA:  { ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 32, lineHeight: 34, letterSpacing: -0.5 },
  screenTitle:{ ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 26, lineHeight: 30, letterSpacing: -0.3 },
  section:    { ...primary, fontFamily: 'OpenSans-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  paxName:    { ...primary, fontFamily: 'OpenSans-Bold',      fontSize: 18, lineHeight: 22 },
  dataLarge:  { ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 22, lineHeight: 24 },
  body:       { ...primary, fontFamily: 'OpenSans-Regular',   fontSize: 16, lineHeight: 24 },
  dataInline: { ...primary, fontFamily: 'OpenSans-SemiBold',  fontSize: 15, lineHeight: 20 },
  meta:       { color: '#97A6BE', fontFamily: 'OpenSans-Regular', fontSize: 14, lineHeight: 20 },
  eyebrow:    { fontFamily: 'OpenSans-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.6 },
  cellLabel:  { color: '#5E7090', fontFamily: 'OpenSans-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.6 },
  button:     { color: '#FFFFFF', fontFamily: 'OpenSans-Bold', fontSize: 16, lineHeight: 16, letterSpacing: 0.2 },
  tab:        { fontFamily: 'OpenSans-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  statusPill: { fontFamily: 'OpenSans-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Boarding Pass (United Blue header + perforation)

```tsx
// components/BoardingPass.tsx
import { View, Text, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function Barcode() {
  const dark = useColorScheme() === 'dark';
  const bar = dark ? colors.darkTextPrimary : colors.textPrimary;
  return (
    <View style={{ flexDirection: 'row', height: 54, borderRadius: 8, overflow: 'hidden' }}>
      {Array.from({ length: 64 }).map((_, i) => (
        <View key={i} style={{ width: i % 3 === 0 ? 3 : 1, marginRight: 2, backgroundColor: bar, height: '100%' }} />
      ))}
    </View>
  );
}

function Perforation() {
  return (
    <View style={{ height: 20, justifyContent: 'center', marginVertical: 16 }}>
      <View style={{ borderTopWidth: 1, borderColor: colors.darkDivider, borderStyle: 'dashed' }} />
      <View style={{ position: 'absolute', left: -28, width: 20, height: 20, borderRadius: 10, backgroundColor: colors.darkCanvas }} />
      <View style={{ position: 'absolute', right: -28, width: 20, height: 20, borderRadius: 10, backgroundColor: colors.darkCanvas }} />
    </View>
  );
}

export function BoardingPass({
  paxName, gate, seat, boards, premierGroup,
}: { paxName: string; gate: string; seat: string; boards: string; premierGroup: string }) {
  const Cell = ({ label, value, gold }: { label: string; value: string; gold?: boolean }) => (
    <View style={{ flex: 1 }}>
      <Text style={typography.cellLabel}>{label}</Text>
      <Text style={[typography.dataLarge, gold && { color: colors.premierGoldBright }]}>{value}</Text>
    </View>
  );
  return (
    <View style={{
      borderRadius: 16, backgroundColor: colors.darkSurface1,
      borderWidth: 1, borderColor: colors.darkDivider, overflow: 'hidden',
      shadowColor: '#000', shadowOpacity: 0.55, shadowRadius: 28, shadowOffset: { width: 0, height: 10 }, elevation: 8,
    }}>
      <View style={{
        flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center',
        backgroundColor: colors.uaBlue, paddingHorizontal: 18, paddingVertical: 12,
      }}>
        <Text style={{ fontFamily: 'OpenSans-ExtraBold', fontSize: 13, color: '#FFF', letterSpacing: 0.5 }}>◍ UNITED</Text>
        <Text style={{ fontFamily: 'OpenSans-Bold', fontSize: 11, color: colors.premierGoldBright }}>{premierGroup}</Text>
      </View>
      <View style={{ padding: 18 }}>
        <Text style={[typography.cellLabel, { color: colors.darkTextSecondary }]}>PASSENGER</Text>
        <Text style={[typography.paxName, { marginTop: 2 }]}>{paxName.toUpperCase()}</Text>
        <View style={{ flexDirection: 'row', gap: 16, marginTop: 16 }}>
          <Cell label="GATE" value={gate} />
          <Cell label="SEAT" value={seat} />
          <Cell label="BOARDS" value={boards} gold />
        </View>
        <Perforation />
        <Barcode />
      </View>
    </View>
  );
}
```

### Interactive Seat Map

```tsx
// components/SeatMap.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Kind = 'standard' | 'econPlus' | 'taken' | 'selected' | 'aisle';

const fillFor = (k: Kind) =>
  k === 'selected' ? colors.rhapsody
  : k === 'econPlus' ? 'rgba(20,20,255,0.18)'
  : k === 'taken' ? colors.darkCanvas
  : colors.darkSurface2;
const borderFor = (k: Kind) =>
  k === 'selected' ? colors.rhapsodyBright
  : k === 'econPlus' ? 'rgba(74,74,255,0.4)'
  : colors.darkDivider;

export function SeatMap({ rows }: { rows: Kind[][] }) {
  const [sel, setSel] = useState('7A');
  return (
    <View style={{
      borderRadius: 16, backgroundColor: colors.darkSurface1,
      borderWidth: 1, borderColor: colors.darkDivider, padding: 18,
    }}>
      <Text style={{ fontFamily: 'OpenSans-Bold', fontSize: 13, color: colors.darkTextPrimary }}>Choose your seat</Text>
      <Text style={{ ...typography.meta, marginTop: 4, marginBottom: 16 }}>
        Economy Plus · Row 7–9 · {sel} selected
      </Text>
      <View style={{ gap: 6 }}>
        {rows.map((row, r) => (
          <View key={r} style={{ flexDirection: 'row', gap: 6 }}>
            {row.map((k, c) => (
              <Pressable
                key={c}
                onPress={() => {
                  if (k !== 'standard' && k !== 'econPlus') return;
                  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
                }}
                style={{
                  flex: 1, aspectRatio: 1,
                  borderTopLeftRadius: 6, borderTopRightRadius: 6,
                  borderBottomLeftRadius: 3, borderBottomRightRadius: 3,
                  backgroundColor: k === 'aisle' ? 'transparent' : fillFor(k),
                  borderWidth: k === 'aisle' ? 0 : 1, borderColor: borderFor(k),
                }}
              />
            ))}
          </View>
        ))}
      </View>
      <View style={{ flexDirection: 'row', gap: 16, marginTop: 16 }}>
        {[['rgba(20,20,255,0.4)', 'Econ+'], [colors.rhapsody, 'Selected'], [colors.darkCanvas, 'Taken']].map(([c, t]) => (
          <View key={t} style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
            <View style={{ width: 10, height: 10, borderRadius: 3, backgroundColor: c }} />
            <Text style={{ fontFamily: 'OpenSans-Regular', fontSize: 11, color: colors.darkTextSecondary }}>{t}</Text>
          </View>
        ))}
      </View>
    </View>
  );
}
```

### Flight Detail Header + Buttons + Status Chip

```tsx
// components/FlightDetailHeader.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, statusPairs } from '../theme/colors';
import { typography } from '../theme/typography';

export function FlightDetailHeader({
  flightNo, origin, originCity, dest, destCity, dur, depRow, arrRow, status,
}: Record<string, string>) {
  const p = statusPairs.onTime;
  return (
    <LinearGradient colors={[colors.uaBlue, '#04152C']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{ paddingHorizontal: 18, paddingTop: 8, paddingBottom: 18 }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}>
        <Text style={[typography.eyebrow, { color: '#7FA0CE', letterSpacing: 1 }]}>{flightNo.toUpperCase()}</Text>
        <Text style={[typography.statusPill, { color: p.fg, backgroundColor: p.bg,
          paddingHorizontal: 10, paddingVertical: 4, borderRadius: 999, overflow: 'hidden' }]}>{status}</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'flex-start', marginTop: 14 }}>
        <View>
          <Text style={[typography.routeIATA, { color: '#FFF' }]}>{origin}</Text>
          <Text style={{ fontFamily: 'OpenSans-Regular', fontSize: 12, color: '#9FBADC', marginTop: 2 }}>{originCity}</Text>
        </View>
        <View style={{ flex: 1, alignItems: 'center', paddingTop: 8 }}>
          <Ionicons name="airplane" size={20} color="#FFF" />
          <Text style={{ fontFamily: 'OpenSans-Regular', fontSize: 11, color: '#9FBADC', marginTop: 4 }}>{dur}</Text>
        </View>
        <View style={{ alignItems: 'flex-end' }}>
          <Text style={[typography.routeIATA, { color: '#FFF' }]}>{dest}</Text>
          <Text style={{ fontFamily: 'OpenSans-Regular', fontSize: 12, color: '#9FBADC', marginTop: 2 }}>{destCity}</Text>
        </View>
      </View>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 6 }}>
        <Text style={{ fontFamily: 'OpenSans-SemiBold', fontSize: 13, color: '#CFE0F2' }}>{depRow}</Text>
        <Text style={{ fontFamily: 'OpenSans-SemiBold', fontSize: 13, color: '#CFE0F2' }}>{arrRow}</Text>
      </View>
    </LinearGradient>
  );
}
```

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors, statusPairs } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onPress(); }}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.rhapsodyPress : colors.rhapsody,
        borderRadius: 4, paddingVertical: 14, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function StatusChip({ kind, label }: { kind: keyof typeof statusPairs; label: string }) {
  const p = statusPairs[kind];
  return (
    <Text style={[typography.statusPill, { color: p.fg, backgroundColor: p.bg,
      paddingHorizontal: 14, paddingVertical: 7, borderRadius: 999, overflow: 'hidden' }]}>{label}</Text>
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
        tabBarActiveTintColor:  colors.rhapsodyBright,
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'OpenSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"       options={{ title: 'Home',        tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="book"        options={{ title: 'Book',        tabBarIcon: ({ color }) => <Ionicons name="search"        size={22} color={color} /> }} />
      <Tabs.Screen name="flights"     options={{ title: 'Flights',     tabBarIcon: ({ color }) => <Ionicons name="airplane"      size={22} color={color} /> }} />
      <Tabs.Screen name="trips"       options={{ title: 'Trips',       tabBarIcon: ({ color }) => <Ionicons name="albums"        size={22} color={color} /> }} />
      <Tabs.Screen name="mileageplus" options={{ title: 'MileagePlus', tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, withTiming, withSpring, Easing } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import * as Brightness from 'expo-brightness';

// Aircraft glide on flight-detail appear
aircraftProgress.value = withTiming(1, { duration: 900, easing: Easing.inOut(Easing.ease) });

// Seat select — spring pop + light haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
seatScale.value = withSpring(1, { damping: 9, stiffness: 200 });

// Status change — cross-fade + soft haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
statusOpacity.value = withTiming(0, { duration: 120 }, () => { /* swap, fade back */ });

// Boarding pass open — brightness boost (restore on dismiss)
const prev = await Brightness.getBrightnessAsync();
await Brightness.setBrightnessAsync(1);
// onDismiss: await Brightness.setBrightnessAsync(prev);

// Check-in success
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Book | `search` |
| Flights | `airplane` |
| Trips | `albums` |
| MileagePlus | `person-circle` |
| Aircraft (header) | `airplane` |
| Seat | `body` |
| Bag tracker | `briefcase` |
| Add to Wallet | `wallet` |
| Boarding pass | `qr-code` |
| Upgrade list | `arrow-up-circle` |
| Gate change | `warning` |
| Premier tier | `star` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Refresh | `refresh` |

## 7. Platform Notes

- **Font choice**: Open Sans (Apache 2.0) is the free analog to United's licensed corporate face — ship all five weights
- **Status bar**: `<StatusBar style="light" />` over the navy flight-detail header and on dark mode; `"dark"` on light content screens
- **Safe area**: wrap screens in `SafeAreaView`; the flight-detail header extends under the status bar (translucent), content offsets below the Dynamic Island; the boarding-pass barcode must stay above the home-indicator inset
- **Brightness**: use `expo-brightness` to max screen brightness when the full-screen pass opens; restore the user's prior value on dismiss
- **Wallet**: use `expo-apple-passkit` (or a dev-client native module) to add the `.pkpass`; keep it live via push so a gate change updates the lock screen
- **Dynamic Type**: keep the barcode, seat-map cells, 10pt cell labels, 10pt tab labels, and status-pill text at `allowFontScaling={false}`; let route/title/body scale
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1` etc.; the barcode bar color flips to light so it stays machine-scannable
- **Seat map performance**: for large cabins render rows with `FlatList` or windowing; keep the tap target ≥ 32pt; the aisle column is a transparent flex cell
- **Accessibility**: label the pass, each seat, and status chip with full strings; status colors are paired against translucent fills validated for AA contrast
