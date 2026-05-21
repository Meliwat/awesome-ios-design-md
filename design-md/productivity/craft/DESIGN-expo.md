# Craft (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Craft's visual language into paste-ready Expo / React Native code: a design-token module, the brand gradient, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FCFCFD',
  surfaceGray:    '#F4F4F6',
  card:           '#FFFFFF',
  surfacePressed: '#ECECEF',
  divider:        '#E6E6EA',

  // Surfaces (dark)
  darkCanvas:     '#1A1A1E',
  darkCard:       '#232328',
  darkSurface2:   '#2C2C32',
  darkDivider:    '#34343C',

  // Text
  textPrimary:     '#1C1C22',
  textSecondary:   '#6A6A78',
  textTertiary:    '#9B9BA6',
  darkTextPrimary: '#ECECEF',
  darkTextTertiary:'#67677A',

  // Brand
  craftBlue:        '#2F5BEA',
  craftBluePressed: '#2347C9',
  craftPurple:      '#6E56CF',
  craftPurpleSoft:  '#8B73E8',

  // Accents / semantic
  green: '#30A46C',
  amber: '#F0A92B',
  red:   '#E5484D',
  pink:  '#D6409F',
  teal:  '#12A594',
} as const;

// The constant Craft brand identity — feed to expo-linear-gradient
export const brandGradient = ['#2F5BEA', '#6E56CF'] as const;
// <LinearGradient colors={brandGradient} start={{x:0,y:0}} end={{x:1,y:1}} />

export type CraftColor = keyof typeof colors;

// Block icon-chip tints (pair tint bg with a brand/utility hue)
export const chipTints = {
  blue:   { bg: 'rgba(47,91,234,0.12)',  fg: colors.craftBlue },
  purple: { bg: 'rgba(110,86,207,0.12)', fg: colors.craftPurple },
  green:  { bg: 'rgba(48,164,108,0.12)', fg: colors.green },
  amber:  { bg: 'rgba(240,169,43,0.14)', fg: colors.amber },
  red:    { bg: 'rgba(229,72,77,0.12)',  fg: colors.red },
  neutral:{ bg: 'rgba(118,118,128,0.12)',fg: colors.textSecondary },
} as const;
```

## 2. Typography

Load Inter, Lora, JetBrains Mono via `expo-font`.

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
    'Lora-Italic':     require('../assets/fonts/Lora-Italic.ttf'),
    'JetBrainsMono-Regular': require('../assets/fonts/JetBrainsMono-Regular.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
const primary = { color: '#1C1C22' } satisfies TextStyle;

export const typography = {
  docTitle:  { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 28, lineHeight: 34, letterSpacing: -0.5 },
  h1:        { ...primary, fontFamily: 'Inter-Bold',      fontSize: 24, lineHeight: 30, letterSpacing: -0.3 },
  h2:        { ...primary, fontFamily: 'Inter-Bold',      fontSize: 20, lineHeight: 26, letterSpacing: -0.2 },
  h3:        { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 17, lineHeight: 23, letterSpacing: -0.1 },
  body:      { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 25 },
  bodyBold:  { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 16, lineHeight: 25 },
  cardTitle: { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  cardSub:   { color: '#6A6A78', fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 16 },
  meta:      { color: '#6A6A78', fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 18 },
  caption:   { color: '#6A6A78', fontFamily: 'Inter-Medium',  fontSize: 12, lineHeight: 17, letterSpacing: 0.1 },
  button:    { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 15, lineHeight: 15 },
  link:      { color: '#2F5BEA', fontFamily: 'Inter-Medium',  fontSize: 16, lineHeight: 25 },
  tab:       { fontFamily: 'Inter-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  sidebar:   { ...primary, fontFamily: 'Inter-Medium', fontSize: 14, lineHeight: 18 },
  quote:     { color: '#6A6A78', fontFamily: 'Lora-Italic', fontSize: 17, lineHeight: 26 },
  codeInline:{ ...primary, fontFamily: 'JetBrainsMono-Regular', fontSize: 14, lineHeight: 20 },
  codeBlock: { ...primary, fontFamily: 'JetBrainsMono-Regular', fontSize: 13, lineHeight: 20 },
  placeholder:{ color: '#9B9BA6', fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 25 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Document Header (Cover + Emoji + Title)

```tsx
// components/CraftDocHeader.tsx
import { Image, TextInput, View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CraftDocHeader({ coverUri, emoji, title, onChangeTitle, subtitle }: {
  coverUri?: string; emoji: string; title: string;
  onChangeTitle: (t: string) => void; subtitle: string;
}) {
  return (
    <View>
      {coverUri ? (
        <Image source={{ uri: coverUri }} style={{ height: 96, marginHorizontal: 22, borderRadius: 14 }} resizeMode="cover" />
      ) : (
        <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ height: 96, marginHorizontal: 22, borderRadius: 14 }} />
      )}
      <Text style={{ fontSize: 40, marginLeft: 26, marginTop: -38,
        textShadowColor: 'rgba(0,0,0,0.4)', textShadowRadius: 6, textShadowOffset: { width: 0, height: 3 } }}>
        {emoji}
      </Text>
      <TextInput value={title} onChangeText={onChangeTitle} placeholder="Untitled"
        placeholderTextColor={colors.textTertiary}
        style={[typography.docTitle, { paddingHorizontal: 22, paddingTop: 6 }]} />
      <Text style={[typography.meta, { paddingHorizontal: 22, paddingTop: 4, paddingBottom: 18 }]}>{subtitle}</Text>
    </View>
  );
}
```

### Card Block (Page / Link) — *signature*

```tsx
// components/CraftCardBlock.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { chipTints, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CraftCardBlock({ emoji, title, subtitle, tint = 'blue', onOpen }: {
  emoji: string; title: string; subtitle: string;
  tint?: keyof typeof chipTints; onOpen: () => void;
}) {
  const c = chipTints[tint];
  return (
    <Pressable onPress={onOpen} style={({ pressed }) => ({
      flexDirection: 'row', alignItems: 'center', gap: 12, padding: 14,
      backgroundColor: pressed ? colors.surfacePressed : colors.card,
      borderRadius: 12, borderWidth: 1, borderColor: colors.divider,
      shadowColor: '#000', shadowOpacity: 0.10, shadowRadius: 9, shadowOffset: { width: 0, height: 6 },
      elevation: 2,
    })}>
      <View style={{ width: 36, height: 36, borderRadius: 9, backgroundColor: c.bg, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontSize: 17 }}>{emoji}</Text>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={typography.cardTitle}>{title}</Text>
        <Text style={typography.cardSub}>{subtitle}</Text>
      </View>
      <Ionicons name="chevron-forward" size={16} color={colors.textTertiary} />
    </Pressable>
  );
}
```

### To-do Block

```tsx
// components/CraftTodoBlock.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CraftTodoBlock({ initialDone = false, text }: { initialDone?: boolean; text: string }) {
  const [done, setDone] = useState(initialDone);
  const scale = useSharedValue(1);
  const boxStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const toggle = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
    setDone((d) => !d);
    scale.value = withSpring(1.18, { damping: 6 }, () => { scale.value = withSpring(1); });
  };

  return (
    <Pressable onPress={toggle} style={{ flexDirection: 'row', alignItems: 'flex-start', gap: 10, paddingVertical: 3 }}>
      <Animated.View style={[{
        width: 18, height: 18, borderRadius: 6, borderWidth: 1.8,
        borderColor: done ? colors.craftBlue : colors.textTertiary,
        backgroundColor: done ? colors.craftBlue : 'transparent',
        alignItems: 'center', justifyContent: 'center',
      }, boxStyle]}>
        {done ? <Ionicons name="checkmark" size={11} color="#FFF" /> : null}
      </Animated.View>
      <Text style={[typography.body, done && { textDecorationLine: 'line-through', color: colors.textTertiary }]}>{text}</Text>
    </Pressable>
  );
}
```

### Primary Button (Gradient)

```tsx
// components/CraftPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function CraftPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ transform: [{ scale: pressed ? 0.98 : 1 }], opacity: pressed ? 0.92 : 1 })}>
      <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ paddingVertical: 13, paddingHorizontal: 26, borderRadius: 12, alignItems: 'center' }}>
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}
```

### Toggle Block

```tsx
// components/CraftToggleBlock.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Animated, { FadeIn, FadeOut, useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CraftToggleBlock({ title, children }: { title: string; children: React.ReactNode }) {
  const [open, setOpen] = useState(false);
  const rot = useSharedValue(0);
  const chev = useAnimatedStyle(() => ({ transform: [{ rotate: `${rot.value}deg` }] }));
  return (
    <View>
      <Pressable onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        const n = !open; setOpen(n); rot.value = withTiming(n ? 90 : 0, { duration: 180 }); }}
        style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingVertical: 6 }}>
        <Animated.View style={chev}><Ionicons name="chevron-forward" size={14} color={colors.textTertiary} /></Animated.View>
        <Text style={[typography.body, { fontFamily: 'Inter-Medium' }]}>{title}</Text>
      </Pressable>
      {open ? (
        <Animated.View entering={FadeIn.duration(200)} exiting={FadeOut.duration(150)} style={{ paddingLeft: 20 }}>
          {children}
        </Animated.View>
      ) : null}
    </View>
  );
}
```

### `/` Block Inserter

```tsx
// components/CraftSlashInserter.tsx
import { FlatList, Pressable, Text, View } from 'react-native';
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Block = { id: string; icon: string; title: string; subtitle: string };

export function CraftSlashInserter({ blocks, onPick }: { blocks: Block[]; onPick: (id: string) => void }) {
  return (
    <Animated.View entering={FadeIn.duration(180)} exiting={FadeOut.duration(150)} style={{
      width: 300, backgroundColor: colors.card, borderRadius: 12,
      borderWidth: 1, borderColor: colors.divider,
      shadowColor: '#000', shadowOpacity: 0.14, shadowRadius: 16, shadowOffset: { width: 0, height: 8 }, elevation: 8,
      paddingVertical: 6,
    }}>
      <FlatList data={blocks} keyExtractor={(b) => b.id} renderItem={({ item }) => (
        <Pressable onPress={() => onPick(item.id)} style={({ pressed }) => ({
          flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 14, height: 44,
          backgroundColor: pressed ? 'rgba(47,91,234,0.10)' : 'transparent',
        })}>
          <View style={{ width: 28, height: 28, borderRadius: 7, backgroundColor: colors.surfaceGray, alignItems: 'center', justifyContent: 'center' }}>
            <Text style={{ fontSize: 14 }}>{item.icon}</Text>
          </View>
          <View style={{ flex: 1 }}>
            <Text style={[typography.caption, { fontFamily: 'Inter-SemiBold', color: colors.textPrimary }]}>{item.title}</Text>
            <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.textSecondary }}>{item.subtitle}</Text>
          </View>
        </Pressable>
      )} />
    </Animated.View>
  );
}
```

## 4. Bottom Tab Bar

Craft's bar has a center gradient FAB — use a custom tab bar.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Pressable, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brandGradient, colors } from '../../theme/colors';

function CraftFab({ onPress }: { onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ top: -12 }}>
      <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 46, height: 46, borderRadius: 14, alignItems: 'center', justifyContent: 'center',
          shadowColor: colors.craftBlue, shadowOpacity: 0.6, shadowRadius: 10, shadowOffset: { width: 0, height: 8 }, elevation: 8 }}>
        <Ionicons name="add" size={22} color="#FFF" />
      </LinearGradient>
    </Pressable>
  );
}

export default function TabsLayout() {
  return (
    <Tabs screenOptions={{
      tabBarActiveTintColor: colors.craftBlue,
      tabBarInactiveTintColor: colors.textSecondary,
      tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
      tabBarShowLabel: false,
    }}>
      <Tabs.Screen name="index"   options={{ tabBarIcon: ({ color }) => <Ionicons name="home" size={21} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ tabBarIcon: ({ color }) => <Ionicons name="search" size={21} color={color} /> }} />
      <Tabs.Screen name="add"     options={{ tabBarButton: () => <CraftFab onPress={() => {}} /> }} />
      <Tabs.Screen name="docs"    options={{ tabBarIcon: ({ color }) => <Ionicons name="document-text" size={21} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={21} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Card open — router push (default iOS push ~320ms)
// To-do complete — spring scale-pop
scale.value = withSpring(1.18, { damping: 6 }, () => { scale.value = withSpring(1); });

// Toggle expand/collapse
// chevron withTiming(90, { duration: 180 }); children FadeIn/FadeOut

// / inserter
// entering={FadeIn.duration(180)} exiting={FadeOut.duration(150)}

// Sheet present (Style / Share / New)
// @gorhom/bottom-sheet with spring config { damping: 18, stiffness: 180 }

// FAB / button press — scale 0.98 + opacity 0.92 on pressed

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // to-do, toggle
Haptics.selectionAsync();                                  // / inserter open
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Search | `search` |
| Documents | `document-text` |
| Profile | `person-circle` |
| Add (FAB) | `add` |
| Back | `chevron-back` |
| Page actions | `ellipsis-horizontal` |
| Card chevron | `chevron-forward` |
| To-do checked | `checkmark` |
| Toggle | `chevron-forward` (rotate 90°) |
| Copy | `copy-outline` |
| Share | `share-outline` |
| Link to page | `link` |
| Daily Note | `calendar-outline` |
| Page block | `document-outline` |
| Image block | `image-outline` |
| Style sheet | `brush-outline` |
| Code | `code-slash` |

## 7. Platform Notes

- **Fonts**: Inter, Lora, JetBrains Mono are all SIL OFL — free to bundle. Ship Inter (UI/body), Lora (optional serif reading mode), JetBrains Mono (code only)
- **Brand gradient**: always render via `expo-linear-gradient` with `start={{x:0,y:0}} end={{x:1,y:1}}` — never approximate with a solid color on primary actions
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark
- **Safe area**: wrap screens in `SafeAreaView`; the FAB must clear the home indicator
- **Dynamic Type**: `<Text>` respects system scale; set `allowFontScaling={false}` on tab labels, card subtitles, slash-menu rows, sidebar labels
- **Keyboard**: `KeyboardAvoidingView` on the editor so the `/` inserter floats above the keyboard
- **Dark mode**: `useColorScheme()` swaps tokens to `darkCanvas` / `darkCard` / `darkDivider`; the gradient is unchanged
- **Shadows**: iOS uses `shadow*` props; on Android use `elevation` — keep them soft (the card shadow is intentionally low-opacity and diffuse)
- **Drag-to-reorder blocks**: `react-native-draggable-flatlist`; lift with slight scale-up + shadow bloom, soft haptic on drop
- **Accessibility**: every block reachable as an accessible element; expose block-actions (Style, Move, Delete) via `accessibilityActions` so the long-press menu has a VoiceOver equivalent
