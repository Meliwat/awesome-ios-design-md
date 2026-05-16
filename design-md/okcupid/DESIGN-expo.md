# OkCupid (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates OkCupid's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#FFFFFF',
  surface:  '#F2F2F2',
  divider:  '#E5E5E5',

  textPrimary:   '#14171A',
  textSecondary: '#6B6B6B',
  textTertiary:  '#9B9B9B',

  magenta:        '#E2024F',
  magentaPressed: '#C00244',
  magentaTint:    '#FCE3EB',
  indigo:         '#0500FF',
  indigoTint:     '#E5E4FF',

  matchHigh: '#E2024F',
  matchMid:  '#F0578A',
  matchLow:  '#9B9B9B',

  success: '#1CAA6A',
  warning: '#F5A623',
  error:   '#D0021B',
} as const;

export type OkcColor = keyof typeof colors;
```

## 2. Typography

Larsseit is OkCupid's licensed brand typeface. Load it via `expo-font`, or use **Inter** (friendly grotesque — avoid hard industrial sans). Fall back to `System`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Larsseit-Regular':  require('../assets/fonts/Larsseit-Regular.ttf'),
    'Larsseit-Semibold': require('../assets/fonts/Larsseit-Medium.ttf'),
    'Larsseit-Bold':     require('../assets/fonts/Larsseit-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#14171A' } satisfies TextStyle;

export const typography = {
  titleLarge:  { ...ink, fontFamily: 'Larsseit-Bold',     fontSize: 30, lineHeight: 35, letterSpacing: -0.4 },
  profileName: { ...ink, fontFamily: 'Larsseit-Bold',     fontSize: 26, lineHeight: 30, letterSpacing: -0.3 },
  section:     { ...ink, fontFamily: 'Larsseit-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  matchPct:    {          fontFamily: 'Larsseit-Bold',     fontSize: 20, lineHeight: 20 },
  question:    { ...ink, fontFamily: 'Larsseit-Semibold', fontSize: 20, lineHeight: 26, letterSpacing: -0.1 },
  cardTitle:   { ...ink, fontFamily: 'Larsseit-Semibold', fontSize: 17, lineHeight: 22, letterSpacing: -0.1 },
  body:        { ...ink, fontFamily: 'Larsseit-Regular',  fontSize: 16, lineHeight: 24 },
  answer:      { ...ink, fontFamily: 'Larsseit-Semibold', fontSize: 16, lineHeight: 21 },
  meta:        {          fontFamily: 'Larsseit-Regular',  fontSize: 14, lineHeight: 19, color: '#6B6B6B' },
  caption:     {          fontFamily: 'Larsseit-Regular',  fontSize: 13, lineHeight: 17, color: '#6B6B6B' },
  labelUpper:  {          fontFamily: 'Larsseit-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.6, color: '#6B6B6B', textTransform: 'uppercase' as const },
  button:      { color: '#FFFFFF', fontFamily: 'Larsseit-Bold', fontSize: 17, lineHeight: 21, letterSpacing: 0.2 },
  tab:         {          fontFamily: 'Larsseit-Semibold', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Match-% Circular Badge

```tsx
// components/MatchBadge.tsx
import { Text, View } from 'react-native';
import { useEffect, useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MatchBadge({ percent, diameter = 48 }: { percent: number; diameter?: number }) {
  const tier = percent >= 90 ? colors.matchHigh : percent >= 70 ? colors.matchMid : colors.matchLow;
  const [display, setDisplay] = useState(0);

  useEffect(() => {
    let raf: number; const start = Date.now(); const dur = 600;
    const tick = () => {
      const t = Math.min(1, (Date.now() - start) / dur);
      setDisplay(Math.round(t * percent));
      if (t < 1) raf = requestAnimationFrame(tick);
    };
    raf = requestAnimationFrame(tick);
    return () => cancelAnimationFrame(raf);
  }, [percent]);

  return (
    <View style={{
      width: diameter, height: diameter, borderRadius: diameter / 2,
      backgroundColor: 'rgba(255,255,255,0.96)',
      borderWidth: 3, borderColor: tier,
      alignItems: 'center', justifyContent: 'center',
    }}>
      <Text style={[typography.matchPct, { color: tier }]}>{display}%</Text>
    </View>
  );
}
```

### Like Button (Magenta Heart)

```tsx
// components/LikeButton.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withSequence } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function LikeButton({
  liked, size = 64, onPress,
}: { liked: boolean; size?: number; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.90, { damping: 12 }))}
      onPressOut={() => (scale.value = withSequence(withSpring(1.2), withSpring(1)))}
      onPress={() => {
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
        onPress();
      }}
    >
      <Animated.View
        style={[
          {
            width: size, height: size, borderRadius: size / 2,
            backgroundColor: liked ? colors.magentaTint : '#FFFFFF',
            borderWidth: 1, borderColor: colors.divider,
            alignItems: 'center', justifyContent: 'center',
            shadowColor: colors.textPrimary, shadowOpacity: 0.16,
            shadowRadius: 14, shadowOffset: { width: 0, height: 4 }, elevation: 6,
          },
          style,
        ]}
      >
        <Ionicons name={liked ? 'heart' : 'heart-outline'} size={size * 0.44} color={colors.magenta} />
      </Animated.View>
    </Pressable>
  );
}
```

### Pass Button (Indigo X)

```tsx
// components/PassButton.tsx
import { Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PassButton({ size = 56, onPress }: { size?: number; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        width: size, height: size, borderRadius: size / 2,
        backgroundColor: '#FFFFFF', borderWidth: 1, borderColor: colors.divider,
        alignItems: 'center', justifyContent: 'center',
        transform: [{ scale: pressed ? 0.90 : 1 }],
        shadowColor: colors.textPrimary, shadowOpacity: 0.16,
        shadowRadius: 14, shadowOffset: { width: 0, height: 4 }, elevation: 6,
      })}
    >
      <Ionicons name="close" size={size * 0.42} color={colors.indigo} />
    </Pressable>
  );
}
```

### Primary Pill

```tsx
// components/PrimaryPill.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryPill({
  title, enabled = true, onPress,
}: { title: string; enabled?: boolean; onPress: () => void }) {
  return (
    <Pressable
      disabled={!enabled}
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 15, paddingHorizontal: 32,
        borderRadius: 500, alignItems: 'center',
        backgroundColor: pressed ? colors.magentaPressed : colors.magenta,
        opacity: enabled ? 1 : 0.35,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Match-Question Card

```tsx
// components/QuestionCard.tsx
import { Pressable, Text, View } from 'react-native';
import { useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { PrimaryPill } from './PrimaryPill';

export function QuestionCard({
  prompt, options, onAnswer,
}: { prompt: string; options: string[]; onAnswer: () => void }) {
  const [selected, setSelected] = useState<number | null>(null);
  const [importance, setImportance] = useState(1);
  const IMP = ['A little', 'Somewhat', 'Very'];

  return (
    <View style={{
      backgroundColor: '#FFFFFF', borderRadius: 20, padding: 20, gap: 16,
      shadowColor: colors.textPrimary, shadowOpacity: 0.06,
      shadowRadius: 8, shadowOffset: { width: 0, height: 2 }, elevation: 2,
    }}>
      <Text style={typography.question}>{prompt}</Text>
      <View style={{ gap: 12 }}>
        {options.map((o, i) => (
          <Pressable key={i} onPress={() => setSelected(i)}
            style={{
              paddingVertical: 16, borderRadius: 500, alignItems: 'center',
              backgroundColor: selected === i ? colors.magenta : '#FFFFFF',
              borderWidth: selected === i ? 0 : 1.5, borderColor: colors.divider,
            }}>
            <Text style={[typography.answer, { color: selected === i ? '#FFF' : colors.textPrimary }]}>
              {o}
            </Text>
          </Pressable>
        ))}
      </View>
      <View style={{ flexDirection: 'row', borderRadius: 500, overflow: 'hidden', borderWidth: 1.5, borderColor: colors.divider }}>
        {IMP.map((label, i) => (
          <Pressable key={i} onPress={() => setImportance(i)}
            style={{ flex: 1, paddingVertical: 10, alignItems: 'center',
              backgroundColor: importance === i ? colors.magenta : colors.surface }}>
            <Text style={{ ...typography.caption, fontWeight: '600',
              color: importance === i ? '#FFF' : colors.textSecondary }}>{label}</Text>
          </Pressable>
        ))}
      </View>
      <PrimaryPill title="Answer" enabled={selected !== null} onPress={onAnswer} />
    </View>
  );
}
```

## 4. DoubleTake Profile Card

```tsx
// components/ProfileCard.tsx
import { Image, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { MatchBadge } from './MatchBadge';

export function ProfileCard({
  name, age, percent, heroUri,
}: { name: string; age: number; percent: number; heroUri: string }) {
  return (
    <View style={{
      borderRadius: 24, overflow: 'hidden', marginHorizontal: 8,
      shadowColor: colors.textPrimary, shadowOpacity: 0.12,
      shadowRadius: 24, shadowOffset: { width: 0, height: 8 }, elevation: 8,
    }}>
      <Image source={{ uri: heroUri }} style={{ width: '100%', aspectRatio: 4 / 5 }} />
      <LinearGradient colors={['transparent', 'rgba(20,23,26,0.45)']}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '40%' }} />
      <View style={{ position: 'absolute', left: 16, right: 16, bottom: 16,
        flexDirection: 'row', alignItems: 'flex-end', justifyContent: 'space-between' }}>
        <Text style={[typography.profileName, { color: '#FFF' }]}>{name}, {age}</Text>
        <MatchBadge percent={percent} />
      </View>
    </View>
  );
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.magenta,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: 'rgba(255,255,255,0.96)', borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontFamily: 'Larsseit-Semibold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="copy"          size={26} color={color} /> }} />
      <Tabs.Screen name="likes"    options={{ title: 'Likes',    tabBarIcon: ({ color }) => <Ionicons name="heart"         size={26} color={color} /> }} />
      <Tabs.Screen name="matches"  options={{ title: 'Matches',  tabBarIcon: ({ color }) => <Ionicons name="sparkles"      size={26} color={color} /> }} />
      <Tabs.Screen name="messages" options={{ title: 'Messages', tabBarIcon: ({ color }) => <Ionicons name="chatbubble"    size={26} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person"        size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Like — success haptic + scale spring + heart bounce (see LikeButton)
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Pass — soft impact, then slide the card out
const x = useSharedValue(0);
const passOut = () => { x.value = withTiming(-screenW, { duration: 300 }); };

// Match-% count-up — requestAnimationFrame loop in MatchBadge (0 → value over 600ms)

// It's a Match — full-screen scale-in
const m = useSharedValue(0.85);
useEffect(() => { m.value = withSpring(1, { damping: 14 }); }, []);
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to OkCupid's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Like | `heart-outline` / `heart` |
| Pass | `close` |
| Star / Boost | `star` |
| Match (tab) | `sparkles` |
| Message send | `arrow-up-circle` |
| Filter | `options` |
| Settings | `settings-sharp` |
| Verified | `checkmark-circle` |
| Discover (tab) | `copy` |
| Likes (tab) | `heart` |
| Messages (tab) | `chatbubble` |
| Profile (tab) | `person` |
| Question | `chatbox-ellipses` |

## 8. Platform Notes

- **Status bar**: set `<StatusBar style="dark" />` from `expo-status-bar` for the bright canvas; switch to `light` under system dark mode
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the DoubleTake action bar floats above the tab bar inside safe area
- **Friendly substitute font**: if Larsseit/Inter is unavailable, prefer a rounded family — the brand voice is warm, never industrial
- **Card gestures**: build the DoubleTake swipe with `react-native-gesture-handler` + Reanimated; spring snap-back on incomplete drags, commit + slide on Like/Pass
- **Dynamic Type**: React Native honors font scaling; set `allowFontScaling={false}` on the match-% badge number and tab labels (fixed ornaments)
- **Accessibility**: `accessibilityRole="button"` on Like ("Like {name}"), Pass ("Pass"); announce the badge as "{n} percent match"; group question prompt + options with `accessible`
