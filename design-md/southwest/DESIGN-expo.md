# Southwest (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Southwest's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets for the signature boarding-position reveal and 24-hour check-in countdown.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Heart tri-color (brand) — strict role system
  swBlue:        '#304CB2', // structure
  swBlueBright:  '#2567E8', // link
  swBlueDeep:    '#243C8E', // hero gradient end
  swRed:         '#E51D23', // ALERTS ONLY
  swRedPressed:  '#C0161B',
  swYellow:      '#F9B612', // THE CTA
  swYellowPressed:'#E0A300',

  // Surfaces (light)
  canvas:        '#FFFFFF',
  wash:          '#F2F5FB',
  surface:       '#FFFFFF',
  pressed:       '#E7ECF6',
  divider:       '#DCE2EE',

  // Surfaces (dark)
  darkCanvas:    '#0E1726',
  darkSurface1:  '#16223A',
  darkSurface2:  '#1F2E4A',
  darkDivider:   '#2A3A57',

  // Text
  textPrimary:   '#1A2233',
  textSecondary: '#5A6783',
  textTertiary:  '#8C99B3',
  darkTextPrimary:   '#EAF0FA',
  darkTextSecondary: '#9DAAC4',
  darkTextTertiary:  '#67748F',
  onYellow:      '#1A1A1A',
  onBlue:        '#FFFFFF',

  // Semantic
  success:       '#1E8E4E',
  successDark:   '#1FAE5E',
  warning:       '#F9B612', // == swYellow
  error:         '#E51D23', // == swRed (alerts only)
} as const;

export type SWColor = keyof typeof colors;
```

## 2. Typography

Brand face is **Southwest Sans** (proprietary). If licensed, bundle it; otherwise ship **Inter** (SIL OFL). Numbers use `fontVariant: ['tabular-nums']` wherever they update.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // swap Inter-* for SouthwestSans-* if licensed — keep the keys
    'SW-Regular':    require('../assets/fonts/Inter-Regular.ttf'),
    'SW-Medium':     require('../assets/fonts/Inter-Medium.ttf'),
    'SW-SemiBold':   require('../assets/fonts/Inter-SemiBold.ttf'),
    'SW-Bold':       require('../assets/fonts/Inter-Bold.ttf'),
    'SW-ExtraBold':  require('../assets/fonts/Inter-ExtraBold.ttf'),
    'SW-Black':      require('../assets/fonts/Inter-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const tnum = { fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] };

export const typography = {
  boardingGroup: { fontFamily: 'SW-Black',     fontSize: 88, lineHeight: 80, letterSpacing: -2 },
  boardingNum:   { fontFamily: 'SW-ExtraBold', fontSize: 52, lineHeight: 52, letterSpacing: -1, ...tnum },
  screenTitle:   { fontFamily: 'SW-Black',     fontSize: 32, lineHeight: 36, letterSpacing: -0.4 },
  airportCode:   { fontFamily: 'SW-ExtraBold', fontSize: 26, lineHeight: 27, letterSpacing: -0.4 },
  section:       { fontFamily: 'SW-ExtraBold', fontSize: 22, lineHeight: 27, letterSpacing: -0.2 },
  subsection:    { fontFamily: 'SW-Bold',      fontSize: 18, lineHeight: 24, letterSpacing: -0.1 },
  body:          { fontFamily: 'SW-Regular',   fontSize: 16, lineHeight: 24 },
  bodyEmphasis:  { fontFamily: 'SW-SemiBold',  fontSize: 16, lineHeight: 24 },
  cardTitle:     { fontFamily: 'SW-SemiBold',  fontSize: 15, lineHeight: 20 },
  farePrice:     { fontFamily: 'SW-ExtraBold', fontSize: 19, lineHeight: 21, ...tnum },
  eyebrow:       { fontFamily: 'SW-Bold',      fontSize: 13, lineHeight: 16, letterSpacing: 0.6 },
  meta:          { fontFamily: 'SW-Medium',    fontSize: 12, lineHeight: 16 },
  button:        { fontFamily: 'SW-ExtraBold', fontSize: 16, lineHeight: 16, letterSpacing: 0.1 },
  tab:           { fontFamily: 'SW-SemiBold',  fontSize: 10, lineHeight: 11, letterSpacing: 0.1 },
  clock:         { fontFamily: 'SW-ExtraBold', fontSize: 11, lineHeight: 11, ...tnum },
  clockLarge:    { fontFamily: 'SW-ExtraBold', fontSize: 34, lineHeight: 34, ...tnum },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Boarding Position Card

```tsx
// components/BoardingPositionCard.tsx
import { View, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BoardingPositionCard({
  group, position, boardingStarts, confirmation,
}: { group: string; position: number; boardingStarts: string; confirmation: string }) {
  return (
    <LinearGradient
      colors={[colors.swBlue, colors.swBlueDeep]}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
      style={{
        borderRadius: 20, padding: 22, marginHorizontal: 20,
        shadowColor: colors.swBlue, shadowOpacity: 0.3, shadowRadius: 15,
        shadowOffset: { width: 0, height: 14 }, elevation: 8,
      }}
    >
      <Text style={[typography.eyebrow, { color: 'rgba(255,255,255,0.78)' }]}>BOARDING POSITION</Text>
      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 4, marginVertical: 6 }}>
        <Text style={[typography.boardingGroup, { color: colors.swYellow }]}>{group}</Text>
        <Text style={[typography.boardingNum, { color: '#FFFFFF' }]}>{position}</Text>
      </View>
      <Text style={[typography.cardTitle, { color: 'rgba(255,255,255,0.85)' }]}>
        Board in group {group}, position {position}
      </Text>
      <View style={{ height: 1, backgroundColor: 'rgba(255,255,255,0.18)', marginVertical: 16 }} />
      <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
        <FootCol label="BOARDING STARTS" value={boardingStarts} />
        <FootCol label="CONFIRMATION" value={confirmation} />
      </View>
    </LinearGradient>
  );
}

function FootCol({ label, value }: { label: string; value: string }) {
  return (
    <View style={{ gap: 2 }}>
      <Text style={[typography.meta, { color: 'rgba(255,255,255,0.7)', letterSpacing: 0.5, fontFamily: 'SW-SemiBold' }]}>{label}</Text>
      <Text style={[typography.subsection, { color: '#FFFFFF' }]}>{value}</Text>
    </View>
  );
}
```

### Check-In Countdown

```tsx
// components/CheckInCountdown.tsx
import { View, Text, Pressable } from 'react-native';
import Svg, { Circle } from 'react-native-svg';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const R = 24, C = 2 * Math.PI * R;

export function CheckInCountdown({
  progress, clock, isOpen = false, onAction,
}: { progress: number; clock: string; isOpen?: boolean; onAction: () => void }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 14,
      marginHorizontal: 20, padding: 18, borderRadius: 16,
      backgroundColor: colors.darkSurface1, borderWidth: 1, borderColor: colors.darkDivider,
    }}>
      <View style={{ width: 54, height: 54 }}>
        <Svg width={54} height={54} style={{ transform: [{ rotate: '-90deg' }] }}>
          <Circle cx={27} cy={27} r={R} stroke={colors.darkDivider} strokeWidth={5} fill="none" />
          <Circle cx={27} cy={27} r={R} stroke={colors.swYellow} strokeWidth={5} fill="none"
            strokeLinecap="round" strokeDasharray={C} strokeDashoffset={C * (1 - progress)} />
        </Svg>
        <View style={{ position: 'absolute', inset: 0, alignItems: 'center', justifyContent: 'center' }}>
          <Text style={[typography.clock, { color: colors.swYellow }]}>{clock}</Text>
        </View>
      </View>

      <View style={{ flex: 1 }}>
        <Text style={[typography.cardTitle, { color: colors.darkTextPrimary }]}>
          {isOpen ? 'Check-in is open' : 'Check-in opens soon'}
        </Text>
        <Text style={[typography.meta, { color: colors.darkTextSecondary, marginTop: 3 }]}>
          Opens 24 hrs before departure to lock your spot
        </Text>
      </View>

      <Pressable onPress={onAction} style={{
        backgroundColor: colors.swYellow, paddingHorizontal: 16, paddingVertical: 10, borderRadius: 8,
      }}>
        <Text style={[typography.button, { color: colors.onYellow }]}>{isOpen ? 'Check in' : 'Notify'}</Text>
      </Pressable>
    </View>
  );
}
```

### Flight Strip

```tsx
// components/FlightStrip.tsx
import { View, Text } from 'react-native';
import Svg, { Path } from 'react-native-svg';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FlightStrip({
  origin, destination, durationLabel, flightLine, gateLine,
}: { origin: string; destination: string; durationLabel: string; flightLine: string; gateLine: string }) {
  return (
    <View style={{
      marginHorizontal: 20, padding: 18, borderRadius: 16, backgroundColor: colors.darkSurface1, gap: 10,
    }}>
      <View style={{ flexDirection: 'row', alignItems: 'center' }}>
        <Text style={[typography.airportCode, { color: colors.darkTextPrimary }]}>{origin}</Text>
        <View style={{ flex: 1, alignItems: 'center', gap: 4 }}>
          <Svg width={60} height={14}>
            <Path d="M2 7 H48" stroke={colors.swYellow} strokeWidth={1.6} />
            <Path d="M44 3 L52 7 L44 11" stroke={colors.swYellow} strokeWidth={1.6} fill="none" />
          </Svg>
          <Text style={{ ...typography.meta, fontFamily: 'SW-SemiBold', fontSize: 10,
            letterSpacing: 0.6, color: colors.darkTextSecondary }}>{durationLabel.toUpperCase()}</Text>
        </View>
        <Text style={[typography.airportCode, { color: colors.darkTextPrimary }]}>{destination}</Text>
      </View>
      <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
        <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>{flightLine}</Text>
        <Text style={[typography.meta, { color: colors.darkTextSecondary }]}>{gateLine}</Text>
      </View>
    </View>
  );
}
```

### Wanna Get Away Fare Card

```tsx
// components/FareCard.tsx
import { Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FareCard({
  name, price, points, selected, onPress,
}: { name: string; price: string; points: string; selected: boolean; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{
      flex: 1, alignItems: 'center', paddingVertical: 14, paddingHorizontal: 12,
      borderRadius: 12,
      backgroundColor: selected ? 'rgba(249,182,18,0.10)' : colors.darkSurface2,
      borderWidth: selected ? 2 : 1,
      borderColor: selected ? colors.swYellow : colors.darkDivider,
    }}>
      <Text style={{ ...typography.eyebrow, fontSize: 11, letterSpacing: 0.4,
        color: colors.darkTextSecondary }}>{name.toUpperCase()}</Text>
      <Text style={[typography.farePrice, { color: colors.darkTextPrimary, marginTop: 8 }]}>{price}</Text>
      <Text style={{ ...typography.meta, fontFamily: 'SW-SemiBold', fontSize: 11,
        color: colors.darkTextSecondary, marginTop: 2 }}>{points}</Text>
    </Pressable>
  );
}
```

### Rapid Rewards Points Pill

```tsx
// components/PointsPill.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PointsPill({ text, tier = false }: { text: string; tier?: boolean }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 8,
      paddingHorizontal: 16, paddingVertical: 10, borderRadius: 500,
      backgroundColor: tier ? colors.swYellow : colors.swBlue,
    }}>
      <Ionicons name="star" size={13} color={tier ? colors.swBlue : colors.swYellow} />
      <Text style={{ ...typography.button, fontSize: 14, color: tier ? colors.onYellow : colors.onBlue }}>{text}</Text>
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
        tabBarActiveTintColor: colors.swYellow,        // Warm Yellow active
        tabBarInactiveTintColor: colors.darkTextTertiary,
        tabBarStyle: { backgroundColor: colors.darkCanvas, borderTopWidth: 0.5, borderTopColor: colors.darkDivider },
        tabBarLabelStyle: { fontFamily: 'SW-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Home',    tabBarIcon: ({ color }) => <Ionicons name="home"        size={22} color={color} /> }} />
      <Tabs.Screen name="book"    options={{ title: 'Book',    tabBarIcon: ({ color }) => <Ionicons name="paper-plane" size={22} color={color} /> }} />
      <Tabs.Screen name="trips"   options={{ title: 'Trips',   tabBarIcon: ({ color }) => <Ionicons name="briefcase"   size={22} color={color} /> }} />
      <Tabs.Screen name="rewards" options={{ title: 'Rapid Rewards', tabBarIcon: ({ color }) => <Ionicons name="star" size={22} color={color} /> }} />
      <Tabs.Screen name="account" options={{ title: 'Account', tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, useSharedValue, useAnimatedStyle, withTiming, withSequence } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';

// Boarding-position reveal — the payoff moment
const scale = useSharedValue(0.96);
const revealStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
function reveal() {
  scale.value = withTiming(1, { duration: 280 });
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
}
// group letter: <Animated.Text entering={FadeIn.duration(280)}>

// Check-in clock — re-key the Text so a 120ms fade plays each tick
// <Animated.Text key={clock} entering={FadeIn.duration(120)}>{clock}</Animated.Text>

// At T-0: cross-fade Notify → full-width "Check in" CTA over 240ms
// Primary CTA press: scale 1 → 0.98 (120ms) + Yellow → Pressed Yellow
const press = useSharedValue(1);
const pressStyle = useAnimatedStyle(() => ({ transform: [{ scale: press.value }] }));
// onPressIn: press.value = withTiming(0.98, { duration: 120 }); + Haptics.impactAsync(Light)

// Fare selection: border animates Yellow in over 150ms; price subtle pop
// price: scale.value = withSequence(withTiming(1.03, { duration: 90 }), withTiming(1, { duration: 90 }));

// Cancellation banner (only red motion): slide down 220ms + warning haptic
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). For the boarding-pass barcode use `react-native-svg` or an `expo-barcode`-style component.

| Purpose | Ionicons |
|---------|----------|
| Home | `home` |
| Book | `paper-plane` |
| Trips | `briefcase` |
| Rapid Rewards | `star` |
| Account | `person-circle` |
| Back | `chevron-back` |
| Help / clock | `time-outline` |
| Bags fly free | `bag-handle` |
| Flight / route | `airplane` |
| On time / success | `checkmark-circle` |
| Delay / warning | `warning` |
| Cancelled (red only) | `close-circle` |
| Points / rewards | `star` |
| Companion Pass | `people` |
| Add a bag | `add-circle-outline` |
| Calendar | `calendar-outline` |
| Notification | `notifications` |
| Boarding pass | `qr-code` / `barcode-outline` |

## 7. Platform Notes

- **Font choice**: Southwest Sans is proprietary — license to ship it, or use Inter (SIL OFL) as the fallback and keep the named ramp identical so layout doesn't shift
- **Tabular numbers**: set `fontVariant: ['tabular-nums']` on the countdown clock, fares, and points balance — the helper is already baked into `typography`
- **Status bar**: `<StatusBar style="light" />` on the navy canvas (dark mode is the primary rendering); `"dark"` on the light theme
- **Safe area**: wrap screens in `SafeAreaView`; pin the primary CTA above the home indicator with `useSafeAreaInsets().bottom`
- **Dynamic Type**: RN scales `<Text>` with system font scale — set `allowFontScaling={false}` on the boarding group/number, countdown clock, tab labels, eyebrow labels, and airport codes (layout-critical)
- **One Yellow per screen**: enforce in review — the primary CTA is the only `colors.swYellow` fill; secondary actions use `colors.swBlue`
- **Red discipline**: `colors.swRed` / `colors.error` only on cancellation/irregular-ops banners and the final destructive confirm — never a CTA or badge
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas` / `darkSurface1` / `darkSurface2`; keep `swYellow` and `swRed` constant, brighten links to `swBlueBright`
- **Accessibility**: give the boarding card an `accessibilityLabel` summarizing group, position, boarding time, and confirmation; the check-in CTA needs `accessibilityRole="button"` and a state-aware label
- **Gradient + shadow**: `expo-linear-gradient` carries the boarding card; on Android set `elevation` for the blue glow since iOS `shadow*` props are no-ops there
- **Barcode**: keep the boarding-pass barcode full-contrast and clear of safe-area insets so scanners read it; never tint it
