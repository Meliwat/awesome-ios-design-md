# VSCO (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates VSCO's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated / Gesture-Handler / Haptics snippets for the signature hairline slider, film-preset carousel, and tool tray.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-reanimated` v3, and `react-native-gesture-handler` v2.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Editor surfaces (dark — the editor's only mode)
  black:     '#000000',
  surface1:  '#0E0E0E',
  surface2:  '#1A1A1A',
  surface3:  '#242424',
  divider:   '#2A2A2A',
  track:     '#3A3A3A',

  // Browsing screens — light theme (NOT the editor)
  lightCanvas:   '#FFFFFF',
  lightSurface1: '#F2F2F2',
  lightSurface2: '#E8E8E8',
  lightDivider:  '#DCDCDC',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#B3B3B3',
  textTertiary:  '#767676',
  textOnLight:   '#000000',

  // Interactive — white only (VSCO has NO accent color)
  white:        '#FFFFFF',
  whitePressed: '#DBDBDB',

  // Incidental film tones (preset-preview content only — never UI fills)
  filmWarm:    '#C8A86B',
  filmCool:    '#7E8FA1',
  filmNeutral: '#9A9388',
  filmShadow:  '#3F382C',

  // Semantic
  destructive: '#E5484D',
} as const;

export type VSCOColor = keyof typeof colors;
```

## 2. Typography

VSCO's brand face is the proprietary "VSCO Gothic". Bundle **Inter** (SIL OFL) as the closest free substitute. The signature is UPPERCASE chrome with wide `letterSpacing`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false, contentStyle: { backgroundColor: '#000000' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  // Editorial (sentence-case content screens)
  display: { ...white, fontFamily: 'Inter-Bold',     fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  title:   { ...white, fontFamily: 'Inter-SemiBold', fontSize: 24, lineHeight: 29, letterSpacing: -0.2 },
  section: { ...white, fontFamily: 'Inter-SemiBold', fontSize: 19, lineHeight: 25, letterSpacing: -0.1 },
  body:    { ...white, fontFamily: 'Inter-Regular',  fontSize: 16, lineHeight: 24 },
  label:   { ...white, fontFamily: 'Inter-Medium',   fontSize: 14, lineHeight: 20 },
  caption: { color: '#B3B3B3', fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 18 },

  // Live numeric value
  value:   { ...white, fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14, fontVariant: ['tabular-nums'] as const },

  // Chrome — UPPERCASE + wide tracking (set textTransform on the <Text>)
  toolLabel: { color: '#B3B3B3', fontFamily: 'Inter-SemiBold', fontSize: 12, letterSpacing: 1.6, textTransform: 'uppercase' as const },
  presetTag: { color: '#B3B3B3', fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 1.0, textTransform: 'uppercase' as const },
  navAction: { ...white, fontFamily: 'Inter-SemiBold', fontSize: 13, letterSpacing: 1.4, textTransform: 'uppercase' as const },
  topTitle:  { color: '#B3B3B3', fontFamily: 'Inter-Medium', fontSize: 13, letterSpacing: 1.4, textTransform: 'uppercase' as const },
  tabLabel:  { color: '#767676', fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.6, textTransform: 'uppercase' as const },
  button:    { color: '#000000', fontFamily: 'Inter-SemiBold', fontSize: 12, letterSpacing: 1.4, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Hairline Bipolar Slider (the core atom)

```tsx
// components/HairlineSlider.tsx
import { useState } from 'react';
import { LayoutChangeEvent, Text, View } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useAnimatedStyle, useSharedValue, withTiming, runOnJS } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function HairlineSlider({
  toolName, value, onChange, min = -6, max = 6, defaultValue = 0,
}: {
  toolName: string;
  value: number;
  onChange: (v: number) => void;
  min?: number; max?: number; defaultValue?: number;
}) {
  const [w, setW] = useState(0);
  const knobX = useSharedValue(0);

  const onLayout = (e: LayoutChangeEvent) => {
    const width = e.nativeEvent.layout.width;
    setW(width);
    knobX.value = ((value - min) / (max - min)) * width;
  };

  const setVal = (px: number) => {
    const clamped = Math.max(0, Math.min(w, px));
    onChange(min + (clamped / w) * (max - min));
  };

  const pan = Gesture.Pan()
    .onChange((e) => {
      'worklet';
      const x = Math.max(0, Math.min(w, e.x));
      knobX.value = x;
      runOnJS(setVal)(x);
    });

  const tap = Gesture.Tap().numberOfTaps(2).onEnd(() => {
    'worklet';
    knobX.value = withTiming(((defaultValue - min) / (max - min)) * w, { duration: 200 });
    runOnJS(onChange)(defaultValue);
    runOnJS(Haptics.impactAsync)(Haptics.ImpactFeedbackStyle.Soft);
  });

  const zeroX = ((0 - min) / (max - min)) * w;
  const fillStyle = useAnimatedStyle(() => ({
    left: Math.min(knobX.value, zeroX),
    width: Math.abs(knobX.value - zeroX),
  }));
  const knobStyle = useAnimatedStyle(() => ({ transform: [{ translateX: knobX.value - 9 }] }));

  return (
    <View style={{ paddingHorizontal: 24 }}>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginBottom: 12 }}>
        <Text style={typography.toolLabel}>{toolName}</Text>
        <Text style={typography.value}>{value >= 0 ? `+${value.toFixed(1)}` : value.toFixed(1)}</Text>
      </View>

      <GestureDetector gesture={Gesture.Exclusive(tap, pan)}>
        <View onLayout={onLayout} style={{ height: 44, justifyContent: 'center' }}>
          <View style={{ height: 1, backgroundColor: colors.track }} />
          <Animated.View style={[{ position: 'absolute', height: 1, backgroundColor: colors.white }, fillStyle]} />
          <Animated.View
            style={[{
              position: 'absolute', width: 18, height: 18, borderRadius: 9,
              backgroundColor: colors.white,
              shadowColor: '#000', shadowOpacity: 0.6, shadowRadius: 4, shadowOffset: { width: 0, height: 1 },
              elevation: 4,
            }, knobStyle]}
          />
        </View>
      </GestureDetector>
    </View>
  );
}
```

### Film-Preset Carousel (the signature)

```tsx
// components/PresetCarousel.tsx
import { ScrollView, Text, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Preset = { id: string; code: string; preview: [string, string] };

export function PresetCarousel({
  presets, selectedId, onSelect,
}: { presets: Preset[]; selectedId: string | null; onSelect: (id: string) => void }) {
  return (
    <ScrollView
      horizontal
      showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ paddingHorizontal: 20, paddingVertical: 14, gap: 14 }}
    >
      {presets.map((p) => {
        const active = p.id === selectedId;
        return (
          <Pressable
            key={p.id}
            onPress={() => { onSelect(p.id); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); }}
            style={{ alignItems: 'center', gap: 7 }}
          >
            <LinearGradient
              colors={p.preview}
              start={{ x: 0, y: 0 }}
              end={{ x: 1, y: 1 }}
              style={{
                width: 56, height: 56, borderRadius: 2,
                borderWidth: active ? 2 : 0, borderColor: colors.white,
              }}
            />
            <Text style={[typography.presetTag, active && { color: colors.textPrimary }]}>{p.code}</Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

### Tool Tray

```tsx
// components/ToolTray.tsx
import { ScrollView, Text, Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Tool = { id: string; icon: keyof typeof Ionicons.glyphMap; label: string };

export function ToolTray({
  tools, activeId, onSelect,
}: { tools: Tool[]; activeId: string | null; onSelect: (id: string) => void }) {
  return (
    <View style={{ height: 84, borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: colors.black }}>
      <ScrollView horizontal showsHorizontalScrollIndicator={false}
        contentContainerStyle={{ paddingHorizontal: 26, gap: 30, alignItems: 'center', height: 84 }}>
        {tools.map((t) => {
          const active = t.id === activeId;
          const tint = active ? colors.textPrimary : colors.textSecondary;
          return (
            <Pressable key={t.id} onPress={() => onSelect(t.id)}
              style={{ alignItems: 'center', gap: 8, minWidth: 44 }}>
              <Ionicons name={t.icon} size={22} color={tint} />
              <Text style={[typography.toolLabel, { color: tint }]}>{t.label}</Text>
            </Pressable>
          );
        })}
      </ScrollView>
    </View>
  );
}
```

### Editor Top Bar

```tsx
// components/EditorTopBar.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function EditorTopBar({ onClose, onNext }: { onClose: () => void; onNext: () => void }) {
  return (
    <View style={{
      height: 52, flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingHorizontal: 20, backgroundColor: colors.black,
    }}>
      <Pressable onPress={onClose} hitSlop={12}>
        <Ionicons name="close" size={26} color={colors.textPrimary} />
      </Pressable>
      <Text style={typography.topTitle}>Edit</Text>
      <Pressable onPress={onNext} hitSlop={12}>
        <Text style={typography.navAction}>Next</Text>
      </Pressable>
    </View>
  );
}
```

### Studio Grid (gapless contact sheet)

```tsx
// components/StudioGrid.tsx
import { FlatList, Image, useWindowDimensions } from 'react-native';
import { colors } from '../theme/colors';

export function StudioGrid({ photos }: { photos: string[] }) {
  const { width } = useWindowDimensions();
  const cols = 4;
  const size = (width - (cols - 1)) / cols;   // 1px hairline gutter

  return (
    <FlatList
      data={photos}
      numColumns={cols}
      keyExtractor={(u, i) => `${u}-${i}`}
      style={{ backgroundColor: colors.surface1 }}
      columnWrapperStyle={{ gap: 1 }}
      contentContainerStyle={{ gap: 1 }}
      renderItem={({ item }) => (
        <Image source={{ uri: item }} style={{ width: size, height: size }} />  // 0 radius — negatives
      )}
    />
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
        tabBarShowLabel: false,                       // VSCO tabs are icon-only
        tabBarActiveTintColor: colors.white,          // white fill, NO tint pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: colors.black,
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
      }}
    >
      <Tabs.Screen name="studio"   options={{ tabBarIcon: ({ color }) => <Ionicons name="grid"   size={23} color={color} /> }} />
      <Tabs.Screen name="feed"     options={{ tabBarIcon: ({ color }) => <Ionicons name="arrow-up" size={23} color={color} /> }} />
      <Tabs.Screen name="discover" options={{ tabBarIcon: ({ color }) => <Ionicons name="camera" size={23} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ tabBarIcon: ({ color }) => <Ionicons name="person" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import { withTiming, FadeIn, FadeOut } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Preset apply — photo cross-dissolves between grades over 250ms
// (render two stacked Image layers; animate top opacity 0→1)
opacity.value = withTiming(1, { duration: 250 });

// Slider double-tap reset — knob springs/timing back over 200ms (see HairlineSlider tap gesture)
knobX.value = withTiming(targetX, { duration: 200 });

// Tool switch — slider fades in / carousel slides out (8pt offset)
// entering={FadeIn.duration(180)} exiting={FadeOut.duration(180)}

// Before/after press — INSTANT swap (no animation; immediacy is the point)
onPressIn={() => setShowOriginal(true)} onPressOut={() => setShowOriginal(false)}

// Tab switch — cross-fade only (default expo-router; avoid slide)

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);     // preset select, double-tap reset, multi-select
Haptics.selectionAsync();                                   // zero-detent tick while dragging
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). VSCO's tool glyphs are custom thin-line; Ionicons outline variants are the closest free match.

| Purpose | Ionicons |
|---------|----------|
| Close editor | `close` |
| Filters tool | `color-filter-outline` |
| Exposure tool | `sunny-outline` |
| Contrast tool | `contrast-outline` |
| Saturation tool | `water-outline` |
| Sharpen tool | `triangle-outline` |
| Crop tool | `crop-outline` |
| Straighten tool | `refresh-outline` |
| Grain tool | `apps-outline` |
| HSL tool | `color-palette-outline` |
| Studio (tab) | `grid` |
| Feed (tab) | `arrow-up` |
| Discover (tab) | `camera` |
| Profile (tab) | `person` |
| Import | `download-outline` |
| Settings | `settings-outline` |
| Recipe | `add-outline` |
| Multi-select check | `checkmark` |
| Share | `share-outline` |

## 7. Platform Notes

- **Font choice**: bundle Inter (SIL OFL, free) as the substitute for the proprietary VSCO Gothic — neutral grotesque, ideal for wide-tracked UPPERCASE chrome
- **The editor is dark-native**: never theme the editor light. Force `<StatusBar style="light" />` and a `#000000` content background app-wide for the editing flow; only the Studio/Discover/Profile browse screens may follow `useColorScheme()`
- **No accent color**: there is nothing to tint — selection is a white ring / white fill / label color change. Do not introduce a brand color
- **Status bar**: `<StatusBar style="light" />` in the editor and on dark browse; `"dark"` only on the (rare) light browse theme
- **Safe area**: wrap the editor in `SafeAreaView`; the photo stage shrinks to fit between safe areas and is never occluded by the top bar, tool tray, or home indicator
- **Gesture handling**: the hairline slider must use `react-native-gesture-handler` `Pan` + `Tap` (double-tap reset) composed with `Gesture.Exclusive`; the before/after uses `onPressIn`/`onPressOut` for an instant swap
- **Dynamic Type**: RN `<Text>` honors system scale — set `allowFontScaling={false}` on tool labels, preset codes, slider values, nav actions, and tab labels (single-line, layout-critical); allow scaling on editorial titles/body
- **Studio grid performance**: use `FlatList` with `getItemLayout` for the gapless contact sheet; thumbnails are square, 0 radius, 1px gutter
- **Slider knob shadow**: keep `shadowOpacity: 0.6, shadowRadius: 4` (+ `elevation: 4` on Android) — it is functional so the white knob stays visible over bright photo regions
- **Accessibility**: give the slider `accessibilityRole="adjustable"` with `accessibilityValue` + `onAccessibilityAction` for increment/decrement; preset tiles get `accessibilityState={{ selected }}`; the before/after long-press needs an `accessibilityActions` "Show original" equivalent
