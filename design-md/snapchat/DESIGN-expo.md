# Snapchat (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Snapchat's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas
  canvas:    '#000000',
  surface1:  '#1A1A1A',
  surface2:  '#2C2C2C',
  divider:   '#333333',

  // Canvas (light, limited)
  lightCanvas:   '#FFFFFF',
  lightSurface1: '#F2F2F2',

  // Text
  textPrimary:        '#FFFFFF',
  textPrimaryLight:   '#000000',
  textSecondary:      '#8A8A8F',
  textTertiary:       '#555555',

  // Brand
  yellow:        '#FFFC00',
  yellowPressed: '#E6E300',

  // Snap types
  photoRed:    '#FF2E3D',
  videoPurple: '#9B51FF',
  chatBlue:    '#4DA7FF',
  audioGreen:  '#4CD964',

  // Semantic
  errorRed:     '#FF3B30',
  successGreen: '#00D873',
  liveRed:      '#FF2E3D',
} as const;

export type SnapColor = keyof typeof colors;
```

## 2. Typography

Avenir Next ships with iOS; on Android it's not available natively and you'll need to either bundle it or fall back to a geometric humanist alternative like `Nunito`.

```tsx
// app/_layout.tsx — only needed on Android or if you want to self-host
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'AvenirNext-Medium': require('../assets/fonts/AvenirNext-Medium.ttf'),
    'AvenirNext-Bold':   require('../assets/fonts/AvenirNext-Bold.ttf'),
    'AvenirNext-Heavy':  require('../assets/fonts/AvenirNext-Heavy.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const base = { color: '#FFFFFF' } satisfies TextStyle;

export const typography = {
  splashTitle:    { ...base, fontFamily: 'AvenirNext-Heavy',  fontSize: 48, lineHeight: 48, letterSpacing: -0.5 },
  screenTitle:    { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 24, lineHeight: 28 },
  sectionHeader:  { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 20, lineHeight: 24 },
  chatRowName:    { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 16, lineHeight: 20 },
  chatMessage:    { ...base, fontFamily: 'AvenirNext-Medium', fontSize: 16, lineHeight: 21 },
  chatStatus:     { fontFamily: 'AvenirNext-Medium', fontSize: 13, lineHeight: 16, color: '#8A8A8F' },
  storyName:      { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 14, lineHeight: 17 },
  timestamp:      { fontFamily: 'AvenirNext-Medium', fontSize: 12, lineHeight: 14, color: '#8A8A8F' },
  streakCount:    { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 18, lineHeight: 18 },
  hudLabel:       { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 13, lineHeight: 13, letterSpacing: 0.3, textTransform: 'uppercase' as const },
  button:         { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 16, lineHeight: 16, letterSpacing: 0.2 },
  lensLabel:      { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 13, lineHeight: 13, letterSpacing: 0.3, textTransform: 'uppercase' as const },
  bitmojiCallout: { ...base, fontFamily: 'AvenirNext-Medium', fontSize: 14, lineHeight: 18 },
  spotlightCap:   { ...base, fontFamily: 'AvenirNext-Medium', fontSize: 14, lineHeight: 18 },
  discoverTitle:  { ...base, fontFamily: 'AvenirNext-Bold',   fontSize: 16, lineHeight: 19 },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Snap Capture Button

```tsx
// components/CaptureButton.tsx
import { Pressable, View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring, withTiming } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Svg, { Circle as SvgCircle } from 'react-native-svg';
import { colors } from '../theme/colors';

const AnimatedCircle = Animated.createAnimatedComponent(SvgCircle);

type Props = {
  isRecording: boolean;
  recordProgress: number; // 0...1
  onPhoto: () => void;
  onVideoStart: () => void;
  onVideoStop: () => void;
  onFlip: () => void;
};

export function CaptureButton({ isRecording, recordProgress, onPhoto, onVideoStart, onVideoStop, onFlip }: Props) {
  const innerScale = useSharedValue(1);
  const progress = useSharedValue(0);

  // Sync progress prop with shared value
  progress.value = withTiming(recordProgress, { duration: 100 });

  const innerStyle = useAnimatedStyle(() => ({ transform: [{ scale: innerScale.value }] }));

  const lastTap = { current: 0 };

  const handlePress = () => {
    const now = Date.now();
    if (now - lastTap.current < 300) {
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
      onFlip();
    } else {
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);
      innerScale.value = withSequence(withSpring(0.92, { damping: 10 }), withSpring(1, { damping: 10 }));
      onPhoto();
    }
    lastTap.current = now;
  };

  const handleLongPress = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    onVideoStart();
  };

  const circumference = 2 * Math.PI * 38; // radius 38 for 82pt ring
  const progressProps = useAnimatedStyle(() => ({
    strokeDashoffset: circumference * (1 - progress.value),
  }));

  return (
    <Pressable
      onPress={handlePress}
      onLongPress={handleLongPress}
      onPressOut={() => isRecording && onVideoStop()}
      delayLongPress={350}
      style={{ width: 82, height: 82, alignItems: 'center', justifyContent: 'center' }}
    >
      {/* Outer yellow ring */}
      <Svg width={82} height={82} style={{ position: 'absolute' }}>
        <SvgCircle cx={41} cy={41} r={38} stroke={colors.yellow} strokeWidth={6} fill="none" />
        {isRecording && (
          <AnimatedCircle
            cx={41} cy={41} r={38}
            stroke={colors.yellow}
            strokeWidth={6}
            fill="none"
            strokeDasharray={circumference}
            strokeLinecap="round"
            transform={`rotate(-90 41 41)`}
            // @ts-ignore strokeDashoffset is an animated prop
            animatedProps={progressProps}
          />
        )}
      </Svg>

      {/* Inner circle */}
      <Animated.View
        style={[
          {
            width: 64, height: 64, borderRadius: 32,
            backgroundColor: isRecording ? colors.yellow : '#FFFFFF',
          },
          innerStyle,
        ]}
      />
    </Pressable>
  );
}
```

### Chat Inbox Row

```tsx
// components/ChatRow.tsx
import { Image, Pressable, StyleSheet, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export type SnapType = 'photo' | 'video' | 'chat' | 'audio' | 'none';
export type Direction = 'incoming' | 'outgoing';

const SNAP_COLORS: Record<SnapType, string> = {
  photo: colors.photoRed,
  video: colors.videoPurple,
  chat:  colors.chatBlue,
  audio: colors.audioGreen,
  none:  colors.textSecondary,
};

type Props = {
  name: string;
  bitmojiUri: string;
  status: string;
  timestamp: string;
  snapType: SnapType;
  direction: Direction;
  isUnread: boolean;
  streakDays?: number;
  onPress?: () => void;
};

export function ChatRow(p: Props) {
  return (
    <Pressable onPress={p.onPress} style={styles.row}>
      <Image source={{ uri: p.bitmojiUri }} style={styles.bitmoji} />
      <View style={{ flex: 1, gap: 4 }}>
        <Text style={typography.chatRowName}>{p.name}</Text>
        <View style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
          {p.snapType !== 'none' && <SnapIndicator type={p.snapType} direction={p.direction} unread={p.isUnread} />}
          <Text style={typography.chatStatus}>{p.status}</Text>
        </View>
      </View>
      <View style={{ alignItems: 'flex-end', gap: 4 }}>
        <Text style={typography.timestamp}>{p.timestamp}</Text>
        {p.streakDays != null && (
          <Text>
            <Text>🔥</Text>
            <Text style={typography.streakCount}>{` ${p.streakDays}`}</Text>
          </Text>
        )}
      </View>
    </Pressable>
  );
}

function SnapIndicator({ type, direction, unread }: { type: SnapType; direction: Direction; unread: boolean }) {
  const color = SNAP_COLORS[type];
  return (
    <View style={{
      width: 16, height: 16, borderRadius: 2,
      backgroundColor: unread ? color : 'transparent',
      borderWidth: 1.5, borderColor: color,
      alignItems: 'center', justifyContent: 'center',
    }}>
      <Ionicons name={direction === 'incoming' ? 'arrow-down' : 'arrow-up'} size={8} color={unread ? '#FFFFFF' : color} />
    </View>
  );
}

const styles = StyleSheet.create({
  row:     { flexDirection: 'row', alignItems: 'center', paddingHorizontal: 16, height: 72, gap: 12,
             backgroundColor: colors.canvas,
             borderBottomWidth: StyleSheet.hairlineWidth, borderBottomColor: colors.divider },
  bitmoji: { width: 48, height: 48, borderRadius: 24, backgroundColor: colors.surface1 },
});
```

### Story Thumbnail

```tsx
// components/StoryThumb.tsx
import { Image, View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { useEffect } from 'react';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type State = 'unread' | 'read' | 'live';

export function StoryThumb({ creatorName, bitmojiUri, previewUri, state }:
  { creatorName: string; bitmojiUri: string; previewUri: string; state: State }) {
  const ringOpacity = useSharedValue(1);

  useEffect(() => {
    if (state === 'unread') {
      ringOpacity.value = withRepeat(withTiming(0.7, { duration: 2000 }), -1, true);
    }
  }, [state]);

  const ringStyle = useAnimatedStyle(() => ({ opacity: ringOpacity.value }));

  const ringColor = state === 'unread' ? colors.yellow : state === 'live' ? colors.liveRed : colors.textTertiary;

  return (
    <View style={{ width: 120, height: 200 }}>
      <Image source={{ uri: previewUri }} style={{ width: 120, height: 200, borderRadius: 16 }} />
      <Animated.View style={[
        { position: 'absolute', inset: 0, borderRadius: 16, borderWidth: 3, borderColor: ringColor },
        ringStyle,
      ]} />
      <Image
        source={{ uri: bitmojiUri }}
        style={{ position: 'absolute', top: 8, left: 8, width: 40, height: 40, borderRadius: 20, borderWidth: 2, borderColor: '#FFFFFF' }}
      />
      <View style={{ position: 'absolute', bottom: 10, left: 10 }}>
        <Text style={[typography.storyName, { textShadowColor: 'rgba(0,0,0,0.4)', textShadowOffset: { width: 0, height: 1 }, textShadowRadius: 2 }]}>
          {creatorName}
        </Text>
      </View>
    </View>
  );
}
```

### Chat Bubble

```tsx
// components/ChatBubble.tsx
import { View, Text } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatBubble({ text, sender }: { text: string; sender: 'me' | 'them' }) {
  const isMe = sender === 'me';
  return (
    <View style={{
      flexDirection: 'row',
      paddingHorizontal: 16,
      justifyContent: isMe ? 'flex-end' : 'flex-start',
    }}>
      <View style={{
        maxWidth: '75%',
        paddingVertical: 10,
        paddingHorizontal: 14,
        borderRadius: 20,
        backgroundColor: isMe ? colors.surface2 : colors.surface1,
      }}>
        <Text style={typography.chatMessage}>{text}</Text>
      </View>
    </View>
  );
}
```

## 4. Snapchat-Specific Feature: Camera HUD

```tsx
// app/(tabs)/camera.tsx
import { View, Pressable, Text } from 'react-native';
import { useState } from 'react';
import Ionicons from '@expo/vector-icons/Ionicons';
import { CameraView } from 'expo-camera'; // optional — can stub with a Color View
import { CaptureButton } from '../../components/CaptureButton';
import { colors } from '../../theme/colors';

export default function CameraScreen() {
  const [flashOn, setFlashOn] = useState(false);
  const [isRecording, setIsRecording] = useState(false);
  const [progress, setProgress] = useState(0);

  return (
    <View style={{ flex: 1, backgroundColor: colors.canvas }}>
      {/* Live viewfinder */}
      <CameraView style={{ flex: 1 }} facing="front" />

      {/* Top HUD */}
      <View style={{ position: 'absolute', top: 16, left: 16, right: 16, flexDirection: 'row', gap: 8 }}>
        <HUDIcon icon="person-circle" />
        <View style={{ flex: 1 }} />
        <HUDIcon icon={flashOn ? 'flash' : 'flash-off'} tint={flashOn ? colors.yellow : '#FFFFFF'}
                 onPress={() => setFlashOn(!flashOn)} />
        <HUDIcon icon="camera-reverse" />
        <HUDIcon icon="search" />
      </View>

      {/* Bottom HUD */}
      <View style={{ position: 'absolute', bottom: 32, left: 0, right: 0, flexDirection: 'row', alignItems: 'center', justifyContent: 'center', gap: 32 }}>
        <HUDIcon icon="images" size={32} />
        <CaptureButton
          isRecording={isRecording}
          recordProgress={progress}
          onPhoto={() => console.log('photo')}
          onVideoStart={() => setIsRecording(true)}
          onVideoStop={() => setIsRecording(false)}
          onFlip={() => console.log('flip')}
        />
        <HUDIcon icon="chatbubble" size={32} />
      </View>
    </View>
  );
}

function HUDIcon({ icon, size = 28, tint = '#FFFFFF', onPress }: any) {
  return (
    <Pressable onPress={onPress} style={{ width: 44, height: 44, alignItems: 'center', justifyContent: 'center' }}>
      <Ionicons name={icon} size={size} color={tint} style={{ textShadowColor: 'rgba(0,0,0,0.4)', textShadowOffset: { width: 0, height: 1 }, textShadowRadius: 2 }} />
    </Pressable>
  );
}
```

## 5. Tab Bar / Swipe Navigation

Snapchat uses 5-screen horizontal swipe navigation rather than a traditional tab bar. Use `FlatList` with `pagingEnabled` or a custom swipe container with `react-native-pager-view`:

```tsx
// app/index.tsx  (root screen, not inside /(tabs))
import PagerView from 'react-native-pager-view';
import MapScreen from '../screens/Map';
import ChatScreen from '../screens/Chat';
import CameraScreen from '../screens/Camera';
import StoriesScreen from '../screens/Stories';
import SpotlightScreen from '../screens/Spotlight';
import { useState } from 'react';
import { View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export default function Root() {
  const [page, setPage] = useState(2); // Camera is center default

  return (
    <View style={{ flex: 1 }}>
      <PagerView
        style={{ flex: 1 }}
        initialPage={2}
        onPageSelected={e => setPage(e.nativeEvent.position)}
      >
        <MapScreen      key="0" />
        <ChatScreen     key="1" />
        <CameraScreen   key="2" />
        <StoriesScreen  key="3" />
        <SpotlightScreen key="4" />
      </PagerView>
      <NavIndicator selected={page} />
    </View>
  );
}

function NavIndicator({ selected }: { selected: number }) {
  const icons = ['map', 'chatbubble', 'camera', 'play-circle', 'diamond'] as const;
  return (
    <View style={{ position: 'absolute', bottom: 0, left: 0, right: 0, height: 56,
                   flexDirection: 'row', backgroundColor: 'rgba(0,0,0,0.5)' }}>
      {icons.map((icon, i) => (
        <View key={icon} style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
          <Ionicons name={icon} size={28} color={i === selected ? '#FFFFFF' : colors.textTertiary} />
        </View>
      ))}
    </View>
  );
}
```

## 6. Motion

```tsx
// Capture tap
import * as Haptics from 'expo-haptics';
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft);

// Capture long-press (video)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Camera flip
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Screen swipe nav — handled by PagerView natively

// Streak save animation
import Animated, { useSharedValue, useAnimatedStyle, withSequence, withSpring } from 'react-native-reanimated';
const flameScale = useSharedValue(1);
const onStreak = () => {
  flameScale.value = withSequence(withSpring(1.3, { damping: 8 }), withSpring(1, { damping: 10 }));
  Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
};

// Story ring pulse — see StoryThumb component above (withRepeat on opacity)

// Bitmoji sticker pop-in
const stickerScale = useSharedValue(0);
useEffect(() => {
  stickerScale.value = withSpring(1, { damping: 12 });
}, []);
```

## 7. Icon Library

Use `@expo/vector-icons` — ships with Ionicons. Snapchat uses custom glyphs in production, but Ionicons provide close approximations:

| Purpose | Ionicons |
|---------|----------|
| Camera | `camera` / `camera-outline` |
| Chat | `chatbubble` / `chatbubble-outline` |
| Map | `map` / `map-outline` |
| Stories | `play-circle` / `play-circle-outline` |
| Spotlight | `diamond` / `diamond-outline` |
| Memories | `images` |
| Flash on | `flash` |
| Flash off | `flash-off` |
| Camera flip | `camera-reverse` |
| Search | `search` |
| Send | `send` or `paper-plane` |
| New chat | `create` |
| Profile | `person-circle` |
| Snap incoming (arrow down) | `arrow-down` |
| Snap outgoing (arrow up) | `arrow-up` |
| Microphone | `mic` |
| Sticker | `happy` |
| Streak flame | 🔥 (emoji character) |
| Best friends | 💛 (emoji character) |
| Live | `radio` + text "LIVE" |

For authentic yellow ghost logo and bitmoji avatars, use SVG files (Snapchat's brand toolkit provides vector assets) rather than icon fonts.

## 8. Platform Notes

- **Camera access**: `expo-camera` is required for the live viewfinder — add to `app.json` with permissions: `"expo": { "ios": { "infoPlist": { "NSCameraUsageDescription": "Take snaps" } } }`
- **Avenir Next**: system-shipped on iOS; on Android you'll need to bundle the font or fall back to Nunito. Check with a conditional font check via `expo-font` on Android builds
- **Status bar**: set `<StatusBar style="light" />` globally — all screens are dark canvas or camera overlay
- **Safe area**: wrap screens (except camera) in `SafeAreaView`. Camera explicitly IGNORES safe area — it's edge-to-edge — but camera HUD icons respect it via manual inset (`paddingTop: insets.top + 16`)
- **Dynamic Type**: set `allowFontScaling={false}` on HUD labels, streak counts, timestamps, lens labels to preserve tight camera-overlay layouts
- **Accessibility**: camera HUD icons ship with drop-shadow for legibility; for VoiceOver users expose a "High contrast HUD" toggle that adds semi-transparent pill backgrounds behind floating icons
- **5-screen swipe nav**: `react-native-pager-view` is the cleanest implementation; alternative is a horizontal `FlatList` with `pagingEnabled={true}` and `showsHorizontalScrollIndicator={false}`
- **Bitmoji integration**: Snapchat's bitmoji API is proprietary and not available for third-party apps. For inspiration packs, use placeholder cartoon avatars from services like DiceBear (`https://api.dicebear.com/...`) or bundle stock illustrations
- **Ghost logo**: render as an SVG with `react-native-svg` — the yellow ghost shape can be approximated with a path; use the official asset from Snap's brand toolkit for production
- **Snap type color-coding**: critical to keep the four colors (red photo, purple video, blue chat, green audio) consistent — this is the app's inbox-at-a-glance signal
- **Haptics on record**: `expo-haptics` doesn't support repeating haptics, so use `setInterval` to fire `Haptics.impactAsync(Soft)` every 1000ms while recording (clean it up on stop)
- **Recording progress ring**: use `react-native-svg` with `<Circle>` and an animated `strokeDasharray` / `strokeDashoffset` for the yellow clockwise fill — see `CaptureButton` above
