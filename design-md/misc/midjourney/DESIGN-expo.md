# Midjourney (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Midjourney's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets. Midjourney is dark-only, true-black, with no brand accent — white is the only primary.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-only — true OLED black)
  canvas:    '#000000',
  surface1:  '#0E0E0E',
  surface2:  '#1A1A1A',
  surface3:  '#242424',
  divider:   '#2A2A2A',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#A1A1A1',
  textTertiary:  '#6E6E6E',

  // Primary — white is the ONLY primary (no brand accent)
  primary:        '#FFFFFF',
  primaryPressed: '#DADADA',

  // Heritage / link
  rateBlue: '#2D7FF9',
  blurple:  '#4D5BCE', // Discord connect only

  // Semantic
  success: '#2ECC71',
  warning: '#F5A623',
  error:   '#E5484D',
} as const;

export type MJColor = keyof typeof colors;
```

## 2. Typography

Load Inter via `expo-font` for the UI; the platform monospace renders prompts and parameters.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':    require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':     require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':   require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':       require('../assets/fonts/Inter-Bold.ttf'),
    'Inter-ExtraBold':  require('../assets/fonts/Inter-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import { Platform, type TextStyle } from 'react-native';

const mono = Platform.select({ ios: 'Menlo', default: 'monospace' }) as string;
const primary = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  navTitle:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  section:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 19, lineHeight: 24, letterSpacing: -0.2 },
  body:        { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  action:      { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 15 },
  cardTitle:   { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  meta:        { color: '#A1A1A1', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },
  caption:     { color: '#A1A1A1', fontFamily: 'Inter-Medium',  fontSize: 12, lineHeight: 16 },
  tab:         { color: '#6E6E6E', fontFamily: 'Inter-Medium',  fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  placeholder: { color: '#6E6E6E', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20 },

  // Monospace — the "this is an instruction" voice
  prompt:  { color: '#A1A1A1', fontFamily: mono, fontSize: 13, lineHeight: 18 },
  param:   { color: '#FFFFFF', fontFamily: mono, fontSize: 13, lineHeight: 16, fontWeight: '600' as const },
  jobChip: { fontFamily: mono, fontSize: 13, lineHeight: 16, fontWeight: '600' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Job Card (prompt + 2×2 grid + action row)

```tsx
// components/JobCard.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { UpscaleChip } from './Chips';

export function JobCard({
  promptKey, promptRest, cells, onUpscale, onFavorite, onDownload,
}: {
  promptKey: string; promptRest: string;
  cells: React.ReactNode[];          // 4 image nodes
  onUpscale: (n: number) => void;
  onFavorite: () => void;
  onDownload: () => void;
}) {
  return (
    <View style={{ paddingHorizontal: 14, paddingVertical: 9, backgroundColor: colors.canvas }}>
      <Text style={typography.prompt} numberOfLines={2}>
        <Text style={{ color: colors.textPrimary }}>{promptKey}</Text>{promptRest}
      </Text>

      <View style={{ marginTop: 10, borderRadius: 12, overflow: 'hidden',
                     flexDirection: 'row', flexWrap: 'wrap', gap: 6 }}>
        {cells.map((c, i) => (
          <View key={i} style={{ width: '48%', aspectRatio: 1 }}>{c}</View>
        ))}
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 14, marginTop: 10 }}>
        <Pressable onPress={onFavorite} hitSlop={8}>
          <Ionicons name="heart-outline" size={18} color={colors.textSecondary} />
        </Pressable>
        <Pressable onPress={onDownload} hitSlop={8}>
          <Ionicons name="download-outline" size={18} color={colors.textSecondary} />
        </Pressable>
        <Pressable hitSlop={8}>
          <Ionicons name="ellipsis-horizontal" size={18} color={colors.textSecondary} />
        </Pressable>
        <View style={{ flex: 1 }} />
        {[1, 2, 3, 4].map((n) => (
          <UpscaleChip key={n} label={`U${n}`} onPress={() => onUpscale(n)} />
        ))}
      </View>
    </View>
  );
}
```

### Upscale / Variation Chips (the Discord-bot grammar)

```tsx
// components/Chips.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function UpscaleChip({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.primaryPressed : colors.primary,
        borderRadius: 8, paddingHorizontal: 14, paddingVertical: 8,
        transform: [{ scale: pressed ? 0.96 : 1 }],
      })}
    >
      <Text style={[typography.jobChip, { color: '#000000' }]}>{label}</Text>
    </Pressable>
  );
}

export function VariationChip({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.surface3 : colors.surface2,
        borderRadius: 8, borderWidth: 1, borderColor: colors.divider,
        paddingHorizontal: 14, paddingVertical: 8,
      })}
    >
      <Text style={[typography.jobChip, { color: colors.textPrimary }]}>{label}</Text>
    </Pressable>
  );
}
```

### Prompt Bar (signature input)

```tsx
// components/PromptBar.tsx
import { useState } from 'react';
import { View, TextInput, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PromptBar({ value, onChangeText, onImagine }: {
  value: string; onChangeText: (t: string) => void; onImagine: () => void;
}) {
  const [focused, setFocused] = useState(false);
  const empty = value.trim().length === 0;
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 10, height: 50,
      marginHorizontal: 14, borderRadius: 25, backgroundColor: colors.surface2,
      borderWidth: 1, borderColor: focused ? '#3A3A3A' : colors.divider,
      paddingLeft: 18, paddingRight: 7,
    }}>
      <TextInput
        value={value}
        onChangeText={onChangeText}
        onFocus={() => setFocused(true)}
        onBlur={() => setFocused(false)}
        placeholder="What will you imagine?"
        placeholderTextColor={colors.textTertiary}
        style={[typography.prompt, { flex: 1, color: colors.textPrimary }]}
      />
      <Pressable
        disabled={empty}
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onImagine(); }}
        style={{
          width: 36, height: 36, borderRadius: 18,
          backgroundColor: empty ? colors.surface3 : colors.primary,
          alignItems: 'center', justifyContent: 'center',
        }}
      >
        <Ionicons name="arrow-forward" size={16} color="#000000" />
      </Pressable>
    </View>
  );
}
```

### Parameter Helper Strip

```tsx
// components/ParameterStrip.tsx
import { ScrollView, Pressable, Text } from 'react-native';
import Animated, { FadeInDown, FadeOutDown } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const FLAGS = ['--ar 1:1', '--ar 16:9', '--v 6', '--style raw', '--stylize 250'];

export function ParameterStrip({ onPick }: { onPick: (f: string) => void }) {
  return (
    <Animated.View entering={FadeInDown.duration(180)} exiting={FadeOutDown.duration(150)}>
      <ScrollView horizontal showsHorizontalScrollIndicator={false}
        contentContainerStyle={{ paddingHorizontal: 14, gap: 8 }}>
        {FLAGS.map((f) => (
          <Pressable key={f} onPress={() => onPick(f)} style={{
            backgroundColor: colors.surface2, borderRadius: 8,
            borderWidth: 1, borderColor: colors.divider,
            paddingHorizontal: 14, paddingVertical: 8,
          }}>
            <Text style={typography.param}>{f}</Text>
          </Pressable>
        ))}
      </ScrollView>
    </Animated.View>
  );
}
```

### Render-Progress Placeholder

```tsx
// components/RenderingGrid.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function RenderingGrid({ percent }: { percent: number }) {
  const x = useSharedValue(0);
  useEffect(() => { x.value = withRepeat(withTiming(1, { duration: 1400 }), -1, true); }, []);
  const style = useAnimatedStyle(() => ({ opacity: 0.6 + x.value * 0.4 }));
  return (
    <View style={{ aspectRatio: 1, borderRadius: 12, overflow: 'hidden', justifyContent: 'center', alignItems: 'center' }}>
      <Animated.View style={[{ ...StyleSheetAbsolute }, style]}>
        <LinearGradient colors={[colors.surface2, colors.surface3, colors.surface2]}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }} />
      </Animated.View>
      <Text style={typography.meta}>{percent}%</Text>
    </View>
  );
}
const StyleSheetAbsolute = { position: 'absolute' as const, top: 0, left: 0, right: 0, bottom: 0 };
```

### Primary Button (Imagine — white is the only primary)

```tsx
// components/ImagineButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ImagineButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.primaryPressed : colors.primary,
      borderRadius: 999, paddingHorizontal: 28, paddingVertical: 14,
      alignItems: 'center', transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={[typography.action, { color: '#000000' }]}>{title}</Text>
    </Pressable>
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
        headerShown: false,
        tabBarActiveTintColor:   colors.textPrimary,   // white — NO tint pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: colors.canvas,               // true black
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Create',  tabBarIcon: ({ color }) => <Ionicons name="home"             size={23} color={color} /> }} />
      <Tabs.Screen name="explore" options={{ title: 'Explore', tabBarIcon: ({ color }) => <Ionicons name="grid"             size={23} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"           size={23} color={color} /> }} />
      <Tabs.Screen name="library" options={{ title: 'Library', tabBarIcon: ({ color }) => <Ionicons name="images"           size={23} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person-circle"    size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Job submit — new card slides in at top, Imagine pulse
import Animated, { FadeInUp, withSequence, withTiming } from 'react-native-reanimated';
// <Animated.View entering={FadeInUp.duration(250)}> ...job card... </Animated.View>
// Imagine pulse: scale withSequence(withTiming(0.96,{duration:60}), withTiming(1,{duration:60}))

// Render shimmer — 1.4s repeating sweep (see RenderingGrid)
// withRepeat(withTiming(1,{duration:1400}), -1, true)

// Resolve — placeholder crossfades to images
// entering={FadeIn.duration(250)}

// Lightbox paging — 1:1 PanGestureHandler, spring on release
// withSpring(target, { damping: 18, stiffness: 160 })

// Tab change — instant color swap, NO slide

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);    // Imagine submit
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // chip tap
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // job complete
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Midjourney's UI is icon-light; the brand lives in the grid and the monospace `--flag` grammar, not iconography.

| Purpose | Ionicons |
|---------|----------|
| Create (tab) | `home` |
| Explore (tab) | `grid` |
| Search (tab) | `search` |
| Library (tab) | `images` |
| Profile (tab) | `person-circle` |
| Imagine (send) | `arrow-forward` |
| Favorite | `heart-outline` / `heart` |
| Download | `download-outline` |
| More | `ellipsis-horizontal` |
| Filter | `options-outline` |
| Re-roll | `refresh` |
| Zoom out | `scan-outline` |
| Remix | `color-wand-outline` |
| Vary | `copy-outline` |
| Back | `chevron-back` |
| Settings | `settings-outline` |
| Discord connect | `logo-discord` |
| Close lightbox | `close` |

## 7. Platform Notes

- **Dark-only**: never expose a light theme. Set the root background to `#000000` (OLED black) and lock `<StatusBar style="light" />`. Do not substitute `#0E0E0E` for the canvas — true black is the brand.
- **No brand accent**: white (`#FFFFFF`) is the only primary. Never add a tinted active-tab pill or a colored primary button. Reserve `#4D5BCE` blurple strictly for the "Connect Discord" row.
- **Monospace prompts**: always render prompts and `--flags` in the platform monospace (`Menlo` on iOS) — never the Inter sans face. This is a load-bearing brand signal.
- **Imagery is content**: render generated images full-bleed and full-color via `expo-image`; never apply a `tintColor`, overlay, or color filter for "theming".
- **Status bar**: `<StatusBar style="light" />` always (dark-only product).
- **Safe area**: wrap screens in `SafeAreaView`; the prompt bar and tab bar need bottom safe-area padding; the lightbox ignores insets (full-bleed).
- **Dynamic Type**: keep `allowFontScaling={false}` on tab labels, job chips (U1/V1), parameter chips, and prompt monospace — the 2×2 grid layout is size-sensitive. Let titles, body, metadata scale.
- **Keyboard**: use `KeyboardAvoidingView` on the composer so the prompt bar and parameter helper strip sit above the keyboard.
- **Grid aspect**: drive the job-grid container's `aspectRatio` from the prompt's `--ar` flag (1 for 1:1, 1.78 for 16:9, 0.56 for 9:16) — the 2×2 structure never changes, only the container shape.
- **Performance**: virtualize the job feed with `FlashList`; use `expo-image` with `recyclingKey` and `contentFit="cover"` for grid cells; the Explore masonry should lazy-load.
- **Accessibility**: label cells "Image {n} of 4"; expose U/V chips with `accessibilityLabel` ("Upscale image {n}" / "Variations of image {n}"); honor `AccessibilityInfo.isReduceMotionEnabled` to drop the shimmer/spring.
- **Reduce transparency**: replace any tab-bar blur with solid `#000000`.
