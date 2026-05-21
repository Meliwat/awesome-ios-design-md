# iMessage (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates iMessage's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font` (optional — iMessage uses the system face), `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand bubble colors (identical light & dark)
  imsgBlue:        '#007AFF',
  imsgBluePressed: '#0062CC',
  imsgGreen:       '#34C759',
  imsgGreenPressed:'#248A3D',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  incoming:      '#E9E9EB',
  groupedBg:     '#F2F2F7',
  fieldBorder:   '#C6C6C8',

  // Surfaces (dark)
  darkCanvas:    '#000000',
  incomingDark:  '#26262A',
  darkSurface1:  '#1C1C1E',
  darkSurface2:  '#2C2C2E',
  darkBorder:    '#38383A',

  // Text
  textPrimary:   '#000000',
  textPrimaryDark:'#FFFFFF',
  onAccent:      '#FFFFFF',
  textSecondary: 'rgba(60,60,67,0.6)',
  textSecondaryDark: 'rgba(235,235,245,0.6)',
  textTertiary:  'rgba(60,60,67,0.3)',
  textTertiaryDark:  'rgba(235,235,245,0.3)',

  // Semantic
  red:     '#FF3B30',
  redDark: '#FF453A',
  link:    '#007AFF',
  linkDark:'#2997FF',
} as const;

export type IMsgColor = keyof typeof colors;

export const bubbleStyle = (kind: 'incoming' | 'imessage' | 'sms', dark: boolean) => {
  if (kind === 'imessage') return { bg: colors.imsgBlue,  fg: colors.onAccent };
  if (kind === 'sms')      return { bg: colors.imsgGreen, fg: colors.onAccent };
  return { bg: dark ? colors.incomingDark : colors.incoming, fg: dark ? colors.textPrimaryDark : colors.textPrimary };
};
```

## 2. Typography

iMessage uses the platform San Francisco face. On iOS, `fontFamily: 'System'` (or omitting it) yields SF Pro — do **not** bundle a font.

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  largeTitle: { fontFamily: 'System', fontSize: 34, fontWeight: '700', letterSpacing: 0.4 },
  title1:     { fontFamily: 'System', fontSize: 28, fontWeight: '700', letterSpacing: 0.36 },
  title3:     { fontFamily: 'System', fontSize: 20, fontWeight: '600', letterSpacing: 0.38 },
  headline:   { fontFamily: 'System', fontSize: 17, fontWeight: '600', letterSpacing: -0.4 },
  body:       { fontFamily: 'System', fontSize: 17, fontWeight: '400', letterSpacing: -0.4, lineHeight: 22 },
  callout:    { fontFamily: 'System', fontSize: 16, fontWeight: '400', letterSpacing: -0.3 },
  subhead:    { fontFamily: 'System', fontSize: 15, fontWeight: '400', letterSpacing: -0.2 },
  footnote:   { fontFamily: 'System', fontSize: 13, fontWeight: '600', letterSpacing: -0.08 },
  caption1:   { fontFamily: 'System', fontSize: 12, fontWeight: '400' },
  caption2:   { fontFamily: 'System', fontSize: 11, fontWeight: '600', letterSpacing: 0.06 },
  receipt:    { fontFamily: 'System', fontSize: 13, fontWeight: '600' }, // fixed (allowFontScaling=false)
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Message Bubble (in / out, with tail)

```tsx
// components/ChatBubble.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors, bubbleStyle } from '../theme/colors';
import { typography } from '../theme/typography';

type Kind = 'incoming' | 'imessage' | 'sms';

export function ChatBubble({ text, kind, tailEnd }: { text: string; kind: Kind; tailEnd: boolean }) {
  const dark = useColorScheme() === 'dark';
  const { bg, fg } = bubbleStyle(kind, dark);
  const outgoing = kind !== 'incoming';

  // 19pt rounded, pinched 6pt corner on sender's edge when tailEnd
  const radius = {
    borderTopLeftRadius: 19,
    borderTopRightRadius: 19,
    borderBottomLeftRadius: !outgoing && tailEnd ? 6 : 19,
    borderBottomRightRadius: outgoing && tailEnd ? 6 : 19,
  };

  return (
    <View style={{ width: '100%', alignItems: outgoing ? 'flex-end' : 'flex-start' }}>
      <View style={[{ maxWidth: '78%', backgroundColor: bg, paddingVertical: 8, paddingHorizontal: 14 }, radius]}>
        <Text style={[typography.body, { color: fg }]}>{text}</Text>
      </View>
    </View>
  );
}
```

### Tapback Reaction Strip

```tsx
// components/TapbackStrip.tsx
import { Pressable, Text, View, useColorScheme } from 'react-native';
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const GLYPHS = ['❤️', '👍', '👎', '😂', '‼️', '❓'];

export function TapbackStrip({ onPick }: { onPick: (g: string) => void }) {
  const dark = useColorScheme() === 'dark';
  return (
    <Animated.View
      entering={FadeIn.duration(180)}
      exiting={FadeOut.duration(150)}
      style={{
        flexDirection: 'row', gap: 10, paddingHorizontal: 14, paddingVertical: 8,
        backgroundColor: dark ? colors.darkSurface2 : colors.canvas,
        borderRadius: 24,
        borderWidth: dark ? 0.5 : 0, borderColor: colors.darkBorder,
        shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 20, shadowOffset: { width: 0, height: 6 },
        elevation: 6,
      }}
    >
      {GLYPHS.map((g) => (
        <Pressable
          key={g}
          hitSlop={8}
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPick(g); }}
          style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}
        >
          <Text style={{ fontSize: 28 }}>{g}</Text>
        </Pressable>
      ))}
    </Animated.View>
  );
}

// Docked chip over a bubble's owner-near top corner
export function TapbackChip({ glyph }: { glyph: string }) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{
      position: 'absolute', top: -16,
      backgroundColor: dark ? colors.darkSurface2 : colors.canvas,
      borderRadius: 14, padding: 6,
      borderWidth: 0.5, borderColor: dark ? colors.darkBorder : colors.fieldBorder,
    }}>
      <Text style={{ fontSize: 12 }}>{glyph}</Text>
    </View>
  );
}
```

### Typing Indicator

```tsx
// components/TypingIndicator.tsx
import { useEffect } from 'react';
import { View, useColorScheme } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';

function Dot({ delay }: { delay: number }) {
  const t = useSharedValue(0);
  useEffect(() => {
    t.value = withRepeat(withTiming(1, { duration: 1400, easing: Easing.inOut(Easing.ease) }), -1, false);
  }, []);
  const dark = useColorScheme() === 'dark';
  const style = useAnimatedStyle(() => {
    const p = (t.value + delay) % 1;
    const s = 0.6 + 0.4 * Math.sin(p * Math.PI);
    return { transform: [{ scale: s }] };
  });
  return <Animated.View style={[{ width: 7, height: 7, borderRadius: 4, backgroundColor: dark ? colors.textTertiaryDark : colors.textTertiary }, style]} />;
}

export function TypingIndicator() {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{
      alignSelf: 'flex-start', flexDirection: 'row', gap: 4,
      paddingVertical: 12, paddingHorizontal: 16,
      backgroundColor: dark ? colors.incomingDark : colors.incoming,
      borderTopLeftRadius: 19, borderTopRightRadius: 19, borderBottomRightRadius: 19, borderBottomLeftRadius: 6,
    }}>
      <Dot delay={0} /><Dot delay={0.2} /><Dot delay={0.4} />
    </View>
  );
}
```

### Delivery Receipt

```tsx
// components/DeliveryReceipt.tsx
import { Text, View, useColorScheme } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function DeliveryReceipt({ label }: { label: string }) {
  const dark = useColorScheme() === 'dark';
  return (
    <View style={{ alignItems: 'flex-end', paddingRight: 6, paddingTop: 2 }}>
      <Text allowFontScaling={false} style={[typography.receipt, { color: dark ? colors.textSecondaryDark : colors.textSecondary }]}>
        {label}
      </Text>
    </View>
  );
}
```

### Compose Bar

```tsx
// components/ComposeBar.tsx
import { useState } from 'react';
import { Pressable, TextInput, View, useColorScheme } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ComposeBar({ isIMessage, onSend }: { isIMessage: boolean; onSend: (t: string) => void }) {
  const [text, setText] = useState('');
  const dark = useColorScheme() === 'dark';
  const empty = text.trim().length === 0;

  return (
    <BlurView intensity={50} tint={dark ? 'dark' : 'light'} style={{ paddingHorizontal: 14, paddingVertical: 8 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <Pressable style={{ width: 30, height: 30, borderRadius: 15, alignItems: 'center', justifyContent: 'center', backgroundColor: dark ? colors.darkSurface2 : colors.incoming }}>
          <Ionicons name="add" size={16} color={dark ? colors.textSecondaryDark : colors.textSecondary} />
        </Pressable>
        <View style={{ flex: 1, minHeight: 36, borderRadius: 18, borderWidth: 1, borderColor: dark ? colors.darkBorder : colors.fieldBorder, justifyContent: 'center', paddingHorizontal: 14 }}>
          <TextInput
            value={text}
            onChangeText={setText}
            placeholder={isIMessage ? 'iMessage' : 'Text Message'}
            placeholderTextColor={dark ? colors.textTertiaryDark : colors.textTertiary}
            multiline
            style={[typography.body, { color: dark ? colors.textPrimaryDark : colors.textPrimary, maxHeight: 110 }]}
          />
        </View>
        <Pressable
          disabled={empty}
          onPress={() => { onSend(text); setText(''); }}
          style={({ pressed }) => ({
            width: 30, height: 30, borderRadius: 15, alignItems: 'center', justifyContent: 'center',
            backgroundColor: empty ? '#8E8E93' : (isIMessage ? colors.imsgBlue : colors.imsgGreen),
            transform: [{ scale: pressed ? 0.92 : 1 }],
          })}
        >
          <Ionicons name="arrow-up" size={15} color="#FFFFFF" />
        </Pressable>
      </View>
    </BlurView>
  );
}
```

### Chat Nav Title (centered avatar + name)

```tsx
// components/ChatNavTitle.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function ChatNavTitle({ name, initials }: { name: string; initials: string }) {
  return (
    <View style={{ alignItems: 'center', gap: 3 }}>
      <LinearGradient colors={['#5E5CE6', colors.imsgBlue]} style={{ width: 34, height: 34, borderRadius: 17, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#FFF', fontSize: 14, fontWeight: '600' }}>{initials}</Text>
      </LinearGradient>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
        <Text style={{ fontSize: 13, fontWeight: '600' }}>{name}</Text>
        <Ionicons name="chevron-down" size={9} color={colors.textSecondary} />
      </View>
    </View>
  );
}
```

## 4. Bottom Tab Bar

iMessage is effectively single-screen. If hosting Messages inside a tab navigator:

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.imsgBlue,   // systemBlue active, no pill
        tabBarInactiveTintColor: '#8E8E93',
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.fieldBorder },
        tabBarLabelStyle: { fontFamily: 'System', fontSize: 11, fontWeight: '500' },
      }}
    >
      <Tabs.Screen name="index" options={{ title: 'Messages', tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

The real navigation is the centered nav title (`<ChatNavTitle/>` via `headerTitle`) plus the blur-backed `<ComposeBar/>` — not a custom tab strip.

## 5. Motion

```tsx
// Outgoing bubble — spring up from input field
import Animated, { SlideInDown, ZoomIn } from 'react-native-reanimated';
// <Animated.View entering={SlideInDown.springify().damping(14)}>

// Tapback present — long-press ~500ms then scale-in
// <Pressable delayLongPress={500} onLongPress={...}>; strip uses FadeIn.duration(180)

// Typing dots — withRepeat sine loop (see TypingIndicator above), 1400ms

// Delivered → Read crossfade
// re-render with Animated FadeIn.duration(200) on the receipt text

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);  // send
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // tapback dock
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // message effect
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) — iMessage uses SF Symbols natively; these are the closest cross-platform equivalents.

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Back | `chevron.left` | `chevron-back` |
| Contact disclosure | `chevron.down` | `chevron-down` |
| FaceTime | `video` | `videocam` |
| Compose (list) | `square.and.pencil` | `create-outline` |
| Attachment | `plus` | `add` |
| Send | `arrow.up` | `arrow-up` |
| Search | `magnifyingglass` | `search` |
| Failed message | `exclamationmark.circle.fill` | `alert-circle` |
| Unread dot | `circle.fill` | `ellipse` |
| Pin | `pin.fill` | `pin` |
| Mute | `bell.slash.fill` | `notifications-off` |
| Delete | `trash.fill` | `trash` |
| Camera | `camera.fill` | `camera` |
| Photos | `photo.on.rectangle` | `images` |
| Audio message | `mic.fill` | `mic` |
| App strip | `circle.grid.3x3.fill` | `apps` |

## 7. Platform Notes

- **Font**: iMessage is pure system San Francisco — set `fontFamily: 'System'` (or omit) on iOS; do not bundle a TTF. On Android this maps to Roboto, which is acceptable for a cross-platform clone.
- **Blur bars**: use `expo-blur` `BlurView` for the nav and compose bars; fall back to a solid `darkSurface1`/`groupedBg` when `Reduce Transparency` is on.
- **Status bar**: `<StatusBar style="auto" />` — dark content on white, light on black.
- **Safe area**: wrap the screen in `SafeAreaView`; the compose bar needs bottom safe-area padding and must ride above the keyboard — wrap the thread in `KeyboardAvoidingView` (iOS `behavior="padding"`).
- **Dynamic Type**: leave `allowFontScaling` default (true) on bubbles, headline, list previews; set `allowFontScaling={false}` on receipts, timestamps, tab labels, and typing dots.
- **Dark mode**: use `useColorScheme()` and swap to `darkCanvas` (true black `#000000`, not charcoal), `incomingDark`, `darkSurface2`; add a 0.5pt `darkBorder` to floating popovers as the dark elevation cue.
- **Bubble grouping**: track sender runs in your message list; only pass `tailEnd` to the last bubble of a same-sender run; render a 3pt gap within a run and 9pt on sender change.
- **Reduce Motion**: gate `SlideInDown`/`ZoomIn` and message-effect playback behind `AccessibilityInfo.isReduceMotionEnabled()`; substitute a crossfade.
- **Accessibility**: set `accessibilityLabel` on bubbles ("You said…" / "{Name} said…"); expose Tapback via `accessibilityActions` so it doesn't require a long-press; announce delivery state as the bubble's `accessibilityValue`.
