# Monzo (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Monzo's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  coral:        '#FF3464',
  coralPressed: '#E02855',
  navy:         '#14233C',
  navyDeep:     '#0E1620',

  // Surfaces (light)
  canvas:         '#F6F7F9',
  surface:        '#FFFFFF',
  surfacePressed: '#EEF0F3',
  divider:        '#E4E7EB',

  // Surfaces (dark)
  darkCanvas:   '#0E1620',
  darkSurface1: '#14233C',
  darkSurface2: '#1B2F4D',
  darkDivider:  '#243A57',

  // Text
  textPrimary:       '#14233C',
  textSecondary:     '#5C6B82',
  textTertiary:      '#94A1B5',
  darkTextPrimary:   '#F4F6F9',
  darkTextSecondary: '#9FB0C6',
  darkTextTertiary:  '#61748E',

  // Pot / category accents (light / dark)
  mintLight: '#2FB59A', mint: '#5CE0C4',
  skyLight:  '#3E7FC4', sky:  '#5AA9F0',
  goldLight: '#E0A23A', gold: '#FFC75F',
  violetLight: '#7A4FD0', violet: '#A98AE6',
  tealLight: '#1F9E8F', teal: '#46C7B7',

  // Semantic
  incomeLight: '#1FA971', income: '#2FCB8F',
  errorLight:  '#E0354A', error:  '#FF5A6E',
  warningLight:'#D98A1F', warning:'#FFB347',
} as const;

export type MonzoColor = keyof typeof colors;

// Category → emoji + tint
export const categories = {
  eatingOut:     { emoji: '☕️', tint: colors.goldLight },
  shopping:      { emoji: '🛍️', tint: colors.skyLight },
  subscription:  { emoji: '🎧', tint: colors.violetLight },
  transport:     { emoji: '🚆', tint: colors.tealLight },
  income:        { emoji: '💸', tint: colors.incomeLight },
  general:       { emoji: '💳', tint: colors.coral },
} as const;
```

## 2. Typography

Load Inter via `expo-font`. Enable tabular figures on money via `fontVariant`.

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

const primary = { color: '#14233C' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle: { ...primary, fontFamily: 'Inter-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  balance:     { ...tnum,    fontFamily: 'Inter-ExtraBold', fontSize: 30, lineHeight: 33, letterSpacing: -0.5 },
  section:     { ...primary, fontFamily: 'Inter-Bold',      fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  subsection:  { ...primary, fontFamily: 'Inter-Bold',      fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  body:        { ...primary, fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 23 },
  merchant:    { ...primary, fontFamily: 'Inter-SemiBold',  fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  amount:      { ...primary, ...tnum, fontFamily: 'Inter-Bold', fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  meta:        { color: '#5C6B82', fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 18 },
  label:       { color: '#5C6B82', fontFamily: 'Inter-Medium', fontSize: 12, lineHeight: 16, letterSpacing: 0.1 },
  button:      { color: '#FFFFFF', fontFamily: 'Inter-Bold',  fontSize: 16, lineHeight: 16 },
  tab:         { fontFamily: 'Inter-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  cardNumber:  { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 13, lineHeight: 13, letterSpacing: 2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Hot Coral Card Hero

```tsx
// components/CardHero.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, interpolate } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CardHero({ balance, maskedNumber }: { balance: string; maskedNumber: string }) {
  const [flipped, setFlipped] = useState(false);
  const rot = useSharedValue(0);
  const style = useAnimatedStyle(() => ({
    transform: [{ perspective: 1000 }, { rotateY: `${interpolate(rot.value, [0, 1], [0, 180])}deg` }],
  }));

  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        const n = !flipped; setFlipped(n);
        rot.value = withTiming(n ? 1 : 0, { duration: 350 });
      }}
      style={{ marginHorizontal: 20 }}
    >
      <Animated.View style={[{
        height: 132, borderRadius: 18, overflow: 'hidden',
        shadowColor: '#FF3464', shadowOpacity: 0.4, shadowRadius: 30, shadowOffset: { width: 0, height: 14 },
        elevation: 12,
      }, style]}>
        <LinearGradient
          colors={['#FF3464', '#FF5A7F', '#E02855']}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ flex: 1, padding: 20, justifyContent: 'space-between' }}
        >
          <Text style={{ alignSelf: 'flex-end', color: 'rgba(255,255,255,0.9)', fontFamily: 'Inter-Bold', fontSize: 11, letterSpacing: 1 }}>MONZO</Text>
          <View>
            <Text style={[typography.balance, { color: '#FFFFFF' }]}>{balance}</Text>
            <Text style={[typography.label, { color: 'rgba(255,255,255,0.85)' }]}>Available to spend</Text>
          </View>
          <Text style={typography.cardNumber}>{maskedNumber}</Text>
        </LinearGradient>
      </Animated.View>
    </Pressable>
  );
}
```

### Transaction Row

```tsx
// components/TransactionRow.tsx
import { Pressable, Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function TransactionRow({
  emoji, tint, merchant, category, time, amount, isIncome, isPending,
}: {
  emoji: string; tint: string; merchant: string; category: string;
  time: string; amount: string; isIncome?: boolean; isPending?: boolean;
}) {
  return (
    <Pressable style={({ pressed }) => ({
      flexDirection: 'row', alignItems: 'center', gap: 14,
      paddingHorizontal: 20, paddingVertical: 10,
      opacity: isPending ? 0.6 : 1,
      backgroundColor: pressed ? colors.darkSurface2 : 'transparent',
    })}>
      <View style={{
        width: 42, height: 42, borderRadius: 21,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: `${tint}2E`, // ~18% alpha
      }}>
        <Text style={{ fontSize: 20 }}>{emoji}</Text>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.merchant, { color: colors.darkTextPrimary }]}>{merchant}</Text>
        <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>{category} · {time}</Text>
      </View>
      <Text style={[typography.amount, { color: isIncome ? colors.income : colors.darkTextPrimary }]}>
        {amount}
      </Text>
    </Pressable>
  );
}
```

### Pot Coin

```tsx
// components/PotCoin.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function PotCoin({
  emoji, name, amount, gradient,
}: { emoji: string; name: string; amount: string; gradient: [string, string] }) {
  return (
    <View style={{ width: 92, alignItems: 'center' }}>
      <LinearGradient
        colors={gradient}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{
          width: 92, height: 92, borderRadius: 16,
          alignItems: 'center', justifyContent: 'center',
          shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 16, shadowOffset: { width: 0, height: 8 },
          elevation: 6,
        }}
      >
        <Text style={{ fontSize: 30 }}>{emoji}</Text>
      </LinearGradient>
      <Text style={[typography.label, { color: colors.darkTextPrimary, marginTop: 8 }]}>{name}</Text>
      <Text style={{ fontFamily: 'Inter-Medium', fontSize: 11, color: colors.darkTextSecondary }}>{amount}</Text>
    </View>
  );
}
```

### Pot Card (detail / list)

```tsx
// components/PotCard.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function PotCard({
  emoji, name, saved, goal, progress,
}: { emoji: string; name: string; saved: string; goal: string; progress: number }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      backgroundColor: colors.darkSurface2, borderRadius: 16, padding: 16,
    }}>
      <LinearGradient colors={['#5CE0C4', '#2FB59A']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ width: 48, height: 48, borderRadius: 12, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontSize: 22 }}>{emoji}</Text>
      </LinearGradient>
      <View style={{ flex: 1 }}>
        <Text style={[typography.merchant, { color: colors.darkTextPrimary }]}>{name}</Text>
        <Text style={[typography.label, { color: colors.darkTextSecondary }]}>{saved} of {goal} goal</Text>
        <View style={{ height: 6, borderRadius: 3, backgroundColor: colors.darkDivider, marginTop: 8, overflow: 'hidden' }}>
          <View style={{ height: 6, borderRadius: 3, backgroundColor: colors.mint, width: `${progress * 100}%` }} />
        </View>
      </View>
    </View>
  );
}
```

### Primary / Pill Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.coralPressed : colors.coral,
        borderRadius: 14, paddingVertical: 15, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function PillButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{ borderWidth: 1.5, borderColor: colors.coral, borderRadius: 500, paddingVertical: 10, paddingHorizontal: 20 }}
    >
      <Text style={{ fontFamily: 'Inter-SemiBold', fontSize: 14, color: colors.coral }}>{title}</Text>
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
        tabBarActiveTintColor: colors.coral,
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: {
          backgroundColor: colors.darkCanvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.darkDivider,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Home',     tabBarIcon: ({ color }) => <Ionicons name="home"        size={22} color={color} /> }} />
      <Tabs.Screen name="payments" options={{ title: 'Payments', tabBarIcon: ({ color }) => <Ionicons name="card"        size={22} color={color} /> }} />
      <Tabs.Screen name="pots"     options={{ title: 'Pots',     tabBarIcon: ({ color }) => <Ionicons name="grid"        size={22} color={color} /> }} />
      <Tabs.Screen name="trends"   options={{ title: 'Trends',   tabBarIcon: ({ color }) => <Ionicons name="trending-up" size={22} color={color} /> }} />
      <Tabs.Screen name="account"  options={{ title: 'Account',  tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Card 3D flip — Reanimated rotateY with perspective
rot.value = withTiming(flipped ? 1 : 0, { duration: 350 }); // ease-in-out feel via Easing.inOut(Easing.ease)

// Pot money move — spring the coin + animate balance count
import { withSpring } from 'react-native-reanimated';
coinX.value = withSpring(target, { damping: 14, stiffness: 120 });
// Balance ticker: animate a sharedValue and render Math.round; or use a count-up lib

// Feed insert — slide from top
import Animated, { SlideInUp, FadeIn } from 'react-native-reanimated';
// <Animated.View entering={SlideInUp.duration(250)}>

// Pull-to-refresh: <RefreshControl tintColor="#FF3464" />

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);   // card flip, tab change
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);    // pot money move
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // payment complete
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Category icons are emoji glyphs, not icon-font icons.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Payments | `card` |
| Pots | `grid` |
| Trends | `trending-up` |
| Account | `person-circle` |
| Search | `search` |
| Add money | `add` |
| Freeze card | `snow` |
| Split bill | `people` |
| Map pin | `location` |
| Receipt | `receipt-outline` |
| Notes | `create-outline` |
| Back | `chevron-back` |
| Settings | `settings-outline` |
| Income | `arrow-down` |
| Pending | `time-outline` |

## 7. Platform Notes

- **Font choice**: Inter is SIL OFL — free to bundle. Ship Regular / Medium / SemiBold / Bold / ExtraBold
- **Tabular numbers**: always set `fontVariant: ['tabular-nums']` on balances, amounts, and Pot totals so money columns align
- **Status bar**: `<StatusBar style="light" />` always (canvas is dark navy in both themes if you ship dark-first, else swap on theme)
- **Safe area**: wrap screens in `SafeAreaView`; the card hero and feed respect top inset; tab bar respects bottom inset
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels, card number, and Pot-coin captions
- **Keyboard**: amount entry uses `keyboardType="decimal-pad"`; wrap Send screen in `KeyboardAvoidingView` so the running total never hides
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkTextPrimary`; keep coral and Pot/category accents unchanged
- **Card flip**: use `react-native-reanimated` `rotateY` with `perspective`; back face holds card controls (freeze, show PIN, details)
- **Pot drag-to-move**: use `react-native-gesture-handler` `PanGestureHandler` to drag a coin into a Pot; soft haptic on drop, animate balance counters
- **Accessibility**: label transaction rows with merchant + amount + category + time; the card hero is a button ("show card details"); ensure Pot progress is exposed as a value
