[![Releases](https://img.shields.io/github/v/release/LUTOPID/tiptap-ai-autocomplete?label=Releases&logo=github)](https://github.com/LUTOPID/tiptap-ai-autocomplete/releases)

# Tiptap AI Autocomplete: Streaming Text Editing Extension for TipTap

A TypeScript TipTap extension that adds AI-powered autocomplete and streaming preview inside the editor. It connects to AI backends, streams partial tokens as they arrive, and applies edits or suggestions in real time. Built for React apps and modern bundles.

![Editor streaming preview demo](https://raw.githubusercontent.com/LUTOPID/tiptap-ai-autocomplete/main/assets/demo.gif)

- Topics: ai · ai-writing · autocomplete · editor · openrouter · react · streaming · text-editing · tiptap · typescript
- Releases: https://github.com/LUTOPID/tiptap-ai-autocomplete/releases

Table of contents
- Features
- Demo
- Quick install
- Browser / Node build
- React + TipTap example
- API: Extension options
- Streaming model adapters
- Customization and commands
- Performance and best practices
- Releases (download and execute)
- Contributing
- License

Features
- Autocomplete suggestion engine that hooks into TipTap input rules and keymaps.
- Streaming preview: show tokens as they arrive from the model.
- Inline and block suggestions. Replace or append text.
- Multiple model adapters (OpenRouter, HTTP, WebSocket).
- Token-level controls: flush, commit, rollback.
- Safe edit transactions. Runs as native TipTap extension.
- TypeScript types and React hooks.
- Small bundle footprint. Works with modern bundlers.

Why this extension
- Keep the editor responsive while AI generates text.
- Let users preview suggestions as they stream.
- Give full control to the app for commit and cancel events.
- Use with local or hosted AI services.

Demo and screenshots
- Live demo GIF: shows streaming tokens and accept/reject flow.
- Screenshot: extension UI with suggestion preview and accept button.

Quick install
Use npm or yarn in your project.

npm
```bash
npm install tiptap-ai-autocomplete @tiptap/core @tiptap/starter-kit
```

yarn
```bash
yarn add tiptap-ai-autocomplete @tiptap/core @tiptap/starter-kit
```

Peer dependencies
- @tiptap/core
- @tiptap/starter-kit
- react (for hooks and components)
- typescript (types included)

Browser / Node build
The package ships ESM and typings. For client-side apps use the ESM build. For SSR or Node-based rendering the package exposes small utilities that do not require DOM.

React + TipTap example
This example shows a minimal React component that uses the extension inside a TipTap editor instance and shows a streaming preview.

```tsx
import React from "react";
import { EditorContent, useEditor } from "@tiptap/react";
import StarterKit from "@tiptap/starter-kit";
import { AiAutocomplete } from "tiptap-ai-autocomplete";
import { OpenRouterAdapter } from "tiptap-ai-autocomplete/adapters";

export default function EditorWithAI() {
  const editor = useEditor({
    extensions: [
      StarterKit,
      AiAutocomplete.configure({
        adapter: new OpenRouterAdapter({
          apiKey: process.env.NEXT_PUBLIC_OPENROUTER_KEY!,
          model: "gpt-4o-mini",
          stream: true,
        }),
        trigger: "/",
        maxTokens: 120,
      }),
    ],
    content: "<p>Start typing and invoke AI with /</p>",
  });

  return <EditorContent editor={editor} />;
}
```

How streaming works
- The adapter opens a connection (HTTP streaming or WS).
- The model sends partial tokens.
- The extension inserts a non-committed preview node into the document.
- The app can accept or reject. Accept commits the text in one TipTap transaction.

API: Extension options
- adapter: ModelAdapter
  - Adapter instance that implements stream start/stop and token events.
- trigger: string | RegExp
  - Characters that invoke autocomplete (like "/" or "@").
- maxTokens: number
  - Max tokens to request per suggestion.
- debounce: number
  - Milliseconds to wait before calling model on fast typing.
- previewNodeType: string
  - Node type name used for preview rendering.
- applyOnCommit: "insert" | "replace"
  - How to apply final text when user accepts suggestion.

Example types (TypeScript)
```ts
type AiAutocompleteOptions = {
  adapter: ModelAdapter;
  trigger?: string | RegExp;
  maxTokens?: number;
  debounce?: number;
  previewNodeType?: string;
  applyOnCommit?: "insert" | "replace";
};
```

Model adapters
- OpenRouterAdapter
  - Uses OpenRouter endpoints. Supports streaming.
  - Sample options: { apiKey, model, stream }
- HttpStreamAdapter
  - Generic HTTP POST streaming adapter. Works with SSE or chunked responses.
- WebSocketAdapter
  - For persistent socket connections and low-latency tokens.

Adapter example
```ts
const adapter = new OpenRouterAdapter({
  apiKey: process.env.OPENROUTER_KEY,
  model: "gpt-4o-mini",
  stream: true,
});

const aiExt = AiAutocomplete.configure({ adapter, trigger: "/", maxTokens: 80 });
```

Commands and events
- editor.commands.aiStart(options)
  - Start a suggestion at current selection.
- editor.commands.aiStop()
  - Stop streaming and clear preview.
- editor.commands.aiAccept()
  - Accept current streamed suggestion.
- editor.commands.aiReject()
  - Reject and remove preview.
- Events (editor.on)
  - "ai:token" — fired for each token chunk.
  - "ai:commit" — fired when suggestion commits.
  - "ai:error" — fired on adapter error.

Customization and UI
- Replace the default preview node rendering using a custom node view.
- Customize accept/cancel buttons and shortcut keys.
- Provide your own toolbar or floating UI for suggestions.
- Use theme CSS to style preview tokens.

Performance and best practices
- Use small token budgets for inline use. Reserve larger budgets for block-level suggestions.
- Debounce calls on fast typing. The extension supports a debounce option.
- Use model-side streaming to get early tokens.
- Manage network retries in adapter configurations.
- Unmount adapters on editor destroy to free sockets and cancel requests.

Security and privacy
- Keep API keys on server when possible. Use a proxy endpoint or short-lived tokens.
- Use adapter modes that support server-side streaming when you must protect keys.

Releases
Download and execute the release artifacts from the releases page:
https://github.com/LUTOPID/tiptap-ai-autocomplete/releases

Go to the releases page and download the build that matches your environment. Each release contains a distribution archive and an optional install script. After download, run the included installer or extract the archive and copy the package into your build pipeline. Example commands (run in your shell):

```bash
# download the release archive (example)
curl -L -o tiptap-ai-autocomplete.tar.gz https://github.com/LUTOPID/tiptap-ai-autocomplete/releases/download/vX.Y.Z/tiptap-ai-autocomplete-vX.Y.Z.tar.gz

# extract
tar -xzf tiptap-ai-autocomplete.tar.gz

# run the included installer if present
chmod +x install.sh
./install.sh
```

Contributing
- Fork the repo.
- Create a branch per feature or bug.
- Add tests for core behaviors (transaction integrity, token streaming).
- Open a pull request with a clear description and change log.
- Follow the coding style: TypeScript, explicit types, and small, focused commits.

Issue templates
- Bug: include reproduction steps and editor setup.
- Feature: describe the UX, API shape, and expected behavior.

Testing
- Unit tests cover adapter flows and tiptap transactions.
- Use JSDOM for node tests and Playwright for integration tests with a real browser.

Changelog
- Each release contains a CHANGELOG.md file with highlights, breaking changes, and migration steps. Check the releases page to find the latest package and changelog: https://github.com/LUTOPID/tiptap-ai-autocomplete/releases

Examples and recipes
- Recipe: Inline suggest with slash trigger
- Recipe: Accept suggestion by Tab key
- Recipe: Use with collaborative editing (sharedb / yjs)
- Example apps live in the /examples folder. Run them with npm run dev.

Packaging and publishing
- The package publishes ESM and types. The build step runs tsc and rollup.
- For private registries, set NPM_TOKEN and use the provided publish script.

License
- MIT. See the LICENSE file in the repository.

Contact and support
- Open issues for bugs and feature requests.
- Start a discussion thread for bigger changes or migration help.

Badges
[![Version](https://img.shields.io/github/v/release/LUTOPID/tiptap-ai-autocomplete)](https://github.com/LUTOPID/tiptap-ai-autocomplete/releases)
[![License](https://img.shields.io/github/license/LUTOPID/tiptap-ai-autocomplete)](https://github.com/LUTOPID/tiptap-ai-autocomplete/blob/main/LICENSE)
[![Topics](https://img.shields.io/badge/topics-ai%20writing%20autocomplete-editor-blue)](https://github.com/LUTOPID/tiptap-ai-autocomplete)

Assets and images
- Demo GIF: assets/demo.gif
- SVG icon: assets/icon.svg

Projects using this extension
- internal CMS
- note-taking apps
- inline code assistants

Repository layout
- src/ — TypeScript source
- dist/ — built packages
- adapters/ — model adapters
- examples/ — example apps and recipes
- test/ — unit and integration tests
- docs/ — usage docs and API reference

Use the releases page to get released builds and installers:
https://github.com/LUTOPID/tiptap-ai-autocomplete/releases