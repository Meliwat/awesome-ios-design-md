# Pinterest (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pinterest's visual language into paste-ready Expo / React Native code: a design-token module, the masonry grid, Save button, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  red:        '#E60023',
  redPressed: '#AD081B',
  redHover:   '#CC0020',

  // Light canvas
  canvas:    '#FFFFFF',
  surface1:  '#F8F8F8',
  input:     '#EFEFEF',
  divider:   '#E9E9E9',

  // Dark canvas
  canvasDark:   '#121212',
  surface1Dark: '#1E1E1E',
  surface2Dark: '#2A2A2A',
  dividerDark:  '#2E2E2E',

  // Text — light
  textPrimary:   '#111111',
  textSecondary: '#767676',
  textTertiary:  '#B5B5B5',

  // Text — dark
  textPrimaryDark:   '#FFFFFF',
  textSecondaryDark: '#AAAAAA',

  // Semantic
  success: '#008A3C',
  info:    '#0074E8',
} as const;

export type PinterestColor = keyof typeof colors;
```

## 2. Typography

Load Pinterest Sans via `expo-font`. Fall back to `System` which is SF Pro on iOS.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'PinterestSans-Regular':  require('../assets/fonts/PinterestSans-Regular.ttf'),
    'PinterestSans-Medium':   require('../assets/fonts/PinterestSans-Medium.ttf'),
    'PinterestSans-Semibold': require('../assets/fonts/PinterestSans-Semibold.ttf'),
    'PinterestSans-Bold':     require('../assets/fonts/PinterestSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#111111' } satisfies TextStyle;

export const typography = {
  pinDetailTitle:  { ...base, fontFamily: 'PinterestSans-Bold',     fontSize: 28, lineHeight: 34, letterSpacing: -0.3 },
  largeTitle:      { ...base, fontFamily: 'PinterestSans-Bold',     fontSize: 24, lineHeight: 29, letterSpacing: -0.2 },
  sectionHeader:   { ...base, fontFamily: 'PinterestSans-Bold',     fontSize: 20, lineHeight: 25, letterSpacing: -0.1 },
  boardName:       { ...base, fontFamily: 'PinterestSans-Bold',     fontSize: 17, lineHeight: 21 },
  pinTitle:        { ...base, fontFamily: 'PinterestSans-Medium',   fontSize: 14, lineHeight: 18 },
  username:        { ...base, fontFamily: 'PinterestSans-Semibold', fontSize: 14, lineHeight: 18 },
  body:            { ...base, fontFamily: 'PinterestSans-Regular',  fontSize: 15, lineHeight: 22 },
  comment:         { ...base, fontFamily: 'PinterestSans-Regular',  fontSize: 14, lineHeight: 20 },
  meta:            { fontFamily: 'PinterestSans-Regular',  fontSize: 12, lineHeight: 16, color: '#767676' },
  button:          { fontFamily: 'PinterestSans-Bold',     fontSize: 16, lineHeight: 20, color: '#FFFFFF' },
  buttonSmall:     { fontFamily: 'PinterestSans-Semibold', fontSize: 14, lineHeight: 18, color: '#111111' },
  tabLabel:        { fontFamily: 'PinterestSans-Semibold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  micro:           { fontFamily: 'PinterestSans-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.4, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Save Button (The Red → Black Morph)

```tsx
// components/SaveButton.tsx
import { Pressable, Text } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming,
  interpolateColor,
  useDerivedValue,
} from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SaveButton({
  saved,
  onToggle,
}: { saved: boolean; onToggle: () => void }) {
  const scale = useSharedValue(1);
  const progress = useDerivedValue(() => withTiming(saved ? 1 : 0, { duration: 200 }));

  const style = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    backgroundColor: interpolateColor(progress.value, [0, 1], [colors.red, colors.textPrimary]),
  }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.97, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
        onToggle();
      }}
    >
      <Animated.View
        style={[
          { paddingHorizontal: 20, paddingVertical: 10, borderRadius: 500 },
          style,
        ]}
      >
        <Text style={[typography.button, { color: '#FFFFFF' }]}>
          {saved ? 'Saved' : 'Save'}
        </Text>
      </Animated.View>
    </Pressable>
  );
}
```

### Pill Button (Follow / Primary CTA)

```tsx
// components/PillButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PillButton({
  title, variant = 'primary', onPress,
}: { title: string; variant?: 'primary' | 'secondary'; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 12,
        paddingHorizontal: 24,
        borderRadius: 500,
        backgroundColor: variant === 'primary' ? colors.red : colors.input,
        opacity: pressed ? 0.9 : 1,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={[
        typography.button,
        { color: variant === 'primary' ? '#FFFFFF' : colors.textPrimary },
      ]}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Pin Tile

```tsx
// components/PinTile.tsx
import { Image, Pressable, Text, View, StyleSheet } from 'react-native';
import { SaveButton } from './SaveButton';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { useState } from 'react';

type Props = {
  imageUrl: string;
  title: string;
  creator: string;
  creatorAvatar: string;
  aspectRatio: number; // width / height
  width: number;
  onPress: () => void;
};

export function PinTile({ imageUrl, title, creator, creatorAvatar, aspectRatio, width, onPress }: Props) {
  const [saved, setSaved] = useState(false);

  return (
    <Pressable onPress={onPress} style={{ marginBottom: 8 }}>
      <View style={[styles.imageContainer, { width, height: width / aspectRatio }]}>
        <Image source={{ uri: imageUrl }} style={styles.image} />
        <View style={styles.saveOverlay}>
          <SaveButton saved={saved} onToggle={() => setSaved(v => !v)} />
        </View>
      </View>
      <Text style={[typography.pinTitle, { marginTop: 4 }]} numberOfLines={2}>{title}</Text>
      <View style={styles.creatorRow}>
        <Image source={{ uri: creatorAvatar }} style={styles.avatar} />
        <Text style={typography.meta} numberOfLines={1}>{creator}</Text>
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  imageContainer: { borderRadius: 16, overflow: 'hidden', backgroundColor: colors.surface1 },
  image:          { width: '100%', height: '100%', resizeMode: 'cover' },
  saveOverlay:    { position: 'absolute', top: 12, right: 12 },
  creatorRow:     { flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 2 },
  avatar:         { width: 20, height: 20, borderRadius: 10, backgroundColor: colors.surface1 },
});
```

### Masonry Two-Column Grid

The idiomatic Expo approach is to compute cumulative column heights and distribute pins to whichever column is shorter. For production, `@shopify/flash-list` with a `masonry` prop is the performant choice. The hand-rolled version below is easier to read:

```tsx
// components/MasonryGrid.tsx
import { Dimensions, ScrollView, View } from 'react-native';
import { PinTile } from './PinTile';

type Pin = {
  id: string;
  imageUrl: string;
  aspectRatio: number;
  title: string;
  creator: string;
  creatorAvatar: string;
};

const H_MARGIN = 16;
const COL_GAP = 8;

export function MasonryGrid({ pins, onPinPress }: { pins: Pin[]; onPinPress: (id: string) => void }) {
  const screenW = Dimensions.get('window').width;
  const colW = (screenW - H_MARGIN * 2 - COL_GAP) / 2;

  // Distribute pins into the shorter column as we go.
  const columns: [Pin[], Pin[]] = [[], []];
  const heights = [0, 0];
  for (const pin of pins) {
    const h = colW / pin.aspectRatio + 60; // caption + creator row
    const idx = heights[0] <= heights[1] ? 0 : 1;
    columns[idx].push(pin);
    heights[idx] += h + 8;
  }

  return (
    <ScrollView contentContainerStyle={{ padding: H_MARGIN, flexDirection: 'row', gap: COL_GAP }}>
      {columns.map((col, i) => (
        <View key={i} style={{ flex: 1 }}>
          {col.map(pin => (
            <PinTile
              key={pin.id}
              imageUrl={pin.imageUrl}
              title={pin.title}
              creator={pin.creator}
              creatorAvatar={pin.creatorAvatar}
              aspectRatio={pin.aspectRatio}
              width={colW}
              onPress={() => onPinPress(pin.id)}
            />
          ))}
        </View>
      ))}
    </ScrollView>
  );
}
```

For performance at scale, swap this for `@shopify/flash-list`:

```tsx
import { MasonryFlashList } from '@shopify/flash-list';

<MasonryFlashList
  data={pins}
  numColumns={2}
  estimatedItemSize={300}
  keyExtractor={(item) => item.id}
  renderItem={({ item }) => <PinTile {...item} width={colW} onPress={() => onPinPress(item.id)} />}
/>
```

### Floating Search Bar

```tsx
// components/FloatingSearchBar.tsx
import { TextInput, View, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function FloatingSearchBar({ value, onChangeText }: { value: string; onChangeText: (s: string) => void }) {
  return (
    <View style={styles.wrap}>
      <View style={styles.pill}>
        <Ionicons name="search" size={20} color={colors.textPrimary} />
        <TextInput
          value={value}
          onChangeText={onChangeText}
          placeholder="Search for ideas"
          placeholderTextColor={colors.textSecondary}
          style={styles.input}
        />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  wrap: { paddingHorizontal: 16, paddingTop: 12 },
  pill: {
    height: 48, borderRadius: 500, backgroundColor: '#FFFFFF',
    flexDirection: 'row', alignItems: 'center', gap: 10, paddingHorizontal: 18,
    shadowColor: '#000', shadowOpacity: 0.08, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
    elevation: 2,
  },
  input: { flex: 1, fontSize: 16, color: colors.textPrimary, fontFamily: 'PinterestSans-Regular' },
});
```

## 4. Tab Bar with Red Center Create Button

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { View } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarShowLabel: false,
        tabBarActiveTintColor:  colors.textPrimary,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { height: 56, backgroundColor: colors.canvas, borderTopColor: colors.divider, borderTopWidth: 1 },
      }}
    >
      <Tabs.Screen name="index"  options={{ tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'home' : 'home-outline'} size={26} color={color} /> }} />
      <Tabs.Screen name="search" options={{ tabBarIcon: ({ color }) => <Ionicons name="search" size={26} color={color} /> }} />
      <Tabs.Screen name="create" options={{
        tabBarIcon: () => (
          <View style={{
            width: 48, height: 48, borderRadius: 14,
            backgroundColor: colors.red, alignItems: 'center', justifyContent: 'center',
          }}>
            <Ionicons name="add" size={26} color="#FFFFFF" />
          </View>
        ),
      }} />
      <Tabs.Screen name="notifications" options={{ tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'heart' : 'heart-outline'} size={26} color={color} /> }} />
      <Tabs.Screen name="profile"       options={{ tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person' : 'person-outline'} size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Shared pin → detail transition
// Use expo-router's built-in stack modal with customInterpolator, or react-native-shared-element.
// For a lightweight version, fade-cross with Reanimated Layout animation.
import Animated, { FadeIn, FadeOut, LinearTransition } from 'react-native-reanimated';

<Animated.View
  entering={FadeIn.duration(300)}
  exiting={FadeOut.duration(200)}
  layout={LinearTransition.springify().damping(16)}
/>

// Pinterest "P" pull-to-refresh spinner
import { Image } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withRepeat, withTiming, Easing } from 'react-native-reanimated';

export function PSpinner() {
  const r = useSharedValue(0);
  useEffect(() => {
    r.value = withRepeat(withTiming(360, { duration: 1000, easing: Easing.linear }), -1, false);
  }, []);
  const style = useAnimatedStyle(() => ({ transform: [{ rotate: `${r.value}deg` }] }));
  return <Animated.Image source={require('../assets/pinterest-p.png')} style={[{ width: 28, height: 28 }, style]} />;
}

// Idea Pin advance — use expo-router's Modal group and onPress zones
```

## 6. Icon Library

Use `@expo/vector-icons` — Ionicons matches Pinterest's SF-Symbol-adjacent style well.

| Purpose | Ionicons |
|---------|----------|
| Home | `home-outline` / `home` |
| Search | `search` |
| Create (center) | `add` (inside red rounded-rect) |
| Notifications | `heart-outline` / `heart` |
| Profile | `person-outline` / `person` |
| Heart (react) | `heart-outline` / `heart` |
| Comment | `chatbubble-outline` / `chatbubble` |
| Share | `share-outline` |
| Bookmark | `bookmark-outline` / `bookmark` |
| More | `ellipsis-horizontal` |
| Back | `chevron-back` |
| Shop tag | `pricetag` |
| Idea Pin | `sparkles` |
| Save confirm | `checkmark` |

## 7. Platform Notes

- **Light mode default, dark mode supported**: use `useColorScheme()` or a theme context. Switch `canvas`, `surface1`, `textPrimary`, and `divider` based on scheme.
- **Status bar**: `<StatusBar style={colorScheme === 'dark' ? 'light' : 'dark'} />` from `expo-status-bar`.
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The floating search bar should inset from the top safe area by 12pt.
- **Image loading**: use `expo-image` for progressive blur-hash placeholders — dramatically improves perceived masonry performance.
- **FlashList for masonry**: `MasonryFlashList` from `@shopify/flash-list` is strongly recommended at production scale; the hand-rolled `ScrollView` version works up to ~200 pins.
- **Dynamic Type**: React Native respects iOS text-scale by default. For masonry pin captions, set `allowFontScaling={false}` so tile heights remain computable.
- **Accessibility**: add `accessibilityRole="button"` + `accessibilityLabel={`Save ${title} by ${creator}`}` on tiles. The Save button gets its own label.
- **Haptics**: success on Save, light on tab change, medium on Follow, selection on long-press context menu.

## 8. Dark Mode Theming Helper

```tsx
// theme/useTheme.ts
import { useColorScheme } from 'react-native';
import { colors } from './colors';

export function useTheme() {
  const scheme = useColorScheme() ?? 'light';
  if (scheme === 'dark') {
    return {
      canvas:        colors.canvasDark,
      surface1:      colors.surface1Dark,
      input:         colors.surface2Dark,
      textPrimary:   colors.textPrimaryDark,
      textSecondary: colors.textSecondaryDark,
      divider:       colors.dividerDark,
      isDark: true,
    };
  }
  return {
    canvas:        colors.canvas,
    surface1:      colors.surface1,
    input:         colors.input,
    textPrimary:   colors.textPrimary,
    textSecondary: colors.textSecondary,
    divider:       colors.divider,
    isDark: false,
  };
}
```
