# Netflix (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Netflix's visual language into paste-ready Expo / React Native code: a design-token module, Play button, poster rows, Top 10 numeral overlay, and Hero trailer auto-play.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-video`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  red:        '#E50914',
  redPressed: '#B7070F',
  redDimmed:  '#831010',

  // Canvas
  canvas:     '#141414',
  deepBlack:  '#000000',
  surface1:   '#1F1F1F',
  surface2:   '#2A2A2A',
  surface3:   '#3A3A3A',
  divider:    '#2B2B2B',
  input:      '#333333',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#AAAAAA',
  textTertiary:  '#777777',

  // Profile rotation
  profileRed:    '#E50914',
  profileBlue:   '#3E3E91',
  profileYellow: '#F5D85C',
  profileGreen:  '#4B8A3E',
  kidsOrange:    '#F8981D',

  // Semantic
  info: '#54B9C5',
} as const;
```

## 2. Typography

Load Netflix Sans via `expo-font`. Fall back to `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'NetflixSans-Regular': require('../assets/fonts/NetflixSans-Regular.otf'),
    'NetflixSans-Medium':  require('../assets/fonts/NetflixSans-Medium.otf'),
    'NetflixSans-Bold':    require('../assets/fonts/NetflixSans-Bold.otf'),
    'NetflixSans-Black':   require('../assets/fonts/NetflixSans-Black.otf'),
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
  titleHero:       { ...base, fontFamily: 'NetflixSans-Bold',    fontSize: 32, lineHeight: 35, letterSpacing: -0.4 },
  screenTitle:     { ...base, fontFamily: 'NetflixSans-Bold',    fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  rowHeader:       { ...base, fontFamily: 'NetflixSans-Bold',    fontSize: 17, lineHeight: 20, letterSpacing: -0.1 },
  episodeTitle:    { ...base, fontFamily: 'NetflixSans-Medium',  fontSize: 17, lineHeight: 20 },
  body:            { ...base, fontFamily: 'NetflixSans-Regular', fontSize: 14, lineHeight: 20 },
  metadata:        { color: '#AAAAAA', fontFamily: 'NetflixSans-Regular', fontSize: 13, lineHeight: 17 },
  metadataSm:      { color: '#AAAAAA', fontFamily: 'NetflixSans-Regular', fontSize: 12, lineHeight: 16 },
  buttonPlay:      { color: '#FFFFFF', fontFamily: 'NetflixSans-Bold',    fontSize: 17, lineHeight: 20 },
  buttonSecondary: { color: '#FFFFFF', fontFamily: 'NetflixSans-Medium',  fontSize: 15, lineHeight: 18 },
  badge:           { color: '#FFFFFF', fontFamily: 'NetflixSans-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  tabLabel:        { fontFamily: 'NetflixSans-Medium',  fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  cert:            { color: '#FFFFFF', fontFamily: 'NetflixSans-Bold',    fontSize: 12, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Play Button

```tsx
// components/PlayButton.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PlayButton({ title = 'Play', onPress }: { title?: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
      style={({ pressed }) => [
        styles.btn,
        { backgroundColor: pressed ? colors.redPressed : colors.red, transform: [{ scale: pressed ? 0.98 : 1 }] },
      ]}
    >
      <Ionicons name="play" size={20} color="#FFFFFF" />
      <Text style={typography.buttonPlay}>{title}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  btn: { flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8, paddingVertical: 14, paddingHorizontal: 24, borderRadius: 4 },
});
```

### Secondary Button (My List / Info / Trailer)

```tsx
// components/SecondaryButton.tsx
import { Pressable, Text, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SecondaryButton({
  icon, title, onPress,
}: { icon: keyof typeof Ionicons.glyphMap; title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [styles.btn, pressed && { backgroundColor: 'rgba(255,255,255,0.25)' }]}
    >
      <Ionicons name={icon} size={16} color="#FFFFFF" />
      <Text style={typography.buttonSecondary}>{title}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  btn: { flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 8, paddingVertical: 12, paddingHorizontal: 20, borderRadius: 4, backgroundColor: 'rgba(255,255,255,0.15)' },
});
```

### Poster Tile (2:3)

```tsx
// components/PosterTile.tsx
import { Image, Pressable, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';

export function PosterTile({
  imageUri, width = 130, progress, onPress,
}: { imageUri: string; width?: number; progress?: number; onPress: () => void }) {
  const height = width * 1.5;

  return (
    <Pressable onPress={onPress} style={{ width, height, borderRadius: 4, overflow: 'hidden', backgroundColor: colors.surface1 }}>
      <Image source={{ uri: imageUri }} style={{ width: '100%', height: '100%', resizeMode: 'cover' }} />
      {typeof progress === 'number' && (
        <View style={styles.progressTrack}>
          <View style={[styles.progressFill, { width: `${Math.round(progress * 100)}%` }]} />
        </View>
      )}
    </Pressable>
  );
}

const styles = StyleSheet.create({
  progressTrack: { position: 'absolute', left: 4, right: 4, bottom: 4, height: 2, backgroundColor: 'rgba(255,255,255,0.3)' },
  progressFill:  { height: 2, backgroundColor: colors.red },
});
```

### Poster Row

```tsx
// components/PosterRow.tsx
import { ScrollView, Text, View } from 'react-native';
import { PosterTile } from './PosterTile';
import { typography } from '../theme/typography';

export function PosterRow({
  title, items, onSelect,
}: {
  title: string;
  items: Array<{ id: string; imageUri: string; progress?: number }>;
  onSelect: (id: string) => void;
}) {
  return (
    <View style={{ marginVertical: 8 }}>
      <Text style={[typography.rowHeader, { paddingHorizontal: 16, marginBottom: 8 }]}>{title}</Text>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8, paddingHorizontal: 16 }}>
        {items.map(item => (
          <PosterTile key={item.id} imageUri={item.imageUri} progress={item.progress} onPress={() => onSelect(item.id)} />
        ))}
      </ScrollView>
    </View>
  );
}
```

### Top 10 Row (with Giant Numerals)

```tsx
// components/Top10Row.tsx
import { ScrollView, Text, View, StyleSheet, Pressable, Image } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Top10Row({
  items, onSelect,
}: { items: Array<{ id: string; imageUri: string }>; onSelect: (id: string) => void }) {
  return (
    <View style={{ marginVertical: 8 }}>
      <Text style={[typography.rowHeader, { paddingHorizontal: 16, marginBottom: 8 }]}>
        Top 10 TV Shows in the U.S. Today
      </Text>
      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 16 }}>
        {items.slice(0, 10).map((item, i) => (
          <Pressable key={item.id} onPress={() => onSelect(item.id)} style={styles.item}>
            <Text style={styles.numeral}>{i + 1}</Text>
            <Image source={{ uri: item.imageUri }} style={styles.poster} />
          </Pressable>
        ))}
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  item:    { flexDirection: 'row', alignItems: 'flex-end', marginRight: 8 },
  numeral: {
    fontFamily: 'NetflixSans-Black', fontSize: 160, lineHeight: 160,
    color: colors.canvas,
    // Stroke emulation via text shadow in the canvas color with offsets; refine with a native module if needed
    textShadowColor: 'rgba(255,255,255,0.18)', textShadowOffset: { width: 0, height: 0 }, textShadowRadius: 1,
    marginRight: -24, // poster overlaps the numeral
    zIndex: 0,
  },
  poster:  { width: 120, height: 180, borderRadius: 4, zIndex: 1, backgroundColor: colors.surface1 },
});
```

**Note**: A stroked-outline numeral is tricky in plain React Native; for a pixel-accurate Netflix look, render the numeral in SVG with `stroke` + `fill="none"`, sized to 160pt height, or use `react-native-skia` for crisp outlined type.

### Hero with Trailer Auto-Play

```tsx
// components/HeroCard.tsx
import { Image, View, Text, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { VideoView, useVideoPlayer } from 'expo-video';
import { useEffect, useState } from 'react';
import { PlayButton } from './PlayButton';
import { SecondaryButton } from './SecondaryButton';
import { colors } from '../theme/colors';

type Props = {
  keyArtUri: string;
  trailerUri?: string;
  titleLogoUri: string;
  genres: string[];
  onPlay: () => void;
  onMyList: () => void;
  onInfo: () => void;
};

export function HeroCard({ keyArtUri, trailerUri, titleLogoUri, genres, onPlay, onMyList, onInfo }: Props) {
  const [showTrailer, setShowTrailer] = useState(false);
  const player = useVideoPlayer(trailerUri ?? null, p => { p.muted = true; p.loop = true; });

  useEffect(() => {
    if (!trailerUri) return;
    const t = setTimeout(() => {
      setShowTrailer(true);
      player.play();
    }, 2000);
    return () => clearTimeout(t);
  }, [trailerUri]);

  return (
    <View style={styles.hero}>
      {showTrailer && trailerUri ? (
        <VideoView player={player} style={StyleSheet.absoluteFillObject} allowsFullscreen={false} />
      ) : (
        <Image source={{ uri: keyArtUri }} style={StyleSheet.absoluteFillObject} resizeMode="cover" />
      )}

      <LinearGradient
        colors={['rgba(0,0,0,0)', colors.canvas]}
        style={styles.gradient}
      />

      <View style={styles.bottom}>
        <Image source={{ uri: titleLogoUri }} style={styles.titleLogo} resizeMode="contain" />
        <View style={styles.genreRow}>
          {genres.map((g, i) => (
            <View key={g} style={{ flexDirection: 'row', alignItems: 'center' }}>
              <Text style={{ color: '#FFFFFF', fontSize: 13, fontFamily: 'NetflixSans-Medium' }}>{g}</Text>
              {i < genres.length - 1 ? (
                <Text style={{ color: colors.textSecondary, marginHorizontal: 6 }}>•</Text>
              ) : null}
            </View>
          ))}
        </View>
        <View style={styles.buttonRow}>
          <View style={{ flex: 1 }}><SecondaryButton icon="add"        title="My List" onPress={onMyList} /></View>
          <View style={{ flex: 1 }}><PlayButton onPress={onPlay} /></View>
          <View style={{ flex: 1 }}><SecondaryButton icon="information-circle-outline" title="Info" onPress={onInfo} /></View>
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  hero:       { width: '100%', height: 440, backgroundColor: colors.deepBlack, overflow: 'hidden' },
  gradient:   { position: 'absolute', left: 0, right: 0, bottom: 0, height: 200 },
  bottom:     { position: 'absolute', left: 0, right: 0, bottom: 16, alignItems: 'center', gap: 12 },
  titleLogo:  { width: '60%', height: 80 },
  genreRow:   { flexDirection: 'row', alignItems: 'center' },
  buttonRow:  { flexDirection: 'row', gap: 12, paddingHorizontal: 16, width: '100%' },
});
```

### NETFLIX Wordmark Header

```tsx
// components/NetflixHeader.tsx
import { Image, ScrollView, View, Pressable, Text, StyleSheet } from 'react-native';
import { useState } from 'react';

export function NetflixHeader({ chips }: { chips: string[] }) {
  const [selected, setSelected] = useState<string | null>(null);

  return (
    <View style={{ paddingVertical: 12, gap: 12 }}>
      <Image source={require('../assets/netflix-wordmark.png')} style={styles.wordmark} resizeMode="contain" />

      <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8, paddingHorizontal: 16 }}>
        {chips.map(chip => (
          <Pressable key={chip} onPress={() => setSelected(selected === chip ? null : chip)} style={styles.chip}>
            <Text style={styles.chipText}>{chip}</Text>
          </Pressable>
        ))}
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  wordmark: { height: 22, width: 110, marginLeft: 16 },
  chip:     { paddingHorizontal: 16, paddingVertical: 6, borderWidth: 1, borderColor: 'rgba(255,255,255,0.4)', borderRadius: 500 },
  chipText: { color: '#FFFFFF', fontFamily: 'NetflixSans-Medium', fontSize: 14 },
});
```

### Profile Picker

```tsx
// app/profile-picker.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { router } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

type Profile = { id: string; name: string; color: string };

const PROFILES: Profile[] = [
  { id: '1', name: 'Me',     color: colors.profileRed },
  { id: '2', name: 'Jordan', color: colors.profileBlue },
  { id: '3', name: 'Sam',    color: colors.profileYellow },
  { id: '4', name: 'Kids',   color: colors.kidsOrange },
];

export default function ProfilePicker() {
  return (
    <View style={styles.screen}>
      <Text style={styles.title}>Who's watching?</Text>
      <View style={styles.grid}>
        {PROFILES.map(p => (
          <Pressable key={p.id} onPress={() => router.replace('/(tabs)')} style={styles.card}>
            <View style={[styles.avatar, { backgroundColor: p.color }]}>
              <Ionicons name="person" size={40} color="#FFFFFF" />
            </View>
            <Text style={styles.name}>{p.name}</Text>
          </Pressable>
        ))}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  screen: { flex: 1, backgroundColor: colors.canvas, alignItems: 'center', justifyContent: 'center', gap: 48 },
  title:  { color: '#FFFFFF', fontFamily: 'NetflixSans-Bold', fontSize: 32 },
  grid:   { flexDirection: 'row', flexWrap: 'wrap', gap: 24, justifyContent: 'center', maxWidth: 360 },
  card:   { alignItems: 'center', gap: 12, width: 140 },
  avatar: { width: 80, height: 80, borderRadius: 40, alignItems: 'center', justifyContent: 'center' },
  name:   { color: '#FFFFFF', fontFamily: 'NetflixSans-Medium', fontSize: 14 },
});
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
        tabBarActiveTintColor: '#FFFFFF',
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarLabelStyle: { fontFamily: 'NetflixSans-Medium', fontSize: 10, letterSpacing: 0.2 },
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent', height: 48 + 24 },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(20,20,20,0.6)' }} />
        ),
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',       tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'home' : 'home-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="new-hot"   options={{ title: 'New & Hot',  tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'play-circle' : 'play-circle-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="my-netflix" options={{ title: 'My Netflix', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person-circle' : 'person-circle-outline'} size={26} color={color} /> }} />
      <Tabs.Screen name="downloads" options={{ title: 'Downloads',  tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'arrow-down-circle' : 'arrow-down-circle-outline'} size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// My List add/remove — plus rotates 90° + cross-fades to checkmark
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useState } from 'react';

export function MyListButton() {
  const [added, setAdded] = useState(false);
  const rotation = useSharedValue(0);

  const style = useAnimatedStyle(() => ({ transform: [{ rotate: `${rotation.value}deg` }] }));

  const toggle = () => {
    rotation.value = withSpring(added ? 0 : 90, { damping: 14 });
    setAdded(!added);
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  };

  return (
    <Animated.View style={style}>
      <Ionicons name={added ? 'checkmark' : 'add'} size={24} color="#FFFFFF" onPress={toggle} />
    </Animated.View>
  );
}

// Poster → Detail shared element
// Use react-native-shared-element or expo-router's Modal group with custom animation-based height interpolation
```

## 6. Icon Library

`@expo/vector-icons` (Ionicons) covers most Netflix needs.

| Purpose | Ionicons |
|---------|----------|
| Play | `play` |
| Pause | `pause` |
| Plus / My List | `add` |
| Checkmark (added) | `checkmark` |
| Info | `information-circle-outline` |
| Trailer | `film-outline` |
| Download | `arrow-down-outline` |
| Downloaded | `checkmark-circle` |
| Share | `share-outline` |
| Thumb up | `thumbs-up` / `thumbs-up-outline` |
| Thumb down | `thumbs-down` / `thumbs-down-outline` |
| Search | `search` |
| Home | `home-outline` / `home` |
| New & Hot | `play-circle-outline` / `play-circle` |
| My Netflix | `person-circle-outline` / `person-circle` |
| Downloads | `arrow-down-circle-outline` / `arrow-down-circle` |
| Back | `chevron-back` |
| More | `ellipsis-horizontal` |
| Close (modal) | `close` |
| Speaker (audio toggle) | `volume-mute` / `volume-high` |
| Fullscreen | `expand-outline` |

## 7. Platform Notes

- **Dark-only**: Netflix iOS has no light mode; ignore `useColorScheme()` or force dark theme.
- **Status bar**: `<StatusBar style="light" />` globally.
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; NETFLIX wordmark sits 12pt below top inset.
- **Video**: use `expo-video` (iOS 17+) for both the hero trailer (muted auto-play) and the fullscreen player. Supports picture-in-picture and audio in the background.
- **Hero trailer**: 2s delay, muted, loops silently; user tap on speaker icon unmutes; pauses on app background.
- **Top 10 numerals**: for pixel-accurate outlined numerals, use `react-native-svg` with a `<Text>` element + `stroke` + `fill="none"`, rather than CSS-like text-shadow trickery.
- **Status bar while player is in fullscreen**: hide with `<StatusBar hidden />`.
- **Orientation**: lock app to portrait; the player modal unlocks to landscape when fullscreen.
- **Accessibility**: group poster + title into one tap target; expose Play and "Add to My List" as accessibility actions; on Continue Watching posters, announce progress percentage.
- **Haptics**: medium on Play, success on My List add, heavy on double-thumbs-up Love, selection on chip tap.

## 8. Theme Export

```ts
// theme/index.ts
export { colors } from './colors';
export { typography } from './typography';

// Always dark — Netflix iOS ships no light theme
export const theme = {
  canvas:        '#141414',
  surface1:      '#1F1F1F',
  surface2:      '#2A2A2A',
  textPrimary:   '#FFFFFF',
  textSecondary: '#AAAAAA',
  red:           '#E50914',
  radius: { poster: 4, sheet: 12, chip: 500 },
};
```
