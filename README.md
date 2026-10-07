# Shield-Browser
# ONLY AVAILABLE ON ANDROID DEVICES 
# FIXED ALOT OF BUGS AND ERRORS IN THE BROWSER, FIXING APP FEATURES AND PERFORMANCE AND IF IT GOES GOOD I WILL RELEASE IT BUT NOT COMING OUT YET AND READ THIS TO SEE WHAT IS IMPLEMENTED INTO THIS BROWSER 
## UPDATED FEATURES AND SETTINGS FIXED/IMPROVED 


<!-- Patreon Badge -->
[![Patreon](https://img.shields.io/badge/Patreon-Support_EmuCoreV-FF424D?style=flat-square&logo=patreon&logoColor=white)](YOUR_PATREON_LINK)

<!-- Discord Badge -->
[!LINK](https://shield-browserapp--shieldbrowser.replit.app/#downloads)](YOUR_DISCORD_INVITE_LINK)

## Availability Note: Exclusively optimized for Android devices. This release documentation highlights current architectural changes, system optimizations, and implemented security frameworks during the active stabilization phase.

## 📁 Downloads Management & System Storage
* **Public Storage Routing:** Updated `startDownload` in `BrowserViewModel` to route downloaded files (both direct streaming network downloads and data URIs) into the system downloads directory (`Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS)`) with graceful fallbacks.
* **System Downloads Folder Navigation:** Implemented `openDownloadsFolder(context)` in `BrowserViewModel` to open the system file manager directly to the Downloads folder via `FileProvider` and `DownloadManager.ACTION_VIEW_DOWNLOADS`.
* **Enhanced Downloads UI:** 
  * Added a dedicated **Open Downloads Folder** action in the `DownloadsDialog` header.
  * Added a **Show in Folder** button on each download item card alongside Quick Open, Share, and Delete actions.

### CURRENTLY WORKING ON READ BELOW
Fix sometimes random stuff on sites like images mainly will not show cause of a blur can you fix this and add more settings to increase battery life while on the browser and fix the safe software mode when this is enabled it gets very choppy and unable load anything and gets all glitchy and it would be a black screen and fix YT music it doesn't work fix all of it and reduce the heavy CPU load caused by running ads and tracking scripts. Limit tabs: Close unused tabs regularly, as keeping dozens of pages active forces the browser to consume continuous memory and processor power. Integrate a built-in content blocker: Heavy JavaScript ad networks, cryptocurrency miners, and tracking pixels consume massive amounts of CPU cycles. Implement a native ad-blocker or content-filtering API (like a declarative net request filter) to block these scripts before they execute. 
Disable or sandbox auto-playing media: Automatically pausing or blocking <video> and <audio> tags from playing until the user taps them stops constant hardware video decoding, which spikes thermal output.
2. Optimize JavaScript and Rendering Loop
Throttle background tabs: When a tab is hidden or inactive, use the Page Visibility API to aggressively throttle JavaScript timers (setInterval / setTimeout), freeze requestAnimationFrame, and suspend heavy DOM manipulations.
Limit concurrent workers and WebSockets: Restrict how many background Web Workers or persistent WebSocket connections a single site can spawn without user interaction.


3. Disable Preloading and Speculative Fetching
Turn off aggressive pre-rendering: Many browsers pre-load linked pages or DNS records in the background. Disable or limit speculative parsing, pre-fetching, and instant-page loading on mobile viewports unless explicitly requested.

Reduce telemetry and usage reporting: Stop sending real-time crash logs, usage statistics, and behavioral data packets while the user is actively browsing.

4. Memory and Cache Management
Aggressive garbage collection: Mobile devices have tight RAM limits. If a browser lets old DOM elements and script closures pile up, the operating system triggers heavy garbage collection, causing CPU spikes and heat.
Auto-discard inactive tabs: Automatically suspend or unload tabs that have been inactive in the background for more than a few minutes.

Home Screen Shortcuts: You can tap your browser's menu (three dots) and select "Add to Home screen" to turn any frequently visited website into a quick-launch app icon.

• Dynamic Network Request Throttling:
•  Detect when the phone is under heavy CPU load or high thermal tracking, and automatically queue or delay non-essential background asset downloads (like extra images or scripts).Hardware-Accelerated WebGL Toggles: Add a dedicated "High-Performance / Low-Thermal Mode" toggle in your custom search bar UI. This allows users to completely disable heavy hardware-accelerated animations or WebGL canvases when they only need raw text or simple layouts, minimizing battery and heat.
Picture-in-Picture (PiP) Video Controller:
 Add a native video injection overlay hook that forces online video files into a floating system window when a user leaves the app, letting them watch tutorial videos seamlessly while running other tools.


• Custom User-Agent Profile Switcher:
•  Implement a seamless per-tab engine toggle that lets users spoof their exact user-agent string to mimic desktop layouts or older rendering environments, bypass aggressive web walls, or optimize mobile layout variations.



The initial video stream requests and routes them through clean region proxies where Twitch ads aren't broadcasted.

Android users primarily want a web browser that offers strong privacy, effective ad blocking, seamless cross-device synchronization, and desktop-grade flexibility.


END OF WHATS BEING ADDED CURRENTLY WILL BE ADDED TOMORROW 




### 1. Video Sites Loading & Playback
* **Preserved Real Video Streams:** Fine-tuned `googlevideo.com/videoplayback` filtering in `AdBlockEngine` to specifically target ad format flags (`adformat=`, `ad_type=`) while never intercepting or blocking clean video stream segments, completely resolving playback stalls and "An error occurred" warnings.
* **Hardware-Accelerated Video Pipeline:** Ensured that HTML5 video playback surfaces on video platforms (YouTube, Twitch, Vimeo, TikTok, Dailymotion) always run with hardware composition enabled (`LAYER_TYPE_NONE`), eliminating black video viewports and hardware decoder stalls.
* **In-Browser Video Scheme Handling:** Intercepted and routed `intent:`, `vnd.youtube:`, `youtube:`, `vimeo:`, `twitch:`, `dailymotion:`, and `tiktok:` scheme requests directly into the `WebView` so selected videos play inside the browser with ad-blocking and background playback.
* **Domain & TLD Resolution:** Updated the Omnibox and Home Search domain parser to recognize short URLs (e.g., `youtu.be`) and custom top-level domains as direct web addresses.
* **Quick Links:** Fixed the Vimeo quick launch shortcut to load `https://vimeo.com` directly.

### 2. Unique Settings & Enhanced Android Experience
* **Native Web Dark Mode:** Enabled Android Q+ `forceDark` synchronized across Midnight Dark and AMOLED Pure Black themes to prevent blinding white backgrounds on external websites.
* **Mixed Content Compatibility:** Configured `MIXED_CONTENT_COMPATIBILITY_MODE` to ensure external video CDNs and media manifests load seamlessly.
* **DRM & Protected Media:** Refined `MediaKeySystem` access to preserve Widevine playback for supported media sites.
* **Data Hygiene Feedback:** Added instant confirmation feedback upon clearing browsing data, history, and cookies.
* **UI Touch Feedback:** Integrated native Material 3 ripples across quick link items and interactive components.

### 3. Permissions & Hardware Access
* **Media & Hardware Permissions:** Added audio recording and camera permissions to the browser to enable web-based video calls, voice input, and media 
---

* **Isolated Tab Execution & Composition:** Wrapped the active web container in `key(activeTab.id)`. Only the currently selected tab is rendered in memory and on screen. Background / inactive tabs are unmounted from the view hierarchy rather than running or rendering continuously in the background.
* **On-Demand Reload When Revisited:** When switching back to an inactive tab, its dedicated WebView instance is cleanly instantiated and loaded on demand, ensuring zero CPU and memory drain from hidden tabs.
* **Cookie & Storage Isolation:**
  * Enforced third-party cookie blocking (`setAcceptThirdPartyCookies(..., false)`) to prevent cross-site tracker sharing and unlinked cookie leaks between different websites.
  * Implemented session partition controls for private / incognito browsing with zero cookie retention and immediate `WebStorage.deleteAllData()` wipe.
* **Lifecycle Resource Disposal:** Added `onRelease` and `onDispose` hooks to halt active network requests (`stopLoading()`), pause JavaScript timers (`onPause()`), and discard transient state when tabs are swapped.


## 🔒 Hardware Biometric App Lock

A privacy-first security module that secures the browser workspace using native Android biometric authentication whenever the application returns from the background.




## 🐛 Bug Fixes & Rendering Pipeline Optimizations

### 🖥️ Mesa DRM & Render Node Error Fixes (`Failed to open rendernode`)
* **Eliminated Offscreen Pre-Rasterization:** Removed `setOffscreenPreRaster(...)` from `BrowserWebViewContainer` and `BrowserViewModel`. Disabling forced offscreen raster thread pools prevents Chromium from probing non-existent Linux Mesa DRI render nodes (`/dev/dri/renderD*`) in virtualized, headless, or cloud-streaming container environments.
* **Layer Type Defaulting:** Removed explicit `setLayerType(View.LAYER_TYPE_HARDWARE, null)` calls and set the WebView layer type to default (`View.LAYER_TYPE_NONE`). This allows the Chromium compositor to manage its Skia/GPU pipeline natively through the system surface without forcing direct DRM node allocations.
* **Deprecated API Cleanup:** Removed deprecated `setRenderPriority` invocations to adhere to modern Android WebView API standards.

---

### ⏱️ Chromium Page Load Metrics & Script Injection Fixes
* **Resolved Metrics Synchronization Errors:** Fixed `E/chromium: Invalid first_paint (unset) for first_image_paint` warnings caused by executing DOM scripts during early, unpainted page lifecycle stages.
* **Synchronized Scriptlet Execution:** Moved anti-### Key Features & Security Architecture

* **Hardware Biometric Lock on Resume:** Integrated `BiometricAuthManager` using Android's hardware `BiometricPrompt` and device credentials. When enabled, the app secures private tabs upon returning from the background and presents an authentication shield.
* **Dynamic FLAG_SECURE Protection:** Implemented real-time synchronization between user preferences and the window flag in `MainActivity.kt` to prevent unauthorized screenshots and screen recorders, while blurring/masking the app preview in Android's recent tasks switcher.
* **On-Device Machine Learning Tracker Blocking:** Connected `SmartTrackerClassifier` directly into `WebViewClient.shouldInterceptRequest` to inspect behavioral telemetry endpoints, fingerprinting queries, and session replay recorders on the fly.
* **AI Phishing & Malicious URL Predictor:** Integrated real-time lexical, punycode homograph, entropy, and domain depth analysis in `BrowserWebViewContainer` before page navigation commits.
* **Canvas & WebGL Fingerprint Spoofing:** Injected dynamic pixel and audio noise via `VpnTunnelManager.ANTI_TRACKING_JS` at the safe `onPageCommitVisible` lifecycle point, with preference toggles in Settings.
* **Settings & Protection Controls:** Added dedicated toggles for Smart ML Tracker Blocking, AI Phishing Predictor, Biometric Lock, Screen Guard (`FLAG_SECURE`), and Stealth Icon Disguise in the Settings bottom sheet.
tracking, viewport modification, and ad-defuser scriptlet injections out of `onPageStarted` and mid-parse `onProgressChanged` thresholds (20%/60%). Injections now occur strictly at `onPageCommitVisible` (when the initial paint commits) and `onPageFinished`.
* **Isolated Progress Dispatcher:** Constrained `onProgressChanged` purely to user-facing UI progress bar updates, eliminating background thread stutter during DOM parsing.

---

## ⚡ Performance & Responsiveness Enhancements
* **Hardware Acceleration:** Enabled full GPU hardware acceleration across the Android Manifest and WebView container, eliminating software rasterization lag and ensuring smooth 60fps scrolling.
* **Debounced Block Telemetry:** Shifted ad and tracker block counter disk persistence into debounced background coroutines, preventing UI thread stutter and frame drops during heavy page loading.
* **Timer & Execution Continuity:** Removed global timer pausing during navigation transitions so background web workers and audio/video threads stay responsive.

---

## 🛡️ VPN Stability & Connection Management
* **Seamless Server Switching:** Fixed VPN switching logic to cleanly release previous tunnel interfaces, preventing orphaned connections and ensuring smooth transitions across all zero-log locations.
* **Service Lifecycle Reliability:** Fixed foreground service startup exceptions during active server switches to ensure stable background execution and prevent crash loops on newer Android versions.

---

## 🔋 Battery & Resource Optimization
* **Reduced Wake-Ups:** Dramatically reduced background traffic polling intervals to keep the CPU in low-power states longer.
* **WebView Render Optimization:** Streamlined compositing and offscreen rendering routines to decrease GPU/CPU usage during active browsing.
* **Scriptlet Execution Efficiency:** Eliminated redundant scriptlet injections on page loads, lowering memory churn and speeding up document parse times.


## 🎬 YouTube & Media Playback Fixes
* **Legitimate Redirect Filtering:** Fixed redirect protection logic to allow standard web redirects (such as `youtube.com` to `m.youtube.com` and authentication handoffs) while continuing to block malicious scheme hijacking and runaway redirect loops.
* **Stream Delivery Optimization:** Whitelisted `googlevideo.com` media streaming endpoints from tracker filters while maintaining targeted blocking of in-stream ad segments (`&adformat=`, `&ad_type=`, `&oad=`).
* **Media Playback Permissions:** Configured media playback settings to permit immediate HTML5 playback without requiring redundant manual tap gestures.
* **Fullscreen View Stability:** Protected video fullscreen transitions by safely detaching views from their parent hierarchies prior to Compose overlay attachment.

---

## 🌐 Browser Compatibility & Standards
* **Modern User Agent Sanitization:** Stripped webview tokens (`; wv`) from User-Agent headers, preventing desktop and mobile sites from serving degraded fallback layouts.
* **Multi-Window & Popup Support:** Added an `onCreateWindow` handler to support OAuth authentication flows, dialog popups, and tabbed navigation.
* **Cookie & Storage Parity:** Enabled necessary third-party cookie handling for normal browsing sessions and configured resilient SSL and resource error handling to prevent blank screens.

---

## 🛠️ Feature Updates & System Controls
* **Real-Time Download Manager & Controls:** Real-time progress updates with full pause, resume, and cancel capabilities, floating progress notifications, dynamic controls, and support for direct URL/media capture.
* **Async Clipboard Manager:** One-touch clipboard history manager with quick search, item categorization, and direct in-page text injection into active web forms and dynamic input fields.
* **Password Vault & 1-Touch Auto-Login:** Fixed vault card signatures to display decrypted secrets with copy and visibility toggles, plus 1-touch auto-login supporting modern SPA frameworks and reactive input bindings.
* **Distraction-Free Reader Mode:** Article extraction (headlines, bylines, body text) with customizable font scaling and theme options (Light, Sepia, Dark, and AMOLED).




### 📥 Downloads Manager Overhaul
* **Interface Cleanup:** Renamed "Offline Downloads" to **Downloads** across the application.
* **Tabbed Downloads Browser:** Implemented structured tabs to easily filter between **All**, **Normal**, and **Offline** downloads.
* **Direct URL Downloader:** Added a direct URL download input feature complete with auto-detected file metadata and real-time progress tracking.

---

### 🛡️ Threat, Malware & Redirect Protection
* **Malware Protection Engine:** Implemented real-time URL threat inspection in `MalwareProtection` covering homograph attacks, suspicious IP addresses, known malware domains, and phishing indicators.
* **Warning Interstitials:** Integrated safety warnings into navigation flows, allowing users to return to safety or proceed at their own discretion.
* **Rogue Redirect & Bounce Loop Prevention:** Blocks rapid background redirects and non-interactive scheme hijacking attempts, notifying users via a dismissible notification banner whenever an unwanted redirect is stopped.

---

### 🔑 Chrome-Style Web Permissions & Native Uploads
* **Permission Prompt Dialogs:** Integrated `onPermissionRequest` and `onGeolocationPermissionsShowPrompt` into `WebChromeClient` with native **Allow/Block** prompts for Camera, Microphone, and Location access.
* **Native File Chooser:** Added full support for standard HTML file uploads (`<input type="file">`) via native Android file picker handling.

---

### 🔒 Zero-Log VPN & Anti-Tracking
* **Shield VPN Service:** Built `ShieldVpnService` leveraging native `VpnService.prepare` system consent flows, live traffic metric tracking, and multi-region node selection.
* **Advanced Scriptlet Injection:** Mitigates WebRTC local IP leaks, blocks Battery API fingerprinting, and automatically signals **Global Privacy Control (GPC)** preferences.

---

### 📺 Enhanced Fullscreen Video Overlays
* **Immersive System Bars:** System bars automatically hide when entering fullscreen video playback.
* **Custom Top Overlay:** Displays an overlay showing an **Exit Fullscreen** button, live system clock, and real-time battery level percentage.
* **Clean Exit Handling:** Automatically restores top and bottom system bars upon exiting fullscreen mode.

---

### ⚡ Rendering & Battery Optimization
* **Mesa Render Node Fix:** Eliminated emulator Mesa driver crashes (`LAYER_TYPE_NONE`) using safe software layer fallbacks.
* **Efficiency Boosts:** Fine-tuned HTTP caching, enabled DOM storage, and restricted unprompted media autoplay to maximize device battery longevity and UI responsiveness.

![image_alt](https://github.com/ytcarphantom/Shield-Browser/blob/49917e069017c34d9d3dad305282da9f5f22551e/Screenshot_20260907_145531_Shield%20Browser.jpg)


![image alt](https://github.com/ytcarphantom/Shield-Browser/blob/760466d2a24ebe2d5dde3abd40ddd014eb543a92/Screenshot_20260907_145537_Shield%20Browser.jpg)

![image alt](https://github.com/ytcarphantom/Shield-Browser/blob/71470f5f34e8ae5a78da78c7265b428b275f5295/AISelect_2026q0907_144850_Chrome.jpg)


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
