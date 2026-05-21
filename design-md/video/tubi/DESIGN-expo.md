# Tubi (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tubi's visual language into paste-ready Expo / React Native code: a design-token module, themed components, gradients, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark-only)
  canvas:   '#0A0A2A',
  surface1: '#15153D',
  surface2: '#1E1E52',
  surface3: '#262666',
  divider:  '#2A2A5C',

  // Brand gradient stops
  purple:  '#7408FF',
  magenta: '#FF00FF',
  violet:  '#A12BFF',
  pink:    '#FF4FD8',

  // CTA / accent
  playWhite:   '#FFFFFF',
  playPressed: '#E6E6F2',
  freeYellow:  '#FFD400',
  onWhite:     '#0A0A2A',
  freeText:    '#1A0A2A',

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#B9B9D6',
  textTertiary:  '#7A7AA0',

  // Semantic
  success: '#1FD17B',
  error:   '#FF4A6E',
  live:    '#FF3B5C',
  rating:  '#FFB020',
} as const;

export type TubiColor = keyof typeof colors;

// Gradient stop arrays for expo-linear-gradient
export const gradients = {
  brand:    { colors: [colors.purple, colors.magenta], start: { x: 0, y: 0 }, end: { x: 1, y: 1 } },
  progress: { colors: [colors.purple, colors.magenta], start: { x: 0, y: 0 }, end: { x: 1, y: 0 } },
  heroScrim:{ colors: ['transparent', 'rgba(10,10,42,0.95)'], locations: [0.4, 1], start: { x: 0, y: 0 }, end: { x: 0, y: 1 } },
} as const;
```

## 2. Typography

Tubi's brand face is a tight geometric grotesque; **Inter** at heavy weights is the closest free analog. Load Inter (SIL OFL) via `expo-font`.

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
    'Inter-Black':      require('../assets/fonts/Inter-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  heroTitle:   { ...white, fontFamily: 'Inter-Black',     fontSize: 32, lineHeight: 32, letterSpacing: -0.6 },
  detailTitle: { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 28, lineHeight: 30, letterSpacing: -0.5 },
  rowHeader:   { ...white, fontFamily: 'Inter-ExtraBold', fontSize: 22, lineHeight: 24, letterSpacing: -0.3 },
  section:     { ...white, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 22, letterSpacing: -0.2 },
  body:        { ...white, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 24 },
  cardTitle:   { ...white, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 17 },
  button:      { color: '#0A0A2A', fontFamily: 'Inter-ExtraBold', fontSize: 15, lineHeight: 15 },
  meta:        { color: '#B9B9D6', fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 17 },
  caption:     { color: '#B9B9D6', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 16 },
  badge:       { ...white, fontFamily: 'Inter-Bold', fontSize: 11, lineHeight: 11, letterSpacing: 0.4 },
  tab:         { color: '#7A7AA0', fontFamily: 'Inter-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Featured Hero

```tsx
// components/TubiHero.tsx
import { ImageBackground, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Ionicons } from '@expo/vector-icons';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function TubiHero({
  backdropUri, badge, title, meta, onPlay, onAdd,
}: {
  backdropUri: string; badge: string; title: string; meta: string;
  onPlay: () => void; onAdd: () => void;
}) {
  return (
    <View style={{ marginHorizontal: 14, height: 230, borderRadius: 18, overflow: 'hidden' }}>
      <ImageBackground source={{ uri: backdropUri }} style={{ flex: 1 }} resizeMode="cover">
        <LinearGradient {...gradients.heroScrim} style={{ flex: 1, padding: 16, justifyContent: 'space-between' }}>
          <View style={{ flexDirection: 'row' }}>
            <LinearGradient {...gradients.brand} style={{ borderRadius: 999, paddingHorizontal: 8, paddingVertical: 4 }}>
              <Text style={[typography.badge, { fontSize: 9 }]}>{badge.toUpperCase()}</Text>
            </LinearGradient>
          </View>
          <View>
            <Text style={typography.heroTitle}>{title}</Text>
            <Text style={[typography.meta, { marginTop: 7 }]}>{meta}</Text>
            <View style={{ flexDirection: 'row', gap: 8, marginTop: 12 }}>
              <Pressable
                onPress={onPlay}
                style={({ pressed }) => ({
                  flex: 1, height: 48, borderRadius: 8,
                  backgroundColor: pressed ? colors.playPressed : colors.playWhite,
                  flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 6,
                })}
              >
                <Ionicons name="play" size={13} color={colors.onWhite} />
                <Text style={typography.button}>Play Free</Text>
              </Pressable>
              <Pressable
                onPress={onAdd}
                style={{ width: 44, height: 48, borderRadius: 8, backgroundColor: 'rgba(255,255,255,0.16)',
                         alignItems: 'center', justifyContent: 'center' }}
              >
                <Ionicons name="add" size={18} color="#FFF" />
              </Pressable>
            </View>
          </View>
        </LinearGradient>
      </ImageBackground>
    </View>
  );
}
```

### Poster Card

```tsx
// components/TubiPoster.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withTiming } from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, gradients } from '../theme/colors';

const AView = Animated.createAnimatedComponent(View);

export function TubiPoster({
  imageUri, title, progress, onPress,
}: {
  imageUri: string; title: string; progress?: number; onPress: () => void;
}) {
  const scale = useSharedValue(1);
  const ring = useSharedValue(0);
  const animStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    borderWidth: ring.value,
  }));

  return (
    <Pressable
      onPressIn={() => { scale.value = withTiming(1.04, { duration: 150 }); ring.value = withTiming(2, { duration: 150 }); }}
      onPressOut={() => { scale.value = withTiming(1, { duration: 120 }); ring.value = withTiming(0, { duration: 120 }); }}
      onPress={onPress}
    >
      <AView style={[{ width: 100, height: 150, borderRadius: 10, overflow: 'hidden',
        shadowColor: '#000', shadowOpacity: 0.45, shadowRadius: 8, shadowOffset: { width: 0, height: 6 }, elevation: 6,
        borderColor: colors.magenta }, animStyle]}>
        <Image source={{ uri: imageUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        <View style={{ position: 'absolute', top: 6, left: 6, backgroundColor: colors.freeYellow,
          borderRadius: 3, paddingHorizontal: 5, paddingVertical: 2 }}>
          <Text style={{ fontFamily: 'Inter-Black', fontSize: 8, color: colors.freeText }}>FREE</Text>
        </View>
        <Text
          numberOfLines={2}
          style={{ position: 'absolute', left: 6, right: 6, bottom: 6, fontFamily: 'Inter-Bold',
            fontSize: 11, color: '#FFF', textShadowColor: 'rgba(0,0,0,0.8)', textShadowRadius: 4, textShadowOffset: { width: 0, height: 1 } }}
        >
          {title}
        </Text>
        {progress != null && (
          <View style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 4, backgroundColor: 'rgba(255,255,255,0.16)' }}>
            <LinearGradient {...gradients.progress} style={{ height: 4, width: `${progress * 100}%` }} />
          </View>
        )}
      </AView>
    </Pressable>
  );
}
```

### Content Row

```tsx
// components/TubiRow.tsx
import { FlatList, Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function TubiRow<T extends { id: string }>({
  title, data, showSeeAll, renderCard,
}: {
  title: string; data: T[]; showSeeAll?: boolean;
  renderCard: (item: T) => React.ReactElement;
}) {
  return (
    <View style={{ marginTop: 16 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, marginBottom: 10 }}>
        <Text style={typography.rowHeader}>{title}</Text>
        <View style={{ flex: 1 }} />
        {showSeeAll && (
          <Text style={{ fontFamily: 'Inter-Bold', fontSize: 13, color: colors.pink }}>See All ›</Text>
        )}
      </View>
      <FlatList
        horizontal
        showsHorizontalScrollIndicator={false}
        data={data}
        keyExtractor={(i) => i.id}
        renderItem={({ item }) => renderCard(item)}
        ItemSeparatorComponent={() => <View style={{ width: 10 }} />}
        contentContainerStyle={{ paddingHorizontal: 16 }}
      />
    </View>
  );
}
```

### Live TV EPG Row

```tsx
// components/EpgRow.tsx
import { FlatList, Image, Text, View } from 'react-native';
import { colors } from '../theme/colors';

type Program = { id: string; title: string; timeRange: string; isOnNow: boolean };

export function EpgRow({ logoUri, channelName, programs }: { logoUri: string; channelName: string; programs: Program[] }) {
  return (
    <View style={{ flexDirection: 'row', gap: 12, paddingHorizontal: 16, paddingVertical: 8 }}>
      <Image source={{ uri: logoUri }} style={{ width: 56, height: 56, borderRadius: 10, backgroundColor: colors.surface1 }} />
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 15, color: colors.textPrimary, marginBottom: 6 }}>{channelName}</Text>
        <FlatList
          horizontal
          showsHorizontalScrollIndicator={false}
          data={programs}
          keyExtractor={(p) => p.id}
          ItemSeparatorComponent={() => <View style={{ width: 8 }} />}
          renderItem={({ item }) => (
            <View style={{ width: 150, padding: 8, borderRadius: 10, backgroundColor: colors.surface2, overflow: 'hidden' }}>
              {item.isOnNow && (
                <>
                  <View style={{ position: 'absolute', left: 0, top: 0, bottom: 0, width: 3, backgroundColor: colors.purple }} />
                  <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginBottom: 2 }}>
                    <View style={{ width: 6, height: 6, borderRadius: 3, backgroundColor: colors.live }} />
                    <Text style={{ fontFamily: 'Inter-Black', fontSize: 9, color: colors.live }}>LIVE</Text>
                  </View>
                </>
              )}
              <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 13, color: colors.textPrimary }}>{item.title}</Text>
              <Text style={{ fontFamily: 'Inter-Medium', fontSize: 11, color: colors.textSecondary }}>{item.timeRange}</Text>
            </View>
          )}
        />
      </View>
    </View>
  );
}
```

### Buttons

```tsx
// components/TubiButtons.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Ionicons } from '@expo/vector-icons';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 48, borderRadius: 8, flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 6,
        backgroundColor: pressed ? colors.playPressed : colors.playWhite,
      })}
    >
      <Ionicons name="play" size={13} color={colors.onWhite} />
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function GradientButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}>
      <LinearGradient {...gradients.brand} style={{ height: 48, borderRadius: 8, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={[typography.button, { color: '#FFF' }]}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.magenta,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(10,10,42,0.96)',
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"     size={22} color={color} /> }} />
      <Tabs.Screen name="search"  options={{ title: 'Search',  tabBarIcon: ({ color }) => <Ionicons name="search"   size={22} color={color} /> }} />
      <Tabs.Screen name="live"    options={{ title: 'Live TV', tabBarIcon: ({ color }) => <Ionicons name="tv"       size={22} color={color} /> }} />
      <Tabs.Screen name="mylist"  options={{ title: 'My List', tabBarIcon: ({ color }) => <Ionicons name="bookmark" size={22} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Poster focus / press — Reanimated
scale.value = withTiming(1.04, { duration: 150 }); // press in
scale.value = withTiming(1.0,  { duration: 120 }); // release

// Hero cross-dissolve between featured titles (~6s dwell)
opacity.value = withTiming(0, { duration: 200 }, () => {
  // swap index on JS thread, then:
  opacity.value = withTiming(1, { duration: 400 });
});

// Continue-watching progress fill on appear
width.value = withTiming(pct, { duration: 500 });

// Add to My List: + → ✓
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
// animate icon swap with a spring scale 1 → 1.2 → 1

// Play transition: cross-fade into player
import Animated, { FadeIn } from 'react-native-reanimated';
// <Animated.View entering={FadeIn.duration(350)}>
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons primary).

| Purpose | Ionicons |
|---------|----------|
| Home | `home` / `home-outline` |
| Search | `search` |
| Live TV | `tv` / `tv-outline` |
| My List | `bookmark` / `bookmark-outline` |
| Account | `person-circle` / `person-circle-outline` |
| Play | `play` |
| Add to list | `add` → `checkmark` |
| Download | `download-outline` |
| Share | `share-outline` |
| Back | `chevron-back` |
| More like this | `albums-outline` |
| Cast & crew | `people-outline` |
| Live dot | `ellipse` (tinted `#FF3B5C`) |
| Restart | `play-back` |
| Settings | `settings-outline` |
| Info | `information-circle-outline` |

## 7. Platform Notes

- **Dark-only**: lock the app to dark — set `userInterfaceStyle: 'dark'` in `app.json`; never derive a light palette. Use `<StatusBar style="light" />` everywhere.
- **Font choice**: Inter is SIL OFL — free to bundle. Ship Regular→Black; heavy weights (ExtraBold/Black) are essential for the marquee title feel.
- **Gradients**: use `expo-linear-gradient`; the brand gradient angle is top-left → bottom-right (`start {0,0} end {1,1}`), progress is left→right.
- **Safe area**: wrap screens in `SafeAreaView`; the hero backdrop should bleed *under* the status bar (use `edges={['bottom']}` for the scroll container), tab bar needs bottom safe-area padding.
- **Edge-bleeding rows**: `FlatList horizontal` with `contentContainerStyle.paddingHorizontal: 16` and no right clipping so cards run off-screen.
- **Dynamic Type**: set `allowFontScaling={false}` on badges, tab labels, progress labels, and EPG time headers (layout-sensitive); leave it on for titles/body.
- **Performance**: poster rows can be long — use `FlatList` with `initialNumToRender`, `windowSize`, and `removeClippedSubviews`; memoize cards.
- **Accessibility**: poster `accessibilityLabel` must include "free to watch" (the FREE tag is core info, not decorative); EPG on-now blocks announce "live now".
- **Reduce Motion**: gate the press-scale and hero ken-burns behind `AccessibilityInfo.isReduceMotionEnabled`; fall back to a plain crossfade and final-width progress.
- **Reduce Transparency**: when enabled, replace the translucent tab bar with solid `#0A0A2A`.
