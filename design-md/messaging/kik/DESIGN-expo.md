# Kik (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Kik's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand Kik Blue (modern) + Green (heritage)
  kikBlue:        '#00B0F0',
  kikBlueDeep:    '#0093C8',
  kikBluePressed: '#0086B8',
  kikCyan:        '#4FCBF7',
  kikGreenLegacy: '#82BC23', // heritage accent only
  inkOnBlue:      '#002A36',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  incoming:      '#E9EAEC',
  surfaceGray:   '#F3F4F6',
  rowPressed:    '#E7E8EB',
  divider:       '#E1E2E5',

  // Surfaces (dark) — neutral near-black
  darkCanvas:    '#121316',
  darkSurface1:  '#1B1D21',
  darkSurface2:  '#25282D',
  darkDivider:   '#31343A',

  // Text
  textPrimary:       '#16181B',
  textPrimaryDark:   '#F1F2F4',
  textSecondary:     '#6C6F77',
  textSecondaryDark: '#9CA0A8',
  textTertiary:      '#9A9DA5',
  textTertiaryDark:  '#686C74',

  // Semantic
  error: '#F0473E',
  link:  '#0093C8',
  linkDark: '#4FCBF7',
} as const;

export type KikColor = keyof typeof colors;

export type SDR = 'sending' | 'sent' | 'delivered' | 'read' | 'failed';

export const sdrLetter = (s: SDR) => ({ sending: '…', sent: 'S', delivered: 'D', read: 'R', failed: '!' }[s]);
export const sdrColor = (s: SDR, dark: boolean) => {
  switch (s) {
    case 'sending':
    case 'sent':      return dark ? colors.textTertiaryDark : colors.textTertiary;
    case 'delivered': return dark ? colors.textSecondaryDark : colors.textSecondary;
    case 'read':      return colors.kikBlue;   // the one color moment
    case 'failed':    return colors.error;
  }
};
```

## 2. Typography

Kik's brand face is proprietary; bundle **Inter** (SIL OFL) via `expo-font` as the closest free substitute.

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
  largeTitle:  { fontFamily: 'Inter-ExtraBold', fontSize: 32, letterSpacing: -0.4 },
  screenTitle: { fontFamily: 'Inter-ExtraBold', fontSize: 26, letterSpacing: -0.3 },
  title3:      { fontFamily: 'Inter-Bold',      fontSize: 22, letterSpacing: -0.2 },
  headline:    { fontFamily: 'Inter-Bold',      fontSize: 17, letterSpacing: -0.2 },
  rowTitle:    { fontFamily: 'Inter-Bold',      fontSize: 15, letterSpacing: -0.1 },
  body:        { fontFamily: 'Inter-Regular',   fontSize: 14, lineHeight: 19 },
  bodyOut:     { fontFamily: 'Inter-Medium',    fontSize: 14, lineHeight: 19 }, // outgoing — crisper on Blue
  bodyEmph:    { fontFamily: 'Inter-SemiBold',  fontSize: 14, lineHeight: 19 },
  preview:     { fontFamily: 'Inter-Medium',    fontSize: 13 },
  footnote:    { fontFamily: 'Inter-SemiBold',  fontSize: 13 },
  caption:     { fontFamily: 'Inter-SemiBold',  fontSize: 11, letterSpacing: 0.1 },
  button:      { fontFamily: 'Inter-Bold',      fontSize: 16 },
  receipt:     { fontFamily: 'Inter-Bold',      fontSize: 11, letterSpacing: 0.2 },
  tab:         { fontFamily: 'Inter-SemiBold',  fontSize: 11, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```tsx
// components/KikBubble.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { SDRReceipt } from './SDRReceipt';
import type { SDR } from '../theme/colors';

export function KikBubble({
  text, outgoing, tailEnd, timestamp, receipt,
}: { text: string; outgoing: boolean; tailEnd: boolean; timestamp: string; receipt: SDR }) {
  const dark = useColorScheme() === 'dark';
  const radius = {
    borderTopLeftRadius: 16, borderTopRightRadius: 16,
    borderBottomLeftRadius: !outgoing && tailEnd ? 5 : 16,
    borderBottomRightRadius: outgoing && tailEnd ? 5 : 16,
  };
  return (
    <View style={{ width: '100%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
      <View style={{ maxWidth: '80%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
        <View style={[{ backgroundColor: outgoing ? colors.kikBlue : (dark ? colors.darkSurface2 : colors.incoming), paddingVertical: 8, paddingHorizontal: 13 }, radius]}>
          <Text style={[outgoing ? typography.bodyOut : typography.body, { color: outgoing ? colors.inkOnBlue : (dark ? colors.textPrimaryDark : colors.textPrimary) }]}>{text}</Text>
        </View>
        {outgoing && (
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, marginTop: 3, marginRight: 4 }}>
            <Text allowFontScaling={false} style={{ fontSize: 10, color: dark ? colors.textTertiaryDark : colors.textTertiary }}>{timestamp}</Text>
            <SDRReceipt state={receipt} />
          </View>
        )}
      </View>
    </View>
  );
}
```

### S / D / R Receipt

```tsx
// components/SDRReceipt.tsx
import { Text, useColorScheme } from 'react-native';
import { typography } from '../theme/typography';
import { sdrLetter, sdrColor, type SDR } from '../theme/colors';

export function SDRReceipt({ state }: { state: SDR }) {
  const dark = useColorScheme() === 'dark';
  // No animation — swap the letter instantly; only the R recolor to Blue reads as an event.
  return (
    <Text allowFontScaling={false} style={[typography.receipt, { color: sdrColor(state, dark) }]}>
      {sdrLetter(state)}
    </Text>
  );
}
```

### Conversation Row (person vs bot)

```tsx
// components/KikConvRow.tsx
import { Text, View, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { SDRReceipt } from './SDRReceipt';
import type { SDR } from '../theme/colors';

export function KikConvRow({
  name, preview, time, isBot, unread, receipt, gradient,
}: {
  name: string; preview: string; time: string; isBot: boolean;
  unread?: number; receipt?: SDR; gradient: [string, string];
}) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, paddingVertical: 12, borderBottomWidth: 0.5, borderBottomColor: dark ? colors.darkDivider : colors.divider }}>
      <LinearGradient
        colors={gradient}
        style={{ width: 48, height: 48, borderRadius: isBot ? 14 : 24, alignItems: 'center', justifyContent: 'center' }}
      >
        <Text style={{ color: '#FFF', fontSize: 16, fontWeight: '700' }}>{name.slice(0, 1)}</Text>
      </LinearGradient>
      <View style={{ flex: 1 }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <Text numberOfLines={1} style={[typography.rowTitle, { color: dark ? colors.textPrimaryDark : colors.textPrimary }]}>{name}</Text>
          {isBot && (
            <Text style={{ fontSize: 9, fontWeight: '700', color: dark ? colors.textSecondaryDark : colors.textSecondary, backgroundColor: dark ? colors.darkSurface2 : colors.surfaceGray, paddingHorizontal: 5, paddingVertical: 1, borderRadius: 4 }}>BOT</Text>
          )}
        </View>
        <Text numberOfLines={1} style={[typography.preview, { color: dark ? colors.textSecondaryDark : colors.textSecondary }]}>{preview}</Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 5 }}>
        <Text allowFontScaling={false} style={{ fontSize: 11, color: dark ? colors.textTertiaryDark : colors.textTertiary }}>{time}</Text>
        {unread != null ? (
          <Text style={{ fontSize: 11, fontWeight: '700', color: colors.inkOnBlue, backgroundColor: colors.kikBlue, minWidth: 18, height: 18, borderRadius: 9, textAlign: 'center', overflow: 'hidden', paddingHorizontal: 5, lineHeight: 18 }}>{unread}</Text>
        ) : receipt ? (
          <SDRReceipt state={receipt} />
        ) : null}
      </View>
    </View>
  );
}
```

### Kik Code

```tsx
// components/KikCode.tsx
import { View } from 'react-native';
import { colors } from '../theme/colors';

export function KikCode() {
  return (
    // Flat, high-contrast — NO shadow (must scan reliably)
    <View style={{ width: 120, height: 120, borderRadius: 24, backgroundColor: colors.kikBlue, alignItems: 'center', justifyContent: 'center' }}>
      <View style={{ width: 96, height: 96, borderRadius: 48, borderWidth: 6, borderColor: '#FFF', borderStyle: 'dotted', alignItems: 'center', justifyContent: 'center' }}>
        <View style={{ width: 44, height: 44, borderRadius: 12, backgroundColor: '#FFF' }} />
      </View>
    </View>
  );
}
```

### Compose Bar (text "Send" label)

```tsx
// components/KikComposeBar.tsx
import { useState } from 'react';
import { Pressable, Text, TextInput, View, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function KikComposeBar({ onSend }: { onSend: (t: string) => void }) {
  const [text, setText] = useState('');
  const dark = useColorScheme() === 'dark';
  const empty = text.trim().length === 0;
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14, paddingVertical: 8, backgroundColor: dark ? colors.darkSurface1 : colors.canvas, borderTopWidth: 0.5, borderTopColor: dark ? colors.darkDivider : colors.divider }}>
      <Pressable><Ionicons name="add" size={22} color={dark ? colors.textSecondaryDark : colors.textSecondary} /></Pressable>
      <View style={{ flex: 1, minHeight: 36, borderRadius: 18, backgroundColor: dark ? colors.darkSurface2 : colors.surfaceGray, justifyContent: 'center', paddingHorizontal: 14 }}>
        <TextInput value={text} onChangeText={setText} placeholder="Type a message"
          placeholderTextColor={dark ? colors.textTertiaryDark : colors.textTertiary} multiline
          style={[typography.body, { color: dark ? colors.textPrimaryDark : colors.textPrimary, maxHeight: 110 }]} />
      </View>
      <Pressable disabled={empty} onPress={() => { onSend(text); setText(''); }}>
        <Text style={[typography.receipt, { fontSize: 14, color: empty ? (dark ? colors.textTertiaryDark : colors.textTertiary) : colors.kikBlue }]}>Send</Text>
      </Pressable>
    </View>
  );
}
```

### Chat Header (no call icons)

```tsx
// components/KikChatHeader.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function KikChatHeader({ username }: { username: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 14, paddingVertical: 8 }}>
      <Pressable><Ionicons name="chevron-back" size={22} color={colors.kikBlue} /></Pressable>
      <View style={{ flex: 1, alignItems: 'center' }}>
        <Text style={typography.headline}>{username}</Text>
      </View>
      <LinearGradient colors={[colors.kikCyan, colors.kikBlueDeep]} style={{ width: 32, height: 32, borderRadius: 16 }} />
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
        tabBarActiveTintColor: colors.kikBlue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={22} color={color} /> }} />
      <Tabs.Screen name="people"   options={{ title: 'People',   tabBarIcon: ({ color }) => <Ionicons name="people" size={22} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="sparkles" size={22} color={color} /> }} />
      <Tabs.Screen name="kikcode"  options={{ title: 'Kik Code', tabBarIcon: ({ color }) => <Ionicons name="qr-code" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { SlideInDown, FadeIn, FadeOut } from 'react-native-reanimated';

// Outgoing bubble — slide up + scale from input
// <Animated.View entering={SlideInDown.springify().damping(16)}>

// S/D/R receipt — NO animation; swap the letter instantly (the R-to-Blue recolor is the only "event")

// List row reorder on new message — LayoutAnimation / Reanimated layout transition, 250ms ease-out

// Bot suggested-reply chips — entering={FadeIn.duration(200)}

// Kik Code match — quick scale pulse on the plate, then router.push to the chat

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // send
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // bot button tap
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // Kik Code match / friend added
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Back | `chevron.left` | `chevron-back` |
| New chat | `plus` | `add` |
| Scan / camera | `qrcode.viewfinder` | `scan` |
| Attachment | `plus` | `add` |
| Search | `magnifyingglass` | `search` |
| Chats (tab) | `bubble.left.fill` | `chatbubble` |
| People (tab) | `person.2.fill` | `people` |
| Discover (tab) | `sparkles` | `sparkles` |
| Kik Code (tab) | `qrcode` | `qr-code` |
| GIF | `photo.stack` | `images` |
| Web content | `globe` | `globe` |
| Failed message | `exclamationmark.circle.fill` | `alert-circle` |
| Group | `person.3.fill` | `people-circle` |
| Block / report | `hand.raised.fill` | `hand-left` |
| Settings | `gearshape.fill` | `settings` |
| Share Kik Code | `square.and.arrow.up` | `share-outline` |

## 7. Platform Notes

- **Font**: Kik's brand face is proprietary — bundle Inter (SIL OFL) via `expo-font`; keep heavy 700/800 weights for titles and the username headline.
- **Outgoing ink**: always render text on the bright `#00B0F0` bubble in dark-cyan `#002A36` (WCAG AA), never white; bump outgoing body to weight 500 for crispness.
- **Status bar**: `<StatusBar style="auto" />` — dark content on white, light on the neutral dark canvas.
- **Safe area**: wrap screens in `SafeAreaView`; compose + tab bars need bottom safe-area padding; thread in `KeyboardAvoidingView`.
- **Dynamic Type**: leave `allowFontScaling` default on titles/body/previews; set `allowFontScaling={false}` on timestamps, tab labels, the S/D/R glyph, "BOT" tag, and keep the Kik Code plate fixed.
- **Dark mode**: use `useColorScheme()`; dark canvas is the neutral `#121316` (not color-cast); incoming `#25282D`; the R receipt stays `#00B0F0` in both modes; add a 1px `darkDivider` border to floating menus as the dark elevation cue.
- **Kik Code**: keep the plate flat and high-contrast (no shadow) so the camera scans it reliably; generate the real dotted-ring pattern from the user's Kik Code data, the snippet shows the visual frame.
- **Reduce Motion**: gate `SlideInDown` and the Kik Code pulse behind `AccessibilityInfo.isReduceMotionEnabled()`; the receipt has no animation to disable.
- **Accessibility**: spell receipts as values ("Sent"/"Delivered"/"Read") via `accessibilityLabel`, never the bare letter; bot rows announce "{name}, bot"; usernames read with leading "at"; the Kik Code announces "Kik Code, double-tap to share".
