# Chapter 02: Wake-on-LAN Implementation

**Goal:** Send magic packets to wake up devices  
**Time:** 2-4 hours  
**You'll Ship:** A working WOL endpoint

---

## What You're Building

An endpoint that accepts a MAC address and wakes up the device:
- `POST /api/wol` with body `{"mac": "AA:BB:CC:DD:EE:FF"}`
- Returns success/error response
- Actually sends UDP magic packet on your network

---

## Understanding Wake-on-LAN First

### What to Search
- "wake on lan protocol explained"
- "magic packet structure"
- "wol udp port 9"

### What to Understand
1. **Magic Packet Structure:**
   - 6 bytes of `FF FF FF FF FF FF`
   - Followed by target MAC address repeated 16 times
   - Total: 102 bytes

2. **Network Requirements:**
   - Device must support WOL (BIOS setting)
   - Device must be on same local network
   - UDP packet sent to broadcast address
   - Default port: 7 or 9

3. **Limitations:**
   - Won't work across routers (usually)
   - Device must be connected to power
   - Some network cards require power supply even when PC is "off"

### Questions to Answer
- Why UDP instead of TCP?
- What's a broadcast address?
- Why does the MAC repeat 16 times?

---

## Step 1: Research Existing Go Library

### What to Search
- "golang wake on lan library"
- "github mdlayher wol"
- "go wol package"

### Documentation Pointers
- **mdlayher/wol:** https://github.com/mdlayher/wol
- **pkg.go.dev:** https://pkg.go.dev/github.com/mdlayher/wol

### What to Figure Out
1. How to install the package
2. What functions it provides
3. How to use `wol.Client` or similar
4. What parameters the wake function needs

### Challenge
Read the library's README and examples. Can you:
- Identify the main function to call?
- Find example code snippets?
- Understand what imports you need?

---

## Step 2: Validate MAC Address Input

### What to Search
- "golang mac address validation"
- "net hardware addr parse"
- "golang net package mac"

### Documentation Pointers
- **net package:** https://pkg.go.dev/net
- **Look for:** `ParseMAC()` function

### What to Figure Out
1. How to parse string like "AA:BB:CC:DD:EE:FF"
2. What format variations are valid (AA-BB-CC, AABBCC, etc.)
3. How to return error if invalid MAC
4. What Go type represents a MAC address

### Common MAC Formats
- `AA:BB:CC:DD:EE:FF` (colon-separated)
- `AA-BB-CC-DD-EE-FF` (dash-separated)
- `AABBCCDDEEFF` (no separator)

Figure out which the library function accepts.

---

## Step 3: Create WOL Service

### What to Search
- "golang service layer pattern"
- "golang internal package structure"
- "dependency injection golang"

### Project Structure to Create
```
internal/
├── wol/
│   └── service.go     # Your WOL business logic
└── api/
    └── handlers/
        └── wol.go     # HTTP handler
```

### What to Figure Out
1. **Service Layer:**
   - Create a `type Service struct` in `internal/wol/service.go`
   - What methods should it have? (hint: `Wake(macAddress string) error`)
   - Where do you initialize the wol client?

2. **Handler Layer:**
   - Create HTTP handler in `internal/api/handlers/wol.go`
   - How to read JSON request body?
   - How to call the service?
   - How to return JSON response?

### Documentation Pointers
- **JSON Decoding:** https://gobyexample.com/json
- **Search:** "golang json decode http request body"
- **Search:** "golang error handling best practices"

---

## Step 4: Wire It All Together

### What to Search
- "golang dependency injection"
- "chi router with handler dependencies"
- "golang struct method receiver"

### What to Figure Out
1. How to pass the WOL service to your HTTP handler
2. How to register the route in `main.go`
3. How to handle errors gracefully

### Architecture Pattern
```
main.go
  ├── Creates WOL service
  ├── Creates HTTP handlers (passing service)
  └── Registers routes

handler
  ├── Receives HTTP request
  ├── Validates input
  ├── Calls service
  └── Returns response

service
  ├── Business logic
  ├── Calls WOL library
  └── Returns error or success
```

---

## Step 5: Test Your Implementation

### Manual Testing

```bash
# Start server
go run cmd/server/main.go

# Send WOL request
curl -X POST http://localhost:8080/api/wol \
  -H "Content-Type: application/json" \
  -d '{"mac": "AA:BB:CC:DD:EE:FF"}'
```

### Test Checklist
- [ ] Valid MAC address wakes device
- [ ] Invalid MAC returns 400 error
- [ ] Missing MAC returns 400 error
- [ ] Empty body returns 400 error
- [ ] Success returns 200 with message
- [ ] Network error returns 500

### How to Test Without Actual Device
**Search:** "wireshark capture udp packets"
- Use Wireshark to verify UDP packet is sent
- Look for broadcast to port 9
- Verify packet contains magic pattern

---

## Request/Response Contract

### Request
```json
POST /api/wol
Content-Type: application/json

{
  "mac": "AA:BB:CC:DD:EE:FF"
}
```

### Success Response (200)
```json
{
  "success": true,
  "message": "Wake packet sent to AA:BB:CC:DD:EE:FF"
}
```

### Error Response (400)
```json
{
  "success": false,
  "error": "Invalid MAC address format"
}
```

### Error Response (500)
```json
{
  "success": false,
  "error": "Failed to send wake packet: <reason>"
}
```

---

## Common Issues & What to Google

### Error: "permission denied" when sending packet
**Search:** "golang udp socket permission denied linux"  
**Reason:** Need root on some systems, or use port > 1024

### Packet sent but device doesn't wake
**Search:** "wake on lan not working troubleshooting"  
**Checklist:**
- Device WOL enabled in BIOS?
- Network card supports WOL?
- Device on same network?
- Using correct MAC address?

### Can't parse MAC address
**Search:** "golang net parsemac examples"  
**Check:** MAC format matches expected pattern

---

## Code Organization Principles

### What to Search
- "golang clean architecture"
- "golang project layout"
- "separation of concerns golang"

### Questions to Answer
1. Why separate handlers from services?
2. What should `internal/` contain vs `pkg/`?
3. When to create a new package?

### Rule of Thumb
- **Handlers:** HTTP-specific logic (read request, write response)
- **Services:** Business logic (validate, process, external calls)
- **Main:** Wiring everything together

---

## Bonus Challenge

Once basic WOL works, try:

1. **Add multiple MACs:**
   - Accept array of MAC addresses
   - Wake all at once
   - **Search:** "golang for loop slice"

2. **Add device names:**
   - Store MAC → Name mapping (in-memory for now)
   - Accept name instead of MAC
   - **Search:** "golang map data structure"

3. **Add rate limiting:**
   - Prevent spam requests
   - **Search:** "chi rate limit middleware"

---

## Understanding Check

Before moving to Chapter 03, answer:

1. What's the structure of a magic packet?
2. Why does WOL use UDP instead of TCP?
3. What does `internal/` directory signify in Go?
4. How does the handler get access to the service?
5. What HTTP status codes should you return for different errors?
6. Where should MAC validation happen: handler or service?

---

## Next: Chapter 03

You'll build the React frontend with:
- WOL button component
- MAC address input
- Success/error feedback

**Remember:** You have a working API. Test it thoroughly before moving on.
