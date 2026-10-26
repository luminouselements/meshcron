# MeshWeather Publisher

This script fetches current weather data from OpenWeatherMap and publishes a formatted message over a Meshtastic mesh network via a local node.

## Prerequisites

Before running this script, ensure you have the following:
1.  **Python 3:** Installed on your system.
2.  **Python Libraries:** The required dependencies must be installed (see `requirements.txt`).
3.  **Meshtastic Node:** A physical device connected to your network, acting as the gateway to the mesh network.

## Setup

1.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

2.  **Configuration:**
    You can configure the script's behavior by either modifying the `meshcron.py` file or by providing arguments when running it.

    **Key Parameters:**
    *   `--node`: IP address of the Meshtastic Node (Default: `192.168.50.49`).
    *   `--api_key`: Your OpenWeatherMap API Key. **Highly recommended to use environment variables for this.**
    *   `--channel`: Meshtastic channel index to broadcast on (Default: `5`).
    *   `--city`: Target city name for weather tracking (Default: `Tampa`).

## Usage

The script must be run with sufficient permissions to write to the log directory (`# MeshWeather Publisher

This script fetches current weather data from OpenWeatherMap and publishes a formatted message over a Meshtastic mesh network via a local node.

## Prerequisites

Before running this script, ensure you have the following:
1.  **Python 3:** Installed on your system.
2.  **Python Libraries:** The required dependencies must be installed (see `requirements.txt`).
3.  **Meshtastic Node:** A physical device connected to your network, acting as the gateway to the mesh network.

## Setup

1.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

2.  **Configuration:**
    You can configure the script's behavior by either modifying the `meshcron.py` file or by providing arguments when running it.

    **Key Parameters:**
    *   `--node`: IP address of the Meshtastic Node (Default: `192.168.50.49`).
    *   `--api_key`: Your OpenWeatherMap API Key. **Highly recommended to use environment variables for this.**
    *   `--channel`: Meshtastic channel index to broadcast on (Default: `5`).
    *   `--city`: Target city name for weather tracking (Default: `Tampa`).

## Usage

The script must be run with sufficient permissions to write to the log directory (`/scripts/logs/`).

```bash
python3 meshcron.py --city "YourCity" --api_key "YOUR_API_KEY"
```

### Running Periodically

For automated use, it is recommended to use a system scheduler like `cron` (Linux/macOS) or Task Scheduler (Windows).

**Example Crontab Entry (to run every 30 minutes):**
*Remember to replace paths and placeholders.*

```cron
# M H D M W command
*/30 * * * * /usr/bin/python3 /path/to/meshcron.py --city "Tampa" --api_key "YOUR_API_KEY" >> /path/to/cron.log 2>&1
```

## Logging & Locking

*   **Logging:** Logs are written to `/logs/mesh_weather.log`.
*   **Locking:** The script uses a file lock (`/logs/mesh_weather_lock.pid`) to ensure only one instance runs at a time, preventing race conditions.
```

### `requirements.txt`

```text
requests
meshtastic
```

### `env.example`

```bash
# --- Environment Variables for meshcron.py ---

# REQUIRED: Your OpenWeatherMap API Key
# Get this from https://openweathermap.org/api
OPENWEATHERMAP_API_KEY="your_actual_openweathermap_api_key"

# OPTIONAL: The IP address of your Meshtastic gateway node
# Default in script is 192.168.50.49
MESHTASTIC_NODE_IP="192.168.50.49"

# OPTIONAL: The Meshtastic channel index to use
# Default in script is 5
MESHTASTIC_CHANNEL=5

# OPTIONAL: The default city to track
# Default in script is Tampa
TARGET_CITY="Tampa"
```

### `SECURITY.md`

```markdown
# Security Guide for MeshWeather Publisher

This document outlines security considerations for running `meshcron.py`.

## ⚠️ API Key Management (High Priority)

The script requires an OpenWeatherMap API Key. **Treat this key as a secret credential.**

**DO NOT** hardcode your API key directly into the `meshcron.py` source code, especially if the code repository is public.

**Recommended Practice:**
Use environment variables. Although the script currently defaults to hardcoded values, you should update `main()` to read the key from an environment variable.

**Change Required (Conceptual):**
Modify `main()` to use `os.getenv("OPENWEATHERMAP_API_KEY")` instead of passing the default hardcoded key when calling `get_weather_data`.

## 🔒 Execution and Locking

The script implements a file-based locking mechanism (`LOCK_FILE`).

*   **Purpose:** This prevents multiple instances from running concurrently, which could lead to race conditions, API rate limiting, or corrupted logs/messages.
*   **Security:** The lock file mechanism is reasonably robust, checking for expiry. However, in highly adversarial environments, race conditions involving file system access are possible, though unlikely for typical cron job setups.

## 🌐 Network & Input Validation

1.  **Node IP Address:** The IP address (`--node`) should be configured to point only to trusted, local gateway hardware.
2.  **Input Sanitization:** The script performs basic logging and truncation for messages. Be aware that while the message text is restricted to 200 characters, any complex, multi-line input (like the city name or API key) should be validated before being passed to the script's CLI arguments if the inputs are user-provided at runtime.

## ✨ Summary Checklist

- [ ] **Environment Variables:** Use `os.getenv()` to load API keys instead of hardcoding them.
- [ ] **Least Privilege:** Run the script under a dedicated, non-root user account with only write access to the log directory (`/logs/`).
- [ ] **Network Segmentation:** Ensure the machine running the script is appropriately firewalled if it accesses external networks./logs/`).

```bash
python3 meshcron.py --city "YourCity" --api_key "YOUR_API_KEY"
```

### Running Periodically

For automated use, it is recommended to use a system scheduler like `cron` (Linux/macOS) or Task Scheduler (Windows).

**Example Crontab Entry (to run every 30 minutes):**
*Remember to replace paths and placeholders.*

```cron
# M H D M W command
*/30 * * * * /usr/bin/python3 /path/to/meshcron.py --city "Tampa" --api_key "YOUR_API_KEY" >> /path/to/cron.log 2>&1
```

## Logging & Locking

*   **Logging:** Logs are written to `/logs/mesh_weather.log`.
*   **Locking:** The script uses a file lock (`/logs/mesh_weather_lock.pid`) to ensure only one instance runs at a time, preventing race conditions.
```