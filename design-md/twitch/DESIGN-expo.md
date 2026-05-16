# Twitch (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Twitch's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:    '#0E0E10',
  deepBlack: '#000000',
  surface1:  '#18181B',
  surface2:  '#1F1F23',
  surface3:  '#2A2A2D',
  divider:   '#2A2A2D',

  textPrimary:   '#EFEFF1',
  textSecondary: '#ADADB8',
  textTertiary:  '#6F6F7B',

  purple:        '#9146FF',
  purplePressed: '#772CE8',
  liveRed:       '#EB0400',
  liveRedPressed:'#C20300',
  onlineGreen:   '#00C16E',
  errorRed:      '#EB0400',
} as const;

export type TwitchColor = keyof typeof colors;
```

## 2. Typography

Load Roobert via `expo-font`. Fall back to Inter (or `System`, SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Roobert-Regular':  require('../assets/fonts/Roobert-Regular.ttf'),
    'Roobert-Semibold': require('../assets/fonts/Roobert-Semibold.ttf'),
    'Roobert-Bold':     require('../assets/fonts/Roobert-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#EFEFF1' } satisfies TextStyle;

export const typography = {
  titleLarge:     { ...base, fontFamily: 'Roobert-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  channelName:    { ...base, fontFamily: 'Roobert-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  sectionHeader:  { ...base, fontFamily: 'Roobert-Bold',     fontSize: 20, lineHeight: 24, letterSpacing: -0.3 },
  streamTitle:    { ...base, fontFamily: 'Roobert-Semibold', fontSize: 16, lineHeight: 20, letterSpacing: -0.1 },
  channelLabel:   { ...base, fontFamily: 'Roobert-Bold',     fontSize: 15, lineHeight: 19, letterSpacing: -0.1 },
  body:           { ...base, fontFamily: 'Roobert-Regular',  fontSize: 15, lineHeight: 22 },
  chatMessage:    { ...base, fontFamily: 'Roobert-Regular',  fontSize: 14, lineHeight: 19 },
  chatUsername:   { ...base, fontFamily: 'Roobert-Bold',     fontSize: 14, lineHeight: 19 },
  meta:           {           fontFamily: 'Roobert-Regular',  fontSize: 13, lineHeight: 17, color: '#ADADB8' },
  cardSubtitle:   {           fontFamily: 'Roobert-Regular',  fontSize: 12, lineHeight: 16, color: '#ADADB8' },
  labelUpper:     { ...base, fontFamily: 'Roobert-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:         { color: '#FFFFFF', fontFamily: 'Roobert-Bold', fontSize: 15, lineHeight: 19, letterSpacing: 0.2 },
  buttonSecondary:{ ...base, fontFamily: 'Roobert-Semibold', fontSize: 14, lineHeight: 18 },
  tab:            {           fontFamily: 'Roobert-Semibold', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  badge:          { color: '#FFFFFF', fontFamily: 'Roobert-Bold', fontSize: 11, lineHeight: 13, letterSpacing: 0.4 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary Purple Follow / Subscribe Button

```tsx
// components/FollowButton.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FollowButton({
  following, onToggle, subscribe = false,
}: { following: boolean; onToggle: () => void; subscribe?: boolean }) {
  const label = following
    ? (subscribe ? 'Subscribed' : 'Following')
    : (subscribe ? 'Subscribe' : 'Follow');
  const icon = following ? 'checkmark' : (subscribe ? 'star' : 'heart');

  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onToggle(); }}
      style={({ pressed }) => ({
        height: 40, paddingHorizontal: 16, borderRadius: 6,
        flexDirection: 'row', alignItems: 'center', gap: 6,
        backgroundColor: following
          ? (pressed ? colors.surface3 : colors.surface2)
          : (pressed ? colors.purplePressed : colors.purple),
        transform: [{ scale: pressed ? 0.97 : 1 }],
        shadowColor: following ? 'transparent' : colors.purple,
        shadowOpacity: following ? 0 : 0.35, shadowRadius: 18, shadowOffset: { width: 0, height: 6 },
      })}
    >
      <Ionicons name={icon as any} size={14}
        color={following ? colors.purple : '#fff'} />
      <Text style={following ? typography.buttonSecondary : typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### LIVE Pill (pulsing) + Viewer-Count Pill

```tsx
// components/LivePill.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LivePill() {
  const s = useSharedValue(1);
  useEffect(() => { s.value = withRepeat(withTiming(0.55, { duration: 800 }), -1, true); }, []);
  const dot = useAnimatedStyle(() => ({ opacity: s.value, transform: [{ scale: s.value }] }));
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, height: 22,
                   paddingHorizontal: 8, borderRadius: 4, backgroundColor: colors.liveRed }}>
      <Animated.View style={[{ width: 6, height: 6, borderRadius: 3, backgroundColor: '#fff' }, dot]} />
      <Text style={typography.badge}>LIVE</Text>
    </View>
  );
}

export function ViewerPill({ count }: { count: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, height: 22,
                   paddingHorizontal: 8, borderRadius: 4, backgroundColor: 'rgba(0,0,0,0.6)' }}>
      <Ionicons name="person" size={9} color="#fff" />
      <Text style={typography.badge}>{count}</Text>
    </View>
  );
}
```

### Live Thumbnail Card

```tsx
// components/LiveCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { typography } from '../theme/typography';
import { LivePill, ViewerPill } from './LivePill';

export function LiveCard({
  title, channel, game, viewers, thumbUri, avatarUri, width,
}: { title: string; channel: string; game: string; viewers: string; thumbUri: string; avatarUri: string; width: number }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withTiming(1.03, { duration: 180 }))}
      onPressOut={() => (scale.value = withTiming(1, { duration: 180 }))}
    >
      <Animated.View style={[{ width }, style]}>
        <View style={{ width, height: width * 9 / 16, borderRadius: 6, overflow: 'hidden' }}>
          <Image source={{ uri: thumbUri }} style={{ width: '100%', height: '100%' }} />
          <View style={{ position: 'absolute', top: 8, left: 8 }}><LivePill /></View>
          <View style={{ position: 'absolute', bottom: 8, left: 8 }}><ViewerPill count={viewers} /></View>
        </View>
        <View style={{ flexDirection: 'row', gap: 8, marginTop: 8 }}>
          <Image source={{ uri: avatarUri }} style={{ width: 32, height: 32, borderRadius: 16 }} />
          <View style={{ flex: 1 }}>
            <Text style={typography.streamTitle} numberOfLines={2}>{title}</Text>
            <Text style={typography.meta}>{channel}</Text>
            <Text style={typography.cardSubtitle}>{game}</Text>
          </View>
        </View>
      </Animated.View>
    </Pressable>
  );
}
```

### Chat Message Row + Channel Live Ring

```tsx
// components/ChatRow.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatRow({
  username, userColor, message, mentioned = false,
}: { username: string; userColor: string; message: string; mentioned?: boolean }) {
  return (
    <View style={{ paddingVertical: 4, paddingHorizontal: 12, flexDirection: 'row',
                    backgroundColor: mentioned ? 'rgba(145,70,255,0.20)' : 'transparent',
                    borderLeftWidth: mentioned ? 2 : 0, borderLeftColor: colors.purple }}>
      <Text style={{ flex: 1 }}>
        <Text style={[typography.chatUsername, { color: userColor }]}>{username} </Text>
        <Text style={typography.chatMessage}>{message}</Text>
      </Text>
    </View>
  );
}

// components/AvatarRing.tsx
import { Image, View } from 'react-native';
export function AvatarRing({ uri, live, size = 44 }: { uri: string; live: boolean; size?: number }) {
  return (
    <View style={{ padding: 2, borderRadius: (size + 4) / 2, borderWidth: 2,
                    borderColor: live ? colors.liveRed : colors.purple }}>
      <Image source={{ uri }} style={{ width: size, height: size, borderRadius: size / 2 }} />
    </View>
  );
}
import { colors as _c } from '../theme/colors';
```

### Theater-Mode Chat Overlay

```tsx
// components/TheaterChatOverlay.tsx
import { FlatList, TextInput, View } from 'react-native';
import { BlurView } from 'expo-blur';
import Animated, { SlideInRight, SlideOutRight } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { ChatRow } from './ChatRow';

export function TheaterChatOverlay({ messages, hidden }: { messages: any[]; hidden: boolean }) {
  if (hidden) return null;
  return (
    <Animated.View entering={SlideInRight} exiting={SlideOutRight}
                   style={{ position: 'absolute', right: 0, top: 0, bottom: 0, width: 320 }}>
      <BlurView intensity={28} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(14,14,16,0.72)' }}>
        <FlatList
          data={messages}
          renderItem={({ item }) => (
            <ChatRow username={item.user} userColor={item.color}
                     message={item.text} mentioned={item.mentionsMe} />
          )}
          keyExtractor={(m) => m.id}
        />
        <TextInput
          placeholder="Send a message"
          placeholderTextColor={colors.textSecondary}
          style={{ margin: 12, padding: 12, borderRadius: 8, backgroundColor: colors.surface2, color: colors.textPrimary }}
        />
      </BlurView>
    </Animated.View>
  );
}
```

## 4. Tab Bar

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
        tabBarActiveTintColor:  colors.purple,    // purple is the indicator
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(14,14,16,0.6)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Roobert-Semibold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Following',     tabBarIcon: ({ color }) => <Ionicons name="heart"        size={24} color={color} /> }} />
      <Tabs.Screen name="browse"  options={{ title: 'Browse',        tabBarIcon: ({ color }) => <Ionicons name="albums"       size={24} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',        tabBarIcon: ({ color }) => <Ionicons name="search"       size={24} color={color} /> }} />
      <Tabs.Screen name="notifs"  options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={24} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile',       tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// LIVE pill pulse — withRepeat(withTiming(0.55, { duration: 800 }), -1, true) on a shared value (see §3)

// Card press scale-up
scale.value = withTiming(1.03, { duration: 180 }); // onPressIn
scale.value = withTiming(1,    { duration: 180 }); // onPressOut

// Follow tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Chat autoscroll: new rows entering={FadeInDown.duration(120)}; auto-scrollToEnd unless user scrolled up
// Theater toggle: SlideInRight / SlideOutRight on the chat overlay
```

## 6. Icon Library

Use `@expo/vector-icons` — ships Ionicons, Feather, MaterialCommunityIcons. Map to Twitch's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Follow | `heart-outline` / `heart` |
| Subscribe | `star-outline` / `star` |
| Followed check | `checkmark` |
| Viewers | `person` |
| Play / Pause | `play` / `pause` |
| Emote picker | `happy-outline` |
| Send | `send` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Search | `search` |
| Following (tab) | `heart-outline` / `heart` |
| Browse (tab) | `albums-outline` / `albums` |
| Notifications (tab) | `notifications-outline` / `notifications` |
| Profile (tab) | `person-circle-outline` / `person-circle` |

## 7. Platform Notes

- **iOS-only feel**: Use `expo-blur` for the tab bar AND the theater-mode chat overlay (`rgba(14,14,16,0.72)` + intensity 28). Android falls back to an opaque panel.
- **Status bar**: Set `<StatusBar style="light" />` from `expo-status-bar` globally — the near-black canvas requires light content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; chat input docks above the home indicator; rails bleed right
- **Dynamic Type**: React Native respects font scaling by default; set `allowFontScaling={false}` on tab labels, LIVE pill, and viewer pills; cap chat font growth via `maxFontSizeMultiplier={1.3}` to keep throughput readable
- **Accessibility**: announce live cards via `accessibilityLabel`; for chat use an `accessibilityLiveRegion="polite"` container so VoiceOver narrates new messages (offer a mute toggle)
- **Color split & color-blindness**: the LIVE pill always carries the word "LIVE" + dot — never rely on purple-vs-red hue alone for state
- **Reduce Motion**: gate the LIVE pulse and card scale-up behind `AccessibilityInfo.isReduceMotionEnabled()` (static red dot, cross-fade theater)
