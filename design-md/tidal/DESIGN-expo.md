# TIDAL (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates TIDAL's visual language into paste-ready Expo / React Native code: a design-token module, themed components, the signature quality badge, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, and `expo-blur`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#000000',
  surface1: '#0A0A0A',
  surface2: '#1A1A1A',
  divider:  '#262626',

  textPrimary:   '#FFFFFF',
  textSecondary: '#9A9A9A',
  textTertiary:  '#5C5C5C',

  white:        '#FFFFFF', // action color
  cyan:         '#00FFFF', // quality-tier badges ONLY
  cyanPressed:  '#00CCCC',
  errorRed:     '#FF453A',
} as const;

export type TidalColor = keyof typeof colors;
```

## 2. Typography

Load TIDAL's brand sans via `expo-font`; fall back to Space Grotesk, then `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'SpaceGrotesk-Regular':  require('../assets/fonts/SpaceGrotesk-Regular.ttf'),
    'SpaceGrotesk-SemiBold': require('../assets/fonts/SpaceGrotesk-SemiBold.ttf'),
    'SpaceGrotesk-Bold':     require('../assets/fonts/SpaceGrotesk-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#000' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  titleLarge:  { ...base, fontFamily: 'SpaceGrotesk-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  nowPlaying:  { ...base, fontFamily: 'SpaceGrotesk-Bold',     fontSize: 24, lineHeight: 29, letterSpacing: -0.3 },
  section:     { ...base, fontFamily: 'SpaceGrotesk-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  albumTitle:  { ...base, fontFamily: 'SpaceGrotesk-Bold',     fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  trackTitle:  { ...base, fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 16, lineHeight: 21 },
  cardTitle:   { ...base, fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 15, lineHeight: 20 },
  subtitle:    {          fontFamily: 'SpaceGrotesk-Regular',  fontSize: 14, lineHeight: 18, color: '#9A9A9A' },
  body:        { ...base, fontFamily: 'SpaceGrotesk-Regular',  fontSize: 15, lineHeight: 22 },
  meta:        {          fontFamily: 'SpaceGrotesk-Regular',  fontSize: 12, lineHeight: 16, color: '#9A9A9A' },
  badge:       { color: '#00FFFF', fontFamily: 'SpaceGrotesk-Bold', fontSize: 10, lineHeight: 12, letterSpacing: 1.0, textTransform: 'uppercase' as const },
  labelUpper:  { ...base, fontFamily: 'SpaceGrotesk-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.8, textTransform: 'uppercase' as const },
  button:      { color: '#000000', fontFamily: 'SpaceGrotesk-Bold', fontSize: 15, lineHeight: 18, letterSpacing: 0.3 },
  tab:         {          fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.3 },
  timestamp:   {          fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 11, lineHeight: 13, fontVariant: ['tabular-nums'] as const, color: '#9A9A9A' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Quality-Tier Badge

```tsx
// components/QualityBadge.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type Quality = 'MASTER' | 'HIFI' | 'MAX' | 'DOLBY ATMOS';

export function QualityBadge({ quality }: { quality: Quality }) {
  return (
    <View
      accessibilityLabel={`${quality} quality`}
      style={{
        borderWidth: 1, borderColor: colors.cyan, borderRadius: 4,
        paddingVertical: 4, paddingHorizontal: 8, alignSelf: 'flex-start',
      }}
    >
      <Text style={typography.badge}>{quality}</Text>
    </View>
  );
}
```

### Primary Play Button (flat — no shadow)

```tsx
// components/PlayButton.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PlayButton({ size = 64, isPlaying, onPress }: { size?: number; isPlaying: boolean; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.93, { damping: 15 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 15 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
    >
      <Animated.View style={[{
        width: size, height: size, borderRadius: size / 2,
        backgroundColor: colors.white, alignItems: 'center', justifyContent: 'center',
        // no shadow — TIDAL is flat
      }, style]}>
        <Ionicons name={isPlaying ? 'pause' : 'play'} size={size * 0.4} color="#000" />
      </Animated.View>
    </Pressable>
  );
}
```

### Primary / Outline Pill

```tsx
// components/TidalPill.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TidalPill({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'outline'; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 11,
        paddingHorizontal: variant === 'filled' ? 32 : 24,
        borderRadius: 500,
        backgroundColor: variant === 'filled' ? (pressed ? '#E5E5E5' : colors.white) : 'transparent',
        borderWidth: variant === 'outline' ? 1 : 0,
        borderColor: pressed ? colors.white : colors.textSecondary,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={[variant === 'filled' ? typography.button : { ...typography.subtitle, fontFamily: 'SpaceGrotesk-SemiBold' },
                    { color: variant === 'filled' ? '#000' : '#FFF' }]}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Track Row (with quality badge + equalizer)

```tsx
// components/TrackRow.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { QualityBadge, Quality } from './QualityBadge';
import { Equalizer } from './Equalizer';

export function TrackRow({
  title, artist, artworkUri, duration, quality, isPlaying, onPress,
}: { title: string; artist: string; artworkUri: string; duration: string; quality?: Quality; isPlaying: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.surface2 }]}>
      <View>
        <Image source={{ uri: artworkUri }} style={styles.art} />
        {isPlaying && <View style={styles.eq}><Equalizer /></View>}
      </View>
      <View style={styles.col}>
        <Text style={typography.trackTitle} numberOfLines={1}>{title}</Text>
        <Text style={typography.subtitle} numberOfLines={1}>{artist}</Text>
      </View>
      {quality && <QualityBadge quality={quality} />}
      <Text style={typography.timestamp}>{duration}</Text>
      <Ionicons name="ellipsis-horizontal" size={20} color={colors.textSecondary} />
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: { height: 60, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 10, backgroundColor: colors.canvas },
  art: { width: 44, height: 44, borderRadius: 0, backgroundColor: colors.surface2 }, // square — never rounded
  eq:  { position: 'absolute', left: 4, top: 14 },
  col: { flex: 1, gap: 2 },
});
```

### Equalizer

```tsx
// components/Equalizer.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function Equalizer() {
  const bars = [useSharedValue(0.4), useSharedValue(0.9), useSharedValue(0.6)];
  useEffect(() => {
    bars.forEach((b, i) =>
      (b.value = withRepeat(withTiming(i === 1 ? 0.3 : 1, { duration: 360 + i * 120, easing: Easing.inOut(Easing.ease) }), -1, true)));
  }, []);
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 2, height: 16 }}>
      {bars.map((b, i) => {
        const s = useAnimatedStyle(() => ({ height: 16 * b.value }));
        return <Animated.View key={i} style={[{ width: 3, backgroundColor: colors.white }, s]} />;
      })}
    </View>
  );
}
```

## 4. Quality Tier Mapping

```ts
export function badgeFor(format: string): Quality | undefined {
  switch (format) {
    case 'flac-24':     return 'MASTER';
    case 'flac-16':     return 'HIFI';
    case 'flac-24-max': return 'MAX';
    case 'atmos':       return 'DOLBY ATMOS';
    default:            return undefined; // no badge for lossy
  }
}
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
        tabBarActiveTintColor: colors.white, // white; cyan is fidelity-only
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => <BlurView intensity={90} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(0,0,0,0.96)' }} />,
        tabBarLabelStyle: { fontFamily: 'SpaceGrotesk-SemiBold', fontSize: 10, letterSpacing: 0.3 },
      }}
    >
      <Tabs.Screen name="index"      options={{ title: 'Home',          tabBarIcon: ({ color }) => <Ionicons name="home"        size={24} color={color} /> }} />
      <Tabs.Screen name="videos"     options={{ title: 'Videos',        tabBarIcon: ({ color }) => <Ionicons name="film"        size={24} color={color} /> }} />
      <Tabs.Screen name="search"     options={{ title: 'Search',        tabBarIcon: ({ color }) => <Ionicons name="search"      size={24} color={color} /> }} />
      <Tabs.Screen name="collection" options={{ title: 'My Collection', tabBarIcon: ({ color }) => <Ionicons name="albums"      size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Screen transitions: opacity crossfade only — no slide, no spring.
// expo-router: screenOptions={{ animation: 'fade' }}

// Play/pause tap: Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium)

// Scrubber: animate track height 1 → 3 while dragging; Haptics.selectionAsync() ticks.

// Favorite tap — fill + subtle bounce
const heart = useSharedValue(1);
const onFav = () => {
  heart.value = withSequence(withSpring(1.12), withSpring(1));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
};

// Mini-bar → Full player: push /now-playing with animation:'fade'; crossfade the artwork, do not spring.
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to TIDAL's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Play | `play` |
| Pause | `pause` |
| Next / Previous | `play-skip-forward` / `play-skip-back` |
| Shuffle | `shuffle` |
| Repeat | `repeat` |
| Favorite | `heart-outline` / `heart` |
| Add to collection | `add-circle-outline` |
| Credits | `information-circle-outline` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Videos | `film-outline` / `film` |
| Collection | `albums-outline` / `albums` |
| Cast | `play-forward-circle-outline` |

## 8. Platform Notes

- **Dark-only**: TIDAL has no light mode. Force dark — set `backgroundColor: '#000'` on the navigator and never read `useColorScheme()`.
- **Status bar**: `<StatusBar style="light" />` globally from `expo-status-bar`.
- **Flat by design**: do not add `shadow*` props to cards, tiles, or the play button — TIDAL communicates elevation via tonal steps (`#0A0A0A` / `#1A1A1A`). The only shadow is on bottom sheets.
- **Square art**: never set `borderRadius` on album art / thumbnails — the square is the signature.
- **Backdrop**: blur the now-playing art with `expo-blur` (or a pre-blurred remote variant) under a `rgba(0,0,0,0.6)` overlay — monochrome, no color extraction.
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; keep the mini-bar above the home indicator.
- **Dynamic Type**: set `allowFontScaling={false}` on quality badges, timestamps, and tab labels where the exact geometry matters.
- **Accessibility**: include the quality tier in the row's `accessibilityLabel` ("…, Master quality, 3:08").
