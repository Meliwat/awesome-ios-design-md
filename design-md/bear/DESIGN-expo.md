# Bear (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bear's visual language into paste-ready Expo / React Native code: a design-token module, the live-Markdown renderer, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceLight:   '#F6F6F4',
  pressedLight:   '#ECECE8',
  dividerLight:   '#E6E6E2',

  // Surfaces (dark — "Charcoal")
  canvasDark:     '#21252B',
  surface1:       '#282C34',
  surface2:       '#2F343D',
  divider:        '#353A42',

  // Text
  textPrimary:    '#E8EAED', // dark
  textPrimaryLt:  '#2A2C33', // light
  textSecondary:  '#9DA3AD',
  textTertiary:   '#6B7280',

  // Brand
  bearRed:        '#E0566F',
  bearOrange:     '#FF8A65',
  bearRedPressed: '#C8485E',
  link:           '#5AAFEF',

  // Tag / highlight accents
  tagBlue:        '#5AAFEF',
  tagGreen:       '#57C98B',
  tagYellow:      '#F2C14E',
  tagPurple:      '#B08CF0',
  highlight:      '#4A431F', // dark

  // Semantic
  error:          '#F2545B',
  success:        '#57C98B',
} as const;

export type BearColor = keyof typeof colors;

// The single brand accent — used by expo-linear-gradient
export const brandGradient = {
  colors: [colors.bearRed, colors.bearOrange] as const,
  start: { x: 0, y: 0 },
  end:   { x: 1, y: 1 },
};
```

## 2. Typography

Load Inter, Lora, JetBrains Mono via `expo-font`. The prose family is user-switchable; fenced code is always JetBrains Mono.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':require('../assets/fonts/Inter-ExtraBold.ttf'),
    'Lora-Regular':   require('../assets/fonts/Lora-Regular.ttf'),
    'Lora-Italic':    require('../assets/fonts/Lora-Italic.ttf'),
    'Lora-Bold':      require('../assets/fonts/Lora-Bold.ttf'),
    'JetBrainsMono-Regular': require('../assets/fonts/JetBrainsMono-Regular.ttf'),
    'JetBrainsMono-Medium':  require('../assets/fonts/JetBrainsMono-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#E8EAED' } satisfies TextStyle;

export const typography = {
  largeTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 38, letterSpacing: -0.6 },
  noteTitle:  { ...primary, fontFamily: 'Inter-Bold',      fontSize: 24, lineHeight: 30, letterSpacing: -0.3 },
  h2:         { ...primary, fontFamily: 'Inter-Bold',      fontSize: 19, lineHeight: 25, letterSpacing: -0.2 },
  h3:         { ...primary, fontFamily: 'Inter-Bold',      fontSize: 17, lineHeight: 23, letterSpacing: -0.1 },
  subhead:    { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 17, lineHeight: 23 },
  body:       { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 25 },
  bodyDense:  { ...primary, fontFamily: 'Inter-Regular',   fontSize: 15, lineHeight: 22 },
  quote:      { color: '#9DA3AD', fontFamily: 'Lora-Italic', fontSize: 17, lineHeight: 26 },
  preview:    { color: '#9DA3AD', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },
  meta:       { color: '#9DA3AD', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },
  tag:        { color: '#E0566F', fontFamily: 'Inter-Medium', fontSize: 14, lineHeight: 20 },
  link:       { color: '#5AAFEF', fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 25, textDecorationLine: 'underline' as const },
  codeInline: { ...primary, fontFamily: 'JetBrainsMono-Regular', fontSize: 14, lineHeight: 20 },
  codeBlock:  { ...primary, fontFamily: 'JetBrainsMono-Regular', fontSize: 13, lineHeight: 20 },
  caption:    { color: '#6B7280', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 16, letterSpacing: 0.1 },
  tabLabel:   { color: '#6B7280', fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 17, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Live Markdown Renderer

Bear's signature: render Markdown in-place, dimming the syntax marker rather than hiding it.

```tsx
// components/MarkdownLine.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';
import { CheckboxRow } from './CheckboxRow';
import { QuoteBlock } from './QuoteBlock';

export function MarkdownLine({ raw }: { raw: string }) {
  if (raw.startsWith('# ')) {
    return (
      <Text style={typography.noteTitle}>
        <Text style={{ color: colors.textTertiary }}># </Text>
        {raw.slice(2)}
      </Text>
    );
  }
  if (raw.startsWith('## ')) {
    return (
      <Text style={typography.h2}>
        <Text style={{ color: colors.textTertiary }}>## </Text>
        {raw.slice(3)}
      </Text>
    );
  }
  if (raw.startsWith('> ')) return <QuoteBlock text={raw.slice(2)} />;
  if (raw.startsWith('- [')) {
    return <CheckboxRow checked={raw.startsWith('- [x]')} label={raw.replace(/^- \[.\]\s*/, '')} />;
  }
  return <InlineStyled raw={raw} />;
}

// Splits a line into runs, tinting #hashtags red and [[wiki]] links blue.
function InlineStyled({ raw }: { raw: string }) {
  const parts = raw.split(/(#[\w/]+|\[\[[^\]]+\]\])/g);
  return (
    <Text style={typography.body}>
      {parts.map((p, i) => {
        if (/^#[\w/]+$/.test(p)) return <Text key={i} style={{ color: colors.bearRed }}>{p}</Text>;
        if (/^\[\[.+\]\]$/.test(p)) return <Text key={i} style={typography.link}>{p}</Text>;
        return <Text key={i}>{p}</Text>;
      })}
    </Text>
  );
}
```

### Note Editor (Title + Body)

```tsx
// components/NoteEditor.tsx
import { ScrollView, TextInput, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';
import { MarkdownLine } from './MarkdownLine';

export function NoteEditor({
  title, onChangeTitle, lines,
}: { title: string; onChangeTitle: (t: string) => void; lines: string[] }) {
  return (
    <ScrollView style={{ backgroundColor: colors.canvasDark }} contentContainerStyle={{ paddingHorizontal: 22, paddingTop: 4 }}>
      <TextInput
        value={title}
        onChangeText={onChangeTitle}
        placeholder="Title"
        placeholderTextColor={colors.textTertiary}
        style={typography.noteTitle}
      />
      <View style={{ gap: 16, marginTop: 16 }}>
        {lines.map((l, i) => <MarkdownLine key={i} raw={l} />)}
      </View>
    </ScrollView>
  );
}
```

### Checkbox Row

```tsx
// components/CheckboxRow.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { useAnimatedStyle, withTiming, useSharedValue } from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { brandGradient } from '../theme/colors';

export function CheckboxRow({ checked: init, label }: { checked: boolean; label: string }) {
  const [checked, setChecked] = useState(init);
  const fill = useSharedValue(init ? 1 : 0);
  const boxStyle = useAnimatedStyle(() => ({ opacity: fill.value }));

  const toggle = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    const next = !checked;
    setChecked(next);
    fill.value = withTiming(next ? 1 : 0, { duration: 150 });
  };

  return (
    <Pressable onPress={toggle} style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
      <View style={{ width: 19, height: 19, borderRadius: 5, borderWidth: 2, borderColor: colors.textTertiary, overflow: 'hidden' }}>
        <Animated.View style={[{ ...StyleSheetAbsolute }, boxStyle]}>
          <LinearGradient {...brandGradient} style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="checkmark" size={11} color="#FFF" />
          </LinearGradient>
        </Animated.View>
      </View>
      <Text style={[typography.body, checked && { color: colors.textTertiary, textDecorationLine: 'line-through' }]}>
        {label}
      </Text>
    </Pressable>
  );
}

const StyleSheetAbsolute = { position: 'absolute' as const, top: -2, left: -2, right: -2, bottom: -2 };
```

### Quote Block

```tsx
// components/QuoteBlock.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function QuoteBlock({ text }: { text: string }) {
  return (
    <View style={{ flexDirection: 'row' }}>
      <View style={{ width: 3, backgroundColor: colors.bearOrange, borderRadius: 2 }} />
      <Text style={[typography.quote, { paddingLeft: 14, paddingVertical: 2, flex: 1 }]}>{text}</Text>
    </View>
  );
}
```

### Code Block

```tsx
// components/CodeBlock.tsx
import { ScrollView, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CodeBlock({ language, code }: { language?: string; code: string }) {
  return (
    <View style={{ backgroundColor: colors.surface1, borderRadius: 8 }}>
      {language ? (
        <Text style={[typography.caption, { textAlign: 'right', paddingHorizontal: 14, paddingTop: 8 }]}>{language}</Text>
      ) : null}
      <ScrollView horizontal showsHorizontalScrollIndicator={false}>
        <Text style={[typography.codeBlock, { padding: 14 }]}>{code}</Text>
      </ScrollView>
    </View>
  );
}
```

### Tag Sidebar Row

```tsx
// components/TagRow.tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TagRow({
  icon = 'pricetag', title, indent = 0, active = false,
}: { icon?: any; title: string; indent?: number; active?: boolean }) {
  return (
    <Pressable
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 8, height: 36,
        paddingLeft: 14 + indent * 18, paddingRight: 12,
        backgroundColor: active ? colors.surface2 : 'transparent',
      }}
    >
      <Ionicons name={icon} size={16} color={colors.bearRed} />
      <Text style={typography.tag}>{title}</Text>
    </Pressable>
  );
}
```

### Compose FAB

```tsx
// components/ComposeFAB.tsx
import { Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brandGradient, colors } from '../theme/colors';

export function ComposeFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        position: 'absolute', right: 20, bottom: 28,
        shadowColor: colors.bearRed, shadowOpacity: 0.35, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
        elevation: 8,
      }}
    >
      <LinearGradient {...brandGradient} style={{ width: 56, height: 56, borderRadius: 28, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="create-outline" size={24} color="#FFF" />
      </LinearGradient>
    </Pressable>
  );
}
```

### Editor Info Bar

```tsx
// components/EditorInfoBar.tsx
import { Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function EditorInfoBar({ words, chars }: { words: number; chars: number }) {
  return (
    <BlurView intensity={40} tint="dark" style={{ height: 60, borderTopWidth: 0.5, borderTopColor: colors.divider }}>
      <View style={{ flex: 1, flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 22 }}>
        <Text style={typography.caption}>{words} words · {chars} chars</Text>
        <View style={{ flexDirection: 'row', gap: 22 }}>
          {(['grid-outline', 'eye-outline', 'share-outline', 'information-circle-outline'] as const).map((n) => (
            <Ionicons key={n} name={n} size={20} color={colors.bearOrange} />
          ))}
        </View>
      </View>
    </BlurView>
  );
}
```

## 4. Bottom Tab Bar

Bear's shell is pushed navigation (sidebar → list → editor), not a tab bar; the editor's bottom strip is the **info bar** above. If you implement the smart-filter sidebar as a Drawer:

```tsx
// app/(tabs)/_layout.tsx — minimal: Notes + Search if you choose a tab shell
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.bearOrange,   // every active glyph is Bear Orange
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopWidth: 0.5, borderTopColor: colors.divider },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 13, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Notes',  tabBarIcon: ({ color }) => <Ionicons name="document-text-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="search" options={{ title: 'Search', tabBarIcon: ({ color }) => <Ionicons name="search" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, FadeOut, withTiming, withSpring } from 'react-native-reanimated';

// Checkbox toggle — gradient fill cross-fade
// fill.value = withTiming(next ? 1 : 0, { duration: 150 });

// Markdown syntax-marker fade (opacity only — never a layout jump)
// markerOpacity.value = withTiming(dimmed ? 0.45 : 1, { duration: 120 });

// Tag tap → note-list filter cross-fade
// <Animated.View entering={FadeIn.duration(200)} exiting={FadeOut.duration(200)}>

// Nested tag disclosure
// rotation.value = withTiming(open ? 90 : 0, { duration: 150 });
// children: entering={FadeIn.duration(200)}

// Compose FAB
// scale.value = withTiming(0.92, { duration: 90 }); then push note (easeOut 280ms)

// Sidebar drawer open: withSpring(1, { damping: 14, stiffness: 120 })  // 50% threshold

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);              // checkbox, tag tap, FAB
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // export complete
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Tint every interactive glyph Bear Orange `#FF8A65`; tag glyphs are Bear Red `#E0566F`.

| Purpose | Ionicons |
|---------|----------|
| Back ("Notes") | `chevron-back` |
| Info (top nav) | `information-circle-outline` |
| Format / Markdown | `text-outline` |
| More | `ellipsis-horizontal` |
| Compose FAB | `create-outline` |
| Checkbox (checked) | `checkmark` |
| Tag / #hashtag | `pricetag` |
| Smart filter — Notes | `document-text-outline` |
| Smart filter — Todo | `checkbox-outline` |
| Smart filter — Today | `calendar-outline` |
| Smart filter — Archive | `archive-outline` |
| Smart filter — Trash | `trash-outline` |
| Search | `search` |
| Info-bar — Table | `grid-outline` |
| Info-bar — Preview | `eye-outline` |
| Info-bar — Export | `share-outline` |
| Disclosure (nested tag) | `chevron-forward` |
| Pin note | `pin-outline` |
| Link / wiki-link | `link-outline` |

## 7. Platform Notes

- **Font choice**: Inter / Lora / JetBrains Mono are all SIL OFL — free to bundle. Ship all three; let the user pick a prose family in Settings; fenced code is always JetBrains Mono
- **User typography switching**: wrap the app in a `BearFontContext`; re-render text when the family changes; never hard-code Inter for prose
- **Status bar**: `<StatusBar style="light" />` on the dark "Charcoal" theme, `"dark"` on light
- **Safe area**: wrap screens in `SafeAreaView`; the editor info bar and FAB need bottom safe-area padding; the keyboard accessory row docks above the keyboard
- **Dynamic Type**: `<Text>` honors system scale — set `allowFontScaling={false}` on tag tokens, the 12pt info-bar caption, sidebar filter labels, tab labels
- **Live Markdown**: render runs with nested `<Text>` so syntax markers can be dimmed in-place without remounting; never use a separate WebView/preview
- **Keyboard**: use `KeyboardAvoidingView` on the editor; the formatting accessory row (`InputAccessoryView`) holds #, **, `- [ ]`, >, ```, link shortcuts
- **Dark mode / themes**: model each Bear theme as a token object; swap with `useColorScheme()` or a theme picker; the brand gradient is constant across all themes
- **Accessibility**: checkbox rows expose an `accessibilityRole="checkbox"` with `accessibilityState={{ checked }}`; #hashtag tokens get `accessibilityHint="Double tap to filter by this tag"`
- **Drag-to-reorder list rows / blocks**: use `react-native-draggable-flatlist`; soft haptic on lift
