# STB Command Center Dashboard

A full-stack web application for managing network devices and services, designed to run on the ZTE B860H V5 set-top box.

## Features

- 🔌 **Wake-on-LAN**: Send magic packets to wake up devices on your network
- 📧 **Contact Form**: Send messages via Discord webhook integration
- 📊 **Device Dashboard**: Manage and monitor network devices
- 🔒 **Security**: Rate limiting, input validation, HTTPS support
- 📱 **Responsive UI**: Works on desktop, tablet, and mobile

## Tech Stack

### Backend
- **Go 1.21+** - Fast, compiled language perfect for ARM devices
- **Chi Router** - Lightweight HTTP router
- **mdlayher/wol** - Wake-on-LAN implementation

### Frontend
- **React 18** with **TypeScript**
- **Vite** - Fast build tool with HMR
- **TailwindCSS** - Utility-first CSS framework
- **Shadcn UI** - Accessible component library
- **Lucide Icons** - Beautiful icon library

### Deployment
- **systemd** - Service management
- **Caddy** - Reverse proxy with automatic HTTPS
- **Tailscale** - Private network access (optional)

## Getting Started

### Prerequisites

- Go 1.21 or higher
- Node.js 20 or higher
- npm or pnpm

### Development Setup

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd b860h_v5-command-center
   ```

2. **Backend Setup**
   ```bash
   # Install Go dependencies
   go mod download
   
   # Run development server
   go run cmd/server/main.go
   ```

3. **Frontend Setup**
   ```bash
   cd web
   
   # Install dependencies
   npm install
   
   # Start dev server
   npm run dev
   ```

4. **Environment Variables**
   
   Create a `.env` file in the project root:
   ```env
   PORT=8080
   DISCORD_WEBHOOK_URL=https://discord.com/api/webhooks/YOUR_WEBHOOK_HERE
   ENVIRONMENT=development
   ```

## Documentation

📚 **Complete learning guides available in [docs/](docs/)**

Start with [docs/00-OVERVIEW.md](docs/00-OVERVIEW.md) for:
- Learning philosophy (self-directed approach)
- Tech stack decisions
- Step-by-step chapter guides

### Chapter Structure

1. **[Backend Setup](docs/01-BACKEND-SETUP.md)** - Initialize Go project and create first endpoint
2. **[Wake-on-LAN](docs/02-WAKE-ON-LAN.md)** - Implement magic packet sending
3. **[React Setup](docs/03-REACT-SETUP.md)** - Initialize React + Vite + TypeScript
4. **[Dashboard UI](docs/04-DASHBOARD-UI.md)** - Build device cards and controls
5. **[Contact & Discord](docs/05-CONTACT-DISCORD.md)** - Form submission to Discord
6. **[Deployment](docs/06-DEPLOYMENT.md)** - Production deployment to B860H V5
7. **[PWA & Mobile](docs/07-PWA-MOBILE.md)** - Progressive Web App + KWGT widgets (Bonus)
8. **[Monitoring & Observability](docs/08-MONITORING-OBSERVABILITY.md)** - Sentry, logging, metrics & alerts

Each chapter provides:
- 🎯 What to build
- 📚 Where to learn (documentation links)
- ❓ What to search
- ✅ Testing checklists

## Project Structure

```
b860h_v5-command-center/
├── cmd/
│   └── server/
│       └── main.go          # Application entry point
├── internal/
│   ├── api/
│   │   ├── router.go        # Route definitions
│   │   └── handlers/        # HTTP handlers
│   │       ├── health.go
│   │       ├── wol.go
│   │       └── contact.go
│   ├── wol/
│   │   └── service.go       # Wake-on-LAN business logic
│   └── discord/
│       └── webhook.go       # Discord integration
├── web/                     # React frontend
│   ├── src/
│   │   ├── components/
│   │   ├── lib/
│   │   └── App.tsx
│   └── package.json
├── docs/                    # Learning guides
├── go.mod
└── README.md               # You are here
```

## API Endpoints

### Health Check
```
GET /health
```
Returns server health status.

### Wake-on-LAN
```
POST /api/wol
Content-Type: application/json

{
  "mac": "AA:BB:CC:DD:EE:FF"
}
```
Sends magic packet to wake device.

### Contact Form
```
POST /api/contact
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "message": "Your message here"
}
```
Sends message to Discord webhook.

## Deployment

### Building for Production

```bash
# Build frontend
cd web
npm run build

# Build backend (cross-compile for ARM)
GOOS=linux GOARCH=arm64 go build -ldflags="-s -w" -o stb-server cmd/server/main.go
```

See [Chapter 06: Deployment](docs/06-DEPLOYMENT.md) for complete deployment guide including:
- Cross-compilation for ARM
- systemd service setup
- Caddy reverse proxy configuration
- HTTPS with Let's Encrypt
- Monitoring and logging

## Testing

```bash
# Backend tests
go test ./...

# Frontend tests
cd web
npm test
```

## Contributing

This is a learning project. Feel free to:
- Add features
- Improve documentation
- Fix bugs
- Share your deployment experiences

## License

MIT

## Acknowledgments

- Built as a learning project for full-stack development
- Documentation follows learn-by-doing philosophy
- Designed for deployment on ARM devices (ZTE B860H V5)

---

**Documentation Style:** This project uses a unique "pointer-based" learning approach. Instead of providing complete code solutions, the documentation guides you to official resources and teaches you how to research and implement features yourself.

Start your journey: **[docs/00-OVERVIEW.md](docs/00-OVERVIEW.md)**
