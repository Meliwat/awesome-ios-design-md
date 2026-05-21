# Nextdoor (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Nextdoor's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `react-native-maps` (or `expo-maps`), and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  cream:    '#FAF9F6',   // warm canvas — never pure white
  surface1: '#FFFFFF',
  surface2: '#F2F0EB',
  divider:  '#E4E2DD',

  textPrimary:   '#221E1F',   // warm ink
  textSecondary: '#6B6864',
  textTertiary:  '#9C9893',

  green:        '#00B246',
  greenPressed: '#007A30',
  linkForest:   '#006B3C',

  alertAmber: '#E58A00',
  error:      '#D93A2B',
  eventBlue:  '#1B6FB3',
  forSale:    '#6E4FA3',
} as const;

export type NDColor = keyof typeof colors;
```

## 2. Typography

Load **Lato** via `expo-font`. Fall back to `System` (SF Pro) at aligned sizes/weights.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Lato-Regular': require('../assets/fonts/Lato-Regular.ttf'),
    'Lato-Bold':    require('../assets/fonts/Lato-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const reg = 'Lato-Regular';
const bold = 'Lato-Bold';

export const typography = {
  titleLarge:  { fontFamily: bold, fontSize: 28, lineHeight: 34, color: '#221E1F' },
  section:     { fontFamily: bold, fontSize: 22, lineHeight: 27, color: '#221E1F' },
  postTitle:   { fontFamily: bold, fontSize: 18, lineHeight: 23, color: '#221E1F' },
  name:        { fontFamily: bold, fontSize: 16, lineHeight: 20, color: '#221E1F' },
  body:        { fontFamily: reg,  fontSize: 16, lineHeight: 23, color: '#221E1F' },   // 1.45
  bodySettings:{ fontFamily: reg,  fontSize: 15, lineHeight: 21, color: '#221E1F' },
  meta:        { fontFamily: reg,  fontSize: 13, lineHeight: 17, color: '#6B6864' },
  chip:        { fontFamily: bold, fontSize: 14, lineHeight: 17 },
  actionLabel: { fontFamily: bold, fontSize: 14, lineHeight: 17 },
  button:      { fontFamily: bold, fontSize: 16, lineHeight: 20 },
  tab:         { fontFamily: bold, fontSize: 10, lineHeight: 12 },
  pinLabel:    { fontFamily: bold, fontSize: 12, lineHeight: 14, color: '#FFFFFF' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Neighborhood Feed Card (the core unit)

```tsx
// components/FeedCard.tsx
import { Image, Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import MaterialIcons from '@expo/vector-icons/MaterialIcons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function FeedCard({
  name, verified, location, body, avatarUri,
}: {
  name: string; verified: boolean; location: string; body: string; avatarUri: string;
}) {
  return (
    <View style={{
      backgroundColor: colors.surface1, borderColor: colors.divider, borderWidth: 1,
      borderRadius: 16, padding: 16, marginHorizontal: 16, marginBottom: 12,
      shadowColor: colors.textPrimary, shadowOpacity: 0.06, shadowRadius: 3, shadowOffset: { width: 0, height: 1 },
    }}>
      <View style={{ flexDirection: 'row', gap: 12 }}>
        <Image source={{ uri: avatarUri }} style={{ width: 44, height: 44, borderRadius: 22 }} />
        <View style={{ flex: 1 }}>
          <View style={{ flexDirection: 'row', alignItems: 'center', gap: 4 }}>
            <Text style={typography.name}>{name}</Text>
            {verified && <MaterialIcons name="verified" size={15} color={colors.green} />}
          </View>
          <Text style={typography.meta}>{location}</Text>
        </View>
        <Ionicons name="ellipsis-horizontal" size={20} color={colors.textSecondary} />
      </View>

      <Text style={[typography.body, { marginTop: 10 }]}>{body}</Text>

      <View style={{ height: 1, backgroundColor: colors.divider, marginVertical: 12 }} />

      <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
        <CardAction icon="thumbs-up-outline" label="React" />
        <CardAction icon="chatbubble-outline" label="Reply" />
        <CardAction icon="share-outline" label="Share" />
      </View>
    </View>
  );
}

function CardAction({ icon, label }: { icon: any; label: string }) {
  return (
    <Pressable hitSlop={10} style={{ flexDirection: 'row', alignItems: 'center', gap: 6, minHeight: 44 }}>
      <Ionicons name={icon} size={20} color={colors.textSecondary} />
      <Text style={[typography.actionLabel, { color: colors.textSecondary }]}>{label}</Text>
    </Pressable>
  );
}
```

### Primary Button & Center Post FAB

```tsx
import { Pressable, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function NDPrimaryButton({
  title, variant = 'filled', onPress,
}: { title: string; variant?: 'filled' | 'outline'; onPress: () => void }) {
  const filled = variant === 'filled';
  return (
    <Pressable onPress={onPress}
      style={({ pressed }) => ({
        backgroundColor: filled ? (pressed ? colors.greenPressed : colors.green) : 'transparent',
        borderWidth: filled ? 0 : 1.5, borderColor: colors.green,
        paddingVertical: filled ? 12 : 10, paddingHorizontal: filled ? 24 : 20,
        borderRadius: 24, transform: [{ scale: pressed ? 0.98 : 1 }],
      })}>
      <Text style={[typography.button, { color: filled ? '#fff' : colors.green }]}>{title}</Text>
    </Pressable>
  );
}

export function CenterPostFAB({ onPress }: { onPress: () => void }) {
  return (
    <Pressable
      onPress={() => { Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); onPress(); }}
      style={({ pressed }) => ({
        width: 56, height: 56, borderRadius: 28,
        backgroundColor: pressed ? colors.greenPressed : colors.green,
        alignItems: 'center', justifyContent: 'center', marginTop: -14,
        transform: [{ scale: pressed ? 0.94 : 1 }],
        shadowColor: colors.green, shadowOpacity: 0.32, shadowRadius: 18, shadowOffset: { width: 0, height: 6 },
      })}>
      <Ionicons name="add" size={26} color="#fff" />
    </Pressable>
  );
}
```

### Group Chip Row

```tsx
// components/GroupChipRow.tsx
import { ScrollView, Pressable, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function GroupChipRow({
  groups, selected, onSelect,
}: { groups: string[]; selected: string; onSelect: (g: string) => void }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false}
      contentContainerStyle={{ paddingHorizontal: 16, gap: 8 }}>
      {groups.map((g) => {
        const active = g === selected;
        return (
          <Pressable key={g} onPress={() => onSelect(g)}
            style={{ paddingVertical: 8, paddingHorizontal: 16, borderRadius: 500,
                     backgroundColor: active ? colors.green : colors.surface2 }}>
            <Text style={[typography.chip, { color: active ? '#fff' : colors.textPrimary }]}>{g}</Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
}
```

## 4. Distinctive System — Hyperlocal Map Pin

```tsx
// components/MapPin.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const CATEGORY_COLOR = {
  recommendation: colors.green,
  event:          colors.eventBlue,
  forSale:        colors.forSale,
  alert:          colors.alertAmber,
} as const;

export function MapPin({
  category, label, selected,
}: { category: keyof typeof CATEGORY_COLOR; label: string; selected: boolean }) {
  return (
    <View style={{
      paddingVertical: 6, paddingHorizontal: 10, borderRadius: 500,
      backgroundColor: CATEGORY_COLOR[category],
      borderWidth: selected ? 2 : 0, borderColor: '#FFFFFF',
      transform: [{ scale: selected ? 1.15 : 1 }],
      shadowColor: colors.textPrimary, shadowOpacity: 0.25, shadowRadius: 6, shadowOffset: { width: 0, height: 2 },
      ...(selected ? { outlineColor: colors.green } : {}),
    }}>
      <Text style={typography.pinLabel}>{label}</Text>
    </View>
  );
}
```

Use as the `Marker` child in `react-native-maps`. Tapping a marker opens a draggable bottom peek sheet (e.g. `@gorhom/bottom-sheet`) with detents at ~220px and full.

## 5. Tab Bar

Nextdoor's tab bar is opaque white on cream with a raised green center Post button. `expo-router` `Tabs` supports a custom center button via `tabBarButton`.

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';
import { CenterPostFAB } from '../../components/CenterPostFAB';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: colors.green,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: { backgroundColor: colors.surface1, borderTopColor: colors.divider, borderTopWidth: 0.5, height: 84 },
        tabBarLabelStyle: { fontFamily: 'Lato-Bold', fontSize: 10 },
      }}
    >
      <Tabs.Screen name="index" options={{ title: 'Home',  tabBarIcon: ({ color }) => <Ionicons name="home" size={26} color={color} /> }} />
      <Tabs.Screen name="map"   options={{ title: 'Map',   tabBarIcon: ({ color }) => <Ionicons name="map"  size={26} color={color} /> }} />
      <Tabs.Screen name="post"  options={{ title: '', tabBarButton: (props) => <CenterPostFAB onPress={() => props.onPress?.()} /> }} />
      <Tabs.Screen name="alerts" options={{ title: 'Notifications', tabBarIcon: ({ color }) => <Ionicons name="notifications" size={26} color={color} /> }} />
      <Tabs.Screen name="inbox"  options={{ title: 'Inbox', tabBarIcon: ({ color }) => <Ionicons name="mail" size={26} color={color} /> }} />
    </Tabs>
  );
}
```

## 6. Motion

```tsx
// FAB press: scale 0.94, #007A30, soft haptic (see CenterPostFAB)

// Reaction tap: fade to #00B246 + subtle 1.0 → 1.12 → 1.0 over 250ms, light haptic
const scale = useSharedValue(1);
const onReact = () => {
  scale.value = withSequence(withTiming(1.12, { duration: 120 }), withSpring(1));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
};

// Card appear: fade + 8px rise over 0.25s ease-out (FlatList itemLayoutAnimation or Reanimated entering)
// entering={FadeInDown.duration(250)}

// Map pin select: scale 1.0 → 1.15 + green outline (see MapPin)

// Chip switch: 0.2s cross-fade of the feed (animate opacity 0→1 on the list container)
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons + MaterialIcons for the verified seal). Map to Nextdoor's SF Symbol equivalents:

| Purpose | SF Symbol (iOS) | Expo Vector Icon |
|---------|-----------------|------------------|
| React | `hand.thumbsup` | Ionicons `thumbs-up-outline` / `thumbs-up` |
| Reply | `bubble.left` | Ionicons `chatbubble-outline` |
| Share | `square.and.arrow.up` | Ionicons `share-outline` |
| More | `ellipsis` | Ionicons `ellipsis-horizontal` |
| Verified neighbor | `checkmark.seal.fill` | MaterialIcons `verified` |
| Post (center FAB) | `plus` | Ionicons `add` |
| Home (tab) | `house` / `house.fill` | Ionicons `home` / `home-outline` |
| Map (tab) | `map` / `map.fill` | Ionicons `map` / `map-outline` |
| Notifications (tab) | `bell` / `bell.fill` | Ionicons `notifications` |
| Inbox (tab) | `envelope` / `envelope.fill` | Ionicons `mail` |
| Search | `magnifyingglass` | Ionicons `search` |
| Map recenter | `location.fill` | Ionicons `locate` |

## 8. Platform Notes

- **Warm cream, light only**: Nextdoor has no dark mode — keep `colors.cream` even if the system is dark; the warm canvas is the identity. Force `<StatusBar style="dark" />` from `expo-status-bar`.
- **Fonts**: bundle Lato via `expo-font`; if loading fails, swap `fontFamily` out so the system SF Pro renders at the same sizes/weights
- **Maps**: use `react-native-maps` (Apple Maps on iOS) with custom `Marker` children for the category pins; the hyperlocal map is the hero screen — make it full-bleed
- **Safe area**: wrap screens in `SafeAreaView`; the map is edge-to-edge while the feed keeps 16px insets and the cream gutter between cards
- **Reading-first**: React Native honors user font scaling — keep it on post body and names; pin only tab labels and map-pin labels
- **Accessibility**: add `accessibilityRole="button"` on actions; give the verified seal an `accessibilityLabel="Verified neighbor"`; announce map pins as category + label
