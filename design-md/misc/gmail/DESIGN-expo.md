# Gmail (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Gmail's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  canvas:        '#FFFFFF',
  surface:       '#F6F8FC',
  surfacePressed:'#F1F3F4',
  divider:       '#DADCE0',

  textPrimary:   '#202124',
  textSecondary: '#5F6368',
  textDisabled:  '#9AA0A6',
  textLink:      '#1A73E8',

  // Gmail red (FAB + destructive)
  gmailRed:        '#D93025',
  gmailRedPressed: '#B3261E',
  gmailRedSubtle:  '#FCE8E6',

  // Google brand palette
  googleRed:    '#EA4335',
  googleYellow: '#FBBC04',
  googleGreen:  '#34A853',
  googleBlue:   '#4285F4',

  // Semantic
  starYellow:    '#F5BA18',
  archiveTeal:   '#1E8E3E',
  selectedRow:   '#E8F0FE',
  snoozePurple:  '#A142F4',

  // Dark mode
  darkCanvas:    '#202124',
  darkSurface1:  '#28292C',
  darkSurface2:  '#303134',
  darkDivider:   '#3C4043',
  darkTextPrimary:   '#E8EAED',
  darkTextSecondary: '#BDC1C6',
  darkRed:       '#F28B82',
} as const;

export type GmailColor = keyof typeof colors;
```

## 2. Typography

Load Google Sans + Roboto via `expo-font`. On iOS without bundled fonts, fall back to system (SF Pro).

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'GoogleSans-Regular': require('../assets/fonts/GoogleSans-Regular.ttf'),
    'GoogleSans-Medium':  require('../assets/fonts/GoogleSans-Medium.ttf'),
    'GoogleSans-Bold':    require('../assets/fonts/GoogleSans-Bold.ttf'),
    'Roboto-Regular':     require('../assets/fonts/Roboto-Regular.ttf'),
    'Roboto-Medium':      require('../assets/fonts/Roboto-Medium.ttf'),
    'Roboto-Bold':        require('../assets/fonts/Roboto-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#202124' } satisfies TextStyle;

export const typography = {
  // Google Sans (structural)
  titleLarge:    { ...primary, fontFamily: 'GoogleSans-Regular', fontSize: 28, lineHeight: 34 },
  titleCompact:  { ...primary, fontFamily: 'GoogleSans-Medium',  fontSize: 20, lineHeight: 24, letterSpacing: 0.15 },
  sectionHeader: { ...primary, fontFamily: 'GoogleSans-Medium',  fontSize: 13, lineHeight: 16, letterSpacing: 0.8, textTransform: 'uppercase' as const },
  threadSubject: { ...primary, fontFamily: 'GoogleSans-Regular', fontSize: 22, lineHeight: 28 },
  senderUnread:  { ...primary, fontFamily: 'GoogleSans-Medium',  fontSize: 14, lineHeight: 17 },
  subjectUnread: { ...primary, fontFamily: 'GoogleSans-Medium',  fontSize: 14, lineHeight: 18 },
  button:        { color: '#1A73E8', fontFamily: 'GoogleSans-Medium', fontSize: 14, lineHeight: 14 },
  fabButton:     { color: '#FFFFFF', fontFamily: 'GoogleSans-Medium', fontSize: 16, lineHeight: 16 },
  smartReply:    { color: '#1A73E8', fontFamily: 'GoogleSans-Medium', fontSize: 14, lineHeight: 14 },
  labelChip:     { fontFamily: 'GoogleSans-Medium', fontSize: 12, lineHeight: 14, letterSpacing: 0.2 },
  tab:           { fontFamily: 'GoogleSans-Medium', fontSize: 12, lineHeight: 12, letterSpacing: 0.1 },

  // Roboto (information)
  senderRead:    { ...primary, fontFamily: 'Roboto-Regular', fontSize: 14, lineHeight: 17 },
  snippet:       { color: '#5F6368', fontFamily: 'Roboto-Regular', fontSize: 13, lineHeight: 17 },
  body:          { ...primary, fontFamily: 'Roboto-Regular', fontSize: 15, lineHeight: 22 },
  timestamp:     { color: '#5F6368', fontFamily: 'Roboto-Regular', fontSize: 13, lineHeight: 16 },
  meta:          { color: '#5F6368', fontFamily: 'Roboto-Regular', fontSize: 12, lineHeight: 16 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Compose FAB

```tsx
// components/ComposeFAB.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ComposeFAB({
  extended = false, onPress,
}: { extended?: boolean; onPress: () => void }) {
  const scale = useSharedValue(1);
  const animStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Pressable
      onPressIn={() => (scale.value = withSpring(0.96, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
      style={{ position: 'absolute', bottom: 16, right: 16 }}
    >
      <Animated.View
        style={[
          {
            flexDirection: 'row', alignItems: 'center', justifyContent: 'center',
            width: extended ? 120 : 56, height: 56,
            backgroundColor: colors.gmailRed,
            borderRadius: 16,
            gap: 8, paddingHorizontal: extended ? 16 : 0,
            shadowColor: '#000', shadowOpacity: 0.2, shadowRadius: 12, shadowOffset: { width: 0, height: 4 },
            elevation: 6,
          },
          animStyle,
        ]}
      >
        <Ionicons name="create" size={24} color="#FFFFFF" />
        {extended ? <Text style={typography.fabButton}>Compose</Text> : null}
      </Animated.View>
    </Pressable>
  );
}
```

### Email List Row (with Swipe Actions)

```tsx
// components/EmailRow.tsx
import { Pressable, Text, View } from 'react-native';
import { Swipeable } from 'react-native-gesture-handler';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

function avatarColor(email: string): { bg: string; initial: string } {
  const first = (email[0] ?? '?').toUpperCase();
  if (first >= 'A' && first <= 'F') return { bg: colors.googleRed, initial: first };
  if (first >= 'G' && first <= 'L') return { bg: colors.googleYellow, initial: first };
  if (first >= 'M' && first <= 'R') return { bg: colors.googleGreen, initial: first };
  return { bg: colors.googleBlue, initial: first };
}

export function EmailRow({
  sender, senderEmail, subject, snippet, timestamp, isUnread = false, isStarred = false,
  onPress, onArchive, onDelete,
}: {
  sender: string; senderEmail: string; subject: string; snippet: string; timestamp: string;
  isUnread?: boolean; isStarred?: boolean;
  onPress: () => void; onArchive: () => void; onDelete: () => void;
}) {
  const { bg, initial } = avatarColor(senderEmail);

  return (
    <Swipeable
      renderLeftActions={() => <ActionPane color={colors.archiveTeal} icon="archive" />}
      renderRightActions={() => <ActionPane color={colors.gmailRed} icon="trash" align="right" />}
      onSwipeableLeftOpen={onArchive}
      onSwipeableRightOpen={onDelete}
    >
      <Pressable
        onPress={onPress}
        style={({ pressed }) => ({
          flexDirection: 'row', alignItems: 'center',
          height: 72, paddingHorizontal: 16, gap: 12,
          backgroundColor: pressed ? colors.surfacePressed : colors.canvas,
        })}
      >
        <View style={{
          width: 36, height: 36, borderRadius: 18,
          alignItems: 'center', justifyContent: 'center',
          backgroundColor: bg,
        }}>
          <Text style={{ color: '#FFF', fontFamily: 'GoogleSans-Medium', fontSize: 16 }}>{initial}</Text>
        </View>

        <View style={{ flex: 1 }}>
          <Text style={isUnread ? typography.senderUnread : typography.senderRead}>{sender}</Text>
          <Text numberOfLines={1}>
            <Text style={isUnread ? typography.subjectUnread : typography.senderRead}>{subject}</Text>
            <Text style={typography.snippet}> — {snippet}</Text>
          </Text>
        </View>

        <View style={{ alignItems: 'flex-end', gap: 4 }}>
          <Text style={[typography.timestamp, isUnread && { color: colors.textPrimary }]}>{timestamp}</Text>
          <Ionicons
            name={isStarred ? 'star' : 'star-outline'}
            size={18}
            color={isStarred ? colors.starYellow : colors.textSecondary}
          />
        </View>
      </Pressable>
    </Swipeable>
  );
}

function ActionPane({ color, icon, align = 'left' }: { color: string; icon: any; align?: 'left' | 'right' }) {
  return (
    <View style={{ flex: 1, backgroundColor: color, justifyContent: 'center', alignItems: align === 'left' ? 'flex-start' : 'flex-end', paddingHorizontal: 24 }}>
      <Ionicons name={icon} size={24} color="#FFFFFF" />
    </View>
  );
}
```

### Tonal Button (Reply / Reply all / Forward)

```tsx
// components/TonalButton.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TonalButton({
  title, icon, onPress,
}: { title: string; icon: keyof typeof Ionicons.glyphMap; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingVertical: 10, paddingHorizontal: 24,
        borderRadius: 500,
        backgroundColor: pressed ? colors.surfacePressed : colors.surface,
        transform: [{ scale: pressed ? 0.98 : 1 }],
      })}
    >
      <Ionicons name={icon} size={18} color={colors.textLink} />
      <Text style={typography.button}>{title}</Text>
    </Pressable>
  );
}
```

### Smart Reply Row

```tsx
// components/SmartReplyRow.tsx
import { ScrollView, Text, TouchableOpacity } from 'react-native';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function SmartReplyRow({
  suggestions, onSelect,
}: { suggestions: string[]; onSelect: (text: string) => void }) {
  return (
    <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ gap: 8, paddingHorizontal: 16 }}>
      {suggestions.map((text) => (
        <TouchableOpacity
          key={text}
          onPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
            onSelect(text);
          }}
          style={{
            paddingVertical: 10, paddingHorizontal: 16,
            borderRadius: 500,
            borderWidth: 1, borderColor: colors.divider,
            backgroundColor: 'transparent',
          }}
        >
          <Text style={typography.smartReply}>{text}</Text>
        </TouchableOpacity>
      ))}
    </ScrollView>
  );
}
```

### Label Chip

```tsx
// components/LabelChip.tsx
import { Text, View } from 'react-native';
import { typography } from '../theme/typography';

export function LabelChip({ label, color }: { label: string; color: string }) {
  return (
    <View style={{
      paddingVertical: 4, paddingHorizontal: 10,
      borderRadius: 500,
      backgroundColor: `${color}1A`, // 10% opacity hex suffix
      borderWidth: 1, borderColor: color,
    }}>
      <Text style={[typography.labelChip, { color }]}>{label}</Text>
    </View>
  );
}
```

## 4. Tab Bar (Material You Active Indicator)

```tsx
// app/(tabs)/_layout.tsx  (expo-router)
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { View } from 'react-native';
import { colors } from '../../theme/colors';

function TabIcon({ name, focused, color }: { name: any; focused: boolean; color: string }) {
  return (
    <View style={{
      width: 56, height: 32,
      borderRadius: 500,
      backgroundColor: focused ? colors.selectedRow : 'transparent',
      alignItems: 'center', justifyContent: 'center',
    }}>
      <Ionicons name={focused ? name : `${name}-outline`} size={24} color={color} />
    </View>
  );
}

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:  colors.textLink,
        tabBarInactiveTintColor: colors.textSecondary,
        tabBarStyle: {
          backgroundColor: colors.canvas,
          borderTopWidth: 0.5,
          borderTopColor: colors.divider,
          height: 68,
        },
        tabBarLabelStyle: { fontFamily: 'GoogleSans-Medium', fontSize: 12, letterSpacing: 0.1 },
      }}
    >
      <Tabs.Screen name="index"  options={{ title: 'Mail',   tabBarIcon: ({ color, focused }) => <TabIcon name="mail"    color={color} focused={focused} /> }} />
      <Tabs.Screen name="chat"   options={{ title: 'Chat',   tabBarIcon: ({ color, focused }) => <TabIcon name="chatbubble" color={color} focused={focused} /> }} />
      <Tabs.Screen name="spaces" options={{ title: 'Spaces', tabBarIcon: ({ color, focused }) => <TabIcon name="people"  color={color} focused={focused} /> }} />
      <Tabs.Screen name="meet"   options={{ title: 'Meet',   tabBarIcon: ({ color, focused }) => <TabIcon name="videocam" color={color} focused={focused} /> }} />
    </Tabs>
  );
}
```

## 5. Scroll-to-Hide FAB

```tsx
// components/InboxWithFAB.tsx
import { useState } from 'react';
import { FlatList, NativeScrollEvent, NativeSyntheticEvent, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withTiming } from 'react-native-reanimated';
import { ComposeFAB } from './ComposeFAB';
import { EmailRow } from './EmailRow';

export function InboxWithFAB({ emails }: { emails: any[] }) {
  const [extended, setExtended] = useState(true);
  let lastY = 0;

  const onScroll = (e: NativeSyntheticEvent<NativeScrollEvent>) => {
    const y = e.nativeEvent.contentOffset.y;
    const delta = y - lastY;
    if (delta > 8 && extended) setExtended(false);
    if (delta < -8 && !extended) setExtended(true);
    lastY = y;
  };

  return (
    <View style={{ flex: 1 }}>
      <FlatList
        data={emails}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => <EmailRow {...item} />}
        onScroll={onScroll}
        scrollEventThrottle={16}
      />
      <ComposeFAB extended={extended} onPress={() => {}} />
    </View>
  );
}
```

## 6. Motion

```tsx
// Star tap animation
const starScale = useSharedValue(1);
const onStar = () => {
  starScale.value = withSequence(withSpring(1.2), withSpring(1));
  Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
};

// Swipe actions — react-native-gesture-handler Swipeable
// Threshold at 40% row width; commits action with .success haptic; otherwise snap-back

// Archive collapse — Reanimated LayoutAnimation FadeOut + height → 0
// Use LinearTransition layout animation on the list wrapper
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to Gmail's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Compose (FAB) | `create` or `pencil` |
| Mail (tab) | `mail` / `mail-outline` |
| Chat (tab) | `chatbubble` / `chatbubble-outline` |
| Spaces (tab) | `people` / `people-outline` |
| Meet (tab) | `videocam` / `videocam-outline` |
| Search | `search` |
| Menu | `menu` |
| Archive | `archive` |
| Delete | `trash` |
| Mark unread | `mail-unread` |
| Snooze | `time` |
| Star | `star` / `star-outline` |
| Reply | `arrow-undo` |
| Reply all | `arrow-undo-circle` |
| Forward | `arrow-redo` |
| Attachment | `attach` |
| Send | `send` |
| Back | `chevron-back` |

## 8. Platform Notes

- **Google fonts**: bundle both Google Sans and Roboto; Google Sans is proprietary and must be licensed via Google's brand agreement for distribution. For prototypes, fall back to system fonts on iOS (SF Pro is a reasonable visual substitute)
- **Material You shapes**: Use `borderRadius: 16` for the FAB — not 28 (circle) — this is the Material You squircle
- **Swipe actions**: Use `react-native-gesture-handler` `Swipeable` for left/right swipe actions; set full-swipe threshold to 40%
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` on light mode, `"light"` on dark mode
- **Safe area**: wrap screens in `SafeAreaView` from `react-native-safe-area-context`; FAB must respect bottom safe area
- **Dynamic Type**: React Native respects system scale; set `allowFontScaling={false}` on tab labels, timestamps, Smart Reply chips, label chips
- **Accessibility**: group email row into accessible element; label: "Sarah Chen, unread, Q4 planning update, 10:42 AM, swipe left to archive, swipe right to delete"
- **Dark mode**: switch to tonal-surface tint strategy instead of alpha shadows on dark backgrounds (Material's recommended approach for dark elevation)
- **Android parity**: if targeting Android, use the correct Material You `elevation` prop instead of shadow; border radius stays the same
