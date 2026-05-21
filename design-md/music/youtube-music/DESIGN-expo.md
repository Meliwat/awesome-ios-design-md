# YouTube Music (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates YouTube Music's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — the only mode)
  canvas:       '#030303',
  surface1:     '#1F1F1F',
  surface2:     '#272727',
  miniSurface:  '#282828',
  chipBg:       '#2A2A2A',
  divider:      '#303030',
  tabBar:       '#0A0A0A',

  // Brand
  ytRed:        '#FF0000',
  ytRedPressed: '#CC0000',
  actionWhite:  '#FFFFFF',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#AAAAAA',
  textTertiary:  '#717171',

  // Semantic
  success: '#2BA640',
  error:   '#FF4E45',
} as const;

export type YTMColor = keyof typeof colors;
```

## 2. Typography

Load **Roboto** via `expo-font` — never substitute; it binds the app to the YouTube family.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Roboto-Regular': require('../assets/fonts/Roboto-Regular.ttf'),
    'Roboto-Medium':  require('../assets/fonts/Roboto-Medium.ttf'),
    'Roboto-Bold':    require('../assets/fonts/Roboto-Bold.ttf'),
    'Roboto-Black':   require('../assets/fonts/Roboto-Black.ttf'),
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
  screenTitle: { ...white, fontFamily: 'Roboto-Black',   fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  nowPlaying:  { ...white, fontFamily: 'Roboto-Bold',    fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { ...white, fontFamily: 'Roboto-Bold',    fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardHeader:  { ...white, fontFamily: 'Roboto-Bold',    fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:        { ...white, fontFamily: 'Roboto-Regular', fontSize: 16, lineHeight: 24 },
  rowTitle:    { ...white, fontFamily: 'Roboto-Medium',  fontSize: 15, lineHeight: 20 },
  subtitle:    { color: '#AAAAAA', fontFamily: 'Roboto-Regular', fontSize: 14, lineHeight: 19 },
  toggle:      { fontFamily: 'Roboto-Bold',   fontSize: 12, lineHeight: 12, letterSpacing: 0.2 },
  chip:        { ...white, fontFamily: 'Roboto-Medium', fontSize: 13, lineHeight: 13 },
  timestamp:   { color: '#AAAAAA', fontFamily: 'Roboto-Regular', fontSize: 11, lineHeight: 11, fontVariant: ['tabular-nums'] as const },
  eyebrow:     { color: '#AAAAAA', fontFamily: 'Roboto-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.6 },
  tab:         { fontFamily: 'Roboto-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:      { fontFamily: 'Roboto-Bold',   fontSize: 15, lineHeight: 15 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Immersive Now Playing (art-glow backdrop + art + meta)

```tsx
// components/NowPlaying.tsx
import { Image, View, Text } from 'react-native';
import { BlurView } from 'expo-blur';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function NowPlaying({ artUri, track, artist }: { artUri: string; track: string; artist: string }) {
  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas }}>
      {/* Art-derived blurred backdrop glow */}
      <View style={{ ...StyleSheetAbsoluteFill }}>
        <Image
          source={{ uri: artUri }}
          style={{ position: 'absolute', top: '-20%', left: '-20%', right: '-20%', bottom: '40%' }}
          blurRadius={40}
          resizeMode="cover"
        />
        <LinearGradient
          colors={['transparent', colors.canvas]}
          style={{ position: 'absolute', left: 0, right: 0, bottom: 0, top: '40%' }}
        />
      </View>

      <View style={{ alignItems: 'center', paddingTop: 26 }}>
        <Image
          source={{ uri: artUri }}
          style={{ width: 232, height: 232, borderRadius: 8 }}
        />
      </View>

      <View style={{ paddingHorizontal: 24, marginTop: 22 }}>
        <Text style={typography.nowPlaying}>{track}</Text>
        <Text style={[typography.subtitle, { marginTop: 4 }]}>{artist}</Text>
      </View>
    </View>
  );
}

const StyleSheetAbsoluteFill = { position: 'absolute' as const, top: 0, left: 0, right: 0, bottom: 0 };
```

### Song / Video Toggle (Signature)

```tsx
// components/SongVideoToggle.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SongVideoToggle({ onChange }: { onChange?: (m: 'song' | 'video') => void }) {
  const [mode, setMode] = useState<'song' | 'video'>('song');
  const pick = (m: 'song' | 'video') => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setMode(m);
    onChange?.(m);
  };
  return (
    <View style={{ flexDirection: 'row', alignSelf: 'center', backgroundColor: 'rgba(255,255,255,0.08)', borderRadius: 999, padding: 4, gap: 6 }}>
      {(['song', 'video'] as const).map((m) => {
        const active = mode === m;
        return (
          <Pressable key={m} onPress={() => pick(m)} style={{
            paddingVertical: 7, paddingHorizontal: 18, borderRadius: 999,
            backgroundColor: active ? colors.actionWhite : 'transparent',
          }}>
            <Text style={[typography.toggle, { color: active ? colors.canvas : colors.textSecondary }]}>
              {m === 'song' ? 'Song' : 'Video'}
            </Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Scrubber (white at rest, red while dragging)

```tsx
// components/Scrubber.tsx
import { useState } from 'react';
import { View, Text, LayoutChangeEvent } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useAnimatedStyle, useSharedValue, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Scrubber() {
  const [w, setW] = useState(0);
  const progress = useSharedValue(0.42);
  const dragging = useSharedValue(0);

  const fillStyle = useAnimatedStyle(() => ({
    width: progress.value * w,
    backgroundColor: dragging.value ? colors.ytRed : colors.actionWhite,
  }));
  const thumbStyle = useAnimatedStyle(() => ({
    left: progress.value * w - (dragging.value ? 8 : 6),
    width: dragging.value ? 16 : 12,
    height: dragging.value ? 16 : 12,
    backgroundColor: dragging.value ? colors.ytRed : colors.actionWhite,
  }));

  const pan = Gesture.Pan()
    .onBegin(() => { dragging.value = withTiming(1, { duration: 80 }); })
    .onUpdate((e) => { progress.value = Math.min(1, Math.max(0, e.x / w)); })
    .onFinalize(() => {
      dragging.value = withTiming(0, { duration: 150 });
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    });

  return (
    <View>
      <GestureDetector gesture={pan}>
        <View onLayout={(e: LayoutChangeEvent) => setW(e.nativeEvent.layout.width)} style={{ height: 16, justifyContent: 'center' }}>
          <View style={{ height: 3, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.22)' }} />
          <Animated.View style={[{ position: 'absolute', height: 3, borderRadius: 2 }, fillStyle]} />
          <Animated.View style={[{ position: 'absolute', borderRadius: 999 }, thumbStyle]} />
        </View>
      </GestureDetector>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 8 }}>
        <Text style={typography.timestamp}>1:48</Text>
        <Text style={typography.timestamp}>-2:34</Text>
      </View>
    </View>
  );
}
```

### Transport Controls (white play button)

```tsx
// components/Transport.tsx
import { useState } from 'react';
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function Transport() {
  const [playing, setPlaying] = useState(true);
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 32 }}>
      <Ionicons name="play-skip-back" size={22} color={colors.textPrimary} />
      <Ionicons name="play-back" size={26} color={colors.textPrimary} />
      <Pressable
        onPress={() => setPlaying((p) => !p)}
        style={{ width: 64, height: 64, borderRadius: 32, backgroundColor: colors.actionWhite, alignItems: 'center', justifyContent: 'center' }}
      >
        <Ionicons name={playing ? 'pause' : 'play'} size={26} color={colors.canvas} />
      </Pressable>
      <Ionicons name="play-forward" size={26} color={colors.textPrimary} />
      <Ionicons name="play-skip-forward" size={22} color={colors.textPrimary} />
    </View>
  );
}
```

### Up-Next Queue Shelf

```tsx
// components/UpNextShelf.tsx
import { View, Text, FlatList } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Item = { id: string; title: string; artist: string };

export function UpNextShelf({ items }: { items: Item[] }) {
  return (
    <View style={{ backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: 'rgba(255,255,255,0.12)' }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', padding: 16 }}>
        <Text style={typography.eyebrow}>UP NEXT</Text>
        <Ionicons name="list" size={20} color={colors.textSecondary} />
      </View>
      <FlatList
        data={items}
        keyExtractor={(i) => i.id}
        renderItem={({ item }) => (
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 16, paddingVertical: 8 }}>
            <LinearGradient colors={['#FF8A00', '#E52E71']} style={{ width: 40, height: 40, borderRadius: 4 }} />
            <View style={{ flex: 1 }}>
              <Text style={typography.rowTitle}>{item.title}</Text>
              <Text style={typography.subtitle}>{item.artist}</Text>
            </View>
            <Ionicons name="reorder-three" size={18} color={colors.textTertiary} />
          </View>
        )}
      />
    </View>
  );
}
```

### Mini-Player Bar

```tsx
// components/MiniPlayer.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function MiniPlayer() {
  return (
    <View style={{ backgroundColor: colors.miniSurface, borderRadius: 8, overflow: 'hidden' }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 12, paddingVertical: 8 }}>
        <LinearGradient colors={['#C13584', '#5851DB']} style={{ width: 40, height: 40, borderRadius: 4 }} />
        <View style={{ flex: 1 }}>
          <Text style={{ color: colors.textPrimary, fontFamily: 'Roboto-Medium', fontSize: 13 }}>Midnight City</Text>
          <Text style={{ color: colors.textSecondary, fontFamily: 'Roboto-Regular', fontSize: 11 }}>M83</Text>
        </View>
        <Ionicons name="pause" size={22} color={colors.textPrimary} />
        <Ionicons name="play-forward" size={22} color={colors.textPrimary} />
      </View>
      <View style={{ height: 2, backgroundColor: colors.actionWhite }} />
    </View>
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
        tabBarActiveTintColor: colors.actionWhite,   // pure white, no red, no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.tabBar, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Roboto-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home" size={24} color={color} /> }} />
      <Tabs.Screen name="samples" options={{ title: 'Samples', tabBarIcon: ({ color }) => <Ionicons name="albums" size={24} color={color} /> }} />
      <Tabs.Screen name="explore" options={{ title: 'Explore', tabBarIcon: ({ color }) => <Ionicons name="search" size={24} color={color} /> }} />
      <Tabs.Screen name="library" options={{ title: 'Library', tabBarIcon: ({ color }) => <Ionicons name="library" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Song ↔ Video toggle — slide active bg + cross-morph player body
import Animated, { withTiming, FadeIn, FadeOut } from 'react-native-reanimated';
// active background: withTiming over 200ms ease-out
// player body: <Animated.View entering={FadeIn.duration(250)} exiting={FadeOut.duration(250)}>

// Now Playing present (mini-bar expands upward)
// entering={SlideInDown.duration(320)} exiting={SlideOutDown.duration(320)}

// Up-next shelf
// entering={SlideInDown.duration(280)} — pair with a scrim Animated.View FadeIn

// Scrubber drag — thumb grows + recolors
// dragging shared value drives width/color via useAnimatedStyle (80ms in, 150ms out)

// Art backdrop crossfade on track change
// key={artUri} on <Animated.Image entering={FadeIn.duration(400)} />

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); // toggle flip, scrub start/end, queue reorder
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). YouTube Music's iconography maps cleanly to Ionicons.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Samples | `albums` |
| Explore | `search` |
| Library | `library` |
| Dismiss player | `chevron-down` |
| Overflow | `ellipsis-vertical` |
| Play | `play` |
| Pause | `pause` |
| Next track | `play-forward` |
| Previous track | `play-back` |
| Skip to end | `play-skip-forward` |
| Skip to start | `play-skip-back` |
| Queue / up-next | `list` |
| Drag handle | `reorder-three` |
| Shuffle | `shuffle` |
| Repeat | `repeat` |
| Like | `thumbs-up` / `thumbs-up-outline` |
| Cast | `tv-outline` |
| Download | `arrow-down-circle` / `checkmark-circle` |
| Add to playlist | `add` |
| Lyrics | `text` |

## 7. Platform Notes

- **Font**: Roboto is Apache 2.0 — free to bundle. Never substitute SF Pro or a serif; Roboto is the YouTube-family identity
- **Dark-only**: YT Music has no light mode on iOS. Hard-code the dark palette; do not branch on `useColorScheme()`
- **Status bar**: `<StatusBar style="light" />` always — the canvas is near-black
- **Blur backdrop**: prefer a `blurRadius={40}` `Image` for the art glow; `expo-blur`'s `BlurView` blurs *content behind it*, which is not what you want for an art-derived backdrop — blur the artwork image itself
- **Safe area**: wrap the Now Playing screen so the top bar clears the Dynamic Island; the mini-player + tab bar stack must clear the home indicator
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on all timestamps so the scrubber time doesn't jitter mid-playback
- **Dynamic Type**: keep the Song/Video toggle label, tab labels, timestamps, and "UP NEXT" eyebrow at `allowFontScaling={false}` (layout-sensitive)
- **Gestures**: the scrubber and the swipe-up up-next shelf both use `react-native-gesture-handler`; wrap the app root in `GestureHandlerRootView`
- **Video mode**: render the 16:9 player with `expo-av` / `expo-video`; the Song↔Video toggle swaps the media surface without tearing down the audio session (keep playback position)
- **Accessibility**: mark the art-glow backdrop `accessibilityElementsHidden`; expose the scrubber via `accessibilityRole="adjustable"` with `accessibilityActions` for increment/decrement
