# TikTok (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates TikTok's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-blur`, `expo-haptics`, `expo-video` (or `expo-av`), and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:       '#010101',
  surface:      '#161823',   // DMs, sheets
  inputField:   '#2F2F2F',   // comment compose

  rose:         '#FE2C55',   // Like, Follow, scrubber — the verb
  cyan:         '#25F4EE',   // Create, chromatic accent

  textPrimary:   '#FFFFFF',
  textSecondary: '#E5E5E5',
  textTertiary:  'rgba(255,255,255,0.6)',
  textDisabled:  'rgba(255,255,255,0.3)',

  followerGray:  'rgba(255,255,255,0.15)',
  scrimLight:    'rgba(0,0,0,0.25)',   // icon shadow
  scrimMedium:   'rgba(0,0,0,0.4)',    // text shadow
  scrimHeavy:    'rgba(0,0,0,0.6)',    // sheet dim
  scrubberTrack: 'rgba(255,255,255,0.3)',
} as const;

export type TikTokColor = keyof typeof colors;
```

## 2. Typography

Load Proxima Nova via `expo-font`. Fall back to `System` which is SF Pro on iOS.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'ProximaNova-Regular':  require('../assets/fonts/ProximaNova-Regular.otf'),
    'ProximaNova-Medium':   require('../assets/fonts/ProximaNova-Medium.otf'),
    'ProximaNova-Semibold': require('../assets/fonts/ProximaNova-Semibold.otf'),
    'ProximaNova-Bold':     require('../assets/fonts/ProximaNova-Bold.otf'),
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
  displayName:     { ...base, fontFamily: 'ProximaNova-Bold',     fontSize: 24, lineHeight: 29, letterSpacing: -0.2 },
  sheetTitle:      { ...base, fontFamily: 'ProximaNova-Semibold', fontSize: 17, lineHeight: 21, letterSpacing: -0.1 },

  username:        { ...base, fontFamily: 'ProximaNova-Bold',     fontSize: 16, lineHeight: 19 },
  caption:         { ...base, fontFamily: 'ProximaNova-Regular',  fontSize: 15, lineHeight: 20 },
  hashtag:         { ...base, fontFamily: 'ProximaNova-Bold',     fontSize: 15, lineHeight: 20 },
  actionCount:     { ...base, fontFamily: 'ProximaNova-Bold',     fontSize: 13, lineHeight: 16 },
  music:           { ...base, fontFamily: 'ProximaNova-Medium',   fontSize: 13, lineHeight: 16 },

  usernameList:    { ...base, fontFamily: 'ProximaNova-Semibold', fontSize: 15, lineHeight: 19 },
  body:            { ...base, fontFamily: 'ProximaNova-Regular',  fontSize: 14, lineHeight: 20 },
  meta:            {            fontFamily: 'ProximaNova-Regular', fontSize: 12, lineHeight: 16, color: '#E5E5E5' },

  buttonPrimary:   { ...base, fontFamily: 'ProximaNova-Bold',     fontSize: 16, lineHeight: 20 },
  buttonSecondary: { ...base, fontFamily: 'ProximaNova-Semibold', fontSize: 14, lineHeight: 18 },
  follow:          { ...base, fontFamily: 'ProximaNova-Bold',     fontSize: 14, lineHeight: 18 },
  tab:             { ...base, fontFamily: 'ProximaNova-Semibold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  chip:            { ...base, fontFamily: 'ProximaNova-Semibold', fontSize: 13, lineHeight: 16 },
} satisfies Record<string, TextStyle>;

// Shadow mixin for text overlaid on video
export const textOnVideo: TextStyle = {
  textShadowColor: 'rgba(0,0,0,0.4)',
  textShadowOffset: { width: 0, height: 1 },
  textShadowRadius: 4,
};
```

## 3. Signature Components

### Chromatic Aberration Wrapper

The visual thesis — used on the logo, Create button, and loading states.

```tsx
// components/ChromaticAberration.tsx
import React from 'react';
import { View, StyleSheet } from 'react-native';

export function ChromaticAberration({
  children, offset = 3,
}: { children: React.ReactNode; offset?: number }) {
  return (
    <View>
      <View style={[StyleSheet.absoluteFill, { transform: [{ translateX: -offset }] }]}>
        {React.cloneElement(children as any, { style: [(children as any).props?.style, { tintColor: '#25F4EE', color: '#25F4EE' }] })}
      </View>
      <View style={[StyleSheet.absoluteFill, { transform: [{ translateX: offset }] }]}>
        {React.cloneElement(children as any, { style: [(children as any).props?.style, { tintColor: '#FE2C55', color: '#FE2C55' }] })}
      </View>
      {React.cloneElement(children as any, { style: [(children as any).props?.style, { color: '#FFFFFF' }] })}
    </View>
  );
}
```

### Follow Button

```tsx
// components/FollowButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FollowButton({
  following, onToggle,
}: { following: boolean; onToggle: (next: boolean) => void }) {
  return (
    <Pressable
      onPress={() => {
        const next = !following;
        Haptics.notificationAsync(
          next ? Haptics.NotificationFeedbackType.Success : Haptics.NotificationFeedbackType.Warning
        );
        onToggle(next);
      }}
      style={({ pressed }) => ({
        paddingVertical: 6,
        paddingHorizontal: 16,
        minHeight: 28,
        borderRadius: 4,
        backgroundColor: following ? colors.followerGray : colors.rose,
        transform: [{ scale: pressed ? 0.95 : 1 }],
        alignItems: 'center',
        justifyContent: 'center',
      })}
    >
      <Text style={following ? typography.buttonSecondary : typography.follow}>
        {following ? 'Following' : 'Follow'}
      </Text>
    </Pressable>
  );
}
```

### Create Tab Button (Chromatic)

```tsx
// components/CreateButton.tsx
import { Pressable, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function CreateButton({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
        onPress();
      }}
      style={({ pressed }) => ({
        width: 44, height: 30,
        transform: [{ scale: pressed ? 0.94 : 1 }],
      })}
    >
      <View style={styles.wrap}>
        <View style={[styles.layer, { backgroundColor: colors.cyan,  transform: [{ translateX: -3 }] }]} />
        <View style={[styles.layer, { backgroundColor: colors.rose,  transform: [{ translateX:  3 }] }]} />
        <View style={[styles.layer, { backgroundColor: '#FFFFFF' }]} />
        <View style={styles.plusWrap}>
          <Ionicons name="add" size={18} color={colors.canvas} />
        </View>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  wrap:     { width: 44, height: 30, borderRadius: 8, overflow: 'hidden' },
  layer:    { ...StyleSheet.absoluteFillObject, borderRadius: 8 },
  plusWrap: { ...StyleSheet.absoluteFillObject, alignItems: 'center', justifyContent: 'center' },
});
```

### Right-Side Action Rail

```tsx
// components/ActionRail.tsx
import { View, Image, Text, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing, withSequence, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography, textOnVideo } from '../theme/typography';
import { formatCount } from '../theme/format';

export function ActionRail(props: {
  avatarUri?: string;
  isFollowed: boolean;
  onFollow: () => void;
  isLiked: boolean;
  likeCount: number;
  onLike: () => void;
  commentCount: number;
  bookmarkCount: number;
  shareCount: number;
  musicUri?: string;
}) {
  return (
    <View style={styles.rail}>
      <AvatarBadge avatarUri={props.avatarUri} isFollowed={props.isFollowed} onFollow={props.onFollow} />
      <Icon
        name={props.isLiked ? 'heart' : 'heart-outline'}
        tint={props.isLiked ? colors.rose : '#FFF'}
        count={props.likeCount}
        heavy
        onPress={props.onLike}
      />
      <Icon name="chatbubble-ellipses" tint="#FFF" count={props.commentCount} onPress={() => {}} />
      <Icon name="bookmark" tint="#FFF" count={props.bookmarkCount} onPress={() => {}} />
      <Icon name="arrow-redo" tint="#FFF" count={props.shareCount} onPress={() => {}} medium />
      <SpinningMusicDisc uri={props.musicUri} />
    </View>
  );
}

function AvatarBadge({ avatarUri, isFollowed, onFollow }: { avatarUri?: string; isFollowed: boolean; onFollow: () => void }) {
  return (
    <View style={{ alignItems: 'center', marginBottom: 8 }}>
      <Image source={avatarUri ? { uri: avatarUri } : undefined} style={styles.avatar} />
      {!isFollowed && (
        <Pressable
          onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onFollow(); }}
          style={styles.plusBadge}
        >
          <Ionicons name="add" size={12} color="#FFF" />
        </Pressable>
      )}
    </View>
  );
}

function Icon({ name, tint, count, onPress, heavy, medium }: {
  name: any; tint: string; count: number; onPress: () => void; heavy?: boolean; medium?: boolean;
}) {
  return (
    <Pressable
      onPress={() => {
        if (heavy) Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
        else if (medium) Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        else Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
      style={styles.iconWrap}
    >
      <Ionicons name={name} size={30} color={tint} style={{ textShadowColor: colors.scrimLight, textShadowOffset: { width: 0, height: 1 }, textShadowRadius: 3 }} />
      <Text style={[typography.actionCount, textOnVideo]}>{formatCount(count)}</Text>
    </Pressable>
  );
}

function SpinningMusicDisc({ uri }: { uri?: string }) {
  const rot = useSharedValue(0);
  useEffect(() => {
    rot.value = withRepeat(withTiming(360, { duration: 6000, easing: Easing.linear }), -1, false);
  }, []);
  const style = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));
  return (
    <Animated.View style={[styles.disc, style]}>
      {uri ? <Image source={{ uri }} style={styles.discArt} /> : <View style={styles.discArt} />}
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  rail:       { position: 'absolute', right: 12, bottom: 120, alignItems: 'center', gap: 24 },
  avatar:     { width: 48, height: 48, borderRadius: 24, borderWidth: 2, borderColor: '#FFF', backgroundColor: colors.surface },
  plusBadge:  { width: 18, height: 18, borderRadius: 9, backgroundColor: colors.rose, alignItems: 'center', justifyContent: 'center', position: 'absolute', bottom: -9 },
  iconWrap:   { alignItems: 'center', gap: 4, minWidth: 48, minHeight: 48 },
  disc:       { width: 44, height: 44, borderRadius: 22, backgroundColor: colors.canvas, alignItems: 'center', justifyContent: 'center', borderWidth: 1, borderColor: 'rgba(255,255,255,0.4)' },
  discArt:    { width: 28, height: 28, borderRadius: 14, backgroundColor: colors.surface },
});
```

```ts
// theme/format.ts
export function formatCount(n: number): string {
  if (n < 1_000) return String(n);
  if (n < 1_000_000) return (n / 1_000).toFixed(1).replace(/\.0$/, '') + 'K';
  return (n / 1_000_000).toFixed(1).replace(/\.0$/, '') + 'M';
}
```

### Caption Overlay (Bold Hashtags Inline)

```tsx
// components/Caption.tsx
import { View, Text, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography, textOnVideo } from '../theme/typography';

export function Caption({ username, caption, music }: { username: string; caption: string; music: string }) {
  const tokens = caption.split(' ');
  return (
    <View style={styles.wrap}>
      <Text style={[typography.username, textOnVideo]}>@{username}</Text>
      <Text style={[typography.caption, textOnVideo]} numberOfLines={2}>
        {tokens.map((t, i) => (
          <Text key={i} style={t.startsWith('#') ? typography.hashtag : undefined}>
            {(i > 0 ? ' ' : '') + t}
          </Text>
        ))}
      </Text>
      <View style={[styles.musicRow]}>
        <Ionicons name="musical-notes" size={12} color="#FFF" />
        <Text style={[typography.music, textOnVideo]} numberOfLines={1}>{music}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  wrap:     { position: 'absolute', left: 16, right: 80, bottom: 92, gap: 8 },
  musicRow: { flexDirection: 'row', alignItems: 'center', gap: 6 },
});
```

### Double-Tap Like Burst

```tsx
// components/DoubleTapFeed.tsx
import { View, Pressable, GestureResponderEvent } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring, withTiming, runOnJS } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { useState } from 'react';
import { colors } from '../theme/colors';

export function DoubleTapFeed({ children, onLike }: { children: React.ReactNode; onLike: () => void }) {
  const [hearts, setHearts] = useState<{ id: number; x: number; y: number }[]>([]);
  const handleDoubleTap = (e: GestureResponderEvent) => {
    const id = Date.now();
    setHearts((h) => [...h, { id, x: e.nativeEvent.pageX - 60, y: e.nativeEvent.pageY - 60 }]);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
    onLike();
    setTimeout(() => setHearts((h) => h.filter((x) => x.id !== id)), 800);
  };

  let lastTap = 0;
  return (
    <Pressable
      style={{ flex: 1 }}
      onPress={(e) => {
        const now = Date.now();
        if (now - lastTap < 300) { handleDoubleTap(e); lastTap = 0; }
        else { lastTap = now; }
      }}
    >
      {children}
      {hearts.map((h) => <HeartBurst key={h.id} x={h.x} y={h.y} />)}
    </Pressable>
  );
}

function HeartBurst({ x, y }: { x: number; y: number }) {
  const scale = useSharedValue(0);
  const opacity = useSharedValue(1);
  scale.value = withSequence(withSpring(1.4, { damping: 6 }), withSpring(1.0, { damping: 8 }));
  opacity.value = withTiming(0, { duration: 600 });
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }], opacity: opacity.value }));
  return (
    <Animated.View pointerEvents="none" style={[{ position: 'absolute', left: x, top: y }, style]}>
      <Ionicons name="heart" size={120} color={colors.rose} />
    </Animated.View>
  );
}
```

### Video Progress Scrubber

```tsx
// components/VideoScrubber.tsx
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function VideoScrubber({ progress, scrubbing = false }: { progress: number; scrubbing?: boolean }) {
  return (
    <View style={{ height: scrubbing ? 4 : 2, backgroundColor: colors.scrubberTrack, borderRadius: 2, overflow: 'hidden' }}>
      <View style={{ width: `${progress * 100}%`, height: '100%', backgroundColor: colors.rose }} />
    </View>
  );
}
```

### Chromatic Loading Spinner

```tsx
// components/LoadingSpinner.tsx
import { View } from 'react-native';
import Animated, { useSharedValue, withRepeat, withTiming, Easing, useAnimatedStyle } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';

export function LoadingSpinner({ size = 32 }: { size?: number }) {
  const rot = useSharedValue(0);
  useEffect(() => {
    rot.value = withRepeat(withTiming(360, { duration: 1000, easing: Easing.linear }), -1, false);
  }, []);
  const style = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));
  const ring = (tint: string, dx: number): React.CSSProperties | any => ({
    position: 'absolute' as const, inset: 0, borderRadius: size / 2,
    borderWidth: 3, borderColor: tint, borderRightColor: 'transparent',
    transform: [{ translateX: dx }],
  });
  return (
    <Animated.View style={[{ width: size, height: size }, style]}>
      <View style={ring(colors.cyan, -3)} />
      <View style={ring(colors.rose,  3)} />
      <View style={ring('#FFFFFF',   0)} />
    </Animated.View>
  );
}
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs, useRouter } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { View } from 'react-native';
import { CreateButton } from '../../components/CreateButton';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  const router = useRouter();
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  '#FFFFFF',
        tabBarInactiveTintColor: 'rgba(255,255,255,0.5)',
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent', height: 48 + 34 },
        tabBarBackground: () => (
          <BlurView intensity={60} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(1,1,1,0.92)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'ProximaNova-Semibold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"          size={24} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="search"        size={24} color={color} /> }} />
      <Tabs.Screen
        name="create"
        options={{
          title: '',
          tabBarIcon: () => <View style={{ alignItems: 'center' }}><CreateButton onPress={() => router.push('/create')} /></View>,
          tabBarButton: (props) => <View {...props as any} style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }} />,
        }}
      />
      <Tabs.Screen name="inbox"    options={{ title: 'Inbox',    tabBarIcon: ({ color }) => <Ionicons name="mail"          size={24} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person"        size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Feed Composition

```tsx
// app/(tabs)/index.tsx
import { View } from 'react-native';
import { StatusBar } from 'expo-status-bar';
import { LinearGradient } from 'expo-linear-gradient';
import { useState } from 'react';
import { ActionRail } from '../../components/ActionRail';
import { Caption } from '../../components/Caption';
import { VideoScrubber } from '../../components/VideoScrubber';
import { DoubleTapFeed } from '../../components/DoubleTapFeed';

export default function Feed() {
  const [isLiked, setIsLiked] = useState(false);
  const [likeCount, setLikeCount] = useState(24_500);
  const [isFollowed, setIsFollowed] = useState(false);

  return (
    <View style={{ flex: 1, backgroundColor: '#010101' }}>
      <StatusBar style="light" />
      <DoubleTapFeed onLike={() => { if (!isLiked) { setIsLiked(true); setLikeCount(c => c + 1); } }}>
        <LinearGradient
          colors={['#FF4E8E', '#7B2FBE', '#1A3CC9']}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ flex: 1 }}
        />
      </DoubleTapFeed>

      <ActionRail
        isFollowed={isFollowed}
        onFollow={() => setIsFollowed(true)}
        isLiked={isLiked}
        likeCount={likeCount}
        onLike={() => { setIsLiked((v) => !v); setLikeCount((c) => c + (isLiked ? -1 : 1)); }}
        commentCount={1234} bookmarkCount={890} shareCount={456}
      />

      <Caption
        username="novapalmer"
        caption="sunset loop at marin headlands #sunset #bayarea #goldenhour"
        music="original sound - novapalmer"
      />

      <View style={{ position: 'absolute', left: 0, right: 0, bottom: 60 + 34 }}>
        <VideoScrubber progress={0.35} />
      </View>
    </View>
  );
}
```

## 6. Motion & Haptics

```tsx
// Heart toggle
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);

// Follow success
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Share
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Feed swipe next: use a vertical FlatList with pagingEnabled + snapToInterval = height
// Double-tap: see DoubleTapFeed above — heavy haptic on the second tap
// Spinning music disc: withRepeat(withTiming(360, { duration: 6000, easing: Easing.linear }))
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to TikTok's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Heart idle / active | `heart-outline` / `heart` |
| Comment | `chatbubble-ellipses` |
| Bookmark | `bookmark-outline` / `bookmark` |
| Share | `arrow-redo` |
| Music note | `musical-notes` |
| Create plus | `add` |
| Home | `home-outline` / `home` |
| Discover | `search` |
| Inbox | `mail-outline` / `mail` |
| Profile | `person-outline` / `person` |
| Back | `chevron-back` |
| More | `ellipsis-horizontal` |
| Send | `paper-plane` |
| Live | `radio` |

## 8. Platform Notes

- **iOS-only feel**: Use `expo-blur` on the tab bar for the `.regularMaterial` equivalent; Android falls back to a solid `rgba(1,1,1,0.92)` background
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` globally — the feed is always dark under light content
- **Safe area**: Do NOT wrap the feed video in `SafeAreaView` — the video is allowed to pass under the Dynamic Island and home indicator. Wrap only the UI overlays (right rail, caption block, tab bar) in `useSafeAreaInsets()`-aware padding
- **Video**: Use `expo-video` (new in SDK 52) or `expo-av`'s `Video` component with `resizeMode="cover"` and `shouldPlay` / `isLooping` — preload the next two videos with `prepareToPlay` for smooth swipe transitions
- **60fps**: Enable `useNativeDriver: true` on all animations; Reanimated runs on the UI thread by default
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel` on each rail icon (e.g., "Like video, 24.5K likes"). Respect `AccessibilityInfo.isReduceMotionEnabled()` — skip the double-tap burst spring and pause the spinning disc
- **Keyboard**: On the comment compose, use `KeyboardAvoidingView` with `behavior="padding"` so the input docks above the keyboard with no gap
