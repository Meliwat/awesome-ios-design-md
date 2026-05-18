# Vimeo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vimeo's cinematic visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the player and Staff Pick feed.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-av` (or `expo-video`), and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — primary)
  canvas:    '#0D0E12',
  surface1:  '#16181F',
  surface2:  '#1F222B',
  divider:   '#262A34',

  // Surfaces (light — rare parity)
  lightCanvas:   '#FFFFFF',
  lightSurface1: '#F5F6F8',
  lightDivider:  '#E2E5EA',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#AEB6C2',
  textTertiary:  '#6B7280',
  onBlue:        '#04121A',

  // Brand
  vimeoBlue:        '#00ADEF',
  vimeoBlueLegacy:  '#1AB7EA',
  vimeoBluePressed: '#008FC4',

  // Accent & curation
  staffGold:  '#FFD24C',
  plusPurple: '#8B5CF6',

  // Semantic
  success: '#2ECC71',
  error:   '#FF4D4F',
  live:    '#FF2D55',
} as const;

export type VimeoColor = keyof typeof colors;

// Player scrim gradient
export const playerScrim = ['rgba(8,21,29,0)', 'rgba(8,21,29,0.85)'] as const;
export const avatarGradient = ['#00ADEF', '#8B5CF6'] as const;
```

## 2. Typography

Load **Inter** via `expo-font` (SIL OFL — free). Use `fontVariant: ['tabular-nums']` on all numeric text.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  display:     { color: '#FFFFFF', fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  screenTitle: { color: '#FFFFFF', fontFamily: 'Inter-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  section:     { color: '#FFFFFF', fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  videoTitle:  { color: '#FFFFFF', fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 24, letterSpacing: -0.2 },
  cardTitle:   { color: '#FFFFFF', fontFamily: 'Inter-SemiBold',  fontSize: 17, lineHeight: 23 },
  body:        { color: '#FFFFFF', fontFamily: 'Inter-Medium',    fontSize: 15, lineHeight: 22 },
  bodyRead:    { color: '#AEB6C2', fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 24 },
  meta:        { color: '#6B7280', fontFamily: 'Inter-Regular',   fontSize: 13, lineHeight: 18 },
  creator:     { color: '#FFFFFF', fontFamily: 'Inter-SemiBold',  fontSize: 13, lineHeight: 18 },
  button:      { color: '#04121A', fontFamily: 'Inter-Bold',      fontSize: 15, lineHeight: 15 },
  pill:        { color: '#04121A', fontFamily: 'Inter-Bold',      fontSize: 12, lineHeight: 12, letterSpacing: 0.1 },
  overline:    { color: '#6B7280', fontFamily: 'Inter-SemiBold',  fontSize: 11, lineHeight: 11, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  timecode:    { color: '#FFFFFF', fontFamily: 'Inter-Bold',      fontSize: 11, lineHeight: 11, ...tnum },
  tab:         { color: '#6B7280', fontFamily: 'Inter-Medium',    fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Cinematic Video Player

```tsx
// components/VimeoPlayer.tsx
import { useEffect, useRef, useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, playerScrim } from '../theme/colors';
import { typography } from '../theme/typography';

export function VimeoPlayer({ elapsed, total, progress }: { elapsed: string; total: string; progress: number }) {
  const [visible, setVisible] = useState(true);
  const opacity = useSharedValue(1);
  const idle = useRef<ReturnType<typeof setTimeout>>();
  const fade = useAnimatedStyle(() => ({ opacity: opacity.value }));

  const scheduleIdle = () => {
    clearTimeout(idle.current);
    idle.current = setTimeout(() => { opacity.value = withTiming(0, { duration: 250 }); setVisible(false); }, 3000);
  };
  useEffect(() => { scheduleIdle(); return () => clearTimeout(idle.current); }, []);

  const toggle = () => {
    const next = !visible;
    setVisible(next);
    opacity.value = withTiming(next ? 1 : 0, { duration: next ? 200 : 250 });
    if (next) scheduleIdle();
  };

  return (
    <Pressable onPress={toggle} style={{ aspectRatio: 16 / 9, borderRadius: 14, overflow: 'hidden' }}>
      <LinearGradient colors={['#16384A', '#08151D']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ ...StyleSheetAbsolute }} />
      <Animated.View style={[{ flex: 1, justifyContent: 'center', alignItems: 'center' }, fade]}>
        <View style={{
          width: 52, height: 52, borderRadius: 26, alignItems: 'center', justifyContent: 'center',
          backgroundColor: 'rgba(13,14,18,0.55)', borderWidth: 1.5, borderColor: 'rgba(255,255,255,0.55)',
        }}>
          <Ionicons name="play" size={18} color="#FFF" style={{ marginLeft: 3 }} />
        </View>
      </Animated.View>
      <Animated.View style={[{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 34 }, fade]}>
        <LinearGradient colors={playerScrim} style={{ flex: 1, flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 12 }}>
          <Text style={typography.timecode}>{elapsed}</Text>
          <ScrubBar progress={progress} />
          <Text style={typography.timecode}>{total}</Text>
          <View style={{ borderWidth: 1, borderColor: 'rgba(255,255,255,0.5)', borderRadius: 3, paddingHorizontal: 4, paddingVertical: 1 }}>
            <Text style={{ fontFamily: 'Inter-Bold', fontSize: 9, color: '#FFF' }}>HD</Text>
          </View>
        </LinearGradient>
      </Animated.View>
    </Pressable>
  );
}

const StyleSheetAbsolute = { position: 'absolute' as const, top: 0, left: 0, right: 0, bottom: 0 };

function ScrubBar({ progress }: { progress: number }) {
  return (
    <View style={{ flex: 1, height: 16, justifyContent: 'center' }}>
      <View style={{ height: 3, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.22)' }} />
      <View style={{ position: 'absolute', height: 3, borderRadius: 2, backgroundColor: colors.vimeoBlue, width: `${progress * 100}%` }} />
      <View style={{
        position: 'absolute', left: `${progress * 100}%`, marginLeft: -4.5,
        width: 9, height: 9, borderRadius: 5, backgroundColor: '#FFF',
        shadowColor: '#000', shadowOpacity: 0.6, shadowRadius: 4, shadowOffset: { width: 0, height: 1 },
      }} />
    </View>
  );
}
```

### Staff Pick Badge

```tsx
// components/StaffPickBadge.tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function StaffPickBadge({ compact = false }: { compact?: boolean }) {
  if (compact) {
    return (
      <View style={{ width: 16, height: 16, borderRadius: 8, backgroundColor: colors.staffGold, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="star" size={9} color={colors.onBlue} />
      </View>
    );
  }
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, backgroundColor: colors.staffGold, borderRadius: 999, paddingHorizontal: 8, paddingVertical: 3 }}>
      <Ionicons name="star" size={10} color={colors.onBlue} />
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 10, color: colors.onBlue }}>Staff Pick</Text>
    </View>
  );
}
```

### Curated Watch Feed Row

```tsx
// components/WatchFeedRow.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { StaffPickBadge } from './StaffPickBadge';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function WatchFeedRow({ title, creator, stats, duration }:
  { title: string; creator: string; stats: string; duration: string }) {
  return (
    <View style={{ flexDirection: 'row', gap: 11, marginBottom: 14 }}>
      <View style={{ width: 124, aspectRatio: 16 / 9, borderRadius: 8, overflow: 'hidden' }}>
        <LinearGradient colors={['#4A6B7C', '#1E323D']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }} />
        <View style={{ position: 'absolute', top: 5, left: 5 }}><StaffPickBadge compact /></View>
        <View style={{ position: 'absolute', bottom: 5, right: 5, backgroundColor: 'rgba(0,0,0,0.7)', borderRadius: 3, paddingHorizontal: 4, paddingVertical: 1 }}>
          <Text style={{ fontFamily: 'Inter-Bold', fontSize: 9, color: '#FFF', fontVariant: ['tabular-nums'] }}>{duration}</Text>
        </View>
      </View>
      <View style={{ flex: 1 }}>
        <Text numberOfLines={2} style={typography.creator}>{title}</Text>
        <Text style={[typography.meta, { marginTop: 4 }]}>by {creator}</Text>
        <Text style={[typography.meta, { marginTop: 6 }]}>{stats}</Text>
      </View>
    </View>
  );
}
```

### Follow Pill

```tsx
// components/FollowPill.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function FollowPill() {
  const [following, setFollowing] = useState(false);
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); setFollowing(v => !v); }}
      style={{
        paddingHorizontal: 16, paddingVertical: 7, borderRadius: 999,
        backgroundColor: following ? colors.surface2 : colors.vimeoBlue,
        borderWidth: following ? 1 : 0, borderColor: colors.divider,
      }}
    >
      <Text style={{ fontFamily: 'Inter-Bold', fontSize: 12, color: following ? colors.textPrimary : colors.onBlue }}>
        {following ? 'Following' : 'Follow'}
      </Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.vimeoBlue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'rgba(13,14,18,0.94)' },
        tabBarBackground: () => <BlurView intensity={28} tint="dark" style={{ flex: 1 }} />,
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home" size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search', tabBarIcon: ({ color }) => <Ionicons name="search" size={22} color={color} /> }} />
      <Tabs.Screen name="upload"  options={{ title: '',       tabBarIcon: () => <Ionicons name="play-circle" size={26} color={colors.vimeoBlue} /> }} />
      <Tabs.Screen name="inbox"   options={{ title: 'Inbox',  tabBarIcon: ({ color }) => <Ionicons name="mail" size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile',tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Player controls auto-fade — withTiming on a shared opacity
opacity.value = withTiming(0, { duration: 250 }); // after 3s idle
opacity.value = withTiming(1, { duration: 200 }); // on tap

// Scrub knob grow
knob.value = withTiming(dragging ? 16 : 9, { duration: 120 });

// Follow toggle — color + label crossfade
// setState + Reanimated interpolateColor over 200ms

// Like heart spring
scale.value = withSequence(withSpring(1.25, { damping: 6 }), withSpring(1));

// Thumbnail load — FadeIn 220ms
import Animated, { FadeIn } from 'react-native-reanimated';
// <Animated.Image entering={FadeIn.duration(220)} />

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);      // follow, scrub start, like
Haptics.selectionAsync();                                    // tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For the Vimeo "v" wordmark, ship the official SVG via `react-native-svg`.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` / `home-outline` |
| Search | `search` |
| Upload (center) | `play-circle` |
| Inbox | `mail` / `mail-outline` |
| Profile | `person-circle` / `person-circle-outline` |
| Play | `play` |
| Pause | `pause` |
| Staff Pick star | `star` |
| Notifications | `notifications-outline` |
| Like | `heart` / `heart-outline` |
| Comment | `chatbubble-outline` |
| Share | `share-outline` |
| Watch Later | `time-outline` |
| Fullscreen | `expand` |
| AirPlay | `tv-outline` |
| Settings (player) | `settings-outline` |
| More | `ellipsis-horizontal` |
| Captions | `text-outline` |

## 7. Platform Notes

- **Font choice**: Inter is SIL OFL — free to bundle. Ship Regular → ExtraBold weights
- **Dark-first**: force `<StatusBar style="light" />`; the app is a screening room — light mode is parity only. Set `userInterfaceStyle: "dark"` in `app.json`
- **Video**: use `expo-video` (or legacy `expo-av` `Video`); render the player at `resizeMode="contain"` so letterboxing preserves the filmmaker's aspect; never crop the frame
- **Tabular figures**: set `fontVariant: ['tabular-nums']` on every timecode, duration, and play-count `Text`; set `allowFontScaling={false}` on timecodes, tab labels, overlines, pill text
- **Safe area**: wrap screens in `SafeAreaView`; the absolute blurred tab bar needs bottom safe-area padding; in landscape, hide the tab bar and go full-bleed cinema
- **Scrubber gesture**: build with `react-native-gesture-handler` `PanGestureHandler`; throttle seek calls; show a frame-preview thumbnail above the knob while dragging
- **Background audio / PiP**: configure `expo-av` `staysActiveInBackground` and enable iOS Picture-in-Picture so playback survives navigation
- **Accessibility**: the scrubber must be an `accessibilityRole="adjustable"` with `accessibilityActions` for increment/decrement (±10s); label the Staff Pick badge "Staff Pick, editor selected"
- **Color accuracy**: do not apply any tint/overlay to video surfaces — Vimeo's audience is color-critical; keep thumbnails and the player at full fidelity on `#0D0E12`
