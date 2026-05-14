# Headspace (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Headspace's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated patterns for the breathing sphere, Aurora gradient, and streak ring.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces
  butterCream:    '#FFF7E7',
  offCream:       '#FBF1DA',
  warmGray:       '#E8DFC9',
  surfaceWhite:   '#FFFFFF',

  // Marigold (primary)
  marigold:        '#FF6B35',
  marigoldPressed: '#E55A2B',
  marigoldLight:   '#FFB89E',

  // Aurora gradient stops
  aurora1:        '#FFB89E',
  aurora2:        '#FFD25C',
  aurora3:        '#FF6B35',

  // Secondary accents
  butter:         '#FFD25C',
  sageMoss:       '#7E9F4B',
  sageLight:      '#B4C68F',
  coralFlush:     '#F4877E',
  skyLavender:    '#B7B0DC',

  // Text — Ink Brown
  inkBrown:       '#2E1A47',
  inkBrownSoft:   '#594675',
  inkBrownMute:   '#8E7DA5',

  // Dusk (Sleep mode)
  duskCanvas:     '#1A1430',
  duskSurface1:   '#2A2046',
  duskSurface2:   '#3A2F5C',
  duskDivider:    '#3F3460',

  // Semantic
  success:        '#7E9F4B',
  warning:        '#FF9F4A',
  errorCoral:     '#E04646',

  // Milestone
  milestoneGold:  '#E5B85C',
} as const;

export type HeadspaceColor = keyof typeof colors;
```

## 2. Typography

Apercu is proprietary. Bundle via `expo-font` or fall back to `Nunito` (Google Fonts) — closest free humanist sans with similar curl.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Apercu-Regular': require('../assets/fonts/Apercu-Regular.otf'),
    'Apercu-Medium':  require('../assets/fonts/Apercu-Medium.otf'),
    'Apercu-Bold':    require('../assets/fonts/Apercu-Bold.otf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const fRegular = 'Apercu-Regular';
const fMedium  = 'Apercu-Medium';
const fBold    = 'Apercu-Bold';
const tabular  = { fontVariant: ['tabular-nums' as const] };

export const typography = {
  greetingHero:  { fontFamily: fBold,    fontSize: 32, lineHeight: 38, letterSpacing: -0.3 },
  screenTitle:   { fontFamily: fBold,    fontSize: 28, lineHeight: 34, letterSpacing: -0.3 },
  medTitle:      { fontFamily: fBold,    fontSize: 22, lineHeight: 29, letterSpacing: -0.2 },
  sectionHdr:    { fontFamily: fBold,    fontSize: 18, lineHeight: 24 },
  cardTitle:     { fontFamily: fMedium,  fontSize: 17, lineHeight: 22 },

  body:          { fontFamily: fRegular, fontSize: 16, lineHeight: 24 },     // 1.5 line-height — generous
  bodyBold:      { fontFamily: fMedium,  fontSize: 16, lineHeight: 24 },
  guidance:      { fontFamily: fRegular, fontSize: 22, lineHeight: 31 },     // play-screen guidance — 1.4 line-height

  meta:          { fontFamily: fRegular, fontSize: 13, lineHeight: 17 },
  tagPill:       { fontFamily: fMedium,  fontSize: 13, letterSpacing: 0.2 },
  caption:       { fontFamily: fRegular, fontSize: 11, lineHeight: 14 },
  tab:           { fontFamily: fMedium,  fontSize: 11, letterSpacing: 0.1 },

  button:        { fontFamily: fMedium,  fontSize: 17 },                     // Medium, not Bold

  timerLg:       { fontFamily: fBold,    fontSize: 56, lineHeight: 56, letterSpacing: -0.5, ...tabular },
  streakNum:     { fontFamily: fBold,    fontSize: 22, lineHeight: 22, ...tabular },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Breathing Sphere (the hero component)

```tsx
// components/BreathingSphere.tsx
import { useEffect } from 'react';
import { View } from 'react-native';
import Svg, { Circle, Defs, RadialGradient, Stop } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withRepeat, withSequence, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function BreathingSphere({ diameter = 234 }: { diameter?: number }) {
  const scale = useSharedValue(1);

  useEffect(() => {
    // 12-second breath cycle:
    //   4s inhale: 1.0 → 1.04
    //   2s hold
    //   4s exhale: 1.04 → 0.96
    //   2s hold-low → 1.0
    scale.value = withRepeat(
      withSequence(
        withTiming(1.04, { duration: 4000, easing: Easing.inOut(Easing.ease) }),
        withTiming(1.04, { duration: 2000 }),  // hold
        withTiming(0.96, { duration: 4000, easing: Easing.inOut(Easing.ease) }),
        withTiming(1.00, { duration: 2000 })   // hold-low
      ),
      -1,
      false
    );
  }, []);

  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Animated.View style={[{ width: diameter, height: diameter }, aStyle]}>
      <Svg width={diameter} height={diameter} viewBox="0 0 100 100">
        <Defs>
          <RadialGradient id="sphere" cx="35%" cy="35%" r="65%">
            <Stop offset="0%" stopColor={colors.aurora1} />
            <Stop offset="70%" stopColor={colors.marigold} />
            <Stop offset="100%" stopColor={colors.marigoldPressed} />
          </RadialGradient>
        </Defs>
        <Circle cx="50" cy="50" r="49" fill="url(#sphere)" stroke="rgba(46,26,71,0.08)" strokeWidth="1" />
      </Svg>
    </Animated.View>
  );
}
```

### Aurora Gradient (Today hero)

```tsx
// components/AuroraGradient.tsx
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';

export function AuroraGradient({ height = 280 }: { height?: number }) {
  return (
    <LinearGradient
      colors={[colors.aurora1, colors.aurora2, colors.aurora3]}
      locations={[0, 0.5, 1]}
      start={{ x: 0.5, y: 0 }}
      end={{ x: 0.5, y: 1 }}
      style={{ height, width: '100%' }}
    />
  );
}
```

### Today Hero (greeting + Aurora wash)

```tsx
// components/TodayHero.tsx
import { View, Text } from 'react-native';
import { AuroraGradient } from './AuroraGradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TodayHero({ name }: { name: string }) {
  return (
    <View>
      <AuroraGradient height={280} />
      <View style={{ position: 'absolute', top: 64, left: 24 }}>
        <Text style={[typography.greetingHero, { color: colors.inkBrown }]}>
          Good morning, {name}
        </Text>
        <Text style={[typography.body, { color: colors.inkBrown, opacity: 0.8, marginTop: 6 }]}>
          Today's vibe: Mindful Moment
        </Text>
      </View>
    </View>
  );
}
```

### Primary CTA Button

```tsx
// components/HSButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Style = 'primary' | 'outline';

export function HSButton({ title, style = 'primary', onPress }: { title: string; style?: Style; onPress: () => void }) {
  if (style === 'outline') {
    return (
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
        style={({ pressed }) => ({
          height: 56, borderRadius: 28, borderWidth: 1.5, borderColor: colors.inkBrown,
          alignItems: 'center', justifyContent: 'center', marginHorizontal: 24,
          backgroundColor: 'transparent',
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}
      >
        <Text style={[typography.button, { color: colors.inkBrown }]}>{title}</Text>
      </Pressable>
    );
  }
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        height: 56, borderRadius: 28, backgroundColor: pressed ? colors.marigoldPressed : colors.marigold,
        alignItems: 'center', justifyContent: 'center', marginHorizontal: 24,
        shadowColor: colors.marigold, shadowOpacity: 0.25, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
        elevation: 8,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Meditation Session Card

```tsx
// components/MeditationCard.tsx
import { View, Text, ImageBackground, StyleSheet, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  illustration: any;
  tag: string;
  title: string;
  host: string;
  duration: string;
  onPress: () => void;
};

export function MeditationCard({ illustration, tag, title, host, duration, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={styles.frame}>
      <ImageBackground source={illustration} style={styles.bg}>
        <LinearGradient
          colors={['transparent', 'rgba(46,26,71,0.6)']}
          locations={[0, 1]}
          style={StyleSheet.absoluteFill}
        />
        <View style={styles.content}>
          <View style={styles.tagPill}>
            <Text style={[typography.tagPill, { color: '#FFF' }]}>{tag}</Text>
          </View>
          <Text style={[typography.medTitle, { color: '#FFF', marginTop: 8 }]}>{title}</Text>
          <Text style={[typography.meta, { color: 'rgba(255,255,255,0.85)', marginTop: 4 }]}>
            {host} · {duration}
          </Text>
        </View>
      </ImageBackground>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  frame: {
    marginHorizontal: 24, marginVertical: 8,
    height: 200, borderRadius: 24, overflow: 'hidden',
    shadowColor: colors.inkBrown, shadowOpacity: 0.08, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
    elevation: 8,
  },
  bg:      { flex: 1, justifyContent: 'flex-end' },
  content: { padding: 16 },
  tagPill: {
    alignSelf: 'flex-start',
    backgroundColor: 'rgba(255,255,255,0.25)',
    paddingVertical: 4, paddingHorizontal: 12, borderRadius: 500,
  },
});
```

### Quick Action Tile

```tsx
// components/QuickActionTile.tsx
import { View, Text, Image, Pressable } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  bg: string;
  illustration: any;
  title: string;
  subtitle: string;
  onPress: () => void;
};

export function QuickActionTile({ bg, illustration, title, subtitle, onPress }: Props) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        width: 140, height: 180, borderRadius: 20,
        backgroundColor: bg, padding: 16, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Image source={illustration} style={{ width: 64, height: 64 }} resizeMode="contain" />
      <Text style={[typography.cardTitle, { color: colors.inkBrown, marginTop: 8, textAlign: 'center' }]}>{title}</Text>
      <Text style={[typography.meta, { color: colors.inkBrown, opacity: 0.7, marginTop: 4 }]}>{subtitle}</Text>
    </Pressable>
  );
}
```

### Player Controls

```tsx
// components/PlayerControls.tsx
import { Pressable, View } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function PlayerControls({ playing, onTogglePlay, onSkipBack, onSkipForward }: any) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 32 }}>
      <CircleControl icon="play-back" onPress={onSkipBack} />
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onTogglePlay(); }}
        style={({ pressed }) => ({
          width: 72, height: 72, borderRadius: 36, backgroundColor: colors.marigold,
          alignItems: 'center', justifyContent: 'center',
          shadowColor: colors.marigold, shadowOpacity: 0.25, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
          elevation: 12,
          transform: [{ scale: pressed ? 0.98 : 1 }],
        })}
      >
        <Ionicons name={playing ? 'pause' : 'play'} size={28} color="#FFF" />
      </Pressable>
      <CircleControl icon="play-forward" onPress={onSkipForward} />
    </View>
  );
}

function CircleControl({ icon, onPress }: { icon: any; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{ width: 48, height: 48, borderRadius: 24, backgroundColor: colors.offCream, alignItems: 'center', justifyContent: 'center' }}
    >
      <Ionicons name={icon} size={22} color={colors.inkBrown} />
    </Pressable>
  );
}
```

### Streak Ring

```tsx
// components/StreakRing.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);
const SIZE = 120;
const R = 52;
const C = 2 * Math.PI * R;

export function StreakRing({ currentStreak, goal = 30 }: { currentStreak: number; goal?: number }) {
  const progress = Math.min(currentStreak / goal, 1);
  const dashOffset = useSharedValue(C);

  useEffect(() => {
    dashOffset.value = withTiming(C * (1 - progress), { duration: 1200, easing: Easing.out(Easing.cubic) });
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  }, []);

  const animProps = useAnimatedProps(() => ({ strokeDashoffset: dashOffset.value }));

  return (
    <View style={{ width: SIZE, height: SIZE, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={SIZE} height={SIZE} style={{ position: 'absolute' }}>
        <Circle cx={SIZE / 2} cy={SIZE / 2} r={R} stroke={colors.offCream} strokeWidth={8} fill="none" />
        <AnimatedCircle
          cx={SIZE / 2} cy={SIZE / 2} r={R}
          stroke={colors.marigold} strokeWidth={8} strokeLinecap="round" fill="none"
          strokeDasharray={`${C},${C}`}
          animatedProps={animProps}
          rotation="-90" originX={SIZE / 2} originY={SIZE / 2}
        />
      </Svg>
      <Text style={[typography.timerLg, { color: colors.inkBrown }]}>{currentStreak}</Text>
      <Text style={[typography.meta, { color: colors.inkBrownSoft }]}>days</Text>
    </View>
  );
}
```

### Mood Tag

```tsx
// components/MoodTag.tsx
import { useState } from 'react';
import { Pressable, View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MoodTag({ emoji, label }: { emoji: string; label: string }) {
  const [selected, setSelected] = useState(false);
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPress={() => {
        Haptics.selectionAsync();
        setSelected((s) => !s);
        scale.value = withSpring(selected ? 1.0 : 1.05, { damping: 12 });
      }}
    >
      <Animated.View
        style={[
          {
            width: 80, height: 88, borderRadius: 16,
            backgroundColor: selected ? colors.butter : colors.offCream,
            alignItems: 'center', justifyContent: 'center',
          },
          aStyle,
        ]}
      >
        <Text style={{ fontSize: 32 }}>{emoji}</Text>
        <Text style={[typography.meta, { color: colors.inkBrown, marginTop: 4 }]}>{label}</Text>
      </Animated.View>
    </Pressable>
  );
}
```

## 4. Tab Bar (with Sage Sleep Tint)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={({ route }) => ({
        tabBarActiveTintColor: route.name === 'sleep' ? colors.sageMoss : colors.marigold,
        tabBarInactiveTintColor: colors.inkBrownMute,
        tabBarStyle: {
          backgroundColor: route.name === 'sleep' ? colors.duskCanvas : colors.butterCream,
          borderTopWidth: 0.5,
          borderTopColor: route.name === 'sleep' ? colors.duskDivider : colors.warmGray,
        },
        tabBarLabelStyle: { fontFamily: 'Apercu-Medium', fontSize: 11 },
      })}
    >
      <Tabs.Screen name="index"    options={{ title: 'Today',    tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'sunny' : 'sunny-outline'}   size={24} color={color} /> }} />
      <Tabs.Screen name="meditate" options={{ title: 'Meditate', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'leaf'  : 'leaf-outline'}    size={24} color={color} /> }} />
      <Tabs.Screen name="sleep"    options={{ title: 'Sleep',    tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'moon'  : 'moon-outline'}    size={24} color={color} /> }} />
      <Tabs.Screen name="move"     options={{ title: 'Move',     tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'walk'  : 'walk-outline'}    size={24} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person' : 'person-outline'} size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Breathing sphere — 12s cycle, pure ease-in-out (NOT spring)
scale.value = withRepeat(
  withSequence(
    withTiming(1.04, { duration: 4000, easing: Easing.inOut(Easing.ease) }),
    withTiming(1.04, { duration: 2000 }),
    withTiming(0.96, { duration: 4000, easing: Easing.inOut(Easing.ease) }),
    withTiming(1.00, { duration: 2000 })
  ), -1, false
);

// Primary CTA tap — soft, never heavy
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Mood tag select
Haptics.selectionAsync();
scale.value = withSpring(1.05, { damping: 12 });

// Streak ring reveal
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
dashOffset.value = withTiming(C * (1 - progress), { duration: 1200 });

// Tab switch
Haptics.selectionAsync();

// Meditation completion
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Sleep mode transition — 600ms canvas color cross-fade
canvasOpacity.value = withTiming(1.0, { duration: 600 });
```

## 6. Icon Library

| Purpose | Ionicons |
|---------|----------|
| Today tab | `sunny-outline` / `sunny` |
| Meditate tab | `leaf-outline` / `leaf` |
| Sleep tab | `moon-outline` / `moon` |
| Move tab | `walk-outline` / `walk` |
| Profile tab | `person-outline` / `person` |
| Play | `play` |
| Pause | `pause` |
| Skip back | `play-back` |
| Skip forward | `play-forward` |
| Settings | `settings-outline` |
| Search | `search-outline` |
| Favorite | `heart-outline` / `heart` |
| Share | `share-outline` |
| Streak flame | `flame` (marigold) |
| Bell | `notifications-outline` |
| Milestone trophy | `trophy` (milestone gold) |
| Host (Andy) | (avatar PNG, not icon) |

## 7. Platform Notes

- **Apercu licensing**: Apercu is proprietary by Colophon Foundry — if you don't have a license, swap to `Nunito` from Google Fonts (loaded via `expo-font`) or fall back to `System` with `Platform.select({ ios: { fontFamily: 'Avenir Next Rounded' } })` for a similar soft humanist feel.
- **Status bar**: `<StatusBar style="dark" />` on Today/Meditate (butter-cream canvas); `style="light"` on Sleep (dusk canvas). Use `expo-status-bar`.
- **Safe area**: wrap in `SafeAreaView` from `react-native-safe-area-context`. The Today greeting respects safe-area top + 16pt. The Sleep tab transitions both the canvas color AND the status bar style.
- **Breathing sphere accuracy**: use `withRepeat(withSequence(...), -1, false)` to drive the 4-2-4-2 breath cadence — not `withRepeat(withTiming(...))` which only gives a simple back-and-forth.
- **Reanimated `RadialGradient`**: SVG-based radial gradient via `react-native-svg`'s `<RadialGradient>` is the simplest path. The native Skia path is faster on long meditations but requires `@shopify/react-native-skia`.
- **Aurora gradient**: `expo-linear-gradient` with 3 color stops at `[0, 0.5, 1]` reproduces the dawn wash. For the Today hero, set `height: 280` and overlay the greeting content absolutely.
- **Sage tint on Sleep tab**: at runtime, swap `tabBarActiveTintColor` based on the currently-focused route — Marigold for everything except Sleep where it's Sage.
- **Tabular numerals**: `fontVariant: ['tabular-nums']` works on iOS for the streak number and timer; Android ignores it — use `Apercu-Bold` or a monospaced fallback if column alignment matters.
- **Sleep mode canvas transition**: animate the canvas backgroundColor via `Reanimated.useAnimatedStyle` with `interpolateColor` — 600ms ease-in-out from `colors.butterCream` to `colors.duskCanvas` when entering the Sleep tab.
- **Illustration assets**: use PNG @2x/@3x bundled in the asset catalog; alternatively use Rive or Lottie for animated companions on milestone reveals (Headspace's real app uses bespoke After Effects animations exported via Lottie).
