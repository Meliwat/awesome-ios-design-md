# Zwift (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zwift's visual language into paste-ready Expo / React Native code: a design-token module, glass components, and Reanimated snippets for the rider HUD over a 3D world.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand / action
  orange:        '#FC6719',
  orangePressed: '#D9530E',
  orangeSoft:    '#3A2110',

  // Ride metric semantics (FIXED — do not remap)
  power:   '#FC6719', // power = orange (hero metric)
  cadence: '#E8C547',
  heart:   '#F0413E',
  wkg:     '#2BD4D9',

  // App-shell surfaces (solid, off-ride)
  canvas:   '#161616',
  card:     '#1F1F1F',
  surface2: '#2A2A2A',
  surface3: '#353535',
  divider:  '#303030',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A8AAAD',
  textTertiary:  '#6C6E72',

  // Semantic
  success: '#57C84D',
} as const;

export type ZwiftColor = keyof typeof colors;

// Glass overlay tints (layer over <BlurView>)
export const glass = {
  panel:      'rgba(12,12,12,0.60)',  // HUD tiles / banner
  panelHeavy: 'rgba(18,18,18,0.92)',  // action bar / modals
  stroke:     'rgba(255,255,255,0.10)',
  button:     'rgba(255,255,255,0.10)',
} as const;
```

## 2. Typography

Two faces: **Barlow Semi Condensed** for numbers/titles/buttons, **Inter** for body/lists. Load via `expo-font`; apply `fontVariant: ['tabular-nums']` on every metric.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Barlow-Bold':      require('../assets/fonts/BarlowSemiCondensed-Bold.ttf'),
    'Barlow-ExtraBold': require('../assets/fonts/BarlowSemiCondensed-ExtraBold.ttf'),
    'Inter-Regular':    require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':     require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':   require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':       require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  // Display / numeric — Barlow Semi Condensed
  hud:        { ...tnum, color: '#FFFFFF', fontFamily: 'Barlow-ExtraBold', fontSize: 34, lineHeight: 34, letterSpacing: -0.3 },
  hudCompact: { ...tnum, color: '#FFFFFF', fontFamily: 'Barlow-ExtraBold', fontSize: 28, lineHeight: 28, letterSpacing: -0.2 },
  screenTitle:{ color: '#FFFFFF', fontFamily: 'Barlow-ExtraBold', fontSize: 28, lineHeight: 31, letterSpacing: 0.4 },
  section:    { color: '#FFFFFF', fontFamily: 'Barlow-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: 0.2 },
  cardTitle:  { color: '#FFFFFF', fontFamily: 'Barlow-Bold',      fontSize: 18, lineHeight: 23, letterSpacing: 0.2 },
  unit:       { color: '#A8AAAD', fontFamily: 'Barlow-Bold',      fontSize: 11, lineHeight: 12, letterSpacing: 0.6 },
  button:     { color: '#FFFFFF', fontFamily: 'Barlow-Bold',      fontSize: 16, lineHeight: 16, letterSpacing: 0.3 },
  badge:      { color: '#FFFFFF', fontFamily: 'Barlow-ExtraBold', fontSize: 11, lineHeight: 12, letterSpacing: 0.4 },
  // Body / UI — Inter
  body:       { color: '#FFFFFF', fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 24 },
  listRow:    { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 20 },
  caption:    { color: '#A8AAAD', fontFamily: 'Inter-Medium',   fontSize: 13, lineHeight: 18 },
  tab:        { color: '#A8AAAD', fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Glass wrapper

```tsx
// components/Glass.tsx
import { BlurView } from 'expo-blur';
import { View, ViewStyle } from 'react-native';
import { glass } from '../theme/colors';

export function Glass({
  children, radius = 12, heavy = false, style,
}: {
  children: React.ReactNode; radius?: number; heavy?: boolean; style?: ViewStyle;
}) {
  return (
    <View style={[{ borderRadius: radius, overflow: 'hidden',
                     borderWidth: 1, borderColor: glass.stroke }, style]}>
      <BlurView intensity={heavy ? 40 : 24} tint="dark" style={{ borderRadius: radius }}>
        <View style={{ backgroundColor: heavy ? glass.panelHeavy : glass.panel }}>
          {children}
        </View>
      </BlurView>
    </View>
  );
}
```

### HUD Metric Tile (the core atom)

```tsx
// components/HUDTile.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedProps, withTiming } from 'react-native-reanimated';
import { Glass } from './Glass';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const METRIC = {
  power:   { color: colors.power,   unit: 'WATTS', dp: 0 },
  cadence: { color: colors.cadence, unit: 'RPM',   dp: 0 },
  heart:   { color: colors.heart,   unit: 'BPM',   dp: 0 },
  wkg:     { color: colors.wkg,     unit: 'W/KG',  dp: 1 },
} as const;

const AText = Animated.createAnimatedComponent(Text);

export function HUDTile({ metric, value }: { metric: keyof typeof METRIC; value: number }) {
  const m = METRIC[metric];
  const v = useSharedValue(value);
  useEffect(() => { v.value = withTiming(value, { duration: 200 }); }, [value]); // ticks, never jumps

  const props = useAnimatedProps(() => ({
    text: m.dp ? v.value.toFixed(1) : String(Math.round(v.value)),
  })) as any;

  return (
    <Glass radius={12} style={{ minWidth: 88 }}>
      <View style={{ paddingVertical: 10, paddingHorizontal: 8, alignItems: 'center' }}>
        <AText editable={false} animatedProps={props}
               style={[typography.hud, { color: m.color }]} />
        <Text style={typography.unit}>{m.unit}</Text>
      </View>
    </Glass>
  );
}

export function RiderHUD({ power, cadence, heart }: { power: number; cadence: number; heart: number }) {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'space-around', paddingHorizontal: 14, gap: 12 }}>
      <HUDTile metric="power" value={power} />
      <HUDTile metric="cadence" value={cadence} />
      <HUDTile metric="heart" value={heart} />
    </View>
  );
}
```

### Route Banner

```tsx
// components/RouteBanner.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { Glass } from './Glass';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RouteBanner({
  route, sub, progress,
}: { route: string; sub: string; progress: number }) {
  return (
    <Glass radius={12} style={{ marginHorizontal: 14 }}>
      <View style={{ padding: 14, gap: 8 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
          <View style={{ width: 30, height: 30, borderRadius: 8, backgroundColor: colors.orange,
                          alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="flag" size={16} color="#fff" />
          </View>
          <View style={{ flex: 1 }}>
            <Text style={typography.cardTitle}>{route}</Text>
            <Text style={{ fontFamily: 'Inter-Medium', fontSize: 11, color: colors.textSecondary }}>{sub}</Text>
          </View>
        </View>
        <View style={{ height: 4, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.18)', overflow: 'hidden' }}>
          <View style={{ width: `${progress * 100}%`, height: '100%', backgroundColor: colors.orange }} />
        </View>
      </View>
    </Glass>
  );
}
```

### Power-Up Tile

```tsx
// components/PowerUpTile.tsx
import { useEffect } from 'react';
import { Pressable } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors, glass } from '../theme/colors';

export function PowerUpTile({
  glyph = 'flash', armed, onDeploy,
}: { glyph?: any; armed: boolean; onDeploy: () => void }) {
  const s = useSharedValue(0.8);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: s.value }] }));

  useEffect(() => {
    if (armed) {
      s.value = withSpring(1, { damping: 7, stiffness: 180 });
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    } else {
      s.value = withSpring(0.8);
    }
  }, [armed]);

  return (
    <Pressable onPress={armed ? onDeploy : undefined}>
      <Animated.View style={[style, {
        width: 56, height: 56, borderRadius: 10, overflow: 'hidden',
        borderWidth: 1, borderColor: glass.stroke,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: armed ? colors.orange : 'transparent',
        shadowColor: colors.orange, shadowOpacity: armed ? 0.6 : 0, shadowRadius: 12,
      }]}>
        {!armed && <BlurView intensity={24} tint="dark"
          style={{ position: 'absolute', inset: 0, backgroundColor: glass.button }} />}
        <Ionicons name={glyph} size={24} color="#fff" />
      </Animated.View>
    </Pressable>
  );
}
```

### Achievement Burst

```tsx
// components/AchievementBurst.tsx
import { useEffect } from 'react';
import { Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AchievementBurst({ label }: { label: string }) {
  const s = useSharedValue(0.8);
  const o = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: s.value }], opacity: o.value }));

  useEffect(() => {
    s.value = withSpring(1, { damping: 8, stiffness: 160 });
    o.value = withSpring(1);
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  }, []);

  return (
    <Animated.View style={[style, {
      alignSelf: 'center', paddingVertical: 8, paddingHorizontal: 18, borderRadius: 999,
      backgroundColor: colors.success,
      shadowColor: colors.success, shadowOpacity: 0.5, shadowRadius: 14,
    }]}>
      <Text style={[typography.badge, { textTransform: 'uppercase' }]}>{label}</Text>
    </Animated.View>
  );
}
```

### Primary CTA

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.orangePressed : colors.orange,
        borderRadius: 10, paddingVertical: 14, paddingHorizontal: 30, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={typography.button}>{title.toUpperCase()}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Action / Tab Bar

```tsx
// Off-ride app-shell tab bar — app/(tabs)/_layout.tsx
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
      }}>
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"        size={22} color={color} /> }} />
      <Tabs.Screen name="workouts" options={{ title: 'Workouts', tabBarIcon: ({ color }) => <Ionicons name="bicycle"     size={22} color={color} /> }} />
      <Tabs.Screen name="events"   options={{ title: 'Events',   tabBarIcon: ({ color }) => <Ionicons name="calendar"    size={22} color={color} /> }} />
      <Tabs.Screen name="clubs"    options={{ title: 'Clubs',    tabBarIcon: ({ color }) => <Ionicons name="people"      size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

The **in-ride action bar** is a `<Glass heavy>` row of game controls (Action / Power-Up / Workout / Riders / Menu), not page navigation — render it as an absolutely-positioned overlay above the 3D scene.

## 5. Motion

```tsx
// HUD metric tick — interpolate, never jump
v.value = withTiming(value, { duration: 200 });

// Power-Up collected: spring scale-pop + glow + medium haptic
s.value = withSpring(1, { damping: 7, stiffness: 180 });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Achievement burst: spring scale-in + success haptic + orange confetti (e.g. react-native-confetti-cannon ~800ms)
s.value = withSpring(1, { damping: 8, stiffness: 160 });
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Ride-On burst: FadeIn/FadeOut over 600ms + soft haptic
// Overlay show/hide: Animated entering={FadeIn.duration(200)} + slide 8px
// Route progress: width animates linearly with distance
// App-shell nav: expo-router stack push (300ms)

// Haptics: Medium on power-up, Success on achievement, Light on Ride-On/lap
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The in-game 3D world is rendered by the engine, not RN; these are for the UI overlays + app shell.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Workouts | `bicycle` |
| Events | `calendar` |
| Clubs | `people` |
| Profile | `person-circle` |
| Route flag | `flag` |
| Power-Up (generic) | `flash` |
| Power-Up: light/feather | `leaf` |
| Action (U-turn) | `return-up-back` |
| Workout | `stopwatch` |
| Riders nearby | `people-circle` |
| Menu | `menu` |
| Heart-rate | `heart` |
| Power | `flash` |
| Elevation / climb | `triangle` |
| Ride On (kudos) | `thumbs-up` |
| Map | `map` |
| Camera angle | `videocam` |
| Achievement | `ribbon` |
| Search | `search` |

## 7. Platform Notes

- **Font choice**: Barlow Semi Condensed + Inter are both SIL OFL — free to bundle. Use Barlow for numbers/titles/buttons, Inter for body/lists; never mix within one element
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every metric (watts, rpm, bpm, W/kg, km, time) and interpolate value changes (`withTiming` 200ms) so the HUD ticks smoothly and never jumps
- **Glass**: use `expo-blur`'s `<BlurView tint="dark">` + a translucent tint overlay; `<BlurView>` is iOS-faithful, Android falls back to a semi-opaque view (acceptable — keep the tint heavier on Android)
- **3D world**: the actual ride scene is rendered by Zwift's engine — in an RN port, treat it as a full-bleed `expo-gl` / native view behind absolutely-positioned glass overlays
- **Status bar**: `<StatusBar style="light" />` — always dark; in-ride the world is the backdrop
- **Orientation**: in-ride is landscape-preferred (the trainer layout) — lock the ride screen to landscape via `expo-screen-orientation`; the app shell is portrait
- **Safe area**: the route banner must clear the Dynamic Island and the action bar the home indicator while the world bleeds under both — use `useSafeAreaInsets()` to inset the overlays, not the world
- **Reduce Transparency**: detect via `AccessibilityInfo.isReduceTransparencyEnabled()` and swap glass to a solid `rgba(18,18,18,0.95)` panel
- **Reduce Motion**: disable the power-up pop, achievement spring, and confetti; keep the smooth metric tick and route-progress fill
- **Accessibility**: label HUD tiles "Power, 248 watts" etc.; the Power-Up is a button with its type + armed state; metric color is always paired with a unit label so color is never the sole signal
- **Always dark**: there is no light theme — do not branch on `useColorScheme()`
