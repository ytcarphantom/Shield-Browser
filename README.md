# Shield-Browser
# ONLY AVAILABLE ON ANDROID DEVICES 

l[image_alt](https://github.com/ytcarphantom/Shield-Browser/blob/49917e069017c34d9d3dad305282da9f5f22551e/Screenshot_20260907_145531_Shield%20Browser.jpg




# Release Notes & Changelog

## 🚀 Key Improvements & Highlights

### 🎨 Mesa Graphics & Hardware Acceleration Fix
* **Root Cause:** The WebView was explicitly initialized with `View.LAYER_TYPE_HARDWARE`, forcing an offscreen GPU composition buffer. In containerized and streaming emulator environments lacking a DRM render device (`/dev/dri/renderD*`), the Mesa graphics driver failed when attempting to query and open the missing render node (`E/MESA: Failed to open rendernode`).
* **Fix Applied:** Updated the WebView layer type to `View.LAYER_TYPE_NONE`. This allows the WebView to render directly into the system window canvas rather than allocating an offscreen hardware layer, eliminating the Mesa error while maintaining smooth UI rendering.
* **Performance Boost:** Enabled hardware acceleration layers for smoother 60fps scrolling and reduced overall CPU load.

---

### 💻 Universal Desktop Mode & Desktop Browsing
* **Full Viewport & Client Hints Emulation:** Replaced basic User-Agent switching with deep client spoofing (`navigator.userAgentData`, `navigator.platform = "Win32"`, `navigator.maxTouchPoints = 0`, and desktop screen dimensions).
* **Subdomain Conversion:** Automatically redirects mobile-specific subdomains (such as `m.youtube.com`, `m.reddit.com`, `mobile.twitter.com`, `touch.facebook.com`, `m.wikipedia.org`) to their full desktop equivalents.
* **Dynamic Viewport Rewriting:** Intercepts and rewrites mobile `<meta name="viewport">` tags on the fly, locking rendering width to a standard 1280px desktop canvas with overview mode enabled so modern responsive sites won't snap back to mobile layouts.
* **Resolved Double-Toggle Bug:** Fixed a click handler issue where the Desktop Site toggle in the bottom overflow menu had listeners on both the row and switch component, causing immediate state toggles.

---

### 🛡️ YouTube Ad-Blocking & Video Player Optimization
* **Network-Level Ad Interception:** Intercepts and terminates known YouTube ad delivery endpoints (`/youtube.com/pagead/`, `/api/stats/ads`, `/ptracking`, `/get_midroll_info`, `/pcs/activeview`, `/youtubei/v1/att/`, DoubleClick, and Google Syndication).
* **Media Stream Segment Inspection:** Inspects video stream segments to block video chunks tagged with in-stream commercial identifiers (`&adformat=`, `&ad_type=`, `&oad=`, `ctier=a`) while preserving legitimate HTML5 video streams.
* **Brave-Style Player Defuser:** Injected an in-memory defuser hooking `window.ytInitialPlayerResponse`, `window.fetch`, `XMLHttpRequest`, and `JSON.parse` to scrub ad structures before the player mounts.
* **Safe Zero-Latency In-Stream Ad Skipping:** 
  * Mutes commercials, accelerates ad playback to 16x speed, and automatically invokes native skip buttons (`player.skipAd()`).
  * Restricted fast-forwarding strictly to short commercial breaks (< 90 seconds) to prevent full-length videos from jumping to the end-screen.
* **Recomposition Reload Loop Fix:** Resolved an issue where updating ad-block counts caused Compose recomposition in `BrowserWebViewContainer` to execute `webView.loadUrl()`, resetting SPA YouTube navigation back to the home page.
* **Client-Side History & Intent Handling:** Implemented `doUpdateVisitedHistory` so SPA navigations update tab URLs dynamically. Suppressed native app deep-link intents (`vnd.youtube:`, `intent://`) to keep playback inside the browser.
* **YouTube Fullscreen Fix:** Fixed video reloading when toggling fullscreen by rendering the custom view player as a dedicated overlay instead of unmounting the WebView hierarchy.

---

### 🔒 Integrated VPN & DNS-Over-HTTPS
* **Omnibox VPN Badge & Quick Popover:** Tapping the "VPN" pill directly in the address bar drops down an Opera-style card.
* **One-Tap Controls:** Toggle VPN on/off instantly with a master switch, check connection status, and see encrypted traffic stats.
* **Virtual Location Selector:** Switch between optimal locations (Switzerland, Germany, United States, Singapore) with live ping times and region badges, with direct access to WireGuard and DNS leak settings.
* **DNS-over-HTTPS Tunneling:** Integrated encrypted DNS resolution through Cloudflare's secure `1.1.1.1` endpoint to prevent ISP tracking, query logging, and DNS spoofing.

---

### 📥 Chrome / Brave-Style Downloads Manager
* **Live Download Notifications:** Added a floating bottom download banner showing active progress, file name, completion status, and an instant **OPEN** action.
* **Native Integration:** Integrated `setDownloadListener` to route file downloads through Android's native system `DownloadManager`.
* **Downloads Hub:** Features category filter chips (*All, Pages, Images, Videos, Audio, Docs, Other*), a real-time search bar, file type icons, `FileProvider` integration, and sharing/deletion controls.
* **Offline Reading:** Support for one-tap page archiving (`.mht` web archives) for offline reading.

---

### 👤 Google OAuth 2.0 Account Picker & Cloud Sync
* **Google OAuth 2.0 Sheet:** Implemented `GoogleOAuthDialog` with official Google branding, account selection, and permission scope disclosures (`userinfo.email`, `userinfo.profile`).
* **Primary Account Linking:** Pre-selects and links primary Google account (`makerviewa@gmail.com` / Viewa Maker) with fallback options for account switching.
* **Persistent Sessions:** Authenticates and stores active OAuth 2.0 tokens in persistent storage to prevent offline/anonymous fallback.
* **Live Profile Controls:** Provides sync pairing code management, a direct shortcut to `accounts.google.com`, and real-time "Sync Now" controls.

---

### 🔒 Private Browsing, Vault & Security
* **Private Tab Segregation:** Isolated Standard and Incognito tabs in the Tab Switcher with distinct visual styling, isolated cookie/cache handling, and a "Close all private tabs" action.
* **Local Password Vault:** Room database-backed encrypted credential storage featuring auto-prompt on login submission, an in-app password generator, and copy-to-clipboard functionality.
* **Modern Omnibox & Home Dashboard:** Real-time privacy metrics (total ads blocked, bandwidth saved), search engine picker, SSL/TLS indicators, speed dials, and theme modes (*Clean Light, Midnight Dark, AMOLED True Black*).

---

### ⚡ Performance & Battery Optimization
* **Active Tab Pausing:** Background WebViews pause timers (`pauseTimers()` / `resumeTimers()`) and JS execution when switching tabs or minimizing the app, eliminating background CPU and battery drain.
* **Media Autoplay Restrictions:** Enforced user gesture requirements for media autoplay to halt background video/audio playback.
* **Smart Network Caching:** Enabled DOM storage and default cache strategies alongside ad/tracker blocking to maximize loading speed and minimize cellular data usage.
* **Persistent Tab Storage:** Automatic local serialization of open tabs (IDs, URLs, titles, private states, desktop states) triggered via `onPause()` and `onStop()`, restoring tabs across app restarts.
