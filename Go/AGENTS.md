# AGENTS.md

You are an AI coding assistant working on a Go (Golang) codebase targeting **Go 1.25+**.

Your job is to:
- Make small, safe, incremental changes.
- Keep the project buildable, well-tested, and idiomatic.
- Follow the conventions in this file before inventing new patterns.

If something is ambiguous, prefer:
1. Reading existing code and tests to infer conventions.
2. Proposing minimally invasive changes.
3. Asking clarifying questions *in comments* rather than guessing.

---

## Tech stack & conventions

- Language: **Go 1.25+**
- Dependency management: **Go modules** (`go.mod`, `go.sum`)
- Build tool: `go build`, `go test`; use `Makefile` targets when they exist
- Linting: Prefer `golangci-lint` + `go vet`
- Formatting: `gofmt` and/or `gofumpt`
- Testing: Standard `testing` package, plus testing helpers in this repo (if any)
- Concurrency: Prefer `context.Context`, `errgroup`, channels, and sync primitives over ad-hoc goroutines

### Code style (Go-specific)

- Always run `gofmt` (and keep imports organized).
- Prefer **small, focused functions** over large ones.
- Use **clear error handling**:
  - Wrap errors with context via `fmt.Errorf("...: %w", err)` where helpful.
  - Return early on error; avoid deep nesting.
- Types and naming:
  - Exported names: `CamelCase`, unexported: `camelCase`.
  - Avoid stutter: if package is `user`, type should be `Service`, not `UserService`.
- Concurrency:
  - Always pass `context.Context` down call stacks that perform I/O or long-running ops.
  - Avoid leaking goroutines; ensure they exit on context cancellation or channel close.
  - When in doubt, use `errgroup`.
- Generics:
  - Use generics when they clearly remove duplication and stay readable.
  - Don’t introduce complex type parameters for trivial abstractions.
- Logging:
  - Use the project’s existing logging library.
  - Don’t log secrets, credentials, or personal data.

---

## Dev environment & setup

1. Use the `Makefile` if it exists (e.g. `make dev`, `make test`, `make lint`).
2. Otherwise, use standard Go commands.

```bash
go mod tidy
go mod download
go build ./...
go run ./cmd/app
go test ./...
go test -race ./...
golangci-lint run ./...


---

Project structure guidelines

Follow existing patterns first. If creating structure:

cmd/<app-name>/main.go – application entrypoints

internal/<domain> – non-exported packages internal to the project

pkg/<name> – reusable packages intended for external use

internal/http, internal/db, internal/service – common layers

internal/config – configuration loading and validation


Rules:

Don’t create new top-level modules unless necessary.

Don’t rename existing modules or change the module path.

Keep packages focused on one responsibility.

Prefer standard library before adding dependencies.

Avoid adding overlapping external libraries.



---

Testing instructions

Use table-driven tests where appropriate.

Put tests in *_test.go files in the same package.

Prefer subtests (t.Run(...)) for multiple scenarios.

Keep tests deterministic and independent.


go test ./...
go test ./path/to/pkg -run TestName
go test -race -cover ./...


---

How to interact with this repo

1. Read before writing


2. Prefer minimal diffs


3. Backwards compatibility & safety


4. Performance & memory: avoid unnecessary allocations; use streaming; manage goroutines carefully.




---

Commit & PR guidelines

Commit message format:

<scope>: <short description>

<optional longer explanation / rationale>

Examples:

api: add pagination to ListUsers

db: fix transaction handling in CreateOrder

config: support ENV overrides for timeouts


Ensure:

go build ./... passes

go test ./... passes

golangci-lint run clean


PR descriptions should state:

What changed

Why it changed

How it was tested

Any rollout notes



---

Security, reliability, and secrets

Never hard-code secrets or credentials.

Use environment variables or existing secret-management solutions.

Don’t log sensitive data.


Reliability:

Propagate context.Context in blocking/I/O functions.

Use timeouts where appropriate.

Ensure goroutines terminate on context cancellation.



---

When you’re unsure

If instructions conflict with existing code, prefer established patterns unless harmful. If a change is significant, propose small refactors with justification. If ambiguous, make the smallest reasonable change and leave a comment explaining alternatives.


---

Quick reference

Language: Go 1.25+

Build: go build ./... or make build

Test: go test ./... or make test

Lint: golangci-lint run or make lint

Format: gofmt / gofumpt

Do:

Preserve behavior and tests.

Follow existing patterns.

Keep changes small and focused.


Don’t:

Introduce major deps casually.

Break exported APIs without reason.

Ignore context or errors.
