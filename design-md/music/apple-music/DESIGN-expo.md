# Apple Music (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Apple Music's visual language into paste-ready Expo / React Native code: design tokens, the red Play button, album tiles, mini-player, Now Playing, and time-synced lyrics.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-audio` (or `expo-av`), `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3. No custom font needed — SF Pro is system default on iOS.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  red:        '#FA2D48',
  coral:      '#FC3C44',
  redPressed: '#D4213B',

  // Canvas
  canvas:     '#FFFFFF',
  canvasDark: '#000000',

  // Surfaces (light — iOS secondary backgrounds)
  surface1:   '#F2F2F7',
  surface2:   '#FFFFFF',
  surface3:   '#E5E5EA',
  divider:    '#C6C6C8',

  // Surfaces (dark)
  surface1Dark: '#1C1C1E',
  surface2Dark: '#2C2C2E',
  surface3Dark: '#3A3A3C',
  dividerDark:  '#38383A',

  // Text (light) — emulate iOS label opacities
  labelPrimary:    '#000000',
  labelSecondary:  'rgba(60,60,67,0.60)',
  labelTertiary:   'rgba(60,60,67,0.30)',
  labelQuaternary: 'rgba(60,60,67,0.18)',

  // Text (dark)
  labelPrimaryDark:    '#FFFFFF',
  labelSecondaryDark:  'rgba(235,235,245,0.60)',
  labelTertiaryDark:   'rgba(235,235,245,0.30)',
  labelQuaternaryDark: 'rgba(235,235,245,0.18)',

  // Badges
  atmosGold:      '#D4A857',
  losslessSilver: '#8E8E93',

  // iOS system
  systemBlue:  '#007AFF',
  systemGreen: '#34C759',
  systemRed:   '#FF3B30',
} as const;
```

## 2. Typography

Use iOS system font — React Native defaults to SF Pro on iOS. No `expo-font` loading needed.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  largeTitle:    { fontSize: 34, lineHeight: 37, fontWeight: '700' as const, letterSpacing: 0.37 },
  title1:        { fontSize: 28, lineHeight: 32, fontWeight: '700' as const, letterSpacing: 0.36 },
  heroListen:    { fontSize: 24, lineHeight: 29, fontWeight: '700' as const, letterSpacing: -0.2 }, // SF Pro Rounded for editorial — see useRoundedFont note below
  title2:        { fontSize: 22, lineHeight: 26, fontWeight: '700' as const, letterSpacing: -0.26 },
  title3:        { fontSize: 20, lineHeight: 24, fontWeight: '600' as const, letterSpacing: -0.15 },
  nowPlaying:    { fontSize: 18, lineHeight: 22, fontWeight: '600' as const, letterSpacing: -0.1 },
  headline:      { fontSize: 17, lineHeight: 21, fontWeight: '600' as const, letterSpacing: -0.43 },
  body:          { fontSize: 17, lineHeight: 25, fontWeight: '400' as const, letterSpacing: -0.43 },
  callout:       { fontSize: 16, lineHeight: 20, fontWeight: '400' as const, letterSpacing: -0.32 },
  subheadline:   { fontSize: 15, lineHeight: 20, fontWeight: '400' as const, letterSpacing: -0.24 },
  footnote:      { fontSize: 13, lineHeight: 17, fontWeight: '400' as const, letterSpacing: -0.08 },
  caption1:      { fontSize: 12, lineHeight: 16, fontWeight: '400' as const },
  caption2:      { fontSize: 11, lineHeight: 13, fontWeight: '400' as const, letterSpacing: 0.07 },

  button:        { fontSize: 17, lineHeight: 22, fontWeight: '600' as const, letterSpacing: -0.43 },
  tabLabel:      { fontSize: 10, lineHeight: 12, fontWeight: '500' as const, letterSpacing: -0.24 },

  lyricsCurrent: { fontSize: 28, lineHeight: 34, fontWeight: '700' as const, letterSpacing: -0.3 },
  lyricsOther:   { fontSize: 22, lineHeight: 29, fontWeight: '600' as const, letterSpacing: -0.2 },
} satisfies Record<string, TextStyle>;
```

**SF Pro Rounded tip**: For the Listen Now editorial hero, specify `fontFamily: 'SF Pro Rounded'` in the `Text` style. This is available system-side on iOS 13+.

## 3. Signature Components

### Primary Red Play Button

```tsx
// components/PlayButton.tsx
import { Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function PlayButton({ isPlaying, size = 64, onPress }: { isPlaying: boolean; size?: number; onPress: () => void }) {
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
          { width: size, height: size, borderRadius: size / 2, backgroundColor: colors.red, alignItems: 'center', justifyContent: 'center' },
          style,
        ]}
      >
        <Ionicons name={isPlaying ? 'pause' : 'play'} size={size * 0.44} color="#FFFFFF" />
      </Animated.View>
    </Pressable>
  );
}
```

### Pill Button (Play / Shuffle / Join Apple Music)

```tsx
// components/PillButton.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PillButton({
  title, icon, color = colors.red, onPress,
}: { title: string; icon: keyof typeof Ionicons.glyphMap; color?: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 8,
        paddingVertical: 10, paddingHorizontal: 24,
        backgroundColor: color, borderRadius: 8,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Ionicons name={icon} size={15} color="#FFFFFF" />
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Album Tile

```tsx
// components/AlbumTile.tsx
import { Image, Pressable, Text, StyleSheet, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  artworkUri: string;
  title: string;
  subtitle: string;
  width?: number;
  onPress: () => void;
};

export function AlbumTile({ artworkUri, title, subtitle, width = 160, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={{ width, gap: 6 }}>
      <View style={[styles.artWrap, { width, height: width }]}>
        <Image source={{ uri: artworkUri }} style={styles.art} />
      </View>
      <Text style={[typography.subheadline, { fontWeight: '600', color: colors.labelPrimary }]} numberOfLines={2}>{title}</Text>
      <Text style={[typography.footnote, { color: colors.labelSecondary }]} numberOfLines={1}>{subtitle}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  artWrap: {
    borderRadius: 12, overflow: 'hidden', backgroundColor: colors.surface1,
    shadowColor: '#000', shadowOpacity: 0.08, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
    elevation: 3,
  },
  art: { width: '100%', height: '100%', resizeMode: 'cover' },
});
```

### Track Row

```tsx
// components/TrackRow.tsx
import { Image, Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  title: string;
  artist: string;
  artworkUri: string;
  isPlaying?: boolean;
  hasAtmos?: boolean;
  explicit?: boolean;
  onPress: () => void;
};

export function TrackRow({ title, artist, artworkUri, isPlaying, hasAtmos, explicit, onPress }: Props) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [styles.row, pressed && { backgroundColor: 'rgba(0,0,0,0.05)' }]}
    >
      <Image source={{ uri: artworkUri }} style={styles.art} />
      <View style={{ flex: 1 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
          {explicit && (
            <View style={styles.explicit}><Text style={styles.explicitText}>E</Text></View>
          )}
          <Text style={[typography.body, { color: isPlaying ? colors.red : colors.labelPrimary, flexShrink: 1 }]} numberOfLines={1}>
            {title}
          </Text>
          {hasAtmos && <AtmosBadge />}
        </View>
        <Text style={[typography.subheadline, { color: colors.labelSecondary }]} numberOfLines={1}>{artist}</Text>
      </View>
      <Pressable hitSlop={12}>
        <Ionicons name="ellipsis-horizontal" size={20} color={colors.labelSecondary} />
      </Pressable>
    </Pressable>
  );
}

export function AtmosBadge() {
  return (
    <View style={{ paddingVertical: 4, paddingHorizontal: 8, backgroundColor: colors.atmosGold, borderRadius: 500 }}>
      <Text style={{ color: '#FFFFFF', fontSize: 11, fontWeight: '600' }}>Dolby Atmos</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  row:           { flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, height: 56 },
  art:           { width: 44, height: 44, borderRadius: 8, backgroundColor: colors.surface1 },
  explicit:      { backgroundColor: colors.labelSecondary, paddingHorizontal: 4, borderRadius: 3 },
  explicitText:  { color: '#FFFFFF', fontSize: 11, fontWeight: '700' },
});
```

### Now Playing

```tsx
// components/NowPlaying.tsx
import { Image, View, Text, StyleSheet, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { PlayButton } from './PlayButton';
import { Scrubber } from './Scrubber';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  title: string;
  artist: string;
  artworkUri: string;
  dominantHex: string;
  complementaryHex: string;
  isPlaying: boolean;
  progress: number; // 0..1
  onTogglePlay: () => void;
  onScrub: (p: number) => void;
};

export function NowPlaying({ title, artist, artworkUri, dominantHex, complementaryHex, isPlaying, progress, onTogglePlay, onScrub }: Props) {
  return (
    <View style={{ flex: 1 }}>
      <LinearGradient colors={[dominantHex, complementaryHex]} style={StyleSheet.absoluteFill} />
      <View style={styles.center}>
        <Image source={{ uri: artworkUri }} style={styles.art} />
        <View style={{ gap: 4, alignItems: 'center', marginTop: 32 }}>
          <Text style={[typography.nowPlaying, { color: '#FFFFFF' }]} numberOfLines={1}>{title}</Text>
          <Text style={[typography.subheadline, { color: 'rgba(255,255,255,0.7)' }]} numberOfLines={1}>{artist}</Text>
        </View>

        <View style={{ width: '100%', marginTop: 32 }}>
          <Scrubber progress={progress} onScrub={onScrub} />
        </View>

        <View style={styles.controls}>
          <Pressable><Ionicons name="play-skip-back" size={32} color="#FFFFFF" /></Pressable>
          <PlayButton isPlaying={isPlaying} size={64} onPress={onTogglePlay} />
          <Pressable><Ionicons name="play-skip-forward" size={32} color="#FFFFFF" /></Pressable>
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  center:   { flex: 1, alignItems: 'center', justifyContent: 'center', paddingHorizontal: 24 },
  art:      { width: 340, height: 340, borderRadius: 16, shadowColor: '#000', shadowOpacity: 0.3, shadowRadius: 40, shadowOffset: { width: 0, height: 20 } },
  controls: { flexDirection: 'row', alignItems: 'center', gap: 32, marginTop: 32 },
});
```

### Scrubber

```tsx
// components/Scrubber.tsx
import { View, StyleSheet, GestureResponderEvent, LayoutChangeEvent } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import { useState } from 'react';
import { colors } from '../theme/colors';

export function Scrubber({ progress, onScrub }: { progress: number; onScrub: (p: number) => void }) {
  const [width, setWidth] = useState(0);
  const thumbScale = useSharedValue(1);

  const fillW = width * progress;

  const onLayout = (e: LayoutChangeEvent) => setWidth(e.nativeEvent.layout.width);

  const thumbStyle = useAnimatedStyle(() => ({ transform: [{ scale: thumbScale.value }] }));

  const handleTouch = (e: GestureResponderEvent) => {
    if (!width) return;
    const p = Math.max(0, Math.min(1, e.nativeEvent.locationX / width));
    onScrub(p);
  };

  return (
    <View
      style={styles.container}
      onLayout={onLayout}
      onStartShouldSetResponder={() => true}
      onResponderGrant={e => { thumbScale.value = withSpring(1.2); handleTouch(e); }}
      onResponderMove={handleTouch}
      onResponderRelease={() => { thumbScale.value = withSpring(1); }}
    >
      <View style={styles.track} />
      <View style={[styles.fill, { width: fillW }]} />
      <Animated.View style={[styles.thumb, { left: fillW - 8 }, thumbStyle]} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { height: 24, justifyContent: 'center' },
  track:     { height: 4, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.2)' },
  fill:      { position: 'absolute', height: 4, borderRadius: 2, backgroundColor: colors.red },
  thumb:     { position: 'absolute', width: 16, height: 16, borderRadius: 8, backgroundColor: '#FFFFFF',
               shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 2, shadowOffset: { width: 0, height: 1 } },
});
```

### Mini-Player

```tsx
// components/MiniPlayer.tsx
import { Image, Pressable, Text, View, StyleSheet } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  title: string;
  artist: string;
  artworkUri: string;
  isPlaying: boolean;
  onExpand: () => void;
  onTogglePlay: () => void;
  onSkip: () => void;
};

export function MiniPlayer({ title, artist, artworkUri, isPlaying, onExpand, onTogglePlay, onSkip }: Props) {
  return (
    <Pressable onPress={onExpand}>
      <BlurView intensity={80} tint="default" style={styles.bar}>
        <Image source={{ uri: artworkUri }} style={styles.art} />
        <View style={{ flex: 1, gap: 2 }}>
          <Text style={[typography.subheadline, { fontWeight: '500', color: colors.labelPrimary }]} numberOfLines={1}>{title}</Text>
          <Text style={[typography.footnote, { color: colors.labelSecondary }]} numberOfLines={1}>{artist}</Text>
        </View>
        <Pressable onPress={onTogglePlay} hitSlop={8}>
          <Ionicons name={isPlaying ? 'pause' : 'play'} size={22} color={colors.labelPrimary} />
        </Pressable>
        <Pressable onPress={onSkip} hitSlop={8} style={{ marginLeft: 12, marginRight: 12 }}>
          <Ionicons name="play-skip-forward" size={22} color={colors.labelPrimary} />
        </Pressable>
      </BlurView>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  bar: { height: 64, flexDirection: 'row', alignItems: 'center', gap: 12, paddingLeft: 12 },
  art: { width: 44, height: 44, borderRadius: 8 },
});
```

### Time-Synced Lyrics

```tsx
// components/LyricsView.tsx
import { ScrollView, Text, View } from 'react-native';
import { useEffect, useRef } from 'react';
import { LinearGradient } from 'expo-linear-gradient';
import { typography } from '../theme/typography';

type Line = { id: string; start: number; end: number; text: string };

export function LyricsView({
  lines, currentTime, dominantHex,
}: { lines: Line[]; currentTime: number; dominantHex: string }) {
  const scrollRef = useRef<ScrollView | null>(null);
  const lineRefs = useRef<Record<string, View | null>>({});

  const currentIndex = lines.findIndex(l => currentTime >= l.start && currentTime < l.end);

  useEffect(() => {
    if (currentIndex < 0) return;
    const line = lines[currentIndex];
    const view = lineRefs.current[line.id];
    if (view && scrollRef.current) {
      view.measureLayout((scrollRef.current as any).getNativeScrollRef?.() ?? scrollRef.current, (_x, y) => {
        scrollRef.current?.scrollTo({ y: y - 200, animated: true });
      }, () => {});
    }
  }, [currentIndex]);

  return (
    <View style={{ flex: 1 }}>
      <LinearGradient colors={[dominantHex, '#000000']} style={{ position: 'absolute', inset: 0 as any }} />
      <ScrollView ref={scrollRef} contentContainerStyle={{ paddingHorizontal: 24, paddingVertical: 80, gap: 24 }}>
        {lines.map((line, i) => {
          const isCurrent = i === currentIndex;
          const opacity = isCurrent ? 1 : i > currentIndex ? 0.6 : 0.3;
          return (
            <View key={line.id} ref={r => (lineRefs.current[line.id] = r)}>
              <Text style={[isCurrent ? typography.lyricsCurrent : typography.lyricsOther, { color: `rgba(255,255,255,${opacity})` }]}>
                {line.text}
              </Text>
            </View>
          );
        })}
      </ScrollView>
    </View>
  );
}
```

## 4. Tab Bar

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
        tabBarActiveTintColor:  colors.red,
        tabBarInactiveTintColor: colors.labelSecondary,
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500', letterSpacing: -0.24 },
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="default" style={{ flex: 1 }} />
        ),
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'home' : 'home-outline'} size={25} color={color} /> }} />
      <Tabs.Screen name="new"     options={{ title: 'New',     tabBarIcon: ({ color }) => <Ionicons name="musical-notes" size={25} color={color} /> }} />
      <Tabs.Screen name="radio"   options={{ title: 'Radio',   tabBarIcon: ({ color }) => <Ionicons name="radio" size={25} color={color} /> }} />
      <Tabs.Screen name="library" options={{ title: 'Library', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'albums' : 'albums-outline'} size={25} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search" size={25} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Dynamic Album Color Extraction

Use `react-native-image-colors` (supported in Expo via a dev-client build).

```tsx
import { getColors } from 'react-native-image-colors';
import { colors } from './theme/colors';

export async function extractPalette(uri: string): Promise<{ dominant: string; complementary: string }> {
  const result = await getColors(uri, { fallback: colors.canvasDark, cache: true, key: uri });
  if (result.platform === 'ios') {
    return {
      dominant: result.primary ?? result.background ?? colors.canvasDark,
      complementary: result.secondary ?? colors.canvasDark,
    };
  }
  return { dominant: colors.canvasDark, complementary: colors.canvasDark };
}
```

For a fully Expo-Go–compatible approach, downsample the image with `expo-image-manipulator` and extract an average pixel using a Canvas-based library or a server endpoint.

## 6. Motion

```tsx
// Heart like
import { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';

const scale = useSharedValue(1);
const onLike = () => {
  scale.value = withSequence(withSpring(1.2, { damping: 6 }), withSpring(1, { damping: 14 }));
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
};

// Mini-player → Now Playing shared element
// Use a BottomSheet (e.g. @gorhom/bottom-sheet) or expo-router Modal group
// Use LayoutAnimation or a Reanimated shared value driving height + image scale simultaneously

// Shuffle toggle
const [shuffle, setShuffle] = useState(false);
const onShuffle = () => {
  setShuffle(s => !s);
  Haptics.selectionAsync();
};
```

## 7. Icon Library

`@expo/vector-icons` (Ionicons) covers most needs; use `MaterialCommunityIcons` for special cases.

| Purpose | Ionicons |
|---------|----------|
| Play / Pause | `play` / `pause` |
| Forward / Back | `play-skip-forward` / `play-skip-back` |
| Shuffle | `shuffle` |
| Repeat | `repeat` |
| Heart | `heart-outline` / `heart` |
| Download | `cloud-download-outline` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Home | `home-outline` / `home` |
| New (Browse) | `musical-notes` |
| Radio | `radio` |
| Library | `albums-outline` / `albums` |
| Back | `chevron-back` |
| Chevron right | `chevron-forward` |
| Lyrics | `chatbubble-ellipses-outline` |
| AirPlay | `tv-outline` (no exact AirPlay icon; use MaterialIcons `cast` if needed) |
| Queue | `list` |
| Volume | `volume-low` / `volume-high` |

## 8. Platform Notes

- **Status bar**: `<StatusBar style="auto" />` from `expo-status-bar` — it adapts to current screen background
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`
- **Audio**: use `expo-audio` for foreground playback; `expo-av` + `useAudioSession({ playThroughEarpiece: false, ...})` for lock-screen playback
- **Lock-screen Now Playing info**: use `expo-av`'s `setAudioModeAsync` + native module for `MPNowPlayingInfoCenter` — or ship a config plugin
- **Dynamic Type**: React Native respects iOS text-scale by default for `Text`; enable `allowFontScaling` (default true) on all but the mini-player geometry
- **Materials / blur**: `expo-blur` provides translucent backgrounds; use `tint="default"` for automatic light/dark adaptation
- **Haptics**: soft on Play, success on heart like, selection on shuffle / chip / tab change
- **Accessibility**: expose every button with a descriptive label; on track rows, announce "Play {title} by {artist}, {optionally: Dolby Atmos}"
- **Image caching**: use `expo-image` for album art — better loading behavior and memory management than `Image`

## 9. Dark Mode Theme Helper

```ts
// theme/useTheme.ts
import { useColorScheme } from 'react-native';
import { colors } from './colors';

export function useTheme() {
  const scheme = useColorScheme() ?? 'light';
  const dark = scheme === 'dark';
  return {
    canvas:         dark ? colors.canvasDark   : colors.canvas,
    surface1:       dark ? colors.surface1Dark : colors.surface1,
    surface2:       dark ? colors.surface2Dark : colors.surface2,
    surface3:       dark ? colors.surface3Dark : colors.surface3,
    divider:        dark ? colors.dividerDark  : colors.divider,
    labelPrimary:   dark ? colors.labelPrimaryDark   : colors.labelPrimary,
    labelSecondary: dark ? colors.labelSecondaryDark : colors.labelSecondary,
    labelTertiary:  dark ? colors.labelTertiaryDark  : colors.labelTertiary,
    isDark: dark,
  };
}
```
