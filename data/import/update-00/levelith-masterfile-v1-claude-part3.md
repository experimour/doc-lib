================================================================================
LEVELITH-2 MASTER DOCUMENTATION - PART 3 OF 4
================================================================================
Project: Levelith-2 - AI-First Social Resume Gamification Platform
Organization: Semour Media Group
Generated: 2025-11-21
Purpose: API Layer and Frontend Architecture
================================================================================

[CONTINUATION FROM PART 2]

================================================================================
SECTION 5: API LAYER
================================================================================

[CHUNK: api-overview-001]
[SPLIT-CANDIDATE: api-reference-standalone]
================================================================================
API OVERVIEW
================================================================================

BASE URLS:
- Production:  https://levelith.online/api/v1
- Development: http://localhost:8000/api/v1

CORE FEATURES:
- User Management: Registration, authentication, profile management
- Experience Tracking: CRUD operations for 9 experience types
- NAICS Integration: Industry classification for all experiences
- Social Features: User discovery, experience sharing (planned)
- Gamification: Points, achievements, levels (planned)

DESIGN PRINCIPLES:
- RESTful: Standard HTTP methods (GET, POST, PUT, PATCH, DELETE)
- JSON: All request/response bodies use JSON format
- Stateless: JWT tokens for authentication
- Versioned: API version in URL path (/api/v1/)
- Documented: OpenAPI/Swagger documentation at /docs

TECHNOLOGY STACK:
| Component       | Technology                |
|-----------------|---------------------------|
| Framework       | FastAPI (Python 3.11+)    |
| Authentication  | JWT tokens                |
| Database        | PostgreSQL                |
| Documentation   | OpenAPI/Swagger (auto)    |
| Hosting         | Render.com                |

AUTHENTICATION FLOW:
1. User sends credentials to /auth/login
2. Server validates credentials
3. Server generates JWT token (valid for 24 hours)
4. Client stores token
5. Client includes token in Authorization header for subsequent requests
6. Server validates token on each request

TOKEN FORMAT:
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 86400,
  "user_id": "user_123abc"
}
```

USING TOKENS:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

TOKEN EXPIRATION:
| Token Type     | Validity  | Returns on Expiry   |
|----------------|-----------|---------------------|
| Access tokens  | 24 hours  | 401 Unauthorized    |
| Refresh tokens | 30 days   | 401 Unauthorized    |

RATE LIMITING:
| Endpoint Category   | Rate Limit          |
|---------------------|---------------------|
| Authentication      | 10 requests/minute  |
| User operations     | 100 requests/minute |
| Experience CRUD     | 50 requests/minute  |
| Search/Read         | 200 requests/minute |

[SEE: CHUNK api-auth-register-001 - User registration endpoint]
[SEE: CHUNK api-auth-login-001 - User login endpoint]
[SEE: CHUNK api-users-001 - Users endpoints]
[SEE: CHUNK api-naics-001 - NAICS endpoints]
[SEE: CHUNK api-errors-001 - Error handling]
[/CHUNK]

[CHUNK: api-auth-register-001]
================================================================================
POST /api/v1/auth/register - Create New User Account
================================================================================

STATUS: Implemented
FILE: backend/api/routes/users.py:23

ENDPOINT DEFINITION:
```python
@router.post("/", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
async def create_user(user_data: UserCreate, db: Session = Depends(get_db))
```

REQUEST:
```json
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePassword123!",
  "profile_data": {
    "display_name": "John Doe",
    "bio": "Software engineer",
    "location": "San Francisco, CA"
  }
}
```

RESPONSE (201 Created):
```json
{
  "id": "user_123abc",
  "username": "johndoe",
  "email": "john@example.com",
  "is_active": true,
  "is_verified": false,
  "profile_data": {
    "display_name": "John Doe",
    "bio": "Software engineer",
    "location": "San Francisco, CA"
  },
  "created_at": "2025-01-17T10:30:00Z",
  "updated_at": "2025-01-17T10:30:00Z",
  "experience_count": 0
}
```

ERRORS:
- 400 Bad Request: Username taken, weak password
- 422 Unprocessable Entity: Validation errors

BUSINESS RULES:
- Username: 3-50 characters, alphanumeric + underscores/hyphens
- Email: Valid format, unique
- Password: At least 8 characters
- New users start unverified but active

FEATURES:
[x] Username uniqueness validation
[x] Email uniqueness validation
[x] Password hashing
[x] Profile data support
[x] Returns UserResponse with experience_count

[SEE: CHUNK schema-users-001 - User database schema]
[/CHUNK]

[CHUNK: api-auth-login-001]
================================================================================
POST /api/v1/auth/login - Authenticate and Receive JWT Token
================================================================================

STATUS: Partial Implementation (JWT generation TODO)
FILE: backend/api/routes/users.py:236

ENDPOINT DEFINITION:
```python
@router.post("/login")
async def login(credentials: dict, db: Session = Depends(get_db))
```

REQUEST:
```json
{
  "email": "john@example.com",
  "password": "SecurePassword123!"
}
```

RESPONSE (200 OK):
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 86400,
  "user": {
    "id": "user_123abc",
    "username": "johndoe",
    "email": "john@example.com"
  }
}
```

ERRORS:
- 401 Unauthorized: Invalid credentials
- 403 Forbidden: Account disabled

FEATURES:
[x] Username + password authentication
[x] Password verification
[x] Active status check
[ ] JWT token generation (TODO - currently returns placeholder)

[/CHUNK]

[CHUNK: api-users-001]
================================================================================
USERS API ENDPOINTS
================================================================================

GET /api/v1/users/{user_id} - Get User by ID
STATUS: Implemented
FILE: backend/api/routes/users.py:84

Request: None
Headers: Authorization: Bearer <token>
Response: UserWithExperiences object
Errors: 404 Not Found

---

GET /api/v1/users/ - List Users (LIMITED)
STATUS: Basic Implementation - NEEDS ENHANCEMENT
FILE: backend/api/routes/users.py:121

Query Parameters:
- skip: int = 0
- limit: int = 100

Current Response: List[UserResponse]

MISSING FEATURES (Required for Admin Dashboard):
[ ] No search by username/email
[ ] No filtering by is_active/is_verified
[ ] No sorting capabilities
[ ] No pagination metadata (total count, total pages)
[ ] Response is List not PaginatedResponse

RECOMMENDED ENHANCEMENT:
```python
@router.get("/paginated", response_model=PaginatedUsersResponse)
async def list_users_paginated(
    page: int = Query(1, ge=1),
    page_size: int = Query(50, ge=1, le=200),
    search: Optional[str] = Query(None),
    is_active: Optional[bool] = Query(None),
    is_verified: Optional[bool] = Query(None),
    sort_by: str = Query("created_at"),
    sort_order: str = Query("desc"),
    db: Session = Depends(get_db)
):
    # Implementation with search, filters, sorting, pagination metadata
```

---

PATCH /api/v1/users/{user_id} - Update User
STATUS: Implemented
FILE: backend/api/routes/users.py:153

Request: UserUpdate (partial object)
Headers: Authorization: Bearer <token>
Response: UserResponse

FEATURES:
[x] Email uniqueness validation
[x] Profile data merge (not replace)
[x] is_active toggle support
[x] 404 error if user not found
[x] 409 conflict if email taken

LIMITATIONS:
[ ] Cannot update username (not in UserUpdate schema)
[ ] Cannot update password (would need separate endpoint)
[ ] Cannot update is_verified (not in update logic)

---

DELETE /api/v1/users/{user_id} - Delete User
STATUS: Implemented
FILE: backend/api/routes/users.py:213

Request: None
Headers: Authorization: Bearer <token>
Response: 204 No Content

FEATURES:
[x] Cascade deletes experiences (via SQLAlchemy relationship)
[x] 404 error if user not found
[x] Returns 204 No Content on success

---

POST /api/v1/users/seed - Seed Database
STATUS: Implemented
FILE: backend/api/routes/users.py:400

Query Parameters:
- user_count: int (number of users to create)

Response:
```json
{
  "success": true,
  "users_created": 50,
  "experiences_created": 250
}
```

FEATURES:
[x] Creates mock users with experiences
[x] Configurable user count
[x] Returns statistics

[/CHUNK]

[CHUNK: api-naics-001]
================================================================================
NAICS API ENDPOINTS
================================================================================

GET /api/v1/naics - List All NAICS Codes
Response: Array of NAICS codes

---

GET /api/v1/naics/paginated - Server-Side Paginated Search
STATUS: Implemented

Query Parameters:
- q: Search query for code/title/description
- category: Filter by category
- level: Filter by level (2, 3, 4, or 6)
- page: Page number (default 1)
- page_size: Items per page (default 50, max 200)

Response:
```json
{
  "items": [/* NAICS codes */],
  "total": 2222,
  "page": 1,
  "page_size": 50,
  "total_pages": 45
}
```

---

GET /api/v1/naics/{code} - Get Specific NAICS Code
Response: NAICS code object with full details

---

PATCH /api/v1/naics/{code} - Update Admin Fields (Admin Only)
STATUS: Implemented

Request:
```json
{
  "tags": ["high-demand", "tech-sector"],
  "custom_category": "Priority Industries",
  "admin_notes": "Requires additional documentation"
}
```

Response: Updated NAICS code

Note: Only admin-specific fields can be updated. Official NAICS
      fields (code, title, description) are read-only.

---

DELETE /api/v1/naics/{code} - Delete NAICS Code (Admin Only)
STATUS: Implemented
Response: 204 No Content
WARNING: Permanent deletion. Use for test/invalid codes only.

---

GET /api/v1/naics/validate/{code} - Validate NAICS Code
Response: Validation result with code details

---

GET /api/v1/naics/search - Advanced Search
POST /api/v1/naics/search

Request:
```json
{
  "query": "software development",
  "category": "technology",
  "limit": 10,
  "min_score": 50.0
}
```

Response:
```json
{
  "query": "software development",
  "total_results": 8,
  "took_ms": 45.2,
  "results": [
    {
      "code": "541511",
      "title": "Custom Computer Programming Services",
      "category": "technology",
      "level": 6,
      "score": 85.0,
      "match_type": "title_contains",
      "matched_terms": ["Custom Computer Programming Services"],
      "keywords": ["software", "programming", "development"],
      "aliases": ["Software Development", "Custom Software"]
    }
  ]
}
```

---

GET /api/v1/naics/autocomplete - Autocomplete Suggestions
Query: ?q=soft&limit=5
Response: Array of suggested codes

---

GET /api/v1/naics/{code}/hierarchy - Get Code Hierarchy
Response: Full hierarchy from sector to national industry

---

GET /api/v1/naics/{code}/children - Get Child Codes
Response: Array of immediate children

---

GET /api/v1/naics/{code}/parent - Get Parent Code
Response: Parent code object

---

GET /api/v1/naics/categories/summary - Category Statistics
Response: Summary of codes by category

---

GET /api/v1/naics/categories/list - List All Categories
Response: Array of all 14 NAICS categories

[SEE: CHUNK logic-naics-overview-001 - NAICS system implementation]
[SEE: CHUNK logic-naics-search-001 - Search capabilities]
[/CHUNK]

[CHUNK: api-health-001]
================================================================================
HEALTH ENDPOINTS
================================================================================

GET /api/v1/health - Basic Health Check

Response:
```json
{
  "status": "healthy",
  "version": "2.0.0",
  "environment": "development",
  "timestamp": "2025-11-21T10:30:00.000000"
}
```

---

GET /api/v1/health/details - Detailed Health with Database Info

Response:
```json
{
  "status": "healthy",
  "version": "2.0.0",
  "environment": "production",
  "database": {
    "status": "connected",
    "database": "levelith",
    "host": "dpg-xxxxx.render.com",
    "version": "PostgreSQL 15.3"
  },
  "timestamp": "2025-11-21T10:30:00.000000"
}
```

[/CHUNK]

[CHUNK: api-statistics-001]
================================================================================
GET /api/v1/statistics - Platform Statistics
================================================================================

Response:
```json
{
  "total_users": 1523,
  "total_experiences": 8945,
  "experiences_by_category": {
    "education": 2341,
    "workplace": 4123,
    "skills": 2481
  },
  "top_naics_codes": [
    {"code": "541511", "title": "Custom Computer Programming", "count": 234},
    {"code": "611110", "title": "Elementary Schools", "count": 189}
  ],
  "top_skills": ["Python", "JavaScript", "Leadership"],
  "user_growth": [
    {"month": "2025-01", "new_users": 45},
    {"month": "2025-02", "new_users": 67}
  ]
}
```

[/CHUNK]

[CHUNK: api-errors-001]
================================================================================
API ERROR HANDLING
================================================================================

STANDARD ERROR RESPONSE:

All errors return:
```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": { /* optional additional info */ }
  }
}
```

HTTP STATUS CODES:

| Code | Meaning               | When Used                        |
|------|-----------------------|----------------------------------|
| 200  | OK                    | Successful GET, PUT, PATCH       |
| 201  | Created               | Successful POST                  |
| 204  | No Content            | Successful DELETE                |
| 400  | Bad Request           | Invalid request data             |
| 401  | Unauthorized          | Missing or invalid token         |
| 403  | Forbidden             | Insufficient permissions         |
| 404  | Not Found             | Resource doesn't exist           |
| 409  | Conflict              | Duplicate resource               |
| 422  | Unprocessable Entity  | Validation errors                |
| 429  | Too Many Requests     | Rate limit exceeded              |
| 500  | Internal Server Error | Server-side error                |

COMMON ERROR CODES:

| Code                    | Description                         |
|-------------------------|-------------------------------------|
| AUTH_INVALID_TOKEN      | Token invalid or expired            |
| AUTH_MISSING_TOKEN      | No token provided                   |
| USER_NOT_FOUND          | User doesn't exist                  |
| USER_ALREADY_EXISTS     | Username/email taken                |
| EXPERIENCE_NOT_FOUND    | Experience doesn't exist            |
| INVALID_NAICS_CODE      | NAICS code not valid                |
| VALIDATION_ERROR        | Request data validation failed      |
| RATE_LIMIT_EXCEEDED     | Too many requests                   |

RATE LIMIT RESPONSE:

```
HTTP/1.1 429 Too Many Requests
```
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please try again later.",
    "retry_after": 60
  }
}
```

Headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1642000000
```

[/CHUNK]

[CHUNK: api-pagination-001]
================================================================================
API PAGINATION
================================================================================

Standard pagination format for list endpoints:

RESPONSE STRUCTURE:
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "page_size": 50,
    "total_items": 150,
    "total_pages": 3,
    "has_next": true,
    "has_previous": false
  }
}
```

QUERY PARAMETERS:
- page: Page number (default: 1)
- page_size: Items per page (default: 50, max: 200)
- limit: Alias for page_size
- offset: Alternative to page-based pagination

EXAMPLES:

```
GET /api/v1/users?page=2&page_size=25
GET /api/v1/experiences?offset=100&limit=50
GET /api/v1/naics/paginated?page=3&page_size=100
```

[/CHUNK]

================================================================================
SECTION 6: FRONTEND ARCHITECTURE
================================================================================

[CHUNK: ui-overview-001]
================================================================================
FRONTEND ARCHITECTURE OVERVIEW
================================================================================

IMPLEMENTATION STATUS: Complete for Admin Dashboard
Branch: claude/setup-ai-agent-dev-01KKaocgt5SDtaL2WqqBTRSu

ARCHITECTURE:
- Main App (Landing Pages, Docs): Tailwind CSS + ONETRUTH
- Admin Dashboard: Inline Styles + ONETRUTH (NO Tailwind)

FEATURE-BASED STRUCTURE:
```
frontend/src/admin/features/{feature}/
├── api/           # API client methods
├── components/    # Feature-specific components
├── hooks/         # Feature-specific hooks
├── schemas/       # Zod validation schemas
├── types/         # TypeScript types
└── index.ts       # Barrel export
```

SEPARATION OF CONCERNS (4 Layers):

Layer 1: API Client (*.api.ts)
  - Direct axios calls to backend
  - Type-safe response handling
  - Error handling

Layer 2: React Query Hooks (*.queries.ts, *.mutations.ts)
  - Query hooks for fetching data
  - Mutation hooks for modifications
  - Cache management
  - Optimistic updates

Layer 3: Composite Hook (use*.ts)
  - Unified interface
  - State management (pagination, search, filters)
  - Business logic
  - Promise-based API

Layer 4: UI Components (*.tsx)
  - Presentation logic
  - User interactions
  - TanStack Table integration
  - Accessible UI

[SEE: CHUNK ui-transformation-layer-001 - API transformation layer]
[SEE: CHUNK ui-admin-overview-001 - Admin dashboard features]
[SEE: CHUNK config-onetruth-001 - ONETRUTH branding system]
[/CHUNK]

[CHUNK: ui-transformation-layer-001]
[SPLIT-CANDIDATE: transformation-layer-guide]
================================================================================
GLOBAL API TRANSFORMATION LAYER
================================================================================

FILES:
- frontend/src/lib/transformers.ts (188 lines)
- frontend/src/lib/__tests__/transformers.test.ts (309 lines, 31 tests)
- frontend/src/lib/api.ts (interceptors)

PURPOSE: Transparently convert between snake_case (backend) and camelCase (frontend)

FUNCTIONS PROVIDED:
```typescript
// String transformations
snakeToCamel('user_name')    // → 'userName'
camelToSnake('userName')     // → 'user_name'

// Object transformations (deep/recursive)
keysToCamel({ user_name: 'John', is_active: true })
// → { userName: 'John', isActive: true }

keysToSnake({ userName: 'John', isActive: true })
// → { user_name: 'John', is_active: true }

// Paginated response normalization
transformPaginatedResponse(response)
// Handles both array and { items: [...], total: N } format
// Returns: { data: [...], total: N, page: 1, pageSize: 50, totalPages: N }
```

HOW IT WORKS:

Frontend sends (camelCase):
```typescript
const data = { userName: 'john', isActive: true }
```

API interceptor transforms to (snake_case):
```javascript
// Backend receives: { user_name: 'john', is_active: true }
```

Backend responds (snake_case):
```json
{ "user_name": "john", "is_active": true, "created_at": "2025-01-01" }
```

API interceptor transforms to (camelCase):
```typescript
// Frontend receives: { userName: 'john', isActive: true, createdAt: '2025-01-01' }
```

AUTOMATIC TRANSFORMATION (lib/api.ts):

```typescript
// Request interceptor - transforms outgoing data
apiClient.interceptors.request.use((config) => {
  if (config.data) {
    config.data = keysToSnake(config.data)
  }
  if (config.params) {
    config.params = keysToSnake(config.params)
  }
  return config
})

// Response interceptor - transforms incoming data
apiClient.interceptors.response.use((response) => {
  if (response.data) {
    response.data = keysToCamel(response.data)
  }
  return response
})
```

SMART HANDLING:
- Nested objects: Recursively transforms all levels
- Arrays: Transforms all items in arrays
- Date objects: Preserves Date instances
- Null/undefined: Safely handles missing values
- Primitives: Leaves strings, numbers, booleans unchanged
- Type safety: Full TypeScript support with generics

TEST COVERAGE:
- 31 test cases covering all scenarios
- Edge cases: null, undefined, primitives, Date objects, nested arrays
- Run tests: cd frontend && npm test transformers.test.ts

USAGE EXAMPLE:

```typescript
// No manual transformation needed!
import { useUsers } from '@/admin/features/users/hooks/useUsers'

function UsersPage() {
  const { data, isLoading } = useUsers({
    pageSize: 50,        // Sent as page_size
    sortBy: 'createdAt'  // Sent as sort_by
  })

  // data.users is already in camelCase
  return (
    <div>
      {data.users.map(user => (
        <div key={user.id}>
          {user.firstName} {user.lastName}  {/* Already camelCase! */}
          {user.isActive ? '✅' : '❌'}
        </div>
      ))}
    </div>
  )
}
```

[/CHUNK]

[CHUNK: ui-admin-overview-001]
[SPLIT-CANDIDATE: admin-dashboard-guide-standalone]
================================================================================
ADMIN DASHBOARD IMPLEMENTATION
================================================================================

OVERVIEW:

The admin dashboard provides complete management of users, experiences, and NAICS codes. Built with React, TypeScript, React Query, and TanStack Table.

INFRASTRUCTURE STATUS:

Phase 0 (Setup): COMPLETE - 15/15 tasks
- TypeScript configuration with path aliases
- ESLint and Prettier setup
- Feature-based folder structure
- Type definitions for all entities
- Zod validation schemas
- Utility functions (formatters, validators, constants)

Phase 1 (Core Infrastructure): COMPLETE - 20/20 tasks
- API client methods for all endpoints
- Custom React hooks (useDebounce, usePagination, useLocalStorage, useDisclosure)
- Reusable UI components (DataTable, SearchBar, FilterPanel, Pagination, etc.)

Phase 2 (Users Feature): COMPLETE - 25/25 tasks
- Full CRUD operations
- Server-side pagination
- Search and filtering
- User statistics widget

ROUTES:

/admin/users          → UsersPage (list view)
/admin/users/new      → CreateUserPage (to be implemented)
/admin/users/:id      → UserDetailPage
/admin/users/:id/edit → EditUserPage (to be implemented)

CUSTOM COMPONENTS:

| Component        | Purpose                              |
|------------------|--------------------------------------|
| DataTable        | TanStack Table wrapper, pagination   |
| SearchBar        | Debounced search input               |
| FilterPanel      | Collapsible filter controls          |
| Pagination       | Full pagination controls             |
| LoadingSpinner   | Loading indicator                    |
| LoadingSkeleton  | Skeleton loading placeholders        |
| ErrorBoundary    | Error catching and display           |
| ConfirmDialog    | Confirmation modal                   |
| UserForm         | Create/Edit user form                |
| UserStats        | Dashboard statistics widget          |

CUSTOM HOOKS:

| Hook            | Purpose                              |
|-----------------|--------------------------------------|
| useDebounce     | Delay value updates (search)         |
| usePagination   | Pagination state management          |
| useLocalStorage | Type-safe localStorage with sync     |
| useDisclosure   | Open/close state for modals          |

KEY FEATURES:

Users Feature:
- Create user with full profile
- Read user details with experiences
- Update user information
- Delete single user with confirmation
- Bulk delete users
- Search by username or email
- Filter by active/inactive status
- Filter by verified/unverified status
- User avatar display with fallback initials
- Responsive design

NAICS Feature:
- Server-side pagination (50 items/page)
- Edit modal with tags, custom_category, admin_notes
- Delete confirmation with warning
- Enhanced table with Tags and Actions columns
- Search and filter support

STYLING:

Admin dashboard uses INLINE STYLES with ONETRUTH exclusively.
NO Tailwind CSS classes in admin components.
All colors, spacing, fonts reference ONETRUTH configuration.

[SEE: CHUNK ui-users-feature-001 - Users feature implementation]
[SEE: CHUNK config-onetruth-001 - ONETRUTH styling rules]
[/CHUNK]

[CHUNK: ui-users-feature-001]
================================================================================
USERS FEATURE IMPLEMENTATION
================================================================================

FILES CREATED: 8
FILES MODIFIED: 3
TOTAL LINES: ~2,740 (implementation) + ~1,493 (integration) = ~4,233

BREAKDOWN:
- users.queries.ts: 180 lines
- users.mutations.ts: 340 lines
- useUsers.ts (composite hook): 430 lines
- UsersTable.tsx: 490 lines
- transformers.ts: 188 lines
- transformers.test.ts: 309 lines
- UsersPage.tsx: 97 lines
- UserDetailPage.tsx: 327 lines
- FormButton.jsx: 65 lines

REACT QUERY HOOKS:

Queries (users.queries.ts):
- useUsers() - Fetch paginated users with filters
- useUser(id) - Fetch single user by ID
- useUserStats() - Fetch user statistics
- useUsersQuery() - Advanced query with custom options
- userKeys - Query key factory for cache management

Mutations (users.mutations.ts):
- useCreateUser() - Create new user
- useUpdateUser() - Update user with optimistic updates
- useDeleteUser() - Delete user with rollback
- useBulkDeleteUsers() - Delete multiple users
- useToggleUserActive() - Toggle user active status

COMPOSITE HOOK (useUsers.ts):

Unified interface combining all operations:

```typescript
const {
  // Data
  users,
  totalUsers,
  isLoading,
  error,

  // Pagination
  page,
  pageSize,
  totalPages,
  setPage,
  nextPage,
  previousPage,

  // Search & Filters
  search,
  setSearch,
  filters,
  setFilters,

  // Sorting
  sortBy,
  sortOrder,
  setSorting,

  // CRUD Operations
  createUser,
  updateUser,
  deleteUser,
  bulkDeleteUsers,
  toggleUserActive,

  // Mutation States
  isCreating,
  isUpdating,
  isDeleting,
} = useUsers({
  initialPageSize: 50,
  onUserCreated: (user) => console.log('Created:', user),
  onError: (err) => console.error(err)
})
```

USERSTABLE COMPONENT:

Features:
[x] TanStack Table v8 integration
[x] Server-side pagination
[x] Search by username/email
[x] Filter by active/verified status
[x] Column sorting
[x] Row selection (bulk operations)
[x] CRUD action buttons (Edit, Delete)
[x] Bulk delete with confirmation
[x] Loading spinner
[x] Error state handling
[x] Empty state with helpful message
[x] Responsive design
[x] Accessible UI

Columns:
1. Selection checkbox (bulk actions)
2. Username (clickable, opens detail)
3. Email
4. Status (Active/Inactive badge, toggleable)
5. Verified (Verified/Unverified badge)
6. Experience Count
7. Created Date
8. Actions (Edit, Delete buttons)

Usage:
```typescript
<UsersTable
  onViewUser={(user) => navigate(`/admin/users/${user.id}`)}
  onEditUser={(user) => setEditingUser(user)}
  initialPageSize={25}
  showBulkActions={true}
  showSearch={true}
  showFilters={true}
/>
```

USER DETAIL PAGE:

Features:
- Breadcrumb navigation (Admin / Users / {username})
- Comprehensive user information display
- Account information card
- Profile information card (if exists)
- Quick stats sidebar
- Action buttons (Edit, Back, View Experiences)
- Loading and error states
- Formatted dates and status badges

DATA FLOW:

```
UsersPage → UsersTable → useUsers → React Query
    │           │           │            │
    │           │           │            ↓
    │           │           └──→ users.queries.ts
    │           │                users.mutations.ts
    │           │                     │
    │           │                     ↓
    │           └──────────────→ users.api.ts
    │                               │
    └───────────────────────────────┘
                                    │
                                    ↓
                           lib/api.ts (interceptors)
                                    │
                          ┌─────────┴─────────┐
                          │                   │
                 Request Interceptor  Response Interceptor
                  (camelCase →         (snake_case
                   snake_case)         → camelCase)
                          │                   │
                          └─────────┬─────────┘
                                    │
                           lib/transformers.ts
                                    │
                                    ↓
                            BACKEND (FastAPI)
```

[SEE: CHUNK ui-transformation-layer-001 - Transformation layer details]
[/CHUNK]

[CHUNK: ui-data-flow-001]
================================================================================
FRONTEND DATA FLOW ARCHITECTURE
================================================================================

COMPONENT HIERARCHY:

```
App.tsx
  └─ DataSourceProvider
      └─ AdminApp.jsx
          └─ AdminLayout
              └─ Routes
                  ├─ UsersPage.tsx
                  │   └─ UsersTable.tsx (from features/users/components)
                  │       └─ useUsers (composite hook)
                  │           ├─ useUsersQuery (React Query)
                  │           ├─ useCreateUser (mutations)
                  │           ├─ useUpdateUser (mutations)
                  │           └─ useDeleteUser (mutations)
                  │
                  └─ UserDetailPage.tsx
                      └─ useUser (React Query)
                          └─ getUserById (API)
```

REACT QUERY CONFIGURATION:

```typescript
{
  staleTime: 1000 * 60 * 5,    // 5 minutes
  gcTime: 1000 * 60 * 10,       // 10 minutes
  retry: 1,                      // Retry once on failure
  refetchOnWindowFocus: false,
}
```

PERFORMANCE OPTIMIZATIONS:
- Automatic caching (React Query)
- Optimistic updates (immediate UI feedback)
- Automatic rollback on error
- Background refetching
- Stale-while-revalidate pattern

[/CHUNK]

================================================================================
END OF PART 3
================================================================================

CONTINUATION:
[SEE: levelith-masterfile-v1-claude-part4.md - Development Workflow, Quality Standards, Appendices]

================================================================================
