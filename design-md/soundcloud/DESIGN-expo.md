# SoundCloud (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates SoundCloud's visual language into paste-ready Expo / React Native code: a design-token module, themed components, the signature waveform scrubber, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, and `react-native-gesture-handler`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Light
  canvas:  '#FFFFFF',
  surface: '#F2F2F2',
  divider: '#E5E5E5',

  // Dark
  canvasDark:  '#1A1A1A',
  surfaceDark: '#262626',
  dividerDark: '#333333',

  // Text
  textPrimary:   '#333333',
  textPrimaryDark: '#FFFFFF',
  textSecondary: '#999999',
  textTertiary:  '#BFBFBF',

  // Brand
  orange:        '#FF5500',
  orangeLight:   '#FF7700',
  orangePressed: '#E64A00',
  errorRed:      '#E5484D',
} as const;

export type SCColor = keyof typeof colors;

export const theme = (dark: boolean) => ({
  canvas:  dark ? colors.canvasDark  : colors.canvas,
  surface: dark ? colors.surfaceDark : colors.surface,
  divider: dark ? colors.dividerDark : colors.divider,
  text:    dark ? colors.textPrimaryDark : colors.textPrimary,
});
```

## 2. Typography

Load Interstate via `expo-font`; fall back to Inter, then `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Interstate-Regular': require('../assets/fonts/Interstate-Regular.ttf'),
    'Interstate-Medium':  require('../assets/fonts/Interstate-Medium.ttf'),
    'Interstate-Bold':    require('../assets/fonts/Interstate-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#333333' } satisfies TextStyle;

export const typography = {
  titleLarge:  { ...base, fontFamily: 'Interstate-Bold',    fontSize: 28, lineHeight: 32, letterSpacing: -0.3 },
  nowPlaying:  { ...base, fontFamily: 'Interstate-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  section:     { ...base, fontFamily: 'Interstate-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  profileName: { ...base, fontFamily: 'Interstate-Bold',    fontSize: 20, lineHeight: 24, letterSpacing: -0.1 },
  trackTitle:  { ...base, fontFamily: 'Interstate-Medium',  fontSize: 16, lineHeight: 21 },
  cardTitle:   { ...base, fontFamily: 'Interstate-Medium',  fontSize: 15, lineHeight: 20 },
  subtitle:    {          fontFamily: 'Interstate-Regular', fontSize: 14, lineHeight: 18, color: '#999999' },
  body:        { ...base, fontFamily: 'Interstate-Regular', fontSize: 15, lineHeight: 22 },
  comment:     { ...base, fontFamily: 'Interstate-Regular', fontSize: 14, lineHeight: 20 },
  meta:        {          fontFamily: 'Interstate-Regular', fontSize: 12, lineHeight: 16, color: '#999999' },
  labelUpper:  { ...base, fontFamily: 'Interstate-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:      { color: '#FFFFFF', fontFamily: 'Interstate-Bold', fontSize: 15, lineHeight: 18, letterSpacing: 0.2 },
  tab:         {          fontFamily: 'Interstate-Medium',  fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  timestamp:   {          fontFamily: 'Interstate-Medium',  fontSize: 11, lineHeight: 13, fontVariant: ['tabular-nums'] as const, color: '#999999' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Commentable Waveform Scrubber

```tsx
// components/Waveform.tsx
import { useState } from 'react';
import { View, Text, Image, LayoutChangeEvent } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Comment = { id: string; position: number; avatar: string; text: string };

export function Waveform({
  samples, progress, comments = [], height = 64, onSeek,
}: {
  samples: number[]; progress: number; comments?: Comment[]; height?: number;
  onSeek?: (p: number) => void;
}) {
  const [w, setW] = useState(0);
  const [revealed, setRevealed] = useState<Comment | null>(null);
  const barW = 1.5, gap = 1;
  const count = Math.max(1, Math.floor(w / (barW + gap)));

  const seek = (x: number) => {
    const p = Math.min(1, Math.max(0, x / w));
    onSeek?.(p);
    Haptics.selectionAsync();
    const hit = comments.find(c => Math.abs(c.position - p) < 0.03);
    if (hit) setRevealed(hit);
  };

  const pan = Gesture.Pan()
    .onBegin(e => seek(e.x))
    .onChange(e => seek(e.x));

  return (
    <GestureDetector gesture={pan}>
      <View onLayout={(e: LayoutChangeEvent) => setW(e.nativeEvent.layout.width)} style={{ height: height + 16, justifyContent: 'center' }}>
        {revealed && (
          <View style={{
            position: 'absolute', top: 0,
            left: Math.min(Math.max(0, revealed.position * w - 60), w - 120),
            backgroundColor: colors.surface, borderRadius: 999, paddingHorizontal: 10, paddingVertical: 6,
          }}>
            <Text style={typography.comment} numberOfLines={1}>{revealed.text}</Text>
          </View>
        )}

        <View style={{ flexDirection: 'row', alignItems: 'center', height }}>
          {Array.from({ length: count }).map((_, i) => {
            const amp = samples.length ? samples[Math.floor((i * samples.length) / count)] : 0.3;
            const played = i / count <= progress;
            return (
              <View key={i} style={{
                width: barW, marginRight: gap,
                height: Math.max(3, amp * (height - 8)),
                borderRadius: 1,
                backgroundColor: played ? colors.orange : colors.orangeLight,
                opacity: played ? 1 : 0.45,
              }} />
            );
          })}
        </View>

        {/* playhead */}
        <View style={{ position: 'absolute', left: progress * w, width: 1, height, backgroundColor: colors.textPrimary }} />

        {/* inline comment avatars on baseline */}
        {comments.map(c => (
          <Image key={c.id} source={{ uri: c.avatar }} style={{
            position: 'absolute', left: c.position * w - 10, top: height - 4,
            width: 20, height: 20, borderRadius: 10, borderWidth: 1.5, borderColor: colors.canvas,
          }} />
        ))}
      </View>
    </GestureDetector>
  );
}
```

### Primary Play FAB

```tsx
// components/PlayFab.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PlayFab({ size = 64, isPlaying, onPress }: { size?: number; isPlaying: boolean; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.93, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
    >
      <Animated.View style={[{
        width: size, height: size, borderRadius: size / 2,
        backgroundColor: colors.orange, alignItems: 'center', justifyContent: 'center',
        shadowColor: colors.orange, shadowOpacity: 0.32, shadowRadius: 20, shadowOffset: { width: 0, height: 6 },
      }, style]}>
        <Ionicons name={isPlaying ? 'pause' : 'play'} size={size * 0.4} color="#FFF" />
      </Animated.View>
    </Pressable>
  );
}
```

### Primary / Outline Pill

```tsx
// components/SCPill.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SCPill({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'outline'; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 9,
        paddingHorizontal: variant === 'filled' ? 24 : 20,
        borderRadius: 4,
        backgroundColor: variant === 'filled' ? (pressed ? colors.orangePressed : colors.orange) : 'transparent',
        borderWidth: variant === 'outline' ? 1 : 0,
        borderColor: pressed ? colors.orange : colors.textSecondary,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={[variant === 'filled' ? typography.button : typography.subtitle,
                    { color: variant === 'filled' ? '#FFF' : colors.textPrimary }]}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Track Row (mini-waveform)

```tsx
// components/TrackRow.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { Waveform } from './Waveform';

export function TrackRow({
  title, uploader, artworkUri, samples, progress, isPlaying, onPress,
}: { title: string; uploader: string; artworkUri: string; samples: number[]; progress: number; isPlaying: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.surface }]}>
      <Image source={{ uri: artworkUri }} style={styles.art} />
      <View style={styles.col}>
        <Text style={typography.subtitle} numberOfLines={1}>{uploader}</Text>
        <Text style={[typography.trackTitle, isPlaying && { color: colors.orange }]} numberOfLines={1}>{title}</Text>
      </View>
      <View style={{ width: 110, height: 28 }} pointerEvents="none">
        <Waveform samples={samples} progress={progress} height={28} />
      </View>
      <Ionicons name="ellipsis-horizontal" size={20} color={colors.textSecondary} />
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: { height: 72, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 12 },
  art: { width: 56, height: 56, borderRadius: 4, backgroundColor: colors.surface },
  col: { flex: 1, gap: 2 },
});
```

## 4. Inline Comment Reveal

Drive reveal from the player clock so a comment surfaces exactly when the orange sweep reaches its avatar:

```tsx
useEffect(() => {
  const hit = comments.find(c => Math.abs(c.position - progress) < 0.004 && !shown.has(c.id));
  if (hit) {
    setShown(s => new Set(s).add(hit.id));
    setRevealed(hit);
    const t = setTimeout(() => setRevealed(null), 3000);
    return () => clearTimeout(t);
  }
}, [progress]);
```

## 5. Tab Bar

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
        tabBarActiveTintColor: colors.orange,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.94)' }} />,
        tabBarLabelStyle: { fontFamily: 'Interstate-Medium', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"        size={24} color={color} /> }} />
      <Tabs.Screen name="library" options={{ title: 'Library', tabBarIcon: ({ color }) => <Ionicons name="albums"        size={24} color={color} /> }} />
      <Tabs.Screen name="upload"  options={{ title: 'Upload',  tabBarIcon: ({ color }) => <Ionicons name="add-circle"    size={28} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'You',     tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Waveform progress sweep — advance progress linearly with the player clock (no easing):
// const progress = position / duration; update via an interval / playback callback.

// Like tap — fill + bounce
const heart = useSharedValue(1);
const onLike = () => {
  heart.value = withSequence(withSpring(1.2), withSpring(1));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
};

// Scrub — selection haptic every ~5%: call Haptics.selectionAsync() on threshold crossings.

// Mini-bar → Full player: push /now-playing as a modal; share artwork via a shared progress value.
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to SoundCloud's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Play | `play` |
| Pause | `pause` |
| Next / Previous | `play-skip-forward` / `play-skip-back` |
| Shuffle | `shuffle` |
| Repeat | `repeat` |
| Like | `heart-outline` / `heart` |
| Repost | `repeat-outline` |
| Comment | `chatbubble-outline` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Library | `albums-outline` / `albums` |
| Upload | `add-circle-outline` / `add-circle` |
| Profile | `person-circle-outline` / `person-circle` |

## 8. Platform Notes

- **Light-first**: SoundCloud's default is the light canvas; drive dark via `useColorScheme()` and the `theme(dark)` helper. SoundCloud Orange stays `#FF5500` in both.
- **Status bar**: `<StatusBar style="dark" />` on light canvas, `style="light"` on dark — set from `expo-status-bar` per theme.
- **Gestures**: the waveform scrubber needs `react-native-gesture-handler`; wrap the app root in `GestureHandlerRootView`.
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; keep the mini-bar above the home indicator.
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` only on waveform timestamps and tab labels where layout breaks.
- **Accessibility**: give the `Waveform` `accessibilityRole="adjustable"` with `accessibilityValue={{ text: '1:24 of 3:32' }}` and an `onAccessibilityAction` for seek.
