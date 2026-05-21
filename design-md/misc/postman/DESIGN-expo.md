# Postman (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Postman's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (dark — default)
  canvas:      '#1A1A1A', // neutral grey — not black, not charcoal-blue
  surface1:    '#212121',
  surface2:    '#2A2A2A',
  surface3:    '#333333',
  codeSurface: '#161616', // JSON body — darker than canvas (data sinks)
  divider:     '#3A3A3A',
  border:      '#444444',

  // Surfaces (light)
  canvasLight:  '#FFFFFF',
  surfaceLight: '#F4F5F5',
  codeLight:    '#FAFAFA',
  dividerLight: '#E1E3E3',
  borderLight:  '#D6D8D8',

  // Text
  textPrimary:   '#E8E8E8',
  textSecondary: '#A6A6A6',
  textTertiary:  '#6E6E6E',
  textOnLight:   '#212121',

  // Brand (the single accent — actions only)
  orange:        '#FF6C37',
  orangePressed: '#E55A2B',
  orangeDim:     '#3D2417',

  // HTTP method colors (dark — semantic core)
  get:    '#6BDD9A',
  post:   '#FFE47A',
  put:    '#74AEF6',
  patch:  '#C0A8E1',
  delete: '#F79090',
  options:'#A6A6A6',

  // JSON syntax
  jsonKey:  '#74AEF6',
  jsonStr:  '#6BDD9A',
  jsonNum:  '#C0A8E1',
  jsonPunc: '#6E6E6E',
} as const;

export type PostmanColor = keyof typeof colors;

export const methodColor: Record<string, string> = {
  GET: colors.get, POST: colors.post, PUT: colors.put,
  PATCH: colors.patch, DELETE: colors.delete, HEAD: colors.options, OPTIONS: colors.options,
};

export function statusClass(code: number): string {
  if (code >= 200 && code < 300) return colors.get;
  if (code >= 300 && code < 400) return colors.put;
  if (code >= 400 && code < 500) return colors.post;
  return colors.delete;
}
```

## 2. Typography

Load Inter (UI chrome) + JetBrains Mono (all request/response data) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':      require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold': require('../assets/fonts/Inter-ExtraBold.ttf'),
    'JetBrainsMono-Regular': require('../assets/fonts/JetBrainsMono-Regular.ttf'),
    'JetBrainsMono-Medium':  require('../assets/fonts/JetBrainsMono-Medium.ttf'),
    'JetBrainsMono-Bold':    require('../assets/fonts/JetBrainsMono-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ contentStyle: { backgroundColor: '#1A1A1A' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#E8E8E8' } satisfies TextStyle;

export const typography = {
  display:    { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 35, letterSpacing: -0.5 },
  title:      { ...primary, fontFamily: 'Inter-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  subsection: { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:       { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  label:      { ...primary, fontFamily: 'Inter-Medium',    fontSize: 15, lineHeight: 20 },
  meta:       { color: '#A6A6A6', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 19 },
  caption:    { color: '#A6A6A6', fontFamily: 'Inter-Medium',  fontSize: 12, lineHeight: 16, letterSpacing: 0.5 },
  tab:        { color: '#6E6E6E', fontFamily: 'Inter-Medium',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:     { fontFamily: 'Inter-Bold', fontSize: 14, lineHeight: 14 },
  // Mono — URLs, headers, params, JSON, status, timing
  method:     { fontFamily: 'JetBrainsMono-Bold',    fontSize: 13, lineHeight: 14, letterSpacing: 0.3 },
  monoData:   { ...primary, fontFamily: 'JetBrainsMono-Regular', fontSize: 13, lineHeight: 18 },
  monoJson:   { ...primary, fontFamily: 'JetBrainsMono-Regular', fontSize: 12, lineHeight: 19 },
  statusCode: { fontFamily: 'JetBrainsMono-Bold',    fontSize: 12, lineHeight: 14, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Method-Colored URL / Request Bar

```tsx
// components/RequestBar.tsx
import { View, Text, TextInput, Pressable } from 'react-native';
import { colors, methodColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function RequestBar({
  method, url, onChangeUrl, onSend, onPickMethod,
}: {
  method: string; url: string;
  onChangeUrl: (s: string) => void; onSend: () => void; onPickMethod: () => void;
}) {
  return (
    <View style={{
      flexDirection: 'row', height: 44, alignItems: 'stretch',
      backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.border,
      borderRadius: 10, overflow: 'hidden',
    }}>
      <Pressable onPress={onPickMethod}
        style={{ justifyContent: 'center', paddingHorizontal: 12, borderRightWidth: 1, borderRightColor: colors.border }}>
        <Text style={[typography.method, { color: methodColor[method] }]}>{method}</Text>
      </Pressable>
      <TextInput
        value={url} onChangeText={onChangeUrl}
        placeholder="Enter request URL" placeholderTextColor={colors.textTertiary}
        autoCapitalize="none" autoCorrect={false}
        style={[typography.monoData, { flex: 1, paddingHorizontal: 12 }]}
      />
      <Pressable onPress={onSend}
        style={({ pressed }) => ({
          justifyContent: 'center', paddingHorizontal: 16,
          backgroundColor: pressed ? colors.orangePressed : colors.orange,
        })}>
        <Text style={[typography.button, { color: '#FFFFFF' }]}>Send</Text>
      </Pressable>
    </View>
  );
}
```

### HTTP Method Chip

```tsx
// components/MethodChip.tsx
import { View, Text } from 'react-native';
import { methodColor } from '../theme/colors';
import { typography } from '../theme/typography';

export function MethodChip({ method }: { method: string }) {
  const c = methodColor[method] ?? '#A6A6A6';
  return (
    <View style={{ backgroundColor: `${c}1F`, borderRadius: 6, paddingVertical: 6, paddingHorizontal: 12, alignSelf: 'flex-start' }}>
      <Text style={[typography.method, { color: c }]}>{method}</Text>
    </View>
  );
}
```

### Status Code Chip

```tsx
// components/StatusChip.tsx
import { View, Text } from 'react-native';
import { statusClass } from '../theme/colors';
import { typography } from '../theme/typography';

export function StatusChip({ code, label }: { code: number; label: string }) {
  const c = statusClass(code);
  return (
    <View style={{
      flexDirection: 'row', paddingVertical: 5, paddingHorizontal: 10, borderRadius: 999,
      backgroundColor: `${c}14`, borderWidth: 1, borderColor: `${c}66`, alignSelf: 'flex-start',
    }}>
      <Text style={[typography.statusCode, { color: c }]}>{code} {label}</Text>
    </View>
  );
}
```

### Key-Value Editor Row

```tsx
// components/KeyValueRow.tsx
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function KeyValueRow({
  k, v, enabled, onK, onV, onToggle,
}: { k: string; v: string; enabled: boolean; onK: (s: string) => void; onV: (s: string) => void; onToggle: () => void }) {
  const cell = {
    backgroundColor: colors.surface1, borderWidth: 1, borderColor: colors.divider,
    borderRadius: 6, paddingVertical: 9, paddingHorizontal: 10,
  } as const;
  return (
    <View style={{ flexDirection: 'row', gap: 8, alignItems: 'center', marginBottom: 8 }}>
      <Pressable onPress={onToggle} hitSlop={8}>
        <Ionicons name={enabled ? 'checkbox' : 'square-outline'} size={18}
          color={enabled ? colors.orange : colors.textTertiary} />
      </Pressable>
      <TextInput value={k} onChangeText={onK} placeholder="key" placeholderTextColor={colors.textTertiary}
        autoCapitalize="none" style={[cell, typography.monoData, { flex: 0, width: 120, color: colors.jsonKey }]} />
      <TextInput value={v} onChangeText={onV} placeholder="value" placeholderTextColor={colors.textTertiary}
        autoCapitalize="none" style={[cell, typography.monoData, { flex: 1 }]} />
    </View>
  );
}
```

### Response Viewer with Syntax-Highlighted JSON

```tsx
// components/ResponseViewer.tsx
import { ScrollView, View, Text } from 'react-native';
import { StatusChip } from './StatusChip';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Tok = { t: string; kind: 'key' | 'str' | 'num' | 'punc' | 'plain' };
const tokColor = { key: colors.jsonKey, str: colors.jsonStr, num: colors.jsonNum, punc: colors.jsonPunc, plain: colors.textPrimary };

export function ResponseViewer({
  code, label, durationMs, sizeText, tokens,
}: { code: number; label: string; durationMs: number; sizeText: string; tokens: Tok[] }) {
  return (
    <View>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 18, paddingVertical: 8 }}>
        <StatusChip code={code} label={label} />
        <Text style={[typography.monoJson, { color: colors.textSecondary }]}>{durationMs} ms</Text>
        <Text style={[typography.monoJson, { color: colors.textSecondary }]}>{sizeText}</Text>
      </View>
      <ScrollView
        style={{
          marginHorizontal: 14, marginBottom: 14,
          backgroundColor: colors.codeSurface, // data sinks darker
          borderWidth: 1, borderColor: colors.divider, borderRadius: 8,
        }}
        contentContainerStyle={{ padding: 12 }}
      >
        <Text style={typography.monoJson}>
          {tokens.map((tk, i) => (
            <Text key={i} style={{ color: tokColor[tk.kind] }}>{tk.t}</Text>
          ))}
        </Text>
      </ScrollView>
    </View>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.orange,   // the single accent
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(26,26,26,0.94)',
          borderTopWidth: 0.5, borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
        headerShown: false,
      }}
    >
      <Tabs.Screen name="index"        options={{ title: 'Request',      tabBarIcon: ({ color }) => <Ionicons name="swap-vertical" size={21} color={color} /> }} />
      <Tabs.Screen name="collections"  options={{ title: 'Collections',  tabBarIcon: ({ color }) => <Ionicons name="folder-outline" size={21} color={color} /> }} />
      <Tabs.Screen name="history"      options={{ title: 'History',      tabBarIcon: ({ color }) => <Ionicons name="time-outline"   size={21} color={color} /> }} />
      <Tabs.Screen name="environments" options={{ title: 'Environments', tabBarIcon: ({ color }) => <Ionicons name="list-outline"   size={21} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Send → spinner → response slide-up + status-chip pop
const respY = useSharedValue(24);
const respOpacity = useSharedValue(0);
function onResponse() {
  respY.value = withTiming(0, { duration: 220 });
  respOpacity.value = withTiming(1, { duration: 220 });
}
const respStyle = useAnimatedStyle(() => ({ opacity: respOpacity.value, transform: [{ translateY: respY.value }] }));

// status-chip pop
const chip = useSharedValue(0.9);
chip.value = withSpring(1, { damping: 12, stiffness: 180 });

// Tab-strip underline slide — animate left/width with withTiming(..., { duration: 200 })

// Method picker → URL pill color cross-fade: interpolateColor over withTiming 180ms

// JSON node collapse — rotate chevron withTiming(open ? 90 : 0, { duration: 150 })

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);                        // Send tap
Haptics.notificationAsync(code < 400 ? Haptics.NotificationFeedbackType.Success
                                     : Haptics.NotificationFeedbackType.Error); // response
Haptics.selectionAsync();                                                       // method / tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The Postman roundel is a custom shape (`react-native-svg` or a styled `View`), not an icon glyph.

| Purpose | Ionicons |
|---------|----------|
| Request (tab) | `swap-vertical` |
| Collections (tab) | `folder-outline` |
| History (tab) | `time-outline` |
| Environments (tab) | `list-outline` |
| Send | `paper-plane` |
| Save | `save-outline` |
| Add request | `add` |
| Method chevron | `chevron-down` |
| JSON node | `chevron-forward` (rotate) |
| Copy | `copy-outline` |
| Enable row | `checkbox` / `square-outline` |
| Delete row | `trash-outline` |
| Search | `search-outline` |
| Environment | `globe-outline` |
| Test pass | `checkmark-circle` |
| Test fail | `close-circle` |
| Back | `chevron-back` |
| More | `ellipsis-horizontal` |

## 7. Platform Notes

- **Font choice**: Inter + JetBrains Mono are SIL OFL — free to bundle. Ship both; the split is by content type (chrome vs machine data), never a user setting
- **Neutral grey canvas**: set `contentStyle.backgroundColor` and screen roots to `#1A1A1A` — NOT black, NOT a blue-tinted charcoal
- **Status bar**: `<StatusBar style="light" />` on dark; `"dark"` on the light variant
- **Safe area**: wrap screens in `SafeAreaView`; the blurred tab bar floats over content — pad list/response bottoms by tab height + safe area
- **Code surface stays darker**: the response JSON `View` uses `codeSurface` (`#161616`) in both modes — data sinks, controls float
- **Dynamic Type**: set `allowFontScaling={false}` on method pills, status chips, tab labels, JSON body, and ≤12pt mono (column-alignment-sensitive); allow scaling on body/headings
- **Keyboard**: use `KeyboardAvoidingView`; the active key-value editor / URL field scrolls above the keyboard and the response pane collapses
- **Dark mode**: neutral grey is the brand. If supporting light, swap to `canvasLight`/`surfaceLight`/`dividerLight` via `useColorScheme()`; switch method hues to the saturated light set (`#0CBB52` GET, `#1A73E8` PUT, `#7D4FC4` PATCH, `#E5484D` DELETE) but keep hue identity
- **JSON performance**: tokenize once and render spans; for very large bodies use `FlatList` over lines rather than one giant `Text`
- **Accessibility**: method and status are always shown as text (`GET`, `200`) — color is never the only signal; announce request rows as "{method} request, {name}"
