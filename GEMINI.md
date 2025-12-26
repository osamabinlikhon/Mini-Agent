# AGENTS.md - Ant Design X AI Application Development Guidelines

This document provides comprehensive guidelines for AI coding agents working with Ant Design X projects. It covers project setup, component usage, data flow management, markdown rendering, and best practices for building AI-driven interfaces.

## 1. Keyword Conventions

This document uses RFC 2119 keywords to indicate requirement levels. The capitalized keywords MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, NOT RECOMMENDED, MAY, and OPTIONAL are to be interpreted as follows:

- **REQUIRED** or **MUST** / **SHALL**: Absolute requirement; the implementation must include this.
- **MUST NOT** / **SHALL NOT**: Absolute prohibition; the implementation must not include this.
- **SHOULD** / **RECOMMENDED**: There may be valid reasons to ignore, but the implications must be understood and weighed.
- **SHOULD NOT** / **NOT RECOMMENDED**: The behavior is generally unacceptable, but may be appropriate in specific circumstances.
- **MAY** / **OPTIONAL**: The item is purely optional; implementations may choose to include or exclude.

For non-normative guidance, this document also uses action-oriented language with direct verbs such as **Add**, **Use**, **Include**, **Avoid**, **Prefer**, and **Consider**.

---

## 2. Project Setup

### 2.1 Installation and Dependencies

- **MUST:** Use the recommended package manager for the project (npm, yarn, pnpm, or bun) as specified in the project's package.json.
- **MUST:** Install `@ant-design/x` for the UI component library:

  ```bash
  npm install @ant-design/x --save
  ```

- **MUST:** Install `@ant-design/x-markdown` for streaming markdown rendering:

  ```bash
  npm install @ant-design/x-markdown --save
  ```

- **MUST:** Install `@ant-design/x-sdk` for AI conversation data flow management:

  ```bash
  npm install @ant-design/x-sdk --save
  ```

- **SHOULD:** Ensure all three packages are kept at compatible versions (version 2.1.1 or higher recommended).
- **SHOULD:** Use TypeScript 5.0 or higher for full type support across all Ant Design X packages.
- **MUST:** Ensure peer dependencies are satisfied, including React 18.0 or higher and Ant Design 5.0 or higher.
- **MAY:** Use the browser import method for prototyping (importing `x-sdk.js` directly), but this is **NOT RECOMMENDED** for production as it prevents on-demand loading and complicates bug fixes.

### 2.2 Framework Integration

Ant Design X supports the following project setups:

- **SHOULD:** Use Vite for new projects due to superior development experience and performance:

  ```bash
  npm create vite@latest my-ai-app -- --template react-ts
  cd my-ai-app
  npm install @ant-design/x @ant-design/x-markdown @ant-design/x-sdk
  ```

- **SHOULD:** Use Next.js 13+ with App Router for server-side rendering requirements:

  ```bash
  npx create-next-app@latest my-ai-app --typescript
  cd my-ai-app
  npm install @ant-design/x @ant-design/x-markdown @ant-design/x-sdk
  ```

- **SHOULD:** Use Rsbuild for optimal build performance in modern React applications.
- **SHOULD:** Use Umi for enterprise-grade React applications with Ant Design integration.
- **MAY:** Use create-react-app for legacy projects, though Vite is preferred for new development.
- **MUST:** Configure CSS-in-JS or CSS modules appropriately for Ant Design X component styling.

### 2.3 TypeScript Configuration

- **MUST:** Enable strict mode in `tsconfig.json` for comprehensive type checking:

  ```json
  {
    "compilerOptions": {
      "strict": true,
      "jsx": "react-jsx",
      "moduleResolution": "bundler",
      "esModuleInterop": true
    }
  }
  ```

- **SHOULD:** Import types from `@ant-design/x`, `@ant-design/x-markdown`, and `@ant-design/x-sdk` when working with component props and API responses.
- **SHOULD:** Define custom types for conversation data structures that extend the built-in types from Ant Design X.
- **MAY:** Create type augmentation modules when extending Ant Design X components with additional props.

---

## 3. Component Library (@ant-design/x)

### 3.1 Core Component Architecture

The `@ant-design/x` library follows the RICH paradigm, which blends GUI with natural conversation to present optimal components for AI interactions. All components are organized into the following categories:

#### 3.1.1 Common Components

- **Bubble:** The primary component for displaying conversation messages. **MUST** be used for all user and AI messages in chat interfaces.
- **Conversations:** **MUST** be used for managing multiple conversation sessions and switching between them.
- **Notification:** (Version 2.0.0+) **SHOULD** be used for system-level notifications and status updates.

#### 3.1.2 Confirmation Components

- **Think:** (Version 2.0.0+) **SHOULD** be used to display AI reasoning or thought processes.
- **ThoughtChain:** (Version 2.0.0+) **MAY** be used to display a sequence of thought processes in a structured manner.

#### 3.1.3 Wake Components

- **Welcome:** **SHOULD** be used for initial greeting screens when no conversation exists.
- **Prompts:** **SHOULD** be used to provide suggested prompts or quick-start options for users.

#### 3.1.4 Express Components

- **Attachments:** **SHOULD** be used for handling file attachments in conversations.
- **Sender:** **MUST** be used for the message input area with text input and send functionality.
- **Suggestion:** **MAY** be used for providing contextual suggestions during conversations.

#### 3.1.5 Feedback Components

- **Actions:** **SHOULD** be used for post-message action buttons (regenerate, copy, edit, etc.).
- **CodeHighlighter:** (Version 2.1.0+) **MUST** be used for displaying code blocks with syntax highlighting.
- **FileCard:** (Version 2.0.0+) **SHOULD** be used for displaying file information in attachments.
- **Mermaid:** (Version 2.1.0+) **SHOULD** be used for rendering Mermaid diagrams in markdown content.
- **Sources:** (Version 2.0.0+) **MAY** be used for displaying source citations or references.

#### 3.1.6 Provider Components

- **XProvider:** **MUST** wrap all Ant Design X components to provide context and theme customization.

### 3.2 Bubble Component Usage

The `Bubble` component is the foundational element for displaying conversation messages:

- **MUST:** Import Bubble from `@ant-design/x`:

  ```tsx
  import { Bubble } from '@ant-design/x';
  ```

- **MUST:** Differentiate between user and AI messages using the `placement` prop:

  ```tsx
  <Bubble placement="start">User message</Bubble>
  <Bubble placement="end">AI message</Bubble>
  ```

- **SHOULD:** Use the `typing` prop for streaming message display:

  ```tsx
  <Bubble typing={{ steps: 10, interval: 30 }}>Streaming content</Bubble>
  ```

- **SHOULD:** Customize bubble appearance using the `styles` and `classNames` props for semantic structure:

  ```tsx
  <Bubble
    placement="start"
    styles={{ content: { backgroundColor: '#f5f5f5' } }}
  >
    Message content
  </Bubble>
  ```

- **SHOULD:** Use the `header` prop for displaying message metadata (timestamps, author, etc.).
- **MAY:** Use the `footer` prop for displaying message actions (copy, edit, delete).

### 3.3 Conversations Component Usage

The `Conversations` component manages conversation history and session switching:

- **MUST:** Import Conversations from `@ant-design/x`:

  ```tsx
  import { Conversations } from '@ant-design/x';
  ```

- **SHOULD:** Use controlled mode with `activeKey` and `onActiveChange` for programmatic control:

  ```tsx
  const [activeKey, setActiveKey] = useState<string>('conv-1');
  
  <Conversations
    activeKey={activeKey}
    onActiveChange={setActiveKey}
    items={conversationItems}
  />
  ```

- **SHOULD:** Enable grouping with the `groupable` prop for organizing conversations:

  ```tsx
  <Conversations
    groupable={{
      label: (group) => group,
      collapsible: true,
      defaultExpandedKeys: ['Today']
    }}
    items={conversationItems}
  />
  ```

- **SHOULD:** Configure shortcut keys for improved accessibility:

  ```tsx
  <Conversations
    shortcutKeys={{
      creation: { key: 'k', modifiers: ['meta'] },
      items: { key: '1', modifiers: ['alt'] }
    }}
  />
  ```

- **SHOULD:** Use the `menu` prop for conversation operations (rename, delete, pin):

  ```tsx
  <Conversations
    menu={{
      items: [{ key: 'delete', label: 'Delete' }]
    }}
  />
  ```

- **SHOULD:** Implement infinite scrolling for large conversation histories using the `onScroll` callback.

### 3.4 Sender Component Usage

The `Sender` component provides the message input interface:

- **MUST:** Import Sender from `@ant-design/x`:

  ```tsx
  import { Sender } from '@ant-design/x';
  ```

- **SHOULD:** Use the `onSubmit` callback for handling message submission:

  ```tsx
  <Sender
    onSubmit={(value) => sendMessage(value)}
    placeholder="Type a message..."
  />
  ```

- **SHOULD:** Use the `loading` prop to indicate message transmission in progress.
- **SHOULD:** Customize the `prefix` prop for adding input attachments, emojis, or other controls.
- **MAY:** Use the `autoSize` prop for multiline message input with automatic height adjustment.

### 3.5 XProvider Configuration

The `XProvider` component provides context and theming for all Ant Design X components:

- **MUST:** Wrap the root application or chat container with XProvider:

  ```tsx
  import { XProvider, Bubble, Sender } from '@ant-design/x';
  import { App } from 'antd';
  
  const App = () => (
    <XProvider theme={{ algorithm: theme.defaultAlgorithm }}>
      <ChatInterface />
    </XProvider>
  );
  ```

- **SHOULD:** Configure theme settings including color scheme and design tokens:

  ```tsx
  <XProvider
    theme={{
      algorithm: theme.darkAlgorithm,
      token: {
        colorPrimary: '#1677ff',
        borderRadius: 6
      }
    }}
  >
    {children}
  </XProvider>
  ```

- **MAY:** Use the `broadcast` prop for global state communication across components.

---

## 4. Markdown Rendering (@ant-design/x-markdown)

### 4.1 Core Markdown Features

The `@ant-design/x-markdown` package provides streaming-friendly, high-performance markdown rendering optimized for AI-generated content:

- **MUST:** Import XMarkdown from `@ant-design/x-markdown`:

  ```tsx
  import { XMarkdown } from '@ant-design/x-markdown';
  ```

- **SHOULD:** Use the `componentOverrides` prop for customizing markdown element rendering:

  ```tsx
  <XMarkdown
    componentOverrides={{
      h1: MyCustomH1,
      pre: MyCustomPre,
      code: MyCustomCode
    }}
  >
    {markdownContent}
  </XMarkdown>
  ```

- **SHOULD:** Use the `onDoubleClick` prop for implementing copy-to-clipboard functionality on code blocks.
- **MUST:** Use the `value` prop for streaming content updates:

  ```tsx
  const [content, setContent] = useState('');
  
  <XMarkdown value={content} />
  ```

### 4.2 Streaming Syntax Processing

Ant Design X-Markdown is specifically designed for streaming rendering scenarios:

- **MUST:** Understand the syntax processing mechanism for incomplete markdown during streaming:

  The system can intelligently identify incomplete Markdown syntax structures and handle them gracefully during partial rendering.

- **SHOULD:** Use the `syntaxHook` prop for custom handling of incomplete syntax during streaming.
- **SHOULD:** Configure animation effects for smooth streaming visualization using the `animation` prop.
- **MUST:** Handle edge cases where streaming content ends mid-syntax (unclosed backticks, partial HTML tags, etc.).

### 4.3 Plugin System

The markdown engine supports a rich plugin ecosystem:

- **SHOULD:** Enable GFM (GitHub Flavored Markdown) plugin for enhanced markdown compatibility:

  ```tsx
  import { XMarkdown, plugins } from '@ant-design/x-markdown';
  
  <XMarkdown plugins={[plugins.gfm]} value={content} />
  ```

- **SHOULD:** Use the `marked` base features while leveraging Ant Design X extensions:

  The package is built on `marked` as the base Markdown renderer, inheriting all features while providing additional streaming capabilities.

- **MAY:** Create custom plugins for domain-specific markdown extensions.
- **SHOULD:** Configure syntax highlighting plugins for code blocks:

  ```tsx
  import { XMarkdown, plugins } from '@ant-design/x-markdown';
  
  <XMarkdown
    plugins={[plugins.codeHighlighter]}
    codeBlock={{ language: 'typescript' }}
  >
    {content}
  </XMarkdown>
  ```

### 4.4 Security Considerations

- **MUST:** The package is secure by default and prevents `dangerouslySetInnerHTML` XSS attacks.
- **SHOULD:** Avoid using `dangerouslySetInnerHTML` when rendering markdown content.
- **SHOULD:** Configure CSP (Content Security Policy) headers appropriately for the markdown rendering engine.
- **MUST NOT:** Execute user-provided markdown content as JavaScript under any circumstances.

### 4.5 Mermaid Diagram Support

- **SHOULD:** Import Mermaid component from `@ant-design/x` for rendering diagrams:

  ```tsx
  import { Mermaid } from '@ant-design/x';
  
  <Mermaid chart={`
    graph TD
      A[Start] --> B[Process]
      B --> C[End]
  `} />
  ```

- **SHOULD:** Use the `theme` prop for customizing diagram appearance.
- **SHOULD:** Handle Mermaid rendering errors gracefully with fallback content.
- **MAY:** Use lazy loading for complex Mermaid diagrams to improve initial page load.

### 4.6 Code Highlighting

- **SHOULD:** Import CodeHighlighter from `@ant-design/x` for syntax highlighting:

  ```tsx
  import { CodeHighlighter } from '@ant-design/x';
  
  <CodeHighlighter
    language="typescript"
    copyable
    showLineNumbers
  >
    {codeContent}
  </CodeHighlighter>
  ```

- **SHOULD:** Use the `language` prop for specifying the programming language.
- **SHOULD:** Enable the `copyable` prop for one-click code copying.
- **SHOULD:** Use the `showLineNumbers` prop for better code readability.
- **MAY:** Use the `highlightedLines` prop for emphasizing specific lines of code.

---

## 5. Data Flow Management (@ant-design/x-sdk)

### 5.1 SDK Overview

The `@ant-design/x-sdk` provides a complete set of tool APIs for managing AI conversation data flows:

- **MUST:** Import SDK functions from `@ant-design/x-sdk`:

  ```tsx
  import { useXChat, useXConversations, XRequest } from '@ant-design/x-sdk';
  ```

- **SHOULD:** Use the SDK hooks (`useXChat`, `useXConversations`) for managing conversation state and data flow.
- **SHOULD:** Use `XRequest` for managing asynchronous requests and streaming responses.
- **SHOULD:** Use `XStream` for handling streaming data from AI models.

### 5.2 useXChat Hook

The `useXChat` hook manages chat-related state and actions:

- **MUST:** Import and use useXChat for chat message management:

  ```tsx
  import { useXChat } from '@ant-design/x-sdk';
  
  const {
    messages,
    sendMessage,
    appendMessage,
    updateMessage,
    deleteMessage,
    clearMessages,
    isLoading,
    error
  } = useXChat({
    defaultMessages: [],
    onError: (error) => console.error(error)
  });
  ```

- **SHOULD:** Use the `messages` array for displaying conversation history.
- **SHOULD:** Use `sendMessage` for sending user messages and receiving AI responses.
- **SHOULD:** Use `appendMessage` for adding messages to the conversation without triggering an API call.
- **SHOULD:** Use `updateMessage` for modifying existing messages (e.g., during streaming).
- **SHOULD:** Use `deleteMessage` for removing specific messages.
- **SHOULD:** Use `clearMessages` for resetting the conversation.
- **SHOULD:** Handle the `isLoading` state for displaying loading indicators.
- **SHOULD:** Handle the `error` state for displaying error messages.

### 5.3 useXConversations Hook

The `useXConversations` hook manages conversation list state:

- **MUST:** Import and use useXConversations for conversation management:

  ```tsx
  import { useXConversations } from '@ant-design/x-sdk';
  
  const {
    conversations,
    activeConversationId,
    createConversation,
    deleteConversation,
    updateConversation,
    switchConversation,
    setConversations
  } = useXConversations({
    defaultConversations: [],
    onChange: (conversations) => saveToStorage(conversations)
  });
  ```

- **SHOULD:** Use the `conversations` array for rendering the conversation list.
- **SHOULD:** Use `createConversation` for creating new conversations.
- **SHOULD:** Use `deleteConversation` for removing conversations.
- **SHOULD:** Use `updateConversation` for modifying conversation metadata (title, settings, etc.).
- **SHOULD:** Use `switchConversation` for switching between conversations.
- **SHOULD:** Implement conversation persistence using the `onChange` callback.

### 5.4 XRequest for API Communication

The `XRequest` function manages asynchronous AI API requests:

- **MUST:** Use XRequest for AI API calls with proper configuration:

  ```tsx
  import { XRequest } from '@ant-design/x-sdk';
  
  XRequest('https://api.example.com/chat', {
    params: {
      model: 'gpt-4',
      messages: [{ role: 'user', content: 'Hello' }],
      stream: true
    },
    callbacks: {
      onSuccess: (response) => console.log('Success:', response),
      onError: (error) => console.error('Error:', error),
      onUpdate: (chunk) => processChunk(chunk)
    }
  });
  ```

- **SHOULD:** Use the `params` object for configuring API request parameters.
- **SHOULD:** Implement `onSuccess` callback for handling successful responses.
- **SHOULD:** Implement `onError` callback for handling errors gracefully.
- **SHOULD:** Implement `onUpdate` callback for processing streaming chunks.
- **MUST:** Cancel in-flight requests when the component unmounts or navigation occurs.

### 5.5 XStream for Streaming Data

The `XStream` utilities provide low-level streaming capabilities:

- **SHOULD:** Use XStream for manual streaming control when useXChat is insufficient:

  ```tsx
  import { XStream } from '@ant-design/x-sdk';
  
  const stream = new XStream({
    url: 'https://api.example.com/stream',
    onData: (data) => appendToMessage(data),
    onComplete: () => finalizeMessage(),
    onError: (error) => handleError(error)
  });
  
  stream.start();
  ```

- **SHOULD:** Use the `abort` controller for cancelling streams.
- **SHOULD:** Handle reconnection scenarios for unstable network conditions.
- **MAY:** Use `XStream` for implementing custom chat providers.

### 5.6 Custom Chat Provider

When the built-in Chat Provider doesn't meet requirements, implement the abstract class `AbstractChatProvider`:

- **MAY:** Extend AbstractChatProvider for custom model integration:

  ```tsx
  import { AbstractChatProvider } from '@ant-design/x-sdk';
  
  class CustomChatProvider extends AbstractChatProvider {
    async sendMessage(messages, options) {
      // Implement custom message sending logic
      // Convert messages to provider format
      // Send to custom API
      // Return standardized response
    }
    
    async *streamMessage(messages, options) {
      // Implement streaming for real-time responses
    }
    
    async cancelRequest(requestId) {
      // Implement request cancellation
    }
  }
  ```

- **SHOULD:** Follow the provider interface for consistent integration.
- **SHOULD:** Convert data from different model providers into the unified format that Ant Design X can consume.
- **SHOULD:** Implement proper error handling and recovery mechanisms.

---

## 6. Model and Agent Integration

### 6.1 OpenAI Integration

- **SHOULD:** Use the built-in OpenAIChatProvider for OpenAI API integration:

  ```tsx
  import { OpenAIChatProvider } from '@ant-design/x-sdk';
  
  <XProvider
    chatProvider={
      new OpenAIChatProvider({
        model: 'gpt-4',
        apiKey: process.env.OPENAI_API_KEY
      })
    }
  >
    {children}
  </XProvider>
  ```

- **SHOULD:** Configure model parameters (temperature, max tokens, top_p) appropriately.
- **SHOULD:** Handle API rate limits and quota exceeded errors gracefully.
- **SHOULD:** Implement proper API key security (environment variables, secure storage).

### 6.2 Qwen Integration

- **SHOULD:** Use the built-in QwenChatProvider for Qwen (Ali) API integration:

  ```tsx
  import { QwenChatProvider } from '@ant-design/x-sdk';
  
  <XProvider
    chatProvider={
      new QwenChatProvider({
        model: 'qwen-turbo',
        apiKey: process.env.QWEN_API_KEY
      })
    }
  >
    {children}
  </XProvider>
  ```

- **SHOULD:** Configure endpoint and region settings for Qwen API.
- **SHOULD:** Handle Qwen-specific response formats and error codes.

### 6.3 Tbox Agent Integration

- **SHOULD:** Integrate with Tbox for agent-based workflows:

  The platform supports Tbox agent integration for advanced AI agent scenarios.

- **SHOULD:** Configure agent parameters and tool definitions appropriately.
- **SHOULD:** Handle agent state and context management.
- **MAY:** Implement custom agents using the AbstractChatProvider interface.

### 6.4 Multi-Model Support

- **SHOULD:** Implement model switching capabilities for applications supporting multiple models.
- **SHOULD:** Store model preferences per conversation.
- **SHOULD:** Display current model information in the UI.
- **MAY:** Implement model-specific optimization and prompts.

---

## 7. Code Style and Patterns

### 7.1 Component Patterns

- **SHOULD:** Use functional components with React hooks for all Ant Design X components.
- **SHOULD:** Follow the Ant Design component composition patterns:

  ```tsx
  // Preferred pattern
  const ChatInterface: React.FC = () => {
    const { messages, sendMessage } = useXChat();
    
    return (
      <XProvider>
        <BubbleList messages={messages} />
        <Sender onSubmit={sendMessage} />
      </XProvider>
    );
  };
  ```

- **SHOULD:** Use semantic naming for component props (e.g., `onActiveChange`, `items`, `styles`).
- **SHOULD:** Destructure props for better readability.
- **SHOULD:** Implement proper TypeScript types for all custom props.

### 7.2 State Management

- **SHOULD:** Use local state with useState for component-specific data.
- **SHOULD:** Use useXChat and useXConversations for chat-related state.
- **SHOULD:** Use useMemo for expensive computations.
- **SHOULD:** Use useCallback for stable callback references.
- **SHOULD:** Implement proper cleanup in useEffect (subscriptions, event listeners).

### 7.3 Data Transformation

- **SHOULD:** Transform API responses to internal types before using them.
- **SHOULD:** Normalize conversation and message data structures.
- **SHOULD:** Implement proper type guards for runtime type checking.
- **SHOULD:** Handle partial data gracefully during loading states.

### 7.4 Error Handling

- **MUST:** Implement error boundaries for preventing UI crashes:

  ```tsx
  class ErrorBoundary extends React.Component {
    state = { hasError: false };
    
    static getDerivedStateFromError() {
      return { hasError: true };
    }
    
    render() {
      if (this.state.hasError) {
        return <ErrorFallback />;
      }
      return this.props.children;
    }
  }
  ```

- **SHOULD:** Use error boundaries around Ant Design X component trees.
- **SHOULD:** Display user-friendly error messages for API failures.
- **SHOULD:** Implement retry mechanisms for failed requests.
- **SHOULD:** Log errors with sufficient context for debugging.

---

## 8. Accessibility and Internationalization

### 8.1 Accessibility Requirements

- **MUST:** Ensure all Ant Design X components are keyboard accessible.
- **SHOULD:** Implement ARIA attributes for custom components:

  ```tsx
  <div role="status" aria-live="polite">
    {statusMessage}
  </div>
  ```

- **SHOULD:** Provide sufficient color contrast for all text content.
- **SHOULD:** Implement focus management for modal and dialog components.
- **MUST:** Support screen readers for all interactive elements.

### 8.2 Keyboard Navigation

- **SHOULD:** Support Alt/⌥ + number for conversation switching.
- **SHOULD:** Support Win/⌘ + K for creating new conversations.
- **SHOULD:** Implement Tab navigation through chat interface.
- **SHOULD:** Provide keyboard shortcuts for common actions (copy, send, cancel).

### 8.3 Internationalization

- **SHOULD:** Use Ant Design's built-in localization support:

  ```tsx
  import { ConfigProvider } from 'antd';
  import enUS from 'antd/locale/en_US';
  
  <ConfigProvider locale={enUS}>
    <App />
  </ConfigProvider>
  ```

- **SHOULD:** Extract and manage translation strings in dedicated files.
- **SHOULD:** Support RTL (right-to-left) layouts for Arabic and Hebrew.
- **SHOULD:** Format dates, numbers, and currencies according to locale.
- **SHOULD:** Handle text expansion for different languages in input fields.

---

## 9. Testing

### 9.1 Testing Requirements

- **SHOULD:** Write unit tests for custom components using Jest and React Testing Library.
- **SHOULD:** Test Ant Design X component integration with custom logic.
- **SHOULD:** Mock XRequest and XStream for testing API interactions.
- **SHOULD:** Test error handling and loading states.

### 9.2 Testing Examples

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Sender, Bubble } from '@ant-design/x';

describe('Chat Components', () => {
  it('renders message in bubble', () => {
    render(<Bubble placement="start">Test message</Bubble>);
    expect(screen.getByText('Test message')).toBeInTheDocument();
  });

  it('triggers onSubmit when send button clicked', () => {
    const handleSubmit = jest.fn();
    render(<Sender onSubmit={handleSubmit} />);
    fireEvent.click(screen.getByRole('button', { name: /send/i }));
    expect(handleSubmit).toHaveBeenCalled();
  });
});
```

- **SHOULD:** Implement end-to-end tests using Playwright or Cypress for critical user flows.
- **SHOULD:** Test across multiple browsers (Chrome, Firefox, Safari, Edge).

---

## 10. Build and Deployment

### 10.1 Build Configuration

- **SHOULD:** Configure proper transpilation for Ant Design X in build tools:

  ```ts
  // vite.config.ts
  export default defineConfig({
    optimizeDeps: {
      include: ['@ant-design/x', '@ant-design/x-markdown', '@ant-design/x-sdk']
    }
  });
  ```

- **SHOULD:** Enable tree shaking for reducing bundle size.
- **SHOULD:** Configure CSS-in-JS runtime compilation if using styled-components.

### 10.2 Performance Optimization

- **SHOULD:** Lazy load non-critical components:

  ```tsx
  const Mermaid = lazy(() => import('./Mermaid'));
  
  <Suspense fallback={<Loading />}>
    <Mermaid chart={content} />
  </Suspense>
  ```

- **SHOULD:** Memoize expensive render operations using React.memo.
- **SHOULD:** Implement code splitting for large conversation histories.
- **SHOULD:** Optimize markdown rendering for large content.

### 10.3 Production Deployment

- **SHOULD:** Use environment variables for API keys and configuration.
- **SHOULD:** Implement proper CORS configuration for API requests.
- **SHOULD:** Configure CDN for static assets.
- **SHOULD:** Implement proper caching strategies for conversation data.
- **SHOULD:** Monitor performance using APM tools.

---

## 11. Security Best Practices

### 11.1 API Security

- **MUST:** Store API keys securely in environment variables, never in source code.
- **MUST:** Use HTTPS for all API communications.
- **SHOULD:** Implement request signing for sensitive operations.
- **SHOULD:** Validate and sanitize all API responses before rendering.

### 11.2 Input Validation

- **MUST:** Validate user input before sending to AI APIs.
- **SHOULD:** Implement rate limiting on the client side.
- **MUST:** Sanitize user-generated markdown content.
- **SHOULD:** Implement content filtering for inappropriate inputs.

### 11.3 XSS Prevention

- **MUST NOT:** Use dangerouslySetInnerHTML for rendering markdown.
- **MUST:** Use Ant Design X's built-in markdown rendering for all untrusted content.
- **SHOULD:** Configure Content Security Policy headers.
- **MUST:** Escape user input in error messages and notifications.

---

## 12. Debugging and Troubleshooting

### 12.1 Common Issues

- **Component Rendering Issues:** Ensure XProvider wraps all Ant Design X components.
- **API Errors:** Check API key configuration and network connectivity.
- **Streaming Interruptions:** Implement proper stream cancellation on component unmount.
- **Type Errors:** Ensure TypeScript configuration includes strict mode and proper type imports.

### 12.2 Debugging Tools

- **SHOULD:** Use React DevTools for inspecting component state and props.
- **SHOULD:** Use browser DevTools for network request monitoring.
- **SHOULD:** Enable debug logging for XRequest and XStream operations.
- **SHOULD:** Use the Accessibility Tree inspector for debugging a11y issues.

---

## 13. Git Workflow

### 13.1 Branch Strategy

- **SHOULD:** Follow the main/develop branch strategy.
- **SHOULD:** Create feature branches for new components and features.
- **SHOULD:** Use conventional commits for commit messages.

### 13.2 Code Review

- **SHOULD:** Review Ant Design X integration code for proper hook usage.
- **SHOULD:** Check for memory leaks (subscriptions, event listeners).
- **SHOULD:** Verify TypeScript types are properly defined and used.
- **SHOULD:** Ensure accessibility requirements are met.

### 13.3 Pull Request Requirements

- **MUST:** Include tests for new functionality.
- **MUST:** Update documentation for new features.
- **SHOULD:** Include visual screenshots for UI changes.
- **SHOULD:** Provide examples for complex integrations.

---

## 14. Version Migration

### 14.1 Upgrading from v1 to v2

When upgrading from `@ant-design/x` 1.x to 2.x:

- **MUST:** Upgrade Ant Design to the latest version first.
- **MUST:** Review the migration guide at `/docs/react/migration-v2/`.
- **SHOULD:** Update imports to use the new package structure (separate packages for X, X-Markdown, and X-SDK).
- **SHOULD:** Update component props that have changed in v2.
- **SHOULD:** Test all chat functionality after migration.

### 14.2 Package Structure Changes

Ant Design X 2.0 has been restructured into a monorepo with three sub-packages:

- `@ant-design/x` - UI components
- `@ant-design/x-markdown` - Markdown rendering
- `@ant-design/x-sdk` - Data flow management

---

## 15. Contributing

### 15.1 Contribution Guidelines

- **SHOULD:** Follow the contributing guidelines in `CONTRIBUTING.md` from the ant-design repository.
- **SHOULD:** Create issues for bug reports and feature requests.
- **SHOULD:** Submit pull requests with clear descriptions and test coverage.
- **SHOULD:** Participate in GitHub Discussions for questions and ideas.

### 15.2 Community Resources

- **MUST:** Use GitHub Discussions for questions and community support.
- **MUST:** Report bugs through GitHub Issues.
- **SHOULD:** Check existing issues before creating new ones.
- **SHOULD:** Provide minimal reproducible examples for bug reports.

---

## 16. Quick Reference Commands

### 16.1 Development Commands

```bash
# Install dependencies
npm install @ant-design/x @ant-design/x-markdown @ant-design/x-sdk

# Run development server
npm run dev

# Run tests
npm test

# Type check
npm run type-check

# Lint code
npm run lint

# Build for production
npm run build
```

### 16.2 Common Imports

```tsx
// Component library
import { Bubble, Sender, Conversations, XProvider } from '@ant-design/x';

// Markdown rendering
import { XMarkdown } from '@ant-design/x-markdown';

// SDK hooks and utilities
import { useXChat, useXConversations, XRequest } from '@ant-design/x-sdk';

// Chat providers
import { OpenAIChatProvider, QwenChatProvider } from '@ant-design/x-sdk';

// Feedback components
import { Actions, CodeHighlighter, Mermaid } from '@ant-design/x';
```

---

This AGENTS.md file provides comprehensive guidance for AI coding agents working with Ant Design X. By following these guidelines, agents can effectively build, maintain, and extend AI-driven applications using the Ant Design X ecosystem.
