# Character.AI (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Character.AI's visual language into paste-ready Expo / React Native code: a design-token module, the asymmetric tuck bubble, the typing indicator, streaming, and themed components.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3. (Avatar gradients use a layered `LinearGradient`/radial approximation.)

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark — primary)
  canvas:     '#0F0F10',
  surface1:   '#161618',
  surface2:   '#1E1E21',
  bubbleAI:   '#1C1C1F',
  bubbleUser: '#26262A',
  divider:    '#2A2A2E',
  scrim:      'rgba(0,0,0,0.5)',

  // Canvas & surfaces (light)
  canvasLight:     '#FFFFFF',
  bubbleAILight:   '#F1F1F3',
  bubbleUserLight: '#E4E8F2',
  dividerLight:    '#E6E6EA',

  // Brand
  accent:      '#3A7BFD',
  accentPress: '#2E63D6',
  accentSoft:  '#1B2A4A',
  accentSoftText: '#9FC0FF',
  lilac:       '#9D7BFF',          // avatar gradient only

  // Text
  textPrimary:   '#ECECEE',
  textSecondary: '#9A9AA2',
  textTertiary:  '#66666E',
  onAccent:      '#FFFFFF',

  // Semantic
  success: '#4ED99A',
  error:   '#FF6B6B',
} as const;

// Character avatar gradient stops (the single warm focal point)
export const avatarBlue  = ['#6FA0FF', '#3A7BFD', '#274FB0'] as const;
export const avatarLilac = ['#C7A8FF', '#9D7BFF', '#6A47C9'] as const;
```

## 2. Typography

Load **Sora** (identity) + **Inter** (reading comfort, incl. italic) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Sora-SemiBold': require('../assets/fonts/Sora-SemiBold.ttf'),
    'Sora-Bold':     require('../assets/fonts/Sora-Bold.ttf'),
    'Inter-Regular': require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Italic':  require('../assets/fonts/Inter-Italic.ttf'),
    'Inter-Medium':  require('../assets/fonts/Inter-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false, contentStyle: { backgroundColor: '#0F0F10' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  display:      { fontFamily: 'Sora-Bold',     fontSize: 32, lineHeight: 38, letterSpacing: -0.5, color: '#ECECEE' },
  screenTitle:  { fontFamily: 'Sora-SemiBold', fontSize: 24, lineHeight: 30, letterSpacing: -0.3, color: '#ECECEE' },
  greetingName: { fontFamily: 'Sora-SemiBold', fontSize: 20, lineHeight: 26, letterSpacing: -0.2, color: '#ECECEE' },
  headerName:   { fontFamily: 'Sora-SemiBold', fontSize: 17, lineHeight: 22, color: '#ECECEE' },
  cardName:     { fontFamily: 'Sora-SemiBold', fontSize: 15, lineHeight: 20, color: '#ECECEE' },
  button:       { fontFamily: 'Sora-SemiBold', fontSize: 15, lineHeight: 15 },
  tab:          { fontFamily: 'Sora-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.2 },
  message:      { fontFamily: 'Inter-Regular', fontSize: 15, lineHeight: 23, color: '#ECECEE' },
  roleplay:     { fontFamily: 'Inter-Italic',  fontSize: 15, lineHeight: 23, color: '#9A9AA2' },
  cardDesc:     { fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 19, color: '#9A9AA2' },
  body:         { fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 25, color: '#ECECEE' },
  meta:         { fontFamily: 'Inter-Medium',  fontSize: 11, lineHeight: 14, color: '#66666E' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Asymmetric Tuck Bubble (with roleplay parsing)

```tsx
// components/MessageBubble.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const TUCK = 4, ROUND = 18;

// AI tucks top-left (points at avatar); user mirrors to top-right.
function bubbleRadius(speaker: 'ai' | 'user') {
  return {
    borderTopLeftRadius:  speaker === 'ai'   ? TUCK : ROUND,
    borderTopRightRadius: speaker === 'user' ? TUCK : ROUND,
    borderBottomLeftRadius: ROUND,
    borderBottomRightRadius: ROUND,
  };
}

// Render `*roleplay actions*` italic + secondary — load-bearing signature.
function renderText(raw: string) {
  return raw.split('*').map((seg, i) =>
    seg.length === 0 ? null : (
      <Text key={i} style={i % 2 === 1 ? typography.roleplay : typography.message}>{seg}</Text>
    )
  );
}

export function MessageBubble({
  speaker, text, avatar,
}: { speaker: 'ai' | 'user'; text: string; avatar: readonly string[] }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-start', gap: 9,
      paddingHorizontal: 14, maxWidth: '100%',
      justifyContent: speaker === 'ai' ? 'flex-start' : 'flex-end',
    }}>
      {speaker === 'ai' && (
        <LinearGradient colors={avatar} start={{ x: 0.2, y: 0.15 }}
          style={{ width: 26, height: 26, borderRadius: 13, marginTop: 2 }} />
      )}
      <View style={{
        maxWidth: '86%',
        backgroundColor: speaker === 'ai' ? colors.bubbleAI : colors.bubbleUser,
        borderWidth: speaker === 'ai' ? 1 : 0, borderColor: colors.divider,
        paddingVertical: 11, paddingHorizontal: 14,
        ...bubbleRadius(speaker),
      }}>
        <Text>{renderText(text)}</Text>
      </View>
    </View>
  );
}
```

### Typing Indicator

```tsx
// components/TypingIndicator.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, withDelay } from 'react-native-reanimated';
import { colors } from '../theme/colors';

function Dot({ delay }: { delay: number }) {
  const o = useSharedValue(0.4);
  useEffect(() => {
    o.value = withDelay(delay, withRepeat(withTiming(1, { duration: 600 }), -1, true));
  }, []);
  const s = useAnimatedStyle(() => ({ opacity: o.value }));
  return <Animated.View style={[{ width: 6, height: 6, borderRadius: 3, backgroundColor: colors.textTertiary }, s]} />;
}

export function TypingIndicator({ avatar }: { avatar: readonly string[] }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-start', gap: 9, paddingHorizontal: 14 }}>
      <LinearGradient colors={avatar} start={{ x: 0.2, y: 0.15 }}
        style={{ width: 26, height: 26, borderRadius: 13, marginTop: 2 }} />
      <View style={{
        flexDirection: 'row', gap: 5, alignItems: 'center',
        backgroundColor: colors.bubbleAI, borderWidth: 1, borderColor: colors.divider,
        paddingVertical: 14, paddingHorizontal: 16,
        borderTopLeftRadius: 4, borderTopRightRadius: 18, borderBottomLeftRadius: 18, borderBottomRightRadius: 18,
      }}>
        <Dot delay={0} /><Dot delay={160} /><Dot delay={320} />
      </View>
    </View>
  );
}
```

### Greeting Header

```tsx
// components/GreetingHeader.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { typography } from '../theme/typography';

export function GreetingHeader({
  avatar, name, tagline,
}: { avatar: readonly string[]; name: string; tagline: string }) {
  const p = useSharedValue(0);
  useEffect(() => { p.value = withTiming(1, { duration: 260 }); }, []);
  const aStyle = useAnimatedStyle(() => ({ opacity: p.value, transform: [{ scale: 0.92 + 0.08 * p.value }] }));
  const tStyle = useAnimatedStyle(() => ({ opacity: p.value }));

  return (
    <View style={{ alignItems: 'center', paddingVertical: 8, gap: 8 }}>
      <Animated.View style={aStyle}>
        <LinearGradient colors={avatar} start={{ x: 0.2, y: 0.15 }}
          style={{ width: 64, height: 64, borderRadius: 32 }} />
      </Animated.View>
      <Animated.Text style={[typography.greetingName, tStyle]}>{name}</Animated.Text>
      <Animated.Text style={[typography.cardDesc, { color: '#66666E' }, tStyle]}>{tagline}</Animated.Text>
    </View>
  );
}
```

### Character Card (Discover)

```tsx
// components/CharacterCard.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CharacterCard({
  avatar, name, desc, chats, onPress,
}: { avatar: readonly string[]; name: string; desc: string; chats: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      flex: 1, gap: 8, padding: 16, borderRadius: 16,
      backgroundColor: pressed ? colors.bubbleUser : colors.surface2,
      borderWidth: 1, borderColor: colors.divider,
      transform: [{ scale: pressed ? 0.99 : 1 }],
    })}>
      <LinearGradient colors={avatar} start={{ x: 0.2, y: 0.15 }}
        style={{ width: 48, height: 48, borderRadius: 24 }} />
      <Text style={typography.cardName}>{name}</Text>
      <Text style={typography.cardDesc} numberOfLines={2}>{desc}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
        <Ionicons name="chatbubbles-outline" size={11} color={colors.textTertiary} />
        <Text style={typography.meta}>{chats}</Text>
      </View>
    </Pressable>
  );
}
```

### Composer

```tsx
// components/Composer.tsx
import { Pressable, TextInput, View } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Composer({
  value, onChange, characterName, streaming, onSend, onStop,
}: {
  value: string; onChange: (t: string) => void; characterName: string;
  streaming: boolean; onSend: () => void; onStop: () => void;
}) {
  const empty = value.trim().length === 0 && !streaming;
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-end', gap: 10,
      paddingHorizontal: 14, paddingTop: 10, paddingBottom: 16,
      borderTopWidth: 0.5, borderTopColor: colors.divider,
    }}>
      <TextInput
        value={value}
        onChangeText={onChange}
        placeholder={`Message ${characterName}…`}
        placeholderTextColor={colors.textTertiary}
        multiline
        style={[typography.message, {
          flex: 1, minHeight: 44, maxHeight: 132, paddingHorizontal: 16, paddingTop: 12,
          backgroundColor: colors.surface2, borderRadius: 22,
          borderWidth: 1, borderColor: colors.divider,
        }]}
      />
      <Pressable
        onPress={streaming ? onStop : onSend}
        style={{
          width: 40, height: 40, borderRadius: 20, alignItems: 'center', justifyContent: 'center',
          backgroundColor: empty ? colors.bubbleUser : colors.accent,
        }}
      >
        <Ionicons name={streaming ? 'stop' : 'arrow-up'} size={18}
          color={empty ? colors.textTertiary : colors.onAccent} />
      </Pressable>
    </View>
  );
}
```

### Buttons

```tsx
// components/CAIButtons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CAIPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.accentPress : colors.accent,
      paddingVertical: 13, paddingHorizontal: 26, borderRadius: 999,
      alignItems: 'center', transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={[typography.button, { color: colors.onAccent }]}>{title}</Text>
    </Pressable>
  );
}

export function CAISoftButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: colors.accentSoft, opacity: pressed ? 0.85 : 1,
      paddingVertical: 13, paddingHorizontal: 26, borderRadius: 999, alignItems: 'center',
    })}>
      <Text style={[typography.button, { color: colors.accentSoftText }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.accent,       // blue icon + blue label; no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarLabelStyle: { fontFamily: 'Sora-SemiBold', fontSize: 10, letterSpacing: 0.2 },
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="sparkles-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="chats"   options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubbles-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="create"  options={{ title: 'Create',   tabBarIcon: ({ color }) => <Ionicons name="add-circle-outline" size={23} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-outline" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Message send — user bubble fade + 8pt slide-up, list autoscroll
import Animated, { FadeInDown } from 'react-native-reanimated';
// <Animated.View entering={FadeInDown.duration(120)}> ... </Animated.View>
flatListRef.current?.scrollToEnd({ animated: true });

// AI streaming — append tokens to state; bubble grows with content (no layout jump).
// Tail caret blink while streaming:
const caret = useSharedValue(1);
useEffect(() => {
  if (streaming) caret.value = withRepeat(withTiming(0, { duration: 500 }), -1, true);
}, [streaming]);

// Typing indicator — per-dot withRepeat(withTiming(1, 600), -1, true), 160ms stagger (see TypingIndicator)

// Greeting entrance — opacity + scale 0.92→1.0 over 260ms (see GreetingHeader)

// Send ↔ Stop — swap icon + bg; 150ms (Pressable re-render)

// Haptics — NONE while streaming (continuous buzz)
import * as Haptics from 'expo-haptics';
Haptics.selectionAsync();                                  // send tap, tab switch
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);    // long-press bubble actions
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Discover (tab) | `sparkles-outline` |
| Chats (tab) | `chatbubbles-outline` |
| Create (tab) | `add-circle-outline` |
| Profile (tab) | `person-outline` |
| Send | `arrow-up` |
| Stop generation | `stop` |
| Back | `chevron-back` |
| Overflow / chat menu | `ellipsis-vertical` |
| Chat-count (card) | `chatbubbles-outline` |
| New chat | `create-outline` |
| Retry message | `refresh` |
| Copy message | `copy-outline` |
| Rate up / down | `thumbs-up-outline` / `thumbs-down-outline` |
| Search | `search` |
| Settings | `settings-outline` |

## 7. Platform Notes

- **Dark-first, light available**: swap canvas/surface/text + bubble greys via `useColorScheme()`; keep `accent`, `lilac`, and the avatar gradients **identical** across modes. Never let the accent become a bubble fill in either theme
- **Fonts**: Sora + Inter (incl. Inter-Italic) are SIL OFL — free to bundle. Sora for identity, Inter for message reading
- **Roleplay parsing**: split message text on `*`; odd segments get `typography.roleplay` (italic + secondary). This is a load-bearing signature — keep it even in plain-text contexts
- **Avatar gradient**: `expo-linear-gradient` is linear; for the radial look, layer a `LinearGradient` under a soft `RadialGradient` (via `react-native-svg` `RadialGradient`) or accept the diagonal approximation — the warm focal point matters more than exact radial math
- **Status bar**: `<StatusBar style="light" />` on dark, `"dark"` on light
- **Safe area**: wrap screens in `SafeAreaView`; the composer must rise above the keyboard (`KeyboardAvoidingView`); the message list scrolls clear of the composer; the greeting scrolls out cleanly
- **Streaming**: append tokens to a state string and let the bubble re-measure; avoid remounting the bubble (causes flicker); a blinking caret at the tail signals "generating"
- **Dynamic Type**: set `allowFontScaling={false}` on tab labels and "by @creator · N chats" meta; allow scaling on message text/names/body
- **Accessibility**: give AI bubbles an `accessibilityLabel` that distinguishes speech vs `*action*` (e.g., "Aria said … ; action: …"); label the typing indicator "Aria is typing"; the send button "Send"/"Stop generating"; never rely on blue-vs-lilac avatar alone to identify a character — the name label must carry it
- **No streaming haptics**: only fire haptics on send and discrete actions, never during token streaming
