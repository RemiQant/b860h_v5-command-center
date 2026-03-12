# Chapter 07: Progressive Web App & Mobile Optimization

**Goal:** Make your dashboard installable and mobile-friendly  
**Time:** 3-5 hours  
**You'll Ship:** PWA that works offline + Android home screen widgets

---

## What You're Building

Transform your web app into a native-like experience:
1. **PWA (Progressive Web App)** - Install on phone/tablet like a native app
2. **Tablet-First UI** - Optimize dashboard for touch interfaces
3. **KWGT Widgets** - Android home screen widgets for quick WOL

**Why PWA?**
- No app store needed
- Works offline (service workers)
- Push notifications (future)
- Feels like native app
- One codebase for web + mobile

**Why KWGT?**
- Wake devices without opening app
- Custom widget designs
- Tap widget → instant WOL
- Show device status on home screen

---

## Part 1: Progressive Web App

### Step 1: Create Web App Manifest

### What to Search
- "pwa manifest json"
- "web app manifest generator"
- "pwa install criteria"

### Documentation Pointers
- **MDN Web Manifest:** https://developer.mozilla.org/en-US/docs/Web/Manifest
- **web.dev PWA:** https://web.dev/progressive-web-apps/

### What to Figure Out

**1. Create `manifest.json`**
**Location:** `web/public/manifest.json`

**What to include:**
- `name` - Full app name
- `short_name` - Home screen name (12 chars max)
- `description` - What the app does
- `start_url` - Entry point (usually `/`)
- `display` - "standalone" (looks like native app)
- `theme_color` - Browser toolbar color
- `background_color` - Splash screen background
- `icons` - Multiple sizes (192x192, 512x512)

**Search:** "pwa manifest example"

**2. Link Manifest in HTML**
Add to `index.html`:
```html
<link rel="manifest" href="/manifest.json">
```

**3. Create App Icons**

**What sizes needed:**
- 192x192 (minimum for installability)
- 512x512 (splash screen)
- 180x180 (iOS Safari)
- Maskable icons (Android adaptive icons)

**Search:** 
- "pwa icon generator"
- "maskable icon editor"
- "favicon generator pwa"

**Tools to research:**
- https://realfavicongenerator.net/
- https://maskable.app/

### Testing Manifest
**Search:** "chrome devtools pwa manifest"

1. Open Chrome DevTools
2. Application tab → Manifest
3. Check for errors
4. Look for "Add to home screen" availability

---

### Step 2: Service Worker (Offline Support)

### What to Search
- "vite pwa plugin"
- "service worker tutorial"
- "vite-plugin-pwa"

### Documentation Pointers
- **Vite PWA Plugin:** https://vite-pwa-org.netlify.app/
- **Service Workers:** https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API

### What to Figure Out

**1. Install Vite PWA Plugin**
```bash
npm install vite-plugin-pwa -D
```

**2. Configure in `vite.config.ts`**
**Search:** "vite-plugin-pwa configuration"

**What to configure:**
- Register type: "autoUpdate" or "prompt"
- Cache strategies (NetworkFirst for API, CacheFirst for assets)
- Offline fallback page
- Manifest integration

**3. Caching Strategies**

**Search:** "service worker caching strategies"

**Options:**
- **Cache First:** Static assets (JS, CSS, images)
- **Network First:** API calls (WOL, contact form)
- **Stale While Revalidate:** Balance speed + freshness

**4. Offline Functionality**

**What should work offline:**
- View cached device list
- See UI (static assets)
- Show "offline" indicator

**What needs network:**
- Sending WOL packets
- Contact form submission
- Fetching fresh device status

**Search:** "pwa offline detection"

### Testing Service Worker
1. Build production version
2. Serve with `npx serve dist`
3. Open DevTools → Application → Service Workers
4. Check "Offline" checkbox
5. Reload page → should work offline

---

### Step 3: Install Prompt

### What to Search
- "pwa install prompt"
- "beforeinstallprompt event"
- "add to home screen banner"

### What to Figure Out

**1. Detect Install Capability**
```tsx
// Your research: How to implement?
// Listen for 'beforeinstallprompt' event
// Store event for later use
```

**2. Custom Install Button**
Create a button that triggers install prompt:
- Show only if app is installable
- Hide after installation
- Use nice icon (e.g., Lucide `Download` icon)

**Search:** "react pwa install button"

**3. Track Installation**
**Search:** "detect pwa installed"

**Questions to answer:**
- How to know if user already installed?
- How to hide install prompt after installation?
- Where to show the install button? (Header? Dashboard?)

### iOS Considerations
**Search:** "pwa ios safari limitations"

**iOS issues:**
- No automatic install prompt
- Must add to home screen manually
- Limited service worker support

**What to add:**
- Meta tags for iOS
- Instructions for iOS users
- iOS-specific icons

---

### Step 4: App-Like Features

### What to Search
- "pwa standalone display"
- "disable pull to refresh pwa"
- "pwa safe area insets"

### Features to Implement

**1. Splash Screen**
**Search:** "pwa splash screen"
- Shown while app loads
- Uses `background_color` + icon from manifest

**2. Status Bar Styling**
**Search:** "pwa status bar color"

Add to `index.html`:
```html
<meta name="theme-color" content="#your-color">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

**3. Disable Browser UI**
**Search:** "pwa disable pull to refresh"

Prevent accidental browser gestures:
- Pull-to-refresh (annoying on mobile)
- Pinch-to-zoom (if not needed)
- Long-press context menu

**4. Safe Areas (Notch Support)**
**Search:** "pwa safe area insets tailwind"

Handle iPhone notches, Android navigation bars:
```css
/* Your research: How to implement? */
padding: env(safe-area-inset-top);
```

---

## Part 2: Mobile UI Optimization

### Step 1: Tablet-First Design

### What to Search
- "tablet first design"
- "tailwind tablet breakpoints"
- "touch target sizes"

### Design Principles

**1. Touch Targets**
**Minimum sizes:**
- Buttons: 44×44px (Apple), 48×48px (Google)
- Cards: Easy to tap, not too close together
- Spacing: 8px minimum between touch targets

**Search:** "touch target size guidelines"

**2. Tablet Layout**
**Screen sizes to target:**
- Small tablet: 768px (iPad Mini)
- Large tablet: 1024px (iPad Pro)

**Layout ideas:**
- 2 columns for device cards
- Side navigation instead of hamburger
- Larger typography
- More whitespace

**3. Test on Real Devices**
**Search:** "chrome remote debugging android"

Connect your phone/tablet:
1. Enable USB debugging
2. Chrome → `chrome://inspect`
3. Test on actual device

---

### Step 2: Touch Interactions

### What to Search
- "mobile touch events react"
- "swipe gestures react"
- "long press events"

### Interactions to Add

**1. Pull-to-Refresh Devices**
**Search:** "react pull to refresh"

Swipe down on device list → refresh device status

**Libraries to research:**
- `react-pull-to-refresh`
- Roll your own with touch events

**2. Swipe Actions**
**Search:** "swipe to delete react"

Swipe device card left → reveal delete/edit

**3. Long Press**
**Search:** "react long press hook"

Long press device card → show options menu

**4. Haptic Feedback**
**Search:** "web vibration api"

Quick vibration on button press:
```js
navigator.vibrate(10); // 10ms haptic
```

---

### Step 3: Mobile Navigation

### What to Search
- "mobile navigation patterns"
- "bottom tab bar react"
- "tailwind mobile menu"

### Navigation Options

**Option A: Bottom Tab Bar** (Recommended)
- Always visible
- Easy thumb reach
- Standard on mobile apps

**Icons needed (Lucide):**
- `Home` - Dashboard
- `Power` - Quick WOL
- `MessageSquare` - Contact
- `Settings` - Future settings

**Option B: Hamburger Menu**
- Saves space
- Less discoverable
- Standard on web apps

**Search:** "mobile bottom navigation react"

### Responsive Nav Pattern
```tsx
// Your research: How to implement?
// Desktop: Top horizontal nav
// Tablet: Top nav with icons + text
// Mobile: Bottom tab bar with icons only
```

---

### Step 4: Performance for Mobile

### What to Search
- "mobile web performance"
- "lighthouse mobile score"
- "react lazy loading"

### Optimizations

**1. Lazy Load Components**
**Search:** "react lazy suspense"

Load contact form only when needed:
```tsx
const ContactForm = lazy(() => import('./ContactForm'))
```

**2. Image Optimization**
**Search:** "vite image optimization"

- Use WebP format
- Responsive images
- Lazy load images

**3. Reduce Bundle Size**
**Search:** "vite bundle analyzer"

Check what's making your bundle big:
```bash
npm install rollup-plugin-visualizer -D
```

**4. Lighthouse Audit**
**Search:** "lighthouse mobile audit"

Run in Chrome DevTools:
1. DevTools → Lighthouse
2. Mobile device
3. Check PWA, Performance, Accessibility
4. Aim for 90+ scores

---

## Part 3: KWGT Android Widgets

### What is KWGT?

**Kustom Widget Maker** - Android app for creating custom home screen widgets

**Your widgets will:**
- Show device list on home screen
- Tap device → send WOL packet
- Show online/offline status
- Open full dashboard

### Step 1: Design Widget API

### What to Search
- "kwgt api calls"
- "kwgt json data"
- "tasker kwgt integration"

### Create Widget API Endpoint

**Backend:** `GET /api/widgets/devices`

**Response:**
```json
{
  "devices": [
    {
      "id": "1",
      "name": "Desktop PC",
      "status": "offline",
      "icon": "monitor"
    }
  ]
}
```

**Why separate endpoint:**
- Simplified data for widgets
- No auth needed (local network only)
- Fast response

**Search:** "golang json api endpoint"

---

### Step 2: Create Widget in KWGT

### What to Search
- "kwgt tutorial"
- "kwgt api data"
- "kwgt touch action"

### Documentation Pointers
- **KWGT Guide:** https://help.kustom.rocks/
- **Community:** r/kustom on Reddit

### What to Figure Out

**1. Install KWGT**
- Download from Play Store
- Buy KWGT Pro (needed for custom features)
- Grant permissions

**2. Create New Widget**
**Search:** "kwgt create custom widget"

**Widget layout:**
```
┌──────────────────────┐
│ 🖥️  Desktop PC       │  ← Device name
│     [WAKE]           │  ← Wake button
│     ⚫ Offline        │  ← Status
├──────────────────────┤
│ 💻  Laptop           │
│     [WAKE]           │
│     🟢 Online         │
└──────────────────────┘
```

**3. Fetch Data from API**
**Search:** "kwgt web data"

Configure in KWGT:
- Data source: Web (JSON)
- URL: `http://your-server:8080/api/widgets/devices`
- Refresh: Every 5 minutes

**4. Parse JSON Response**
**Search:** "kwgt json parsing"

Access device data:
```
$wg("your-api", ".devices[0].name")$
$wg("your-api", ".devices[0].status")$
```

**5. Add Touch Actions**
**Search:** "kwgt touch action url"

On tap → trigger WOL:
- Touch action: Open URL
- URL: `http://your-server:8080/api/wol?mac=AA:BB:CC:DD:EE:FF`

**Or use Tasker integration:**
**Search:** "kwgt tasker integration"

---

### Step 3: Widget Design

### What to Search
- "kwgt design tips"
- "kwgt icon packs"
- "kwgt shadows effects"

### Design Elements

**1. Device Icons**
Use Material Icons or custom icons:
- Desktop: 🖥️
- Laptop: 💻
- Server: 🔲

**Search:** "kwgt custom icons"

**2. Status Indicators**
- Online: Green dot 🟢
- Offline: Gray dot ⚫
- Unknown: Yellow dot 🟡

**3. Wake Button**
**Search:** "kwgt button design"

Styled button with:
- Background color
- Rounded corners
- Shadow effect
- Press animation

**4. Background**
- Semi-transparent background
- Blur effect
- Matches your phone theme

**Search:** "kwgt glassmorphism"

---

### Step 4: Advanced Widget Features

### What to Search
- "kwgt variables"
- "kwgt conditional display"
- "kwgt animation"

### Features to Add

**1. Auto-Refresh**
Update device status automatically:
- Set refresh interval (5 minutes)
- Show last update time
- Manual refresh button

**2. Conditional Display**
**Search:** "kwgt if formula"

Show different colors based on status:
```
$if(status=online, #00FF00, #808080)$
```

**3. Multiple Widget Sizes**
Create 3 variants:
- **1×1:** Single device quick wake
- **2×2:** 2-4 devices with status
- **4×2:** Full device list

**4. Animations**
**Search:** "kwgt animation tutorial"

- Fade in on wake button press
- Pulse effect for online status
- Slide in when data refreshes

---

### Step 5: Tasker Integration (Advanced)

### What is Tasker?

**Tasker** - Android automation app that can trigger actions

**Why use with KWGT:**
- More complex logic
- Better error handling
- Local variables
- HTTP requests with headers

### What to Search
- "tasker http request"
- "tasker kwgt integration"
- "tasker scenes"

### Tasker Task for WOL

**Create task:**
1. HTTP Post to `/api/wol`
2. Check response
3. Show notification (success/fail)
4. Update KWGT widget

**Search:** "tasker rest api tutorial"

### Trigger from KWGT
Set touch action to:
- Launch Task → Your WOL Task
- Pass device MAC as variable

**Search:** "kwgt launch tasker task"

---

## Part 4: Testing & Deployment

### PWA Testing Checklist

**Desktop:**
- [ ] Manifest loads correctly
- [ ] Icons display in manifest
- [ ] Service worker registers
- [ ] Works offline
- [ ] Install prompt appears
- [ ] Installs successfully

**Mobile (Android):**
- [ ] Add to home screen works
- [ ] App icon shows on home screen
- [ ] Opens in standalone mode
- [ ] No browser UI visible
- [ ] Status bar styled correctly
- [ ] Works offline
- [ ] Service worker updates

**Tablet:**
- [ ] Layout optimized for tablet size
- [ ] Touch targets large enough
- [ ] Navigation accessible
- [ ] All features work

**iOS (Safari):**
- [ ] Add to home screen works
- [ ] Splash screen shows
- [ ] Icons correct size
- [ ] Runs in standalone mode

---

### KWGT Testing Checklist

- [ ] Widget displays device list
- [ ] Data fetches from API
- [ ] Auto-refresh works
- [ ] Status colors correct
- [ ] Touch actions trigger WOL
- [ ] Error handling (API down)
- [ ] Battery efficient (refresh not too often)
- [ ] Works on lock screen (if desired)

---

## Performance Targets

### PWA Lighthouse Scores
- **Performance:** 90+
- **Accessibility:** 95+
- **Best Practices:** 90+
- **SEO:** 90+
- **PWA:** 100 (all checks pass)

### Mobile Performance
- **First Contentful Paint:** < 1.5s
- **Time to Interactive:** < 3.5s
- **Largest Contentful Paint:** < 2.5s
- **Bundle size:** < 500KB (gzipped)

### Widget Performance
- **API response time:** < 200ms
- **Widget update time:** < 1s
- **Battery impact:** Minimal
- **Data usage:** < 1KB per refresh

---

## Common Issues

### PWA Not Installing
**Search:** "pwa install criteria not met"

**Check:**
- HTTPS enabled (required!)
- Manifest valid JSON
- Service worker registered
- 192px icon exists
- start_url accessible

### Service Worker Not Updating
**Search:** "service worker update strategy"

**Solutions:**
- Clear cache
- skipWaiting() in SW
- Update version in manifest

### KWGT Not Fetching Data
**Search:** "kwgt web data not loading"

**Check:**
- API accessible from phone
- CORS headers set correctly
- JSON format valid
- Network permissions granted

### Widget Draining Battery
**Search:** "kwgt battery optimization"

**Solutions:**
- Increase refresh interval
- Use "when screen on" trigger
- Optimize API response size
- Remove animations

---

## Security Considerations

### PWA Security
**Search:** "pwa security best practices"

- HTTPS required (always)
- Content Security Policy
- Secure service worker
- No sensitive data in cache

### Widget Security
**Search:** "api security local network"

**Considerations:**
- Widget API only on local network
- No authentication in widget (security risk)
- Rate limit widget endpoints
- Consider Tailscale for remote access

**Alternative:** 
Create separate widget API with API key:
```
/api/widgets/devices?key=your-secret-key
```

Store key in KWGT global variable.

---

## Understanding Check

Before calling it done:

1. What makes an app a PWA?
2. Why is HTTPS required for PWA?
3. What's the difference between Cache First and Network First?
4. How does service worker enable offline functionality?
5. What's the minimum icon size for PWA installability?
6. Why use KWGT instead of a native app?
7. How does Tasker enhance KWGT widgets?

---

## Resources

### PWA
- **PWA Checklist:** https://web.dev/pwa-checklist/
- **Vite PWA Plugin:** https://vite-pwa-org.netlify.app/
- **PWA Builder:** https://www.pwabuilder.com/

### KWGT
- **KWGT Help:** https://help.kustom.rocks/
- **r/kustom:** https://reddit.com/r/kustom
- **KWGT Tutorials:** YouTube search "kwgt tutorial"

### Mobile Design
- **Touch Target Sizes:** https://web.dev/accessible-tap-targets/
- **Mobile UX:** https://material.io/design/platform-guidance/android-bars.html

---

## What's Next?

### 📊 Chapter 08: Monitoring & Observability (Recommended)

Now that your app is mobile-ready, add production monitoring!

**[Go to Chapter 08: Monitoring & Observability](08-MONITORING-OBSERVABILITY.md)**

You'll add:
- **Sentry** - Track errors in production
- **Structured logging** - Debug issues faster
- **Metrics** - Monitor performance and usage
- **Alerts** - Get notified when things break

**Time:** 4-6 hours  
**Result:** Production-grade observability

---

### Future Enhancements
1. **Push Notifications** - Alert when device comes online
2. **Widget Themes** - Match system dark/light mode
3. **Shortcuts API** - Long-press app icon → quick actions
4. **Share Target** - Share device MAC to app
5. **Background Sync** - Queue WOL requests when offline

### Advanced KWGT
1. **Multiple pages** - Swipe between device groups
2. **Edit mode** - Long-press to edit device
3. **Folder widgets** - Nested device groups
4. **Status bar integration** - Show in notification shade

---

**Remember:** PWA and KWGT make your dashboard feel native. Test on real devices, optimize for touch, and ship something delightful! 📱✨
