# Auth Service — Load Testing with Artillery.io

> Load-test scenarios for an authentication and session-management API, written with Artillery.io.

Built for **SWEN3165 — Software Quality Assurance & Testing** at the University of the West Indies, Mona.

## Overview

A test suite that load-tests a Node.js authentication API using [Artillery.io](https://artillery.io). The project demonstrates:

- Realistic multi-step request scenarios (authenticate → capture token → keep-alive)
- Response value capture and chaining between requests
- Coverage of both happy-path (200 OK) and failure-path (401 Unauthorized) responses
- Performance assessment under simulated concurrent load

The auth service itself is included in the `auth/` folder as the System Under Test.

## Tech Stack

- **Language:** JavaScript (Node.js)
- **Load Testing:** Artillery.io
- **Security Scanning:** Snyk
- **Build:** Babel

## Project Structure

```
├── auth/                   # The auth API (System Under Test)
├── artillery-load-tests/   # Artillery load test scenarios
├── testdoc.pdf             # Assessment brief
├── .snyk                   # Snyk security policy
├── .babelrc                # Babel config
└── package.json
```

## The Auth Service Under Test

The included service exposes two endpoints:

### `GET /authenticate`

Authenticates a user and returns a session token. Matching `username` and `password` return 200 OK with a token; mismatched credentials return 401.

```bash
# Success
curl -v "http://localhost:9876/authenticate?username=smith&password=smith"
# → 200 OK { "sessionToken": "session_4137", "userId": "user_smith" }

# Failure
curl -v "http://localhost:9876/authenticate?username=john&password=smith"
# → 401 Unauthorized
```

### `GET /session/keep-alive`

Validates that a session token is still active. Returns 200 OK with the token if found, 401 if not.

```bash
curl -v "http://localhost:9876/session/keep-alive?sessionToken=session_4137"
# → 200 OK { "sessionToken": "session_4137" }
```

## Setup

```bash
npm install
```

## Running

**Step 1 — Start the auth service** (in one terminal):
```bash
npm start
```
The service listens on `http://localhost:9876`.

**Step 2 — Run the load tests** (in a separate terminal):
```bash
cd artillery-load-tests
artillery run <scenario-file>.yml
```

Artillery will print a real-time summary including request rate, response times (median, p95, p99), and status-code distribution.

## Testing Concepts Demonstrated

- Multi-step scenario design with request chaining
- Response value capture (`capture` directive) — extracting `sessionToken` from one response and feeding it into the next
- Validating both 2xx and 4xx response paths under load
- Throughput, latency, and error-rate measurement under sustained traffic
- Performance testing as the third tier of the test pyramid (alongside unit and end-to-end testing)

## References

- [Artillery.io Documentation](https://artillery.io/docs/)
- [Artillery HTTP Engine — `capture` directive](https://www.artillery.io/docs/reference/engines/http)

Built by [Josiah-John Green](https://github.com/j0hnc0d3s) — Software Engineering, UWI Mona '26.
