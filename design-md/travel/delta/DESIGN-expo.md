# Delta (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Delta's visual language into paste-ready Expo / React Native code: a design-token module, themed components (boarding pass + flight-status timeline), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-brightness`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  deltaBlue:        '#003268',
  deltaBlueBright:  '#0F4C9A',
  deltaRed:         '#C8102E',
  deltaRedPressed:  '#A30D26',
  widgetMaroon:     '#862633',
  skyMilesGold:     '#C99700',

  // Surfaces (light)
  canvas:           '#FFFFFF',
  surfaceGray:      '#F4F6F9',
  surfacePressed:   '#E8ECF3',
  dividerLight:     '#D6DCE7',

  // Surfaces (dark)
  darkCanvas:       '#0B1320',
  darkSurface1:     '#131C2B',
  darkSurface2:     '#1C2738',
  darkDivider:      '#25314A',

  // Text
  textPrimary:      '#10243F',
  textSecondary:    '#5A6A82',
  textTertiary:     '#8A98AE',
  darkTextPrimary:  '#E8ECF3',
  darkTextSecondary:'#9AA6BC',
  darkTextTertiary: '#5F6E88',

  // Flight status (functional, constant)
  onTime:        '#1E8E5A',
  onTimeDark:    '#4FD18C',
  boarding:      '#0F4C9A',
  boardingDark:  '#6FB0EA',
  delayed:       '#C99700',
  delayedDark:   '#E0B743',
  canceled:      '#C8102E',
  canceledDark:  '#E0414E',
} as const;

export type DeltaColor = keyof typeof colors;

// Status chip pairs (foreground / translucent fill)
export const statusPairs = {
  onTime:   { fg: colors.onTimeDark,   bg: 'rgba(30,142,90,0.18)' },
  boarding: { fg: colors.boardingDark, bg: 'rgba(15,76,154,0.24)' },
  delayed:  { fg: colors.delayedDark,  bg: 'rgba(201,151,0,0.18)' },
  canceled: { fg: colors.canceledDark, bg: 'rgba(200,16,46,0.18)' },
} as const;
```

## 2. Typography

Load Open Sans (Apache 2.0, the closest free analog to Delta's Whitney-class corporate face) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'OpenSans-Regular':    require('../assets/fonts/OpenSans-Regular.ttf'),
    'OpenSans-Medium':     require('../assets/fonts/OpenSans-Medium.ttf'),
    'OpenSans-SemiBold':   require('../assets/fonts/OpenSans-SemiBold.ttf'),
    'OpenSans-Bold':       require('../assets/fonts/OpenSans-Bold.ttf'),
    'OpenSans-ExtraBold':  require('../assets/fonts/OpenSans-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#E8ECF3' } satisfies TextStyle;

export const typography = {
  routeIATA:  { ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 34, lineHeight: 36, letterSpacing: -0.5 },
  screenTitle:{ ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 26, lineHeight: 30, letterSpacing: -0.3 },
  section:    { ...primary, fontFamily: 'OpenSans-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  paxName:    { ...primary, fontFamily: 'OpenSans-Bold',      fontSize: 18, lineHeight: 22 },
  dataLarge:  { ...primary, fontFamily: 'OpenSans-ExtraBold', fontSize: 22, lineHeight: 24 },
  body:       { ...primary, fontFamily: 'OpenSans-Regular',   fontSize: 16, lineHeight: 24 },
  dataInline: { ...primary, fontFamily: 'OpenSans-SemiBold',  fontSize: 15, lineHeight: 20 },
  meta:       { color: '#9AA6BC', fontFamily: 'OpenSans-Regular', fontSize: 14, lineHeight: 20 },
  eyebrow:    { fontFamily: 'OpenSans-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.6 },
  cellLabel:  { color: '#5F6E88', fontFamily: 'OpenSans-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.6 },
  button:     { color: '#FFFFFF', fontFamily: 'OpenSans-Bold', fontSize: 16, lineHeight: 16, letterSpacing: 0.2 },
  tab:        { fontFamily: 'OpenSans-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  statusPill: { fontFamily: 'OpenSans-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Digital Boarding Pass

```tsx
// components/BoardingPass.tsx
import { View, Text, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function Barcode() {
  const dark = useColorScheme() === 'dark';
  const bar = dark ? colors.darkTextPrimary : colors.textPrimary;
  return (
    <View style={{ flexDirection: 'row', height: 56, borderRadius: 8, overflow: 'hidden' }}>
      {Array.from({ length: 64 }).map((_, i) => (
        <View key={i} style={{ width: i % 3 === 0 ? 3 : 1, marginRight: 2, backgroundColor: bar, flex: undefined, height: '100%' }} />
      ))}
    </View>
  );
}

export function BoardingPass({
  paxName, gate, seat, group, boardsAt, zone,
}: { paxName: string; gate: string; seat: string; group: string; boardsAt: string; zone: string }) {
  const Cell = ({ label, value, gold }: { label: string; value: string; gold?: boolean }) => (
    <View style={{ flex: 1 }}>
      <Text style={typography.cellLabel}>{label}</Text>
      <Text style={[typography.dataLarge, gold && { color: colors.skyMilesGold }]}>{value}</Text>
    </View>
  );
  return (
    <View style={{
      borderRadius: 18, backgroundColor: colors.darkSurface1,
      borderWidth: 1, borderColor: colors.darkDivider, overflow: 'hidden',
      shadowColor: '#000', shadowOpacity: 0.55, shadowRadius: 28, shadowOffset: { width: 0, height: 10 }, elevation: 8,
    }}>
      <LinearGradient colors={[colors.deltaBlue, colors.deltaRed]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
        style={{ height: 6 }} />
      <View style={{ padding: 18 }}>
        <Text style={[typography.cellLabel, { color: colors.darkTextSecondary }]}>PASSENGER</Text>
        <Text style={[typography.paxName, { marginTop: 2 }]}>{paxName.toUpperCase()}</Text>
        <View style={{ flexDirection: 'row', gap: 18, marginTop: 16 }}>
          <Cell label="GATE" value={gate} />
          <Cell label="SEAT" value={seat} />
          <Cell label="GROUP" value={group} gold />
        </View>
        <View style={{
          flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center',
          marginTop: 16, padding: 14, borderRadius: 10, backgroundColor: colors.darkSurface2,
        }}>
          <Text style={{ fontFamily: 'OpenSans-SemiBold', fontSize: 11, color: colors.darkTextSecondary }}>
            Boards {boardsAt} · Zone
          </Text>
          <Text style={{ fontFamily: 'OpenSans-Bold', fontSize: 14, color: colors.darkTextPrimary }}>{zone}</Text>
        </View>
        <View style={{ marginTop: 16 }}><Barcode /></View>
      </View>
    </View>
  );
}
```

### Flight Status Timeline

```tsx
// components/FlightStatusTimeline.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Step = { title: string; meta: string; done: boolean };

export function FlightStatusTimeline({ steps }: { steps: Step[] }) {
  return (
    <View style={{
      borderRadius: 16, backgroundColor: colors.darkSurface1,
      borderWidth: 1, borderColor: colors.darkDivider, padding: 18,
    }}>
      <Text style={{ fontFamily: 'OpenSans-Bold', fontSize: 13, color: colors.darkTextPrimary, marginBottom: 16 }}>
        Flight Status
      </Text>
      <View style={{ flexDirection: 'row', gap: 14 }}>
        <View style={{ alignItems: 'center' }}>
          {steps.map((s, i) => (
            <View key={i} style={{ alignItems: 'center', flex: 1 }}>
              <View style={{
                width: 12, height: 12, borderRadius: 6,
                backgroundColor: s.done ? colors.onTimeDark : colors.darkTextSecondary,
                borderWidth: 2, borderColor: s.done ? colors.onTimeDark : colors.darkTextSecondary,
              }} />
              {i < steps.length - 1 && (
                <View style={{ width: 2, flex: 1, backgroundColor: colors.darkDivider, marginVertical: 2 }} />
              )}
            </View>
          ))}
        </View>
        <View style={{ flex: 1, gap: 18 }}>
          {steps.map((s, i) => (
            <View key={i}>
              <Text style={{ fontFamily: 'OpenSans-Bold', fontSize: 13, color: colors.darkTextPrimary }}>{s.title}</Text>
              <Text style={[typography.meta, { marginTop: 2 }]}>{s.meta}</Text>
            </View>
          ))}
        </View>
      </View>
    </View>
  );
}
```

### Trip Header

```tsx
// components/TripHeader.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { statusPairs } from '../theme/colors';

export function TripHeader({
  flightNo, origin, dest, status,
}: { flightNo: string; origin: string; dest: string; status: string }) {
  const pair = statusPairs.onTime;
  return (
    <LinearGradient colors={[colors.deltaBlue, '#06203F']} start={{ x: 0, y: 0 }} end={{ x: 0, y: 1 }}
      style={{ paddingHorizontal: 18, paddingTop: 8, paddingBottom: 16 }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}>
        <Text style={[typography.eyebrow, { color: '#8FB4DD', letterSpacing: 1 }]}>
          TODAY · {flightNo}
        </Text>
        <Text style={[typography.statusPill, {
          color: pair.fg, backgroundColor: pair.bg,
          paddingHorizontal: 10, paddingVertical: 4, borderRadius: 999, overflow: 'hidden',
        }]}>{status}</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 14, marginTop: 12 }}>
        <Text style={[typography.routeIATA, { color: '#FFF' }]}>{origin}</Text>
        <View style={{ flex: 1, flexDirection: 'row', alignItems: 'center', gap: 6, paddingBottom: 8 }}>
          <View style={{ flex: 1, height: 2, backgroundColor: 'rgba(255,255,255,0.35)' }} />
          <Ionicons name="airplane" size={18} color="#FFF" />
          <View style={{ flex: 1, height: 2, backgroundColor: 'rgba(255,255,255,0.35)' }} />
        </View>
        <Text style={[typography.routeIATA, { color: '#FFF' }]}>{dest}</Text>
      </View>
    </LinearGradient>
  );
}
```

### Buttons & Status Chip

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { statusPairs } from '../theme/colors';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onPress(); }}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.deltaRedPressed : colors.deltaRed,
        borderRadius: 6, paddingVertical: 14, alignItems: 'center',
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
    <Text style={[typography.statusPill, {
      color: p.fg, backgroundColor: p.bg,
      paddingHorizontal: 14, paddingVertical: 7, borderRadius: 999, overflow: 'hidden',
    }]}>{label}</Text>
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
        tabBarActiveTintColor:  colors.boardingDark,
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'OpenSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Today',    tabBarIcon: ({ color }) => <Ionicons name="home"            size={22} color={color} /> }} />
      <Tabs.Screen name="book"     options={{ title: 'Book',     tabBarIcon: ({ color }) => <Ionicons name="airplane"        size={22} color={color} /> }} />
      <Tabs.Screen name="trips"    options={{ title: 'Trips',    tabBarIcon: ({ color }) => <Ionicons name="albums"          size={22} color={color} /> }} />
      <Tabs.Screen name="skymiles" options={{ title: 'SkyMiles', tabBarIcon: ({ color }) => <Ionicons name="person-circle"   size={22} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',     tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, withTiming, withRepeat, Easing } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import * as Brightness from 'expo-brightness';

// Plane glide on header appear
planeProgress.value = withTiming(1, { duration: 1200, easing: Easing.inOut(Easing.ease) });

// Status change — cross-fade + soft haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
statusOpacity.value = withTiming(0, { duration: 120 }, () => { /* swap, fade back */ });

// Active timeline dot pulse
dotScale.value = withRepeat(withTiming(1.15, { duration: 1600 }), -1, true);

// Boarding pass open — brightness boost (restore on dismiss)
const prev = await Brightness.getBrightnessAsync();
await Brightness.setBrightnessAsync(1);
// onDismiss: await Brightness.setBrightnessAsync(prev);

// Check-in success
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons / MaterialCommunityIcons).

| Purpose | Ionicons |
|---------|----------|
| Today | `home` |
| Book | `airplane` |
| Trips | `albums` |
| SkyMiles | `person-circle` |
| More | `ellipsis-horizontal-circle` |
| In-flight plane | `airplane` |
| Departed | `airplane-outline` (rotate) |
| Gate change | `warning` |
| Bag tracker | `briefcase` |
| Add to Wallet | `wallet` |
| Boarding pass / barcode | `qr-code` |
| Seat | `body` |
| Back | `chevron-back` |
| Share | `share-outline` |
| Refresh | `refresh` |
| Medallion tier | `star` |

## 7. Platform Notes

- **Font choice**: Open Sans (Apache 2.0) is the free analog to Delta's licensed Whitney-class corporate face — ship all five weights
- **Status bar**: `<StatusBar style="light" />` over the navy trip header and on dark mode; `"dark"` on light content screens
- **Safe area**: wrap screens in `SafeAreaView`; the trip header extends under the status bar (translucent), content offsets below the Dynamic Island; the boarding-pass barcode must stay above the home-indicator inset
- **Brightness**: use `expo-brightness` to max screen brightness when the full-screen pass opens; restore the user's prior value on dismiss
- **Wallet**: use `expo-apple-passkit` (or a dev-client native module) to add the `.pkpass`; keep the pass live via push so a gate change updates the lock screen
- **Dynamic Type**: keep the barcode, 10pt cell labels, 10pt tab labels, and status-pill text at `allowFontScaling={false}`; let route/title/body scale
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1` etc.; the barcode bar color flips to light so it stays machine-scannable
- **Accessibility**: label the pass, each timeline step, and status pill with full strings; status colors are paired against translucent fills validated for AA contrast
