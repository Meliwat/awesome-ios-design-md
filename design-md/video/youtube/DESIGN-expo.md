# YouTube (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates YouTube's visual language into paste-ready Expo / React Native code: a design-token module, Subscribe button, video card, mini-player, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-video`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  red:        '#FF0000',
  redPressed: '#CC0000',
  redHover:   '#E60000',

  // Light canvas
  canvas:    '#FFFFFF',
  surface1:  '#F9F9F9',
  surface2:  '#F2F2F2',
  divider:   '#E5E5E5',

  // Dark canvas
  canvasDark:   '#0F0F0F',
  surface1Dark: '#1F1F1F',
  surface2Dark: '#272727',
  surface3Dark: '#3F3F3F',
  dividerDark:  '#303030',

  // Text (light)
  textPrimary:   '#0F0F0F',
  textSecondary: '#606060',
  textTertiary:  '#909090',

  // Text (dark)
  textPrimaryDark:   '#FFFFFF',
  textSecondaryDark: '#AAAAAA',
  textTertiaryDark:  '#717171',

  // Semantic
  infoBlue: '#3EA6FF',
} as const;
```

## 2. Typography

Load YouTube Sans + Roboto via `expo-font`. Fall back to `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'YouTubeSans-Medium': require('../assets/fonts/YouTubeSans-Medium.ttf'),
    'YouTubeSans-Bold':   require('../assets/fonts/YouTubeSans-Bold.ttf'),
    'Roboto-Regular':     require('../assets/fonts/Roboto-Regular.ttf'),
    'Roboto-Medium':      require('../assets/fonts/Roboto-Medium.ttf'),
    'Roboto-Bold':        require('../assets/fonts/Roboto-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const lightBase = { color: '#0F0F0F' } satisfies TextStyle;

export const typography = {
  screenTitle:    { ...lightBase, fontFamily: 'YouTubeSans-Bold',   fontSize: 20, lineHeight: 24, letterSpacing: -0.1 },
  shortsCaption:  { color: '#FFFFFF', fontFamily: 'YouTubeSans-Medium', fontSize: 15, lineHeight: 20 },

  videoDetailTitle: { ...lightBase, fontFamily: 'Roboto-Medium', fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  videoTitle:       { ...lightBase, fontFamily: 'Roboto-Medium', fontSize: 16, lineHeight: 21 },
  channelName:      { ...lightBase, fontFamily: 'Roboto-Medium', fontSize: 14, lineHeight: 18 },
  metadata:         { color: '#606060', fontFamily: 'Roboto-Regular', fontSize: 13, lineHeight: 17 },
  commentBody:      { ...lightBase, fontFamily: 'Roboto-Regular', fontSize: 14, lineHeight: 20 },
  commentAuthor:    { ...lightBase, fontFamily: 'Roboto-Medium',  fontSize: 13, lineHeight: 17 },
  body:             { ...lightBase, fontFamily: 'Roboto-Regular', fontSize: 14, lineHeight: 20 },
  button:           { color: '#FFFFFF', fontFamily: 'Roboto-Medium', fontSize: 14, lineHeight: 18 },
  chip:             { ...lightBase, fontFamily: 'Roboto-Regular', fontSize: 14, lineHeight: 18 },
  tabLabel:         { fontFamily: 'Roboto-Medium',  fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  durationTag:      { color: '#FFFFFF', fontFamily: 'Roboto-Medium', fontSize: 11, lineHeight: 13 },
  timestamp:        { color: '#606060', fontFamily: 'Roboto-Regular', fontSize: 12, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Subscribe Button

```tsx
// components/SubscribeButton.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useDerivedValue, withTiming, useAnimatedStyle, interpolateColor } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { useState } from 'react';

export function SubscribeButton({
  subscribed, onToggle,
}: { subscribed: boolean; onToggle: () => void }) {
  const [bellOn, setBellOn] = useState(false);
  const progress = useDerivedValue(() => withTiming(subscribed ? 1 : 0, { duration: 200 }));

  const pillStyle = useAnimatedStyle(() => ({
    backgroundColor: interpolateColor(progress.value, [0, 1], [colors.red, colors.surface2]),
  }));
  const textStyle = useAnimatedStyle(() => ({
    color: interpolateColor(progress.value, [0, 1], ['#FFFFFF', colors.textPrimary]),
  }));

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
      <Pressable
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
          onToggle();
        }}
      >
        <Animated.View style={[styles.pill, pillStyle]}>
          <Animated.Text style={[typography.button, textStyle]}>
            {subscribed ? 'Subscribed' : 'Subscribe'}
          </Animated.Text>
        </Animated.View>
      </Pressable>

      {subscribed && (
        <Pressable onPress={() => { setBellOn(v => !v); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); }}>
          <View style={[styles.pill, { backgroundColor: colors.surface2, paddingHorizontal: 12 }]}>
            <Ionicons name={bellOn ? 'notifications' : 'notifications-outline'} size={16} color={colors.textPrimary} />
          </View>
        </Pressable>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  pill: { paddingVertical: 8, paddingHorizontal: 16, borderRadius: 18 },
});
```

### Video Card (Feed)

```tsx
// components/VideoCard.tsx
import { Image, Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  thumbnailUri: string;
  duration: string;      // "12:34"
  isLive?: boolean;
  title: string;
  channelName: string;
  channelAvatarUri: string;
  viewCount: string;     // "1.2M views"
  uploadedAgo: string;   // "3 days ago"
  onPress: () => void;
};

export function VideoCard({ thumbnailUri, duration, isLive, title, channelName, channelAvatarUri, viewCount, uploadedAgo, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={styles.card}>
      <View style={styles.thumbWrap}>
        <Image source={{ uri: thumbnailUri }} style={styles.thumb} />
        {isLive ? (
          <View style={styles.liveTag}>
            <View style={styles.liveDot} />
            <Text style={styles.liveText}>LIVE</Text>
          </View>
        ) : (
          <View style={styles.durationTag}>
            <Text style={typography.durationTag}>{duration}</Text>
          </View>
        )}
      </View>

      <View style={styles.meta}>
        <Image source={{ uri: channelAvatarUri }} style={styles.avatar} />
        <View style={styles.textCol}>
          <Text style={typography.videoTitle} numberOfLines={2}>{title}</Text>
          <Text style={typography.metadata} numberOfLines={1}>
            {channelName} · {viewCount} · {uploadedAgo}
          </Text>
        </View>
        <Pressable hitSlop={12} style={{ paddingTop: 2 }}>
          <Ionicons name="ellipsis-vertical" size={20} color={colors.textPrimary} />
        </Pressable>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card:        { paddingBottom: 16 },
  thumbWrap:   { width: '100%', aspectRatio: 16/9, backgroundColor: colors.surface2 },
  thumb:       { width: '100%', height: '100%', resizeMode: 'cover' },
  durationTag: { position: 'absolute', right: 6, bottom: 6, backgroundColor: 'rgba(0,0,0,0.75)', paddingHorizontal: 6, paddingVertical: 4, borderRadius: 4 },
  liveTag:     { position: 'absolute', left: 6, top: 6, flexDirection: 'row', alignItems: 'center', gap: 4, backgroundColor: colors.red, paddingHorizontal: 6, paddingVertical: 4, borderRadius: 4 },
  liveDot:     { width: 6, height: 6, borderRadius: 3, backgroundColor: '#FFFFFF' },
  liveText:    { color: '#FFFFFF', fontFamily: 'Roboto-Bold', fontSize: 11 },
  meta:        { flexDirection: 'row', alignItems: 'flex-start', gap: 12, paddingHorizontal: 16, paddingTop: 12 },
  avatar:      { width: 28, height: 28, borderRadius: 14, backgroundColor: colors.surface2 },
  textCol:     { flex: 1, gap: 4 },
});
```

### Action Pill Row

```tsx
// components/ActionPillRow.tsx
import { Pressable, Text, View, ScrollView, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import * as Haptics from 'expo-haptics';

type Action = { icon: keyof typeof Ionicons.glyphMap; label?: string; onPress: () => void };

export function ActionPillRow({ actions }: { actions: Action[] }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8, paddingHorizontal: 16 }}>
      {actions.map((a, i) => (
        <Pressable key={i} onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); a.onPress(); }} style={styles.pill}>
          <Ionicons name={a.icon} size={20} color={colors.textPrimary} />
          {a.label ? <Text style={[typography.metadata, { color: colors.textPrimary, fontFamily: 'Roboto-Medium' }]}>{a.label}</Text> : null}
        </Pressable>
      ))}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  pill: {
    flexDirection: 'row', alignItems: 'center', gap: 6,
    backgroundColor: colors.surface2,
    paddingHorizontal: 12, paddingVertical: 8,
    borderRadius: 18,
  },
});
```

### Mini-Player

```tsx
// components/MiniPlayer.tsx
import { Image, Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  thumbnailUri: string;
  title: string;
  channelName: string;
  isPlaying: boolean;
  onExpand: () => void;
  onTogglePlay: () => void;
  onDismiss: () => void;
};

export function MiniPlayer({ thumbnailUri, title, channelName, isPlaying, onExpand, onTogglePlay, onDismiss }: Props) {
  return (
    <Pressable onPress={onExpand} style={styles.bar}>
      <Image source={{ uri: thumbnailUri }} style={styles.thumb} />
      <View style={styles.textCol}>
        <Text style={typography.channelName} numberOfLines={1}>{title}</Text>
        <Text style={[typography.metadata, { fontSize: 12 }]} numberOfLines={1}>{channelName}</Text>
      </View>
      <Pressable onPress={onTogglePlay} hitSlop={12}>
        <Ionicons name={isPlaying ? 'pause' : 'play'} size={20} color={colors.textPrimary} />
      </Pressable>
      <Pressable onPress={onDismiss} hitSlop={12} style={{ paddingRight: 12 }}>
        <Ionicons name="close" size={20} color={colors.textPrimary} />
      </Pressable>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  bar:     { flexDirection: 'row', alignItems: 'center', height: 72, backgroundColor: colors.canvas, gap: 12, borderTopWidth: 1, borderTopColor: colors.divider },
  thumb:   { width: 128, height: 72 },
  textCol: { flex: 1, gap: 2 },
});
```

### Filter Chip Row

```tsx
// components/FilterChipRow.tsx
import { Pressable, ScrollView, Text, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FilterChipRow({ chips, selected, onSelect }: { chips: string[]; selected: string; onSelect: (c: string) => void }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8, paddingHorizontal: 12, paddingVertical: 8 }}>
      {chips.map(chip => {
        const isSelected = chip === selected;
        return (
          <Pressable key={chip} onPress={() => onSelect(chip)} style={[styles.chip, isSelected && styles.chipSelected]}>
            <Text style={[typography.chip, isSelected && { color: '#FFFFFF' }]}>{chip}</Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  chip:         { backgroundColor: colors.surface2, paddingHorizontal: 12, paddingVertical: 8, borderRadius: 500 },
  chipSelected: { backgroundColor: colors.textPrimary },
});
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.textPrimary,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarLabelStyle: { fontFamily: 'Roboto-Medium', fontSize: 10, letterSpacing: 0.1 },
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 1, height: 56 + 24 },
      }}
    >
      <Tabs.Screen name="index"        options={{ title: 'Home',          tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'home' : 'home-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="shorts"       options={{ title: 'Shorts',        tabBarIcon: ({ color }) => <Ionicons name="film-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="create"       options={{ title: '',              tabBarIcon: () => <Ionicons name="add-circle-outline" size={30} color={colors.textPrimary} /> }} />
      <Tabs.Screen name="subscriptions" options={{ title: 'Subscriptions', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'albums' : 'albums-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="you"          options={{ title: 'You',           tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person-circle' : 'person-circle-outline'} size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Like bounce
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';

const scale = useSharedValue(1);
const onLike = () => {
  scale.value = withSequence(withSpring(1.15, { damping: 6 }), withSpring(1, { damping: 14 }));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
};
const likeStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

// Double-tap seek ripple (+10s / -10s overlay)
// Use a PanGestureHandler or onTouchEnd with detection of double-tap,
// then show a fade-in/out Animated.View on the left or right half

// Mini-player ← → full player expansion
// Use a BottomSheet library (e.g. @gorhom/bottom-sheet) or a custom Reanimated-driven shared value
// that controls the height interpolation from 72 → screen height
```

## 6. Icon Library

Use `@expo/vector-icons`. Ionicons covers most; use `MaterialCommunityIcons` for the Shorts glyph if needed.

| Purpose | Ionicons |
|---------|----------|
| Play / Pause | `play` / `pause` |
| Next / Previous | `play-forward` / `play-back` |
| Like (thumbs-up) | `thumbs-up-outline` / `thumbs-up` |
| Dislike | `thumbs-down-outline` / `thumbs-down` |
| Share | `share-social-outline` / `arrow-redo-outline` |
| Download | `download-outline` / `download` |
| Save | `bookmark-outline` / `bookmark` |
| Comment | `chatbubble-outline` |
| Subscribe bell | `notifications-outline` / `notifications` |
| Cast | `tv-outline` |
| Notifications | `notifications-outline` |
| Search | `search` |
| Home | `home-outline` / `home` |
| Shorts | `film-outline` / `film` |
| Create (+) | `add-circle-outline` |
| Subscriptions | `albums-outline` / `albums` |
| You | `person-circle-outline` / `person-circle` |
| Back | `chevron-back` |
| More | `ellipsis-vertical` |
| Captions | `chatbox-outline` |
| Fullscreen | `expand-outline` |
| Close | `close` |

## 7. Platform Notes

- **Dark mode support**: `useColorScheme()` to swap `canvas`, `surface1/2`, `textPrimary/Secondary`, and `divider`. Red stays `#FF0000` in both modes.
- **Status bar**: set `<StatusBar style={colorScheme === 'dark' ? 'light' : 'dark'} />` globally.
- **Video playback**: use `expo-video` (new API, iOS 17+) for the player; it supports background audio, PiP, and fullscreen landscape rotation.
- **Picture-in-Picture / mini-player**: on iOS, `expo-video` supports system PiP; for the in-app mini-player use a `BottomSheet` from `@gorhom/bottom-sheet` or a Reanimated shared value.
- **Shorts swipe**: use `PagerView` or `FlatList` with `pagingEnabled={true}` and `snapToInterval={screenHeight}`. Respect safe area at the top (Dynamic Island) and bottom (home indicator).
- **Dynamic Type**: enabled by default; set `allowFontScaling={false}` on duration tags, Subscribe text, and tab labels where layout breaks.
- **Accessibility**: every video card needs `accessibilityRole="button"` and `accessibilityLabel={`${title} by ${channelName}, ${viewCount}, uploaded ${uploadedAgo}`}`. Subscribe button has its own label that switches with state.
- **Haptics**: light on Subscribe / Like / tab change; success on share confirm; selection on filter chip change.

## 8. Dark Mode Theme Helper

```ts
// theme/useTheme.ts
import { useColorScheme } from 'react-native';
import { colors } from './colors';

export function useTheme() {
  const scheme = useColorScheme() ?? 'light';
  const dark = scheme === 'dark';
  return {
    canvas:        dark ? colors.canvasDark   : colors.canvas,
    surface1:      dark ? colors.surface1Dark : colors.surface1,
    surface2:      dark ? colors.surface2Dark : colors.surface2,
    divider:       dark ? colors.dividerDark  : colors.divider,
    textPrimary:   dark ? colors.textPrimaryDark   : colors.textPrimary,
    textSecondary: dark ? colors.textSecondaryDark : colors.textSecondary,
    isDark: dark,
  };
}
```
