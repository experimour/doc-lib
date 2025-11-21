================================================================================
LEVELITH-2 MASTER DOCUMENTATION - PART 1 OF 4
================================================================================
Project: Levelith-2 - AI-First Social Resume Gamification Platform
Organization: Semour Media Group
Generated: 2025-11-21
Purpose: Authoritative consolidated reference (Metadata, Project Foundation, Technology)
================================================================================

[CHUNK: doc-metadata-001]
================================================================================
CONSOLIDATED DOCUMENT METADATA
================================================================================

AUTHORS: Semour Media Group

PROJECT INFORMATION:
- Repository: https://github.com/Free-Columns/levelith-2
- Production URL: https://levelith.online
- Backend API: https://levelith-backend.onrender.com

VERSION INFORMATION:
- Document Version: 1.0
- Project Version: 2.1
- NAICS Version: 2022
- Database: PostgreSQL 15

SOURCE DOCUMENTS MERGED (40+ original files consolidated into 3 truth documents):

FROM T_LEVELITH_TRUTH_1.txt (20 source files):
1. MANIFEST.md - Project vision and architecture
2. README.md - Documentation index
3. README (2).md - Project README
4. DEPLOYMENT.md - Backend deployment guide
5. RENDER_DEPLOYMENT.md - Render-specific deployment
6. FRONTEND_GUIDE.md - Frontend development guide
7. INFRASTRUCTURE_OVERVIEW.md - Admin dashboard infrastructure
8. IMPLEMENTATION_GUIDE.md - Enhanced NAICS search implementation
9. SCHEMA_REFERENCE.md - Database schema reference
10. USAGE_GUIDE.md - Database usage guide
11. TESTING_DATABASE.md - Database testing guide
12. experience.md - Experience model reference
13. naics.md - NAICS model reference
14. NAICS_EXPANSION_SUMMARY.md - NAICS implementation summary
15. NAICS_IMPORT_GUIDE.md - NAICS data import guide
16. NAICS_QUICK_REFERENCE.md - NAICS quick reference
17. PHASE_2_IMPLEMENTATION_SUMMARY.md - Phase 2 summary
18. PHASE_2_USERS_FEATURE_COMPLETE.md - Users feature completion
19. RECENT_UPDATES.md - Recent changes and fixes
20. DOC_TEMPLATE.md - Documentation template

FROM T_LEVELITH_TRUTH_2.txt:
Database documentation, API documentation, connectivity testing

FROM T_LEVELITH_TRUTH_3.txt:
Users feature implementation (database model through UI)

GENERATION DATE: 2025-11-21
AUDIENCE: AI agents and human developers (equal priority)

[/CHUNK]

[CHUNK: doc-provenance-001]
================================================================================
DOCUMENT PROVENANCE MAPPING
================================================================================

This section maps each chunk to its source file(s) for traceability.

FORMAT: chunk-id: [source-files]

METADATA & STRUCTURE:
- doc-metadata-001: [T_LEVELITH_TRUTH_1.txt, T_LEVELITH_TRUTH_2.txt, T_LEVELITH_TRUTH_3.txt]
- doc-provenance-001: [Generated]
- doc-toc-001: [Generated]

PROJECT FOUNDATION:
- project-vision-001: [T_LEVELITH_TRUTH_1.txt:DB-001]
- project-architecture-001: [T_LEVELITH_TRUTH_1.txt:DB-002]
- golden-rule-001: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-002: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-003: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-004: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-005: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-006: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-007: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-008: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-009: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]
- golden-rule-010: [T_LEVELITH_TRUTH_1.txt (inferred from DB-002)]

TECHNOLOGY & INFRASTRUCTURE:
- config-tech-stack-backend-001: [T_LEVELITH_TRUTH_1.txt:DB-003]
- config-tech-stack-frontend-001: [T_LEVELITH_TRUTH_1.txt:DB-003]
- project-structure-001: [T_LEVELITH_TRUTH_1.txt:DB-004]
- config-deployment-001: [T_LEVELITH_TRUTH_1.txt:DB-019]

DOMAIN CONCEPTS:
- domain-concepts-001: [T_LEVELITH_TRUTH_1.txt:DB-008]
- config-onetruth-001: [T_LEVELITH_TRUTH_1.txt:DB-009]

[/CHUNK]

[CHUNK: doc-toc-001]
================================================================================
TABLE OF CONTENTS - NAVIGABLE INDEX
================================================================================

PART 1 (THIS FILE): METADATA, PROJECT FOUNDATION, TECHNOLOGY
├── doc-metadata-001: Consolidated document metadata
├── doc-provenance-001: Source file mapping
├── doc-toc-001: This table of contents
├── project-vision-001: Project vision and goals
├── project-architecture-001: Architecture philosophy and principles
├── golden-rule-001: Test-First Development
├── golden-rule-002: Explicit Over Implicit
├── golden-rule-003: Self-Documenting Code
├── golden-rule-004: Security By Design
├── golden-rule-005: Scalability From Day One
├── golden-rule-006: AI Agent Tooling (Not Static Context)
├── golden-rule-007: 80% Test Coverage Minimum
├── golden-rule-008: Golden Rules Enforced via CI/CD
├── golden-rule-009: Monorepo Structure
├── golden-rule-010: Single ONETRUTH Branding
├── config-tech-stack-backend-001: Backend technology stack
├── config-tech-stack-frontend-001: Frontend technology stack
├── project-structure-001: Project directory structure
├── config-deployment-001: Deployment guide (Render.com)
├── domain-concepts-001: Domain-specific context (Experience, NAICS, ONETRUTH)
└── config-onetruth-001: ONETRUTH branding system

PART 2: DATABASE LAYER, NAICS SYSTEM
[See levelith-masterfile-v1-claude-part2.md]

PART 3: API LAYER, FRONTEND ARCHITECTURE
[See levelith-masterfile-v1-claude-part3.md]

PART 4: DEVELOPMENT WORKFLOW, QUALITY STANDARDS, APPENDICES
[See levelith-masterfile-v1-claude-part4.md]

[/CHUNK]

================================================================================
SECTION 1: PROJECT FOUNDATION
================================================================================

[CHUNK: project-vision-001]
[SPLIT-CANDIDATE: project-overview-standalone]
================================================================================
PROJECT VISION AND GOALS
================================================================================

Levelith is an AI-first social-resume gamification platform that transforms professional experience tracking into an engaging, interactive experience. Built with AI agents as first-class development citizens, Levelith demonstrates how modern development practices can create scalable, maintainable applications.

CORE PRODUCT:
- Social resume platform with gamification elements
- Experience tracking across Education, Workplace, and Skills categories
- NAICS-based professional categorization (North American Industry Classification System)
- Mobile-first responsive design
- Real-time social interactions

TECHNICAL INNOVATION:
- AI-first development methodology
- Test-driven architecture with 80% minimum coverage requirement
- Automated quality and security enforcement via CI/CD
- Self-documenting codebase
- Intelligent navigation system for AI agents

PRIMARY GOALS (Immediate Focus - Phase 0-2):
1. Enable user input capabilities - Fix admin dashboard CRUD, enable user registration
2. Replace traditional resumes - Create compelling digital career showcase platform
3. Profile creation and management - Allow users to build and display professional profiles
4. Experience tracking foundation - Support all 9 experience types with NAICS classification

LONG-TERM VISION:
1. Create engaging user experience through gamification
2. Enable social professional networking (guilds, parties, assets)
3. Maintain high quality (80% test coverage, Golden Rules enforcement)
4. Enable scalability for growing user base
5. Demonstrate effective AI-first development methodology
6. Foster professional growth tracking

NON-GOALS:
- Replace LinkedIn or traditional resume platforms
- Generate code without tests
- Sacrifice security for convenience
- Create technical debt
- Build features without clear requirements
- Ignore accessibility standards

[SEE: CHUNK project-architecture-001 - Architectural principles]
[SEE: CHUNK domain-concepts-001 - Core domain concepts (Experience, NAICS, ONETRUTH)]
[/CHUNK]

[CHUNK: project-architecture-001]
================================================================================
ARCHITECTURE PHILOSOPHY AND PRINCIPLES
================================================================================

CORE PRINCIPLES:

1. Explicit over Implicit
   - Clear, typed interfaces
   - No magic or hidden behavior
   - Document all assumptions

2. Test-First, Always
   - Tests define behavior
   - 80% minimum coverage is non-negotiable
   - Integration tests for cross-component features

3. Self-Documenting Code
   - Code should explain itself
   - Docstrings for public APIs
   - Type hints everywhere
   - Comments only for "why", not "what"

4. Security by Design
   - Never trust user input
   - Parameterized queries only
   - Environment variables for secrets
   - Regular security audits

5. Scalability from Day One
   - Stateless design
   - Pagination for large datasets
   - Rate limiting on APIs
   - Horizontal scaling considerations

ARCHITECTURAL DECISIONS:

Decision: Use intelligent AI agent tooling (not static context nodes)
Rationale: Always in sync with code, zero maintenance overhead, query-driven
Status: Implemented

Decision: Enforce 80% test coverage minimum
Rationale: High coverage prevents regressions, documents behavior
Status: Implemented

Decision: Golden rules enforced via CI/CD
Rationale: Automation ensures consistency, prevents human error
Status: Implemented

Decision: Monorepo structure (backend, frontend, dev tools)
Rationale: Easier to maintain consistency, shared tooling
Status: In Progress

Decision: NAICS code required for all experiences
Rationale: Standardized industry classification enables professional categorization
Note: Uses 123456 as fallback "GENERAL" code
Status: In Progress

Decision: Single ONETRUTH branding configuration
Rationale: Centralized branding ensures consistency, simplifies theme updates
Status: In Progress

Decision: Deploy on Render.com
Rationale: Simple deployment, auto-scaling, integrated CI/CD, cost-effective
Status: In Progress

[SEE: CHUNK golden-rule-001 through golden-rule-010 - Detailed golden rules]
[SEE: CHUNK config-tech-stack-backend-001 - Backend technology choices]
[SEE: CHUNK config-tech-stack-frontend-001 - Frontend technology choices]
[/CHUNK]

[CHUNK: golden-rule-001]
================================================================================
GOLDEN RULE 1: TEST-FIRST DEVELOPMENT
================================================================================

PRINCIPLE: Tests define behavior. Write tests BEFORE implementation.

REQUIREMENTS:
- 80% minimum test coverage (non-negotiable)
- All new features require tests first
- Integration tests for cross-component features
- Test failures block merges

WORKFLOW:
1. Generate test template: python tests/test_system.py generate <file>
2. Write tests that define expected behavior
3. Implement feature to make tests pass
4. Run full test suite: pytest
5. Verify coverage: pytest --cov=backend --cov-report=html

RATIONALE:
- Tests document intended behavior
- High coverage prevents regressions
- Forces consideration of edge cases
- Enables confident refactoring

[SEE: CHUNK tests-strategies-001 - Testing strategies and patterns]
[/CHUNK]

[CHUNK: golden-rule-002]
================================================================================
GOLDEN RULE 2: EXPLICIT OVER IMPLICIT
================================================================================

PRINCIPLE: Clear, typed interfaces. No magic or hidden behavior.

REQUIREMENTS:
- Type hints on all function signatures
- Clear variable and function names
- Document all assumptions
- No implicit global state
- Dependency injection over singletons

EXAMPLES:

GOOD - Explicit:
```python
def get_users(db: Session, active_only: bool = False) -> List[UserDB]:
    """
    Retrieve users from database.

    Args:
        db: Database session
        active_only: If True, only return active users

    Returns:
        List of UserDB objects
    """
    query = db.query(UserDB)
    if active_only:
        query = query.filter(UserDB.is_active == True)
    return query.all()
```

BAD - Implicit:
```python
def get_users(active=False):
    # Where does db come from? What type is active?
    return db.query(User).filter_by(active=active).all()
```

[SEE: CHUNK quality-conventions-python-001 - Python coding standards]
[/CHUNK]

[CHUNK: golden-rule-003]
================================================================================
GOLDEN RULE 3: SELF-DOCUMENTING CODE
================================================================================

PRINCIPLE: Code should explain itself. Docstrings for public APIs.

REQUIREMENTS:
- Meaningful names (functions, variables, classes)
- Google-style docstrings for all public functions/classes
- Type hints everywhere (Python 3.11+ syntax)
- Comments explain "why", not "what"
- 90%+ documentation coverage (interrogate tool)

DOCSTRING FORMAT (Google Style):
```python
def calculate_experience_points(
    experience: ExperienceDB,
    multiplier: float = 1.0
) -> int:
    """
    Calculate points awarded for an experience.

    Points are based on experience duration, type, and a multiplier.
    Base formula: days * type_weight * multiplier

    Args:
        experience: The experience to calculate points for
        multiplier: Optional multiplier for bonus events

    Returns:
        Total points as integer

    Raises:
        ValueError: If experience has no start_date
    """
    if not experience.start_date:
        raise ValueError("Experience must have start_date")

    # Implementation...
```

[SEE: CHUNK quality-conventions-python-001 - Python documentation standards]
[/CHUNK]

[CHUNK: golden-rule-004]
================================================================================
GOLDEN RULE 4: SECURITY BY DESIGN
================================================================================

PRINCIPLE: Never trust user input. Security is not optional.

REQUIREMENTS:
- Parameterized queries only (no string interpolation)
- Environment variables for all secrets
- Input validation on all API endpoints
- Password hashing (bcrypt/argon2)
- HTTPS only in production
- Regular security audits (bandit tool)

SECURITY CHECKLIST:
- [ ] All user input validated (Pydantic schemas)
- [ ] No secrets in code (use .env files)
- [ ] Passwords hashed, never stored plain text
- [ ] SQL injection prevention (SQLAlchemy parameterization)
- [ ] XSS prevention (sanitize HTML output)
- [ ] CSRF protection (JWT tokens)
- [ ] Rate limiting on API endpoints
- [ ] CORS configured correctly

EXAMPLES:

SECURE - Parameterized:
```python
user = db.query(UserDB).filter(UserDB.email == user_email).first()
```

INSECURE - String interpolation:
```python
# NEVER DO THIS - SQL INJECTION RISK
user = db.execute(f"SELECT * FROM users WHERE email = '{user_email}'")
```

[SEE: CHUNK api-overview-001 - API security patterns]
[/CHUNK]

[CHUNK: golden-rule-005]
================================================================================
GOLDEN RULE 5: SCALABILITY FROM DAY ONE
================================================================================

PRINCIPLE: Design for scale, even if not needed yet.

REQUIREMENTS:
- Stateless design (no server-side sessions)
- Pagination for all list endpoints (default 50, max 200)
- Rate limiting on APIs
- Database indexes on foreign keys
- Connection pooling (QueuePool)
- Horizontal scaling considerations

PAGINATION PATTERN:
```python
@router.get("/users", response_model=PaginatedResponse[UserResponse])
def list_users(
    page: int = Query(1, ge=1),
    page_size: int = Query(50, ge=1, le=200),
    db: Session = Depends(get_db)
):
    offset = (page - 1) * page_size
    users = db.query(UserDB).offset(offset).limit(page_size).all()
    total = db.query(UserDB).count()

    return {
        "items": users,
        "total": total,
        "page": page,
        "page_size": page_size,
        "total_pages": (total + page_size - 1) // page_size
    }
```

RATE LIMITING:
- Authentication: 10 requests/minute
- User operations: 100 requests/minute
- Search/Read: 200 requests/minute

[SEE: CHUNK api-overview-001 - API rate limiting implementation]
[/CHUNK]

[CHUNK: golden-rule-006]
================================================================================
GOLDEN RULE 6: AI AGENT TOOLING (NOT STATIC CONTEXT)
================================================================================

PRINCIPLE: Use intelligent AI navigation tools, not static context nodes.

DECISION: Intelligent AI agent tooling over static context files
Rationale: Always in sync with code, zero maintenance overhead, query-driven

IMPLEMENTATION:
- AI navigation system: dev/aiagent_navigator.py
- Query-driven codebase exploration
- Auto-updating index
- No manual context file maintenance

AI AGENT COMMANDS:
```bash
# Build index and explore codebase
python dev/aiagent_navigator.py index
python dev/aiagent_navigator.py plan

# Query codebase
python dev/aiagent_navigator.py ask "How does authentication work?"

# After changes: update AI index
python dev/aiagent_navigator.py index
```

BENEFITS:
- Always synchronized with current code
- No stale documentation
- Query-based discovery
- Zero maintenance overhead

[SEE: CHUNK workflow-ai-agent-001 - AI agent development workflow]
[/CHUNK]

[CHUNK: golden-rule-007]
================================================================================
GOLDEN RULE 7: 80% TEST COVERAGE MINIMUM
================================================================================

PRINCIPLE: 80% test coverage is non-negotiable.

ENFORCEMENT:
- CI/CD fails if coverage < 80%
- pytest-cov measures coverage
- HTML reports generated: htmlcov/index.html

COMMANDS:
```bash
# Run tests with coverage
pytest --cov=backend --cov-report=term-missing

# Generate HTML report
pytest --cov=backend --cov-report=html
open htmlcov/index.html

# Check specific module
pytest --cov=backend.services --cov-report=term-missing
```

COVERAGE TARGETS:
- Overall: ≥80%
- Services: ≥85%
- Models: ≥90%
- Utilities: ≥95%

WHAT TO TEST:
- All business logic (services layer)
- Database operations (CRUD)
- API endpoints (request/response)
- Edge cases and error handling
- Validation logic

WHAT NOT TO TEST:
- Third-party libraries
- Simple getters/setters
- Configuration files

[SEE: CHUNK tests-strategies-001 - Testing strategies and patterns]
[/CHUNK]

[CHUNK: golden-rule-008]
================================================================================
GOLDEN RULE 8: GOLDEN RULES ENFORCED VIA CI/CD
================================================================================

PRINCIPLE: Automation ensures consistency, prevents human error.

CI/CD CHECKS (GitHub Actions):
1. Test suite passes (pytest)
2. Coverage ≥ 80% (pytest-cov)
3. Code formatting (black, prettier)
4. Linting (pylint, eslint)
5. Type checking (mypy)
6. Security audit (bandit)
7. Golden rules enforcement (test_system.py)

ENFORCEMENT SCRIPT:
```bash
# Enforce all golden rules
python tests/test_system.py enforce

# Check specific rule
python tests/test_system.py check-coverage
python tests/test_system.py check-typing
```

MERGE REQUIREMENTS:
- All CI checks pass (green)
- Code review approval
- No conflicts with main branch
- Documentation updated

[SEE: CHUNK workflow-human-dev-001 - Human developer workflow]
[/CHUNK]

[CHUNK: golden-rule-009]
================================================================================
GOLDEN RULE 9: MONOREPO STRUCTURE
================================================================================

PRINCIPLE: Easier to maintain consistency with unified repository.

BENEFITS:
- Shared tooling and configurations
- Atomic commits across frontend/backend
- Easier refactoring across boundaries
- Consistent versioning

STRUCTURE:
```
levelith-2/
├── backend/            # Python/FastAPI
├── frontend/           # React/TypeScript
├── docs/               # Documentation
├── tests/              # Test suite
├── dev/                # Development tools
├── .github/            # CI/CD workflows
└── README.md           # Entry point
```

SHARED CONFIGURATIONS:
- .gitignore (unified)
- .editorconfig (consistent formatting)
- CI/CD workflows (test both together)

[SEE: CHUNK project-structure-001 - Detailed directory structure]
[/CHUNK]

[CHUNK: golden-rule-010]
================================================================================
GOLDEN RULE 10: SINGLE ONETRUTH BRANDING
================================================================================

PRINCIPLE: Centralized branding ensures consistency, simplifies theme updates.

LOCATION: frontend/src/config/ONETRUTH.ts

STRUCTURE:
```typescript
export const ONETRUTH = {
  colors: {
    primary: "#3498db",
    secondary: "#2ecc71",
    // ... all colors
  },
  fonts: {
    heading: "Montserrat, sans-serif",
    body: "Open Sans, sans-serif",
    // ... all typography
  },
  spacing: { xs: "4px", sm: "8px", md: "16px", /* ... */ },
  borderRadius: { sm: "4px", md: "8px", /* ... */ },
  shadows: { sm: "...", md: "...", /* ... */ },
}
```

RULES:
- ALWAYS import from ONETRUTH
- NEVER hardcode colors, fonts, spacing
- NO inline style values
- Admin dashboard: inline styles with ONETRUTH only (NO Tailwind)
- Main app: Tailwind CSS (maps to ONETRUTH values)

[SEE: CHUNK config-onetruth-001 - Complete ONETRUTH specification]
[/CHUNK]

================================================================================
SECTION 2: TECHNOLOGY & INFRASTRUCTURE
================================================================================

[CHUNK: config-tech-stack-backend-001]
================================================================================
BACKEND TECHNOLOGY STACK
================================================================================

CURRENT STACK:

| Component       | Technology        | Version  | Rationale                        |
|-----------------|-------------------|----------|----------------------------------|
| Language        | Python            | 3.11+    | AI agent familiarity, ecosystem  |
| Web Framework   | FastAPI           | Latest   | Modern Python API, auto-docs     |
| Database        | PostgreSQL        | 15       | ACID compliance, relational      |
| ORM             | SQLAlchemy        | 2.0+     | Mature, well-documented          |
| Migrations      | Alembic           | Latest   | Schema versioning                |
| Testing         | Pytest            | Latest   | Powerful, flexible, AI support   |
| Password Hash   | bcrypt/PBKDF2     | Latest   | Secure password hashing          |
| HTTP Client     | httpx             | Latest   | Async HTTP for external APIs     |

WHY PYTHON 3.11+:
- Type hints with modern syntax
- Performance improvements
- Better error messages
- AI agent familiarity

WHY FASTAPI:
- Automatic OpenAPI/Swagger documentation
- Built-in validation (Pydantic)
- Async support
- Fast development cycle
- Type hints integration

WHY POSTGRESQL:
- ACID compliance (data integrity)
- JSON/JSONB support (flexible schemas)
- Excellent performance and query optimization
- Robust indexing
- Open source, no licensing costs

WHY SQLALCHEMY 2.0:
- Type-safe ORM with Python type hints
- Both ORM and raw SQL support
- Database-agnostic (can switch databases)
- Relationship management
- Connection pooling

PLANNED/IN PROGRESS:
- Caching: Redis (session management, performance)
- Authentication: JWT tokens (stateless auth)
- Task Queue: Celery (background jobs)

FUTURE CONSIDERATIONS:
- Search: Elasticsearch (fast experience/skill search)
- Analytics: Custom dashboard (user engagement metrics)
- Monitoring: Sentry (error tracking)

[SEE: CHUNK config-deployment-001 - Deployment configuration on Render.com]
[/CHUNK]

[CHUNK: config-tech-stack-frontend-001]
================================================================================
FRONTEND TECHNOLOGY STACK
================================================================================

CURRENT STACK:

| Technology          | Version | Purpose                              |
|---------------------|---------|--------------------------------------|
| React               | 18.2+   | UI library with hooks                |
| TypeScript          | 5.3+    | Type safety and developer experience |
| Vite                | 5.0+    | Build tool and dev server            |
| Tailwind CSS        | 3.4+    | Utility-first styling (main app)     |
| React Query         | 5.90+   | Server state management, caching     |
| TanStack Table      | 8.21+   | Headless table library               |
| Axios               | 1.6+    | HTTP client with interceptors        |
| React Hook Form     | 7.66+   | Form state management                |
| Zod                 | 3.25+   | Schema validation                    |
| Radix UI            | Various | Accessible component primitives      |
| Lucide React        | 0.303+  | Icon library                         |
| Sonner              | 1.7+    | Toast notifications                  |
| date-fns            | 3.6+    | Date manipulation                    |
| ESLint              | 8.55+   | Code linting                         |
| Prettier            | 3.1+    | Code formatting                      |

WHY REACT 18:
- Concurrent rendering
- Automatic batching
- Hooks ecosystem
- Large community

WHY TYPESCRIPT:
- Compile-time type checking
- Better IDE support
- Self-documenting code
- Catch errors early

WHY VITE:
- Lightning-fast HMR (Hot Module Replacement)
- Optimized build output
- Modern ES modules
- Better DX than Create React App

WHY REACT QUERY:
- Server state management
- Automatic caching and refetching
- Optimistic updates
- Background synchronization
- Eliminates need for Redux for server data

WHY TANSTACK TABLE:
- Headless (bring your own UI)
- Powerful features (sorting, filtering, pagination)
- TypeScript-first
- Performance optimized

WHY TAILWIND CSS (MAIN APP):
- Utility-first approach
- No context switching (HTML + CSS together)
- Responsive design made easy
- Purging removes unused CSS

WHY INLINE STYLES + ONETRUTH (ADMIN DASHBOARD):
- Single source of truth for branding
- Easy theme switching
- No className pollution
- Direct mapping to design tokens

ARCHITECTURE PATTERNS:
- Feature-based folder structure
- Barrel exports (index.ts)
- API client layer (axios)
- React Query hooks layer
- Composite hooks layer
- Component layer

[SEE: CHUNK project-structure-001 - Frontend folder structure]
[SEE: CHUNK config-onetruth-001 - ONETRUTH branding system]
[/CHUNK]

[CHUNK: project-structure-001]
[SPLIT-CANDIDATE: project-structure-reference]
================================================================================
PROJECT STRUCTURE AND ORGANIZATION
================================================================================

DIRECTORY LAYOUT:

levelith-2/
├── README.md           # Entry point for humans
├── MANIFEST.md         # Entry point for AI agents
├── AI_AGENT_*          # AI agent specific documentation
│
├── backend/            # Backend application (Python/FastAPI)
│   ├── api/
│   │   └── routes/     # 37 REST endpoints
│   ├── models/         # User, Experience, NAICS models
│   ├── services/       # Business logic layer
│   ├── repositories/   # Data access layer
│   ├── data/           # Reference data (NAICS JSON)
│   └── alembic/        # Database migrations
│
├── frontend/           # Frontend application (React + TypeScript)
│   └── src/
│       ├── main.tsx              # Main app entry point
│       ├── App.tsx               # Main app router
│       ├── config/
│       │   └── ONETRUTH.ts       # Single source of truth for branding
│       ├── pages/                # Main app pages
│       ├── components/           # Reusable UI components
│       ├── hooks/                # Custom React hooks
│       ├── services/             # API services
│       ├── lib/                  # Utilities (transformers, api)
│       └── admin/                # Admin dashboard (separate app)
│           ├── main.jsx          # Admin entry point
│           ├── App.jsx           # Admin router
│           ├── pages/            # Admin pages
│           ├── features/         # Feature modules
│           │   ├── users/        # Users feature
│           │   ├── experiences/  # Experiences feature
│           │   ├── naics/        # NAICS feature
│           │   ├── dashboard/    # Dashboard widgets
│           │   └── settings/     # Settings feature
│           ├── components/       # Admin components
│           │   ├── ui/           # shadcn/ui components
│           │   ├── custom/       # Custom reusable components
│           │   └── layout/       # Layout components
│           ├── hooks/            # Admin hooks
│           └── lib/              # Admin utilities
│
├── dev/                # Development tools
│   ├── aiagent_navigator.py  # AI navigation system
│   └── dev-frontend/         # Dev admin dashboard
│
├── tests/              # Comprehensive test suite (491 tests)
├── docs/               # Documentation (41 files)
└── .github/            # CI/CD workflows

FILE NAMING CONVENTIONS:

| Type                    | Convention              | Examples                           |
|-------------------------|-------------------------|------------------------------------|
| Python                  | snake_case.py           | user_service.py, naics_repository.py |
| JS/TS Components        | PascalCase.tsx          | UserCard.tsx, ExperienceList.tsx   |
| JS/TS Utilities         | camelCase.ts            | apiClient.ts, formatDate.ts        |
| Tests                   | test_*.py               | test_user_service.py               |
| Important Docs          | UPPERCASE.md            | MANIFEST.md, GOLDEN_RULES.md       |
| Guide Docs              | lowercase.md            | deployment.md, troubleshooting.md  |
| Config                  | .lowercase              | .gitignore, .env.example           |

ADMIN DASHBOARD FOLDER STRUCTURE:

frontend/src/admin/
├── features/                    # Feature modules
│   ├── users/
│   │   ├── api/                 # API client methods
│   │   ├── components/          # Feature components
│   │   ├── schemas/             # Zod validation
│   │   ├── types/               # TypeScript types
│   │   └── hooks/               # Feature hooks
│   ├── experiences/
│   │   ├── api/
│   │   ├── components/
│   │   │   └── forms/           # 9 experience type forms
│   │   ├── schemas/
│   │   ├── types/
│   │   └── hooks/
│   └── naics/
│       ├── api/
│       ├── components/
│       ├── schemas/
│       ├── types/
│       └── hooks/
├── components/
│   ├── ui/                      # shadcn/ui components
│   └── custom/                  # Custom reusable components
│       ├── DataTable.tsx
│       ├── SearchBar.tsx
│       ├── FilterPanel.tsx
│       ├── Pagination.tsx
│       ├── LoadingSpinner.tsx
│       ├── LoadingSkeleton.tsx
│       ├── ErrorBoundary.tsx
│       └── ConfirmDialog.tsx
├── hooks/                       # Shared custom hooks
│   ├── useDebounce.ts
│   ├── usePagination.ts
│   ├── useLocalStorage.ts
│   └── useDisclosure.ts
├── lib/                         # Utility functions
│   ├── constants.ts
│   ├── formatters.ts
│   └── validators.ts
└── types/                       # Global types
    ├── common.types.ts
    └── api.types.ts

[SEE: CHUNK workflow-ai-agent-001 - AI agent development workflow]
[SEE: CHUNK workflow-human-dev-001 - Human developer workflow]
[/CHUNK]

[CHUNK: config-deployment-001]
[SPLIT-CANDIDATE: deployment-guide-standalone]
================================================================================
DEPLOYMENT GUIDE (RENDER.COM)
================================================================================

PLATFORM: Render.com

ARCHITECTURE:

GitHub Repository (levelith-2/main branch)
        │ Auto-deploy on push
        ▼
Render Web Service
  ├── FastAPI Application (Python 3.11, uvicorn)
  └── PostgreSQL Database (Managed, automatic backups)
        │
        ▼
    https://levelith-backend.onrender.com

PREREQUISITES:

Required Accounts:
- Render Account (https://render.com)
- GitHub Account with repository access

Required Files (already in repo):
- Procfile: Defines application start command
- backend/requirements.txt: Python dependencies
- backend/main.py: FastAPI application entry point
- render.yaml: Infrastructure as Code blueprint
- .env.render.example: Environment variable template

DEPLOYMENT OPTIONS:

Option 1: Automated Blueprint (5 minutes)
1. Push code to GitHub
2. Go to dashboard.render.com
3. Click "New +" → "Blueprint"
4. Connect repository with render.yaml
5. Click "Apply"

Option 2: Manual Setup (15 minutes)
1. Create PostgreSQL Database
   - Click "New +" → "PostgreSQL"
   - Name: levelith-db
   - Region: Oregon (US West)
   - Plan: Starter ($7/month) or Free

2. Create Web Service
   - Click "New +" → "Web Service"
   - Connect GitHub repository
   - Runtime: Python 3
   - Build Command: pip install -r backend/requirements.txt
   - Start Command: (uses Procfile)

3. Set Environment Variables (see below)

ENVIRONMENT VARIABLES:

Critical (MUST SET):
| Key          | Value                     | Notes                      |
|--------------|---------------------------|----------------------------|
| DATABASE_URL | (from Database Setup)     | Internal Database URL      |
| SECRET_KEY   | (generate)                | openssl rand -hex 32       |
| ENVIRONMENT  | production                | Sets production mode       |
| DEBUG        | false                     | Disables debug mode        |

Recommended:
| Key              | Value                    | Notes                      |
|------------------|--------------------------|----------------------------|
| PYTHON_VERSION   | 3.11.0                   | Python version             |
| LOG_LEVEL        | INFO                     | Logging level              |
| CORS_ORIGINS     | https://yourdomain.com   | Frontend URL               |
| API_V1_PREFIX    | /api/v1                  | API prefix                 |

Optional:
| Key                         | Value | Notes                      |
|-----------------------------|-------|----------------------------|
| ACCESS_TOKEN_EXPIRE_MINUTES | 30    | JWT token expiry           |
| DATABASE_POOL_SIZE          | 10    | Connection pool size       |
| RATE_LIMIT_ENABLED          | true  | Enable rate limiting       |
| RATE_LIMIT_REQUESTS         | 100   | Requests per window        |
| RATE_LIMIT_WINDOW           | 60    | Window in seconds          |

VERIFICATION:

# Test health endpoint
curl https://levelith-backend.onrender.com/health

Expected Response:
{
  "status": "healthy",
  "timestamp": "2025-11-19T10:30:00.000000",
  "version": "2.0.0",
  "environment": "production"
}

# Test database connection
curl https://levelith-backend.onrender.com/health/details

COST BREAKDOWN:

Development (Free Tier):
- Web Service: Free (512MB RAM, sleeps after 15min)
- PostgreSQL: Free (256MB storage, no backups)
- Total: $0/month

Production Minimal:
- Web Service Starter: $7/month (2GB RAM, no sleep)
- PostgreSQL Starter: $7/month (1GB, backups)
- Total: $14/month

Production Recommended:
- Web Service Standard: $25/month
- PostgreSQL Standard: $25/month
- Total: $50/month

SECURITY CHECKLIST:

[ ] SECRET_KEY is strong and unique
[ ] DEBUG=false in production
[ ] DATABASE_URL uses Internal URL
[ ] CORS_ORIGINS only includes your domains
[ ] Database has strong password
[ ] Environment variables not committed to Git
[ ] HTTPS enabled (automatic on Render)
[ ] Health checks configured
[ ] Rate limiting enabled

TROUBLESHOOTING:

Build Failed:
- Verify requirements.txt is complete
- Check build command: pip install -r backend/requirements.txt
- Set PYTHON_VERSION=3.11.0

Database Connection Failed:
- Use Internal Database URL (not External)
- Verify database status in dashboard
- Ensure database and service in same region

Health Check Fails:
- Verify /health endpoint exists
- Check application logs for startup errors
- Ensure PORT environment variable is used

[SEE: CHUNK api-overview-001 - API endpoints reference]
[/CHUNK]

================================================================================
SECTION 3: DOMAIN CONCEPTS
================================================================================

[CHUNK: domain-concepts-001]
================================================================================
DOMAIN-SPECIFIC CONTEXT
================================================================================

KEY CONCEPTS:

Experience: The core data model representing any trackable professional/educational activity. Categories: Education, Workplace, Skills.

NAICS Code: North American Industry Classification System code REQUIRED for all experiences. Fallback: 123456 for "GENERAL" classification.

ONETRUTH: Single authoritative branding configuration file that all components must reference for consistent theming. Location: /frontend/src/config/ONETRUTH.ts

Gamification: Point systems, achievements, levels based on experience tracking and social engagement.

Social Resume: Public-facing profile showcasing user's experiences in an interactive, gamified format.

USER SYSTEM:

Authentication:
- Username + password login (traditional)
- JWT tokens for session management
- Secure password hashing (bcrypt)

User Model Attributes:
- id: Unique user identifier
- username: User's chosen username
- password_hash: Hashed password (never store plain text)
- email: User's email address
- experiences: Array of Experience objects
- created_at: Account creation timestamp
- profile_data: Additional profile information (JSON)

EXPERIENCE MODEL:

Every user has an array of Experience elements. Experiences come in three main variants, each with three subtypes (9 total):

1. EDUCATION EXPERIENCES:
   - Certificate: Short-term certifications, professional credentials
   - Degree: Formal academic degrees, university programs
   - Course: Individual courses, workshops, online learning

2. WORKPLACE EXPERIENCES:
   - Gig: Short-term contract work, freelance projects
   - Part-Time: Regular part-time employment
   - Full-Time: Primary career positions, standard employment

3. SKILLS EXPERIENCES:
   - Soft Skills: Interpersonal abilities, communication, leadership
   - Hard Skills: Technical abilities, measurable competencies
   - Native Skills: Natural talents, innate abilities, language fluencies

NAICS INTEGRATION:

CRITICAL REQUIREMENT: Every Experience entry MUST include a NAICS code.

NAICS Code: 123456 is the special fallback code representing "GENERAL" classification when specific industry code is not available.

| Industry             | NAICS Code |
|----------------------|------------|
| Software Development | 541511     |
| Elementary Schools   | 611110     |
| Graphic Design       | 541430     |
| Unknown/General      | 123456     |

BUSINESS LOGIC AREAS:

1. User Management
   - Username/password authentication
   - User profile management
   - Experience CRUD operations
   - Social connections and networking

2. Experience Tracking
   - Create/edit experiences (9 types)
   - NAICS code validation and assignment
   - Experience categorization and filtering
   - Timeline visualization

3. Gamification
   - Points system based on activity
   - Achievements and badges
   - User levels and progression
   - Leaderboards and rankings

4. Social Features
   - View other users' profiles
   - Connect with professionals
   - Share experiences
   - Comment and engage

5. API Layer
   - RESTful endpoints for all operations
   - JWT authentication
   - Rate limiting (prevent abuse)
   - Versioning (future compatibility)

[SEE: CHUNK schema-users-001 - User database schema]
[SEE: CHUNK schema-experiences-001 - Experience database schema]
[SEE: CHUNK schema-naics-001 - NAICS database schema]
[SEE: CHUNK config-onetruth-001 - ONETRUTH branding system details]
[/CHUNK]

[CHUNK: config-onetruth-001]
[SPLIT-CANDIDATE: onetruth-guide-standalone]
================================================================================
ONETRUTH BRANDING SYSTEM
================================================================================

WHAT IS ONETRUTH?

ONETRUTH is the single source of truth for all branding, theming, and styling in the Levelith application. It provides centralized control over colors, fonts, spacing, and all visual elements.

LOCATION: /frontend/src/config/ONETRUTH.ts

BENEFITS:
- Consistent colors, fonts, spacing across all components
- Easy theme updates (change once, apply everywhere)
- No hardcoded values scattered throughout codebase
- Dynamic theme switching capability
- Future-ready for dark mode

ONETRUTH STRUCTURE:

```typescript
export const ONETRUTH = {
  // Colors
  colors: {
    primary: "#3498db",        // Main brand color (blue)
    secondary: "#2ecc71",      // Success green
    accent: "#e74c3c",         // Alert red
    background: "#ecf0f1",     // Light gray background
    surface: "#ffffff",        // White surfaces
    textDark: "#2c3e50",       // Dark text
    textLight: "#7f8c8d",      // Light text
    textInverse: "#ffffff",    // Inverse text
    error: "#e74c3c",
    warning: "#f39c12",
    success: "#27ae60",
    info: "#3498db",
  },

  // Typography
  fonts: {
    heading: "Montserrat, sans-serif",
    body: "Open Sans, sans-serif",
    monospace: "Fira Code, monospace",

    sizes: {
      xs: "12px", sm: "14px", base: "16px", lg: "18px",
      xl: "20px", "2xl": "24px", "3xl": "30px", "4xl": "36px", "5xl": "48px"
    },

    weights: {
      light: 300, normal: 400, medium: 500,
      semibold: 600, bold: 700, extrabold: 800
    },

    lineHeights: {
      tight: "1.25", normal: "1.5", relaxed: "1.75", loose: "2"
    }
  },

  // Spacing
  spacing: {
    xs: "4px", sm: "8px", md: "16px", lg: "24px",
    xl: "32px", "2xl": "48px", "3xl": "64px", "4xl": "96px"
  },

  // Border Radius
  borderRadius: {
    sm: "4px", md: "8px", lg: "12px", xl: "16px", "2xl": "24px", full: "9999px"
  },

  // Shadows
  shadows: {
    sm: "0 1px 2px rgba(0, 0, 0, 0.05)",
    md: "0 4px 6px rgba(0, 0, 0, 0.1)",
    lg: "0 10px 15px rgba(0, 0, 0, 0.1)",
    xl: "0 20px 25px rgba(0, 0, 0, 0.1)",
    "2xl": "0 25px 50px rgba(0, 0, 0, 0.25)"
  },

  // Transitions
  transitions: {
    fast: "150ms ease", base: "300ms ease", slow: "500ms ease"
  },

  // Experience Type Colors
  experienceTypes: {
    education: "#9b59b6",
    workplace: "#e67e22",
    skills: "#1abc9c"
  },

  // NAICS Industry Colors
  naicsColors: {
    technology: "#3498db",
    healthcare: "#e74c3c",
    finance: "#27ae60"
  },

  // Z-Index
  zIndex: {
    dropdown: 1000, sticky: 1020, fixed: 1030,
    modalBackdrop: 1040, modal: 1050, popover: 1060, tooltip: 1070
  }
};
```

ONETRUTH RULES:

CORRECT USAGE:
```typescript
import { ONETRUTH } from '../config/ONETRUTH';

const styles = {
  container: {
    backgroundColor: ONETRUTH.colors.background,
    padding: ONETRUTH.spacing.lg,
    borderRadius: ONETRUTH.borderRadius.md,
    fontFamily: ONETRUTH.fonts.body,
  }
};
```

INCORRECT USAGE (NEVER DO THIS):
```typescript
const badStyles = {
  container: {
    backgroundColor: "#ecf0f1",  // Hardcoded color
    padding: "24px",             // Hardcoded spacing
    borderRadius: "8px",         // Hardcoded radius
    fontFamily: "Open Sans",     // Hardcoded font
  }
};
```

STYLING RULES:
- Always import branding from ONETRUTH
- Never hardcode colors, fonts, or spacing
- All UI components reference ONETRUTH
- No inline styles with hardcoded values
- No CSS variables outside ONETRUTH
- No component-specific theme overrides

ADMIN DASHBOARD VS MAIN APP:

Admin Dashboard:
- Uses INLINE STYLES with ONETRUTH exclusively
- NO Tailwind CSS classes
- NO className attributes (except markdown rendering)

Main Application (Landing, Docs):
- Can use Tailwind CSS classes
- Still references ONETRUTH color palette
- Tailwind config maps to ONETRUTH values

COMPONENT CHECKLIST (Admin Dashboard):
Before committing, verify:
- [ ] NO className attributes
- [ ] All colors use ONETRUTH.colors.*
- [ ] All spacing uses ONETRUTH.spacing.*
- [ ] All fonts use ONETRUTH.fonts.*
- [ ] All borders/shadows use ONETRUTH values
- [ ] Component imports ONETRUTH theme
- [ ] No hardcoded hex colors
- [ ] No hardcoded pixel values

[SEE: CHUNK ui-admin-overview-001 - Admin dashboard architecture]
[/CHUNK]

================================================================================
END OF PART 1
================================================================================

CONTINUATION:
[SEE: levelith-masterfile-v1-claude-part2.md - Database Layer, NAICS System]
[SEE: levelith-masterfile-v1-claude-part3.md - API Layer, Frontend Architecture]
[SEE: levelith-masterfile-v1-claude-part4.md - Development Workflow, Quality Standards, Appendices]

================================================================================
