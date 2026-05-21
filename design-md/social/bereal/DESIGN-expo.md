# BeReal (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates BeReal's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#000000',
  surface1: '#1C1C1E',
  surface2: '#2C2C2E',
  divider:  '#2C2C2E',

  textPrimary:   '#FFFFFF',
  textSecondary: '#8E8E93',
  textTertiary:  '#636366',

  accent:    '#FFFFFF',  // no brand hue — white is the verb
  onAccent:  '#000000',

  late:    '#FFD60A',
  error:   '#FF453A',
  online:  '#30D158',
} as const;

export type BeRealColor = keyof typeof colors;
```

## 2. Typography

BeReal uses the system font (SF Pro on iOS) — no custom face to load. Use `System` and enable tabular figures on the countdown via `fontVariant`.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  timer:        { ...base, fontSize: 24, lineHeight: 26, fontWeight: '700',
                  fontVariant: ['tabular-nums'] as const },
  screenTitle:  { ...base, fontSize: 22, lineHeight: 26, fontWeight: '700', letterSpacing: -0.3 },
  username:     { ...base, fontSize: 17, lineHeight: 20, fontWeight: '600', letterSpacing: -0.2 },
  caption:      { ...base, fontSize: 16, lineHeight: 22, fontWeight: '400' },
  body:         { ...base, fontSize: 15, lineHeight: 21, fontWeight: '400' },
  commentAuthor:{ ...base, fontSize: 14, lineHeight: 18, fontWeight: '600' },
  meta:         {           fontSize: 13, lineHeight: 17, fontWeight: '400', color: '#8E8E93' },
  lateBadge:    {           fontSize: 12, lineHeight: 14, fontWeight: '700', color: '#FFD60A', letterSpacing: 0.2 },
  button:       {           fontSize: 17, lineHeight: 20, fontWeight: '600', letterSpacing: -0.2 },
  tab:          {           fontSize: 10, lineHeight: 12, fontWeight: '600' },
  retake:       { ...base, fontSize: 11, lineHeight: 13, fontWeight: '600' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Dual-Lens Composite Card (the signature)

```tsx
// components/DualLensCard.tsx
import { Image, View } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';

const CORNERS = [
  { top: 12, left: 12 }, { top: 12, right: 12 },
  { bottom: 12, left: 12 }, { bottom: 12, right: 12 },
] as const;

export function DualLensCard({ backUri, frontUri }: { backUri: string; frontUri: string }) {
  const lift = useSharedValue(1);
  const corner = useSharedValue(0);

  const longPress = Gesture.LongPress().minDuration(200)
    .onStart(() => (lift.value = withSpring(1.05)));
  const drag = Gesture.Pan()
    .onEnd(() => {
      lift.value = withSpring(1);
      corner.value = Math.floor(Math.random() * 4);
    });
  const gesture = Gesture.Simultaneous(longPress, drag);

  const selfieStyle = useAnimatedStyle(() => ({
    transform: [{ scale: lift.value }],
    ...CORNERS[corner.value],
  }));

  return (
    <View style={{ width: '100%', aspectRatio: 3 / 4, borderRadius: 18,
                   shadowColor: '#000', shadowOpacity: 0.6, shadowRadius: 24,
                   shadowOffset: { width: 0, height: 8 } }}>
      <Image source={{ uri: backUri }} style={{ flex: 1, borderRadius: 18 }} />
      <GestureDetector gesture={gesture}>
        <Animated.View style={[{ position: 'absolute', width: 100, height: 134,
          borderRadius: 14, borderWidth: 2, borderColor: '#000', overflow: 'hidden' }, selfieStyle]}>
          <Image source={{ uri: frontUri }} style={{ flex: 1 }} />
        </Animated.View>
      </GestureDetector>
    </View>
  );
}
```

### Capture Button (Dual Shutter)

```tsx
// components/CaptureButton.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

export function CaptureButton({ onCapture }: { onCapture: () => void }) {
  const scale = useSharedValue(1);
  const fill  = useSharedValue(0);
  const ring  = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const disc  = useAnimatedStyle(() => ({ width: fill.value, height: fill.value }));

  return (
    <Pressable
      onPressIn={() => { scale.value = withSpring(0.92); fill.value = withSpring(64); }}
      onPressOut={() => { scale.value = withSpring(1); fill.value = withSpring(0); }}
      onPress={async () => {
        await Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);     // back camera
        onCapture();
        setTimeout(() => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft), 500); // front
      }}
    >
      <Animated.View style={[{ width: 76, height: 76, borderRadius: 38,
        borderWidth: 4, borderColor: '#FFF', alignItems: 'center', justifyContent: 'center' }, ring]}>
        <Animated.View style={[{ backgroundColor: '#FFF', borderRadius: 32 }, disc]} />
      </Animated.View>
    </Pressable>
  );
}
```

### Primary / Secondary Button

```tsx
// components/BeRealButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BeRealButton({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'outline'; onPress: () => void }) {
  const filled = variant === 'filled';
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: filled ? 14 : 12,
        paddingHorizontal: filled ? 32 : 24,
        borderRadius: 500,
        backgroundColor: filled ? colors.accent : 'transparent',
        borderWidth: filled ? 0 : 1,
        borderColor: 'rgba(255,255,255,0.4)',
        opacity: pressed ? 0.85 : 1,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: filled ? colors.onAccent : '#fff' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Countdown Banner & Late Badge

```tsx
import { Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CountdownBanner({ remaining }: { remaining: string }) {
  return (
    <BlurView intensity={40} tint="dark"
      style={{ borderRadius: 500, overflow: 'hidden',
               borderWidth: 1, borderColor: 'rgba(255,214,10,0.4)',
               paddingVertical: 12, paddingHorizontal: 20, backgroundColor: 'rgba(0,0,0,0.5)' }}>
      <Text style={[typography.timer, { textAlign: 'center' }]}>⏰ {remaining} left to capture a BeReal</Text>
    </BlurView>
  );
}

export function LateBadge({ text }: { text: string }) {
  return <Text style={typography.lateBadge}>⚠ {text}</Text>;
}
```

### RealMoji Reaction Cluster

```tsx
// components/RealMojiCluster.tsx — selfie photos, never emoji, never counts
import { Image, Pressable, Text, View } from 'react-native';

export function RealMojiCluster({ selfies, onAdd }: { selfies: string[]; onAdd: () => void }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center' }}>
      {selfies.map((uri, i) => (
        <Image key={i} source={{ uri }}
          style={{ width: 30, height: 30, borderRadius: 15, borderWidth: 2,
                   borderColor: '#000', marginLeft: i === 0 ? 0 : -8 }} />
      ))}
      <Pressable onPress={onAdd}
        style={{ width: 30, height: 30, borderRadius: 15, borderWidth: 1.5,
                 borderStyle: 'dashed', borderColor: 'rgba(255,255,255,0.6)',
                 alignItems: 'center', justifyContent: 'center', marginLeft: 12 }}>
        <Text style={{ color: '#fff', fontSize: 14, fontWeight: '600' }}>+</Text>
      </Pressable>
    </View>
  );
}
```

## 4. Distinctive System — Selfie Corner-Snap

The front selfie inset is the soul of BeReal. The `DualLensCard` above wires the full long-press → drag → spring-snap. Snap spring: `withSpring(target, { damping: 14, stiffness: 180 })`. A tap (not drag) on the selfie swaps which camera is full-bleed via a cross-fade — drive a shared `progress` value between the two `Image`s.

## 5. Tab Bar

BeReal's tab bar is opaque true-black — **no blur**, the canvas continues. Active tint is white.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: '#FFFFFF',
        tabBarInactiveTintColor: 'rgba(255,255,255,0.45)',
        tabBarStyle: { backgroundColor: '#000000', borderTopColor: 'rgba(255,255,255,0.08)', borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '600' },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Friends',  tabBarIcon: ({ color }) => <Ionicons name="people"      size={26} color={color} /> }} />
      <Tabs.Screen name="official" options={{ title: 'Official', tabBarIcon: ({ color }) => <Ionicons name="star"        size={26} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search"      size={26} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Dual-shutter capture: heavy then soft ~0.5s later (see CaptureButton)
await Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
setTimeout(() => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft), 500);

// Camera flip: 180° rotateY over 0.35s
const flip = useSharedValue(0);
const flipStyle = useAnimatedStyle(() => ({ transform: [{ rotateY: `${flip.value}deg` }] }));
flip.value = withTiming(flipped ? 180 : 0, { duration: 350 });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// RealMoji pop: 0 → 1.2 → 1 over 280ms
scale.value = withSequence(withTiming(1.2, { duration: 160 }), withSpring(1));

// Friends' BeReals blurred until you post — animate a BlurView intensity 30 → 0 over 0.4s
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to BeReal's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Camera flip | `arrow.triangle.2.circlepath.camera` | `camera-reverse` |
| Flash | `bolt.fill` / `bolt.slash.fill` | `flash` / `flash-off` |
| Late badge | `exclamationmark.triangle.fill` | `warning` |
| Add RealMoji | `plus` | `add` |
| Comments | `bubble.left` | `chatbubble-outline` |
| Share | `square.and.arrow.up` | `share-outline` |
| More | `ellipsis` | `ellipsis-horizontal` |
| Friends (tab) | `person.2.fill` | `people` |
| Official (tab) | `star.circle.fill` | `star` |
| Search (tab) | `magnifyingglass` | `search` |
| Profile (tab) | `person.crop.circle` | `person-circle` |
| BTS video | `play.rectangle.on.rectangle` | `albums` |

## 8. Platform Notes

- **iOS-only feel**: BeReal's tab bar is opaque true-black with no blur — do **not** add `expo-blur` to the tab bar; only the countdown pill uses a `BlurView`. Android matches naturally (solid black).
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` globally — the pure-black canvas requires light content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the dual-lens card intentionally goes edge-to-edge horizontally (ignore left/right insets for it)
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on the countdown so `2:00 → 1:59` doesn't shift; pin `allowFontScaling={false}` on the timer
- **Camera**: use `expo-camera` with `facing` toggled to capture both lenses in sequence (~0.5s apart) — the dual exposure is the product
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel="Capture BeReal"` on the shutter; group the dual-lens card text and never expose a reaction count (there is none)
