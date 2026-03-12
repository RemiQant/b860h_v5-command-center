# STB Command Center - Learning-First Approach

**Goal:** Ship a working full-stack app while learning proper software engineering  
**Timeline:** 2-3 weeks  
**Learning Style:** Self-directed with guidance - you read docs, I point you in the right direction

---

## Philosophy: Learn by Doing

This guide won't give you complete code. Instead, it will:
- ✅ Tell you WHAT to build
- ✅ Point you to WHERE to learn it
- ✅ Explain WHY you're doing it
- ❌ Not spoon-feed you solutions

**Your job:** Read official docs, experiment, break things, fix them, and learn.

---

## 1. What You're Building

A dashboard running on your ZTE B860H V5 STB that can:
1. **Wake devices** via Wake-on-LAN (send magic packets)
2. **Contact form** that sends to Discord webhook
3. **Health monitoring** (future: AdGuard/Gatus integration)

Simple, focused, shippable.

---

## 2. Tech Stack Decisions

### Frontend: Need to Decide

**Option A: Next.js**
- ✅ Full framework (routing, API routes, SSR built-in)
- ✅ Production-ready patterns
- ✅ Better for portfolio/SEO
- ❌ More opinionated
- ❌ Heavier learning curve

**Option B: React + Vite + React Router**
- ✅ More control over architecture
- ✅ Lighter, faster dev server (Vite is FAST)
- ✅ Learn fundamentals better
- ❌ Need to wire up routing yourself
- ❌ More decisions to make

**My Recommendation for Learning: React + Vite**
- You'll understand routing better by setting it up yourself
- Faster iteration (Vite HMR is instant)
- Lighter mental model for first project
- Can always upgrade to Next.js later

**UI Components:**
- Start with **Shadcn** - it's copy-paste, you own the code, easy to customize
- If you want to build from scratch later, you can replace components one by one

### Backend: Go + Chi ✅ (Good Choice)
- Chi is lightweight, follows standard `net/http` patterns
- Perfect for learning REST APIs

---

## 3. Project Structure

```
b860h_v5-command-center/
├── web/                    # Frontend (React + Vite)
│   ├── src/
│   │   ├── main.tsx
│   │   ├── App.tsx
│   │   ├── components/
│   │   ├── pages/
│   │   └── lib/
│   ├── package.json
│   └── vite.config.ts
│
├── cmd/
│   └── server/
│       └── main.go        # Entry point
│
├── internal/
│   ├── api/
│   │   ├── router.go
│   │   └── handlers/
│   ├── services/          # Business logic
│   └── models/            # Types
│
├── go.mod
└── docs/                  # Learning guides (this!)
```

---

## 4. Learning Roadmap

Your path to shipping this:

### Week 1: Foundation
**Goal:** Get basic app running

1. **Frontend Setup**
   - [ ] Initialize Vite + React + TypeScript project
   - [ ] Set up Tailwind CSS
   - [ ] Install Shadcn UI (or plan component architecture)
   - [ ] Create basic routing (home, contact pages)
   
2. **Backend Setup**
   - [ ] Initialize Go module
   - [ ] Set up Chi router
   - [ ] Create health check endpoint (`GET /health`)
   - [ ] Test with curl/Postman

3. **Integration**
   - [ ] Connect frontend to backend API
   - [ ] Handle CORS
   - [ ] Display health check data

### Week 2: Core Features
**Goal:** Ship working features

1. **Wake-on-LAN**
   - [ ] Understand magic packet structure
   - [ ] Implement Go WOL service (UDP broadcast)
   - [ ] Create frontend UI for device selection
   - [ ] Test waking actual device

2. **Contact Form**
   - [ ] Build form component with validation
   - [ ] Create Go endpoint for Discord webhook
   - [ ] Test sending messages to Discord

### Week 3: Polish & Deploy
**Goal:** Production ready

1. **Polish**
   - [ ] Error handling
   - [ ] Loading states
   - [ ] Responsive design

2. **Deploy**
   - [ ] Build React for production
   - [ ] Embed in Go binary
   - [ ] Cross-compile for ARM
   - [ ] Deploy to STB

---

## 5. Documentation Pointers

I won't write code for you. Instead, here's where to learn each piece:

### Frontend Learning Resources

**Vite:**
- **Start here:** https://vitejs.dev/guide/
- **What to read:** "Getting Started", "Features", "Building for Production"
- **Key concepts:** HMR, build optimizations, env variables

**React + TypeScript:**
- **Start here:** https://react.dev/learn
- **What to read:** "Quick Start", "Thinking in React", "Managing State"
- **TypeScript:** https://react-typescript-cheatsheet.netlify.app/

**React Router:**
- **Start here:** https://reactrouter.com/en/main/start/tutorial
- **What to read:** "Tutorial", "Routing", "Data Loading"

**Tailwind CSS:**
- **Start here:** https://tailwindcss.com/docs/installation/using-vite
- **What to read:** "Installation", "Utility-First Fundamentals", "Responsive Design"

**Shadcn UI:**
- **Start here:** https://ui.shadcn.com/docs/installation/vite
- **What to read:** "Installation", "Components" (Button, Card, Input, Form)

### Backend Learning Resources

**Go Basics:**
- **Start here:** https://go.dev/tour/
- **What to read:** Complete the tour (it's interactive!)
- **Then:** https://go.dev/doc/effective_go

**Chi Router:**
- **Start here:** https://github.com/go-chi/chi
- **What to read:** README examples, look at `/examples` folder
- **Key concepts:** Middleware, route groups, URL parameters

**Wake-on-LAN in Go:**
- **Library:** https://github.com/mdlayher/wol
- **What to search:** "UDP programming in Go", "broadcasting packets"
- **Understand:** Magic packet structure (6 × 0xFF + 16 × MAC address)

**HTTP Client (Discord):**
- **Start here:** https://pkg.go.dev/net/http
- **What to read:** `http.Post`, `http.Client`
- **Discord Webhooks:** https://discord.com/developers/docs/resources/webhook

---

## 6. How to Use These Guides

Each chapter will give you:
- 🎯 **What to build** - Clear feature requirements
- 📚 **Where to learn** - Links to official docs  
- ❓ **What to search** - Google/Stack Overflow terms
- 🚫 **No complete code** - You figure it out!

When stuck:
1. Read the official docs first
2. Search Stack Overflow
3. Check GitHub issues
4. Ask me SPECIFIC questions (not "write this for me")

---

## 7. Chapter Guide

### 📖 Chapter 00: Overview (You Are Here)
- Project goals and philosophy
- Tech stack decisions
- Learning roadmap

### 🔧 Chapter 01: Backend Setup & First Endpoint
**Time:** 2-3 hours | **Goal:** Health check endpoint working

- Initialize Go project structure
- Install Chi router
- Create first route
- Test with curl

### 🌐 Chapter 02: Wake-on-LAN Implementation
**Time:** 2-4 hours | **Goal:** Send magic packets

- Understand WOL protocol
- Implement magic packet sending
- Create WOL API endpoint
- Test waking real device

### ⚛️ Chapter 03: React Frontend Setup
**Time:** 2-3 hours | **Goal:** Dev server running

- Initialize Vite + React + TypeScript
- Install TailwindCSS & Shadcn
- Create first component
- Connect to backend API

### 📊 Chapter 04: Dashboard UI & WOL Controls
**Time:** 4-6 hours | **Goal:** Functional dashboard

- Build device cards
- Implement wake buttons
- Add loading/error states
- Responsive layout

### 📬 Chapter 05: Contact Form & Discord Integration
**Time:** 3-4 hours | **Goal:** Working contact form

- Build form component
- Validate input
- Send to Discord webhook
- Rate limiting

### 🚀 Chapter 06: Production Deployment
**Time:** 4-6 hours | **Goal:** Running 24/7 on B860H V5

- Cross-compile for ARM
- systemd service setup
- Caddy reverse proxy
- HTTPS & monitoring

### 📱 Chapter 07: PWA & Mobile Optimization (Bonus)
**Time:** 3-5 hours | **Goal:** Native-like mobile experience

- Progressive Web App (PWA)
- Tablet-first responsive design
- KWGT Android widgets
- Offline functionality

### 📊 Chapter 08: Monitoring & Observability
**Time:** 4-6 hours | **Goal:** Production monitoring & error tracking

- Sentry error tracking (frontend + backend)
- Structured logging with zerolog
- Prometheus metrics
- Health checks & uptime monitoring
- Alerting strategy

---

## Next Steps

Ready to start? Go to **[Chapter 01: Backend Setup](01-BACKEND-SETUP.md)** 

Remember: **Read docs, experiment, break things, learn.** That's how you become a real engineer.



---

*Document Version: 1.0*  
*Last Updated: January 28, 2026*  
*Author: Your VP of Engineering*
