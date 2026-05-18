# Citymapper (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Citymapper's visual language into paste-ready Expo / React Native: a design-token module, the mode-color system, the leg strip, the GO button, GO trip mode, and the departures board.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:       '#FFFFFF',
  surface1:     '#F4F5F8',
  surface2:     '#E8EAF0',
  divider:      '#E3E5EC',

  // Surfaces (dark) — deep blue-black, NOT pure black
  darkCanvas:   '#0C0E14',
  darkSurface1: '#15171F',
  darkSurface2: '#1E212B',
  darkDivider:  '#282C38',

  // Text
  textPrimary:    '#10131A',
  textSecondary:  '#5A6473',
  textTertiary:   '#8A93A3',
  darkTextPrim:   '#ECEEF3',
  darkTextSec:    '#98A0AE',

  // Brand
  cmBlue:        '#2B5BFF',
  cmBlueBright:  '#4D7BFF',
  cmBluePressed: '#1E45CC',
  goGreen:       '#00C281',

  // Transit mode colors (theme-invariant — never swap for dark)
  modeWalk:  '#00B894',
  modeBus:   '#E8453C',
  modeTube:  '#2B5BFF',
  modeRail:  '#8E44D8',
  modeBike:  '#00A8C5',
  modeCab:   '#FFB400',
  modeFerry: '#0094C6',

  // Semantic
  disruption: '#FF8A00',
} as const;

export type CMColor = keyof typeof colors;

export type Mode = 'walk' | 'bus' | 'tube' | 'rail' | 'bike' | 'cab' | 'ferry';

export const modeColor: Record<Mode, string> = {
  walk: colors.modeWalk, bus: colors.modeBus, tube: colors.modeTube,
  rail: colors.modeRail, bike: colors.modeBike, cab: colors.modeCab, ferry: colors.modeFerry,
};

export const modeIcon: Record<Mode, string> = {
  walk: 'walk', bus: 'bus', tube: 'subway', rail: 'train',
  bike: 'bicycle', cab: 'car', ferry: 'boat',
};
```

## 2. Typography

Load the custom grotesque or Inter (400–900) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'CM-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'CM-Semibold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'CM-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
    'CM-Heavy':    require('../assets/fonts/Inter-ExtraBold.ttf'),
    'CM-Black':    require('../assets/fonts/Inter-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#10131A' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  display:   { ...primary, fontFamily: 'CM-Black',    fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  etaHero:   { ...primary, ...tnum, fontFamily: 'CM-Heavy', fontSize: 26, lineHeight: 30, letterSpacing: -0.3 },
  section:   { ...primary, fontFamily: 'CM-Heavy',    fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  rowTitle:  { ...primary, fontFamily: 'CM-Bold',     fontSize: 18, lineHeight: 23 },
  body:      { ...primary, fontFamily: 'CM-Regular',  fontSize: 16, lineHeight: 23 },
  place:     { ...primary, fontFamily: 'CM-Semibold', fontSize: 15, lineHeight: 20 },
  meta:      { color: '#5A6473', fontFamily: 'CM-Regular', fontSize: 14, lineHeight: 20 },
  modeBadge: { color: '#FFFFFF', fontFamily: 'CM-Heavy', fontSize: 12, lineHeight: 12, letterSpacing: 0.3 },
  etaSuffix: { color: '#5A6473', fontFamily: 'CM-Semibold', fontSize: 13, lineHeight: 13 },
  goLabel:   { fontFamily: 'CM-Black', fontSize: 18, lineHeight: 18, letterSpacing: 0.4 },
  button:    { fontFamily: 'CM-Heavy', fontSize: 15, lineHeight: 15 },
  tag:       { fontFamily: 'CM-Heavy', fontSize: 10, lineHeight: 10, letterSpacing: 0.4 },
  depMin:    { ...tnum, fontFamily: 'CM-Heavy', fontSize: 16, lineHeight: 16 },
  tab:       { fontFamily: 'CM-Semibold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Mode Chip & Leg Strip

```tsx
// components/LegStrip.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, modeColor, modeIcon, Mode } from '../theme/colors';
import { typography } from '../theme/typography';

export function ModeChip({ mode, label }: { mode: Mode; label: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 5,
      paddingHorizontal: 9, paddingVertical: 5, borderRadius: 8,
      backgroundColor: modeColor[mode],
    }}>
      <Ionicons name={modeIcon[mode] as any} size={13} color="#FFF" />
      <Text style={typography.modeBadge}>{label}</Text>
    </View>
  );
}

export function LegStrip({ legs }: { legs: { mode: Mode; label: string }[] }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', flexWrap: 'wrap', gap: 6 }}>
      {legs.map((l, i) => (
        <View key={i} style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          <ModeChip mode={l.mode} label={l.label} />
          {i < legs.length - 1 && (
            <Text style={{ color: colors.textTertiary, fontSize: 11, fontFamily: 'CM-Bold' }}>›</Text>
          )}
        </View>
      ))}
    </View>
  );
}
```

### Route Option Card

```tsx
// components/RouteCard.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { LegStrip } from './LegStrip';
import type { Mode } from '../theme/colors';

export function RouteCard({ eta, tag, legs, best }: {
  eta: number;
  tag?: { text: string; color: string };
  legs: { mode: Mode; label: string }[];
  best?: boolean;
}) {
  return (
    <View style={{
      backgroundColor: colors.surface1, borderRadius: 16,
      borderWidth: 1, borderColor: best ? colors.goGreen : colors.divider,
      paddingHorizontal: 16, paddingVertical: 14, marginBottom: 12, gap: 12,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center' }}>
        <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4 }}>
          <Text style={typography.section}>{eta}</Text>
          <Text style={typography.etaSuffix}>min</Text>
        </View>
        <View style={{ flex: 1 }} />
        {tag && (
          <View style={{ backgroundColor: `${tag.color}2E`, borderRadius: 999, paddingHorizontal: 9, paddingVertical: 4 }}>
            <Text style={[typography.tag, { color: tag.color, textTransform: 'uppercase' }]}>{tag.text}</Text>
          </View>
        )}
      </View>
      <LegStrip legs={legs} />
    </View>
  );
}
```

### GO Button (shape/color-locked)

```tsx
// components/GoButton.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GoButton({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        marginHorizontal: 16, height: 54, borderRadius: 28,
        backgroundColor: colors.goGreen,           // LOCKED — never theme/context dependent
        alignItems: 'center', justifyContent: 'center', flexDirection: 'row', gap: 8,
        transform: [{ scale: pressed ? 0.98 : 1 }], opacity: pressed ? 0.95 : 1,
        shadowColor: colors.goGreen, shadowOpacity: 0.7, shadowRadius: 22,
        shadowOffset: { width: 0, height: 10 }, elevation: 10,
      })}
    >
      <Ionicons name="play" size={20} color="#003322" />
      <Text style={[typography.goLabel, { color: '#003322' }]}>GO</Text>
    </Pressable>
  );
}
```

### Origin → Destination Card

```tsx
// components/ODCard.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function ODRow({ dot, text }: { dot: string; text: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, height: 34 }}>
      <View style={{ width: 10, height: 10, borderRadius: 5, backgroundColor: dot }} />
      <Text style={typography.place}>{text}</Text>
    </View>
  );
}

export function ODCard({ from, to }: { from: string; to: string }) {
  return (
    <View style={{
      marginHorizontal: 16, backgroundColor: colors.surface1, borderRadius: 14,
      borderWidth: 1, borderColor: colors.divider, paddingHorizontal: 14, paddingVertical: 12,
    }}>
      <ODRow dot={colors.textSecondary} text={from} />
      <View style={{ width: 2, height: 14, backgroundColor: colors.divider, marginLeft: 4 }} />
      <View style={{ height: 1, backgroundColor: colors.divider, marginVertical: 2 }} />
      <ODRow dot={colors.cmBlue} text={to} />
    </View>
  );
}
```

### Departures Board

```tsx
// components/DeparturesBoard.tsx
import { View, Text } from 'react-native';
import { colors, modeColor, Mode } from '../theme/colors';
import { typography } from '../theme/typography';

type Dep = { badge: string; mode: Mode; destination: string; minutes: number };

export function DeparturesBoard({ rows }: { rows: Dep[] }) {
  return (
    <View style={{ backgroundColor: colors.surface2, borderRadius: 14, overflow: 'hidden' }}>
      {rows.map((d, i) => (
        <View
          key={i}
          style={{
            flexDirection: 'row', alignItems: 'center', gap: 12,
            paddingHorizontal: 16, paddingVertical: 13,
            borderBottomWidth: i < rows.length - 1 ? 1 : 0, borderBottomColor: colors.divider,
          }}
        >
          <View style={{ width: 34, height: 24, borderRadius: 6, backgroundColor: modeColor[d.mode], alignItems: 'center', justifyContent: 'center' }}>
            <Text style={typography.modeBadge}>{d.badge}</Text>
          </View>
          <Text style={[typography.place, { flex: 1 }]}>{d.destination}</Text>
          <Text style={[typography.depMin, { color: d.minutes <= 3 ? colors.disruption : colors.goGreen }]}>
            {d.minutes} min
          </Text>
        </View>
      ))}
    </View>
  );
}
```

### GO Trip-Mode Header & Disruption Banner

```tsx
// components/GoTripHeader.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GoTripHeader({ instruction, sub, legColor }:
  { instruction: string; sub: string; legColor: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-start', gap: 14, padding: 20 }}>
      <View style={{ alignItems: 'center' }}>
        <View style={{ width: 12, height: 12, borderRadius: 6, backgroundColor: legColor }} />
        <View style={{ width: 3, height: 60, backgroundColor: legColor }} />
        <View style={{ width: 12, height: 12, borderRadius: 6, borderWidth: 2, borderColor: colors.textTertiary }} />
      </View>
      <View style={{ flex: 1, gap: 6 }}>
        <Text style={typography.etaHero}>{instruction}</Text>
        <Text style={typography.body}>{sub}</Text>
      </View>
    </View>
  );
}

export function DisruptionBanner({ text, severe }: { text: string; severe?: boolean }) {
  const c = severe ? colors.modeBus : colors.disruption;
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 8,
      backgroundColor: `${c}2E`, borderWidth: 1, borderColor: c,
      borderRadius: 10, padding: 12, marginHorizontal: 16,
    }}>
      <Text style={{ color: c, fontSize: 14 }}>⚠</Text>
      <Text style={{ color: colors.textPrimary, fontFamily: 'CM-Bold', fontSize: 14, flex: 1 }}>{text}</Text>
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
        tabBarActiveTintColor:  colors.cmBlue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'CM-Semibold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Get me', tabBarIcon: ({ color }) => <Ionicons name="search" size={22} color={color} /> }} />
      <Tabs.Screen name="nearby" options={{ title: 'Nearby', tabBarIcon: ({ color }) => <Ionicons name="location-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="saved"  options={{ title: 'Saved',  tabBarIcon: ({ color }) => <Ionicons name="heart-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="you"    options={{ title: 'You',    tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, FadeOut, SlideInUp, useSharedValue, withTiming, withSpring } from 'react-native-reanimated';

// Route list ⇄ map drag — use @gorhom/bottom-sheet with snap points [120, '50%', '90%'] and a spring config

// GO start — cross-dissolve + progress line trim 0→1 (500ms)
// progress.value = withTiming(1, { duration: 500 });

// GO-mode step — fill completed leg (250ms) + slide instruction
// legFill.value = withTiming(1, { duration: 250 });
// instruction <Animated.Text entering={SlideInUp.duration(200)} />

// Departure tick — number cross-fade (re-mount the minutes <Text> with FadeIn/FadeOut 150ms)

// Route cards stagger-in
// each <Animated.View entering={FadeIn.duration(250).delay(i * 60)}>

// Disruption banner — slide down + border pulse
// <Animated.View entering={SlideInUp.duration(250)}> then animate borderColor opacity once

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // route select / tab
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // GO start + each step
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning); // disruption on active route
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Real operator line bullets should be bundled as SVG via `react-native-svg`; mode icons use the set below.

| Purpose | Ionicons |
|---------|----------|
| Get me (tab) | `search` |
| Nearby (tab) | `location-outline` |
| Saved (tab) | `heart-outline` / `heart` |
| You (tab) | `person-circle-outline` |
| Walk leg | `walk` |
| Bus leg | `bus` |
| Tube/Metro leg | `subway` |
| Rail leg | `train` |
| Bike leg | `bicycle` |
| Cab leg | `car` |
| Ferry leg | `boat` |
| GO | `play` |
| Leg arrow | `chevron-forward` |
| Disruption | `warning` |
| Live dot | `ellipse` |
| Back | `chevron-back` |
| Rain-safe tag | `rainy` |
| Step-free tag | `accessibility` |

## 7. Platform Notes

- **Font choice**: Citymapper's grotesque is proprietary — for a clone, bundle Inter (400–900) which best matches the chunky feel; a heavy System font is an acceptable fallback
- **Tabular numbers**: set `fontVariant: ['tabular-nums']` on every ETA / departure-min / GO-mode countdown `<Text>` so countdowns don't jitter
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the route bottom-sheet peek and the GO button must sit above the home indicator
- **Bottom sheet**: use `@gorhom/bottom-sheet` for the route-list-over-map metaphor with snap points and a spring
- **Dynamic Type**: keep `allowFontScaling={false}` on mode badges, route tags, the GO label, departure-min, and tab labels (chips are layout-sensitive); allow scaling on display/ETA/body/place; let the GO-mode instruction scale generously
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkDivider`; **never** swap `modeWalk`/`modeBus`/`modeTube`/etc. or `goGreen` — these are theme-invariant brand constants (a bus is always red, GO is always `#00C281`)
- **Real line colors**: when an operator's official line color is known (e.g., London Victoria navy), override the category mode color with it on the chip/badge
- **Color-blind safety**: every mode chip includes a glyph + label; announce the mode name and the "leaving soon" state in `accessibilityLabel`, never relying on color alone
- **Live data**: stream departures/route updates into state and re-render with the 150ms minute cross-fade; debounce GPS-driven GO-mode step transitions to avoid flicker
