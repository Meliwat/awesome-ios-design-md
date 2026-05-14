# Claude (iOS) — Expo / React Native Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Claude's visual language into paste-ready Expo / React Native code: a design-token module, themed components for the assistant message block, the chat input, code blocks, an SVG asterisk-star logomark, and a Reanimated streaming cursor.

Assumes Expo SDK 51+ with `expo-router`, `expo-font`, `expo-haptics`, `expo-blur`, `react-native-svg`, `react-native-markdown-display`, and `react-native-reanimated` v3.

## 1. Color Tokens

```ts
// theme/colors.ts — Claude brand palette (warm cream paper, warm ink, terracotta orange)
export const colors = {
  // Canvas — the Claude paper
  cream:         '#F8F4ED',  // primary canvas
  paper:         '#FBF9F4',  // elevated surfaces (input, code outer)
  surface1:      '#F0EAE0',  // user pill, soft callouts
  surface2:      '#E8E0D2',  // pressed states, chips
  sand:          '#DDD2BD',  // hairline dividers

  // Text (warm ink, never pure black)
  ink:           '#2D2520',  // primary text
  graphite:      '#5A4F44',  // secondary text
  stone:         '#8A7E72',  // tertiary text
  bone:          '#B5AB9E',  // disabled

  // Claude Orange — the signature accent
  orange:        '#D97757',
  orangePressed: '#BE6242',
  orangeSoft:    '#F2DDD0',
  orangeSoftDk:  '#4A352A',

  // Code & syntax (warm palette)
  codeBg:        '#1F1B16',
  codeFg:        '#E8E0D2',
  syntaxKey:     '#D97757',  // keywords
  syntaxString:  '#7FB069',  // strings (sage)
  syntaxNum:     '#E8B96F',  // numbers (gold)
  syntaxFunc:    '#9DA4F2',  // functions (periwinkle)
  syntaxCmt:     '#8A7E72',  // comments

  // Semantic
  success:       '#6B9D5E',
  warning:       '#D49952',
  error:         '#C16654',
  info:          '#5A6273',

  // Dark mode (warm dark)
  darkCanvas:    '#1F1B16',
  darkSurface:   '#2A2520',
  darkSurface2:  '#3A332C',
  darkDivider:   '#3A332C',
  darkText:      '#E8E0D2',
  darkTextSec:   '#B5AB9E',

  // Shadow tokens (warm-tinted)
  shadowSubtle:  'rgba(40, 30, 20, 0.04)',
  shadowCard:    'rgba(40, 30, 20, 0.06)',
  shadowFloat:   'rgba(40, 30, 20, 0.10)',
  shadowOverlay: 'rgba(40, 30, 20, 0.14)',
} as const;

export type ClaudeColor = keyof typeof colors;
```

## 2. Typography

Tiempos and Styrene are proprietary (Klim Type Foundry). Bundle via `expo-font`. Fall back to Source Serif Pro + Inter — the closest open-source pairing.

```tsx
// app/_layout.tsx
import { useFonts } from 'expo-font';

export default function Root() {
  const [loaded] = useFonts({
    'Tiempos-Text':            require('../assets/fonts/Tiempos-Text-Regular.ttf'),
    'Tiempos-Text-Italic':     require('../assets/fonts/Tiempos-Text-Italic.ttf'),
    'Tiempos-Text-Semibold':   require('../assets/fonts/Tiempos-Text-Semibold.ttf'),
    'Tiempos-Headline':        require('../assets/fonts/Tiempos-Headline-Semibold.ttf'),
    'Styrene-Regular':         require('../assets/fonts/Styrene-Regular.ttf'),
    'Styrene-Medium':          require('../assets/fonts/Styrene-Medium.ttf'),
    'Styrene-Semibold':        require('../assets/fonts/Styrene-Semibold.ttf'),
    'JetBrainsMono-Regular':   require('../assets/fonts/JetBrainsMono-Regular.ttf'),
    'JetBrainsMono-Medium':    require('../assets/fonts/JetBrainsMono-Medium.ttf'),
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
  // Display
  display:       { color: colors.ink,      fontFamily: 'Tiempos-Headline',     fontSize: 32, lineHeight: 38, letterSpacing: -0.4 },
  convTitle:     { color: colors.ink,      fontFamily: 'Styrene-Semibold',     fontSize: 18, lineHeight: 22, letterSpacing: -0.1 },

  // Markdown headings inside assistant messages
  h1:            { color: colors.ink,      fontFamily: 'Tiempos-Headline',     fontSize: 24, lineHeight: 31, letterSpacing: -0.2 },
  h2:            { color: colors.ink,      fontFamily: 'Tiempos-Headline',     fontSize: 20, lineHeight: 26, letterSpacing: -0.1 },
  h3:            { color: colors.ink,      fontFamily: 'Tiempos-Headline',     fontSize: 17, lineHeight: 23 },

  // Assistant body (the most-rendered text)
  body:          { color: colors.ink,      fontFamily: 'Tiempos-Text',         fontSize: 16, lineHeight: 25 },
  bodyBold:      { color: colors.ink,      fontFamily: 'Tiempos-Text-Semibold', fontSize: 16, lineHeight: 25 },
  bodyItalic:    { color: colors.ink,      fontFamily: 'Tiempos-Text-Italic',  fontSize: 16, lineHeight: 25, fontStyle: 'italic' },
  blockquote:    { color: colors.graphite, fontFamily: 'Tiempos-Text-Italic',  fontSize: 16, lineHeight: 25 },

  // User & UI chrome
  user:          { color: colors.ink,      fontFamily: 'Styrene-Regular',      fontSize: 16, lineHeight: 24 },
  action:        { color: colors.ink,      fontFamily: 'Styrene-Semibold',     fontSize: 15, lineHeight: 19 },
  chip:          { color: colors.ink,      fontFamily: 'Styrene-Medium',       fontSize: 13, lineHeight: 17 },
  meta:          { color: colors.graphite, fontFamily: 'Styrene-Regular',      fontSize: 12, lineHeight: 16 },
  caption:       { color: colors.stone,    fontFamily: 'Styrene-Regular',      fontSize: 11, lineHeight: 14 },
  senderLabel:   { color: colors.graphite, fontFamily: 'Styrene-Medium',       fontSize: 13, lineHeight: 17 },
  groupHeader:   { color: colors.stone,    fontFamily: 'Styrene-Bold',         fontSize: 11, lineHeight: 14, letterSpacing: 0.4 },

  // Code
  codeInline:    { color: colors.ink,      fontFamily: 'JetBrainsMono-Medium', fontSize: 14, lineHeight: 20 },
  codeBlock:     { color: colors.codeFg,   fontFamily: 'JetBrainsMono-Regular', fontSize: 14, lineHeight: 21 },
  codeLang:      { color: colors.stone,    fontFamily: 'JetBrainsMono-Regular', fontSize: 11, lineHeight: 14 },
} satisfies Record<string, TextStyle>;
```

## 3. The Asterisk-Star Logomark (SVG)

```tsx
// components/ClaudeMark.tsx
import Svg, { Path } from 'react-native-svg';
import { colors } from '../theme/colors';

export function ClaudeMark({ size = 18, color = colors.orange }: { size?: number; color?: string }) {
  // 6 narrow petals radiating from center, 60° apart
  const cx = size / 2;
  const cy = size / 2;
  const r = size / 2;
  const inner = r * 0.18;

  const petals = Array.from({ length: 6 }).map((_, i) => {
    const angle = (Math.PI / 3) * i - Math.PI / 2;
    const tip = [cx + r * Math.cos(angle), cy + r * Math.sin(angle)] as const;
    const lAngle = angle + Math.PI / 2;
    const lBase = [cx + inner * Math.cos(lAngle), cy + inner * Math.sin(lAngle)] as const;
    const rBase = [cx - inner * Math.cos(lAngle), cy - inner * Math.sin(lAngle)] as const;
    return `M ${lBase[0]} ${lBase[1]} Q ${(lBase[0] + tip[0]) / 2 + 1} ${(lBase[1] + tip[1]) / 2 + 1} ${tip[0]} ${tip[1]} Q ${(tip[0] + rBase[0]) / 2 - 1} ${(tip[1] + rBase[1]) / 2 - 1} ${rBase[0]} ${rBase[1]} Z`;
  });

  return (
    <Svg width={size} height={size}>
      {petals.map((d, i) => (<Path key={i} d={d} fill={color} />))}
    </Svg>
  );
}
```

## 4. Signature Components

### Assistant Message Block

```tsx
// components/AssistantMessage.tsx
import { View, Text } from 'react-native';
import Markdown from 'react-native-markdown-display';
import { ClaudeMark } from './ClaudeMark';
import { StreamingCursor } from './StreamingCursor';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Props = {
  modelName: string;
  content: string;     // raw markdown
  isStreaming: boolean;
};

export function AssistantMessage({ modelName, content, isStreaming }: Props) {
  return (
    <View style={{ paddingHorizontal: 4, gap: 12 }}>
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <ClaudeMark size={18} />
        <Text style={typography.senderLabel}>Claude</Text>
        <Text style={[typography.chip, { color: colors.stone }]}>· {modelName}</Text>
      </View>

      <Markdown
        style={{
          body:       typography.body,
          heading1:   typography.h1,
          heading2:   typography.h2,
          heading3:   typography.h3,
          em:         typography.bodyItalic,
          strong:     typography.bodyBold,
          blockquote: { ...typography.blockquote, borderLeftWidth: 3, borderLeftColor: colors.sand, paddingLeft: 12 },
          bullet_list_icon: { color: colors.ink },
          ordered_list_icon: { color: colors.ink },
          code_inline: { ...typography.codeInline, backgroundColor: colors.surface1, paddingHorizontal: 4, borderRadius: 4 },
        }}
      >
        {content}
      </Markdown>

      {isStreaming && <StreamingCursor />}
    </View>
  );
}
```

### Streaming Cursor

```tsx
// components/StreamingCursor.tsx
import { useEffect } from 'react';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming } from 'react-native-reanimated';
import { colors } from '../theme/colors';

export function StreamingCursor() {
  const opacity = useSharedValue(1);

  useEffect(() => {
    opacity.value = withRepeat(
      withTiming(0, { duration: 300 }),
      -1, true,
    );
  }, []);

  const style = useAnimatedStyle(() => ({ opacity: opacity.value }));

  return (
    <Animated.View
      style={[
        { width: 8, height: 18, borderRadius: 1, backgroundColor: colors.orange },
        style,
      ]}
      accessibilityLabel="Claude is responding"
    />
  );
}
```

### User Message Pill

```tsx
// components/UserMessage.tsx
import { View, Text, Dimensions } from 'react-native';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

const MAX_W = Dimensions.get('window').width * 0.8;

export function UserMessage({ text }: { text: string }) {
  return (
    <View style={{ flexDirection: 'row', justifyContent: 'flex-end', paddingHorizontal: 4 }}>
      <View
        style={{
          maxWidth: MAX_W,
          backgroundColor: colors.surface1,
          paddingVertical: 12, paddingHorizontal: 16,
          borderRadius: 18,
        }}
      >
        <Text style={typography.user}>{text}</Text>
      </View>
    </View>
  );
}
```

### Chat Input

```tsx
// components/ChatInput.tsx
import { useState } from 'react';
import { View, TextInput, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import * as Haptics from 'expo-haptics';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ChatInput({ onSend, onAttach }: { onSend: (text: string) => void; onAttach: () => void }) {
  const [text, setText] = useState('');
  const [focused, setFocused] = useState(false);
  const canSend = text.trim().length > 0;

  return (
    <View
      style={{
        flexDirection: 'row', alignItems: 'flex-end', gap: 8,
        marginHorizontal: 16, marginBottom: 8,
        paddingHorizontal: 16, paddingVertical: 6,
        backgroundColor: colors.paper,
        borderRadius: 24,
        borderWidth: focused ? 1.5 : 1,
        borderColor: focused ? colors.orange : colors.sand,
      }}
    >
      <Pressable onPress={onAttach} style={{ paddingBottom: 14 }}>
        <Ionicons name="add-circle-outline" size={24} color={colors.graphite} />
      </Pressable>

      <TextInput
        value={text}
        onChangeText={setText}
        placeholder="Reply to Claude…"
        placeholderTextColor={colors.stone}
        multiline
        onFocus={() => setFocused(true)}
        onBlur={() => setFocused(false)}
        style={{ ...typography.user, flex: 1, minHeight: 40, maxHeight: 160, paddingVertical: 12 }}
      />

      <Pressable
        onPress={() => {
          if (!canSend) return;
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
          onSend(text);
          setText('');
        }}
        disabled={!canSend}
        style={({ pressed }) => ({
          width: 40, height: 40, borderRadius: 20,
          backgroundColor: canSend ? colors.orange : colors.surface2,
          alignItems: 'center', justifyContent: 'center',
          marginBottom: 6,
          transform: [{ scale: pressed ? 0.94 : 1 }],
        })}
      >
        <Ionicons name="arrow-up" size={16} color={canSend ? colors.paper : colors.stone} />
      </Pressable>
    </View>
  );
}
```

### Model Picker Chip + Sheet

```tsx
// components/ModelPickerChip.tsx
import { Pressable, Text, View } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { ClaudeMark } from './ClaudeMark';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ModelPickerChip({ modelName, onPress }: { modelName: string; onPress: () => void }) {
  return (
    <Pressable
      onPress={onPress}
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 6,
        paddingVertical: 6, paddingHorizontal: 12,
        backgroundColor: colors.surface1, borderRadius: 16,
        alignSelf: 'flex-start',
      }}
    >
      <ClaudeMark size={14} />
      <Text style={typography.chip}>{modelName}</Text>
      <Ionicons name="chevron-down" size={10} color={colors.stone} />
    </Pressable>
  );
}
```

```tsx
// components/ModelPickerSheet.tsx
import { View, Text, Pressable, Modal } from 'react-native';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { ClaudeMark } from './ClaudeMark';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Model = { name: string; subtitle: string; available: boolean };

export function ModelPickerSheet({
  visible, models, selectedName, onSelect, onClose,
}: { visible: boolean; models: Model[]; selectedName: string; onSelect: (name: string) => void; onClose: () => void }) {
  return (
    <Modal visible={visible} transparent animationType="slide">
      <View style={{ flex: 1, justifyContent: 'flex-end', backgroundColor: 'rgba(0,0,0,0.4)' }}>
        <View style={{ backgroundColor: colors.paper, borderTopLeftRadius: 24, borderTopRightRadius: 24, paddingBottom: 24 }}>
          <View style={{ flexDirection: 'row', alignItems: 'center', paddingHorizontal: 20, paddingVertical: 16 }}>
            <Text style={[typography.action, { flex: 1 }]}>Select Model</Text>
            <Pressable onPress={onClose}>
              <Ionicons name="close-circle" size={22} color={colors.stone} />
            </Pressable>
          </View>

          {models.map((m) => {
            const isSelected = m.name === selectedName;
            return (
              <Pressable
                key={m.name}
                disabled={!m.available}
                onPress={() => {
                  Haptics.selectionAsync();
                  onSelect(m.name);
                  onClose();
                }}
                style={{ flexDirection: 'row', alignItems: 'center', gap: 16, paddingHorizontal: 20, paddingVertical: 14 }}
              >
                <ClaudeMark size={24} color={m.available ? colors.orange : colors.bone} />
                <View style={{ flex: 1 }}>
                  <Text style={{ ...typography.user, fontFamily: 'Styrene-Medium' }}>{m.name}</Text>
                  <Text style={typography.chip}>{m.subtitle}</Text>
                </View>
                {isSelected && (
                  <View style={{ width: 12, height: 12, borderRadius: 6, backgroundColor: colors.orange }} />
                )}
              </Pressable>
            );
          })}
        </View>
      </View>
    </Modal>
  );
}
```

### Code Block

```tsx
// components/CodeBlock.tsx
import { useState } from 'react';
import { View, Text, Pressable, ScrollView } from 'react-native';
import * as Clipboard from 'expo-clipboard';
import * as Haptics from 'expo-haptics';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function CodeBlock({ language, code }: { language: string; code: string }) {
  const [copied, setCopied] = useState(false);

  const handleCopy = async () => {
    await Clipboard.setStringAsync(code);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setCopied(true);
    setTimeout(() => setCopied(false), 1200);
  };

  return (
    <View
      style={{
        borderRadius: 12, overflow: 'hidden',
        backgroundColor: colors.codeBg,
        borderWidth: 1, borderColor: colors.sand,
      }}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 12, paddingVertical: 8, backgroundColor: 'rgba(58,51,44,0.4)' }}>
        <Text style={typography.codeLang}>{language}</Text>
        <Pressable onPress={handleCopy} hitSlop={8}>
          <Ionicons name={copied ? 'checkmark' : 'copy-outline'} size={14} color={colors.codeFg} />
        </Pressable>
      </View>
      <ScrollView horizontal showsHorizontalScrollIndicator={false}>
        <Text style={[typography.codeBlock, { padding: 16 }]}>{code}</Text>
      </ScrollView>
    </View>
  );
}
```

### "Thinking…" Indicator

```tsx
// components/ThinkingIndicator.tsx
import { useEffect } from 'react';
import { View, Text } from 'react-native';
import Animated, { useSharedValue, useAnimatedStyle, withRepeat, withTiming, withSequence } from 'react-native-reanimated';
import { ClaudeMark } from './ClaudeMark';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export function ThinkingIndicator({ elapsedSeconds }: { elapsedSeconds?: number }) {
  const scale = useSharedValue(1);

  useEffect(() => {
    scale.value = withRepeat(
      withSequence(
        withTiming(1.15, { duration: 600 }),
        withTiming(1.0, { duration: 600 }),
      ),
      -1, false,
    );
  }, []);

  const markStyle = useAnimatedStyle(() => ({ transform: [{ scale: scale.value }] }));

  return (
    <View
      style={{
        flexDirection: 'row', alignItems: 'center', gap: 8,
        alignSelf: 'flex-start',
        paddingVertical: 6, paddingHorizontal: 12,
        backgroundColor: colors.orangeSoft, borderRadius: 12,
      }}
    >
      <Animated.View style={markStyle}>
        <ClaudeMark size={14} />
      </Animated.View>
      <Text style={typography.chip}>
        {elapsedSeconds ? `Thought for ${elapsedSeconds}s` : 'Thinking…'}
      </Text>
    </View>
  );
}
```

### Artifact Card

```tsx
// components/ArtifactCard.tsx
import { View, Text, Pressable } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

type Kind = 'document' | 'code' | 'chart';

export function ArtifactCard({
  title, kind, preview, onOpen,
}: { title: string; kind: Kind; preview: string; onOpen: () => void }) {
  const icon = kind === 'document' ? 'document-text' : kind === 'code' ? 'code-slash' : 'bar-chart';

  return (
    <Pressable
      onPress={onOpen}
      style={({ pressed }) => ({
        backgroundColor: colors.paper,
        borderRadius: 12, padding: 16,
        borderWidth: 1, borderColor: colors.sand,
        shadowColor: colors.shadowCard, shadowOpacity: 1, shadowRadius: 8, shadowOffset: { width: 0, height: 2 },
        opacity: pressed ? 0.95 : 1,
        gap: 12,
      })}
    >
      <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
        <Ionicons name={icon} size={14} color={colors.graphite} />
        <Text style={{ ...typography.user, fontFamily: 'Styrene-Medium', flex: 1 }}>{title}</Text>
        <Ionicons name="arrow-up" size={12} color={colors.stone} style={{ transform: [{ rotate: '45deg' }] }} />
      </View>

      <Text style={[typography.codeBlock, { color: colors.graphite }]} numberOfLines={6}>
        {preview}
      </Text>
    </Pressable>
  );
}
```

## 5. Conversation Composition

```tsx
// app/(chat)/index.tsx
import { useState } from 'react';
import { View, ScrollView, Text, Pressable, SafeAreaView } from 'react-native';
import Ionicons from '@expo/vector-icons/Ionicons';
import { AssistantMessage } from '../components/AssistantMessage';
import { UserMessage } from '../components/UserMessage';
import { ChatInput } from '../components/ChatInput';
import { ModelPickerChip } from '../components/ModelPickerChip';
import { ModelPickerSheet } from '../components/ModelPickerSheet';
import { colors } from '../theme/colors';
import { typography } from '../theme/typography';

export default function ChatScreen() {
  const [model, setModel] = useState('Claude Opus 4.5');
  const [pickerOpen, setPickerOpen] = useState(false);

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: colors.cream }}>
      {/* Header */}
      <View style={{ height: 52, flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between', paddingHorizontal: 16 }}>
        <Pressable><Ionicons name="menu" size={18} color={colors.ink} /></Pressable>
        <Text style={typography.convTitle}>Untitled chat</Text>
        <Pressable><Ionicons name="ellipsis-horizontal" size={18} color={colors.ink} /></Pressable>
      </View>

      {/* Model chip */}
      <View style={{ paddingHorizontal: 20, paddingBottom: 8 }}>
        <ModelPickerChip modelName={model} onPress={() => setPickerOpen(true)} />
      </View>

      {/* Conversation */}
      <ScrollView contentContainerStyle={{ padding: 16, gap: 32 }}>
        <UserMessage text="Explain Bayesian inference like I'm a curious 15-year-old." />
        <AssistantMessage
          modelName={model}
          content={`Imagine you're trying to figure out…`}
          isStreaming
        />
      </ScrollView>

      <ChatInput onSend={() => {}} onAttach={() => {}} />

      <ModelPickerSheet
        visible={pickerOpen}
        selectedName={model}
        onSelect={setModel}
        onClose={() => setPickerOpen(false)}
        models={[
          { name: 'Claude Opus 4.5',   subtitle: 'Most intelligent',     available: true },
          { name: 'Claude Sonnet 4.5', subtitle: 'Fast and capable',     available: true },
          { name: 'Claude Haiku 4.5',  subtitle: 'Quickest responses',   available: true },
        ]}
      />
    </SafeAreaView>
  );
}
```

## 6. Motion & Haptics

```tsx
// Send tap
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

// Copy code
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);

// Model picker select
Haptics.selectionAsync();

// Streaming cursor blink (Reanimated)
opacity.value = withRepeat(withTiming(0, { duration: 300 }), -1, true);

// "Thinking…" pulse
scale.value = withRepeat(
  withSequence(withTiming(1.15, { duration: 600 }), withTiming(1.0, { duration: 600 })),
  -1, false,
);

// Artifact card → modal expansion via expo-router presentation
// router.push({ pathname: '/artifact/[id]', params: { id, presentation: 'modal' } });
```

## 7. Icon Library

Use `@expo/vector-icons` (Ionicons by default).

| Purpose | Ionicons |
|---------|----------|
| Send (arrow up) | `arrow-up` |
| Attach (plus) | `add-circle-outline` |
| Copy | `copy-outline` |
| Copied | `checkmark` |
| Sidebar / hamburger | `menu` |
| Overflow | `ellipsis-horizontal` |
| Chevron down | `chevron-down` |
| Close modal | `close-circle` |
| Artifact — document | `document-text` |
| Artifact — code | `code-slash` |
| Artifact — chart | `bar-chart` |
| Open arrow | `arrow-up` (rotated 45°) |
| Settings | `settings-outline` |
| New chat | `add` |

## 8. Platform Notes

- **iOS-first restraint**: Claude doesn't use heavy blur or glass effects. The chat surface is intentionally flat — `expo-blur` is reserved for modal backdrops (`intensity={20}`) only. Don't add backdrop blur to the input or message bubbles.
- **Status bar**: Set `<StatusBar style="dark" />` from `expo-status-bar` on the cream canvas; `"light"` on the dark canvas.
- **Safe area**: Wrap screens in `SafeAreaView` from `react-native-safe-area-context`. The chat input pins above the keyboard via `KeyboardAvoidingView` (`behavior="padding"` on iOS).
- **Auto-grow textarea**: Use `multiline` on `TextInput` and a max-height to switch from auto-grow to scroll. The textarea should grow from 40pt min to 160pt max, then scroll internally.
- **Markdown rendering**: Use `react-native-markdown-display` and override the style map with the `typography` tokens. Pre-process code fences with `react-native-syntax-highlighter` or similar — render with the Claude warm palette (orange/sage/gold/periwinkle).
- **Streaming text**: Animate word-by-word, not character-by-character. Buffer incoming tokens until a whitespace boundary, then fade in the next word over 80ms. Use `LayoutAnimation.configureNext({ duration: 80, update: { type: 'easeInEaseOut' } })` on each word append.
- **Dynamic Type**: React Native respects user font-scaling. Set `allowFontScaling={false}` on the model chip, timestamps, code-block language label, and the send icon — everywhere else, allow scaling. Code block font scales but cap at 18pt via `maxFontSizeMultiplier`.
- **Accessibility**: Assistant messages should announce model name first, then content, with `accessibilityRole="text"`. The streaming cursor has `accessibilityLabel="Claude is responding"`. Code blocks should have a `accessibilityLabel="Code block in {language}"` and the copy button announces "Copy code, button".
- **Reduce Motion**: Check `AccessibilityInfo.isReduceMotionEnabled()` and disable the streaming cursor blink + the asterisk pulse — replace with static states.
- **Dark mode**: Use `useColorScheme()` and a token resolver that returns the dark palette. Claude Orange stays identical; only chrome inverts. The code-block background is identical to the dark canvas (`#1F1B16`) — they unify visually in dark mode.
- **iPad layout**: Use `useWindowDimensions()` to detect tablet width (>= 768pt). Render a persistent 260pt sidebar via a two-pane layout (`<View flex-direction="row">`); the chat content gets a max width of 720pt centered in the remaining space.
- **Performance**: Use `FlatList` (not `ScrollView`) for the conversation when message count exceeds ~20. Memoize each message with `React.memo` keyed on message ID and streaming state.
