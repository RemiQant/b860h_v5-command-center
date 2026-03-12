# Chapter 01: Backend Setup & First Endpoint

**Goal:** Get a basic Go server running with Chi router  
**Time:** 2-3 hours  
**You'll Ship:** A working health check endpoint

---

## What You're Building

A Go HTTP server that responds to:
- `GET /health` → Returns `{"status": "ok"}`

Simple. But you'll learn the foundation of all web backends.

---

## Step 1: Initialize Go Project

### What to Search
- "go mod init tutorial"
- "go project structure best practices"
- "golang cmd internal directory pattern"

### Documentation Pointers
- **Go Modules:** https://go.dev/doc/modules/managing-dependencies
- **Read:** "Creating a module", "Adding dependencies"

### What to Figure Out
1. How to run `go mod init` with your module name
2. What `go.mod` file does
3. Where to create `cmd/server/main.go`

### Expected Result
```
b860h_v5-command-center/
├── go.mod
└── cmd/
    └── server/
        └── main.go
```

---

## Step 2: Install Chi Router

### What to Search
- "go-chi router installation"
- "chi router example"
- "golang http router comparison"

### Documentation Pointers
- **Chi GitHub:** https://github.com/go-chi/chi
- **Read:** README, look at the basic example
- **Go Packages:** https://pkg.go.dev/github.com/go-chi/chi/v5

### What to Figure Out
1. How to add Chi to your project (hint: `go get`)
2. How to import it in your code
3. What `chi.NewRouter()` returns

### Questions to Answer Yourself
- Why use Chi instead of standard `http.ServeMux`?
- What's the difference between `chi.Router` and `http.Handler`?

---

## Step 3: Create Your First Route

### What to Search
- "chi router get handler"
- "golang http handler function"
- "net/http responsewriter example"

### Documentation Pointers
- **Chi Router Methods:** Look at Chi README for `r.Get()` examples
- **net/http pkg:** https://pkg.go.dev/net/http
- **Read:** `ResponseWriter` interface, `Request` struct

### What to Figure Out
1. How to define a route with `r.Get("/health", handler)`
2. How to write a handler function signature
3. How to:
   - Set response status code
   - Set Content-Type header
   - Write JSON response body

### Challenge
Make the `/health` endpoint return:
```json
{
  "status": "ok",
  "timestamp": 1234567890
}
```

Hints to search:
- "golang time unix timestamp"
- "golang write json response"
- "json marshal golang"

---

## Step 4: Start the Server

### What to Search
- "golang http listenandserve"
- "golang http server lifecycle"
- "graceful shutdown golang http server"

### Documentation Pointers
- **net/http:** https://pkg.go.dev/net/http#ListenAndServe
- **Read:** How to start a server on a specific port

### What to Figure Out
1. How to use `http.ListenAndServe()`
2. Why we pass the Chi router to it
3. What port to use (suggestion: 8080)

### Expected Behavior
```bash
# Terminal 1
go run cmd/server/main.go
# Output: Server running on :8080 (or similar)

# Terminal 2
curl http://localhost:8080/health
# Output: {"status":"ok","timestamp":1710123456}
```

---

## Step 5: Project Structure

Before moving on, make sure your structure looks like:

```
b860h_v5-command-center/
├── go.mod
├── go.sum               # Auto-generated after go get
└── cmd/
    └── server/
        └── main.go      # All code here for now (≈30 lines)
```

Later you'll split into `internal/` packages, but for learning, keep it simple first.

---

## Testing Checklist

- [ ] Server starts without errors
- [ ] `GET /health` returns 200 status
- [ ] Response is valid JSON
- [ ] Can stop server with Ctrl+C
- [ ] Can change port by modifying code

---

## Common Errors & What to Google

### Error: "cannot find package"
**Search:** "go mod tidy command"  
**Reason:** Dependencies not downloaded

### Error: "address already in use"
**Search:** "golang address already in use"  
**Solution:** Port 8080 is taken, change port or kill process

### Error: "method X is not defined"
**Search:** "chi router methods"  
**Reason:** Check Chi version, might need `v5` import

---

## Questions to Test Your Understanding

Before moving to Chapter 02, make sure you can answer:

1. What does `go.mod` do?
2. Why do we use `cmd/server/` directory?
3. What interface does Chi router implement?
4. What are the two parameters of `ListenAndServe()`?
5. How does `ResponseWriter` differ from a normal file write?
6. Why use Chi instead of standard lib router?

If you can't answer these, **re-read the docs**.

---

## Next: Chapter 02

Once your health endpoint works, move to Chapter 02 where you'll:
- Add CORS middleware (search: "chi cors middleware")
- Add request logging (search: "chi logger middleware")
- Structure your code into packages

---

**Remember:** I didn't give you code. You figured it out. That's how you learn.
