# Language Adaptation Guide

> The SDD **process** (specs, phases, tickets, PRs, retros, rules, skills) is fully language-agnostic. What varies by language is the *code examples* inside ~14 of the 73 kit files. This guide tells you exactly what to change when you drop the kit into a project using Python, Go, Java, Kotlin, Rust, C#, or any other language.

---

## The 80/20 rule of portability

**Untouched across languages (59 of 73 files):**

- Everything in `docs/` (except this file)
- All 11 process rules in `.cursor/rules/process/`
- 8 of 10 skills (`epic-implementation-planner`, `phase-card-generator`, `ticket-prd-builder`, `layered-pr-planner`, `pr-readiness-check`, `phase-report-writer`, `prd-sync`, `agent-learning-loop`)
- Most templates (`prd.md`, `technical-solution.md`, `implementation-phases.md`, `milestone-implementation-plan.md`, `phase-report.md`, `adr.md`, `traceability-matrix.md`, `raci-roles.md`, both AGENTS.md)
- All 8 prompts
- All 4 quickstart checklists
- `.cursor/BUGBOT.md.template`, `.cursor/LEARNINGS.md.template`

**Adapt or drop per your stack (14 files):**

| # | File | Nature of the language coupling | Adaptation strategy |
|---|---|---|---|
| 1 | `.cursor/rules/engineering/no-inline-jsx-handlers.mdc` | React/JSX-specific | **Drop** if not React |
| 2 | `.cursor/rules/engineering/types-in-dedicated-files.mdc` | TypeScript-specific (`.ts` / `.tsx` glob) | **Drop or adapt** — see per-language section below |
| 3 | `.cursor/rules/engineering/component-decomposition.mdc` | React examples | **Rewrite examples** for your UI framework; concept unchanged |
| 4 | `.cursor/rules/engineering/runtime-input-validation.mdc` | Zod examples | **Rewrite examples** with your validation library |
| 5 | `.cursor/rules/engineering/typed-error-classes.mdc` | TS class examples | **Rewrite examples** with your language's exception idiom |
| 6 | `.cursor/rules/engineering/surface-all-errors.mdc` | TanStack Query examples | **Rewrite for your UI stack** or drop if backend-only |
| 7 | `.cursor/rules/engineering/handler-thin-services-thick.mdc` | Node/TS handler examples | **Rewrite examples** for your HTTP framework |
| 8 | `.cursor/rules/engineering/package-dependency-hygiene.mdc` | npm-centric | Already has multi-language section — light rework |
| 9 | `.cursor/skills/tdd-bdd-workflow/SKILL.md` | Vitest + Testing Library examples | **Rewrite Layer-Specific Patterns** section |
| 10 | `.cursor/skills/lld-creation/SKILL.md` | Frontend + backend HTTP boundary | **Keep as-is** for full-stack web; **drop or restructure** for pure backend / CLI / data pipeline |
| 11 | `templates/ui-strategy.md` | React + design system | **Drop** if not building UI; keep if any UI |
| 12 | `templates/feature-phase.md` | TS/React file paths in example | **Rewrite the "Layer Implementation" section** for your stack |
| 13 | `templates/lld.md` | TS file paths in example | **Rewrite the file table** for your stack |
| 14 | All 4 `examples/*.md` | Alpha (TS/React) sanitized examples | **Reference-only** — do not adapt; author your own examples as you build |

---

## Adaptation by language

For each language, this section covers the 8 code-example files (rules + tdd-bdd skill). The other language-dependent files (feature-phase, lld templates, ui-strategy) either apply directly to full-stack web work regardless of language, or should be dropped.

### TypeScript + React (as-shipped)

**No changes needed.** The kit ships with TS/React examples throughout. This is the reference stack.

Optional additions specific to TS/React that you'd re-author per ClientName project:

- `tanstack-query-adoption.mdc`, `react-hook-form-patterns.mdc`, `use-pds-components.mdc` (ClientName's design system), `cors-origin-security.mdc`, `no-hardcoded-tokens.mdc`, `lambda-cold-start-safety.mdc`, `dynamo-table-safety.mdc`, `dynamo-scan-pagination.mdc`, `api-verb-semantics.mdc`, `css-cascade-overrides.mdc` — these were dropped from the portable kit but are excellent for their stack.

---

### TypeScript backend-only (Node + Express / Fastify / Nest)

- **Drop:** `no-inline-jsx-handlers.mdc`, `component-decomposition.mdc`, `surface-all-errors.mdc`, `ui-strategy.md` template.
- **Keep:** `types-in-dedicated-files.mdc` (rewrite the glob from `.tsx` to `.ts`).
- **Handler-thin-services-thick:** already TS-flavored — adjust framework references (Express `req/res` vs Lambda `event`).
- **TDD skill:** drop the "Feature — React Components and Hooks" layer section.
- **Add:** consider authoring `express-error-middleware.mdc`, `fastify-schema-validation.mdc`, or `nestjs-module-boundaries.mdc` per your framework.

---

### Python (FastAPI / Django / Flask)

**Files to drop:** `no-inline-jsx-handlers.mdc`, `component-decomposition.mdc`, `types-in-dedicated-files.mdc`, `surface-all-errors.mdc`, `ui-strategy.md` template.

**Files to rewrite examples in:**

#### `runtime-input-validation.mdc`

Replace Zod examples with **Pydantic**:

```python
from pydantic import BaseModel, Field, ValidationError

class CreateEntity(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    status: Literal["ACTIVE", "INACTIVE"]

@app.post("/entities")
def create_entity(body: CreateEntity):  # FastAPI validates automatically
    return service.create(body)

# For non-FastAPI (Django, Flask):
try:
    parsed = CreateEntity.model_validate(request.json)
except ValidationError as e:
    return {"errors": e.errors()}, 400
```

For Django, use **DRF serializers** or **django-ninja** (Pydantic under the hood).

#### `typed-error-classes.mdc`

Python uses exception classes natively:

```python
class ValidationError(Exception):
    pass

class NotFoundError(Exception):
    def __init__(self, resource: str, id: str):
        super().__init__(f"{resource} with id {id} not found")
        self.resource = resource
        self.id = id

class ForbiddenError(Exception):
    pass

# Centralized mapper (FastAPI)
@app.exception_handler(ValidationError)
def _validation(request, exc):
    return JSONResponse(status_code=400, content={"message": str(exc)})

@app.exception_handler(NotFoundError)
def _not_found(request, exc):
    return JSONResponse(status_code=404, content={"message": str(exc)})
```

Rule: classify errors by `isinstance(exc, SomeError)` — Python's `instanceof` equivalent.

#### `handler-thin-services-thick.mdc`

FastAPI example:

```python
# BAD — logic in route
@app.put("/entities/{entity_id}")
async def update(entity_id: str, body: UpdateEntity):
    existing = await repo.find(entity_id)
    if existing.status == "ACTIVE":
        # 20 lines of logic...
        return await repo.save(existing)

# GOOD — thin route
@app.put("/entities/{entity_id}")
async def update(entity_id: str, body: UpdateEntity, service: EntityService = Depends()):
    return await service.update(entity_id, body)
```

Django views / Flask blueprints follow the same pattern — route function is thin, business logic in a `service.py` module.

#### `tdd-bdd-workflow/SKILL.md`

Rewrite the "Layer-Specific Patterns" section:

- **Framework:** pytest
- **File naming:** `test_<name>.py` co-located with source (or in `tests/` mirror tree)
- **Test discovery:** pytest auto-discovers `test_*.py`
- **Fixtures:** `pytest.fixture` for setup
- **Mocking:** `pytest-mock` (`mocker.patch`)
- **HTTP tests:** FastAPI `TestClient`, Django `Client`, Flask `test_client`
- **BDD:** use `describe` blocks via `pytest-describe` if desired, or nested classes:

```python
class TestCreateEntity:
    class WhenSubmittedWithValidData:
        def test_returns_created_entity(self, service):
            result = service.create(name="foo")
            assert result.status == "ACTIVE"

        def test_persists_to_repository(self, service, mock_repo):
            service.create(name="foo")
            mock_repo.save.assert_called_once()
```

**Package hygiene:** `pyproject.toml` split — `[project.dependencies]` (runtime) vs `[project.optional-dependencies].dev` (test/lint tools). Pin with `~=` or exact versions.

**Common frameworks to reference in AGENTS.md:**
- FastAPI + Pydantic + SQLAlchemy + Alembic
- Django + DRF + django-ninja
- Flask + Marshmallow + Flask-SQLAlchemy

---

### Go

**Files to drop:** `no-inline-jsx-handlers.mdc`, `component-decomposition.mdc`, `types-in-dedicated-files.mdc`, `surface-all-errors.mdc`, `ui-strategy.md` template.

**Files to rewrite examples in:**

#### `runtime-input-validation.mdc`

Use `go-playground/validator`:

```go
type CreateEntity struct {
    Name   string `json:"name" validate:"required,min=1,max=100"`
    Status string `json:"status" validate:"required,oneof=ACTIVE INACTIVE"`
}

var validate = validator.New()

func handleCreate(c *gin.Context) {
    var body CreateEntity
    if err := c.ShouldBindJSON(&body); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    if err := validate.Struct(body); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    // ...
}
```

Alternative: `github.com/invopop/validation` for programmatic validation.

#### `typed-error-classes.mdc`

Go uses sentinel errors + typed error structs:

```go
var (
    ErrNotFound   = errors.New("not found")
    ErrForbidden  = errors.New("forbidden")
)

type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("%s: %s", e.Field, e.Message)
}

// Classification with errors.Is / errors.As
if errors.Is(err, ErrNotFound) {
    return http.StatusNotFound, err.Error()
}

var ve *ValidationError
if errors.As(err, &ve) {
    return http.StatusBadRequest, ve.Error()
}
```

Rule: prefer `errors.Is` / `errors.As` over string matching.

#### `handler-thin-services-thick.mdc`

Gin example:

```go
// BAD — logic in handler
func UpdateHandler(c *gin.Context) {
    id := c.Param("id")
    existing, _ := repo.Find(id)
    if existing.Status == "ACTIVE" {
        // 20 lines of logic...
    }
    c.JSON(200, existing)
}

// GOOD — thin handler
func UpdateHandler(c *gin.Context) {
    id := c.Param("id")
    var body UpdateEntity
    if err := c.ShouldBindJSON(&body); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    result, err := service.Update(c, id, body)
    if err != nil {
        c.JSON(mapError(err))
        return
    }
    c.JSON(200, result)
}
```

#### `tdd-bdd-workflow/SKILL.md`

- **Framework:** stdlib `testing` + `testify` (assertions) + `gomock` or `testify/mock`
- **File naming:** `<name>_test.go` in same package (unit) or `<name>_integration_test.go` (integration)
- **Table-driven tests** are Go idiom — use them:

```go
func TestValidateStatus(t *testing.T) {
    tests := []struct {
        name    string
        input   string
        wantErr bool
    }{
        {"active is valid", "ACTIVE", false},
        {"lowercase rejected", "active", true},
        {"empty rejected", "", true},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            err := ValidateStatus(tt.input)
            if (err != nil) != tt.wantErr {
                t.Errorf("got err=%v, wantErr=%v", err, tt.wantErr)
            }
        })
    }
}
```

- **BDD naming:** t.Run names carry the "when / then" — e.g., `t.Run("when submitted with valid data, returns created entity", ...)`.

**Package hygiene:** `go.mod` is flat; use `go mod tidy` regularly; pin versions in `go.mod` (Go tooling does this).

**Common frameworks to reference in AGENTS.md:**
- Gin / Echo / Chi / stdlib net/http
- GORM / sqlx / stdlib database/sql
- go-playground/validator
- testify + gomock

---

### Java + Spring Boot

**Files to drop:** `no-inline-jsx-handlers.mdc`, `component-decomposition.mdc`, `types-in-dedicated-files.mdc`, `surface-all-errors.mdc`, `ui-strategy.md` template.

**Files to rewrite examples in:**

#### `runtime-input-validation.mdc`

Use **Bean Validation** (`@Valid` + `@NotBlank`, `@Size`, `@Pattern`):

```java
public record CreateEntityRequest(
    @NotBlank @Size(min = 1, max = 100) String name,
    @Pattern(regexp = "ACTIVE|INACTIVE") String status
) {}

@PostMapping("/entities")
public ResponseEntity<Entity> create(@Valid @RequestBody CreateEntityRequest body) {
    return ResponseEntity.status(201).body(service.create(body));
}

@ExceptionHandler(MethodArgumentNotValidException.class)
ResponseEntity<Map<String, Object>> onValidation(MethodArgumentNotValidException e) {
    return ResponseEntity.badRequest().body(Map.of("errors", e.getBindingResult().getFieldErrors()));
}
```

#### `typed-error-classes.mdc`

Java exception hierarchy + `@ControllerAdvice`:

```java
public class NotFoundException extends RuntimeException {
    public NotFoundException(String resource, String id) {
        super("%s with id %s not found".formatted(resource, id));
    }
}

@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(NotFoundException.class)
    ResponseEntity<Map<String, String>> notFound(NotFoundException e) {
        return ResponseEntity.status(404).body(Map.of("message", e.getMessage()));
    }
    @ExceptionHandler(AccessDeniedException.class)
    ResponseEntity<Map<String, String>> forbidden(AccessDeniedException e) {
        return ResponseEntity.status(403).body(Map.of("message", e.getMessage()));
    }
}
```

Rule: classify via `instanceof` (Java 21 pattern matching helps).

#### `handler-thin-services-thick.mdc`

```java
// BAD — logic in controller
@PutMapping("/entities/{id}")
public Entity update(@PathVariable String id, @RequestBody UpdateEntityRequest body) {
    var existing = repository.findById(id).orElseThrow();
    if (existing.getStatus() == Status.ACTIVE) {
        // 20 lines of logic...
    }
    return repository.save(existing);
}

// GOOD — thin controller
@PutMapping("/entities/{id}")
public Entity update(@PathVariable String id, @Valid @RequestBody UpdateEntityRequest body) {
    return entityService.update(id, body);
}
```

#### `tdd-bdd-workflow/SKILL.md`

- **Framework:** JUnit 5 + AssertJ + Mockito
- **File naming:** `<Name>Test.java` in `src/test/java/`, mirroring package structure
- **BDD structure:** `@Nested` classes give Given/When/Then feel:

```java
class EntityServiceTest {
    @Nested
    class WhenSubmittedWithValidData {
        @Test
        void returnsCreatedEntity() { /* ... */ }
        @Test
        void persistsToRepository() { /* ... */ }
    }
    @Nested
    class WhenSubmittedWithMissingFields {
        @Test
        void throwsValidationException() { /* ... */ }
    }
}
```

**Package hygiene:** Maven `pom.xml` / Gradle `build.gradle` — compile / test / runtime scopes; use `<version>` locked, never `LATEST`.

**Common frameworks to reference in AGENTS.md:**
- Spring Boot 3 (WebMVC / WebFlux)
- Spring Data JPA + Hibernate
- Jakarta Bean Validation
- JUnit 5 + Mockito + AssertJ + Testcontainers

---

### Kotlin + Spring Boot

Same as Java, with these swaps:

- **Data classes** replace records for request/response DTOs.
- **Sealed classes** for typed errors:
  ```kotlin
  sealed class DomainError : RuntimeException() {
      data class Validation(val field: String, val msg: String) : DomainError()
      data class NotFound(val resource: String, val id: String) : DomainError()
      data object Forbidden : DomainError()
  }
  ```
- **`when` expressions** for centralized error mapping.
- **Ktor** as alternative to Spring Boot — same layered pattern.
- **Test framework:** Kotest (BDD-native) or JUnit + MockK.

---

### Rust

**Files to drop:** `no-inline-jsx-handlers.mdc`, `component-decomposition.mdc`, `types-in-dedicated-files.mdc`, `surface-all-errors.mdc`, `ui-strategy.md` template.

**Files to rewrite examples in:**

- **Runtime validation:** `serde` + `validator` crate or manual `impl TryFrom`.
- **Typed errors:** `thiserror` for library errors + `anyhow` for application errors. Match on the enum:
  ```rust
  #[derive(Debug, thiserror::Error)]
  pub enum EntityError {
      #[error("validation: {0}")]
      Validation(String),
      #[error("not found: {resource} {id}")]
      NotFound { resource: String, id: String },
      #[error("forbidden")]
      Forbidden,
  }
  ```
- **Handler thin, services thick:** Axum / Actix example — extract validation into a request struct with `Deserialize + Validate`; keep handlers as `async fn` that call service methods and map errors.
- **TDD:** `#[cfg(test)] mod tests { ... }` — same file, unit tests inline. Integration tests in `tests/`. Cargo runs both.
- **Package hygiene:** `Cargo.toml` — dependencies vs dev-dependencies vs build-dependencies. Cargo pins via `Cargo.lock`.

**Common frameworks to reference in AGENTS.md:**
- Axum / Actix-web / Rocket
- sqlx / diesel
- serde + validator
- tokio-test + mockall

---

### C# + .NET (ASP.NET Core)

**Files to drop:** same as Java section.

**Files to rewrite examples in:**

- **Validation:** `System.ComponentModel.DataAnnotations` or **FluentValidation**:
  ```csharp
  public class CreateEntityRequest {
      [Required, StringLength(100, MinimumLength = 1)]
      public string Name { get; set; }
      [Required, RegularExpression("ACTIVE|INACTIVE")]
      public string Status { get; set; }
  }
  ```
- **Typed errors:** exception hierarchy + `IExceptionHandler` (or `UseExceptionHandler` middleware).
- **Handler thin:** Minimal APIs or `[ApiController]` — inject services via DI, keep endpoints one-liners.
- **TDD:** xUnit + Moq + FluentAssertions. BDD: `[Fact(DisplayName = "when X, then Y")]` or nested classes.
- **Package hygiene:** `<PackageReference>` in `.csproj`; use `Directory.Packages.props` for centralized version management.

---

## Universal decisions when adapting

When you copy the kit into a non-TS/React project, do this checklist at Day 0:

- [ ] **Delete** `.cursor/rules/engineering/no-inline-jsx-handlers.mdc` if not React.
- [ ] **Delete** `.cursor/rules/engineering/types-in-dedicated-files.mdc` if not TypeScript.
- [ ] **Delete** `templates/ui-strategy.md` if not building UI.
- [ ] **Delete or restructure** `.cursor/skills/lld-creation/SKILL.md` if backend-only / CLI / data pipeline.
- [ ] **Rewrite examples** in the 5 engineering rules that use TS code (`runtime-input-validation`, `typed-error-classes`, `handler-thin-services-thick`, `surface-all-errors` if frontend, `component-decomposition` if UI).
- [ ] **Rewrite Layer-Specific Patterns** section in `.cursor/skills/tdd-bdd-workflow/SKILL.md` with your test framework's idioms.
- [ ] **Rewrite `templates/feature-phase.md`** file paths and code patterns for your stack.
- [ ] **Rewrite `templates/lld.md`** file table columns for your stack.
- [ ] **Update `AGENTS.md.root`** Tech Stack + Commands sections for your stack.
- [ ] **Add stack-specific rules** in `.cursor/rules/engineering/` for patterns that repeat 3+ times (e.g., `pydantic-strict-mode`, `sqlalchemy-session-management`, `gin-middleware-order`, `spring-transactional-boundaries`).

Total adaptation time on Day 0 of a new language project: **~2 hours**.

---

## When to promote adaptations back into the kit

If you work in the same language for **3+ projects**, promote your language-specific variants into the kit as `.cursor/rules/engineering/_lang-python/`, `_lang-golang/`, etc. Then future you drops the whole folder in at Day 0.

Until you hit that 3× threshold, keep language adaptations in the project — not in the kit. Cargo-culting Python examples into a Go project causes more harm than the boilerplate saved.

---

## What NEVER changes across languages

These are the invariants of SDD, regardless of language:

1. **Spec → code, never code → spec.** PRD, Tech Solution, UI Strategy, Implementation Phases come first.
2. **10 mandatory ticket sections.**
3. **One layer per PR, ~400 impl lines max.**
4. **Append-only updates to specs.**
5. **Phase report at every phase close.**
6. **TDD red-green-refactor.**
7. **Nested AGENTS.md hierarchy.**
8. **Skills triggered by user intent, rules applied always.**
9. **Milestone folder structure** (`docs/delivery/<milestone>/`).
10. **PR readiness self-verification loop.**

If a "language pack" ever asks you to change one of these, the pack is wrong.
