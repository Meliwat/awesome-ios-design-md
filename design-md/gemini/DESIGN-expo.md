# Google Gemini (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Gemini's visual language into paste-ready Expo / React Native code: a design-token module, the gradient, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Light
  canvas:  '#FFFFFF',
  surface: '#F0F4F9',
  divider: '#E3E3E3',
  textPrimary:   '#1F1F1F',
  textSecondary: '#5F6368',
  textTertiary:  '#9AA0A6',

  // Dark
  darkCanvas:  '#1E1E1E',
  darkSurface: '#282A2C',
  darkDivider: '#3C3C3C',
  darkTextPrimary:   '#E3E3E3',
  darkTextSecondary: '#9AA0A6',

  // Brand
  blue:        '#4285F4',
  bluePressed: '#3367D6',
  darkBlue:    '#8AB4F8',
  violet:      '#9B72CB',
  coral:       '#D96570',

  success: '#1E8E3E',
  warning: '#F9AB00',
  error:   '#D93025',
} as const;

// The single brand gesture
export const GEMINI_GRADIENT = ['#4285F4', '#9B72CB', '#D96570'] as const;

export type GeminiColor = keyof typeof colors;
```

## 2. Typography

Google Sans is Google's product typeface. Load it via `expo-font`, or use **Inter** (closest free substitute). Use Roboto Mono / SpaceMono for code.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'GS-Regular': require('../assets/fonts/GoogleSansText-Regular.ttf'),
    'GS-Medium':  require('../assets/fonts/GoogleSans-Medium.ttf'),
    'GS-Bold':    require('../assets/fonts/GoogleSans-Bold.ttf'),
    'GS-Mono':    require('../assets/fonts/RobotoMono-Regular.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#1F1F1F' } satisfies TextStyle;

export const typography = {
  greeting:    { ...ink, fontFamily: 'GS-Medium',  fontSize: 28, lineHeight: 34, letterSpacing: -0.2 },
  title:       { ...ink, fontFamily: 'GS-Medium',  fontSize: 22, lineHeight: 28, letterSpacing: -0.1 },
  section:     { ...ink, fontFamily: 'GS-Medium',  fontSize: 18, lineHeight: 23 },
  answerH2:    { ...ink, fontFamily: 'GS-Medium',  fontSize: 20, lineHeight: 26 },
  answerH3:    { ...ink, fontFamily: 'GS-Medium',  fontSize: 17, lineHeight: 23 },
  answerBody:  { ...ink, fontFamily: 'GS-Regular', fontSize: 16, lineHeight: 25 }, // ≈ 1.55
  userTurn:    { ...ink, fontFamily: 'GS-Regular', fontSize: 16, lineHeight: 23 },
  promptInput: { ...ink, fontFamily: 'GS-Regular', fontSize: 16, lineHeight: 22 },
  chip:        { ...ink, fontFamily: 'GS-Medium',  fontSize: 14, lineHeight: 18 },
  meta:        {          fontFamily: 'GS-Regular', fontSize: 13, lineHeight: 17, color: '#5F6368' },
  code:        { ...ink, fontFamily: 'GS-Mono',    fontSize: 14, lineHeight: 21 },
  labelUpper:  {          fontFamily: 'GS-Medium',  fontSize: 11, lineHeight: 13, letterSpacing: 0.6, color: '#5F6368', textTransform: 'uppercase' as const },
  button:      {          fontFamily: 'GS-Medium',  fontSize: 15, lineHeight: 19, color: '#4285F4' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Gradient Sparkle

```tsx
// components/Sparkle.tsx — gradient-masked ✦ via expo-linear-gradient + MaskedView
import MaskedView from '@react-native-masked-view/masked-view';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { GEMINI_GRADIENT } from '../theme/colors';

export function Sparkle({ size = 20 }: { size?: number }) {
  return (
    <MaskedView
      style={{ width: size, height: size }}
      maskElement={<Ionicons name="sparkles" size={size} color="#000" />}
    >
      <LinearGradient colors={GEMINI_GRADIENT as unknown as string[]}
        start={{ x: 0, y: 0.5 }} end={{ x: 1, y: 0.5 }} style={{ flex: 1 }} />
    </MaskedView>
  );
}
```

### User Turn Chip

```tsx
// components/UserTurn.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function UserTurn({ text }: { text: string }) {
  return (
    <View style={{ paddingHorizontal: 16, alignItems: 'flex-end' }}>
      <View style={{
        maxWidth: '80%',
        backgroundColor: colors.surface,
        paddingVertical: 12, paddingHorizontal: 16,
        borderTopLeftRadius: 20, borderTopRightRadius: 20,
        borderBottomLeftRadius: 20, borderBottomRightRadius: 4,
      }}>
        <Text style={typography.userTurn}>{text}</Text>
      </View>
    </View>
  );
}
```

### Assistant Turn (plain text, no bubble)

```tsx
// components/AssistantTurn.tsx
import { Text, View, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { Sparkle } from './Sparkle';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AssistantTurn({
  text, streaming,
}: { text: string; streaming: boolean }) {
  return (
    <View style={{ paddingHorizontal: 16, gap: 12 }}>
      <View style={{ flexDirection: 'row', gap: 10 }}>
        <Sparkle size={20} />
        <Text style={[typography.answerBody, { flex: 1 }]} selectable>{text}</Text>
      </View>
      {!streaming && (
        <View style={{ flexDirection: 'row', gap: 20, paddingLeft: 30 }}>
          {['copy-outline', 'refresh', 'share-outline', 'ellipsis-horizontal'].map((n) => (
            <Pressable key={n} hitSlop={12}>
              <Ionicons name={n as any} size={18} color={colors.textSecondary} />
            </Pressable>
          ))}
        </View>
      )}
    </View>
  );
}
```

### Streaming Shimmer + Thinking

```tsx
// components/Streaming.tsx
import { Text, View } from 'react-native';
import { useEffect } from 'react';
import Animated, {
  useSharedValue, useAnimatedStyle, withRepeat, withTiming, interpolate,
} from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import { GEMINI_GRADIENT } from '../theme/colors';
import { typography } from '../theme/typography';

export function StreamingText({ text }: { text: string }) {
  const p = useSharedValue(0);
  useEffect(() => { p.value = withRepeat(withTiming(1, { duration: 1100 }), -1, false); }, []);
  const sweep = useAnimatedStyle(() => ({
    transform: [{ translateX: interpolate(p.value, [0, 1], [-120, 320]) }],
  }));
  return (
    <View>
      <Text style={typography.answerBody}>{text}</Text>
      <Animated.View style={[{ position: 'absolute', top: 0, bottom: 0, width: 120, opacity: 0.28 }, sweep]}
        pointerEvents="none">
        <LinearGradient colors={['transparent', GEMINI_GRADIENT[1], 'transparent']}
          start={{ x: 0, y: 0.5 }} end={{ x: 1, y: 0.5 }} style={{ flex: 1 }} />
      </Animated.View>
    </View>
  );
}

export function Thinking() {
  return (
    <View style={{ flexDirection: 'row', gap: 6 }}>
      {[0, 1, 2].map((i) => <Dot key={i} delay={i * 150} />)}
    </View>
  );
}
function Dot({ delay }: { delay: number }) {
  const s = useSharedValue(0.5);
  useEffect(() => {
    s.value = withRepeat(withTiming(1, { duration: 500 }), -1, true);
  }, []);
  const st = useAnimatedStyle(() => ({ transform: [{ scale: s.value }] }));
  return (
    <Animated.View style={[{ width: 7, height: 7, borderRadius: 4 }, st]}>
      <LinearGradient colors={GEMINI_GRADIENT as unknown as string[]} style={{ flex: 1, borderRadius: 4 }} />
    </Animated.View>
  );
}
```

### Prompt Bar (gradient focus ring)

```tsx
// components/PromptBar.tsx
import { Pressable, TextInput, View } from 'react-native';
import { useState } from 'react';
import { LinearGradient } from 'expo-linear-gradient';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, GEMINI_GRADIENT } from '../theme/colors';
import { typography } from '../theme/typography';

export function PromptBar({
  value, onChangeText, onSend, onStop, streaming = false,
}: { value: string; onChangeText: (s: string) => void; onSend: () => void; onStop: () => void; streaming?: boolean }) {
  const [focused, setFocused] = useState(false);
  const active = value.length > 0;

  const Bar = (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10,
      minHeight: 52, paddingHorizontal: 14, paddingVertical: 8,
      borderRadius: 26, backgroundColor: colors.surface, margin: focused ? 1.5 : 0,
    }}>
      <Pressable hitSlop={8}><Ionicons name="add" size={22} color={colors.textSecondary} /></Pressable>
      <TextInput
        style={[typography.promptInput, { flex: 1, maxHeight: 120 }]}
        placeholder="Ask Gemini" placeholderTextColor={colors.textSecondary}
        value={value} onChangeText={onChangeText} multiline
        onFocus={() => setFocused(true)} onBlur={() => setFocused(false)}
      />
      <Pressable hitSlop={8}><Ionicons name="mic" size={22} color={colors.textSecondary} /></Pressable>
      {streaming ? (
        <Pressable onPress={onStop} style={{ width: 36, height: 36, borderRadius: 18, backgroundColor: colors.blue, alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="stop" size={14} color="#FFF" />
        </Pressable>
      ) : (
        <Pressable
          disabled={!active}
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onSend(); }}
          style={{ width: 36, height: 36, borderRadius: 18, backgroundColor: active ? colors.blue : 'transparent', alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="arrow-up" size={18} color={active ? '#FFF' : colors.textTertiary} />
        </Pressable>
      )}
    </View>
  );

  return (
    <View style={{ paddingHorizontal: 16 }}>
      {focused ? (
        <LinearGradient colors={GEMINI_GRADIENT as unknown as string[]}
          start={{ x: 0, y: 0.5 }} end={{ x: 1, y: 0.5 }} style={{ borderRadius: 27.5 }}>
          {Bar}
        </LinearGradient>
      ) : Bar}
    </View>
  );
}
```

### Suggestion Chip

```tsx
// components/SuggestionChip.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Sparkle } from './Sparkle';
import { colors, GEMINI_GRADIENT } from '../theme/colors';
import { typography } from '../theme/typography';

export function SuggestionChip({
  label, featured = false, onPress,
}: { label: string; featured?: boolean; onPress: () => void }) {
  const inner = (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 6,
      paddingVertical: 10, paddingHorizontal: 16, borderRadius: 18,
      backgroundColor: colors.surface, margin: featured ? 1 : 0,
    }}>
      {featured && <Sparkle size={12} />}
      <Text style={typography.chip}>{label}</Text>
    </View>
  );
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.98 : 1 }] })}>
      {featured ? (
        <LinearGradient colors={GEMINI_GRADIENT as unknown as string[]}
          start={{ x: 0, y: 0.5 }} end={{ x: 1, y: 0.5 }} style={{ borderRadius: 19 }}>{inner}</LinearGradient>
      ) : (
        <View style={{ borderRadius: 18, borderWidth: 1, borderColor: colors.divider }}>{inner}</View>
      )}
    </Pressable>
  );
}
```

## 4. Navigation (no tab bar — drawer + top bar)

Use `expo-router` with a Drawer (no `Tabs`). The conversation is a single screen; the drawer is the sole primary nav.

```tsx
// app/_layout.tsx
import { Drawer } from 'expo-router/drawer';
import { colors } from '../theme/colors';

export default function Layout() {
  return (
    <Drawer
      screenOptions={{
        drawerType: 'front',
        drawerStyle: { width: '82%', backgroundColor: colors.canvas },
        overlayColor: 'rgba(0,0,0,0.40)',
        headerShadowVisible: false,
        headerTitleAlign: 'center',
        headerTitle: 'Gemini',
      }}
      drawerContent={(props) => <GemDrawerContent {...props} />}
    />
  );
}
```

## 5. Side Drawer Content

```tsx
import { ScrollView, Text, Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function GemDrawerContent({ recents }: { recents: { id: string; title: string; active: boolean }[] }) {
  return (
    <ScrollView contentContainerStyle={{ padding: 16, gap: 8 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, backgroundColor: colors.surface, borderRadius: 500, paddingVertical: 12, paddingHorizontal: 16 }}>
        <Ionicons name="add" size={20} color={colors.textPrimary} />
        <Text style={[typography.button, { color: colors.textPrimary }]}>New chat</Text>
      </View>
      <Text style={[typography.labelUpper, { marginTop: 16, marginLeft: 8 }]}>Today</Text>
      {recents.map((r) => (
        <Pressable key={r.id} style={{
          flexDirection: 'row', alignItems: 'center', gap: 12,
          paddingVertical: 12, paddingHorizontal: 16, borderRadius: 500,
          backgroundColor: r.active ? colors.surface : 'transparent',
        }}>
          <Ionicons name="chatbubble-outline" size={18} color={colors.textSecondary} />
          <Text style={typography.userTurn} numberOfLines={1}>{r.title}</Text>
        </Pressable>
      ))}
    </ScrollView>
  );
}
```

## 6. Motion

```tsx
// Streaming sweep — StreamingText (low-opacity gradient translateX loop)
// Thinking dots — Thinking (gradient scale pulse before first token)
// Send ⇄ Stop — swap component; wrap in LayoutAnimation or Reanimated layout transition
// Prompt focus ring — toggle gradient wrapper; animate margin/opacity with withTiming(180)
// Drawer — handled by expo-router Drawer (front type, scrim fade)
// Copy toast — Reanimated entering={SlideInDown} exiting={FadeOut}, 1.5s
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons) + `@react-native-masked-view/masked-view` for the gradient sparkle. Map to Gemini's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Sparkle (brand) | `sparkles` (gradient-masked) |
| Send | `arrow-up` |
| Stop streaming | `stop` |
| Mic | `mic` |
| Add content | `add` |
| Copy | `copy-outline` |
| Regenerate | `refresh` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Drawer toggle | `menu` |
| Model chevron | `chevron-down` |
| New chat | `add` / `create-outline` |
| Search (drawer) | `search` |
| Conversation row | `chatbubble-outline` |
| Account | `person-circle-outline` |

## 8. Platform Notes

- **No tab bar**: use `expo-router/drawer` (front type) — the side drawer is the only primary nav
- **Status bar**: set `<StatusBar style="dark" />` in light, `light` in dark — drive from `useColorScheme()`
- **Safe area**: wrap with `SafeAreaView`; the prompt bar uses `KeyboardAvoidingView` (iOS `padding`) so it floats above the keyboard with ~12pt clearance
- **Gradient sparkle**: requires `@react-native-masked-view/masked-view`; on Expo Go it works via the JS masked view — verify on device
- **Dynamic Type**: React Native honors font scaling; set `allowFontScaling={false}` on the prompt-bar base height and drawer day labels
- **Light + dark parity**: resolve `colors.canvas` vs `colors.darkCanvas` via `useColorScheme()`; keep `GEMINI_GRADIENT` identical across themes
- **Accessibility**: mark the `Sparkle` as `accessibilityElementsHidden`; give the send button `accessibilityRole="button"` + label "Send" / "Stop generating"; announce streaming completion via `AccessibilityInfo.announceForAccessibility`
