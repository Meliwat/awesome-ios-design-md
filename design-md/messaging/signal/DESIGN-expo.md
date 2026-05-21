# Signal (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Signal's visual language into paste-ready Expo / React Native code: a design-token module, the message bubbles, and the slide-up send button.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const light = {
  canvas:   '#FFFFFF',
  surface:  '#F5F5F5',
  incoming: '#E9E9EB',
  divider:  '#E5E5E5',
  textPrimary:   '#000000',
  textSecondary: '#6B6B6B',
  textTertiary:  '#9A9A9A',
} as const;

export const dark = {
  canvas:   '#1B1B1B',
  surface:  '#2A2A2A',
  incoming: '#2A2A2A',
  divider:  '#3A3A3A',
  textPrimary:   '#FFFFFF',
  textSecondary: '#9A9A9A',
  textTertiary:  '#6B6B6B',
} as const;

export const brand = {
  blue:        '#3A76F0',
  bluePressed: '#2F5FCC',
  blueTint:    '#E7EEFD',
  outMeta:     '#CBD9F9',
  error:       '#D7263D',
  success:     '#3AB54A',
} as const;
```

## 2. Typography

Signal uses **Inter** everywhere at weights 400/500/700. Load via `expo-font`; the safety number uses a monospace stack.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
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
  largeTitle:    { fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.3 },
  convoName:     { fontFamily: 'Inter-Medium',   fontSize: 17, lineHeight: 22 },
  threadTitle:   { fontFamily: 'Inter-SemiBold', fontSize: 17, lineHeight: 21 },
  messageBody:   { fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 22 },
  preview:       { fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 20 },
  sectionHeader: { fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 16, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  timestamp:     { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 16 },
  bubbleMeta:    { fontFamily: 'Inter-Regular',  fontSize: 12, lineHeight: 14 },
  button:        { fontFamily: 'Inter-SemiBold', fontSize: 16, lineHeight: 16 },
  buttonText:    { fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 16 },
  tab:           { fontFamily: 'Inter-Medium',   fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  timerChip:     { fontFamily: 'Inter-Medium',   fontSize: 12, lineHeight: 12 },
  systemNote:    { fontFamily: 'Inter-Regular',  fontSize: 13, lineHeight: 18 },
  safetyNumber:  { fontFamily: 'Menlo',          fontSize: 15, lineHeight: 22, letterSpacing: 0.5 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Message Bubble (Outgoing + Incoming with same-sender tail)

```tsx
// components/MessageBubble.tsx
import { Text, View, StyleSheet } from 'react-native';
import { light, brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function MessageBubble({
  text, time, isOutgoing, isLastInRun,
}: { text: string; time: string; isOutgoing: boolean; isLastInRun: boolean }) {
  const tail = isLastInRun ? 6 : 18;
  return (
    <View style={[styles.row, { justifyContent: isOutgoing ? 'flex-end' : 'flex-start' }]}>
      <View
        style={[
          styles.bubble,
          {
            backgroundColor: isOutgoing ? brand.blue : light.incoming,
            borderBottomRightRadius: isOutgoing ? tail : 18,
            borderBottomLeftRadius:  isOutgoing ? 18 : tail,
          },
        ]}
      >
        <Text style={[typography.messageBody, { color: isOutgoing ? '#FFF' : light.textPrimary }]}>
          {text}
        </Text>
        <View style={styles.meta}>
          <Text style={[typography.bubbleMeta, { color: isOutgoing ? brand.outMeta : light.textSecondary }]}>
            {time}
          </Text>
          {isOutgoing && (
            <Text style={[typography.bubbleMeta, { color: brand.outMeta }]}>✓✓</Text>
          )}
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row:    { flexDirection: 'row', paddingHorizontal: 12, marginVertical: 1 },
  bubble: { maxWidth: '75%', paddingVertical: 9, paddingHorizontal: 13,
            borderRadius: 18 },
  meta:   { flexDirection: 'row', gap: 3, alignSelf: 'flex-end', marginTop: 2 },
});
```

### Slide-Up Send Button (Signature)

```tsx
// components/ComposerBar.tsx
import { useState } from 'react';
import { Pressable, TextInput, View, Text, StyleSheet } from 'react-native';
import Animated, { useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light, brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function ComposerBar() {
  const [text, setText] = useState('');
  const hasText = text.trim().length > 0;

  const sendStyle = useAnimatedStyle(() => ({
    opacity: withSpring(hasText ? 1 : 0.9, { damping: 14 }),
    transform: [{ translateY: withSpring(hasText ? 0 : 4, { damping: 14 }) }],
  }));

  return (
    <View style={styles.bar}>
      <View style={styles.pill}>
        <Ionicons name="add" size={22} color={light.textSecondary} />
        <TextInput
          style={[typography.messageBody, styles.input]}
          placeholder="Signal message"
          placeholderTextColor={light.textTertiary}
          value={text}
          onChangeText={setText}
          multiline
        />
        <Ionicons name="camera-outline" size={20} color={light.textSecondary} />
      </View>
      <Pressable
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
          setText('');
        }}
      >
        {({ pressed }) => (
          <Animated.View
            style={[
              styles.send,
              sendStyle,
              { backgroundColor: pressed ? brand.bluePressed : brand.blue,
                transform: [{ scale: pressed ? 0.9 : 1 }] },
            ]}
          >
            {/* up-arrow when text exists, mic when empty */}
            <Ionicons name={hasText ? 'arrow-up' : 'mic'} size={16} color="#FFF" />
          </Animated.View>
        )}
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  bar:   { flexDirection: 'row', alignItems: 'flex-end', gap: 8, paddingHorizontal: 12, paddingVertical: 8 },
  pill:  { flex: 1, flexDirection: 'row', alignItems: 'center', gap: 8, minHeight: 36,
           paddingHorizontal: 12, borderRadius: 18, backgroundColor: light.surface },
  input: { flex: 1, color: light.textPrimary, maxHeight: 110 },
  send:  { width: 32, height: 32, borderRadius: 16, alignItems: 'center', justifyContent: 'center' },
});
```

### Conversation Row

```tsx
// components/ConversationRow.tsx
import { Pressable, Text, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light, brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function ConversationRow({
  name, preview, time, unread, disappearing,
}: { name: string; preview: string; time: string; unread: number; disappearing: boolean }) {
  return (
    <Pressable
      style={({ pressed }) => [styles.row, pressed && { backgroundColor: light.surface }]}
    >
      <View style={styles.avatar} />
      <View style={{ flex: 1, gap: 3 }}>
        <View style={styles.nameRow}>
          <Text style={[typography.convoName, { color: light.textPrimary }]}>{name}</Text>
          {disappearing && <Ionicons name="timer-outline" size={13} color={light.textSecondary} />}
        </View>
        <Text style={[typography.preview, { color: light.textSecondary }]} numberOfLines={1}>
          {preview}
        </Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 6 }}>
        <Text style={[typography.timestamp, { color: light.textSecondary }]}>{time}</Text>
        {unread > 0 && (
          <View style={styles.pill}>
            <Text style={[typography.bubbleMeta, { color: '#FFF' }]}>{unread}</Text>
          </View>
        )}
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row:     { height: 72, flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 16 },
  avatar:  { width: 48, height: 48, borderRadius: 24, backgroundColor: light.surface },
  nameRow: { flexDirection: 'row', alignItems: 'center', gap: 5 },
  pill:    { minWidth: 20, height: 20, borderRadius: 10, paddingHorizontal: 6,
             alignItems: 'center', justifyContent: 'center', backgroundColor: brand.blue },
});
```

### Disappearing-Message Timer Chip + Encryption Note

```tsx
import { Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light } from '../theme/colors';
import { typography } from '../theme/typography';

export const TimerChip = ({ duration }: { duration: string }) => (
  <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
    {/* privacy is quiet — neutral gray, never colored */}
    <Ionicons name="timer-outline" size={13} color={light.textSecondary} />
    <Text style={[typography.timerChip, { color: light.textSecondary }]}>{duration}</Text>
  </View>
);

export const EncryptionNote = () => (
  <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 5, paddingVertical: 12 }}>
    <Ionicons name="lock-closed" size={12} color={light.textSecondary} />
    <Text style={[typography.systemNote, { color: light.textSecondary }]}>
      Messages and calls are end-to-end encrypted.
    </Text>
  </View>
);
```

## 4. Theme Switching

```tsx
// theme/useTheme.ts — Signal follows system; soft-black dark, never pure black
import { useColorScheme } from 'react-native';
import { light, dark } from './colors';

export function useTheme() {
  const scheme = useColorScheme();
  return scheme === 'dark' ? dark : light;
}
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
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={24} color={color} /> }} />
      <Tabs.Screen name="calls"    options={{ title: 'Calls',    tabBarIcon: ({ color }) => <Ionicons name="call" size={24} color={color} /> }} />
      <Tabs.Screen name="stories"  options={{ title: 'Stories',  tabBarIcon: ({ color }) => <Ionicons name="ellipse-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="settings" options={{ title: 'Settings', tabBarIcon: ({ color }) => <Ionicons name="settings" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Send button slide-up + fade — see ComposerBar (withSpring opacity + translateY)
// Send press: scale 0.90 + light haptic

// Outgoing bubble enters from composer
import Animated, { SlideInDown, FadeIn } from 'react-native-reanimated';
<Animated.View entering={SlideInDown.duration(250).springify()} />

// Reaction picker scales in above the bubble
import { ZoomIn } from 'react-native-reanimated';
<Animated.View entering={ZoomIn.springify().damping(12)} />

// Disappearing timer ring: animate an SVG circle's strokeDashoffset over message lifetime
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Signal's glyphs:

| Purpose | Ionicons |
|---------|----------|
| Send | `arrow-up` |
| Voice (empty composer) | `mic` |
| Attach | `add` |
| Camera | `camera-outline` |
| Audio call | `call` |
| Video call | `videocam` |
| Compose | `create-outline` |
| Disappearing timer | `timer-outline` |
| Sealed sender | `lock-closed` |
| Safety shield | `shield-checkmark` |
| Search | `search` |
| Chats | `chatbubble` |
| Calls | `call` |
| Stories | `ellipse-outline` |
| Settings | `settings` |

## 8. Platform Notes

- **Privacy-minimal stance**: do not add ads, Stories pressure, streaks, or vanity counts — the absence is the design even cross-platform
- **Status bar**: `<StatusBar style="auto" />` from `expo-status-bar` — follows the white/soft-black canvas
- **Dark mode**: use `useColorScheme()`; the dark canvas is `#1B1B1B`, never `#000000`
- **Safe area**: wrap screens in `SafeAreaView`; the composer must sit above the keyboard via `KeyboardAvoidingView` (iOS `behavior="padding"`)
- **Send button visibility**: drive it off `text.trim().length` and animate with Reanimated `withSpring` — never render it when empty (show mic instead)
- **Dynamic Type**: RN respects font scaling; set `allowFontScaling={false}` on bubble meta, timestamps, and tab labels only
- **Accessibility**: announce bubble direction before text; hide the send button from accessibility when it's a mic; expose the timer chip as "Disappearing messages, <duration>"
