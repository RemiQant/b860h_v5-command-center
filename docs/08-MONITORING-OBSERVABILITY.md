# Chapter 08: Monitoring & Observability

**Goal:** Track errors, performance, and application health  
**Time:** 4-6 hours  
**You'll Ship:** Production monitoring with Sentry + structured logging

---

## What You're Building

Complete observability for your dashboard:
1. **Error Tracking** - Sentry for frontend + backend errors
2. **Structured Logging** - Proper logging in Go
3. **Metrics** - Track WOL success rate, API latency
4. **Health Checks** - Endpoint monitoring
5. **Alerts** - Get notified when things break

**Why Monitor?**
- Know when errors happen (before users complain)
- Debug production issues faster
- Track performance over time
- Prove your app is reliable

---

## Part 1: Sentry Error Tracking

### What is Sentry?

**Sentry** - Error tracking and performance monitoring platform

**Free tier includes:**
- 5,000 errors/month
- 10,000 performance transactions/month
- 30-day history
- Perfect for personal projects

### Step 1: Create Sentry Account & Project

### What to Search
- "sentry getting started"
- "sentry create project"
- "sentry dsn"

### Documentation Pointers
- **Sentry Docs:** https://docs.sentry.io/
- **Go SDK:** https://docs.sentry.io/platforms/go/
- **JavaScript SDK:** https://docs.sentry.io/platforms/javascript/

### What to Figure Out

**1. Sign Up**
- Go to https://sentry.io/signup/
- Create free account
- Verify email

**2. Create Two Projects**
- **Project 1:** "STB-Backend" (Platform: Go)
- **Project 2:** "STB-Frontend" (Platform: React)

**3. Get DSN Keys**
**Search:** "what is sentry dsn"

DSN (Data Source Name) = URL to send errors to

Format: `https://<key>@<org>.ingest.sentry.io/<project-id>`

**Save both DSNs:**
- Backend DSN → store in `.env`
- Frontend DSN → store in Vite env

---

### Step 2: Integrate Sentry in Go Backend

### What to Search
- "sentry go installation"
- "sentry go error tracking"
- "sentry go http middleware"

### What to Figure Out

**1. Install Sentry Go SDK**
```bash
go get github.com/getsentry/sentry-go
```

**2. Initialize Sentry**
**Search:** "sentry go init"

**Where to initialize:**
- In `main.go` before starting server
- Set DSN from environment variable
- Configure options (environment, release, sample rate)

**Questions to answer:**
- When should you call `sentry.Flush()`?
- What's a good sample rate for performance monitoring?
- How to set the environment (dev/prod)?

**3. Capture Errors**
**Search:** "sentry go capture error"

**When to capture:**
- Unhandled panics
- WOL send failures
- Discord webhook failures
- Database errors (future)
- Any unexpected errors

**Manual capture:**
```go
// Your research: How to implement?
if err != nil {
    sentry.CaptureException(err)
    // Also log locally
}
```

**4. HTTP Middleware**
**Search:** "sentry go chi middleware"

Automatically capture panics and errors from HTTP handlers.

**What to implement:**
- Wrap Chi router with Sentry middleware
- Capture request context
- Track response status codes
- Include user info (if authenticated)

**5. Add Context**
**Search:** "sentry go breadcrumbs"

**Breadcrumbs** = trail of events leading to error

Example:
```go
// When user triggers WOL
sentry.AddBreadcrumb(&sentry.Breadcrumb{
    Category: "wol",
    Message:  "Sending magic packet",
    Level:    sentry.LevelInfo,
})
```

**6. Custom Tags**
**Search:** "sentry go tags"

Add metadata to errors:
- Device MAC being woken
- User IP address
- Request ID
- Feature being used

---

### Step 3: Integrate Sentry in React Frontend

### What to Search
- "sentry react installation"
- "sentry react vite"
- "sentry react error boundary"

### What to Figure Out

**1. Install Sentry React SDK**
```bash
cd web/
npm install @sentry/react
```

**2. Initialize Sentry**
**Search:** "sentry react init vite"

**Where to initialize:**
- In `main.tsx` before rendering React
- Use environment variable for DSN
- Set up integrations (BrowserTracing for performance)

**3. Environment Variables in Vite**
**Search:** "vite environment variables"

Create `web/.env`:
```
VITE_SENTRY_DSN=https://your-frontend-dsn@sentry.io/project
```

Access in code:
```ts
const dsn = import.meta.env.VITE_SENTRY_DSN
```

**4. Error Boundary**
**Search:** "sentry react error boundary"

Wrap your app to catch React errors:
```tsx
// Your research: How to implement?
<Sentry.ErrorBoundary fallback={<ErrorFallback />}>
  <App />
</Sentry.ErrorBoundary>
```

**5. Track API Errors**
**Search:** "sentry javascript fetch errors"

Capture failed API calls:
```ts
// When WOL API fails
try {
  await fetch('/api/wol', {...})
} catch (error) {
  Sentry.captureException(error)
  // Show error to user
}
```

**6. User Feedback**
**Search:** "sentry user feedback widget"

Let users report issues:
- Show dialog when error occurs
- User can add description
- Linked to error in Sentry

**7. Performance Monitoring**
**Search:** "sentry react performance"

Track:
- Page load time
- Component render time
- API request duration
- Navigation timing

**8. Custom Transactions**
**Search:** "sentry javascript custom transaction"

Track specific operations:
```ts
// Measure WOL operation time
const transaction = Sentry.startTransaction({
  name: "wake-device",
  op: "wol"
})

// ... perform WOL ...

transaction.finish()
```

---

## Part 2: Structured Logging

### Why Structured Logging?

**Problem with standard logging:**
```go
log.Printf("User woke device %s from %s", mac, ip)
```
Hard to search, parse, or analyze.

**Structured logging:**
```go
logger.Info().
  Str("mac", mac).
  Str("ip", ip).
  Msg("Device woken")
```
Easy to query: "Show all WOL events for MAC XX:XX:XX"

### Step 1: Choose Logging Library

### What to Search
- "golang structured logging"
- "zerolog vs zap vs logrus"
- "golang logging best practices"

### Options

**Zerolog** (Recommended)
- ✅ Zero allocations
- ✅ Fast
- ✅ JSON output
- ✅ Simple API

**Zap** (Alternative)
- ✅ Uber's logger
- ✅ Very fast
- ❌ More complex API

**Logrus** (Popular but slower)
- ✅ Mature
- ❌ Slower than others
- ❌ Not actively developed

### Documentation Pointers
- **Zerolog:** https://github.com/rs/zerolog
- **Zap:** https://github.com/uber-go/zap

---

### Step 2: Implement Zerolog

### What to Search
- "zerolog getting started"
- "zerolog middleware chi"
- "zerolog configuration"

### What to Figure Out

**1. Install Zerolog**
```bash
go get github.com/rs/zerolog
```

**2. Initialize Logger**
**Search:** "zerolog setup"

**Where to create:**
- Global logger in `main.go`
- Or singleton logger package

**Configuration:**
- Development: Pretty console output
- Production: JSON output
- Set log level from environment

**3. Log Levels**
**Search:** "zerolog log levels"

**When to use each:**
- **Trace:** Very detailed (rarely used)
- **Debug:** Developer info (disabled in prod)
- **Info:** General app events
- **Warn:** Something unexpected but handled
- **Error:** Error occurred, needs attention
- **Fatal:** Unrecoverable error, exit app
- **Panic:** Panic immediately

**4. Structured Fields**
**Search:** "zerolog field types"

Add context to logs:
```go
// Your research: How to implement?
logger.Info().
  Str("mac", deviceMAC).
  Str("device_name", name).
  Dur("duration", elapsed).
  Bool("success", true).
  Msg("WOL packet sent")
```

**5. HTTP Middleware**
**Search:** "zerolog chi middleware"

Log all HTTP requests:
- Method, path, status
- Duration
- Response size
- User agent
- Request ID

**6. Context Logger**
**Search:** "zerolog context"

Attach logger to request context:
```go
// Add to context in middleware
ctx := logger.WithContext(r.Context())

// Retrieve in handler
zerolog.Ctx(ctx).Info().Msg("Processing request")
```

---

### Step 3: Log Management Strategy

### What to Search
- "golang log rotation"
- "systemd journal"
- "log aggregation tools"

### Where to Store Logs

**Option A: systemd Journal** (Recommended for B860H)
- Logs automatically captured
- Query with `journalctl`
- Rotation handled automatically
- No extra setup needed

**Option B: File with Rotation**
**Search:** "golang lumberjack log rotation"

- Write to file
- Rotate daily or by size
- Keep last N files
- Library: `gopkg.in/natefinch/lumberjack.v2`

**Option C: Remote Log Aggregation**
**Search:** "log aggregation comparison"

**Options:**
- Loki (Grafana)
- ELK Stack
- Papertrail
- Logtail

**For your project:** systemd journal is simplest.

### What to Log

**Always log:**
- Application start/stop
- Configuration loaded
- Errors and warnings
- Important state changes

**For each WOL request:**
- Device MAC
- Client IP
- Success/failure
- Error details (if failed)
- Duration

**For each contact form:**
- Submission received
- Discord webhook success/fail
- Rate limit hits

**Don't log:**
- Passwords or secrets
- Full Discord webhook URL
- Personal data (emails - unless necessary)
- Every single health check (too noisy)

### Log Sampling

**Search:** "zerolog sampling"

In production, sample noisy logs:
```go
// Sample 1 in 10 health checks
sampledLogger := logger.Sample(&zerolog.BasicSampler{N: 10})
```

---

## Part 3: Metrics & Monitoring

### What are Metrics?

**Logs** = individual events  
**Metrics** = aggregated numbers over time

**Examples:**
- Total WOL packets sent
- Success rate (%)
- API response time (p50, p95, p99)
- Active goroutines
- Memory usage

### Step 1: Prometheus Metrics

### What to Search
- "prometheus golang"
- "prometheus metrics types"
- "promhttp handler"

### Documentation Pointers
- **Prometheus Go Client:** https://github.com/prometheus/client_golang
- **Best Practices:** https://prometheus.io/docs/practices/naming/

### What to Figure Out

**1. Install Prometheus Client**
```bash
go get github.com/prometheus/client_golang/prometheus
go get github.com/prometheus/client_golang/prometheus/promauto
go get github.com/prometheus/client_golang/prometheus/promhttp
```

**2. Metric Types**
**Search:** "prometheus metric types"

**Counter** - Only goes up
- Total WOL packets sent
- Total errors
- Total requests

**Gauge** - Can go up and down
- Active connections
- Memory usage
- Goroutine count

**Histogram** - Distribution of values
- Request duration
- Response size

**Summary** - Like histogram, pre-calculated quantiles

**3. Create Metrics**
**Search:** "prometheus go counter example"

Example metrics for your app:
```go
// Your research: How to implement?
var (
    wolPacketsSent = promauto.NewCounter(...)
    wolPacketsFailed = promauto.NewCounter(...)
    httpRequestDuration = promauto.NewHistogramVec(...)
    activeSessions = promauto.NewGauge(...)
)
```

**4. Instrument Your Code**
**Search:** "prometheus instrumentation"

Track WOL operations:
```go
// Increment counter
wolPacketsSent.Inc()

// Record duration
timer := prometheus.NewTimer(httpRequestDuration)
defer timer.ObserveDuration()
```

**5. Expose Metrics Endpoint**
**Search:** "prometheus go http handler"

Create endpoint: `GET /metrics`

```go
// Your research: How to implement?
r.Handle("/metrics", promhttp.Handler())
```

**6. Scrape Metrics**
**Search:** "prometheus scrape config"

**Options:**
- Run Prometheus locally (scrape your B860H)
- Use remote metrics service (Grafana Cloud)
- Just expose endpoint for manual checks

---

### Step 2: Application Metrics Dashboard

### What to Search
- "grafana cloud free tier"
- "grafana prometheus dashboard"
- "prometheus query examples"

### Grafana Cloud (Free Tier)

**What to figure out:**
1. Sign up for Grafana Cloud free tier
2. Get Prometheus remote write endpoint
3. Configure your app to push metrics
4. Create dashboards

**Alternative:** 
Run Grafana locally on your PC, scrape from B860H.

### Useful Queries

**Search:** "promql tutorial"

**WOL success rate:**
```promql
rate(wol_packets_sent_total[5m]) - rate(wol_packets_failed_total[5m])
```

**API latency (95th percentile):**
```promql
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

**Error rate:**
```promql
rate(http_errors_total[5m])
```

---

### Step 3: Simple In-App Monitoring

### What to Search
- "golang expvar"
- "runtime metrics golang"

### Built-in Metrics with expvar

**Search:** "golang expvar package"

**No dependencies needed!** Built into Go.

**What to figure out:**
1. Import `expvar` package
2. Register custom variables
3. Expose at `/debug/vars`

**Metrics available:**
- Goroutine count
- Memory stats
- Garbage collection
- Custom counters/gauges

**Simple way to track:**
```go
var wolCounter = expvar.NewInt("wol_packets_sent")
wolCounter.Add(1)
```

View metrics: `curl http://localhost:8080/debug/vars`

---

## Part 4: Health Checks & Uptime Monitoring

### Step 1: Enhanced Health Check Endpoint

### What to Search
- "health check endpoint best practices"
- "kubernetes liveness readiness"

### What to Figure Out

**Current:** `GET /health` returns `{"status": "ok"}`

**Enhanced health check:**
```json
{
  "status": "healthy",
  "timestamp": 1234567890,
  "version": "1.0.0",
  "uptime": 3600,
  "checks": {
    "database": "n/a",
    "discord_webhook": "healthy",
    "network": "healthy"
  }
}
```

**What to check:**
- Can send UDP packets? (test WOL capability)
- Discord webhook reachable?
- Memory usage acceptable?
- Disk space available?

**Status codes:**
- 200 = Healthy
- 503 = Unhealthy (something failed)

---

### Step 2: Uptime Monitoring Services

### What to Search
- "uptime monitoring free"
- "uptime robot vs pingdom"
- "health check monitoring"

### Free Options

**UptimeRobot** (Recommended)
- Free tier: 50 monitors
- Check every 5 minutes
- Email/Discord alerts
- Status page

**BetterUptime**
- Free tier: 10 monitors
- Beautiful status pages
- Incident management

**Cronitor**
- Cron job monitoring
- API endpoint monitoring

### What to Figure Out

**1. Create Uptime Monitor**
- Monitor your `/health` endpoint
- Set check interval (5 minutes)
- Set timeout (10 seconds)

**2. Configure Alerts**
**Search:** "uptimerobot discord webhook"

Send alerts to:
- Discord (same server as contact form)
- Email
- SMS (paid)

**3. Create Status Page**
**Search:** "uptimerobot status page"

Public page showing:
- Current status
- Uptime percentage
- Incident history

---

## Part 5: Alerting Strategy

### What Should Alert You?

**Critical (Immediate notification):**
- App crashes or becomes unreachable
- High error rate (>5% of requests)
- Disk space < 10%

**Warning (Check within hours):**
- Increased response time
- Discord webhook failing
- Memory usage high

**Info (Check when convenient):**
- New error type in Sentry
- Unusual traffic pattern

### Alert Channels

**Search:** "alerting best practices"

**Levels:**
1. **Critical:** Discord + Email
2. **Warning:** Discord only
3. **Info:** Sentry notification only

### Avoid Alert Fatigue

**Search:** "alert fatigue devops"

**Don't alert on:**
- Individual errors (unless critical)
- Health check success (only failures)
- Normal metrics (only anomalies)

**Do alert on:**
- Service unavailable
- Error spike
- Performance degradation

---

## Part 6: Debugging Production Issues

### Step 1: Error Investigation Workflow

**When Sentry catches an error:**

1. **Check Sentry issue:**
   - Error message and stack trace
   - Breadcrumbs (events leading to error)
   - User context
   - Device/browser info

2. **Check logs:**
   - SSH to B860H
   - `journalctl -u stb-server -f` (follow logs)
   - Search for related logs around error time

3. **Check metrics:**
   - Is error rate increasing?
   - What's the performance impact?
   - Is it affecting all users or specific ones?

4. **Reproduce:**
   - Try to recreate the issue
   - Check if it's environment-specific

### Step 2: Debug Utilities

### What to Search
- "golang pprof"
- "pprof tutorial"

### pprof - Performance Profiling

**Add to your app:**
```go
import _ "net/http/pprof"

// Register pprof handlers
r.Mount("/debug/pprof", middleware.Profiler())
```

**What you can profile:**
- CPU usage
- Memory allocations
- Goroutine stacks
- Blocking operations

**Search:** "pprof visualization"

---

## Part 7: Frontend Monitoring

### Performance Monitoring

**Beyond Sentry, track:**
- Web Vitals (LCP, FID, CLS)
- Bundle size over time
- API call duration
- Component render time

### Web Vitals

### What to Search
- "web vitals"
- "core web vitals measurement"
- "web-vitals npm package"

### What to Figure Out

**1. Install web-vitals**
```bash
npm install web-vitals
```

**2. Measure & Report**
**Search:** "web vitals sentry integration"

```ts
// Your research: How to implement?
import {getCLS, getFID, getFCP, getLCP, getTTFB} from 'web-vitals'

// Send to Sentry or analytics
```

**3. Performance Budget**
**Search:** "vite performance budget"

Set limits in `vite.config.ts`:
- Bundle size < 500KB
- Initial chunk < 200KB

**4. Bundle Analysis**
**Search:** "vite bundle analyzer"

Visualize what's in your bundle:
```bash
npm install rollup-plugin-visualizer -D
```

---

## Part 8: Cost Optimization

### Free Tier Limits

**Sentry Free:**
- 5,000 errors/month
- 10,000 transactions/month

**Strategy:**
- Sample non-critical errors
- Filter out known issues
- Focus on unique errors

**Search:** "sentry sampling"

### Self-Hosted Alternatives

**Search:** "sentry self hosted"

**If you exceed free tier:**
- Self-host Sentry (requires more resources)
- Use only logging + metrics
- Upgrade to paid ($26/month)

---

## Testing Your Monitoring

### Error Tracking Test

**1. Test Sentry Backend:**
```go
// Add test endpoint
r.Get("/debug/sentry", func(w http.ResponseWriter, r *http.Request) {
    sentry.CaptureException(errors.New("Test error from backend"))
    w.Write([]byte("Error sent to Sentry"))
})
```

Visit endpoint → check Sentry dashboard

**2. Test Sentry Frontend:**
```tsx
// Add test button
<button onClick={() => {
  throw new Error("Test error from frontend")
}}>
  Test Sentry
</button>
```

**3. Test Panic Recovery:**
Cause a panic → should be caught and logged

---

### Logging Test

**Check structured logs:**
```bash
# On B860H
journalctl -u stb-server --since "1 hour ago" -o json-pretty
```

**Verify:**
- JSON format
- Correct fields
- Log levels working
- No sensitive data leaked

---

### Metrics Test

**1. Generate traffic:**
```bash
# Send some WOL requests
for i in {1..10}; do
  curl -X POST http://localhost:8080/api/wol \
    -H "Content-Type: application/json" \
    -d '{"mac":"AA:BB:CC:DD:EE:FF"}'
done
```

**2. Check metrics:**
```bash
curl http://localhost:8080/metrics | grep wol
```

**3. Verify counters increased**

---

### Alerting Test

**1. Simulate downtime:**
```bash
# Stop your service
sudo systemctl stop stb-server
```

**2. Wait for alert:**
- UptimeRobot should detect (within 5 min)
- Discord alert should arrive
- Status page should show down

**3. Restart and verify recovery:**
```bash
sudo systemctl start stb-server
```

---

## Monitoring Checklist

### Initial Setup
- [ ] Sentry project created for backend
- [ ] Sentry project created for frontend
- [ ] DSN keys stored in environment variables
- [ ] Error tracking tested (frontend + backend)
- [ ] Structured logging implemented (zerolog)
- [ ] Log levels configured correctly
- [ ] Logs viewable in systemd journal

### Metrics
- [ ] Prometheus metrics defined
- [ ] `/metrics` endpoint exposed
- [ ] Key metrics instrumented (WOL, errors, latency)
- [ ] Metrics dashboard created (Grafana or manual)

### Health & Uptime
- [ ] Enhanced `/health` endpoint implemented
- [ ] Uptime monitoring service configured
- [ ] Alerts configured (Discord + Email)
- [ ] Status page created (optional)

### Production Ready
- [ ] Alerts tested and working
- [ ] Logs not exposing sensitive data
- [ ] Error sampling configured
- [ ] Performance monitoring enabled
- [ ] Documentation updated with monitoring URLs

---

## Understanding Check

Before calling it done:

1. What's the difference between logs, metrics, and traces?
2. When should you use Sentry vs logging?
3. What's a good error sampling rate?
4. What are the 4 Prometheus metric types?
5. How do you check logs on your B860H device?
6. What HTTP status code should `/health` return when unhealthy?
7. What are Web Vitals and why do they matter?
8. How do you avoid alert fatigue?

---

## Resources

### Sentry
- **Sentry Docs:** https://docs.sentry.io/
- **Best Practices:** https://docs.sentry.io/platforms/javascript/best-practices/
- **Go Integration:** https://docs.sentry.io/platforms/go/

### Logging
- **Zerolog:** https://github.com/rs/zerolog
- **Structured Logging:** https://www.honeycomb.io/blog/structured-logging-and-your-team

### Metrics
- **Prometheus:** https://prometheus.io/docs/introduction/overview/
- **Go Client:** https://github.com/prometheus/client_golang
- **Grafana:** https://grafana.com/docs/

### Monitoring
- **UptimeRobot:** https://uptimerobot.com/
- **Google SRE Book:** https://sre.google/sre-book/monitoring-distributed-systems/

---

## What's Next?

### 🔐 Chapter 09: Authentication & Security (Future)
Add user accounts and secure your dashboard:
- JWT authentication
- Rate limiting
- CORS configuration
- Input validation
- Security headers

### 💾 Chapter 10: Database & Persistence (Future)
Store device configurations permanently:
- SQLite setup
- CRUD operations
- Data migrations
- Backup strategy

---

**Remember:** You can't improve what you don't measure. Good monitoring means you find issues before your users do! 📊🔍
