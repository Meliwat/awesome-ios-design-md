# Zoom (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zoom's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#1A1A1A',   // in-call theater + dark mode
  surface1: '#2D2D2D',
  surface2: '#3A3A3A',
  divider:  '#3A3A3A',

  lightCanvas:  '#FFFFFF',
  lightSurface: '#F5F5F5',
  lightDivider: '#E5E5E5',

  textPrimary:   '#FFFFFF',
  textSecondary: '#B0B0B0',
  textTertiary:  '#7A7A7A',

  blue:        '#2D8CFF',
  bluePressed: '#1F6FCC',
  blueTint:    'rgba(45,140,255,0.12)',

  red:         '#E02828',
  redPressed:  '#B91F1F',
  handYellow:  '#F5C518',
  success:     '#0E8A45',
} as const;

export type ZoomColor = keyof typeof colors;
```

## 2. Typography

Zoom uses a custom grotesque; Lato (or Inter) is the closest free substitute. Load via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Lato-Regular':  require('../assets/fonts/Lato-Regular.ttf'),
    'Lato-Semibold': require('../assets/fonts/Lato-Semibold.ttf'),
    'Lato-Bold':     require('../assets/fonts/Lato-Bold.ttf'),
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
  titleLarge:   { ...base, fontFamily: 'Lato-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.3 },
  meetingTopic: { ...base, fontFamily: 'Lato-Bold',     fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  section:      { ...base, fontFamily: 'Lato-Bold',     fontSize: 17, lineHeight: 22, letterSpacing: -0.1 },
  listTitle:    { ...base, fontFamily: 'Lato-Semibold', fontSize: 16, lineHeight: 21 },
  body:         { ...base, fontFamily: 'Lato-Regular',  fontSize: 15, lineHeight: 22 },
  button:       { color: '#FFFFFF', fontFamily: 'Lato-Bold', fontSize: 17, lineHeight: 17 },
  controlLabel: { ...base, fontFamily: 'Lato-Semibold', fontSize: 11, lineHeight: 12 },
  metadata:     {           fontFamily: 'Lato-Regular',  fontSize: 13, lineHeight: 18, color: '#B0B0B0' },
  tileName:     { ...base, fontFamily: 'Lato-Semibold', fontSize: 13, lineHeight: 13 },
  timer:        { ...base, fontFamily: 'Lato-Semibold', fontSize: 14, lineHeight: 14, fontVariant: ['tabular-nums'] as const },
  tab:          {           fontFamily: 'Lato-Semibold', fontSize: 10, lineHeight: 12 },
  tinyUpper:    { ...base, fontFamily: 'Lato-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Gallery Video Tile (the signature element)

```tsx
// components/GalleryTile.tsx
import { View, Text, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GalleryTile({
  name, isMuted, isActiveSpeaker, hasVideo = false,
}: { name: string; isMuted: boolean; isActiveSpeaker: boolean; hasVideo?: boolean }) {
  const initials = name.split(' ').slice(0, 2).map((w) => w[0]).join('');
  return (
    <View style={[styles.tile, isActiveSpeaker && styles.speaking]}>
      {!hasVideo && (
        <View style={styles.avatar}>
          <Text style={styles.avatarText}>{initials}</Text>
        </View>
      )}
      <View style={styles.labelRow}>
        {isMuted && (
          <View style={styles.muteBadge}>
            <Ionicons name="mic-off" size={12} color="#fff" />
          </View>
        )}
        <View style={styles.nameChip}>
          <Text style={typography.tileName}>{name}</Text>
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  tile:      { aspectRatio: 16 / 9, borderRadius: 8, backgroundColor: colors.surface2, overflow: 'hidden', alignItems: 'center', justifyContent: 'center' },
  speaking:  { borderWidth: 3, borderColor: colors.blue },
  avatar:    { width: 56, height: 56, borderRadius: 28, backgroundColor: 'rgba(45,140,255,0.25)', alignItems: 'center', justifyContent: 'center' },
  avatarText:{ fontFamily: 'Lato-Semibold', fontSize: 20, color: '#fff' },
  labelRow:  { position: 'absolute', left: 8, bottom: 8, flexDirection: 'row', alignItems: 'center', gap: 6 },
  muteBadge: { width: 22, height: 22, borderRadius: 11, backgroundColor: colors.red, alignItems: 'center', justifyContent: 'center' },
  nameChip:  { backgroundColor: 'rgba(0,0,0,0.45)', borderRadius: 4, paddingHorizontal: 6, paddingVertical: 3 },
});
```

### Primary "Join" Button (the signature CTA)

```tsx
// components/JoinButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function JoinButton({ title = 'Join', onPress }: { title?: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        height: 48, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### In-Call Control Bar

```tsx
// components/ControlBar.tsx
import { View, Pressable, Text, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function Control({ icon, label, tint, onPress }: any) {
  return (
    <Pressable style={styles.ctrl} onPress={onPress}>
      <Ionicons name={icon} size={24} color={tint} />
      <Text style={[typography.controlLabel, { color: tint }]}>{label}</Text>
    </Pressable>
  );
}

export function ControlBar({ micOn, videoOn, onToggleMic, onToggleVideo, onLeave }: any) {
  return (
    <View style={styles.bar}>
      <Control icon={micOn ? 'mic' : 'mic-off'} label={micOn ? 'Mute' : 'Unmute'}
        tint={micOn ? '#fff' : colors.red} onPress={onToggleMic} />
      <Control icon={videoOn ? 'videocam' : 'videocam-off'} label={videoOn ? 'Stop Video' : 'Start Video'}
        tint="#fff" onPress={onToggleVideo} />
      <Control icon="share-outline" label="Share" tint="#fff" onPress={() => {}} />
      <Control icon="people" label="Participants" tint="#fff" onPress={() => {}} />
      <Control icon="happy-outline" label="React" tint="#fff" onPress={() => {}} />
      <Pressable style={styles.leave} onPress={onLeave}>
        <Text style={{ color: '#fff', fontFamily: 'Lato-Bold', fontSize: 15 }}>Leave</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  bar:   { height: 72, marginHorizontal: 12, paddingHorizontal: 12, borderRadius: 16,
           flexDirection: 'row', alignItems: 'center',
           backgroundColor: 'rgba(45,45,45,0.96)',
           shadowColor: '#000', shadowOpacity: 0.4, shadowRadius: 24, shadowOffset: { width: 0, height: 8 } },
  ctrl:  { flex: 1, alignItems: 'center', gap: 4 },
  leave: { marginLeft: 4, paddingHorizontal: 18, paddingVertical: 8, borderRadius: 8, backgroundColor: colors.red },
});
```

### Meeting List Row

```tsx
// components/MeetingRow.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MeetingRow({
  time, topic, subtitle, onJoin,
}: { time: string; topic: string; subtitle: string; onJoin: () => void }) {
  return (
    <View style={styles.row}>
      <Text style={[typography.metadata, styles.time]}>{time}</Text>
      <View style={{ flex: 1, gap: 4 }}>
        <Text style={typography.listTitle}>{topic}</Text>
        <Text style={typography.metadata}>{subtitle}</Text>
      </View>
      <Pressable
        onPress={onJoin}
        style={({ pressed }) => [styles.joinPill, pressed && { backgroundColor: colors.bluePressed }]}
      >
        <Text style={{ color: '#fff', fontFamily: 'Lato-Bold', fontSize: 14 }}>Join</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  row:      { height: 72, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 12, backgroundColor: colors.surface1 },
  time:     { width: 64 },
  joinPill: { paddingHorizontal: 18, height: 32, borderRadius: 500, backgroundColor: colors.blue, alignItems: 'center', justifyContent: 'center' },
});
```

### Recording Indicator

```tsx
// components/RecordingIndicator.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RecordingIndicator() {
  const s = useSharedValue(1);
  useEffect(() => { s.value = withRepeat(withTiming(0.7, { duration: 1200 }), -1, true); }, []);
  const dot = useAnimatedStyle(() => ({ transform: [{ scale: s.value }] }));
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 10, paddingVertical: 6, borderRadius: 500, backgroundColor: 'rgba(0,0,0,0.45)' }}>
      <Animated.View style={[{ width: 10, height: 10, borderRadius: 5, backgroundColor: colors.red }, dot]} />
      <Text style={typography.tinyUpper}>Recording</Text>
    </View>
  );
}
```

## 4. Gallery Grid Layout

Compute columns from participant count; animate reflow on join/leave.

```tsx
// components/GalleryGrid.tsx
import { View } from 'react-native';
import Animated, { LinearTransition } from 'react-native-reanimated';
import { GalleryTile } from './GalleryTile';

export function GalleryGrid({ participants }: { participants: any[] }) {
  const cols = participants.length <= 1 ? 1 : participants.length <= 4 ? 2 : 3;
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', padding: 4 }}>
      {participants.map((p) => (
        <Animated.View
          key={p.id}
          layout={LinearTransition.duration(300)}
          style={{ width: `${100 / cols}%`, padding: 2 }}
        >
          <GalleryTile name={p.name} isMuted={p.isMuted} isActiveSpeaker={p.isSpeaking} />
        </Animated.View>
      ))}
    </View>
  );
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Lato-Semibold', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="meetings" options={{ title: 'Meetings',  tabBarIcon: ({ color }) => <Ionicons name="videocam"  size={24} color={color} /> }} />
      <Tabs.Screen name="chat"     options={{ title: 'Team Chat', tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={24} color={color} /> }} />
      <Tabs.Screen name="mail"     options={{ title: 'Mail',      tabBarIcon: ({ color }) => <Ionicons name="mail"      size={24} color={color} /> }} />
      <Tabs.Screen name="phone"    options={{ title: 'Phone',     tabBarIcon: ({ color }) => <Ionicons name="call"      size={24} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',      tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Join button tap: medium haptic + scale (handled in Pressable style)
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Active-speaker border (debounced ~300ms upstream): toggle borderWidth with a timing animation
import Animated, { useAnimatedStyle, withTiming } from 'react-native-reanimated';
const border = useAnimatedStyle(() => ({ borderWidth: withTiming(speaking ? 3 : 0, { duration: 220 }) }));

// Control bar auto-hide: translateY + opacity over 250ms after 4s idle
// Reactions float: translateY 0 → -120 + opacity 1 → 0 over ~3s
// Grid reflow: LinearTransition.duration(300) on each tile
```

## 7. Icon Library

Use `@expo/vector-icons`. Map to Zoom's controls:

| Purpose | Ionicons |
|---------|----------|
| Mic on / off | `mic` / `mic-off` |
| Video on / off | `videocam` / `videocam-off` |
| Share | `share-outline` |
| Participants | `people` |
| React | `happy-outline` |
| Leave (red) | `call` (rotated) |
| Raise hand | `hand-left` |
| Camera flip | `camera-reverse` |
| Search | `search` |
| Meetings | `videocam` |
| Team Chat | `chatbubbles` |
| Mail | `mail` |
| Phone | `call` |
| More | `ellipsis-horizontal` |

## 8. Platform Notes

- **In-call theater**: force the in-call screen to the dark canvas `#1A1A1A` regardless of app appearance; only the outside-call surfaces follow light/dark
- **Status bar**: `<StatusBar style="light" />` while in a call; follow appearance elsewhere
- **Safe area**: wrap with `SafeAreaView` from `react-native-safe-area-context`; the floating control bar must sit above the home indicator with a 12pt inset
- **Keep-awake**: use `expo-keep-awake` so the screen does not dim during a call
- **Tabular numerics**: set `fontVariant: ['tabular-nums']` on the call timer and meeting IDs so they never reflow
- **Dynamic Type**: respect font scaling on titles/body; clamp tile name labels and set `allowFontScaling={false}` on the timer and 11pt control labels where layout is fixed
- **Accessibility**: announce mic/video state changes; label the active-speaker tile; keep the control bar in the accessibility tree even when visually auto-hidden, and disable auto-hide under a screen reader or reduced-motion
