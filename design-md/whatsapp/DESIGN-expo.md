# WhatsApp (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates WhatsApp's visual language into paste-ready Expo / React Native code: a design-token module, themed components, Reanimated + Haptics snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-haptics`, `expo-blur`, `@expo/vector-icons`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  green:         '#25D366',
  greenPressed:  '#1EBE5D',
  teal:          '#075E54',
  midTeal:       '#128C7E',
  darkTeal:      '#054D44',

  // Bubbles
  outgoingLight: '#D9FDD3',
  outgoingDark:  '#005C4B',
  incomingLight: '#FFFFFF',
  incomingDark:  '#1F2C34',

  // Canvas
  wallpaperLight: '#ECE5DD',
  wallpaperDark:  '#0B141A',
  canvasLight:    '#FFFFFF',
  canvasDark:     '#111B21',
  surface1Light:  '#F7F8FA',
  surface1Dark:   '#202C33',
  surface2Dark:   '#2A3942',
  dividerLight:   '#E9EDEF',
  dividerDark:    '#222D34',

  // Text
  textPrimary:      '#111B21',
  textSecondary:    '#667781',
  textTertiary:     '#8696A0',
  textPrimaryDark:  '#E9EDEF',
  textSecondaryDark:'#8696A0',

  // Semantic
  readBlue:   '#53BDEB',
  errorRed:   '#F15C6D',
} as const;

export type WAColor = keyof typeof colors;
```

## 2. Typography

WhatsApp uses the system font — on iOS this renders as SF Pro. Do not bundle custom fonts.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
import { Platform } from 'react-native';

const system = Platform.OS === 'ios' ? undefined : 'System'; // iOS default is SF Pro automatically

export const typography = {
  largeTitle:     { fontFamily: system, fontSize: 34, fontWeight: '700' as const, letterSpacing: 0.37 },
  navTitle:       { fontFamily: system, fontSize: 17, fontWeight: '600' as const, letterSpacing: -0.4 },
  contactName:    { fontFamily: system, fontSize: 17, fontWeight: '600' as const, letterSpacing: -0.4 },
  messagePreview: { fontFamily: system, fontSize: 15, fontWeight: '400' as const, letterSpacing: -0.2, color: '#667781' },
  bubbleBody:     { fontFamily: system, fontSize: 17, fontWeight: '400' as const, letterSpacing: -0.4 },
  groupSender:    { fontFamily: system, fontSize: 13, fontWeight: '600' as const, letterSpacing: -0.1 },
  timestampList:  { fontFamily: system, fontSize: 12, fontWeight: '400' as const, color: '#667781' },
  timestampBubble:{ fontFamily: system, fontSize: 11, fontWeight: '400' as const, color: '#667781' },
  sectionHeader:  { fontFamily: system, fontSize: 13, fontWeight: '600' as const, letterSpacing: -0.1 },
  systemMessage:  { fontFamily: system, fontSize: 12, fontWeight: '500' as const, color: '#54636D' },
  tabLabel:       { fontFamily: system, fontSize: 10, fontWeight: '500' as const, letterSpacing: 0.1 },
  button:         { fontFamily: system, fontSize: 17, fontWeight: '600' as const, color: '#FFFFFF' },
  inputPlaceholder:{ fontFamily: system, fontSize: 16, fontWeight: '400' as const, color: '#8696A0' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Send / Mic Morphing Button

```tsx
// components/SendButton.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function SendButton({ hasText, onPress }: { hasText: boolean; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.92, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
    >
      <Animated.View
        style={[
          {
            width: 36, height: 36, borderRadius: 18,
            backgroundColor: colors.green,
            alignItems: 'center', justifyContent: 'center',
          },
          style,
        ]}
      >
        <Ionicons name={hasText ? 'send' : 'mic'} size={18} color="#FFF" />
      </Animated.View>
    </Pressable>
  );
}
```

### Chat Bubble (Outgoing) with Asymmetric Tail

React Native does not support per-corner radius on `View` the way SwiftUI does. Use individual `borderXXXRadius` properties.

```tsx
// components/OutgoingBubble.tsx
import { View, Text, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type ReadState = 'sent' | 'delivered' | 'read';

export function OutgoingBubble({ text, timestamp, readState }: {
  text: string;
  timestamp: string;
  readState: ReadState;
}) {
  const tintedIcon = readState === 'read' ? colors.readBlue : colors.textTertiary;

  return (
    <View style={styles.row}>
      <View style={styles.bubble}>
        <Text style={typography.bubbleBody}>{text}</Text>
        <View style={styles.metaRow}>
          <Text style={typography.timestampBubble}>{timestamp}</Text>
          {readState === 'sent' ? (
            <Ionicons name="checkmark" size={14} color={tintedIcon} />
          ) : (
            <Ionicons name="checkmark-done" size={16} color={tintedIcon} />
          )}
        </View>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row: { width: '100%', alignItems: 'flex-end', paddingRight: 8, paddingVertical: 1 },
  bubble: {
    maxWidth: '80%',
    backgroundColor: colors.outgoingLight,
    paddingVertical: 8,
    paddingHorizontal: 12,
    borderTopLeftRadius: 12,
    borderTopRightRadius: 12,
    borderBottomLeftRadius: 12,
    borderBottomRightRadius: 0, // tail corner
  },
  metaRow: { flexDirection: 'row', alignItems: 'center', gap: 4, alignSelf: 'flex-end', marginTop: 2 },
});
```

### Chat Bubble (Incoming)

```tsx
// components/IncomingBubble.tsx
import { View, Text, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function IncomingBubble({ text, sender }: { text: string; sender?: string }) {
  return (
    <View style={styles.row}>
      <View style={styles.bubble}>
        {sender && <Text style={[typography.groupSender, { color: '#E35D87' }]}>{sender}</Text>}
        <Text style={typography.bubbleBody}>{text}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  row: { width: '100%', alignItems: 'flex-start', paddingLeft: 8, paddingVertical: 1 },
  bubble: {
    maxWidth: '80%',
    backgroundColor: colors.incomingLight,
    paddingVertical: 8,
    paddingHorizontal: 12,
    borderTopLeftRadius: 12,
    borderTopRightRadius: 12,
    borderBottomLeftRadius: 0, // tail corner
    borderBottomRightRadius: 12,
    shadowColor: '#000',
    shadowOpacity: 0.08,
    shadowRadius: 2,
    shadowOffset: { width: 0, height: 1 },
    elevation: 1,
  },
});
```

### Chat List Row

```tsx
// components/ChatListRow.tsx
import { View, Text, Image, Pressable, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatListRow({
  avatarUri, name, preview, timestamp, unreadCount, hasStatusRing, onPress,
}: {
  avatarUri: string;
  name: string;
  preview: string;
  timestamp: string;
  unreadCount: number;
  hasStatusRing: boolean;
  onPress: () => void;
}) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => [styles.row, pressed && { backgroundColor: '#F0F2F5' }]}
    >
      <View style={styles.avatarWrap}>
        {hasStatusRing && <View style={styles.ring} />}
        <Image source={{ uri: avatarUri }} style={styles.avatar} />
      </View>
      <View style={styles.textCol}>
        <Text style={typography.contactName} numberOfLines={1}>{name}</Text>
        <Text style={typography.messagePreview} numberOfLines={1}>{preview}</Text>
      </View>
      <View style={styles.metaCol}>
        <Text style={[typography.timestampList, unreadCount > 0 && { color: colors.green }]}>
          {timestamp}
        </Text>
        {unreadCount > 0 && (
          <View style={styles.badge}>
            <Text style={styles.badgeText}>{unreadCount}</Text>
          </View>
        )}
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: { height: 72, flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, gap: 12 },
  avatarWrap: { width: 54, height: 54, alignItems: 'center', justifyContent: 'center' },
  ring: { position: 'absolute', width: 54, height: 54, borderRadius: 27, borderWidth: 2.5, borderColor: colors.green },
  avatar: { width: 48, height: 48, borderRadius: 24, backgroundColor: colors.surface1Light },
  textCol: { flex: 1, gap: 3 },
  metaCol: { alignItems: 'flex-end', gap: 4 },
  badge: { backgroundColor: colors.green, minWidth: 20, height: 20, paddingHorizontal: 6, borderRadius: 10, alignItems: 'center', justifyContent: 'center' },
  badgeText: { color: '#FFF', fontSize: 12, fontWeight: '600' },
});
```

### Voice Message Waveform Bubble

```tsx
// components/VoiceWaveformBubble.tsx
import { useState } from 'react';
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function VoiceWaveformBubble({ duration = '0:23' }: { duration?: string }) {
  const [isPlaying, setIsPlaying] = useState(false);
  const [progress, setProgress] = useState(0);
  const amplitudes = Array.from({ length: 48 }, () => 0.2 + Math.random() * 0.8);

  return (
    <View style={styles.bubble}>
      <Pressable
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
          setIsPlaying((p) => !p);
        }}
        style={styles.playBtn}
      >
        <Ionicons name={isPlaying ? 'pause' : 'play'} size={16} color="#FFF" />
      </Pressable>
      <View style={styles.wave}>
        {amplitudes.map((a, i) => {
          const played = i / amplitudes.length <= progress;
          return (
            <View
              key={i}
              style={{
                width: 2,
                height: Math.max(4, a * 20),
                backgroundColor: played ? colors.green : colors.textTertiary,
                marginRight: 2,
                borderRadius: 1,
              }}
            />
          );
        })}
      </View>
      <Text style={typography.timestampBubble}>{duration}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  bubble: {
    flexDirection: 'row',
    alignItems: 'center',
    gap: 10,
    backgroundColor: colors.outgoingLight,
    paddingVertical: 10,
    paddingHorizontal: 12,
    borderTopLeftRadius: 12,
    borderTopRightRadius: 12,
    borderBottomLeftRadius: 12,
    borderBottomRightRadius: 0,
    alignSelf: 'flex-end',
    marginRight: 8,
    maxWidth: '80%',
  },
  playBtn: {
    width: 36, height: 36, borderRadius: 18,
    backgroundColor: colors.green,
    alignItems: 'center', justifyContent: 'center',
  },
  wave: { flexDirection: 'row', alignItems: 'center', height: 24 },
});
```

### Compose Bar

```tsx
// components/ComposeBar.tsx
import { useState } from 'react';
import { View, TextInput, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { SendButton } from './SendButton';
import { colors } from '../theme/colors';

export function ComposeBar({ onSend }: { onSend: (text: string) => void }) {
  const [text, setText] = useState('');
  return (
    <View style={styles.wrap}>
      <View style={styles.pill}>
        <Pressable hitSlop={8}>
          <Ionicons name="happy-outline" size={22} color={colors.textTertiary} />
        </Pressable>
        <TextInput
          placeholder="Message"
          placeholderTextColor={colors.textTertiary}
          value={text}
          onChangeText={setText}
          multiline
          style={styles.input}
        />
        <Pressable hitSlop={8}>
          <Ionicons name="attach" size={22} color={colors.textTertiary} />
        </Pressable>
        <Pressable hitSlop={8}>
          <Ionicons name="camera" size={22} color={colors.textTertiary} />
        </Pressable>
      </View>
      <SendButton hasText={text.length > 0} onPress={() => { if (text) { onSend(text); setText(''); } }} />
    </View>
  );
}

const styles = StyleSheet.create({
  wrap: { flexDirection: 'row', alignItems: 'flex-end', gap: 8, paddingHorizontal: 8, paddingVertical: 8, backgroundColor: colors.surface1Light },
  pill: {
    flex: 1, flexDirection: 'row', alignItems: 'center', gap: 10,
    backgroundColor: '#FFFFFF', paddingHorizontal: 14, paddingVertical: 8,
    borderRadius: 22,
    minHeight: 40,
  },
  input: { flex: 1, fontSize: 16, color: colors.textPrimary, maxHeight: 120 },
});
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.green,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          position: 'absolute',
          borderTopWidth: 0,
          backgroundColor: 'transparent',
        },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.92)' }} />
        ),
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="updates"    options={{ title: 'Updates',    tabBarIcon: ({ color }) => <Ionicons name="ellipsis-horizontal-circle-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="calls"      options={{ title: 'Calls',      tabBarIcon: ({ color }) => <Ionicons name="call" size={24} color={color} /> }} />
      <Tabs.Screen name="communities" options={{ title: 'Communities', tabBarIcon: ({ color }) => <Ionicons name="people" size={24} color={color} /> }} />
      <Tabs.Screen name="chats"      options={{ title: 'Chats',      tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={24} color={color} />, tabBarBadge: 3 }} />
      <Tabs.Screen name="settings"   options={{ title: 'Settings',   tabBarIcon: ({ color }) => <Ionicons name="settings" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Wallpaper & Chat Canvas

```tsx
// components/ChatCanvas.tsx
import { View, Image } from 'react-native';
import { colors } from '../theme/colors';

export function ChatCanvas({ children, dark = false }: { children: React.ReactNode; dark?: boolean }) {
  return (
    <View style={{ flex: 1, backgroundColor: dark ? colors.wallpaperDark : colors.wallpaperLight }}>
      {/* Optionally layer a doodle pattern via expo-image or a locally bundled asset */}
      {children}
    </View>
  );
}
```

## 6. Motion

```tsx
// Send pressed
scale.value = withSpring(0.92, { damping: 14 });
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Mic hold (voice record)
// Use a Gesture.LongPress + Gesture.Pan to let user drag up to lock, drag left to cancel
import { Gesture } from 'react-native-gesture-handler';

const long = Gesture.LongPress().onStart(() => { /* start record */ });
const pan = Gesture.Pan().onUpdate((e) => { /* ring scale + decibel */ });

// Read receipt turning blue
// Swap icon name when read; pair with LayoutAnimation or Reanimated .withTiming

// Swipe-to-reply
// Gesture.Pan with translationX threshold of ~60px; fire Haptics.selectionAsync once per threshold
```

## 7. Icon Library

Use `@expo/vector-icons` Ionicons — they map cleanly to SF Symbols WhatsApp uses.

| Purpose | Ionicons |
|---------|----------|
| Send | `send` |
| Mic | `mic` |
| Attach | `attach` |
| Camera | `camera` / `camera-outline` |
| Emoji | `happy-outline` |
| Read ticks | `checkmark` (single) / `checkmark-done` (double) |
| Phone | `call` / `call-outline` |
| Video | `videocam` |
| Lock | `lock-closed` |
| Chats tab | `chatbubble` / `chatbubble-outline` |
| Calls tab | `call` |
| Communities tab | `people` |
| Updates tab | `ellipsis-horizontal-circle-outline` |
| Settings tab | `settings` / `settings-outline` |
| New chat | `create` |
| Search | `search` |
| Ellipsis | `ellipsis-horizontal` |
| Play / Pause | `play` / `pause` |

## 8. Platform Notes

- **iOS-only feel**: Use `expo-blur` for tab-bar blur. Android falls back to opaque.
- **Status bar**: Set `<StatusBar style="auto" />` from `expo-status-bar`. In chat screens with a dark wallpaper, force `dark-content` on light and `light-content` on dark.
- **Safe area**: Wrap with `SafeAreaView` from `react-native-safe-area-context`. The compose bar hugs the bottom safe-area inset.
- **Dynamic Type**: React Native respects user font-scaling by default; set `allowFontScaling={false}` only on 11pt inline timestamps.
- **Accessibility**: `accessibilityRole="button"`, `accessibilityLabel` dynamically per send vs mic; group bubble text + timestamp + read-state via `accessibilityLabel` concatenation.
- **RTL**: React Native supports `I18nManager.isRTL`. Manually swap `borderBottomLeftRadius` and `borderBottomRightRadius` on bubbles when RTL is active so the tail corner mirrors.
- **Wallpaper**: bundle a low-contrast SVG/PNG doodle as a repeating pattern via `expo-image` or background `resizeMode: 'repeat'` on `<Image>`.
