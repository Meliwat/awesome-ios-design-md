# Starbucks (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Starbucks' visual language into paste-ready Expo / React Native code: design tokens, the Stars ring, size selector, category card, syrup stepper, and scan-to-pay card.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `react-native-svg`, `expo-haptics`, `expo-linear-gradient`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas
  canvas:         '#F9F9F9',
  cardSurface:    '#FFFFFF',
  surfaceMuted:   '#F1F1F1',
  divider:        '#E8E8E8',

  // Text
  textPrimary:    '#000000',
  textSecondary:  '#595959',
  textTertiary:   '#8E8E8E',

  // Brand
  sirenGreen:     '#00704A',
  greenPressed:   '#00563A',
  houseGreen:     '#1E3932',
  mint:           '#D4E9E2',

  // Gold
  goldStar:       '#CBA258',
  goldAlt:        '#C1A265',
  goldLight:      '#E4C896',

  // Semantic
  successGreen:   '#00754A',
  alertRed:       '#DD3333',
  infoBlue:       '#006FCF',
  warningAmber:   '#F5A623',

  // Dark
  darkCanvas:     '#1E3932',
  darkSurface1:   '#2D4A43',
  darkSurface2:   '#3B5852',
  darkDivider:    '#3A4F48',
  darkTextPrim:   '#FFFFFF',
  darkTextSec:    '#C9CDCB',
} as const;

export const goldGradient = [colors.goldStar, colors.goldLight, colors.goldStar] as const;
```

## 2. Typography

SoDo Sans is proprietary (licensed from Dalton Maag). Bundle via `expo-font` if available; otherwise fall back to Lato or System (SF Pro).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // If you have SoDo Sans licensed:
    // 'SoDoSans-Regular':   require('../assets/fonts/SoDoSans-Regular.ttf'),
    // 'SoDoSans-Semibold':  require('../assets/fonts/SoDoSans-Semibold.ttf'),
    // 'SoDoSans-Bold':      require('../assets/fonts/SoDoSans-Bold.ttf'),
    // 'SoDoSans-ExtraBold': require('../assets/fonts/SoDoSans-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { fontFamily: 'System' } satisfies TextStyle;

export const typography = {
  starsHero:     { ...base, fontWeight: '800', fontSize: 48, lineHeight: 48, letterSpacing: -0.5, color: '#CBA258' },
  screenTitle:   { ...base, fontWeight: '700', fontSize: 28, lineHeight: 32, letterSpacing: -0.3, color: '#000000' },
  sheetTitle:    { ...base, fontWeight: '700', fontSize: 22, lineHeight: 26, letterSpacing: -0.2, color: '#000000' },
  section:       { ...base, fontWeight: '700', fontSize: 18, lineHeight: 22, color: '#000000' },
  cardTitle:     { ...base, fontWeight: '600', fontSize: 16, lineHeight: 20, color: '#FFFFFF' },
  drinkTitle:    { ...base, fontWeight: '700', fontSize: 22, lineHeight: 26, letterSpacing: -0.2, color: '#000000' },
  body:          { ...base, fontWeight: '400', fontSize: 15, lineHeight: 22, color: '#000000' },
  meta:          { ...base, fontWeight: '400', fontSize: 13, lineHeight: 17, color: '#595959' },
  price:         { ...base, fontWeight: '700', fontSize: 17, lineHeight: 19, color: '#000000' },
  starsInline:   { ...base, fontWeight: '800', fontSize: 22, lineHeight: 22, color: '#CBA258' },
  button:        { ...base, fontWeight: '700', fontSize: 16, letterSpacing: 0.2, color: '#FFFFFF' },
  buttonSmall:   { ...base, fontWeight: '600', fontSize: 15, color: '#00704A' },
  size:          { ...base, fontWeight: '600', fontSize: 13, letterSpacing: 0.2, color: '#000000' },
  tab:           { ...base, fontWeight: '600', fontSize: 11, letterSpacing: 0.1, color: '#595959' },
  chip:          { ...base, fontWeight: '700', fontSize: 12, letterSpacing: 0.3, color: '#CBA258' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Stars Progress Ring

```tsx
// components/StarbucksStarsRing.tsx
import React from 'react';
import { Text, View } from 'react-native';
import Svg, { Circle, Defs, LinearGradient, Stop } from 'react-native-svg';
import Animated, {
  useSharedValue, useAnimatedProps, withTiming, Easing,
} from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);
const SIZE = 180;
const STROKE = 12;
const RADIUS = (SIZE - STROKE) / 2;
const CIRCUMFERENCE = 2 * Math.PI * RADIUS;

export function StarbucksStarsRing({
  currentStars, targetStars,
}: { currentStars: number; targetStars: number }) {
  const progress = useSharedValue(0);

  React.useEffect(() => {
    progress.value = withTiming(currentStars / targetStars, {
      duration: 1500, easing: Easing.out(Easing.ease),
    });
  }, []);

  const circleProps = useAnimatedProps(() => ({
    strokeDashoffset: CIRCUMFERENCE * (1 - progress.value),
  }));

  return (
    <View style={{ alignItems: 'center', gap: 16 }}>
      <View style={{ width: SIZE, height: SIZE }}>
        <Svg width={SIZE} height={SIZE} style={{ transform: [{ rotate: '-90deg' }] }}>
          <Defs>
            <LinearGradient id="goldGrad" x1="0" y1="0" x2="1" y2="0">
              <Stop offset="0"    stopColor={colors.goldStar} />
              <Stop offset="0.5"  stopColor={colors.goldLight} />
              <Stop offset="1"    stopColor={colors.goldStar} />
            </LinearGradient>
          </Defs>
          <Circle
            cx={SIZE / 2} cy={SIZE / 2} r={RADIUS}
            stroke={colors.divider} strokeWidth={STROKE} fill="transparent"
          />
          <AnimatedCircle
            cx={SIZE / 2} cy={SIZE / 2} r={RADIUS}
            stroke="url(#goldGrad)" strokeWidth={STROKE} fill="transparent"
            strokeDasharray={CIRCUMFERENCE}
            strokeLinecap="round"
            animatedProps={circleProps}
          />
        </Svg>
        <View style={{
          position: 'absolute', inset: 0, alignItems: 'center', justifyContent: 'center',
        }}>
          <Text style={[typography.starsHero, { fontVariant: ['tabular-nums'] }]}>{currentStars}</Text>
          <Text style={{ ...typography.meta, fontWeight: '600' }}>Stars</Text>
        </View>
      </View>
      <Text style={typography.body}>
        {targetStars - currentStars} Stars until free reward
      </Text>
    </View>
  );
}
```

### Size Selector

```tsx
// components/StarbucksSizeSelector.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type CoffeeSize = 'Short' | 'Tall' | 'Grande' | 'Venti' | 'Trenta';

const sizes: { size: CoffeeSize; scale: number }[] = [
  { size: 'Short',  scale: 0.55 },
  { size: 'Tall',   scale: 0.7 },
  { size: 'Grande', scale: 0.85 },
  { size: 'Venti',  scale: 1.0 },
  { size: 'Trenta', scale: 1.15 },
];

export function StarbucksSizeSelector({
  selected, onSelect,
}: { selected: CoffeeSize; onSelect: (s: CoffeeSize) => void }) {
  return (
    <View style={{ flexDirection: 'row', gap: 8 }}>
      {sizes.map(({ size, scale }) => {
        const active = size === selected;
        return (
          <Pressable
            key={size}
            onPress={() => { Haptics.selectionAsync(); onSelect(size); }}
            style={{
              paddingHorizontal: 14,
              minHeight: 36,
              borderRadius: 18,
              alignItems: 'center', justifyContent: 'center',
              backgroundColor: active ? colors.sirenGreen : 'transparent',
              borderWidth: active ? 0 : 1.5,
              borderColor: colors.divider,
              flexDirection: 'column', gap: 2,
            }}
          >
            <Ionicons name="cafe" size={20 * scale} color={active ? '#FFFFFF' : colors.textPrimary} />
            <Text style={[typography.size, { color: active ? '#FFFFFF' : colors.textPrimary }]}>{size}</Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

### Primary CTA

```tsx
// components/StarbucksPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StarbucksPrimaryButton({
  title, onPress,
}: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
      style={({ pressed }) => ({
        height: 52, borderRadius: 26,
        backgroundColor: pressed ? colors.greenPressed : colors.sirenGreen,
        alignItems: 'center', justifyContent: 'center',
        paddingHorizontal: 24,
        opacity: pressed ? 0.95 : 1,
        transform: [{ scale: pressed ? 0.98 : 1 }],
        shadowColor: colors.sirenGreen, shadowOpacity: 0.2, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Category Card

```tsx
// components/StarbucksCategoryCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import * as Haptics from 'expo-haptics';
import { typography } from '../theme/typography';

export function StarbucksCategoryCard({
  title, imageUri, onPress,
}: { title: string; imageUri: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        aspectRatio: 2/3,
        borderRadius: 16, overflow: 'hidden',
        transform: [{ scale: pressed ? 0.98 : 1 }],
        shadowColor: '#000', shadowOpacity: 0.08, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
      })}
    >
      <Image source={{ uri: imageUri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
      <LinearGradient
        colors={['transparent', 'rgba(0,0,0,0.5)']}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '50%' }}
      />
      <View style={{ position: 'absolute', left: 16, bottom: 16 }}>
        <Text style={typography.cardTitle}>{title}</Text>
      </View>
    </Pressable>
  );
}
```

### Syrup Pump Stepper

```tsx
// components/StarbucksPumpStepper.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function StarbucksPumpStepper({
  label, count, onChange,
}: { label: string; count: number; onChange: (n: number) => void }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center',
      paddingVertical: 12,
      borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <Text style={[typography.buttonSmall, { color: colors.textPrimary, flex: 1 }]}>{label}</Text>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
        <StepButton icon="remove" onPress={() => count > 0 && onChange(count - 1)} />
        <Text style={[typography.price, { minWidth: 44, textAlign: 'center', fontVariant: ['tabular-nums'] }]}>
          {count}
        </Text>
        <StepButton icon="add" onPress={() => onChange(count + 1)} />
      </View>
    </View>
  );
}

function StepButton({ icon, onPress }: { icon: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onPress(); }}
      style={({ pressed }) => ({
        width: 36, height: 36, borderRadius: 18,
        alignItems: 'center', justifyContent: 'center',
        borderWidth: 1.5, borderColor: colors.divider,
        transform: [{ scale: pressed ? 0.9 : 1 }],
      })}
    >
      <Ionicons name={icon as any} size={14} color={colors.textPrimary} />
    </Pressable>
  );
}
```

### Scan-to-Pay Barcode Card

```tsx
// components/StarbucksScanPayCard.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, goldGradient } from '../theme/colors';
import { typography } from '../theme/typography';
import { StarbucksPrimaryButton } from './StarbucksPrimaryButton';

export function StarbucksScanPayCard({
  stars, balance, barcodeNumber,
}: { stars: number; balance: number; barcodeNumber: string }) {
  return (
    <View style={{ gap: 20, paddingHorizontal: 16 }}>
      {/* Stars summary */}
      <LinearGradient
        colors={goldGradient as unknown as string[]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
        style={{ borderRadius: 16, padding: 16, flexDirection: 'row', alignItems: 'center', gap: 8, opacity: 0.9 }}
      >
        <Ionicons name="star" size={20} color="#FFFFFF" />
        <Text style={{ ...typography.starsInline, color: '#FFFFFF', fontVariant: ['tabular-nums'] }}>
          {stars} Stars
        </Text>
        <View style={{ flex: 1 }} />
        <Text style={{ ...typography.chip, color: '#FFFFFF' }}>Gold Member</Text>
      </LinearGradient>

      {/* Barcode */}
      <View style={{ backgroundColor: colors.cardSurface, borderRadius: 20, padding: 20, alignItems: 'center', gap: 12 }}>
        <BarcodeMock />
        <Text style={[typography.meta, { letterSpacing: 2, fontVariant: ['tabular-nums'] }]}>
          {barcodeNumber}
        </Text>
      </View>

      {/* Balance + Reload */}
      <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16 }}>
        <View style={{ flex: 1 }}>
          <Text style={typography.meta}>Balance</Text>
          <Text style={[typography.drinkTitle, { fontVariant: ['tabular-nums'] }]}>
            ${balance.toFixed(2)}
          </Text>
        </View>
        <View style={{ minWidth: 120 }}>
          <StarbucksPrimaryButton title="Reload" onPress={() => {}} />
        </View>
      </View>
    </View>
  );
}

function BarcodeMock() {
  // In production, render a real Code128 via a library (e.g. react-native-svg + jsbarcode logic)
  const widths = Array.from({ length: 60 }, () => Math.floor(Math.random() * 3) + 1);
  return (
    <View style={{ flexDirection: 'row', height: 80, gap: 1 }}>
      {widths.map((w, i) => (
        <View key={i} style={{ width: w, height: '100%', backgroundColor: '#000' }} />
      ))}
    </View>
  );
}
```

## 4. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.sirenGreen,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.cardSurface, borderTopColor: colors.divider, borderTopWidth: 0.5 },
        tabBarLabelStyle: { fontSize: 11, fontWeight: '600', letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Home',   tabBarIcon: ({ color }) => <Ionicons name="home"    size={24} color={color} /> }} />
      <Tabs.Screen name="scan"   options={{ title: 'Scan',   tabBarIcon: ({ color }) => <Ionicons name="qr-code" size={24} color={color} /> }} />
      <Tabs.Screen name="order"  options={{ title: 'Order',  tabBarIcon: ({ color }) => <Ionicons name="cafe"    size={24} color={color} /> }} />
      <Tabs.Screen name="gift"   options={{ title: 'Gift',   tabBarIcon: ({ color }) => <Ionicons name="gift"    size={24} color={color} /> }} />
      <Tabs.Screen name="offers" options={{ title: 'Offers', tabBarIcon: ({ color }) => <Ionicons name="pricetag" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion Specs

```tsx
// Stars ring fill — 1.5s ease-out
progress.value = withTiming(target, { duration: 1500, easing: Easing.out(Easing.ease) });

// Size-pill background slide
// The gestalt of a "sliding" pill is achieved via conditional styling.
// For a literal slide, overlay an absolutely positioned green pill and animate its left offset.

// Stepper press
.transform: [{ scale: pressed ? 0.9 : 1 }]

// Category card tap — spring 0.2s damping 0.8
.transform: [{ scale: pressed ? 0.98 : 1 }]

// Stars earned tick-up
for (let i = 0; i < earned; i++) {
  setTimeout(() => setStars(s => s + 1), i * 80);
}

// Order placed checkmark — spring scale 0 → 1.2 → 1.0
checkScale.value = withSequence(
  withSpring(1.2, { damping: 10 }),
  withSpring(1.0, { damping: 14 })
);
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons):

| Purpose | Ionicons |
|---------|----------|
| Home (tab) | `home` |
| Scan / QR (tab) | `qr-code` |
| Order (tab) / cup | `cafe` |
| Gift (tab) | `gift` |
| Offers / tag (tab) | `pricetag` |
| Star | `star` / `star-outline` |
| Plus / Minus (stepper) | `add` / `remove` |
| Cart | `cart` |
| Store pin | `location` |
| Checkmark (order placed) | `checkmark-circle` |
| Info | `information-circle` |
| Clock | `time` |

## 7. Platform Notes

- **Safe area**: Wrap the root in `SafeAreaView` from `react-native-safe-area-context`. Warm White canvas should extend into the safe areas visually.
- **Status bar**: `<StatusBar style="dark" />` in light mode; `"light"` on dark mode (House Green canvas).
- **Dynamic Type**: Drink names, body, descriptions scale freely. The Stars count hero and size selector are layout-sensitive — cap with `maxFontSizeMultiplier={1.1}`. Tab labels fixed.
- **Dark mode**: Detect with `useColorScheme()`. Canvas becomes `#1E3932` (House Green), surfaces lift to `#2D4A43` and `#3B5852`. Siren Green stays `#00704A` — legible on both.
- **Accessibility**:
  - Stars ring: `accessibilityLabel="{currentStars} Stars earned, {targetStars - currentStars} more Stars until free reward"`
  - Size selector: each pill announces its size with `accessibilityState={{ selected: isActive }}`
  - Barcode: live-region announces balance and reload availability
  - Stepper: `accessibilityRole="adjustable"`, `accessibilityValue={{ text: String(count) }}`
- **Haptics**:
  - `Haptics.selectionAsync()` on size-pill tap
  - `Haptics.impactAsync(Light)` on stepper +/− and category card
  - `Haptics.impactAsync(Medium)` on primary CTA
  - `Haptics.notificationAsync(Success)` on order placed and Stars earned
- **Tabular numerals**: Set `fontVariant: ['tabular-nums']` on every Stars count, price, and calorie display so columns align.
- **Barcode rendering**: Use `react-native-svg` with a Code128 generator (e.g., port jsbarcode's logic). Refresh every 30s with a 300ms cross-dissolve.
