# Amazon Music (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Amazon Music's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — the primary mode)
  canvas:      '#0C1B22',
  surface1:    '#122A33',
  surface2:    '#1A3742',
  divider:     '#234653',
  gradientTop: '#16404C',
  tabBar:      '#081218',

  // Brand
  cyan:        '#00A8E1',
  cyanBright:  '#25D1DA',
  cyanPressed: '#0086B3',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#9FB6BF',
  textTertiary:  '#6B8693',
  onBright:      '#042730',

  // Semantic
  success: '#2EC4B6',
  error:   '#FF6B6B',

  // X-Ray panel
  xrayFill:   'rgba(0,168,225,0.07)',
  xrayBorder: 'rgba(37,209,218,0.22)',
} as const;

export type AmazonColor = keyof typeof colors;

// Player background gradient stops (top → bottom)
export const playerGradient = ['#1C4A57', '#163A45', '#0F2730', '#0C1B22'] as const;
```

## 2. Typography

Load **Amazon Ember** via `expo-font`; the documented fallback is Inter (its metrics track Ember).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'AmazonEmber-Regular': require('../assets/fonts/AmazonEmber-Regular.ttf'),
    'AmazonEmber-Medium':  require('../assets/fonts/AmazonEmber-Medium.ttf'),
    'AmazonEmber-Bold':    require('../assets/fonts/AmazonEmber-Bold.ttf'),
    'AmazonEmber-Heavy':   require('../assets/fonts/AmazonEmber-Heavy.ttf'),
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
  greeting:    { ...white, fontFamily: 'AmazonEmber-Heavy',   fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  playerTitle: { ...white, fontFamily: 'AmazonEmber-Bold',    fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:     { ...white, fontFamily: 'AmazonEmber-Bold',    fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  cardTitle:   { ...white, fontFamily: 'AmazonEmber-Bold',    fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:        { ...white, fontFamily: 'AmazonEmber-Regular', fontSize: 16, lineHeight: 24 },
  rowTitle:    { ...white, fontFamily: 'AmazonEmber-Medium',  fontSize: 15, lineHeight: 20 },
  lyricLine:   { fontFamily: 'AmazonEmber-Medium',  fontSize: 15, lineHeight: 23 },
  subtitle:    { color: '#9FB6BF', fontFamily: 'AmazonEmber-Regular', fontSize: 14, lineHeight: 19 },
  xrayLabel:   { color: '#9FB6BF', fontFamily: 'AmazonEmber-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.6 },
  xrayBadge:   { color: '#042730', fontFamily: 'AmazonEmber-Heavy', fontSize: 9, lineHeight: 9, letterSpacing: 0.5 },
  chip:        { ...white, fontFamily: 'AmazonEmber-Medium', fontSize: 13, lineHeight: 13 },
  timestamp:   { color: '#9FB6BF', fontFamily: 'AmazonEmber-Regular', fontSize: 11, lineHeight: 11, fontVariant: ['tabular-nums'] as const },
  tab:         { fontFamily: 'AmazonEmber-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:      { fontFamily: 'AmazonEmber-Bold',   fontSize: 15, lineHeight: 15 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Full-Screen Player (gradient + art + meta + X-Ray)

```tsx
// components/Player.tsx
import { Image, View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, playerGradient } from '../theme/colors';
import { typography } from '../theme/typography';
import { XRayLyricsPanel } from './XRayLyricsPanel';

export function Player({ artUri, track, artist }: { artUri: string; track: string; artist: string }) {
  return (
    <LinearGradient colors={playerGradient as unknown as string[]} style={{ flex: 1 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 20, paddingTop: 6 }}>
        <Ionicons name="chevron-down" size={22} color={colors.textPrimary} />
        <Text style={[typography.xrayLabel, { letterSpacing: 0.7 }]}>PLAYING FROM PLAYLIST</Text>
        <Ionicons name="ellipsis-vertical" size={22} color={colors.textPrimary} />
      </View>

      <Image source={{ uri: artUri }} style={{ width: 196, height: 196, borderRadius: 6, alignSelf: 'center', marginTop: 18 }} />

      <View style={{ flexDirection: 'row', alignItems: 'flex-end', justifyContent: 'space-between', paddingHorizontal: 22, marginTop: 16 }}>
        <View>
          <Text style={typography.playerTitle}>{track}</Text>
          <Text style={[typography.subtitle, { marginTop: 3 }]}>{artist}</Text>
        </View>
        <Ionicons name="add" size={26} color={colors.cyanBright} />
      </View>

      <XRayLyricsPanel
        lines={['Daylight, I wake up feeling like', 'But stay woke', "Niggas creepin'", 'They gon’ find you']}
        currentIndex={1}
      />
    </LinearGradient>
  );
}
```

### X-Ray Lyrics Panel (Signature)

```tsx
// components/XRayLyricsPanel.tsx
import { View, Text, Pressable } from 'react-native';
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function XRayLyricsPanel({ lines, currentIndex, onSeekLine }: {
  lines: string[];
  currentIndex: number;
  onSeekLine?: (i: number) => void;
}) {
  const colorFor = (i: number) =>
    i === currentIndex ? colors.cyanBright
      : i === currentIndex + 1 ? colors.textSecondary
      : colors.textTertiary;

  return (
    <View style={{
      marginHorizontal: 18, marginTop: 16,
      backgroundColor: colors.xrayFill,
      borderWidth: 1, borderColor: colors.xrayBorder,
      borderRadius: 10, paddingHorizontal: 16, paddingVertical: 14,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 7, marginBottom: 10 }}>
        <View style={{ backgroundColor: colors.cyanBright, borderRadius: 4, paddingHorizontal: 7, paddingVertical: 3 }}>
          <Text style={typography.xrayBadge}>X-RAY</Text>
        </View>
        <Text style={typography.xrayLabel}>LYRICS</Text>
      </View>
      {lines.map((line, i) => (
        <Pressable key={i} onPress={() => onSeekLine?.(i)}>
          <Text style={[typography.lyricLine, { color: colorFor(i), paddingVertical: 3 }]}>{line}</Text>
        </Pressable>
      ))}
    </View>
  );
}
```

### Scrubber

```tsx
// components/Scrubber.tsx
import { useState } from 'react';
import { View, Text, LayoutChangeEvent } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useAnimatedStyle, useSharedValue, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Scrubber() {
  const [w, setW] = useState(0);
  const progress = useSharedValue(0.38);
  const dragging = useSharedValue(0);

  const fill = useAnimatedStyle(() => ({ width: progress.value * w }));
  const thumb = useAnimatedStyle(() => ({
    left: progress.value * w - 6.5,
    transform: [{ scale: dragging.value ? 1.18 : 1 }],
  }));

  const pan = Gesture.Pan()
    .onBegin(() => { dragging.value = withTiming(1, { duration: 80 }); })
    .onUpdate((e) => { progress.value = Math.min(1, Math.max(0, e.x / w)); })
    .onFinalize(() => { dragging.value = withTiming(0, { duration: 150 }); });

  return (
    <View style={{ paddingHorizontal: 22, marginTop: 16 }}>
      <GestureDetector gesture={pan}>
        <View onLayout={(e: LayoutChangeEvent) => setW(e.nativeEvent.layout.width)} style={{ height: 16, justifyContent: 'center' }}>
          <View style={{ height: 4, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.16)' }} />
          <Animated.View style={[{ position: 'absolute', height: 4, borderRadius: 2, backgroundColor: colors.cyan }, fill]} />
          <Animated.View style={[{ position: 'absolute', width: 13, height: 13, borderRadius: 999, backgroundColor: colors.cyan }, thumb]} />
        </View>
      </GestureDetector>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 7 }}>
        <Text style={typography.timestamp}>1:54</Text>
        <Text style={typography.timestamp}>-3:33</Text>
      </View>
    </View>
  );
}
```

### Transport Controls (cyan play button with glow)

```tsx
// components/Transport.tsx
import { useState } from 'react';
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function Transport() {
  const [playing, setPlaying] = useState(true);
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 30, marginTop: 12 }}>
      <Ionicons name="repeat" size={22} color={colors.textSecondary} />
      <Ionicons name="play-back" size={24} color={colors.textPrimary} />
      <Pressable
        onPress={() => setPlaying((p) => !p)}
        style={{
          width: 60, height: 60, borderRadius: 30, backgroundColor: colors.cyan,
          alignItems: 'center', justifyContent: 'center',
          shadowColor: colors.cyan, shadowOpacity: 0.55, shadowRadius: 11, shadowOffset: { width: 0, height: 8 },
          elevation: 8,
        }}
      >
        <Ionicons name={playing ? 'pause' : 'play'} size={24} color="#FFFFFF" />
      </Pressable>
      <Ionicons name="play-forward" size={24} color={colors.textPrimary} />
      <Ionicons name="shuffle" size={22} color={colors.textSecondary} />
    </View>
  );
}
```

### Home Shelf (time-of-day greeting + carousels)

```tsx
// components/HomeShelf.tsx
import { ScrollView, View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Card = { title: string; subtitle: string };

export function HomeShelf({ greeting, cards }: { greeting: string; cards: Card[] }) {
  return (
    <ScrollView style={{ backgroundColor: colors.canvas }} contentContainerStyle={{ paddingVertical: 16 }}>
      <Text style={[typography.greeting, { paddingHorizontal: 18 }]}>{greeting}</Text>
      <Text style={[typography.section, { paddingHorizontal: 18, marginTop: 24 }]}>Recently played</Text>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 18, gap: 8, marginTop: 12 }}>
        {cards.map((c) => (
          <View key={c.title} style={{ width: 150 }}>
            <LinearGradient colors={[colors.cyan, colors.surface1]} style={{ width: 150, height: 150, borderRadius: 6 }} />
            <Text style={[typography.cardTitle, { marginTop: 8 }]}>{c.title}</Text>
            <Text style={typography.subtitle}>{c.subtitle}</Text>
          </View>
        ))}
      </ScrollView>
    </ScrollView>
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
        tabBarActiveTintColor: colors.cyanBright,   // BRIGHT cyan, no tint pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.tabBar, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'AmazonEmber-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home" size={22} color={color} /> }} />
      <Tabs.Screen name="find"     options={{ title: 'Find',     tabBarIcon: ({ color }) => <Ionicons name="search" size={22} color={color} /> }} />
      <Tabs.Screen name="podcasts" options={{ title: 'Podcasts', tabBarIcon: ({ color }) => <Ionicons name="mic" size={22} color={color} /> }} />
      <Tabs.Screen name="library"  options={{ title: 'Library',  tabBarIcon: ({ color }) => <Ionicons name="library" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Player present/dismiss (mini-bar expands upward)
// entering={SlideInDown.duration(300)} exiting={SlideOutDown.duration(300)}

// X-Ray lyric recolor + auto-scroll
// drive line color from `currentIndex` (withTiming 180ms); ScrollView ref.scrollTo to center current line

// X-Ray seek tap — tapped line pulses bright cyan
// brief withTiming color to cyanBright then back via state

// Scrubber drag — thumb scales on touch-down
// dragging shared value (80ms in / 150ms out)

// Gradient + art crossfade on track change
// key={artUri} on <Animated.Image entering={FadeIn.duration(350)} />

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); // play/pause, lyric tap-to-seek, scrub start
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Amazon Music's iconography maps cleanly.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Find | `search` |
| Podcasts | `mic` |
| Library | `library` |
| Dismiss player | `chevron-down` |
| Overflow | `ellipsis-vertical` |
| Play / Pause | `play` / `pause` |
| Next / Previous | `play-forward` / `play-back` |
| Repeat | `repeat` |
| Shuffle | `shuffle` |
| Add to library | `add` / `checkmark` |
| Queue | `list` |
| Lyrics toggle | `chatbox-ellipses-outline` |
| Equalizer / Atmos | `options-outline` |
| Cast / devices | `tv-outline` |
| Search | `search` |
| Download | `arrow-down-circle` / `checkmark-circle` |
| Like | `heart` / `heart-outline` |

## 7. Platform Notes

- **Font**: bundle Amazon Ember; the only documented substitute is Inter (its proportions track Ember so layouts transfer faithfully). Never substitute a serif
- **Dark-first**: Amazon Music is dark-first on iOS. Hard-code the teal-tinted dark palette; do not neutralize any surface toward grey
- **Status bar**: `<StatusBar style="light" />` always — the canvas is dark teal-navy
- **Player gradient**: use `expo-linear-gradient` with the four `playerGradient` stops top→bottom; the art and X-Ray panel sit over it
- **X-Ray auto-scroll**: keep the lyrics in a `ScrollView` with a ref; on `currentIndex` change call `scrollTo` so the current line stays vertically centered; recolor with Reanimated `withTiming(180)`
- **Safe area**: wrap the player so the top bar clears the Dynamic Island; the mini-player + tab bar stack must clear the home indicator
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on all timestamps so the scrubber time doesn't jitter
- **Dynamic Type**: keep the "X-RAY" badge, X-Ray label, tab labels, and timestamps at `allowFontScaling={false}`; let the lyric panel grow and re-center as lines scale
- **Gestures**: the scrubber uses `react-native-gesture-handler`; wrap the app root in `GestureHandlerRootView`
- **Accessibility**: expose each lyric line with `accessibilityRole="button"` and `accessibilityHint="Plays from this line"`; the scrubber as `accessibilityRole="adjustable"`; ensure the "X-RAY" badge keeps its dark-on-bright contrast
