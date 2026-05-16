# Messenger (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Messenger's visual language into paste-ready Expo / React Native code: a design-token module, the conversation-anchored gradient bubble, and the bouncy reactions popover.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const gradient = ['#0A7CFF', '#9D4EDD', '#FF5CA0'] as const; // outgoing bubble only

export const light = {
  canvas:   '#FFFFFF',
  surface:  '#F1F1F2',
  incoming: '#F1F1F2',
  divider:  '#E4E6EB',
  textPrimary:   '#050505',
  textSecondary: '#65676B',
  textTertiary:  '#8A8D91',
} as const;

export const dark = {
  canvas:   '#000000',   // true black — makes the gradient glow
  surface:  '#1C1C1D',
  incoming: '#303030',
  divider:  '#3A3B3C',
  textPrimary:   '#E4E6EB',
  textSecondary: '#B0B3B8',
  textTertiary:  '#8A8D91',
} as const;

export const brand = {
  blue:        '#0A7CFF',
  bluePressed: '#0866D6',
  activeGreen: '#31D158',
  error:       '#FA383E',
  success:     '#31A24C',
} as const;
```

## 2. Typography

Messenger uses **Inter / SF** at weights 400/600/700. Load Inter via `expo-font`; reactions render as system emoji.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  largeTitle:  { fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.3 },
  convoName:   { fontFamily: 'Inter-SemiBold', fontSize: 17, lineHeight: 22 },
  convoUnread: { fontFamily: 'Inter-Bold',     fontSize: 17, lineHeight: 22 },
  threadTitle: { fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 20 },
  messageBody: { fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 22 },
  preview:     { fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 20 },
  section:     { fontFamily: 'Inter-Bold',     fontSize: 13, lineHeight: 16, letterSpacing: 0.2 },
  timestamp:   { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 16 },
  bubbleMeta:  { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 14 },
  reactCount:  { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 12 },
  button:      { fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 16 },
  tab:         { fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  activeNow:   { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 14 },
  system:      { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Conversation-Anchored Gradient Bubble

The gradient belongs to the conversation, not the bubble. Render one full-height `LinearGradient` behind the message stack and mask it to the outgoing bubbles with `MaskedView`. The example below shows the per-bubble fallback (each bubble carries a gradient sized to the thread, offset by its position).

```tsx
// components/OutgoingBubble.tsx
import { Text, View, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { light } from '../theme/colors';
import { typography } from '../theme/typography';

export function OutgoingBubble({
  text, isLastInRun, threadHeight, originY,
}: { text: string; isLastInRun: boolean; threadHeight: number; originY: number }) {
  const tail = isLastInRun ? 6 : 18;
  return (
    <View style={styles.row}>
      <View
        style={[
          styles.bubble,
          { borderBottomRightRadius: tail },
        ]}
      >
        {/* Gradient anchored to the conversation: full thread height, offset by this bubble's Y */}
        <LinearGradient
          colors={['#0A7CFF', '#9D4EDD', '#FF5CA0']}
          start={{ x: 0, y: 0 }}
          end={{ x: 1, y: 1 }}
          style={[StyleSheet.absoluteFill, { height: threadHeight, top: -originY }]}
        />
        <Text style={[typography.messageBody, { color: '#FFF' }]}>{text}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row:    { flexDirection: 'row', justifyContent: 'flex-end', paddingHorizontal: 10, marginVertical: 1 },
  bubble: { maxWidth: '72%', paddingVertical: 9, paddingHorizontal: 14,
            borderRadius: 18, overflow: 'hidden' },
});
```

> Production approach: wrap the whole `FlatList` of messages in `@react-native-masked-view/masked-view`; the mask is a transparent layer with white rounded rectangles at every outgoing bubble's measured frame, and the masked content is a single full-screen `LinearGradient`. This yields a perfectly continuous ribbon.

### Reactions Popover (Signature — bouncy)

```tsx
// components/ReactionsPopover.tsx
import Animated, { FadeIn, ZoomIn } from 'react-native-reanimated';
import { Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light } from '../theme/colors';

const EMOJI = ['👍', '❤️', '😆', '😮', '😢', '😡'];

export function ReactionsPopover({ onPick }: { onPick: (e: string) => void }) {
  return (
    <Animated.View
      entering={ZoomIn.springify().damping(11).mass(0.6)}
      style={styles.capsule}
    >
      {EMOJI.map((e, i) => (
        <Animated.View key={e} entering={ZoomIn.delay(i * 20).springify().damping(9)}>
          <Pressable onPress={() => onPick(e)}>
            <Text style={styles.emoji}>{e}</Text>
          </Pressable>
        </Animated.View>
      ))}
      <Ionicons name="add" size={18} color={light.textSecondary} />
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  capsule: {
    flexDirection: 'row', alignItems: 'center', gap: 12,
    paddingHorizontal: 16, paddingVertical: 10, borderRadius: 28,
    backgroundColor: light.canvas,
    shadowColor: '#000', shadowOpacity: 0.18, shadowRadius: 14, shadowOffset: { width: 0, height: 8 },
    elevation: 8,
  },
  emoji: { fontSize: 30 },
});
```

```tsx
// Corner reaction badge that lands with a bounce
import Animated, { ZoomIn } from 'react-native-reanimated';
export function ReactionBadge({ emoji, count }: { emoji: string; count: number }) {
  return (
    <Animated.View
      entering={ZoomIn.springify().damping(8)}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 3,
        paddingHorizontal: 6, paddingVertical: 3, borderRadius: 999,
        backgroundColor: light.canvas, borderWidth: 1, borderColor: light.divider }}
    >
      <Text style={{ fontSize: 14 }}>{emoji}</Text>
      {count > 1 && <Text style={[typography.reactCount, { color: light.textSecondary }]}>{count}</Text>}
    </Animated.View>
  );
}
```

### Big-Thumb Send / One-Tap Like

```tsx
// components/ComposerBar.tsx
import { useState } from 'react';
import { Pressable, TextInput, View, StyleSheet } from 'react-native';
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light, brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function ComposerBar() {
  const [text, setText] = useState('');
  const hasText = text.trim().length > 0;

  return (
    <View style={styles.bar}>
      {!hasText && (
        <Animated.View entering={FadeIn} exiting={FadeOut} style={styles.actions}>
          {(['camera', 'image', 'mic', 'happy'] as const).map(n => (
            <Ionicons key={n} name={`${n}-outline`} size={22} color={brand.blue} />
          ))}
        </Animated.View>
      )}
      <View style={styles.pill}>
        <TextInput
          style={[typography.messageBody, styles.input]}
          placeholder="Aa"
          placeholderTextColor={light.textTertiary}
          value={text}
          onChangeText={setText}
          multiline
        />
        <Ionicons name="happy-outline" size={20} color={light.textSecondary} />
      </View>
      <Pressable
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
          setText('');
        }}
      >
        {hasText ? (
          <View style={styles.sendFilled}>
            <Ionicons name="send" size={16} color="#FFF" />
          </View>
        ) : (
          // empty composer → blue thumbs-up; tap sends a 👍 like
          <View style={styles.sendThumb}>
            <Ionicons name="thumbs-up" size={22} color={brand.blue} />
          </View>
        )}
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  bar:        { flexDirection: 'row', alignItems: 'flex-end', gap: 10, paddingHorizontal: 10, paddingVertical: 8 },
  actions:    { flexDirection: 'row', alignItems: 'center', gap: 16 },
  pill:       { flex: 1, flexDirection: 'row', alignItems: 'center', gap: 8, minHeight: 36,
                paddingHorizontal: 12, borderRadius: 18, backgroundColor: light.surface },
  input:      { flex: 1, color: light.textPrimary, maxHeight: 110 },
  sendFilled: { width: 32, height: 32, borderRadius: 16, backgroundColor: brand.blue,
                alignItems: 'center', justifyContent: 'center' },
  sendThumb:  { width: 32, height: 32, alignItems: 'center', justifyContent: 'center' },
});
```

### Conversation Row (active-now dot)

```tsx
// components/ConversationRow.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import { light, brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function ConversationRow({
  name, preview, time, unread, activeNow,
}: { name: string; preview: string; time: string; unread: boolean; activeNow: boolean }) {
  return (
    <Pressable style={({ pressed }) => [styles.row, pressed && { backgroundColor: light.surface }]}>
      <View>
        <View style={styles.avatar} />
        {activeNow && <View style={styles.active} />}
      </View>
      <View style={{ flex: 1, gap: 3 }}>
        <Text style={[unread ? typography.convoUnread : typography.convoName, { color: light.textPrimary }]}>
          {name}
        </Text>
        <Text style={[typography.preview, { color: light.textSecondary }]} numberOfLines={1}>
          {preview}
        </Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 6 }}>
        <Text style={[typography.timestamp, { color: light.textSecondary }]}>{time}</Text>
        {unread && <View style={styles.dot} />}
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:    { height: 72, flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16 },
  avatar: { width: 56, height: 56, borderRadius: 28, backgroundColor: light.surface },
  active: { position: 'absolute', bottom: 0, right: 0, width: 14, height: 14, borderRadius: 7,
            backgroundColor: brand.activeGreen, borderWidth: 2, borderColor: light.canvas },
  dot:    { width: 8, height: 8, borderRadius: 4, backgroundColor: brand.blue },
});
```

## 4. Theme Switching

```tsx
// theme/useTheme.ts — Messenger uses TRUE black in dark mode
import { useColorScheme } from 'react-native';
import { light, dark } from './colors';
export const useTheme = () => (useColorScheme() === 'dark' ? dark : light);
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brand, light } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   brand.blue,
        tabBarInactiveTintColor: light.textSecondary,
        tabBarStyle: { backgroundColor: light.canvas, borderTopColor: light.divider, borderTopWidth: 1 },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"       options={{ title: 'Chats',       tabBarIcon: ({ color }) => <Ionicons name="chatbubble-ellipses" size={26} color={color} /> }} />
      <Tabs.Screen name="marketplace" options={{ title: 'Marketplace', tabBarIcon: ({ color }) => <Ionicons name="storefront" size={26} color={color} /> }} />
      <Tabs.Screen name="stories"     options={{ title: 'Stories',     tabBarIcon: ({ color }) => <Ionicons name="play-circle" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Reactions popover — ZoomIn springify + per-emoji delay (see ReactionsPopover)
// Reaction badge land — ZoomIn springify damping 8 (see ReactionBadge)

// Sent bubble pop
import Animated, { ZoomIn } from 'react-native-reanimated';
<Animated.View entering={ZoomIn.springify().damping(11).mass(0.7)} />

// Big-thumb morph — animate between thumb and filled send with FadeIn/FadeOut + LayoutAnimation
// Typing dots — three Reanimated shared values with staggered withRepeat(withTiming(...))
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Messenger's glyphs:

| Purpose | Ionicons |
|---------|----------|
| Send (text present) | `send` |
| Like / thumbs-up (empty) | `thumbs-up` |
| Camera | `camera-outline` |
| Photo | `image-outline` |
| Mic | `mic-outline` |
| Emoji / sticker | `happy-outline` |
| More reactions | `add` |
| Audio call | `call` |
| Video call | `videocam` |
| Compose | `create-outline` |
| Search | `search` |
| Chats | `chatbubble-ellipses` |
| Marketplace | `storefront` |
| Stories | `play-circle` |

## 8. Platform Notes

- **Conversation-anchored gradient**: use `@react-native-masked-view/masked-view` over a single full-screen `expo-linear-gradient` for a truly continuous ribbon; the per-bubble fallback is acceptable for short threads
- **Status bar**: `<StatusBar style="auto" />` from `expo-status-bar`
- **Dark mode**: the dark canvas is TRUE `#000000` (unlike privacy-minimal apps) — this is intentional; it makes the gradient luminesce
- **Safe area**: wrap screens in `SafeAreaView`; the composer rises with the keyboard via `KeyboardAvoidingView`
- **Reactions popover positioning**: measure the long-pressed bubble's frame and clamp the popover within screen bounds (reposition near edges)
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on bubble meta, reaction counts, tab labels
- **Reduce Motion**: gate the `springify()` enters behind `AccessibilityInfo.isReduceMotionEnabled()` and fall back to `FadeIn`
- **Accessibility**: the gradient is decorative — keep the bubble's accessible label plain ("You said …"); expose each popover emoji as a labeled button
