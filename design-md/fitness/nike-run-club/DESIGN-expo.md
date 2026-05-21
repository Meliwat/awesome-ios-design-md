# Nike Run Club (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates NRC's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated patterns for the Volt progress ring and achievement reveal.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Nike Brand
  volt:          '#CCFF00',
  voltPressed:   '#9FCC00',
  voltDim:       '#8AA800',     // light-mode accessible
  red:           '#FA5400',     // Nike Red / HR Zone 4
  brakeRed:      '#E50916',     // HR Zone 5 / error

  // Canvas & surfaces (always dark)
  canvas:        '#000000',
  charcoal:      '#0A0A0A',
  surface1:      '#1A1A1A',
  surface2:      '#2B2B2B',
  divider:       '#333333',

  // Text
  white:         '#FFFFFF',
  gray1:         '#B3B3B3',
  gray2:         '#8E8E8E',
  gray3:         '#5C5C5C',

  // Heart Rate Zones
  hr1Blue:       '#3D87F4',
  hr2Green:      '#48C77E',
  hr3Yellow:     '#FFD600',
  hr4Orange:     '#FA5400',
  hr5Red:        '#E50916',

  // Semantic
  warning:       '#FFC400',
} as const;

export type NRCColor = keyof typeof colors;
```

## 2. Typography

Trade Gothic Next LT W04 must be bundled via `expo-font`. Fall back to `Oswald` (free, geometric-condensed) or `System` with the `condensed` style on iOS.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'TradeGothic-Heavy': require('../assets/fonts/TradeGothicNextLT-Heavy.otf'),
    'TradeGothic-Bold':  require('../assets/fonts/TradeGothicNextLT-Bold.otf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tradeGothic = 'TradeGothic-Heavy';
const tradeGothicBold = 'TradeGothic-Bold';
const tabular = { fontVariant: ['tabular-nums' as const] };

export const typography = {
  // Hero stats
  runHero:      { fontFamily: tradeGothic, fontSize: 88, letterSpacing: 0.5, lineHeight: 84, ...tabular },
  preHero:      { fontFamily: tradeGothic, fontSize: 64, letterSpacing: 0.4, lineHeight: 61, ...tabular },
  headline:     { fontFamily: tradeGothic, fontSize: 48, letterSpacing: 0.3, lineHeight: 46 },
  screamHdr:    { fontFamily: tradeGothic, fontSize: 32, letterSpacing: 0.3, lineHeight: 32 },
  subHdr:       { fontFamily: tradeGothicBold, fontSize: 22, letterSpacing: 0.3, lineHeight: 24 },
  statLabel:    { fontFamily: tradeGothic, fontSize: 13, letterSpacing: 1.0 },
  button:       { fontFamily: tradeGothic, fontSize: 17, letterSpacing: 0.5 },
  achievement:  { fontFamily: tradeGothic, fontSize: 56, letterSpacing: 0, ...tabular },

  // SF Pro body / metadata
  cardTitle:    { fontFamily: 'System', fontSize: 17, fontWeight: '700' },
  body:         { fontFamily: 'System', fontSize: 15, fontWeight: '400', lineHeight: 22 },
  bodyBold:     { fontFamily: 'System', fontSize: 15, fontWeight: '600' },
  meta:         { fontFamily: 'System', fontSize: 13, fontWeight: '400' },
  caption:      { fontFamily: 'System', fontSize: 11, fontWeight: '500', letterSpacing: 0.1 },
  tab:          { fontFamily: 'System', fontSize: 10, fontWeight: '600', letterSpacing: 0.3 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Volt Progress Ring (the Nike Run heart)

```tsx
// components/VoltProgressRing.tsx
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);

export function VoltProgressRing({ progress, diameter = 280, strokeWidth = 6 }: { progress: number; diameter?: number; strokeWidth?: number; }) {
  const radius = (diameter - strokeWidth) / 2;
  const circumference = 2 * Math.PI * radius;
  const dashOffset = useSharedValue(circumference);

  useEffect(() => {
    dashOffset.value = withTiming(circumference * (1 - progress), { duration: 1000 });
  }, [progress]);

  const animProps = useAnimatedProps(() => ({ strokeDashoffset: dashOffset.value }));

  return (
    <Svg width={diameter} height={diameter} viewBox={`0 0 ${diameter} ${diameter}`}>
      {/* Track */}
      <Circle cx={diameter / 2} cy={diameter / 2} r={radius} stroke="rgba(255,255,255,0.1)" strokeWidth={strokeWidth} fill="none" />
      {/* Volt fill */}
      <AnimatedCircle
        cx={diameter / 2} cy={diameter / 2} r={radius}
        stroke={colors.volt} strokeWidth={strokeWidth} strokeLinecap="round" fill="none"
        strokeDasharray={`${circumference},${circumference}`}
        animatedProps={animProps}
        rotation="-90" originX={diameter / 2} originY={diameter / 2}
      />
    </Svg>
  );
}
```

### Run Tracking Screen (the hero component)

```tsx
// app/run-tracking.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import * as Haptics from 'expo-haptics';
import { useState } from 'react';
import { VoltProgressRing } from '../components/VoltProgressRing';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export default function RunTracking() {
  const [paused, setPaused] = useState(false);
  const progress = 0.42;

  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas, paddingTop: 64 }}>
      {/* 3-up stats */}
      <View style={styles.statsRow}>
        <Stat label="Distance" value="1.4" />
        <Stat label="Min/Mi"   value="8:42" />
        <Stat label="BPM"      value="152" />
      </View>

      {/* Ring with elapsed inside */}
      <View style={{ alignItems: 'center', marginTop: 32 }}>
        <VoltProgressRing progress={progress} />
        <View style={styles.ringInner} pointerEvents="none">
          <Text style={[typography.runHero, { color: '#FFF' }]}>12:34</Text>
          <Text style={[typography.statLabel, { color: 'rgba(255,255,255,0.7)', textTransform: 'uppercase', marginTop: 4 }]}>Elapsed</Text>
        </View>
      </View>

      <View style={{ flex: 1 }} />

      {/* Pause + Lock */}
      <View style={styles.controlsRow}>
        <View style={{ flex: 1 }} />
        <Pressable
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy); setPaused((p) => !p); }}
          style={[styles.pauseBtn, { backgroundColor: paused ? colors.volt : '#FFFFFF' }]}
        >
          <Ionicons name={paused ? 'play' : 'pause'} size={28} color="#000" />
        </Pressable>
        <View style={{ flex: 1 }} />
        <Pressable style={styles.lockBtn} onPress={() => Haptics.selectionAsync()}>
          <Ionicons name="lock-closed" size={18} color="#FFF" />
        </Pressable>
      </View>
    </View>
  );
}

function Stat({ label, value }: { label: string; value: string }) {
  return (
    <View style={{ flex: 1, alignItems: 'center' }}>
      <Text style={[typography.statLabel, { color: 'rgba(255,255,255,0.7)', textTransform: 'uppercase' }]}>{label}</Text>
      <Text style={[typography.preHero, { color: '#FFF', marginTop: 4 }]}>{value}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  statsRow: { flexDirection: 'row', paddingHorizontal: 24, gap: 24 },
  ringInner: { position: 'absolute', alignItems: 'center', top: 88 },
  controlsRow: { flexDirection: 'row', alignItems: 'center', paddingBottom: 48, paddingHorizontal: 16 },
  pauseBtn: { width: 72, height: 72, borderRadius: 36, alignItems: 'center', justifyContent: 'center' },
  lockBtn: { width: 48, height: 48, borderRadius: 24, backgroundColor: colors.surface2, alignItems: 'center', justifyContent: 'center', position: 'absolute', right: 16 },
});
```

### Nike Action Button ("START RUN" / "LET'S DO THIS")

```tsx
// components/NikeActionButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Style = 'white' | 'volt' | 'outline';

export function NikeActionButton({ title, style = 'white', onPress }: { title: string; style?: Style; onPress: () => void }) {
  const bg = style === 'white' ? '#FFFFFF' : style === 'volt' ? colors.volt : 'transparent';
  const fg = style === 'outline' ? '#FFFFFF' : '#000000';
  const border = style === 'outline' ? 2 : 0;

  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy); onPress(); }}
      style={({ pressed }) => ({
        backgroundColor: bg, borderRadius: 32, height: 56,
        alignItems: 'center', justifyContent: 'center',
        marginHorizontal: 16,
        borderWidth: border, borderColor: '#FFFFFF',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: fg, textTransform: 'uppercase' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Run Plan Card (Home Feed)

```tsx
// components/RunPlanCard.tsx
import { View, Text, ImageBackground, Pressable, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  photo: any;
  eyebrow: string;
  title: string;
  description: string;
  onPress: () => void;
};

export function RunPlanCard({ photo, eyebrow, title, description, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={styles.frame}>
      <ImageBackground source={photo} style={styles.bg}>
        <LinearGradient
          colors={['rgba(0,0,0,0)', 'rgba(0,0,0,0.6)', 'rgba(0,0,0,0.85)']}
          locations={[0, 0.55, 1]}
          style={StyleSheet.absoluteFill}
        />
        <View style={styles.content}>
          <Text style={[typography.caption, { color: 'rgba(255,255,255,0.8)', textTransform: 'uppercase', letterSpacing: 0.5 }]}>
            {eyebrow}
          </Text>
          <Text style={[typography.screamHdr, { color: '#FFFFFF', textTransform: 'uppercase', marginTop: 6 }]}>{title}</Text>
          <Text style={[typography.meta, { color: 'rgba(255,255,255,0.85)', marginTop: 4 }]}>{description}</Text>
        </View>
      </ImageBackground>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  frame:   { marginHorizontal: 16, marginVertical: 12, borderRadius: 16, overflow: 'hidden', height: 320 },
  bg:      { flex: 1, justifyContent: 'flex-end' },
  content: { padding: 16 },
});
```

### Achievement Medal Reveal

```tsx
// components/AchievementMedal.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Svg, { Polygon, Defs, LinearGradient as SvgGradient, Stop } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withDelay } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AchievementMedal({ milestone, label, subtitle }: { milestone: string; label: string; subtitle: string }) {
  const scale = useSharedValue(0.5);
  const opacity = useSharedValue(0);

  useEffect(() => {
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    scale.value = withSpring(1, { damping: 8, mass: 0.8 });
    opacity.value = withDelay(50, withSpring(1));
  }, []);

  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }], opacity: opacity.value }));

  return (
    <View style={{ alignItems: 'center', padding: 24 }}>
      <Animated.View style={aStyle}>
        <Svg width={200} height={200} viewBox="0 0 100 100">
          <Defs>
            <SvgGradient id="med" x1="0" y1="0" x2="1" y2="1">
              <Stop offset="0" stopColor={colors.volt} />
              <Stop offset="1" stopColor={colors.red} />
            </SvgGradient>
          </Defs>
          <Polygon points="50,0 100,25 100,75 50,100 0,75 0,25" fill="url(#med)" />
        </Svg>
        <Text style={[typography.achievement, { color: '#000', position: 'absolute', top: 70, alignSelf: 'center' }]}>{milestone}</Text>
      </Animated.View>

      <Text style={[typography.subHdr, { color: '#FFF', marginTop: 16, textTransform: 'uppercase' }]}>{label}</Text>
      <Text style={[typography.body, { color: colors.gray1, marginTop: 4 }]}>{subtitle}</Text>
    </View>
  );
}
```

### Heart Rate Zone Bar

```tsx
// components/HRZoneBar.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const ZONES = [
  { color: colors.hr1Blue,   label: 'Z1 Easy' },
  { color: colors.hr2Green,  label: 'Z2 Steady' },
  { color: colors.hr3Yellow, label: 'Z3 Mod' },
  { color: colors.hr4Orange, label: 'Z4 Hard' },
  { color: colors.hr5Red,    label: 'Z5 Max' },
];

export function HRZoneBar({ times }: { times: [number, number, number, number, number] }) {
  const total = times.reduce((a, b) => a + b, 0);
  return (
    <View style={{ gap: 12 }}>
      <View style={{ flexDirection: 'row', height: 8, borderRadius: 4, overflow: 'hidden' }}>
        {times.map((t, i) => (
          <View key={i} style={{ flex: t / total, backgroundColor: ZONES[i].color }} />
        ))}
      </View>
      <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 16 }}>
        {ZONES.map((z) => (
          <View key={z.label} style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <View style={{ width: 6, height: 6, borderRadius: 3, backgroundColor: z.color }} />
            <Text style={[typography.caption, { color: '#FFF', textTransform: 'uppercase' }]}>{z.label}</Text>
          </View>
        ))}
      </View>
    </View>
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { View } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={({ route }) => ({
        tabBarActiveTintColor: '#FFFFFF',
        tabBarInactiveTintColor: colors.gray3,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider, height: 56 + 24 },
        tabBarLabelStyle: { fontFamily: 'System', fontSize: 10, fontWeight: '600' },
      })}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color, focused }) => <TabIcon name={focused ? 'home' : 'home-outline'} color={color} focused={focused} /> }} />
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarIcon: ({ color, focused }) => <TabIcon name={focused ? 'stats-chart' : 'stats-chart-outline'} color={color} focused={focused} /> }} />
      <Tabs.Screen name="coach"    options={{ title: 'Coach',    tabBarIcon: ({ color, focused }) => <TabIcon name={focused ? 'person' : 'person-outline'} color={color} focused={focused} /> }} />
      <Tabs.Screen name="goal"     options={{ title: 'Goal',     tabBarIcon: ({ color, focused }) => <TabIcon name="locate" color={color} focused={focused} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color, focused }) => <TabIcon name={focused ? 'person-circle' : 'person-circle-outline'} color={color} focused={focused} /> }} />
    </Tabs>
  );
}

function TabIcon({ name, color, focused }: any) {
  return (
    <View style={{ alignItems: 'center' }}>
      <Ionicons name={name} size={24} color={color} />
      {focused && <View style={{ width: 4, height: 4, borderRadius: 2, backgroundColor: colors.volt, marginTop: 4 }} />}
    </View>
  );
}
```

## 5. Motion & Haptics

```tsx
// START RUN — heaviest haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);

// PAUSE / RESUME
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);

// Tab switch
Haptics.selectionAsync();

// Achievement medal reveal
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
scale.value = withSpring(1, { damping: 8 });

// Progress ring linear advance
dashOffset.value = withTiming(circumference * (1 - progress), { duration: 1000 });

// PR celebration confetti burst
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
```

## 6. Icon Library

| Purpose | Ionicons |
|---------|----------|
| Pause | `pause` |
| Play / Resume | `play` |
| Stop | `stop` |
| Lock | `lock-closed` |
| GPS | `locate` |
| Coach | `person` / `person-outline` |
| Home tab | `home` / `home-outline` |
| Activity tab | `stats-chart` / `stats-chart-outline` |
| Goal tab | `locate` |
| Profile tab | `person-circle` / `person-circle-outline` |
| Heart rate | `heart` |
| Share | `share-outline` |
| Settings | `settings` |
| Music | `musical-notes` |
| Achievement medal | (custom SVG hexagon) |

## 7. Platform Notes

- **Bundled font**: Trade Gothic Next LT W04 must be bundled via `assets/fonts/*.otf` and registered in `expo-font`. If you lack a license, fall back to `Oswald` from Google Fonts or `System` with `condensed` style on iOS (`fontFamily: 'System', fontWeight: '900'` + iOS-specific width modifier).
- **Status bar**: `<StatusBar style="light" />` everywhere — NRC is dark-only.
- **Safe area**: wrap in `SafeAreaView` from `react-native-safe-area-context`. Run tracking screen needs to clear Dynamic Island — use `useSafeAreaInsets().top + 16` for the stats row.
- **SVG progress ring**: use `react-native-svg` for the ring. Reanimated bridge via `Animated.createAnimatedComponent(Circle)` lets you animate `strokeDashoffset` natively at 60fps.
- **Tabular numerals**: `fontVariant: ['tabular-nums']` works on iOS but not Android — for splits/pace columns on Android, use a custom monospace font or accept proportional digits.
- **Dark-only**: NRC does not toggle to light mode — ignore `useColorScheme()` and lock to the dark token set. (Apple Accessibility users can invert colors at the system level.)
- **Achievement medal hexagon**: render as SVG `<Polygon>` with 6 points; gradient via `<Defs><LinearGradient/>` referenced by `fill="url(#id)"`.
- **Confetti**: keep it simple — 8 to 16 `<View>` dots positioned absolutely and animated via Reanimated `withTiming` for opacity + translateY. Don't load a confetti library for a single screen.
- **HR zone bar**: 5-segment flex row with `borderRadius: 4` and `overflow: 'hidden'` on the outer; segment widths via `flex: time / total`.
