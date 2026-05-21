# Tinder (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tinder's visual language into paste-ready Expo / React Native code: design tokens, the signature swipe-card stack, stamp overlays, match screen, and the five-button action bar.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `react-native-reanimated` v3, `react-native-gesture-handler`, `expo-linear-gradient`, `expo-haptics`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas
  canvas:        '#FFFFFF',
  surfaceMuted:  '#F5F5F5',
  surfaceTint:   '#FAFAFA',
  divider:       '#E5E5E5',

  // Text
  textPrimary:   '#424242',
  textSecondary: '#737373',
  textTertiary:  '#9E9E9E',

  // Brand
  pink:          '#FD267A',
  orange:        '#FF6036',

  // Action colors
  nopeRed:       '#FF4458',
  superLikeBlue: '#5D8DF1',
  boostPurple:   '#A952FF',
  rewindGold:    '#FFBD3B',
  likeStampGreen:'#00D68F',

  // Semantic
  verifiedBlue:  '#29B0FF',
  matchGlow:     '#00D68F',

  // Dark
  darkCanvas:    '#121212',
  darkSurface1:  '#1D1D1D',
  darkSurface2:  '#2A2A2A',
} as const;

export const brandGradient = [colors.pink, colors.orange] as const;
```

## 2. Typography

Tinder Sans is proprietary. Bundle if licensed; otherwise use System (SF Pro).

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { fontFamily: 'System' } satisfies TextStyle;

export const typography = {
  matchHero:      { ...base, fontWeight: '800', fontStyle: 'italic', fontSize: 48, lineHeight: 53, letterSpacing: -0.5, color: '#FFFFFF' },
  screenTitle:    { ...base, fontWeight: '700', fontSize: 28, lineHeight: 34, letterSpacing: -0.3, color: '#424242' },
  profileName:    { ...base, fontWeight: '700', fontSize: 28, lineHeight: 32, letterSpacing: -0.3, color: '#FFFFFF' },
  profileAge:     { ...base, fontWeight: '400', fontSize: 24, lineHeight: 28, color: '#FFFFFF' },
  section:        { ...base, fontWeight: '700', fontSize: 20, lineHeight: 24, letterSpacing: -0.2, color: '#424242' },
  button:         { ...base, fontWeight: '700', fontSize: 16, letterSpacing: 0.2, color: '#FFFFFF' },
  buttonSmall:    { ...base, fontWeight: '500', fontSize: 15, color: '#424242' },
  body:           { ...base, fontWeight: '400', fontSize: 15, lineHeight: 22, color: '#424242' },
  cardMeta:       { ...base, fontWeight: '400', fontSize: 14, lineHeight: 18, color: 'rgba(255,255,255,0.9)' },
  chat:           { ...base, fontWeight: '400', fontSize: 15, lineHeight: 20, color: '#424242' },
  chatTimestamp:  { ...base, fontWeight: '400', fontSize: 11, color: '#9E9E9E' },
  stamp:          { ...base, fontWeight: '800', fontStyle: 'italic', fontSize: 36, letterSpacing: 2 },
  superStamp:     { ...base, fontWeight: '800', fontStyle: 'italic', fontSize: 32, letterSpacing: 2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Swipe Card with Drag, Rotation, and Stamps

```tsx
// components/TinderSwipeCard.tsx
import React from 'react';
import { Image, Pressable, Text, View, Dimensions } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, {
  useSharedValue, useAnimatedStyle, withSpring, withTiming, runOnJS, interpolate,
} from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors, brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';

const { width: SCREEN_W } = Dimensions.get('window');
const SWIPE_THRESHOLD = SCREEN_W * 0.35;

export type SwipeDirection = 'left' | 'right' | 'up';

export function TinderSwipeCard({
  name, age, distance, occupation, photoUris, onSwipe,
}: {
  name: string; age: number; distance: string; occupation: string;
  photoUris: string[];
  onSwipe: (dir: SwipeDirection) => void;
}) {
  const tx = useSharedValue(0);
  const ty = useSharedValue(0);
  const [photoIdx, setPhotoIdx] = React.useState(0);

  const commit = (dir: SwipeDirection) => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    onSwipe(dir);
  };

  const pan = Gesture.Pan()
    .onUpdate(e => {
      tx.value = e.translationX;
      ty.value = e.translationY;
    })
    .onEnd(e => {
      if (e.translationX > SWIPE_THRESHOLD) {
        tx.value = withTiming(SCREEN_W * 1.5, { duration: 400 });
        runOnJS(commit)('right');
      } else if (e.translationX < -SWIPE_THRESHOLD) {
        tx.value = withTiming(-SCREEN_W * 1.5, { duration: 400 });
        runOnJS(commit)('left');
      } else if (e.translationY < -SWIPE_THRESHOLD) {
        ty.value = withTiming(-1000, { duration: 400 });
        runOnJS(commit)('up');
      } else {
        tx.value = withSpring(0, { damping: 14 });
        ty.value = withSpring(0, { damping: 14 });
      }
    });

  const cardStyle = useAnimatedStyle(() => ({
    transform: [
      { translateX: tx.value }, { translateY: ty.value },
      { rotate: `${tx.value / 12}deg` },
    ],
  }));
  const likeOpacity = useAnimatedStyle(() => ({
    opacity: interpolate(tx.value, [0, 150], [0, 1], 'clamp'),
  }));
  const nopeOpacity = useAnimatedStyle(() => ({
    opacity: interpolate(tx.value, [0, -150], [0, 1], 'clamp'),
  }));
  const superOpacity = useAnimatedStyle(() => ({
    opacity: interpolate(ty.value, [0, -150], [0, 1], 'clamp'),
  }));

  return (
    <GestureDetector gesture={pan}>
      <Animated.View
        style={[{
          width: SCREEN_W - 32,
          aspectRatio: 3 / 4,
          borderRadius: 16,
          overflow: 'hidden',
          shadowColor: '#000', shadowOpacity: 0.15, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
        }, cardStyle]}
      >
        {/* Photo */}
        <Pressable
          onPress={(e) => {
            const x = e.nativeEvent.locationX;
            if (x < (SCREEN_W - 32) / 2) setPhotoIdx(Math.max(0, photoIdx - 1));
            else                         setPhotoIdx(Math.min(photoUris.length - 1, photoIdx + 1));
          }}
          style={{ flex: 1 }}
        >
          <Image source={{ uri: photoUris[photoIdx] }} style={{ flex: 1, width: '100%' }} resizeMode="cover" />
        </Pressable>

        {/* Photo paginator */}
        <View style={{
          position: 'absolute', top: 8, left: 16, right: 16,
          flexDirection: 'row', gap: 3,
        }}>
          {photoUris.map((_, i) => (
            <View key={i} style={{
              flex: 1, height: 3, borderRadius: 1.5,
              backgroundColor: i === photoIdx ? '#FFFFFF' : 'rgba(255,255,255,0.4)',
            }} />
          ))}
        </View>

        {/* Bottom gradient */}
        <LinearGradient
          colors={['transparent', 'rgba(0,0,0,0.7)']}
          style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '40%' }}
        />

        {/* Name + age + meta */}
        <View style={{ position: 'absolute', left: 20, right: 20, bottom: 20 }}>
          <View style={{ flexDirection: 'row', alignItems: 'baseline' }}>
            <Text style={typography.profileName}>{name}</Text>
            <Text style={typography.profileAge}>, {age}</Text>
          </View>
          <Text style={typography.cardMeta}>{distance} · {occupation}</Text>
        </View>

        {/* Stamps */}
        <Animated.View style={[{ position: 'absolute', top: 32, left: 32 }, likeOpacity]}>
          <StampView text="LIKE" color={colors.likeStampGreen} rotation={-15} />
        </Animated.View>
        <Animated.View style={[{ position: 'absolute', top: 32, right: 32 }, nopeOpacity]}>
          <StampView text="NOPE" color={colors.nopeRed} rotation={15} />
        </Animated.View>
        <Animated.View style={[{ position: 'absolute', top: 80, alignSelf: 'center' }, superOpacity]}>
          <StampView text="SUPER LIKE" color={colors.superLikeBlue} rotation={0} />
        </Animated.View>
      </Animated.View>
    </GestureDetector>
  );
}

function StampView({ text, color, rotation }: { text: string; color: string; rotation: number }) {
  return (
    <View style={{
      borderWidth: 4, borderColor: color, borderRadius: 6,
      paddingHorizontal: 12, paddingVertical: 6,
      transform: [{ rotate: `${rotation}deg` }],
    }}>
      <Text style={[typography.stamp, { color }]}>{text}</Text>
    </View>
  );
}
```

### Five Action Buttons Row

```tsx
// components/TinderActionBar.tsx
import { View, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

type Action = {
  size: number;
  color: string;
  icon: string;
  onPress: () => void;
};

export function TinderActionBar({
  onRewind, onNope, onSuper, onLike, onBoost,
}: {
  onRewind: () => void; onNope: () => void; onSuper: () => void;
  onLike: () => void; onBoost: () => void;
}) {
  const actions: Action[] = [
    { size: 48, color: colors.rewindGold,     icon: 'refresh',    onPress: onRewind },
    { size: 56, color: colors.nopeRed,        icon: 'close',      onPress: onNope },
    { size: 40, color: colors.superLikeBlue,  icon: 'star',       onPress: onSuper },
    { size: 56, color: colors.likeStampGreen, icon: 'heart',      onPress: onLike },
    { size: 48, color: colors.boostPurple,    icon: 'flash',      onPress: onBoost },
  ];
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'center', alignItems: 'center', gap: 12 }}>
      {actions.map((a, i) => <TinderActionButton key={i} {...a} />)}
    </View>
  );
}

function TinderActionButton({ size, color, icon, onPress }: Action) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.92, { damping: 10 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 10 }))}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium); onPress(); }}
    >
      <Animated.View style={[{
        width: size, height: size, borderRadius: size / 2,
        backgroundColor: colors.canvas,
        borderWidth: 2, borderColor: color,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#000', shadowOpacity: 0.1, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
      }, style]}>
        <Ionicons name={icon as any} size={size * 0.42} color={color} />
      </Animated.View>
    </Pressable>
  );
}
```

### Primary Brand CTA (Pill with Gradient)

```tsx
// components/TinderBrandButton.tsx
import { Pressable, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TinderBrandButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        opacity: pressed ? 0.9 : 1,
        transform: [{ scale: pressed ? 0.97 : 1 }],
        shadowColor: colors.pink, shadowOpacity: 0.3, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
      })}
    >
      <LinearGradient
        colors={brandGradient}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
        style={{
          height: 52, borderRadius: 32, alignItems: 'center', justifyContent: 'center',
          paddingHorizontal: 32,
        }}
      >
        <Text style={typography.button}>{title}</Text>
      </LinearGradient>
    </Pressable>
  );
}
```

### Match Screen

```tsx
// components/TinderMatchScreen.tsx
import { Image, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient } from '../theme/colors';
import { typography } from '../theme/typography';
import * as Haptics from 'expo-haptics';
import { useEffect } from 'react';

export function TinderMatchScreen({
  yourPhoto, theirPhoto, theirName, onSend, onKeepPlaying,
}: {
  yourPhoto: string; theirPhoto: string; theirName: string;
  onSend: () => void; onKeepPlaying: () => void;
}) {
  useEffect(() => {
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  }, []);

  return (
    <LinearGradient
      colors={brandGradient}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
      style={{ flex: 1, alignItems: 'center', justifyContent: 'center', gap: 24, padding: 24 }}
    >
      <Text style={typography.matchHero}>It's a Match!</Text>
      <Text style={typography.body}>You and {theirName} liked each other</Text>

      <View style={{ flexDirection: 'row', gap: 20 }}>
        <Image source={{ uri: yourPhoto }}  style={{ width: 120, height: 120, borderRadius: 60, borderWidth: 3, borderColor: '#FFFFFF' }} />
        <Image source={{ uri: theirPhoto }} style={{ width: 120, height: 120, borderRadius: 60, borderWidth: 3, borderColor: '#FFFFFF' }} />
      </View>

      <View style={{ width: '100%', gap: 12, marginTop: 24 }}>
        <Pressable onPress={onSend}
          style={{ height: 52, borderRadius: 32, alignItems: 'center', justifyContent: 'center', backgroundColor: '#FFFFFF' }}>
          <Text style={{ ...typography.button, color: brandGradient[0] }}>Send Message</Text>
        </Pressable>
        <Pressable onPress={onKeepPlaying}
          style={{ height: 52, borderRadius: 32, alignItems: 'center', justifyContent: 'center', borderWidth: 2, borderColor: '#FFFFFF' }}>
          <Text style={typography.button}>Keep Playing</Text>
        </Pressable>
      </View>
    </LinearGradient>
  );
}
```

### Chat Bubble (Gradient Receiver)

```tsx
// components/TinderChatBubble.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { brandGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TinderChatBubble({
  text, sender,
}: { text: string; sender: 'me' | 'them' }) {
  const isThem = sender === 'them';
  return (
    <View style={{
      flexDirection: 'row',
      justifyContent: isThem ? 'flex-start' : 'flex-end',
      paddingHorizontal: 16, marginVertical: 4,
    }}>
      {isThem ? (
        <LinearGradient
          colors={brandGradient}
          start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
          style={{ maxWidth: '75%', borderRadius: 20, paddingVertical: 10, paddingHorizontal: 14 }}>
          <Text style={[typography.chat, { color: '#FFFFFF' }]}>{text}</Text>
        </LinearGradient>
      ) : (
        <View style={{
          maxWidth: '75%', borderRadius: 20,
          paddingVertical: 10, paddingHorizontal: 14,
          backgroundColor: colors.surfaceMuted,
        }}>
          <Text style={typography.chat}>{text}</Text>
        </View>
      )}
    </View>
  );
}
```

## 4. Tab Bar (icon-only)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { LinearGradient } from 'expo-linear-gradient';
import { colors, brandGradient } from '../../theme/colors';

function GradientIcon({ name, focused, size }: { name: any; focused: boolean; size: number }) {
  if (!focused) return <Ionicons name={name} size={size} color={colors.textTertiary} />;
  return (
    <LinearGradient
      colors={brandGradient}
      start={{ x: 0, y: 0 }} end={{ x: 1, y: 0 }}
      style={{ width: size, height: size, alignItems: 'center', justifyContent: 'center' }}>
      <Ionicons name={name} size={size} color="#FFFFFF" />
    </LinearGradient>
  );
}

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarShowLabel: false,
        tabBarStyle: {
          height: 64,
          backgroundColor: colors.canvas,
          borderTopColor: colors.divider, borderTopWidth: 0.5,
        },
      }}
    >
      <Tabs.Screen name="index"     options={{ tabBarIcon: ({ focused }) => <GradientIcon name="flame"       focused={focused} size={28} /> }} />
      <Tabs.Screen name="top-picks" options={{ tabBarIcon: ({ focused }) => <GradientIcon name="star"        focused={focused} size={28} /> }} />
      <Tabs.Screen name="chats"     options={{ tabBarIcon: ({ focused }) => <GradientIcon name="chatbubble"  focused={focused} size={28} /> }} />
      <Tabs.Screen name="profile"   options={{ tabBarIcon: ({ focused }) => <GradientIcon name="person"      focused={focused} size={28} /> }} />
    </Tabs>
  );
}
```

## 5. Motion Specs

```tsx
// Card commit off-screen — 400ms ease-out
tx.value = withTiming(SCREEN_W * 1.5, { duration: 400 });

// Snap back — spring damping 0.7
tx.value = withSpring(0, { damping: 14 });

// Next card rise (peek behind)
nextScale.value = withSpring(1.0, { damping: 12 });

// Match screen confetti — use react-native-confetti-cannon
<ConfettiCannon count={80} origin={{ x: -10, y: 0 }} colors={[colors.pink, colors.orange, colors.likeStampGreen]} />

// New match avatar glow
const glow = useSharedValue(1);
glow.value = withRepeat(withTiming(1.2, { duration: 1500 }), -1, true);
const glowStyle = useAnimatedStyle(() => ({ transform: [{ scale: glow.value }], opacity: 2 - glow.value }));
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons):

| Purpose | Ionicons |
|---------|----------|
| Flame (tab) | `flame` |
| Star (tab / Super Like) | `star` |
| Chat (tab) | `chatbubble` |
| Profile (tab) | `person` |
| Rewind | `refresh` |
| Nope | `close` |
| Like (heart) | `heart` |
| Boost | `flash` |
| Verified | `checkmark-circle` |
| Info (card ⓘ) | `information-circle` |
| Send | `arrow-up` |
| GIF picker | `images` |
| Settings | `settings-sharp` |

## 7. Platform Notes

- **Gesture handler setup**: Wrap the root in `GestureHandlerRootView` from `react-native-gesture-handler`.
- **Reanimated**: Add `'react-native-reanimated/plugin'` to Babel config (last plugin in the list).
- **Fonts**: Load `TinderSans-*` TTFs via `expo-font` if licensed; fall back to `System`.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. Swipe screen uses `edges={['top', 'left', 'right']}` so the action buttons sit 24pt above the home indicator.
- **Status bar**: `<StatusBar style="dark" />` from `expo-status-bar` on light mode. Flip to `"light"` on the Match screen since the gradient is saturated.
- **Dynamic Type**: Honor user scaling on body + bio + chat. Cap `maxFontSizeMultiplier={1.3}` on action button labels if labels are added; stamp sizes stay fixed.
- **Dark mode**: `useColorScheme()`; swap canvas to `#121212`, surfaces to `#1D1D1D` / `#2A2A2A`, text primary to `#FFFFFF`.
- **Haptics**:
  - `Haptics.impactAsync(Medium)` on swipe commit and action button press
  - `Haptics.notificationAsync(Success)` on Match reveal
  - `Haptics.impactAsync(Heavy)` on Boost activation
  - `Haptics.selectionAsync()` on photo paginator tap
- **Accessibility**:
  - Swipe card: `accessibilityLabel="[Name], [age], [distance], [occupation]"`, `accessibilityHint="Swipe right to like, left to pass, up to super like"`
  - Action buttons: `accessibilityLabel` per action; never rely on shape alone
  - Stamps: mark `accessibilityElementsHidden={true}` — they are decorative overlays
