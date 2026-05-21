# Mastodon (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Mastodon's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const light = {
  canvas:   '#FFFFFF',
  surface1: '#FFFFFF',
  surface2: '#F2F3F8',
  divider:  '#C0CDD9',
  textPrimary:   '#000000',
  textSecondary: '#606984',
  textTertiary:  '#8B95A8',
} as const;

export const dark = {
  canvas:   '#191B22',   // blue-gray, NOT true black
  surface1: '#282C37',
  surface2: '#313543',
  divider:  '#393F4F',
  textPrimary:   '#FFFFFF',
  textSecondary: '#9BAEC8',
  textTertiary:  '#606984',
} as const;

export const brand = {
  purple:        '#6364FF',
  purplePressed: '#563ACC',
  boostGreen:    '#2DCE89',
  favGold:       '#CA8F04',
  error:         '#DF405A',
} as const;
```

## 2. Typography

Mastodon uses the system font (SF Pro on iOS); the web client renders Inter. Load Inter via `expo-font` only if you want web parity — otherwise omit `fontFamily` to use the system face.

```tsx
// app/_layout.tsx (optional Inter parity)
import { useFonts } from 'expo-font';
const [loaded] = useFonts({
  'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
  'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
  'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
  'Inter-Bold':     require('../assets/fonts/Inter-Bold.ttf'),
});
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  titleLarge:  { fontSize: 28, lineHeight: 34, fontWeight: '700' },
  section:     { fontSize: 22, lineHeight: 27, fontWeight: '700' },
  displayName: { fontSize: 16, lineHeight: 20, fontWeight: '600', letterSpacing: -0.2 },
  body:        { fontSize: 16, lineHeight: 23, fontWeight: '400' },   // 1.45 ratio
  bodySettings:{ fontSize: 15, lineHeight: 21, fontWeight: '400' },
  handle:      { fontSize: 14, lineHeight: 18, fontWeight: '400' },
  cw:          { fontSize: 15, lineHeight: 20, fontWeight: '600' },
  count:       { fontSize: 13, lineHeight: 16, fontWeight: '500' },
  button:      { fontSize: 16, lineHeight: 20, fontWeight: '600', letterSpacing: -0.2 },
  showMore:    { fontSize: 14, lineHeight: 16, fontWeight: '700', letterSpacing: 0.2 },
  tab:         { fontSize: 10, lineHeight: 12, fontWeight: '600' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Toot Card (the core unit)

```tsx
// components/TootCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light as c, brand } from '../theme/colors';
import { typography } from '../theme/typography';
import { BoostButton } from './BoostButton';
import { FavouriteButton } from './FavouriteButton';

export function TootCard({
  displayName, handle, timestamp, body, avatarUri,
}: { displayName: string; handle: string; timestamp: string; body: string; avatarUri: string }) {
  return (
    <View style={{ padding: 16, backgroundColor: c.canvas, borderBottomWidth: 1, borderBottomColor: c.divider }}>
      <View style={{ flexDirection: 'row', gap: 12 }}>
        <Image source={{ uri: avatarUri }}
          style={{ width: 46, height: 46, borderRadius: 8 }} />{/* rounded-square, not circle */}
        <View style={{ flex: 1 }}>
          <Text style={[typography.displayName, { color: c.textPrimary }]}>{displayName}</Text>
          <Text style={[typography.handle, { color: c.textSecondary }]}>{handle}</Text>
        </View>
        <Text style={[typography.count, { color: c.textSecondary }]}>{timestamp}</Text>
        <Ionicons name="ellipsis-horizontal" size={18} color={c.textSecondary} />
      </View>

      <Text style={[typography.body, { color: c.textPrimary, marginTop: 10 }]}>{body}</Text>

      <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 10 }}>
        <Action icon="arrow-undo-outline" count={12} />
        <BoostButton count={34} />
        <FavouriteButton count={211} />
        <Action icon="share-outline" count={0} />
      </View>
    </View>
  );
}

function Action({ icon, count }: { icon: any; count: number }) {
  return (
    <Pressable hitSlop={12} style={{ flexDirection: 'row', alignItems: 'center', gap: 6, minWidth: 44 }}>
      <Ionicons name={icon} size={18} color="#606984" />
      {count > 0 && <Text style={[typography.count, { color: '#606984' }]}>{count}</Text>}
    </Pressable>
  );
}
```

### Boost Button (the 360° spin)

```tsx
// components/BoostButton.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, Easing } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function BoostButton({ count }: { count: number }) {
  const [boosted, setBoosted] = useState(false);
  const spin = useSharedValue(0);
  const style = useAnimatedStyle(() => ({ transform: [{ rotate: `${spin.value}deg` }] }));

  return (
    <Pressable
      hitSlop={12}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 6, minWidth: 44 }}
      onPress={() => {
        const next = !boosted;
        setBoosted(next);
        spin.value = withTiming(spin.value + (next ? 360 : -360), { duration: 400, easing: Easing.out(Easing.cubic) });
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
      }}
    >
      <Animated.View style={style}>
        <Ionicons name="repeat" size={18} color={boosted ? brand.boostGreen : '#606984'} />
      </Animated.View>
      <Text style={[typography.count, { color: boosted ? brand.boostGreen : '#606984' }]}>
        {boosted ? count + 1 : count}
      </Text>
    </Pressable>
  );
}
```

### Favourite Button (gold bounce)

```tsx
// components/FavouriteButton.tsx
import { useState } from 'react';
import { Pressable, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function FavouriteButton({ count }: { count: number }) {
  const [fav, setFav] = useState(false);
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      hitSlop={12}
      style={{ flexDirection: 'row', alignItems: 'center', gap: 6, minWidth: 44 }}
      onPress={() => {
        setFav(!fav);
        scale.value = withSequence(withSpring(1.2), withSpring(1));
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
      }}
    >
      <Animated.View style={style}>
        <Ionicons name={fav ? 'star' : 'star-outline'} size={18} color={fav ? brand.favGold : '#606984'} />
      </Animated.View>
      <Text style={[typography.count, { color: fav ? brand.favGold : '#606984' }]}>
        {fav ? count + 1 : count}
      </Text>
    </Pressable>
  );
}
```

### Content-Warning Spoiler Card (the signature)

```tsx
// components/ContentWarningCard.tsx
import { useState } from 'react';
import { LayoutAnimation, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { light as c, brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function ContentWarningCard({ warning, body }: { warning: string; body: string }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <View>
      <View style={{ flexDirection: 'row', alignItems: 'center', backgroundColor: c.surface2,
                     borderRadius: 8, padding: 12 }}>
        <Ionicons name="eye-off-outline" size={16} color={c.textPrimary} />
        <Text style={[typography.cw, { color: c.textPrimary, marginLeft: 8, flex: 1 }]}>{warning}</Text>
        <Pressable onPress={() => {
          LayoutAnimation.configureNext(LayoutAnimation.create(250, 'easeOut', 'opacity'));
          setExpanded(!expanded);
        }}>
          <Text style={[typography.showMore, { color: brand.purple }]}>
            {expanded ? 'SHOW LESS' : 'SHOW MORE'}
          </Text>
        </Pressable>
      </View>
      {expanded && (
        <Text style={[typography.body, { color: c.textPrimary, marginTop: 10 }]}>{body}</Text>
      )}
    </View>
  );
}
```

### Primary Button & Compose FAB

```tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { brand } from '../theme/colors';
import { typography } from '../theme/typography';

export function MastoPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? brand.purplePressed : brand.purple,
        paddingVertical: 12, paddingHorizontal: 24, borderRadius: 8,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={[typography.button, { color: '#fff' }]}>{title}</Text>
    </Pressable>
  );
}

export function ComposeFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        position: 'absolute', right: 16, bottom: 16,
        width: 56, height: 56, borderRadius: 28,
        backgroundColor: pressed ? brand.purplePressed : brand.purple,
        alignItems: 'center', justifyContent: 'center',
        transform: [{ scale: pressed ? 0.94 : 1 }],
        shadowColor: brand.purple, shadowOpacity: 0.35, shadowRadius: 20, shadowOffset: { width: 0, height: 6 },
      })}>
      <Ionicons name="create-outline" size={24} color="#fff" />
    </Pressable>
  );
}
```

## 4. Distinctive System — Federated Timeline Switcher

```tsx
// components/TimelineSwitcher.tsx
import { Pressable, Text, View } from 'react-native';
import { brand, light as c } from '../theme/colors';

const SCOPES = ['Home', 'Local', 'Federated'] as const;

export function TimelineSwitcher({ scope, onChange }:
  { scope: string; onChange: (s: string) => void }) {
  return (
    <View style={{ flexDirection: 'row' }}>
      {SCOPES.map((s) => {
        const active = s === scope;
        return (
          <Pressable key={s} onPress={() => onChange(s)}
            style={{ flex: 1, alignItems: 'center', paddingTop: 12 }}>
            <Text style={{ fontSize: 15, fontWeight: active ? '600' : '400',
                           color: active ? brand.purple : c.textSecondary }}>{s}</Text>
            <View style={{ height: 2, alignSelf: 'stretch', marginTop: 8,
                           backgroundColor: active ? brand.purple : 'transparent' }} />
          </Pressable>
        );
      })}
    </View>
  );
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { brand, light as c } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: brand.purple,
        tabBarInactiveTintColor: c.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(255,255,255,0.6)' }} />
        ),
        tabBarLabelStyle: { fontSize: 10, fontWeight: '600' },
      }}
    >
      <Tabs.Screen name="index"         options={{ title: 'Home',          tabBarIcon: ({ color }) => <Ionicons name="home"      size={26} color={color} /> }} />
      <Tabs.Screen name="search"        options={{ title: 'Search',        tabBarIcon: ({ color }) => <Ionicons name="search"    size={26} color={color} /> }} />
      <Tabs.Screen name="notifications" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={26} color={color} /> }} />
      <Tabs.Screen name="profile"       options={{ title: 'Profile',       tabBarIcon: ({ color }) => <Ionicons name="person-circle" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Boost: 360° spin (Easing.out(Easing.cubic), 400ms) + success haptic (see BoostButton)
// Favourite: star scale 1 → 1.2 → 1 via withSequence + light haptic (see FavouriteButton)
// CW expand: LayoutAnimation 250ms easeOut + opacity (see ContentWarningCard)

// Timeline switch: cross-fade + 8px horizontal nudge
const x = useSharedValue(0);
x.value = withSequence(withTiming(8, { duration: 120 }), withSpring(0));

// Compose FAB: scale 0.94 on press + soft haptic; present compose as a router modal
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Mastodon's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Ionicons |
|---------|-----------------|----------|
| Reply | `arrowshape.turn.up.left` | `arrow-undo-outline` |
| Boost | `arrow.2.squarepath` | `repeat` |
| Favourite | `star` / `star.fill` | `star-outline` / `star` |
| Share | `square.and.arrow.up` | `share-outline` |
| More | `ellipsis` | `ellipsis-horizontal` |
| Content warning | `eye.slash` | `eye-off-outline` |
| Compose (FAB) | `square.and.pencil` | `create-outline` |
| Home (tab) | `house.fill` | `home` |
| Search (tab) | `magnifyingglass` | `search` |
| Notifications (tab) | `bell.fill` | `notifications` |
| Profile (tab) | `person.crop.square` | `person-circle` |
| Visibility public | `globe` | `globe-outline` |
| Visibility followers | `lock.fill` | `lock-closed` |

## 8. Platform Notes

- **iOS-only feel**: use `expo-blur` for the tab bar's `.regularMaterial` equivalent (light tint on light, dark tint in dark mode). Android falls back to a 94%-opaque solid surface.
- **Status bar**: drive `<StatusBar style="auto" />` from `expo-status-bar` so it flips with light/dark — light content on `#191B22`, dark content on `#FFFFFF`
- **Safe area**: wrap screens in `SafeAreaView`; the profile banner goes edge-to-edge while content keeps 16px insets
- **Dark mode**: detect with `useColorScheme()` and swap the `light`/`dark` token objects; never collapse the dark canvas to `#000` — keep the `#191B22` blue cast
- **Reading-first**: React Native honors user font scaling — keep it on toot body and names; pin only tab labels and "SHOW MORE"
- **Accessibility**: add `accessibilityRole="button"` + state labels ("Boosted", "Favourited") and expose the CW card as a disclosure with a clear "Show content warning" hint
