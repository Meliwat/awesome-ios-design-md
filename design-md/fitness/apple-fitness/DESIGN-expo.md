# Apple Fitness (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Apple Fitness's rings-on-black visual language into paste-ready Expo / React Native: a design-token module, themed components, an SVG Activity-rings stack, frosted-glass overlays, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // The three Activity rings (the brand — immutable, theme-invariant)
  move:       '#FA114F',
  moveLabel:  '#FF375F',   // brightened on dark for text
  exercise:   '#92E82A',
  exerciseHi: '#66FF00',
  stand:      '#1EE4E1',
  standHi:    '#00F0FF',

  // Ring tracks = ring color @ 22% opacity
  moveTrack:     'rgba(250,17,79,0.22)',
  exerciseTrack: 'rgba(146,232,42,0.22)',
  standTrack:    'rgba(30,228,225,0.22)',

  // Chrome accent (single)
  accent:        '#FF375F',
  accentPressed: '#D80E45',
  fitnessPlus:   '#C969E0',

  // Surfaces (dark — iPhone primary)
  canvas:    '#000000',
  grouped1:  '#1C1C1E',
  grouped2:  '#2C2C2E',
  grouped3:  '#3A3A3C',
  separator: '#38383A',
  fill:      'rgba(118,118,128,0.24)',

  // Text — Apple label-opacity ramp
  labelPrimary:   '#FFFFFF',
  labelSecondary: 'rgba(235,235,245,0.60)',
  labelTertiary:  'rgba(235,235,245,0.30)',

  // Semantic
  success:   '#30D158',
  error:     '#FF453A',
  awardGold: '#FFD60A',
} as const;

export type AFColor = keyof typeof colors;

// Ring config — order matters (outer → inner). Never recolor for theme.
export const ringConfig = [
  { key: 'move',     color: colors.move,     track: colors.moveTrack,     label: colors.moveLabel },
  { key: 'exercise', color: colors.exercise, track: colors.exerciseTrack, label: colors.exercise },
  { key: 'stand',    color: colors.stand,    track: colors.standTrack,    label: colors.stand },
] as const;
```

## 2. Typography

Apple Fitness uses SF Pro (the iOS system font). On RN, `System` resolves to SF Pro on iOS; bundle `Inter` (SIL OFL) as the cross-platform fallback. Numerals tabular.

```tsx
// app/_layout.tsx — only needed for the Inter fallback (Android / web)
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import { Platform, type TextStyle } from 'react-native';

// iOS → SF Pro via system font; else Inter
const f = (ios: TextStyle['fontWeight'], inter: string): Pick<TextStyle, 'fontFamily' | 'fontWeight'> =>
  Platform.OS === 'ios' ? { fontWeight: ios } : { fontFamily: inter };

const TABULAR: TextStyle = { fontVariant: ['tabular-nums'] };

export const typography = {
  largeTitle: { color: '#FFFFFF', ...f('800', 'Inter-ExtraBold'), fontSize: 40, lineHeight: 44, letterSpacing: -1 },
  date:       { color: '#FFFFFF', ...f('800', 'Inter-ExtraBold'), fontSize: 32, lineHeight: 35, letterSpacing: -0.6 },
  header:     { color: '#FFFFFF', ...f('800', 'Inter-ExtraBold'), fontSize: 26, lineHeight: 30, letterSpacing: -0.5 },
  section:    { color: '#FFFFFF', ...f('800', 'Inter-ExtraBold'), fontSize: 22, lineHeight: 26, letterSpacing: -0.4 },
  title3:     { color: '#FFFFFF', ...f('700', 'Inter-Bold'),      fontSize: 20, lineHeight: 25, letterSpacing: -0.3 },
  body:       { color: '#FFFFFF', ...f('600', 'Inter-SemiBold'),  fontSize: 17, lineHeight: 24, letterSpacing: -0.2 },
  bodyReg:    { color: '#FFFFFF', ...f('400', 'Inter-Regular'),   fontSize: 17, lineHeight: 26, letterSpacing: -0.2 },
  ringValue:  { ...TABULAR, color: '#FFFFFF', ...f('800', 'Inter-ExtraBold'), fontSize: 19, lineHeight: 21 },
  tileValue:  { ...TABULAR, color: '#FFFFFF', ...f('800', 'Inter-ExtraBold'), fontSize: 22, lineHeight: 24 },
  cardTitle:  { color: '#FFFFFF', ...f('600', 'Inter-SemiBold'),  fontSize: 15, lineHeight: 20, letterSpacing: -0.2 },
  footnote:   { color: 'rgba(235,235,245,0.60)', ...f('400', 'Inter-Regular'), fontSize: 13, lineHeight: 18 },
  eyebrow:    { ...f('700', 'Inter-Bold'),  fontSize: 12, lineHeight: 15, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  button:     { color: '#FFFFFF', ...f('600', 'Inter-SemiBold'),  fontSize: 17, lineHeight: 17, letterSpacing: -0.2 },
  tab:        { ...f('500', 'Inter-Regular'), fontSize: 10, lineHeight: 10 },
  badge:      { color: '#FFFFFF', ...f('700', 'Inter-Bold'), fontSize: 10, lineHeight: 10, letterSpacing: 0.5, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Activity Rings (SVG)

```tsx
// components/ActivityRings.tsx
import { useEffect } from 'react';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, withDelay, Easing } from 'react-native-reanimated';
import { ringConfig } from '../theme/colors';

const ACircle = Animated.createAnimatedComponent(Circle);

function Ring({ progress, color, track, r, cx, cy, sw, delay }: {
  progress: number; color: string; track: string; r: number; cx: number; cy: number; sw: number; delay: number;
}) {
  const C = 2 * Math.PI * r;
  const p = useSharedValue(0);
  useEffect(() => {
    p.value = withDelay(delay, withTiming(Math.min(progress, 1), { duration: 1000, easing: Easing.out(Easing.ease) }));
  }, [progress]);
  const animatedProps = useAnimatedProps(() => ({ strokeDashoffset: C * (1 - p.value) }));
  return (
    <>
      <Circle cx={cx} cy={cy} r={r} stroke={track} strokeWidth={sw} fill="none" />
      <ACircle cx={cx} cy={cy} r={r} stroke={color} strokeWidth={sw} fill="none"
        strokeLinecap="round" strokeDasharray={C} animatedProps={animatedProps}
        transform={`rotate(-90 ${cx} ${cy})`} />
    </>
  );
}

export function ActivityRings({ move, exercise, stand, size = 130, lineWidth = 14 }:
  { move: number; exercise: number; stand: number; size?: number; lineWidth?: number }) {
  const c = size / 2;
  const radii = [c - lineWidth / 2 - 2, c - lineWidth * 1.5 - 5, c - lineWidth * 2.5 - 8];
  const vals = [move, exercise, stand];
  return (
    <Svg width={size} height={size}>
      {ringConfig.map((rc, i) => (
        <Ring key={rc.key} progress={vals[i]} color={rc.color} track={rc.track}
          r={radii[i]} cx={c} cy={c} sw={lineWidth} delay={i * 80} />
      ))}
    </Svg>
  );
}
```

### Ring Legend + Hero Card

```tsx
// components/RingHeroCard.tsx
import { View, Text } from 'react-native';
import { ActivityRings } from './ActivityRings';
import { typography } from '../theme/typography';
import { colors, ringConfig } from '../theme/colors';

const data = [
  { name: 'Move',     value: '486', goal: '/620 KCAL' },
  { name: 'Exercise', value: '38',  goal: '/30 MIN' },
  { name: 'Stand',    value: '9',   goal: '/12 HR' },
];

export function RingHeroCard() {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 20,
      marginHorizontal: 16, padding: 22, borderRadius: 18,
      backgroundColor: colors.grouped1,
    }}>
      <ActivityRings move={0.80} exercise={1.27} stand={0.75} size={130} />
      <View style={{ flex: 1, gap: 14 }}>
        {data.map((it, i) => (
          <View key={it.name}>
            <Text style={[typography.eyebrow, { color: ringConfig[i].label }]}>{it.name}</Text>
            <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4, marginTop: 2 }}>
              <Text style={typography.ringValue}>{it.value}</Text>
              <Text style={{ ...typography.footnote, color: colors.labelSecondary }}>{it.goal}</Text>
            </View>
          </View>
        ))}
      </View>
    </View>
  );
}
```

### Metric Tile

```tsx
// components/MetricTile.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function MetricTile({ icon, tint, name, value, unit, sub }: {
  icon: any; tint: string; name: string; value: string; unit?: string; sub: string;
}) {
  return (
    <View style={{ flex: 1, padding: 14, borderRadius: 14, backgroundColor: colors.grouped1 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name={icon} size={16} color={tint} />
        <Text style={{ ...typography.footnote, color: colors.labelSecondary, fontSize: 12 }}>{name}</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 2, marginTop: 8 }}>
        <Text style={typography.tileValue}>{value}</Text>
        {unit ? <Text style={{ ...typography.footnote, color: colors.labelSecondary }}>{unit}</Text> : null}
      </View>
      <Text style={{ ...typography.footnote, color: colors.labelTertiary, marginTop: 2 }}>{sub}</Text>
    </View>
  );
}
```

### Fitness+ Card (frosted overlays)

```tsx
// components/FitnessPlusCard.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function FitnessPlusCard({ badge, type, title, meta, gradient }: {
  badge: string; type: string; title: string; meta: string; gradient: [string, string];
}) {
  return (
    <View style={{ width: 168 }}>
      <View style={{ height: 200, borderRadius: 16, overflow: 'hidden' }}>
        <LinearGradient colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }} />
        <BlurView intensity={28} tint="dark" style={{ position: 'absolute', top: 12, left: 12, borderRadius: 6, overflow: 'hidden' }}>
          <Text style={[typography.badge, { paddingHorizontal: 8, paddingVertical: 4 }]}>{badge}</Text>
        </BlurView>
        <BlurView intensity={36} tint="light" style={{ position: 'absolute', bottom: 14, left: 14, width: 34, height: 34, borderRadius: 17, overflow: 'hidden', alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="play" size={14} color="#FFF" style={{ marginLeft: 2 }} />
        </BlurView>
      </View>
      <Text style={[typography.eyebrow, { color: colors.moveLabel, marginTop: 10 }]}>{type}</Text>
      <Text style={[typography.cardTitle, { marginTop: 3 }]}>{title}</Text>
      <Text style={[typography.footnote, { marginTop: 2 }]}>{meta}</Text>
    </View>
  );
}
```

### Buttons

```tsx
// components/AFButton.tsx
import { Pressable, Text } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function AFPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.accentPressed : colors.move,
      borderRadius: 14, paddingVertical: 15, alignItems: 'center',
      transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function AFTintedButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      backgroundColor: 'rgba(250,17,79,0.18)',
      borderRadius: 12, paddingVertical: 12, paddingHorizontal: 22, alignSelf: 'flex-start',
    }}>
      <Text style={{ ...typography.button, fontSize: 15, color: colors.accent }}>{title}</Text>
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
        tabBarActiveTintColor:  colors.accent,            // Move-pink accent, no pill
        tabBarInactiveTintColor: colors.labelTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.separator, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={50} tint="dark" style={{ flex: 1 }} />,
        tabBarLabelStyle: { fontSize: 10 },
      }}>
      <Tabs.Screen name="index"      options={{ title: 'Summary',  tabBarIcon: ({ color }) => <Ionicons name="ellipse-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="fitnessplus" options={{ title: 'Fitness+', tabBarIcon: ({ color }) => <Ionicons name="play-circle"   size={24} color={color} /> }} />
      <Tabs.Screen name="sharing"    options={{ title: 'Sharing',  tabBarIcon: ({ color }) => <Ionicons name="people"         size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Rings sweep from 0, staggered Move → Exercise → Stand (delay i * 80ms)
p.value = withDelay(i * 80, withTiming(Math.min(progress, 1), { duration: 1000, easing: Easing.out(Easing.ease) }));

// Ring close — particle burst + success haptic
import * as Haptics from 'expo-haptics';
if (progress >= 1) Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Card press scale
style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.97 : 1 }] })}

// Tab / segment change
Haptics.selectionAsync();

// Start Workout
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Live HUD (in-workout) — smooth ease as values change
liveMove.value = withTiming(next, { duration: 300 });

// Fitness+ shelf — parallax via Animated scroll offset; snapToInterval on the ScrollView
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). On iOS you may bridge to SF Symbols via `expo-symbols` for exact parity.

| Purpose | Ionicons | SF Symbol (via expo-symbols) |
|---------|----------|------------------------------|
| Summary (tab) | `ellipse-outline` / `ellipse` | `circle.circle` |
| Fitness+ (tab) | `play-circle` | `play.rectangle.fill` |
| Sharing (tab) | `people` | `person.2.fill` |
| Steps | `walk` | `figure.walk` |
| Distance | `map-outline` | `point.topleft.down.curvedto.point.bottomright.up` |
| Heart Rate | `heart` | `heart.fill` |
| Workouts | `fitness` | `figure.run` |
| Play (card) | `play` | `play.fill` |
| Move detail | `flame` | `flame.fill` |
| Stand detail | `body` | `figure.stand` |
| Award | `trophy` | `medal.fill` |
| Share | `share-outline` | `square.and.arrow.up` |
| See All | `chevron-forward` | `chevron.right` |
| Trends up / down | `arrow-up` / `arrow-down` | `arrow.up.right` / `arrow.down.right` |

## 7. Platform Notes

- **Font**: on iOS use the system font (resolves to SF Pro); bundle `Inter` only as the Android/web fallback — never substitute a different face on iOS
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on the ring legend, metric tiles, and in-workout HUD so live values don't jitter
- **Status bar**: `<StatusBar style="light" />` — iPhone Fitness is dark-first
- **Blur**: use `expo-blur` `BlurView` for the tab bar background and frosted Fitness+ badge/play overlays — this is core to the Apple look; provide a solid `#1C1C1E` fallback when Reduce Transparency is on
- **Safe area**: wrap screens in `SafeAreaView`; with an absolute blurred tab bar, pad scroll content by the tab-bar height + bottom inset
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on the ring legend, in-workout HUD, tab labels, and frosted badges; ring `size` scales with container width, not type
- **SVG**: `react-native-svg` for the rings; animate with `react-native-reanimated` `useAnimatedProps` on `strokeDashoffset`; the soft "glow" can be approximated with a duplicated blurred stroke or a subtle `shadow*`/`elevation`
- **Ring colors are sacred**: never branch ring colors on `useColorScheme()` — they are theme-invariant; only swap surfaces/labels for light (iPad)
- **Accessibility**: announce rings as "Move ring, 486 of 620 kilocalories"; keep text labels beside colored rings so hue isn't the only signal; differentiate rings by radius, not color alone
- **Reduce Motion**: gate the ring sweep, particle burst, card scale, and shelf parallax behind `AccessibilityInfo.isReduceMotionEnabled()` — fall back to a 250ms crossfade to final ring values and a static "closed" check
