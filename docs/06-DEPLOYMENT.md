# Chapter 06: Production Deployment

**Goal:** Deploy to ZTE B860H V5 (ARM device)  
**Time:** 4-6 hours  
**You'll Ship:** 24/7 running production app

---

## What You're Deploying To

**Device:** ZTE B860H V5 Set-Top Box
- **Architecture:** ARM (armv7 or arm64)
- **OS:** Linux (likely Debian/Ubuntu-based)
- **Access:** SSH via Tailscale VPN
- **Power:** Always on, low power consumption

---

## Step 1: Prepare Your Build

### Backend: Cross-Compile for ARM

### What to Search
- "golang cross compile arm"
- "GOOS GOARCH list"
- "go build for raspberry pi"

### Documentation Pointers
- **Go Cross Compilation:** https://go.dev/doc/install/source#environment

### What to Figure Out

**1. Determine Device Architecture**
SSH into your B860H and run:
```bash
uname -m
# Output examples:
# armv7l → Use GOARCH=arm GOARM=7
# aarch64 → Use GOARCH=arm64
```

**2. Build Command**
**Search:** "golang cross compile command"

```bash
# Example - adapt to your architecture
GOOS=linux GOARCH=arm64 go build -o stb-server cmd/server/main.go

# Or for armv7:
GOOS=linux GOARCH=arm GOARM=7 go build -o stb-server cmd/server/main.go
```

**3. Optimize Binary**
**Search:** "golang binary size optimization"

Reduce binary size:
```bash
go build -ldflags="-s -w" -o stb-server cmd/server/main.go
```

### What to Figure Out
1. What's the difference between `arm` and `arm64`?
2. What do `-s -w` flags do?
3. How to verify binary architecture?
   - **Search:** "linux file command architecture"

---

### Frontend: Build for Production

### What to Search
- "vite build production"
- "react production build"
- "vite build output directory"

### What to Figure Out

**1. Build Command**
```bash
cd web/
npm run build
```

**2. Output Location**
Where does Vite put built files?
- Default: `web/dist/`
- Contains: `index.html`, `assets/` (JS, CSS)

**3. Optimize Build**
**Search:** "vite build optimization"

Check `vite.config.ts`:
```ts
// Your research: What settings optimize for production?
// - Minification?
// - Source maps?
// - Chunk splitting?
```

### Documentation Pointers
- **Vite Building:** https://vitejs.dev/guide/build.html

---

## Step 2: Serve Frontend from Go Backend

### What to Search
- "golang serve static files"
- "go chi serve spa"
- "golang embed static files"

### Option 1: Serve from Filesystem

**Search:** "chi static file server"

### Option 2: Embed in Binary (Recommended)

**Search:** "golang embed directive"  
**Documentation:** https://pkg.go.dev/embed

### What to Figure Out
1. How to use `//go:embed` directive?
2. How to serve embedded files with Chi?
3. How to handle SPA routing (all routes → index.html)?

### Example Pattern to Research
```go
// Your research: How to implement this?
//go:embed dist
var frontend embed.FS

// Serve at root: GET / → index.html
```

### SPA Routing Problem
**Issue:** Direct navigation to `/devices` returns 404  
**Solution:** All non-API routes should serve `index.html`

**Search:** "spa fallback server golang"

---

## Step 3: Environment Configuration

### What to Search
- "golang environment variables production"
- "twelve factor app config"
- ".env file golang"

### Variables to Configure
1. `PORT` - Server port (default: 8080)
2. `DISCORD_WEBHOOK_URL` - Contact form webhook
3. `ENVIRONMENT` - "production" or "development"
4. `CORS_ORIGINS` - Allowed origins (if needed)

### What to Figure Out

**1. Load .env File**
**Search:** "godotenv golang"

**Library:** `github.com/joho/godotenv`

**2. Access Environment Variables**
```go
// Your research: How to implement?
port := os.Getenv("PORT")
if port == "" {
    port = "8080" // default
}
```

**3. Validate Required Variables**
Fail fast if critical variables missing:
```go
webhookURL := os.Getenv("DISCORD_WEBHOOK_URL")
if webhookURL == "" {
    log.Fatal("DISCORD_WEBHOOK_URL not set")
}
```

---

## Step 4: Transfer to B860H V5

### What to Search
- "scp file transfer linux"
- "rsync deployment"
- "ssh copy file"

### What to Figure Out

**1. Create Deployment Directory on Device**
```bash
ssh user@b860h
mkdir -p /opt/stb-command-center
```

**2. Transfer Files**
**Search:** "scp copy directory"

```bash
# Transfer binary
scp stb-server user@b860h:/opt/stb-command-center/

# Transfer .env file
scp .env user@b860h:/opt/stb-command-center/

# If not using embed, transfer frontend:
scp -r web/dist user@b860h:/opt/stb-command-center/
```

**3. Set Permissions**
```bash
ssh user@b860h
chmod +x /opt/stb-command-center/stb-server
```

---

## Step 5: Create systemd Service

### What to Search
- "systemd service file example"
- "systemd service tutorial"
- "systemd user service"

### Documentation Pointers
- **systemd.service:** https://www.freedesktop.org/software/systemd/man/systemd.service.html

### What to Figure Out

**1. Create Service File**
```bash
ssh user@b860h
sudo nano /etc/systemd/system/stb-command-center.service
```

**2. Service Configuration**
**Search:** "systemd service file format"

**What to include:**
- `[Unit]` section: Description, After=network.target
- `[Service]` section: Type, ExecStart, WorkingDirectory, User, Restart
- `[Install]` section: WantedBy=multi-user.target

**3. Environment Variables**
How to pass `.env` to service?
**Search:** "systemd environment file"

**Option A:** `EnvironmentFile=/opt/stb-command-center/.env`  
**Option B:** Load in code with godotenv

**4. Enable & Start Service**
```bash
sudo systemctl daemon-reload
sudo systemctl enable stb-command-center
sudo systemctl start stb-command-center
```

**5. Check Status**
```bash
sudo systemctl status stb-command-center
```

### Common Service Directives
**Search:** "systemd restart policy"
- `Restart=on-failure`
- `RestartSec=5s`
- `StartLimitInterval=0`

---

## Step 6: Setup Reverse Proxy (Caddy)

### Why Reverse Proxy?
- Serve on port 80/443 (without root for Go app)
- HTTPS with automatic Let's Encrypt certificates
- Static file caching
- Better security

### What to Search
- "caddy install linux"
- "caddy reverse proxy"
- "caddy automatic https"

### Documentation Pointers
- **Caddy Docs:** https://caddyserver.com/docs/
- **Installing:** https://caddyserver.com/docs/install

### What to Figure Out

**1. Install Caddy on B860H**
**Search:** "caddy install debian arm"

**2. Create Caddyfile**
```bash
sudo nano /etc/caddy/Caddyfile
```

**3. Configure Reverse Proxy**
**Search:** "caddy reverse proxy example"

**What to configure:**
- Your domain name
- Proxy to `localhost:8080`
- Automatic HTTPS
- Static file caching headers

**4. Start Caddy**
```bash
sudo systemctl enable caddy
sudo systemctl start caddy
```

### Caddyfile Research
**Search:** "caddy spa configuration"  
**Needed:** Handle SPA routing, proxy /api to backend

---

## Step 7: Domain & DNS Setup

### What to Search
- "dns a record setup"
- "dynamic dns tailscale"
- "cloudflare dns"

### What to Figure Out

**1. Get Domain Name** (if you don't have one)
- Register at Cloudflare, Namecheap, etc.
- Or use Tailscale MagicDNS

**2. Point Domain to Your B860H**
**Options:**
- **Tailscale VPN:** Use internal domain (e.g., `b860h.tail1234.ts.net`)
- **Public IP:** Create A record pointing to your public IP
- **Dynamic DNS:** If IP changes
  - **Search:** "ddns setup"

**3. Configure Caddy for Your Domain**
Replace `example.com` in Caddyfile with your domain

### Tailscale Option (Recommended for Private Use)
**Search:** "tailscale https caddy"

**Benefits:**
- No public exposure
- Free HTTPS certificates
- No port forwarding needed

---

## Step 8: Monitoring & Logs

### What to Search
- "systemd journalctl"
- "golang logging best practices"
- "systemd log rotation"

### What to Figure Out

**1. View Service Logs**
```bash
# Live logs
sudo journalctl -u stb-command-center -f

# Last 100 lines
sudo journalctl -u stb-command-center -n 100

# Since boot
sudo journalctl -u stb-command-center -b
```

**2. Add Logging to Your App**
**Search:** "golang structured logging"

**Libraries:**
- Standard library: `log` package
- Advanced: `github.com/rs/zerolog`
- Or: `github.com/sirupsen/logrus`

**What to log:**
- Server start/stop
- API requests (with status codes)
- Errors with context
- Wake-on-LAN actions

**3. Log Levels**
**Search:** "log levels best practices"
- DEBUG: Verbose info
- INFO: Normal operations
- WARN: Unexpected but handled
- ERROR: Failures

**4. Log Rotation**
**Search:** "systemd journal max size"

Configure `/etc/systemd/journald.conf`:
```
SystemMaxUse=100M
```

---

## Step 9: Security Hardening

### What to Search
- "linux server security checklist"
- "systemd security options"
- "golang http security headers"

### What to Figure Out

**1. Firewall (if applicable)**
**Search:** "ufw firewall ubuntu"

Only allow:
- SSH (via Tailscale)
- HTTP/HTTPS (80, 443)

**2. systemd Security Options**
**Search:** "systemd service hardening"

Add to service file:
```ini
[Service]
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=strict
ProtectHome=true
ReadOnlyPaths=/
ReadWritePaths=/opt/stb-command-center
```

**3. Security Headers**
**Search:** "golang security headers middleware"

Add to your Chi router:
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `Content-Security-Policy`

**Search:** "chi security headers middleware"

**4. Rate Limiting (Already Implemented)**
Verify it works in production

---

## Step 10: Testing Production Deployment

### Checklist

**Backend:**
- [ ] Service starts without errors
- [ ] Logs show "Server listening on :8080"
- [ ] `/health` endpoint responds
- [ ] `/api/wol` endpoint works
- [ ] `/api/contact` endpoint works

**Frontend:**
- [ ] Homepage loads at your domain
- [ ] All assets load (check browser console)
- [ ] Device cards display
- [ ] Wake buttons work
- [ ] Contact form submits

**Caddy:**
- [ ] HTTPS certificate obtained
- [ ] HTTP redirects to HTTPS
- [ ] Reverse proxy works
- [ ] No certificate warnings

**systemd:**
- [ ] Service starts on boot
- [ ] Service restarts on failure
- [ ] Logs accessible via journalctl

**Test Failure Scenarios:**
- Kill the process → should auto-restart
- Reboot device → service should start
- Submit spam → rate limit should block

---

## Continuous Deployment

### What to Search
- "deployment automation script"
- "bash deployment script"
- "makefile deployment"

### Create Deploy Script

**File:** `deploy.sh`

```bash
#!/bin/bash
# Your research: What should this script do?
# 1. Build Go binary for ARM
# 2. Build frontend
# 3. SCP to device
# 4. Restart service
```

Make it executable:
```bash
chmod +x deploy.sh
```

### Makefile Pattern
**Search:** "makefile example"

```makefile
.PHONY: build deploy

build:
    # Build commands

deploy:
    # Deploy commands
```

---

## Backup & Recovery

### What to Search
- "backup deployment script"
- "systemd service backup"

### What to Back Up
1. `.env` file (secrets!)
2. Service file
3. Caddyfile
4. Source code (Git)

### Where to Store
- Git repository (remove secrets!)
- Encrypted backup
- **Search:** "git secrets management"

### Recovery Plan
If device dies:
1. Install OS on new device
2. Run deploy script
3. Restore `.env` file
4. Start service

**Document this process!**

---

## Performance Optimization

### What to Search
- "golang http server performance"
- "caddy performance tuning"
- "small server optimization"

### What to Monitor
1. **Memory usage:** `free -h`
2. **CPU usage:** `top`
3. **Binary size:** `ls -lh stb-server`
4. **Response times:** Browser dev tools

### Optimization Targets
- Binary size: < 20 MB
- Memory usage: < 50 MB
- Response time: < 100ms

### If Resource Constrained
**Search:** "golang memory profiling"

---

## Common Deployment Issues

### Service won't start
**Search:** "systemd service failed to start"  
**Debug:** `sudo journalctl -xe`

### Port already in use
**Search:** "linux check port usage"  
**Command:** `sudo netstat -tulpn | grep :8080`

### Permission denied
**Search:** "linux file permissions"  
**Fix:** Check binary is executable, user has access

### HTTPS certificate fails
**Search:** "caddy certificate error"  
**Check:** Port 80/443 accessible, DNS correct

### Frontend shows blank page
**Search:** "react production build blank page"  
**Check:** Browser console for errors, check paths

---

## Monitoring Dashboard (Future)

### Tools to Research
- **Uptime Monitoring:** UptimeRobot (free)
- **Self-Hosted:** Gatus (you mentioned this!)
- **Logs:** Grafana Loki

**Search:**
- "gatus self hosted monitoring"
- "uptime monitoring free"
- "lightweight monitoring tools"

---

## Understanding Check

Before calling it done:

1. What's the purpose of a reverse proxy?
2. Why cross-compile instead of building on device?
3. How does systemd ensure your app stays running?
4. What's the difference between HTTP and HTTPS?
5. Why is log rotation important?
6. What security risks does public deployment introduce?

---

## 🎉 Congratulations!

You've deployed a full-stack application:
- ✅ Go backend (cross-compiled for ARM)
- ✅ React frontend (production build)
- ✅ systemd service (auto-restart)
- ✅ Caddy reverse proxy (HTTPS)
- ✅ Monitoring & logs
- ✅ Security hardening

**You shipped it.**

---

## What's Next?

### � Chapter 08: Monitoring & Observability (Recommended Next)

Your app is deployed! Now make sure you know when it breaks.

**[Go to Chapter 08: Monitoring & Observability](08-MONITORING-OBSERVABILITY.md)**

You'll add:
- **Sentry** - Error tracking (frontend + backend)
- **Structured logging** - Debug production issues
- **Metrics** - Track WOL success rate, API performance
- **Alerts** - Get notified when things break

**Time:** 4-6 hours  
**Result:** Production-grade monitoring and observability

---

### 📱 Chapter 07: PWA & Mobile (Bonus Extension)

Want to use your dashboard on mobile like a native app?

**[Go to Chapter 07: PWA & Mobile Optimization](07-PWA-MOBILE.md)**

You'll add:
- **Progressive Web App** - Install on phone/tablet
- **Tablet-first design** - Optimized touch UI
- **KWGT widgets** - Wake devices from Android home screen
- **Offline support** - Service workers & caching

**Time:** 3-5 hours  
**Result:** Native-like mobile experience with home screen widgets

---

### Future Enhancement Ideas

1. **Persistent device storage** (SQLite or JSON file)
2. **User authentication** (if exposing publicly)
3. **Device statistics** (last wake time, success rate)
4. **Push notifications** (device status alerts)

### Skills to Level Up
- **Go concurrency** (goroutines, channels)
- **Database design** (PostgreSQL, SQLite)
- **Testing** (unit tests, integration tests)
- **CI/CD** (GitHub Actions, automated deployment)
- **Docker** (containerization)

---

## Resources for Continued Learning

### Go
- **Go by Example:** https://gobyexample.com/
- **Effective Go:** https://go.dev/doc/effective_go
- **Go Standard Library:** https://pkg.go.dev/std

### React
- **React Docs:** https://react.dev/learn
- **TypeScript Handbook:** https://www.typescriptlang.org/docs/handbook/
- **Patterns.dev:** https://www.patterns.dev/

### DevOps
- **systemd by Example:** https://systemd.by-example.com/
- **Caddy Documentation:** https://caddyserver.com/docs/
- **Linux Journey:** https://linuxjourney.com/

---

**Remember:** You learned this by doing. Keep building.
