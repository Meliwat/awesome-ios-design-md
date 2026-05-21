# Locket (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Locket's warm cream-gold visual language into paste-ready Expo / React Native code: a design-token module, themed components, Reanimated snippets for the capture button, and a note on the Home Screen widget (the widget IS the product).

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, `expo-camera`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Brand
  locketGold:   '#FFB02E',
  locketAmber:  '#FF9E2C',
  locketDeep:   '#F08A1D', // on-cream accent
  goldPressed:  '#DC7A12',
  onGold:       '#3A2400',

  // Surfaces
  cream:    '#FFF7EC',
  bgTop:    '#FFE9CC',
  bgBottom: '#FFD9A8',
  white:    '#FFFFFF',
  surface:  '#FFF1DD',
  divider:  '#F2E2C9',

  // Text
  textPrimary:   '#2C2014', // warm brown, NOT black
  textSecondary: '#8A7A63',
  textTertiary:  '#B8A88E',

  // Accent
  coral: '#FF7A59',

  // Semantic
  success: '#38C172',
  error:   '#FF5A5A',
} as const;

export type LocketColor = keyof typeof colors;

// World background (full-bleed, every screen)
export const worldGradient = ['#FFE9CC', '#FFD9A8'] as const;
export const avatarGradient = ['#FFB02E', '#FF7A59'] as const;

// Warm honey-tinted shadow — NEVER neutral gray
export const honeyShadow = (opacity = 0.3) => ({
  shadowColor: '#B4781E', shadowOpacity: opacity, shadowRadius: 20,
  shadowOffset: { width: 0, height: 8 }, elevation: 10,
});
```

## 2. Typography

Load **Poppins** via `expo-font` (SIL OFL — free), warm 600–800.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Poppins-Regular':   require('../assets/fonts/Poppins-Regular.ttf'),
    'Poppins-Medium':    require('../assets/fonts/Poppins-Medium.ttf'),
    'Poppins-SemiBold':  require('../assets/fonts/Poppins-SemiBold.ttf'),
    'Poppins-Bold':      require('../assets/fonts/Poppins-Bold.ttf'),
    'Poppins-ExtraBold': require('../assets/fonts/Poppins-ExtraBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  display:   { color: '#2C2014', fontFamily: 'Poppins-ExtraBold', fontSize: 32, lineHeight: 37, letterSpacing: -0.4 },
  screen:    { color: '#2C2014', fontFamily: 'Poppins-Bold',      fontSize: 26, lineHeight: 31, letterSpacing: -0.3 },
  section:   { color: '#2C2014', fontFamily: 'Poppins-Bold',      fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  sender:    { color: '#FFFFFF', fontFamily: 'Poppins-Bold',      fontSize: 18, lineHeight: 24 },
  caption:   { color: '#FFFFFF', fontFamily: 'Poppins-SemiBold',  fontSize: 16, lineHeight: 22 },
  body:      { color: '#8A7A63', fontFamily: 'Poppins-Medium',    fontSize: 15, lineHeight: 22 },
  bigNum:    { color: '#2C2014', fontFamily: 'Poppins-ExtraBold', fontSize: 28, lineHeight: 28, letterSpacing: -0.5 },
  meta:      { color: '#B8A88E', fontFamily: 'Poppins-Regular',   fontSize: 13, lineHeight: 17 },
  label:     { color: '#8A7A63', fontFamily: 'Poppins-Bold',      fontSize: 12, lineHeight: 12, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  button:    { color: '#3A2400', fontFamily: 'Poppins-Bold',      fontSize: 16, lineHeight: 16, letterSpacing: 0.2 },
  pill:      { color: '#2C2014', fontFamily: 'Poppins-Bold',      fontSize: 14, lineHeight: 14 },
  tab:       { color: '#B8A88E', fontFamily: 'Poppins-Bold',      fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  link:      { color: '#F08A1D', fontFamily: 'Poppins-Bold',      fontSize: 13, lineHeight: 13 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### World Background

```tsx
// components/LocketWorld.tsx
import { LinearGradient } from 'expo-linear-gradient';
import { StyleSheet } from 'react-native';
import { worldGradient } from '../theme/colors';

export function LocketWorld() {
  return <LinearGradient colors={worldGradient} style={StyleSheet.absoluteFill} />;
}
```

### Capture Button (the signature interaction)

```tsx
// components/CaptureButton.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withSequence, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

const APressable = Animated.createAnimatedComponent(Pressable);

export function CaptureButton({ onCapture }: { onCapture: () => void }) {
  const scale = useSharedValue(1);
  const pulse = useSharedValue(0);

  const btnStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  const pulseStyle = useAnimatedStyle(() => ({
    opacity: 1 - pulse.value,
    transform: [{ scale: 1 + pulse.value * 0.6 }],
  }));

  const fire = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
    scale.value = withSequence(withSpring(0.92, { damping: 8 }), withSpring(1, { damping: 6 }));
    pulse.value = 0;
    pulse.value = withTiming(1, { duration: 250 }, () => { pulse.value = 0; });
    onCapture();
  };

  return (
    <View style={{ width: 84, height: 84, alignItems: 'center', justifyContent: 'center' }}>
      <Animated.View style={[{
        position: 'absolute', width: 84, height: 84, borderRadius: 42,
        borderWidth: 4, borderColor: colors.locketGold,
      }, pulseStyle]} />
      <APressable onPress={fire} style={[{
        width: 84, height: 84, borderRadius: 42, backgroundColor: colors.white,
        borderWidth: 5, borderColor: 'rgba(255,255,255,0.55)',
        alignItems: 'center', justifyContent: 'center',
        shadowColor: '#B4781E', shadowOpacity: 0.32, shadowRadius: 26, shadowOffset: { width: 0, height: 10 }, elevation: 12,
      }, btnStyle]}>
        <View style={{ width: 66, height: 66, borderRadius: 33, borderWidth: 4, borderColor: colors.locketGold }} />
      </APressable>
    </View>
  );
}
```

### Square Viewfinder / Friend Photo

```tsx
// components/FriendPhoto.tsx
import { ImageBackground, Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import { LinearGradient } from 'expo-linear-gradient';
import { avatarGradient, colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FriendPhoto({ uri, sender, caption }:
  { uri?: string; sender: string; caption?: string }) {
  return (
    <View style={{ aspectRatio: 1, borderRadius: 40, overflow: 'hidden',
      shadowColor: '#A06414', shadowOpacity: 0.55, shadowRadius: 24, shadowOffset: { width: 0, height: 24 }, elevation: 16 }}>
      <ImageBackground source={uri ? { uri } : undefined} style={{ flex: 1, padding: 16 }}>
        {!uri && (
          <LinearGradient colors={['#F5B96B', '#C56A22']} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
            style={{ position: 'absolute', inset: 0 }} />
        )}
        <View style={{ flexDirection: 'row' }}>
          <BlurView intensity={28} tint="dark" style={{ borderRadius: 999, overflow: 'hidden' }}>
            <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8,
              backgroundColor: 'rgba(44,32,20,0.32)', paddingLeft: 6, paddingRight: 14, paddingVertical: 6 }}>
              <LinearGradient colors={avatarGradient} style={{ width: 24, height: 24, borderRadius: 12 }} />
              <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 13, color: '#FFF' }}>from {sender}</Text>
            </View>
          </BlurView>
        </View>
        <View style={{ flex: 1 }} />
        {caption ? (
          <View style={{ alignItems: 'center' }}>
            <BlurView intensity={28} tint="dark" style={{ borderRadius: 16, overflow: 'hidden' }}>
              <Text style={[typography.caption, { backgroundColor: 'rgba(44,32,20,0.36)', paddingHorizontal: 16, paddingVertical: 8 }]}>
                {caption}
              </Text>
            </BlurView>
          </View>
        ) : null}
      </ImageBackground>
    </View>
  );
}
```

### Friends Photo-History Grid

```tsx
// components/HistoryGrid.tsx
import { View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';

export function HistoryGrid({ tints }: { tints: readonly [string, string][] }) {
  return (
    <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 8 }}>
      {tints.map((g, i) => (
        <View key={i} style={{ width: '23%', aspectRatio: 1, borderRadius: 14, overflow: 'hidden' }}>
          <LinearGradient colors={g} start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }} style={{ flex: 1 }} />
          <View style={{ position: 'absolute', bottom: 4, left: 4, width: 14, height: 14, borderRadius: 7,
            backgroundColor: 'rgba(44,32,20,0.4)', borderWidth: 1.5, borderColor: '#FFF' }} />
        </View>
      ))}
    </View>
  );
}
```

### Friends Pill + Primary Button

```tsx
// components/FriendsPill.tsx
import { Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { avatarGradient, colors, honeyShadow } from '../theme/colors';

export function FriendsPill({ count }: { count: number }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', gap: 8,
      backgroundColor: colors.white, borderRadius: 999,
      paddingLeft: 10, paddingRight: 16, paddingVertical: 8, ...honeyShadow(0.18),
    }}>
      <View style={{ flexDirection: 'row' }}>
        {[0, 1, 2].map(i => (
          <LinearGradient key={i} colors={avatarGradient}
            style={{ width: 22, height: 22, borderRadius: 11, borderWidth: 2, borderColor: '#FFF', marginLeft: i ? -8 : 0 }} />
        ))}
      </View>
      <Text style={{ fontFamily: 'Poppins-Bold', fontSize: 13, color: colors.textPrimary }}>{count} friends</Text>
    </View>
  );
}
```

```tsx
// components/LocketPrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import { colors, honeyShadow } from '../theme/colors';
import { typography } from '../theme/typography';

export function LocketPrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.goldPressed : colors.locketGold,
        borderRadius: 999, paddingVertical: 14, paddingHorizontal: 28,
        transform: [{ scale: pressed ? 0.97 : 1 }], ...honeyShadow(0.3),
      })}
    >
      <Text style={typography.button}>{title}</Text>
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
        headerShown: false,
        tabBarActiveTintColor: colors.locketDeep,
        tabBarInactiveTintColor: colors.textTertiary,
        // transparent over the warm world — no frosted bar; Locket stays minimal
        tabBarStyle: { position: 'absolute', borderTopWidth: 0, backgroundColor: 'transparent', elevation: 0 },
        tabBarBackground: () => null,
        tabBarLabelStyle: { fontFamily: 'Poppins-Bold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"   options={{ title: 'Camera',  tabBarIcon: ({ color }) => <Ionicons name="camera" size={24} color={color} /> }} />
      <Tabs.Screen name="history" options={{ title: 'History', tabBarIcon: ({ color }) => <Ionicons name="grid" size={24} color={color} /> }} />
      <Tabs.Screen name="you"     options={{ title: 'You',     tabBarIcon: ({ color }) => <Ionicons name="person" size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. The Widget (the real product surface)

The Home Screen widget IS Locket. Expo/React Native cannot render a native WidgetKit widget from JS — ship a tiny **native iOS Widget Extension** (Swift/WidgetKit) and bridge data via an App Group + a config plugin (e.g. `@bacons/apple-targets` or `expo-apple-targets`). The RN app writes the latest friend photo + sender to the shared App Group container; the widget reads it and calls `WidgetCenter.reloadTimelines` on push.

```ts
// Pseudocode: after a friend's photo arrives (push handler)
import * as FileSystem from 'expo-file-system';
// 1. download photo to the App Group shared dir
// 2. write { sender, uri, ts } to a shared JSON in the App Group
// 3. trigger a silent push or use a native module to call WidgetCenter.shared.reloadAllTimelines()
```

Treat onboarding's "Add Locket to your Home Screen" as the core activation step — render an in-app 116pt rounded-24 widget mock with a sample photo + "from {name}" chip.

## 6. Motion

```tsx
// Capture press → spring + gold ring pulse
scale.value = withSequence(withSpring(0.92, { damping: 8 }), withSpring(1, { damping: 6 }));
pulse.value = withTiming(1, { duration: 250 }, () => { pulse.value = 0; });

// Send: photo lifts + fades up
import Animated, { FadeOutUp } from 'react-native-reanimated';
// <Animated.View exiting={FadeOutUp.duration(320)} />

// Incoming photo: cross-dissolve + gentle scale
import { FadeIn } from 'react-native-reanimated';
// <Animated.Image entering={FadeIn.duration(280)} />

// History open: scale into full screen (shared element style)
// react-native-shared-element OR scale + opacity 300ms spring

// Reaction: emoji bounce + float up
translateY.value = withTiming(-40, { duration: 500 }); opacity.value = withTiming(0, { duration: 500 });

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);  // shutter
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);   // reaction
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); // "Sent"
Haptics.selectionAsync();                                // tab change
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Emoji reactions (❤️ 😂 🔥 😮) render as `<Text>`.

| Purpose | Ionicons |
|---------|----------|
| Camera (tab) | `camera` / `camera-outline` |
| History (tab) | `grid` / `grid-outline` |
| You (tab) | `person` / `person-outline` |
| Flip camera | `camera-reverse-outline` |
| Flash / effects | `flash-outline` / `sparkles-outline` |
| Add friend | `person-add-outline` |
| Messages / chat | `chatbubble` |
| Send | `paper-plane` |
| Reaction heart | `heart` (tint `#FF7A59`) |
| See all | `chevron-forward` |
| Add widget | `add-circle-outline` |
| Settings | `settings` |
| Close | `close` |

## 8. Platform Notes

- **Font choice**: Poppins is SIL OFL — free to bundle. Ship Regular → ExtraBold; Locket runs warm 600–800 (avoid light)
- **Warm-by-design**: set `userInterfaceStyle: "light"` in `app.json`; force `<StatusBar style="dark" />` (the cream world is light, dark status content). Locket has NO dark theme — the cozy gold identity must hold, including the widget
- **Camera**: use `expo-camera`; the viewfinder is always SQUARE (`aspectRatio: 1`) with a 40pt radius — never rectangular; request permission only at first capture
- **Safe area**: wrap screens in `SafeAreaView`; the transparent tab bar still needs bottom safe-area padding so the capture button clears the home indicator
- **Phone-shaped on tablet**: cap the viewfinder at ~480pt max-width and keep it centered on iPad — never a wide multi-pane
- **The widget needs native code**: a WidgetKit extension via a config plugin + App Group; JS cannot render the widget. Treat "Add to Home Screen" as the core onboarding activation
- **Warm shadows only**: use `honeyShadow()` everywhere — `shadowColor: '#B4781E'` / `#A06414`, never `#000`
- **Accessibility**: label the viewfinder "Photo from {sender}{, caption}"; the capture button "Take a photo, sends to your {n} friends"; set `allowFontScaling={false}` on tab labels, uppercase labels, and the capture-button chrome
- **Privacy**: photos go only to a tiny confirmed circle; never auto-capture; no public feed — keep the intimate model intact
