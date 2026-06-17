# Smart Email Assistant Extension

A Chrome extension that adds an **AI Reply** button to Gmail compose windows. When clicked, the extension reads the current email thread, sends it to a local backend API, and inserts the generated professional reply into the Gmail compose box.

## Features

- Injects an **AI Reply** button into the Gmail compose toolbar.
- Detects Gmail compose windows dynamically with a `MutationObserver`.
- Extracts visible email content from common Gmail message containers.
- Sends the email content to a local API endpoint for reply generation.
- Inserts the generated response directly into the active Gmail compose editor.
- Shows a loading state while the reply is being generated.

## How It Works

The main logic lives in `content.js`.

1. The content script runs on `https://mail.google.com/*`.
2. It watches the page for Gmail compose windows.
3. When a compose toolbar is found, it adds an **AI Reply** button.
4. Clicking the button sends a `POST` request to:

```text
http://localhost:8080/api/email/generate
```

The request body is JSON:

```json
{
  "emailContent": "Extracted Gmail email text",
  "tone": "professional"
}
```

The backend should return the generated email reply as plain text. The extension then inserts that text into Gmail's compose box.

## Project Structure

```text
.
├── content.js       # Gmail page logic and AI Reply button behavior
├── content.css      # Styles for the injected AI Reply button
├── manifest.json    # Chrome extension manifest
├── icons/           # Extension icons
└── README.md
```

## Requirements

- Google Chrome or another Chromium-based browser.
- Gmail account opened at `https://mail.google.com/`.
- A backend service running locally on port `8080`.
- Backend endpoint:

```text
POST /api/email/generate
```

The extension is configured with host permissions for:

```text
https://mail.google.com/*
http://localhost:8080/*
```

## Installation

1. Open Chrome and go to:

```text
chrome://extensions/
```

2. Enable **Developer mode**.
3. Click **Load unpacked**.
4. Select this extension folder.
5. Open or refresh Gmail.

## Usage

1. Start the local backend API on `http://localhost:8080`.
2. Open Gmail.
3. Open an email and click reply, or open a compose window.
4. Click **AI Reply** in the compose toolbar.
5. Wait for the generated text to appear in the compose box.

## Troubleshooting

### The AI Reply button does not appear

- Refresh Gmail after loading the extension.
- Make sure the extension is enabled in `chrome://extensions/`.
- Open a Gmail compose or reply window.
- Check the browser console for `Email Writer Extension - Content Script Loaded`.

### The button says "Failed to generate reply"

- Confirm the backend is running on `http://localhost:8080`.
- Confirm the backend exposes `POST /api/email/generate`.
- Make sure the backend returns a successful response with plain text.

### The generated reply is not inserted

- Make sure a Gmail compose editor is open and focused.
- Gmail DOM changes can affect selectors used by the extension. Check `content.js` if Gmail updates its toolbar or editor structure.

## Notes

- The extension currently uses a fixed tone of `professional`.
- The API URL is hardcoded in `content.js`.
- The extension is intended for local development with a locally running backend.
