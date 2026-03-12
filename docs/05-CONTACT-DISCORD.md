# Chapter 05: Contact Form & Discord Integration

**Goal:** Send contact form messages to Discord  
**Time:** 3-4 hours  
**You'll Ship:** A working contact form that reaches you

---

## What You're Building

A contact form that:
- Accepts name, email, message
- Validates input
- Sends to Discord webhook
- Shows success/error feedback
- Prevents spam (basic rate limiting)

**Why Discord?**
- No email server setup
- Free webhooks
- Instant notifications on your phone
- Easy to manage in your server

---

## Step 1: Create Discord Webhook

### What to Search
- "discord webhook create"
- "discord webhook url format"
- "discord webhook testing"

### Documentation Pointers
- **Discord Webhooks:** https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks

### What to Figure Out
1. Go to your Discord server
2. Server Settings → Integrations → Webhooks
3. Create New Webhook
4. Copy webhook URL (looks like: `https://discord.com/api/webhooks/...`)

### Important
**DON'T hardcode webhook URL in frontend!**
- Frontend code is visible to users
- Store URL only in backend
- **Search:** "environment variables golang"

### Webhook URL Storage
Create `.env` file in project root:
```
DISCORD_WEBHOOK_URL=https://discord.com/api/webhooks/YOUR_WEBHOOK_HERE
```

**Add to `.gitignore`:**
```
.env
```

---

## Step 2: Build Contact Form Component

### What to Search
- "react form handling"
- "react controlled components"
- "react form validation"

### Form Fields
1. **Name:** text input (required) - Icon: `User`
2. **Email:** email input (required, validated) - Icon: `Mail`
3. **Message:** textarea (required, min 10 chars) - Icon: `MessageSquare`
4. **Submit:** button with loading state - Icon: `Send` (idle) / `Loader2` (loading)

### What to Figure Out

**If using Shadcn:**
```bash
npx shadcn-ui@latest add form
npx shadcn-ui@latest add input
npx shadcn-ui@latest add textarea
```
**Read:** https://ui.shadcn.com/docs/components/form

**If building from scratch:**
- **Search:** "react form component tutorial"
- **Search:** "tailwind form styling"

### Adding Icons to Form Fields
**Search:** "form input icon inside tailwind"

**Lucide Icons for Form:**
- `User` - Name field
- `Mail` - Email field
- `MessageSquare` - Message textarea
- `Send` - Submit button (default)
- `Loader2` - Submit button (loading) with `animate-spin`
- `Check` - Success state
- `AlertCircle` - Error state

**What to figure out:**
- How to position icon inside input?
- How to add padding so text doesn't overlap icon?
- Should icon be clickable?

### State Management
**Search:** "react form state management"

**What to track:**
- Input values (name, email, message)
- Validation errors
- Submission state (idle, loading, success, error)
- Icon states (changes based on submission state)

---

## Step 3: Client-Side Validation

### What to Search
- "react form validation"
- "javascript email regex"
- "react form error messages"

### Validation Rules
1. **Name:**
   - Required
   - Min 2 characters
   - Max 50 characters

2. **Email:**
   - Required
   - Valid email format
   - **Search:** "javascript email validation regex"

3. **Message:**
   - Required
   - Min 10 characters
   - Max 1000 characters

### What to Figure Out
1. When to validate? (onChange vs onSubmit)
2. How to show error messages?
3. How to disable submit if invalid?

### Documentation Pointers
- **HTML5 Validation:** https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation
- **React Forms:** https://react.dev/learn/sharing-state-between-components

### UX Considerations
**Search:** "form validation ux best practices"
- Show errors after user leaves field (onBlur)?
- Show errors only on submit attempt?
- Inline vs summary errors?

---

## Step 4: Backend API Endpoint

### What to Search
- "golang discord webhook"
- "golang post json to webhook"
- "golang environment variables"

### Create Backend Endpoint
**File:** `internal/api/handlers/contact.go`

**Endpoint:** `POST /api/contact`

### What to Figure Out

**1. Read Environment Variable**
**Search:** "golang os.Getenv"

**2. Parse Request Body**
```go
// Your research: How to implement?
type ContactRequest struct {
    Name    string `json:"name"`
    Email   string `json:"email"`
    Message string `json:"message"`
}
```

**3. Validate Input**
**Search:** "golang validate email"
- Check required fields
- Validate email format
- Check min/max lengths

**4. Format Discord Message**
**Search:** "discord webhook message format"

**Discord Webhook Payload:**
```json
{
  "content": "New contact form submission",
  "embeds": [{
    "title": "Contact Form",
    "fields": [
      {"name": "Name", "value": "John Doe"},
      {"name": "Email", "value": "john@example.com"},
      {"name": "Message", "value": "Hello..."}
    ],
    "color": 3447003,
    "timestamp": "2024-01-01T12:00:00Z"
  }]
}
```

**5. Send HTTP POST to Discord**
**Search:** "golang http post json"

### Documentation Pointers
- **Discord Webhook API:** https://discord.com/developers/docs/resources/webhook#execute-webhook
- **Go net/http:** https://pkg.go.dev/net/http

---

## Step 5: Rate Limiting (Anti-Spam)

### What to Search
- "golang rate limiting"
- "chi rate limit middleware"
- "golang rate limiter simple"

### Why Rate Limit?
- Prevent spam bots
- Protect Discord webhook
- Prevent abuse

### Options

**Option 1: Chi Rate Limit Middleware**
**Search:** "github.com/go-chi/httprate"

**Option 2: Simple In-Memory Counter**
**Search:** "golang map rate limit ip"

**Option 3: Per-IP Cooldown**
Store last submission time per IP address

### What to Figure Out
1. How many requests per IP per time period?
   - Suggestion: 3 requests per hour
2. How to get client IP address?
   - **Search:** "golang get client ip address"
3. How to store rate limit data?
   - In-memory map (simple, resets on restart)
   - Redis (persistent, for production)

### For MVP: Simple In-Memory
```go
// Your research: How to implement?
// - Map of IP → last submission time
// - Check if < 1 hour since last submit
// - Return 429 if too soon
```

---

## Step 6: Connect Frontend to Backend

### What to Search
- "react form submit handler"
- "fetch post form data"
- "react form success message"

### Implementation Flow
1. User fills form → clicks submit
2. Validate on client side
3. If valid, disable form & show loading
4. POST to `/api/contact`
5. Handle response:
   - **200:** Show success message, clear form
   - **400:** Show validation errors
   - **429:** Show "Please wait before submitting again"
   - **500:** Show generic error

### What to Figure Out
1. How to prevent double submission?
2. How to clear form after success?
3. How to show success message?
4. Should form be in a modal or separate page?

### Success State Options
**Option A:** Inline success message in form  
**Option B:** Toast notification  
**Option C:** Redirect to success page

**Search:** "react form success patterns"

---

## Step 7: Loading & Error States

### What to Search
- "react form loading state"
- "button disabled during submit"
- "form error display patterns"

### States to Handle

**1. Idle State**
- Form enabled
- Submit button: "Send Message"

**2. Loading State**
- Form disabled (entire fieldset)
- Submit button: "Sending..." with spinner
- Can't submit twice

**3. Success State**
- Show `Check` icon with green color
- Success message: "Message sent! I'll reply soon."
- Clear form after 3 seconds
- **Search:** "lucide check icon animation"

**4. Error States**
- Network error: "Failed to send. Check connection." - `WifiOff` icon
- Rate limit: "Please wait X minutes before sending again." - `Clock` icon
- Validation error: Show specific field errors - `AlertCircle` icon

### What to Figure Out
1. How to disable all inputs during loading?
2. How to re-enable after response?
3. How to handle timeout (request takes too long)?
4. How to swap button icon based on state?

**Button Icon States:**
```tsx
// Your research: How to implement?
// Idle: <Send /> 
// Loading: <Loader2 className="animate-spin" />
// Success: <Check />
// Error: <AlertCircle />
```

---

## Step 8: Testing Your Implementation

### Manual Testing

**Test Valid Submission:**
```bash
# Start server
go run cmd/server/main.go

# Submit form in browser
# Check Discord for message
```

**Test Validation:**
- Empty fields → should show errors
- Invalid email → should show error
- Too short message → should show error

**Test Rate Limiting:**
- Submit form 3 times quickly
- 4th attempt should be blocked
- Wait 1 hour, try again → should work

### Curl Testing (Backend)
```bash
curl -X POST http://localhost:8080/api/contact \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test User",
    "email": "test@example.com",
    "message": "This is a test message"
  }'
```

Check Discord channel for message.

---

## Security Considerations

### What to Search
- "webhook security best practices"
- "golang xss prevention"
- "input sanitization"

### What to Implement

**1. Sanitize Input**
**Search:** "golang html escape"
- Remove HTML tags from input
- Escape special characters
- Prevent XSS attacks

**2. Validate on Backend**
**Never trust frontend validation alone**
- Re-validate all fields
- Check data types
- Enforce length limits

**3. Protect Webhook URL**
- Never expose in frontend
- Store in environment variable
- Add to `.gitignore`

**4. Rate Limiting**
- Already implemented above
- Consider CAPTCHA for public sites
  - **Search:** "hcaptcha react"

**5. HTTPS Only (Production)**
- Use reverse proxy (Caddy)
- Force HTTPS for form submission

---

## Common Issues & What to Google

### Discord webhook returns 400
**Search:** "discord webhook 400 bad request"  
**Check:** JSON payload format, field types

### Rate limit not working
**Search:** "golang get real ip behind proxy"  
**Issue:** Might be getting proxy IP, not client IP

### Form submits but doesn't reach Discord
**Search:** "golang http post debug"  
**Debug:** Log request/response, check webhook URL

### Success state doesn't show
**Search:** "react state not updating after fetch"  
**Fix:** Ensure `setState` called after response

---

## Code Organization

```
Backend:
internal/
├── api/
│   └── handlers/
│       ├── contact.go      # Contact form handler
│       └── wol.go         # WOL handler
├── discord/
│   └── webhook.go         # Discord webhook client
└── middleware/
    └── ratelimit.go       # Rate limiting

Frontend:
src/
├── components/
│   ├── ContactForm.tsx    # Form component
│   └── FormField.tsx      # Reusable input field
└── lib/
    └── api.ts            # API calls
```

---

## Bonus Features (If Time)

### 1. Email Validation API
**Search:** "email validation api free"  
Verify email actually exists (disposable email check)

### 2. Message Templates
Pre-fill message for common requests

### 3. File Attachments
**Search:** "react file upload"  
**Search:** "discord webhook file upload"

### 4. Auto-Reply
Respond in Discord thread directly

---

## Understanding Check

Before moving to Chapter 06 (Deployment):

1. Why store webhook URL in backend?
2. What's the difference between client and server validation?
3. Why rate limit by IP address?
4. How does Discord webhook authentication work?
5. What's the difference between POST and GET requests?
6. How do you prevent XSS attacks?

---

## Checkpoint: MVP Complete! 🎉

You now have:
1. ✅ Go backend with Wake-on-LAN API
2. ✅ React frontend with device dashboard
3. ✅ Contact form → Discord integration
4. ✅ Basic security & rate limiting
5. ✅ Responsive UI with loading states

**This is shippable.** You can use it on your local network.

---

## Next: Chapter 06 (Deployment)

You'll learn:
- Building for production
- Cross-compiling for ARM (B860H V5)
- systemd service setup
- Reverse proxy with Caddy
- HTTPS with Let's Encrypt
- Monitoring & logs

**Goal:** Run your app 24/7 on your set-top box.
