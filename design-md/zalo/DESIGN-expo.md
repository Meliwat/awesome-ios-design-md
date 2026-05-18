# Zalo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zalo's visual language into paste-ready Expo / React Native code: a design-token module, the asymmetric chat bubble, the chat thread, the mini-app launcher grid, and the blue header with Reanimated + Haptics.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (single anchor)
  zaloBlue:        '#0068FF',
  zaloBluePressed: '#0052CC',
  zaloBlueDeep:    '#0047B3',
  bubbleOut:       '#DBEBFF',
  bubbleOutInk:    '#14223A',

  // Surfaces (light)
  canvas:    '#FFFFFF',
  chatBg:    '#E8ECF1',
  surface1:  '#F4F5F7',
  surface2:  '#EBEDF0',
  divider:   '#E4E6EB',

  // Surfaces (dark)
  darkCanvas:    '#15171A',
  darkChatBg:    '#101418',
  darkBubbleIn:  '#1F2329',
  darkBubbleOut: '#0A3A7A',
  darkDivider:   '#262A30',

  // Text
  ink:           '#1A1A1A',
  textSecondary: '#6B7280',
  textTertiary:  '#9AA0AA',
  textPrimaryDk: '#ECEDEF',

  // Semantic
  notify:  '#F5325B',
  success: '#18A957',
  warning: '#FF9500',

  // Mini-app tiles
  tileBlue:   '#0068FF',
  tileGreen:  '#18A957',
  tileOrange: '#FF9500',
  tileRed:    '#F5325B',
  tileViolet: '#7B5CFF',
  tileCyan:   '#00B8D4',
} as const;
```

## 2. Typography

Load **Be Vietnam Pro** via `expo-font` — designed for Vietnamese diacritics (SIL OFL).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'BeVietnamPro-ExtraBold': require('../assets/fonts/BeVietnamPro-ExtraBold.ttf'),
    'BeVietnamPro-Bold':      require('../assets/fonts/BeVietnamPro-Bold.ttf'),
    'BeVietnamPro-SemiBold':  require('../assets/fonts/BeVietnamPro-SemiBold.ttf'),
    'BeVietnamPro-Medium':    require('../assets/fonts/BeVietnamPro-Medium.ttf'),
    'BeVietnamPro-Regular':   require('../assets/fonts/BeVietnamPro-Regular.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  display:    { fontFamily: 'BeVietnamPro-ExtraBold', fontSize: 32, lineHeight: 40, letterSpacing: -0.5, color: '#1A1A1A' },
  screenTitle:{ fontFamily: 'BeVietnamPro-Bold',      fontSize: 24, lineHeight: 31, letterSpacing: -0.3, color: '#1A1A1A' },
  section:    { fontFamily: 'BeVietnamPro-Bold',      fontSize: 20, lineHeight: 26, letterSpacing: -0.2, color: '#1A1A1A' },
  listTitle:  { fontFamily: 'BeVietnamPro-SemiBold',  fontSize: 17, lineHeight: 23, color: '#1A1A1A' },
  body:       { fontFamily: 'BeVietnamPro-Regular',   fontSize: 15, lineHeight: 22, color: '#1A1A1A' }, // body + bubble
  cellTitle:  { fontFamily: 'BeVietnamPro-SemiBold',  fontSize: 14, lineHeight: 19, color: '#1A1A1A' },
  preview:    { fontFamily: 'BeVietnamPro-Regular',   fontSize: 13, lineHeight: 18, color: '#6B7280' },
  button:     { fontFamily: 'BeVietnamPro-Bold',      fontSize: 15, lineHeight: 15, color: '#FFFFFF' },
  timestamp:  { fontFamily: 'BeVietnamPro-SemiBold',  fontSize: 11, lineHeight: 14, color: '#9AA0AA' },
  tab:        { fontFamily: 'BeVietnamPro-Medium',    fontSize: 10, lineHeight: 10, color: '#9AA0AA' },
  badge:      { fontFamily: 'BeVietnamPro-Bold',      fontSize: 9,  lineHeight: 9,  color: '#FFFFFF' },
  miniLabel:  { fontFamily: 'BeVietnamPro-Medium',    fontSize: 11, lineHeight: 14, color: '#1A1A1A' },
} satisfies Record<string, TextStyle>;
```

> Vietnamese diacritics need vertical room — line heights above are deliberately generous; never wrap bubble/body text in a fixed-height clipped container.

## 3. Signature Components

### Asymmetric Chat Bubble

The signature: 16pt corners with one tail corner clipped to 5pt (bottom-left incoming, bottom-right outgoing). React Native `borderRadius` is per-corner, so this is simple.

```tsx
// components/ChatBubble.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { LinearGradient } from 'expo-linear-gradient';

type Msg = { id: string; text: string; outgoing: boolean; time: string; showAvatar: boolean };

export function ChatBubble({ msg }: { msg: Msg }) {
  const bubbleStyle = {
    backgroundColor: msg.outgoing ? colors.bubbleOut : colors.canvas,
    borderTopLeftRadius: 16,
    borderTopRightRadius: 16,
    borderBottomLeftRadius: msg.outgoing ? 16 : 5,
    borderBottomRightRadius: msg.outgoing ? 5 : 16,
    paddingVertical: 9,
    paddingHorizontal: 13,
    maxWidth: '78%' as const,
    ...(msg.outgoing
      ? {}
      : { shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 2, shadowOffset: { width: 0, height: 1 }, elevation: 1 }),
  };

  return (
    <View
      style={{
        flexDirection: msg.outgoing ? 'row-reverse' : 'row',
        alignItems: 'flex-end',
        gap: 8,
        paddingHorizontal: 12,
        justifyContent: msg.outgoing ? 'flex-end' : 'flex-start',
      }}
    >
      {!msg.outgoing &&
        (msg.showAvatar ? (
          <LinearGradient colors={[colors.zaloBlue, colors.zaloBlueDeep]} style={{ width: 28, height: 28, borderRadius: 14 }} />
        ) : (
          <View style={{ width: 28 }} />
        ))}
      <View style={{ alignItems: msg.outgoing ? 'flex-end' : 'flex-start' }}>
        <View style={bubbleStyle}>
          <Text style={[typography.body, { color: msg.outgoing ? colors.bubbleOutInk : colors.ink }]}>{msg.text}</Text>
        </View>
        <Text style={{ fontFamily: 'BeVietnamPro-Regular', fontSize: 10, color: colors.textTertiary, marginTop: 3 }}>{msg.time}</Text>
      </View>
    </View>
  );
}
```

### Chat Thread

```tsx
// components/ChatThread.tsx
import { ScrollView, View, Text } from 'react-native';
import { ChatBubble } from './ChatBubble';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatThread({ messages }: { messages: any[] }) {
  return (
    <ScrollView style={{ flex: 1, backgroundColor: colors.chatBg }} contentContainerStyle={{ paddingVertical: 14, gap: 6 }}>
      <View style={{ alignSelf: 'center', backgroundColor: 'rgba(255,255,255,0.7)', borderRadius: 999, paddingVertical: 3, paddingHorizontal: 12, marginBottom: 4 }}>
        <Text style={[typography.timestamp, { color: colors.textSecondary }]}>Hôm nay · Today</Text>
      </View>
      {messages.map((m) => <ChatBubble key={m.id} msg={m} />)}
      <Text style={[typography.timestamp, { alignSelf: 'flex-end', marginRight: 16, marginTop: 2, color: colors.textTertiary }]}>
        Đã xem · Seen 09:27
      </Text>
    </ScrollView>
  );
}
```

### Chat Header (Zalo Blue bar)

```tsx
// components/ChatHeader.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function ChatHeader({ name, presence }: { name: string; presence: string }) {
  return (
    <View style={{ backgroundColor: colors.zaloBlue, flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, paddingTop: 6, paddingBottom: 14 }}>
      <Ionicons name="chevron-back" size={22} color="#FFFFFF" />
      <LinearGradient colors={[colors.zaloBlue === '#0068FF' ? '#4D9FFF' : colors.zaloBlue, colors.zaloBlueDeep]} style={{ width: 38, height: 38, borderRadius: 19 }} />
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'BeVietnamPro-SemiBold', fontSize: 15, color: '#FFFFFF' }}>{name}</Text>
        <Text style={{ fontFamily: 'BeVietnamPro-Regular', fontSize: 11, color: 'rgba(255,255,255,0.85)' }}>{presence}</Text>
      </View>
      <Ionicons name="call" size={20} color="#FFFFFF" />
      <Ionicons name="videocam" size={20} color="#FFFFFF" />
    </View>
  );
}
```

### Message Composer

```tsx
// components/Composer.tsx
import { useState } from 'react';
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function Composer({ onSend }: { onSend?: (t: string) => void }) {
  const [text, setText] = useState('');
  const send = () => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onSend?.(text); setText(''); };
  return (
    <View style={{ backgroundColor: colors.canvas, flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 14, paddingTop: 9, paddingBottom: 14, borderTopWidth: 0.5, borderTopColor: colors.divider }}>
      <Ionicons name="happy-outline" size={23} color={colors.zaloBlue} />
      <TextInput
        value={text}
        onChangeText={setText}
        placeholder="Tin nhắn"
        placeholderTextColor={colors.textTertiary}
        style={{ flex: 1, height: 38, backgroundColor: colors.surface1, borderRadius: 19, paddingHorizontal: 16, fontFamily: 'BeVietnamPro-Regular', fontSize: 13, color: colors.ink }}
      />
      {text.length === 0 ? (
        <Ionicons name="camera" size={22} color={colors.zaloBlue} />
      ) : (
        <Pressable onPress={send} style={{ width: 38, height: 38, borderRadius: 19, backgroundColor: colors.zaloBlue, alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="send" size={16} color="#FFFFFF" />
        </Pressable>
      )}
    </View>
  );
}
```

### Mini-App Launcher Grid

```tsx
// components/LauncherGrid.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Mini = { icon: any; tile: string; label: string };

export function LauncherGrid({ apps }: { apps: Mini[] }) {
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', padding: 16, backgroundColor: colors.canvas }}>
      {apps.map((a, i) => (
        <View key={i} style={{ width: '25%', alignItems: 'center', marginBottom: 18, gap: 7 }}>
          <View style={{ width: 52, height: 52, borderRadius: 15, backgroundColor: a.tile, alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name={a.icon} size={24} color="#FFFFFF" />
          </View>
          <Text numberOfLines={2} style={[typography.miniLabel, { textAlign: 'center' }]}>{a.label}</Text>
        </View>
      ))}
    </View>
  );
}
```

### Chat List Row

```tsx
// components/ChatListRow.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatListRow({ name, preview, time, unread, online }: {
  name: string; preview: string; time: string; unread: number; online: boolean;
}) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16, height: 72, borderBottomWidth: 0.5, borderBottomColor: colors.divider }}>
      <View>
        <LinearGradient colors={['#4D9FFF', colors.zaloBlueDeep]} style={{ width: 48, height: 48, borderRadius: 24 }} />
        {online && (
          <View style={{ position: 'absolute', right: 0, bottom: 0, width: 10, height: 10, borderRadius: 5, backgroundColor: colors.success, borderWidth: 2, borderColor: '#FFFFFF' }} />
        )}
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.listTitle, { fontFamily: unread > 0 ? 'BeVietnamPro-Bold' : 'BeVietnamPro-SemiBold' }]}>{name}</Text>
        <Text numberOfLines={1} style={[typography.preview, { color: unread > 0 ? colors.ink : colors.textSecondary }]}>{preview}</Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 6 }}>
        <Text style={typography.timestamp}>{time}</Text>
        {unread > 0 && (
          <View style={{ minWidth: 16, height: 16, borderRadius: 8, backgroundColor: colors.notify, alignItems: 'center', justifyContent: 'center', paddingHorizontal: 5 }}>
            <Text style={typography.badge}>{unread}</Text>
          </View>
        )}
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
        tabBarActiveTintColor: colors.zaloBlue,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'BeVietnamPro-Medium', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Tin nhắn', tabBarBadge: 5, tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={23} color={color} /> }} />
      <Tabs.Screen name="contacts" options={{ title: 'Danh bạ',  tabBarIcon: ({ color }) => <Ionicons name="people" size={23} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ title: 'Khám phá', tabBarIcon: ({ color }) => <Ionicons name="grid" size={23} color={color} /> }} />
      <Tabs.Screen name="timeline" options={{ title: 'Nhật ký',  tabBarIcon: ({ color }) => <Ionicons name="time" size={23} color={color} /> }} />
      <Tabs.Screen name="me"       options={{ title: 'Cá nhân',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Outgoing bubble send
import Animated, { FadeIn } from 'react-native-reanimated';
// wrap outgoing bubble: <Animated.View entering={FadeIn.duration(200)}>
// or scale 0.9 -> 1.0 via useSharedValue + withTiming(1, { duration: 200 })

// Incoming bubble + typing indicator
// <Animated.View entering={SlideInDown.duration(200)}>
// typing: 3 dots each with a looped withRepeat(withSequence(...)) bounce

// Tab change — handled by Tabs tint; icon swap is instant

// Launcher tile press
// Pressable style fn: transform: [{ scale: pressed ? 0.94 : 1 }]

// Reaction picker (long-press bubble)
// <Animated.View entering={ZoomIn.duration(180)}>

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // send
Haptics.selectionAsync();                                // tab change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); // Zalo Pay confirm / friend add
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Tin nhắn (tab) | `chatbubble` / `chatbubble-outline` |
| Danh bạ (tab) | `people` / `people-outline` |
| Khám phá (tab) | `grid` / `grid-outline` |
| Nhật ký (tab) | `time` / `time-outline` |
| Cá nhân (tab) | `person-circle` / `person-circle-outline` |
| Back | `chevron-back` |
| Voice call | `call` |
| Video call | `videocam` |
| Send | `send` |
| Sticker / emoji | `happy-outline` |
| Camera | `camera` |
| Attachment | `add` / `attach` |
| Search | `search` |
| Like (timeline) | `heart` (`#F5325B`) |
| Verified (OA) | `checkmark-circle` (`#0068FF`) |
| Settings | `settings` |

## 7. Platform Notes

- **Fonts**: Be Vietnam Pro is SIL OFL — free to bundle via `expo-font`; it is the correct face for Vietnamese diacritics (do not substitute a generic Latin font)
- **Status bar**: `<StatusBar style="light" />` because the Zalo Blue header sits behind it; keep light content even in dark mode (header stays blue)
- **Safe area**: the blue header should extend under the status bar — wrap it so its background fills the top inset; the composer and tab bar respect the bottom inset; the chat thread scrolls clear of the keyboard via `KeyboardAvoidingView`
- **Dynamic Type**: set `allowFontScaling={false}` on timestamps, badges, tab labels, mini-app captions; allow scaling on titles/body/bubbles; let chat rows grow vertically rather than clip Vietnamese tone marks
- **Bubble corners**: React Native supports per-corner `borderRadius`, so the asymmetric tail (5pt on one bottom corner, 16pt elsewhere) is a direct style — no custom path needed
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkChatBg` / `darkBubbleIn` / `darkBubbleOut`; the header and active tab stay `zaloBlue`; drop the incoming-bubble shadow on dark
- **Accessibility**: label bubbles "{incoming/outgoing} message: {text}"; chat rows "{name}, {preview}, {unread} unread"; send as "Send message"; launcher tiles "{label}, opens mini-app"; group day separators as headers
- **Reduce Motion**: gate the bubble entrance and reaction-picker zoom behind `AccessibilityInfo.isReduceMotionEnabled()`; keep a static "typing…" label instead of the animated dots
- **Lists at scale**: use `FlatList` (chat list) and inverted `FlatList` (thread) for long histories; key by message id and memoize bubble rows
