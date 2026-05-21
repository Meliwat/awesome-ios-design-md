# Grok (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grok's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#000000',
  surface1: '#16181C',
  surface2: '#1E2126',
  surface3: '#272A2E',
  divider:  '#2F3336',

  textPrimary:   '#E7E9EA',
  textSecondary: '#71767B',
  textTertiary:  '#4D5156',

  accentWhite:  '#FFFFFF',
  pressedWhite: '#D7DBDC',
  linkBlue:     '#1D9BF0',
  linkPressed:  '#1A8CD8',
  success:      '#00BA7C',
  error:        '#F4212E',
} as const;

export type GrokColor = keyof typeof colors;
```

## 2. Typography

Load Inter via `expo-font` and enable the slashed zero through `fontVariant`.

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
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const slashedZero = { fontVariant: ['slashed-zero'] as TextStyle['fontVariant'] };
const base = { color: '#E7E9EA' } satisfies TextStyle;

export const typography = {
  screenTitle:  { ...base, fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.4 },
  sectionHead:  { ...base, fontFamily: 'Inter-Bold',     fontSize: 20, lineHeight: 25, letterSpacing: -0.3 },
  convoTitle:   { ...base, fontFamily: 'Inter-SemiBold', fontSize: 17, lineHeight: 22, letterSpacing: -0.2 },
  body:         { ...base, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 25 },
  userMessage:  { ...base, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 23 },
  promptInput:  { ...base, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 22 },
  modePill:     {           fontFamily: 'Inter-SemiBold', fontSize: 14 },
  citeAuthor:   { ...base, fontFamily: 'Inter-Bold',     fontSize: 14, letterSpacing: -0.1 },
  citeMeta:     {           fontFamily: 'Inter-Regular',  fontSize: 13, color: '#71767B', ...slashedZero },
  bodySmall:    { ...base, fontFamily: 'Inter-Regular',  fontSize: 14, lineHeight: 21 },
  code:         { ...base, fontFamily: 'Menlo',           fontSize: 13.5, lineHeight: 20, ...slashedZero },
  button:       { ...base, fontFamily: 'Inter-SemiBold', fontSize: 15 },
  caption:      {           fontFamily: 'Inter-Regular',  fontSize: 12, color: '#71767B' },
  labelUpper:   {           fontFamily: 'Inter-Bold',     fontSize: 11, letterSpacing: 0.6, color: '#71767B', textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### User Message Bubble

```tsx
// components/UserBubble.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function UserBubble({ text }: { text: string }) {
  return (
    <View style={{ alignItems: 'flex-end', paddingHorizontal: 16, marginVertical: 6 }}>
      <View
        style={{
          maxWidth: '78%',
          backgroundColor: colors.surface1,
          paddingVertical: 12,
          paddingHorizontal: 16,
          borderTopLeftRadius: 20,
          borderTopRightRadius: 20,
          borderBottomLeftRadius: 20,
          borderBottomRightRadius: 6, // tail toward sender
        }}
      >
        <Text style={typography.userMessage}>{text}</Text>
      </View>
    </View>
  );
}
```

### Assistant Response with Streaming Cursor

```tsx
// components/AssistantResponse.tsx
import { useEffect, useState } from 'react';
import { View, Text, Image } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AssistantResponse({ text, streaming }: { text: string; streaming: boolean }) {
  const [cursor, setCursor] = useState(true);

  useEffect(() => {
    if (!streaming) { setCursor(false); return; }
    const id = setInterval(() => setCursor((c) => !c), 530);
    return () => clearInterval(id);
  }, [streaming]);

  return (
    <View style={{ paddingHorizontal: 16, marginVertical: 6 }}>
      <Image source={require('../assets/grok-glyph.png')} style={{ width: 24, height: 24, marginBottom: 12 }} />
      <Text style={typography.body}>
        {text}
        {streaming && (
          <Text style={{ color: cursor ? colors.textPrimary : 'transparent' }}>▍</Text>
        )}
      </Text>
    </View>
  );
}
```

### X Post Citation Card (signature)

```tsx
// components/XCitationCard.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function XCitationCard({
  author, handle, timeAgo, verified, postText, replies, reposts, likes, onOpen,
}: {
  author: string; handle: string; timeAgo: string; verified: boolean;
  postText: string; replies: number; reposts: number; likes: number; onOpen: () => void;
}) {
  return (
    <Pressable
      onPress={onOpen}
      style={({ pressed }) => ({
        marginHorizontal: 16, marginVertical: 8, padding: 14, borderRadius: 16,
        backgroundColor: pressed ? colors.surface3 : colors.surface2,
        borderWidth: 1, borderColor: pressed ? colors.linkBlue : colors.divider,
      })}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <View style={{ width: 28, height: 28, borderRadius: 14, backgroundColor: colors.surface3 }} />
        <Text style={typography.citeAuthor}>{author}</Text>
        {verified && <Ionicons name="checkmark-circle" size={14} color={colors.linkBlue} />}
        <Text style={typography.citeMeta}>@{handle} · {timeAgo}</Text>
        <View style={{ flex: 1 }} />
        <Ionicons name="logo-twitter" size={16} color={colors.textSecondary} />
      </View>
      <Text style={[typography.bodySmall, { marginTop: 10 }]} numberOfLines={4}>{postText}</Text>
      <View style={{ flexDirection: 'row', gap: 20, marginTop: 10 }}>
        <Metric icon="chatbubble-outline" n={replies} />
        <Metric icon="repeat-outline" n={reposts} />
        <Metric icon="heart-outline" n={likes} />
      </View>
    </Pressable>
  );
}

function Metric({ icon, n }: { icon: any; n: number }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
      <Ionicons name={icon} size={14} color={colors.textSecondary} />
      <Text style={typography.caption}>{n}</Text>
    </View>
  );
}
```

### Send Button (state machine)

```tsx
// components/SendButton.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

type State = 'disabled' | 'enabled' | 'streaming';

export function SendButton({ state, onPress }: { state: State; onPress: () => void }) {
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const bg =
    state === 'enabled' ? colors.accentWhite : colors.surface3;
  const icon = state === 'streaming' ? 'stop' : 'arrow-up';
  const iconColor = state === 'enabled' ? '#000' : colors.textSecondary;

  return (
    <Pressable
      disabled={state === 'disabled'}
      onPressIn={() => (scale.value = withSpring(0.92, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
    >
      <Animated.View
        style={[
          { width: 32, height: 32, borderRadius: 16, backgroundColor: bg,
            alignItems: 'center', justifyContent: 'center' },
          aStyle,
        ]}
      >
        <Ionicons name={icon as any} size={16} color={state === 'streaming' ? colors.textPrimary : iconColor} />
      </Animated.View>
    </Pressable>
  );
}
```

### Mode Toggle (Regular / Fun)

```tsx
// components/ModeToggle.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ModeToggle({ isFun, onChange }: { isFun: boolean; onChange: (v: boolean) => void }) {
  const seg = (label: string, active: boolean, fun: boolean) => (
    <Pressable
      onPress={() => { Haptics.selectionAsync(); onChange(fun); }}
      style={{
        paddingVertical: 7, paddingHorizontal: 14, borderRadius: 999,
        backgroundColor: active ? (fun ? colors.surface1 : colors.accentWhite) : 'transparent',
      }}
    >
      <Text style={[typography.modePill, {
        color: active ? (fun ? colors.linkBlue : '#000') : colors.textSecondary,
      }]}>{label}</Text>
    </Pressable>
  );
  return (
    <View style={{
      flexDirection: 'row', padding: 3, borderRadius: 999,
      backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.divider,
    }}>
      {seg('Regular', !isFun, false)}
      {seg('Fun', isFun, true)}
    </View>
  );
}
```

### Prompt Bar

```tsx
// components/PromptBar.tsx
import { useState } from 'react';
import { View, TextInput } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { SendButton } from './SendButton';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PromptBar({ value, onChange, sendState, onSend }: {
  value: string; onChange: (t: string) => void;
  sendState: 'disabled' | 'enabled' | 'streaming'; onSend: () => void;
}) {
  const [focused, setFocused] = useState(false);
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-end', gap: 8,
      marginHorizontal: 16, paddingHorizontal: 12, paddingVertical: 8,
      borderRadius: 24, backgroundColor: colors.surface1,
      borderWidth: 1, borderColor: focused ? '#3E4146' : colors.divider,
    }}>
      <Ionicons name="attach" size={18} color={colors.textSecondary} />
      <TextInput
        value={value}
        onChangeText={onChange}
        placeholder="Ask Grok anything"
        placeholderTextColor={colors.textSecondary}
        multiline
        onFocus={() => setFocused(true)}
        onBlur={() => setFocused(false)}
        style={[typography.promptInput, { flex: 1, maxHeight: 120, paddingTop: 4 }]}
      />
      <SendButton state={sendState} onPress={onSend} />
    </View>
  );
}
```

## 4. Navigation (no tab bar)

Grok is single-surface — use a `Stack`, not `Tabs`. The top bar hosts the Mode Toggle centered.

```tsx
// app/index.tsx
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { useState } from 'react';
import { ModeToggle } from '../components/ModeToggle';
import { colors } from '../theme/colors';

export default function Conversation() {
  const [isFun, setIsFun] = useState(false);
  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas }}>
      <View style={{
        height: 44, flexDirection: 'row', alignItems: 'center',
        justifyContent: 'space-between', paddingHorizontal: 16,
      }}>
        <Ionicons name="menu" size={20} color={colors.textPrimary} />
        <ModeToggle isFun={isFun} onChange={setIsFun} />
        <Ionicons name="create-outline" size={20} color={colors.textPrimary} />
      </View>
      {/* ScrollView of UserBubble / AssistantResponse / XCitationCard, PromptBar pinned bottom */}
    </View>
  );
}
```

## 5. Motion

```tsx
// Streaming cursor — toggle a boolean every 530ms (see AssistantResponse)

// Send → Stop: crossfade two Ionicons with Animated opacity over 200ms

// Mode toggle slide
import { LayoutAnimation } from 'react-native';
LayoutAnimation.configureNext(LayoutAnimation.create(200, 'easeInEaseOut', 'opacity'));

// Copy confirm — swap icon to checkmark, animate tint to colors.success, revert after 1200ms

// "Searching X…" pulse
const pulse = useSharedValue(1);
pulse.value = withRepeat(withTiming(0.4, { duration: 900 }), -1, true);
```

Haptics via `expo-haptics`: `impactAsync(Soft)` on send, `selectionAsync()` on mode toggle, `notificationAsync(Success)` on copy-confirmed.

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Grok's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Send | `arrow.up` | `arrow-up` |
| Stop generation | `stop.fill` | `stop` |
| Attach | `paperclip` | `attach` |
| New chat | `square.and.pencil` | `create-outline` |
| History / menu | `line.3.horizontal` | `menu` |
| Verified (citation) | `checkmark.seal.fill` | `checkmark-circle` |
| Reply | `bubble.left` | `chatbubble-outline` |
| Repost | `arrow.2.squarepath` | `repeat-outline` |
| Like | `heart` | `heart-outline` |
| Copy | `doc.on.doc` | `copy-outline` → `checkmark` |
| Regenerate | `arrow.clockwise` | `refresh` |
| Share | `square.and.arrow.up` | `share-outline` |
| Searching X | `globe` | `globe-outline` |

## 7. Platform Notes

- **True black**: set the canvas to `#000000` everywhere; `<StatusBar style="light" />` from `expo-status-bar` globally
- **No tab bar**: do not use `expo-router` Tabs — Grok is single-surface; history is a modal/slide-over (`presentation: 'transparentModal'`)
- **Slashed zero**: `fontVariant: ['slashed-zero']` works on iOS for numerals; on Android it is a no-op, so also ship an Inter build with the `zero` feature baked in for parity
- **Safe area**: wrap in `SafeAreaView` from `react-native-safe-area-context`; the prompt bar uses `useSafeAreaInsets().bottom` padding and rises with `KeyboardAvoidingView`
- **Dynamic Type**: RN honors font scaling by default — keep it on body/user/title text; set `allowFontScaling={false}` on the mode-pill label, Send glyph, and citation meta
- **Accessibility**: `accessibilityRole="button"` + state-aware `accessibilityLabel` on Send ("Send message" / "Stop generating"); group citation card content with `accessibilityRole="link"`; on Reduce Motion, render the full response at once and hide the blinking cursor
- **Dark-only**: do not branch on `useColorScheme()` — lock to the dark token set
