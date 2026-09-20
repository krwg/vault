# VAULT OS

Offline hardware password vault. ESP32-C3 + OLED + 4 buttons + local Wi-Fi web UI.
No cloud, no accounts, no phone app — secrets never leave the device.

## Features
- AES-256-CBC + HMAC-SHA256 (Encrypt-then-MAC), MAC verified before decrypt
- PBKDF2-HMAC-SHA256 → 64-byte key (32 AES + 32 HMAC), random salt + IV
- Atomic writes, failed-password backoff, session cookie, CSRF, HTML escaping
- Random Wi-Fi AP password shown on OLED, master password never stored
- On-device UI: unlock, add/edit/delete, search, generator, change master, auto-lock
- Web UI: full CRUD, search, generator, change master, backup/restore
- Encrypted vault on LittleFS, up to 16 records

## Hardware
| Signal | GPIO |
|--------|------|
| SDA | 8 |
| SCL | 9 |
| R5 (LEFT / CHARSET−) | 3 |
| R6 (RIGHT / CHARSET+) | 4 |
| R7 (ENTER / ACCEPT) | 5 |
| R8 (BACK / DELETE) | 6 |

ESP32-C3 dev board · SSD1306 128×64 I²C OLED · 4 push buttons to GND.

## Buttons
| Button | Short | Long |
|--------|-------|------|
| R5 | prev char | prev charset |
| R6 | next char | next charset |
| R7 | insert char | accept / next field |
| R8 | delete char | cancel / back |

Charsets: `abc` → `ABC` → `123` → `#@!`

## Getting started
1. Flash the sketch.
2. First boot: device starts AP `VAULT-XXXX` (password on OLED).
3. Connect and open `http://192.168.4.1`.
4. Set master password, add records.

Master password is never stored. No recovery.

## Vault format
magic | version | salt(16) | iv(16) | dataLen(4) | ciphertext | HMAC(32)
HMAC covers header + ciphertext. Backup file = vault file.

## Web routes
`/` `/setup` `/unlock` `/add` `/view` `/edit` `/delete` `/search`
`/generator` `/settings` `/change-master` `/backup` `/restore` `/lock`

## Security model
Protected: offline attacker with flash/backup, tampering, CSRF/XSS, brute force.
Not protected: physical glitching, RAM dump while unlocked, weak master password, compromised client browser.
Treat as a convenience vault, not an HSM.

## Limitations
- Max 16 records
- No master-password recovery
- No migration across major versions
- Open (password-protected) Wi-Fi AP while powered
- Time-based auto-lock only

## Build
Arduino IDE (ESP32 Core 3.x) or PlatformIO. Deps: `U8G2`, `mbedtls`, `LittleFS`, `WebServer`.

## License
MIT
