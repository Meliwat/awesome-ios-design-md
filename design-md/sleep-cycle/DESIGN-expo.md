# Sleep Cycle (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Sleep Cycle's visual language into paste-ready Expo / React Native code: a token module, themed components, the signature hypnogram + score ring, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, `react-native-reanimated` v3, and optionally `expo-brightness`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Night gradient & canvas (dark, default)
  nightTop:    '#2A2D5A',
  nightMid:    '#3B4371',
  canvasDeep:  '#14152E',
  surface1:    '#1E2046',
  surface2:    '#292C58',
  divider:     '#353974',

  // Canvas (light — Dawn)
  dawnCanvas:  '#F4F5FC',
  dawnSurface: '#FFFFFF',
  dawnDivider: '#E3E5F2',

  // Text
  textPrimary:   '#F2F3FB',
  textSecondary: '#A6A9D4',
  textTertiary:  '#6C70A8',
  textPrimaryLt: '#1B1D3A',

  // Accents
  accent:        '#6C7BFF',
  accentPressed: '#5563E6',
  deepSleep:     '#3D4ABF',
  aqua:          '#4FD1E6',
  aquaSoft:      '#7FE3F0',

  // Sleep stages (semantic)
  stageAwake: '#FF8FB1',
  stageLight: '#6C7BFF',
  stageDeep:  '#3D4ABF',
  stageREM:   '#4FD1E6',

  // Semantic
  success: '#4FD1A0',
  error:   '#FF6B81',
  gold:    '#FFC56B',
} as const;

export const nightShell = ['#2A2D5A', '#3B4371', '#14152E'] as const;
export const nightShellLocations = [0, 0.3, 1] as const;

export function scoreColor(score: number): string {
  if (score < 50) return colors.stageAwake; // rose
  if (score < 75) return colors.accent;     // indigo
  return colors.aqua;                       // aqua
}
```

## 2. Typography

Load **Nunito** via `expo-font`. Numerals heavy (800–900); body light (400).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Nunito-Regular':  require('../assets/fonts/Nunito-Regular.ttf'),
    'Nunito-Medium':   require('../assets/fonts/Nunito-Medium.ttf'),
    'Nunito-SemiBold': require('../assets/fonts/Nunito-SemiBold.ttf'),
    'Nunito-Bold':     require('../assets/fonts/Nunito-Bold.ttf'),
    'Nunito-ExtraBold':require('../assets/fonts/Nunito-ExtraBold.ttf'),
    'Nunito-Black':    require('../assets/fonts/Nunito-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
const p  = { color: '#F2F3FB' } satisfies TextStyle;
const p2 = { color: '#A6A9D4' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...p,  fontFamily: 'Nunito-Black',     fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  heroStat:    { ...p,  fontFamily: 'Nunito-Black',     fontSize: 34, lineHeight: 37, letterSpacing: -0.5 },
  section:     { ...p,  fontFamily: 'Nunito-ExtraBold', fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:   { ...p,  fontFamily: 'Nunito-Bold',      fontSize: 18, lineHeight: 23 },
  body:        { ...p,  fontFamily: 'Nunito-Regular',   fontSize: 16, lineHeight: 24 },
  listItem:    { ...p,  fontFamily: 'Nunito-SemiBold',  fontSize: 15, lineHeight: 21 },
  meta:        { ...p2, fontFamily: 'Nunito-Regular',   fontSize: 14, lineHeight: 20 },
  eyebrow:     { color: '#4FD1E6', fontFamily: 'Nunito-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 1.4 },
  button:      { color: '#FFFFFF', fontFamily: 'Nunito-ExtraBold', fontSize: 16, lineHeight: 16, letterSpacing: 0.2 },
  pill:        { fontFamily: 'Nunito-ExtraBold', fontSize: 11, lineHeight: 11, letterSpacing: 0.2 },
  axis:        { ...p2, fontFamily: 'Nunito-Bold', fontSize: 9, lineHeight: 9 },
  tab:         { fontFamily: 'Nunito-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Night Shell Wrapper

```tsx
// components/NightShell.tsx
import { LinearGradient } from 'expo-linear-gradient';
import { nightShell, nightShellLocations } from '../theme/colors';

export function NightShell({ children }: { children: React.ReactNode }) {
  return (
    <LinearGradient
      colors={nightShell as unknown as string[]}
      locations={nightShellLocations as unknown as number[]}
      style={{ flex: 1 }}
    >
      {children}
    </LinearGradient>
  );
}
```

### Sleep-Analysis Graph (Hypnogram)

```tsx
// components/HypnogramCard.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Svg, { Path, Defs, LinearGradient as SvgGrad, Stop } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const APath = Animated.createAnimatedComponent(Path);

/** points: normalized {x:0..1, y:0..1} (y 0=awake/top, 1=deep/bottom) */
export function HypnogramCard({ points, w = 280, h = 110 }: { points: { x: number; y: number }[]; w?: number; h?: number }) {
  const progress = useSharedValue(0);
  useEffect(() => { progress.value = withTiming(1, { duration: 900, easing: Easing.out(Easing.cubic) }); }, []);

  const toXY = (pt: { x: number; y: number }) => [pt.x * w, 8 + pt.y * (h - 16)] as const;
  let line = '';
  points.forEach((pt, i) => {
    const [x, y] = toXY(pt);
    if (i === 0) { line += `M${x},${y}`; return; }
    const [px, py] = toXY(points[i - 1]);
    const mx = (px + x) / 2, my = (py + y) / 2;
    line += ` Q${px},${py} ${mx},${my} Q${x},${y} ${x},${y}`;
  });
  const fill = `${line} L${w},${h} L0,${h} Z`;
  const totalLen = w * 1.4;
  const dashProps = useAnimatedProps(() => ({
    strokeDashoffset: totalLen * (1 - progress.value),
  }));

  return (
    <View style={{
      backgroundColor: colors.surface1, borderRadius: 20, borderWidth: 1, borderColor: colors.divider,
      padding: 16, shadowColor: '#000', shadowOpacity: 0.35, shadowRadius: 24, shadowOffset: { width: 0, height: 8 }, elevation: 8,
    }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginBottom: 12 }}>
        <Text style={{ ...typography.cardTitle, fontSize: 13 }}>Sleep stages</Text>
        <Text style={[typography.axis, { fontSize: 11 }]}>23:42 — 07:18</Text>
      </View>

      <Svg width="100%" height={h} viewBox={`0 0 ${w} ${h}`} preserveAspectRatio="none">
        <Defs>
          <SvgGrad id="wf" x1="0" y1="0" x2="0" y2="1">
            <Stop offset="0" stopColor={colors.accent} stopOpacity={0.45} />
            <Stop offset="1" stopColor={colors.accent} stopOpacity={0.02} />
          </SvgGrad>
        </Defs>
        <Path d={fill} fill="url(#wf)" />
        <APath d={line} stroke={colors.aquaSoft} strokeWidth={2.5} strokeLinecap="round"
               fill="none" strokeDasharray={totalLen} animatedProps={dashProps} />
      </Svg>

      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 6 }}>
        {['00', '02', '04', '06', '07'].map((t) => (
          <Text key={t} style={typography.axis}>{t}</Text>
        ))}
      </View>

      <View style={{ flexDirection: 'row', gap: 14, marginTop: 12 }}>
        {([['Awake', colors.stageAwake], ['Light', colors.stageLight], ['Deep', colors.stageDeep], ['REM', colors.stageREM]] as const)
          .map(([label, c]) => (
            <View key={label} style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
              <View style={{ width: 9, height: 9, borderRadius: 3, backgroundColor: c }} />
              <Text style={{ ...typography.tab, color: colors.textSecondary }}>{label}</Text>
            </View>
          ))}
      </View>
    </View>
  );
}
```

### Sleep-Quality Ring

```tsx
// components/ScoreRing.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { colors, scoreColor } from '../theme/colors';
import { typography } from '../theme/typography';

const ACircle = Animated.createAnimatedComponent(Circle);

export function ScoreRing({ score, size = 92 }: { score: number; size?: number }) {
  const r = 42, c = 2 * Math.PI * r;
  const progress = useSharedValue(0);
  useEffect(() => { progress.value = withTiming(1, { duration: 800, easing: Easing.out(Easing.cubic) }); }, []);
  const animProps = useAnimatedProps(() => ({ strokeDashoffset: c * (1 - progress.value) }));

  return (
    <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={size} height={size} viewBox="0 0 100 100" style={{ position: 'absolute' }}>
        <Circle cx="50" cy="50" r={r} stroke={colors.surface2} strokeWidth={9} fill="none" />
        <ACircle cx="50" cy="50" r={r} stroke={scoreColor(score)} strokeWidth={9} fill="none"
                 strokeLinecap="round" strokeDasharray={c} animatedProps={animProps}
                 transform="rotate(-90 50 50)" />
      </Svg>
      <Text style={{ ...typography.heroStat, fontSize: 26 }}>{score}</Text>
      <Text style={{ ...typography.tab, fontSize: 9, color: colors.textSecondary, marginTop: 2 }}>SLEEP QUALITY</Text>
    </View>
  );
}
```

### Smart-Alarm Card

```tsx
// components/SmartAlarmCard.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SmartAlarmCard({ time = '07:30' }: { time?: string }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      backgroundColor: colors.surface1, borderRadius: 18, borderWidth: 1, borderColor: colors.divider, padding: 14,
    }}>
      <LinearGradient colors={[colors.accent, colors.deepSleep]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 38, height: 38, borderRadius: 12, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="alarm-outline" size={20} color="#FFF" />
      </LinearGradient>
      <View style={{ flex: 1 }}>
        <Text style={{ ...typography.listItem, fontFamily: 'Nunito-ExtraBold' }}>Smart alarm</Text>
        <Text style={{ ...typography.tab, fontSize: 11, color: colors.textSecondary, fontFamily: 'Nunito-SemiBold' }}>
          Wakes you in your lightest sleep
        </Text>
      </View>
      <Text style={{ ...typography.heroStat, fontSize: 19 }}>{time}</Text>
    </View>
  );
}
```

### Primary Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.98 : 1 }] })}>
      <LinearGradient colors={[colors.accent, colors.deepSleep]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{
          borderRadius: 16, paddingVertical: 15, paddingHorizontal: 30, alignItems: 'center',
          shadowColor: colors.accent, shadowOpacity: 0.35, shadowRadius: 20, shadowOffset: { width: 0, height: 8 }, elevation: 8,
        }}>
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
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
        tabBarActiveTintColor: colors.aqua,        // active = aqua, no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={40} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(20,21,46,0.92)' }} />,
        tabBarLabelStyle: { fontFamily: 'Nunito-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Sleep',   tabBarIcon: ({ color }) => <Ionicons name="pulse"             size={22} color={color} /> }} />
      <Tabs.Screen name="journal" options={{ title: 'Journal', tabBarIcon: ({ color }) => <Ionicons name="calendar-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="alarm"   options={{ title: 'Alarm',   tabBarIcon: ({ color }) => <Ionicons name="alarm-outline"    size={22} color={color} /> }} />
      <Tabs.Screen name="sounds"  options={{ title: 'Sounds',  tabBarIcon: ({ color }) => <Ionicons name="musical-notes-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Hypnogram draw-in — strokeDashoffset reveal
progress.value = withTiming(1, { duration: 900, easing: Easing.out(Easing.cubic) });

// Score ring sweep
progress.value = withTiming(1, { duration: 800, easing: Easing.out(Easing.cubic) });

// Bars grow staggered
bars.forEach((b, i) => { b.value = withDelay(i * 40, withTiming(1, { duration: 500, easing: Easing.out(Easing.cubic) })); });

// Segmented control slide
indicatorX.value = withTiming(targetX, { duration: 220, easing: Easing.out(Easing.cubic) });

// Toggle + haptic
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Session start: pulse moon, then dim
import * as Brightness from 'expo-brightness';
moonScale.value = withRepeat(withTiming(1.06, { duration: 1200 }), -1, true);
const orig = await Brightness.getBrightnessAsync();
await Brightness.setBrightnessAsync(Math.max(0.05, orig - 0.4)); // restore orig on session end
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For a heart-rate / waveform glyph, `MaterialCommunityIcons` has `sleep`-adjacent icons.

| Purpose | Ionicons |
|---------|----------|
| Sleep (tab) | `pulse` |
| Journal (tab) | `calendar-outline` |
| Alarm (tab) | `alarm-outline` |
| Sounds (tab) | `musical-notes-outline` |
| Profile (tab) | `person-circle-outline` |
| Smart alarm tile | `alarm-outline` |
| Moon / session | `moon` (or MCI `weather-night`) |
| Play / pause | `play` / `pause` |
| Trend up | `trending-up` |
| Streak | `flame` |
| Settings | `settings-outline` |
| Back / close | `chevron-back` / `close` |
| Snooze | `time-outline` |
| Heart rate | `heart` |

## 7. Platform Notes

- **Font choice**: Nunito (Google Fonts, SIL OFL) — free to bundle. Ship Regular/Medium/SemiBold/Bold/ExtraBold/Black
- **Status bar**: `<StatusBar style="light" />` (dark-first); switch to `"dark"` only on the Dawn light theme
- **Gradient shell**: wrap each screen in `<NightShell>`; the gradient must reach under the status bar and the absolute-positioned blurred tab bar — add bottom content padding equal to tab-bar height + safe area
- **Safe area**: use `react-native-safe-area-context`; the header eyebrow/title respects the top inset
- **SVG**: `react-native-svg` powers the hypnogram and score ring; animate via Reanimated `useAnimatedProps` on `Circle`/`Path`
- **Brightness**: `expo-brightness` for session auto-dim — request permission, never drop below 0.05, always restore on session end and on unmount
- **Dynamic Type**: `<Text>` honors system scale; set `allowFontScaling={false}` on axis labels, legend, tab labels, pill text, ring sublabel
- **Dark mode**: `useColorScheme()` swaps to the Dawn palette (`dawnCanvas`, `dawnSurface`) — accent/aqua/stage colors stay identical
- **Accessibility**: give the hypnogram and ring `accessibilityRole="image"` with a summarizing `accessibilityLabel` ("Slept 7h 36m, quality 78 of 100, 2 awakenings") instead of exposing the path
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` — skip the draw-in/sweep, render at final value, disable the moon pulse
