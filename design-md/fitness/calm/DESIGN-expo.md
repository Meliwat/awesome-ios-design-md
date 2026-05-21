# Calm (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Calm's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  gradientTop:    '#2A6FD6',
  gradientMid:    '#1A4A8F',
  gradientBottom: '#0B1E3F',
  scrimNavy:      '#091830',

  glass:        'rgba(255,255,255,0.08)',
  glassRaised:  'rgba(255,255,255,0.12)',
  glassDivider: 'rgba(255,255,255,0.12)',
  glassHover:   'rgba(255,255,255,0.16)',

  textPrimary:   '#FFFFFF',
  textSecondary: '#B8C4D8',
  textTertiary:  '#8794A8',

  calmBlue:        '#2A6FD6',
  calmBluePressed: '#1F57AB',
  calmBlueSoft:    '#4A85E0',
  streakGold:      '#F2C94C',
  gentleError:     '#E08A8A',
  success:         '#7FB8A0',
} as const;

export const skyStops = ['#2A6FD6', '#1A4A8F', '#0B1E3F'] as const;
export const skyLocations = [0, 0.45, 1] as const;
export type CalmColor = keyof typeof colors;
```

## 2. Typography

Load Lora (serif) and Inter (sans) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Lora-Regular':   require('../assets/fonts/Lora-Regular.ttf'),
    'Lora-SemiBold':  require('../assets/fonts/Lora-SemiBold.ttf'),
    'Inter-Light':    require('../assets/fonts/Inter-Light.ttf'),
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  // Lora — serif, the brand voice
  greeting:    { ...white, fontFamily: 'Lora-SemiBold', fontSize: 30, lineHeight: 38, letterSpacing: -0.2 },
  screenTitle: { ...white, fontFamily: 'Lora-SemiBold', fontSize: 26, lineHeight: 34, letterSpacing: -0.2 },
  dailyTitle:  { ...white, fontFamily: 'Lora-SemiBold', fontSize: 24, lineHeight: 31, letterSpacing: -0.1 },
  cardTitle:   { ...white, fontFamily: 'Lora-SemiBold', fontSize: 19, lineHeight: 26 },
  breatheCue:  {           fontFamily: 'Lora-Regular',  fontSize: 22, letterSpacing: 0.5, color: '#0B1E3F' },

  // Inter — sans, supporting
  section:     { ...white, fontFamily: 'Inter-Bold',     fontSize: 16, letterSpacing: 0.2 },
  rowTitle:    { ...white, fontFamily: 'Inter-SemiBold', fontSize: 17, lineHeight: 23 },
  body:        { ...white, fontFamily: 'Inter-Regular',  fontSize: 17, lineHeight: 26 },
  subtitle:    {           fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 21, color: '#B8C4D8' },
  button:      { ...white, fontFamily: 'Inter-SemiBold', fontSize: 17, letterSpacing: 0.1 },
  meta:        {           fontFamily: 'Inter-Medium',   fontSize: 13, letterSpacing: 0.2, color: '#B8C4D8' },
  tab:         {           fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
  timer:       { ...white, fontFamily: 'Inter-Light',    fontSize: 44 },
  labelUpper:  { ...white, fontFamily: 'Inter-Bold',     fontSize: 12, letterSpacing: 1, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Night-Sky Background

```tsx
// components/CalmBackground.tsx
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet, View } from 'react-native';
import { skyStops, skyLocations } from '../theme/colors';

export function CalmBackground({ children }: { children: React.ReactNode }) {
  return (
    <View style={{ flex: 1 }}>
      <LinearGradient
        colors={skyStops as unknown as string[]}
        locations={skyLocations as unknown as number[]}
        style={StyleSheet.absoluteFill}
      />
      {children}
    </View>
  );
}
```

### Breathe Bubble (signature)

```tsx
// components/BreatheBubble.tsx
import { useEffect, useState } from 'react';
import { View, Text } from 'react-native';
import Animated, {
  useSharedValue, useAnimatedStyle, withTiming, withSequence, withRepeat, Easing,
} from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BreatheBubble() {
  const size = useSharedValue(120);
  const [cue, setCue] = useState('Breathe in');

  useEffect(() => {
    size.value = withRepeat(
      withSequence(
        withTiming(220, { duration: 4000, easing: Easing.in(Easing.ease) }),  // inhale
        withTiming(220, { duration: 7000 }),                                   // hold
        withTiming(120, { duration: 8000, easing: Easing.out(Easing.ease) }),  // exhale
      ),
      -1,
    );
    const phases = [
      [0, 'Breathe in'], [4000, 'Hold'], [11000, 'Breathe out'],
    ] as const;
    const timers = phases.map(([t, label]) =>
      setInterval(() => {}, 1) && setTimeoutLoop(t, () => setCue(label as string)));
    return () => timers.forEach(clearInterval);
  }, []);

  const bubbleStyle = useAnimatedStyle(() => ({
    width: size.value, height: size.value, borderRadius: size.value / 2,
  }));

  return (
    <View style={{ alignItems: 'center', justifyContent: 'center' }}>
      <Animated.View
        style={[
          { backgroundColor: 'rgba(255,255,255,0.9)',
            shadowColor: colors.calmBlueSoft, shadowOpacity: 0.6, shadowRadius: 40,
            alignItems: 'center', justifyContent: 'center' },
          bubbleStyle,
        ]}
      >
        <Text style={typography.breatheCue}>{cue}</Text>
      </Animated.View>
    </View>
  );
}

// helper: re-arm a phased label loop every 19s
function setTimeoutLoop(offset: number, fn: () => void) {
  const id = setInterval(() => {
    const ms = Date.now() % 19000;
    if (Math.abs(ms - offset) < 60) fn();
  }, 50);
  return id;
}
```

### Primary Play Pill

```tsx
// components/PlayPill.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PlayPill({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 10,
        paddingVertical: 16, paddingHorizontal: 40, borderRadius: 999,
        backgroundColor: pressed ? colors.calmBluePressed : colors.calmBlue,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Ionicons name="play" size={16} color="#FFF" />
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Daily Calm Hero Card (signature)

```tsx
// components/DailyCalmHero.tsx
import { ImageBackground, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function DailyCalmHero({ title, subtitle, photo, onPlay }: {
  title: string; subtitle: string; photo: any; onPlay: () => void;
}) {
  return (
    <ImageBackground
      source={photo}
      style={{ height: 220, borderRadius: 20, overflow: 'hidden' }}
    >
      <LinearGradient
        colors={['transparent', 'rgba(9,24,48,0.85)']}
        style={{ flex: 1, justifyContent: 'flex-end' }}
      >
        <View style={{ flexDirection: 'row', alignItems: 'flex-end', padding: 20 }}>
          <View style={{ flex: 1, gap: 6 }}>
            <Text style={typography.labelUpper}>Daily Calm</Text>
            <Text style={typography.dailyTitle}>{title}</Text>
            <Text style={typography.subtitle}>{subtitle}</Text>
          </View>
          <Pressable
            onPress={onPlay}
            style={{ width: 44, height: 44, borderRadius: 22,
                     backgroundColor: colors.glassHover,
                     alignItems: 'center', justifyContent: 'center' }}
          >
            <Ionicons name="play" size={18} color="#FFF" />
          </Pressable>
        </View>
      </LinearGradient>
    </ImageBackground>
  );
}
```

### Sleep Story List Row

```tsx
// components/SleepStoryRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SleepStoryRow({ title, narrator, duration, thumb, onPress }: {
  title: string; narrator: string; duration: string; thumb: any; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 14,
        height: 76, paddingHorizontal: 20,
        backgroundColor: pressed ? colors.glassRaised : 'transparent',
        borderBottomWidth: 1, borderBottomColor: colors.glassDivider,
      })}
    >
      <Image source={thumb} style={{ width: 56, height: 56, borderRadius: 12 }} />
      <View style={{ flex: 1, gap: 4 }}>
        <Text style={typography.rowTitle}>{title}</Text>
        <Text style={typography.subtitle}>Narrated by {narrator} · {duration}</Text>
      </View>
      <Ionicons name="download-outline" size={22} color="rgba(255,255,255,0.8)" />
    </Pressable>
  );
}
```

### Glass Card

```tsx
// components/GlassCard.tsx
import { BlurView } from 'expo-blur';
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function GlassCard({ children }: { children: React.ReactNode }) {
  return (
    <BlurView intensity={28} tint="dark"
      style={{ borderRadius: 20, overflow: 'hidden',
               borderWidth: 1, borderColor: colors.glassDivider }}>
      <View style={{ backgroundColor: colors.glass, padding: 20 }}>{children}</View>
    </BlurView>
  );
}
```

## 4. Tab Bar

`expo-router` Tabs with a deep-navy `BlurView` background. **Active tint is Calm Blue.**

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.calmBlue,
        tabBarInactiveTintColor: 'rgba(255,255,255,0.6)',
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={50} tint="dark"
            style={{ flex: 1, backgroundColor: 'rgba(11,30,63,0.72)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"        size={24} color={color} /> }} />
      <Tabs.Screen name="sleep"    options={{ title: 'Sleep',    tabBarIcon: ({ color }) => <Ionicons name="moon"        size={24} color={color} /> }} />
      <Tabs.Screen name="meditate" options={{ title: 'Meditate', tabBarIcon: ({ color }) => <Ionicons name="leaf"        size={24} color={color} /> }} />
      <Tabs.Screen name="music"    options={{ title: 'Music',    tabBarIcon: ({ color }) => <Ionicons name="musical-notes" size={24} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',     tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Breathe bubble: withSequence(in 4s ease-in → hold 7s → out 8s ease-out), withRepeat(-1)

// Card tap: scale 0.97 via Pressable style, ~250ms (use withTiming for smoothness)

// Screen transitions — Calm fades
// expo-router: set animation: 'fade' on the Stack screen options

// Session timer ring
progress.value = withTiming(1, { duration: sessionMs, easing: Easing.linear });

// Session complete — soft sage check fades in over 600ms
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

Haptics via `expo-haptics`: `impactAsync(Soft)` on play, `notificationAsync(Success)` on session complete, `selectionAsync()` on mood-chip pick. Keep them gentle and rare.

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Calm's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Play | `play.fill` | `play` |
| Pause | `pause.fill` | `pause` |
| Favorite | `heart` / `heart.fill` | `heart-outline` / `heart` |
| Download | `arrow.down.circle` | `download-outline` |
| Share | `square.and.arrow.up` | `share-outline` |
| Background sound | `slider.horizontal.3` | `options-outline` |
| Timer | `timer` | `timer-outline` |
| Search | `magnifyingglass` | `search` |
| Streak | `flame.fill` | `flame` |
| Settings | `gearshape` | `settings-outline` |
| Home (tab) | `house.fill` | `home` |
| Sleep (tab) | `moon.stars.fill` | `moon` |
| Meditate (tab) | `figure.mind.and.body` | `leaf` |
| Music (tab) | `music.note` | `musical-notes` |
| More (tab) | `ellipsis` | `ellipsis-horizontal` |

## 7. Platform Notes

- **Gradient canvas**: every screen wraps in `CalmBackground`; set `<StatusBar style="light" />` from `expo-status-bar` globally
- **Glass requires blur**: `expo-blur` `BlurView` gives the iOS `.regularMaterial` feel; Android falls back to a semi-opaque `#13294B` — branch on `Platform.OS` and bump opacity on Android so cards stay legible
- **Safe area**: wrap content in `SafeAreaView` from `react-native-safe-area-context`; the gradient itself runs full-bleed (`StyleSheet.absoluteFill`), only content insets
- **Reanimated for breath**: the breathe bubble must run on the UI thread (`react-native-reanimated`) so the 19s cycle stays smooth under load
- **Dynamic Type**: RN honors font scaling — keep it on greetings/titles/body; set `allowFontScaling={false}` on tab labels, duration meta, and cap the timer
- **Accessibility**: mark the breathe bubble `accessibilityElementsHidden` and expose phase changes via an `AccessibilityInfo.announceForAccessibility` call; on Reduce Motion render a static circle + text cue; on Reduce Transparency swap glass for solid navy
- **Dark-only**: do not branch on `useColorScheme()` — Calm is a night-sky product
