# Microsoft Teams (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Teams' visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Teams is dual-identity, so tokens resolve against `useColorScheme()`.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const light = {
  canvas:   '#F5F5F5',
  surface1: '#FFFFFF',
  surface2: '#F0F0F0',
  divider:  '#E1E1E1',
  text1:    '#252423',
  text2:    '#616161',
  text3:    '#8A8886',
  accent:   '#6264A7',
} as const;

export const dark = {
  canvas:   '#1F1F1F',
  surface1: '#2D2C2C',
  surface2: '#3D3C3C',
  divider:  '#3D3C3C',
  text1:    '#FFFFFF',
  text2:    '#ADADAD',
  text3:    '#7A7A7A',
  accent:   '#5B5FC7',
} as const;

export const shared = {
  accentPressed: '#4F52B2',
  accentTint:    'rgba(98,100,167,0.12)',
  available:     '#6BB700',
  busy:          '#C4314B',
  away:          '#FFAA44',
  offline:       '#8A8886',
} as const;

import { useColorScheme } from 'react-native';
export function useTeams() {
  return useColorScheme() === 'dark' ? dark : light;
}
```

## 2. Typography

Teams uses Segoe UI; Inter is the closest free substitute. Load via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Segoe-Regular':  require('../assets/fonts/SegoeUI-Regular.ttf'),
    'Segoe-Semibold': require('../assets/fonts/SegoeUI-Semibold.ttf'),
    'Segoe-Bold':     require('../assets/fonts/SegoeUI-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts  (color applied at the call site since Teams is dual-identity)
import type { TextStyle } from 'react-native';

export const typography = {
  titleLarge: { fontFamily: 'Segoe-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.3 },
  section:    { fontFamily: 'Segoe-Bold',     fontSize: 20, lineHeight: 25, letterSpacing: -0.2 },
  teamName:   { fontFamily: 'Segoe-Bold',     fontSize: 16, lineHeight: 21 },
  listTitle:  { fontFamily: 'Segoe-Semibold', fontSize: 16, lineHeight: 21 },
  author:     { fontFamily: 'Segoe-Semibold', fontSize: 15, lineHeight: 20 },
  body:       { fontFamily: 'Segoe-Regular',  fontSize: 15, lineHeight: 22 },
  button:     { fontFamily: 'Segoe-Semibold', fontSize: 16, lineHeight: 16 },
  metadata:   { fontFamily: 'Segoe-Regular',  fontSize: 13, lineHeight: 18 },
  reaction:   { fontFamily: 'Segoe-Semibold', fontSize: 12, lineHeight: 12 },
  tab:        { fontFamily: 'Segoe-Semibold', fontSize: 10, lineHeight: 12 },
  tinyUpper:  { fontFamily: 'Segoe-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Presence Dot (the loaded semantic color)

```tsx
// components/PresenceDot.tsx
import { View } from 'react-native';
import { shared } from '../theme/colors';

type Presence = 'available' | 'busy' | 'dnd' | 'away' | 'offline';

export function PresenceDot({ presence, size = 10, ringColor }: { presence: Presence; size?: number; ringColor: string }) {
  const fill = presence === 'available' ? shared.available
    : presence === 'busy' || presence === 'dnd' ? shared.busy
    : presence === 'away' ? shared.away : 'transparent';
  return (
    <View style={{
      width: size, height: size, borderRadius: size / 2,
      backgroundColor: fill,
      borderWidth: presence === 'offline' ? 1.5 : 2,
      borderColor: presence === 'offline' ? shared.offline : ringColor,
      alignItems: 'center', justifyContent: 'center',
    }}>
      {presence === 'dnd' && (
        <View style={{ width: size * 0.5, height: size * 0.18, borderRadius: 1, backgroundColor: '#fff' }} />
      )}
    </View>
  );
}

export function AvatarWithPresence({ initials, presence, size = 32 }: { initials: string; presence: Presence; size?: number }) {
  return (
    <View style={{ width: size, height: size }}>
      <View style={{ width: size, height: size, borderRadius: size / 2, backgroundColor: 'rgba(98,100,167,0.25)', alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Segoe-Semibold', fontSize: size * 0.4, color: '#fff' }}>{initials}</Text>
      </View>
      <View style={{ position: 'absolute', right: -1, bottom: -1 }}>
        <PresenceDot presence={presence} size={size * 0.32} ringColor="#1F1F1F" />
      </View>
    </View>
  );
}
import { Text } from 'react-native';
```

### Teams → Channels Tree (the signature element)

```tsx
// components/TeamTree.tsx
import { useState } from 'react';
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, LinearTransition } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useTeams, shared } from '../theme/colors';
import { typography } from '../theme/typography';

export function TeamTree({ team, activeChannel, onSelect }: any) {
  const t = useTeams();
  const [expanded, setExpanded] = useState(true);
  const rot = useSharedValue(1);
  const chev = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value * 90}deg` }] }));

  return (
    <View>
      <Pressable
        style={styles.team}
        onPress={() => { setExpanded((e) => !e); rot.value = withTiming(expanded ? 0 : 1, { duration: 200 }); }}
      >
        <View style={styles.teamAvatar}><Text style={{ fontFamily: 'Segoe-Bold', fontSize: 14, color: '#fff' }}>{team.name[0]}</Text></View>
        <Text style={[typography.teamName, { color: t.text1, flex: 1 }]}>{team.name}</Text>
        <Animated.View style={chev}><Ionicons name="chevron-forward" size={14} color={t.text2} /></Animated.View>
      </Pressable>
      {expanded && team.channels.map((ch: any) => {
        const active = activeChannel === ch.id;
        return (
          <Animated.View key={ch.id} layout={LinearTransition.duration(200)}>
            <Pressable
              onPress={() => onSelect(ch.id)}
              style={[styles.channel, active && { backgroundColor: shared.accentTint }]}
            >
              {active && <View style={[styles.activeBar, { backgroundColor: t.accent }]} />}
              <Text style={{ fontFamily: 'Segoe-Semibold', fontSize: 16, color: t.text2 }}>#</Text>
              <Text style={[typography.listTitle, { color: t.text1, fontFamily: ch.unread ? 'Segoe-Bold' : 'Segoe-Semibold', flex: 1 }]}>{ch.name}</Text>
              {ch.unread && <View style={{ width: 8, height: 8, borderRadius: 4, backgroundColor: t.accent }} />}
            </Pressable>
          </Animated.View>
        );
      })}
    </View>
  );
}

const styles = StyleSheet.create({
  team:       { height: 56, flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16 },
  teamAvatar: { width: 32, height: 32, borderRadius: 8, backgroundColor: 'rgba(98,100,167,0.30)', alignItems: 'center', justifyContent: 'center' },
  channel:    { height: 44, flexDirection: 'row', alignItems: 'center', gap: 8, paddingLeft: 44, paddingRight: 16 },
  activeBar:  { position: 'absolute', left: 0, top: 0, bottom: 0, width: 3 },
});
```

### Message Card

```tsx
// components/MessageCard.tsx
import { View, Text, StyleSheet } from 'react-native';
import { AvatarWithPresence } from './PresenceDot';
import { useTeams, shared } from '../theme/colors';
import { typography } from '../theme/typography';

export function MessageCard({ author, initials, presence, timestamp, body, replyCount }: any) {
  const t = useTeams();
  return (
    <View style={[styles.card, { backgroundColor: t.surface1 }]}>
      <AvatarWithPresence initials={initials} presence={presence} />
      <View style={{ flex: 1, gap: 4 }}>
        <View style={{ flexDirection: 'row', gap: 8, alignItems: 'baseline' }}>
          <Text style={[typography.author, { color: t.text1 }]}>{author}</Text>
          <Text style={[typography.metadata, { color: t.text2 }]}>{timestamp}</Text>
        </View>
        <Text style={[typography.body, { color: t.text1 }]}>{body}</Text>
        <View style={{ flexDirection: 'row', gap: 6, marginTop: 2 }}>
          <Chip emoji="👍" count={3} mine />
          <Chip emoji="❤️" count={1} />
        </View>
        {replyCount > 0 && (
          <Text style={[typography.metadata, { color: t.text2 }]}>💬 {replyCount} replies · Last reply 2h ago</Text>
        )}
      </View>
    </View>
  );
}

function Chip({ emoji, count, mine }: { emoji: string; count: number; mine?: boolean }) {
  const t = useTeams();
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, paddingHorizontal: 8, paddingVertical: 4, borderRadius: 500, backgroundColor: mine ? shared.accentTint : t.surface2 }}>
      <Text style={{ fontSize: 12 }}>{emoji}</Text>
      <Text style={[typography.reaction, { color: t.text2 }]}>{count}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  card: { flexDirection: 'row', gap: 12, padding: 12, borderRadius: 8 },
});
```

### Meeting Join Bar

```tsx
// components/MeetingJoinBar.tsx
import { useEffect } from 'react';
import { View, Text, Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useTeams } from '../theme/colors';

export function MeetingJoinBar({ title, onJoin }: { title: string; onJoin: () => void }) {
  const t = useTeams();
  const o = useSharedValue(1);
  useEffect(() => { o.value = withRepeat(withTiming(0.85, { duration: 2000 }), -1, true); }, []);
  const pulse = useAnimatedStyle(() => ({ opacity: o.value }));
  return (
    <Animated.View style={[{
      marginHorizontal: 12, height: 56, borderRadius: 12, paddingHorizontal: 16,
      flexDirection: 'row', alignItems: 'center', gap: 12, backgroundColor: t.accent,
      shadowColor: '#6264A7', shadowOpacity: 0.35, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
    }, pulse]}>
      <Ionicons name="videocam" size={18} color="#fff" />
      <Text style={{ flex: 1, fontFamily: 'Segoe-Semibold', fontSize: 15, color: '#fff' }}>{title}</Text>
      <Pressable onPress={onJoin} style={{ backgroundColor: '#fff', borderRadius: 500, paddingHorizontal: 18, height: 30, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'Segoe-Semibold', fontSize: 14, color: t.accent }}>Join</Text>
      </Pressable>
    </Animated.View>
  );
}
```

### Primary Button

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { useTeams, shared } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  const t = useTeams();
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        height: 44, paddingHorizontal: 24, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? shared.accentPressed : t.accent,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: '#fff' }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Navigation — Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useTeams } from '../../theme/colors';

export default function TabsLayout() {
  const t = useTeams();
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: t.accent,
        tabBarInactiveTintColor: t.text2,
        tabBarStyle: { backgroundColor: t.surface1, borderTopColor: t.divider },
        tabBarLabelStyle: { fontFamily: 'Segoe-Semibold', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="activity" options={{ title: 'Activity', tabBarBadge: 3, tabBarIcon: ({ color }) => <Ionicons name="notifications" size={24} color={color} /> }} />
      <Tabs.Screen name="chat"     options={{ title: 'Chat',     tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={24} color={color} /> }} />
      <Tabs.Screen name="teams"    options={{ title: 'Teams',    tabBarIcon: ({ color }) => <Ionicons name="people" size={24} color={color} /> }} />
      <Tabs.Screen name="calendar" options={{ title: 'Calendar', tabBarIcon: ({ color }) => <Ionicons name="calendar" size={24} color={color} /> }} />
      <Tabs.Screen name="calls"    options={{ title: 'Calls',    tabBarIcon: ({ color }) => <Ionicons name="call" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Primary button tap: light haptic + scale (handled in Pressable style)
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Tree expand/collapse: chevron rotate (withTiming 200ms) + LinearTransition on child rows
// Reaction picker (long-press): spring scale 0.9 → 1 + opacity
// Presence change: animate dot backgroundColor over 250ms (withTiming on a color shared value)
// Join bar: withRepeat opacity 1.0 ↔ 0.85 over 2000ms; slide+fade on appear
```

## 6. Icon Library

Use `@expo/vector-icons`:

| Purpose | Ionicons |
|---------|----------|
| Activity (tab) | `notifications` |
| Chat (tab) | `chatbubbles` |
| Teams (tab) | `people` |
| Calendar (tab) | `calendar` |
| Calls (tab) | `call` |
| Channel marker | text `#` |
| Expand chevron | `chevron-forward` |
| Meeting / join | `videocam` |
| Send | `send` |
| Attach | `attach` |
| Emoji | `happy-outline` |
| Search | `search` |
| New chat | `create-outline` |
| Reply / thread | `arrow-undo` |
| More | `ellipsis-horizontal` |

## 7. Platform Notes

- **Dual identity**: every component must resolve tokens via `useTeams()` and look correct in both light and dark — test both
- **Status bar**: follow the appearance — `<StatusBar style="auto" />` from `expo-status-bar`
- **Safe area**: wrap with `SafeAreaView` from `react-native-safe-area-context`; composer + tab bar clear the home indicator
- **Presence as text**: never communicate presence by color alone — pair the dot with an accessibility label
- **Tabular numerics**: set `fontVariant: ['tabular-nums']` on call durations and aligned timestamps
- **Dynamic Type**: respect font scaling on names/body/titles; pin the 10pt presence dot and tabular timestamps and 10pt tab labels with `allowFontScaling={false}` where layout is fixed
- **Accessibility**: announce presence as text ("Priya Anand, Available"); mark unread with an "unread" state; expose tree expand/collapse state; group message-card text with `accessibilityRole` and a combined label
