# Plenty of Fish (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Plenty of Fish's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  white:         '#FFFFFF',
  background:    '#F4F5F7',
  pressedLight:  '#E9EBEF',
  dividerLight:  '#E4E6EA',

  // Surfaces (dark)
  canvasDark:    '#121417',
  surface1:      '#1A1D21',
  surface2:      '#23272C',
  divider:       '#2C3036',

  // Text
  textPrimary:   '#EDEFF2', // dark
  textPrimaryLt: '#16181C', // light
  textSecondary: '#9BA1AB',
  textTertiary:  '#6B7079',
  onPhoto:       '#FFFFFF',

  // Brand
  blue:          '#0098DB',
  blueLight:     '#00A6E2',
  bluePressed:   '#0079B0',

  // Accent / status
  teal:          '#00C9B7', // online
  pink:          '#FF4F8B', // unread
  gold:          '#FFB23E', // upgrade

  // Semantic
  success:       '#20C997',
  error:         '#FF5267',
} as const;

export type POFColor = keyof typeof colors;

// expo-linear-gradient props
export const brandGradient = {
  colors: [colors.blue, colors.blueLight] as const,
  start: { x: 0, y: 0 }, end: { x: 1, y: 1 },
};
export const scrimGradient = {
  colors: ['transparent', 'rgba(0,0,0,0.78)'] as const,
  start: { x: 0, y: 0 }, end: { x: 0, y: 1 },
};
```

## 2. Typography

POF's UI sans stand-in: **Nunito Sans** (SIL OFL). Load via `expo-font`. Heavy rounded weights (800/900) for names and titles.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Nunito-Regular':  require('../assets/fonts/NunitoSans-Regular.ttf'),
    'Nunito-SemiBold': require('../assets/fonts/NunitoSans-SemiBold.ttf'),
    'Nunito-Bold':     require('../assets/fonts/NunitoSans-Bold.ttf'),
    'Nunito-ExtraBold':require('../assets/fonts/NunitoSans-ExtraBold.ttf'),
    'Nunito-Black':    require('../assets/fonts/NunitoSans-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#EDEFF2' } satisfies TextStyle;

export const typography = {
  display:  { ...primary, fontFamily: 'Nunito-Black',     fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  title:    { ...primary, fontFamily: 'Nunito-ExtraBold', fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:  { ...primary, fontFamily: 'Nunito-ExtraBold', fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  profile:  { ...primary, fontFamily: 'Nunito-ExtraBold', fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:     { ...primary, fontFamily: 'Nunito-Regular',   fontSize: 16, lineHeight: 24 },
  cardName: { color: '#FFFFFF', fontFamily: 'Nunito-Bold', fontSize: 15, lineHeight: 19 },
  button:   { color: '#FFFFFF', fontFamily: 'Nunito-ExtraBold', fontSize: 16, lineHeight: 16 },
  meta:     { color: '#9BA1AB', fontFamily: 'Nunito-Regular', fontSize: 14, lineHeight: 20 },
  cardMeta: { color: 'rgba(255,255,255,0.85)', fontFamily: 'Nunito-SemiBold', fontSize: 12, lineHeight: 16 },
  chip:     { color: '#FFFFFF', fontFamily: 'Nunito-Bold', fontSize: 12, lineHeight: 16, letterSpacing: 0.1 },
  tab:      { fontFamily: 'Nunito-ExtraBold', fontSize: 10, lineHeight: 12, letterSpacing: 0.1 },
  badge:    { color: '#FFFFFF', fontFamily: 'Nunito-ExtraBold', fontSize: 9, lineHeight: 11, letterSpacing: 0.2 },
  bubble:   { ...primary, fontFamily: 'Nunito-SemiBold', fontSize: 15, lineHeight: 21 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Profile Grid Card

```tsx
// components/ProfileGridCard.tsx
import { ImageBackground, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors, scrimGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function ProfileGridCard({
  uri, name, age, meta, isOnline, onOpen, onLike,
}: {
  uri: string; name: string; age: number; meta: string;
  isOnline?: boolean; onOpen: () => void; onLike: () => void;
}) {
  return (
    <Pressable onPress={onOpen} style={{ flex: 1, aspectRatio: 3 / 4, borderRadius: 16, overflow: 'hidden' }}>
      <ImageBackground source={{ uri }} style={{ flex: 1 }}>
        {isOnline ? (
          <View style={{ position: 'absolute', top: 10, left: 10, flexDirection: 'row', alignItems: 'center', gap: 5,
            backgroundColor: 'rgba(0,0,0,0.42)', borderRadius: 999, paddingVertical: 4, paddingHorizontal: 9 }}>
            <View style={{ width: 7, height: 7, borderRadius: 4, backgroundColor: colors.teal }} />
            <Text style={typography.chip}>Online</Text>
          </View>
        ) : null}

        <LinearGradient {...scrimGradient} style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 90 }} />

        <View style={{ position: 'absolute', left: 12, bottom: 12 }}>
          <Text style={typography.cardName}>{name}, {age}</Text>
          <Text style={typography.cardMeta}>{meta}</Text>
        </View>

        <Pressable
          onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light); onLike(); }}
          style={{ position: 'absolute', right: 12, bottom: 12, width: 36, height: 36, borderRadius: 18,
            backgroundColor: colors.blue, alignItems: 'center', justifyContent: 'center',
            shadowColor: colors.blue, shadowOpacity: 0.5, shadowRadius: 12, shadowOffset: { width: 0, height: 4 }, elevation: 6 }}
        >
          <Ionicons name="heart" size={18} color="#FFF" />
        </Pressable>
      </ImageBackground>
    </Pressable>
  );
}
```

### Match Grid

```tsx
// components/MatchGrid.tsx
import { FlatList } from 'react-native';
import { colors } from '../theme/colors';
import { ProfileGridCard } from './ProfileGridCard';

export function MatchGrid({ data }: { data: any[] }) {
  return (
    <FlatList
      data={data}
      numColumns={2}
      keyExtractor={(i) => i.id}
      columnWrapperStyle={{ gap: 10 }}
      contentContainerStyle={{ padding: 14, gap: 10, backgroundColor: colors.canvasDark }}
      renderItem={({ item }) => (
        <ProfileGridCard {...item} onOpen={item.open} onLike={item.like} />
      )}
    />
  );
}
```

### Meet Me Card

```tsx
// components/MeetMeCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MeetMeCard({
  uri, name, age, onNo, onMaybe, onYes,
}: { uri: string; name: string; age: number; onNo: () => void; onMaybe: () => void; onYes: () => void }) {
  return (
    <View style={{ backgroundColor: colors.surface1, borderRadius: 20, padding: 16, alignItems: 'center', gap: 14 }}>
      <Image source={{ uri }} style={{ width: '100%', aspectRatio: 1, borderRadius: 16 }} />
      <Text style={typography.profile}>{name}, {age}</Text>
      <Text style={[typography.meta, { fontFamily: 'Nunito-Bold', color: colors.textSecondary }]}>
        Would you like to meet {name}?
      </Text>
      <View style={{ flexDirection: 'row', gap: 14 }}>
        <RoundBtn icon="close" borderColor={colors.divider} tint={colors.textSecondary} onPress={onNo} />
        <RoundBtn icon="help" borderColor={colors.gold} tint={colors.gold} onPress={onMaybe} />
        <Pressable
          onPress={onYes}
          style={{ width: 56, height: 56, borderRadius: 28, backgroundColor: colors.blue,
            alignItems: 'center', justifyContent: 'center',
            shadowColor: colors.blue, shadowOpacity: 0.5, shadowRadius: 16, shadowOffset: { width: 0, height: 6 }, elevation: 8 }}
        >
          <Ionicons name="heart" size={24} color="#FFF" />
        </Pressable>
      </View>
    </View>
  );
}

function RoundBtn({ icon, borderColor, tint, onPress }: { icon: any; borderColor: string; tint: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ width: 56, height: 56, borderRadius: 28, backgroundColor: colors.surface1,
      borderWidth: 1.5, borderColor, alignItems: 'center', justifyContent: 'center' }}>
      <Ionicons name={icon} size={22} color={tint} />
    </Pressable>
  );
}
```

### Buttons + "Online" Pill

```tsx
// components/Buttons.tsx
import { Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({ opacity: pressed ? 0.93 : 1, transform: [{ scale: pressed ? 0.98 : 1 }] })}>
      <LinearGradient {...brandGradient} style={{ borderRadius: 999, paddingVertical: 14, paddingHorizontal: 28, alignItems: 'center' }}>
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}

export function OutlineButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ borderRadius: 999, borderWidth: 1.5, borderColor: colors.blue, paddingVertical: 11, paddingHorizontal: 20 }}>
      <Text style={[typography.button, { color: colors.blue, fontSize: 15, fontFamily: 'Nunito-Bold' }]}>{title}</Text>
    </Pressable>
  );
}

export function UpgradeButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={{ backgroundColor: colors.gold, borderRadius: 999, paddingVertical: 10, paddingHorizontal: 18 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
        <Ionicons name="star" size={12} color="#2A1B05" />
        <Text style={{ color: '#2A1B05', fontFamily: 'Nunito-ExtraBold', fontSize: 14 }}>{title}</Text>
      </View>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

5 slots; active is POF Blue (no Material pill — tint + filled icon). The Messages tab carries a pink unread badge.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { View, Text } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

function Badge({ n }: { n: number }) {
  if (n <= 0) return null;
  return (
    <View style={{ position: 'absolute', top: -4, right: -10, minWidth: 16, height: 16, borderRadius: 8,
      backgroundColor: colors.pink, alignItems: 'center', justifyContent: 'center', paddingHorizontal: 4 }}>
      <Text style={{ color: '#FFF', fontFamily: 'Nunito-ExtraBold', fontSize: 9 }}>{n}</Text>
    </View>
  );
}

export default function TabsLayout() {
  const unread = 5;
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.blue,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarLabelStyle: { fontFamily: 'Nunito-ExtraBold', fontSize: 10, letterSpacing: 0.1 },
        tabBarBackground: () => <BlurView intensity={40} tint="dark" style={{ flex: 1 }} />,
        tabBarStyle: { borderTopWidth: 0.5, borderTopColor: colors.divider, backgroundColor: 'transparent' },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Matches',  tabBarIcon: ({ color }) => <Ionicons name="grid" size={23} color={color} /> }} />
      <Tabs.Screen name="meetme"   options={{ title: 'Meet Me',  tabBarIcon: ({ color }) => <Ionicons name="heart" size={23} color={color} /> }} />
      <Tabs.Screen name="search"   options={{ title: 'Search',   tabBarIcon: ({ color }) => <Ionicons name="search" size={23} color={color} /> }} />
      <Tabs.Screen name="messages" options={{ title: 'Messages', tabBarIcon: ({ color }) => (
        <View><Ionicons name="chatbubbles" size={23} color={color} /><Badge n={unread} /></View>
      ) }} />
      <Tabs.Screen name="me"       options={{ title: 'Me',       tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { FadeIn, withTiming, withRepeat, withSequence, useSharedValue, useAnimatedStyle } from 'react-native-reanimated';

// Like (grid card) — heart pop
// scale.value = withSequence(withTiming(1.25, { duration: 110 }), withTiming(1, { duration: 110 }))

// Meet Me decision — card off-screen
// translateX.value = withTiming(decision === 'yes' ? width : -width, { duration: 260 })
// "maybe": translateY.value = withTiming(-height, { duration: 240 })

// Meet Me drag — react-native-gesture-handler Pan; rotate ≈ dragX / 18; commit > 35%, spring-back damping 0.8

// Online dot pulse
// opacity.value = withRepeat(withTiming(0.5, { duration: 2000 }), -1, true)

// Tab active pop — scale → 1.05 (120ms)

// Profile open — shared element zoom (react-navigation shared element / Reanimated), 320ms

// Message send — bubble scale-in
// <Animated.View entering={FadeIn.duration(180)}> + scale 0.6 → 1

// Skeleton shimmer — withRepeat sweep over 1200ms

// Haptics
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);              // like, Meet Me decision, tab
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // new match
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). POF Blue = brand/action; teal = online; pink = unread; gold = upgrade.

| Purpose | Ionicons |
|---------|----------|
| Matches (tab) | `grid` |
| Meet Me (tab) | `heart` |
| Search (tab) | `search` |
| Messages (tab) | `chatbubbles` |
| Me (tab) | `person-circle` |
| Like (card / yes) | `heart` |
| Meet Me — no | `close` |
| Meet Me — maybe | `help` |
| Filter | `options` |
| Upgrade / premium | `star` / `diamond` |
| Send message | `send` / `arrow-up-circle` |
| Photo (composer) | `image` |
| Block / report | `flag` |
| Back | `chevron-back` |
| More | `ellipsis-horizontal` |
| Distance | `location` |
| Verified | `checkmark-circle` |
| Carousel dot | `ellipse` |
| Online dot | (teal `View` circle) |

## 7. Platform Notes

- **Font choice**: Nunito Sans (SIL OFL, free) is the POF UI stand-in — bundle it; heavy 800/900 weights for names and titles
- **Status bar**: `<StatusBar style="light" />` on the dark canvas, `"dark"` on light
- **Safe area**: wrap screens in `SafeAreaView`; the tab bar and the profile-detail message bar need bottom safe-area padding
- **Dynamic Type**: `<Text>` honors system scale — set `allowFontScaling={false}` on card-scrim name/meta, tab labels (10pt), the unread badge (9pt), status chips (12pt) so they don't break the photo composition
- **Photos**: use `expo-image` for fast profile-photo loading + blurhash placeholders; never desaturate photos in dark mode
- **LinearGradient**: `expo-linear-gradient` for the brand gradient and the bottom-of-card scrim (`transparent → rgba(0,0,0,0.78)`)
- **Meet Me gestures**: use `react-native-gesture-handler` Pan + Reanimated for the drag/fly card; light haptic on commit
- **Keyboard**: `KeyboardAvoidingView` on the message composer; the composer pill must rise above the keyboard
- **Dark mode**: `useColorScheme()` swaps to `canvasDark` / `surface1`; sheets add a faint 1pt `#2C3036` top border since shadows nearly vanish
- **Accessibility**: each grid card is one accessible element with the name/age/meta/online state; the like button is a separate action; unread badge gets an `accessibilityLabel="{n} unread messages"`
- **Performance**: virtualize the match grid with `FlashList`; show shimmering rounded photo placeholders while photos fetch
