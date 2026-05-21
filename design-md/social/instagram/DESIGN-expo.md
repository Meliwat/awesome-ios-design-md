# Instagram (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md). Paste-ready Expo / React Native code: design tokens, components, and the signature brand gradient.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-blur`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:      '#FFFFFF',
  canvasDark:  '#000000',
  elevated:    '#121212',
  surfaceInputLight: '#EFEFEF',
  surfaceInputDark:  '#262626',
  dividerLight: '#DBDBDB',
  dividerDark:  '#262626',

  text:        '#000000',
  textDark:    '#FFFFFF',
  secondary:   '#8E8E8E',
  secondaryDark: '#A8A8A8',

  actionBlue:     '#0095F6',
  actionPressed:  '#1877F2',
  destructive:    '#ED4956',
  linkLight:      '#00376B',
  linkDark:       '#E0F1FF',

  onlineGreen:   '#78DE45',
  closeFriends:  '#2FB825',
} as const;

// Full 10-stop brand gradient
export const brandGradient = [
  '#FFDC80', '#FCAF45', '#F77737', '#F56040',
  '#FD1D1D', '#E1306C', '#C13584', '#833AB4',
  '#5851DB', '#405DE6',
];

// Short 3-stop version for UI moments
export const brandGradientShort = ['#FCAF45', '#FD1D1D', '#833AB4'];
```

## 2. Typography

Instagram uses SF Pro (system default). Load a script font for the logotype.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Billabong': require('../assets/fonts/Billabong.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  logotype:      { fontFamily: 'Billabong', fontSize: 28, lineHeight: 28 },
  screenTitle:   { fontSize: 16, fontWeight: '600' as const, letterSpacing: -0.2 },
  usernameLarge: { fontSize: 20, fontWeight: '700' as const, letterSpacing: -0.3 },
  username:      { fontSize: 14, fontWeight: '600' as const, letterSpacing: -0.15 },
  bio:           { fontSize: 14, fontWeight: '400' as const },
  caption:       { fontSize: 14, fontWeight: '400' as const, lineHeight: 19 },
  secondaryMeta: { fontSize: 12, fontWeight: '400' as const, color: '#8E8E8E' },
  buttonPrimary: { fontSize: 14, fontWeight: '600' as const, color: '#fff' },
  counterLarge:  { fontSize: 16, fontWeight: '700' as const, fontVariant: ['tabular-nums' as const] },
  dmBubble:      { fontSize: 16, fontWeight: '400' as const, lineHeight: 21 },
  badge:         { fontSize: 11, fontWeight: '700' as const, fontVariant: ['tabular-nums' as const] },
  timestamp:     { fontSize: 11, fontWeight: '400' as const, letterSpacing: 0.5, textTransform: 'uppercase' as const, color: '#8E8E8E' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Story Ring (with brand gradient)

```tsx
// components/StoryRing.tsx
import { View, Image, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient, colors } from '../theme/colors';

export function StoryRing({
  uri, isUnread, size = 66,
}: { uri: string; isUnread: boolean; size?: number }) {
  const inner = size - 8;
  const Ring = isUnread
    ? (
      <LinearGradient
        colors={brandGradient}
        start={{ x: 0, y: 1 }} end={{ x: 1, y: 0 }}
        style={[styles.ring, { width: size, height: size, borderRadius: size / 2, padding: 2.5 }]}
      >
        <View style={[styles.ringInner, { borderRadius: (size - 5) / 2 }]}>
          <Image source={{ uri }} style={{ width: inner, height: inner, borderRadius: inner / 2 }} />
        </View>
      </LinearGradient>
    )
    : (
      <View style={[styles.ringPlain, { width: size, height: size, borderRadius: size / 2 }]}>
        <Image source={{ uri }} style={{ width: inner, height: inner, borderRadius: inner / 2 }} />
      </View>
    );
  return Ring;
}

const styles = StyleSheet.create({
  ring:       { alignItems: 'center', justifyContent: 'center' },
  ringInner:  { backgroundColor: '#fff', padding: 2 },
  ringPlain:  { alignItems: 'center', justifyContent: 'center', borderWidth: 1, borderColor: colors.dividerLight },
});
```

### Primary Button

```tsx
// components/IGButton.tsx
import { Pressable, Text, ViewStyle } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function IGButton({
  title, variant = 'primary', onPress, style,
}: {
  title: string;
  variant?: 'primary' | 'secondary' | 'destructive';
  onPress: () => void;
  style?: ViewStyle;
}) {
  const bg = { primary: colors.actionBlue, secondary: colors.surfaceInputLight, destructive: 'transparent' }[variant];
  const fg = { primary: '#fff', secondary: '#000', destructive: colors.destructive }[variant];

  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [
        { backgroundColor: variant === 'primary' && pressed ? colors.actionPressed : bg,
          paddingVertical: 7, paddingHorizontal: 16, borderRadius: 8, alignItems: 'center',
          transform: [{ scale: pressed ? 0.97 : 1 }] },
        style,
      ]}
    >
      <Text style={[typography.buttonPrimary, { color: fg }]}>{title}</Text>
    </Pressable>
  );
}
```

### Feed Post

```tsx
// components/FeedPost.tsx
import { Image, Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring, withTiming, runOnJS } from 'react-native-reanimated';
import { useState } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FeedPost({
  username, avatarUri, photoUri, likes, caption, timestamp,
}: {
  username: string; avatarUri: string; photoUri: string;
  likes: number; caption: string; timestamp: string;
}) {
  const [liked, setLiked] = useState(false);
  const scale = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }], opacity: scale.value }));

  const onDoubleTap = () => {
    setLiked(true);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    scale.value = withSequence(
      withSpring(1.4, { damping: 8 }),
      withTiming(0, { duration: 400 })
    );
  };

  return (
    <View>
      {/* Header */}
      <View style={styles.header}>
        <Image source={{ uri: avatarUri }} style={styles.avatar} />
        <Text style={typography.username}>{username}</Text>
        <View style={{ flex: 1 }} />
        <Ionicons name="ellipsis-horizontal" size={20} />
      </View>

      {/* Photo */}
      <Pressable onLongPress={() => { /* open menu */ }}
                 onPress={() => { /* single tap */ }}
                 onDoublePress={onDoubleTap}>
        <Image source={{ uri: photoUri }} style={styles.photo} />
        <Animated.View style={[styles.heartOverlay, style]}>
          <Ionicons name="heart" size={120} color="#fff" />
        </Animated.View>
      </Pressable>

      {/* Action bar */}
      <View style={styles.actions}>
        <Pressable onPress={() => { setLiked(!liked); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); }}>
          <Ionicons name={liked ? 'heart' : 'heart-outline'} size={26} color={liked ? colors.destructive : '#000'} />
        </Pressable>
        <Ionicons name="chatbubble-outline" size={26} />
        <Ionicons name="paper-plane-outline" size={26} />
        <View style={{ flex: 1 }} />
        <Ionicons name="bookmark-outline" size={26} />
      </View>

      <Text style={{ paddingHorizontal: 14, fontSize: 13, fontWeight: '600' }}>{likes} likes</Text>
      <Text numberOfLines={2} style={[typography.caption, { paddingHorizontal: 14, paddingTop: 4 }]}>
        <Text style={{ fontWeight: '600' }}>{username} </Text>{caption}
      </Text>
      <Text style={[typography.timestamp, { paddingHorizontal: 14, paddingVertical: 4 }]}>
        {timestamp}
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  header:       { height: 48, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 14, gap: 10 },
  avatar:       { width: 30, height: 30, borderRadius: 15 },
  photo:        { width: '100%', aspectRatio: 1, backgroundColor: colors.dividerLight },
  heartOverlay: { position: 'absolute', inset: 0, alignItems: 'center', justifyContent: 'center' },
  actions:      { height: 48, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 14, gap: 16 },
});
```

### Tab Bar (icon-only)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarShowLabel: false,
        tabBarActiveTintColor: '#000',
        tabBarInactiveTintColor: '#000',
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.6)' }} />
        ),
      }}
    >
      <Tabs.Screen name="index"   options={{ tabBarIcon: ({ focused }) => <Ionicons name={focused ? 'home' : 'home-outline'} size={26} /> }} />
      <Tabs.Screen name="search"  options={{ tabBarIcon: () => <Ionicons name="search" size={26} /> }} />
      <Tabs.Screen name="reels"   options={{ tabBarIcon: () => <Ionicons name="play-outline" size={26} /> }} />
      <Tabs.Screen name="create"  options={{ tabBarIcon: () => <Ionicons name="add-outline" size={28} /> }} />
      <Tabs.Screen name="profile" options={{ tabBarIcon: ({ focused }) => <Ionicons name={focused ? 'person-circle' : 'person-circle-outline'} size={26} /> }} />
    </Tabs>
  );
}
```

## 4. Motion & Haptics

```tsx
// Double-tap-to-like: spring 1.4 → 0 over 700ms
scale.value = withSequence(
  withSpring(1.4, { damping: 8 }),
  withTiming(0, { duration: 400 })
);
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Tab selection — selection haptic
Haptics.selectionAsync();

// Long-press on Home tab — medium haptic → account switcher
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
```

## 5. Icon Mappings

| Purpose | Ionicons |
|---------|----------|
| Home | `home-outline` / `home` |
| Search | `search-outline` / `search` |
| Reels | `play-outline` / `play` |
| Create | `add-outline` / `add` |
| Profile | `person-circle-outline` / `person-circle` |
| Heart | `heart-outline` / `heart` |
| Comment | `chatbubble-outline` / `chatbubble` |
| Share (DM) | `paper-plane-outline` / `paper-plane` |
| Save | `bookmark-outline` / `bookmark` |
| More | `ellipsis-horizontal` |

## 6. Platform Notes

- **Safe area**: wrap feed in `SafeAreaView` with `edges={['top']}`; the bottom tab bar extends under the safe area with blur
- **Status bar**: `<StatusBar style={colorScheme === 'dark' ? 'light' : 'dark'} />` from `expo-status-bar`
- **Dynamic Type**: scales on username/caption/comment; fix the 28pt logotype with `allowFontScaling={false}`
- **True-black dark mode**: override `userInterfaceStyle` or use a custom theme context — RN's default dark is `#000` which matches Instagram exactly
- **`onDoublePress`** is not built into RN — implement via gesture handler or a simple time-based tap counter
