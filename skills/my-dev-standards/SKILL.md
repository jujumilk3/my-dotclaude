---
name: my-dev-standards
description: |
  Core development standards and conventions for ALL programming work.

  ALWAYS apply when writing, modifying, or reviewing ANY code in ANY language.

  Critical rules:
  - No redundant comments/docstrings (if code is self-explanatory, NO comment)
  - Python: use uv package manager exclusively
  - TypeScript over JavaScript (always prefer .ts/.tsx)
  - Database: singular snake_case table names
  - DateTime: timezone-aware ISO 8601 format
  - Web testing: Playwright MCP for user behavior testing
  - Git: NEVER auto-commit or auto-push without explicit user request
  - Project state: maintain @STATUS.md for handoff documentation

  Applies to: writing code, creating functions/classes/methods, adding comments,
  working with databases, handling git operations, building web apps/APIs, writing tests,
  project setup, documentation, Python, TypeScript, JavaScript, SQL, any programming language.

  Triggered by: code, write, create, function, class, method, variable, comment,
  docstring, python, typescript, javascript, ts, js, database, table, schema,
  datetime, timezone, git, commit, push, web, API, frontend, backend, test,
  project, documentation, file, edit, modify, develop, implement, build.
---

# My Development Standards

This skill maintains consistent conventions across all development work.

## Python Project Rules

### Required: uv-based Project Management

All Python projects **must** use **uv** for management.

#### Creating a New Project

```bash
# Initialize project
uv init <project-name>
cd <project-name>

# Or in current directory
uv init
```

#### Dependency Management

```bash
# Add package
uv add <package-name>

# Add dev dependency
uv add --dev <package-name>

# Remove package
uv remove <package-name>

# Sync dependencies
uv sync
```

#### Running Scripts

```bash
# Run Python script
uv run python <script.py>

# Or run script defined in pyproject.toml
uv run <script-name>
```

#### Project Structure Example

```
project-name/
├── pyproject.toml      # Project config managed by uv
├── uv.lock             # Lock file (auto-generated)
├── .python-version     # Python version pinning
├── src/
│   └── project_name/
│       ├── __init__.py
│       └── main.py
├── tests/
│   └── test_main.py
└── README.md
```

### pyproject.toml Template

```toml
[project]
name = "project-name"
version = "0.1.0"
description = ""
readme = "README.md"
requires-python = ">=3.11"
dependencies = []

[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "ruff>=0.4.0",
]

[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP"]

[tool.pytest.ini_options]
testpaths = ["tests"]
```

### Never Do

- ❌ Use `pip install` directly
- ❌ Manually manage `requirements.txt`
- ❌ Create `venv` or `virtualenv` manually
- ❌ Use `poetry`
- ❌ Use `conda`

### Always Do

- ✅ Start projects with `uv init`
- ✅ Add packages with `uv add`
- ✅ Run scripts with `uv run`
- ✅ Manage project config in `pyproject.toml`
- ✅ Include `uv.lock` in version control

---

## Code Documentation Rules

### CRITICAL RULE: No Redundant Comments

**If the code clearly shows WHAT it does, DO NOT add comments or docstrings.**

This applies to:
- ✅ **All functions and methods** - If the name and signature are clear, no docstring needed
- ✅ **All classes** - If the class name is descriptive, no class docstring needed
- ✅ **All variables** - If the name is clear, no comment needed
- ✅ **All tests** - If the test name describes what it tests, no docstring needed
- ✅ **All magic methods** - If behavior is standard (__add__, __sub__, __str__, etc.), no docstring needed
- ✅ **All getters/setters** - If they just get/set a value, no docstring needed
- ✅ **All simple operations** - If code is self-explanatory, no comment needed

**The test**: Can someone understand what this does by reading the code? → Then NO comment/docstring.

### When Comments ARE Needed

Only add comments for:
1. **WHY decisions were made** (not WHAT the code does)
2. **Complex algorithms** that aren't obvious from code alone
3. **Performance optimizations** and their tradeoffs
4. **Workarounds** for external bugs or limitations
5. **Business rules** that aren't self-evident from code
6. **Non-obvious constraints** or preconditions
7. **Side effects** that aren't visible in the function signature

#### Bad Comments (Don't Write These)

**THE GOLDEN RULE**: If someone can understand what the code does by reading it, DO NOT add a comment or docstring.

```python
# ❌ Obvious comments
# Get user by ID
user = get_user_by_id(user_id)

# ❌ Obvious docstrings
def validate_email(email: str) -> bool:
    """Validate email format."""  # Name already says this!
    return re.match(r"[^@]+@[^@]+\.[^@]+", email) is not None

class User:
    """User class."""  # Duh!
    pass

# ❌ Test docstrings
class TestUser:
    """Tests for User class."""  # Obvious!

    def test_default_init(self):
        """Test default initialization."""  # Name already says this!
        user = User()

# ❌ Magic methods
def __add__(self, other: float) -> Value:
    """Add offset to value."""  # Code shows this!
    return Value(self.offset + other)

# ❌ CRUD operations
def create_user(name: str, email: str) -> User:
    """Create a user."""  # Obviously!
    return User(name=name, email=email)
```

```typescript
// ❌ Obvious comments
// Create new user
const user = new User(name, email);

// ❌ Obvious docstrings
/**
 * Validates email format.  // Name already says this!
 */
function validateEmail(email: string): boolean {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

// ❌ Obvious class docs
/**
 * User service class.  // Obviously!
 */
class UserService {
    /**
     * Create a new user.  // Obviously!
     */
    async create(data: CreateUserDto): Promise<User> {
        return this.repository.save(data);
    }
}
```

#### Good Comments (Write These)

```python
# ✅ Explains WHY, not WHAT
# Use binary search - dataset can be 10M+ rows, linear search = 30s
result = binary_search(sorted_data, target)

# ✅ Non-obvious business logic
# Price BEFORE discount due to tax regulations (TICKET-1234)
base_price = calculate_base_price(item)
final_price = apply_discount(base_price, discount)

# ✅ Workaround
# External API has 5% error rate per SLA
@retry(max_attempts=3, backoff=exponential)
def fetch_external_data():
    pass
```

```typescript
// ✅ Complex algorithm
// Floyd's cycle detection (tortoise and hare) - O(1) space
function hasCycle(head: Node): boolean {
    // ... implementation
}

// ✅ Performance consideration
// Computing on every render = 200ms lag on large datasets
const memoizedValue = useMemo(() => expensiveComputation(data), [data]);
```

#### Test Documentation

**NEVER write docstrings for test functions that just restate the test name.**

```python
# ❌ Bad
class TestUser:
    """Tests for User class."""  # Obvious!

    def test_login_with_valid_credentials(self):
        """Test login with valid credentials."""  # Name already says this!
        assert auth.login("user@example.com", "password123").success

# ✅ Good: No docstrings when test name is clear
class TestUser:
    def test_login_with_valid_credentials(self):
        assert auth.login("user@example.com", "password123").success

    def test_account_locks_after_five_failed_attempts(self):
        """After 5 failed attempts, account locks for 15min (SECURITY-123)."""
        for _ in range(5):
            auth.login("user@example.com", "wrong")
        assert auth.login("user@example.com", "correct").error == "locked"
```

**Only add test docstrings for non-obvious business rules or complex setup.**

#### Function Documentation

**Only document when name + signature don't tell the full story.**

```python
# ❌ Bad: Obvious
def add(a: int, b: int) -> int:
    """Adds two numbers."""  # Duh!
    return a + b

def __add__(self, other: float) -> Value:
    """Add offset."""  # Code shows this!
    return Value(self.offset + other)

# ✅ Good: No docs for simple functions
def add(a: int, b: int) -> int:
    return a + b

def __add__(self, other: float) -> Value:
    return Value(self.offset + other)

# ✅ Good: Document non-obvious behavior
def process_payment(amount: Decimal, currency: str = "USD") -> PaymentResult:
    """
    Process payment with idempotency guarantee.

    Calling multiple times with same amount charges once.
    Uses request ID from context for deduplication.
    """
    pass
```

```typescript
// ❌ Bad
/**
 * Gets user by ID
 */
function getUserById(id: number): User {
    return db.users.find(id);
}

// ✅ Good: No docs when obvious
function getUserById(id: number): User {
    return db.users.find(id);
}

// ✅ Good: Document caching behavior
/**
 * Cache TTL: 5min. Use getUserByIdFresh() for real-time data.
 */
function getUserById(id: number): Promise<User> {
    return cachedFetch(`/users/${id}`, { ttl: 300 });
}
```

### Self-Documenting Code First

Instead of comments, prefer:
- Clear variable and function names
- Small, focused functions
- Type annotations (Python) or TypeScript types
- Extracting magic numbers to named constants

```python
# ❌ Bad: Needs comments
# Check if user can access premium features
if u.subscription_type == 1 and u.payment_status == 'active':
    # ... grant access

# ✅ Good: Self-documenting
PREMIUM_SUBSCRIPTION = 1

def has_active_premium_subscription(user: User) -> bool:
    return (
        user.subscription_type == PREMIUM_SUBSCRIPTION
        and user.payment_status == PaymentStatus.ACTIVE
    )

if has_active_premium_subscription(user):
    # ... grant access
```

### Never Do

- ❌ **CRITICAL**: Write ANY comment or docstring that just restates what the code obviously does
- ❌ Add comments that describe WHAT the code does (code already shows this)
- ❌ Write docstrings for functions where the name + signature tell the full story
- ❌ Add docstrings for obvious magic methods (__add__, __sub__, __mul__, __str__, etc.)
- ❌ Document simple getters/setters where behavior is obvious
- ❌ Write docstrings that just restate the function/class/method name
- ❌ Add test docstrings that just repeat the test name
- ❌ Write class docstrings like "Tests for X" or "User class" or "Helper for X"
- ❌ Document every function "because it's good practice" (it's not!)
- ❌ Leave TODO comments without tickets/dates
- ❌ Comment out code (use version control)
- ❌ Explain HOW code works (code shows this)
- ❌ Add comments like "initialize variable", "loop through items", "return result"

### Always Do

- ✅ **CRITICAL**: Only write comments when code alone doesn't tell the full story
- ✅ Write comments explaining WHY, never WHAT
- ✅ Use clear, descriptive names that eliminate need for comments
- ✅ Document complex algorithms and non-obvious logic
- ✅ Explain non-obvious business rules and constraints
- ✅ Note performance considerations and tradeoffs
- ✅ Warn about gotchas, edge cases, and side effects
- ✅ Prefer self-documenting code over comments/docstrings
- ✅ Use descriptive function/method names that eliminate need for docstrings
- ✅ Use descriptive test names instead of docstrings
- ✅ Skip docstrings for simple, obvious functions/methods/classes
- ✅ Ask yourself: "Does the code clearly show what this does?" → If yes, NO comment

---

## JavaScript/TypeScript Rules

### Required: TypeScript First

When both JavaScript and TypeScript options are available, **always choose TypeScript**.

Only use JavaScript when TypeScript is not an option.

#### Decision Tree

```
Can I use TypeScript?
├─ Yes → ✅ Use TypeScript
└─ No  → ✅ Use JavaScript (fallback only)
```

#### Examples

**Creating a New Project:**

```bash
# ✅ Correct: Use TypeScript
npm create vite@latest my-app -- --template react-ts
npm create next-app@latest --typescript

# ❌ Wrong: Don't use JavaScript if TypeScript is available
npm create vite@latest my-app -- --template react
```

**Adding Files:**

```bash
# ✅ Correct
touch component.tsx
touch utils.ts

# ❌ Wrong (when TypeScript is available)
touch component.jsx
touch utils.js
```

**Installing Dependencies:**

```bash
# ✅ Always install type definitions when available
npm install axios
npm install -D @types/node @types/react
```

### When TypeScript is NOT Available

Only these scenarios justify using JavaScript:

1. Legacy project without TypeScript setup
2. Quick prototyping in browser console
3. Simple build scripts where types add no value
4. Third-party library doesn't support TypeScript
5. Explicitly instructed to use JavaScript

### Project Configuration

**Always include these in TypeScript projects:**

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true
  }
}
```

### Never Do

- ❌ Choose JavaScript when TypeScript is available
- ❌ Use `.js` or `.jsx` files in TypeScript projects
- ❌ Skip type definitions (`@types/*` packages)
- ❌ Use `any` type excessively (defeats the purpose)

### Always Do

- ✅ Prefer TypeScript for all new projects
- ✅ Use `.ts` and `.tsx` extensions
- ✅ Install type definitions for dependencies
- ✅ Enable strict mode in `tsconfig.json`
- ✅ Add proper type annotations

---

## Web Project Testing Rules

### Required: Playwright MCP for User Behavior Testing

All web projects **must** use **Playwright MCP** to test functionality by simulating real user behavior.

When implementing or modifying web features, always verify the changes work correctly by:
1. Opening the browser using Playwright MCP
2. Simulating actual user interactions (clicks, typing, navigation)
3. Verifying the expected behavior occurs

#### When to Use Playwright Testing

**Always test with Playwright MCP when:**
- Implementing new UI features
- Modifying existing user-facing functionality
- Adding forms or interactive elements
- Changing navigation or routing
- Updating authentication flows
- Making API integration changes that affect the UI

#### Testing Workflow

```typescript
// 1. Navigate to the page
await browser_navigate({ url: "http://localhost:3000/login" });

// 2. Take a snapshot to see the current state
await browser_snapshot();

// 3. Interact with elements (fill forms, click buttons)
await browser_type({
  element: "email input field",
  ref: "input[name='email']",
  text: "user@example.com"
});

await browser_click({
  element: "login button",
  ref: "button[type='submit']"
});

// 4. Verify the result
await browser_snapshot();  // Check if login succeeded
```

#### Example Test Scenarios

**Login Flow:**
```typescript
// Navigate to login page
await browser_navigate({ url: "http://localhost:3000/login" });

// Fill credentials
await browser_fill_form({
  fields: [
    { name: "email", type: "textbox", ref: "input[name='email']", value: "test@example.com" },
    { name: "password", type: "textbox", ref: "input[name='password']", value: "password123" }
  ]
});

// Submit and verify redirect
await browser_click({ element: "login button", ref: "button[type='submit']" });
await browser_wait_for({ text: "Dashboard" });
```

**Form Submission:**
```typescript
// Navigate to form page
await browser_navigate({ url: "http://localhost:3000/contact" });

// Fill form fields
await browser_type({
  element: "name field",
  ref: "input#name",
  text: "John Doe"
});

// Submit and check success message
await browser_click({ element: "submit button", ref: "button#submit" });
await browser_wait_for({ text: "Thank you for your message" });
```

**Navigation Testing:**
```typescript
// Test menu navigation
await browser_navigate({ url: "http://localhost:3000" });
await browser_click({ element: "products menu", ref: "a[href='/products']" });
await browser_wait_for({ text: "Product Catalog" });

// Verify URL changed
await browser_snapshot();  // Check current page state
```

#### Integration with Development Workflow

**After implementing a feature:**
1. Start the development server (`npm run dev` or equivalent)
2. Use Playwright MCP to navigate to the feature
3. Test all user interactions
4. Verify expected outcomes
5. Take screenshots if needed for documentation

**Before committing:**
1. Test critical user flows
2. Verify no regressions in existing features
3. Document any new testing scenarios

#### Available Playwright MCP Tools

- `browser_navigate`: Navigate to URLs
- `browser_snapshot`: Capture page state (better than screenshot for testing)
- `browser_click`: Click elements
- `browser_type`: Type text into inputs
- `browser_fill_form`: Fill multiple form fields at once
- `browser_select_option`: Select dropdown options
- `browser_wait_for`: Wait for text to appear
- `browser_take_screenshot`: Visual verification
- `browser_evaluate`: Execute JavaScript on page

#### Testing Best Practices

1. **Always verify the final state**: Don't just perform actions, check that they worked
2. **Use snapshots over screenshots**: `browser_snapshot` provides better context
3. **Wait for dynamic content**: Use `browser_wait_for` for content that loads asynchronously
4. **Test error states**: Verify error messages and validation work correctly
5. **Test across different screen sizes**: Use `browser_resize` for responsive testing

### Never Do

- ❌ Deploy web changes without testing user interactions
- ❌ Rely only on unit tests for UI features
- ❌ Skip testing because "it's a small change"
- ❌ Test only the happy path (test errors and edge cases)
- ❌ Assume functionality works without visual verification

### Always Do

- ✅ Test all user-facing changes with Playwright MCP
- ✅ Simulate real user behavior (don't just check DOM)
- ✅ Verify both success and error states
- ✅ Take snapshots to confirm expected UI state
- ✅ Test critical user flows before committing
- ✅ Document complex test scenarios

---

## Database Rules

### Table Naming Convention

All database table names must use **singular form + underscore (snake_case)**.

#### Correct Examples

| Description | Table Name |
|-------------|------------|
| User | `user` |
| Order | `order` |
| Order Item | `order_item` |
| Product Category | `product_category` |
| Payment History | `payment_history` |
| User Profile | `user_profile` |
| API Access Log | `api_access_log` |

#### Incorrect Examples

| Wrong | Correct | Reason |
|-------|---------|--------|
| `users` | `user` | No plural |
| `Orders` | `order` | No uppercase |
| `orderItem` | `order_item` | No camelCase |
| `OrderItems` | `order_item` | No PascalCase or plural |
| `order-item` | `order_item` | No hyphens |

### Column Naming Convention

Columns also use **snake_case**.

```sql
-- Correct example
CREATE TABLE user (
    id BIGINT PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE,
    profile_image_url TEXT
);

CREATE TABLE order_item (
    id BIGINT PRIMARY KEY,
    order_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    unit_price DECIMAL(10, 2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Foreign Key Naming

Foreign key column: `{referenced_table}_id`

```sql
-- Examples
user_id      -- references user table
order_id     -- references order table
category_id  -- references category table
```

### Index Naming

`idx_{table}_{column}`

```sql
CREATE INDEX idx_user_email ON user(email);
CREATE INDEX idx_order_item_order_id ON order_item(order_id);
```

### ORM Model Example (SQLAlchemy)

```python
from sqlalchemy import Column, Integer, String, Boolean, DateTime, ForeignKey
from sqlalchemy.orm import relationship
from datetime import datetime

class User(Base):
    __tablename__ = "user"  # singular, snake_case
    
    id = Column(Integer, primary_key=True)
    email = Column(String(255), nullable=False, unique=True)
    is_active = Column(Boolean, default=True)
    created_at = Column(DateTime, default=datetime.utcnow)
    
    orders = relationship("Order", back_populates="user")


class Order(Base):
    __tablename__ = "order"  # singular, snake_case
    
    id = Column(Integer, primary_key=True)
    user_id = Column(Integer, ForeignKey("user.id"), nullable=False)
    total_amount = Column(Integer, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
    
    user = relationship("User", back_populates="orders")
    items = relationship("OrderItem", back_populates="order")


class OrderItem(Base):
    __tablename__ = "order_item"  # singular, snake_case
    
    id = Column(Integer, primary_key=True)
    order_id = Column(Integer, ForeignKey("order.id"), nullable=False)
    product_id = Column(Integer, ForeignKey("product.id"), nullable=False)
    quantity = Column(Integer, nullable=False)
    unit_price = Column(Integer, nullable=False)
    
    order = relationship("Order", back_populates="items")
```

### Django Model Example

```python
from django.db import models

class User(models.Model):
    class Meta:
        db_table = "user"  # singular, snake_case
    
    email = models.EmailField(unique=True)
    is_active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)


class Order(models.Model):
    class Meta:
        db_table = "order"  # singular, snake_case
    
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name="orders")
    total_amount = models.IntegerField()
    created_at = models.DateTimeField(auto_now_add=True)


class OrderItem(models.Model):
    class Meta:
        db_table = "order_item"  # singular, snake_case
    
    order = models.ForeignKey(Order, on_delete=models.CASCADE, related_name="items")
    product = models.ForeignKey("Product", on_delete=models.CASCADE)
    quantity = models.IntegerField()
    unit_price = models.IntegerField()
```

---

## DateTime Handling Rules

### Required: Timezone Awareness

All datetime fields **must** include timezone offset information.

#### Database Storage

Always use timezone-aware columns:

```sql
-- PostgreSQL
created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
updated_at TIMESTAMPTZ

-- MySQL (8.0+)
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
updated_at TIMESTAMP
```

#### API Request/Response Format

When sending datetime as string in parameters or request body, **always use ISO 8601 format with timezone offset**.

**Correct Examples:**

```json
{
  "created_at": "2024-01-15T09:30:00+09:00",
  "updated_at": "2024-01-15T14:45:30Z",
  "scheduled_time": "2024-12-25T00:00:00+00:00"
}
```

**Incorrect Examples:**

```json
// ❌ No timezone offset
{
  "created_at": "2024-01-15T09:30:00"
}

// ❌ Local datetime without offset
{
  "updated_at": "2024-01-15 14:45:30"
}
```

#### Python Implementation

```python
from datetime import datetime, timezone
from zoneinfo import ZoneInfo

# ✅ Always use timezone-aware datetime
now_utc = datetime.now(timezone.utc)
now_kst = datetime.now(ZoneInfo("Asia/Seoul"))

# ✅ Parse ISO format string
from dateutil import parser
dt = parser.isoparse("2024-01-15T09:30:00+09:00")

# ✅ Convert to ISO format for API
iso_string = now_utc.isoformat()  # "2024-01-15T09:30:00+00:00"

# ❌ Never use naive datetime
naive_dt = datetime.now()  # Wrong!
```

#### JavaScript/TypeScript Implementation

```typescript
// ✅ Always use ISO format with timezone
const now = new Date();
const isoString = now.toISOString();  // "2024-01-15T09:30:00.000Z"

// ✅ Parse ISO format
const dt = new Date("2024-01-15T09:30:00+09:00");

// ✅ Send to API
const payload = {
  created_at: new Date().toISOString()
};

// ❌ Never use locale string or custom format
const wrong = now.toLocaleString();  // Wrong!
```

#### ORM Examples

**SQLAlchemy:**

```python
from sqlalchemy import Column, DateTime
from datetime import datetime, timezone

class User(Base):
    __tablename__ = "user"

    # ✅ Always specify timezone=True
    created_at = Column(DateTime(timezone=True), default=lambda: datetime.now(timezone.utc))
    updated_at = Column(DateTime(timezone=True), onupdate=lambda: datetime.now(timezone.utc))
```

**Django:**

```python
from django.db import models
from django.utils import timezone

class User(models.Model):
    class Meta:
        db_table = "user"

    # ✅ Django DateTimeField is timezone-aware by default (if USE_TZ=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    # ✅ For manual datetime usage
    def save(self, *args, **kwargs):
        if not self.pk:
            self.created_at = timezone.now()
        super().save(*args, **kwargs)
```

### Never Do

- ❌ Store datetime without timezone information
- ❌ Use naive datetime objects in Python
- ❌ Send datetime strings without timezone offset
- ❌ Use custom datetime formats (like "YYYY-MM-DD HH:MM:SS")
- ❌ Assume local timezone

### Always Do

- ✅ Use `TIMESTAMPTZ` in PostgreSQL or equivalent
- ✅ Store all times in UTC or with explicit timezone
- ✅ Use ISO 8601 format for API communication
- ✅ Include timezone offset in all datetime strings
- ✅ Use timezone-aware datetime objects in code

---

## Version Control Rules

### Required: Never Auto-Commit or Auto-Push

**CRITICAL: Do NOT automatically commit or push code changes unless explicitly requested by the user.**

#### Commit Behavior

- ❌ **NEVER** create git commits automatically after writing code
- ❌ **NEVER** commit changes "to save progress" without explicit user request
- ❌ **NEVER** assume the user wants changes committed
- ✅ **ONLY** commit when user explicitly says "commit", "create a commit", or similar
- ✅ **ALWAYS** ask before committing if unclear

#### Push Behavior

- ❌ **NEVER** push to remote repositories automatically
- ❌ **NEVER** run `git push` without explicit user instruction
- ❌ **NEVER** suggest or offer to push unless user asks
- ✅ **ONLY** push when user explicitly says "push", "push to remote", or similar

#### What TO Do Instead

After completing code changes:
1. ✅ Inform user that changes are complete
2. ✅ Summarize what was changed
3. ✅ **STOP** - do not commit or push
4. ✅ Wait for user to explicitly request commit/push if they want it

#### Examples

**WRONG:**
```
User: "Fix the login bug"
Assistant: [Fixes bug] "I've fixed the bug and committed the changes."  ❌ BAD
```

**CORRECT:**
```
User: "Fix the login bug"
Assistant: [Fixes bug] "I've fixed the login bug in src/auth.ts:45."  ✅ GOOD
```

**CORRECT with explicit request:**
```
User: "Fix the login bug and commit it"
Assistant: [Fixes bug, then commits] "Fixed and committed."  ✅ GOOD
```

#### Why This Rule Exists

1. Users may want to review changes before committing
2. Users may want to modify commit messages
3. Users may want to stage changes selectively
4. Users have their own git workflow preferences
5. Auto-committing is intrusive and takes away user control

### Never Do

- ❌ Commit automatically after code changes
- ❌ Push to remote without explicit instruction
- ❌ Assume user wants git operations
- ❌ Use TodoWrite with commit/push tasks unless user requested it
- ❌ Suggest "shall I commit this?" after every change

### Always Do

- ✅ Complete the coding task
- ✅ Inform user of what was changed
- ✅ Let user decide when to commit/push
- ✅ Only perform git operations when explicitly asked
- ✅ Respect user's version control workflow

---

## Project State Documentation Rules

### Required: Maintain Handoff Documentation

**CRITICAL: Always maintain up-to-date state documentation for the next developer (or future you).**

Every project MUST have a **state tracking file** that gets updated after completing any work session.

#### The Roguelike Principle

Like a roguelike game that shows "Last seen: Level 5, HP: 50/100, Next: Find the Ruby Key", your project should have clear state documentation so anyone (including you tomorrow) can immediately understand:

1. **What was completed** - Recent accomplishments
2. **What needs to be done next** - Prioritized TODO list
3. **Where things are** - Code location map
4. **How to work on it** - Build/run/test instructions

#### Required File: @STATUS.md

Create and maintain `@STATUS.md` (or `STATUS.md`) in the project root:

```markdown
# Project Status

**Last Updated:** 2026-01-10 15:30 KST
**Last Author:** Claude Code / [Your Name]

## Recent Changes (Latest First)

### 2026-01-10: User Authentication Implemented
- ✅ Added JWT token generation in `src/auth/jwt.ts:15-45`
- ✅ Created login endpoint at `POST /auth/login` in `src/api/auth.ts:23`
- ✅ Database table `user` with bcrypt password hashing
- ✅ Playwright tests for signup/login flow passing
- 🔧 Known issue: Token refresh not implemented yet

### 2026-01-09: Database Schema Created
- ✅ PostgreSQL schema with singular snake_case tables
- ✅ Tables: `user`, `task`, `tag`, `task_tag`
- ✅ All datetime fields use TIMESTAMPTZ
- ✅ Migrations in `migrations/001_initial_schema.sql`

## Next Actions (Priority Order)

1. **[HIGH]** Implement JWT token refresh mechanism
   - Location: `src/auth/jwt.ts`
   - Reference: `specs/authentication.md` lines 20-35
   - Estimated: ~1-2 hours

2. **[MEDIUM]** Add task CRUD endpoints
   - Location: `src/api/tasks.ts` (new file)
   - Reference: `specs/tasks.md`
   - Depends on: Auth middleware

3. **[LOW]** Add Playwright tests for task management
   - Location: `tests/e2e/tasks.spec.ts`
   - After: Task CRUD endpoints complete

## Code Location Map

### Authentication (`src/auth/`)
- `jwt.ts:15-45` - Token generation
- `jwt.ts:47-68` - Token validation
- `middleware.ts:10-30` - Auth middleware

### API Endpoints (`src/api/`)
- `auth.ts:23` - POST /auth/login
- `auth.ts:45` - POST /auth/signup
- `tasks.ts` - [TODO] Task endpoints

### Database (`src/db/`)
- `schema.ts:10-25` - User model
- `schema.ts:27-45` - Task model
- `migrations/` - SQL migration files

### Tests
- `tests/unit/auth.test.ts` - Auth unit tests
- `tests/e2e/auth.spec.ts` - Playwright auth tests
- `tests/e2e/tasks.spec.ts` - [TODO] Task tests

## How to Work on This

### Setup
```bash
# Backend
cd backend
uv sync
uv run uvicorn main:app --reload

# Frontend
cd frontend
npm install
npm run dev

# Database
docker-compose up -d postgres
uv run alembic upgrade head
```

### Run Tests
```bash
# Unit tests
uv run pytest

# Playwright tests (requires dev server running)
npm run test:e2e
```

### Current Tech Stack
- Backend: Python 3.11, FastAPI, SQLAlchemy
- Frontend: Next.js 14, TypeScript
- Database: PostgreSQL 16
- Testing: pytest, Playwright

## Known Issues

- ❌ Token refresh endpoint not implemented (expires in 15min)
- ❌ Password reset flow missing
- ⚠️ Rate limiting not enabled (planned for v0.2.0)

## Architecture Decisions

- Using JWT instead of sessions (stateless API)
- Bcrypt cost factor: 12 (security vs performance tradeoff)
- Singular snake_case table naming (my-dev-standards)
- All datetime in UTC with timezone info (ISO 8601)

## Notes for Next Session

- Auth is working, focus on task management next
- Don't forget to add Playwright tests for each feature
- Token refresh is blocking production deployment
- Consider adding rate limiting before public release
```

#### When to Update @STATUS.md

**Update AFTER completing any work:**

1. ✅ After implementing a feature
2. ✅ After fixing a bug
3. ✅ After refactoring code
4. ✅ After making architectural decisions
5. ✅ Before ending a work session
6. ✅ After discovering blockers or issues

**Update frequency:** At minimum, once per work session.

#### What to Include

##### 1. Recent Changes (Latest First)
```markdown
### YYYY-MM-DD: Brief Title
- ✅ What was accomplished
- ✅ File locations with line numbers
- ✅ Test status
- 🔧 Known issues discovered
```

Use emojis for quick scanning:
- ✅ Completed
- 🔧 Needs work
- ❌ Broken/Not working
- ⚠️ Warning/Important
- 📝 Note/Documentation

##### 2. Next Actions (Priority Order)

```markdown
1. **[HIGH]** Specific actionable task
   - Location: Exact file paths
   - Reference: Spec/doc references
   - Estimated: Time estimate (optional)
   - Depends on: Prerequisites (if any)
```

Priority levels:
- **[CRITICAL]** - Blocking other work
- **[HIGH]** - Important, do soon
- **[MEDIUM]** - Normal priority
- **[LOW]** - Nice to have

##### 3. Code Location Map

```markdown
### Feature Area (`path/`)
- `file.ts:10-25` - Brief description of what's there
- `file.ts:30-50` - Another important section
```

**Include:**
- Key modules and their responsibilities
- Important functions with line ranges
- Configuration file locations
- Test file locations

##### 4. How to Work on This

```bash
# Setup instructions
# Build commands
# Test commands
# Common operations
```

**Must include:**
- How to set up development environment
- How to run the project
- How to run tests
- Any special commands or workflows

##### 5. Known Issues

```markdown
- ❌ Critical bugs
- ⚠️ Important warnings
- 🔧 Things that need fixing
```

Be honest about problems. Document them clearly.

#### Examples for Different Project Types

**Web Application:**
```markdown
## Code Location Map

### Frontend (`frontend/src/`)
- `components/TaskList.tsx:15-45` - Task list component
- `pages/dashboard.tsx:20` - Main dashboard page
- `hooks/useTasks.ts:10-30` - Task data fetching hook

### Backend (`backend/src/`)
- `api/tasks.py:25-60` - Task CRUD endpoints
- `models/task.py:10-25` - Task SQLAlchemy model
- `services/task_service.py:15-80` - Task business logic
```

**Library/SDK:**
```markdown
## Code Location Map

### Public API (`src/`)
- `client.ts:20-50` - Main client class
- `types.ts:10-80` - Public type definitions
- `errors.ts:5-30` - Custom error classes

### Internal (`src/internal/`)
- `http.ts:15-100` - HTTP request handling
- `auth.ts:20-60` - Authentication logic
```

**CLI Tool:**
```markdown
## Code Location Map

### Commands (`src/commands/`)
- `init.ts:10-45` - Initialize new project
- `build.ts:20-80` - Build command
- `deploy.ts:15-60` - Deploy command

### Core (`src/core/`)
- `config.ts:10-40` - Configuration parsing
- `logger.ts:5-25` - Logging utilities
```

#### Integration with Ralph Loop

For Ralph Loop projects, @STATUS.md complements existing files:

- **@fix_plan.md** - Ralph's TODO list (Ralph updates)
- **@AGENT.md** - Build/run instructions (Ralph updates)
- **@STATUS.md** - Human-readable state (You update)

Ralph can also update @STATUS.md if instructed:

```markdown
# In PROMPT.md for Ralph Loop:

After completing work:
1. Update @fix_plan.md using subagent
2. Update @AGENT.md if build/run learnings
3. Update @STATUS.md "Recent Changes" section using subagent
4. STOP
```

#### Template for New Projects

```markdown
# Project Status

**Last Updated:** YYYY-MM-DD HH:MM TZ
**Last Author:** [Name]

## Recent Changes (Latest First)

### YYYY-MM-DD: Project Initialized
- ✅ Created project structure
- ✅ Set up development environment
- 📝 See README.md for getting started

## Next Actions (Priority Order)

1. **[HIGH]** Implement core functionality
   - Location: TBD
   - Reference: specs/

## Code Location Map

[To be filled as code is written]

## How to Work on This

### Setup
```bash
# Add setup commands
```

### Run Tests
```bash
# Add test commands
```

### Current Tech Stack
- [List technologies]

## Known Issues

[None yet]

## Architecture Decisions

[Document important decisions as they're made]

## Notes for Next Session

[Add notes for future work]
```

### Never Do

- ❌ Skip updating @STATUS.md after work
- ❌ Write vague descriptions ("Fixed stuff")
- ❌ Forget to include file paths and line numbers
- ❌ Leave outdated information (always update dates)
- ❌ Use only for yourself (write for others too)

### Always Do

- ✅ Update after every work session
- ✅ Include specific file locations with line numbers
- ✅ Prioritize next actions clearly
- ✅ Document known issues honestly
- ✅ Write for someone who knows nothing about the project
- ✅ Keep "How to Work on This" up to date

### Why This Matters

**Scenario without @STATUS.md:**
```
You: "Claude, continue working on the project"
Claude: [Searches entire codebase for 5 minutes]
Claude: "I see there's auth code... should I work on that?"
You: "No, we already finished auth. Work on the task system."
Claude: "Where is the task code?"
You: "It's in... uh... let me check..."
[Wastes 10 minutes searching]
```

**Scenario with @STATUS.md:**
```
You: "Claude, continue working on the project"
Claude: [Reads @STATUS.md in 5 seconds]
Claude: "I see auth is complete. Next priority is implementing JWT token
refresh in src/auth/jwt.ts. Should I start on that?"
You: "Yes, do it."
[Immediately productive]
```

**The difference:** 10 minutes vs 5 seconds to get context.

### Integration with Version Control

@STATUS.md should be:
- ✅ Committed to git (it's part of the project)
- ✅ Updated in the same commit as code changes
- ✅ Used to write better commit messages

**Example workflow:**
```bash
# After implementing feature
# 1. Update @STATUS.md
# 2. Commit together
git add src/auth/jwt.ts @STATUS.md
git commit -m "Implement JWT token generation

- Added token generation in src/auth/jwt.ts:15-45
- 15-minute expiry, RS256 signing
- Updated @STATUS.md with next action: token refresh"
```

---

## Checklist

### When Starting a Python Project

- [ ] Use `uv init`
- [ ] Verify `pyproject.toml` configuration
- [ ] Add dependencies with `uv add`
- [ ] Create `.python-version` file

### When Starting a JavaScript/TypeScript Project

- [ ] Choose TypeScript over JavaScript (if available)
- [ ] Use `.ts` or `.tsx` file extensions
- [ ] Configure `tsconfig.json` with strict mode
- [ ] Install type definitions (`@types/*`) for dependencies
- [ ] Never use `.js` or `.jsx` when TypeScript is an option

### When Writing Code

- [ ] **CRITICAL**: Never add comments/docstrings that just restate what code does
- [ ] Ask: "Can someone understand this by reading the code?" → If yes, NO comment
- [ ] Only comment when explaining WHY (never WHAT or HOW)
- [ ] Only add docstrings when code alone doesn't tell the full story
- [ ] Prefer self-documenting code (clear names, small functions)
- [ ] Remove commented-out code (use version control instead)
- [ ] Skip docstrings for simple, obvious functions/methods/classes
- [ ] Skip test docstrings when test name is descriptive

### When Working on Web Projects

- [ ] Test all UI changes with Playwright MCP
- [ ] Simulate actual user interactions (not just check DOM)
- [ ] Verify expected behavior occurs after interactions
- [ ] Test both success and error states
- [ ] Take snapshots to confirm UI state
- [ ] Test critical user flows before committing

### When Designing Database Schema

- [ ] Table names are singular
- [ ] Table names use snake_case
- [ ] Column names use snake_case
- [ ] Foreign keys follow `{table}_id` format
- [ ] Index names follow `idx_{table}_{column}` format
- [ ] Datetime columns include timezone information

### When Handling DateTime

- [ ] All datetime fields are timezone-aware
- [ ] API requests/responses use ISO 8601 format with timezone offset
- [ ] Database columns use `TIMESTAMPTZ` or equivalent
- [ ] Python code uses timezone-aware datetime objects
- [ ] No naive datetime or custom formats

### When Completing Code Changes

- [ ] Inform user what was changed
- [ ] Summarize modifications made
- [ ] **Update @STATUS.md** with recent changes and next actions
- [ ] Include file paths with line numbers in @STATUS.md
- [ ] Update "Code Location Map" if new files/features added
- [ ] **DO NOT** commit automatically
- [ ] **DO NOT** push automatically
- [ ] Wait for explicit user request before any git operations

### When Starting a New Project

- [ ] Create @STATUS.md from template
- [ ] Document initial tech stack
- [ ] Add setup instructions
- [ ] Initialize "Next Actions" list

### When Continuing Existing Work

- [ ] **Read @STATUS.md first** to understand current state
- [ ] Check "Next Actions" for priorities
- [ ] Use "Code Location Map" to find relevant files
- [ ] Update @STATUS.md after completing work
