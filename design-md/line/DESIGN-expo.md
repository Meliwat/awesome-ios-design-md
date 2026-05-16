# LINE (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates LINE's visual language into paste-ready Expo / React Native code: a design-token module, the periwinkle backdrop, text bubbles, and the signature oversized bubble-less sticker.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  backdrop:   '#8CABD9',  // periwinkle chat background (themeable) — NOT white
  backdropDk: '#1A2230',
  canvas:     '#FFFFFF',
  surface:    '#F7F7F7',
  divider:    '#E5E5E5',

  textPrimary:   '#000000',
  textSecondary: '#8C8C8C',
  textTertiary:  '#B3B3B3',

  green:        '#06C755',
  greenPressed: '#05A647',
  greenWash:    '#F1FBF3',

  incoming: '#FFFFFF',
  outgoing: '#C5F0B1',   // pale chat-green — NOT the brand green

  notif: '#FF334B',
  link:  '#1F8FFF',
} as const;

export type LineColor = keyof typeof colors;
```

## 2. Typography

LINE uses **Noto Sans** at weights 400/700 (no semibold tier). Load via `expo-font`; the system font covers CJK fallback.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'NotoSans-Regular': require('../assets/fonts/NotoSans-Regular.ttf'),
    'NotoSans-Bold':    require('../assets/fonts/NotoSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  largeTitle:  { fontFamily: 'NotoSans-Bold',    fontSize: 24, lineHeight: 29 },
  friendName:  { fontFamily: 'NotoSans-Regular', fontSize: 16, lineHeight: 21 },
  threadTitle: { fontFamily: 'NotoSans-Bold',    fontSize: 17, lineHeight: 21 },
  messageBody: { fontFamily: 'NotoSans-Regular', fontSize: 16, lineHeight: 23 }, // CJK-friendly 1.45
  status:      { fontFamily: 'NotoSans-Regular', fontSize: 13, lineHeight: 17 },
  preview:     { fontFamily: 'NotoSans-Regular', fontSize: 14, lineHeight: 18 },
  section:     { fontFamily: 'NotoSans-Bold',    fontSize: 13, lineHeight: 16, letterSpacing: 0.2 },
  timestamp:   { fontFamily: 'NotoSans-Regular', fontSize: 12, lineHeight: 14 },
  readLabel:   { fontFamily: 'NotoSans-Regular', fontSize: 11, lineHeight: 13 },
  button:      { fontFamily: 'NotoSans-Bold',    fontSize: 16, lineHeight: 16 },
  buttonText:  { fontFamily: 'NotoSans-Bold',    fontSize: 15, lineHeight: 15 },
  tab:         { fontFamily: 'NotoSans-Regular', fontSize: 10, lineHeight: 12 },
  badge:       { fontFamily: 'NotoSans-Bold',    fontSize: 11, lineHeight: 11 },
  stickerPx:   { fontFamily: 'NotoSans-Bold',    fontSize: 13, lineHeight: 13 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Oversized Bubble-less Sticker (Signature)

```tsx
// components/StickerMessage.tsx
import { useEffect } from 'react';
import { Image, Text, View, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StickerMessage({
  uri, time, isOutgoing, read,
}: { uri: string; time: string; isOutgoing: boolean; read: boolean }) {
  const scale = useSharedValue(0.6);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  useEffect(() => {
    // bounce-in: 0.6 → 1.08 → 1.0
    scale.value = withSequence(
      withSpring(1.08, { damping: 8, stiffness: 220 }),
      withSpring(1, { damping: 12 }),
    );
  }, []);

  const meta = (
    <View style={{ alignItems: isOutgoing ? 'flex-end' : 'flex-start' }}>
      {isOutgoing && read && (
        <Text style={[typography.readLabel, { color: colors.textSecondary }]}>Read</Text>
      )}
      <Text style={[typography.timestamp, { color: colors.textSecondary }]}>{time}</Text>
    </View>
  );

  return (
    <View style={[styles.row, { justifyContent: isOutgoing ? 'flex-end' : 'flex-start' }]}>
      {isOutgoing && meta}
      {/* bubble-less, oversized — sits directly on the backdrop */}
      <Animated.Image source={{ uri }} style={[styles.sticker, style]} resizeMode="contain" />
      {!isOutgoing && meta}
    </View>
  );
}

const styles = StyleSheet.create({
  row:     { flexDirection: 'row', alignItems: 'flex-end', gap: 6,
             paddingHorizontal: 12, paddingVertical: 10 }, // stickers need air
  sticker: { width: 140, height: 140 },
});
```

### Text Bubble (on the periwinkle backdrop)

```tsx
// components/TextBubble.tsx
import { Text, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TextBubble({
  text, time, isOutgoing, read,
}: { text: string; time: string; isOutgoing: boolean; read: boolean }) {
  const meta = (
    <View style={{ alignItems: isOutgoing ? 'flex-end' : 'flex-start' }}>
      {isOutgoing && read && (
        <Text style={[typography.readLabel, { color: colors.textSecondary }]}>Read</Text>
      )}
      <Text style={[typography.timestamp, { color: colors.textSecondary }]}>{time}</Text>
    </View>
  );
  return (
    <View style={[styles.row, { justifyContent: isOutgoing ? 'flex-end' : 'flex-start' }]}>
      {isOutgoing && meta}
      <View
        style={[
          styles.bubble,
          {
            backgroundColor: isOutgoing ? colors.outgoing : colors.incoming,
            // 4pt tail notch on the avatar-side top corner:
            borderTopLeftRadius:  isOutgoing ? 16 : 4,
            borderTopRightRadius: isOutgoing ? 4 : 16,
          },
        ]}
      >
        <Text style={[typography.messageBody, { color: colors.textPrimary }]}>{text}</Text>
      </View>
      {!isOutgoing && meta}
    </View>
  );
}

const styles = StyleSheet.create({
  row:    { flexDirection: 'row', alignItems: 'flex-end', gap: 6,
            paddingHorizontal: 8, paddingVertical: 2 },
  bubble: { maxWidth: '70%', paddingVertical: 9, paddingHorizontal: 12, borderRadius: 16,
            // faint lift so white/green read on periwinkle
            shadowColor: '#000', shadowOpacity: 0.06, shadowRadius: 1, shadowOffset: { width: 0, height: 1 },
            elevation: 1 },
});
```

### Friend List Row (official-account badge)

```tsx
// components/FriendRow.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FriendRow({
  name, status, time, official,
}: { name: string; status: string; time: string; official: boolean }) {
  return (
    <Pressable style={({ pressed }) => [styles.row, pressed && { backgroundColor: colors.surface }]}>
      <View style={styles.avatar} />
      <View style={{ flex: 1, gap: 3 }}>
        <View style={styles.nameRow}>
          <Text style={[typography.friendName, { color: colors.textPrimary }]}>{name}</Text>
          {official && (
            // LINE's verified/business trust primitive
            <Ionicons name="shield-checkmark" size={14} color={colors.green} />
          )}
        </View>
        <Text style={[typography.status, { color: colors.textSecondary }]} numberOfLines={1}>
          {status}
        </Text>
      </View>
      <Text style={[typography.timestamp, { color: colors.textSecondary }]}>{time}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:     { height: 68, flexDirection: 'row', alignItems: 'center', gap: 12,
             paddingHorizontal: 16, backgroundColor: colors.canvas },
  avatar:  { width: 52, height: 52, borderRadius: 26, backgroundColor: colors.surface },
  nameRow: { flexDirection: 'row', alignItems: 'center', gap: 5 },
});
```

### Sticker-Shop Tile

```tsx
// components/StickerShopTile.tsx
import { Image, Text, View, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StickerShopTile({
  previewUri, title, author, price,
}: { previewUri: string; title: string; author: string; price: string }) {
  return (
    <View style={styles.tile}>
      <Image source={{ uri: previewUri }} style={styles.preview} resizeMode="contain" />
      <Text style={[typography.section, { color: colors.textPrimary }]}>{title}</Text>
      <Text style={[typography.timestamp, { color: colors.textSecondary }]}>{author}</Text>
      <View style={styles.priceRow}>
        <View style={styles.pricePill}>
          <Text style={[typography.stickerPx, { color: colors.green }]}>
            {price === 'Free' ? 'Free' : `🪙 ${price}`}
          </Text>
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  tile:      { backgroundColor: colors.surface, borderRadius: 12, padding: 12, gap: 8 },
  preview:   { height: 96, backgroundColor: '#FFF', borderRadius: 8 },
  priceRow:  { flexDirection: 'row', justifyContent: 'flex-end' },
  pricePill: { paddingVertical: 6, paddingHorizontal: 14, borderRadius: 999,
               borderWidth: 1, borderColor: colors.green },
});
```

### Composer (sticker keyboard is primary)

```tsx
// components/ComposerBar.tsx
import { useState } from 'react';
import { Pressable, TextInput, View, StyleSheet } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ComposerBar() {
  const [text, setText] = useState('');
  const hasText = text.trim().length > 0;
  return (
    <View style={styles.bar}>
      <Ionicons name="add" size={24} color="#FFF" />
      <View style={styles.pill}>
        <TextInput
          style={[typography.messageBody, styles.input]}
          placeholder="Aa"
          placeholderTextColor={colors.textTertiary}
          value={text}
          onChangeText={setText}
          multiline
        />
        {/* sticker keyboard — a primary input mode in LINE */}
        <Ionicons name="happy-outline" size={22} color={colors.textSecondary} />
        <Ionicons name="camera-outline" size={20} color={colors.textSecondary} />
      </View>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); setText(''); }}
      >
        {hasText ? (
          <View style={styles.send}><Ionicons name="arrow-up" size={16} color="#FFF" /></View>
        ) : (
          <Ionicons name="mic" size={22} color="#FFF" />
        )}
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  bar:   { flexDirection: 'row', alignItems: 'flex-end', gap: 10,
           paddingHorizontal: 8, paddingVertical: 8, backgroundColor: colors.backdrop },
  pill:  { flex: 1, flexDirection: 'row', alignItems: 'center', gap: 8, minHeight: 36,
           paddingHorizontal: 12, borderRadius: 18, backgroundColor: '#FFF' },
  input: { flex: 1, color: colors.textPrimary, maxHeight: 110 },
  send:  { width: 30, height: 30, borderRadius: 15, backgroundColor: colors.green,
           alignItems: 'center', justifyContent: 'center' },
});
```

## 4. Chat Backdrop

```tsx
// components/ChatScreen.tsx — periwinkle, themeable, NEVER white
import { ScrollView } from 'react-native';
import { colors } from '../theme/colors';

export function ChatScreen({ children }: { children: React.ReactNode }) {
  return (
    <ScrollView style={{ backgroundColor: colors.backdrop }}
                contentContainerStyle={{ paddingVertical: 12 }}>
      {children}
    </ScrollView>
  );
}
```

## 5. Tab Bar (Super-App)

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.green,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 1 },
        tabBarLabelStyle: { fontFamily: 'NotoSans-Regular', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home" size={24} color={color} /> }} />
      <Tabs.Screen name="talk"   options={{ title: 'Talk',   tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={24} color={color} /> }} />
      <Tabs.Screen name="voom"   options={{ title: 'VOOM',   tabBarIcon: ({ color }) => <Ionicons name="play-circle" size={24} color={color} /> }} />
      <Tabs.Screen name="news"   options={{ title: 'News',   tabBarIcon: ({ color }) => <Ionicons name="newspaper" size={24} color={color} /> }} />
      <Tabs.Screen name="wallet" options={{ title: 'Wallet', tabBarIcon: ({ color }) => <Ionicons name="wallet" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Sticker bounce-in (signature) — see StickerMessage
scale.value = withSequence(withSpring(1.08, { damping: 8 }), withSpring(1, { damping: 12 }));

// Animated sticker replay on tap — re-run the sequence
const replay = () => { scale.value = 0.6; scale.value = withSequence(withSpring(1.08), withSpring(1)); };

// Text bubble enter — fade + 6pt rise
import Animated, { FadeInUp } from 'react-native-reanimated';
<Animated.View entering={FadeInUp.duration(220)} />

// Sticker keyboard present — slide up
import { SlideInDown } from 'react-native-reanimated';
<Animated.View entering={SlideInDown.duration(280)} />
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to LINE's glyphs:

| Purpose | Ionicons |
|---------|----------|
| Send (text present) | `arrow-up` |
| Voice (empty composer) | `mic` |
| Sticker keyboard | `happy-outline` |
| Plus / more | `add` |
| Camera | `camera-outline` |
| Official-account badge | `shield-checkmark` |
| Audio call | `call` |
| Video call | `videocam` |
| Menu (chat) | `menu` |
| Search | `search` |
| Home | `home` |
| Talk | `chatbubble` |
| VOOM | `play-circle` |
| News | `newspaper` |
| Wallet | `wallet` |

## 8. Platform Notes

- **Bubble-less stickers**: render the sticker as a plain `Image`/`Animated.Image` — never wrap it in a bubble or clamp it to the 70% bubble width; ~140pt on the long edge
- **Periwinkle backdrop**: the chat `ScrollView` background is `#8CABD9` by default (themeable) — never white; re-check bubble/meta contrast if you support custom backgrounds
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` for list/Home screens; consider `light` over a dark themed backdrop
- **CJK**: keep `lineHeight` generous (≈1.45 on body) so CJK glyphs aren't cramped; the system font covers CJK fallback when Noto Sans lacks a glyph
- **Safe area**: wrap screens in `SafeAreaView`; the composer + sticker keyboard rise with the keyboard via `KeyboardAvoidingView`
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on tab labels, badge counts, sticker prices; stickers are image assets and don't scale with type
- **Accessibility**: a sticker MUST have an `accessibilityLabel` describing it (no text fallback exists); announce the official-account badge as "Official account, verified"
- **Reduce Motion**: gate the bounce-in behind `AccessibilityInfo.isReduceMotionEnabled()` and fall back to a quick fade
