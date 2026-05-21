# The League (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates The League's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3. No `expo-linear-gradient` or `expo-blur` needed — The League uses flat black surfaces and hairline borders, never gradients or blur.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & Surfaces (dark-native — the only theme)
  canvas:   '#0A0A0A',
  surface1: '#141414',
  surface2: '#1C1A17',
  cardBase: '#100F0C',
  hairline: '#2A2620', // the 1px gold-tinted border that does ALL depth work

  // Text
  textPrimary:   '#EDE9E2',
  textSecondary: '#9E988C',
  textTertiary:  '#6B665C',
  onGold:        '#1A1408',

  // Accent — the SINGLE accent is gold
  gold:       '#C8A35A',
  goldBright: '#DBBA71',
  goldDeep:   '#A8863F',
  champagne:  '#E8DCC0',

  // Semantic
  success: '#6FAE8A',
  error:   '#C77A7A',
} as const;

export type LeagueColor = keyof typeof colors;
```

## 2. Typography

Load Cormorant Garamond (display serif) + Jost (UI sans) via `expo-font` — both SIL OFL. Serif = the person & institution; sans = the machine & metadata.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Cormorant-Regular':      require('../assets/fonts/CormorantGaramond-Regular.ttf'),
    'Cormorant-Medium':       require('../assets/fonts/CormorantGaramond-Medium.ttf'),
    'Cormorant-SemiBold':     require('../assets/fonts/CormorantGaramond-SemiBold.ttf'),
    'Cormorant-Bold':         require('../assets/fonts/CormorantGaramond-Bold.ttf'),
    'Cormorant-MediumItalic': require('../assets/fonts/CormorantGaramond-MediumItalic.ttf'),
    'Jost-Regular':  require('../assets/fonts/Jost-Regular.ttf'),
    'Jost-Medium':   require('../assets/fonts/Jost-Medium.ttf'),
    'Jost-SemiBold': require('../assets/fonts/Jost-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  // Serif — names, wordmark, titles, concierge
  wordmark:    { color: '#C8A35A', fontFamily: 'Cormorant-Bold',     fontSize: 34, letterSpacing: 0.2 },
  name:        { color: '#EDE9E2', fontFamily: 'Cormorant-Bold',     fontSize: 26, letterSpacing: 0.2 },
  ageInline:   { color: '#9E988C', fontFamily: 'Cormorant-Regular',  fontSize: 26 },
  screenTitle: { color: '#EDE9E2', fontFamily: 'Cormorant-SemiBold', fontSize: 21, letterSpacing: 0.2 },
  concierge:   { color: '#EDE9E2', fontFamily: 'Cormorant-MediumItalic', fontSize: 16, lineHeight: 26 },
  stat:        { color: '#C8A35A', fontFamily: 'Cormorant-SemiBold', fontSize: 21 },
  // Sans — credentials, body, eyebrows, buttons, tabs
  subtitle:    { color: '#EDE9E2', fontFamily: 'Jost-Medium',   fontSize: 16, letterSpacing: 0.3 },
  body:        { color: '#EDE9E2', fontFamily: 'Jost-Regular',  fontSize: 15, lineHeight: 24 },
  credential:  { color: '#9E988C', fontFamily: 'Jost-Regular',  fontSize: 13, lineHeight: 22 },
  eyebrow:     { color: '#C8A35A', fontFamily: 'Jost-SemiBold', fontSize: 11, letterSpacing: 2, textTransform: 'uppercase' as const },
  button:      { color: '#1A1408', fontFamily: 'Jost-SemiBold', fontSize: 13, letterSpacing: 1.6, textTransform: 'uppercase' as const },
  caption:     { color: '#9E988C', fontFamily: 'Jost-Regular',  fontSize: 12 },
  tab:         { fontFamily: 'Jost-Medium', fontSize: 9, letterSpacing: 1, textTransform: 'uppercase' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Prospect Card (the embossed calling card)

```tsx
// components/ProspectCard.tsx
import { Image, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function Credential({ children }: { children: string }) {
  return (
    <View style={{ flexDirection: 'row', gap: 6, paddingVertical: 2 }}>
      <Text style={{ color: colors.gold, fontFamily: 'Jost-Regular', fontSize: 13 }}>◦</Text>
      <Text style={[typography.credential, { flex: 1 }]}>{children}</Text>
    </View>
  );
}

export function ProspectCard({
  uri, name, age, title, school, location, verified,
}: {
  uri: string; name: string; age: number; title: string;
  school: string; location: string; verified?: boolean;
}) {
  return (
    <View style={{
      backgroundColor: colors.cardBase, borderRadius: 6,
      borderWidth: 1, borderColor: colors.hairline, overflow: 'hidden',
      // NO shadow — depth is the hairline
    }}>
      <View style={{ height: 280 }}>
        <Image source={{ uri }} style={{ width: '100%', height: '100%' }} resizeMode="cover" />
        {verified && (
          <View style={{
            position: 'absolute', top: 14, right: 14,
            width: 30, height: 30, borderRadius: 15,
            borderWidth: 1, borderColor: colors.gold,
            backgroundColor: 'rgba(10,10,10,0.6)',
            alignItems: 'center', justifyContent: 'center',
          }}>
            <Ionicons name="star" size={15} color={colors.gold} />
          </View>
        )}
      </View>
      <View style={{ paddingHorizontal: 18, paddingVertical: 14 }}>
        <View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 6 }}>
          <Text style={typography.name}>{name}</Text>
          <Text style={typography.ageInline}>{age}</Text>
        </View>
        <View style={{ width: 28, height: 1, backgroundColor: colors.gold, marginVertical: 8 }} />
        <Credential>{title}</Credential>
        <Credential>{school}</Credential>
        <Credential>{location}</Credential>
      </View>
    </View>
  );
}
```

### Card Action Row

```tsx
// components/CardActions.tsx
import { Pressable, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

function CircleAction({ icon, size, tint, border, onPress }: {
  icon: any; size: number; tint: string; border: string; onPress: () => void;
}) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={{
        width: size, height: size, borderRadius: size / 2,
        backgroundColor: colors.surface1,
        borderWidth: 1, borderColor: border,
        alignItems: 'center', justifyContent: 'center',
      }}
    >
      <Ionicons name={icon} size={size * 0.4} color={tint} />
    </Pressable>
  );
}

export function CardActions({ onPass, onHeart, onMessage }: Record<string, () => void>) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 18 }}>
      <CircleAction icon="close" size={48} tint={colors.textSecondary} border={colors.hairline} onPress={onPass} />
      {/* Heart is the focal action — gold OUTLINE, not a fill */}
      <CircleAction icon="heart-outline" size={56} tint={colors.gold} border={colors.gold} onPress={onHeart} />
      <CircleAction icon="chatbubble-outline" size={48} tint={colors.gold} border={colors.hairline} onPress={onMessage} />
    </View>
  );
}
```

### Concierge Note

```tsx
// components/ConciergeNote.tsx
import { useEffect } from 'react';
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withDelay } from 'react-native-reanimated';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ConciergeNote({ message, onIntroduce }: { message: string; onIntroduce?: () => void }) {
  const ruleScale = useSharedValue(0);
  const textOpacity = useSharedValue(0);
  const ruleStyle = useAnimatedStyle(() => ({ transform: [{ scaleY: ruleScale.value }] }));
  const textStyle = useAnimatedStyle(() => ({ opacity: textOpacity.value }));

  useEffect(() => {
    ruleScale.value = withTiming(1, { duration: 200 });
    textOpacity.value = withDelay(200, withTiming(1, { duration: 180 }));
  }, []);

  return (
    <View style={{
      flexDirection: 'row', backgroundColor: colors.surface2,
      borderRadius: 4, borderWidth: 1, borderColor: colors.hairline, overflow: 'hidden',
    }}>
      <Animated.View style={[{ width: 2, backgroundColor: colors.gold }, ruleStyle]} />
      <Animated.View style={[{ flex: 1, padding: 18 }, textStyle]}>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8, marginBottom: 8 }}>
          <Ionicons name="star" size={12} color={colors.gold} />
          <Text style={typography.eyebrow}>Your Concierge</Text>
        </View>
        <Text style={typography.concierge}>{message}</Text>
        {onIntroduce && (
          <Pressable onPress={onIntroduce} style={{ marginTop: 12 }}>
            <Text style={[typography.eyebrow, { letterSpacing: 1.2 }]}>Make the introduction →</Text>
          </Pressable>
        )}
      </Animated.View>
    </View>
  );
}
```

### Batch Banner

```tsx
// components/BatchBanner.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BatchBanner({ count }: { count: number }) {
  return (
    <View style={{
      flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      paddingHorizontal: 14, paddingVertical: 10,
      backgroundColor: colors.surface1,
      borderRadius: 4, borderWidth: 1, borderColor: colors.hairline,
    }}>
      <Text style={[typography.eyebrow, { color: colors.textSecondary, letterSpacing: 1.5 }]}>
        Today's Batch
      </Text>
      <Text style={typography.stat}>{count} Prospects</Text>
    </View>
  );
}
```

### Buttons

```tsx
// components/LeagueButton.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function LeaguePrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.goldDeep : colors.gold,
        borderRadius: 3, paddingVertical: 15, alignItems: 'center',
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function LeagueOutlineButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{ borderRadius: 3, paddingVertical: 14, alignItems: 'center', borderWidth: 1, borderColor: colors.gold }}
    >
      <Text style={[typography.button, { color: colors.gold }]}>{title}</Text>
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
        tabBarActiveTintColor: colors.gold,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: 'rgba(10,10,10,0.96)', // opaque black — no blur
          borderTopWidth: 1,
          borderTopColor: colors.hairline, // 1px hairline, not a shadow
        },
        tabBarLabelStyle: { fontFamily: 'Jost-Medium', fontSize: 9, letterSpacing: 1, textTransform: 'uppercase' },
      }}
    >
      <Tabs.Screen name="index"     options={{ title: 'Prospects', tabBarIcon: ({ color }) => <Ionicons name="albums-outline"   size={21} color={color} /> }} />
      <Tabs.Screen name="matches"   options={{ title: 'Matches',   tabBarIcon: ({ color }) => <Ionicons name="heart-outline"    size={21} color={color} /> }} />
      <Tabs.Screen name="concierge" options={{ title: 'Concierge', tabBarIcon: ({ color }) => <Ionicons name="chatbubble-outline" size={21} color={color} /> }} />
      <Tabs.Screen name="profile"   options={{ title: 'Profile',   tabBarIcon: ({ color }) => <Ionicons name="person-outline"   size={21} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
import Animated, { useSharedValue, useAnimatedStyle, withTiming, withSequence, withDelay } from 'react-native-reanimated';

// Card advance — restrained fade + drift up (never a thrown swipe)
const opacity = useSharedValue(1);
const ty = useSharedValue(0);
function advance() {
  opacity.value = withTiming(0, { duration: 280 });
  ty.value = withTiming(-12, { duration: 280 });
  // then load next prospect, reset, cross-fade in
}

// Heart send — outline "fills", gentle pulse
const heartScale = useSharedValue(1);
heartScale.value = withSequence(withTiming(1.12, { duration: 120 }), withTiming(1, { duration: 120 }));
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Concierge note arrival — see ConciergeNote (rule scaleY then text opacity)

// Batch count — fade-swap the number (RN has no odometer; cross-fade is the restrained analog)
// Wrap the count in an <Animated.Text> keyed by value with FadeIn/FadeOut

// Match celebration — wax-seal stroke draw via react-native-svg <Path> strokeDashoffset
// animate strokeDashoffset full → 0 over 600ms; NO confetti

// Tab switch — instant; tint cross-fades (expo-router default)
// Field focus — animate the bottom border color transparent → gold over 160ms

// Haptics — restraint extends to touch
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);                  // heart send
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);    // introduction accepted
Haptics.selectionAsync();                                               // tab change
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). The League's iconography is fine-line, never filled.

| Purpose | Ionicons |
|---------|----------|
| Prospects (tab) | `albums-outline` |
| Matches (tab) | `heart-outline` |
| Concierge (tab) | `chatbubble-outline` |
| Profile (tab) | `person-outline` |
| Heart (card action) | `heart-outline` |
| Pass (card action) | `close` |
| Message (card action) | `chatbubble-outline` |
| Verification seal | `star` |
| Concierge mark | `star` |
| Search | `search-outline` |
| Settings | `settings-outline` |
| Membership / premium | `ribbon-outline` |
| Back | `chevron-back` |
| Credential mark | text `◦` (not an icon) |

## 7. Platform Notes

- **Font choice**: Cormorant Garamond + Jost (both SIL OFL) — bundle the weights above; the serif/sans split is load-bearing, never collapse to one family
- **Dark-native**: The League has no light mode in spirit. Set `<StatusBar style="light" />` always; do not branch on `useColorScheme()` — render the dark aesthetic unconditionally
- **No gradients, no blur**: surfaces are flat black; depth is the 1px `#2A2620` hairline border — never reach for `expo-linear-gradient` or `expo-blur`
- **No shadows**: never set `shadowColor`/`elevation`; separation is the hairline border plus the tonal surface lift (`#141414` vs `#0A0A0A`)
- **Safe area**: wrap screens in `SafeAreaView`; the prospect card is a mounted object (not full-bleed) and stays inside insets; the opaque tab bar respects the home indicator
- **Dynamic Type**: set `allowFontScaling={false}` on eyebrows, tab labels, the batch count, and button labels (letter-spaced uppercase, layout-sensitive); let serif names / titles / body / credentials / concierge scale
- **Letter-spacing**: React Native `letterSpacing` is in px — the values above (2 for eyebrows, 1.6 for buttons, 1 for tabs) match the spec's tracked-uppercase look
- **SVG for the seal**: use `react-native-svg` for the wax-seal celebration stroke-draw (`strokeDasharray` + animated `strokeDashoffset`) — no confetti library
- **Accessibility**: the prospect card is one `accessible` element announcing name/age/credentials; card actions get `accessibilityLabel`s; the concierge note announces "Concierge: {message}"; ensure the gold hairline is never the sole carrier of meaning (pair with labels)
- **Performance**: the calling-card list (Matches/Concierge) is a `FlatList` of hairline-bordered rows — flat, no shadows, so it scrolls cheaply
