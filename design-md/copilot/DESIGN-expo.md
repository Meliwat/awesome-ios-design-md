# Microsoft Copilot (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Copilot's visual language into paste-ready Expo / React Native code: a design-token module, the flourish gradient, acrylic surfaces via `expo-blur`, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Light
  canvas:  '#FFFFFF',
  surface: '#F3F3F3',
  divider: '#E0E0E0',
  textPrimary:   '#242424',
  textSecondary: '#616161',
  textTertiary:  '#919191',

  // Dark
  darkCanvas:  '#202020',
  darkSurface: '#2D2D2D',
  darkDivider: '#3A3A3A',
  darkTextPrimary:   '#FFFFFF',
  darkTextSecondary: '#A6A6A6',

  // Brand
  blue:        '#0078D4',
  bluePressed: '#005A9E',
  blueTint:    '#DEECF9',
  darkBlue:    '#4DA3E0',
  coral:       '#FF6F61',
  gold:        '#FFB900',

  success: '#107C10',
  warning: '#F7630C',
  error:   '#C50F1F',
} as const;

// The single warm brand gesture
export const FLOURISH_GRADIENT = ['#FF6F61', '#FFB900'] as const;

export type CopilotColor = keyof typeof colors;
```

## 2. Typography

Segoe UI is Microsoft's product typeface. Load it via `expo-font`, or use **Inter** (closest free humanist-grotesque). Use Cascadia Code / SpaceMono for code.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Segoe-Regular':  require('../assets/fonts/SegoeUI-Regular.ttf'),
    'Segoe-Semibold': require('../assets/fonts/SegoeUI-Semibold.ttf'),
    'Segoe-Bold':     require('../assets/fonts/SegoeUI-Bold.ttf'),
    'Segoe-Mono':     require('../assets/fonts/CascadiaCode-Regular.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const ink = { color: '#242424' } satisfies TextStyle;

export const typography = {
  greeting:    { ...ink, fontFamily: 'Segoe-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.2 },
  title:       { ...ink, fontFamily: 'Segoe-Bold',     fontSize: 22, lineHeight: 28, letterSpacing: -0.1 },
  section:     { ...ink, fontFamily: 'Segoe-Semibold', fontSize: 18, lineHeight: 23 },
  answerH2:    { ...ink, fontFamily: 'Segoe-Semibold', fontSize: 20, lineHeight: 26 },
  answerH3:    { ...ink, fontFamily: 'Segoe-Semibold', fontSize: 17, lineHeight: 23 },
  answerBody:  { ...ink, fontFamily: 'Segoe-Regular',  fontSize: 16, lineHeight: 24 }, // ≈ 1.5
  userTurn:    {          fontFamily: 'Segoe-Regular',  fontSize: 16, lineHeight: 23, color: '#FFFFFF' },
  promptInput: { ...ink, fontFamily: 'Segoe-Regular',  fontSize: 16, lineHeight: 22 },
  chip:        { ...ink, fontFamily: 'Segoe-Semibold', fontSize: 14, lineHeight: 18 },
  tone:        { ...ink, fontFamily: 'Segoe-Semibold', fontSize: 13, lineHeight: 16, letterSpacing: 0.1 },
  meta:        {          fontFamily: 'Segoe-Regular',  fontSize: 13, lineHeight: 17, color: '#616161' },
  code:        { ...ink, fontFamily: 'Segoe-Mono',     fontSize: 14, lineHeight: 21 },
  labelUpper:  {          fontFamily: 'Segoe-Semibold', fontSize: 11, lineHeight: 13, letterSpacing: 0.6, color: '#616161', textTransform: 'uppercase' as const },
  button:      {          fontFamily: 'Segoe-Semibold', fontSize: 15, lineHeight: 19, color: '#0078D4' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Acrylic Surface

```tsx
// components/Acrylic.tsx — Fluent acrylic ≈ expo-blur + faint tint + hairline + blur-in
import { useEffect } from 'react';
import { View, useColorScheme } from 'react-native';
import { BlurView } from 'expo-blur';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function Acrylic({
  children, radius = 16, appears = true,
}: { children: React.ReactNode; radius?: number; appears?: boolean }) {
  const dark = useColorScheme() === 'dark';
  const o = useSharedValue(appears ? 0 : 1);
  useEffect(() => { o.value = withTiming(1, { duration: 220 }); }, []);
  const st = useAnimatedStyle(() => ({ opacity: o.value }));

  return (
    <Animated.View style={[{ borderRadius: radius, overflow: 'hidden' }, st]}>
      <BlurView intensity={40} tint={dark ? 'dark' : 'light'} style={{ borderRadius: radius }}>
        <View style={{
          backgroundColor: (dark ? colors.darkSurface : colors.surface) + '80',
          borderRadius: radius,
          borderWidth: 1,
          borderColor: (dark ? colors.darkDivider : colors.divider) + '99',
        }}>
          {children}
        </View>
      </BlurView>
    </Animated.View>
  );
}
```

### Flourish Mark

```tsx
// components/Flourish.tsx — gradient-masked swirl glyph
import MaskedView from '@react-native-masked-view/masked-view';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { FLOURISH_GRADIENT } from '../theme/colors';

export function Flourish({ size = 22 }: { size?: number }) {
  return (
    <MaskedView
      style={{ width: size, height: size }}
      maskElement={<Ionicons name="sparkles" size={size} color="#000" />}
    >
      <LinearGradient colors={FLOURISH_GRADIENT as unknown as string[]}
        start={{ x: 0, y: 0.5 }} end={{ x: 1, y: 0.5 }} style={{ flex: 1 }} />
    </MaskedView>
  );
}
```

### Answer Card

```tsx
// components/AnswerCard.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { Acrylic } from './Acrylic';
import { Flourish } from './Flourish';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AnswerCard({
  text, streaming,
}: { text: string; streaming: boolean }) {
  return (
    <View style={{ paddingHorizontal: 16 }}>
      <Acrylic radius={16}>
        <View style={{ padding: 16, gap: 12 }}>
          <View style={{ flexDirection: 'row', gap: 10 }}>
            <Flourish size={22} />
            <Text style={[typography.answerBody, { flex: 1 }]} selectable>{text}</Text>
          </View>
          {!streaming && (
            <View style={{ flexDirection: 'row', gap: 20, paddingLeft: 32 }}>
              {['copy-outline', 'thumbs-up-outline', 'thumbs-down-outline',
                'refresh', 'share-outline', 'ellipsis-horizontal'].map((n) => (
                <Pressable key={n} hitSlop={12}>
                  <Ionicons name={n as any} size={18} color={colors.textSecondary} />
                </Pressable>
              ))}
            </View>
          )}
        </View>
      </Acrylic>
    </View>
  );
}
```

### User Turn

```tsx
// components/UserTurn.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function UserTurn({ text, neutral = false }: { text: string; neutral?: boolean }) {
  return (
    <View style={{ paddingHorizontal: 16, alignItems: 'flex-end' }}>
      <View style={{
        maxWidth: '80%',
        backgroundColor: neutral ? colors.surface : colors.blue,
        paddingVertical: 12, paddingHorizontal: 16,
        borderTopLeftRadius: 16, borderTopRightRadius: 16,
        borderBottomLeftRadius: 16, borderBottomRightRadius: 4,
      }}>
        <Text style={[typography.userTurn, neutral && { color: colors.textPrimary }]}>{text}</Text>
      </View>
    </View>
  );
}
```

### Tone Selector

```tsx
// components/ToneSelector.tsx
import { Pressable, Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ToneSelector({
  value, onChange,
}: { value: number; onChange: (i: number) => void }) {
  const TONES = ['Creative', 'Balanced', 'Precise'];
  return (
    <BlurView intensity={40} tint="light" style={{ borderRadius: 500, padding: 3, flexDirection: 'row', borderWidth: 1, borderColor: colors.divider + '99' }}>
      {TONES.map((t, i) => (
        <Pressable key={t} onPress={() => onChange(i)}
          style={{
            flex: 1, paddingVertical: 9, borderRadius: 500, alignItems: 'center',
            backgroundColor: value === i ? colors.blue : 'transparent',
          }}>
          <Text style={[typography.tone, { color: value === i ? '#FFF' : colors.textSecondary }]}>{t}</Text>
        </Pressable>
      ))}
    </BlurView>
  );
}
```

### Prompt Bar (acrylic, focus accent)

```tsx
// components/PromptBar.tsx
import { Pressable, TextInput, View, useColorScheme } from 'react-native';
import { useState } from 'react';
import { BlurView } from 'expo-blur';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PromptBar({
  value, onChangeText, onSend, onStop, streaming = false,
}: { value: string; onChangeText: (s: string) => void; onSend: () => void; onStop: () => void; streaming?: boolean }) {
  const dark = useColorScheme() === 'dark';
  const [focused, setFocused] = useState(false);
  const active = value.length > 0;

  return (
    <View style={{ paddingHorizontal: 16 }}>
      <BlurView intensity={40} tint={dark ? 'dark' : 'light'}
        style={{
          borderRadius: 24, overflow: 'hidden',
          borderWidth: focused ? 1.5 : 1,
          borderColor: focused ? colors.blue : colors.divider + '99',
        }}>
        <View style={{
          flexDirection: 'row', alignItems: 'center', gap: 10,
          minHeight: 52, paddingHorizontal: 14, paddingVertical: 8,
          backgroundColor: (dark ? colors.darkSurface : colors.surface) + '80',
        }}>
          <Pressable hitSlop={8}><Ionicons name="add" size={22} color={colors.textSecondary} /></Pressable>
          <TextInput
            style={[typography.promptInput, { flex: 1, maxHeight: 120 }]}
            placeholder="Message Copilot" placeholderTextColor={colors.textSecondary}
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
              <Ionicons name="arrow-up" size={18} color={active ? '#FFF' : colors.textSecondary} />
            </Pressable>
          )}
        </View>
      </BlurView>
    </View>
  );
}
```

### Suggestion Chip

```tsx
// components/SuggestionChip.tsx
import { Pressable, Text } from 'react-native';
import { BlurView } from 'expo-blur';
import { Flourish } from './Flourish';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SuggestionChip({
  label, featured = false, onPress,
}: { label: string; featured?: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.98 : 1 }] })}>
      <BlurView intensity={40} tint="light"
        style={{ borderRadius: 18, overflow: 'hidden', borderWidth: 1, borderColor: colors.divider + '99' }}>
        <Pressable onPress={onPress} style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingVertical: 10, paddingHorizontal: 16 }}>
          {featured && <Flourish size={12} />}
          <Text style={typography.chip}>{label}</Text>
        </Pressable>
      </BlurView>
    </Pressable>
  );
}
```

### Streaming Shimmer + Thinking

```tsx
import { Text, View } from 'react-native';
import { useEffect } from 'react';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, interpolate } from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import { FLOURISH_GRADIENT } from '../theme/colors';
import { typography } from '../theme/typography';

export function StreamingText({ text }: { text: string }) {
  const p = useSharedValue(0);
  useEffect(() => { p.value = withRepeat(withTiming(1, { duration: 1100 }), -1, false); }, []);
  const sweep = useAnimatedStyle(() => ({ transform: [{ translateX: interpolate(p.value, [0, 1], [-120, 320]) }] }));
  return (
    <View>
      <Text style={typography.answerBody}>{text}</Text>
      <Animated.View pointerEvents="none" style={[{ position: 'absolute', top: 0, bottom: 0, width: 120, opacity: 0.26 }, sweep]}>
        <LinearGradient colors={['transparent', FLOURISH_GRADIENT[1], FLOURISH_GRADIENT[0], 'transparent']}
          start={{ x: 0, y: 0.5 }} end={{ x: 1, y: 0.5 }} style={{ flex: 1 }} />
      </Animated.View>
    </View>
  );
}
```

## 4. Navigation (no tab bar — drawer + top bar)

Use `expo-router` with a Drawer (no `Tabs`). The conversation is a single screen; the sidebar is the sole primary nav.

```tsx
// app/_layout.tsx
import { Drawer } from 'expo-router/drawer';
import { colors } from '../theme/colors';

export default function Layout() {
  return (
    <Drawer
      screenOptions={{
        drawerType: 'front',
        drawerStyle: { width: '80%', backgroundColor: colors.canvas },
        overlayColor: 'rgba(0,0,0,0.40)',
        headerShadowVisible: false,
        headerTitleAlign: 'center',
        headerTitle: 'Copilot',
      }}
    />
  );
}
```

## 5. Motion

```tsx
// Acrylic blur-in — Acrylic ramps opacity over 220ms on mount
// Streaming sweep — StreamingText (warm low-opacity gradient translateX loop)
// Thinking dots — gradient scale pulse (mirror Gemini pattern with FLOURISH_GRADIENT)
// Send ⇄ Stop — swap component; Reanimated layout transition
// Tone pill — animate selected background with withTiming(180)
// Prompt focus accent — toggle border width/color
// Sidebar — handled by expo-router Drawer (front type, scrim fade)
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) + `@react-native-masked-view/masked-view` for the gradient flourish. Map to Copilot's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Flourish (brand) | `sparkles` (gradient-masked) |
| Send | `arrow-up` |
| Stop streaming | `stop` |
| Voice / Mic | `mic` |
| Add content | `add` |
| Copy | `copy-outline` |
| Like / Dislike | `thumbs-up-outline` / `thumbs-down-outline` |
| Regenerate | `refresh` |
| Share | `share-outline` |
| More | `ellipsis-horizontal` |
| Sidebar toggle | `menu` |
| Selector chevron | `chevron-down` |
| New chat | `add` / `create-outline` |
| Search (sidebar) | `search` |
| Conversation row | `chatbubble-outline` |
| Account | `person-circle-outline` |

## 7. Platform Notes

- **Acrylic = `expo-blur`**: `BlurView` gives the Fluent frosted layer on iOS; on Android the blur is approximate — fall back to a solid tinted surface there
- **No tab bar**: use `expo-router/drawer` (front type) — the sidebar is the only primary nav
- **Status bar**: set `<StatusBar style="dark" />` in light, `light` in dark — drive from `useColorScheme()`
- **Safe area**: wrap with `SafeAreaView`; the prompt bar uses `KeyboardAvoidingView` (iOS `padding`) so the acrylic bar floats above the keyboard with ~12pt clearance
- **Reduce Transparency**: check `AccessibilityInfo.isReduceTransparencyEnabled()` — if true, render `Acrylic` as a solid `colors.surface` so contrast holds
- **Dynamic Type**: React Native honors font scaling; set `allowFontScaling={false}` on the prompt-bar base height, sidebar day labels, and tone labels
- **Light + dark parity**: resolve surface/text via `useColorScheme()`; keep `FLOURISH_GRADIENT` identical across themes
- **Accessibility**: mark `Flourish` as `accessibilityElementsHidden`; the send button gets `accessibilityRole="button"` + label "Send" / "Stop generating"; expose the tone selector as a segmented `accessibilityRole="adjustable"`
