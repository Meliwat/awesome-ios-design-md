# Acorns (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Acorns' visual language into paste-ready Expo / React Native code: a design-token module, themed components, the allocation donut, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Growth / action
  green:        '#6FBF4E',
  greenBright:  '#84D962',
  greenPressed: '#5AA53D',
  forestInk:    '#14302A', // text on green

  // Brand (Oak)
  oak:        '#5A2B82',
  oakBright:  '#7B43A8',
  oakPressed: '#47206A',
  oakSoft:    '#C8AEDF',

  // Surfaces (light)
  canvas:         '#F5F3F8',
  surface:        '#FFFFFF',
  surfacePressed: '#EDE9F2',
  divider:        '#E5E1EC',

  // Surfaces (dark)
  darkCanvas:   '#14121A',
  darkSurface1: '#1E1B27',
  darkSurface2: '#292533',
  darkDivider:  '#383143',

  // Text
  textPrimary:       '#1F1A2B',
  textSecondary:     '#6E6480',
  textTertiary:      '#9C92AD',
  darkTextPrimary:   '#F2EFF6',
  darkTextSecondary: '#ADA4BC',
  darkTextTertiary:  '#756B85',

  // Allocation / chart palette (fixed slice → asset map)
  sliceLarge: '#5A2B82',
  sliceSmall: '#6FBF4E',
  sliceIntl:  '#45C2B0',
  sliceREIT:  '#F2C84B',
  sliceBonds: '#F2785C',
  sliceEM:    '#9B6FD4',

  // Semantic
  gainLight:   '#6FBF4E', gain:   '#84D962',
  lossLight:   '#E0584F', loss:   '#FF6B6B',
  warningLight:'#E0A52A', warning:'#F2C84B',
} as const;

export type AcornColor = keyof typeof colors;
```

## 2. Typography

Acorns ships a rounded brand face; load **Nunito Sans** (SIL OFL) as the free substitute via `expo-font`. Enable tabular figures on money via `fontVariant`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'NunitoSans-Regular':   require('../assets/fonts/NunitoSans-Regular.ttf'),
    'NunitoSans-SemiBold':  require('../assets/fonts/NunitoSans-SemiBold.ttf'),
    'NunitoSans-Bold':      require('../assets/fonts/NunitoSans-Bold.ttf'),
    'NunitoSans-ExtraBold': require('../assets/fonts/NunitoSans-ExtraBold.ttf'),
    'NunitoSans-Black':     require('../assets/fonts/NunitoSans-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#1F1A2B' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  portfolio:  { ...tnum,    fontFamily: 'NunitoSans-Black',     fontSize: 38, lineHeight: 40, letterSpacing: -1 },
  heroNumber: { ...tnum,    fontFamily: 'NunitoSans-Black',     fontSize: 28, lineHeight: 31, letterSpacing: -0.5 },
  section:    { ...primary, fontFamily: 'NunitoSans-ExtraBold', fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  subsection: { ...primary, fontFamily: 'NunitoSans-ExtraBold', fontSize: 18, lineHeight: 23, letterSpacing: -0.2 },
  body:       { ...primary, fontFamily: 'NunitoSans-Regular',   fontSize: 16, lineHeight: 23 },
  rowTitle:   { ...primary, fontFamily: 'NunitoSans-Bold',      fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  amount:     { ...primary, ...tnum, fontFamily: 'NunitoSans-ExtraBold', fontSize: 15, lineHeight: 20, letterSpacing: -0.1 },
  meta:       { color: '#6E6480', fontFamily: 'NunitoSans-SemiBold', fontSize: 13, lineHeight: 18 },
  label:      { color: '#6E6480', fontFamily: 'NunitoSans-Bold', fontSize: 12, lineHeight: 16, letterSpacing: 0.4 },
  button:     { color: '#14302A', fontFamily: 'NunitoSans-ExtraBold', fontSize: 16, lineHeight: 16 },
  tab:        { fontFamily: 'NunitoSans-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  delta:      { fontFamily: 'NunitoSans-Bold', fontSize: 14, lineHeight: 17 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Portfolio Header

```tsx
// components/PortfolioHeader.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function PortfolioHeader({ total, gain }: { total: string; gain: string }) {
  return (
    <View style={{ alignItems: 'center', paddingVertical: 18 }}>
      <Text style={[typography.portfolio, { color: colors.darkTextPrimary }]}>{total}</Text>
      <View style={{ flexDirection: 'row', gap: 4, marginTop: 4 }}>
        <Text style={[typography.delta, { color: colors.gain }]}>▲ {gain}</Text>
        <Text style={[typography.delta, { color: colors.darkTextSecondary }]}>all time</Text>
      </View>
    </View>
  );
}
```

### Allocation Donut + Legend

```tsx
// components/AllocationDonut.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import Animated, { useSharedValue, useAnimatedProps, withTiming, Easing } from 'react-native-reanimated';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

const AnimatedCircle = Animated.createAnimatedComponent(Circle);

export type Slice = { name: string; percent: number; value: string; color: string };

export function AllocationDonut({ slices, styleLabel }: { slices: Slice[]; styleLabel: string }) {
  const R = 70, STROKE = 29, C = 2 * Math.PI * R;
  const sweep = useSharedValue(0);
  useEffect(() => { sweep.value = withTiming(1, { duration: 600, easing: Easing.out(Easing.cubic) }); }, []);

  let acc = 0;
  return (
    <View style={{ width: 168, height: 168, alignItems: 'center', justifyContent: 'center' }}>
      <Svg width={168} height={168} style={{ transform: [{ rotate: '-90deg' }] }}>
        {slices.map((s, i) => {
          const offset = acc; acc += s.percent;
          const len = s.percent * C;
          const props = useAnimatedProps(() => ({
            strokeDashoffset: -offset * C,
            strokeDasharray: [len * sweep.value, C] as any,
          }));
          return (
            <AnimatedCircle
              key={i} cx={84} cy={84} r={R}
              stroke={s.color} strokeWidth={STROKE} fill="none"
              animatedProps={props}
            />
          );
        })}
      </Svg>
      <View style={{ position: 'absolute', alignItems: 'center' }}>
        <Text style={[typography.label, { color: colors.darkTextSecondary, textTransform: 'uppercase' }]}>{styleLabel}</Text>
        <Text style={[typography.subsection, { color: colors.darkTextPrimary, marginTop: 3 }]}>100% invested</Text>
      </View>
    </View>
  );
}

export function LegendRow({ slice }: { slice: Slice }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12, paddingVertical: 9,
      borderBottomWidth: 1, borderBottomColor: colors.darkDivider }}>
      <View style={{ width: 12, height: 12, borderRadius: 4, backgroundColor: slice.color }} />
      <Text style={[typography.rowTitle, { color: colors.darkTextPrimary }]}>{slice.name}</Text>
      <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>{Math.round(slice.percent * 100)}%</Text>
      <View style={{ flex: 1 }} />
      <Text style={[typography.amount, { color: colors.darkTextPrimary, width: 78, textAlign: 'right' }]}>{slice.value}</Text>
    </View>
  );
}
```

### Round-Ups Card

```tsx
// components/RoundUpsCard.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { typography } from '../theme/typography';

export function RoundUpsCard({ amount, subtitle }: { amount: string; subtitle: string }) {
  return (
    <LinearGradient
      colors={['#5A2B82', '#7B43A8']}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{
        marginHorizontal: 18, borderRadius: 18, padding: 18,
        shadowColor: '#4A206A', shadowOpacity: 0.4, shadowRadius: 24, shadowOffset: { width: 0, height: 10 },
        elevation: 8,
      }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' }}>
        <Text style={[typography.label, { color: 'rgba(255,255,255,0.85)' }]}>ROUND-UPS</Text>
        <View style={{ backgroundColor: 'rgba(255,255,255,0.16)', borderRadius: 999, paddingVertical: 4, paddingHorizontal: 10 }}>
          <Text style={{ fontFamily: 'NunitoSans-ExtraBold', fontSize: 11, color: '#FFF' }}>Auto</Text>
        </View>
      </View>
      <Text style={[typography.heroNumber, { color: '#FFF', marginTop: 12 }]}>{amount}</Text>
      <Text style={[typography.meta, { color: 'rgba(255,255,255,0.8)', marginTop: 2 }]}>{subtitle}</Text>
    </LinearGradient>
  );
}
```

### Found Money Row

```tsx
// components/FoundMoneyRow.tsx
import { Pressable, Text, View } from 'react-native';
import { typography } from '../theme/typography';
import { colors } from '../theme/colors';

export function FoundMoneyRow({
  monogram, brandColor, brand, detail, amount,
}: { monogram: string; brandColor: string; brand: string; detail: string; amount: string }) {
  return (
    <Pressable style={({ pressed }) => ({
      flexDirection: 'row', alignItems: 'center', gap: 14,
      paddingHorizontal: 22, paddingVertical: 11,
      backgroundColor: pressed ? colors.darkSurface2 : 'transparent',
    })}>
      <View style={{ width: 40, height: 40, borderRadius: 12, backgroundColor: brandColor, alignItems: 'center', justifyContent: 'center' }}>
        <Text style={{ fontFamily: 'NunitoSans-Black', fontSize: 18, color: '#FFF' }}>{monogram}</Text>
      </View>
      <View style={{ flex: 1 }}>
        <Text style={[typography.rowTitle, { color: colors.darkTextPrimary }]}>{brand}</Text>
        <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>{detail}</Text>
      </View>
      <Text style={[typography.amount, { color: colors.gain }]}>{amount}</Text>
    </Pressable>
  );
}
```

### Pill Buttons

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
        backgroundColor: pressed ? colors.greenPressed : colors.green,
        borderRadius: 500, paddingVertical: 15, paddingHorizontal: 30, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function SecondaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.oakPressed : colors.oak,
        borderRadius: 500, paddingVertical: 14, paddingHorizontal: 26, alignItems: 'center',
      })}
    >
      <Text style={{ fontFamily: 'NunitoSans-ExtraBold', fontSize: 15, color: '#FFF' }}>{title}</Text>
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
        tabBarActiveTintColor: colors.greenBright,   // dark: Green Bright; use colors.green on light
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: {
          backgroundColor: colors.darkCanvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.darkDivider,
        },
        tabBarLabelStyle: { fontFamily: 'NunitoSans-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Invest',  tabBarIcon: ({ color }) => <Ionicons name="trending-up" size={22} color={color} /> }} />
      <Tabs.Screen name="later"   options={{ title: 'Later',   tabBarIcon: ({ color }) => <Ionicons name="calendar"    size={22} color={color} /> }} />
      <Tabs.Screen name="spend"   options={{ title: 'Spend',   tabBarIcon: ({ color }) => <Ionicons name="card"        size={22} color={color} /> }} />
      <Tabs.Screen name="earn"    options={{ title: 'Earn',    tabBarIcon: ({ color }) => <Ionicons name="gift"        size={22} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile', tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Donut sweep — animate strokeDasharray length on each segment (see AllocationDonut)
sweep.value = withTiming(1, { duration: 600, easing: Easing.out(Easing.cubic) });

// Number ticker — portfolio total / Round-Ups
// animate a sharedValue and render Math.round → currency, or use a count-up component

// Milestone celebration — confetti/leaf burst (react-native-confetti-cannon or Lottie) + success haptic
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Card press — scale on tap-down
transform: [{ scale: pressed ? 0.98 : 1 }]

// Range switch — crossfade
import Animated, { FadeIn, FadeOut } from 'react-native-reanimated';
// key the chart by range so it remounts with FadeIn/FadeOut(250)

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);             // card press / range switch
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // invest done / milestone
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Allocation chips and brand monograms are colored squares, not icon-font icons.

| Purpose | Ionicons |
|---------|----------|
| Invest | `trending-up` |
| Later | `calendar` |
| Spend | `card` |
| Earn | `gift` |
| Profile | `person-circle` |
| Notifications | `notifications-outline` |
| Round-Ups | `arrow-up-circle` |
| Gain (▲) | `caret-up` |
| Loss (▼) | `caret-down` |
| Recurring | `repeat` |
| One-time invest | `cash-outline` |
| Found Money | `gift` |
| Goal / projection | `trending-up` |
| Back | `chevron-back` |
| Settings | `settings-outline` |
| Info | `information-circle-outline` |
| Linked account | `business-outline` |

## 7. Platform Notes

- **Font choice**: Acorns ships a rounded brand face; bundle **Nunito Sans** (SIL OFL) as the free substitute. Ship Regular / SemiBold / Bold / ExtraBold / Black
- **Tabular numbers**: always set `fontVariant: ['tabular-nums']` on the portfolio total, allocation values, and deltas so currency aligns
- **Donut**: use `react-native-svg` `Circle` with animated `strokeDasharray` (Reanimated `useAnimatedProps`); rotate the `Svg` -90° so segments start at 12 o'clock
- **Status bar**: `<StatusBar style="light" />` (dark-first); swap on theme if you ship light too
- **Safe area**: wrap screens in `SafeAreaView`; the Invest header respects top inset; tab bar + sheets respect bottom inset
- **Dynamic Type**: RN respects system scale on `<Text>`; set `allowFontScaling={false}` on tab labels and eyebrow labels
- **Keyboard**: amount entry uses `keyboardType="decimal-pad"`; wrap invest flow in `KeyboardAvoidingView` so the running value never hides
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkTextPrimary`; keep the donut palette, Round-Ups gradient, and greens unchanged; positive text uses `gain` (`#84D962`) on dark
- **Green text rule**: never put white on the bright green button — use `forestInk` (`#14302A`); enforce in the button component
- **Accessibility**: summarize the donut as one allocation element ("Large Company 38%, …") plus per-slice legend rows; the portfolio header announces total + gain; Found Money rows expose brand + amount
