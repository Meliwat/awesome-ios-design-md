# Bandcamp (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bandcamp's visual language into paste-ready Expo / React Native code: a design-token module, the big square album art, the buy/support card, the inline teal-waveform player, the tracklist, and the fan collection card.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-image`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Surfaces (light — Bandcamp's true identity)
  paper:        '#FFFFFF',
  paperGray:    '#F4F4F4',
  hoverGray:    '#EDEDED',
  divider:      '#E2E2E2',
  trackDivider: '#F0F0F0',

  // Surfaces (dark — true inversion, NOT default)
  darkCanvas:   '#101417',
  darkSurface1: '#1A2024',
  darkSurface2: '#232B30',
  darkDivider:  '#2C353B',

  // Text
  ink:           '#1A1A1A',
  textSecondary: '#767676',
  textTertiary:  '#9A9A9A',
  darkInk:       '#EDF1F3',

  // Brand
  teal:       '#1DA0C3',
  tealDeep:   '#629AA9',
  tealPress:  '#17819E',
  darkTeal:   '#3DB5D6',

  // Semantic
  success: '#4CAF50',
  error:   '#E45858',
  warning: '#E0A030',
} as const;

export type BCColor = keyof typeof colors;
```

## 2. Typography

Bandcamp uses a clean humanist sans; use **DM Sans** as the faithful fallback (SIL OFL, free to bundle). Load via `expo-font`. Editorial, record-sleeve rhythm — titles at 700, artist/price at 600–700, body at 400.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    // Swap DMSans-* → your licensed Bandcamp face if available.
    'DMSans-Regular':  require('../assets/fonts/DMSans-Regular.ttf'),
    'DMSans-Medium':   require('../assets/fonts/DMSans-Medium.ttf'),
    'DMSans-SemiBold': require('../assets/fonts/DMSans-SemiBold.ttf'),
    'DMSans-Bold':     require('../assets/fonts/DMSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const R  = 'DMSans-Regular';
const M  = 'DMSans-Medium';
const SB = 'DMSans-SemiBold';
const B  = 'DMSans-Bold';
const ink = { color: '#1A1A1A' } satisfies TextStyle;

export const typography = {
  screenTitle: { ...ink, fontFamily: B,  fontSize: 32, lineHeight: 37, letterSpacing: -0.6 },
  albumTitle:  { ...ink, fontFamily: B,  fontSize: 22, lineHeight: 26, letterSpacing: -0.3 },
  section:     { ...ink, fontFamily: B,  fontSize: 22, lineHeight: 28, letterSpacing: -0.2 },
  artistLink:  { color: '#1DA0C3', fontFamily: SB, fontSize: 18, lineHeight: 23 },
  subhead:     { ...ink, fontFamily: SB, fontSize: 18, lineHeight: 23, letterSpacing: -0.1 },
  body:        { ...ink, fontFamily: R,  fontSize: 16, lineHeight: 25 },
  price:       { ...ink, fontFamily: B,  fontSize: 22, lineHeight: 24, letterSpacing: -0.2 },
  priceUnit:   { color: '#767676', fontFamily: SB, fontSize: 13, lineHeight: 16 },
  rowTitle:    { ...ink, fontFamily: SB, fontSize: 15, lineHeight: 20 },
  meta:        { color: '#767676', fontFamily: R, fontSize: 14, lineHeight: 19 },
  trackName:   { ...ink, fontFamily: M, fontSize: 14, lineHeight: 19 },
  trackNum:    { color: '#9A9A9A', fontFamily: SB, fontSize: 12, lineHeight: 12, fontVariant: ['tabular-nums'] as const },
  tabLabel:    { fontFamily: SB, fontSize: 10, lineHeight: 10, letterSpacing: 0.1 },
  button:      { color: '#FFFFFF', fontFamily: B, fontSize: 15, lineHeight: 15 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Big Square Album Art

```tsx
// components/AlbumArt.tsx
import { View } from 'react-native';
import { Image } from 'expo-image';
import { colors } from '../theme/colors';

export function AlbumArt({ uri, fullBleed = true }: { uri?: string; fullBleed?: boolean }) {
  return (
    <View style={{ width: '100%', aspectRatio: 1, borderRadius: fullBleed ? 0 : 4, overflow: 'hidden', backgroundColor: colors.paperGray }}>
      {uri ? (
        // strictly square, never circular, never tinted, never shadowed — content is sovereign
        <Image source={{ uri }} style={{ width: '100%', height: '100%' }} contentFit="cover" transition={150} />
      ) : null}
    </View>
  );
}
```

### Buy / Support Card (signature)

```tsx
// components/BuySupportCard.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function BuySupportCard({
  price, currency, nameYourPrice, onBuy, onWishlist,
}: {
  price: string; currency: string; nameYourPrice: boolean;
  onBuy: () => void; onWishlist: () => void;
}) {
  return (
    <View style={styles.card}>
      <View style={styles.priceRow}>
        <View style={{ flexDirection: 'row', alignItems: 'baseline', gap: 6 }}>
          <Text style={typography.price}>${price}</Text>
          <Text style={typography.priceUnit}>{currency}</Text>
        </View>
        {nameYourPrice ? <Text style={typography.priceUnit}>or more</Text> : null}
      </View>
      {nameYourPrice ? (
        <Text style={[typography.priceUnit, { marginTop: 4 }]}>Name your price · Pay what you want</Text>
      ) : null}

      <Pressable
        onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onBuy(); }}
        style={({ pressed }) => [styles.buy, pressed && { backgroundColor: colors.tealPress, transform: [{ scale: 0.99 }] }]}
      >
        <Text style={typography.button}>Buy Digital Album</Text>
      </Pressable>

      <Pressable onPress={onWishlist} style={{ alignSelf: 'center', marginTop: 10 }}>
        <Text style={{ fontFamily: 'DMSans-SemiBold', fontSize: 13, color: colors.teal }}>Add to wishlist</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  card: {
    backgroundColor: colors.paperGray, borderWidth: 1, borderColor: colors.divider,
    borderRadius: 6, padding: 16,
  },
  priceRow: { flexDirection: 'row', alignItems: 'flex-end', justifyContent: 'space-between' },
  buy: {
    marginTop: 14, backgroundColor: colors.teal, borderRadius: 4,
    paddingVertical: 13, alignItems: 'center',
  },
});
```

### Inline Teal-Waveform Player

```tsx
// components/InlinePlayer.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const BARS = [6,13,9,16,7,11,8,14,10,12];

export function InlinePlayer({
  playing, track, elapsed, total, progress, onToggle,
}: { playing: boolean; track: string; elapsed: string; total: string; progress: number; onToggle: () => void }) {
  return (
    <View style={styles.bar}>
      <Pressable onPress={onToggle} style={styles.play}>
        <Ionicons name={playing ? 'pause' : 'play'} size={14} color="#FFF" style={{ marginLeft: playing ? 0 : 2 }} />
      </Pressable>
      <View style={{ flex: 1 }}>
        <Text style={{ fontFamily: 'DMSans-SemiBold', fontSize: 13, color: colors.ink }}>{track}</Text>
        <Text style={[typography.priceUnit, { fontSize: 11, fontFamily: 'DMSans-Regular', color: colors.textSecondary, marginTop: 2 }]}>
          {elapsed} / {total}
        </Text>
      </View>
      <View style={styles.wave}>
        {BARS.map((h, i) => (
          <View
            key={i}
            style={{
              width: 2.5, height: h, borderRadius: 1.25,
              backgroundColor: colors.teal,
              opacity: i / BARS.length < progress ? 1 : 0.35,
            }}
          />
        ))}
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  bar: {
    flexDirection: 'row', alignItems: 'center', gap: 12,
    paddingHorizontal: 20, paddingVertical: 12,
    borderTopWidth: 1, borderBottomWidth: 1, borderColor: colors.divider,
  },
  play: { width: 36, height: 36, borderRadius: 18, backgroundColor: colors.ink, alignItems: 'center', justifyContent: 'center' },
  wave: { flexDirection: 'row', alignItems: 'center', gap: 2, height: 18 },
});
```

### Tracklist Row

```tsx
// components/TrackRow.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TrackRow({
  number, name, duration, isPlaying,
}: { number: number; name: string; duration: string; isPlaying: boolean }) {
  return (
    <Pressable style={({ pressed }) => [pressed && { backgroundColor: colors.hoverGray }]}>
      <View style={styles.row}>
        <Text style={[typography.trackNum, { width: 18 }]}>{number}</Text>
        <Text style={[
          isPlaying ? { fontFamily: 'DMSans-Bold', fontSize: 14, color: colors.teal } : typography.trackName,
          { flex: 1 },
        ]}>
          {name}
        </Text>
        <Text style={[typography.priceUnit, { fontFamily: 'DMSans-Regular', color: colors.textSecondary, fontSize: 12 }]}>
          {duration}
        </Text>
      </View>
      <View style={styles.div} />
    </Pressable>
  );
}

const styles = StyleSheet.create({
  row: { flexDirection: 'row', alignItems: 'center', gap: 12, paddingHorizontal: 20, paddingVertical: 9 },
  div: { height: 1, backgroundColor: colors.trackDivider, marginHorizontal: 20 },
});
```

### Fan Collection Card

```tsx
// components/CollectionCard.tsx
import { View, Text } from 'react-native';
import { AlbumArt } from './AlbumArt';
import { colors } from '../theme/colors';

export function CollectionCard({ artUri, title, artist }: { artUri?: string; title: string; artist: string }) {
  return (
    <View style={{ flex: 1 }}>
      <AlbumArt uri={artUri} fullBleed={false} />
      <Text numberOfLines={1} style={{ fontFamily: 'DMSans-SemiBold', fontSize: 13, color: colors.ink, marginTop: 8 }}>{title}</Text>
      <Text numberOfLines={1} style={{ fontFamily: 'DMSans-Regular', fontSize: 12, color: colors.textSecondary, marginTop: 2 }}>{artist}</Text>
    </View>
  );
}

// Feed wrapper: "{Fan} bought this" + optional note above the card
export function FeedItem({ fan, note, children }: { fan: string; note?: string; children: React.ReactNode }) {
  return (
    <View style={{ paddingHorizontal: 20, gap: 8 }}>
      <Text style={{ fontFamily: 'DMSans-SemiBold', fontSize: 13, color: colors.ink }}>{fan} bought this</Text>
      {note ? <Text style={{ fontFamily: 'DMSans-Regular', fontSize: 16, lineHeight: 25, color: colors.textSecondary }}>{note}</Text> : null}
      {children}
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
        tabBarActiveTintColor: colors.teal,          // Bandcamp teal
        tabBarInactiveTintColor: colors.textTertiary,
        tabBarStyle: {
          backgroundColor: colors.paper,
          borderTopWidth: 1,
          borderTopColor: colors.divider,
        },
        tabBarLabelStyle: { fontFamily: 'DMSans-SemiBold', fontSize: 10, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"      options={{ title: 'Feed',       tabBarIcon: ({ color }) => <Ionicons name="home"            size={22} color={color} /> }} />
      <Tabs.Screen name="discover"   options={{ title: 'Discover',   tabBarIcon: ({ color }) => <Ionicons name="search"          size={22} color={color} /> }} />
      <Tabs.Screen name="collection" options={{ title: 'Collection', tabBarIcon: ({ color }) => <Ionicons name="grid"            size={22} color={color} /> }} />
      <Tabs.Screen name="profile"    options={{ title: 'Profile',    tabBarIcon: ({ color }) => <Ionicons name="person"          size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion

```tsx
// Buy button press — scale 0.99 + darken to tealPress, soft haptic
// (Pressable style callback) pressed && { backgroundColor: colors.tealPress, transform: [{ scale: 0.99 }] }
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Add to collection / wishlist — quick teal fill (150ms) + soft haptic
// withTiming on a heart/check opacity or scale; then a toast "Added to collection"

// Page navigation — native stack push (300ms); use a shared-element lib for the
// album-art zoom from the feed tile if desired (react-native-shared-element)

// Player progress — linear, no spring; bar opacity = i/len < progress ? 1 : 0.35

// Tracklist row tap — pressed highlight #EDEDED, then track name turns teal
// Bottom sheet (checkout/share) — @gorhom/bottom-sheet slide-up + backdrop fade
```

## 6. Icon Library

Use `@expo/vector-icons` (Ionicons). Bandcamp's iconography is simple and outline-leaning; the waveform is a drawn bar set.

| Purpose | Ionicons |
|---------|----------|
| Back | `chevron-back` |
| Overflow menu | `ellipsis-horizontal` |
| Play | `play` |
| Pause | `pause` |
| Next track | `play-forward` |
| Previous track | `play-back` |
| Wishlist (add) | `heart-outline` |
| Wishlist (added) | `heart` |
| In collection | `checkmark-circle` |
| Gift this | `gift-outline` |
| Share | `share-outline` |
| Feed (tab) | `home` |
| Discover (tab) | `search` |
| Collection (tab) | `grid` |
| Profile (tab) | `person` |
| Download | `arrow-down-circle-outline` |
| Buy / cart | `bag-outline` |
| Merch | `shirt-outline` |
| Tag / genre | `pricetag-outline` |

## 7. Platform Notes

- **Font choice**: bundle the Bandcamp face if licensed; otherwise ship **DM Sans** (SIL OFL) as the faithful fallback for all roles — never leave titles to system-only
- **Status bar**: `<StatusBar style="dark" />` on light (the default), `"light"` only in dark mode
- **Light-first**: do NOT default to dark. Follow `useColorScheme()`; support a true dark inversion (`darkCanvas` etc.) but the white editorial page is the identity
- **Safe area**: wrap screens in `SafeAreaView`; the top nav and bottom tab bar need safe-area padding; album art may extend full-bleed under the status bar
- **expo-image**: use it for album art with `contentFit="cover"`; a flat `paperGray` background as the placeholder — never a shimmer gradient
- **Dynamic Type**: keep `allowFontScaling` on titles/body/prices/track names; set `allowFontScaling={false}` on tab labels, track numbers, captions, price unit (layout-sensitive)
- **Square art is non-negotiable**: always `aspectRatio: 1`; never `borderRadius: 999` (no circles); never apply a tint, dim, or shadow to the artwork
- **Reduce Motion**: read `AccessibilityInfo.isReduceMotionEnabled()` — disable the album-art zoom and the teal fill animation (instant state); the player progress is functional, keep it
- **Accessibility**: the price is critical — never hide it from screen readers; announce the buy card as one element "{price} {currency} or more, Buy Digital Album"; convey the playing track in its `accessibilityLabel`, not teal color alone
- **Commerce**: the Buy button should be unmistakably primary on every release; don't bury it below the fold or de-emphasize it relative to the play control
