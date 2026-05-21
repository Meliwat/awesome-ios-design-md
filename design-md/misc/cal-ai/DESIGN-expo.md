# Cal AI (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md). This file translates Cal AI's visual language into paste-ready Expo / React Native code.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-blur`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Dark (primary)
  canvas:    '#0A0A0A',
  surface1:  '#151515',
  surface2:  '#1E1E1E',
  surface3:  '#2A2A2A',
  divider:   '#252525',

  textPrimary:   '#FFFFFF',
  textSecondary: '#A1A1A1',
  textTertiary:  '#6E6E6E',

  // Macro trio
  protein: '#4DA8FF',
  carbs:   '#FFB54C',
  fat:     '#FF6E87',

  // Semantic
  success:     '#42E17D',
  warning:     '#FFB54C',
  destructive: '#FF5A5A',
  aiAccent:    '#8B8DF5',

  // Light (secondary)
  lightCanvas:    '#FFFFFF',
  lightSurface1:  '#F5F5F5',
  lightText:      '#0A0A0A',
  lightSecondary: '#5E5E5E',
} as const;
```

## 2. Typography

Register Inter via `expo-font`. Use `fontVariant: ['tabular-nums']` on every numeric text.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-Semibold': require('../assets/fonts/Inter-SemiBold.ttf'),
    'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const T = (overrides: TextStyle): TextStyle => ({ color: '#FFFFFF', ...overrides });
const tabular = { fontVariant: ['tabular-nums' as const] };

export const typography = {
  hero:        T({ fontFamily: 'Inter-Bold',     fontSize: 72, lineHeight: 72, letterSpacing: -1.5, ...tabular }),
  mealCals:    T({ fontFamily: 'Inter-Bold',     fontSize: 40, lineHeight: 40, letterSpacing: -0.8, ...tabular }),
  streak:      T({ fontFamily: 'Inter-Bold',     fontSize: 48, lineHeight: 48, letterSpacing: -1, ...tabular }),
  screenTitle: T({ fontFamily: 'Inter-Bold',     fontSize: 28, lineHeight: 31, letterSpacing: -0.4 }),
  section:     T({ fontFamily: 'Inter-Bold',     fontSize: 22, lineHeight: 26, letterSpacing: -0.3 }),
  navTitle:    T({ fontFamily: 'Inter-Semibold', fontSize: 17, lineHeight: 19, letterSpacing: -0.1 }),
  cardTitle:   T({ fontFamily: 'Inter-Semibold', fontSize: 17, lineHeight: 22, letterSpacing: -0.15 }),
  body:        T({ fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 21 }),
  macroChip:   T({ fontFamily: 'Inter-Semibold', fontSize: 12, lineHeight: 14, ...tabular }),
  meta:        T({ fontFamily: 'Inter-Medium',   fontSize: 13, lineHeight: 17, color: '#A1A1A1', ...tabular }),
  labelUpper:  T({ fontFamily: 'Inter-Bold',     fontSize: 11, lineHeight: 13, letterSpacing: 0.8, color: '#A1A1A1', textTransform: 'uppercase' }),
  button:      T({ fontFamily: 'Inter-Semibold', fontSize: 16, lineHeight: 20, color: '#000000' }),
  tab:         T({ fontFamily: 'Inter-Semibold', fontSize: 10, lineHeight: 12, letterSpacing: 0.2 }),
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Primary CTA (White Pill — black text)

```tsx
// components/PrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: '#FFFFFF',
        paddingVertical: 16,
        paddingHorizontal: 28,
        borderRadius: 28,
        alignItems: 'center',
        transform: [{ scale: pressed ? 0.97 : 1 }],
        opacity: pressed ? 0.92 : 1,
      })}
    >
      <Text style={[typography.button, { color: '#000' }]}>{title}</Text>
    </Pressable>
  );
}
```

### Capture FAB

```tsx
// components/CaptureFAB.tsx
import { Pressable, View, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function CaptureFAB({ onPress }: { onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.92, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
      hitSlop={20}
    >
      <Animated.View style={[styles.fab, style]}>
        <View style={styles.inner} />
      </Animated.View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  fab: {
    width: 68, height: 68, borderRadius: 34,
    backgroundColor: '#FFFFFF',
    alignItems: 'center', justifyContent: 'center',
    shadowColor: '#FFFFFF', shadowOpacity: 0.18, shadowRadius: 40, shadowOffset: { width: 0, height: 0 },
    elevation: 10,
  },
  inner: {
    width: 52, height: 52, borderRadius: 26,
    borderWidth: 2, borderColor: colors.canvas,
  },
});
```

### Macro Ring

```tsx
// components/MacroRing.tsx
import { Text, View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type MacroKey = 'protein' | 'carbs' | 'fat';
const LABELS: Record<MacroKey, { color: string; label: string }> = {
  protein: { color: colors.protein, label: 'PROTEIN' },
  carbs:   { color: colors.carbs,   label: 'CARBS' },
  fat:     { color: colors.fat,     label: 'FAT' },
};

export function MacroRing({
  macro, current, target, size = 80,
}: { macro: MacroKey; current: number; target: number; size?: number }) {
  const { color, label } = LABELS[macro];
  const radius = (size - 8) / 2;
  const circumference = 2 * Math.PI * radius;
  const progress = Math.min(current / target, 1);
  const offset = circumference * (1 - progress);

  return (
    <View style={{ alignItems: 'center', gap: 6 }}>
      <View style={{ width: size, height: size }}>
        <Svg width={size} height={size} style={{ transform: [{ rotate: '-90deg' }] }}>
          <Circle cx={size/2} cy={size/2} r={radius} stroke={colors.surface3} strokeWidth={6} fill="none" />
          <Circle cx={size/2} cy={size/2} r={radius} stroke={color} strokeWidth={6} fill="none"
                  strokeLinecap="round" strokeDasharray={circumference} strokeDashoffset={offset} />
        </Svg>
        <View style={{ position: 'absolute', inset: 0, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={{ fontFamily: 'Inter-Bold', fontSize: 18, color: '#FFF', fontVariant: ['tabular-nums'] }}>
            {Math.round(current)}
            <Text style={{ fontSize: 9, fontFamily: 'Inter-Medium', color: colors.textSecondary }}>g</Text>
          </Text>
        </View>
      </View>
      <Text style={[typography.labelUpper]}>{label}</Text>
    </View>
  );
}
```

### Meal Card (Photo + Stats)

```tsx
// components/MealCard.tsx
import { Image, Text, View, StyleSheet } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MealCard({
  title, time, servings, protein, carbs, fat, calories, photoUri,
}: {
  title: string; time: string; servings: string;
  protein: number; carbs: number; fat: number; calories: number; photoUri: string;
}) {
  return (
    <View style={styles.card}>
      <View style={styles.photoWrap}>
        <Image source={{ uri: photoUri }} style={styles.photo} />
        <LinearGradient
          colors={['transparent', 'rgba(0,0,0,0.4)']}
          style={styles.gradient}
        />
      </View>
      <View style={styles.info}>
        <Text style={typography.cardTitle}>{title}</Text>
        <Text style={typography.meta}>{time} · {servings}</Text>
        <View style={styles.bottomRow}>
          <View style={{ flexDirection: 'row', gap: 6 }}>
            <MacroChip label="P" value={protein} color={colors.protein} />
            <MacroChip label="C" value={carbs}   color={colors.carbs}   />
            <MacroChip label="F" value={fat}     color={colors.fat}     />
          </View>
          <Text style={typography.mealCals}>{calories}</Text>
        </View>
      </View>
    </View>
  );
}

function MacroChip({ label, value, color }: { label: string; value: number; color: string }) {
  return (
    <View style={{ paddingVertical: 3, paddingHorizontal: 8, borderRadius: 10, backgroundColor: color + '2E' }}>
      <Text style={[typography.macroChip, { color }]}>{label} {value}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  card:       { backgroundColor: colors.surface1, borderRadius: 20, overflow: 'hidden', marginBottom: 12 },
  photoWrap:  { aspectRatio: 16/10, overflow: 'hidden' },
  photo:      { width: '100%', height: '100%' },
  gradient:   { position: 'absolute', left: 0, right: 0, bottom: 0, height: 60 },
  info:       { padding: 16, gap: 10 },
  bottomRow:  { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' },
});
```

### AI Detection Card (with animated gradient bar)

```tsx
// components/AIDetectionCard.tsx
import { Text, View, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AIDetectionCard({
  title, items, onSave, onReject,
}: {
  title: string;
  items: { name: string; confidence: number }[];
  onSave: () => void;
  onReject: () => void;
}) {
  return (
    <View style={{
      backgroundColor: colors.surface2,
      borderRadius: 20,
      padding: 16,
      overflow: 'hidden',
    }}>
      <LinearGradient
        colors={[colors.surface2, colors.surface3]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ position: 'absolute', inset: 0 }}
      />
      <LinearGradient
        colors={[colors.protein, colors.aiAccent, colors.fat]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
        style={{ position: 'absolute', top: 0, left: 0, right: 0, height: 2 }}
      />
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4, marginBottom: 8 }}>
        <Text style={[typography.labelUpper, { color: colors.aiAccent, letterSpacing: 0.6 }]}>◆ AI DETECTED</Text>
      </View>
      <Text style={[typography.cardTitle, { marginBottom: 8 }]}>{title}</Text>
      <View style={{ marginBottom: 12, gap: 4 }}>
        {items.map(it => (
          <View key={it.name} style={{ flexDirection: 'row', alignItems: 'baseline', gap: 4 }}>
            <Text style={[typography.body, { color: colors.textSecondary, fontSize: 13 }]}>{it.name}</Text>
            <Text style={{ fontFamily: 'Inter-Regular', fontSize: 11, color: colors.textTertiary }}>{it.confidence}%</Text>
          </View>
        ))}
      </View>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 10 }}>
        <Pressable onPress={onSave} style={{
          backgroundColor: '#FFF', paddingVertical: 12, paddingHorizontal: 20, borderRadius: 20,
        }}>
          <Text style={{ fontFamily: 'Inter-Semibold', fontSize: 14, color: '#000' }}>Save to today</Text>
        </Pressable>
        <Pressable onPress={onReject}>
          <Text style={{ fontFamily: 'Inter-Semibold', fontSize: 14, color: colors.destructive }}>Reject</Text>
        </Pressable>
      </View>
    </View>
  );
}
```

## 4. Tab Bar (with centered Capture FAB)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { CaptureFAB } from '../../components/CaptureFAB';
import { colors } from '../../theme/colors';
import { router } from 'expo-router';

export default function TabsLayout() {
  return (
    <>
      <Tabs
        screenOptions={{
          tabBarActiveTintColor: '#FFFFFF',
          tabBarInactiveTintColor: colors.textTertiary,
          tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent', height: 84 },
          tabBarBackground: () => (
            <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(10,10,10,0.6)' }} />
          ),
          tabBarLabelStyle: { fontFamily: 'Inter-Semibold', fontSize: 10, letterSpacing: 0.2 },
          tabBarItemStyle: { marginTop: 4 },
        }}
      >
        <Tabs.Screen name="index"    options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"     size={22} color={color} /> }} />
        <Tabs.Screen name="history"  options={{ title: 'History', tabBarIcon: ({ color }) => <Ionicons name="calendar" size={22} color={color} /> }} />
        <Tabs.Screen name="spacer"   options={{ title: '', tabBarIcon: () => <View style={{ width: 48 }} /> }} />
        <Tabs.Screen name="insights" options={{ title: 'Insights', tabBarIcon: ({ color }) => <Ionicons name="stats-chart" size={22} color={color} /> }} />
        <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person"   size={22} color={color} /> }} />
      </Tabs>
      <View style={{ position: 'absolute', bottom: 46, left: 0, right: 0, alignItems: 'center' }}>
        <CaptureFAB onPress={() => router.push('/capture')} />
      </View>
    </>
  );
}
```

## 5. Motion & Haptics

```tsx
// AI processing shimmer bar
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';

export function AIShimmerBar({ width }: { width: number }) {
  const x = useSharedValue(-80);
  useEffect(() => {
    x.value = withRepeat(
      withTiming(width, { duration: 1200 }),
      -1,  // infinite
      false
    );
  }, [width]);
  const style = useAnimatedStyle(() => ({ transform: [{ translateX: x.value }] }));
  return (
    <View style={{ height: 2, width, overflow: 'hidden', backgroundColor: 'rgba(139,141,245,0.2)' }}>
      <Animated.View style={[{ width: 80, height: 2, backgroundColor: colors.aiAccent }, style]} />
    </View>
  );
}
```

## 6. Icon Mappings

| Purpose | Ionicons (`@expo/vector-icons`) |
|---------|--------------------------------|
| Home (tab) | `home-outline` / `home` |
| History (tab) | `calendar-outline` / `calendar` |
| Insights (tab) | `stats-chart-outline` / `stats-chart` |
| Profile (tab) | `person-outline` / `person` |
| AI sparkle | `sparkles-outline` |
| Flame (streak) | `flame` |
| Barcode | `barcode-outline` |
| Library | `images-outline` |
| Camera close | `close` |
| Flash | `flash-outline` / `flash` |

## 7. Platform Notes

- **Safe area**: wrap all screens in `SafeAreaView` from `react-native-safe-area-context`
- **Status bar**: `<StatusBar style="light" />` from `expo-status-bar` for dark mode
- **Tabular numerals**: every numeric `<Text>` must include `fontVariant: ['tabular-nums']` or numbers will shift during updates
- **Haptics fallback**: on Android, `expo-haptics` maps `Medium` impact to a short vibration; on iPhone, ensure the device has haptic engine (iPhone 7+)
- **Blur fallback**: `expo-blur` falls back to a semi-transparent solid on Android — acceptable degradation for this app
- **Dynamic Type**: React Native respects system font scale; lock the hero numeral to `allowFontScaling={false}` to prevent layout break, or set `maxFontSizeMultiplier={1.4}`
- **Dark mode lock**: Cal AI is dark-primary. Either lock via `userInterfaceStyle: 'dark'` in `app.json` or respect system: `useColorScheme()`
