---
name: ralph-spec-writer
description: Write specifications following the Ralph Wiggum methodology through deep, iterative interviews using AskUserQuestion. Use when the user asks to write specs, requirements, JTBD documentation, or when breaking down features into specification files. Automatically conducts comprehensive interviews about technical implementation, UI/UX, concerns, and tradeoffs before writing specs. Triggered by keywords like "spec", "specification", "requirements", "JTBD", "user story", "acceptance criteria", or "Ralph spec".
allowed-tools: Read, Write, Edit, Grep, Glob, AskUserQuestion, WebFetch
---

# Ralph Spec Writer

Expert at writing specifications following the **Ralph Wiggum methodology** - an AI-driven development approach where specifications guide autonomous implementation loops.

**Core Process**: Conduct deep, iterative interviews using AskUserQuestion about technical implementation, UI/UX, concerns, and tradeoffs, then write comprehensive specs based on gathered insights.

## Core Philosophy

Specs are the **upstream steering mechanism** that shapes what gets built. Well-structured specs enable AI agents to:
- Understand what needs to exist
- Identify gaps between specs and current code
- Generate prioritized implementation plans
- Self-correct through iteration

**Key principle**: "Context is everything" - specs must be focused, discoverable, and consumable within limited context windows.

## Specification Structure

### The JTBD → Topic → Spec Flow

1. **JTBD (Job to Be Done)**: High-level user outcome or need
   - Example: "As a developer, I need to authenticate users"

2. **Topic of Concern**: Distinct aspect within a JTBD
   - Topics are separated by the **"One Sentence Without 'And'" test**
   - If you need conjunctions to describe it, split into multiple topics
   - Example topics from auth JTBD:
     - Login flow
     - Session management
     - Password reset
     - OAuth integration

3. **Spec File**: One markdown file per topic in `specs/` directory
   - Filename: `{topic-name}.md` (lowercase, hyphenated)
   - Contains: What, Why, Acceptance Criteria, Context

### Standard Spec Template

```markdown
# {Topic Name}

## What
[1-2 sentences: What this is and what it does]

## Why
[Capture the "why" - the user need or business value this addresses]

## Acceptance Criteria

### Must Have
- [ ] Criterion 1 (specific, testable)
- [ ] Criterion 2 (specific, testable)
- [ ] Criterion 3 (specific, testable)

### Should Have
- [ ] Nice-to-have feature 1
- [ ] Nice-to-have feature 2

### Won't Have (This Iteration)
- [ ] Explicitly out of scope item 1
- [ ] Explicitly out of scope item 2

## Context

### User Flow
[Describe the happy path step-by-step]

1. User does X
2. System responds with Y
3. User sees Z

### Edge Cases
- **Case 1**: What happens when...
- **Case 2**: What happens if...

### Technical Constraints
- Must work with existing authentication system
- Should handle 1000 concurrent users
- Must complete within 200ms

### Related Specs
- `session-management.md` (depends on this)
- `oauth-integration.md` (related feature)

## Examples

### Example 1: Success Case
[Concrete example of expected behavior]

### Example 2: Error Case
[Concrete example of error handling]

## Open Questions
- [ ] Question 1 that needs answering?
- [ ] Question 2 that needs deciding?
```

## Deep Interview Process

**CRITICAL**: When writing specs, you MUST conduct comprehensive, multi-round interviews using AskUserQuestion. Never write specs without thoroughly interviewing the user first.

### Interview Philosophy

**Avoid obvious questions**. Ask insightful questions that:
- Uncover hidden complexity and edge cases
- Reveal tradeoffs and constraints
- Challenge assumptions about the design
- Explore the "why behind the why"
- Surface technical and UX implications

**Continue interviewing** until you have complete clarity on:
- All technical implementation details
- UI/UX decisions and their rationale
- Non-obvious edge cases and failure modes
- Performance and scalability considerations
- Security and data privacy implications
- Integration points and dependencies
- Tradeoffs between different approaches

### Multi-Stage Interview Strategy

#### Stage 1: Context & Goals (The "Why Behind the Why")

**❌ Obvious**: "What feature do you want to build?"

**✅ Insightful**:
```yaml
questions:
  - question: "What user pain point is this addressing that existing solutions fail to solve?"
    header: "Core Problem"
    options:
      - label: "Existing solutions are too slow"
        description: "Performance is the blocking factor"
      - label: "Existing solutions lack critical functionality"
        description: "Feature gap prevents users from completing their goal"
      - label: "Existing solutions are too complex"
        description: "Cognitive overhead or steep learning curve"
      - label: "No existing solution at all"
        description: "Greenfield feature for new capability"

  - question: "What happens if we don't build this feature?"
    header: "Impact"
    options:
      - label: "Users will use workarounds"
        description: "Painful but they can still accomplish the goal"
      - label: "Users will churn"
        description: "This is a blocker for retention"
      - label: "Revenue impact"
        description: "Directly affects business metrics"
      - label: "Competitive disadvantage"
        description: "Competitors have this and we don't"
```

#### Stage 2: Technical Architecture & Tradeoffs

**❌ Obvious**: "What technology should we use?"

**✅ Insightful**:
```yaml
questions:
  - question: "What's your tolerance for implementation complexity vs. feature completeness in the first iteration?"
    header: "Complexity Tradeoff"
    options:
      - label: "Simple implementation, basic features"
        description: "Ship fast with minimal edge case handling"
      - label: "Moderate complexity, core features polished"
        description: "Balance between speed and robustness"
      - label: "Handle edge cases comprehensively"
        description: "Take time to build it right from the start"

  - question: "Where do you expect the bottleneck to be as this scales?"
    header: "Scaling Concern"
    options:
      - label: "Database queries"
        description: "Data access patterns will be the constraint"
      - label: "API response time"
        description: "Server-side processing will be slow"
      - label: "Client-side rendering"
        description: "Browser performance will degrade"
      - label: "Network bandwidth"
        description: "Data transfer will be the bottleneck"

  - question: "What happens when this operation fails mid-execution?"
    header: "Failure Recovery"
    options:
      - label: "Fail fast, user retries"
        description: "Show error and require manual retry"
      - label: "Automatic retry with backoff"
        description: "System attempts recovery transparently"
      - label: "Partial success is acceptable"
        description: "Complete what's possible, report failures"
      - label: "Transactional all-or-nothing"
        description: "Rollback on any failure"
```

#### Stage 3: UI/UX & Interaction Design

**❌ Obvious**: "Where should the button go?"

**✅ Insightful**:
```yaml
questions:
  - question: "What should users understand immediately vs. discover through exploration?"
    header: "Information Architecture"
    options:
      - label: "Everything visible upfront"
        description: "All options and controls immediately apparent"
      - label: "Progressive disclosure"
        description: "Basic options visible, advanced features hidden initially"
      - label: "Contextual revelation"
        description: "Features appear based on user actions/state"

  - question: "How should the system communicate progress for long-running operations?"
    header: "Feedback Pattern"
    options:
      - label: "Spinner with percentage"
        description: "Deterministic progress indicator"
      - label: "Indeterminate loading"
        description: "Generic 'working' indicator without timeline"
      - label: "Real-time status updates"
        description: "Stream messages about what's happening"
      - label: "Background with notification"
        description: "Non-blocking operation, notify when complete"

  - question: "When users make mistakes, what's the recovery path?"
    header: "Error Recovery"
    options:
      - label: "Undo functionality"
        description: "One-click reversal of last action"
      - label: "Confirmation dialogs"
        description: "Prevent mistakes with warnings"
      - label: "Draft/autosave"
        description: "Never lose work, always recoverable"
      - label: "Clear error messages with fix suggestions"
        description: "Guide users to correct the issue"
```

#### Stage 4: Edge Cases & Non-Happy Paths

**❌ Obvious**: "What if the user enters invalid data?"

**✅ Insightful**:
```yaml
questions:
  - question: "What happens if two users try to modify the same resource simultaneously?"
    header: "Concurrent Access"
    options:
      - label: "Last write wins"
        description: "Simple but may lose data"
      - label: "Optimistic locking with conflict detection"
        description: "Alert user to conflicts, require resolution"
      - label: "Real-time collaborative editing"
        description: "Merge changes like Google Docs"
      - label: "Pessimistic locking (one editor at a time)"
        description: "Lock resource while being edited"

  - question: "How should the system behave when external dependencies are unavailable?"
    header: "Dependency Failure"
    options:
      - label: "Fail completely with error"
        description: "Cannot function without the dependency"
      - label: "Degrade gracefully with reduced functionality"
        description: "Core features work, enhanced features disabled"
      - label: "Queue operations for retry"
        description: "Buffer requests until dependency recovers"
      - label: "Cached/stale data acceptable"
        description: "Use last known good state"

  - question: "What data size extremes should we handle?"
    header: "Scale Extremes"
    options:
      - label: "Empty state (zero items)"
        description: "How does UI look with no data?"
      - label: "Single item"
        description: "Is the plural/singular handled correctly?"
      - label: "Thousands of items"
        description: "Pagination, virtualization, performance"
      - label: "Extremely large individual items"
        description: "Long text, huge files, deep nesting"
```

#### Stage 5: Security, Privacy & Compliance

**❌ Obvious**: "Should this be secure?"

**✅ Insightful**:
```yaml
questions:
  - question: "What's the blast radius if this data is compromised?"
    header: "Security Impact"
    options:
      - label: "Low - public/non-sensitive data"
        description: "Already public or not personally identifiable"
      - label: "Medium - user preferences/settings"
        description: "Privacy concern but not critical"
      - label: "High - personal/financial data"
        description: "GDPR/CCPA regulated, needs encryption"
      - label: "Critical - auth/credentials"
        description: "Compromise would enable account takeover"

  - question: "Who should be able to access this functionality?"
    header: "Access Control"
    options:
      - label: "Public (unauthenticated)"
        description: "Anyone on the internet"
      - label: "Authenticated users only"
        description: "Must have account and be logged in"
      - label: "Role-based (admin, user, etc.)"
        description: "Different permission levels"
      - label: "Resource-based (ownership)"
        description: "Can only access your own data"

  - question: "How long should this data be retained?"
    header: "Data Retention"
    options:
      - label: "Forever (or until user deletes)"
        description: "Permanent storage"
      - label: "Session-based (temporary)"
        description: "Cleared on logout or timeout"
      - label: "Time-based expiration"
        description: "Automatically deleted after X days/months"
      - label: "Compliance-driven retention"
        description: "Legal/regulatory requirements dictate"
```

#### Stage 6: Performance & Observability

**✅ Insightful**:
```yaml
questions:
  - question: "What performance degradation would users actually notice?"
    header: "Perceived Performance"
    options:
      - label: "Any delay over 100ms feels slow"
        description: "Real-time interaction expectation"
      - label: "Up to 1 second is acceptable"
        description: "Standard web page load tolerance"
      - label: "Users will wait 3-5 seconds"
        description: "Complex operation, expectation is set"
      - label: "Background process, not time-sensitive"
        description: "Minutes to hours acceptable"

  - question: "When something goes wrong, what information do you need to debug it?"
    header: "Debugging Needs"
    options:
      - label: "User action logs"
        description: "What steps led to the problem"
      - label: "System state snapshots"
        description: "Data state when error occurred"
      - label: "Performance metrics"
        description: "Timing, resource usage"
      - label: "External service interactions"
        description: "API calls, database queries"

  - question: "How will you know if this feature is being used successfully?"
    header: "Success Metrics"
    multiSelect: true
    options:
      - label: "Usage analytics"
        description: "Track feature adoption and frequency"
      - label: "Error rates"
        description: "Monitor failures and edge cases hit"
      - label: "Performance metrics"
        description: "Response times, throughput"
      - label: "User feedback/satisfaction"
        description: "Qualitative assessment"
```

### Iterative Deepening

After each round of questions:

1. **Synthesize** what you've learned
2. **Identify gaps** in understanding
3. **Formulate next layer** of questions based on answers
4. **Dive deeper** into areas of complexity or ambiguity

**Continue interviewing** until you can confidently answer:
- "What exactly needs to be built?"
- "Why this approach over alternatives?"
- "What could go wrong and how do we handle it?"
- "What does success look like?"

### Interview Completion Criteria

Only consider the interview complete when you have clarity on:

- [ ] **User value**: Why this matters and what problem it solves
- [ ] **Technical approach**: Architecture, technologies, integration points
- [ ] **UI/UX decisions**: Interaction patterns, feedback, error handling
- [ ] **Edge cases**: At least 5-10 non-obvious scenarios documented
- [ ] **Performance targets**: Specific metrics (response time, throughput, etc.)
- [ ] **Security model**: Authentication, authorization, data protection
- [ ] **Failure modes**: What can go wrong and recovery strategies
- [ ] **Tradeoffs**: Decisions made and alternatives rejected
- [ ] **Success metrics**: How to measure if it's working
- [ ] **Dependencies**: What this relies on and what relies on this

**If any of these are unclear, continue interviewing.**

## Requirements Gathering Process

### Phase 1: Understanding the JTBD

Use **AskUserQuestion** systematically to explore:

1. **Who & Why**:
   ```yaml
   questions:
     - question: "Who is the primary user for this feature?"
       header: "Target User"
       options:
         - label: "End users"
           description: "People using the application directly"
         - label: "Developers"
           description: "Engineers integrating or extending the system"
         - label: "Administrators"
           description: "People managing and configuring the system"
   ```

2. **Current State**:
   - "What problem are users experiencing today?"
   - "What workarounds exist currently?"
   - "What's the impact of not having this?"

3. **Success Criteria**:
   - "How will you know this is working correctly?"
   - "What metrics define success?"
   - "What would make this delightful vs. just functional?"

### Phase 2: Breaking Down Topics

Apply the **"One Sentence Without 'And'" Test**:

❌ Bad (multiple topics):
> "Authentication system that handles login and password reset and OAuth integration"

✅ Good (single topics):
> "Login flow with email/password"
> "Password reset via email token"
> "OAuth integration with Google/GitHub"

For each topic, ask:
- "Can I describe this in one sentence without using 'and'?"
- "Does this represent a single concern?"
- "Would changing this require touching distinct code areas?"

### Phase 3: Deriving Acceptance Criteria

**Acceptance-Driven Approach**: Tests derive from acceptance criteria, not afterthoughts.

For each criterion:
- **Make it specific**: "Fast" → "Loads in < 200ms"
- **Make it testable**: "User-friendly" → "New user completes first task in < 2 minutes"
- **Make it binary**: Pass/fail with no ambiguity

**Test Requirements Derivation**:
```markdown
### Must Have
- [ ] User can log in with email and password
  - **Test**: Valid credentials return 200 and JWT token
  - **Test**: Invalid credentials return 401
  - **Test**: Missing fields return 400 with error details

- [ ] Session expires after 24 hours
  - **Test**: Token invalid after expiry timestamp
  - **Test**: Expired token returns 401 with "expired" error
```

### Phase 4: Capturing Context & Examples

**Context Shapes Implementation**:
- Existing patterns agent should follow
- Technical constraints that limit approaches
- Related specs that inform decisions

**Examples Eliminate Ambiguity**:
```markdown
## Examples

### Example 1: Successful Login
**Request:**
```json
POST /auth/login
{
  "email": "user@example.com",
  "password": "securePass123"
}
```

**Response:**
```json
{
  "token": "eyJhbGc...",
  "expiresAt": "2024-01-18T12:00:00Z",
  "user": {
    "id": "123",
    "email": "user@example.com"
  }
}
```

### Example 2: Invalid Credentials
**Request:**
```json
POST /auth/login
{
  "email": "user@example.com",
  "password": "wrongPassword"
}
```

**Response:**
```json
{
  "error": "Invalid credentials",
  "code": "AUTH_FAILED"
}
```
Status: 401
```

## Spec Writing Best Practices

### 1. Focus on WHAT, not HOW
❌ "Use bcrypt with 10 salt rounds to hash passwords"
✅ "Passwords must be securely hashed and irreversible"

The HOW emerges from code patterns and agent investigation.

### 2. Explicit Edge Cases
Don't assume the agent will think of edge cases. List them:
- Empty inputs
- Invalid formats
- Rate limiting scenarios
- Concurrent access patterns
- Failure recovery

### 3. Clear Dependencies
```markdown
### Dependencies
- **Requires**: `database-schema.md` (users table must exist)
- **Blocks**: `session-management.md` (needs auth to create sessions)
- **Related**: `password-reset.md` (shares token generation patterns)
```

### 4. Scope Boundaries
Explicitly state what's OUT of scope:
```markdown
### Won't Have (This Iteration)
- [ ] Two-factor authentication (separate spec)
- [ ] Social login (separate spec: `oauth-integration.md`)
- [ ] Remember me functionality (future enhancement)
```

### 5. Progressive Detail
Start broad, add detail as questions arise:
- Version 1: Basic what/why/acceptance
- Version 2: Add examples after clarifying questions
- Version 3: Add edge cases discovered during planning

## File Organization

```
project-root/
├── specs/
│   ├── README.md              # Index of all specs
│   ├── auth-login.md          # Login specification
│   ├── auth-password-reset.md # Password reset spec
│   ├── user-profile.md        # User profile spec
│   └── ...
├── IMPLEMENTATION_PLAN.md     # Generated by Ralph Loop
├── AGENTS.md                  # Operational guide
└── src/                       # Implementation
```

### specs/README.md Template

```markdown
# Specifications Index

## Active Specs

### Authentication
- [Login Flow](auth-login.md) - Email/password authentication
- [Password Reset](auth-password-reset.md) - Token-based password recovery
- [Session Management](auth-session.md) - JWT session handling

### User Management
- [User Profile](user-profile.md) - Profile CRUD operations
- [User Settings](user-settings.md) - Preferences and configuration

## Completed Specs
- [Database Schema](database-schema.md) - ✅ Implemented

## Parking Lot
Ideas not yet scoped:
- Two-factor authentication
- OAuth social login
- API rate limiting
```

## Integration with Ralph Loop

### How Specs Drive the Loop

**Planning Mode** (PROMPT_plan.md):
1. Reads all specs in `specs/`
2. Compares specs against existing code
3. Identifies gaps and generates tasks
4. Prioritizes based on dependencies

**Building Mode** (PROMPT_build.md):
1. Orients by studying relevant specs
2. Selects highest priority task
3. Investigates existing patterns
4. Implements according to spec
5. Validates against acceptance criteria

### Spec Evolution

Specs are **living documents** during planning/building:
- Discovered ambiguities → Add clarifying examples
- Found edge cases → Update edge cases section
- Changed requirements → Update spec and regenerate plan

**When to regenerate plan**:
- Significant spec changes (new acceptance criteria)
- Scope adjustments (added/removed topics)
- Wrong trajectory detected

## Writing Specs: Complete Workflow

### 0. Pre-Work: Read Existing Context (If Provided)

If user references existing files (SPEC.md, requirements docs, etc.):

1. **Read** all referenced files first
2. **Extract** existing requirements, constraints, decisions
3. **Use as foundation** for your interview questions
4. **Fill gaps** through iterative questioning

### 1. Identify the JTBD

**User Request**: "I need user authentication for my app"

**First Action**: Begin deep interview (see Deep Interview Process above)

**After Interviews, Expand to JTBD**:
> As a developer, I need a secure authentication system so users can safely access their accounts

### 2. Break into Topics

Use AskUserQuestion to understand scope:
```yaml
questions:
  - question: "What authentication methods do you want to support?"
    header: "Auth Methods"
    multiSelect: true
    options:
      - label: "Email/Password"
        description: "Traditional username and password login"
      - label: "OAuth (Google, GitHub)"
        description: "Social login integration"
      - label: "Magic Links"
        description: "Passwordless email-based login"
      - label: "Two-Factor (2FA)"
        description: "Additional security layer with TOTP/SMS"
```

Based on response, create separate specs:
- `auth-email-password.md`
- `auth-oauth.md`
- `auth-magic-links.md`
- `auth-2fa.md`

### 3. Write Each Spec

For `auth-email-password.md`:

a) **Start with What/Why**
b) **Derive Acceptance Criteria** (use AskUserQuestion for clarification)
c) **Map User Flow**
d) **List Edge Cases**
e) **Add Examples**
f) **Document Dependencies**

### 4. Review & Validate

**Self-Check**:
- [ ] Can I explain this topic in one sentence without "and"?
- [ ] Are acceptance criteria specific and testable?
- [ ] Have I covered **non-obvious** edge cases from interviews?
- [ ] Are examples concrete with actual data?
- [ ] Is it clear what's in/out of scope?
- [ ] Have I documented **tradeoffs** and rejected alternatives?
- [ ] Are **performance targets** and **security requirements** explicit?

**Upstream Steering Check**:
- [ ] Does this give Ralph enough context to start?
- [ ] Are there discoverable patterns in existing code to follow?
- [ ] Have I avoided prescribing HOW (implementation details)?

**Interview Completeness Check**:
- [ ] Did I conduct multiple rounds of questions?
- [ ] Did I avoid obvious questions and ask insightful ones?
- [ ] Do I understand the technical implications?
- [ ] Do I understand the UI/UX rationale?
- [ ] Do I know what could go wrong?

### 5. Write Spec to File

Once interview is complete and all criteria are met:

1. **Create spec file** in `specs/` directory
2. **Use all interview insights** to populate sections
3. **Document tradeoffs** explicitly in Context section
4. **Include edge cases** discovered through questioning
5. **Add examples** based on scenarios discussed

### 6. Update specs/README.md

Add to index with clear description and status.

## Advanced Patterns

### Subjective Acceptance Criteria

Some criteria resist programmatic validation:
- "Login page feels modern and professional"
- "Error messages are helpful and friendly"
- "Workflow is intuitive for first-time users"

**LLM-as-Judge Pattern** (proposed enhancement):
```markdown
### Must Have (Subjective)
- [ ] Login page provides a modern, professional appearance
  - **Validation**: LLM review of screenshot against brand guidelines
  - **Pass Criteria**: Claude rates design as "professional" with score ≥ 8/10

- [ ] Error messages are clear and actionable
  - **Validation**: LLM review of error message text
  - **Pass Criteria**: Claude confirms messages explain what went wrong and how to fix it
```

### Branch-Scoped Specs

For **Ralph-Friendly Work Branches**, create focused specs per feature branch:

```bash
# Current directory structure
feature/user-auth/
├── specs/
│   ├── auth-email-password.md  # Only relevant specs
│   └── auth-session.md
├── IMPLEMENTATION_PLAN.md       # Scoped to this branch
└── AGENTS.md                    # Branch-specific notes
```

Maintains determinism by scoping at branch creation, not runtime.

### Story Mapping Integration

When using **JTBD → Story Map → SLC** approach:

1. **Audience JTBD**: `AUDIENCE_JTBD.md` captures who and why
2. **Story Map**: Sequence activities into user journey
3. **SLC Slicing**: Horizontal slices for Simple/Lovable/Complete
4. **Spec Generation**: Create specs for each slice's activities

This reframes product thinking around outcomes while maintaining Ralph's workflow.

## Common Pitfalls

### ❌ Over-Specification
**Problem**: Prescribing implementation details
```markdown
Use Express.js with helmet middleware and rate-limiter-flexible
with Redis backend to rate limit login attempts to 5 per minute
using a sliding window algorithm...
```

**Fix**: Focus on requirements
```markdown
Login attempts must be rate-limited to prevent brute force attacks.
Target: Maximum 5 attempts per minute per IP address.
```

### ❌ Under-Specification
**Problem**: Too vague for actionable tasks
```markdown
The system should be secure and performant.
```

**Fix**: Concrete, testable criteria
```markdown
### Must Have
- [ ] Passwords hashed using industry-standard algorithm
- [ ] Login endpoint responds in < 200ms for p95
- [ ] Failed login attempts locked after 5 tries for 15 minutes
```

### ❌ Mixed Topics
**Problem**: Multiple concerns in one spec
```markdown
# User Authentication and Profile Management
Handles login, sessions, password reset, profile updates, avatar uploads...
```

**Fix**: Separate specs by topic
- `auth-login.md`
- `auth-sessions.md`
- `auth-password-reset.md`
- `user-profile-update.md`
- `user-avatar-upload.md`

### ❌ Missing Context
**Problem**: No examples or edge cases
```markdown
## What
Users can log in.

## Acceptance Criteria
- [ ] Login works
```

**Fix**: Rich context with examples
```markdown
## What
Users authenticate using email and password, receiving a JWT token for subsequent requests.

## Acceptance Criteria
- [ ] Valid credentials return JWT token
- [ ] Invalid credentials return 401 with error message
- [ ] Missing fields return 400 with field-specific errors

## Examples
[Concrete request/response examples]

## Edge Cases
- Empty email/password
- Email not found
- Incorrect password
- Account locked
- Expired session
```

## Integration with AskUserQuestion

Use Claude's built-in **AskUserQuestionTool** during requirements gathering:

### Discovery Questions Template

```yaml
# Stage 1: Understand the Why
questions:
  - question: "What problem does this solve for users?"
    header: "Problem"
    options:
      - label: "Current process is too slow"
        description: "Users spend excessive time on this task"
      - label: "Current process is error-prone"
        description: "Users frequently make mistakes"
      - label: "Feature doesn't exist"
        description: "Users can't accomplish this goal at all"

# Stage 2: Define Success
questions:
  - question: "How will you measure if this is working well?"
    header: "Success Metric"
    options:
      - label: "Task completion time"
        description: "Users complete the workflow faster"
      - label: "Error reduction"
        description: "Fewer mistakes or support requests"
      - label: "User satisfaction"
        description: "Positive feedback or NPS improvement"
      - label: "Adoption rate"
        description: "Percentage of users actually using the feature"

# Stage 3: Clarify Scope
questions:
  - question: "Which features are most important for the initial release?"
    header: "Priority"
    multiSelect: true
    options:
      - label: "Basic functionality (Recommended)"
        description: "Minimum viable feature that solves the core problem"
      - label: "Advanced options"
        description: "Additional configuration and customization"
      - label: "Integration with existing features"
        description: "Connect to other parts of the system"
```

### Iterative Refinement

Use multiple AskUserQuestion calls to progressively refine specs:

1. **Initial exploration**: Understand JTBD and user needs
2. **Scope definition**: Break into topics
3. **Criteria clarification**: Define acceptance criteria per topic
4. **Edge case discovery**: Uncover non-obvious scenarios
5. **Example validation**: Confirm understanding with concrete examples

## Language Patterns for Specs

Use **Ralph-aligned language** that guides agents effectively:

### Discovery Language
- "Study the existing authentication patterns" (not "read" or "look at")
- "Investigate how sessions are currently managed"
- "Don't assume not implemented" (counters reinvention)

### Requirement Language
- "Must have" / "Should have" / "Won't have"
- "Validates that..." / "Ensures..." / "Prevents..."
- "Returns..." / "Responds with..." / "Displays..."

### Context Language
- "This builds on..." (dependencies)
- "This differs from... because..." (distinctions)
- "Related to... but focused on..." (relationships)

### Example Language
- "Given... when... then..." (BDD-style)
- "For example, if... the system..." (concrete scenarios)
- "In the case of... expect..." (edge cases)

## Validation Checklist

Before considering a spec complete:

**Structure**:
- [ ] File in `specs/` directory with clear name
- [ ] What/Why/Acceptance/Context sections present
- [ ] Added to `specs/README.md` index

**Content**:
- [ ] Passes "One Sentence Without 'And'" test
- [ ] Acceptance criteria are specific and testable
- [ ] At least 2 concrete examples included
- [ ] Edge cases documented
- [ ] Dependencies/relationships noted

**Ralph Integration**:
- [ ] Focused enough for context window constraints
- [ ] Provides upstream steering without prescribing HOW
- [ ] Enables test-driven development via clear criteria
- [ ] Discoverable by planning mode gap analysis

**Clarity**:
- [ ] Non-technical stakeholder can understand what/why
- [ ] Technical implementer knows what "done" looks like
- [ ] Ralph agent has enough context to start

## Reference Resources

### Key Concepts
- **Context Budget**: ~176K usable tokens, scope specs accordingly
- **Upstream Steering**: Specs + patterns shape generation
- **Downstream Steering**: Tests + builds create backpressure
- **Eventual Consistency**: Iteration achieves correctness

### File Interactions
```
specs/*.md
    ↓
PROMPT_plan.md → reads specs → generates tasks
    ↓
IMPLEMENTATION_PLAN.md
    ↓
PROMPT_build.md → reads specs + plan → implements
    ↓
src/* (with tests validating acceptance criteria)
```

### Further Reading
- Ralph Playbook: https://github.com/ghuntley/how-to-ralph-wiggum
- Agent Skills: https://platform.claude.com/docs/en/agents-and-tools/agent-skills
- Claude Code Skills: https://code.claude.com/docs/en/skills

---

## Quick Start

**User asks**: "Help me write specs for user authentication"

**Your workflow**:

1. **Read any referenced files** (if user provides SPEC.md or similar)
2. **Begin deep interview** using AskUserQuestion:
   - Stage 1: Context & Goals (why behind the why)
   - Stage 2: Technical Architecture & Tradeoffs
   - Stage 3: UI/UX & Interaction Design
   - Stage 4: Edge Cases & Non-Happy Paths
   - Stage 5: Security, Privacy & Compliance
   - Stage 6: Performance & Observability
3. **Continue iterating** on questions until Interview Completion Criteria met
4. **Break into topics** using "One Sentence Without 'And'" test
5. **For each topic**, create spec file in `specs/`:
   - Write What/Why based on interview insights
   - Derive acceptance criteria from discussed requirements
   - Document edge cases uncovered in interviews
   - Add examples from scenarios discussed
   - Explicitly note tradeoffs and rejected alternatives
6. **Update** `specs/README.md` with new specs
7. **Validate** against comprehensive checklist

**Result**: Deep, well-reasoned specifications informed by thorough understanding of technical, UX, and business tradeoffs, ready for Ralph Loop consumption

---

## Critical Reminders

🎯 **ALWAYS conduct deep interviews** - Never skip the iterative questioning process

🎯 **Avoid obvious questions** - Ask insightful questions that uncover hidden complexity

🎯 **Continue until complete** - Keep interviewing until Interview Completion Criteria are met

🎯 **Document everything learned** - Capture insights from interviews in the spec files
