# Flutter ChatGPT UI

[![pub package](https://img.shields.io/pub/v/flutter_gen_ai_chat_ui.svg)](https://pub.dev/packages/flutter_gen_ai_chat_ui)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Build ChatGPT-style chat interfaces in Flutter. Simple to use, easy to customize.

<table>
  <tr>
    <td align="center">
      <img src="https://raw.githubusercontent.com/hooshyar/flutter_gen_ai_chat_ui/main/screenshots/detailed_dark.png" alt="Dark Mode" width="300px">
      <br>
      <em>Dark Mode</em>
    </td>
    <td align="center">
      <img src="https://raw.githubusercontent.com/hooshyar/flutter_gen_ai_chat_ui/main/screenshots/detailed.gif" alt="Chat Demo" width="300px">
      <br>
      <em>Chat Demo</em>
    </td>
  </tr>
</table>

## 🤖 Quick Integration with AI Help

Need help integrating this package with your specific use case? Copy this prompt into ChatGPT:

```
Help me integrate flutter_gen_ai_chat_ui with my Flutter app.

My app details:
1. App type: [e.g., AI chatbot, customer support, education app]
2. Backend: [e.g., OpenAI API, custom API, Firebase]
3. Features needed: [e.g., streaming responses, markdown support, dark mode]
4. Current state management: [e.g., Provider, Bloc, GetX]

Please show me:
1. How to integrate the chat UI
2. How to connect it with my backend
3. How to customize the theme to match my app
4. Best practices for my specific use case
```

The AI will provide:
- ✅ Complete integration code
- ✅ Backend connection setup
- ✅ Theme customization examples
- ✅ Performance optimization tips
- ✅ Use case specific recommendations

## Key Features

### Core Features
- 🎨 Dark and light mode support
- 💫 Smooth message animations
- 🔄 Word-by-word text streaming (like ChatGPT)
- ✨ Loading indicators with shimmer effect
- 📱 Responsive layout for all screen sizes
- 🎤 Speech-to-text support

### Speech-to-Text Setup

#### 1. Platform Configuration

**iOS** - Add to `ios/Runner/Info.plist`:
```xml
<key>NSMicrophoneUsageDescription</key>
<string>This app needs microphone access for speech recognition</string>
<key>NSSpeechRecognitionUsageDescription</key>
<string>This app needs speech recognition to convert your voice to text</string>
```

**Android** - Add to `android/app/src/main/AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.RECORD_AUDIO"/>
```

#### 2. Enable in AiChatConfig:
```dart
AiChatConfig(
  enableSpeechToText: true,  // Enable the feature
  speechToTextIcon: Icons.mic_none_rounded,  // Optional: Custom icon
  speechToTextActiveIcon: Icons.mic_rounded,  // Optional: Custom active icon
  speechToTextLocale: 'en_US',  // Optional: Set language
  // Optional: Custom callbacks
  onSpeechStart: () async { /* Custom logic when speech starts */ },
  onSpeechEnd: () async { /* Custom logic when speech ends */ },
  onSpeechError: (error) { /* Handle errors */ },
  onRequestSpeechPermission: () async { /* Custom permission handling */ },
)
```

The speech-to-text button now includes:
- 🎯 Visual sound level indicator
- 💫 Pulsing animation when active
- 🎨 Theme-aware styling
- ⚡️ Improved error handling
- 🌐 Automatic language detection
- 📱 Works on both iOS and Android (physical devices)

### Message Features
- 📝 Markdown support with syntax highlighting
- 🎯 Selectable text in messages
- 🔗 Clickable links
- 📜 Message pagination
- 🌐 RTL language support

### UI Components
- 👋 Customizable welcome message
- ⭐️ Example questions widget
- 💬 Custom message bubbles
- 🎮 Custom input field and send button

## Quick Start

### 1. Add the dependency

```yaml
dependencies:
  flutter_gen_ai_chat_ui: ^1.1.6
```

### 2. Import the package

```dart
import 'package:flutter_gen_ai_chat_ui/flutter_gen_ai_chat_ui.dart';
import 'package:dash_chat_2/dash_chat_2.dart';
```

### 3. Basic Implementation

Here's a simple chat screen:

```dart
class ChatScreen extends StatefulWidget {
  @override
  State<ChatScreen> createState() => _ChatScreenState();
}

class _ChatScreenState extends State<ChatScreen> {
  late final _controller = ChatMessagesController();
  late final _currentUser = ChatUser(id: '1', firstName: 'User');
  late final _aiUser = ChatUser(id: '2', firstName: 'AI Assistant');
  bool _isLoading = false;

  Future<void> _handleSendMessage(ChatMessage message) async {
    setState(() => _isLoading = true);
    _controller.addMessage(message);

    try {
      // Add your AI response logic here
      final response = ChatMessage(
        text: "Hello! I received: ${message.text}",
        user: _aiUser,
        createdAt: DateTime.now(),
      );
      _controller.addMessage(response);
    } finally {
      setState(() => _isLoading = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('AI Chat')),
      body: Material(
        type: MaterialType.transparency,
        child: AiChatWidget(
          config: AiChatConfig(
            hintText: 'Type a message...',
            enableAnimation: true,
          ),
          controller: _controller,
          currentUser: _currentUser,
          aiUser: _aiUser,
          onSendMessage: _handleSendMessage,
          isLoading: _isLoading,
        ),
      ),
    );
  }
}
```

## Customization Examples

### 1. Dark Mode Support

```dart
Theme(
  data: Theme.of(context).copyWith(
    extensions: [
      CustomThemeExtension(
        // Message colors
        messageBubbleColor: isDark ? Color(0xFF262626) : Colors.white,
        userBubbleColor: isDark ? Color(0xFF1A4B8F) : Color(0xFFE3F2FD),
        messageTextColor: isDark ? Color(0xFFE5E5E5) : Colors.grey[800]!,
        
        // Input field colors
        inputBackgroundColor: isDark ? Color(0xFF262626) : Colors.white,
        inputBorderColor: isDark ? Color(0xFF404040) : Colors.grey[300]!,
        
        // Background and accent colors
        chatBackground: isDark ? Color(0xFF171717) : Colors.grey[50]!,
        sendButtonIconColor: isDark ? Color(0xFF60A5FA) : Colors.blue,
      ),
    ],
  ),
  child: AiChatWidget(...),
)
```

### 2. Streaming Responses

```dart
Future<void> handleStreamingResponse(ChatMessage message) async {
  final response = ChatMessage(
    text: "",
    user: aiUser,
    createdAt: DateTime.now(),
  );
  controller.addMessage(response);

  // Simulate streaming response
  final words = "Hello! How can I help you today?".split(' ');
  String currentText = '';
  
  for (var word in words) {
    await Future.delayed(Duration(milliseconds: 50));
    currentText += (currentText.isEmpty ? '' : ' ') + word;
    controller.messages.removeWhere((m) => 
      m.createdAt == response.createdAt && m.user.id == aiUser.id
    );
    controller.addMessage(ChatMessage(
      text: currentText,
      user: aiUser,
      createdAt: response.createdAt,
    ));
  }
}
```

### 3. Markdown Messages

```dart
AiChatConfig(
  messageBuilder: (message) => MarkdownBody(
    data: message.text,
    styleSheet: MarkdownStyleSheet(
      p: TextStyle(color: Colors.white),
      code: TextStyle(backgroundColor: Colors.grey[800]),
      h1: TextStyle(color: Colors.white, fontSize: 24),
    ),
  ),
)
```

## Examples

Check out our [example](example) folder for complete implementations:

1. **Streaming Example**: Word-by-word text streaming like ChatGPT
2. **Custom Styling**: Dark/light mode with beautiful UI
3. **Markdown Support**: Rich text formatting in messages

## Need Help?

- 📘 Check our [example](example) folder
- 🐛 File issues on our [GitHub repository](https://github.com/hooshyar/flutter_gen_ai_chat_ui)
- 💡 Contribute to the project

## License

MIT License - see the [LICENSE](LICENSE) file for details.

### Speech-to-Text Features

The package includes a professional speech-to-text button with:
- 🌊 Smooth dual-layer pulse animation
- 📊 Real-time sound level visualization
- 🎨 Adaptive theming for light/dark modes
- 🎯 Precise error handling and recovery
- 🔄 Automatic language detection
- 📱 iOS and Android support (physical devices)

#### Configuration Example:
```dart
AiChatConfig(
  enableSpeechToText: true,
  speechToTextIcon: Icons.mic_none_rounded,
  speechToTextActiveIcon: Icons.mic_rounded,
  speechToTextLocale: 'en_US',
  // Optional callbacks
  onSpeechStart: () async {
    // Show recording indicator
  },
  onSpeechEnd: () async {
    // Hide recording indicator
  },
  onSpeechError: (error) {
    // Handle errors gracefully
  },
  onRequestSpeechPermission: () async {
    // Custom permission handling
    return true;
  },
)
```

Note: Speech recognition requires a physical device. It will not work on simulators/emulators.

