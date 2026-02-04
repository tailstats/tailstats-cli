# TailStats CLI

Push data to your [TailStats](https://tailstats.com) app from the command line. Available on macOS, iOS, Android, and Windows (coming soon).

## Quick Install (macOS / Linux)

```bash
curl -fsSL https://tailstats.com/install | sh
```

## Manual Download

| Platform | Download |
|----------|----------|
| macOS (Apple Silicon) | [tailstats-darwin-arm64](https://raw.githubusercontent.com/tailstats/tailstats-cli/main/tailstats-darwin-arm64) |
| macOS (Intel) | [tailstats-darwin-amd64](https://raw.githubusercontent.com/tailstats/tailstats-cli/main/tailstats-darwin-amd64) |
| Linux (x86_64) | [tailstats-linux-amd64](https://raw.githubusercontent.com/tailstats/tailstats-cli/main/tailstats-linux-amd64) |
| Linux (ARM64) | [tailstats-linux-arm64](https://raw.githubusercontent.com/tailstats/tailstats-cli/main/tailstats-linux-arm64) |
| Windows | [tailstats-windows-amd64](https://raw.githubusercontent.com/tailstats/tailstats-cli/main/tailstats-windows-amd64) |

## Installation

### macOS / Linux

```bash
# Download (example for Apple Silicon Mac)
curl -L -o tailstats https://raw.githubusercontent.com/tailstats/tailstats-cli/main/tailstats-darwin-arm64

# Make executable
chmod +x tailstats

# Move to PATH
sudo mv tailstats /usr/local/bin/tailstats

# Verify
tailstats --version
```

### Windows

1. Download `tailstats-windows-amd64.exe`
2. Rename to `tailstats.exe`
3. Add to your PATH or run directly

---

## Quick Start

### 1. Login

```bash
tailstats login
```

Enter your TailStats email and password. Your API token is saved to `~/.tailstats.yaml`.

### 2. Create a Card

In the [TailStats app](https://app.tailstats.com), create a card with source type **"Remote Push"** and note the card ID.

### 3. Push Data

```bash
tailstats push my-card "Hello World"
```

---

## Usage

### Push Data

```bash
tailstats push <card-id> [value] [flags]
```

**Examples:**

```bash
# Simple value
tailstats push my-card "Success"

# With title and color
tailstats push deploy "Completed" --title "Deploy Status" --color green

# Progress bar (0.0 to 1.0)
tailstats push download 0.75 --type progress

# Status indicator
tailstats push server "Online" --type status --color green

# Number with formatting
tailstats push revenue 12450 --type number --format currency:USD

# With icon
tailstats push build "Passing" --icon checkmark.circle.fill --color green

# Show in menu bar
tailstats push cpu 75 --type progress --menubar

# With notification
tailstats push alert "Error!" --color red --notify "Check the server" --notify-emoji "🔥"

# From JSON
tailstats push my-card --json '{"title":"Stats","value":42,"emoji":"📊"}'
```

**Flags:**

| Flag | Description |
|------|-------------|
| `-t, --title` | Item title |
| `--type` | Value type: `string`, `number`, `progress`, `status`, `array`, `gauge` |
| `--format` | Format: `percent`, `currency:USD`, `number:compact` |
| `--icon` | SF Symbol name |
| `--emoji` | Emoji character |
| `--color` | Color: `red`, `green`, `blue`, `yellow`, or hex `#FF0000` |
| `--menubar` | Show in macOS menu bar |
| `--notify` | Send push notification |
| `--notify-emoji` | Notification emoji |
| `--notify-sound` | Notification sound (`default` for system) |
| `--json` | Full JSON payload |
| `-f, --file` | JSON file path |
| `--local` | Force local API |
| `--remote` | Force remote API |
| `--verbose` | Show debug output |

### Send Notification

macOS local notification (macOS only):

```bash
tailstats notify "Build completed!" --title "CI" --sound default
```

### List Cards

```bash
tailstats cards
```

### Configuration

```bash
# Set API key manually
tailstats config set api_key "your-token"

# View config
tailstats config list

# Show config path
tailstats config path
```

---

## Configuration

Config file: `~/.tailstats.yaml`

```yaml
api_key: "1|abc123..."
remote_url: "https://app.tailstats.com"
local_url: "http://localhost:9876"
default_mode: "auto"
```

**Environment variables:**

```bash
export TAILSTATS_API_KEY="your-token"
```

---

## Examples

### CI/CD Pipeline

```bash
tailstats push build "Building..." --color blue

if make build; then
  tailstats push build "Success" --color green --notify "Build passed!" --notify-emoji "✅"
else
  tailstats push build "Failed" --color red --notify "Build failed!" --notify-emoji "❌"
fi
```

### System Monitoring

```bash
#!/bin/bash
while true; do
  cpu=$(top -l 1 | grep "CPU usage" | awk '{print $3}' | tr -d '%')
  tailstats push cpu "$cpu" --type progress --title "CPU" --local
  sleep 5
done
```

### Cron Job

```cron
* * * * * /usr/local/bin/tailstats push clock "$(date +%H:%M)" --title "Time"
0 * * * * /usr/local/bin/tailstats push disk "$(df -h / | tail -1 | awk '{print $5}')" --type progress
```

### GitHub Actions

```yaml
- name: Update TailStats
  env:
    TAILSTATS_API_KEY: ${{ secrets.TAILSTATS_API_KEY }}
  run: tailstats push deploy "v${{ github.ref_name }}" --color green
```

---

## Help

```bash
tailstats --help
tailstats push --help
```

## Documentation

Full documentation: [tailstats.com/docs](https://tailstats.com/docs)

## Support

- Website: [tailstats.com](https://tailstats.com)
