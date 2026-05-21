# Shazam (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Shazam's visual language into paste-ready Expo / React Native code: a design-token module, the radial gradient, the signature pulsing button + concentric rings, the result card, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `expo-blur`, and `expo-linear-gradient` (with `react-native-svg` for the radial gradient).

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Gradient stops
  core:  '#0050FF',
  blue:  '#0088FF',
  space: '#08090E',
  bluePressed: '#006FE0',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#B8C4FF',                 // periwinkle, tuned to the gradient
  textTertiary:  'rgba(184,196,255,0.55)',

  // Glass
  glass:       'rgba(255,255,255,0.08)',
  glassStrong: 'rgba(255,255,255,0.14)',
  divider:     'rgba(255,255,255,0.12)',

  // Semantic
  appleMusicPink: '#FA243C',
  errorRed:       '#FF453A',
} as const;

export type ShazamColor = keyof typeof colors;
```

## 2. Typography

Load Montserrat via `expo-font`; fall back to `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Montserrat-Medium': require('../assets/fonts/Montserrat-Medium.ttf'),
    'Montserrat-Bold':   require('../assets/fonts/Montserrat-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#08090E' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  resultLarge: { ...base, fontFamily: 'Montserrat-Bold',   fontSize: 28, lineHeight: 32, letterSpacing: -0.3 },
  result:      { ...base, fontFamily: 'Montserrat-Bold',   fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  section:     { ...base, fontFamily: 'Montserrat-Bold',   fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  prompt:      { ...base, fontFamily: 'Montserrat-Bold',   fontSize: 18, lineHeight: 22, letterSpacing: 0.2 },
  cardTitle:   { ...base, fontFamily: 'Montserrat-Bold',   fontSize: 16, lineHeight: 21 },
  subtitle:    {          fontFamily: 'Montserrat-Medium', fontSize: 14, lineHeight: 18, color: '#B8C4FF' },
  body:        { ...base, fontFamily: 'Montserrat-Medium', fontSize: 15, lineHeight: 22 },
  meta:        {          fontFamily: 'Montserrat-Medium', fontSize: 13, lineHeight: 17, color: '#B8C4FF' },
  labelUpper:  { ...base, fontFamily: 'Montserrat-Bold',   fontSize: 11, lineHeight: 13, letterSpacing: 1.0, textTransform: 'uppercase' as const },
  button:      { color: '#08090E', fontFamily: 'Montserrat-Bold', fontSize: 16, lineHeight: 20, letterSpacing: 0.3 },
  buttonSec:   { ...base, fontFamily: 'Montserrat-Bold',   fontSize: 14, lineHeight: 18, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Radial Hero Gradient

`expo-linear-gradient` is linear-only; use `react-native-svg` for the radial:

```tsx
// components/HeroGradient.tsx
import Svg, { Defs, RadialGradient, Stop, Rect } from 'react-native-svg';
import { StyleSheet } from 'react-native';

export function HeroGradient() {
  return (
    <Svg style={StyleSheet.absoluteFill}>
      <Defs>
        <RadialGradient id="shz" cx="50%" cy="42%" r="75%">
          <Stop offset="0%"   stopColor="#0088FF" />
          <Stop offset="30%"  stopColor="#0050FF" />
          <Stop offset="100%" stopColor="#08090E" />
        </RadialGradient>
      </Defs>
      <Rect width="100%" height="100%" fill="url(#shz)" />
    </Svg>
  );
}
```

### The Pulsing Shazam Button + Concentric Rings

```tsx
// components/ShazamButton.tsx
import { useEffect } from 'react';
import { Pressable, View, StyleSheet } from 'react-native';
import Animated, {
  useSharedValue, useAnimatedStyle, withRepeat, withTiming, withSpring, withDelay, Easing,
} from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Svg, { Path } from 'react-native-svg';
import { colors } from '../theme/colors';

const SIZE = 132;

function Ring({ delay }: { delay: number }) {
  const p = useSharedValue(0);
  useEffect(() => {
    p.value = withDelay(delay, withRepeat(withTiming(1, { duration: 1800, easing: Easing.out(Easing.ease) }), -1, false));
  }, []);
  const style = useAnimatedStyle(() => ({
    transform: [{ scale: 1 + p.value * 1.6 }],
    opacity: 0.22 * (1 - p.value),
  }));
  return <Animated.View style={[styles.ring, style]} />;
}

export function ShazamButton({ isListening, onTap }: { isListening: boolean; onTap: () => void }) {
  const breathe = useSharedValue(1);
  const press = useSharedValue(1);
  const glow = useSharedValue(0);

  useEffect(() => {
    breathe.value = withRepeat(withTiming(1.04, { duration: 2400, easing: Easing.inOut(Easing.ease) }), -1, true);
  }, []);
  useEffect(() => {
    glow.value = withTiming(isListening ? 1 : 0, { duration: 500 });
  }, [isListening]);

  const btnStyle = useAnimatedStyle(() => ({
    transform: [{ scale: press.value * (isListening ? 1 : breathe.value) }],
  }));
  const glowStyle = useAnimatedStyle(() => ({ opacity: glow.value * 0.5 }));

  return (
    <View style={styles.wrap}>
      {isListening && <><Ring delay={0} /><Ring delay={600} /><Ring delay={1200} /></>}
      <Animated.View style={[styles.glow, glowStyle]} />
      <Pressable
        onPressIn={() => (press.value = withSpring(0.94, { damping: 16 }))}
        onPressOut={() => (press.value = withSpring(1, { damping: 16 }))}
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy); onTap(); }}
      >
        <Animated.View style={[styles.btn, btnStyle]}>
          <Svg width={SIZE * 0.42} height={SIZE * 0.42} viewBox="0 0 100 100">
            <Path
              d="M62 18 C40 18 30 30 30 46 C30 62 70 50 70 62 C70 78 58 86 38 86"
              stroke={colors.blue} strokeWidth={13} strokeLinecap="round" fill="none"
            />
          </Svg>
        </Animated.View>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  wrap: { width: SIZE, height: SIZE, alignItems: 'center', justifyContent: 'center' },
  ring: {
    position: 'absolute', width: SIZE, height: SIZE, borderRadius: SIZE / 2,
    borderWidth: 2, borderColor: 'rgba(255,255,255,0.22)',
  },
  glow: {
    position: 'absolute', width: SIZE * 1.4, height: SIZE * 1.4, borderRadius: SIZE,
    backgroundColor: colors.blue, // soft via low opacity (RN has no cheap blur here)
  },
  btn: {
    width: SIZE, height: SIZE, borderRadius: SIZE / 2,
    backgroundColor: '#EAF1FF', borderWidth: 1, borderColor: 'rgba(255,255,255,0.4)',
    alignItems: 'center', justifyContent: 'center',
    shadowColor: '#0050FF', shadowOpacity: 0.45, shadowRadius: 48, shadowOffset: { width: 0, height: 16 },
  },
});
```

### Hero Screen (no tab bar)

```tsx
// app/index.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import { SafeAreaView } from 'react-native-safe-area-context';
import Ionicons from '@expo/vector-icons/Ionicons';
import { HeroGradient } from '../components/HeroGradient';
import { ShazamButton } from '../components/ShazamButton';
import { typography } from '../theme/typography';

export default function Home() {
  const [listening, setListening] = useState(false);
  return (
    <View style={{ flex: 1, backgroundColor: '#08090E' }}>
      <HeroGradient />
      <SafeAreaView style={{ flex: 1 }}>
        <View style={{ flexDirection: 'row', justifyContent: 'space-between', paddingHorizontal: 20 }}>
          <Ionicons name="person-circle-outline" size={28} color="#FFF" />
          <Ionicons name="list" size={28} color="#FFF" />
        </View>
        <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
          <ShazamButton isListening={listening} onTap={() => setListening(v => !v)} />
          <Text style={[listening ? typography.button : typography.prompt, { color: '#FFF', marginTop: 24 }]}>
            {listening ? 'Listening for music…' : 'Tap to Shazam'}
          </Text>
        </View>
      </SafeAreaView>
    </View>
  );
}
```

### Result Card

```tsx
// components/ResultCard.tsx
import { View, Text, Image, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { FadeIn } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ResultCard({ title, artist, artworkUri }: { title: string; artist: string; artworkUri: string }) {
  return (
    <Animated.View entering={FadeIn.duration(450)} style={{
      marginHorizontal: 20, padding: 20, borderRadius: 20,
      backgroundColor: colors.glass, borderWidth: 1, borderColor: colors.glassStrong, gap: 20,
    }}>
      <View style={{ flexDirection: 'row', gap: 16 }}>
        <Image source={{ uri: artworkUri }} style={{ width: 88, height: 88, borderRadius: 12 }} />
        <View style={{ flex: 1, gap: 6, justifyContent: 'center' }}>
          <Text style={typography.result} numberOfLines={2}>{title}</Text>
          <Text style={typography.subtitle} numberOfLines={1}>{artist}</Text>
        </View>
      </View>

      <Pressable style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8,
        paddingVertical: 14, borderRadius: 500, backgroundColor: pressed ? '#E8F0FF' : '#FFF',
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}>
        <Ionicons name="musical-note" size={18} color={colors.appleMusicPink} />
        <Text style={typography.button}>Open in Apple Music</Text>
      </Pressable>

      <View style={{ flexDirection: 'row', gap: 12 }}>
        {[['share-outline', 'Share'], ['add', 'Add'], ['document-text-outline', 'Lyrics']].map(([icon, label]) => (
          <View key={label} style={{
            flex: 1, alignItems: 'center', gap: 6, paddingVertical: 12, borderRadius: 500,
            backgroundColor: colors.glass, borderWidth: 1, borderColor: 'rgba(255,255,255,0.16)',
          }}>
            <Ionicons name={icon as any} size={18} color="#FFF" />
            <Text style={typography.buttonSec}>{label}</Text>
          </View>
        ))}
      </View>
    </Animated.View>
  );
}
```

## 4. Listening State Machine

```ts
export type ShazamState =
  | { kind: 'idle' }
  | { kind: 'listening' }
  | { kind: 'matched'; title: string; artist: string }
  | { kind: 'noMatch' };

// On tap → 'listening'; on recognizer result → 'matched' (reveal card);
// on failure → 'noMatch' (soft shake + Haptics.notificationAsync(Error)).
```

## 5. Navigation — No Tab Bar

Shazam deliberately uses **no `Tabs`**. The hero is the root route; the library/history is a modal sheet:

```tsx
// app/_layout.tsx
import { Stack } from 'expo-router';
export default function Layout() {
  return (
    <Stack screenOptions={{ headerShown: false, contentStyle: { backgroundColor: '#08090E' } }}>
      <Stack.Screen name="index" />
      <Stack.Screen name="library" options={{ presentation: 'modal' }} />
    </Stack>
  );
}
```

The library screen renders a `BlurView` (`expo-blur`, `intensity={40}`) with `rgba(255,255,255,0.14)` tint, a 24pt-radius top, and a 36×4 grab handle.

## 6. Motion

```tsx
// Idle breathing: withRepeat(withTiming(1.04, 2400ms ease-in-out), -1, true)

// Concentric rings: scale 1 → 2.6, opacity 0.22 → 0 over 1800ms ease-out, staggered withDelay 600ms

// Tap: press scale 0.94 withSpring + Haptics.impactAsync(Heavy)

// Result reveal: Animated entering={FadeIn.duration(450)} (combine with a scale-from-0.85 via withSpring)

// No match: single horizontal shake + Haptics.notificationAsync(Haptics.NotificationFeedbackType.Error)
const shake = useSharedValue(0);
const noMatch = () => {
  shake.value = withSequence(withTiming(-8, { duration: 60 }), withTiming(8, { duration: 60 }), withTiming(0, { duration: 60 }));
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Error);
};
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Shazam's SF Symbol equivalents (the Shazam mark itself ships as a licensed SVG asset):

| Purpose | Ionicons |
|---------|----------|
| Profile / Settings | `person-circle-outline` |
| Library / History | `list` |
| Open in Apple Music | `musical-note` |
| Share | `share-outline` |
| Add | `add` |
| Lyrics | `document-text-outline` |
| Close result | `close` |
| Sheet chevron | `chevron-forward` |
| Search (library) | `search` |
| Play / Pause (mini) | `play` / `pause` |
| No-match | `radio-outline` |

## 8. Platform Notes

- **Dark-only hero**: Shazam's hero is dark by design. Force the `#08090E` background on the navigator; never read `useColorScheme()` for the hero.
- **Radial gradient**: `expo-linear-gradient` cannot do radial — use `react-native-svg`'s `RadialGradient` (shown above).
- **Status bar**: `<StatusBar style="light" />` globally from `expo-status-bar`.
- **Glow without blur**: RN has no cheap runtime blur for the listening glow — approximate with a low-opacity colored circle, or use `expo-blur` behind a tinted overlay if a true bloom is required.
- **No tab bar**: do not add `expo-router` `Tabs` — the single-screen hero is the defining choice. Library is a `presentation: 'modal'` route.
- **Safe area**: wrap the hero in `SafeAreaView` from `react-native-safe-area-context`; the gradient extends full-bleed under it.
- **Reduce Motion**: gate the ring emission behind `AccessibilityInfo.isReduceMotionEnabled()` — fall back to a static glow.
- **Accessibility**: give the button `accessibilityRole="button"` and `accessibilityLabel="Shazam. Tap to identify the music playing around you."`; announce state changes with `AccessibilityInfo.announceForAccessibility`.
