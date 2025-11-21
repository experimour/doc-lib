================================================================================
LEVELITH-2 MASTER DOCUMENTATION - PART 4 OF 4
================================================================================
Project: Levelith-2 - AI-First Social Resume Gamification Platform
Organization: Semour Media Group
Generated: 2025-11-21
Purpose: Development Workflow, Quality Standards, Appendices
================================================================================

[CONTINUATION FROM PART 3]

================================================================================
SECTION 8: DEVELOPMENT WORKFLOW
================================================================================

[CHUNK: workflow-ai-agent-001]
================================================================================
AI AGENT DEVELOPMENT WORKFLOW
================================================================================

MANDATORY WORKFLOW FOR AI AGENTS:

1. Read AI_AGENT_GOLDEN_RULES.md (if first time)
2. Read MANIFEST.md for project context
3. Check .aiagent.json for specific instructions
4. Generate test template: python tests/test_system.py generate <file>
5. Write tests FIRST
6. Implement feature with proper docstrings
7. Run tests: pytest (must pass with 80%+ coverage)
8. Update AI index: python dev/aiagent_navigator.py index
9. Format code: black . (Python) or prettier --write . (JS)
10. Enforce rules: python tests/test_system.py enforce
11. Commit with conventional commit message

AI AGENT COMMANDS:

```bash
# Build index and explore codebase
python dev/aiagent_navigator.py index
python dev/aiagent_navigator.py plan

# Before coding: generate test template
python tests/test_system.py generate <module_path>

# After changes: update AI index
python dev/aiagent_navigator.py index

# Query codebase
python dev/aiagent_navigator.py ask "How does authentication work?"
```

EXAMPLE WORKFLOW:

```bash
# 1. Generate test template
python tests/test_system.py generate backend/services/user_service.py

# 2. Write tests in generated file
# Edit: tests/test_user_service.py

# 3. Implement feature
# Edit: backend/services/user_service.py

# 4. Run tests
pytest tests/test_user_service.py -v

# 5. Check coverage
pytest --cov=backend.services.user_service --cov-report=term-missing

# 6. Update AI index
python dev/aiagent_navigator.py index

# 7. Format code
black backend/services/user_service.py

# 8. Enforce golden rules
python tests/test_system.py enforce

# 9. Commit
git add .
git commit -m "feat: add user service with authentication"
```

DECISION: Intelligent AI agent tooling (not static context nodes)
Rationale: Always in sync with code, zero maintenance overhead, query-driven
Status: Implemented

[SEE: CHUNK golden-rule-006 - AI agent tooling principle]
[/CHUNK]

[CHUNK: workflow-human-dev-001]
================================================================================
HUMAN DEVELOPER WORKFLOW
================================================================================

SETUP:

```bash
# Install dependencies
pip install -r backend/requirements.txt
pip install -r backend/requirements-dev.txt

# Run tests (80% min coverage)
pytest

# Start backend
cd backend && uvicorn main:app --reload

# API docs at http://localhost:8000/docs

# Start frontend
cd frontend && npm install && npm run dev
# Frontend at http://localhost:5173
```

DEVELOPMENT WORKFLOW:

1. Understand the golden rules
2. Create feature branch from develop
3. Write tests first (TDD)
4. Implement feature
5. Run full test suite
6. Update documentation if needed
7. Create pull request with clear description
8. Address code review feedback
9. Merge when CI passes and approved

BRANCHING STRATEGY:

| Branch Type  | Purpose                    | Lifetime               |
|--------------|----------------------------|------------------------|
| main         | Production-ready code      | Permanent (protected)  |
| develop      | Integration branch         | Permanent (default)    |
| feature/     | Feature branches           | Short-lived            |
| fix/         | Bug fix branches           | Short-lived            |
| claude/      | AI agent work branches     | Auto-managed           |

COMMIT MESSAGE FORMAT (Conventional Commits):

```
<type>(<scope>): <subject>

<body>

<footer>
```

Types:
- feat: New feature
- fix: Bug fix
- docs: Documentation changes
- style: Code style changes (formatting)
- refactor: Code refactoring
- test: Adding or updating tests
- chore: Maintenance tasks

Examples:
```
feat(users): add user registration endpoint

Implements POST /api/v1/users/ endpoint with:
- Username/email uniqueness validation
- Password hashing with bcrypt
- Profile data support

Closes #42

fix(naics): correct hierarchy query for level 6 codes

The parent_code extraction was incorrectly handling 6-digit codes.
Now properly extracts 4-digit parent from 6-digit codes.

Fixes #89

docs(api): update API reference with pagination examples

Added examples for pagination query parameters and response format.

test(database): increase coverage for user CRUD operations

Added edge case tests for unique constraint violations.
```

[SEE: CHUNK golden-rule-008 - CI/CD enforcement]
[/CHUNK]

[CHUNK: tests-strategies-001]
[SPLIT-CANDIDATE: testing-guide-standalone]
================================================================================
TESTING STRATEGIES
================================================================================

TESTING PYRAMID:

```
         /\
        /  \  E2E Tests (Few)
       /    \
      /------\  Integration Tests (Some)
     /        \
    /----------\  Unit Tests (Many)
   /__________  \
```

UNIT TESTS (Most Common):
- Test individual functions/methods in isolation
- Mock external dependencies
- Fast execution (<1ms per test)
- 70% of test suite

Example:
```python
def test_hash_password():
    """Test password hashing produces different hash each time."""
    password = "secure_password123"
    hash1 = hash_password(password)
    hash2 = hash_password(password)

    assert hash1 != hash2  # Different salts
    assert verify_password(password, hash1)
    assert verify_password(password, hash2)
```

INTEGRATION TESTS:
- Test multiple components working together
- Use test database (SQLite in-memory)
- Moderate execution speed (10-100ms per test)
- 25% of test suite

Example:
```python
def test_create_user_with_experiences(test_db):
    """Test creating user and associated experiences."""
    user = UserDB(username="john", email="john@example.com")
    test_db.add(user)
    test_db.commit()

    exp = ExperienceDB(
        user_id=user.id,
        title="Software Engineer",
        category="workplace",
        experience_type="full_time"
    )
    test_db.add(exp)
    test_db.commit()

    # Verify relationship
    assert len(user.experiences) == 1
    assert user.experiences[0].title == "Software Engineer"
```

E2E TESTS (Fewest):
- Test complete user workflows
- Real browser automation (Playwright)
- Slow execution (seconds per test)
- 5% of test suite
- Currently: Manual testing guide provided

TEST ORGANIZATION:

```
tests/
├── unit/
│   ├── test_models.py
│   ├── test_services.py
│   └── test_utils.py
├── integration/
│   ├── test_api.py
│   ├── test_database.py
│   └── test_naics_system.py
└── e2e/
    ├── test_user_registration.py
    └── test_experience_creation.py
```

COVERAGE REQUIREMENTS:

- Overall: ≥80%
- Services: ≥85%
- Models: ≥90%
- Utilities: ≥95%

CHECK COVERAGE:

```bash
# Terminal report
pytest --cov=backend --cov-report=term-missing

# HTML report
pytest --cov=backend --cov-report=html
open htmlcov/index.html

# Fail if below threshold
pytest --cov=backend --cov-fail-under=80
```

WHAT TO TEST:

Priority 1 (Must Test):
- Business logic (services)
- Database operations (CRUD)
- API endpoints (request/response)
- Input validation
- Error handling
- Edge cases

Priority 2 (Should Test):
- Utility functions
- Data transformations
- Complex calculations

Priority 3 (Optional):
- Simple getters/setters
- Configuration files
- Third-party library wrappers

WHAT NOT TO TEST:
- Third-party libraries themselves
- Framework code (FastAPI, SQLAlchemy)
- Simple pass-through functions

MOCKING STRATEGIES:

Mock External Services:
```python
from unittest.mock import Mock, patch

@patch('backend.services.email_service.send_email')
def test_user_registration_sends_email(mock_send_email, test_db):
    """Test that user registration sends verification email."""
    mock_send_email.return_value = True

    user = create_user(
        username="john",
        email="john@example.com",
        password="password"
    )

    mock_send_email.assert_called_once_with(
        to=user.email,
        subject="Verify your account",
        template="verification"
    )
```

Mock Database:
```python
def test_with_mock_db():
    mock_db = Mock()
    mock_user = UserDB(username="test", email="test@example.com")
    mock_db.query().filter().first.return_value = mock_user

    result = mock_db.query(UserDB).filter(UserDB.id == "123").first()
    assert result.username == "test"
```

TEST FIXTURES:

```python
# conftest.py
import pytest

@pytest.fixture(scope="function")
def test_db():
    """Provide clean database for each test."""
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    SessionLocal = sessionmaker(bind=engine)
    db = SessionLocal()
    yield db
    db.close()
    Base.metadata.drop_all(engine)

@pytest.fixture
def create_user(test_db):
    """Factory for creating test users."""
    def _create(**kwargs):
        defaults = {"username": "test", "email": "test@example.com"}
        defaults.update(kwargs)
        user = UserDB(**defaults)
        test_db.add(user)
        test_db.commit()
        return user
    return _create
```

[SEE: CHUNK database-testing-001 - Database-specific testing]
[SEE: CHUNK golden-rule-001 - Test-first development]
[SEE: CHUNK golden-rule-007 - 80% coverage minimum]
[/CHUNK]

================================================================================
SECTION 9: QUALITY STANDARDS
================================================================================

[CHUNK: quality-metrics-001]
================================================================================
QUALITY STANDARDS AND METRICS
================================================================================

CODE QUALITY METRICS:

| Metric                    | Target | Tool        |
|---------------------------|--------|-------------|
| Test Coverage             | ≥80%   | pytest-cov  |
| Complexity (Cyclomatic)   | ≤10    | radon       |
| Type Hint Coverage        | ≥90%   | mypy        |
| Documentation Coverage    | ≥90%   | interrogate |
| Security Issues           | 0      | bandit      |

PERFORMANCE STANDARDS:
- API Response Time: <200ms p95
- Page Load Time: <2s on 3G
- Database Queries: No N+1 queries
- Bundle Size: Frontend <500KB gzipped

SECURITY STANDARDS:
- OWASP Top 10: Zero vulnerabilities
- Dependencies: Updated monthly, zero high/critical CVEs
- Secrets: Never in code, always in environment
- Input Validation: All user input validated and sanitized

PROJECT STATUS:

Overall Grade: B+ (85/100)

| Component          | Status           | Coverage/Completion |
|--------------------|------------------|---------------------|
| Backend Core       | Functional       | 85%                 |
| Test Coverage      | Below target     | 75% (need 80%)      |
| Authentication     | Incomplete       | JWT partially done  |
| Admin Dashboard    | Complete         | 95%                 |
| Main Frontend      | Not started      | 0%                  |
| Documentation      | Excellent        | 95%                 |

Path to Production: 60-100 hours

CRITICAL ISSUES BEFORE PRODUCTION:

1. Add service tests (5-6 hrs) - 1,550 lines untested
2. Refactor API routes (3-4 hrs) - Use service layer properly
3. Complete JWT auth (2-3 hrs) - Token generation missing
4. Build main frontend (40-80 hrs) - User-facing app

[SEE: CHUNK project-status-001 - Detailed project status]
[SEE: CHUNK project-roadmap-001 - Development roadmap]
[/CHUNK]

[CHUNK: quality-conventions-python-001]
================================================================================
PYTHON CODING CONVENTIONS
================================================================================

DOCSTRINGS (Google Style):

```python
def process_data(data: dict, validate: bool = True) -> dict:
    """
    Process raw data and return validated results.

    Args:
        data: Raw input data dictionary
        validate: Whether to validate before processing

    Returns:
        Processed data dictionary with validation results

    Raises:
        ValueError: If data is invalid and validate=True
    """
    pass
```

TYPE HINTS:

```python
from typing import List, Optional, Dict, Any

def get_users(
    db: Session,
    active_only: bool = False,
    limit: Optional[int] = None
) -> List[UserDB]:
    """Retrieve users from database."""
    query = db.query(UserDB)
    if active_only:
        query = query.filter(UserDB.is_active == True)
    if limit:
        query = query.limit(limit)
    return query.all()
```

ERROR HANDLING:

```python
try:
    result = risky_operation()
except SpecificError as e:
    logger.error(f"Operation failed: {e}")
    raise
except Exception as e:
    logger.exception("Unexpected error occurred")
    raise RuntimeError("Internal error") from e
```

NAMING CONVENTIONS:

```python
# Variables and functions: snake_case
user_name = "john"
def calculate_total():
    pass

# Classes: PascalCase
class UserService:
    pass

# Constants: UPPER_SNAKE_CASE
MAX_RETRY_COUNT = 3
DEFAULT_PAGE_SIZE = 50

# Private: _leading_underscore
def _internal_helper():
    pass
```

PATTERNS TO FOLLOW:

1. Dependency Injection:
```python
class UserService:
    def __init__(self, db: Session, cache: Cache):
        self.db = db
        self.cache = cache
```

2. Factory Pattern:
```python
def create_test_user(**overrides):
    defaults = {"email": "test@example.com", "name": "Test User"}
    return UserDB(**{**defaults, **overrides})
```

3. Repository Pattern:
```python
class UserRepository:
    def find_by_email(self, email: str) -> Optional[UserDB]:
        return self.db.query(UserDB).filter(UserDB.email == email).first()
```

ANTI-PATTERNS TO AVOID:

1. God Objects (classes doing too much)
2. Hidden Dependencies (implicit global state)
3. Silent Failures (catching exceptions without handling)
4. Hardcoded Values (use constants or config)

[SEE: CHUNK golden-rule-002 - Explicit over implicit]
[SEE: CHUNK golden-rule-003 - Self-documenting code]
[/CHUNK]

[CHUNK: quality-conventions-typescript-001]
================================================================================
TYPESCRIPT CODING CONVENTIONS
================================================================================

INTERFACES FOR PROPS:

```typescript
interface UserCardProps {
  user: User;
  onEdit?: (user: User) => void;
  showActions?: boolean;
}
```

FUNCTIONAL COMPONENTS WITH TYPES:

```typescript
export const UserCard: React.FC<UserCardProps> = ({
  user,
  onEdit,
  showActions = true
}) => {
  // Component logic
  return (
    <div>
      <h3>{user.username}</h3>
      {showActions && <button onClick={() => onEdit?.(user)}>Edit</button>}
    </div>
  );
};
```

CUSTOM HOOKS:

```typescript
function useAuth() {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(false);

  const login = async (credentials: LoginCredentials) => {
    setIsLoading(true);
    try {
      const user = await authApi.login(credentials);
      setUser(user);
    } finally {
      setIsLoading(false);
    }
  };

  return { user, isLoading, login };
}
```

TYPE DEFINITIONS:

```typescript
// User types
export interface User {
  id: string;
  username: string;
  email: string;
  isActive: boolean;
  isVerified: boolean;
  profile?: UserProfile;
  createdAt: string;
  updatedAt: string;
}

export interface UserProfile {
  firstName?: string;
  lastName?: string;
  bio?: string;
  avatarUrl?: string;
  location?: string;
}

// API types
export interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  pageSize: number;
  totalPages: number;
}
```

NAMING CONVENTIONS:

```typescript
// Variables and functions: camelCase
const userName = "john";
function calculateTotal() {}

// Interfaces and Types: PascalCase
interface UserData {}
type UserId = string;

// Constants: UPPER_SNAKE_CASE
const MAX_RETRY_COUNT = 3;
const DEFAULT_PAGE_SIZE = 50;

// React Components: PascalCase
function UserCard() {}
const ProfilePage = () => {};
```

BARREL EXPORTS:

```typescript
// features/users/index.ts
export * from './types/user.types';
export * from './api/users.api';
export * from './hooks/useUsers';
export * from './components/UsersTable';

// Usage elsewhere
import { User, useUsers, UsersTable } from '@/admin/features/users';
```

[SEE: CHUNK golden-rule-002 - Explicit over implicit]
[SEE: CHUNK ui-overview-001 - Frontend architecture]
[/CHUNK]

================================================================================
SECTION 10: PROJECT STATUS & ROADMAP
================================================================================

[CHUNK: project-status-001]
================================================================================
CURRENT PROJECT STATUS
================================================================================

CURRENT PHASE: Foundation & Critical Fixes (Phase 1/2)

COMPLETED:
- AI agent navigation system
- Golden rules framework
- Test system with enforcement
- CI/CD pipelines
- Documentation structure
- Production infrastructure (Render + PostgreSQL)
- Backend API deployed to levelith.online
- NAICS 2022 database integration
- Admin Dashboard Infrastructure (Phase 0 & 1)
- Users Feature (Phase 2)
- Global API transformation layer (snake_case ↔ camelCase)

IN PROGRESS:
- PRIORITY 0: AdminDashboardRefactorv2 (Plan C) - Complete rebuild
  Status: Phase 0-2 complete, continuing with remaining phases
  Timeline: 20-30 hours across 8 phases

- PRIORITY 1.1: Add service layer tests (target 80% coverage)
- PRIORITY 1.2: Refactor API to use service layer properly
- PRIORITY 1.3: Complete JWT authentication implementation
- PRIORITY 1.4: Standardize model usage (remove domain model duplication)

METRICS:

Current Test Coverage: 75% (target: 80%)
Documentation Coverage: 95%
Backend Core: 85% functional
Admin Dashboard: 95% complete
Main Frontend: 0% (not started)

Path to Production: 60-100 hours remaining

KNOWN LIMITATIONS:

Backend:
- List endpoints lack pagination metadata
- No search functionality in some endpoints
- No filtering capabilities in some endpoints
- JWT token generation incomplete

Frontend:
- Main user-facing app not started
- User create/edit forms deferred
- Component tests not configured (Vitest issue)
- E2E tests not implemented

Deferred Items:
- User create/edit forms (admin authentication needed first)
- Component tests (Vitest configuration issue)
- E2E tests (manual testing guide provided)
- API response transformation for all edge cases
- Storybook stories
- Virtualization for large lists

[SEE: CHUNK project-roadmap-001 - Future development roadmap]
[SEE: CHUNK project-limitations-001 - Detailed limitations]
[/CHUNK]

[CHUNK: project-roadmap-001]
================================================================================
PROJECT DEVELOPMENT ROADMAP
================================================================================

IMMEDIATE PRIORITIES (Next 4-6 Weeks):

Week 1: Foundation (DONE)
- Phase 0: Setup (TypeScript/Tailwind/React Query)
- Phase 1: Core Infrastructure (API client, components)

Week 2: Core Features
- Phase 2: Users Feature (Complete CRUD) - DONE
- Phase 3: Experiences Feature (All 9 types with forms)

Week 3: Polish & Deploy
- Phase 4: NAICS Feature (Tag/category editing)
- Phase 5: Dashboard (Analytics and charts)
- Phase 6: Settings (Seed database form)
- Phase 7: Routing (/admin prefix, route guards)
- Phase 8: Production (Error handling, deployment)

Week 4-6: Post-Refactor
- User registration page (public-facing)
- Profile viewing and editing
- Advanced search and filtering
- Gamification UI elements

MEDIUM-TERM (1-3 Months):
- Experience timeline visualization
- Search and browse functionality
- Gamification UI (points, levels, achievements)
- Performance optimization
- Mobile-responsive design
- E2E testing suite

LONG-TERM VISION:

Social & Collaboration Features:
- User "Guilds" - groups emulating fiscal entities
- Asset valuations and tracking
- "Parties" - grouped experience objects
- Social networking and connections
- Profile sharing and discovery

Technical Improvements:
- Multi-language support
- Advanced AI agent capabilities
- Real-time collaboration features
- Performance monitoring and analytics
- Mobile app (React Native)

[SEE: CHUNK project-status-001 - Current status]
[/CHUNK]

[CHUNK: project-limitations-001]
================================================================================
PROJECT LIMITATIONS
================================================================================

CURRENT LIMITATIONS:

Backend API Limitations:
- List endpoint lacks pagination metadata
- No search functionality in backend for users
- No filtering capabilities for some endpoints
- No sorting support in backend
- JWT token generation incomplete (returns placeholder)
- Response uses snake_case (handled by transformation layer)

Frontend Limitations:
- Main user-facing app not started (0% complete)
- User create/edit forms deferred (authentication needed first)
- Component tests not configured (Vitest issue)
- E2E tests not implemented (manual guide provided)

Technical Debt:
- Service layer tests below 80% coverage
- API routes should use service layer more consistently
- Domain model duplication (some overlap with ORM models)
- Some hardcoded values need ONETRUTH migration

NOT YET IMPLEMENTED:
- API response transformation layer for all edge cases
- Integration tests for full workflows
- Storybook stories for components
- Error boundary integration in all pages
- Loading skeleton for better UX
- Virtualization for large lists (performance)
- Dark mode theme support
- Multi-language i18n support

WORKAROUNDS IN PLACE:
- Frontend expects proper paginated responses (transformation layer handles)
- API client has transformation layer for snake_case/camelCase
- Manual testing guide provided instead of E2E tests
- Admin dashboard complete, user forms can be added later

DEFERRED FOR LATER:
1. User Create/Edit Forms (not critical for MVP)
2. Component Tests (Vitest configuration issue)
3. E2E Tests (manual testing sufficient for now)
4. Storybook (nice to have, not required)

[SEE: CHUNK project-status-001 - Overall project status]
[/CHUNK]

================================================================================
APPENDICES
================================================================================

[CHUNK: backlog-summary-001]
================================================================================
CONSOLIDATED BACKLOG SUMMARY
================================================================================

This section consolidates all TODO items, incomplete features, deferred work, and future enhancements from all source documents.

FORMAT: {category}: {item} - Source: {file}

TODO_ITEMS:

- TODO: Implement JWT token generation in login endpoint
  Source: T_LEVELITH_TRUTH_3.txt (api-status)
  Priority: High
  Estimate: 2-3 hours

- TODO: Add service layer tests to reach 80% coverage
  Source: T_LEVELITH_TRUTH_1.txt (project-status)
  Priority: High
  Estimate: 5-6 hours
  Details: 1,550 lines of service layer code untested

- TODO: Refactor API routes to use service layer properly
  Source: T_LEVELITH_TRUTH_1.txt (project-status)
  Priority: Medium
  Estimate: 3-4 hours

NOT_YET_IMPLEMENTED:

- Enhanced Users List Endpoint with pagination metadata
  Source: T_LEVELITH_TRUTH_3.txt (api-required-enhancements)
  Priority: High
  Details: Needs search, filtering, sorting, pagination metadata

- User Statistics Endpoint
  Source: T_LEVELITH_TRUTH_3.txt (api-required-enhancements)
  Priority: Medium
  Details: GET /api/v1/users/stats

- Bulk Operations Endpoint
  Source: T_LEVELITH_TRUTH_3.txt (api-required-enhancements)
  Priority: Medium
  Details: POST /api/v1/users/bulk-delete

- User Create/Edit Forms
  Source: T_LEVELITH_TRUTH_3.txt (known-limitations)
  Priority: Low (deferred)
  Details: Admin dashboard forms for user management
  Blocker: Admin authentication needed first

- Component Tests
  Source: T_LEVELITH_TRUTH_3.txt (known-limitations)
  Priority: Low (deferred)
  Details: Vitest not properly configured
  Workaround: Manual testing guide provided

- E2E Tests
  Source: T_LEVELITH_TRUTH_3.txt (known-limitations)
  Priority: Low (deferred)
  Details: Playwright tests for full user flow
  Workaround: Manual testing guide provided

DEFERRED_FEATURES:

- Main User-Facing Frontend
  Source: T_LEVELITH_TRUTH_1.txt (project-status)
  Priority: High (next phase)
  Estimate: 40-80 hours
  Details: Landing pages, user registration, profile viewing

- Gamification UI
  Source: T_LEVELITH_TRUTH_1.txt (project-vision)
  Priority: Medium (long-term)
  Details: Points, achievements, levels, leaderboards

- Social Features
  Source: T_LEVELITH_TRUTH_1.txt (project-vision)
  Priority: Medium (long-term)
  Details: Guilds, parties, connections, profile sharing

- Mobile App
  Source: T_LEVELITH_TRUTH_1.txt (tech-stack)
  Priority: Low (future)
  Technology: React Native
  Details: Code sharing with web frontend

- Real-time Collaboration
  Source: T_LEVELITH_TRUTH_1.txt (roadmap)
  Priority: Low (future)
  Technology: WebSockets
  Details: Live updates, notifications

INCOMPLETE_FEATURES:

- JWT Authentication
  Status: Partially implemented
  Source: T_LEVELITH_TRUTH_3.txt (api-login)
  Missing: Token generation logic
  Present: Credential validation, active status check

- Password Update
  Status: Not implemented
  Source: T_LEVELITH_TRUTH_3.txt (api-users)
  Details: PATCH /users/:id doesn't support password updates
  Reason: Needs separate endpoint for security

- Username Update
  Status: Not implemented
  Source: T_LEVELITH_TRUTH_3.txt (api-users)
  Details: Not in UserUpdate schema
  Reason: Design decision (usernames should be immutable?)

FUTURE_ENHANCEMENTS:

Performance:
- Redis caching for session management
- Elasticsearch for advanced search
- CDN for global content delivery
- Docker + Kubernetes for scaling
- Virtual scrolling for large lists

Security:
- Two-factor authentication (2FA)
- OAuth integration (Google, GitHub)
- Security headers (CSP, HSTS)
- Rate limiting per user/IP

UX:
- Dark mode theme support
- Multi-language i18n support
- Loading skeletons
- Error boundaries on all pages
- Toast notifications
- Keyboard shortcuts

Developer Experience:
- Storybook for component library
- API response transformation for all edge cases
- Component tests (once Vitest configured)
- E2E test suite (Playwright)
- Performance monitoring (Sentry)

[/CHUNK]

[CHUNK: doc-canonical-mapping-001]
================================================================================
CANONICAL MAPPING SCHEMA
================================================================================

This section provides canonical term mappings for harmonizing column names and field names across different sources.

PURPOSE: Enable machine unification while preserving original content untouched.

FORMAT: {original_term: canonical_term}

USER FIELDS:

{
  "user_name": "username",
  "user_email": "email",
  "email_address": "email",
  "user_id": "id",
  "userid": "id",
  "is_active_user": "is_active",
  "active": "is_active",
  "verified": "is_verified",
  "email_verified": "is_verified",
  "profile": "profile_data",
  "user_profile": "profile_data",
  "created": "created_at",
  "date_created": "created_at",
  "updated": "updated_at",
  "date_updated": "updated_at",
  "last_logged_in": "last_login",
  "last_login_date": "last_login"
}

EXPERIENCE FIELDS:

{
  "experience_id": "id",
  "exp_id": "id",
  "owner_id": "user_id",
  "user": "user_id",
  "experience_title": "title",
  "name": "title",
  "description_text": "description",
  "details": "description",
  "naics": "naics_code",
  "industry_code": "naics_code",
  "type": "experience_type",
  "exp_type": "experience_type",
  "cat": "category",
  "exp_category": "category",
  "start": "start_date",
  "begin_date": "start_date",
  "end": "end_date",
  "finish_date": "end_date",
  "current": "is_current",
  "active": "is_current",
  "org": "organization",
  "company": "organization",
  "place": "location",
  "city": "location",
  "type_data": "type_specific_data",
  "specific_data": "type_specific_data",
  "metadata": "experience_metadata"
}

NAICS FIELDS:

{
  "naics_code": "code",
  "industry_code": "code",
  "naics_title": "title",
  "industry_title": "title",
  "name": "title",
  "desc": "description",
  "industry_description": "description",
  "hierarchy_level": "level",
  "tier": "level",
  "parent": "parent_code",
  "parent_naics": "parent_code",
  "industry_sector": "sector",
  "industry_subsector": "subsector",
  "group": "industry_group",
  "detail": "industry_detail",
  "cat": "category",
  "industry_category": "category",
  "size_standard": "sba_size_standard",
  "sba_standard": "sba_size_standard",
  "search_terms": "keywords",
  "terms": "keywords",
  "alternate_names": "aliases",
  "synonyms": "aliases",
  "is_active_code": "is_active",
  "active": "is_active",
  "naics_year": "year",
  "version": "year"
}

PAGINATION FIELDS:

{
  "items": "data",
  "results": "data",
  "records": "data",
  "count": "total",
  "total_count": "total",
  "total_items": "total",
  "page_number": "page",
  "current_page": "page",
  "per_page": "page_size",
  "limit": "page_size",
  "page_count": "total_pages",
  "num_pages": "total_pages"
}

SNAKE_CASE TO CAMELCASE:

{
  "user_name": "userName",
  "is_active": "isActive",
  "is_verified": "isVerified",
  "profile_data": "profileData",
  "created_at": "createdAt",
  "updated_at": "updatedAt",
  "last_login": "lastLogin",
  "experience_type": "experienceType",
  "type_specific_data": "typeSpecificData",
  "experience_metadata": "experienceMetadata",
  "naics_code": "naicsCode",
  "parent_code": "parentCode",
  "industry_group": "industryGroup",
  "industry_detail": "industryDetail",
  "sba_size_standard": "sbaSizeStandard",
  "sba_source": "sbaSource",
  "custom_category": "customCategory",
  "admin_notes": "adminNotes",
  "is_current": "isCurrent",
  "page_size": "pageSize",
  "total_pages": "totalPages"
}

NOTE: This mapping is applied automatically by the frontend transformation layer (lib/transformers.ts) for API requests/responses.

[/CHUNK]

[CHUNK: doc-changelog-001]
================================================================================
DOCUMENT CHANGELOG
================================================================================

This section consolidates all version history, updates, and changes in reverse chronological order.

FORMAT: [YYYY-MM-DD] - {Description} - Source: {file}

[2025-11-21] - Master Documentation Generated
- Consolidated 3 truth documents (5,237 lines) into unified masterfile
- Created 4-part structure for machine parseability
- Implemented all core mandates (M1-M15)
- Source: Master consolidation process

[2025-11-20] - FormButton Component Fix
- Created frontend/src/admin/components/forms/FormButton.jsx (65 lines)
- Resolved deployment blocker (missing component)
- Three variants: primary, secondary, danger
- ONETRUTH theme integration
- Source: T_LEVELITH_TRUTH_3.txt (formbutton-component)

[2025-11-20] - Users Feature Complete
- Global API transformation layer (188 lines)
- Transformation tests (309 lines, 31 tests)
- Users admin pages (UsersPage, UserDetailPage)
- Full CRUD with React Query hooks
- Data flow architecture documented
- Source: T_LEVELITH_TRUTH_3.txt

[2025-11-20] - NAICS CRUD Complete
- Database migration: Added admin fields (tags, custom_category, admin_notes)
- Repository layer: UPDATE, DELETE, paginated search
- Service layer: Business logic for admin operations
- API endpoints: PATCH /naics/{code}, DELETE /naics/{code}
- Frontend: NAICSCodes.jsx rewrite (244→558 lines)
- Server-side pagination (50 items/page)
- Edit/Delete modals
- Source: T_LEVELITH_TRUTH_1.txt (recent-updates)

[2025-11-19] - Admin Dashboard Fixes
- Modal component: Complete rewrite using ONETRUTH styling
- White screen bug fixed (backdrop and overlay z-index)
- AdminLayout: Full ONETRUTH styling
- DataSourceSwitcher: Removed className, added inline styles
- Experiences filters: Added category and type dropdowns
- Source: T_LEVELITH_TRUTH_1.txt (recent-updates)

[2025-11-15] - Admin Dashboard Phase 0-2 Complete
- Phase 0: Setup (TypeScript, Tailwind, React Query)
- Phase 1: Core Infrastructure (API client, components, hooks)
- Phase 2: Users feature (CRUD, pagination, search, filters)
- 60+ files created across features/users
- Statistics: ~2,740 lines implementation
- Source: T_LEVELITH_TRUTH_1.txt

[2025-11-10] - NAICS System Complete
- Domain layer (420 LOC)
- Repository layer (760 LOC combined)
- Service layer (360 LOC)
- API layer (560 LOC)
- 197 tests (98% coverage)
- Enhanced search with relevance scoring
- Source: T_LEVELITH_TRUTH_1.txt (naics-system)

[2025-11-05] - Database Schema Finalized
- Users table with profile_data JSONB
- Experiences table with single table inheritance
- NAICS codes table with 22 columns
- Denormalized hierarchy for performance
- Admin fields for dashboard editing
- Source: T_LEVELITH_TRUTH_1.txt, T_LEVELITH_TRUTH_2.txt

[2025-11-01] - Project Foundation
- Project vision and goals defined
- 10 Golden Rules established
- Architecture philosophy documented
- Technology stack selected
- Deployment on Render.com configured
- Source: T_LEVELITH_TRUTH_1.txt

[2025-10-25] - Initial Project Setup
- Repository created
- Backend scaffolding (FastAPI)
- Frontend scaffolding (React + TypeScript)
- PostgreSQL database provisioned
- CI/CD pipeline configured
- Source: Inferred from metadata

[/CHUNK]

[CHUNK: doc-cross-ref-index-001]
================================================================================
MASTER CROSS-REFERENCE INDEX
================================================================================

This index maps all cross-references used throughout the masterfile for quick navigation.

PART 1: METADATA, PROJECT FOUNDATION, TECHNOLOGY

doc-metadata-001: Document metadata
doc-provenance-001: Source file mappings
doc-toc-001: Table of contents

project-vision-001: Project vision and goals
project-architecture-001: Architecture philosophy and principles

golden-rule-001: Test-First Development
golden-rule-002: Explicit Over Implicit
golden-rule-003: Self-Documenting Code
golden-rule-004: Security By Design
golden-rule-005: Scalability From Day One
golden-rule-006: AI Agent Tooling
golden-rule-007: 80% Test Coverage Minimum
golden-rule-008: Golden Rules Enforced via CI/CD
golden-rule-009: Monorepo Structure
golden-rule-010: Single ONETRUTH Branding

config-tech-stack-backend-001: Backend technology stack
config-tech-stack-frontend-001: Frontend technology stack
project-structure-001: Project directory structure
config-deployment-001: Deployment guide (Render.com)

domain-concepts-001: Domain-specific context
config-onetruth-001: ONETRUTH branding system

PART 2: DATABASE LAYER, NAICS SYSTEM

schema-overview-001: Database overview
schema-users-001: Users table schema
schema-experiences-001: Experiences table schema
schema-naics-001: NAICS codes table schema

database-setup-001: Database setup and configuration
database-usage-001: Database usage patterns
database-testing-001: Database testing strategies

logic-naics-overview-001: NAICS system implementation
logic-naics-import-001: NAICS data import procedures
logic-naics-search-001: Enhanced search features

PART 3: API LAYER, FRONTEND ARCHITECTURE

api-overview-001: API overview and authentication
api-auth-register-001: User registration endpoint
api-auth-login-001: User login endpoint
api-users-001: Users API endpoints
api-naics-001: NAICS API endpoints
api-health-001: Health check endpoints
api-statistics-001: Platform statistics endpoint
api-errors-001: Error handling
api-pagination-001: Pagination format

ui-overview-001: Frontend architecture overview
ui-transformation-layer-001: Global API transformation layer
ui-admin-overview-001: Admin dashboard implementation
ui-users-feature-001: Users feature complete implementation
ui-data-flow-001: Frontend data flow architecture

PART 4: DEVELOPMENT, QUALITY, APPENDICES

workflow-ai-agent-001: AI agent development workflow
workflow-human-dev-001: Human developer workflow

tests-strategies-001: Testing strategies and pyramid

quality-metrics-001: Quality standards and metrics
quality-conventions-python-001: Python coding conventions
quality-conventions-typescript-001: TypeScript coding conventions

project-status-001: Current project status
project-roadmap-001: Development roadmap
project-limitations-001: Project limitations

backlog-summary-001: Consolidated backlog
doc-canonical-mapping-001: Canonical term mappings
doc-changelog-001: Document changelog
doc-cross-ref-index-001: This cross-reference index

[/CHUNK]

================================================================================
END OF LEVELITH-2 MASTER DOCUMENTATION
================================================================================

SUMMARY OF PARTS:

PART 1: Metadata, TOC, Project Foundation, Technology Stack
  - 20 chunks covering vision, golden rules, tech stack, structure, deployment

PART 2: Database Layer, NAICS System
  - 12 chunks covering schemas, usage, testing, NAICS implementation

PART 3: API Layer, Frontend Architecture
  - 12 chunks covering API endpoints, authentication, frontend, transformation

PART 4: Development Workflow, Quality Standards, Appendices
  - 11 chunks covering workflows, testing, quality, status, backlog, changelog

TOTAL CHUNKS: 55 fine-grained chunks
TOTAL PARTS: 4 files for efficient processing
TOTAL SOURCE LINES: ~5,237 lines consolidated
TOTAL OUTPUT LINES: ~5,500 lines (consolidated, deduplicated, structured)

ALL MANDATES IMPLEMENTED:
[x] M1: Single canonical version (deduplicated)
[x] M3: Changelog preserved (doc-changelog-001)
[x] M4: Fine-grained chunking (55 chunks)
[x] M5: Machine-addressable cross-references ([SEE: CHUNK ...])
[x] M6: Split hints ([SPLIT-CANDIDATE: ...])
[x] M7: Provenance tracking (doc-provenance-001)
[x] M9: Consolidated metadata (doc-metadata-001)
[x] M10: Machine-parseable formatting
[x] M11: Complete code inclusion (full code preserved)
[x] M13: Backlog consolidation (backlog-summary-001)
[x] M14: Table preservation (original structures kept)
[x] M15: 100% semantic preservation (all data intact)

DEFERRED (as agreed):
[ ] M2: Educational integration (minimal, contextual)
[ ] M8: Deprecated content (none identified)
[ ] M12: ASCII → Mermaid conversion (preserved ASCII as-is)

AUDIENCE: AI agents and human developers (equal priority)
PURPOSE: Authoritative reference and master blueprint for documentation collection

================================================================================
