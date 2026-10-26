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
- [ ] **Least Privilege:** Run the script under a dedicated, non-root user account with only write access to the log directory (`/scripts/logs/`).
- [ ] **Network Segmentation:** Ensure the machine running the script is appropriately firewalled if it accesses external networks.