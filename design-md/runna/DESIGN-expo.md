# Runna (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Runna's visual language into paste-ready Expo / React Native code: a token module, themed components, the signature week strip + session card + structure breakdown, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  indigo:        '#4F46E5',
  indigoBright:  '#6366F1',
  indigoPressed: '#4338CA',
  lime:          '#C2F94E',
  limePressed:   '#A3D93B',

  // Canvas & surfaces (dark, default)
  canvas:   '#0E0E16',
  surface1: '#181826',
  surface2: '#222234',
  divider:  '#2C2C42',

  // Canvas & surfaces (light, optional)
  lightCanvas:  '#F6F6FB',
  lightSurface: '#FFFFFF',
  lightDivider: '#E5E5F0',

  // Text
  textPrimary:   '#F3F3FB',
  textSecondary: '#9C9CB8',
  textTertiary:  '#65657E',
  textOnLime:    '#1A1A1A',

  // Run-type system (FIXED both themes)
  rtEasy:     '#34D399',
  rtTempo:    '#FBBF24',
  rtInterval: '#FB7185',
  rtLong:     '#4F46E5',

  // Semantic
  success: '#34D399',
  error:   '#FB7185',
  warning: '#FBBF24',
  pr:      '#C2F94E',
} as const;

export type RunType = 'easy' | 'tempo' | 'interval' | 'long';
export const runTypeMap: Record<RunType, { color: string; onColor: string }> = {
  easy:     { color: colors.rtEasy,     onColor: colors.textOnLime },
  tempo:    { color: colors.rtTempo,    onColor: colors.textOnLime },
  interval: { color: colors.rtInterval, onColor: colors.textOnLime },
  long:     { color: colors.rtLong,     onColor: '#FFFFFF' },
};

export const sessionGradient = ['#4F46E5', '#3A33B8'] as const;
```

## 2. Typography

Load **Sora** via `expo-font`. Heavy stats, regular prose.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Sora-Regular':  require('../assets/fonts/Sora-Regular.ttf'),
    'Sora-Medium':   require('../assets/fonts/Sora-Medium.ttf'),
    'Sora-SemiBold': require('../assets/fonts/Sora-SemiBold.ttf'),
    'Sora-Bold':     require('../assets/fonts/Sora-Bold.ttf'),
    'Sora-ExtraBold':require('../assets/fonts/Sora-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
const p  = { color: '#F3F3FB' } satisfies TextStyle;
const p2 = { color: '#9C9CB8' } satisfies TextStyle;

export const typography = {
  screenTitle:  { ...p,  fontFamily: 'Sora-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  planTitle:    { ...p,  fontFamily: 'Sora-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  sessionTitle: { color: '#FFFFFF', fontFamily: 'Sora-ExtraBold', fontSize: 21, lineHeight: 25, letterSpacing: -0.3 },
  section:      { ...p,  fontFamily: 'Sora-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:    { ...p,  fontFamily: 'Sora-Bold',      fontSize: 18, lineHeight: 23 },
  body:         { ...p,  fontFamily: 'Sora-Regular',   fontSize: 16, lineHeight: 24 },
  stepItem:     { ...p,  fontFamily: 'Sora-SemiBold',  fontSize: 15, lineHeight: 21 },
  meta:         { ...p2, fontFamily: 'Sora-Regular',   fontSize: 14, lineHeight: 20 },
  statValue:    { color: '#FFFFFF', fontFamily: 'Sora-ExtraBold', fontSize: 18, lineHeight: 20 },
  eyebrow:      { color: '#C2F94E', fontFamily: 'Sora-Bold', fontSize: 11, lineHeight: 11, letterSpacing: 1.2 },
  button:       { fontFamily: 'Sora-ExtraBold', fontSize: 16, lineHeight: 16, letterSpacing: 0.1 },
  pace:         { ...p,  fontFamily: 'Sora-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.2 },
  dayNum:       { ...p,  fontFamily: 'Sora-ExtraBold', fontSize: 14, lineHeight: 14 },
  tab:          { fontFamily: 'Sora-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Training-Plan Week Strip

```tsx
// components/WeekStrip.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withDelay, withTiming, Easing } from 'react-native-reanimated';
import { colors, runTypeMap, type RunType } from '../theme/colors';
import { typography } from '../theme/typography';

type DayState =
  | { kind: 'done' } | { kind: 'today' } | { kind: 'rest' } | { kind: 'upcoming'; type: RunType };
export type Day = { weekday: string; date: number; state: DayState };

function Cell({ day, index }: { day: Day; index: number }) {
  const o = useSharedValue(0);
  useEffect(() => { o.value = withDelay(index * 40, withTiming(1, { duration: 220, easing: Easing.out(Easing.cubic) })); }, []);
  const anim = useAnimatedStyle(() => ({ opacity: o.value }));

  const isToday = day.state.kind === 'today';
  const dateColor =
    day.state.kind === 'done' ? colors.rtEasy
    : isToday ? '#FFF'
    : day.state.kind === 'rest' ? colors.textTertiary
    : colors.textPrimary;
  const dotColor =
    day.state.kind === 'done' ? colors.rtEasy
    : isToday ? colors.lime
    : day.state.kind === 'rest' ? colors.divider
    : runTypeMap[(day.state as any).type as RunType].color;
  const bg = day.state.kind === 'done' ? 'rgba(52,211,153,0.12)' : isToday ? colors.indigo : colors.surface1;
  const border = day.state.kind === 'done' ? 'rgba(52,211,153,0.35)' : isToday ? colors.indigoBright : colors.divider;

  return (
    <Animated.View style={[anim, {
      flex: 1, aspectRatio: 0.62, borderRadius: 14, backgroundColor: bg,
      borderWidth: 1, borderColor: border, alignItems: 'center', paddingVertical: 9,
    }]}>
      <Text style={{ fontFamily: 'Sora-Bold', fontSize: 10, color: isToday ? '#FFF' : colors.textTertiary }}>
        {day.weekday}
      </Text>
      <View style={{ flex: 1 }} />
      <Text style={[typography.dayNum, { color: dateColor }]}>{day.date}</Text>
      <View style={{ width: 7, height: 7, borderRadius: 3.5, marginTop: 6, backgroundColor: dotColor }} />
    </Animated.View>
  );
}

export function WeekStrip({ days }: { days: Day[] }) {
  return (
    <View style={{ flexDirection: 'row', gap: 7 }}>
      {days.map((d, i) => <Cell key={`${d.weekday}-${d.date}`} day={d} index={i} />)}
    </View>
  );
}
```

### Guided Run-Session Card

```tsx
// components/SessionCard.tsx
import { useEffect } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, Easing } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, sessionGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function SessionCard({
  tag, title, distance, duration, pace, onStart,
}: { tag: string; title: string; distance: string; duration: string; pace: string; onStart: () => void }) {
  const s = useSharedValue(0.97);
  const o = useSharedValue(0);
  useEffect(() => {
    s.value = withTiming(1, { duration: 300, easing: Easing.out(Easing.cubic) });
    o.value = withTiming(1, { duration: 300 });
  }, []);
  const anim = useAnimatedStyle(() => ({ transform: [{ scale: s.value }], opacity: o.value }));

  const Stat = ({ v, l }: { v: string; l: string }) => (
    <View>
      <Text style={typography.statValue}>{v}</Text>
      <Text style={{ fontFamily: 'Sora-SemiBold', fontSize: 10, color: 'rgba(255,255,255,0.7)', marginTop: 2, textTransform: 'uppercase', letterSpacing: 0.5 }}>{l}</Text>
    </View>
  );

  return (
    <Animated.View style={[anim, {
      borderRadius: 22, shadowColor: colors.indigo, shadowOpacity: 0.30, shadowRadius: 28, shadowOffset: { width: 0, height: 10 }, elevation: 10,
    }]}>
      <LinearGradient colors={sessionGradient as unknown as string[]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ borderRadius: 22, padding: 18, overflow: 'hidden' }}>
        <View style={{ position: 'absolute', right: -34, bottom: -34, width: 130, height: 130, borderRadius: 65, backgroundColor: 'rgba(194,249,78,0.14)' }} />
        <View style={{ alignSelf: 'flex-start', backgroundColor: colors.lime, paddingHorizontal: 10, paddingVertical: 4, borderRadius: 999 }}>
          <Text style={{ fontFamily: 'Sora-ExtraBold', fontSize: 10, color: colors.textOnLime, textTransform: 'uppercase', letterSpacing: 0.5 }}>{tag}</Text>
        </View>
        <Text style={[typography.sessionTitle, { marginTop: 12 }]}>{title}</Text>
        <View style={{ flexDirection: 'row', gap: 18, marginTop: 12 }}>
          <Stat v={distance} l="Distance" /><Stat v={duration} l="Duration" /><Stat v={pace} l="Avg pace" />
        </View>
        <Pressable onPress={onStart} style={({ pressed }) => ({
          marginTop: 16, flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
          backgroundColor: pressed ? colors.limePressed : colors.lime, paddingVertical: 13, borderRadius: 14,
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}>
          <Ionicons name="play" size={15} color={colors.textOnLime} />
          <Text style={[typography.button, { color: colors.textOnLime }]}>Start guided run</Text>
        </Pressable>
      </LinearGradient>
    </Animated.View>
  );
}
```

### Workout-Structure Breakdown

```tsx
// components/StructureBreakdown.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type Step = { code: string; title: string; detail: string; pace: string; color: string; flex: number };

export function StructureBreakdown({ steps }: { steps: Step[] }) {
  const grow = useSharedValue(0);
  useEffect(() => { grow.value = withTiming(1, { duration: 500, easing: Easing.out(Easing.cubic) }); }, []);

  return (
    <View>
      <Text style={{ fontFamily: 'Sora-Bold', fontSize: 13, color: colors.textPrimary, marginBottom: 10 }}>
        Workout structure
      </Text>
      <View style={{ flexDirection: 'row', gap: 3, height: 30, borderRadius: 8, overflow: 'hidden' }}>
        {steps.map((s, i) => {
          const style = useAnimatedStyle(() => ({ flex: s.flex * grow.value || 0.0001 }));
          return <Animated.View key={i} style={[style, { backgroundColor: s.color }]} />;
        })}
      </View>
      {steps.map((s, i) => (
        <View key={i} style={{
          flexDirection: 'row', alignItems: 'center', gap: 12,
          paddingVertical: 10, borderBottomWidth: i < steps.length - 1 ? 1 : 0, borderBottomColor: colors.divider,
        }}>
          <View style={{ width: 32, height: 32, borderRadius: 9, backgroundColor: s.color, alignItems: 'center', justifyContent: 'center' }}>
            <Text style={{ fontFamily: 'Sora-ExtraBold', fontSize: 11, color: colors.textOnLime }}>{s.code}</Text>
          </View>
          <View style={{ flex: 1 }}>
            <Text style={{ fontFamily: 'Sora-Bold', fontSize: 15, color: colors.textPrimary }}>{s.title}</Text>
            <Text style={{ fontFamily: 'Sora-Medium', fontSize: 11, color: colors.textSecondary, marginTop: 2 }}>{s.detail}</Text>
          </View>
          <View style={{ alignItems: 'flex-end' }}>
            <Text style={{ fontFamily: 'Sora-ExtraBold', fontSize: 13, color: colors.textPrimary }}>{s.pace}</Text>
            <Text style={{ fontFamily: 'Sora-SemiBold', fontSize: 10, color: colors.textTertiary }}>/km</Text>
          </View>
        </View>
      ))}
    </View>
  );
}
```

### Primary "Go" Button

```tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RunGoButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
      backgroundColor: pressed ? colors.limePressed : colors.lime,
      borderRadius: 14, paddingVertical: 15, transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Ionicons name="play" size={15} color={colors.textOnLime} />
      <Text style={[typography.button, { color: colors.textOnLime }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.lime,        // active = Lime, no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={40} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(14,14,22,0.94)' }} />,
        tabBarLabelStyle: { fontFamily: 'Sora-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Plan',     tabBarIcon: ({ color }) => <Ionicons name="calendar-outline"  size={22} color={color} /> }} />
      <Tabs.Screen name="run"      options={{ title: 'Run',      tabBarIcon: ({ color }) => <Ionicons name="walk"              size={22} color={color} /> }} />
      <Tabs.Screen name="progress" options={{ title: 'Progress', tabBarIcon: ({ color }) => <Ionicons name="trending-up"       size={22} color={color} /> }} />
      <Tabs.Screen name="club"     options={{ title: 'Club',     tabBarIcon: ({ color }) => <Ionicons name="people"            size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle"     size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Week strip stagger-fade
o.value = withDelay(index * 40, withTiming(1, { duration: 220, easing: Easing.out(Easing.cubic) }));
// Today cell one-shot pulse: scale 1 → 1.04 → 1 via withSequence

// Session card entrance
s.value = withTiming(1, { duration: 300, easing: Easing.out(Easing.cubic) });

// Structure bar grow — animate each segment's flex via shared value
grow.value = withTiming(1, { duration: 500, easing: Easing.out(Easing.cubic) });

// Start run + haptic, then navigate to live run
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
// router.push('/run/live') with a slide-up presentation

// Live run: km haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// PR celebration
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
// badge spring: withSpring(1, { damping: 10, stiffness: 140 })

// Segmented control slide
indicatorX.value = withTiming(target, { duration: 220, easing: Easing.out(Easing.cubic) });
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Plan (tab) | `calendar-outline` |
| Run (tab) | `walk` (or MCI `run`) |
| Progress (tab) | `trending-up` |
| Club (tab) | `people` |
| Profile (tab) | `person-circle` |
| Start run | `play` |
| Pause / stop | `pause` / `stop` |
| Week switcher | `chevron-down` |
| Completed step | `checkmark` |
| PR / achievement | `trophy` / `flash` |
| Pace / speed | `speedometer` |
| Route map | `map` |
| Kudos | `thumbs-up` |
| Back / close | `chevron-back` / `close` |
| Settings | `settings-outline` |

## 7. Platform Notes

- **Font choice**: Sora (Google Fonts, SIL OFL) — free to bundle. Ship Regular/Medium/SemiBold/Bold/ExtraBold
- **Status bar**: `<StatusBar style="light" />` (dark-first); `"dark"` only on the optional light theme
- **Safe area**: use `react-native-safe-area-context`; the plan header respects the top inset; add bottom content padding for the absolute blurred tab bar; live-run controls float above the home indicator
- **Tabular numerals**: apply `fontVariant: ['tabular-nums']` (or a Sora tabular feature) on the live clock/pace/distance so the run screen doesn't shift
- **Run-type accessibility**: never color-only — include state/type in the `accessibilityLabel` for week cells ("Friday 16, today, intervals") and step rows
- **Dynamic Type**: `<Text>` honors system scale; set `allowFontScaling={false}` on eyebrow, pace/tag, day-cell number, stat labels, tab labels; cap session title scaling
- **Dark mode**: `useColorScheme()` may swap to the optional light theme — Indigo, Lime, and run-type hues are identical in both
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` — skip stagger/scale/grow, render at final state, no today-cell pulse, static PR badge
- **Gradient + glow**: `expo-linear-gradient` for the session card; the indigo glow is `shadowColor: colors.indigo` (iOS) / `elevation` (Android)
- **Reduce Transparency**: replace the blurred tab background with an opaque `#0E0E16`
- **Live Activity / Watch**: if targeting `expo` + native modules, mirror pace/distance/step + Lime progress with high contrast for mid-run glance-ability
