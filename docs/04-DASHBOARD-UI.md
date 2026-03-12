# Chapter 04: Dashboard UI & WOL Controls

**Goal:** Build a functional Wake-on-LAN dashboard  
**Time:** 4-6 hours  
**You'll Ship:** A working UI to wake devices

---

## What You're Building

A dashboard with:
- Header with app title
- Device cards (name, MAC, status indicator)
- Wake button for each device
- Toast notifications for success/error
- Responsive layout (mobile & desktop)

**No database yet.** Hardcode 2-3 devices for now.

---

## Step 1: Plan Your Component Structure

### What to Search
- "react component composition"
- "atomic design react"
- "react component hierarchy"

### Component Breakdown
```
App
├── Header
├── Dashboard
│   ├── DeviceCard (repeated)
│   │   ├── DeviceInfo
│   │   └── WakeButton
│   └── AddDeviceButton (future)
└── Toast/Notification
```

### What to Figure Out
1. Which components should have state?
2. Which should just receive props?
3. Where should API calls happen?

### Principle: "Lift State Up"
**Search:** "react lifting state up"  
**Read:** When to keep state in parent vs child

---

## Step 2: Create Device Type

### What to Search
- "typescript interface vs type"
- "typescript object types"

### What to Figure Out
Define a `Device` type in TypeScript:

**What fields does a device need?**
- `id`: string (unique identifier)
- `name`: string (display name)
- `mac`: string (MAC address)
- `status`: "online" | "offline" | "unknown"
- `lastSeen`?: Date (optional, for monitoring)

### Where to Put Types
**Search:** "react typescript types folder structure"

**Options:**
1. `src/types/device.ts`
2. `src/models/device.ts`
3. Inline in component file (if only used once)

### Documentation Pointers
- **TypeScript Handbook:** https://www.typescriptlang.org/docs/handbook/2/everyday-types.html
- **Read:** "Object Types"

---

## Step 3: Mock Device Data

### What to Figure Out
Create an array of devices in `App.tsx` (or separate file):

```tsx
// Example structure - don't copy, implement yourself
const devices = [
  { id: '1', name: 'Desktop PC', mac: 'AA:BB:CC:DD:EE:FF', status: 'offline' },
  // Add 2-3 more
]
```

### What to Search
- "react mock data best practices"
- "typescript array of objects"

### Challenge
- Add your actual device MAC addresses
- Give them meaningful names
- Later you'll store this in backend/database

---

## Step 4: Build Device Card Component

### What to Search
- "react card component tailwind"
- "tailwind card hover effects"
- "lucide react icons"

### Component Requirements
Each card should display:
- Device icon (desktop, laptop, server, etc.)
- Device name
- MAC address (maybe abbreviated or hidden)
- Status indicator (colored dot)
- Wake button

### What to Figure Out
1. **Layout:**
   - How to structure with Tailwind grid/flexbox?
   - **Search:** "tailwind card layout examples"

2. **Icons with Lucide:**
   Choose icons for device types:
   - **Desktop PC:** `Monitor` icon
   - **Laptop:** `Laptop` icon
   - **Server:** `Server` icon
   - **Network Device:** `Router` or `Wifi` icon
   - **Gaming PC:** `Gamepad2` icon
   - **Media Center:** `Tv` icon
   
   **Wake Button Icon:** `Power` or `ZapOff` → `Zap` (on hover)
   
   **Search:** "lucide react dynamic icon"
   **Question:** How to conditionally render icons based on device type?

3. **Button States:**
   - Default, Hover, Loading, Disabled
   - **Search:** "react button loading state"
   - **Loading Icon:** Use `Loader2` with animation
   - **Search:** "lucide loader2 spin animation tailwind"

### Documentation Pointers
- **Lucide Icons:** https://lucide.dev/guide/packages/lucide-react
- **Icon Browser:** https://lucide.dev/icons/ (search for device types)
- **Tailwind Components:** https://tailwindui.com/components (free examples)

### Lucide Icon Patterns for This Project

**Status Indicators:**
```tsx
// Your research: How to implement?
// Online: <Wifi className="text-green-500" />
// Offline: <WifiOff className="text-gray-400" />
```

**Device Type Icons:**
```tsx
// Map device type to icon
// type DeviceType = 'desktop' | 'laptop' | 'server'
// How to render correct icon for each type?
```

**Wake Button with Icon:**
```tsx
// Default state: <Power className="w-5 h-5" />
// Loading state: <Loader2 className="w-5 h-5 animate-spin" />
// Success state: <Check className="w-5 h-5" />
```

**Search:** "react conditional icon rendering"

### Visual Inspiration
**Search images:**
- "device dashboard ui"
- "network device management interface"
- "wake on lan dashboard"

---

## Step 5: Implement Wake Functionality

### What to Search
- "react fetch post request"
- "react async await"
- "react error handling"

### What to Figure Out

**In WakeButton component:**
1. How to handle click event?
2. How to make POST request to `/api/wol`?
3. How to show loading state during request?
4. How to handle success/error?

### State Management
```tsx
// What hooks do you need?
// Search: "react useState hook"
// Search: "react useEffect hook"

const [loading, setLoading] = useState(false)
const [error, setError] = useState<string | null>(null)
```

### Fetch Implementation
**Search:** "react fetch api post json"

**Questions to answer:**
- How to set Content-Type header?
- How to parse response?
- How to handle network errors?
- How to handle non-200 responses?

### Documentation Pointers
- **MDN Fetch:** https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
- **React Hooks:** https://react.dev/reference/react/useState

---

## Step 6: Add Toast Notifications

### What to Search
- "react toast notification library"
- "shadcn toast component" (if using Shadcn)
- "react hot toast"

### Options

**If using Shadcn:**
```bash
npx shadcn-ui@latest add toast
```
**Read:** https://ui.shadcn.com/docs/components/toast

**If rolling your own:**
**Search:** "react notification component tutorial"

**If using library:**
- `react-hot-toast`: https://react-hot-toast.com/
- `react-toastify`: https://fkhadra.github.io/react-toastify/

### What to Figure Out
1. How to trigger toast on wake success?
2. How to show error toast on failure?
3. How to customize toast appearance?
4. Where to place the toast provider?

### Toast Examples
- Success: "✓ Wake packet sent to Desktop PC"
- Error: "✗ Failed to wake device: Network error"
- Loading: "Sending wake packet..."

---

## Step 7: Responsive Layout

### What to Search
- "tailwind grid responsive"
- "tailwind breakpoints"
- "mobile first design"

### What to Figure Out
Layout device cards in a grid:
- **Mobile:** 1 column
- **Tablet:** 2 columns
- **Desktop:** 3 columns

### Tailwind Responsive Classes
**Search:** "tailwind responsive design"

**Example pattern:**
- `grid-cols-1` (default mobile)
- `md:grid-cols-2` (tablet)
- `lg:grid-cols-3` (desktop)

### Testing Responsiveness
1. Open browser dev tools
2. Toggle device toolbar (Ctrl+Shift+M)
3. Test different screen sizes
4. Check that layout adapts

---

## Step 8: Loading & Error States

### What to Search
- "react loading spinner"
- "react error boundary"
- "skeleton loading react"

### States to Handle

**1. Initial Loading:**
- While fetching device list (future)
- Show skeleton cards or spinner

**2. Action Loading:**
- While sending wake packet
- Disable button, show spinner inside

**3. Error State:**
- Network error
- Invalid response
- Show error message in toast or inline

**4. Empty State:**
- No devices configured
- Show helpful message

### What to Figure Out
1. How to create loading spinner component?
2. How to disable button during loading?
3. How to prevent multiple simultaneous requests?

### Documentation Pointers
- **React Patterns:** https://react.dev/learn/conditional-rendering

---

## Step 9: Polish & UX

### Micro-interactions

**What to add:**
- Button hover effects
- Card hover shadow
- Smooth transitions
- Click feedback
- Icon animations

**Search:**
- "tailwind transition effects"
- "tailwind hover scale"
- "css active state"
- "lucide icon animation hover"

**Icon Hover Effects:**
```tsx
// Your research: How to implement?
// Power icon rotates on hover?
// Status icon pulses when online?
// Button icon changes on hover?
```

**Search:** "tailwind transform rotate hover"

### Keyboard Accessibility

**Search:**
- "react keyboard accessibility"
- "focus visible tailwind"

**What to figure out:**
- Can you tab through buttons?
- Does Enter key work on focused button?
- Is focus indicator visible?

### Color & Visual Hierarchy

**Search:**
- "tailwind color palette"
- "ui color system best practices"

**Status indicators:**
- Online: Green (`bg-green-500`)
- Offline: Gray (`bg-gray-400`)
- Unknown: Yellow (`bg-yellow-500`)

---

## Testing Checklist

- [ ] Device cards render correctly
- [ ] Wake button sends POST to `/api/wol`
- [ ] Loading spinner shows during request
- [ ] Success toast appears on 200 response
- [ ] Error toast appears on failure
- [ ] Button disabled during loading
- [ ] Layout responsive on mobile
- [ ] Can wake multiple devices sequentially
- [ ] No console errors
- [ ] Keyboard navigation works

---

## Common Issues & What to Google

### Toast not appearing
**Search:** "shadcn toast not showing"  
**Check:** Did you add Toaster component to root?

### Fetch fails with CORS error
**Search:** "vite proxy not working"  
**Check:** Proxy configuration in `vite.config.ts`

### Button stays in loading state
**Search:** "react state not updating"  
**Check:** Are you calling `setLoading(false)` in finally block?

### Layout broken on mobile
**Search:** "tailwind mobile breakpoint"  
**Check:** Using mobile-first approach? Default classes are for mobile.

### TypeScript errors on props
**Search:** "react typescript component props"  
**Fix:** Define interface for component props

---

## Code Organization

### Suggested Structure
```
src/
├── components/
│   ├── Header.tsx
│   ├── Dashboard.tsx
│   ├── DeviceCard.tsx
│   └── WakeButton.tsx
├── types/
│   └── device.ts
├── lib/
│   └── api.ts          # Fetch functions
├── App.tsx
└── main.tsx
```

### Separation Pattern
- **Components:** UI rendering only
- **lib/api.ts:** All fetch calls
- **types/:** TypeScript interfaces

**Search:** "react project structure best practices"

---

## Bonus Features (If Time)

### 1. Device Grouping
**Search:** "react group by array"  
Group by room/type (Bedroom, Office, Servers)

### 2. Last Wake Time
**Search:** "javascript local storage"  
Store last wake timestamp in localStorage

### 3. Quick Wake All
Button to wake all devices at once  
**Search:** "javascript promise all"

### 4. Custom Device Icons
Let user choose icon per device  
**Search:** "lucide icons list react"

---

## Understanding Check

Before moving to Chapter 05:

1. What's the difference between props and state?
2. Why should API calls happen in handlers, not render?
3. How do Tailwind responsive classes work?
4. What's the purpose of TypeScript interfaces?
5. Why use `finally` block in try-catch?
6. What makes a button accessible?

---

## Checkpoint: What You Have Now

1. ✅ Functional dashboard with device cards
2. ✅ Working wake buttons that call backend API
3. ✅ Loading and error states
4. ✅ Toast notifications
5. ✅ Responsive layout
6. ✅ Polished UI with Tailwind

---

## Next: Chapter 05

You'll add:
- Contact form component
- Discord webhook integration
- Form validation
- Email/message sending

**Milestone:** This is your MVP. After Chapter 05, you have a shippable product.
