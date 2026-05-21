# Grindr (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grindr's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:   '#000000',
  surface1: '#1A1A1A',
  surface2: '#222222',
  surface3: '#2C2C2C',
  divider:  '#2C2C2C',

  textPrimary:   '#FFFFFF',
  textSecondary: '#999999',
  textTertiary:  '#5C5C5C',

  yellow:        '#FFDE00',
  yellowPressed: '#E6C700',
  yellowTint:    '#FFF2A8',

  online: '#4CD964',
  error:  '#FF3B30',
  info:   '#3D8BFF',
} as const;

export type GrindrColor = keyof typeof colors;
```

## 2. Typography

Grindr ships a custom product grotesque. Load it via `expo-font`, or use **Inter** (closest free grotesque). Fall back to `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular': require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Bold':    require('../assets/fonts/Inter-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const w = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  profileName: { ...w, fontFamily: 'Inter-Bold',    fontSize: 24, lineHeight: 28, letterSpacing: -0.3 },
  title:       { ...w, fontFamily: 'Inter-Bold',    fontSize: 22, lineHeight: 26, letterSpacing: -0.2 },
  section:     { ...w, fontFamily: 'Inter-Bold',    fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  tileName:    { ...w, fontFamily: 'Inter-Bold',    fontSize: 15, lineHeight: 18 },
  rowTitle:    { ...w, fontFamily: 'Inter-Bold',    fontSize: 16, lineHeight: 21, letterSpacing: -0.1 },
  body:        { ...w, fontFamily: 'Inter-Regular', fontSize: 15, lineHeight: 22 },
  message:     { ...w, fontFamily: 'Inter-Regular', fontSize: 16, lineHeight: 22 },
  meta:        {        fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 17, color: '#999999' },
  distance:    { ...w, fontFamily: 'Inter-Bold',    fontSize: 12, lineHeight: 14, letterSpacing: 0.2 },
  labelUpper:  {        fontFamily: 'Inter-Bold',    fontSize: 11, lineHeight: 13, letterSpacing: 0.6, color: '#999999', textTransform: 'uppercase' as const },
  button:      { color: '#000000', fontFamily: 'Inter-Bold', fontSize: 16, lineHeight: 20, letterSpacing: 0.2 },
  tab:         {        fontFamily: 'Inter-Bold',    fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Cascade Grid Tile

```tsx
// components/Tile.tsx
import { Pressable, Image, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Tile({
  name, distance, photoUri, online, onPress,
}: { name: string; distance: string; photoUri: string; online: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({ flex: 1 / 3, aspectRatio: 1, margin: 1, transform: [{ scale: pressed ? 0.97 : 1 }] })}
    >
      <Image source={{ uri: photoUri }} style={{ ...StyleSheet.absoluteFillObject }} resizeMode="cover" />
      <LinearGradient
        colors={['transparent', 'rgba(0,0,0,0.65)']}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '45%' }}
      />
      {online && (
        <View style={{
          position: 'absolute', top: 8, right: 8, width: 10, height: 10, borderRadius: 5,
          backgroundColor: colors.online, borderWidth: 2, borderColor: '#000',
        }} />
      )}
      <View style={{ position: 'absolute', left: 8, bottom: 8, right: 8 }}>
        <Text style={typography.tileName} numberOfLines={1}>{name}</Text>
        <Text style={typography.distance}>{distance}</Text>
      </View>
    </Pressable>
  );
}
```

### Yellow Tap Button

```tsx
// components/TapButton.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function TapButton({
  tapped, size = 56, onPress,
}: { tapped: boolean; size?: number; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.92, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
        onPress();
      }}
    >
      <Animated.View
        style={[
          {
            width: size, height: size, borderRadius: size / 2,
            alignItems: 'center', justifyContent: 'center',
            backgroundColor: tapped ? 'transparent' : colors.yellow,
            borderWidth: tapped ? 1 : 0, borderColor: colors.textSecondary,
          },
          style,
        ]}
      >
        <Ionicons name={tapped ? 'checkmark' : 'flame'} size={size * 0.42} color={tapped ? '#FFF' : '#000'} />
      </Animated.View>
    </Pressable>
  );
}
```

### Primary Pill (Send / Chat)

```tsx
// components/PillButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PillButton({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'outline'; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        paddingVertical: 12,
        paddingHorizontal: variant === 'filled' ? 28 : 24,
        borderRadius: 500,
        backgroundColor: variant === 'filled' ? colors.yellow : 'transparent',
        borderWidth: variant === 'outline' ? 1 : 0,
        borderColor: colors.textSecondary,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={[typography.button, { color: variant === 'filled' ? '#000' : '#FFF' }]}>
        {title}
      </Text>
    </Pressable>
  );
}
```

### Chat Bubble

```tsx
// components/Bubble.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function Bubble({ text, outgoing }: { text: string; outgoing: boolean }) {
  return (
    <View style={{
      maxWidth: '76%',
      alignSelf: outgoing ? 'flex-end' : 'flex-start',
      backgroundColor: outgoing ? colors.yellow : colors.surface2,
      paddingVertical: 10, paddingHorizontal: 14,
      borderRadius: 18,
      borderBottomRightRadius: outgoing ? 4 : 18,
      borderBottomLeftRadius: outgoing ? 18 : 4,
      marginVertical: 3,
    }}>
      <Text style={[typography.message, { color: outgoing ? '#000' : '#FFF' }]}>{text}</Text>
    </View>
  );
}
```

### Message List Row

```tsx
// components/MessageRow.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function MessageRow({
  name, preview, time, avatarUri, online, unread, onPress,
}: { name: string; preview: string; time: string; avatarUri: string; online: boolean; unread: boolean; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        height: 72, flexDirection: 'row', alignItems: 'center',
        paddingHorizontal: 16, gap: 12,
        backgroundColor: pressed ? colors.surface1 : colors.canvas,
      })}
    >
      <View>
        <Image source={{ uri: avatarUri }} style={{ width: 52, height: 52, borderRadius: 26 }} />
        {online && <View style={{
          position: 'absolute', top: 0, right: 0, width: 12, height: 12, borderRadius: 6,
          backgroundColor: colors.online, borderWidth: 2, borderColor: '#000',
        }} />}
      </View>
      <View style={{ flex: 1, gap: 3 }}>
        <Text style={typography.rowTitle} numberOfLines={1}>{name}</Text>
        <Text style={[typography.meta, unread && { color: 'rgba(255,255,255,0.85)' }]} numberOfLines={1}>
          {preview}
        </Text>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 6 }}>
        <Text style={[typography.meta, { fontSize: 12 }]}>{time}</Text>
        {unread && <View style={{ width: 8, height: 8, borderRadius: 4, backgroundColor: colors.yellow }} />}
      </View>
    </Pressable>
  );
}
```

## 4. Cascade Grid (lazy-load fade)

```tsx
import { FlatList } from 'react-native';
import Animated, { FadeIn } from 'react-native-reanimated';
import { Tile } from './components/Tile';
import { colors } from './theme/colors';

export function Cascade({ profiles }: { profiles: Profile[] }) {
  return (
    <FlatList
      data={profiles}
      numColumns={3}
      keyExtractor={(p) => p.id}
      style={{ backgroundColor: colors.canvas }}
      renderItem={({ item }) => (
        <Animated.View entering={FadeIn.duration(180)} style={{ flex: 1 / 3 }}>
          <Tile name={item.name} distance={item.distance} photoUri={item.photo}
                online={item.online} onPress={() => openProfile(item)} />
        </Animated.View>
      )}
      removeClippedSubviews
      windowSize={7}
    />
  );
}
```

## 5. Tab Bar

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.yellow,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="dark" style={{ flex: 1, backgroundColor: 'rgba(0,0,0,0.7)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Inter-Bold', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Browse',   tabBarIcon: ({ color }) => <Ionicons name="grid"          size={26} color={color} /> }} />
      <Tabs.Screen name="taps"     options={{ title: 'Taps',     tabBarIcon: ({ color }) => <Ionicons name="flame"         size={26} color={color} /> }} />
      <Tabs.Screen name="messages" options={{ title: 'Messages', tabBarIcon: ({ color }) => <Ionicons name="chatbubble"    size={26} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person"        size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// Tap send — success haptic
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// Cascade lazy-load — Reanimated FadeIn.duration(180) per tile (see Cascade)

// Profile sheet present — push a modal route; animate the hero photo with a shared
// progress value (useAnimatedStyle) from the tile frame to full-bleed.

// Message send — outgoing bubble scale-in
const s = useSharedValue(0.9);
useEffect(() => { s.value = withSpring(1, { damping: 16 }); }, []);
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Grindr's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Tap action | `flame` |
| Tapped (sent) | `checkmark` |
| Favorite | `star-outline` / `star` |
| Block | `ban` |
| More | `ellipsis-horizontal` |
| Send arrow | `arrow-up-circle` |
| Photo / camera | `camera` |
| Filter | `options` |
| Search | `search` |
| Settings | `settings-sharp` |
| Browse (tab) | `grid` |
| Taps (tab) | `flame` |
| Messages (tab) | `chatbubble` |
| Profile (tab) | `person` |

## 8. Platform Notes

- **iOS-only feel**: use `expo-blur` for the translucent tab bar; Android falls back to a near-opaque `rgba(0,0,0,0.94)`
- **Status bar**: set `<StatusBar style="light" />` from `expo-status-bar` globally — the true-black canvas requires light content
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; the cascade goes edge-to-edge horizontally but respects top/bottom safe area; the chat composer floats above the keyboard
- **Grid performance**: use `FlatList` with `removeClippedSubviews`, a tuned `windowSize`, and `getItemLayout` (square tiles make this exact) so the cascade scrolls at 60fps
- **Dynamic Type**: React Native honors font scaling; set `allowFontScaling={false}` on grid overlay labels (name/distance) and tab labels — they are layout-locked over photos
- **Accessibility**: each `Tile` should be one `accessible` element with a combined `accessibilityLabel` ("Alex, 220 feet, online"); the Tap button gets `accessibilityRole="button"` and announces its sent state
