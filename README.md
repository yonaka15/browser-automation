# CDP-MCP

Chrome DevTools Protocol MCP Server - A desktop application that provides browser automation capabilities through the Model Context Protocol (MCP), built with Tauri, React, and Rust.

![CDP-MCP Dashboard](./docs/images/cdp-mcp-dashboard.png)

The CDP-MCP dashboard provides an intuitive interface for managing your MCP server. Simply click "Stop MCP Server" or "Start MCP Server" to control the service, and use the provided command to connect with Claude Code.

## 🚀 Features

- **MCP Server**: Streamable HTTP server using Axum on configurable port (default: 37650)
- **Browser Automation**: Control browsers programmatically via Chrome DevTools Protocol
- **Headless/Headful Mode**: Run browser in background (headless) or with visible window
- **Cross-Platform**: Works on macOS, Windows, and Linux
- **Modern UI**: React-based interface with Tailwind CSS styling
- **Real-time Control**: Start/stop MCP server from the UI

## 📋 Prerequisites

- Node.js (v16 or higher)
- Rust (latest stable)
- npm/pnpm/yarn
- A modern web browser (Chrome, Firefox, Safari, etc.)

## 🛠️ Installation

1. Clone the repository:

```bash
git clone https://github.com/yonaka15/cdp-mcp.git
cd cdp-mcp
```

2. Install dependencies:

```bash
npm install
```

3. Start the development server:

```bash
npm run tauri dev
```

## 🎮 Usage

### Running as MCP Server

The application runs as an MCP Streamable HTTP server using Axum:

```bash
cargo run --release --manifest-path src-tauri/Cargo.toml
```

The server runs on port 37650 and provides:
- HTTP POST endpoint for JSON-RPC 2.0 requests
- HTTP GET endpoint for Server-Sent Events (SSE) streaming
- Session management via Mcp-Session-Id header

### Connect with Claude Desktop

Add to your Claude Desktop configuration:

```json
{
  "mcpServers": {
    "browser-automation": {
      "url": "http://localhost:37650"
    }
  }
}
```

### Browser Mode Configuration

The browser automation tools support both headless (background) and headful (visible window) modes:

- **Headless mode**: Browser runs in the background without a visible window
- **Headful mode (default)**: Browser window is visible for debugging and monitoring

You must first open a browser instance before navigating:

```javascript
// Step 1: Open browser (choose mode)
await browser_open({ headless: false }); // Visible browser window (default)
// or
await browser_open({ headless: true }); // Headless mode

// Step 2: Navigate to URLs
await browser_navigate({ url: "https://example.com" });

// Step 3: Interact with the page
await browser_click({ selector: "button" });
await browser_type({ selector: "input", text: "Hello" });

// Step 4: Close when done
await browser_close();
```


## 🔧 Available MCP Tools

### Core Browser Controls

#### browser_open

Opens a new browser instance in headless or headful mode.

**Parameters:**

- `headless` (boolean, optional): Run in headless mode (default: false)

#### browser_navigate

Navigates to a URL in the browser (requires browser_open first).

**Parameters:**

- `url` (string, required): The URL to navigate to

#### browser_close

Closes the browser and all tabs.

### Page Interaction

#### browser_click

Clicks an element on the page.

**Parameters:**

- `selector` (string, required): CSS selector for the element to click

#### browser_type

Types text into an input field.

**Parameters:**

- `selector` (string, required): CSS selector for the input field
- `text` (string, required): Text to type

#### browser_select_option

Selects an option in a dropdown.

**Parameters:**

- `selector` (string, required): CSS selector for the dropdown
- `values` (array, required): Values to select

### Navigation

#### browser_go_back

Navigates back in browser history.

#### browser_go_forward

Navigates forward in browser history.

#### browser_reload

Reloads the current page.

### Page Content

#### browser_get_content

Gets the HTML content of the current page.

#### browser_screenshot

Takes a screenshot of the current page.

**Parameters:**

- `full_page` (boolean, optional): Capture full page (default: false)

#### browser_snapshot

Gets a snapshot of the current page state including tabs, console messages, and page info.

### JavaScript Execution

#### browser_evaluate

Executes JavaScript in the browser.

**Parameters:**

- `script` (string, required): JavaScript code to execute

#### browser_wait_for

Waits for an element to appear.

**Parameters:**

- `selector` (string, required): CSS selector to wait for
- `timeout` (number, optional): Timeout in milliseconds (default: 30000)

### Tab Management

#### browser_tab_list

Lists all open browser tabs.

#### browser_tab_switch

Switches to a different browser tab.

**Parameters:**

- `index` (number, required): Tab index to switch to

#### browser_tab_close

Closes a specific browser tab.

**Parameters:**

- `index` (number, required): Tab index to close

## 🏗️ Tech Stack

### Frontend

- **React 19** - UI framework
- **TypeScript** - Type safety
- **Tailwind CSS v4** - Styling
- **Vite** - Build tool

### Backend

- **Rust** - Core backend language
- **Tauri v2** - Desktop application framework
- **Axum** - Web framework for MCP Streamable HTTP server
- **Chromiumoxide** - Chrome DevTools Protocol client
- **Tokio** - Async runtime
- **Tower-http** - CORS and middleware support
- **Image crate** - Image processing (JPEG compression)

### Protocol

- **MCP Streamable HTTP** - HTTP/SSE transport with Axum
- **JSON-RPC 2.0** - Message format
- **Server-Sent Events (SSE)** - Real-time server-to-client streaming
- **Chrome DevTools Protocol** - Browser control protocol

## 📁 Project Structure

```
cdp-mcp/
├── src/                      # React frontend
│   ├── App.tsx              # Main UI component with MCP controls
│   ├── App.css              # Tailwind CSS styles
│   └── main.tsx             # Application entry point
├── src-tauri/               # Rust backend
│   ├── src/
│   │   ├── lib.rs           # Tauri commands and server lifecycle
│   │   ├── mcp_server.rs    # MCP Streamable HTTP server (Axum)
│   │   ├── cdp_browser.rs   # Chrome DevTools Protocol integration
│   │   ├── image_processor.rs # Rust image processing for screenshots
│   │   └── config.rs        # Configuration management
│   ├── Cargo.toml           # Rust dependencies
│   └── tauri.conf.json      # Tauri configuration
├── .claude/                 # Claude AI documentation
│   └── CLAUDE.md           # Project context for AI assistance
├── package.json            # Node.js dependencies
├── vite.config.ts          # Vite configuration
└── README.md              # This file
```

## 🔨 Build

Build the application for production:

```bash
npm run tauri build
```

The built application will be available in:

- **macOS**: `src-tauri/target/release/bundle/dmg/`
- **Windows**: `src-tauri/target/release/bundle/msi/`
- **Linux**: `src-tauri/target/release/bundle/appimage/`

## 🧪 Testing

Test the MCP server endpoints:

```bash
# Run the test script
./test-mcp-server.sh
```

Or manually test with curl commands as shown in the Usage section.

## 🦀 Why Rust?

CDP-MCP leverages Rust's unique advantages for browser automation:

### Performance

- **Native Speed** - Compiled to machine code for maximum performance
- **Zero-Cost Abstractions** - High-level code without runtime overhead
- **Efficient Memory Usage** - No garbage collector delays or memory bloat

### Reliability

- **Memory Safety** - Prevents segfaults and memory leaks at compile time
- **Thread Safety** - Fearless concurrency with Send/Sync traits
- **Error Handling** - Robust Result types for predictable error recovery

### Integration

- **chromiumoxide** - Native Rust CDP client for direct Chrome control
- **Tauri v2** - Seamless desktop app integration with minimal resource usage
- **Async/Await** - Modern async runtime with Tokio for concurrent operations

### Developer Experience

- **Type Safety** - Catch errors at compile time, not runtime
- **Pattern Matching** - Elegant handling of complex browser states
- **Cargo Ecosystem** - Rich ecosystem of high-quality libraries

These benefits make CDP-MCP more reliable and performant than JavaScript or Python alternatives, especially for long-running automation tasks.

## 🚧 Roadmap

- [x] MCP Streamable HTTP server with Axum
- [x] Chrome DevTools Protocol integration
- [x] Full browser automation suite (17 tools)
- [x] Screenshot capture with JPEG compression
- [x] Tab management
- [x] Pure Rust implementation
- [x] Window size optimization (800x600)
- [ ] Chrome extension integration
- [ ] Recording and playback of browser actions
- [ ] Browser profile management
- [ ] Cookie management

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Tauri](https://tauri.app/) - For the amazing desktop framework
- [MCP Specification](https://spec.modelcontextprotocol.io/) - For the protocol documentation

## 📞 Support

For issues and questions:

- Open an issue on GitHub
- Check the [CLAUDE.md](.claude/CLAUDE.md) file for detailed technical documentation

---

**Note**: The application supports both connecting to existing Chrome instances with debug port or launching new instances automatically.
