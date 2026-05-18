# Skype (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Skype's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand Skype Blue
  skypeBlue:        '#00AFF0',
  skypeBlueDeep:    '#0078D4',
  skypeBluePressed: '#0091CC',
  skypeCyan:        '#34C3FF',
  inkOnBright:      '#00343F',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  incoming:      '#EBEBEF',
  surfaceGray:   '#F4F4F6',
  rowPressed:    '#E8E8EC',
  divider:       '#E2E2E6',

  // Surfaces (dark) — neutral near-black
  darkCanvas:    '#16161A',
  darkSurface1:  '#1F1F24',
  darkSurface2:  '#2A2A30',
  darkDivider:   '#34343C',

  // Text
  textPrimary:       '#1B1B1F',
  textPrimaryDark:   '#F2F2F4',
  onBlue:            '#FFFFFF',
  textSecondary:     '#6E6E78',
  textSecondaryDark: '#A4A4AE',
  textTertiary:      '#9A9AA4',
  textTertiaryDark:  '#6F6F79',

  // Presence & semantic
  green:  '#2DC26B',
  yellow: '#FFC400',
  red:    '#E8364F',
  link:   '#0078D4',
  linkDark: '#4FBDF0',
} as const;

export type SkypeColor = keyof typeof colors;

export const presenceColor = (p: 'active' | 'away' | 'dnd' | 'inCall') => ({
  active: colors.green, away: colors.yellow, dnd: colors.red, inCall: colors.skypeBlue,
}[p]);
```

## 2. Typography

Skype's brand face is Segoe UI (proprietary); bundle **Inter** (SIL OFL) via `expo-font` as the closest free substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

const [loaded] = useFonts({
  'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
  'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
  'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
  'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
  'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
});
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  largeTitle: { fontFamily: 'Inter-ExtraBold', fontSize: 32, letterSpacing: -0.4 },
  title1:     { fontFamily: 'Inter-Bold',      fontSize: 26, letterSpacing: -0.3 },
  title3:     { fontFamily: 'Inter-Bold',      fontSize: 22, letterSpacing: -0.2 },
  headline:   { fontFamily: 'Inter-Bold',      fontSize: 17, letterSpacing: -0.2 },
  body:       { fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 20 },
  bodyEmph:   { fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  preview:    { fontFamily: 'Inter-Medium',    fontSize: 14 },
  footnote:   { fontFamily: 'Inter-SemiBold',  fontSize: 13 },
  caption:    { fontFamily: 'Inter-SemiBold',  fontSize: 11, letterSpacing: 0.1 },
  button:     { fontFamily: 'Inter-Bold',      fontSize: 16 },
  tab:        { fontFamily: 'Inter-SemiBold',  fontSize: 11, letterSpacing: 0.1 },
  badge:      { fontFamily: 'Inter-Bold',      fontSize: 11 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```tsx
// components/SkypeBubble.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SkypeBubble({
  text, outgoing, tailEnd, meta,
}: { text: string; outgoing: boolean; tailEnd: boolean; meta: string }) {
  const dark = useColorScheme() === 'dark';
  const radius = {
    borderTopLeftRadius: 14, borderTopRightRadius: 14,
    borderBottomLeftRadius: !outgoing && tailEnd ? 4 : 14,
    borderBottomRightRadius: outgoing && tailEnd ? 4 : 14,
  };
  return (
    <View style={{ width: '100%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
      <View style={{ maxWidth: '80%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
        <View style={[{ backgroundColor: outgoing ? colors.skypeBlueDeep : (dark ? colors.darkSurface2 : colors.incoming), paddingVertical: 9, paddingHorizontal: 14 }, radius]}>
          <Text style={[typography.body, { color: outgoing ? colors.onBlue : (dark ? colors.textPrimaryDark : colors.textPrimary) }]}>{text}</Text>
        </View>
        {outgoing && (
          <Text allowFontScaling={false} style={{ fontSize: 10, marginTop: 4, marginRight: 4, color: dark ? colors.textTertiaryDark : colors.textTertiary }}>{meta}</Text>
        )}
      </View>
    </View>
  );
}
```

### Inline Call Card

```tsx
// components/CallCard.tsx
import { Text, View, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function CallCard({ title, subtitle, answered }: { title: string; subtitle: string; answered: boolean }) {
  const dark = useColorScheme() === 'dark';
  const tint = answered ? colors.green : colors.red;
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10, alignSelf: 'flex-start', maxWidth: '80%',
      paddingVertical: 10, paddingHorizontal: 14, borderRadius: 14,
      backgroundColor: dark ? colors.darkSurface2 : colors.surfaceGray,
    }}>
      <View style={{ width: 30, height: 30, borderRadius: 15, alignItems: 'center', justifyContent: 'center', backgroundColor: `${tint}2E` }}>
        <Ionicons name={answered ? 'call' : 'call-outline'} size={15} color={tint} />
      </View>
      <View>
        <Text style={{ fontSize: 13, fontWeight: '700', color: dark ? colors.textPrimaryDark : colors.textPrimary }}>{title}</Text>
        <Text style={{ fontSize: 11, color: dark ? colors.textSecondaryDark : colors.textSecondary }}>{subtitle}</Text>
      </View>
    </View>
  );
}
```

### Video Tile + Control Bar

```tsx
// components/VideoCall.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function VideoTile({ name, muted, gradient }: { name: string; muted: boolean; gradient: [string, string] }) {
  return (
    <LinearGradient colors={gradient} style={{ flex: 1, borderRadius: 14, overflow: 'hidden', justifyContent: 'flex-end' }}>
      <View style={{ position: 'absolute', bottom: 8, left: 8, backgroundColor: 'rgba(0,0,0,0.35)', paddingHorizontal: 8, paddingVertical: 4, borderRadius: 999 }}>
        <Text style={{ color: '#FFF', fontSize: 11, fontWeight: '600' }}>{name}</Text>
      </View>
      {muted && (
        <View style={{ position: 'absolute', top: 8, right: 8, width: 18, height: 18, borderRadius: 9, backgroundColor: 'rgba(0,0,0,0.45)', alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="mic-off" size={10} color="#FFF" />
        </View>
      )}
    </LinearGradient>
  );
}

export function CallControlBar({ muted, videoOn, onMute, onVideo, onEnd }: {
  muted: boolean; videoOn: boolean; onMute: () => void; onVideo: () => void; onEnd: () => void;
}) {
  const Btn = ({ icon, bg, onPress }: { icon: any; bg: string; onPress: () => void }) => (
    <Pressable onPress={onPress} style={{ width: 56, height: 56, borderRadius: 28, alignItems: 'center', justifyContent: 'center', backgroundColor: bg }}>
      <Ionicons name={icon} size={22} color="#FFF" />
    </Pressable>
  );
  return (
    <View style={{ flexDirection: 'row', gap: 14, alignSelf: 'center', backgroundColor: 'rgba(0,0,0,0.4)', padding: 16, borderRadius: 999 }}>
      <Btn icon={muted ? 'mic-off' : 'mic'} bg={colors.darkSurface2} onPress={onMute} />
      <Btn icon={videoOn ? 'videocam' : 'videocam-off'} bg={colors.darkSurface2} onPress={onVideo} />
      <Btn icon="copy" bg={colors.darkSurface2} onPress={() => {}} />
      <Btn icon="call" bg={colors.red} onPress={onEnd} />
    </View>
  );
}
```

### Presence Dot

```tsx
// components/PresenceDot.tsx
import { View, useColorScheme } from 'react-native';
import { colors, presenceColor } from '../theme/colors';

export function PresenceDot({ presence }: { presence: 'active' | 'away' | 'dnd' | 'inCall' | 'offline' }) {
  const dark = useColorScheme() === 'dark';
  const ring = dark ? colors.darkCanvas : colors.canvas;
  if (presence === 'offline') {
    return <View style={{ width: 11, height: 11, borderRadius: 6, borderWidth: 2, borderColor: colors.textTertiary, backgroundColor: ring }} />;
  }
  return <View style={{ width: 11, height: 11, borderRadius: 6, backgroundColor: presenceColor(presence), borderWidth: 2, borderColor: ring }} />;
}
```

### Compose Bar

```tsx
// components/SkypeComposeBar.tsx
import { useState } from 'react';
import { Pressable, TextInput, View, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SkypeComposeBar({ onSend }: { onSend: (t: string) => void }) {
  const [text, setText] = useState('');
  const dark = useColorScheme() === 'dark';
  const empty = text.trim().length === 0;
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14, paddingVertical: 8, backgroundColor: dark ? colors.darkSurface1 : colors.canvas, borderTopWidth: 0.5, borderTopColor: dark ? colors.darkDivider : colors.divider }}>
      <Pressable><Ionicons name="add" size={22} color={dark ? colors.textSecondaryDark : colors.textSecondary} /></Pressable>
      <View style={{ flex: 1, flexDirection: 'row', alignItems: 'center', minHeight: 36, borderRadius: 18, backgroundColor: dark ? colors.darkSurface2 : colors.surfaceGray, paddingHorizontal: 14 }}>
        <TextInput value={text} onChangeText={setText} placeholder="Type a message"
          placeholderTextColor={dark ? colors.textTertiaryDark : colors.textTertiary} multiline
          style={[typography.body, { flex: 1, color: dark ? colors.textPrimaryDark : colors.textPrimary, maxHeight: 110 }]} />
        <Pressable><Ionicons name="happy-outline" size={22} color={dark ? colors.textSecondaryDark : colors.textSecondary} /></Pressable>
      </View>
      <Pressable onPress={() => { if (!empty) { onSend(text); setText(''); } }}
        style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.skypeBlue }}>
        <Ionicons name={empty ? 'mic' : 'paper-plane'} size={16} color={colors.inkOnBright} />
      </Pressable>
    </View>
  );
}
```

### Chat Header (call-first)

```tsx
// components/SkypeChatHeader.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { PresenceDot } from './PresenceDot';

export function SkypeChatHeader({ name, initials, status }: { name: string; initials: string; status: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14, paddingVertical: 8 }}>
      <Pressable><Ionicons name="chevron-back" size={20} color={colors.skypeBlue} /></Pressable>
      <View>
        <LinearGradient colors={[colors.skypeCyan, colors.skypeBlueDeep]} style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={{ color: '#FFF', fontSize: 14, fontWeight: '700' }}>{initials}</Text>
        </LinearGradient>
        <View style={{ position: 'absolute', bottom: -1, right: -1 }}><PresenceDot presence="active" /></View>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={typography.headline}>{name}</Text>
        <Text style={[typography.caption, { color: colors.green }]}>{status}</Text>
      </View>
      <Pressable style={{ marginRight: 16 }}><Ionicons name="call" size={22} color={colors.skypeBlue} /></Pressable>
      <Pressable><Ionicons name="videocam" size={22} color={colors.skypeBlue} /></Pressable>
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
        tabBarActiveTintColor: colors.skypeBlue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={22} color={color} /> }} />
      <Tabs.Screen name="calls"    options={{ title: 'Calls',    tabBarIcon: ({ color }) => <Ionicons name="call" size={22} color={color} /> }} />
      <Tabs.Screen name="contacts" options={{ title: 'Contacts', tabBarIcon: ({ color }) => <Ionicons name="people" size={22} color={color} />, tabBarBadge: 5, tabBarBadgeStyle: { backgroundColor: colors.red } }} />
      <Tabs.Screen name="alerts"   options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { SlideInDown, FadeIn, FadeOut, ZoomIn } from 'react-native-reanimated';

// Outgoing bubble — slide up + scale from input
// <Animated.View entering={SlideInDown.springify().damping(16)}>

// Reactions strip — <Pressable delayLongPress={450} onLongPress={...}>; strip uses FadeIn.duration(180)

// Call card insert — <Animated.View entering={FadeIn.duration(250)}>

// Video tile join — entering={ZoomIn.duration(300)}; leave — exiting={FadeOut.duration(300)} + grid reflow

// Call connect — ringing FadeOut → grid FadeIn (300ms); control bar SlideInDown.springify()

// Presence change — re-render dot with FadeIn.duration(200) color swap

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // send
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // reaction dock
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // call connect
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning); // call end
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Back | `chevron.left` | `chevron-back` |
| Voice call | `phone` | `call` |
| Video call | `video` | `videocam` |
| End call | `phone.down.fill` | `call` (rotated) |
| Mute | `mic.fill` / `mic.slash.fill` | `mic` / `mic-off` |
| Share screen | `rectangle.on.rectangle` | `copy` |
| Attachment | `plus` | `add` |
| Emoji | `face.smiling` | `happy-outline` |
| Send | `paperplane.fill` | `paper-plane` |
| Voice message | `mic.fill` | `mic` |
| Chats (tab) | `bubble.left.fill` | `chatbubble` |
| Calls (tab) | `phone.fill` | `call` |
| Contacts (tab) | `person.2.fill` | `people` |
| Notifications (tab) | `bell.fill` | `notifications` |
| Search | `magnifyingglass` | `search` |
| Meet Now | `video.badge.plus` | `videocam-outline` |
| Reaction | `heart.fill` | `heart` |
| Add people | `person.badge.plus` | `person-add` |

## 7. Platform Notes

- **Font**: Skype's brand face is Segoe UI (proprietary cross-platform) — bundle Inter (SIL OFL) via `expo-font`; keep heavy 700/800 weights for titles/buttons.
- **Video**: render real tiles with `expo-camera` / a WebRTC layer; the gradient `LinearGradient` is the avatar fallback when video is off. Self-view is a smaller pinned tile.
- **Status bar**: `<StatusBar style="auto" />` — dark content on white, light on the neutral dark canvas; force `light` during a call.
- **Safe area**: wrap screens in `SafeAreaView`; compose + tab + call-control bars need bottom safe-area padding; thread in `KeyboardAvoidingView`.
- **Dynamic Type**: leave `allowFontScaling` default on titles/body/previews/call-card text; set `allowFontScaling={false}` on timestamps, tab labels, badges, presence, and per-tile mic/name labels.
- **Dark mode**: use `useColorScheme()`; dark canvas is the neutral `#16161A` (not color-cast); incoming `#2A2A30`; add a 1px `darkDivider` border to floating menus as the dark elevation cue.
- **Call-control scrim**: `expo-blur` `BlurView` (or `rgba(0,0,0,0.4)` capsule) behind the control bar; solid `darkSurface2` when Reduce Transparency is on.
- **Reduce Motion**: gate `SlideInDown`/`ZoomIn` behind `AccessibilityInfo.isReduceMotionEnabled()`; tiles fade instead of scaling.
- **Accessibility**: `accessibilityLabel` on bubbles with delivery state; call cards announce title+subtitle; presence dots are the avatar's `accessibilityValue`; expose Reactions via `accessibilityActions`; tiles label name + muted state.
