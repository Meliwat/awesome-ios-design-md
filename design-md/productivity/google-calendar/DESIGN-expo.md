# Google Calendar (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Google Calendar's Material-on-iOS visual language into paste-ready Expo / React Native code: a design-token module, themed components for the event card, Schedule day banner, Material FAB with dual-shadow, and Month grid cell.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas & surfaces
  canvas:        '#FFFFFF',
  surfaceGray:   '#F1F3F4',
  surfaceGray2:  '#F8F9FA',
  divider:       '#DADCE0',

  // Text
  ink:           '#202124',
  secondary:     '#5F6368',
  tertiary:      '#80868B',

  // Brand
  blue:          '#1A73E8',
  bluePressed:   '#1557B0',
  blueTint:      '#E8F0FE',
  blueDark:      '#8AB4F8',  // dark mode

  // Event colors (Material primary set)
  eventBlue:     '#1A73E8',
  eventRed:      '#D93025',
  eventYellow:   '#F9AB00',
  eventGreen:    '#188038',

  // Semantic
  success:       '#188038',
  error:         '#D93025',

  // Dark mode
  darkCanvas:    '#202124',
  darkSurface:   '#2D2E30',
  darkSurface2:  '#3C4043',
  darkDivider:   '#3C4043',
  darkText:      '#E8EAED',
  darkTextSec:   '#9AA0A6',
} as const;

// 24-color user calendar palette
export const calendarColors = {
  tomato:        '#D50000',
  flamingo:      '#E67C73',
  tangerine:     '#F4511E',
  banana:        '#F6BF26',
  sage:          '#33B679',
  basil:         '#0B8043',
  peacock:       '#039BE5',
  blueberry:     '#3F51B5',
  lavender:      '#7986CB',
  grape:         '#8E24AA',
  graphite:      '#616161',
  birch:         '#A79B8E',
  eucalyptus:    '#16A765',
  cobalt:        '#0277BD',
  pumpkin:       '#F4511E',
  cherryBlossom: '#FAD165',
  avocado:       '#7CB342',
  citron:        '#C0CA33',
  pistachio:     '#9CCC65',
  wisteria:      '#5C6BC0',
  amethyst:      '#AB47BC',
  rose:          '#D81B60',
  cocoa:         '#795548',
  slate:         '#9E9E9E',
} as const;
```

## 2. Typography

Google Sans is publicly distributed via Google Fonts. On iOS, Google Calendar deliberately falls back to SF Pro Text for body content — we mirror that hybrid stack.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'GoogleSans-Regular':   require('../assets/fonts/GoogleSans-Regular.ttf'),
    'GoogleSans-Medium':    require('../assets/fonts/GoogleSans-Medium.ttf'),
    'GoogleSans-Bold':      require('../assets/fonts/GoogleSans-Bold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

export const typography = {
  // Google Sans Display for headlines
  navTitle:      { fontFamily: 'GoogleSans-Medium',  fontSize: 22, color: '#202124', lineHeight: 26 },
  dayNumberLg:   { fontFamily: 'GoogleSans-Regular', fontSize: 36, color: '#202124', lineHeight: 36, letterSpacing: -0.4 },
  sectionHdr:    { fontFamily: 'GoogleSans-Medium',  fontSize: 14, color: '#202124', lineHeight: 18 },
  dayLabel:      { fontFamily: 'GoogleSans-Medium',  fontSize: 13, color: '#5F6368', letterSpacing: 0.8, textTransform: 'uppercase' as const, lineHeight: 16 },
  eventDetail:   { fontFamily: 'GoogleSans-Regular', fontSize: 22, color: '#202124', letterSpacing: -0.1, lineHeight: 27 },

  // SF Pro Text fallback for body (system on iOS)
  eventTitle:    { fontSize: 14, fontWeight: '500', color: '#202124', lineHeight: 18 },
  eventTime:     { fontSize: 13, fontWeight: '400', color: '#5F6368', lineHeight: 16, fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] },
  eventLocation: { fontSize: 13, fontWeight: '400', color: '#5F6368', lineHeight: 17 },

  // Tinted block (Day/Week)
  blockTitle:    { fontSize: 12, fontWeight: '500', lineHeight: 14 },
  blockTime:     { fontSize: 11, fontWeight: '400', lineHeight: 13, fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] },

  // Month grid
  dayNumber:     { fontFamily: 'GoogleSans-Regular', fontSize: 14, color: '#202124', fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] },
  todayNumber:   { fontFamily: 'GoogleSans-Medium',  fontSize: 14, color: '#FFFFFF' },
  weekdayHdr:    { fontSize: 12, fontWeight: '500', color: '#5F6368', letterSpacing: 0.4, textTransform: 'uppercase' as const },

  // Time gutter
  timeGutter:    { fontSize: 11, fontWeight: '400', color: '#5F6368', fontVariant: ['tabular-nums'] as TextStyle['fontVariant'] },

  // Sidebar
  sidebar:       { fontSize: 14, fontWeight: '500', color: '#202124' },

  // Buttons (Google Sans Medium, UPPERCASE, tracked)
  button:        { fontFamily: 'GoogleSans-Medium', fontSize: 14, letterSpacing: 0.4, textTransform: 'uppercase' as const },

  // Misc
  tabLabel:      { fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
  caption:       { fontSize: 12, fontWeight: '400', color: '#5F6368' },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Event Card (Schedule View — Hero Component)

```tsx
// components/EventCard.tsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  title: string;
  timeRange: string;
  location?: string;
  calendarColor: string;
  onPress?: () => void;
};

export function EventCard({ title, timeRange, location, calendarColor, onPress }: Props) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => [styles.card, pressed && { backgroundColor: colors.surfaceGray }]}>
      <View style={[styles.bar, { backgroundColor: calendarColor }]} />
      <View style={styles.content}>
        <Text style={typography.eventTitle} numberOfLines={2}>{title}</Text>
        <Text style={[typography.eventTime, { marginTop: 4 }]}>{timeRange}</Text>
        {location && (
          <View style={styles.locRow}>
            <Ionicons name="location-outline" size={11} color={colors.secondary} />
            <Text style={typography.eventLocation} numberOfLines={1}>{location}</Text>
          </View>
        )}
      </View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card: {
    flexDirection: 'row',
    minHeight: 56,
    backgroundColor: colors.canvas,
    borderRadius: 4,
    marginHorizontal: 16,
    marginVertical: 4,
    overflow: 'hidden',
    // Material Level 1 dual shadow
    shadowColor: 'rgba(60,64,67,1)',
    shadowOpacity: 0.10,
    shadowRadius: 3,
    shadowOffset: { width: 0, height: 1 },
    elevation: 1,
  },
  bar:     { width: 4 },
  content: { flex: 1, paddingHorizontal: 16, paddingVertical: 12 },
  locRow:  { flexDirection: 'row', alignItems: 'center', gap: 4, marginTop: 4 },
});
```

### Schedule Day Banner

```tsx
// components/DayBanner.tsx
import { View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  dayNumber: string;
  dayLabel: string;
  weatherSymbol?: keyof typeof Ionicons.glyphMap;
  temperature?: string;
  isToday?: boolean;
};

export function DayBanner({ dayNumber, dayLabel, weatherSymbol, temperature, isToday }: Props) {
  return (
    <View style={{ flexDirection: 'row', alignItems: 'center', height: 80, paddingHorizontal: 16, gap: 16 }}>
      <View>
        <Text style={typography.dayLabel}>{dayLabel}</Text>
        {isToday ? (
          <View style={{
            width: 56, height: 56, borderRadius: 28,
            backgroundColor: colors.blue,
            alignItems: 'center', justifyContent: 'center',
            marginTop: 4,
          }}>
            <Text style={[typography.dayNumberLg, { color: '#FFFFFF' }]}>{dayNumber}</Text>
          </View>
        ) : (
          <Text style={[typography.dayNumberLg, { marginTop: 4 }]}>{dayNumber}</Text>
        )}
      </View>

      <View style={{ flex: 1 }} />

      {weatherSymbol && temperature && (
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
          <Ionicons name={weatherSymbol} size={20} color={colors.eventYellow} />
          <Text style={{ fontSize: 14, fontWeight: '500', color: colors.ink }}>{temperature}</Text>
        </View>
      )}
    </View>
  );
}
```

### Floating Action Button (Material Level 6 — Dual Shadow)

```tsx
// components/FAB.tsx
import { Pressable, View, StyleSheet } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';

export function FAB({ onPress }: { onPress: () => void }) {
  const ripple = useSharedValue(0);

  const rippleStyle = useAnimatedStyle(() => ({
    transform: [{ scale: 0.5 + ripple.value * 1.5 }],
    opacity: 0.3 * (1 - ripple.value),
  }));

  const handlePress = () => {
    ripple.value = 0;
    ripple.value = withSpring(1, { duration: 350 });
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    onPress();
  };

  return (
    <View style={styles.wrap}>
      {/* Inner dark shadow (Level 6 - sharp ambient) */}
      <View style={[styles.shadowInner]} />
      <Pressable onPress={handlePress} style={styles.fab}>
        <Animated.View style={[styles.ripple, rippleStyle]} />
        <Ionicons name="add" size={24} color="#FFFFFF" />
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  wrap: {
    position: 'absolute',
    right: 16,
    bottom: 16,
    width: 56,
    height: 56,
    // Outer soft shadow
    shadowColor: 'rgba(60,64,67,1)',
    shadowOpacity: 0.15,
    shadowRadius: 8,
    shadowOffset: { width: 0, height: 4 },
    elevation: 6,
  },
  shadowInner: {
    position: 'absolute',
    inset: 0,
    borderRadius: 28,
    // Inner sharper shadow for Material Level 6
    shadowColor: 'rgba(60,64,67,1)',
    shadowOpacity: 0.30,
    shadowRadius: 2,
    shadowOffset: { width: 0, height: 1 },
  },
  fab: {
    width: 56, height: 56, borderRadius: 28,
    backgroundColor: colors.blue,
    alignItems: 'center', justifyContent: 'center',
    overflow: 'hidden',
  },
  ripple: {
    position: 'absolute',
    width: 56, height: 56, borderRadius: 28,
    backgroundColor: '#FFFFFF',
  },
});
```

### Month Grid Cell

```tsx
// components/MonthCell.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  day: number;
  isToday?: boolean;
  isCurrentMonth?: boolean;
  events: string[]; // up to 3 calendar colors
};

export function MonthCell({ day, isToday, isCurrentMonth = true, events }: Props) {
  return (
    <View style={{
      flex: 1,
      minHeight: 52,
      backgroundColor: colors.canvas,
      borderTopWidth: 0.5,
      borderTopColor: colors.divider,
      paddingHorizontal: 4,
      paddingTop: 4,
    }}>
      <View style={{ flexDirection: 'row' }}>
        {isToday ? (
          <View style={{
            width: 28, height: 28, borderRadius: 14,
            backgroundColor: colors.blue,
            alignItems: 'center', justifyContent: 'center',
          }}>
            <Text style={typography.todayNumber}>{day}</Text>
          </View>
        ) : (
          <Text style={[typography.dayNumber, !isCurrentMonth && { color: colors.tertiary }]}>{day}</Text>
        )}
      </View>

      <View style={{ flexDirection: 'row', gap: 2, marginTop: 4, paddingLeft: 4, flexWrap: 'wrap' }}>
        {events.slice(0, 3).map((c, i) => (
          <View key={i} style={{ width: 4, height: 4, borderRadius: 2, backgroundColor: c }} />
        ))}
        {events.length > 3 && (
          <Text style={{ fontSize: 9, fontWeight: '500', color: colors.tertiary }}>+{events.length - 3}</Text>
        )}
      </View>
    </View>
  );
}
```

### Day / Week Tinted Event Block

```tsx
// components/TintedBlock.tsx
import { View, Text } from 'react-native';
import { typography } from '../theme/typography';

type Props = {
  title: string;
  timeRange: string;
  calendarColor: string;
  heightPt: number;
};

export function TintedBlock({ title, timeRange, calendarColor, heightPt }: Props) {
  return (
    <View style={{
      flexDirection: 'row',
      height: heightPt,
      borderRadius: 4,
      backgroundColor: calendarColor + '33',  // 20% opacity
      overflow: 'hidden',
    }}>
      <View style={{ width: 3, backgroundColor: calendarColor }} />
      <View style={{ paddingHorizontal: 6, paddingVertical: 4, flex: 1 }}>
        <Text style={[typography.blockTitle, { color: calendarColor }]} numberOfLines={1}>{title}</Text>
        {heightPt > 32 && (
          <Text style={[typography.blockTime, { color: calendarColor, opacity: 0.85 }]}>{timeRange}</Text>
        )}
      </View>
    </View>
  );
}
```

### Drawer Row

```tsx
// components/DrawerRow.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  icon?: keyof typeof Ionicons.glyphMap;
  color?: string; // for calendar swatch rows
  label: string;
  isSelected?: boolean;
  onPress: () => void;
};

export function DrawerRow({ icon, color, label, isSelected, onPress }: Props) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 16,
        height: 48, paddingHorizontal: 16,
        backgroundColor: isSelected ? colors.blueTint : 'transparent',
      }}
    >
      {color ? (
        <View style={{ width: 18, height: 18, borderRadius: 2, backgroundColor: color }} />
      ) : (
        icon && <Ionicons name={icon} size={20} color={isSelected ? colors.blue : colors.secondary} style={{ width: 24 }} />
      )}
      <Text style={[typography.sidebar, isSelected && { color: colors.blue }]}>{label}</Text>
    </Pressable>
  );
}
```

### Material Buttons

```tsx
// components/MaterialButton.tsx
import { Pressable, View, Text } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function TextButton({ title, onPress }: { title: string; onPress: () => void }) {
  return (
    <Pressable onPress={onPress} style={({ pressed }) => ({
      paddingVertical: 8, paddingHorizontal: 12,
      opacity: pressed ? 0.7 : 1,
    })}>
      <Text style={[typography.button, { color: colors.blue }]}>{title}</Text>
    </Pressable>
  );
}

export function FilledButton({ title, icon, onPress }: { title: string; icon?: keyof typeof Ionicons.glyphMap; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 8,
        backgroundColor: pressed ? colors.bluePressed : colors.blue,
        paddingVertical: 10, paddingHorizontal: 24,
        borderRadius: 4,
      })}
    >
      {icon && <Ionicons name={icon} size={14} color="#FFFFFF" />}
      <Text style={[typography.button, { color: '#FFFFFF' }]}>{title}</Text>
    </Pressable>
  );
}
```

### RSVP Pills

```tsx
// components/RSVPPills.tsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';
import { colors } from '../theme/colors';

type RSVP = 'yes' | 'no' | 'maybe';

export function RSVPPills() {
  const [selected, setSelected] = useState<RSVP>('yes');
  const options: { id: RSVP; label: string }[] = [
    { id: 'yes',   label: 'Yes' },
    { id: 'no',    label: 'No' },
    { id: 'maybe', label: 'Maybe' },
  ];

  return (
    <View style={{ flexDirection: 'row', gap: 8 }}>
      {options.map((opt) => {
        const isSel = opt.id === selected;
        return (
          <Pressable
            key={opt.id}
            onPress={() => setSelected(opt.id)}
            style={{
              flex: 1, height: 40, borderRadius: 500,
              alignItems: 'center', justifyContent: 'center',
              backgroundColor: isSel ? colors.blue : 'transparent',
              borderWidth: isSel ? 0 : 1, borderColor: colors.divider,
            }}
          >
            <Text style={{ fontSize: 14, fontWeight: '500', color: isSel ? '#FFFFFF' : colors.secondary }}>
              {opt.label}
            </Text>
          </Pressable>
        );
      })}
    </View>
  );
}
```

## 4. Tab Bar (iPad — expo-router)

```tsx
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../../theme/colors';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor:   colors.blue,
        tabBarInactiveTintColor: colors.secondary,
        tabBarLabelStyle: { fontSize: 10, fontWeight: '500', letterSpacing: 0.1 },
        tabBarStyle: { backgroundColor: colors.canvas, borderTopWidth: 0.5, borderTopColor: colors.divider },
      }}
    >
      <Tabs.Screen name="schedule" options={{ title: 'Schedule', tabBarIcon: ({ color }) => <Ionicons name="list-outline"     size={22} color={color} /> }} />
      <Tabs.Screen name="day"      options={{ title: 'Day',      tabBarIcon: ({ color }) => <Ionicons name="today-outline"    size={22} color={color} /> }} />
      <Tabs.Screen name="week"     options={{ title: 'Week',     tabBarIcon: ({ color }) => <Ionicons name="calendar-outline" size={22} color={color} /> }} />
      <Tabs.Screen name="month"    options={{ title: 'Month',    tabBarIcon: ({ color }) => <Ionicons name="grid-outline"     size={22} color={color} /> }} />
    </Tabs>
  );
}
```

## 5. Motion & Haptics

```tsx
// FAB tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Event saved
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);

// RSVP change
Haptics.selectionAsync();

// FAB ripple (concentric expansion)
ripple.value = withSpring(1, { duration: 350 });

// Month day tap (transition zoom to Day view)
scale.value = withSequence(withSpring(1.05, { damping: 8 }), withSpring(1, { damping: 8 }));

// Current time indicator update (every 60s)
useEffect(() => {
  const interval = setInterval(() => {
    yOffset.value = withTiming(computeYForCurrentTime(), { duration: 1000 });
  }, 60_000);
  return () => clearInterval(interval);
}, []);
```

## 6. Icon Library

Use `@expo/vector-icons`. Map to Google Calendar's iconography:

| Purpose | Ionicons / Other |
|---------|------------------|
| FAB plus | `add` |
| Schedule | `list-outline` / `list` |
| Day | `today-outline` / `today` |
| Week | `calendar-outline` / `calendar` |
| Month | `grid-outline` / `grid` |
| Location pin | `location-outline` |
| Reminder | `notifications` |
| Drawer menu | `menu` |
| Settings | `settings-outline` |
| Search | `search` |
| Today (jump button) | `today` |
| Weather sun | `sunny` |
| Google Meet | `videocam` |
| Add guest | `person-add-outline` |
| Add description | `document-text-outline` |
| Color swatch | colored `View` |
| Close | `close` |

## 7. Platform Notes

- **Material dual-shadow on Android**: Android only respects `elevation`. To approximate Material Level 6 on Android, use `elevation: 6` and accept the platform-default shadow rendering. The iOS dual-shadow approach renders correctly via stacked `shadowColor`/`shadowOpacity`/`shadowRadius` props.
- **Tabular numerals**: React Native supports `fontVariant: ['tabular-nums']` on `TextStyle`. Apply this to every component that displays time, date, or event-count text.
- **Status bar**: Set `<StatusBar style="dark" />` on light canvas; switch to `light` in dark mode.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The FAB respects the safe-area bottom — typically 16pt above the home indicator.
- **Drawer navigation**: use `Drawer` from `expo-router` for the hamburger sidebar on iPhone. On iPad, the drawer becomes persistent — switch to a split-view via `react-navigation`'s drawer with `drawerType: 'permanent'`.
- **Bottom sheet for FAB action menu**: use `react-native-bottom-sheet` or the iOS native `Modal` with `presentationStyle: 'pageSheet'` for the Event/Task/Goal picker.
- **Accessibility**: Set `accessibilityRole="button"` on event cards; `accessibilityLabel` should combine title + time + location: `"Stand-up, 9 to 9:30 AM, Conference Room B"`. The FAB needs `accessibilityLabel="Create new event"`.
- **Dark mode**: use `useColorScheme()` to switch the token object. Google Blue brightens to `#8AB4F8` in dark mode for the FAB; event tints in Day view brighten to ~30% opacity.
- **Performance on Month grid**: implement the 42-cell month with `FlatList` `numColumns={7}` and `getItemLayout` for constant-time scrolling; memoize cells with `React.memo` to avoid re-renders on day taps.
