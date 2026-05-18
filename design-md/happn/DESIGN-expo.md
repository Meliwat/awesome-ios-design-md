# happn (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates happn's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets — centered on the Crossings timeline and the Charm gesture.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, `react-native-reanimated` v3, and `react-native-maps` for the Map view.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces (dark)
  canvas:   '#0E0E12',
  surface1: '#18181F',
  surface2: '#21212B',
  surface3: '#2C2C38',
  divider:  '#2A2A33',          // also the timeline spine
  scrim:    'rgba(0,0,0,0.45)',

  // Canvas & surfaces (light)
  canvasLight:   '#FFFFFF',
  surface1Light: '#F6F6F8',
  surface2Light: '#EDEDF1',
  dividerLight:  '#E6E6EC',

  // Brand
  pink:      '#FF4865',
  pinkPress: '#E5354F',
  magenta:   '#E91E63',
  rose:      '#FF7B93',
  gold:      '#FFC24B',          // Crush / premium ONLY

  // Text
  textPrimary:      '#F4F4F6',
  textSecondary:    '#A0A0AE',
  textTertiary:     '#6C6C7A',
  textPrimaryLight: '#15151B',
  onPink:           '#FFFFFF',
  onGold:           '#1A1A1A',

  // Semantic
  success: '#4ED9A4',
  error:   '#FF5C5C',
} as const;

export const heroGradient = ['#FF4865', '#E91E63'] as const; // 120deg
```

## 2. Typography

Load **Poppins** (warmth) + **Inter** (legibility) via `expo-font`.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Poppins-SemiBold': require('../assets/fonts/Poppins-SemiBold.ttf'),
    'Poppins-Bold':     require('../assets/fonts/Poppins-Bold.ttf'),
    'Inter-Regular':    require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':     require('../assets/fonts/Inter-Medium.ttf'),
  });
  if (!loaded) return null;
  return <Stack screenOptions={{ headerShown: false, contentStyle: { backgroundColor: '#0E0E12' } }} />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  display:    { fontFamily: 'Poppins-Bold',     fontSize: 32, lineHeight: 37, letterSpacing: -0.5, color: '#F4F4F6' },
  name:       { fontFamily: 'Poppins-Bold',     fontSize: 26, lineHeight: 30, letterSpacing: -0.4, color: '#F4F4F6' },
  section:    { fontFamily: 'Poppins-SemiBold', fontSize: 22, lineHeight: 26, letterSpacing: -0.3, color: '#F4F4F6' },
  subsection: { fontFamily: 'Poppins-SemiBold', fontSize: 18, lineHeight: 24, color: '#F4F4F6' },
  cardName:   { fontFamily: 'Poppins-SemiBold', fontSize: 15, lineHeight: 20, color: '#F4F4F6' },
  button:     { fontFamily: 'Poppins-SemiBold', fontSize: 16, lineHeight: 16 },
  count:      { fontFamily: 'Poppins-SemiBold', fontSize: 12, lineHeight: 16, color: '#FF4865' },
  tab:        { fontFamily: 'Poppins-SemiBold', fontSize: 10, lineHeight: 10, letterSpacing: 0.2 },
  body:       { fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 24, color: '#F4F4F6' },
  bodyMeta:   { fontFamily: 'Inter-Regular', fontSize: 14, lineHeight: 20, color: '#A0A0AE' },
  location:   { fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 16, color: '#A0A0AE' },
  caption:    { fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 16, color: '#6C6C7A' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Crossings Timeline

```tsx
// components/CrossingsTimeline.tsx
import { FlatList, View } from 'react-native';
import { colors } from '../theme/colors';
import { CrossingRow } from './CrossingRow';

export type Crossing = {
  id: string;
  avatar: [string, string];
  name: string;          // "Camille, 27"
  place: string;         // "Le Marais"
  timeAgo: string;       // "11 min ago"
  crossCount: number;
  charmed: boolean;
};

export function CrossingsTimeline({ data, onCharm }: { data: Crossing[]; onCharm: (id: string) => void }) {
  return (
    <FlatList
      data={data}
      keyExtractor={(c) => c.id}
      contentContainerStyle={{ paddingHorizontal: 16, paddingTop: 4, paddingBottom: 24 }}
      ItemSeparatorComponent={() => <View style={{ height: 14 }} />}
      style={{ backgroundColor: colors.canvas }}
      renderItem={({ item }) => <CrossingRow crossing={item} onCharm={() => onCharm(item.id)} />}
    />
  );
}
```

```tsx
// components/CrossingRow.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Ionicons } from '@expo/vector-icons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';
import { CharmButton } from './CharmButton';
import type { Crossing } from './CrossingsTimeline';

export function CrossingRow({ crossing, onCharm }: { crossing: Crossing; onCharm: () => void }) {
  const countLabel =
    crossing.crossCount === 1 ? 'You crossed paths once' : `You crossed paths ${crossing.crossCount} times`;

  return (
    <View style={{ flexDirection: 'row' }}>
      {/* Connector spine + dot */}
      <View style={{ width: 18, alignItems: 'center' }}>
        <View style={{ position: 'absolute', top: 6, bottom: -14, width: 2, backgroundColor: colors.divider }} />
        <View style={{
          marginTop: 6, width: 12, height: 12, borderRadius: 6, backgroundColor: colors.pink,
          borderWidth: 4, borderColor: 'rgba(255,72,101,0.18)',
        }} />
      </View>

      {/* Card */}
      <View style={{
        flex: 1, marginLeft: 8, flexDirection: 'row', alignItems: 'center', gap: 12,
        backgroundColor: colors.surface1, borderColor: colors.divider, borderWidth: 1,
        borderRadius: 18, padding: 12,
      }}>
        <LinearGradient colors={crossing.avatar} start={{ x: 0.15, y: 0.1 }}
          style={{ width: 56, height: 56, borderRadius: 16 }} />
        <View style={{ flex: 1, minWidth: 0 }}>
          <Text style={typography.cardName} numberOfLines={1}>{crossing.name}</Text>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5, marginTop: 2 }}>
            <Ionicons name="location-outline" size={11} color={colors.textTertiary} />
            <Text style={typography.location} numberOfLines={1}>{crossing.place} · {crossing.timeAgo}</Text>
          </View>
          <Text style={[typography.count, { marginTop: 5 }]}>{countLabel}</Text>
        </View>
        <CharmButton charmed={crossing.charmed} onCharm={onCharm} size={40} />
      </View>
    </View>
  );
}
```

### Charm Button (heart)

```tsx
// components/CharmButton.tsx
import { Pressable } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function CharmButton({ charmed, onCharm, size = 40 }: { charmed: boolean; onCharm: () => void; size?: number }) {
  const scale = useSharedValue(1);
  const aStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const press = () => {
    scale.value = withSequence(withSpring(1.25, { damping: 6 }), withSpring(1, { damping: 9 }));
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    onCharm();
  };

  return (
    <Animated.View style={aStyle}>
      <Pressable
        onPress={press}
        style={{
          width: size, height: size, borderRadius: size / 2,
          alignItems: 'center', justifyContent: 'center',
          backgroundColor: charmed ? colors.pink : colors.surface2,
          borderWidth: charmed ? 0 : 1, borderColor: colors.divider,
          shadowColor: colors.pink, shadowOpacity: charmed ? 0.5 : 0,
          shadowRadius: 8, shadowOffset: { width: 0, height: 6 }, elevation: charmed ? 6 : 0,
        }}
      >
        <Ionicons name={charmed ? 'heart' : 'heart-outline'} size={size * 0.45}
          color={charmed ? colors.onPink : colors.textSecondary} />
      </Pressable>
    </Animated.View>
  );
}
```

### Crush Celebration

```tsx
// components/CrushCelebration.tsx
import { useEffect } from 'react';
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors, heroGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function CrushCelebration({
  left, right, onStartChat,
}: { left: [string, string]; right: [string, string]; onStartChat: () => void }) {
  const offset = useSharedValue(160);
  const lStyle = useAnimatedStyle(() => ({ transform: [{ translateX: -offset.value }] }));
  const rStyle = useAnimatedStyle(() => ({ transform: [{ translateX: offset.value }] }));

  useEffect(() => {
    offset.value = withTiming(28, { duration: 360 });
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  }, []);

  return (
    <View style={{ flex: 1, backgroundColor: 'rgba(0,0,0,0.92)', alignItems: 'center', justifyContent: 'center', gap: 28 }}>
      <View style={{ flexDirection: 'row' }}>
        <Animated.View style={lStyle}>
          <LinearGradient colors={left} style={{ width: 96, height: 96, borderRadius: 48 }} />
        </Animated.View>
        <Animated.View style={rStyle}>
          <LinearGradient colors={right} style={{ width: 96, height: 96, borderRadius: 48 }} />
        </Animated.View>
      </View>
      <Text style={typography.display}>It's a Crush!</Text>
      {/* Gold sparkle layer would mount here — the only place gold appears besides premium */}
      <Pressable onPress={onStartChat}>
        <LinearGradient colors={heroGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
          style={{ paddingVertical: 15, paddingHorizontal: 30, borderRadius: 999 }}>
          <Text style={[typography.button, { color: colors.onPink }]}>Start chatting</Text>
        </LinearGradient>
      </Pressable>
    </View>
  );
}
```

### Map Crossing Pin (teardrop)

```tsx
// components/CrossingPin.tsx
import { View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, heroGradient } from '../theme/colors';

export function CrossingPin({ kind }: { kind: 'standard' | 'mutual' | 'ghost' }) {
  const body = {
    width: 44, height: 44,
    borderRadius: 22, borderBottomLeftRadius: 6,
    transform: [{ rotate: '45deg' }],
    alignItems: 'center' as const, justifyContent: 'center' as const,
  };
  const inner = { width: 30, height: 30, borderRadius: 15, transform: [{ rotate: '-45deg' }] };

  if (kind === 'mutual') {
    return (
      <LinearGradient colors={heroGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={body}>
        <View style={[inner, { backgroundColor: '#FFFFFF' }]} />
      </LinearGradient>
    );
  }
  return (
    <View style={[body, {
      backgroundColor: kind === 'ghost' ? colors.surface2 : colors.pink,
      borderWidth: kind === 'ghost' ? 1 : 0, borderColor: colors.divider,
    }]}>
      <View style={[inner, { backgroundColor: kind === 'ghost' ? colors.textTertiary : '#FFFFFF' }]} />
    </View>
  );
}
```

### Buttons

```tsx
// components/HappnButtons.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, heroGradient } from '../theme/colors';
import { typography } from '../theme/typography';

export function HappnPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      backgroundColor: pressed ? colors.pinkPress : colors.pink,
      paddingVertical: 15, paddingHorizontal: 30, borderRadius: 999,
      alignItems: 'center', transform: [{ scale: pressed ? 0.98 : 1 }],
    })}>
      <Text style={[typography.button, { color: colors.onPink }]}>{title}</Text>
    </Pressable>
  );
}

export function HappnGradientButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress}>
      <LinearGradient colors={heroGradient} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ paddingVertical: 15, paddingHorizontal: 30, borderRadius: 999, alignItems: 'center' }}>
        <Text style={[typography.button, { color: colors.onPink }]}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: colors.pink,        // pink icon + pink label; no pill
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarLabelStyle: { fontFamily: 'Poppins-SemiBold', fontSize: 10, letterSpacing: 0.2 },
        tabBarBackground: () => <BlurView intensity={30} tint="dark" style={{ flex: 1 }} />,
        tabBarStyle: { position: 'absolute', backgroundColor: 'rgba(14,14,18,0.94)', borderTopWidth: 0.5, borderTopColor: colors.divider },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Timeline', tabBarIcon: ({ color }) => <Ionicons name="heart-circle" size={23} color={color} /> }} />
      <Tabs.Screen name="map"     options={{ title: 'Map',      tabBarIcon: ({ color }) => <Ionicons name="map" size={23} color={color} /> }} />
      <Tabs.Screen name="chats"   options={{ title: 'Chats',    tabBarIcon: ({ color }) => <Ionicons name="chatbubbles" size={23} color={color} /> }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Charm tap — heart withSequence(spring 1.25 → spring 1) + success haptic (see CharmButton)

// Crush reveal — avatars slide from edges withTiming(28, { duration: 360 }) + success haptic (see CrushCelebration)

// Timeline new-crossing entrance — Reanimated layout animation
import Animated, { FadeInDown } from 'react-native-reanimated';
// entering={FadeInDown.duration(240)}

// Dot pulse for a fresh crossing — halo scale 1 → 1.4 → 1 once
haloScale.value = withSequence(withTiming(1.4, { duration: 300 }), withTiming(1, { duration: 300 }));

// Map pin tap — lift -6 + bounce
pinY.value = withSequence(withSpring(-6, { damping: 6 }), withSpring(0, { damping: 9 }));

// Haptics
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // Charm sent, Crush
Haptics.selectionAsync();                                            // tab switch, pin tap
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). happn iconography is rounded/simple; Ionicons covers it.

| Purpose | Ionicons |
|---------|----------|
| Timeline (tab) | `heart-circle` |
| Map (tab) | `map` |
| Chats (tab) | `chatbubbles` |
| Profile (tab) | `person` |
| Charm (heart) | `heart-outline` / `heart` |
| Crush (star) | `star` |
| Location stamp | `location-outline` |
| Search | `search` |
| Back | `chevron-back` |
| Send message | `arrow-up-circle` |
| FlashNote | `chatbox-ellipses-outline` |
| Block / report | `shield-outline` |
| Settings | `settings-outline` |
| Interest received | `heart-circle-outline` |

## 7. Platform Notes

- **Both themes**: happn ships light + dark; brand is most recognizable on dark. Swap canvas/surface/text via `useColorScheme()`; keep pink `#FF4865`, magenta, the hero gradient, and Crush gold `#FFC24B` **identical** across modes
- **Fonts**: Poppins + Inter are SIL OFL — free to bundle. Poppins for warmth, Inter for legibility
- **Map**: use `react-native-maps`; render `CrossingPin` as a custom `<Marker>` child; keep the teardrop's `transform: rotate('45deg')` and counter-rotate the inner dot
- **Status bar**: `<StatusBar style="light" />` on dark, `"dark"` on light
- **Safe area**: wrap screens in `SafeAreaView`; the absolute blurred tab bar needs bottom safe-area padding; the last timeline card must scroll clear of it
- **Blur**: `expo-blur` powers the tab bar; on Android blur is approximate — fall back to solid `rgba(14,14,18,0.94)`
- **Dynamic Type**: set `allowFontScaling={false}` on the crossing count, tab labels, and location/time meta (timeline row layout is sensitive); allow scaling on display/name/section/body
- **Card name**: `numberOfLines={1}` so a long name never forces the card to 2 lines and misaligns the connector dot
- **Accessibility**: give each `CrossingRow` an `accessibilityLabel` "Crossed paths with {name}, near {place}, {timeAgo}, {n} times"; expose Charm as an `accessibilityRole="button"` "Send a Charm"; the crossing count must be read by screen readers (it's meaning, not decoration)
- **Gold discipline**: never use `colors.gold` outside the Crush celebration or premium upsell — it dilutes the moment
