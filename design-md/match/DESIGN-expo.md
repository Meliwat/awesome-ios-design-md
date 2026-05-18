# Match (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Match's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-blur`, and `react-native-reanimated` v3 + `react-native-gesture-handler`.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light)
  canvas:         '#FFFFFF',
  surfaceGray:    '#F6F4F4',
  surfacePressed: '#ECE8E8',
  divider:        '#E7E2E2',

  // Surfaces (dark)
  darkCanvas:     '#141414',
  darkSurface1:   '#1E1E1E',
  darkSurface2:   '#2A2424',
  darkDivider:    '#322C2C',

  // Text
  textPrimary:        '#1A1A1A',
  textSecondary:      '#6E6666',
  textTertiary:       '#A39B9B',
  darkTextPrimary:    '#F2EDED',
  darkTextSecondary:  '#A89E9E',

  // Brand
  matchRed:        '#E92434',
  matchRedBright:  '#F0203E',
  matchRedPressed: '#C81B2C',
  matchInk:        '#1A1A1A',

  // Functional accents
  activeGreen: '#2ECC71',
  superBlue:   '#1FB6FF',
  gold:        '#D6A75B',
  boost:       '#B36BD8',
  error:       '#F2545B',

  // On-photo
  onPhoto:        '#FFFFFF',
  onPhotoDim:     'rgba(255,255,255,0.75)',
  chipFill:       'rgba(255,255,255,0.16)',
  chipBorder:     'rgba(255,255,255,0.28)',
} as const;

export type MatchColor = keyof typeof colors;

// Gradients (use with expo-linear-gradient)
export const gradients = {
  photoScrim:  ['transparent', 'rgba(20,20,20,0.95)'] as const,
  celebration: ['#2A1418', '#1A1A1A'] as const,
  premium:     ['#F0203E', '#B81DB0'] as const,
};
```

## 2. Typography

Load `Poppins` (SIL OFL) via `expo-font` as the closest free substitute for Match's custom geometric face.

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

const primary = { color: '#1A1A1A' } satisfies TextStyle;
const onPhoto = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  wordmark:    { color: '#E92434', fontFamily: 'Poppins-ExtraBold', fontSize: 28, letterSpacing: -0.4 },
  profileName: { ...onPhoto, fontFamily: 'Poppins-Bold',    fontSize: 26, letterSpacing: -0.3 },
  profileAge:  { ...onPhoto, fontFamily: 'Poppins-Medium',  fontSize: 22, letterSpacing: -0.2 },
  matchTitle:  { color: '#F0203E', fontFamily: 'Poppins-ExtraBold', fontSize: 26, letterSpacing: 0.5 },
  screenTitle: { ...primary, fontFamily: 'Poppins-Bold',    fontSize: 22, letterSpacing: -0.2 },
  section:     { ...primary, fontFamily: 'Poppins-SemiBold', fontSize: 18, letterSpacing: -0.1 },
  body:        { ...primary, fontFamily: 'Poppins-Regular',  fontSize: 16, lineHeight: 24 },
  listTitle:   { ...primary, fontFamily: 'Poppins-SemiBold', fontSize: 15 },
  button:      { color: '#FFFFFF', fontFamily: 'Poppins-Bold', fontSize: 16, letterSpacing: 0.2 },
  meta:        { color: '#6E6666', fontFamily: 'Poppins-Regular', fontSize: 14 },
  subtitle:    { fontFamily: 'Poppins-Medium', fontSize: 14 },
  chip:        { ...onPhoto, fontFamily: 'Poppins-SemiBold', fontSize: 12, letterSpacing: 0.1 },
  tab:         { fontFamily: 'Poppins-SemiBold', fontSize: 10, letterSpacing: 0.1 },
  timestamp:   { fontFamily: 'Poppins-Regular', fontSize: 12 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Full-Bleed Profile Card

```tsx
// components/ProfileCard.tsx
import { Image, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function ProfileCard({
  uri, name, age, job, distance, interests, photoCount, activeIndex, verified,
}: {
  uri: string; name: string; age: number; job: string; distance: string;
  interests: string[]; photoCount: number; activeIndex: number; verified?: boolean;
}) {
  return (
    <View style={{ flex: 1, borderRadius: 20, overflow: 'hidden',
      shadowColor: '#000', shadowOpacity: 0.55, shadowRadius: 28, shadowOffset: { width: 0, height: 12 } }}>
      <Image source={{ uri }} style={{ ...StyleSheetAbsolute }} resizeMode="cover" />
      <LinearGradient colors={gradients.photoScrim} style={{ ...StyleSheetAbsolute, top: '45%' }} />

      {/* Story bars */}
      <View style={{ position: 'absolute', top: 12, left: 12, right: 12, flexDirection: 'row', gap: 4 }}>
        {Array.from({ length: photoCount }).map((_, i) => (
          <View key={i} style={{ flex: 1, height: 3, borderRadius: 2,
            backgroundColor: i === activeIndex ? '#FFF' : 'rgba(255,255,255,0.35)' }} />
        ))}
      </View>

      <View style={{ position: 'absolute', left: 18, right: 18, bottom: 92, gap: 6 }}>
        <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 8 }}>
          <Text style={typography.profileName}>{name}</Text>
          <Text style={typography.profileAge}>{age}</Text>
          {verified && <Ionicons name="shield-checkmark" size={17} color={colors.superBlue} />}
        </View>
        <Text style={[typography.subtitle, { color: 'rgba(255,255,255,0.9)' }]}>{job}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 5 }}>
          <View style={{ width: 7, height: 7, borderRadius: 4, backgroundColor: colors.activeGreen }} />
          <Text style={[typography.meta, { color: colors.onPhotoDim }]}>{distance}</Text>
        </View>
        <View style={{ flexDirection: 'row', flexWrap: 'wrap', gap: 6, marginTop: 4 }}>
          {interests.slice(0, 4).map((t) => (
            <View key={t} style={{ borderRadius: 999, overflow: 'hidden' }}>
              <BlurView intensity={18} tint="dark"
                style={{ paddingHorizontal: 11, paddingVertical: 5,
                  borderWidth: 1, borderColor: colors.chipBorder, borderRadius: 999,
                  backgroundColor: colors.chipFill }}>
                <Text style={typography.chip}>{t}</Text>
              </BlurView>
            </View>
          ))}
        </View>
      </View>
    </View>
  );
}

const StyleSheetAbsolute = { position: 'absolute', top: 0, left: 0, right: 0, bottom: 0 } as const;
```

### Action Dock

```tsx
// components/ActionDock.tsx
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import Animated, { useAnimatedStyle, useSharedValue, withSpring } from 'react-native-reanimated';
import { colors } from '../theme/colors';

const AP = Animated.createAnimatedComponent(Pressable);

function Dock({ icon, size, tint, fill = colors.darkSurface1, bordered = true, onPress }: {
  icon: any; size: number; tint: string; fill?: string; bordered?: boolean; onPress: () => void;
}) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <AP
      onPressIn={() => { scale.value = withSpring(0.92); }}
      onPressOut={() => { scale.value = withSpring(1); }}
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={[{
        width: size, height: size, borderRadius: size / 2,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: fill,
        borderWidth: bordered ? 1 : 0, borderColor: colors.darkDivider,
        shadowColor: '#000', shadowOpacity: 0.5, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
      }, style]}
    >
      <Ionicons name={icon} size={size * 0.42} color={tint} />
    </AP>
  );
}

export function ActionDock({ onRewind, onPass, onSuper, onLike, onBoost }: Record<string, () => void>) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 16 }}>
      <Dock icon="arrow-undo" size={44} tint={colors.gold} onPress={onRewind} />
      <Dock icon="close" size={56} tint={colors.darkTextSecondary} onPress={onPass} />
      <Dock icon="star" size={48} tint={colors.superBlue} onPress={onSuper} />
      <Dock icon="heart" size={56} tint="#FFFFFF" fill={colors.matchRedBright} bordered={false} onPress={onLike} />
      <Dock icon="flash" size={44} tint={colors.boost} onPress={onBoost} />
    </View>
  );
}
```

### "It's a Match!" Celebration

```tsx
// components/MatchCelebration.tsx
import { useEffect } from 'react';
import { Image, Pressable, Text, View } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import * as Haptics from 'expo-haptics';
import Animated, { SlideInLeft, SlideInRight } from 'react-native-reanimated';
import { colors, gradients } from '../theme/colors';
import { typography } from '../theme/typography';

export function MatchCelebration({ mine, theirs, theirName, onMessage, onKeep }: {
  mine: string; theirs: string; theirName: string; onMessage: () => void; onKeep: () => void;
}) {
  useEffect(() => { Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success); }, []);
  return (
    <LinearGradient colors={gradients.celebration} style={{ flex: 1, alignItems: 'center', justifyContent: 'center', padding: 40 }}>
      <Text style={[typography.matchTitle, { textShadowColor: 'rgba(240,32,62,0.5)', textShadowRadius: 24 }]}>It's a Match!</Text>
      <Text style={[typography.timestamp, { color: colors.darkTextSecondary, marginTop: 6 }]}>
        You and {theirName} liked each other
      </Text>
      <View style={{ flexDirection: 'row', marginVertical: 16 }}>
        <Animated.Image entering={SlideInLeft.springify()} source={{ uri: mine }}
          style={{ width: 56, height: 56, borderRadius: 28, borderWidth: 3, borderColor: colors.matchInk }} />
        <Animated.Image entering={SlideInRight.springify()} source={{ uri: theirs }}
          style={{ width: 56, height: 56, borderRadius: 28, borderWidth: 3, borderColor: colors.matchInk, marginLeft: -14 }} />
      </View>
      <Pressable onPress={onMessage} style={{ width: '100%', backgroundColor: colors.matchRedBright, borderRadius: 999, paddingVertical: 15, alignItems: 'center', marginBottom: 12 }}>
        <Text style={typography.button}>Send a Message</Text>
      </Pressable>
      <Pressable onPress={onKeep} style={{ width: '100%', borderWidth: 1.5, borderColor: colors.darkTextSecondary, borderRadius: 999, paddingVertical: 13, alignItems: 'center' }}>
        <Text style={[typography.button, { color: colors.darkTextPrimary }]}>Keep Swiping</Text>
      </Pressable>
    </LinearGradient>
  );
}
```

### Likes You Tile

```tsx
// components/LikesYouTile.tsx
import { Image, Text, View } from 'react-native';
import { BlurView } from 'expo-blur';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LikesYouTile({ uri, unlocked }: { uri: string; unlocked: boolean }) {
  return (
    <View style={{ width: 96, height: 128, borderRadius: 14, overflow: 'hidden' }}>
      <Image source={{ uri }} style={{ width: 96, height: 128 }} resizeMode="cover" />
      {!unlocked && (
        <>
          <BlurView intensity={45} tint="dark" style={{ position: 'absolute', inset: 0 }} />
          <View style={{ position: 'absolute', bottom: 10, left: 0, right: 0, alignItems: 'center' }}>
            <Text style={[typography.timestamp, { color: '#FFF', backgroundColor: 'rgba(0,0,0,0.5)', paddingHorizontal: 8, paddingVertical: 4, borderRadius: 999 }]}>
              Liked you
            </Text>
          </View>
        </>
      )}
      {unlocked && (
        <View style={{ position: 'absolute', top: 8, right: 8, width: 22, height: 22, borderRadius: 11, backgroundColor: colors.matchRed, alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name="heart" size={12} color="#FFF" />
        </View>
      )}
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
        headerShown: false,
        tabBarActiveTintColor: colors.matchRedBright,
        tabBarInactiveTintColor: colors.darkTextSecondary,
        tabBarStyle: {
          backgroundColor: 'rgba(20,20,20,0.94)',
          borderTopWidth: 0.5,
          borderTopColor: colors.darkDivider,
        },
        tabBarLabelStyle: { fontFamily: 'Poppins-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="albums" size={23} color={color} /> }} />
      <Tabs.Screen name="likes"    options={{ title: 'Likes',    tabBarIcon: ({ color }) => <Ionicons name="heart" size={23} color={color} />, tabBarBadge: 5 }} />
      <Tabs.Screen name="messages" options={{ title: 'Messages', tabBarIcon: ({ color }) => <Ionicons name="chatbubble" size={23} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person" size={23} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Swipe card — rotate + translate, spring on commit (react-native-gesture-handler + reanimated)
import { Gesture } from 'react-native-gesture-handler';
import { useSharedValue, useAnimatedStyle, withSpring, runOnJS } from 'react-native-reanimated';

const x = useSharedValue(0);
const cardStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: x.value }, { rotateZ: `${x.value / 18}deg` }],
}));
const swipe = Gesture.Pan()
  .onUpdate((e) => { x.value = e.translationX; })
  .onEnd((e) => {
    if (Math.abs(e.translationX) > 110) {
      runOnJS(Haptics.impactAsync)(Haptics.ImpactFeedbackStyle.Soft);
      x.value = withSpring(e.translationX > 0 ? 700 : -700, { damping: 16 });
    } else {
      x.value = withSpring(0, { damping: 14 });
    }
  });

// Like pulse
heartScale.value = withSequence(withTiming(1.25, { duration: 140 }), withTiming(1, { duration: 140 }));

// It's a Match! — avatars from opposite edges
// entering={SlideInLeft.springify()} / entering={SlideInRight.springify()}

// Boost glow — pulsing scale loop
glow.value = withRepeat(withTiming(1.04, { duration: 1600 }), -1, true);

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                  // swipe commit, dock tap
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);    // a match
Haptics.selectionAsync();                                               // story-bar nav
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Match's iconography is simple and geometric.

| Purpose | Ionicons |
|---------|----------|
| Discover (tab) | `albums` / `albums-outline` |
| Likes (tab) | `heart` / `heart-outline` |
| Messages (tab) | `chatbubble` / `chatbubble-outline` |
| Profile (tab) | `person` / `person-outline` |
| Like (dock) | `heart` |
| Pass (dock) | `close` |
| Super Like (dock) | `star` |
| Rewind (dock) | `arrow-undo` |
| Boost (dock) | `flash` |
| Verified | `shield-checkmark` |
| Filter / preferences | `options` |
| Send message | `paper-plane` |
| Unmatch / report | `flag-outline` |
| Premium | `diamond` |

## 7. Platform Notes

- **Font choice**: `Poppins` (SIL OFL) is the free substitute for Match's custom geometric face — bundle the five weights above
- **Full-bleed photo**: render the `ProfileCard` so the image extends into the safe areas; keep text/controls inside via `useSafeAreaInsets()`; the card sits in a `GestureDetector` for swipe
- **Blur**: `expo-blur` `BlurView` powers both the frosted interest chips and the locked Likes-You tiles; on Android the blur intensity is approximate — back it with a translucent `backgroundColor` fallback
- **Status bar**: `<StatusBar style="light" />` over the (dark-photo) Discover screen; switch to `"dark"`/`"light"` per color scheme on the supporting screens
- **Safe area**: wrap supporting screens in `SafeAreaView`; the action dock floats above the home indicator
- **Dynamic Type**: set `allowFontScaling={false}` on tab labels, interest chips, story-bar text, timestamps, and the distance line; let profile name / body / section headers scale
- **Dark mode**: use `useColorScheme()` to swap to `darkCanvas`/`darkSurface1`; Match Red brightens to `#F0203E` (`matchRedBright`) for the Like button and active tab on dark
- **Gesture conflicts**: the swipe `Pan` gesture and the story-bar tap zones share the card — use `Gesture.Exclusive` or a small `activeOffsetX` so a tap doesn't start a swipe
- **Accessibility**: give the dock buttons `accessibilityLabel`s; locked Likes-You tiles announce "Someone liked you — subscribe to see"; the match overlay uses `accessibilityViewIsModal`
- **Confetti**: use `react-native-confetti-cannon` or a Reanimated particle burst on the "It's a Match!" screen, fired alongside the success haptic
