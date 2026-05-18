# DeepSeek (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates DeepSeek's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. DeepSeek's signature is the **recessed reasoning trace** with a blue left-bar and dimmed-italic chain-of-thought, subordinate to an upright answer.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const dark = {
  canvas:        '#0E0E10',
  surface1:      '#1A1B1E',
  surface2:      '#232428',
  surface3:      '#2C2D31',
  reasoningBg:   '#16171A',   // DARKER than canvas — recessed
  divider:       '#2E2F33',

  textPrimary:   '#ECECEC',
  textSecondary: '#9A9BA0',
  textTertiary:  '#6A6B70',
  reasoningText: '#8A8B90',   // dimmed italic chain-of-thought

  blue:          '#4D6BFE',   // the single accent
  bluePressed:   '#3B57E0',
  blueSoft:      '#1E2240',
  blueToken:     '#C5CDFF',

  success:       '#2EBD85',
  error:         '#E5484D',
} as const;

export const light = {
  canvas:        '#FFFFFF',
  surface1:      '#F5F6F8',
  surface2:      '#ECEEF2',
  surface3:      '#E4E6EB',
  reasoningBg:   '#F7F8FA',   // slightly off-white — recessed
  divider:       '#E4E6EB',

  textPrimary:   '#1A1B1E',
  textSecondary: '#6A6B70',
  textTertiary:  '#9A9BA0',
  reasoningText: '#6E6F74',

  blue:          '#4D6BFE',
  bluePressed:   '#3B57E0',
  blueSoft:      '#EBEFFF',
  blueToken:     '#3B57E0',

  success:       '#2EBD85',
  error:         '#E5484D',
} as const;

export type DSColors = typeof dark;
```

## 2. Typography

DeepSeek uses the system sans (Inter as the open fallback). The reasoning trace is the only italic in the system.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':       require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Italic':        require('../assets/fonts/Inter-Italic.ttf'),
    'Inter-Medium':        require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':      require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':          require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':     require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import { Platform, type TextStyle } from 'react-native';

const mono = Platform.select({ ios: 'Menlo', default: 'monospace' }) as string;

export const typography = {
  screenTitle:   { fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  emptyHeadline: { fontFamily: 'Inter-Bold',      fontSize: 26, lineHeight: 33, letterSpacing: -0.3 },
  section:       { fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 24, letterSpacing: -0.2 },
  body:          { fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 24 },
  action:        { fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 15 },
  cardTitle:     { fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  reasoning:     { fontFamily: 'Inter-Italic',    fontSize: 13, lineHeight: 20, fontStyle: 'italic' as const },
  meta:          { fontFamily: 'Inter-Regular',   fontSize: 14, lineHeight: 20 },
  caption:       { fontFamily: 'Inter-Medium',    fontSize: 12, lineHeight: 16 },
  tab:           { fontFamily: 'Inter-Medium',    fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  placeholder:   { fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 20 },
  codeInline:    { fontFamily: mono, fontSize: 13, lineHeight: 18 },
  codeBlock:     { fontFamily: mono, fontSize: 13, lineHeight: 20 },
  citation:      { fontFamily: mono, fontSize: 11, lineHeight: 14, fontWeight: '600' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Reasoning Trace (the signature)

```tsx
// components/ReasoningTrace.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import Animated, { FadeIn, FadeOut, useAnimatedStyle, useSharedValue, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { dark as c } from '../theme/colors';
import { typography } from '../theme/typography';

export function ReasoningTrace({ durationSeconds, thought }: {
  durationSeconds: number; thought: string;
}) {
  const [collapsed, setCollapsed] = useState(false);
  const rot = useSharedValue(0);
  const chevron = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));

  return (
    <View style={{
      backgroundColor: c.reasoningBg,                 // DARKER than canvas
      borderRadius: 10,
      borderLeftWidth: 2, borderLeftColor: c.blue,    // 2pt blue left-bar
      paddingVertical: 12, paddingHorizontal: 14,
    }}>
      <Pressable
        onPress={() => { setCollapsed(v => !v); rot.value = withTiming(collapsed ? 0 : -90, { duration: 150 }); }}
        style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}
      >
        <Ionicons name="sparkles" size={14} color={c.blue} />
        <Text style={[typography.caption, { color: c.textSecondary }]}>
          Thought for {durationSeconds} seconds
        </Text>
        <Text style={[typography.caption, { color: c.textTertiary }]}>
          {collapsed ? '· tap to expand' : '· tap to collapse'}
        </Text>
        <View style={{ flex: 1 }} />
        <Animated.View style={chevron}>
          <Ionicons name="chevron-down" size={11} color={c.textTertiary} />
        </Animated.View>
      </Pressable>

      {!collapsed && (
        <Animated.Text
          entering={FadeIn.duration(150)} exiting={FadeOut.duration(120)}
          style={[typography.reasoning, { color: c.reasoningText, marginTop: 8 }]}  // dimmed ITALIC
        >
          {thought}
        </Animated.Text>
      )}
    </View>
  );
}
```

### DeepThink / Search Toggle Pill

```tsx
// components/TogglePill.tsx
import { Pressable, Text, View } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { dark as c } from '../theme/colors';
import { typography } from '../theme/typography';

export function TogglePill({ title, icon, on, onToggle }: {
  title: string; icon: keyof typeof Ionicons.glyphMap; on: boolean; onToggle: () => void;
}) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onToggle(); }}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingVertical: 7, paddingHorizontal: 13, borderRadius: 999,
        backgroundColor: on ? c.blueSoft : c.surface1,
        borderWidth: 1, borderColor: on ? c.blue : c.divider,
      }}
    >
      <Ionicons name={icon} size={14} color={on ? c.blue : c.textSecondary} />
      <Text style={[typography.action, { color: on ? c.blue : c.textSecondary }]}>{title}</Text>
    </Pressable>
  );
}
```

### User Bubble & Assistant Message

```tsx
// components/Messages.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { dark as c } from '../theme/colors';
import { typography } from '../theme/typography';

export function UserBubble({ text }: { text: string }) {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'flex-end', marginBottom: 18 }}>
      <View style={{
        maxWidth: '78%',
        backgroundColor: c.blueSoft,
        borderWidth: 1, borderColor: '#2A3360',
        paddingVertical: 12, paddingHorizontal: 16,
        borderTopLeftRadius: 18, borderTopRightRadius: 18,
        borderBottomLeftRadius: 18, borderBottomRightRadius: 4,   // 4pt corner points at sender
      }}>
        <Text style={[typography.body, { color: c.textPrimary }]}>{text}</Text>
      </View>
    </View>
  );
}

export function AssistantMessage({ model, trace, answer, children }: {
  model: string; trace?: React.ReactNode; answer: React.ReactNode; children?: React.ReactNode;
}) {
  return (
    <View style={{ marginBottom: 18 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 7, marginBottom: 8 }}>
        <Ionicons name="aperture" size={18} color={c.blue} />
        <Text style={[typography.caption, { color: c.textSecondary }]}>{model}</Text>
      </View>
      {trace}
      <Text style={[typography.body, { color: c.textPrimary, marginTop: trace ? 10 : 0 }]}>
        {answer}
      </Text>
      <View style={{ flexDirection: 'row', gap: 16, marginTop: 10 }}>
        {(['copy-outline', 'thumbs-up-outline', 'thumbs-down-outline', 'refresh-outline'] as const)
          .map((n) => <Ionicons key={n} name={n} size={16} color={c.textTertiary} />)}
      </View>
      {children}
    </View>
  );
}
```

### Composer (toggle row + input pill)

```tsx
// components/Composer.tsx
import { View, TextInput, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { TogglePill } from './TogglePill';
import { dark as c } from '../theme/colors';
import { typography } from '../theme/typography';

export function Composer({
  value, onChangeText, deepThink, search, isStreaming,
  onToggleDeepThink, onToggleSearch, onSend, onStop,
}: {
  value: string; onChangeText: (t: string) => void;
  deepThink: boolean; search: boolean; isStreaming: boolean;
  onToggleDeepThink: () => void; onToggleSearch: () => void;
  onSend: () => void; onStop: () => void;
}) {
  return (
    <View style={{ paddingHorizontal: 14 }}>
      <View style={{ flexDirection: 'row', gap: 8, marginBottom: 8 }}>
        <TogglePill title="DeepThink (R1)" icon="sparkles" on={deepThink} onToggle={onToggleDeepThink} />
        <TogglePill title="Search" icon="search" on={search} onToggle={onToggleSearch} />
      </View>
      <View style={{
        flexDirection: 'row', alignItems: 'center', gap: 10, height: 50,
        backgroundColor: c.surface1, borderWidth: 1, borderColor: c.divider,
        borderRadius: 25, paddingLeft: 18, paddingRight: 7,
      }}>
        <TextInput
          value={value} onChangeText={onChangeText}
          placeholder="Message DeepSeek" placeholderTextColor={c.textTertiary}
          style={[typography.body, { flex: 1, color: c.textPrimary }]}
        />
        <Pressable
          onPress={() => {
            if (isStreaming) { onStop(); }
            else { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onSend(); }
          }}
          style={{
            width: 36, height: 36, borderRadius: 18,
            backgroundColor: isStreaming ? c.error : c.blue,
            alignItems: 'center', justifyContent: 'center',
          }}
        >
          <Ionicons name={isStreaming ? 'stop' : 'arrow-up'} size={16} color="#FFFFFF" />
        </Pressable>
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
import { dark as c } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor:   c.blue,        // the single accent — no pill
        tabBarInactiveTintColor: c.textTertiary,
        tabBarStyle: {
          backgroundColor: c.canvas,
          borderTopWidth: 0.5,
          borderTopColor: c.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Chat',     tabBarIcon: ({ color }) => <Ionicons name="chatbubbles-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="history"  options={{ title: 'History',  tabBarIcon: ({ color }) => <Ionicons name="time-outline"        size={22} color={color} /> }} />
      <Tabs.Screen name="settings" options={{ title: 'Settings', tabBarIcon: ({ color }) => <Ionicons name="settings-outline"   size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Toggle on/off — fill + border + content crossfade (150ms)
// handled by re-render; wrap color in withTiming if you want an explicit tween

// Trace collapse/expand — chevron rotate + body fade
// rot.value = withTiming(collapsed ? 0 : -90, { duration: 150 })
// body: entering={FadeIn.duration(150)} exiting={FadeOut.duration(120)}

// Reasoning stream — append tokens to `thought` on an interval; count duration up live
// blink a #4D6BFE caret View with withRepeat(withTiming(0, {duration: 530}), -1, true)

// Send → stop — swap icon "arrow-up" → "stop" and bg c.blue → c.error on isStreaming

// Message append — new bubble slide-up + fade
import Animated, { FadeInDown } from 'react-native-reanimated';
// <Animated.View entering={FadeInDown.duration(200)}><UserBubble .../></Animated.View>

// Citation tap — source sheet (react-native bottom sheet), 300ms ease-out

// Tab change — instant color swap, NO slide

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);              // toggle, send
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // copy
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The whale logomark should be a bundled SVG (`expo-image` / `react-native-svg`) for brand fidelity; everything else maps to Ionicons.

| Purpose | Ionicons |
|---------|----------|
| Chat (tab) | `chatbubbles-outline` |
| History (tab) | `time-outline` |
| Settings (tab) | `settings-outline` |
| DeepThink toggle | `sparkles` |
| Search toggle | `search` |
| Whale / model glyph | custom SVG asset (fallback `aperture`) |
| Send | `arrow-up` |
| Stop streaming | `stop` |
| New chat | `create-outline` |
| Hamburger / history | `menu` |
| Copy | `copy-outline` |
| Thumbs up / down | `thumbs-up-outline` / `thumbs-down-outline` |
| Regenerate | `refresh-outline` |
| Share | `share-outline` |
| Trace collapse | `chevron-down` |
| Citation source | `link-outline` |
| Attach | `attach-outline` / `add` |

## 7. Platform Notes

- **Both themes**: ship light and dark via `useColorScheme()` and the `dark`/`light` token objects. The key invariant in both: the reasoning-trace background is *darker/recessed* relative to the canvas (`#16171A` on `#0E0E10`; `#F7F8FA` on `#FFFFFF`) — recession, not elevation.
- **Single accent**: `#4D6BFE` is the only chrome color. Never add a second accent or a tinted active-tab pill (active = `#4D6BFE` glyph+label).
- **Reasoning is italic**: the chain-of-thought always uses `Inter-Italic` / `fontStyle: 'italic'` and `reasoningText` color. Never render it upright or in `textPrimary`.
- **Status bar**: `<StatusBar style="light" />` on dark, `"dark"` on light.
- **Safe area**: wrap screens in `SafeAreaView`; the composer (toggle row + input) and tab bar need bottom safe-area padding.
- **Keyboard**: use `KeyboardAvoidingView` so the composer (with its toggle row) docks above the keyboard; the chat list should `scrollToEnd` on new messages.
- **Dynamic Type**: keep `allowFontScaling={false}` on tab labels, citation chips, and toggle text (layout-sensitive composer); let message body, headings, and reasoning text scale.
- **Streaming**: append tokens to the trace first (with a live duration counter), then to the answer; debounce list `scrollToEnd`. While streaming, the send control is a `#E5484D` stop button with `accessibilityLabel="Stop generating"`.
- **Asymmetric bubble**: the user bubble uses individual `borderTop/BottomLeft/RightRadius` to get the 18/18/4/18 corner — RN supports per-corner radii directly (no clip path needed).
- **Accessibility**: set `accessibilityLabel` on the trace ("Reasoning, thought for N seconds, {collapsed/expanded}") and on toggles ("DeepThink, {on/off}"); honor `AccessibilityInfo.isReduceMotionEnabled` to drop the chevron rotation and caret blink.
- **Code blocks**: render with the platform monospace and the blue token color (`blueToken`); add a "Copy" affordance using `expo-clipboard`.
