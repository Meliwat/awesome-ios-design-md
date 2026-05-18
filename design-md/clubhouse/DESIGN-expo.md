# Clubhouse (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Clubhouse's visual language into paste-ready Expo / React Native code: a design-token module, the serif/sans pairing, themed components, and Reanimated / Haptics snippets for the signature speaking-pulse avatar, room stage, and raise-hand bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Light — the Hallway
  cream:        '#F2EFE4',
  creamCard:    '#FBF9F1',
  creamPressed: '#EAE6D8',
  dividerLight: '#DDD8C8',

  // Dark — the Room (warm near-black, NOT slate)
  room:         '#1A1A1A',
  darkSurface1: '#242220',
  darkSurface2: '#2E2B27',
  dividerDark:  '#3A372F',

  // Text
  ink:          '#1A1A1A',
  inkSecondary: '#6B6453',
  inkTertiary:  '#9A9483',
  creamText:    '#F2EFE4',
  creamText2:   '#B8B2A0',
  creamText3:   '#837D6D',

  // Brand — the single accent
  emerald:      '#38B569',
  emeraldDeep:  '#2E9A58',
  emeraldSoft:  'rgba(56,181,105,0.18)',  // pulse halo / tinted chip
  emeraldInk:   '#07210F',                // text on emerald

  // Semantic
  gold:         '#E9C46A',                // moderator
  leave:        '#E5575C',
  scrim:        'rgba(20,18,16,0.5)',     // warm, NOT black

  // Avatar gradient palette (content color)
  peachA: '#F4C77B', peachB: '#E0A24B',
  sageA:  '#9FD8B4', sageB:  '#5FB484',
  lavA:   '#C9B6E8', lavB:   '#9C7FD0',
  coralA: '#F2A9A0', coralB: '#D9776B',
  skyA:   '#A9C7E8', skyB:   '#6E9CD0',
} as const;

export const avatarGradients = [
  [colors.peachA, colors.peachB],
  [colors.sageA,  colors.sageB],
  [colors.lavA,   colors.lavB],
  [colors.coralA, colors.coralB],
  [colors.skyA,   colors.skyB],
] as const;

export const avatarFor = (id: number) => avatarGradients[Math.abs(id) % avatarGradients.length];
```

## 2. Typography

Clubhouse pairs a serif display for titles with a sans for UI. Load **DM Serif Display** (titles) + **Inter** (UI) — both SIL OFL.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'DMSerif-Regular': require('../assets/fonts/DMSerifDisplay-Regular.ttf'),
    'DMSerif-Italic':  require('../assets/fonts/DMSerifDisplay-Italic.ttf'),
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  // Serif display — titles, topics, greetings, quotes
  roomTitle:   { fontFamily: 'DMSerif-Regular', fontSize: 28, lineHeight: 34, letterSpacing: -0.2, color: '#F2EFE4' },
  screenTitle: { fontFamily: 'DMSerif-Regular', fontSize: 24, lineHeight: 29, letterSpacing: -0.2, color: '#1A1A1A' },
  quote:       { fontFamily: 'DMSerif-Italic',  fontSize: 20, lineHeight: 26, color: '#1A1A1A' },

  // Sans (Inter) — every UI text
  section:    { fontFamily: 'Inter-Bold',     fontSize: 18, lineHeight: 23, letterSpacing: -0.1, color: '#1A1A1A' },
  subsection: { fontFamily: 'Inter-Bold',     fontSize: 16, lineHeight: 21, color: '#1A1A1A' },
  body:       { fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 24, color: '#1A1A1A' },
  name:       { fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 18, color: '#F2EFE4' },
  button:     { fontFamily: 'Inter-Bold',     fontSize: 16, lineHeight: 16 },
  meta:       { fontFamily: 'Inter-Regular',  fontSize: 14, lineHeight: 20, color: '#6B6453' },
  roleTag:    { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 14 },
  overline:   { fontFamily: 'Inter-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.8, textTransform: 'uppercase' as const, color: '#837D6D' },
  tab:        { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 12 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Speaking-Pulse Avatar (the core atom)

```tsx
// components/SpeakerAvatar.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, cancelAnimation } from 'react-native-reanimated';
import { colors, avatarFor } from '../theme/colors';
import { typography } from '../theme/typography';

export function SpeakerAvatar({
  id, initials, name, role, size = 60,
  isSpeaking = false, isMuted = false, isHost = false,
}: {
  id: number; initials: string; name: string; role?: string; size?: number;
  isSpeaking?: boolean; isMuted?: boolean; isHost?: boolean;
}) {
  const [a, b] = avatarFor(id);
  const scale = useSharedValue(1);

  useEffect(() => {
    if (isSpeaking) {
      scale.value = withRepeat(withTiming(1.12, { duration: 900 }), -1, true);
    } else {
      cancelAnimation(scale);
      scale.value = withTiming(1, { duration: 200 });
    }
  }, [isSpeaking]);

  const haloStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <View style={{ alignItems: 'center', gap: 7, width: size + 16 }}>
      <View style={{ width: size + 16, height: size + 16, alignItems: 'center', justifyContent: 'center' }}>
        {isSpeaking && (
          <Animated.View
            style={[{
              position: 'absolute', width: size + 16, height: size + 16,
              borderRadius: (size + 16) / 2, backgroundColor: colors.emeraldSoft,
            }, haloStyle]}
          />
        )}
        <LinearGradient
          colors={[a, b]}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{
            width: size, height: size, borderRadius: size / 2,
            alignItems: 'center', justifyContent: 'center',
            borderWidth: isSpeaking ? 3 : 0, borderColor: colors.emerald,
            opacity: isMuted ? 0.55 : 1,
          }}
        >
          <Text style={{ fontFamily: 'Inter-Bold', fontSize: size * 0.33, color: '#2A2620' }}>{initials}</Text>
        </LinearGradient>

        {isMuted && (
          <View style={{
            position: 'absolute', right: 0, bottom: 0, width: 22, height: 22, borderRadius: 11,
            backgroundColor: colors.darkSurface2, borderWidth: 2, borderColor: colors.room,
            alignItems: 'center', justifyContent: 'center',
          }}>
            <Ionicons name="mic-off" size={11} color={colors.creamText2} />
          </View>
        )}
        {isHost && (
          <View style={{
            position: 'absolute', right: 0, top: 0, width: 20, height: 20, borderRadius: 10,
            backgroundColor: colors.darkSurface2, borderWidth: 2, borderColor: colors.room,
            alignItems: 'center', justifyContent: 'center',
          }}>
            <Text style={{ fontSize: 11 }}>🎙️</Text>
          </View>
        )}
      </View>

      <View style={{ alignItems: 'center', gap: 1 }}>
        <Text numberOfLines={1} style={[typography.name, role === 'Moderator' && { color: colors.gold }]}>{name}</Text>
        {role ? <Text style={{ fontFamily: 'Inter-Medium', fontSize: 11, color: colors.creamText3 }}>{role}</Text> : null}
      </View>
    </View>
  );
}
```

### Room Stage (grouped speaker grid)

```tsx
// components/RoomStage.tsx
import { ScrollView, Text, View } from 'react-native';
import { SpeakerAvatar } from './SpeakerAvatar';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Speaker = { id: number; initials: string; name: string; role?: string; speaking?: boolean; muted?: boolean; host?: boolean };
type Group = { label: string; speakers: Speaker[] };

export function RoomStage({ groups }: { groups: Group[] }) {
  return (
    <ScrollView style={{ backgroundColor: colors.room }} contentContainerStyle={{ paddingHorizontal: 16 }}>
      {groups.map((g) => (
        <View key={g.label}>
          <Text style={[typography.overline, { paddingHorizontal: 6, paddingTop: 14, paddingBottom: 10 }]}>{g.label}</Text>
          <View style={{ flexDirection: 'row', flexWrap: 'wrap', rowGap: 14 }}>
            {g.speakers.map((s) => (
              <View key={s.id} style={{ width: '25%', alignItems: 'center' }}>
                <SpeakerAvatar
                  id={s.id} initials={s.initials} name={s.name} role={s.role}
                  isSpeaking={s.speaking} isMuted={s.muted} isHost={s.host}
                />
              </View>
            ))}
          </View>
        </View>
      ))}
    </ScrollView>
  );
}
```

### Raise-Hand Bar (footer)

```tsx
// components/RaiseHandBar.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withSequence, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RaiseHandBar() {
  const [raised, setRaised] = useState(false);
  const rot = useSharedValue(0);
  const handStyle = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));

  const onPress = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    if (!raised) {
      rot.value = withRepeat(withSequence(withTiming(-15, { duration: 150 }), withTiming(15, { duration: 150 })), 2, true);
    }
    setRaised((r) => !r);
  };

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingHorizontal: 20, paddingVertical: 16, backgroundColor: colors.room,
      borderTopWidth: 0.5, borderTopColor: colors.dividerDark,
    }}>
      <Pressable onPress={onPress} style={{
        flexDirection: 'row', alignItems: 'center', gap: 8,
        paddingVertical: 12, paddingHorizontal: 20, borderRadius: 999,
        backgroundColor: raised ? colors.emeraldSoft : colors.darkSurface2,
      }}>
        <Animated.Text style={[{ fontSize: 18 }, handStyle]}>✋</Animated.Text>
        <Text style={[typography.button, { color: raised ? colors.emerald : colors.creamText }]}>
          {raised ? 'Hand raised' : 'Raise hand'}
        </Text>
      </Pressable>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 22 }}>
        <View style={{ width: 24, height: 24, borderRadius: 12, backgroundColor: colors.emeraldSoft, alignItems: 'center', justifyContent: 'center' }}>
          <View style={{ width: 14, height: 14, borderRadius: 7, backgroundColor: colors.emerald }} />
        </View>
        <Ionicons name="mic" size={22} color={colors.creamText2} />
      </View>
    </View>
  );
}
```

### Hallway Room Card

```tsx
// components/HallwayCard.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, avatarFor } from '../theme/colors';
import { typography } from '../theme/typography';

export function HallwayCard({
  club, isLive, title, avatars, count,
}: { club: string; isLive: boolean; title: string; avatars: { id: number; initials: string }[]; count: number }) {
  return (
    <View style={{
      backgroundColor: colors.creamCard, borderRadius: 16, padding: 16, gap: 10,
      shadowColor: '#000', shadowOpacity: 0.05, shadowRadius: 10, shadowOffset: { width: 0, height: 2 }, elevation: 2,
    }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}>
        <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 14, color: colors.inkSecondary }}>{club}</Text>
        <View style={{
          paddingVertical: 5, paddingHorizontal: 10, borderRadius: 999,
          backgroundColor: isLive ? colors.emeraldSoft : colors.creamPressed,
        }}>
          <Text style={[typography.roleTag, { color: isLive ? colors.emerald : colors.inkSecondary }]}>
            {isLive ? '● Open' : 'Upcoming'}
          </Text>
        </View>
      </View>
      <Text style={{ fontFamily: 'DMSerif-Regular', fontSize: 20, color: colors.ink }}>{title}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center' }}>
        {avatars.slice(0, 5).map((a, i) => {
          const [c1, c2] = avatarFor(a.id);
          return (
            <LinearGradient key={a.id} colors={[c1, c2]}
              style={{
                width: 32, height: 32, borderRadius: 16, marginLeft: i === 0 ? 0 : -10,
                borderWidth: 2, borderColor: colors.creamCard, alignItems: 'center', justifyContent: 'center',
              }}>
              <Text style={{ fontFamily: 'Inter-Bold', fontSize: 11, color: '#fff' }}>{a.initials}</Text>
            </LinearGradient>
          );
        })}
        <Text style={[typography.meta, { marginLeft: 16 }]}>{count} in the room</Text>
      </View>
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
        tabBarActiveTintColor: colors.ink,            // ink/cream, no colored pill
        tabBarInactiveTintColor: colors.inkTertiary,
        tabBarStyle: {
          backgroundColor: colors.cream,
          borderTopWidth: 0.5,
          borderTopColor: colors.dividerLight,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Hallway',  tabBarIcon: ({ color }) => <Ionicons name="home-outline"     size={24} color={color} /> }} />
      <Tabs.Screen name="explore"  options={{ title: 'Explore',  tabBarIcon: ({ color }) => <Ionicons name="compass-outline"  size={24} color={color} /> }} />
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarIcon: ({ color }) => <Ionicons name="notifications-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import { withRepeat, withTiming, withSequence, FadeIn } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Speaking pulse — the signature breath (loop while audio detected)
scale.value = withRepeat(withTiming(1.12, { duration: 900 }), -1, true);
// stop: cancelAnimation(scale); scale.value = withTiming(1, { duration: 200 });

// New speaker joins — scale 0.8→1 + fade
// entering={FadeIn.duration(200)}  (+ initial transform scale 0.8)

// Raise hand — emoji wave
rot.value = withRepeat(withSequence(withTiming(-15,{duration:150}), withTiming(15,{duration:150})), 2, true);

// Invited to speak — celebratory sheet + medium haptic
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Room enter: full-screen modal slide-up 300ms; collapse: slide-down to docked mini-bar 280ms (pulse continues)

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);    // raise hand, ping, new speaker
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);  // invited to speak
Haptics.selectionAsync();                                  // Open/Social/Closed audience change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The host pin is the 🎙️ emoji, not an icon; "speaking" is never an icon — it is the emerald ring + halo.

| Purpose | Ionicons |
|---------|----------|
| Collapse room | `chevron-down` |
| Add people | `person-add-outline` |
| Share / more | `share-outline` / `ellipsis-horizontal` |
| Mic on | `mic` |
| Mic off / muted badge | `mic-off` |
| Hallway (tab) | `home-outline` |
| Explore (tab) | `compass-outline` |
| Activity (tab) | `notifications-outline` |
| Profile (tab) | `person-circle-outline` |
| Search | `search` |
| Reminder | `notifications-outline` |
| Start a room (+) | `add` |
| Leave | `close` |
| Host pin | 🎙️ (emoji) |
| Speaking | (no icon — emerald ring + halo) |

## 7. Platform Notes

- **Font choice**: bundle DM Serif Display (titles) + Inter (UI) — both SIL OFL, free. Never set names/buttons in serif or titles in sans
- **Warm dark mode**: use `useColorScheme()` to swap to the Room palette (`#1A1A1A` / `#242220` / `#2E2B27`) — keep it warm, never let it become a neutral/blue `#191919`
- **The emerald never changes**: `#38B569` is identical light and dark; do not theme it, do not use it decoratively — it means "this person has the floor"
- **Status bar**: `<StatusBar style="dark" />` on the cream hallway, `"light"` in the dark room
- **Safe area**: wrap screens in `SafeAreaView`; the room footer / hand bar and the bottom tab bar need bottom safe-area padding; the collapsed mini room-bar docks just above the tab bar
- **Dynamic Type**: RN `<Text>` honors system scale on titles/body/names; set `allowFontScaling={false}` on role tags, group overlines, tab labels, and the "+N" cluster chip (layout-critical); truncate names to 1 line
- **Speaking pulse performance**: drive the halo with a single Reanimated `withRepeat` shared value (UI thread) — never re-render React on each frame; cancel on silence
- **Audio session**: pair with `expo-av` / a WebRTC layer; the speaking ring should be driven by an audio-level callback, debounced ~150ms so it doesn't flicker on brief pauses
- **Modal room**: present the Room as a full-screen `expo-router` modal route; collapsing pushes a persistent mini-bar component above the tab navigator
- **Accessibility**: a speaking avatar needs `accessibilityLabel="{name}, speaking"` (the pulse must not be the only cue); the raise-hand pill toggles its label "Raise hand" ↔ "Hand raised"; muted avatars announce "muted"
- **Warm scrim**: modal/sheet backdrops use `rgba(20,18,16,0.5)` (warm), never `rgba(0,0,0,0.5)` — preserve the hallway warmth
