# ⚡ HardcoreRevive

[![Paper Version](https://img.shields.io/badge/Paper-1.20%2B-blue.svg)](https://papermc.io/)
[![Java Version](https://img.shields.io/badge/Java-17%20%2F%2021-orange.svg)](https://adoptium.net/)
[![Vault Hook](https://img.shields.io/badge/Vault-Integrated-brightgreen.svg)](https://www.spigotmc.org/resources/vault.34315/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A complete Hardcore death-management and bleedout plugin built for **Paper 1.20+**. 

Instead of an instant permanent ban upon dying, **HardcoreRevive** places players into an immersive downed bleedout state. Teammates can rush to perform an interactive resuscitation, or players can purchase a costly self-revive through Vault-backed economy. If the timer runs out—or if they attempt to combat-log—the ban hammer falls.

---

## ✨ Features

- **🛡️ Intercepted Hardcore Death:** Cancels fatal elimination safely, preserves player inventories, and pulls victims out of the void before placing them in bleedout.
- **🩸 Downed Bleedout State:** Freezes victims with blindness/glowing effects, updates an active action bar countdown, and emits an accelerating heartbeat sound.
- **🪦 Interactive Tombstone Beacons:** Generates holographic markers and soul-fire particle beacons over downed bodies for one-click right-click resuscitation.
- **💰 Economy & Vault Support:** Fully integrated with Vault (EssentialsX, etc.) for configurable revive fees and dynamic streak pricing.
- **⏱️ Automated Tempbans:** Imposes configurable 12-hour bans synced to `bans.yml` with real-time countdown screens on reconnect attempts.
- **🚨 Combat-Log Prevention:** Disconnecting while downed immediately triggers death penalties and initiates the tempban.

---

## 🎮 Supported & Tested Versions

| Server Software | Target / Baseline | Tested Versions | Status |
| :--- | :--- | :--- | :--- |
| **Paper / Purpur** | `1.20.4` | `1.20.x`, `1.21.x`, **Latest Release** | ![Supported](https://img.shields.io/badge/Tested-Working-brightgreen.svg) |
| **Java Runtime** | `Java 17` | `Java 17`, `Java 21` | ![Supported](https://img.shields.io/badge/Compatible-Passing-brightgreen.svg) |

> ℹ️ *Note: Built against the `1.20.4-R0.1-SNAPSHOT` Paper API baseline, ensuring backward compatibility across 1.20 while remaining fully functional on modern Paper builds up to the latest release.*

## 🛠️ Architecture & Core Components

<details>
<summary><b>1. Build & Plugin Metadata</b></summary>

* **`plugin.yml`**: Configured with `api-version: '1.20'`, soft dependency on `Vault`, and built-in aliases (`/revive`, `/reviveself`, `/reviveforce`, `/hardcorerevive`).
</details>

<details>
<summary><b>2. Economy Integration</b></summary>

* **`VaultHook.java`**: Interfaces with Vault's Registered Service Provider. Safely verifies player balances prior to executing transactions, deducts revival fees, and handles localized currency formatting.
</details>

<details>
<summary><b>3. Death Interception & Downed State</b></summary>

* **`PlayerDeathListener.java`**: Cancels `PlayerDeathEvent` at `EventPriority.HIGHEST`. Rescues falling players from the void onto the nearest solid block above.
* **`DownedManager.java`**: Manages the victim's state (`FROZEN` survival with slowness 255, jump locks, glowing, and darkness, or `SPECTATOR`). Broadcasts interactive clickable JSON messages (`/revive <player>`) and drives a 1-second task loop updating action bars and heartbeat audio cues.
* **`DownedPlayerListener.java`**: Restricts movement to camera panning only. Cancels incoming/outgoing damage, blocks interactions, item pickups, drops, and inventory tampering. Enforces a strict command whitelist (blocking `/home`, `/spawn`, `/tpa`).
</details>

<details>
<summary><b>4. Revive Beacons & Tombstones</b></summary>

* **`TombstoneManager.java`**: Spawns an ArmorStand wearing the victim's player skull, floating holograms (`[RIGHT-CLICK] to Revive ($400)`, `Bleedout in: X seconds`), and soul-fire particle rings.
* **`TombstoneInteractListener.java`**: Listens for right-click interactions on tombstones to immediately process teammate or self revivals. Cleans up entities on rescue, bleedout, reload, or server stop.
</details>

<details>
<summary><b>5. Tempbans & Anti-Abuse</b></summary>

* **`BanManager.java`**: Manages 12-hour tempbans synced with Bukkit's native `BanList` and logged to `data/bans.yml`.
* **`PlayerConnectionListener.java`**: Instantly bans players who combat-log while downed. Uses `AsyncPlayerPreLoginEvent` to display a custom countdown (`Remaining Ban: Xh Ym Zs`).
* **Dynamic Pricing:** Supports configurable streak scaling in `config.yml` (e.g., +$100 per death occurring within 60 minutes).
</details>

---

## 📜 Commands & Permissions

| Command | Description | Default Cost / Node | Permission |
| :--- | :--- | :--- | :--- |
| `/reviveself` | Self-revive from bleedout | `$400` | `hardcorerevive.revive.self` |
| `/revive <player>` | Revive a downed teammate | `$400` | `hardcorerevive.revive.others` |
| `/reviveforce <player>` | Admin override revive (free) | Free | `hardcorerevive.admin.force` |
| `/hardcorerevive reload` | Reload configuration files | Admin | `hardcorerevive.admin` |
| `/hardcorerevive unban <player>`| Manually remove death tempban | Admin | `hardcorerevive.admin` |
| `/hardcorerevive down <player>` | Force player into downed state | Admin | `hardcorerevive.admin` |
| `/hardcorerevive list` | View active downed players | Admin | `hardcorerevive.admin` |
| `/hardcorerevive info` | Check plugin diagnostics | Admin | `hardcorerevive.admin` |

> **Bypass Permission:** `hardcorerevive.bypass`  
> *(Grants immunity from the bleedout state and standard death ban penalties).*

---

## ⚙️ Installation

1. Ensure your server runs **Paper 1.20+** on **Java 17 or Java 21**.
2. Install **[Vault](https://www.spigotmc.org/resources/vault.34315/)** and an economy plugin (e.g., EssentialsX).
3. Drop `HardcoreRevive.jar` into your server's `/plugins/` folder.
4. Restart your server to generate `plugins/HardcoreRevive/config.yml`.
5. Customize revival costs, bleedout timers, and ban durations to your liking.

---
