# Bumble (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bumble's visual language into paste-ready Expo / React Native code: a design-token module, themed components, a hexagon clip-path helper, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `react-native-svg`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts — Bumble brand palette (yellow, black, hexagons)
export const colors = {
  // Brand
  yellow:        '#FFC629',  // dominant brand color
  honeyDeep:     '#F5B616',  // pressed yellow
  yellowLight:   '#FFE9A1',  // soft chip backgrounds, premium accents

  // Mode colors (Date/BFF/Bizz)
  bffTeal:       '#11AAA8',
  bizzOrange:    '#FF8000',

  // Canvas & surfaces
  canvas:        '#FFFFFF',
  bffCream:      '#FFFCF2',
  surface1:      '#F5F5F5',
  surface2:      '#EDEDED',
  divider:       '#E5E5E5',

  // Text (warm-tinted)
  black:         '#1F1F1F',  // primary text (warm Bumble Black)
  slate:         '#5A5A5A',  // secondary
  mist:          '#9C9C9C',  // tertiary
  pureBlack:     '#000000',  // text on yellow — WCAG AA requires this

  // Semantic
  matchPink:     '#E94B7B',  // the "It's a Match" heart only
  verified:      '#0066FF',
  error:         '#D72638',
  success:       '#00A86B',
  warning:       '#FF9500',

  // Dark mode
  darkCanvas:    '#0F0F0F',
  darkSurface:   '#1A1A1A',
  darkSurface2:  '#2A2A2A',
  darkDivider:   '#2F2F2F',
  darkText:      '#F2F2F2',
  darkTextSec:   '#9C9C9C',
  honeyDeepDark: '#FFD45C',  // OLED-brightened pressed state

  // Shadow tokens
  shadowSoft:    'rgba(0, 0, 0, 0.08)',
  shadowMed:     'rgba(0, 0, 0, 0.12)',
  shadowLg:      'rgba(0, 0, 0, 0.16)',
  yellowGlow:    'rgba(255, 198, 41, 0.5)',
  yellowGlowSm:  'rgba(255, 198, 41, 0.4)',
} as const;

export type BumbleColor = keyof typeof colors;
```

## 2. Typography

Brando is proprietary. Bundle via `expo-font`; falls back to `System` (SF Pro on iOS).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Brando-Medium':   require('../assets/fonts/Brando-Medium.ttf'),
    'Brando-Bold':     require('../assets/fonts/Brando-Bold.ttf'),
    'Brando-Black':    require('../assets/fonts/Brando-Black.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';
import { colors } from './colors';

export const typography = {
  matchHero:    { color: colors.black,     fontFamily: 'Brando-Black',  fontSize: 44, lineHeight: 46, letterSpacing: -0.8 },
  display:      { color: colors.black,     fontFamily: 'Brando-Black',  fontSize: 32, lineHeight: 35, letterSpacing: -0.6 },
  screenTitle:  { color: colors.black,     fontFamily: 'Brando-Black',  fontSize: 24, lineHeight: 28, letterSpacing: -0.4 },
  cardName:     { color: '#FFFFFF',        fontFamily: 'Brando-Bold',   fontSize: 28, lineHeight: 31, letterSpacing: -0.4 },
  section:      { color: colors.black,     fontFamily: 'Brando-Bold',   fontSize: 18, lineHeight: 22, letterSpacing: -0.1 },

  body:         { color: colors.black,     fontFamily: 'Brando-Medium', fontSize: 16, lineHeight: 22 },
  bodyBold:     { color: colors.black,     fontFamily: 'Brando-Bold',   fontSize: 16, lineHeight: 22 },
  bodySmall:    { color: colors.slate,     fontFamily: 'Brando-Medium', fontSize: 14, lineHeight: 19 },

  button:       { color: colors.pureBlack, fontFamily: 'Brando-Bold',   fontSize: 16, lineHeight: 20 },
  buttonLarge:  { color: colors.black,     fontFamily: 'Brando-Bold',   fontSize: 18, lineHeight: 22 },
  tab:          { color: colors.mist,      fontFamily: 'Brando-Bold',   fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  chip:         { color: colors.black,     fontFamily: 'Brando-Medium', fontSize: 13, lineHeight: 17 },
  meta:         { color: colors.slate,     fontFamily: 'Brando-Medium', fontSize: 13, lineHeight: 17 },
  counter:      { color: colors.pureBlack, fontFamily: 'Brando-Bold',   fontSize: 11, lineHeight: 13, letterSpacing: 0.2 },
  compliment:   { color: colors.black,     fontFamily: 'Brando-Black',  fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
} satisfies Record<string, TextStyle>;
```

## 3. Hexagon Helper

Use `react-native-svg` to render the brand-signature hexagon shape.

```tsx
// components/Hexagon.tsx
import Svg, { Path } from 'react-native-svg';

type Props = {
  size?: number;
  fill?: string;
  stroke?: string;
  strokeWidth?: number;
  children?: React.ReactNode;
};

// Regular point-up hexagon path
export function HexagonPath({ size = 120 }: { size?: number }) {
  const c = size / 2;
  const r = size / 2;
  const points = Array.from({ length: 6 }).map((_, i) => {
    const angle = (Math.PI / 3) * i - Math.PI / 2;
    return [c + r * Math.cos(angle), c + r * Math.sin(angle)] as const;
  });
  const d = `M ${points[0][0]} ${points[0][1]} ${points
    .slice(1)
    .map(([x, y]) => `L ${x} ${y}`)
    .join(' ')} Z`;
  return d;
}

export function HexagonOutline({ size = 120, fill = 'transparent', stroke = '#FFC629', strokeWidth = 4 }: Props) {
  const d = HexagonPath({ size });
  return (
    <Svg width={size} height={size}>
      <Path d={d} fill={fill} stroke={stroke} strokeWidth={strokeWidth} />
    </Svg>
  );
}
```

For hex-clipping an Image, wrap it in an SVG with a `clipPath`:

```tsx
import Svg, { Defs, ClipPath, Path, Image as SvgImage } from 'react-native-svg';

export function HexAvatar({ uri, size = 120, border = '#FFFFFF' }: { uri: string; size?: number; border?: string }) {
  const d = HexagonPath({ size });
  const id = `hex-${size}`;
  return (
    <Svg width={size} height={size}>
      <Defs>
        <ClipPath id={id}><Path d={d} /></ClipPath>
      </Defs>
      <SvgImage href={{ uri }} width={size} height={size} clipPath={`url(#${id})`} preserveAspectRatio="xMidYMid slice" />
      <Path d={d} fill="none" stroke={border} strokeWidth={4} />
    </Svg>
  );
}
```

## 4. Signature Components

### Primary Yellow CTA

```tsx
// components/BumblePrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BumblePrimaryButton({
  label, onPress, hasGlow = false,
}: { label: string; onPress: () => void; hasGlow?: boolean }) {
  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
      style={({ pressed }) => ({
        height: 56, borderRadius: 28,
        backgroundColor: pressed ? colors.honeyDeep : colors.yellow,
        alignItems: 'center', justifyContent: 'center',
        shadowColor: hasGlow ? colors.yellow : 'transparent',
        shadowOpacity: hasGlow ? 0.4 : 0,
        shadowRadius: 24,
        shadowOffset: { width: 0, height: 8 },
        elevation: hasGlow ? 6 : 0,
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Swipe Action Row

```tsx
// components/SwipeActionRow.tsx
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function SwipeActionRow({
  onRewind, onPass, onLike, onSuper, onCompliment,
}: { onRewind: () => void; onPass: () => void; onLike: () => void; onSuper: () => void; onCompliment: () => void }) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 20, paddingVertical: 24 }}>
      <ActionCircle size={48} bg={colors.surface1} onPress={onRewind}>
        <Ionicons name="arrow-undo" size={20} color={colors.mist} />
      </ActionCircle>

      <ActionCircle size={56} bg={colors.canvas} stroke={colors.black} onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        onPass();
      }}>
        <Ionicons name="close" size={24} color={colors.black} />
      </ActionCircle>

      <Pressable
        onPress={() => {
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
          onLike();
        }}
        style={({ pressed }) => ({
          width: 64, height: 64, borderRadius: 32, backgroundColor: colors.yellow,
          alignItems: 'center', justifyContent: 'center',
          shadowColor: colors.yellow, shadowOpacity: 0.5, shadowRadius: 16, shadowOffset: { width: 0, height: 6 },
          elevation: 8,
          transform: [{ scale: pressed ? 0.9 : 1 }],
        })}
      >
        <Ionicons name="heart" size={30} color={colors.pureBlack} />
      </Pressable>

      <ActionCircle size={56} bg={colors.yellow} onPress={() => {
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
        onSuper();
      }}>
        <Ionicons name="star" size={24} color={colors.pureBlack} />
      </ActionCircle>

      <ActionCircle size={56} bg={colors.canvas} stroke={colors.yellow} onPress={onCompliment}>
        <Ionicons name="flower" size={22} color={colors.yellow} />
      </ActionCircle>
    </View>
  );
}

function ActionCircle({
  size, bg, stroke, onPress, children,
}: { size: number; bg: string; stroke?: string; onPress: () => void; children: React.ReactNode }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        width: size, height: size, borderRadius: size / 2, backgroundColor: bg,
        alignItems: 'center', justifyContent: 'center',
        borderWidth: stroke ? 1.5 : 0,
        borderColor: stroke,
        transform: [{ scale: pressed ? 0.9 : 1 }],
      })}
    >
      {children}
    </Pressable>
  );
}
```

### Swipe Card (the hero)

```tsx
// components/SwipeCard.tsx
import { useState } from 'react';
import { View, Text, Image, Pressable, Dimensions } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const W = Dimensions.get('window').width - 48;
const H = (W * 4) / 3;

type Props = {
  photos: string[];
  name: string;
  age: number;
  bio: string;
  verified?: boolean;
};

export function SwipeCard({ photos, name, age, bio, verified }: Props) {
  const [idx, setIdx] = useState(0);

  return (
    <View
      style={{
        width: W, height: H, borderRadius: 12, overflow: 'hidden',
        shadowColor: '#000', shadowOpacity: 0.12, shadowRadius: 16, shadowOffset: { width: 0, height: 4 },
        elevation: 6,
      }}
    >
      <Image source={{ uri: photos[idx] }} style={{ position: 'absolute', inset: 0 }} />

      {/* Tap zones to advance photos */}
      <Pressable
        onPress={() => setIdx(Math.max(0, idx - 1))}
        style={{ position: 'absolute', top: 0, bottom: 0, left: 0, width: '33%' }}
      />
      <Pressable
        onPress={() => setIdx(Math.min(photos.length - 1, idx + 1))}
        style={{ position: 'absolute', top: 0, bottom: 0, right: 0, width: '33%' }}
      />

      {/* Progress bar at top */}
      <View style={{ position: 'absolute', top: 12, left: 12, right: 12, flexDirection: 'row', gap: 4 }}>
        {photos.map((_, i) => (
          <View
            key={i}
            style={{
              flex: 1, height: 3, borderRadius: 1.5,
              backgroundColor: i === idx ? '#FFFFFF' : 'rgba(255,255,255,0.4)',
            }}
          />
        ))}
      </View>

      {/* Bottom dark gradient + text overlay */}
      <LinearGradient
        colors={['rgba(0,0,0,0)', 'rgba(0,0,0,0.7)']}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: 220, padding: 20, justifyContent: 'flex-end' }}
      >
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
          <Text style={typography.cardName}>{name}, {age}</Text>
          {verified && <Ionicons name="checkmark-circle" size={20} color={colors.verified} />}
        </View>
        <Text style={[typography.body, { color: '#FFFFFF', marginTop: 4 }]} numberOfLines={1}>{bio}</Text>
      </LinearGradient>
    </View>
  );
}
```

### 24-Hour Countdown Chip

```tsx
// components/CountdownChip.tsx
import { useEffect } from 'react';
import { Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, withSequence } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CountdownChip({ remainingSeconds }: { remainingSeconds: number }) {
  const opacity = useSharedValue(1);
  useEffect(() => {
    opacity.value = withRepeat(
      withSequence(withTiming(0.95, { duration: 750 }), withTiming(1, { duration: 750 })),
      -1, true,
    );
  }, []);
  const style = useAnimatedStyle(() => ({ opacity: opacity.value }));

  const expired = remainingSeconds <= 0;
  const h = Math.floor(Math.max(remainingSeconds, 0) / 3600);
  const m = Math.floor((Math.max(remainingSeconds, 0) % 3600) / 60);
  const label = expired ? "Time's up — extend?" : `Your turn: ${h}h ${m}m`;

  return (
    <Animated.View
      style={[
        {
          alignSelf: 'stretch', marginHorizontal: 16,
          height: 32, borderRadius: 16,
          backgroundColor: expired ? colors.error : colors.yellow,
          borderWidth: 1, borderColor: expired ? colors.error : colors.honeyDeep,
          alignItems: 'center', justifyContent: 'center',
        },
        style,
      ]}
    >
      <Text style={[typography.meta, { color: colors.pureBlack, fontFamily: 'Brando-Bold' }]}>{label}</Text>
    </Animated.View>
  );
}
```

### Match Celebration

```tsx
// components/MatchCelebration.tsx
import { useEffect } from 'react';
import { View, Text, Pressable, Dimensions } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { HexAvatar } from './Hexagon';
import { BumblePrimaryButton } from './BumblePrimaryButton';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const { width: W } = Dimensions.get('window');

export function MatchCelebration({
  myAvatar, theirAvatar, theirName, onSendMessage, onKeepSwiping,
}: { myAvatar: string; theirAvatar: string; theirName: string; onSendMessage: () => void; onKeepSwiping: () => void }) {
  const heartScale = useSharedValue(0.2);

  useEffect(() => {
    heartScale.value = withSpring(1, { damping: 8 });
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    setTimeout(() => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium), 200);
  }, []);

  const heartStyle = useAnimatedStyle(() => ({ transform: [{ scale: heartScale.value }] }));

  return (
    <View style={{ flex: 1, backgroundColor: colors.yellow, padding: 24, justifyContent: 'space-between' }}>
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <View style={{ flexDirection: 'row', gap: 32, alignItems: 'center', justifyContent: 'center' }}>
          <HexAvatar uri={myAvatar} size={120} />
          <HexAvatar uri={theirAvatar} size={120} />
        </View>

        <Animated.View style={[
          { position: 'absolute', alignItems: 'center', justifyContent: 'center' },
          heartStyle,
        ]}>
          <Ionicons name="heart" size={56} color={colors.matchPink} />
        </Animated.View>

        <Text style={[typography.matchHero, { marginTop: 40 }]}>It's a Match!</Text>
        <Text style={[typography.body, { marginTop: 8 }]}>You and {theirName} want to chat</Text>
        <Text style={[typography.meta, { marginTop: 16, opacity: 0.8 }]}>
          She has 24 hours to make the first move
        </Text>
      </View>

      <View style={{ gap: 16, paddingBottom: 24 }}>
        <Pressable
          onPress={onSendMessage}
          style={{
            height: 56, borderRadius: 28, backgroundColor: colors.canvas,
            alignItems: 'center', justifyContent: 'center',
          }}
        >
          <Text style={typography.buttonLarge}>Send a Message</Text>
        </Pressable>
        <Pressable onPress={onKeepSwiping} style={{ alignItems: 'center', padding: 12 }}>
          <Text style={typography.button}>Keep Swiping</Text>
        </Pressable>
      </View>
    </View>
  );
}
```

### Chat Input

```tsx
// components/BumbleChatInput.tsx
import { useState } from 'react';
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BumbleChatInput({ onSend }: { onSend: (text: string) => void }) {
  const [text, setText] = useState('');
  const canSend = text.trim().length > 0;

  return (
    <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 10, paddingHorizontal: 16, paddingBottom: 24 }}>
      <TextInput
        value={text}
        onChangeText={setText}
        placeholder="Type your message…"
        placeholderTextColor={colors.mist}
        multiline
        style={{
          ...typography.body,
          flex: 1, minHeight: 48, maxHeight: 120,
          backgroundColor: colors.surface1,
          borderRadius: 24, paddingVertical: 14, paddingHorizontal: 20,
        }}
      />
      <Pressable
        onPress={() => {
          if (!canSend) return;
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
          onSend(text);
          setText('');
        }}
        style={{
          width: 40, height: 40, borderRadius: 20,
          backgroundColor: canSend ? colors.yellow : colors.surface1,
          alignItems: 'center', justifyContent: 'center',
        }}
      >
        <Ionicons name="arrow-up" size={18} color={canSend ? colors.pureBlack : colors.mist} />
      </Pressable>
    </View>
  );
}
```

## 5. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { View } from 'react-native';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={({ route }) => ({
        tabBarActiveTintColor:   colors.black,
        tabBarInactiveTintColor: colors.mist,
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider, height: 56 },
        tabBarLabelStyle: { fontFamily: 'Brando-Bold', fontSize: 10, letterSpacing: 0.2 },
        tabBarIcon: ({ focused, color }) => {
          const name = ({
            index:    focused ? 'people' : 'people-outline',
            hives:    focused ? 'apps' : 'apps-outline',
            matches:  focused ? 'heart' : 'heart-outline',
            chats:    focused ? 'chatbubble' : 'chatbubble-outline',
            profile:  focused ? 'person-circle' : 'person-circle-outline',
          } as const)[route.name as 'index'] ?? 'circle';
          return (
            <View style={{ alignItems: 'center' }}>
              <Ionicons name={name as any} size={24} color={color} />
              {focused && (
                <View style={{ width: 4, height: 4, borderRadius: 2, backgroundColor: colors.yellow, marginTop: 2 }} />
              )}
            </View>
          );
        },
      })}
    >
      <Tabs.Screen name="index"   options={{ title: 'People' }} />
      <Tabs.Screen name="hives"   options={{ title: 'Hives' }} />
      <Tabs.Screen name="matches" options={{ title: 'Matches' }} />
      <Tabs.Screen name="chats"   options={{ title: 'Chats' }} />
      <Tabs.Screen name="profile" options={{ title: 'Profile' }} />
    </Tabs>
  );
}
```

## 6. Motion & Haptics

```tsx
// Heart action button (the most-pressed action)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
heartScale.value = withSequence(
  withSpring(1.2, { damping: 8 }),
  withSpring(1, { damping: 8 }),
);

// SuperSwipe (yellow star) — heavier moment + screen flash
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
flashOpacity.value = withSequence(
  withTiming(1, { duration: 200 }),
  withTiming(0, { duration: 200 }),
);

// Compliment (bee) — softer, premium
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Match celebration entry
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
setTimeout(() => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium), 200);

// Tab switch
Haptics.selectionAsync();

// Swipe commit rotation/translate
cardRotate.value = withTiming(8, { duration: 350 });
cardX.value      = withTiming(500, { duration: 350 });
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons, Feather, MaterialCommunityIcons. Bumble's signature glyphs:

| Purpose | Ionicons |
|---------|----------|
| Heart action (filled) | `heart` |
| X Pass | `close` |
| Star SuperSwipe | `star` |
| Rewind | `arrow-undo` |
| Bee Compliment | `flower` (proprietary bee replacement) |
| Send (chat) | `arrow-up` |
| Verified | `checkmark-circle` |
| Photo info | `information-circle-outline` |
| People tab | `people-outline` / `people` |
| Hives tab | `apps-outline` / `apps` (hex placeholder) |
| Matches tab | `heart-outline` / `heart` |
| Chats tab | `chatbubble-outline` / `chatbubble` |
| Profile tab | `person-circle-outline` / `person-circle` |
| Filters | `options-outline` |
| Back | `chevron-back` |
| Settings | `settings-outline` |

## 8. Platform Notes

- **iOS-first surfaces**: Bumble doesn't use heavy blur — the tab bar and chrome are opaque white surfaces on light, opaque `#0F0F0F` on dark. Android renders identically.
- **Status bar**: Set `<StatusBar style="dark" />` on the white canvas; `style="light"` on the yellow match celebration takeover.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The chat input and bottom action row must respect the home indicator — use `useSafeAreaInsets().bottom`.
- **Dynamic Type**: React Native respects user font-scaling. Set `allowFontScaling={false}` on the 24-hour countdown chip, tab labels, badge counters, and the photo progress bar — everywhere else, allow scaling.
- **Pure black on yellow**: When setting `color: '#1F1F1F'` on `backgroundColor: '#FFC629'`, contrast fails WCAG AA at small sizes. Always use `colors.pureBlack` (`#000000`) for text on yellow surfaces — the `typography.button` and `typography.counter` tokens already enforce this.
- **Hexagon performance**: For the match celebration with two hex avatars, `react-native-svg`'s `clipPath` is hardware-accelerated on iOS. For long lists with many hex thumbnails (Hives screen, Beeline grid), consider pre-rendering a static hex mask image and using `MaskedView` for performance.
- **Accessibility**: Add `accessibilityRole="button"` + descriptive `accessibilityLabel` on each swipe action ("Like", "Pass", "SuperSwipe", "Send Compliment", "Rewind"). The card itself should be an accessibility element with combined label and a set of accessibility actions so VoiceOver users can swipe without physical gestures.
- **Dark mode**: Use `useColorScheme()` to switch the token object — Bumble Yellow stays identical (`#FFC629`); only chrome and text invert.
- **Photo loading**: Use `expo-image` for swipe-card photos with `transition={250}` and `contentFit="cover"`. Preload the next 2 cards in the stack for instant swipe response.
