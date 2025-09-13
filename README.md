# Kernel Profile-Based Browser Automation Demo

This TypeScript application demonstrates how to use Kernel's profile-based browser automation system for persistent authentication and task execution across browser sessions.

## 🔌 MCP Integration

This repository is designed to work seamlessly with MCP (Model Context Protocol) clients via [Kernel's MCP server](https://www.onkernel.com/docs/reference/mcp-server). When connected through MCP, you can execute browser automation tasks using natural language on authenticated browser sessions with saved profiles. This enables powerful workflows where AI assistants can interact with authenticated web services on your behalf while maintaining security through isolated browser profiles.

**Key Benefits with MCP:**
- Execute natural language commands directly on authenticated sessions
- Maintain persistent authentication across multiple task executions
- Seamlessly integrate browser automation into AI workflows
- Secure profile isolation ensures data safety

## Overview

This demo showcases Kernel's browser profile capabilities, allowing you to:
- Create persistent browser profiles that maintain authentication state
- Execute automated tasks using pre-authenticated profiles
- Manage browser sessions programmatically with proper cleanup

The application combines [Kernel SDK](https://docs.onkernel.com) for browser orchestration with [Magnitude](https://docs.magnitude.run/getting-started/introduction) for LLM-powered browser automation.

## Features

### 🔐 Persistent Authentication
Create browser profiles that save authentication state (cookies, local storage, etc.) for reuse across sessions

### 🤖 AI-Powered Automation
Execute natural language tasks on authenticated websites using Claude's capabilities

### 🛡️ Stealth Mode
Built-in residential proxy and auto-CAPTCHA solving for reliable automation

### 🎯 Profile Management
Efficiently manage multiple browser profiles for different accounts or use cases

## Prerequisites

- Node.js and npm
- Kernel CLI installed
- Anthropic API key for LLM-powered automation

## Installation

```bash
# Install dependencies
npm install
```

## Configuration

Create a `.env` file with your Anthropic API key:

```env
ANTHROPIC_API_KEY=your_api_key_here
```

## Usage

The application provides three main actions:

### 1. Create Profile Browser

Creates a new browser instance with a persistent profile for authentication:

```bash
kernel invoke profile-auth-and-task-execution create-profile-browser
```

**Returns:**
- `browser_live_view_url`: URL to access the browser for manual authentication
- `profile_name`: Generated unique profile identifier
- `session_id`: Browser session ID for management

**What it does:**
- Generates a unique profile name (e.g., `profile_1234567890_abc123`)
- Creates a new browser with `save_changes: true` to persist authentication
- Provides a live view URL where you can manually log into websites
- Saves all authentication data when the session ends

### 2. End Session and Save Profile

Terminates a browser session and saves the profile state:

```bash
kernel invoke profile-auth-and-task-execution end-session-and-save-profile \
  --payload '{"session_id": "your_session_id"}'
```

**Parameters:**
- `session_id`: The browser session ID to terminate

**Returns:**
- `success`: Boolean indicating if the session was ended successfully

### 3. Execute Task with Profile

Executes automated tasks using an existing profile with pre-loaded authentication:

```bash
kernel invoke profile-auth-and-task-execution execute-task-with-profile \
  --payload '{
    "profile_name": "profile_1234567890_abc123",
    "task": "Go to ign.com and get the latest news stories",
    "url": "https://ign.com"
  }'
```

**Parameters:**
- `profile_name`: The profile to use (from step 1)
- `task`: Natural language description of the task to execute
- `url` (optional): Starting URL for the browser

**Returns:**
- `success`: Boolean indicating task completion
- `task_result`: Result from the executed task
- `session_id`: Browser session ID
- `error`: Error message if task failed

**What it does:**
- Creates a new browser with the existing profile (`save_changes: false`)
- Loads all saved authentication from the profile
- Uses Magnitude with Claude to execute the natural language task
- Properly cleans up the browser session after completion

## Example Workflow

1. **Create a profile and authenticate:**
   ```bash
   kernel invoke profile-auth-and-task-execution create-profile-browser
   ```
   Use the returned `browser_live_view_url` to manually log into your target website.

2. **Save the profile:**
   ```bash
   kernel invoke profile-auth-and-task-execution end-session-and-save-profile \
     --payload '{"session_id": "abc123"}'
   ```

3. **Execute automated tasks with the saved profile:**
   ```bash
   kernel invoke profile-auth-and-task-execution execute-task-with-profile \
     --payload '{
       "profile_name": "profile_1234567890_abc123",
       "task": "Check my notifications and summarize them"
     }'
   ```

## Architecture

### Core Components

- **Kernel SDK** (`@onkernel/sdk`): Manages browser instances, profiles, and invocations
- **Magnitude Core** (`magnitude-core`): Provides browser automation with LLM integration
- **Profile System**: Persists authentication across sessions using named profiles
- **Stealth Features**: Residential proxy and CAPTCHA solving for reliable automation

### How Profiles Work

1. **Profile Creation**: When creating a browser with `save_changes: true`, all browser state (cookies, local storage, session data) is saved to the profile upon session termination
2. **Profile Reuse**: Loading a profile with `save_changes: false` restores all saved state but doesn't modify the original profile
3. **State Persistence**: Profiles maintain authentication indefinitely until explicitly deleted or sessions expire

## Development

```bash
# Run the application directly
npx tsx index.ts

# TypeScript is configured for:
- ESNext target with bundler module resolution
- Strict mode enabled
- Support for .ts and .tsx files
```

## Error Handling

The application includes comprehensive error handling:
- Profile conflict detection (handles existing profiles gracefully)
- API key validation before task execution
- Proper cleanup of browser sessions and agents
- Detailed error messages for debugging

## Security Considerations

- Never commit your `.env` file (already in `.gitignore`)
- API keys are validated before task execution
- Browser sessions are properly terminated to prevent resource leaks
- Profiles are isolated and cannot access other profiles' data

## Documentation

- [Kernel Browsers Documentation](https://www.onkernel.com/docs/browsers/profiles)
- [Kernel SDK Reference](https://docs.onkernel.com)
- [Magnitude Framework](https://docs.magnitude.run/getting-started/introduction)

## License

[Add your license here]

## Support

For issues or questions about:
- Kernel platform: Visit [onkernel.com](https://onkernel.com)
- This demo: Open an issue in this repository