# Taco Bell (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Taco Bell's visual language into paste-ready Expo / React Native code: a design-token module, the brand gradient, themed components (box-preview hero, numbered step pills, the customizer option row, the persistent gradient CTA bar), and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  tbPurple:        '#702082',
  tbPurpleBright:  '#8A2BA0',
  tbMagenta:       '#C72BC8',
  tbMagentaPressed:'#A8239F',
  tbPink:          '#E928A0',

  // Energy / value
  tbYellow: '#FFC700',
  tbOrange: '#FF6A1A',

  // Text on yellow / gradient
  onYellow:   '#2A1530',
  onGradient: '#FFFFFF',

  // Surfaces (dark — primary)
  canvas:   '#0E0A14',
  surface1: '#1A1322',
  surface2: '#261B32',
  divider:  '#34273F',

  // Surfaces (light — secondary)
  canvasLight:   '#FFFFFF',
  surfaceLight1: '#F6F2F8',
  surfaceLight2: '#EDE6F1',
  dividerLight:  '#E3D9EA',

  // Text
  textPrimaryD:   '#F4EFF7',
  textSecondaryD: '#A99BB6',
  textTertiaryD:  '#6E6079',
  textPrimaryL:   '#1F1626',

  // Semantic
  success: '#36C275',
  error:   '#FF4D6D',
} as const;

export type TBColor = keyof typeof colors;

// The brand gradient — the identity on screen.
// Use with <LinearGradient colors={brandGradient} start={{x:0,y:0}} end={{x:1,y:1}} />
export const brandGradient = ['#702082', '#C72BC8'] as const;
export const boxHeroGradient = ['#702082', '#4A1559', '#2B0C36'] as const;
```

## 2. Typography

Load the licensed Taco Bell brand sans via `expo-font`; fall back to system. Tabular numerals for prices, totals, steps.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'TacoBellSans-Regular': require('../assets/fonts/TacoBellSans-Regular.ttf'),
    'TacoBellSans-Medium':  require('../assets/fonts/TacoBellSans-Medium.ttf'),
    'TacoBellSans-Bold':    require('../assets/fonts/TacoBellSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#F4EFF7' } satisfies TextStyle;
const tnum = { fontVariant: ['tabular-nums'] as const };

export const typography = {
  screenTitle:  { ...primary, fontFamily: 'TacoBellSans-Bold', fontSize: 32, lineHeight: 38, letterSpacing: -0.4 },
  builderTitle: { ...primary, fontFamily: 'TacoBellSans-Bold', fontSize: 26, lineHeight: 33, letterSpacing: -0.5 },
  section:      { ...primary, fontFamily: 'TacoBellSans-Bold', fontSize: 22, lineHeight: 29, letterSpacing: -0.2 },
  cardTitle:    { ...primary, fontFamily: 'TacoBellSans-Bold', fontSize: 18, lineHeight: 23, letterSpacing: -0.3 },
  body:         { ...primary, fontFamily: 'TacoBellSans-Regular', fontSize: 16, lineHeight: 24 },
  option:       { ...primary, fontFamily: 'TacoBellSans-Medium', fontSize: 15, lineHeight: 21 },
  price:        { color: '#FFC700', ...tnum, fontFamily: 'TacoBellSans-Bold', fontSize: 15, lineHeight: 20 },
  meta:         { color: '#A99BB6', fontFamily: 'TacoBellSans-Regular', fontSize: 14, lineHeight: 20 },
  eyebrow:      { color: '#FFC700', fontFamily: 'TacoBellSans-Bold', fontSize: 12, lineHeight: 13, letterSpacing: 1, textTransform: 'uppercase' as const },
  stepPill:     { fontFamily: 'TacoBellSans-Bold', fontSize: 12, lineHeight: 12 },
  button:       { color: '#FFFFFF', fontFamily: 'TacoBellSans-Bold', fontSize: 15, lineHeight: 15, letterSpacing: 0.2 },
  chip:         { fontFamily: 'TacoBellSans-Bold', fontSize: 12, lineHeight: 13, letterSpacing: 0.3, textTransform: 'uppercase' as const },
  caption:      { color: '#A99BB6', fontFamily: 'TacoBellSans-Regular', fontSize: 12, lineHeight: 17 },
  tab:          { fontFamily: 'TacoBellSans-Bold', fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Box-Preview Hero

```tsx
// components/BoxPreviewHero.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, boxHeroGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function BoxPreviewHero({
  boxName, itemSummary, price,
}: { boxName: string; itemSummary: string; price: string }) {
  return (
    <View style={{ marginHorizontal: 18, height: 150, borderRadius: 20, overflow: 'hidden', borderWidth: 1, borderColor: colors.divider }}>
      <LinearGradient colors={boxHeroGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }}>
        {/* glow blooms */}
        <View style={{ position: 'absolute', right: -28, top: -28, width: 130, height: 130, borderRadius: 65, backgroundColor: 'rgba(199,43,200,0.30)' }} />
        <View style={{ position: 'absolute', left: -30, bottom: -30, width: 120, height: 120, borderRadius: 60, backgroundColor: 'rgba(255,199,0,0.20)' }} />

        {/* stylized taco */}
        <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
          <View style={{ width: 96, height: 70 }}>
            <View style={{ position: 'absolute', bottom: 30, left: 14, right: 14, height: 26, borderTopLeftRadius: 40, borderTopRightRadius: 40, backgroundColor: '#8FBF4A' }} />
            <View style={{ position: 'absolute', bottom: 0, left: 8, right: 8, height: 44, borderBottomLeftRadius: 60, borderBottomRightRadius: 60, backgroundColor: '#E8A53C' }} />
          </View>
        </View>

        <View style={{ position: 'absolute', left: 14, bottom: 12 }}>
          <Text style={[typography.price, { color: '#FFFFFF' }]}>{boxName}</Text>
          <Text style={[typography.caption, { color: colors.tbYellow, marginTop: 2 }]}>{itemSummary}</Text>
        </View>
        <Text style={[typography.builderTitle, { color: colors.tbYellow, position: 'absolute', right: 14, bottom: 10 }]}>{price}</Text>
      </LinearGradient>
    </View>
  );
}
```

### Numbered Step Pills

```tsx
// components/StepPill.tsx
import { ScrollView, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

type State = 'done' | 'active' | 'todo';

function Pill({ index, label, state }: { index: number; label: string; state: State }) {
  const Body = (
    <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, paddingHorizontal: 12, height: 30 }}>
      <View style={{
        width: 16, height: 16, borderRadius: 8, alignItems: 'center', justifyContent: 'center',
        backgroundColor: state === 'active' ? 'rgba(255,255,255,0.25)' : state === 'done' ? colors.success : colors.surface2,
      }}>
        {state === 'done'
          ? <Ionicons name="checkmark" size={10} color="#06210F" />
          : <Text style={{ fontSize: 10, fontFamily: 'TacoBellSans-Bold', color: state === 'active' ? '#FFF' : colors.textSecondaryD }}>{index}</Text>}
      </View>
      <Text style={[typography.stepPill, { color: state === 'active' ? '#FFF' : state === 'done' ? colors.textPrimaryD : colors.textSecondaryD }]}>{label}</Text>
    </View>
  );

  if (state === 'active') {
    return (
      <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ borderRadius: 999 }}>
        {Body}
      </LinearGradient>
    );
  }
  return (
    <View style={{
      borderRadius: 999, backgroundColor: colors.surface1,
      borderWidth: 1, borderColor: state === 'done' ? colors.success : colors.divider,
    }}>
      {Body}
    </View>
  );
}

export function StepPillRow() {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8, paddingHorizontal: 18 }}>
      <Pill index={1} label="Main"  state="done" />
      <Pill index={2} label="Side"  state="active" />
      <Pill index={3} label="Drink" state="todo" />
      <Pill index={4} label="Sweet" state="todo" />
    </ScrollView>
  );
}
```

### Customizer Option Row

```tsx
// components/OptionRow.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Control =
  | { kind: 'radio'; on: boolean; onSelect: () => void }
  | { kind: 'stepper'; qty: number; onChange: (n: number) => void };

export function OptionRow({
  name, subtitle, upcharge, thumbColor, control,
}: { name: string; subtitle: string; upcharge?: string; thumbColor: string; control: Control }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingVertical: 14, borderBottomWidth: 1, borderBottomColor: colors.divider,
    }}>
      <View style={{ width: 46, height: 46, borderRadius: 12, backgroundColor: thumbColor }} />
      <View style={{ flex: 1 }}>
        <Text style={typography.option}>{name}</Text>
        <Text style={[typography.caption, { marginTop: 2 }]}>{subtitle}</Text>
      </View>
      {upcharge && <Text style={{ fontFamily: 'TacoBellSans-Bold', fontSize: 13, color: colors.textSecondaryD, marginRight: 4 }}>{upcharge}</Text>}

      {control.kind === 'radio' ? (
        <Pressable
          hitSlop={12}
          onPress={() => { control.onSelect(); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); }}
          style={{
            width: 24, height: 24, borderRadius: 12, borderWidth: 2,
            borderColor: control.on ? colors.tbMagenta : colors.divider,
            alignItems: 'center', justifyContent: 'center',
          }}
        >
          {control.on && <View style={{ width: 14, height: 14, borderRadius: 7, backgroundColor: colors.tbMagenta }} />}
        </Pressable>
      ) : (
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 12 }}>
          <Pressable hitSlop={12} onPress={() => control.onChange(Math.max(0, control.qty - 1))}
            style={{ width: 26, height: 26, borderRadius: 13, borderWidth: 1.5, borderColor: colors.tbMagenta, alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="remove" size={13} color={colors.tbMagenta} />
          </Pressable>
          <Text style={{ fontFamily: 'TacoBellSans-Bold', fontSize: 14, color: colors.textPrimaryD, minWidth: 14, textAlign: 'center', fontVariant: ['tabular-nums'] }}>{control.qty}</Text>
          <Pressable hitSlop={12} onPress={() => control.onChange(control.qty + 1)}
            style={{ width: 26, height: 26, borderRadius: 13, borderWidth: 1.5, borderColor: colors.tbMagenta, alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="add" size={13} color={colors.tbMagenta} />
          </Pressable>
        </View>
      )}
    </View>
  );
}
```

### Buttons

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ borderRadius: 14, opacity: pressed ? 0.92 : 1, transform: [{ scale: pressed ? 0.98 : 1 }] })}>
      <LinearGradient colors={brandGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ borderRadius: 14, paddingVertical: 15, alignItems: 'center' }}>
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}

export function ValueButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      borderRadius: 14, paddingVertical: 15, alignItems: 'center',
      backgroundColor: colors.tbYellow, opacity: pressed ? 0.92 : 1,
    })}>
      <Text style={[typography.button, { color: colors.onYellow }]}>{title}</Text>
    </Pressable>
  );
}
```

### Persistent Bottom CTA Bar

```tsx
// components/CtaBar.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { PrimaryButton } from './Buttons';

export function CtaBar({ total, onAdd }: { total: string; onAdd: () => void }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 12,
      paddingHorizontal: 18, paddingVertical: 12,
      backgroundColor: 'rgba(14,10,20,0.96)',
      borderTopWidth: 1, borderTopColor: colors.divider,
    }}>
      <View>
        <Text style={{ fontFamily: 'TacoBellSans-Regular', fontSize: 11, color: colors.textSecondaryD }}>Box total</Text>
        <Text style={{ fontFamily: 'TacoBellSans-Bold', fontSize: 19, color: colors.textPrimaryD, fontVariant: ['tabular-nums'] }}>{total}</Text>
      </View>
      <View style={{ flex: 1 }}>
        <PrimaryButton title="Add to Order" onPress={onAdd} />
      </View>
    </View>
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
        tabBarActiveTintColor:  colors.tbMagenta,
        tabBarInactiveTintColor: '#7A6C86',
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopWidth: 0.5, borderTopColor: colors.divider, height: 70,
        },
        tabBarLabelStyle: { fontFamily: 'TacoBellSans-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"          size={22} color={color} /> }} />
      <Tabs.Screen name="menu"    options={{ title: 'Menu',    tabBarIcon: ({ color }) => <Ionicons name="restaurant"    size={22} color={color} /> }} />
      <Tabs.Screen name="rewards" options={{ title: 'Rewards', tabBarIcon: ({ color }) => <Ionicons name="time"          size={22} color={color} /> }} />
      <Tabs.Screen name="bag"     options={{ title: 'Bag',     tabBarIcon: ({ color }) => <Ionicons name="bag-handle"    size={22} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Add to Order — gradient CTA flash + total roll
import Animated, { useSharedValue, withSequence, withTiming } from 'react-native-reanimated';
// brightness via an overlay opacity: flash.value = withSequence(withTiming(0.12,{duration:110}), withTiming(0,{duration:110}))
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Step advance — pill default → gradient → green (250ms color/layout)
// Crossfade the pill variant with Animated layout or a 250ms timing on bg

// Radio fill — instant or 150ms scale-in on the inner dot
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Box-preview morph on step complete — Animated crossfade of contents (200ms)

// Cravings "Add" → customizer — expo-router shared transition / Reanimated layout (~300ms)

// Combo / sauce sheet — @gorhom/bottom-sheet, 28px top radius

// Reward redeem — scale bounce + success haptic
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Bundle the brand bell as an SVG via `expo-image`; render the brand gradient with `expo-linear-gradient`.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` / `home-outline` |
| Menu | `restaurant` / `restaurant-outline` |
| Rewards | `time` / `time-outline` |
| Bag | `bag-handle` / `bag-handle-outline` |
| Account | `person-circle` / `-outline` |
| Step done | `checkmark` |
| Stepper minus / plus | `remove` / `add` |
| Search | `search` |
| Back | `chevron-back` |
| Sauce / heat | `flame` |
| Reward | `gift` |
| Store / location | `location-outline` |
| Combo upsell | `add-circle` |
| Close | `close` |
| Customize | `options` |

## 7. Platform Notes

- **Fonts**: bundle the licensed Taco Bell brand sans; never download at runtime — fall back to `System` if absent
- **Gradient is the brand**: render every primary CTA, the active step pill, and the box hero with `expo-linear-gradient` (`['#702082','#C72BC8']`) — never a flat purple where the gradient belongs
- **Dark-first**: default the app to the `#0E0A14` canvas; the light theme is secondary — gate it behind `useColorScheme()` only if you ship one
- **Text on yellow / gradient**: hard-code `#2A1530` on yellow and `#FFFFFF` on the gradient — never invert
- **Tabular numerals**: set `fontVariant: ['tabular-nums']` on prices, the running total, and step counts so values don't reflow while rolling
- **Status bar**: `<StatusBar style="light" />` (dark canvas)
- **Safe area**: wrap screens in `SafeAreaView`; the persistent CTA bar sits above the tab bar, both above the home indicator — pad with `useSafeAreaInsets()`
- **expo-image** for all food imagery (memory-disk cache, `contentFit="cover"`, blurhash placeholders) — imagery stays vivid against the neon
- **Dynamic Type**: `<Text>` respects system scale; set `allowFontScaling={false}` on step pills, eyebrows, chips/badges, tab labels, and the box-hero price (layout-critical)
- **Glow**: emulate the box-hero/active-step glow with translucent absolutely-positioned circles (as shown) — RN has no real blur-shadow; on Android the soft drop shadow is the fallback for the CTA bar/sheets
- **Accessibility**: label the box hero, step pills, option rows, and CTA (see DESIGN.md §9 / SwiftUI §8); radios/steppers need a 44pt hit area via `hitSlop`; the whole option row should toggle the radio
