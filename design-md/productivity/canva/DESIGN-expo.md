# Canva (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Canva's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  white:         '#FFFFFF',
  workspace:     '#F7F8FA',
  pressedLight:  '#EEF0F4',
  dividerLight:  '#E6E8EC',

  // Surfaces (dark)
  canvasDark:    '#18191B',
  surface1:      '#1F2023',
  surface2:      '#27282C',
  divider:       '#303135',

  // Text
  textPrimary:   '#ECECEE', // dark
  textPrimaryLt: '#0D0E10', // light
  textSecondary: '#A4A6AD',
  textTertiary:  '#6F7178',

  // Brand
  cyan:          '#00C4CC',
  purple:        '#7D2AE8',
  blue:          '#3B5CFF',
  purplePressed: '#6A1FD0',

  // Semantic
  proGold:       '#FFC24B',
  success:       '#1FC77C',
  error:         '#FF5163',
} as const;

export type CanvaColor = keyof typeof colors;

// Gradients (expo-linear-gradient props)
export const brandGradient = {
  colors: [colors.cyan, colors.purple] as const,
  start: { x: 0, y: 0 }, end: { x: 1, y: 1 },
};
export const tileGradients = {
  instagram:    { colors: ['#00C4CC', '#3B5CFF'] as const, start: { x: 0, y: 0 }, end: { x: 1, y: 1 } },
  presentation: { colors: ['#7D2AE8', '#C13AE0'] as const, start: { x: 0, y: 0 }, end: { x: 1, y: 1 } },
  doc:          { colors: ['#FF7A59', '#FFC24B'] as const, start: { x: 0, y: 0 }, end: { x: 1, y: 1 } },
  whiteboard:   { colors: ['#1FC77C', '#00C4CC'] as const, start: { x: 0, y: 0 }, end: { x: 1, y: 1 } },
  video:        { colors: ['#3B5CFF', '#7D2AE8'] as const, start: { x: 0, y: 0 }, end: { x: 1, y: 1 } },
};
```

## 2. Typography

Canva Sans stand-in: **Plus Jakarta Sans** (SIL OFL). Load via `expo-font`. App chrome always uses this family.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'PJS-Regular':   require('../assets/fonts/PlusJakartaSans-Regular.ttf'),
    'PJS-Medium':    require('../assets/fonts/PlusJakartaSans-Medium.ttf'),
    'PJS-SemiBold':  require('../assets/fonts/PlusJakartaSans-SemiBold.ttf'),
    'PJS-Bold':      require('../assets/fonts/PlusJakartaSans-Bold.ttf'),
    'PJS-ExtraBold': require('../assets/fonts/PlusJakartaSans-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#ECECEE' } satisfies TextStyle;

export const typography = {
  display:    { ...primary, fontFamily: 'PJS-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  greeting:   { ...primary, fontFamily: 'PJS-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:    { ...primary, fontFamily: 'PJS-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  subsection: { ...primary, fontFamily: 'PJS-Bold',      fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:       { ...primary, fontFamily: 'PJS-Regular',   fontSize: 16, lineHeight: 24 },
  label:      { ...primary, fontFamily: 'PJS-SemiBold',  fontSize: 15, lineHeight: 20 },
  button:     { color: '#FFFFFF', fontFamily: 'PJS-Bold', fontSize: 16, lineHeight: 16 },
  meta:       { color: '#A4A6AD', fontFamily: 'PJS-Regular', fontSize: 14, lineHeight: 20 },
  chip:       { color: '#A4A6AD', fontFamily: 'PJS-SemiBold', fontSize: 12, lineHeight: 16, letterSpacing: 0.1 },
  tab:        { fontFamily: 'PJS-SemiBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  proBadge:   { color: '#1A1205', fontFamily: 'PJS-ExtraBold', fontSize: 9, lineHeight: 11, letterSpacing: 0.3 },
  tool:       { fontFamily: 'PJS-SemiBold', fontSize: 11, lineHeight: 14, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Home Header (Greeting + Avatar)

```tsx
// components/HomeHeader.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function HomeHeader({ name, initial }: { name: string; initial: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 18, paddingVertical: 4 }}>
      <Text style={typography.greeting}>Hi, {name} 👋</Text>
      <LinearGradient {...brandGradient} style={{ width: 34, height: 34, borderRadius: 17, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ color: '#FFF', fontFamily: 'PJS-Bold', fontSize: 14 }}>{initial}</Text>
      </LinearGradient>
    </View>
  );
}
```

### Search Field

```tsx
// components/SearchField.tsx
import { useState } from 'react';
import { TextInput, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SearchField() {
  const [q, setQ] = useState('');
  return (
    <View style={{ marginHorizontal: 18 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10, height: 46, borderRadius: 14, backgroundColor: colors.surface2, paddingHorizontal: 14 }}>
        <Ionicons name="search" size={17} color={colors.textSecondary} />
        <TextInput
          value={q}
          onChangeText={setQ}
          placeholder="Search templates, photos…"
          placeholderTextColor={colors.textSecondary}
          style={[typography.label, { flex: 1, fontFamily: 'PJS-Regular' }]}
        />
      </View>
    </View>
  );
}
```

### Design-Type Tile

```tsx
// components/DesignTypeTile.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { typography } from '../theme/typography';

export function DesignTypeTile({
  icon, label, gradient, onPress,
}: { icon: any; label: string; gradient: any; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ width: 76, alignItems: 'center', gap: 7, transform: [{ scale: pressed ? 0.96 : 1 }] })}>
      <LinearGradient {...gradient} style={{ width: 56, height: 56, borderRadius: 16, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name={icon} size={24} color="#FFF" />
      </LinearGradient>
      <Text style={typography.chip} numberOfLines={1}>{label}</Text>
    </Pressable>
  );
}
```

### Template Thumbnail (with PRO badge)

```tsx
// components/TemplateThumb.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TemplateThumb({ gradient, isPro, onPress }: { gradient: any; isPro?: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ flex: 1, aspectRatio: 3 / 4 }}>
      <LinearGradient {...gradient} style={{ flex: 1, borderRadius: 12 }} />
      {isPro ? (
        <View style={{ position: 'absolute', top: 7, right: 7, backgroundColor: colors.proGold, borderRadius: 6, paddingVertical: 2, paddingHorizontal: 6 }}>
          <Text style={typography.proBadge}>PRO</Text>
        </View>
      ) : null}
    </Pressable>
  );
}
```

### Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ opacity: pressed ? 0.93 : 1, transform: [{ scale: pressed ? 0.98 : 1 }] })}>
      <LinearGradient {...brandGradient} style={{ borderRadius: 999, paddingVertical: 14, paddingHorizontal: 28, alignItems: 'center' }}>
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}

export function SolidButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.purplePressed : colors.purple,
      borderRadius: 999, paddingVertical: 12, paddingHorizontal: 22, alignItems: 'center',
    })}>
      <Text style={[typography.button, { fontSize: 15 }]}>{title}</Text>
    </Pressable>
  );
}

export function ProButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ backgroundColor: colors.proGold, borderRadius: 999, paddingVertical: 10, paddingHorizontal: 18 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="star" size={12} color="#1A1205" />
        <Text style={[typography.proBadge, { fontSize: 14 }]}>{title}</Text>
      </View>
    </Pressable>
  );
}
```

### Create FAB

```tsx
// components/CreateFAB.tsx
import { Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brandGradient, colors } from '../theme/colors';

export function CreateFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        marginTop: -4,
        transform: [{ scale: pressed ? 0.92 : 1 }],
        shadowColor: colors.purple, shadowOpacity: 0.45, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
        elevation: 8,
      })}
    >
      <LinearGradient {...brandGradient} style={{ width: 46, height: 46, borderRadius: 16, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="add" size={26} color="#FFF" />
      </LinearGradient>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

The center slot is the gradient create FAB — lifted and shadowed (the one elevated element). Implement via a custom `tabBar`.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';
import { CreateFAB } from '../../components/CreateFAB';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.textPrimary,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarLabelStyle: { fontFamily: 'PJS-SemiBold', fontSize: 10, letterSpacing: 0.1 },
        tabBarBackground: () => <BlurView intensity={40} tint="dark" style={{ flex: 1 }} />,
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Home',      tabBarIcon: ({ color }) => <Ionicons name="home" size={22} color={color} /> }} />
      <Tabs.Screen name="templates" options={{ title: 'Templates', tabBarIcon: ({ color }) => <Ionicons name="grid" size={22} color={color} /> }} />
      <Tabs.Screen name="create"    options={{ title: '', tabBarButton: () => <CreateFAB onPress={() => {/* open sheet */}} /> }} />
      <Tabs.Screen name="projects"  options={{ title: 'Projects',  tabBarIcon: ({ color }) => <Ionicons name="albums" size={22} color={color} /> }} />
      <Tabs.Screen name="you"       options={{ title: 'You',       tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, FadeOut, SlideInDown, withTiming, withSpring } from 'react-native-reanimated';

// Create FAB tap → "Create a design" sheet
// <Animated.View entering={SlideInDown.duration(320)} exiting={FadeOut.duration(200)}>

// Design-type tile / FAB press scale (Pressable style transform above)

// Bottom sheet — use @gorhom/bottom-sheet; spring release damping ≈ 0.85
// snapPoints={['55%', '90%']}, enableDynamicSizing

// Editor element select — contextual toolbar
// entering={SlideInDown.duration(220)}

// Tab active pop
// scale.value = withTiming(active ? 1.05 : 1, { duration: 120 })

// Skeleton shimmer (template grid)
// translateX.value = withRepeat(withTiming(width, { duration: 1200 }), -1, false)

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);              // FAB, tile, element select
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // export / publish complete
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The brand gradient marks brand + primary action; Pro is gold.

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home` |
| Templates (tab) | `grid` |
| Create FAB (center) | `add` |
| Projects (tab) | `albums` |
| You (tab) | `person-circle` |
| Search | `search` |
| Pro / premium | `star` / `diamond` |
| Instagram type | `camera` |
| Presentation type | `easel` |
| Doc type | `document-text` |
| Whiteboard type | `grid-outline` |
| Video type | `play-circle` |
| Editor — back | `chevron-back` |
| Editor — undo / redo | `arrow-undo` / `arrow-redo` |
| Editor — share | `share-outline` |
| Tool — Elements | `shapes` |
| Tool — Text | `text` |
| Tool — Uploads | `cloud-upload-outline` |
| Tool — Photos | `image` |
| Tool — Draw | `brush` |
| Element — color | `color-palette` |
| Element — animate | `sparkles` |
| Element — delete | `trash` |

## 7. Platform Notes

- **Font choice**: Plus Jakarta Sans (SIL OFL, free) is the Canva Sans stand-in — bundle it; app chrome always uses it. The hundreds of in-editor content fonts are design data, loaded on demand for the canvas only
- **Status bar**: `<StatusBar style="light" />` on the dark canvas, `"dark"` on light
- **Safe area**: wrap screens in `SafeAreaView`; the tab bar + create FAB and bottom sheets need bottom safe-area padding; the contextual element toolbar sits above the keyboard
- **Dynamic Type**: `<Text>` honors system scale — set `allowFontScaling={false}` on tab labels (10pt), chip labels (12pt), the Pro badge (9pt), tool labels (11pt)
- **LinearGradient**: use `expo-linear-gradient` for the brand gradient, design-type tiles and the create FAB; keep the 135° angle (`start {0,0}` → `end {1,1}`)
- **Bottom sheets**: use `@gorhom/bottom-sheet` for the create-design chooser, tool pickers and share flow — rounded 24pt top corners, grab handle, dimmed backdrop
- **Keyboard**: `KeyboardAvoidingView` on project-name / search; the editor's contextual element toolbar must track above the keyboard
- **Dark mode**: `useColorScheme()` swaps to `canvasDark` / `surface1`; never desaturate content thumbnails — only chrome dims; sheets add a faint 1pt `#303135` top border
- **Accessibility**: label the create FAB `accessibilityLabel="Create a design"`; template thumbs get the template name + a "Pro" suffix when locked; ensure white text over the cyan end of gradients keeps AA contrast (add a dark scrim if needed)
- **Performance**: virtualize template grids with `FlashList`; show shimmering rounded placeholders while thumbnails fetch
