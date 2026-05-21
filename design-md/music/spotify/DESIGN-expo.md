# Spotify (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Spotify's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas: '#121212',
  deepBlack: '#000000',
  surface1: '#181818',
  surface2: '#282828',
  surface3: '#3E3E3E',
  divider:  '#2A2A2A',

  textPrimary:   '#FFFFFF',
  textSecondary: '#B3B3B3',
  textTertiary:  '#6A6A6A',

  green:        '#1DB954',
  greenPressed: '#169C46',
  logoGreen:    '#1ED760',
  errorRed:     '#F15E6C',
} as const;

export type SpotifyColor = keyof typeof colors;
```

## 2. Typography

Load Spotify Mix via `expo-font`. Fall back to `System` which is SF Pro on iOS.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'SpotifyMix-Regular':  require('../assets/fonts/SpotifyMix-Regular.ttf'),
    'SpotifyMix-Semibold': require('../assets/fonts/SpotifyMix-Semibold.ttf'),
    'SpotifyMix-Bold':     require('../assets/fonts/SpotifyMix-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  titleLarge:    { ...base, fontFamily: 'SpotifyMix-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  title:         { ...base, fontFamily: 'SpotifyMix-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  playlistHero:  { ...base, fontFamily: 'SpotifyMix-Bold',     fontSize: 24, lineHeight: 29, letterSpacing: -0.3 },
  trackTitle:    { ...base, fontFamily: 'SpotifyMix-Semibold', fontSize: 16, lineHeight: 20, letterSpacing: -0.1 },
  cardTitle:     { ...base, fontFamily: 'SpotifyMix-Semibold', fontSize: 15, lineHeight: 19, letterSpacing: -0.1 },
  subtitle:      {           fontFamily: 'SpotifyMix-Regular', fontSize: 14, lineHeight: 18, color: '#B3B3B3' },
  body:          { ...base, fontFamily: 'SpotifyMix-Regular', fontSize: 15, lineHeight: 21 },
  meta:          {           fontFamily: 'SpotifyMix-Regular', fontSize: 12, lineHeight: 16, color: '#B3B3B3' },
  labelUpper:    { ...base, fontFamily: 'SpotifyMix-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.5, textTransform: 'uppercase' as const },
  button:        { color: '#000000', fontFamily: 'SpotifyMix-Bold', fontSize: 16, lineHeight: 20 },
  tab:           {           fontFamily: 'SpotifyMix-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Green Play Button

```tsx
// components/PlayButton.tsx
import { Pressable, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function PlayButton({
  size = 72,
  isPlaying,
  onPress,
}: {
  size?: number;
  isPlaying: boolean;
  onPress: () => void;
}) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.92, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
    >
      <Animated.View
        style={[
          {
            width: size, height: size, borderRadius: size / 2,
            backgroundColor: colors.green,
            alignItems: 'center', justifyContent: 'center',
          },
          style,
        ]}
      >
        <Ionicons name={isPlaying ? 'pause' : 'play'} size={size * 0.45} color="#000" />
      </Animated.View>
    </Pressable>
  );
}
```

### Primary Pill Button (Follow / Premium)

```tsx
// components/PillButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PillButton({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'outline'; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 10,
        paddingHorizontal: 32,
        borderRadius: 500,
        backgroundColor: variant === 'filled' ? colors.green : 'transparent',
        borderWidth: variant === 'outline' ? 1 : 0,
        borderColor: colors.textSecondary,
        opacity: pressed ? 0.9 : 1,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: variant === 'filled' ? '#000' : '#fff' }]}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Track Row

```tsx
// components/TrackRow.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TrackRow({
  title, artist, artworkUri, isPlaying, onPress,
}: { title: string; artist: string; artworkUri: string; isPlaying: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.surface2 }]}
    >
      <Image source={{ uri: artworkUri }} style={styles.art} />
      <View style={styles.textCol}>
        <Text style={[typography.trackTitle, isPlaying && { color: colors.green }]} numberOfLines={1}>
          {title}
        </Text>
        <Text style={[typography.subtitle, isPlaying && { color: colors.green }]} numberOfLines={1}>
          {artist}
        </Text>
      </View>
      <Pressable hitSlop={12}>
        <Ionicons name="ellipsis-horizontal" size={20} color={colors.textSecondary} />
      </Pressable>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:     { height: 56, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 12 },
  art:     { width: 44, height: 44, borderRadius: 4, backgroundColor: colors.surface2 },
  textCol: { flex: 1, gap: 2 },
});
```

### Now Playing Hero (with dynamic gradient)

```tsx
// components/NowPlayingHero.tsx
import { Image, View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function NowPlayingHero({
  title, artist, artworkUri, dominantHex,
}: { title: string; artist: string; artworkUri: string; dominantHex: string }) {
  return (
    <View style={{ flex: 1 }}>
      <LinearGradient
        colors={[dominantHex, colors.canvas]}
        style={{ position: 'absolute', inset: 0 }}
      />
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center', paddingHorizontal: 24, gap: 32 }}>
        <Image
          source={{ uri: artworkUri }}
          style={{ width: 340, height: 340, borderRadius: 8,
                   shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 32, shadowOffset: { width: 0, height: 12 } }}
        />
        <View style={{ gap: 6, alignItems: 'center' }}>
          <Text style={typography.title}>{title}</Text>
          <Text style={typography.subtitle}>{artist}</Text>
        </View>
      </View>
    </View>
  );
}
```

## 4. Dynamic Album Color Extraction

Use `react-native-image-colors` (supported in Expo via a dev-client build).

```tsx
import { getColors } from 'react-native-image-colors';

async function extractDominant(uri: string): Promise<string> {
  const result = await getColors(uri, { fallback: '#121212', cache: true, key: uri });
  if (result.platform === 'ios') return result.background ?? '#121212';
  return '#121212';
}
```

For a fully Expo-Go–compatible solution (no native module), use a server-side extraction endpoint or `expo-image-manipulator` to downsample then parse pixel data.

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  '#FFFFFF',
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: {
          position: 'absolute',
          borderTopWidth: 0,
          backgroundColor: 'transparent',
        },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(18,18,18,0.6)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'SpotifyMix-Bold', fontSize: 11, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"           size={24} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"         size={24} color={color} /> }} />
      <Tabs.Screen name="library" options={{ title: 'Library', tabBarIcon: ({ color }) => <Ionicons name="library"        size={24} color={color} /> }} />
      <Tabs.Screen name="premium" options={{ title: 'Premium', tabBarIcon: ({ color }) => <Ionicons name="sparkles"       size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Heart like animation
const scale = useSharedValue(1);
const onLike = () => {
  scale.value = withSequence(withSpring(1.15), withSpring(1));
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
};

// Mini-bar → Full player
// Use expo-router modal presentation: push /now-playing as modal; animate the artwork using `useAnimatedStyle`
// with a shared progress value that both views subscribe to.
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons, Feather, MaterialCommunityIcons, etc. Map to Spotify's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Play | `play` |
| Pause | `pause` |
| Next / Previous | `play-forward` / `play-back` |
| Shuffle | `shuffle` |
| Repeat | `repeat` |
| Heart | `heart-outline` / `heart` |
| Download | `download-outline` / `download` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Library | `library-outline` / `library` |
| Device | `volume-high-outline` |

## 8. Platform Notes

- **iOS-only feel**: Use `expo-blur` for the tab bar to get the `.regularMaterial` equivalent. Android falls back gracefully to an opaque background.
- **Status bar**: Set `<StatusBar style="light" />` from `expo-status-bar` globally — the dark canvas requires light-content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context` to respect home indicator + notch
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` only on the 72pt Play button and scrubber timestamps where layout breaks
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on the Play button, and group track-row text with `accessibilityElementsHidden` on the ellipsis
