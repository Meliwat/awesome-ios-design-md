# Raya (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Raya's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-av` (slideshow video), and `react-native-reanimated` v3.

> Raya has, by design, **no brand color** and **no light mode**. The canvas is pure black `#000000`, the only accent is pure white `#FFFFFF`, depth is a 1px `#2E2E2E` hairline (never a shadow), and the only color anywhere is the member's full-bleed media. Keep all of that intact.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & Surfaces (permanently dark — the only theme)
  canvas:    '#000000', // pure black, never warmed
  surface1:  '#0D0D0D', // sheets, settings rows — the first faint lift
  surface2:  '#161616', // inset fields, pressed rows
  divider:   '#242424', // hairline dividers between settings rows
  hairline:  '#2E2E2E', // the 1px border — the ONLY depth cue

  // Text
  textPrimary:   '#FFFFFF',
  textSecondary: '#9A9A9A',
  textTertiary:  '#5E5E5E',

  // Accent (there is, by design, only one)
  accent:        '#FFFFFF',
  accentPressed: '#D8D8D8',

  // Semantic (used reluctantly)
  error: '#E5484D', // leave-Raya / report / payment-failed only

  // On-photo
  onPhoto:    'rgba(255,255,255,0.92)', // caption text over media
  onPhotoDim: 'rgba(255,255,255,0.60)', // location / least-emphasis over media
} as const;

export type RayaColor = keyof typeof colors;

// Bottom scrim under every slideshow caption
export const scrimStops = ['transparent', 'rgba(0,0,0,0.55)', 'rgba(0,0,0,0.92)'] as const;
export const scrimLocations = [0, 0.55, 1] as const;
```

There is no `darkColors` map — Raya is permanently dark. There is no accent palette — `accent` is white and that is the entire brand. Never introduce a hue; the member's media is the only color the product permits.

## 2. Typography

One clean low-contrast neo-grotesque does everything (the typographic mirror of the one-color rule). Load `Inter` (SIL OFL) via `expo-font` as the closest free substitute. No serif, no mono, no display face.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-SemiBold': require('../assets/fonts/Inter-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const white = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  wordmark:    { ...white, fontFamily: 'Inter-SemiBold', fontSize: 30, lineHeight: 30, letterSpacing: -0.5 },
  profileName: { ...white, fontFamily: 'Inter-SemiBold', fontSize: 28, lineHeight: 31, letterSpacing: -0.4 },
  section:     { ...white, fontFamily: 'Inter-SemiBold', fontSize: 20, lineHeight: 24, letterSpacing: -0.2 },
  subtitle:    { ...white, fontFamily: 'Inter-Medium',   fontSize: 16, lineHeight: 21 },
  body:        { ...white, fontFamily: 'Inter-Regular',  fontSize: 15, lineHeight: 23 },
  role:        { color: 'rgba(255,255,255,0.92)', fontFamily: 'Inter-Medium', fontSize: 13, lineHeight: 17, letterSpacing: 0.4, textTransform: 'uppercase' as const },
  music:       { color: 'rgba(255,255,255,0.85)', fontFamily: 'Inter-Medium', fontSize: 11, lineHeight: 11, letterSpacing: 0.2 },
  meta:        { color: '#9A9A9A', fontFamily: 'Inter-Regular', fontSize: 13, lineHeight: 17 },
  eyebrow:     { color: '#FFFFFF', fontFamily: 'Inter-SemiBold', fontSize: 11, lineHeight: 11, letterSpacing: 1.6, textTransform: 'uppercase' as const },
  button:      { color: '#000000', fontFamily: 'Inter-SemiBold', fontSize: 14, lineHeight: 14, letterSpacing: 0.3 },
  caption:     { color: '#5E5E5E', fontFamily: 'Inter-Regular', fontSize: 12, lineHeight: 16 },
  tab:         { color: '#5E5E5E', fontFamily: 'Inter-Medium', fontSize: 10, lineHeight: 10, letterSpacing: 0.4 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Slideshow Profile (the product)

Full-bleed photo/video, segmented story bars, the music ticker + equalizer, and an editorial caption over a black scrim.

```tsx
// components/SlideshowProfile.tsx
import { View, Text, Image, Pressable, useWindowDimensions } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors, scrimStops, scrimLocations } from '../theme/colors';
import { typography } from '../theme/typography';
import { Equalizer } from './Equalizer';

type Profile = {
  slides: string[]; activeIndex: number; progress: number; // 0..1 within active slide
  name: string; role: string; bio: string; location: string;
  artist: string; track: string;
};

export function SlideshowProfile(p: Profile & { onScrubPrev: () => void; onScrubNext: () => void }) {
  const { width } = useWindowDimensions();
  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas }}>
      <Image source={{ uri: p.slides[p.activeIndex] }} style={{ position: 'absolute', inset: 0 }} resizeMode="cover" />
      <LinearGradient
        colors={scrimStops as unknown as string[]}
        locations={scrimLocations as unknown as number[]}
        style={{ position: 'absolute', left: 0, right: 0, bottom: 0, height: '62%' }}
      />

      {/* Scrub zones — tap left/right thirds */}
      <Pressable onPress={p.onScrubPrev} style={{ position: 'absolute', left: 0, top: 0, bottom: 0, width: width / 3 }} />
      <Pressable onPress={p.onScrubNext} style={{ position: 'absolute', right: 0, top: 0, bottom: 0, width: width / 3 }} />

      {/* Story bars */}
      <View style={{ position: 'absolute', top: 56, left: 16, right: 16, flexDirection: 'row', gap: 5 }}>
        {p.slides.map((_, i) => (
          <View key={i} style={{ flex: 1, height: 2.5, borderRadius: 2, backgroundColor: 'rgba(255,255,255,0.3)' }}>
            <View style={{
              height: 2.5, borderRadius: 2, backgroundColor: '#FFFFFF',
              width: i < p.activeIndex ? '100%' : i === p.activeIndex ? `${p.progress * 100}%` : 0,
            }} />
          </View>
        ))}
      </View>

      {/* Music ticker */}
      <View style={{ position: 'absolute', top: 76, left: 16, right: 16, flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <Ionicons name="musical-notes" size={13} color="#FFFFFF" />
        <Text style={typography.music}>{p.artist} — {p.track}</Text>
        <View style={{ marginLeft: 'auto' }}><Equalizer /></View>
      </View>

      {/* Caption */}
      <View style={{ position: 'absolute', left: 20, right: 20, bottom: 96 }}>
        <Text style={typography.profileName}>{p.name}</Text>
        <Text style={[typography.role, { marginTop: 8 }]}>{p.role}</Text>
        <Text style={[typography.body, { color: 'rgba(255,255,255,0.72)', marginTop: 10 }]}>{p.bio}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6, marginTop: 10 }}>
          <Ionicons name="location-outline" size={12} color={colors.onPhotoDim} />
          <Text style={[typography.meta, { color: colors.onPhotoDim }]}>{p.location}</Text>
        </View>
      </View>
    </View>
  );
}
```

### Equalizer (animated 4-bar)

```tsx
// components/Equalizer.tsx
import { useEffect } from 'react';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';

export function Equalizer() {
  const t = useSharedValue(0);
  useEffect(() => { t.value = withRepeat(withTiming(1, { duration: 600 }), -1, true); }, []);
  const peaks = [0.4, 0.9, 0.6, 1.0];
  return (
    <Animated.View style={{ flexDirection: 'row', alignItems: 'flex-end', gap: 2, height: 11 }}>
      {peaks.map((peak, i) => {
        // eslint-disable-next-line react-hooks/rules-of-hooks
        const s = useAnimatedStyle(() => ({
          height: 11 * (i % 2 === 0 ? 0.4 + t.value * (peak - 0.4) : peak - t.value * (peak - 0.4)),
        }));
        return <Animated.View key={i} style={[{ width: 2, borderRadius: 1, backgroundColor: '#FFFFFF' }, s]} />;
      })}
    </Animated.View>
  );
}
```

### Slideshow Actions (Skip / Heart / Note)

```tsx
// components/SlideshowActions.tsx
import { View, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';

export function SlideshowActions({ onSkip, onLike, onNote }: { onSkip: () => void; onLike: () => void; onNote: () => void }) {
  const scale = useSharedValue(1);
  const heartStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));
  return (
    <View style={{ position: 'absolute', left: 0, right: 0, bottom: 18, flexDirection: 'row', justifyContent: 'center', alignItems: 'center', gap: 56 }}>
      <Pressable onPress={onSkip} hitSlop={16}>
        <Ionicons name="close" size={26} color="rgba(255,255,255,0.9)" />
      </Pressable>
      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); scale.value = withSequence(withSpring(1.15), withSpring(1)); onLike(); }}
        style={{ width: 60, height: 60, borderRadius: 30, borderWidth: 1, borderColor: '#FFFFFF', alignItems: 'center', justifyContent: 'center' }}
      >
        <Animated.View style={heartStyle}>
          <Ionicons name="heart-outline" size={24} color="#FFFFFF" />
        </Animated.View>
      </Pressable>
      <Pressable onPress={onNote} hitSlop={16}>
        <Ionicons name="chatbubble-outline" size={24} color="rgba(255,255,255,0.9)" />
      </Pressable>
    </View>
  );
}
```

### Member Chip (selection = black/white inversion)

```tsx
// components/MemberChip.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';

export function MemberChip({ label, selected }: { label: string; selected?: boolean }) {
  return (
    <Pressable style={{
      paddingVertical: 8, paddingHorizontal: 16, borderRadius: 999,
      borderWidth: 1,
      borderColor: selected ? '#FFFFFF' : colors.hairline,
      backgroundColor: selected ? '#FFFFFF' : 'transparent',
    }}>
      <Text style={{ fontFamily: 'Inter-Medium', fontSize: 12, color: selected ? '#000000' : '#FFFFFF' }}>{label}</Text>
    </Pressable>
  );
}
```

### Buttons (white fill rationed to one per screen)

```tsx
// components/Buttons.tsx
import { Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function PrimaryButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? colors.accentPressed : colors.accent,
        paddingVertical: 15, paddingHorizontal: 30, borderRadius: 999, alignItems: 'center',
        transform: [{ scale: pressed ? 0.99 : 1 }],
      })}
    >
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}

export function OutlineButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: pressed ? 'rgba(255,255,255,0.08)' : 'transparent',
        borderWidth: 1, borderColor: '#FFFFFF',
        paddingVertical: 14, paddingHorizontal: 28, borderRadius: 999, alignItems: 'center',
      })}
    >
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
```

## 4. Bottom Tab Bar

Minimal — 3 tabs, true-black opaque (no blur, no active pill), a 1px `#2E2E2E` top hairline; active is white, inactive `#5E5E5E`.

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
        tabBarActiveTintColor: colors.textPrimary,
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: colors.canvas, // opaque true black — no blur
          borderTopWidth: 1,
          borderTopColor: colors.hairline,
        },
        tabBarLabelStyle: { fontFamily: 'Inter-Medium', fontSize: 10, letterSpacing: 0.4 },
      }}
    >
      <Tabs.Screen name="index"    options={{ title: 'Discover', tabBarIcon: ({ color }) => <Ionicons name="sparkles-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="messages" options={{ title: 'Messages', tabBarIcon: ({ color }) => <Ionicons name="chatbubble-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="profile"  options={{ title: 'Profile',  tabBarIcon: ({ color }) => <Ionicons name="person-outline" size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Slideshow auto-advance — drive `progress` 0→1 per slide, then advance index
// Use a frame loop or withTiming on a sharedValue; tap left/right thirds to scrub instantly

// Story bar fill
// width: i < activeIndex ? '100%' : i === activeIndex ? `${progress*100}%` : 0  (no spring — linear with playback)

// Equalizer
// withRepeat(withTiming(1, { duration: 600 }), -1, true)  — loops to imply playback

// Heart press
// scale: withSequence(withSpring(1.15), withSpring(1)) + Haptics.impactAsync(Soft)

// Wordmark cold-open (optional)
// FadeIn over 400ms, hold 1.5s, FadeOut 300ms — then media is unobstructed

// Slide cross-fade
// 220ms ease-in-out opacity between slides; Reduce Motion → instant cut

// Haptics
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);  // like, send, scrub commit — sparingly
```

Motion, like color, is rationed: nothing pulses, nothing bounces beyond the single heart confirmation. The equalizer is the only persistent animation, and it exists only to imply the song is playing.

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons) — fine-line, 1.5–2px stroke, **never filled** (the filled-vs-outline language does not exist in Raya; everything is an outline).

| Purpose | Ionicons |
|---------|----------|
| Skip / pass | `close` |
| Like (the focal action) | `heart-outline` (never `heart`) |
| Note / message | `chatbubble-outline` |
| Music ticker glyph | `musical-notes` / `disc-outline` |
| Location | `location-outline` |
| Discover (tab) | `sparkles-outline` |
| Messages (tab) | `chatbubble-outline` |
| Profile (tab) | `person-outline` |
| More / actions | `ellipsis-horizontal` |
| Settings | `settings-outline` |
| Back | `chevron-back` |
| Send (in conversation) | text `SEND` — no filled button |

## 7. Platform Notes

- **Font choice**: Raya ships a custom neo-grotesque; `Inter` (SIL OFL) is the closest free substitute and is safe to bundle. One family only — never add a serif or mono.
- **No light mode**: do NOT branch on `useColorScheme()`. Raya is permanently dark by design; force `<StatusBar style="light" />` and a black `SafeAreaView` everywhere.
- **No brand color**: the accent is white; never introduce a tint, gradient, or colored state. The only color in the app is the member's full-bleed photo/video — never filter, tint, or colored-frame it.
- **No shadows**: never set `shadowColor` / `elevation`. Separation is the faint surface lift (`#0D0D0D` vs `#000000`) and the 1px `#2E2E2E` hairline only.
- **Slideshow media**: use `expo-av` `Video` for video slides (mute by default, loop within the slide); preload the next slide's asset so advance is seamless.
- **Scrim legibility**: under a bright/high-key member photo, deepen the bottom scrim (raise the `rgba(0,0,0,0.92)` stop earlier) so caption text stays readable; never lighten the caption text instead.
- **Safe area**: wrap screens in `SafeAreaView`; story bars sit just below the Dynamic Island — offset the top row by the safe-area inset.
- **Dynamic Type**: allow scaling on name, body, section titles, bio; set `allowFontScaling={false}` on story-bar/music-ticker text, eyebrows, role lines, and tab labels (layout-sensitive over media).
- **Reduce Motion**: replace slide cross-fades with instant cuts, freeze the equalizer at a static mid-state, skip the wordmark cold-open, and disable the heart pulse — keep the soft haptic.
- **Accessibility**: the 1px hairline must never be the sole carrier of meaning for screen-reader users — pair structural separation with labels; the Heart needs a 60pt target, Skip/Note a 44pt `hitSlop`, and the story-bar scrub zones span the full left/right thirds.
