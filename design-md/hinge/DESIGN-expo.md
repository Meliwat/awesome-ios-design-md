# Hinge (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hinge's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `expo-linear-gradient`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts — Hinge brand palette (warm cream paper, warm near-black, one gold)
export const colors = {
  // Canvas & surfaces (cream paper)
  cream:         '#FDF8F2',  // canvas
  paper:         '#FAF6F0',  // cards, sheets
  sand:          '#F2EBE0',  // chips, comment input
  sand2:         '#E8DFD0',  // pressed states
  dividerBone:   '#E0D6C5',  // hairlines

  // Text (warm-tinted, never neutral grey)
  black:         '#1A1A1A',  // primary text, "H" mark, CTAs
  blackPressed:  '#0A0A0A',
  graphite:      '#4A4239',  // secondary text
  stone:         '#7A7268',  // tertiary text, placeholders
  bone:          '#B0A89C',  // disabled

  // Rose Gold — single accent, reserved for Standouts/Roses
  rose:          '#E8A04D',
  roseDeep:      '#C57E2E',
  roseLight:     '#F5D9A8',

  // Semantic
  matchGreen:    '#2D7A4B',
  warning:       '#D88B2E',
  error:         '#B33A2F',
  info:          '#5A6273',

  // Dark mode (warm dark — cream paper at night)
  darkCanvas:    '#16130E',
  darkSurface:   '#1E1A14',
  darkSurface2:  '#2A2520',
  darkDivider:   '#2F2A22',
  darkText:      '#EFE8DA',
  darkTextSec:   '#A89E8E',
  roseDark:      '#F0B05C',  // OLED-brightened

  // Warm-tinted shadow base (never blue-grey)
  shadowWarm:    'rgba(28, 20, 10, 0.06)',
  shadowWarmMd:  'rgba(28, 20, 10, 0.10)',
  shadowWarmLg:  'rgba(28, 20, 10, 0.14)',
} as const;

export type HingeColor = keyof typeof colors;
```

## 2. Typography

Sailec is proprietary. Bundle via `expo-font`; pair with Inter for chrome. Falls back to `System` (SF Pro on iOS — the warmest system substitute).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Sailec-Medium':   require('../assets/fonts/Sailec-Medium.ttf'),
    'Sailec-Bold':     require('../assets/fonts/Sailec-Bold.ttf'),
    'Sailec-Black':    require('../assets/fonts/Sailec-Black.ttf'),
    'Inter-Regular':   require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':    require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold':  require('../assets/fonts/Inter-SemiBold.ttf'),
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
  display:       { color: colors.black,    fontFamily: 'Sailec-Bold',     fontSize: 36, lineHeight: 40, letterSpacing: -0.6 },
  name:          { color: colors.black,    fontFamily: 'Sailec-Bold',     fontSize: 28, lineHeight: 32, letterSpacing: -0.4 },
  promptQ:       { color: colors.graphite, fontFamily: 'Sailec-Medium',   fontSize: 22, lineHeight: 28, letterSpacing: -0.2, fontStyle: 'italic' },
  promptA:       { color: colors.black,    fontFamily: 'Sailec-Bold',     fontSize: 24, lineHeight: 30, letterSpacing: -0.3 },
  section:       { color: colors.black,    fontFamily: 'Sailec-Bold',     fontSize: 18, lineHeight: 22, letterSpacing: -0.1 },

  body:          { color: colors.black,    fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 22 },
  bodyBold:      { color: colors.black,    fontFamily: 'Inter-SemiBold',  fontSize: 16, lineHeight: 22 },
  chip:          { color: colors.black,    fontFamily: 'Inter-Medium',    fontSize: 14, lineHeight: 18 },
  meta:          { color: colors.graphite, fontFamily: 'Inter-Regular',   fontSize: 13, lineHeight: 17 },
  caption:       { color: colors.stone,    fontFamily: 'Inter-Regular',   fontSize: 12, lineHeight: 16 },
  commentInput:  { color: colors.black,    fontFamily: 'Inter-Regular',   fontSize: 16, lineHeight: 22 },

  button:        { color: colors.paper,    fontFamily: 'Sailec-Bold',     fontSize: 16, lineHeight: 20 },
  tab:           { color: colors.stone,    fontFamily: 'Sailec-Medium',   fontSize: 10, lineHeight: 12, letterSpacing: 0.2 },
  matchBanner:   { color: colors.paper,    fontFamily: 'Sailec-Bold',     fontSize: 14, lineHeight: 18, letterSpacing: 0.2 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Heart Tap (the most-pressed button in the app)

```tsx
// components/HeartTap.tsx
import { Pressable } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring, withSequence } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function HeartTap({ isFilled, onPress }: { isFilled: boolean; onPress: () => void }) {
  const scale = useSharedValue(1);
  const style = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const handle = () => {
    scale.value = withSequence(withSpring(0.88, { damping: 8 }), withSpring(1, { damping: 8 }));
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    onPress();
  };

  return (
    <Pressable onPress={handle} hitSlop={8}>
      <Animated.View
        style={[
          {
            width: 44, height: 44, borderRadius: 22,
            backgroundColor: isFilled ? colors.black : colors.paper,
            borderWidth: 1, borderColor: colors.black,
            alignItems: 'center', justifyContent: 'center',
          },
          style,
        ]}
      >
        <Ionicons name="heart" size={18} color={isFilled ? colors.paper : colors.black} />
      </Animated.View>
    </Pressable>
  );
}
```

### Prompt Card (the hero component)

```tsx
// components/PromptCard.tsx
import { View, Text, Pressable } from 'react-native';
import { useState } from 'react';
import { HeartTap } from './HeartTap';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  question: string;
  answer: string;
  onComment: () => void;
};

export function PromptCard({ question, answer, onComment }: Props) {
  const [liked, setLiked] = useState(false);

  return (
    <Pressable onPress={onComment}>
      <View
        style={{
          backgroundColor: colors.paper,
          borderColor: colors.dividerBone, borderWidth: 0.5,
          borderRadius: 16, padding: 24, marginHorizontal: 16, marginBottom: 16,
          shadowColor: colors.shadowWarm, shadowOpacity: 1, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
          elevation: 1,
        }}
      >
        <Text style={[typography.promptQ, { marginBottom: 12 }]}>{question}</Text>
        <Text style={[typography.promptA, { marginBottom: 16 }]}>{answer}</Text>
        <View style={{ flexDirection: 'row', justifyContent: 'flex-end' }}>
          <HeartTap isFilled={liked} onPress={() => { setLiked(!liked); onComment(); }} />
        </View>
      </View>
    </Pressable>
  );
}
```

### Photo Card (4:5 portrait)

```tsx
// components/PhotoCard.tsx
import { useState } from 'react';
import { View, Image, Dimensions } from 'react-native';
import { HeartTap } from './HeartTap';
import { colors } from '../theme/colors';

const W = Dimensions.get('window').width - 32;
const H = (W * 5) / 4; // 4:5 portrait

export function PhotoCard({ uri, onComment }: { uri: string; onComment: () => void }) {
  const [liked, setLiked] = useState(false);

  return (
    <View
      style={{
        marginHorizontal: 16, marginBottom: 16,
        shadowColor: colors.shadowWarm, shadowOpacity: 1, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
        elevation: 1,
      }}
    >
      <Image
        source={{ uri }}
        style={{ width: W, height: H, borderRadius: 16 }}
      />
      <View style={{ position: 'absolute', bottom: 16, right: 16 }}>
        <HeartTap isFilled={liked} onPress={() => { setLiked(!liked); onComment(); }} />
      </View>
    </View>
  );
}
```

### Attribute Chip

```tsx
// components/AttributeChip.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  glyph: keyof typeof Ionicons.glyphMap;
  label: string;
  verified?: boolean;
};

export function AttributeChip({ glyph, label, verified }: Props) {
  return (
    <View
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 6,
        backgroundColor: colors.sand,
        borderRadius: 500, paddingVertical: 8, paddingHorizontal: 14,
      }}
    >
      <Ionicons name={glyph} size={13} color={colors.black} />
      <Text style={typography.chip}>{label}</Text>
      {verified && <Ionicons name="checkmark-circle" size={12} color={colors.matchGreen} />}
    </View>
  );
}
```

### Standouts Card

```tsx
// components/StandoutsCard.tsx
import { View, Text, Image, Dimensions, Pressable } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const W = Dimensions.get('window').width - 32;

export function StandoutsCard({
  photoUri, answer, onSendRose,
}: { photoUri: string; answer: string; onSendRose: () => void }) {
  return (
    <View
      style={{
        marginHorizontal: 16, marginBottom: 24,
        borderRadius: 20, overflow: 'hidden',
        backgroundColor: colors.paper,
        shadowColor: colors.shadowWarmMd, shadowOpacity: 1, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
        elevation: 3,
      }}
    >
      <LinearGradient
        colors={[colors.rose, colors.roseLight, colors.rose]}
        start={{ x: 0, y: 0 }} end={{ x: 1, y: 1 }}
        style={{ position: 'absolute', inset: 0, borderRadius: 20, padding: 1 }}
      >
        <View style={{ flex: 1, borderRadius: 19, backgroundColor: colors.paper }} />
      </LinearGradient>

      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, height: 32, paddingHorizontal: 16, backgroundColor: colors.roseLight }}>
        <Ionicons name="star" size={14} color={colors.rose} />
        <Text style={{ ...typography.button, color: colors.black, fontSize: 12 }}>Standout</Text>
      </View>

      <Image source={{ uri: photoUri }} style={{ width: W, height: W }} />

      <View style={{ padding: 16, gap: 16 }}>
        <Text style={typography.promptA}>{answer}</Text>
        <Pressable
          onPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);
            onSendRose();
          }}
          style={({ pressed }) => ({
            flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 10,
            height: 56, borderRadius: 28,
            backgroundColor: pressed ? colors.blackPressed : colors.black,
            borderWidth: 1, borderColor: colors.rose,
          })}
        >
          <Ionicons name="leaf" size={16} color={colors.rose} />
          <Text style={typography.button}>Send a Rose</Text>
        </Pressable>
      </View>
    </View>
  );
}
```

### Primary CTA

```tsx
// components/HingePrimaryButton.tsx
import { Pressable, Text } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function HingePrimaryButton({ label, onPress }: { label: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
      style={({ pressed }) => ({
        height: 56, borderRadius: 28, backgroundColor: pressed ? colors.blackPressed : colors.black,
        alignItems: 'center', justifyContent: 'center',
        transform: [{ scale: pressed ? 0.97 : 1 }],
      })}
    >
      <Text style={typography.button}>{label}</Text>
    </Pressable>
  );
}
```

### Comment Sheet

```tsx
// components/CommentSheet.tsx
import { useState } from 'react';
import { View, Text, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CommentSheet({
  Source, onSend,
}: { Source: React.ComponentType; onSend: (comment: string) => void }) {
  const [comment, setComment] = useState('');
  const canSend = comment.trim().length > 0;

  return (
    <View
      style={{
        backgroundColor: colors.paper,
        borderTopLeftRadius: 24, borderTopRightRadius: 24,
        paddingTop: 24, paddingBottom: 24,
      }}
    >
      <View style={{ paddingHorizontal: 16, marginBottom: 16 }}>
        <Source />
      </View>

      <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 10, paddingHorizontal: 16 }}>
        <TextInput
          value={comment}
          onChangeText={setComment}
          placeholder="Add a comment about her response"
          placeholderTextColor={colors.stone}
          multiline
          style={{
            ...typography.commentInput,
            flex: 1, minHeight: 48, maxHeight: 120,
            backgroundColor: colors.sand,
            borderRadius: 24, paddingVertical: 14, paddingHorizontal: 20,
          }}
        />
        <Pressable
          onPress={() => {
            if (!canSend) return;
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
            onSend(comment);
          }}
          disabled={!canSend}
          style={{
            width: 44, height: 44, borderRadius: 22,
            backgroundColor: canSend ? colors.black : colors.bone,
            alignItems: 'center', justifyContent: 'center',
          }}
        >
          <Ionicons name="arrow-up" size={16} color={colors.paper} />
        </Pressable>
      </View>
    </View>
  );
}
```

### Match Celebration

```tsx
// components/MatchCelebration.tsx
import { useEffect } from 'react';
import { View, Text, Image, Pressable, Dimensions } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import { HingePrimaryButton } from './HingePrimaryButton';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const { width: W } = Dimensions.get('window');

export function MatchCelebration({
  myAvatar, theirAvatar, theirName, onMessage, onKeep,
}: { myAvatar: string; theirAvatar: string; theirName: string; onMessage: () => void; onKeep: () => void }) {
  useEffect(() => {
    Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
  }, []);

  return (
    <View style={{ flex: 1, backgroundColor: colors.cream, alignItems: 'center', justifyContent: 'center', padding: 24 }}>
      {Array.from({ length: 24 }).map((_, i) => (
        <Confetti key={i} index={i} />
      ))}

      <View style={{ flexDirection: 'row', gap: 24, marginBottom: 32 }}>
        <Image source={{ uri: myAvatar }}    style={{ width: 120, height: 120, borderRadius: 60 }} />
        <Image source={{ uri: theirAvatar }} style={{ width: 120, height: 120, borderRadius: 60 }} />
      </View>

      <Text style={typography.display}>It's a match!</Text>
      <Text style={[typography.body, { marginTop: 8, color: colors.graphite }]}>
        You and {theirName} liked each other
      </Text>

      <View style={{ width: '100%', marginTop: 32, gap: 12 }}>
        <HingePrimaryButton label="Send a message" onPress={onMessage} />
        <Pressable onPress={onKeep} style={{ alignItems: 'center', padding: 12 }}>
          <Text style={{ ...typography.chip, color: colors.graphite }}>Keep browsing</Text>
        </Pressable>
      </View>
    </View>
  );
}

function Confetti({ index }: { index: number }) {
  const y = useSharedValue(-300);
  const opacity = useSharedValue(1);

  useEffect(() => {
    y.value = withTiming(600, { duration: 1800 });
    opacity.value = withTiming(0, { duration: 1800 });
  }, []);

  const style = useAnimatedStyle(() => ({
    transform: [{ translateY: y.value }],
    opacity: opacity.value,
  }));

  const size = 4 + Math.random() * 6;
  const x = Math.random() * W - W / 2;

  return (
    <Animated.View
      style={[
        {
          position: 'absolute',
          left: '50%', top: 100,
          width: size, height: size, borderRadius: size / 2,
          backgroundColor: colors.rose,
          transform: [{ translateX: x }],
        },
        style,
      ]}
    />
  );
}
```

## 4. Tab Bar (expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { BlurView } from 'expo-blur';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.black,
        tabBarInactiveTintColor: colors.stone,
        tabBarStyle: { position: 'absolute', borderTopWidth: 0.5, borderTopColor: colors.dividerBone, backgroundColor: 'transparent' },
        tabBarBackground: () => (
          <BlurView intensity={80} tint="light" style={{ flex: 1, backgroundColor: 'rgba(253,248,242,0.92)' }} />
        ),
        tabBarLabelStyle: { fontFamily: 'Sailec-Medium', fontSize: 10, letterSpacing: 0.2 },
      }}
    >
      <Tabs.Screen name="index"      options={{ title: 'Discover',  tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'compass'  : 'compass-outline'}  size={24} color={color} /> }} />
      <Tabs.Screen name="likes-you"  options={{ title: 'Likes You', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'heart'    : 'heart-outline'}    size={24} color={color} /> }} />
      <Tabs.Screen name="standouts"  options={{ title: 'Standouts', tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'star'     : 'star-outline'}     size={24} color={color} /> }} />
      <Tabs.Screen name="matches"    options={{ title: 'Matches',   tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'chatbubble' : 'chatbubble-outline'} size={24} color={color} /> }} />
      <Tabs.Screen name="profile"    options={{ title: 'Profile',   tabBarIcon: ({ color, focused }) => <Ionicons name={focused ? 'person' : 'person-outline'} size={24} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// Heart tap (the most-repeated motion)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
scale.value = withSequence(withSpring(0.88, { damping: 8 }), withSpring(1, { damping: 8 }));

// Send Like CTA
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Rose send (premium / currency)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);

// Match celebration entry
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
// + soft secondary 200ms later as photos settle
setTimeout(() => Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft), 200);

// Tab switch
Haptics.selectionAsync();

// Comment sheet — shared-element via expo-router presentation
// router.push({ pathname: '/comment', params: { sourceId, presentation: 'transparentModal' } });
```

## 6. Icon Library

Use `@expo/vector-icons` — ships with Ionicons, Feather, MaterialCommunityIcons. Map to Hinge's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Heart-tap (filled / outline) | `heart` / `heart-outline` |
| X decline | `close` |
| Send arrow (comment) | `arrow-up` |
| Standouts star | `star` / `star-outline` |
| Rose currency | `leaf` (proprietary glyph replacement) |
| Verified | `checkmark-circle` |
| Attribute — height | `resize-outline` |
| Attribute — job | `briefcase-outline` |
| Attribute — location | `location-outline` |
| Attribute — education | `school-outline` |
| Attribute — religion | `book-outline` |
| Discover tab | `compass-outline` / `compass` |
| Likes You tab | `heart-outline` / `heart` |
| Standouts tab | `star-outline` / `star` |
| Matches tab | `chatbubble-outline` / `chatbubble` |
| Profile tab | `person-outline` / `person` |
| Back | `chevron-back` |
| Settings | `settings-outline` |

## 7. Platform Notes

- **iOS-first blur**: Use `expo-blur` `tint="light"` at intensity 80 for the tab bar and any sticky bottom action bar. Android falls back to an opaque `rgba(253,248,242,0.96)` background to preserve the cream feel.
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` on light/cream-canvas screens.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The bottom action bar on Likes You must respect the home indicator — use `useSafeAreaInsets().bottom`.
- **Dynamic Type**: React Native respects user font-scaling by default. Set `allowFontScaling={false}` only on tab labels, attribute chip glyphs, and the "H" mark — everywhere else, scale generously (Hinge's profile content is the brand).
- **Accessibility**: Add `accessibilityRole="button"` + descriptive `accessibilityLabel` on the heart-tap (`"Like, button"` / `"Liked, button"`); group prompt cards with combined accessibility labels announcing question + answer + double-tap-to-comment hint.
- **Confetti performance**: For the match celebration, prefer `react-native-reanimated` worklets over JS-thread setInterval. On older devices, drop the particle count from 24 to 12.
- **Dark mode**: Use `useColorScheme()` to switch the token object between light and dark palettes — Rose Gold brightens to `#F0B05C` on dark canvas for OLED contrast.
- **Photo loading**: Use `expo-image` for fade-in transitions on prompt-card photos — the cream-to-photo reveal should be smooth, never flashy. Set `transition={400}` and `contentFit="cover"`.
