# Gas (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Gas's playful purple visual language into paste-ready Expo / React Native code: a design-token module, the brand & choice gradients, themed components, and Reanimated snippets for the poll card and flame currency.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  gasIndigo:  '#6C5CE7',
  gasPurple:  '#A06CFF',
  gasViolet:  '#7B4DFF', // on-white tint
  indigoPressed: '#5A4AD1',

  // Surfaces
  cardWhite:   '#FFFFFF',
  tintSurface: '#F4F2FF',
  divider:     '#ECE9FB',

  // Text on white cards
  textPrimary:   '#1A1530',
  textSecondary: '#6B6588',
  textTertiary:  '#A29DB8',

  // Currency
  flame: '#FF7A45',

  // Semantic
  success: '#2BC48A',
  error:   '#FF5470',
} as const;

export type GasColor = keyof typeof colors;

// World background (full-bleed, every screen)
export const appGradient = ['#6C5CE7', '#A06CFF'] as const;

// Fixed festive choice palette (rotates by slot)
export const choiceGradients = [
  { g: ['#6C5CE7', '#8B6CFF'] as const, text: '#FFFFFF' }, // 1 indigo
  { g: ['#FF6CC8', '#FF9CDB'] as const, text: '#FFFFFF' }, // 2 pink
  { g: ['#FFC93C', '#FFB13C'] as const, text: '#4A3300' }, // 3 yellow
  { g: ['#2BC48A', '#4FD9A6'] as const, text: '#FFFFFF' }, // 4 green
];

export const plumShadow = 'rgba(40,20,90,0.55)';
```

## 2. Typography

Load **Nunito** via `expo-font` (SIL OFL — free), set HEAVY.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Nunito-SemiBold':  require('../assets/fonts/Nunito-SemiBold.ttf'),
    'Nunito-Bold':      require('../assets/fonts/Nunito-Bold.ttf'),
    'Nunito-ExtraBold': require('../assets/fonts/Nunito-ExtraBold.ttf'),
    'Nunito-Black':     require('../assets/fonts/Nunito-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  display:   { color: '#FFFFFF', fontFamily: 'Nunito-Black',     fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  screen:    { color: '#FFFFFF', fontFamily: 'Nunito-Black',     fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  question:  { color: '#1A1530', fontFamily: 'Nunito-Black',     fontSize: 23, lineHeight: 29, letterSpacing: -0.2, textAlign: 'center' as const },
  cardTitle: { color: '#1A1530', fontFamily: 'Nunito-ExtraBold', fontSize: 18, lineHeight: 23 },
  choice:    { color: '#FFFFFF', fontFamily: 'Nunito-ExtraBold', fontSize: 16, lineHeight: 18, textAlign: 'center' as const },
  body:      { color: '#6B6588', fontFamily: 'Nunito-Bold',      fontSize: 15, lineHeight: 22 },
  bigNum:    { color: '#FFFFFF', fontFamily: 'Nunito-Black',     fontSize: 28, lineHeight: 28, letterSpacing: -0.5 },
  meta:      { color: '#A29DB8', fontFamily: 'Nunito-Bold',      fontSize: 13, lineHeight: 17 },
  tag:       { color: '#7B4DFF', fontFamily: 'Nunito-ExtraBold', fontSize: 12, lineHeight: 12, letterSpacing: 0.5, textTransform: 'uppercase' as const },
  button:    { color: '#FFFFFF', fontFamily: 'Nunito-Black',     fontSize: 16, lineHeight: 16, letterSpacing: 0.2 },
  pill:      { color: '#FFFFFF', fontFamily: 'Nunito-Black',     fontSize: 15, lineHeight: 15 },
  tab:       { color: '#FFFFFF', fontFamily: 'Nunito-ExtraBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### App Background (full-bleed gradient world)

```tsx
// components/GasBackground.tsx
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet } from 'react-native';
import { appGradient } from '../theme/colors';

export function GasBackground() {
  return <LinearGradient colors={appGradient} style={StyleSheet.absoluteFill} />;
}
```

### Poll Card

```tsx
// components/PollCard.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { choiceGradients, colors, plumShadow } from '../theme/colors';
import { typography } from '../theme/typography';

const APressable = Animated.createAnimatedComponent(Pressable);

type Choice = { id: string; name: string };

export function PollCard({
  emoji, question, choices, onPick, onShuffle,
}: {
  emoji: string; question: string; choices: Choice[];
  onPick: (c: Choice) => void; onShuffle: () => void;
}) {
  return (
    <View style={{
      backgroundColor: colors.cardWhite, borderRadius: 28,
      paddingTop: 26, paddingHorizontal: 20, paddingBottom: 24,
      shadowColor: '#281450', shadowOpacity: 0.55, shadowRadius: 25,
      shadowOffset: { width: 0, height: 24 }, elevation: 16,
    }}>
      <Text style={{ fontSize: 50, textAlign: 'center' }}>{emoji}</Text>
      <Text style={[typography.question, { marginTop: 14 }]}>{question}</Text>
      <Text style={[typography.tag, { textAlign: 'center', marginTop: 8 }]}>Anonymous · be nice</Text>

      <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 12, marginTop: 22 }}>
        {choices.slice(0, 4).map((c, i) => {
          const slot = choiceGradients[i];
          return <ChoiceButton key={c.id} name={c.name} slot={slot} onPress={() => onPick(c)} />;
        })}
      </View>

      <Pressable onPress={onShuffle} style={{ marginTop: 20, alignSelf: 'center' }}>
        <Text style={{ fontFamily: 'Nunito-Bold', fontSize: 14, color: colors.textTertiary }}>🔄 Shuffle names</Text>
      </Pressable>
    </View>
  );
}

function ChoiceButton({
  name, slot, onPress,
}: { name: string; slot: { g: readonly [string, string]; text: string }; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <APressable
      onPressIn={() => { scale.value = withSpring(1.04, { damping: 6 }); }}
      onPressOut={() => { scale.value = withSpring(1, { damping: 8 }); }}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={[{ flexGrow: 1, flexBasis: '46%' }, style]}
    >
      <LinearGradient
        colors={slot.g} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ borderRadius: 20, paddingVertical: 16, paddingHorizontal: 10 }}
      >
        <Text style={[typography.choice, { color: slot.text }]}>{name}</Text>
      </LinearGradient>
    </APressable>
  );
}
```

### Flame Pill (currency)

```tsx
// components/FlamePill.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FlamePill({ count, onGradient = true }: { count: number; onGradient?: boolean }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 6,
      paddingVertical: 7, paddingHorizontal: 14, borderRadius: 999,
      backgroundColor: onGradient ? 'rgba(255,255,255,0.22)' : 'rgba(255,122,69,0.15)',
    }}>
      <Text style={{ fontSize: 16 }}>🔥</Text>
      <Text style={[typography.pill, { color: onGradient ? '#FFFFFF' : colors.flame }]}>{count}</Text>
    </View>
  );
}
```

### Progress Dots

```tsx
// components/PollProgress.tsx
import { View } from 'react-native';

export function PollProgress({ total, index }: { total: number; index: number }) {
  return (
    <View style={{ flexDirection: 'row', gap: 6, justifyContent: 'center' }}>
      {Array.from({ length: total }).map((_, i) => (
        <View key={i} style={{
          width: i === index ? 30 : 22, height: 5, borderRadius: 3,
          backgroundColor: i === index ? '#FFFFFF' : 'rgba(255,255,255,0.35)',
        }} />
      ))}
    </View>
  );
}
```

### Primary Button

```tsx
// components/GasPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { appGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function GasPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}>
      {({ pressed }) => (
        <LinearGradient
          colors={appGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{
            borderRadius: 999, paddingVertical: 15, paddingHorizontal: 30,
            transform: [{ scale: pressed ? 0.97 : 1 }],
            shadowColor: '#6C5CE7', shadowOpacity: 0.45, shadowRadius: 24,
            shadowOffset: { width: 0, height: 10 }, elevation: 12,
          }}
        >
          <Text style={typography.button}>{title}</Text>
        </LinearGradient>
      )}
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { BlurView } from 'expo-blur';
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

function CenterAdd() {
  return (
    <View style={{
      width: 50, height: 50, borderRadius: 25, marginTop: -16,
      backgroundColor: '#FFFFFF', alignItems: 'center', justifyContent: 'center',
      shadowColor: '#281450', shadowOpacity: 0.5, shadowRadius: 20, shadowOffset: { width: 0, height: 8 }, elevation: 14,
    }}>
      <Ionicons name="add" size={26} color={colors.gasIndigo} />
    </View>
  );
}

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: '#FFFFFF',
        tabBarInactiveTintColor: 'rgba(255,255,255,0.6)',
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'rgba(255,255,255,0.16)' },
        tabBarBackground: () => <BlurView intensity={20} tint="light" style={{ flex: 1 }} />,
        tabBarLabelStyle: { fontFamily: 'Nunito-ExtraBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Polls',  tabBarIcon: ({ color }) => <Ionicons name="albums" size={23} color={color} /> }} />
      <Tabs.Screen name="inbox"  options={{ title: 'Inbox',  tabBarIcon: ({ color }) => <Ionicons name="checkmark-circle" size={23} color={color} /> }} />
      <Tabs.Screen name="add"    options={{ title: '',       tabBarIcon: () => <CenterAdd /> }} />
      <Tabs.Screen name="school" options={{ title: 'School', tabBarIcon: ({ color }) => <Ionicons name="business" size={23} color={color} /> }} />
      <Tabs.Screen name="you"    options={{ title: 'You',    tabBarIcon: ({ color }) => <Ionicons name="person" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Choice press — springy scale up
scale.value = withSpring(1.04, { damping: 6 }); // press in
scale.value = withSpring(1, { damping: 8 });    // release

// Card transition (vote) — fly out + next springs in
cardX.value = withTiming(-400, { duration: 280 }); // out
nextScale.value = withSpring(1, { damping: 7, stiffness: 120 }); // next in from 0.9

// Flame count-up — increment with per-tick haptic
// useEffect loop: setCount(c => c+1) every ~40ms, Haptics.impactAsync(Soft) per tick

// Milestone confetti
import ConfettiCannon from 'react-native-confetti-cannon';
// <ConfettiCannon count={80} origin={{x: W/2, y: 0}} colors={['#FF6CC8','#FFC93C','#2BC48A']} />

// Tab switch — bouncy crossfade; active icon pop scale 1 → 1.15 → 1

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);   // vote
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);     // each flame tick
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // milestone
Haptics.selectionAsync();                                  // skip / shuffle
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Emoji (🔥, 🔄, 😄) are first-class — render as `<Text>`, they ARE the brand voice.

| Purpose | Ionicons |
|---------|----------|
| Polls (tab) | `albums` / `albums-outline` |
| Inbox (tab) | `checkmark-circle` / `-outline` |
| Add (center) | `add` (in raised white circle) |
| School (tab) | `business` / `business-outline` |
| You (tab) | `person` / `person-outline` |
| Flame (currency) | 🔥 emoji (or `flame` tinted) |
| Shuffle | 🔄 emoji (or `shuffle`) |
| Skip | `chevron-forward` |
| Lock (hint) | `lock-closed` |
| Share / Invite | `share-outline` |
| Settings | `settings` |
| Back | `chevron-back` |
| Close | `close` |
| Sparkle | `sparkles` |

## 7. Platform Notes

- **Font choice**: Nunito is SIL OFL — free to bundle. Ship SemiBold → Black; Gas runs HEAVY (never below Bold)
- **Light-by-design**: set `userInterfaceStyle: "light"` in `app.json`; force `<StatusBar style="light" />` (the gradient is dark enough for white status content). Gas has NO dark theme — the bright purple gradient IS the brand; never invert
- **Safe area**: wrap screens in `SafeAreaView`; the frosted absolute tab bar needs bottom safe-area padding; the raised center circle must clear the home indicator
- **Phone-shaped on tablet**: cap the poll card at ~440pt max-width and keep it centered even on iPad — never spread to a wide grid
- **The 2×2 grid is sacred**: render choices with `flexBasis: '46%'` + wrap so they always form 2×2; never a single column or row
- **Yellow slot contrast**: choice slot 3 MUST use `#4A3300` text (white fails on `#FFC93C`) — enforce via `choiceGradients[2].text`
- **Confetti**: use `react-native-confetti-cannon` for milestones; pink/yellow/green particles
- **Accessibility**: each choice button `accessibilityRole="button"` labeled "{name}, choice {n} of 4"; the poll is one group "Poll: {question}"; set `allowFontScaling={false}` on choice labels, tab labels, tags, progress dots
- **Teen safety**: keep all copy positive; never render real identities in poll context; haptics on by default (part of the joy) but respect Reduce Motion / low-power
