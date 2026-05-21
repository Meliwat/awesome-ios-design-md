# Viber (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Viber's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand purple
  viberPurple:        '#7360F2',
  viberPurpleDeep:    '#665CAC',
  viberPurplePressed: '#5B4ACC',
  viberViolet:        '#8F7DF7',
  viberBannerDeep:    '#59267C',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  incoming:      '#EDEBF5',
  surfaceGray:   '#F4F3F8',
  rowPressed:    '#E9E7F2',
  divider:       '#E4E2EC',

  // Surfaces (dark) — aubergine-tinted, NOT neutral
  darkCanvas:    '#121118',
  darkSurface1:  '#1C1A24',
  darkSurface2:  '#26232F',
  darkDivider:   '#322F3C',

  // Text
  textPrimary:       '#1A1825',
  textPrimaryDark:   '#F3F1F8',
  onPurple:          '#FFFFFF',
  textSecondary:     '#7D7A8C',
  textSecondaryDark: '#9D98AC',
  textTertiary:      '#A8A5B5',
  textTertiaryDark:  '#6C6779',

  // Semantic
  green: '#46C26A',
  red:   '#F0506E',
  link:  '#7360F2',
  linkDark: '#9B8CFF',
} as const;

export type ViberColor = keyof typeof colors;

export const receiptColor = (state: 'sending' | 'sent' | 'delivered' | 'seen' | 'failed', dark: boolean) => {
  switch (state) {
    case 'sending':
    case 'sent':      return dark ? colors.textTertiaryDark : colors.textTertiary;
    case 'delivered': return dark ? colors.textSecondaryDark : colors.textSecondary;
    case 'seen':      return colors.viberViolet;
    case 'failed':    return colors.red;
  }
};
```

## 2. Typography

Viber's brand face is proprietary; bundle **Manrope** (SIL OFL) via `expo-font` as the closest free substitute.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

const [loaded] = useFonts({
  'Manrope-Regular':   require('../assets/fonts/Manrope-Regular.ttf'),
  'Manrope-Medium':    require('../assets/fonts/Manrope-Medium.ttf'),
  'Manrope-SemiBold':  require('../assets/fonts/Manrope-SemiBold.ttf'),
  'Manrope-Bold':      require('../assets/fonts/Manrope-Bold.ttf'),
  'Manrope-ExtraBold': require('../assets/fonts/Manrope-ExtraBold.ttf'),
});
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  largeTitle: { fontFamily: 'Manrope-ExtraBold', fontSize: 32, letterSpacing: -0.4 },
  title1:     { fontFamily: 'Manrope-ExtraBold', fontSize: 26, letterSpacing: -0.3 },
  title3:     { fontFamily: 'Manrope-Bold',      fontSize: 22, letterSpacing: -0.2 },
  headline:   { fontFamily: 'Manrope-Bold',      fontSize: 17, letterSpacing: -0.2 },
  body:       { fontFamily: 'Manrope-Regular',   fontSize: 15, lineHeight: 20 },
  bodyEmph:   { fontFamily: 'Manrope-SemiBold',  fontSize: 15, lineHeight: 20 },
  preview:    { fontFamily: 'Manrope-Medium',    fontSize: 14 },
  footnote:   { fontFamily: 'Manrope-SemiBold',  fontSize: 13 },
  caption:    { fontFamily: 'Manrope-SemiBold',  fontSize: 11, letterSpacing: 0.1 },
  button:     { fontFamily: 'Manrope-Bold',      fontSize: 16 },
  tab:        { fontFamily: 'Manrope-SemiBold',  fontSize: 11, letterSpacing: 0.1 },
  badge:      { fontFamily: 'Manrope-Bold',      fontSize: 11 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```tsx
// components/ViberBubble.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { ReceiptCheck, ReceiptState } from './ReceiptCheck';

export function ViberBubble({
  text, outgoing, tailEnd, timestamp, receipt,
}: { text: string; outgoing: boolean; tailEnd: boolean; timestamp: string; receipt: ReceiptState }) {
  const dark = useColorScheme() === 'dark';
  const radius = {
    borderTopLeftRadius: 16,
    borderTopRightRadius: 16,
    borderBottomLeftRadius: !outgoing && tailEnd ? 5 : 16,
    borderBottomRightRadius: outgoing && tailEnd ? 5 : 16,
  };
  return (
    <View style={{ width: '100%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
      <View style={{ maxWidth: '80%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
        <View style={[{ backgroundColor: outgoing ? colors.viberPurple : (dark ? colors.darkSurface2 : colors.incoming), paddingVertical: 9, paddingHorizontal: 14 }, radius]}>
          <Text style={[typography.body, { color: outgoing ? colors.onPurple : (dark ? colors.textPrimaryDark : colors.textPrimary) }]}>{text}</Text>
        </View>
        {outgoing && (
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 3, marginRight: 4 }}>
            <Text allowFontScaling={false} style={{ fontSize: 10, color: dark ? colors.textSecondaryDark : colors.textSecondary }}>{timestamp}</Text>
            <ReceiptCheck state={receipt} />
          </View>
        )}
      </View>
    </View>
  );
}
```

### Three-State Check Receipt

```tsx
// components/ReceiptCheck.tsx
import { View, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, receiptColor } from '../theme/colors';

export type ReceiptState = 'sending' | 'sent' | 'delivered' | 'seen' | 'failed';

export function ReceiptCheck({ state }: { state: ReceiptState }) {
  const dark = useColorScheme() === 'dark';
  const c = receiptColor(state, dark);
  if (state === 'sending') return <Ionicons name="time-outline" size={12} color={c} />;
  if (state === 'failed')  return <Ionicons name="alert-circle" size={12} color={c} />;
  if (state === 'sent')    return <Ionicons name="checkmark" size={13} color={c} />;
  // delivered / seen — double check
  return (
    <View style={{ width: 18, height: 12 }}>
      <Ionicons name="checkmark" size={13} color={c} style={{ position: 'absolute', left: 0 }} />
      <Ionicons name="checkmark" size={13} color={c} style={{ position: 'absolute', left: 5 }} />
    </View>
  );
}
```

### Free Viber Call Banner

```tsx
// components/FreeCallBanner.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function FreeCallBanner({ name, onCall }: { name: string; onCall: () => void }) {
  return (
    <LinearGradient
      colors={[colors.viberPurple, colors.viberBannerDeep]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 10,
        marginHorizontal: 14, marginTop: 10,
        padding: 12, borderRadius: 14,
        shadowColor: colors.viberPurple, shadowOpacity: 0.10, shadowRadius: 8, shadowOffset: { width: 0, height: 2 }, elevation: 3,
      }}
    >
      <Ionicons name="call" size={18} color="#FFF" />
      <View style={{ flex: 1 }}>
        <Text style={{ fontSize: 13, fontWeight: '700', color: '#FFF' }}>Free Viber Call</Text>
        <Text style={{ fontSize: 11, color: 'rgba(255,255,255,0.8)' }}>Call {name} free over Viber-to-Viber</Text>
      </View>
      <Pressable onPress={onCall} style={{ backgroundColor: 'rgba(255,255,255,0.22)', paddingHorizontal: 12, paddingVertical: 6, borderRadius: 999 }}>
        <Text style={{ fontSize: 12, fontWeight: '700', color: '#FFF' }}>Call</Text>
      </Pressable>
    </LinearGradient>
  );
}
```

### In-Thread Sticker

```tsx
// components/StickerMessage.tsx
import { Text, View, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { ReceiptCheck, ReceiptState } from './ReceiptCheck';

export function StickerMessage({ outgoing, timestamp, receipt }: { outgoing: boolean; timestamp: string; receipt: ReceiptState }) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{ width: '100%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
      {/* Replace with a Lottie / animated APNG sticker */}
      <LinearGradient colors={[colors.viberViolet, colors.viberBannerDeep]} style={{ width: 96, height: 96, borderRadius: 20 }} />
      {outgoing && (
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 3, marginRight: 4 }}>
          <Text allowFontScaling={false} style={{ fontSize: 10, color: dark ? colors.textSecondaryDark : colors.textSecondary }}>{timestamp}</Text>
          <ReceiptCheck state={receipt} />
        </View>
      )}
    </View>
  );
}
```

### Compose Bar

```tsx
// components/ViberComposeBar.tsx
import { useState } from 'react';
import { Pressable, TextInput, View, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ViberComposeBar({ onSend }: { onSend: (t: string) => void }) {
  const [text, setText] = useState('');
  const dark = useColorScheme() === 'dark';
  const empty = text.trim().length === 0;
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14, paddingVertical: 8, backgroundColor: dark ? colors.darkSurface1 : colors.canvas, borderTopWidth: 0.5, borderTopColor: dark ? colors.darkDivider : colors.divider }}>
      <Pressable><Ionicons name="add" size={22} color={dark ? colors.textSecondaryDark : colors.textSecondary} /></Pressable>
      <View style={{ flex: 1, flexDirection: 'row', alignItems: 'center', minHeight: 36, borderRadius: 18, backgroundColor: dark ? colors.darkSurface2 : colors.surfaceGray, paddingHorizontal: 14 }}>
        <TextInput
          value={text} onChangeText={setText}
          placeholder="Type a message"
          placeholderTextColor={dark ? colors.textTertiaryDark : colors.textTertiary}
          multiline
          style={[typography.body, { flex: 1, color: dark ? colors.textPrimaryDark : colors.textPrimary, maxHeight: 110 }]}
        />
        <Pressable><Ionicons name="happy-outline" size={22} color={dark ? colors.textSecondaryDark : colors.textSecondary} /></Pressable>
      </View>
      <Pressable
        onPress={() => { if (!empty) { onSend(text); setText(''); } }}
        style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center', backgroundColor: colors.viberPurple }}
      >
        <Ionicons name={empty ? 'mic' : 'paper-plane'} size={16} color="#FFF" />
      </Pressable>
    </View>
  );
}
```

### Chat Header (call-first)

```tsx
// components/ViberChatHeader.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ViberChatHeader({ name, initials, online }: { name: string; initials: string; online: boolean }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14, paddingVertical: 8 }}>
      <Pressable><Ionicons name="chevron-back" size={20} color={colors.viberPurple} /></Pressable>
      <LinearGradient colors={[colors.viberViolet, colors.viberPurpleDeep]} style={{ width: 36, height: 36, borderRadius: 18, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#FFF', fontSize: 14, fontWeight: '700' }}>{initials}</Text>
      </LinearGradient>
      <View style={{ flex: 1 }}>
        <Text style={typography.headline}>{name}</Text>
        {online && <Text style={[typography.caption, { color: colors.green }]}>online</Text>}
      </View>
      <Pressable style={{ marginRight: 16 }}><Ionicons name="call" size={22} color={colors.viberPurple} /></Pressable>
      <Pressable><Ionicons name="videocam" size={22} color={colors.viberPurple} /></Pressable>
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
        tabBarActiveTintColor: colors.viberPurple,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Manrope-SemiBold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Chats',   tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={22} color={color} /> }} />
      <Tabs.Screen name="calls"   options={{ title: 'Calls',   tabBarIcon: ({ color }) => <Ionicons name="call" size={22} color={color} /> }} />
      <Tabs.Screen name="explore" options={{ title: 'Explore', tabBarIcon: ({ color }) => <Ionicons name="happy" size={22} color={color} />, tabBarBadge: 3, tabBarBadgeStyle: { backgroundColor: colors.red } }} />
      <Tabs.Screen name="more"    options={{ title: 'More',    tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { SlideInDown, FadeIn, FadeOut } from 'react-native-reanimated';

// Outgoing bubble — slide up + scale from input
// <Animated.View entering={SlideInDown.springify().damping(15)}>

// Receipt sent → delivered → seen — re-render with FadeIn.duration(200); on 'seen' add a quick scale pulse

// Reactions strip — <Pressable delayLongPress={450} onLongPress={...}>; strip uses FadeIn.duration(180)

// Sticker market open — <Animated.View entering={SlideInDown.duration(300)}>

// Incoming call screen — gradient FadeIn.duration(300); accept/decline buttons SlideInDown.springify()

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // send
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // reaction dock
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // call connect
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning); // call decline
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Back | `chevron.left` | `chevron-back` |
| Voice call | `phone` | `call` |
| Video call | `video` | `videocam` |
| Attachment | `plus` | `add` |
| Emoji / sticker | `face.smiling` | `happy-outline` |
| Send | `paperplane.fill` | `paper-plane` |
| Voice message | `mic.fill` | `mic` |
| Sent | `checkmark` | `checkmark` |
| Delivered / Seen | `checkmark` ×2 | `checkmark` ×2 |
| Sending | `clock` | `time-outline` |
| Failed | `exclamationmark.circle.fill` | `alert-circle` |
| Chats (tab) | `bubble.left.fill` | `chatbubble` |
| Calls (tab) | `phone.fill` | `call` |
| Explore (tab) | `smiley` | `happy` |
| More (tab) | `person.crop.circle` | `person-circle` |
| Search | `magnifyingglass` | `search` |
| Reaction | `hand.thumbsup.fill` | `thumbs-up` |
| Secret message | `timer` | `timer-outline` |

## 7. Platform Notes

- **Font**: Viber's brand face is proprietary — bundle Manrope (SIL OFL) via `expo-font` as the closest free substitute; keep heavy 700/800 weights for the friendly feel.
- **Stickers**: render as Lottie (`lottie-react-native`) or animated APNG at ~96pt; auto-play on appear, ~2s loop. Never wrap in a bubble.
- **Status bar**: `<StatusBar style="auto" />` — dark content on white, light on the aubergine dark canvas.
- **Safe area**: wrap screens in `SafeAreaView`; compose + tab bars need bottom safe-area padding; thread in `KeyboardAvoidingView`.
- **Dynamic Type**: leave `allowFontScaling` default on titles/body/previews; set `allowFontScaling={false}` on timestamps, receipts, tab labels, badges, presence, and sticker artwork.
- **Dark mode**: use `useColorScheme()`; dark canvas must be the aubergine `#121118` (not neutral gray); incoming `#26232F`; add a 1px `darkDivider` border to floating menus as the dark elevation cue.
- **Bubble grouping**: track sender runs; only the last bubble of a run gets `tailEnd`; 4px within a run, 8px on sender change.
- **Reduce Motion**: gate `SlideInDown` and sticker auto-loop behind `AccessibilityInfo.isReduceMotionEnabled()`; show the sticker's first frame, play on tap.
- **Accessibility**: `accessibilityLabel` on bubbles with receipt state ("You said…, Seen"); receipt conveyed by glyph shape + color; expose Reactions via `accessibilityActions`.
