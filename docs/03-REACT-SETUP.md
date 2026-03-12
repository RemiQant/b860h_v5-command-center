# Chapter 03: React Frontend Setup

**Goal:** Get React + TypeScript + Vite running  
**Time:** 2-3 hours  
**You'll Ship:** A working dev server with one button

---

## Decision Point: Framework Choice

You need to decide: **Next.js or React + Vite?**

### Next.js (Recommended if...)
- You want built-in routing
- You might add SSR later
- You like convention over configuration
- **Time investment:** Slightly more setup

### React + Vite + React Router (Recommended if...)
- You want minimal, fast builds
- You understand client-side routing
- You prefer explicit control
- **Time investment:** Faster initial setup

### What to Search
- "nextjs vs vite 2024"
- "when to use nextjs vs create react app"
- "vite react performance"

### Documentation Pointers
- **Next.js:** https://nextjs.org/docs
- **Vite:** https://vitejs.dev/guide/
- **React Router:** https://reactrouter.com/en/main

**For this guide, we'll assume Vite** (faster to ship). Adapt if you choose Next.

---

## Step 1: Initialize React + TypeScript + Vite

### What to Search
- "vite create react typescript"
- "vite react swc template"
- "npm create vite"

### Documentation Pointers
- **Vite Getting Started:** https://vitejs.dev/guide/#scaffolding-your-first-vite-project
- **Look for:** The command to create a new project

### What to Figure Out
1. What command creates a Vite project?
2. How to select React + TypeScript template?
3. What's the difference between TypeScript and TypeScript + SWC?
   - **Hint:** Search "vite swc vs babel"

### Expected Commands
```bash
cd web/  # Or your frontend directory
# [FIGURE OUT THE CREATE COMMAND]
npm install
npm run dev
```

### Expected Result
Browser opens to `http://localhost:5173` with Vite + React welcome screen.

---

## Step 2: Understand the Project Structure

### What to Search
- "vite project structure explained"
- "vite config file"
- "react src folder structure"

### What to Figure Out
Look at the generated files:
```
web/
├── index.html          # Entry point (Vite serves this)
├── vite.config.ts      # Vite configuration
├── tsconfig.json       # TypeScript config
├── package.json        # Dependencies
└── src/
    ├── main.tsx        # React entry point
    ├── App.tsx         # Root component
    └── assets/         # Images, fonts, etc.
```

**Questions to answer:**
1. Why is `index.html` in the root, not `public/`?
2. What does `main.tsx` do?
3. What's the difference between `.tsx` and `.ts`?

### Documentation Pointers
- **Vite Features:** https://vitejs.dev/guide/features.html
- **Read:** "index.html and Project Root"

---

## Step 3: Install TailwindCSS

### What to Search
- "tailwind vite install"
- "tailwindcss react setup"
- "tailwind config typescript"

### Documentation Pointers
- **Tailwind Vite Guide:** https://tailwindcss.com/docs/guides/vite
- **Follow:** Step-by-step installation

### What to Figure Out
1. What npm packages to install?
2. What config files to create?
3. Where to add Tailwind directives?
4. How to test it works?

### Testing Tailwind
Replace something in `App.tsx` with:
```tsx
<div className="bg-blue-500 text-white p-4">
  Tailwind works!
</div>
```

If the background is blue, you're good.

---

## Step 4: Install Shadcn UI (Optional)

**Decision:** Use Shadcn or build custom components?

### Shadcn (Recommended if...)
- You want pre-built, accessible components
- You don't want to reinvent buttons, cards, etc.
- You're okay with a CLI adding files to your project

### Custom Components (Choose if...)
- You want to learn by building everything
- You have specific design requirements
- You want minimal dependencies

### What to Search
- "shadcn ui vite setup"
- "shadcn ui vs component library"
- "tailwindcss component patterns"

### Documentation Pointers
- **Shadcn Installation:** https://ui.shadcn.com/docs/installation/vite
- **Read:** "Installation" → "Vite"

### What to Figure Out (if using Shadcn)
1. How to run `npx shadcn-ui@latest init`?
2. What configuration options to choose?
3. Where do components get installed?
4. How to add a component (e.g., button)?

### Test Installation
```bash
# If using Shadcn
npx shadcn-ui@latest add button

# Now use it in App.tsx
import { Button } from "@/components/ui/button"
```

---

## Step 4.5: Install Lucide Icons

**Lucide** is a beautiful, consistent icon library perfect for React projects.

### What to Search
- "lucide react install"
- "lucide icons list"
- "lucide react usage"

### Documentation Pointers
- **Lucide React:** https://lucide.dev/guide/packages/lucide-react
- **Icon Browser:** https://lucide.dev/icons/
- **Read:** "Installation", "Usage"

### What to Figure Out

**1. Installation**
```bash
npm install lucide-react
```

**2. How to Import Icons**
**Search:** "lucide react import icons"

**Questions:**
- Do you import all icons or individual ones?
- What's the naming convention? (PascalCase)
- How to find the icon you need?

**3. Basic Usage Pattern**
**Search:** "lucide react icon props"

**What to learn:**
- How to set icon size?
- How to change color?
- How to add CSS classes?
- What props are available?

### Common Icons for This Project

Browse https://lucide.dev/icons/ and find:
- **Power** - Wake button
- **Wifi** / **WifiOff** - Network status
- **Monitor** / **Laptop** / **Server** - Device types
- **Mail** - Contact form
- **Send** - Submit button
- **Check** / **X** - Success/error states
- **Loader2** - Loading spinner (search: "lucide loader animation")
- **AlertCircle** - Error messages
- **Info** - Info tooltips

### Testing Icons

Add to `App.tsx` to verify:
```tsx
// Your research: How to import and use?
import { Power, Wifi, Monitor } from 'lucide-react'

// How to render with custom size and color?
```

### Styling Icons with Tailwind
**Search:** "lucide react tailwind css"

**What to figure out:**
- Can you use Tailwind classes directly on icons?
- How to animate on hover?
- How to make icons responsive?

### Icon Size Guidelines
- **Small:** 16px (`size={16}` or `className="w-4 h-4"`)
- **Medium:** 20-24px (default)
- **Large:** 32px for prominent buttons
- **Hero:** 48px+ for empty states

---

## Step 5: Create Your First Component

### What to Search
- "react functional component typescript"
- "react props typescript"
- "react state management"

### Goal
Create a `WakeButton` component that:
- Shows a button
- Accepts a `macAddress` prop
- Logs to console when clicked (we'll add API call later)

### What to Figure Out
1. How to create a new component file?
2. How to define props interface in TypeScript?
3. How to handle click events?
4. How to use `useState` hook?

### Documentation Pointers
- **React TypeScript:** https://react.dev/learn/typescript
- **React Hooks:** https://react.dev/reference/react/hooks

### Expected File Structure
```
src/
├── components/
│   └── WakeButton.tsx    # New component
├── App.tsx
└── main.tsx
```

### Component Requirements
```tsx
// Your research: How to implement this?
<WakeButton macAddress="AA:BB:CC:DD:EE:FF" />

// On click, should log: "Waking AA:BB:CC:DD:EE:FF"
```

---

## Step 6: Connect Frontend to Backend

### What to Search
- "react fetch api example"
- "vite proxy api requests"
- "cors react golang"

### Problem to Solve
- Frontend runs on `http://localhost:5173`
- Backend runs on `http://localhost:8080`
- Browser blocks cross-origin requests

### Solution Options

**Option 1: Vite Proxy (Development)**
**Search:** "vite config proxy"  
**Edit:** `vite.config.ts`

**Option 2: CORS Headers (Backend)**
**Search:** "chi cors middleware"  
**Edit:** Your Go backend

### What to Figure Out
1. How to configure Vite proxy for `/api` requests?
2. How to use `fetch()` in React component?
3. How to handle loading and error states?

### Expected Behavior
```tsx
// Click button
setLoading(true)

// Call API
fetch('/api/wol', { method: 'POST', ... })

// Show success or error
setLoading(false)
```

---

## Step 7: Add Basic Routing (Optional)

If you want multiple pages:

### What to Search
- "react router v6 setup"
- "react router dom vite"
- "react router typescript"

### Documentation Pointers
- **React Router:** https://reactrouter.com/en/main/start/tutorial

### What to Figure Out
1. How to install `react-router-dom`?
2. How to set up `BrowserRouter`?
3. How to create route paths?

### Basic Structure
```
Routes:
- / (Home page with WOL controls)
- /devices (List of saved devices)
- /contact (Contact form - future)
```

**For now:** You can skip routing and keep everything in one page.

---

## Testing Checklist

- [ ] Vite dev server runs without errors
- [ ] Hot reload works (change code, see instant update)
- [ ] Tailwind classes apply correctly
- [ ] Component renders with props
- [ ] Button click triggers event
- [ ] Can fetch from backend (check Network tab)
- [ ] Console shows no errors

---

## Common Issues & What to Google

### Error: "Cannot find module '@/components/...'"
**Search:** "vite path alias typescript"  
**Fix:** Configure `tsconfig.json` and `vite.config.ts`

### Tailwind classes not working
**Search:** "tailwind vite not working"  
**Check:**
- Tailwind directives in `index.css`?
- `tailwind.config.js` correct?
- Imported CSS in `main.tsx`?

### CORS errors when calling backend
**Search:** "vite proxy configuration"  
**Solution:** Set up proxy in `vite.config.ts`

### TypeScript errors on component props
**Search:** "react typescript props interface"  
**Learn:** How to define prop types

---

## Understanding Check

Before moving to Chapter 04, answer:

1. What's the difference between Vite and Create React App?
2. Why use TypeScript instead of JavaScript?
3. What's the purpose of `index.html` in a Vite project?
4. How does Tailwind's JIT compilation work?
5. What's the difference between `tsx` and `jsx`?
6. Why do we need a proxy for API calls during development?

---

## Checkpoint: What You Have Now

1. ✅ React + TypeScript + Vite running
2. ✅ TailwindCSS working
3. ✅ (Optional) Shadcn UI installed
4. ✅ Lucide icons installed
5. ✅ Basic component created
6. ✅ Can call backend API

---

## Next: Chapter 04

You'll build the actual dashboard UI:
- Device cards
- WOL controls
- Loading states
- Error handling
- Responsive layout

**Tip:** Keep the dev server running (`npm run dev`) while coding. Hot reload is your friend.
