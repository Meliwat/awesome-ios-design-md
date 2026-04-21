# ChatGPT (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates ChatGPT's visual language into paste-ready Expo / React Native code: a design-token module, themed components, and Reanimated snippets.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-linear-gradient`, `expo-clipboard`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts
export const colors = {
  // Canvas
  canvas:        '#FFFFFF',
  darkCanvas:    '#212121',

  // Sidebar
  sidebarLight:  '#F9F9F9',
  sidebarDark:   '#181818',
  sidebarActive: '#ECECEC',
  sidebarActiveDark: '#2F2F2F',
  divider:       '#E5E5E5',
  dividerDark:   '#424242',

  // Text
  textPrimary:       '#0D0D0D',
  textSecondary:     '#676767',
  textTertiary:      '#8E8E8E',
  darkTextPrimary:   '#ECECEC',
  darkTextSecondary: '#B4B4B4',

  // User bubble
  userBubbleLight: '#F7F7F8',
  userBubbleDark:  '#2F2F2F',

  // Code
  codeBlockLight:  '#F7F7F8',
  codeBlockDark:   '#1E1E1E',
  codeInlineLight: '#F0F0F0',
  codeInlineDark:  '#424242',

  // Send button
  sendLight:       '#0D0D0D',
  sendDark:        '#FFFFFF',
  sendDisabled:    '#CCCCCC',
  sendDisabledDark:'#4D4D4D',

  // Semantic
  linkBlue:        '#2A7FFF',
  legacyGreen:     '#10A37F',   // mostly retired
  errorRed:        '#E53E3E',
  warningOrange:   '#D97706',

  // Voice mode sphere gradient
  voiceBlue1:      '#3B82F6',
  voiceBlue2:      '#60A5FA',
  voiceBlue3:      '#93C5FD',
} as const;

export type GPTColor = keyof typeof colors;
```

## 2. Typography

Söhne requires commercial license. Fall back to Inter (SIL OFL) or system (SF Pro on iOS) for free prototyping.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Söhne-Buch':      require('../assets/fonts/Söhne-Buch.otf'),       // Regular
    'Söhne-Kraftig':   require('../assets/fonts/Söhne-Kraftig.otf'),    // Medium
    'Söhne-Halbfett':  require('../assets/fonts/Söhne-Halbfett.otf'),   // Semibold
    // Inter fallback
    'Inter-Regular':  require('../assets/fonts/Inter-Regular.ttf'),
    'Inter-Medium':   require('../assets/fonts/Inter-Medium.ttf'),
    'Inter-Semibold': require('../assets/fonts/Inter-SemiBold.ttf'),
  });
  if (!loaded) return null;
  return <Stack />;
}
```

```ts
// theme/typography.ts
import type { TextStyle } from 'react-native';

const primary = { color: '#0D0D0D' } satisfies TextStyle;
const FAM_REG = 'Söhne-Buch';
const FAM_MED = 'Söhne-Kraftig';
const FAM_SEMI = 'Söhne-Halbfett';

export const typography = {
  body:         { ...primary, fontFamily: FAM_REG,  fontSize: 16, lineHeight: 24 },
  bodyCompact:  { ...primary, fontFamily: FAM_REG,  fontSize: 15, lineHeight: 22 },
  h1:           { ...primary, fontFamily: FAM_SEMI, fontSize: 24, lineHeight: 30, letterSpacing: -0.2 },
  h2:           { ...primary, fontFamily: FAM_SEMI, fontSize: 20, lineHeight: 26, letterSpacing: -0.15 },
  h3:           { ...primary, fontFamily: FAM_SEMI, fontSize: 17, lineHeight: 23, letterSpacing: -0.1 },
  modelChip:    { ...primary, fontFamily: FAM_MED,  fontSize: 14, lineHeight: 14 },
  sidebarTitle: { ...primary, fontFamily: FAM_MED,  fontSize: 15, lineHeight: 20 },
  sidebarSection: { color: '#676767', fontFamily: FAM_MED, fontSize: 12, lineHeight: 12, letterSpacing: 0.2 },
  button:       { ...primary, fontFamily: FAM_MED,  fontSize: 14, lineHeight: 14 },
  meta:         { color: '#676767', fontFamily: FAM_REG, fontSize: 13, lineHeight: 16 },
  placeholder:  { color: '#676767', fontFamily: FAM_REG, fontSize: 16, lineHeight: 22 },
  codeInline:   { ...primary, fontFamily: 'Menlo', fontSize: 14, lineHeight: 18 },
  codeBlock:    { ...primary, fontFamily: 'Menlo', fontSize: 13, lineHeight: 20 },
  link:         { color: '#2A7FFF', fontFamily: FAM_REG, fontSize: 16, lineHeight: 24, textDecorationLine: 'underline' as const },
} satisfies Record<string, TextStyle>;
```

## 3. Signature Components

### Send Button (Black/White Circle)

```tsx
// components/SendButton.tsx
import { Pressable, useColorScheme } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';

export function SendButton({
  isEnabled, isGenerating, onPress,
}: { isEnabled: boolean; isGenerating: boolean; onPress: () => void }) {
  const scheme = useColorScheme();
  const isDark = scheme === 'dark';
  const scale = useSharedValue(1);
  const animStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  const circleColor = !isEnabled && !isGenerating
    ? (isDark ? colors.sendDisabledDark : colors.sendDisabled)
    : (isDark ? colors.sendDark : colors.sendLight);
  const iconColor = isDark ? colors.sendLight : colors.sendDark;

  return (
    <Pressable
      disabled={!isEnabled && !isGenerating}
      onPressIn={() => (scale.value = withSpring(0.94, { damping: 14 }))}
      onPressOut={() => (scale.value = withSpring(1, { damping: 14 }))}
      onPress={() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
        onPress();
      }}
    >
      <Animated.View
        style={[
          {
            width: 32, height: 32, borderRadius: 16,
            alignItems: 'center', justifyContent: 'center',
            backgroundColor: circleColor,
          },
          animStyle,
        ]}
      >
        <Ionicons name={isGenerating ? 'stop' : 'arrow-up'} size={16} color={iconColor} />
      </Animated.View>
    </Pressable>
  );
}
```

### User Message Bubble (Asymmetric Corners)

```tsx
// components/UserMessageBubble.tsx
import { Text, View } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function UserMessageBubble({ text, attachmentName }: { text: string; attachmentName?: string }) {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'flex-end', paddingHorizontal: 16, marginVertical: 8 }}>
      <View style={{ maxWidth: '80%', alignItems: 'flex-end', gap: 8 }}>
        {attachmentName ? (
          <View style={{
            flexDirection: 'row', alignItems: 'center', gap: 8,
            padding: 8,
            backgroundColor: colors.codeBlockLight,
            borderRadius: 8,
          }}>
            <View style={{ width: 40, height: 40, borderRadius: 4, backgroundColor: colors.codeInlineLight }} />
            <Text style={[typography.meta, { maxWidth: 160 }]} numberOfLines={1}>{attachmentName}</Text>
          </View>
        ) : null}
        <View style={{
          paddingHorizontal: 14, paddingVertical: 10,
          backgroundColor: colors.userBubbleLight,
          borderTopLeftRadius: 18,
          borderTopRightRadius: 18,
          borderBottomLeftRadius: 18,
          borderBottomRightRadius: 4,  // asymmetric — the "pointer" corner
        }}>
          <Text style={typography.body}>{text}</Text>
        </View>
      </View>
    </View>
  );
}
```

### Assistant Message (No Bubble, with Feedback Row)

```tsx
// components/AssistantMessage.tsx
import { useState } from 'react';
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function AssistantMessage({
  content, onRegenerate, onCopy, onThumbUp, onThumbDown,
}: {
  content: string;
  onRegenerate: () => void; onCopy: () => void; onThumbUp: () => void; onThumbDown: () => void;
}) {
  const [thumb, setThumb] = useState<'neutral' | 'up' | 'down'>('neutral');

  return (
    <View style={{ paddingHorizontal: 16, paddingVertical: 8, gap: 12 }}>
      <View style={{ flexDirection: 'row', gap: 8 }}>
        <Ionicons name="sparkles" size={18} color={colors.textPrimary} style={{ marginTop: 3 }} />
        <View style={{ flex: 1 }}>
          {/* Replace with a proper Markdown renderer in production */}
          <Text style={typography.body}>{content}</Text>
        </View>
      </View>
      <View style={{ flexDirection: 'row', gap: 4, paddingLeft: 26 }}>
        <FeedbackIconButton icon="refresh" onPress={onRegenerate} />
        <FeedbackIconButton icon="copy-outline" onPress={onCopy} />
        <FeedbackIconButton
          icon={thumb === 'up' ? 'thumbs-up' : 'thumbs-up-outline'}
          onPress={() => { setThumb('up'); onThumbUp(); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); }}
        />
        <FeedbackIconButton
          icon={thumb === 'down' ? 'thumbs-down' : 'thumbs-down-outline'}
          onPress={() => { setThumb('down'); onThumbDown(); Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft); }}
        />
      </View>
    </View>
  );
}

function FeedbackIconButton({ icon, onPress }: { icon: any; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        width: 32, height: 32, borderRadius: 16,
        alignItems: 'center', justifyContent: 'center',
        backgroundColor: pressed ? colors.codeInlineLight : 'transparent',
      })}
    >
      <Ionicons name={icon} size={16} color={colors.textSecondary} />
    </Pressable>
  );
}
```

### Code Block

```tsx
// components/CodeBlock.tsx
import { useState } from 'react';
import { Pressable, ScrollView, Text, View } from 'react-native';
import * as Clipboard from 'expo-clipboard';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CodeBlock({ language, code }: { language: string; code: string }) {
  const [copied, setCopied] = useState(false);

  const onCopy = async () => {
    await Clipboard.setStringAsync(code);
    setCopied(true);
    setTimeout(() => setCopied(false), 1000);
  };

  return (
    <View style={{
      marginHorizontal: 16, marginVertical: 8,
      borderRadius: 8,
      borderWidth: 1, borderColor: colors.divider,
      backgroundColor: colors.codeBlockLight,
      overflow: 'hidden',
    }}>
      <View style={{
        flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center',
        paddingHorizontal: 12, paddingVertical: 8,
        borderBottomWidth: 1, borderBottomColor: colors.divider,
      }}>
        <Text style={{ color: colors.textSecondary, fontFamily: 'Menlo', fontSize: 12 }}>{language}</Text>
        <Pressable onPress={onCopy} style={{ flexDirection: 'row', gap: 4, alignItems: 'center' }}>
          <Ionicons name={copied ? 'checkmark' : 'copy-outline'} size={12} color={colors.textSecondary} />
          <Text style={[typography.button, { color: colors.textSecondary }]}>{copied ? 'Copied!' : 'Copy'}</Text>
        </Pressable>
      </View>
      <ScrollView horizontal showsHorizontalScrollIndicator={false}>
        <Text style={[typography.codeBlock, { padding: 12 }]}>{code}</Text>
      </ScrollView>
    </View>
  );
}
```

### Model Selector Chip

```tsx
// components/ModelChip.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ModelChip({ modelName, onPress }: { modelName: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={({ pressed }) => ({
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingHorizontal: 10, paddingVertical: 6,
        borderRadius: 500,
        borderWidth: 1, borderColor: colors.divider,
        backgroundColor: pressed ? colors.codeInlineLight : 'transparent',
      })}
    >
      <Ionicons name="sparkles" size={14} color={colors.textPrimary} />
      <Text style={typography.modelChip}>{modelName}</Text>
      <Ionicons name="chevron-down" size={10} color={colors.textSecondary} />
    </Pressable>
  );
}
```

### Message Composer

```tsx
// components/Composer.tsx
import { useState } from 'react';
import { Pressable, TextInput, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { SendButton } from './SendButton';

export function Composer({
  onSend, onVoice, onAttach, onWebSearch,
}: {
  onSend: (text: string) => void;
  onVoice: () => void;
  onAttach: () => void;
  onWebSearch: () => void;
}) {
  const [text, setText] = useState('');
  const isEmpty = text.trim().length === 0;

  return (
    <View style={{
      flexDirection: 'row', alignItems: 'flex-end', gap: 8,
      marginHorizontal: 16, marginBottom: 8,
      paddingHorizontal: 8, paddingVertical: 8,
      backgroundColor: colors.canvas,
      borderRadius: 24,
      borderWidth: 1, borderColor: colors.divider,
    }}>
      <Pressable onPress={onAttach} hitSlop={8} style={{ width: 40, height: 40, alignItems: 'center', justifyContent: 'center' }}>
        <Ionicons name="add" size={20} color={colors.textSecondary} />
      </Pressable>

      <TextInput
        value={text}
        onChangeText={setText}
        placeholder="Message ChatGPT…"
        placeholderTextColor={colors.textSecondary}
        multiline
        style={{ flex: 1, paddingVertical: 10, fontFamily: 'Söhne-Buch', fontSize: 16, lineHeight: 22 }}
      />

      {isEmpty ? (
        <View style={{ flexDirection: 'row', gap: 4 }}>
          <Pressable onPress={onWebSearch} hitSlop={8} style={{ width: 40, height: 40, alignItems: 'center', justifyContent: 'center' }}>
            <Ionicons name="globe-outline" size={18} color={colors.textSecondary} />
          </Pressable>
          <Pressable onPress={onVoice} style={{
            width: 32, height: 32, borderRadius: 16,
            alignItems: 'center', justifyContent: 'center',
            borderWidth: 1, borderColor: colors.divider,
          }}>
            <Ionicons name="mic" size={18} color={colors.textPrimary} />
          </Pressable>
        </View>
      ) : (
        <SendButton isEnabled isGenerating={false} onPress={() => { onSend(text); setText(''); }} />
      )}
    </View>
  );
}
```

### Voice Mode (Full-Screen Sphere)

```tsx
// components/VoiceMode.tsx
import { Pressable, Text, View } from 'react-native';
import Animated, { FadeIn, FadeOut, useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { LinearGradient } from 'expo-linear-gradient';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function VoiceMode({ onEnd }: { onEnd: () => void }) {
  const scale = useSharedValue(1);
  scale.value = withRepeat(withTiming(1.05, { duration: 2000 }), -1, true);
  const sphereStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <Animated.View
      entering={FadeIn.duration(400)}
      exiting={FadeOut.duration(300)}
      style={{
        position: 'absolute',
        top: 0, bottom: 0, left: 0, right: 0,
        backgroundColor: '#000',
        alignItems: 'center', justifyContent: 'center',
      }}
    >
      <Animated.View style={[sphereStyle, {
        width: 280, height: 280, borderRadius: 140,
        shadowColor: colors.voiceBlue1, shadowOpacity: 0.4, shadowRadius: 40,
        elevation: 10,
      }]}>
        <LinearGradient
          colors={[colors.voiceBlue3, colors.voiceBlue2, colors.voiceBlue1]}
          style={{ flex: 1, borderRadius: 140 }}
          start={{ x: 0.5, y: 0 }}
          end={{ x: 0.5, y: 1 }}
        />
      </Animated.View>

      <View style={{
        position: 'absolute', left: 24, right: 24, bottom: 48,
        flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between',
      }}>
        <Pressable><Text style={[typography.button, { color: '#FFF' }]}>Mute</Text></Pressable>
        <View><Text style={[typography.meta, { color: 'rgba(255,255,255,0.7)' }]}>ChatGPT is listening…</Text></View>
        <Pressable
          onPress={() => {
            Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
            onEnd();
          }}
          style={{
            width: 44, height: 44, borderRadius: 22,
            backgroundColor: 'rgba(255,255,255,0.2)',
            alignItems: 'center', justifyContent: 'center',
          }}
        >
          <Ionicons name="close" size={20} color="#FFF" />
        </Pressable>
      </View>
    </Animated.View>
  );
}
```

### Sidebar (Conversation History)

```tsx
// components/Sidebar.tsx
import { Pressable, ScrollView, Text, View, TextInput } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Chat = { id: string; title: string };
type Section = { title: string; chats: Chat[] };

export function Sidebar({ sections, activeChatId, onNewChat, onSelectChat }: {
  sections: Section[];
  activeChatId?: string;
  onNewChat: () => void;
  onSelectChat: (id: string) => void;
}) {
  return (
    <View style={{ flex: 1, width: 260, backgroundColor: colors.sidebarLight, padding: 12 }}>
      <Pressable onPress={onNewChat} style={{
        flexDirection: 'row', alignItems: 'center', gap: 10,
        paddingVertical: 10, paddingHorizontal: 12,
        borderRadius: 8, backgroundColor: colors.sidebarActive, marginBottom: 12,
      }}>
        <Ionicons name="create-outline" size={18} color={colors.textPrimary} />
        <Text style={typography.button}>New chat</Text>
      </Pressable>

      <View style={{
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingHorizontal: 10, paddingVertical: 8,
        borderRadius: 8, backgroundColor: colors.sidebarActive, marginBottom: 8,
      }}>
        <Ionicons name="search" size={14} color={colors.textSecondary} />
        <TextInput placeholder="Search chats" placeholderTextColor={colors.textSecondary} style={[typography.meta, { flex: 1 }]} />
      </View>

      <ScrollView>
        {sections.map((section) => (
          <View key={section.title}>
            <Text style={[typography.sidebarSection, { paddingTop: 16, paddingBottom: 4, textTransform: 'uppercase' }]}>{section.title}</Text>
            {section.chats.map((chat) => (
              <Pressable
                key={chat.id}
                onPress={() => onSelectChat(chat.id)}
                style={({ pressed }) => ({
                  paddingHorizontal: 12, paddingVertical: 8,
                  borderRadius: 8,
                  backgroundColor: chat.id === activeChatId ? colors.sidebarActive : pressed ? '#F0F0F0' : 'transparent',
                })}
              >
                <Text style={typography.sidebarTitle} numberOfLines={1}>{chat.title}</Text>
              </Pressable>
            ))}
          </View>
        ))}
      </ScrollView>
    </View>
  );
}
```

### Typing Indicator

```tsx
// components/TypingIndicator.tsx
import { View } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withDelay, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function TypingIndicator() {
  const dots = [0, 1, 2].map(() => useSharedValue(0.3));
  dots.forEach((d, i) => {
    d.value = withDelay(i * 200, withRepeat(withTiming(1, { duration: 600 }), -1, true));
  });

  return (
    <View style={{ flexDirection: 'row', gap: 6, padding: 16 }}>
      {dots.map((d, i) => (
        <Animated.View
          key={i}
          style={[{ width: 8, height: 8, borderRadius: 4, backgroundColor: colors.textSecondary },
                  useAnimatedStyle(() => ({ opacity: d.value }))]}
        />
      ))}
    </View>
  );
}
```

## 4. Motion

```tsx
// Send button press — haptic medium + scale
// Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium)
// scale.value = withSpring(0.94) ... withSpring(1)

// Voice mode entry
// <Animated.View entering={FadeIn.duration(400)} exiting={FadeOut.duration(300)} />
// Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium)

// Voice sphere pulse
// scale.value = withRepeat(withTiming(1.05, { duration: 2000 }), -1, true)

// Thumbs feedback
// Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Soft)

// Streaming text — character-by-character append, no motion
// Use a reducer to incrementally append tokens to message.content
```

## 5. Icon Library

Use `@expo/vector-icons` (Ionicons). Map to ChatGPT's SF Symbol equivalents:

| Purpose | Ionicons |
|---------|----------|
| Send | `arrow-up` |
| Stop | `stop` |
| Voice | `mic` / `waveform-outline` |
| Attach (+) | `add` |
| Web search | `globe-outline` |
| New chat | `create-outline` |
| Sidebar toggle | `menu` |
| Regenerate | `refresh` |
| Copy | `copy-outline` |
| Thumbs up | `thumbs-up-outline` / `thumbs-up` |
| Thumbs down | `thumbs-down-outline` / `thumbs-down` |
| Sparkle | `sparkles` |
| Chevron down | `chevron-down` |
| Close | `close` |
| Search | `search` |
| Settings | `settings-outline` |
| More | `ellipsis-horizontal` |
| Share | `share-outline` |

## 6. Markdown Rendering

Use [react-native-markdown-display](https://github.com/iamacup/react-native-markdown-display) or `react-native-marked` for rendering assistant responses with proper markdown.

```tsx
import Markdown from 'react-native-markdown-display';

<Markdown
  style={{
    body: typography.body,
    heading1: typography.h1,
    heading2: typography.h2,
    heading3: typography.h3,
    code_inline: { ...typography.codeInline, backgroundColor: colors.codeInlineLight, padding: 2, borderRadius: 4 },
    fence: { ...typography.codeBlock, backgroundColor: colors.codeBlockLight, padding: 12, borderRadius: 8 },
    link: typography.link,
  }}
  onLinkPress={(url) => { /* open url */ return false; }}
>
  {assistantContent}
</Markdown>
```

For LaTeX rendering, integrate `react-native-katex` via WebView.

## 7. Platform Notes

- **Söhne licensing**: commercially licensed; acquire from [Klim Type Foundry](https://klim.co.nz/retail-fonts/soehne/) before shipping. For prototypes, Inter is a close free substitute
- **Streaming**: implement via Server-Sent Events (SSE) or WebSocket; incrementally append tokens to message content; avoid any entrance animation for streamed text — it should just appear
- **Status bar**: `<StatusBar style="dark" />` in light mode, `"light"` in dark; switch to `"light"` when voice mode is active (white-on-black is needed over blue gradient)
- **Safe area**: wrap screens in `SafeAreaView`; compose field needs bottom safe-area padding; voice mode should ignore safe area (full-bleed)
- **Keyboard**: use `KeyboardAvoidingView` with `behavior="padding"` so compose rises with keyboard; chat scroll stays anchored to bottom
- **Voice mode audio**: configure `Audio.setAudioModeAsync({ allowsRecordingIOS: true, playsInSilentModeIOS: true })` from `expo-av`; handle mic permissions with `Audio.requestPermissionsAsync()`
- **Dynamic Type**: React Native respects system scale; set `allowFontScaling={false}` on model chip, sidebar section headers, code block labels, copy-button text
- **Dark mode**: use `useColorScheme()` to swap tokens; the redesign leans heavily on true-black (`#212121`) canvas on dark
- **Accessibility**: label user messages as "You: {content}"; assistant as "ChatGPT: {content}"; voice mode as "Voice mode active, ChatGPT is listening"
- **Haptics discipline**: medium on send + voice entry; soft on thumbs, copy, regenerate; avoid chaining haptics (one per user action)
- **Long-running voice mode**: keep audio session active across backgrounding with proper `UIBackgroundModes` ('audio') in `Info.plist`
- **Token counter**: consider a small `13pt #676767` counter in the top-right of the chat ("Used 2.3k tokens") — shown for Pro users
