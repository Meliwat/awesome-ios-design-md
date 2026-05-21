# Duolingo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Duolingo's visual language into paste-ready Expo / React Native code: a design-token module, themed components, Reanimated + Haptics, and an `expo-router` tab layout.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand Greens
  featherGreen:      '#58CC02',
  maskGreen:         '#89E219',
  buttonGreenLedge:  '#58A700',

  // Gamification
  cardinalRed:       '#FF4B4B',
  cardinalLedge:     '#E53030',
  foxOrange:         '#FF9600',
  beeYellow:         '#FFC800',
  macawBlue:         '#1CB0F6',
  beetlePurple:      '#CE82FF',

  // Canvas (Light)
  snow:              '#FFFFFF',
  polar:             '#F7F7F7',
  swan:              '#E5E5E5',
  swanLedge:         '#C4C4C4',

  // Canvas (Dark)
  darkCanvas:        '#131F24',
  darkSurface1:      '#1F2C34',
  darkSurface2:      '#37464F',

  // Text
  eel:               '#4B4B4B',
  hare:              '#AFAFAF',
  wolf:              '#777777',

  // Tile States
  tileSelectedFill:  '#DDF4FF',
  tileSelectedBorder:'#84D8FF',
  tileCorrectFill:   '#D7FFB8',
  tileWrongFill:     '#FFDFE0',
} as const;

export type DuoColor = keyof typeof colors;
```

## 2. Typography

Load Feather Bold and DIN Next Rounded Pro via `expo-font`. Fall back to the system rounded (SF Pro Rounded on iOS). A web-friendly substitute for previews is Nunito or Baloo 2 from Google Fonts.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';
import { Stack } from 'expo-router';

export default function Root() {
  const [loaded] = useFonts({
    'Feather-Bold':              require('../assets/fonts/Feather-Bold.ttf'),
    'DINNextRoundedPro-Regular': require('../assets/fonts/DINNextRoundedPro-Regular.ttf'),
    'DINNextRoundedPro-Medium':  require('../assets/fonts/DINNextRoundedPro-Medium.ttf'),
    'DINNextRoundedPro-Bold':    require('../assets/fonts/DINNextRoundedPro-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
import { colors } from './colors';

const base = { color: colors.eel } satisfies TextStyle;

export const typography = {
  // Feather Bold — headlines, numbers
  wordmark:     { ...base, fontFamily: 'Feather-Bold', fontSize: 34, lineHeight: 34, letterSpacing: -0.5 },
  heroNumber:   { ...base, fontFamily: 'Feather-Bold', fontSize: 56, lineHeight: 56, letterSpacing: -1 },
  screenTitle:  { ...base, fontFamily: 'Feather-Bold', fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  question:     { ...base, fontFamily: 'Feather-Bold', fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  hudNumber:    { ...base, fontFamily: 'Feather-Bold', fontSize: 18, lineHeight: 18, letterSpacing: -0.1 },

  // DIN Next Rounded Pro — UI
  sectionHeader:{ ...base, fontFamily: 'DINNextRoundedPro-Bold',    fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  skillLabel:   { ...base, fontFamily: 'DINNextRoundedPro-Bold',    fontSize: 16, lineHeight: 19 },
  button:       { color: colors.snow, fontFamily: 'DINNextRoundedPro-Bold', fontSize: 17, lineHeight: 17, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  tile:         { ...base, fontFamily: 'DINNextRoundedPro-Bold',    fontSize: 17, lineHeight: 20 },
  body:         { ...base, fontFamily: 'DINNextRoundedPro-Regular', fontSize: 16, lineHeight: 22 },
  helper:       { color: colors.hare, fontFamily: 'DINNextRoundedPro-Medium', fontSize: 14, lineHeight: 18 },
  meta:         { color: colors.hare, fontFamily: 'DINNextRoundedPro-Medium', fontSize: 13, lineHeight: 16 },
  tab:          { ...base, fontFamily: 'DINNextRoundedPro-Bold',    fontSize: 11, lineHeight: 11, letterSpacing: 0.2 },
  labelUpper:   { ...base, fontFamily: 'DINNextRoundedPro-Bold',    fontSize: 12, lineHeight: 14, letterSpacing: 1, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### The 3D Primary Button (The Green Slab)

```tsx
// components/DuoButton.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSpring, Easing } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Variant = 'primary' | 'neutral' | 'destructive';

const palette: Record<Variant, { top: string; ledge: string; text: string; border?: string }> = {
  primary:     { top: colors.featherGreen, ledge: colors.buttonGreenLedge, text: colors.snow },
  neutral:     { top: colors.snow,         ledge: colors.swan,             text: colors.eel, border: colors.swan },
  destructive: { top: colors.cardinalRed,  ledge: colors.cardinalLedge,    text: colors.snow },
};

export function DuoButton({
  title, variant = 'primary', onPress, disabled,
}: { title: string; variant?: Variant; onPress: () => void; disabled?: boolean }) {
  const offset = useSharedValue(0);
  const p = palette[variant];
  const style = useAnimatedStyle(() => ({ transform: [{ translateY: offset.value }] }));

  return (
    <Pressable
      onPressIn={() => { offset.value = withTiming(4, { duration: 80, easing: Easing.linear }); }}
      onPressOut={() => { offset.value = withSpring(0, { damping: 14, stiffness: 200 }); }}
      onPress={() => {
        if (disabled) return;
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
        onPress();
      }}
      disabled={disabled}
      style={{ opacity: disabled ? 0.4 : 1 }}
    >
      <View style={{ height: 60 }}>
        {/* Ledge */}
        <View style={{
          position: 'absolute', left: 0, right: 0, top: 4, bottom: 0,
          backgroundColor: p.ledge, borderRadius: 16,
        }} />
        {/* Top face */}
        <Animated.View style={[
          {
            position: 'absolute', left: 0, right: 0, top: 0, height: 56,
            backgroundColor: p.top, borderRadius: 16,
            borderWidth: p.border ? 2 : 0, borderColor: p.border,
            alignItems: 'center', justifyContent: 'center',
          }, style,
        ]}>
          <Text style={[typography.button, { color: p.text }]}>{title}</Text>
        </Animated.View>
      </View>
    </Pressable>
  );
}
```

### Skill Node (Path Circle)

```tsx
// components/SkillNode.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type NodeState = 'locked' | 'available' | 'current' | 'completed' | 'gold' | 'legendary';

const stateStyles: Record<NodeState, { top: string; ledge: string }> = {
  locked:     { top: colors.swan,         ledge: colors.swanLedge },
  available:  { top: colors.featherGreen, ledge: colors.buttonGreenLedge },
  current:    { top: colors.featherGreen, ledge: colors.buttonGreenLedge },
  completed:  { top: colors.featherGreen, ledge: colors.buttonGreenLedge },
  gold:       { top: colors.beeYellow,    ledge: '#E29F03' },
  legendary:  { top: colors.beetlePurple, ledge: '#9B5BEC' },
};

export function SkillNode({
  icon, state, crowns = 0, label, onPress,
}: {
  icon: keyof typeof Ionicons.glyphMap;
  state: NodeState;
  crowns?: number;
  label: string;
  onPress: () => void;
}) {
  const size = state === 'current' ? 96 : 80;
  const scale = useSharedValue(1);
  const pulse = useSharedValue(1);

  useEffect(() => {
    if (state === 'current') {
      pulse.value = withRepeat(withTiming(1.1, { duration: 1000 }), -1, true);
    }
  }, [state]);

  const topStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const pulseStyle = useAnimatedStyle(() => ({ transform: [{ scale: pulse.value }], opacity: 0.6 / pulse.value }));

  const s = stateStyles[state];

  return (
    <View style={{ alignItems: 'center', gap: 8 }}>
      {crowns > 0 && state !== 'locked' && (
        <View style={{ flexDirection: 'row', gap: 2, backgroundColor: colors.eel, paddingHorizontal: 8, paddingVertical: 3, borderRadius: 999 }}>
          <Ionicons name="star" size={14} color={colors.beeYellow} />
          <Text style={[typography.meta, { color: colors.snow }]}>{crowns}</Text>
        </View>
      )}
      <Pressable
        onPressIn={() => { scale.value = withSpring(0.96, { damping: 14 }); }}
        onPressOut={() => { scale.value = withSpring(1, { damping: 14 }); }}
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
          onPress();
        }}
      >
        <View style={{ width: size, height: size + 4 }}>
          <View style={{ position: 'absolute', top: 4, width: size, height: size, borderRadius: size / 2, backgroundColor: s.ledge }} />
          {state === 'current' && (
            <Animated.View style={[{ position: 'absolute', top: 4, width: size, height: size, borderRadius: size / 2, borderWidth: 4, borderColor: colors.maskGreen }, pulseStyle]} />
          )}
          <Animated.View style={[{ position: 'absolute', top: 0, width: size, height: size, borderRadius: size / 2, backgroundColor: s.top, alignItems: 'center', justifyContent: 'center' }, topStyle]}>
            <Ionicons
              name={state === 'locked' ? 'lock-closed' : icon}
              size={size * 0.4}
              color={state === 'locked' ? colors.hare : colors.snow}
            />
          </Animated.View>
        </View>
      </Pressable>
      <Text style={typography.skillLabel}>{label}</Text>
    </View>
  );
}
```

### Answer Tile

```tsx
// components/AnswerTile.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type TileState = 'idle' | 'selected' | 'correct' | 'wrong';

const palette: Record<TileState, { fill: string; border: string; text: string }> = {
  idle:     { fill: colors.snow,             border: colors.swan,              text: colors.eel },
  selected: { fill: colors.tileSelectedFill, border: colors.tileSelectedBorder, text: colors.eel },
  correct:  { fill: colors.tileCorrectFill,  border: colors.featherGreen,       text: colors.buttonGreenLedge },
  wrong:    { fill: colors.tileWrongFill,    border: colors.cardinalRed,        text: colors.cardinalRed },
};

export function AnswerTile({ word, state, onPress }: { word: string; state: TileState; onPress: () => void }) {
  const shake = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ transform: [{ translateX: shake.value }] }));
  const p = palette[state];

  useEffect(() => {
    if (state === 'correct') Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    if (state === 'wrong') {
      Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);
      shake.value = withSequence(
        withTiming(-6, { duration: 50 }),
        withTiming(6,  { duration: 50 }),
        withTiming(-6, { duration: 50 }),
        withTiming(6,  { duration: 50 }),
        withTiming(0,  { duration: 50 }),
      );
    }
  }, [state]);

  return (
    <Animated.View style={style}>
      <Pressable onPress={onPress}>
        <View style={{ paddingBottom: 2 }}>
          <View style={{ position: 'absolute', left: 0, right: 0, top: 2, bottom: 0, backgroundColor: p.border, borderRadius: 12 }} />
          <View style={{
            backgroundColor: p.fill, borderRadius: 12, borderWidth: 2, borderColor: p.border,
            paddingVertical: 12, paddingHorizontal: 20, minHeight: 48,
            alignItems: 'center', justifyContent: 'center',
          }}>
            <Text style={[typography.tile, { color: p.text }]}>{word}</Text>
          </View>
        </View>
      </Pressable>
    </Animated.View>
  );
}
```

### HUD Chips (Streak, Gems, Hearts)

```tsx
// components/HudRow.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type ChipKind = 'streak' | 'gems' | 'hearts';

const chipConfig: Record<ChipKind, { icon: keyof typeof Ionicons.glyphMap; tint: string }> = {
  streak: { icon: 'flame',   tint: colors.foxOrange },
  gems:   { icon: 'diamond', tint: colors.macawBlue },
  hearts: { icon: 'heart',   tint: colors.cardinalRed },
};

export function HudChip({ kind, value }: { kind: ChipKind; value: number | string }) {
  const { icon, tint } = chipConfig[kind];
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 10, height: 32 }}>
      <Ionicons name={icon} size={20} color={tint} />
      <Text style={[typography.hudNumber, { color: tint }]}>{value}</Text>
    </View>
  );
}

export function HudRow({ streak, gems, hearts, flagEmoji }: { streak: number; gems: number; hearts: number; flagEmoji: string }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 16, height: 56 }}>
      <View style={{ width: 32, height: 32, borderRadius: 16, backgroundColor: colors.polar, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontSize: 22 }}>{flagEmoji}</Text>
      </View>
      <View style={{ flexDirection: 'row', gap: 4 }}>
        <HudChip kind="streak" value={streak} />
        <HudChip kind="gems"   value={gems} />
        <HudChip kind="hearts" value={hearts} />
      </View>
    </View>
  );
}
```

### Celebration Takeover

```tsx
// components/Celebration.tsx
import { View, Text, Image } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withRepeat, withSequence, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { DuoButton } from './DuoButton';

export function Celebration({ xpEarned, onContinue }: { xpEarned: number; onContinue: () => void }) {
  useEffect(() => {
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  }, []);

  return (
    <View style={{ flex: 1, backgroundColor: colors.featherGreen, alignItems: 'center', justifyContent: 'space-between', paddingVertical: 80 }}>
      <View style={{ alignItems: 'center', gap: 24 }}>
        <Image source={require('../assets/duo-cheering.png')} style={{ width: 280, height: 280, resizeMode: 'contain' }} />
        <Text style={[typography.screenTitle, { color: colors.snow, letterSpacing: 0.4 }]}>LESSON COMPLETE!</Text>
        <View style={{ paddingHorizontal: 20, paddingVertical: 8, borderRadius: 999, backgroundColor: 'rgba(255,200,0,0.25)', borderWidth: 2, borderColor: colors.beeYellow }}>
          <Text style={[typography.question, { color: colors.snow }]}>+{xpEarned} XP</Text>
        </View>
      </View>
      <View style={{ width: '100%', paddingHorizontal: 16 }}>
        <DuoButton title="Continue" variant="neutral" onPress={onContinue} />
      </View>
      <ConfettiLayer />
    </View>
  );
}

function ConfettiLayer() {
  // Render 12 <Animated.View/> rects that animate translateY from -20 to screen height + 40 over 1.2s,
  // each with a random x, random delay 0..0.3s, and random rotation.
  // Implementation shortened for brevity — see §6 for the full confetti component.
  return null;
}
```

## 4. The Learning Path

```tsx
// app/(tabs)/index.tsx
import { ScrollView, View } from 'react-native';
import { SafeAreaView } from 'react-native-safe-area-context';
import { SkillNode, NodeState } from '../../components/SkillNode';
import { HudRow } from '../../components/HudRow';
import { colors } from '../../theme/colors';

const nodes: Array<{ icon: 'egg' | 'restaurant' | 'airplane' | 'home'; state: NodeState; crowns: number; label: string }> = [
  { icon: 'egg',        state: 'completed', crowns: 5, label: 'Basics 1' },
  { icon: 'egg',        state: 'completed', crowns: 3, label: 'Basics 2' },
  { icon: 'restaurant', state: 'current',   crowns: 1, label: 'Food' },
  { icon: 'home',       state: 'available', crowns: 0, label: 'Home' },
  { icon: 'airplane',   state: 'locked',    crowns: 0, label: 'Travel' },
];

export default function LearnScreen() {
  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: colors.snow }} edges={['top']}>
      <HudRow flagEmoji="🇪🇸" streak={12} gems={245} hearts={5} />
      <ScrollView contentContainerStyle={{ paddingVertical: 32, gap: 32, alignItems: 'center' }}>
        {nodes.map((n, i) => {
          const wave = Math.sin(i * (Math.PI / 3)) * 60;
          return (
            <View key={i} style={{ transform: [{ translateX: wave }] }}>
              <SkillNode icon={n.icon} state={n.state} crowns={n.crowns} label={n.label} onPress={() => {}} />
            </View>
          );
        })}
      </ScrollView>
    </SafeAreaView>
  );
}
```

## 5. Tab Bar

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
        tabBarActiveTintColor: colors.featherGreen,
        tabBarInactiveTintColor: colors.hare,
        tabBarStyle: {
          backgroundColor: colors.snow,
          borderTopColor: colors.swan,
          borderTopWidth: 1,
          height: 84,
          paddingBottom: 24,
          paddingTop: 8,
        },
        tabBarLabelStyle: {
          fontFamily: 'DINNextRoundedPro-Bold',
          fontSize: 11,
          letterSpacing: 0.2,
        },
      }}
    >
      <Tabs.Screen name="index"       options={{ title: 'Learn',       tabBarIcon: ({ color }) => <Ionicons name="home"        size={28} color={color} /> }} />
      <Tabs.Screen name="leaderboard" options={{ title: 'Leaderboard', tabBarIcon: ({ color }) => <Ionicons name="trophy"      size={28} color={color} /> }} />
      <Tabs.Screen name="quests"      options={{ title: 'Quests',      tabBarIcon: ({ color }) => <Ionicons name="bookmark"    size={28} color={color} /> }} />
      <Tabs.Screen name="shop"        options={{ title: 'Shop',        tabBarIcon: ({ color }) => <Ionicons name="bag-handle"  size={28} color={color} /> }} />
      <Tabs.Screen name="profile"     options={{ title: 'Profile',     tabBarIcon: ({ color }) => <Ionicons name="person"      size={28} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion & Effects

### Confetti Layer

```tsx
// components/Confetti.tsx
import { useEffect } from 'react';
import { Dimensions } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withDelay, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';

const palette = [colors.featherGreen, colors.foxOrange, colors.beeYellow, colors.macawBlue, colors.cardinalRed, colors.beetlePurple];

function Piece({ delay }: { delay: number }) {
  const { height } = Dimensions.get('window');
  const y = useSharedValue(-20);
  const rotate = useSharedValue(0);
  const x = Math.random() * (Dimensions.get('window').width - 16);
  const color = palette[Math.floor(Math.random() * palette.length)];

  useEffect(() => {
    y.value = withDelay(delay, withTiming(height + 40, { duration: 1200, easing: Easing.in(Easing.cubic) }));
    rotate.value = withDelay(delay, withTiming((Math.random() - 0.5) * 720, { duration: 1200 }));
  }, []);

  const style = useAnimatedStyle(() => ({ transform: [{ translateY: y.value }, { rotate: `${rotate.value}deg` }] }));

  return (
    <Animated.View
      style={[
        { position: 'absolute', left: x, width: 8, height: 14, borderRadius: 2, backgroundColor: color },
        style,
      ]}
    />
  );
}

export function Confetti() {
  const pieces = Array.from({ length: 12 }, (_, i) => i);
  return (
    <>
      {pieces.map((i) => (
        <Piece key={i} delay={Math.random() * 300} />
      ))}
    </>
  );
}
```

### Haptics Summary

```ts
import * as Haptics from 'expo-haptics';

// Correct answer
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Wrong answer
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);

// Button press
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Skill node tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Level complete / streak save
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

### Streak Flame Pulse (Reanimated)

```tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, Easing } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StreakFlame({ count }: { count: number }) {
  const scale = useSharedValue(1);
  useEffect(() => {
    scale.value = withRepeat(withTiming(1.05, { duration: 2000, easing: Easing.inOut(Easing.ease) }), -1, true);
  }, []);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
      <Animated.View style={style}>
        <Ionicons name="flame" size={20} color={colors.foxOrange} />
      </Animated.View>
      <Text style={[typography.hudNumber, { color: colors.foxOrange }]}>{count}</Text>
    </View>
  );
}
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons, Feather, MaterialCommunityIcons, etc.

| Purpose | Ionicons |
|---------|----------|
| Streak flame | `flame` / `flame-outline` |
| Gems | `diamond` / `diamond-outline` |
| Hearts | `heart` / `heart-outline` |
| Locked skill | `lock-closed` |
| Completed skill | `star` / `checkmark` |
| Speaking exercise | `mic` |
| Listening exercise | `volume-high` |
| Close lesson | `close` |
| Continue | `arrow-forward` |
| Learn tab | `home` / `home-outline` |
| Leaderboard tab | `trophy` / `trophy-outline` |
| Quests tab | `bookmark` / `bookmark-outline` |
| Shop tab | `bag-handle` / `bag-handle-outline` |
| Profile tab | `person` / `person-outline` |
| Crown (skill mastery) | `ribbon` or custom SVG |

## 8. Platform Notes

- **Sound Effects**: Use `expo-av` to play short SFX (`correct.mp3`, `wrong.mp3`, `cheer.mp3`, `streak-save.mp3`) paired with every haptic
- **Status bar**: `<StatusBar style="dark" />` on light canvas, `"light"` in lesson celebrations
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`
- **Reduce Motion**: check `AccessibilityInfo.isReduceMotionEnabled()` and skip confetti + pulse animations when enabled
- **Dynamic Type**: React Native respects user font scaling; set `allowFontScaling={false}` only on button labels, HUD numbers, and tab labels where layout breaks
- **Accessibility**:
  - Answer tiles: `accessibilityRole="button"` + `accessibilityState={{ selected: state === 'selected' }}` + `accessibilityLabel`
  - Skill nodes: label includes state ("Food, current lesson, 1 crown") and `accessibilityHint` ("Double tap to start lesson")
  - Celebration: announce "Lesson complete, plus 10 XP earned" via `AccessibilityInfo.announceForAccessibility`
- **Android parity**: the 3D ledge + rounded type work on Android too; haptics need `expo-haptics` installed; tab bar height differs — use `useSafeAreaInsets`
- **Font loading splash**: hold the splash screen (`expo-splash-screen`) until `useFonts` resolves, otherwise Feather Bold falls back to system rounded on first paint
