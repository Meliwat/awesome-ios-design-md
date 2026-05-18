# Noom (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Noom's visual language into paste-ready Expo / React Native code: a token module, themed components, the signature lesson card + food log + weight graph, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  noomBlue:        '#2A5DF6',
  noomBluePressed: '#1E47C8',
  noomTeal:        '#1FC29B',
  noomNavy:        '#0C1B4D',

  // Canvas & surfaces (light)
  canvas:      '#FBFBFD',
  surface:     '#FFFFFF',
  surfaceSub:  '#F2F3F7',
  dividerLt:   '#E6E7EC',

  // Canvas & surfaces (dark)
  darkCanvas:  '#121212',
  darkSurf1:   '#1C1C1E',
  darkSurf2:   '#262629',
  darkDivider: '#2E2E31',

  // Text
  textPriLt: '#1A1A1F',
  textSecLt: '#6A6A70',
  textPriDk: '#F0F0F2',
  textSecDk: '#9A9AA0',

  // Food-color system (FIXED both modes)
  foodGreen:  '#34C759',
  foodYellow: '#FFC531',
  foodRed:    '#FF5A52',

  // Coach
  coachPurple: '#7B61FF',

  // Semantic
  success: '#34C759',
  error:   '#FF5A52',
  flame:   '#FF8A3D',
} as const;

export type FoodClass = 'green' | 'yellow' | 'red';
export const foodMap: Record<FoodClass, { color: string; tint: string; label: string }> = {
  green:  { color: colors.foodGreen,  tint: 'rgba(52,199,89,0.16)',  label: 'Green' },
  yellow: { color: colors.foodYellow, tint: 'rgba(255,197,49,0.16)', label: 'Yellow' },
  red:    { color: colors.foodRed,    tint: 'rgba(255,90,82,0.16)',  label: 'Red' },
};

export const lessonGradient = ['#2A5DF6', '#1FC29B'] as const;
```

## 2. Typography

Load **Poppins** via `expo-font`. Heavy numerals, conversational body.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Poppins-Regular':  require('../assets/fonts/Poppins-Regular.ttf'),
    'Poppins-Medium':   require('../assets/fonts/Poppins-Medium.ttf'),
    'Poppins-SemiBold': require('../assets/fonts/Poppins-SemiBold.ttf'),
    'Poppins-Bold':     require('../assets/fonts/Poppins-Bold.ttf'),
    'Poppins-ExtraBold':require('../assets/fonts/Poppins-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
const p  = { color: '#1A1A1F' } satisfies TextStyle;
const p2 = { color: '#6A6A70' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...p,  fontFamily: 'Poppins-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  greeting:    { ...p,  fontFamily: 'Poppins-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  heroStat:    { ...p,  fontFamily: 'Poppins-ExtraBold', fontSize: 24, lineHeight: 27, letterSpacing: -0.3 },
  section:     { ...p,  fontFamily: 'Poppins-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:   { ...p,  fontFamily: 'Poppins-Bold',      fontSize: 18, lineHeight: 23 },
  body:        { ...p,  fontFamily: 'Poppins-Regular',   fontSize: 16, lineHeight: 24 },
  listItem:    { ...p,  fontFamily: 'Poppins-SemiBold',  fontSize: 15, lineHeight: 21 },
  meta:        { ...p2, fontFamily: 'Poppins-Regular',   fontSize: 14, lineHeight: 20 },
  eyebrow:     { fontFamily: 'Poppins-SemiBold', fontSize: 12, lineHeight: 14, letterSpacing: 1.0 },
  button:      { color: '#FFFFFF', fontFamily: 'Poppins-Bold', fontSize: 16, lineHeight: 16, letterSpacing: 0.1 },
  pill:        { fontFamily: 'Poppins-Bold', fontSize: 11, lineHeight: 11, letterSpacing: 0.2 },
  axis:        { ...p2, fontFamily: 'Poppins-Bold', fontSize: 9, lineHeight: 9 },
  tab:         { fontFamily: 'Poppins-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Daily Psychology Lesson Card

```tsx
// components/LessonCard.tsx
import { useEffect } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, Easing } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, lessonGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function LessonCard({
  eyebrow, title, step, total, onContinue,
}: { eyebrow: string; title: string; step: number; total: number; onContinue: () => void }) {
  const s = useSharedValue(0.96);
  const o = useSharedValue(0);
  useEffect(() => {
    s.value = withTiming(1, { duration: 320, easing: Easing.out(Easing.cubic) });
    o.value = withTiming(1, { duration: 320 });
  }, []);
  const anim = useAnimatedStyle(() => ({ transform: [{ scale: s.value }], opacity: o.value }));

  return (
    <Animated.View style={[anim, {
      borderRadius: 20,
      shadowColor: colors.noomBlue, shadowOpacity: 0.22, shadowRadius: 26, shadowOffset: { width: 0, height: 10 }, elevation: 10,
    }]}>
      <LinearGradient colors={lessonGradient as unknown as string[]} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ borderRadius: 20, padding: 18, overflow: 'hidden' }}>
        <View style={{ position: 'absolute', right: -30, top: -30, width: 110, height: 110, borderRadius: 55, backgroundColor: 'rgba(255,255,255,0.10)' }} />
        <Text style={[typography.eyebrow, { color: 'rgba(255,255,255,0.85)' }]}>{eyebrow}</Text>
        <Text style={[typography.cardTitle, { color: '#FFF', marginTop: 8, maxWidth: '80%' }]}>{title}</Text>

        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, marginTop: 14 }}>
          <View style={{ flex: 1, height: 6, borderRadius: 3, backgroundColor: 'rgba(255,255,255,0.25)' }}>
            <View style={{ height: '100%', width: `${(step / total) * 100}%`, borderRadius: 3, backgroundColor: '#FFF' }} />
          </View>
          <Text style={[typography.pill, { color: '#FFF' }]}>{step} of {total}</Text>
        </View>

        <Pressable onPress={onContinue} style={{
          marginTop: 14, alignSelf: 'flex-start', flexDirection: 'row', alignItems: 'center', gap: 6,
          backgroundColor: '#FFF', paddingHorizontal: 16, paddingVertical: 9, borderRadius: 999,
        }}>
          <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 13, color: colors.noomBlue }}>Continue lesson</Text>
          <Ionicons name="arrow-forward" size={13} color={colors.noomBlue} />
        </Pressable>
      </LinearGradient>
    </Animated.View>
  );
}
```

### Food Log Row + Stacked Ratio Bar

```tsx
// components/FoodLog.tsx
import { Text, View } from 'react-native';
import { colors, foodMap, type FoodClass } from '../theme/colors';
import { typography } from '../theme/typography';

export function FoodRatioBar({ green, yellow, red }: { green: number; yellow: number; red: number }) {
  const sum = green + yellow + red || 1;
  return (
    <View style={{ flexDirection: 'row', height: 10, borderRadius: 5, overflow: 'hidden', marginVertical: 10 }}>
      <View style={{ flex: green / sum, backgroundColor: colors.foodGreen }} />
      <View style={{ flex: yellow / sum, backgroundColor: colors.foodYellow }} />
      <View style={{ flex: red / sum, backgroundColor: colors.foodRed }} />
    </View>
  );
}

export function FoodRow({ name, cls, kcal }: { name: string; cls: FoodClass; kcal: number }) {
  const f = foodMap[cls];
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingVertical: 9, borderBottomWidth: 1, borderBottomColor: colors.dividerLt,
    }}>
      <View style={{ width: 10, height: 10, borderRadius: 5, backgroundColor: f.color }} />
      <Text style={[typography.listItem, { flex: 1 }]}>{name}</Text>
      <View style={{ backgroundColor: f.tint, paddingHorizontal: 9, paddingVertical: 3, borderRadius: 999 }}>
        <Text style={[typography.pill, { color: f.color }]}>{f.label}</Text>
      </View>
      <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 13, color: colors.textSecLt, width: 52, textAlign: 'right' }}>
        {kcal}
      </Text>
    </View>
  );
}
```

### Weight Graph Card

```tsx
// components/WeightGraphCard.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Svg, { Path, Line, Circle, Defs, LinearGradient as SvgGrad, Stop } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const APath = Animated.createAnimatedComponent(Path);

export function WeightGraphCard({ pathD, fillD, goalY, w = 280, h = 90 }: {
  pathD: string; fillD: string; goalY: number; w?: number; h?: number;
}) {
  const progress = useSharedValue(0);
  useEffect(() => { progress.value = withTiming(1, { duration: 700, easing: Easing.out(Easing.cubic) }); }, []);
  const len = w * 1.4;
  const dash = useAnimatedProps(() => ({ strokeDashoffset: len * (1 - progress.value) }));

  return (
    <View style={{
      backgroundColor: colors.surface, borderRadius: 18, borderWidth: 1, borderColor: colors.dividerLt, padding: 16,
      shadowColor: colors.noomNavy, shadowOpacity: 0.06, shadowRadius: 18, shadowOffset: { width: 0, height: 6 }, elevation: 4,
    }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'baseline' }}>
        <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 14, color: colors.textPriLt }}>Weight</Text>
        <Text style={{ fontFamily: 'Poppins-SemiBold', fontSize: 11, color: colors.textSecLt }}>Last 30 days</Text>
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 8, marginTop: 4 }}>
        <Text style={typography.heroStat}>164.2 lb</Text>
        <Text style={{ fontFamily: 'Poppins-SemiBold', fontSize: 13, color: colors.noomTeal }}>▼ 6.8 lb</Text>
      </View>

      <Svg width="100%" height={h} viewBox={`0 0 ${w} ${h}`} style={{ marginTop: 6 }} preserveAspectRatio="none">
        <Defs>
          <SvgGrad id="wg" x1="0" y1="0" x2="0" y2="1">
            <Stop offset="0" stopColor={colors.noomBlue} stopOpacity={0.30} />
            <Stop offset="1" stopColor={colors.noomBlue} stopOpacity={0.02} />
          </SvgGrad>
        </Defs>
        <Path d={fillD} fill="url(#wg)" />
        <Line x1="4" y1={goalY} x2={w - 4} y2={goalY} stroke={colors.noomTeal} strokeWidth={1.5} strokeDasharray="3 4" />
        <APath d={pathD} stroke={colors.noomBlue} strokeWidth={2.5} fill="none" strokeLinecap="round"
               strokeDasharray={len} animatedProps={dash} />
        <Circle cx={w - 4} cy={goalY - 4} r={3.5} fill={colors.noomBlue} stroke={colors.surface} strokeWidth={2} />
      </Svg>

      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 6 }}>
        {['Apr 14', 'Apr 28', 'May 12'].map((d) => <Text key={d} style={typography.axis}>{d}</Text>)}
      </View>
    </View>
  );
}
```

### Primary Button (Pill)

```tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function NoomPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.noomBluePressed : colors.noomBlue,
        borderRadius: 999, paddingVertical: 15, paddingHorizontal: 30, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
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
        headerShown: false,
        tabBarActiveTintColor: colors.noomBlue,    // active = Noom Blue, no pill
        tabBarInactiveTintColor: colors.textSecDk,
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.dividerLt },
        tabBarLabelStyle: { fontFamily: 'Poppins-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Today',   tabBarIcon: ({ color }) => <Ionicons name="home"               size={22} color={color} /> }} />
      <Tabs.Screen name="learn"   options={{ title: 'Learn',   tabBarIcon: ({ color }) => <Ionicons name="book"               size={22} color={color} /> }} />
      <Tabs.Screen name="log"     options={{ title: 'Log',     tabBarIcon: ({ color }) => <Ionicons name="add-circle"         size={22} color={color} /> }} />
      <Tabs.Screen name="coach"   options={{ title: 'Coach',   tabBarIcon: ({ color }) => <Ionicons name="chatbubble"         size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person-circle"      size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Lesson card entrance
s.value = withTiming(1, { duration: 320, easing: Easing.out(Easing.cubic) });

// Weight graph draw-in (strokeDashoffset reveal)
progress.value = withTiming(1, { duration: 700, easing: Easing.out(Easing.cubic) });

// Food ratio bar re-animates on add — animate segment flex via withTiming(400)
// New food row insert
import Animated, { FadeInDown } from 'react-native-reanimated';
// entering={FadeInDown.duration(250)}

// Segmented control slide
indicatorX.value = withTiming(target, { duration: 220, easing: Easing.out(Easing.cubic) });

// Lesson complete + haptic
import * as Haptics from 'expo-haptics';
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Coach typing indicator: 3 dots scale 0.6↔1 via withRepeat(withTiming(1,{duration:600}),-1,true)
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Today (tab) | `home` |
| Learn (tab) | `book` |
| Log (tab) | `add-circle` |
| Coach (tab) | `chatbubble` |
| Profile (tab) | `person-circle` |
| Lesson CTA arrow | `arrow-forward` |
| Weight delta down | `arrow-down` |
| Search / scan | `search` / `barcode-outline` |
| Coach send | `arrow-up-circle` |
| Streak | `flame` |
| Add food | `add` |
| Back / close | `chevron-back` / `close` |
| Goal reached | `checkmark-circle` |
| Settings | `settings-outline` |

## 7. Platform Notes

- **Font choice**: Poppins (Google Fonts, SIL OFL) — free to bundle. Ship Regular/Medium/SemiBold/Bold/ExtraBold
- **Status bar**: `<StatusBar style="dark" />` on light mode, `"light"` on dark
- **Safe area**: use `react-native-safe-area-context`; the greeting/header respects the top inset; the coach composer floats above the keyboard with `KeyboardAvoidingView`
- **SVG**: `react-native-svg` powers the weight graph; animate the curve via Reanimated `useAnimatedProps` on a `Path` `strokeDashoffset`
- **Food-color accessibility**: never color-only — always render the text label next to the dot; set `accessibilityLabel` per row ("Greek yogurt, Green food, 180 calories")
- **Dynamic Type**: `<Text>` honors system scale; set `allowFontScaling={false}` on eyebrow, food pill, axis, tab labels; cap lesson title scaling
- **Dark mode**: `useColorScheme()` swaps canvas/surface tokens to `darkCanvas`/`darkSurf1`; Noom Blue and the food-color hues are identical in both modes
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` — skip the lesson scale-in and graph draw, render at final state, disable the coach typing pulse
- **Gradient**: use `expo-linear-gradient` for the lesson card; the colored glow is a `shadowColor: colors.noomBlue` (iOS) / `elevation` (Android approximation)
- **Reduce Transparency**: replace any blurred tab background with an opaque surface
