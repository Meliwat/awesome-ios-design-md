# Grammarly (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grammarly's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand (single accent)
  green:        '#15C39A',
  greenDeep:    '#11A683',
  greenPressed: '#0E8A6D',
  greenOnBtn:   '#06281F', // text on green fills

  // Suggestion categories (underlines/dots only — never CTAs)
  correctness:  '#E5484D',
  clarity:      '#3B82F6',
  engagement:   '#16A34A',
  delivery:     '#8B5CF6',

  // Premium
  premiumGold:  '#E0A82E',

  // Surfaces (light)
  canvas:    '#FFFFFF',
  surface1:  '#F7F8F8',
  surface2:  '#EEF0F0',
  divider:   '#E4E6E6',

  // Surfaces (dark)
  darkCanvas:   '#121212',
  darkSurface1: '#1C1C1E',
  darkSurface2: '#262629',
  darkDivider:  '#2C2C2E',

  // Text
  textPrimary:     '#1A1A1A',
  textSecondary:   '#6B6B70',
  textTertiary:    '#9A9A9F',
  darkTextPrimary: '#E4E4E4',
} as const;

export type GmColor = keyof typeof colors;

// Category → color + underline style
export const categoryStyle = {
  Correctness: { color: colors.correctness, style: 'wavy'   as const },
  Clarity:     { color: colors.clarity,     style: 'fill'   as const },
  Engagement:  { color: colors.engagement,  style: 'dotted' as const },
  Delivery:    { color: colors.delivery,    style: 'solid'  as const },
};
```

## 2. Typography

Grammarly's brand face is Apercu (licensed); SF Pro on iOS. Bundle **Inter** via `expo-font` as the closest free face, or use the system font for an SF-Pro build.

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
import type { TextStyle } from 'react-native';

const primary = { color: '#1A1A1A' } satisfies TextStyle;

export const typography = {
  screenTitle:   { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  docHeading:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.4 },
  docSubhead:    { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.3 },
  cardTitle:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 23 },
  body:          { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 26 }, // ~1.65
  suggestTitle:  { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20 },
  swap:          { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 16, lineHeight: 22 },
  explain:       { color: '#6B6B70', fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 21 },
  action:        { fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 13 },
  categoryTag:   { color: '#6B6B70', fontFamily: 'Inter-Bold', fontSize: 12, lineHeight: 12, letterSpacing: 0.4 },
  tonePill:      { fontFamily: 'Inter-SemiBold', fontSize: 12, lineHeight: 12, letterSpacing: 0.2 },
  scoreNum:      { fontFamily: 'Inter-Bold', fontSize: 17, lineHeight: 17 },
  barLabel:      { fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 13, letterSpacing: 0.1 },
  button:        { fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Suggestion Card (core atom)

```tsx
// components/SuggestionCard.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { SlideInDown, SlideOutDown } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors, categoryStyle } from '../theme/colors';
import { typography } from '../theme/typography';

type Cat = keyof typeof categoryStyle;

export function SuggestionCard({
  category, oldText, newText, explanation, onAccept, onDismiss,
}: {
  category: Cat; oldText: string; newText: string; explanation: string;
  onAccept: () => void; onDismiss: () => void;
}) {
  const cat = categoryStyle[category];
  return (
    <Animated.View
      entering={SlideInDown.springify().damping(16)}
      exiting={SlideOutDown.duration(200)}
      style={{
        marginHorizontal: 16,
        backgroundColor: colors.darkSurface1,
        borderRadius: 16,
        borderWidth: 1, borderColor: colors.darkDivider,
        padding: 16,
        shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 24, shadowOffset: { width: 0, height: 8 },
        elevation: 12,
      }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, marginBottom: 10 }}>
        <View style={{ width: 10, height: 10, borderRadius: 5, backgroundColor: cat.color }} />
        <Text style={[typography.categoryTag]}>{category.toUpperCase()}</Text>
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, marginBottom: 6 }}>
        <Text style={[typography.swap, { color: cat.color, textDecorationLine: 'line-through' }]}>{oldText}</Text>
        <Text style={[typography.swap, { color: colors.textTertiary }]}>→</Text>
        <Text style={[typography.swap, { color: colors.green }]}>{newText}</Text>
      </View>

      <Text style={[typography.explain, { marginBottom: 14 }]} numberOfLines={2}>{explanation}</Text>

      <View style={{ flexDirection: 'row', gap: 10 }}>
        <Pressable
          onPress={() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); onAccept(); }}
          style={({ pressed }) => ({
            flex: 1, alignItems: 'center', paddingVertical: 10, borderRadius: 999,
            backgroundColor: pressed ? colors.greenPressed : colors.green,
          })}
        >
          <Text style={[typography.action, { color: colors.greenOnBtn }]}>Accept</Text>
        </Pressable>
        <Pressable
          onPress={onDismiss}
          style={{ width: 44, height: 40, borderRadius: 999, borderWidth: 1, borderColor: colors.darkDivider, alignItems: 'center', justifyContent: 'center' }}
        >
          <Ionicons name="close" size={16} color={colors.textTertiary} />
        </Pressable>
      </View>
    </Animated.View>
  );
}
```

### Document Score Ring

```tsx
// components/ScoreRing.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const ACircle = Animated.createAnimatedComponent(Circle);

export function ScoreRing({ score, size = 38 }: { score: number; size?: number }) {
  const r = size / 2 - 2;
  const c = 2 * Math.PI * r;
  const tint = score >= 90 ? colors.green : score >= 60 ? colors.premiumGold : colors.correctness;
  const progress = useSharedValue(0);

  useEffect(() => {
    progress.value = withTiming(score / 100, { duration: 600, easing: Easing.out(Easing.cubic) });
  }, [score]);

  const animatedProps = useAnimatedProps(() => ({ strokeDashoffset: c * (1 - progress.value) }));

  return (
    <View style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={size} height={size} style={{ position: 'absolute', transform: [{ rotate: '-90deg' }] }}>
        <Circle cx={size / 2} cy={size / 2} r={r} stroke={colors.darkDivider} strokeWidth={3} fill="none" />
        <ACircle
          cx={size / 2} cy={size / 2} r={r}
          stroke={tint} strokeWidth={3} fill="none"
          strokeDasharray={c} strokeLinecap="round" animatedProps={animatedProps}
        />
      </Svg>
      <Text style={[size > 48 ? typography.scoreNum : typography.action, { color: tint }]}>{score}</Text>
    </View>
  );
}
```

### Colored Underline

```tsx
// components/MarkedSpan.tsx
import { Text } from 'react-native';
import { categoryStyle } from '../theme/colors';

export function MarkedSpan({ children, category, onPress }: {
  children: string; category: keyof typeof categoryStyle; onPress: () => void;
}) {
  const c = categoryStyle[category];
  // Clarity = highlight fill + underline; others = colored underline (RN can't draw wavy/dotted natively —
  // use a custom SVG underline overlay for production parity)
  return (
    <Text
      onPress={onPress}
      style={{
        textDecorationLine: 'underline',
        textDecorationColor: c.color,
        backgroundColor: category === 'Clarity' ? `${c.color}2E` : undefined,
        borderRadius: category === 'Clarity' ? 2 : 0,
      }}
    >
      {children}
    </Text>
  );
}
```

### Tone Detector Row

```tsx
// components/ToneRow.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ToneRow({ emoji, tones }: { emoji: string; tones: string[] }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, paddingHorizontal: 20, paddingVertical: 10 }}>
      <Text style={{ fontSize: 18 }}>{emoji}</Text>
      <Text style={[typography.tonePill, { color: colors.textSecondary }]}>Tone sounds</Text>
      {tones.map((t) => (
        <View key={t} style={{
          backgroundColor: colors.darkSurface2, borderColor: colors.darkDivider, borderWidth: 1,
          borderRadius: 999, paddingVertical: 5, paddingHorizontal: 12,
        }}>
          <Text style={[typography.tonePill, { color: colors.darkTextPrimary }]}>{t}</Text>
        </View>
      ))}
    </View>
  );
}
```

### Assistant Bar

```tsx
// components/AssistantBar.tsx
import { Pressable, Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AssistantBar({ total, breakdown, onReview }: {
  total: number; breakdown: string; onReview: () => void;
}) {
  return (
    <BlurView intensity={40} tint="dark" style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      height: 64, paddingHorizontal: 18,
      borderTopWidth: 0.5, borderTopColor: colors.darkDivider,
    }}>
      <View style={{ width: 36, height: 36, borderRadius: 18, backgroundColor: colors.green, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="logo-buffer" size={18} color="#fff" /> {/* stand-in for rotated-G */}
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.barLabel, { color: colors.darkTextPrimary, fontFamily: 'Inter-Bold' }]}>
          {total === 0 ? 'All clear' : `${total} suggestions`}
        </Text>
        {total > 0 && <Text style={[typography.barLabel, { color: colors.textTertiary }]}>{breakdown}</Text>}
      </View>
      {total > 0 && (
        <Pressable onPress={onReview} style={{ backgroundColor: colors.green, borderRadius: 999, paddingVertical: 9, paddingHorizontal: 16 }}>
          <Text style={[typography.action, { color: colors.greenOnBtn }]}>Review</Text>
        </Pressable>
      )}
    </BlurView>
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
        tabBarActiveTintColor: colors.green,           // icon + label go green; no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'Inter-SemiBold', fontSize: 11, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Documents', tabBarIcon: ({ color }) => <Ionicons name="document-text-outline" size={24} color={color} /> }} />
      <Tabs.Screen name="assistant" options={{ title: 'Assistant', tabBarIcon: ({ color }) => <Ionicons name="logo-buffer" size={24} color={color} /> }} />
      <Tabs.Screen name="account"   options={{ title: 'Account',   tabBarIcon: ({ color }) => <Ionicons name="person-circle-outline" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Suggestion card present / dismiss
import Animated, { SlideInDown, SlideOutDown } from 'react-native-reanimated';
// entering={SlideInDown.springify().damping(16)}  (~280ms)
// exiting={SlideOutDown.duration(200)}

// Accept: crossfade old→new text + green wash flash
// withTiming on a sharedValue 1 → 0 over 600ms for the flash opacity

// Score ring: withTiming(score/100, { duration: 600, easing: Easing.out(Easing.cubic) })

// Assistant-bar count: animate a Reanimated number (or react-native-animateable-text) over 250ms

// Haptics
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // Accept
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);              // score-ring complete
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Grammarly's rotated-G is a brand mark — render it as a bundled SVG via `react-native-svg` for production fidelity; the table uses the nearest stand-in.

| Purpose | Ionicons |
|---------|----------|
| Documents (tab) | `document-text-outline` |
| Assistant (tab / orb) | bundled SVG (stand-in `logo-buffer`) |
| Account (tab) | `person-circle-outline` |
| Back | `chevron-back` |
| Dismiss suggestion | `close` |
| Premium lock | `lock-closed` |
| Search | `search` |
| Share | `share-outline` |
| Settings | `settings-outline` |
| New document | `add` |
| Plagiarism flag | `warning` |
| Insights | `stats-chart` |
| Tone sentiment | emoji glyphs (😊🙂😐😟), not an icon font |

## 7. Platform Notes

- **Font choice**: bundle Inter (SIL OFL — free) as the closest face, or use the system font for an SF-Pro look. Do **not** redistribute Apercu (Grammarly's licensed brand face).
- **Wavy / dotted underlines**: React Native's `textDecorationLine` only does solid; for the Correctness wavy and Engagement dotted styles, overlay a custom `react-native-svg` path beneath the text run measured with `onTextLayout`.
- **Status bar**: `<StatusBar style="dark" />` on light, `"light"` on dark.
- **Safe area**: wrap screens in `SafeAreaView`; the assistant bar and suggestion card need bottom safe-area padding and must lift above the keyboard (`KeyboardAvoidingView` / `useAnimatedKeyboard`).
- **One card at a time**: keep a single `activeSuggestion` in state; never render a stack. The Review walkthrough advances the index.
- **Dynamic Type**: allow scaling on body/headings; set `allowFontScaling={false}` on category tags, tone pills, score number, bar/tab labels.
- **Dark mode**: `useColorScheme()` swaps to `darkCanvas` / `darkSurface1`; brighten category hues (`#F05A5F` / `#5A95F8` / `#22B85A` / `#A07CF8`) on dark.
- **Accessibility**: marked spans must be reachable as accessible elements with category + suggestion read aloud, and expose Accept/Dismiss as `accessibilityActions` so the card need not be opened.
- **Score ring**: give it `accessibilityRole="progressbar"` with `accessibilityValue={{ now: score, min: 0, max: 100 }}`.
