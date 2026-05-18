# KakaoTalk (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates KakaoTalk's visual language into paste-ready Expo / React Native code: a design-token module, the rounded-square avatar, the message bubble with its iconic side-docked unread mark, the friends roster, the gifticon card, the composer, and the bottom tab bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  ktYellow:        '#FEE500',
  ktYellowPressed: '#E6CF00',
  ktBrown:         '#3C1E1E',
  ktBrownSoft:     '#5A3A3A',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  chatBgLight:   '#B2C7DA',
  surfaceGray:   '#F5F5F5',
  inboundLight:  '#FFFFFF',
  dividerLight:  '#E6E6E6',

  // Surfaces (dark)
  darkCanvas:    '#1A1A1A',
  chatBgDark:    '#1E2A33',
  darkSurface1:  '#242424',
  inboundDark:   '#2E2E2E',
  darkDivider:   '#333333',

  // Text
  textPrimary:       '#191919',
  textSecondary:     '#6A6A6A',
  textTertiary:      '#9A9A9A',
  darkTextPrimary:   '#EDEDED',
  darkTextSecondary: '#9A9A9A',
  onYellow:          '#3C1E1E',

  // Semantic
  unread:    '#FEE500',
  link:      '#4B9BFF',
  success:   '#2ECC71',
  error:     '#F15E6C',
  badge:     '#FF3B30',
} as const;

export type KTColor = keyof typeof colors;

// Avatar generated gradients, assigned per friend id
export const avatarGradients: [string, string][] = [
  ['#FFB13C', '#FF8E53'], // warm
  ['#6DC0F5', '#4B9BFF'], // blue
  ['#A3D977', '#6BBF59'], // green
  ['#C792EA', '#9B6FD6'], // purple
  ['#FEE500', '#E6CF00'], // kakao yellow (self / channel)
];
export const gradientForId = (id: number) =>
  avatarGradients[Math.abs(id) % avatarGradients.length];
```

## 2. Typography

KakaoTalk uses the iOS system faces: SF Pro for Latin, Apple SD Gothic Neo for Hangul (iOS applies this automatically — do not set `fontFamily`). Big titles go weight `900` (KakaoBig feel). `<Text>` scales with Dynamic Type by default.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#191919' } satisfies TextStyle;

export const typography = {
  largeTitle:  { ...primary, fontSize: 32, fontWeight: '900', lineHeight: 37, letterSpacing: -0.4 },
  screen:      { ...primary, fontSize: 26, fontWeight: '700', lineHeight: 31, letterSpacing: -0.3 },
  navTitle:    { ...primary, fontSize: 20, fontWeight: '900', lineHeight: 24, letterSpacing: -0.3 },
  profileName: { ...primary, fontSize: 17, fontWeight: '700', lineHeight: 21 },
  section:     { ...primary, fontSize: 18, fontWeight: '700', lineHeight: 23 },
  body:        { ...primary, fontSize: 15, fontWeight: '400', lineHeight: 20 }, // Hangul-friendly LH
  rowTitle:    { ...primary, fontSize: 15, fontWeight: '600', lineHeight: 20 },
  status:      { color: '#6A6A6A', fontSize: 13, fontWeight: '400', lineHeight: 17 },
  sender:      { fontSize: 12, fontWeight: '500', lineHeight: 14 },
  unreadMark:  { color: '#FEE500', fontSize: 11, fontWeight: '700', lineHeight: 11 },
  bubbleTime:  { fontSize: 10, fontWeight: '400', lineHeight: 10 },
  tab:         { fontSize: 10, fontWeight: '500', lineHeight: 10 },
  tabBadge:    { color: '#FFFFFF', fontSize: 10, fontWeight: '700', lineHeight: 10 },
  button:      { color: '#3C1E1E', fontSize: 16, fontWeight: '700', lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Rounded-Square Avatar (used everywhere)

```tsx
// components/KTAvatar.tsx
import { Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';

export function KTAvatar({
  gradient, initials, size = 44,
}: { gradient: [string, string]; initials: string; size?: number }) {
  return (
    <LinearGradient
      colors={gradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{
        width: size, height: size, borderRadius: size * 0.36,
        alignItems: 'center', justifyContent: 'center',
      }}
    >
      <Text style={{ color: '#FFF', fontSize: size * 0.36, fontWeight: '700' }}>{initials}</Text>
    </LinearGradient>
  );
}
```

### Message Bubble + iconic side-docked unread mark

```tsx
// components/MessageBubble.tsx
import { View, Text, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MessageBubble({
  text, outbound, time = '', unread = 0,
}: { text: string; outbound: boolean; time?: string; unread?: number }) {
  const dark = useColorScheme() === 'dark';
  const bg = outbound ? colors.ktYellow : dark ? colors.inboundDark : colors.inboundLight;
  const fg = outbound ? colors.ktBrown : dark ? colors.darkTextPrimary : colors.textPrimary;
  const metaColor = dark ? 'rgba(255,255,255,0.5)' : colors.textSecondary;

  const Meta = (
    <View style={{ alignItems: outbound ? 'flex-end' : 'flex-start', gap: 1 }}>
      {outbound && unread > 0 && <Text style={typography.unreadMark}>{unread}</Text>}
      {!!time && <Text style={[typography.bubbleTime, { color: metaColor }]}>{time}</Text>}
    </View>
  );

  return (
    <View style={{
      flexDirection: outbound ? 'row-reverse' : 'row',
      alignItems: 'flex-end', gap: 5,
      maxWidth: 270, alignSelf: outbound ? 'flex-end' : 'flex-start',
    }}>
      <View style={{
        backgroundColor: bg, paddingVertical: 9, paddingHorizontal: 12,
        borderRadius: 14,
        borderTopLeftRadius: outbound ? 14 : 4,
        borderTopRightRadius: outbound ? 4 : 14,
      }}>
        <Text style={[typography.body, { color: fg, fontWeight: outbound ? '500' : '400' }]}>{text}</Text>
      </View>
      {Meta}
    </View>
  );
}
```

### Inbound Sender Row (rounded-square avatar + name above a run)

```tsx
// components/InboundRow.tsx
import { View, Text, useColorScheme } from 'react-native';
import { KTAvatar } from './KTAvatar';
import { MessageBubble } from './MessageBubble';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function InboundRow({
  senderName, gradient, initials, bubbles,
}: {
  senderName: string; gradient: [string, string]; initials: string;
  bubbles: { text: string; time: string }[];
}) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-start', gap: 8 }}>
      <KTAvatar gradient={gradient} initials={initials} size={36} />
      <View style={{ gap: 3, flexShrink: 1 }}>
        <Text style={[typography.sender, {
          color: dark ? 'rgba(255,255,255,0.78)' : colors.textSecondary, marginLeft: 2,
        }]}>{senderName}</Text>
        {bubbles.map((b, i) => (
          <MessageBubble key={i} text={b.text} outbound={false} time={b.time} />
        ))}
      </View>
    </View>
  );
}
```

### Friend Row (with optional now-playing badge)

```tsx
// components/FriendRow.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { KTAvatar } from './KTAvatar';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FriendRow({
  name, status, nowPlaying, gradient, initials,
}: {
  name: string; status: string; nowPlaying?: string;
  gradient: [string, string]; initials: string;
}) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingHorizontal: 18, height: 64,
    }}>
      <KTAvatar gradient={gradient} initials={initials} size={44} />
      <View style={{ flex: 1, gap: 2 }}>
        <Text style={typography.rowTitle}>{name}</Text>
        {nowPlaying ? (
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <Ionicons name="musical-notes" size={11} color={colors.textTertiary} />
            <Text style={[typography.status, { color: colors.textTertiary }]} numberOfLines={1}>{nowPlaying}</Text>
          </View>
        ) : (
          <Text style={typography.status} numberOfLines={1}>{status}</Text>
        )}
      </View>
    </View>
  );
}
```

### Gifticon Card

```tsx
// components/GifticonCard.tsx
import { View, Text, Pressable, useColorScheme } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function GifticonCard({
  brand, product, isRecipient, onUse,
}: { brand: string; product: string; isRecipient: boolean; onUse: () => void }) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{
      width: 188, borderRadius: 16, overflow: 'hidden',
      backgroundColor: dark ? colors.darkSurface1 : colors.canvas,
      shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 3, shadowOffset: { width: 0, height: 1 },
    }}>
      <LinearGradient colors={['#FFE08A', '#FEC84B']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ height: 120, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="gift" size={42} color={colors.ktBrown} />
      </LinearGradient>
      <View style={{ padding: 12, gap: 6 }}>
        <Text style={{ fontSize: 11, color: colors.textTertiary }}>{brand}</Text>
        <Text style={{ fontSize: 13, fontWeight: '700', color: dark ? colors.darkTextPrimary : colors.textPrimary }}>{product}</Text>
        {isRecipient && (
          <Pressable onPress={onUse} style={{
            backgroundColor: colors.ktYellow, borderRadius: 999,
            paddingVertical: 8, alignItems: 'center', marginTop: 4,
          }}>
            <Text style={{ fontSize: 13, fontWeight: '600', color: colors.ktBrown }}>사용하기</Text>
          </Pressable>
        )}
      </View>
    </View>
  );
}
```

### Composer

```tsx
// components/Composer.tsx
import { useState } from 'react';
import { View, TextInput, Pressable, useColorScheme } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Composer({
  onMenu, onEmoticon, onSend,
}: { onMenu: () => void; onEmoticon: () => void; onSend: (t: string) => void }) {
  const [text, setText] = useState('');
  const dark = useColorScheme() === 'dark';
  const send = () => {
    if (!text.trim()) return;
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    onSend(text); setText('');
  };
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10,
      paddingHorizontal: 14, paddingTop: 10, paddingBottom: 8,
      backgroundColor: dark ? colors.darkCanvas : colors.canvas,
      borderTopWidth: 0.5, borderTopColor: dark ? colors.darkDivider : colors.dividerLight,
    }}>
      <Pressable onPress={onMenu} hitSlop={8}>
        <Ionicons name="add" size={24} color={colors.textSecondary} />
      </Pressable>
      <View style={{
        flex: 1, flexDirection: 'row', alignItems: 'center',
        minHeight: 38, paddingHorizontal: 16, borderRadius: 999,
        backgroundColor: dark ? colors.inboundDark : colors.surfaceGray,
      }}>
        <TextInput
          value={text} onChangeText={setText} multiline
          style={[typography.body, { flex: 1, color: dark ? colors.darkTextPrimary : colors.textPrimary }]}
        />
        {!!text.trim() && (
          <Pressable onPress={send} hitSlop={6}>
            <Ionicons name="arrow-up-circle" size={24} color={colors.ktYellow} />
          </Pressable>
        )}
      </View>
      <Pressable onPress={onEmoticon} hitSlop={8}>
        <Ionicons name="happy-outline" size={24} color={colors.textSecondary} />
      </Pressable>
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
        tabBarActiveTintColor:  colors.textPrimary,  // primary text color, NOT yellow
        tabBarInactiveTintColor: '#777777',
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.dividerLight },
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500' },
        tabBarBadgeStyle: { backgroundColor: colors.badge, color: '#FFF', fontSize: 10, fontWeight: '700' },
      }}
    >
      <Tabs.Screen name="friends"  options={{ title: 'Friends',  tabBarIcon: ({ color }) => <Ionicons name="people" size={24} color={color} /> }} />
      <Tabs.Screen name="chats"    options={{ title: 'Chats', tabBarBadge: 5, tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={24} color={color} /> }} />
      <Tabs.Screen name="open"     options={{ title: 'Open',     tabBarIcon: ({ color }) => <Ionicons name="globe-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="shopping" options={{ title: 'Shopping', tabBarIcon: ({ color }) => <Ionicons name="bag" size={24} color={color} /> }} />
      <Tabs.Screen name="more"     options={{ title: 'More',     tabBarIcon: ({ color }) => <Ionicons name="menu" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Message send — pop up + fade from composer
import Animated, { FadeInDown } from 'react-native-reanimated';
// <Animated.View entering={FadeInDown.duration(200)}>

// Unread countdown — cross-fade the number (re-key the Text)
import { FadeIn } from 'react-native-reanimated';
// <Animated.Text key={unread} entering={FadeIn.duration(200)}>{unread}</Animated.Text>

// New inbound — fade + 6pt slide-up
// entering={FadeIn.duration(180)}

// Emoticon send — scale 0.7 → 1.0 spring
import { useSharedValue, withSpring } from 'react-native-reanimated';
// scale.value = withSpring(1, { damping: 12, stiffness: 220 });

// Action sheet / emoticon keyboard — @gorhom/bottom-sheet, ~280ms

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // on send
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // on emoticon send
Haptics.selectionAsync();                                 // on tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Friends (tab) | `people` |
| Chats (tab) | `chatbubble` |
| Open Chat (tab) | `globe-outline` |
| Shopping (tab) | `bag` |
| More (tab) | `menu` |
| Back | `chevron-back` |
| Search | `search` |
| Add friend | `person-add-outline` |
| Settings / menu | `ellipsis-horizontal` |
| Composer menu (+) | `add` |
| Emoticon keyboard | `happy-outline` |
| Send | `arrow-up-circle` |
| Now-playing badge | `musical-notes` |
| Gifticon | `gift` |
| Album / photo | `images` |
| Camera | `camera` |
| Pay / 송금 | `wallet` |
| Location | `location` |
| Schedule | `calendar` |
| Voice | `mic` |

## 7. Platform Notes

- **Font choice**: KakaoTalk ships no brand typeface — do not set `fontFamily`; iOS renders SF Pro for Latin and Apple SD Gothic Neo for Hangul automatically. Dynamic Type scales `<Text>` by default.
- **Korean line height**: keep `lineHeight` generous (`fontSize × ~1.33`) on body and status text so Hangul glyphs breathe; never tighten below ~1.3.
- **Status bar**: `dark` on the light friends list, `light` on dark mode; over the chat thread match the backdrop (blue-gray light → `dark` icons; `#1E2A33` dark → `light` icons).
- **Safe area**: wrap screens in `SafeAreaView`; nav bars extend into the top inset; composer + tab bar respect the home indicator.
- **Side-docked meta**: render the unread mark + timestamp as a sibling column to the bubble (not inside it) using `flexDirection: 'row-reverse'` for outbound so the meta sits on the inner side — this is the defining KakaoTalk layout.
- **Rounded-square avatars**: always `borderRadius: size * 0.36` — never `size / 2` (circles are wrong for KakaoTalk).
- **Bubble run grouping**: collapse consecutive same-sender messages within 60s — render avatar + name once at the top; show the meta only on the last bubble of the run.
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `chatBgDark` / `inboundDark`; outbound bubbles stay `ktYellow` with `ktBrown` text regardless of scheme; never pure black — `#1A1A1A` canvas + `#1E2A33` thread keep KakaoTalk warm.
- **Active tab**: use the primary text color (`colors.textPrimary`) — never a yellow tint on the tab bar.
- **Accessibility**: label the unread mark with its meaning ("{n} people have not read this"); keep `allowFontScaling={false}` on tab labels, unread marks, bubble times; expose the `+` menu and emoticon keyboard as labeled buttons.
- **Emoticons**: Kakao Friends emoticons are large bubble-less image stickers (~120pt) — render via `expo-image`, no bubble background; gifticons are rich cards with a barcode redeem screen.
