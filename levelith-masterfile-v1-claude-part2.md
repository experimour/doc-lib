================================================================================
LEVELITH-2 MASTER DOCUMENTATION - PART 2 OF 4
================================================================================
Project: Levelith-2 - AI-First Social Resume Gamification Platform
Organization: Semour Media Group
Generated: 2025-11-21
Purpose: Database Layer and NAICS System
================================================================================

[CONTINUATION FROM PART 1]

================================================================================
SECTION 3: DATABASE LAYER
================================================================================

[CHUNK: schema-overview-001]
[SPLIT-CANDIDATE: database-reference-standalone]
================================================================================
DATABASE OVERVIEW
================================================================================

The Levelith database consists of 3 primary tables:

| Table        | Rows (est.) | Purpose                              |
|--------------|-------------|--------------------------------------|
| users        | Variable    | User accounts and profiles           |
| experiences  | Variable    | Professional experiences (9 types)   |
| naics_codes  | 60+ (expandable) | NAICS 2022 industry classification |

DATABASE STACK:
- PostgreSQL 15 (managed on Render)
- SQLAlchemy ORM (2.0+)
- Alembic migrations

DESIGN PHILOSOPHY:
- Single table inheritance for experiences (9 types, 1 table)
- Denormalized NAICS hierarchy for fast queries
- JSONB for flexible metadata (profile_data, type_specific_data)

CONNECTION MANAGEMENT:
- Use Depends(get_db) in FastAPI routes
- Sessions auto-close after requests
- Context manager for scripts: with get_db_context() as db:

RELATIONSHIPS:
- users → experiences: One-to-Many (CASCADE delete)
- experiences → naics_codes: Many-to-One (reference)
- naics_codes → naics_codes: Self-referential (parent_code)

LAYERED ARCHITECTURE:

┌───────────────────────────────────────────────┐
│  API Layer (FastAPI Routes)                   │  ← HTTP request/response
│  - Input validation (Pydantic)                │
│  - Depends(get_db) for session injection      │
└────────────────┬──────────────────────────────┘
                 ↓
┌───────────────────────────────────────────────┐
│  Service Layer (Business Logic)               │  ← Business rules
│  - User registration, authentication          │
│  - Experience creation and validation         │
│  - NAICS code lookups and suggestions         │
└────────────────┬──────────────────────────────┘
                 ↓
┌───────────────────────────────────────────────┐
│  Repository Layer (Data Access)               │  ← Data abstraction
│  - CRUD operations                            │
│  - Query building                             │
│  - Database abstraction                       │
└────────────────┬──────────────────────────────┘
                 ↓
┌───────────────────────────────────────────────┐
│  ORM Layer (SQLAlchemy Models)                │  ← Type definitions
│  - UserDB, ExperienceDB, NAICSCodeDB          │
│  - Relationships and constraints              │
└────────────────┬──────────────────────────────┘
                 ↓
┌───────────────────────────────────────────────┐
│  PostgreSQL                                   │  ← Physical storage
│  - Transactions and ACID guarantees           │
│  - Indexes and constraints                    │
└───────────────────────────────────────────────┘

WHY POSTGRESQL:
- ACID Compliance: Ensures data integrity and consistency
- JSON Support: Native JSONB columns for flexible data
- Performance: Excellent query optimization and indexing
- Scalability: Handles millions of records efficiently
- Open Source: No licensing costs, community-driven

WHY SQLALCHEMY 2.0:
- Type Safety: Python type hints with ORM models
- Flexibility: Both ORM and raw SQL support
- Abstraction: Database-agnostic queries
- Relationships: Automatic JOIN handling
- Testing: Easy to mock and test

ARCHITECTURAL DECISION: Single Table Inheritance
Problem: Need to store 9 different experience types with shared and unique fields
Solution: Single table with polymorphic inheritance + JSON for type-specific data

Implementation:
```python
class ExperienceDB(Base):
    __tablename__ = "experiences"
    experience_type = Column(SQLEnum(ExperienceType))
    type_specific_data = Column(JSON)  # Type-specific fields

class CertificateDB(ExperienceDB):
    __mapper_args__ = {'polymorphic_identity': ExperienceType.CERTIFICATE}
```

Pros:
- Simple queries (single table)
- Easy to add new types
- Referential integrity maintained
- Good performance

Cons:
- Sparse columns (many NULLs) - mitigated by JSON field

[SEE: CHUNK schema-users-001 - Users table schema]
[SEE: CHUNK schema-experiences-001 - Experiences table schema]
[SEE: CHUNK schema-naics-001 - NAICS codes table schema]
[/CHUNK]

[CHUNK: schema-users-001]
================================================================================
USERS TABLE SCHEMA
================================================================================

CREATE TABLE users (
    -- Identity
    id                 VARCHAR(32)  PRIMARY KEY,
    username           VARCHAR(50)  UNIQUE NOT NULL,
    email              VARCHAR(255) UNIQUE NOT NULL,
    password_hash      VARCHAR(255) NOT NULL,

    -- Profile
    is_active          BOOLEAN      DEFAULT TRUE NOT NULL,
    is_verified        BOOLEAN      DEFAULT FALSE NOT NULL,
    profile_data       JSONB        DEFAULT '{}' NOT NULL,

    -- Timestamps
    created_at         TIMESTAMP    DEFAULT NOW() NOT NULL,
    updated_at         TIMESTAMP    DEFAULT NOW() NOT NULL,
    last_login         TIMESTAMP
);

CREATE UNIQUE INDEX idx_users_username ON users(username);
CREATE UNIQUE INDEX idx_users_email ON users(email);

SQLALCHEMY MODEL:

```python
class UserDB(Base):
    """User ORM model for database persistence."""
    __tablename__ = "users"

    # Identity Fields
    id = Column(String(32), primary_key=True,
                default=lambda: secrets.token_hex(16))
    username = Column(String(50), unique=True, nullable=False, index=True)
    password_hash = Column(String(255), nullable=False)
    email = Column(String(255), unique=True, nullable=False, index=True)

    # Profile Fields
    is_active = Column(Boolean, default=True, nullable=False)
    is_verified = Column(Boolean, default=False, nullable=False)
    profile_data = Column(JSON, default=dict, nullable=False)

    # Timestamp Fields
    created_at = Column(DateTime, default=datetime.utcnow, nullable=False)
    updated_at = Column(DateTime, default=datetime.utcnow,
                       onupdate=datetime.utcnow, nullable=False)
    last_login = Column(DateTime, nullable=True)

    # Relationships
    experiences = relationship("ExperienceDB", back_populates="user",
                             cascade="all, delete-orphan")
```

COLUMN REFERENCE:

| Column        | Type         | Nullable | Default              | Description                    |
|---------------|--------------|----------|----------------------|--------------------------------|
| id            | VARCHAR(32)  | NO       | secrets.token_hex(16)| Unique user identifier (hex)   |
| username      | VARCHAR(50)  | NO       | -                    | Unique username (3-50 chars)   |
| email         | VARCHAR(255) | NO       | -                    | Unique email address           |
| password_hash | VARCHAR(255) | NO       | -                    | Hashed password (PBKDF2/bcrypt)|
| is_active     | BOOLEAN      | NO       | TRUE                 | Account active status          |
| is_verified   | BOOLEAN      | NO       | FALSE                | Email verification status      |
| profile_data  | JSONB        | NO       | {}                   | Flexible profile metadata      |
| created_at    | TIMESTAMP    | NO       | NOW()                | Account creation timestamp     |
| updated_at    | TIMESTAMP    | NO       | NOW()                | Last update timestamp          |
| last_login    | TIMESTAMP    | YES      | NULL                 | Last login timestamp           |

CONSTRAINTS:
- Primary Key: id
- Unique: username, email
- Not Null: id, username, email, password_hash, is_active, is_verified, profile_data, created_at, updated_at

PROFILE_DATA JSON SCHEMA:

```json
{
  "bio": "Software engineer passionate about...",
  "location": "San Francisco, CA",
  "avatar_url": "https://example.com/avatar.jpg",
  "website": "https://example.com",
  "social": {
    "github": "username",
    "linkedin": "profile-url",
    "twitter": "@handle"
  },
  "preferences": {
    "theme": "dark",
    "notifications": true
  }
}
```

CRUD OPERATIONS:

CREATE:
```python
user = UserDB(
    username="john_doe",
    email="john@example.com",
    password_hash=hash_password("secure_password"),
    profile_data={"bio": "Software engineer"}
)
db.add(user)
db.commit()
db.refresh(user)
```

READ:
```python
# By ID
user = db.query(UserDB).filter(UserDB.id == user_id).first()

# By email
user = db.query(UserDB).filter(UserDB.email == "john@example.com").first()

# By username
user = db.query(UserDB).filter(UserDB.username == "john_doe").first()

# Active users
users = db.query(UserDB).filter(UserDB.is_active == True).all()
```

UPDATE:
```python
user = db.query(UserDB).filter(UserDB.id == user_id).first()
user.username = "johndoe"
user.profile_data["bio"] = "Updated bio"
db.commit()
```

DELETE (cascades to experiences):
```python
user = db.query(UserDB).filter(UserDB.id == user_id).first()
db.delete(user)  # All user's experiences automatically deleted
db.commit()
```

SECURITY CONSIDERATIONS:
- PASSWORD STORAGE: NEVER store plain text passwords. Use bcrypt, argon2, or PBKDF2 with minimum 10 rounds
- EMAIL VERIFICATION: Use is_verified flag, send verification email on signup
- ACCOUNT STATUS: Use is_active flag for deactivation instead of deletion for audit trails

[SEE: CHUNK schema-experiences-001 - Experiences table with relationships]
[/CHUNK]

[CHUNK: schema-experiences-001]
================================================================================
EXPERIENCES TABLE SCHEMA
================================================================================

CREATE TABLE experiences (
    -- Identity
    id                    VARCHAR(32)  PRIMARY KEY,
    user_id               VARCHAR(32)  REFERENCES users(id) ON DELETE CASCADE NOT NULL,

    -- Core
    title                 VARCHAR(255) NOT NULL,
    description           TEXT,
    naics_code            VARCHAR(6)   DEFAULT '123456' NOT NULL,

    -- Classification
    category              VARCHAR(20)  NOT NULL,
    experience_type       VARCHAR(20)  NOT NULL,

    -- Dates
    start_date            TIMESTAMP,
    end_date              TIMESTAMP,
    is_current            BOOLEAN      DEFAULT FALSE,

    -- Organization
    organization          VARCHAR(255),
    location              VARCHAR(255),

    -- Flexible Data
    type_specific_data    JSONB        DEFAULT '{}' NOT NULL,
    tags                  JSONB        DEFAULT '[]' NOT NULL,
    experience_metadata   JSONB        DEFAULT '{}' NOT NULL,

    -- Timestamps
    created_at            TIMESTAMP    DEFAULT NOW() NOT NULL,
    updated_at            TIMESTAMP    DEFAULT NOW() NOT NULL
);

CREATE INDEX idx_experiences_user_id ON experiences(user_id);
CREATE INDEX idx_experiences_category ON experiences(category);
CREATE INDEX idx_experiences_type ON experiences(experience_type);
CREATE INDEX idx_experiences_naics ON experiences(naics_code);

COLUMN REFERENCE:

| Column              | Type         | Nullable | Default   | Description               |
|---------------------|--------------|----------|-----------|---------------------------|
| id                  | VARCHAR(32)  | NO       | token_hex | Unique experience ID      |
| user_id             | VARCHAR(32)  | NO       | -         | Foreign key to users.id   |
| title               | VARCHAR(255) | NO       | -         | Experience title          |
| description         | TEXT         | YES      | NULL      | Detailed description      |
| naics_code          | VARCHAR(6)   | NO       | '123456'  | NAICS industry code       |
| category            | VARCHAR(20)  | NO       | -         | education/workplace/skills|
| experience_type     | VARCHAR(20)  | NO       | -         | 9 specific types          |
| start_date          | TIMESTAMP    | YES      | NULL      | Start date                |
| end_date            | TIMESTAMP    | YES      | NULL      | End date                  |
| is_current          | BOOLEAN      | NO       | FALSE     | Currently active          |
| organization        | VARCHAR(255) | YES      | NULL      | Organization/institution  |
| location            | VARCHAR(255) | YES      | NULL      | Location                  |
| type_specific_data  | JSONB        | NO       | {}        | Type-specific fields      |
| tags                | JSONB        | NO       | []        | Searchable tags           |
| experience_metadata | JSONB        | NO       | {}        | Additional metadata       |
| created_at          | TIMESTAMP    | NO       | NOW()     | Creation timestamp        |
| updated_at          | TIMESTAMP    | NO       | NOW()     | Last update               |

CATEGORY AND TYPE ENUMS:

Categories:
- education
- workplace
- skills

Experience Types by Category:

Education:
- certificate: Professional certifications
- degree: Academic degrees
- course: Individual courses

Workplace:
- gig: Short-term contracts
- part_time: Part-time employment
- full_time: Full-time employment

Skills:
- soft_skill: Interpersonal skills
- hard_skill: Technical skills
- native_skill: Innate abilities/languages

TYPE_SPECIFIC_DATA SCHEMAS:

Certificate:
```json
{
  "certification_number": "ABC123",
  "certifying_body": "AWS",
  "expiration_date": "2025-12-31",
  "is_renewable": true
}
```

Degree:
```json
{
  "degree_level": "Bachelor",
  "major": "Computer Science",
  "minor": "Mathematics",
  "gpa": 3.8,
  "honors": ["Dean's List", "Cum Laude"]
}
```

Course:
```json
{
  "course_code": "CS101",
  "credits": 3,
  "grade": "A",
  "instructor": "Dr. Smith"
}
```

Gig:
```json
{
  "contract_type": "1099",
  "hourly_rate": 150,
  "total_hours": 120
}
```

Part-Time:
```json
{
  "hours_per_week": 20,
  "employment_type": "w2",
  "department": "Engineering"
}
```

Full-Time:
```json
{
  "job_title": "Senior Software Engineer",
  "department": "Engineering",
  "team_size": 8,
  "responsibilities": ["Led team of 5", "Architected system"]
}
```

Soft Skill:
```json
{
  "skill_level": "advanced",
  "contexts": ["team leadership", "public speaking"]
}
```

Hard Skill:
```json
{
  "proficiency_level": "expert",
  "years_experience": 5,
  "tools": ["Python", "FastAPI"]
}
```

Native Skill:
```json
{
  "skill_type": "language",
  "language": "Spanish",
  "proficiency": "native"
}
```

POLYMORPHIC ORM USAGE:

```python
# Query returns correct subtype automatically
experiences = db.query(ExperienceDB).all()
for exp in experiences:
    print(type(exp))  # DegreeDB, CertificateDB, etc.

# Access type-specific data
if isinstance(exp, DegreeDB):
    gpa = exp.type_specific_data.get("gpa")

# Filter by category
education = db.query(ExperienceDB).filter(
    ExperienceDB.user_id == user_id,
    ExperienceDB.category == "education"
).all()
```

RELATIONSHIP WITH USERS:

```python
# Eager load (single query with JOIN)
from sqlalchemy.orm import joinedload

user = db.query(UserDB).options(
    joinedload(UserDB.experiences)
).filter(UserDB.id == user_id).first()

# Access experiences (already loaded)
for exp in user.experiences:
    print(exp.title)

# Experience → User (many-to-one)
experience = db.query(ExperienceDB).filter(ExperienceDB.id == exp_id).first()
user = experience.user
```

[SEE: CHUNK schema-naics-001 - NAICS codes table for industry classification]
[/CHUNK]

[CHUNK: schema-naics-001]
================================================================================
NAICS CODES TABLE SCHEMA
================================================================================

CREATE TABLE naics_codes (
    -- Primary Key
    code                  VARCHAR(6)   PRIMARY KEY,

    -- Core
    title                 VARCHAR(500) NOT NULL,
    description           TEXT,

    -- Hierarchy
    level                 INTEGER      NOT NULL,
    parent_code           VARCHAR(6),
    sector                VARCHAR(2),
    subsector             VARCHAR(3),
    industry_group        VARCHAR(4),
    industry_detail       VARCHAR(6),

    -- Classification
    category              VARCHAR(50)  NOT NULL,

    -- SBA
    sba_size_standard     VARCHAR(255),
    sba_source            VARCHAR(255),

    -- Search
    keywords              JSONB        DEFAULT '[]' NOT NULL,
    aliases               JSONB        DEFAULT '[]' NOT NULL,
    examples              TEXT,
    cross_references      JSONB        DEFAULT '[]' NOT NULL,
    notes                 TEXT,

    -- Admin
    tags                  JSONB        DEFAULT '[]' NOT NULL,
    custom_category       VARCHAR(100),
    admin_notes           TEXT,

    -- Metadata
    is_active             BOOLEAN      DEFAULT TRUE NOT NULL,
    year                  INTEGER      DEFAULT 2022 NOT NULL,
    data_source           VARCHAR(255),

    -- Timestamps
    created_at            TIMESTAMP    DEFAULT NOW() NOT NULL,
    updated_at            TIMESTAMP    DEFAULT NOW() NOT NULL
);

CREATE INDEX idx_naics_level ON naics_codes(level);
CREATE INDEX idx_naics_sector ON naics_codes(sector);
CREATE INDEX idx_naics_subsector ON naics_codes(subsector);
CREATE INDEX idx_naics_category ON naics_codes(category);
CREATE INDEX idx_naics_parent ON naics_codes(parent_code);

HIERARCHY LEVELS:

| Level | Digits | Example  | Description        |
|-------|--------|----------|--------------------|
| 2     | XX     | 54       | Sector             |
| 3     | XXX    | 541      | Subsector          |
| 4     | XXXX   | 5415     | Industry Group     |
| 6     | XXXXXX | 541511   | National Industry  |

Note: 5-digit codes are NOT valid in NAICS system.

DENORMALIZED HIERARCHY FIELDS:

For code "541511":
- level: 6
- parent_code: "5415"
- sector: "54"
- subsector: "541"
- industry_group: "5415"
- industry_detail: "541511"

Benefits of denormalization:
- Fast queries: WHERE level = 2 (get all sectors)
- No string parsing needed
- Indexed for performance

NAICS CATEGORIES (14):

- AGRICULTURE_FORESTRY_FISHING
- MINING_QUARRYING
- UTILITIES
- CONSTRUCTION
- MANUFACTURING
- WHOLESALE_TRADE
- RETAIL_TRADE
- TRANSPORTATION_WAREHOUSING
- INFORMATION
- FINANCE_INSURANCE
- REAL_ESTATE
- PROFESSIONAL_TECHNICAL_SERVICES
- EDUCATIONAL_SERVICES
- HEALTHCARE

SEARCH FIELDS:

| Field            | Type       | Description                    |
|------------------|------------|--------------------------------|
| keywords         | JSON Array | Searchable keywords            |
| aliases          | JSON Array | Alternative names/synonyms     |
| examples         | Text       | Example businesses             |
| cross_references | JSON Array | Related NAICS codes            |

ADMIN FIELDS (editable via admin dashboard):

| Field           | Type       | Purpose                            |
|-----------------|------------|------------------------------------|
| tags            | JSON Array | Custom tags for organization       |
| custom_category | VARCHAR    | Custom classification              |
| admin_notes     | Text       | Internal notes about code          |

EXAMPLE QUERIES:

```sql
-- Get all sectors (2-digit)
SELECT * FROM naics_codes WHERE level = 2 ORDER BY code;

-- Get subsectors of a sector
SELECT * FROM naics_codes WHERE parent_code = '54' ORDER BY code;

-- Search by keyword
SELECT * FROM naics_codes
WHERE title ILIKE '%software%' OR description ILIKE '%software%';

-- Get hierarchy for a code (recursive)
WITH RECURSIVE hierarchy AS (
  SELECT * FROM naics_codes WHERE code = '541511'
  UNION ALL
  SELECT n.* FROM naics_codes n
  INNER JOIN hierarchy h ON n.code = h.parent_code
)
SELECT * FROM hierarchy;

-- Fast sector query using denormalized field
SELECT * FROM naics_codes WHERE sector = '54';
```

[SEE: CHUNK logic-naics-overview-001 - NAICS system implementation]
[SEE: CHUNK logic-naics-search-001 - Enhanced search capabilities]
[/CHUNK]

[CHUNK: database-setup-001]
================================================================================
DATABASE SETUP AND CONFIGURATION
================================================================================

ENVIRONMENT FILES:

Local Development (backend/.env):
```
DATABASE_URL=postgresql://levelith_user:password@localhost/levelith
SECRET_KEY=generated_secure_key
ENVIRONMENT=development
DEBUG=true
CORS_ORIGINS=["http://localhost:3000","http://localhost:5173"]
LOG_LEVEL=DEBUG
```

Production (.env.render.production):
```
DATABASE_URL=postgresql://user:pass@dpg-xxxxx.oregon-postgres.render.com/levelith
SECRET_KEY=generated_secure_key
ENVIRONMENT=production
DEBUG=false
CORS_ORIGINS=["https://levelith-frontend.onrender.com","https://levelith.online"]
LOG_LEVEL=INFO
```

IMPORTANT: Never commit .env files to Git. Use .env.example as template.

DATABASE URL FORMAT:

Render PostgreSQL URL:
```
postgresql://user:password@dpg-{id}.{region}-postgres.render.com/database
```

Internal URL (recommended for Render services):
```
postgresql://levelith_user:xxx@dpg-xxx-a.oregon-postgres.render.com/levelith
```

External URL (for external access, includes port):
```
postgresql://levelith_user:xxx@dpg-xxx-a.oregon-postgres.render.com:5432/levelith
```

INITIALIZATION:

From Python:
```python
from backend.database import init_db
init_db()  # Create all tables
```

From Command Line:
```bash
# Test database connection
python init_db.py --check

# Initialize database (create tables)
python init_db.py

# Reset database (WARNING: destroys all data - dev only!)
python init_db.py --reset
```

GET DATABASE SESSION:

FastAPI (Dependency Injection):
```python
from backend.database import get_db
from fastapi import Depends
from sqlalchemy.orm import Session

@app.get("/users")
def list_users(db: Session = Depends(get_db)):
    users = db.query(UserDB).all()
    return users
```

Scripts (Context Manager):
```python
from backend.database import get_db_context

with get_db_context() as db:
    user = db.query(UserDB).filter(UserDB.id == user_id).first()
    print(user.username)
```

CONNECTION POOLING:

Configuration:
```python
pool_size = 5          # Base pool size
max_overflow = 10      # Additional connections during peak
pool_pre_ping = True   # Verify connections before use
```

Behavior:
1. Request arrives → Pool provides connection
2. Pool empty → Create overflow connection (up to 10)
3. Request completes → Return connection to pool
4. Idle connections → Maintained for reuse
Total max connections = 15

[SEE: CHUNK database-usage-001 - Database usage patterns]
[SEE: CHUNK database-testing-001 - Database testing strategies]
[/CHUNK]

[CHUNK: database-usage-001]
================================================================================
DATABASE USAGE GUIDE
================================================================================

COMMON CRUD PATTERNS:

CREATE:
```python
from backend.models.db_models import UserDB
import secrets

user = UserDB(
    username="john_doe",
    email="john@example.com",
    password_hash=hash_password("password"),
    profile_data={"bio": "Engineer"}
)
db.add(user)
db.commit()
db.refresh(user)  # Get updated data (timestamps, defaults)
```

READ:
```python
# Single record
user = db.query(UserDB).filter(UserDB.id == user_id).first()
user = db.query(UserDB).filter(UserDB.email == email).first()

# Multiple records
users = db.query(UserDB).filter(UserDB.is_active == True).all()

# With pagination
users = db.query(UserDB).limit(10).offset(20).all()
```

UPDATE:
```python
user = db.query(UserDB).filter(UserDB.id == user_id).first()
user.username = "new_username"
user.profile_data = {"bio": "Updated bio"}
db.commit()
db.refresh(user)
```

DELETE:
```python
user = db.query(UserDB).filter(UserDB.id == user_id).first()
db.delete(user)  # Cascades to experiences
db.commit()
```

PAGINATION:

```python
def get_users_paginated(db: Session, page: int = 1, page_size: int = 50):
    offset = (page - 1) * page_size
    users = db.query(UserDB).offset(offset).limit(page_size).all()
    total = db.query(UserDB).count()
    return {
        "items": users,
        "total": total,
        "page": page,
        "page_size": page_size,
        "pages": (total + page_size - 1) // page_size
    }
```

SEARCH:

```python
def search_users(db: Session, query: str):
    users = db.query(UserDB).filter(
        UserDB.username.ilike(f"%{query}%") |
        UserDB.email.ilike(f"%{query}%")
    ).all()
    return users
```

FILTERING:

```python
# Multiple filters
experiences = db.query(ExperienceDB).filter(
    ExperienceDB.user_id == user_id,
    ExperienceDB.category == "education",
    ExperienceDB.is_current == True
).all()

# OR conditions
from sqlalchemy import or_
experiences = db.query(ExperienceDB).filter(
    or_(
        ExperienceDB.experience_type == "degree",
        ExperienceDB.experience_type == "certificate"
    )
).all()
```

BEST PRACTICES:

DO:
- Use Dependency Injection: db: Session = Depends(get_db)
- Use Parameterized Queries: .filter(UserDB.email == email)
- Close Sessions Automatically (context managers)
- Use Transactions for related operations

DON'T:
- Create Engine/Session Manually
- Keep Sessions Open (use context managers)
- Use String Interpolation (SQL injection risk)
- Ignore N+1 Query Problems (use joinedload)

ERROR HANDLING:

```python
from sqlalchemy.exc import IntegrityError, SQLAlchemyError

try:
    user = UserDB(username="duplicate", email="existing@example.com")
    db.add(user)
    db.commit()
except IntegrityError as e:
    db.rollback()
    if "unique constraint" in str(e).lower():
        raise ValueError("Username or email already exists")
    raise
except SQLAlchemyError as e:
    db.rollback()
    logger.error(f"Database error: {e}")
    raise
```

[SEE: CHUNK database-testing-001 - Testing database operations]
[/CHUNK]

[CHUNK: database-testing-001]
================================================================================
DATABASE TESTING GUIDE
================================================================================

TEST DATABASE SETUP:

```python
# conftest.py
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from backend.database import Base
from backend.models.db_models import UserDB, ExperienceDB

@pytest.fixture(scope="function")
def test_db():
    """Create test database for each test."""
    # Use in-memory SQLite for speed
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)

    SessionLocal = sessionmaker(bind=engine)
    db = SessionLocal()

    yield db

    db.close()
    Base.metadata.drop_all(engine)
```

FACTORY FIXTURES:

```python
@pytest.fixture
def create_user(test_db):
    """Factory for creating test users."""
    def _create_user(**kwargs):
        defaults = {
            "username": f"testuser_{secrets.token_hex(4)}",
            "email": f"test_{secrets.token_hex(4)}@example.com",
            "password_hash": "hashed_password",
            "is_active": True,
            "is_verified": False
        }
        defaults.update(kwargs)

        user = UserDB(**defaults)
        test_db.add(user)
        test_db.commit()
        test_db.refresh(user)
        return user

    return _create_user

# Usage
def test_user_creation(create_user):
    user = create_user(username="john_doe")
    assert user.username == "john_doe"
```

TEST PATTERNS:

CRUD Testing:
```python
class TestUserCRUD:
    def test_create(self, test_db):
        user = UserDB(username="test", email="test@example.com",
                     password_hash="hash")
        test_db.add(user)
        test_db.commit()
        assert user.id is not None

    def test_read(self, test_db, create_user):
        user = create_user()
        found = test_db.query(UserDB).filter(UserDB.id == user.id).first()
        assert found.username == user.username

    def test_update(self, test_db, create_user):
        user = create_user()
        user.username = "updated"
        test_db.commit()
        updated = test_db.query(UserDB).filter(UserDB.id == user.id).first()
        assert updated.username == "updated"

    def test_delete(self, test_db, create_user):
        user = create_user()
        user_id = user.id
        test_db.delete(user)
        test_db.commit()
        deleted = test_db.query(UserDB).filter(UserDB.id == user_id).first()
        assert deleted is None
```

Relationship Testing:
```python
def test_user_experiences_relationship(test_db, create_user):
    user = create_user()

    exp1 = ExperienceDB(user_id=user.id, title="Experience 1",
                       category="education", experience_type="degree")
    exp2 = ExperienceDB(user_id=user.id, title="Experience 2",
                       category="workplace", experience_type="full_time")

    test_db.add_all([exp1, exp2])
    test_db.commit()

    test_db.refresh(user)
    assert len(user.experiences) == 2
```

Cascade Delete Testing:
```python
def test_cascade_delete(test_db, create_user):
    user = create_user()
    exp = ExperienceDB(user_id=user.id, title="Test",
                      category="education", experience_type="degree")
    test_db.add(exp)
    test_db.commit()
    exp_id = exp.id

    test_db.delete(user)
    test_db.commit()

    deleted_exp = test_db.query(ExperienceDB).filter(
        ExperienceDB.id == exp_id
    ).first()
    assert deleted_exp is None
```

BEST PRACTICES:

DO:
- Use In-Memory SQLite for Speed: sqlite:///:memory:
- Isolate Tests: @pytest.fixture(scope="function")
- Use Factories for Test Data
- Test Edge Cases (unique constraints, etc.)
- Maintain 80% Coverage: pytest --cov=backend --cov-report=html

DON'T:
- Use Production Database
- Share State Between Tests
- Skip Cleanup (always close sessions)

RUN TESTS:

```bash
# Run with coverage
pytest --cov=backend --cov-report=term-missing

# Generate HTML report
pytest --cov=backend --cov-report=html
open htmlcov/index.html

# Run specific test file
pytest tests/test_database.py -v
```

[SEE: CHUNK tests-strategies-001 - Overall testing strategies]
[/CHUNK]

================================================================================
SECTION 4: NAICS SYSTEM
================================================================================

[CHUNK: logic-naics-overview-001]
[SPLIT-CANDIDATE: naics-guide-standalone]
================================================================================
NAICS SYSTEM IMPLEMENTATION
================================================================================

OVERVIEW:

The NAICS (North American Industry Classification System) implementation provides comprehensive industry classification for the Levelith platform. Implementation status: COMPLETE with database integration.

COMPONENTS:

1. Domain Layer (backend/models/naics.py) - 420 LOC
   - NAICSCode dataclass with complete metadata
   - NAICSLevel enum (SECTOR, SUBSECTOR, INDUSTRY_GROUP, NATIONAL_INDUSTRY)
   - NAICSCategory enum (14 industry categories)
   - Validation functions: normalize_naics_code(), get_naics_level(), extract_parent_codes()
   - Factory function: create_naics_code()
   - Hierarchical operations: get_hierarchy(), is_parent_of(), is_child_of()

2. Data Layer (backend/data/naics_codes_2022.json)
   - 60+ official NAICS codes
   - Expandable via TSV import

3. Repository Layer (backend/repositories/)
   - naics_repository.py (in-memory, 380 LOC)
   - naics_db_repository.py (PostgreSQL, 380 LOC)
   - Same interface for both implementations

4. Service Layer (backend/services/naics_service.py) - 360 LOC
   - Code validation against official NAICS database
   - Smart suggestions based on experience types
   - Search and autocomplete functionality
   - Category and level filtering
   - Hierarchical operations

5. API Layer (backend/api/routes/naics.py) - 560 LOC
   - 15 REST endpoints

6. Test Coverage - 197 tests (98% coverage)

NAICS HIERARCHY:

Level 2 (Sector): "54" - Professional Services
└── Level 3 (Subsector): "541" - Professional, Scientific, Technical
    └── Level 4 (Industry Group): "5415" - Computer Systems Design
        └── Level 6 (National Industry): "541511" - Custom Programming

EXPERIENCE TYPE MAPPING:

| Experience Type | Suggested NAICS Categories              |
|-----------------|----------------------------------------|
| Education       | Education, Professional Services       |
| Workplace       | Technology, Finance, Healthcare, etc.  |
| Skills          | General fallback                       |

REPOSITORY PERFORMANCE:

| Operation          | Time Complexity | Notes                    |
|--------------------|-----------------|--------------------------|
| find_by_code()     | O(1)            | Direct dictionary lookup |
| find_by_category() | O(k)            | k = codes in category    |
| find_by_level()    | O(k)            | k = codes at level       |
| search_by_title()  | O(n + m)        | n = keywords, m = results|
| get_children()     | O(n)            | n = total codes          |
| get_parent()       | O(1)            | Direct lookup            |
| get_hierarchy()    | O(d)            | d = depth (max 4)        |

MEMORY USAGE:
- NAICS Codes: ~60 codes × 500 bytes ≈ 30 KB
- Indexes: ~150 entries × 100 bytes ≈ 15 KB
- Total: ~45 KB in memory (very lightweight)

[SEE: CHUNK logic-naics-import-001 - NAICS data import procedures]
[SEE: CHUNK logic-naics-search-001 - Enhanced search features]
[SEE: CHUNK api-naics-001 - NAICS API endpoints]
[/CHUNK]

[CHUNK: logic-naics-import-001]
================================================================================
NAICS DATA IMPORT GUIDE
================================================================================

IMPORT OPTIONS:

1. Seed from JSON (Fastest - Development):
```bash
python backend/seed_naics.py
```
Loads 60+ reference NAICS codes from existing JSON file.

2. Import from TSV (Complete - Production):
```bash
python backend/import_naics.py docs/dev/naics-import.tsv
```

TSV FILE FORMAT:

Required Columns:
| Column | Type   | Required | Description                          |
|--------|--------|----------|--------------------------------------|
| code   | string | YES      | NAICS code (2, 3, 4, or 6 digits)    |
| title  | string | YES      | Official title/name                  |

Optional Columns:
| Column      | Type    | Default | Description             |
|-------------|---------|---------|-------------------------|
| description | text    | -       | Detailed description    |
| category    | string  | general | Industry category       |
| parent_code | string  | -       | Parent NAICS code       |
| is_active   | boolean | TRUE    | Whether code is active  |
| year        | integer | 2022    | NAICS version year      |

Valid Categories:
technology, education, healthcare, finance, manufacturing,
retail, hospitality, construction, agriculture, transportation,
professional_services, arts_entertainment, public_administration, general

Sample TSV:
```
code	title	description	category	parent_code	is_active	year
11	Agriculture, Forestry...	Establishments...	agriculture		TRUE	2022
111	Crop Production	Establishments...	agriculture	11	TRUE	2022
541511	Custom Computer Programming	Writing software...	technology	5415	TRUE	2022
```

IMPORT COMMANDS:

```bash
# Basic import
python backend/import_naics.py data.tsv

# Clear existing data first (BACKUP FIRST!)
python backend/import_naics.py --clear data.tsv

# Verbose output
python backend/import_naics.py --verbose data.tsv

# Generate sample file
python backend/import_naics.py --sample sample.tsv
```

SEEDING COMMANDS:

```bash
# Seed from JSON
python backend/seed_naics.py

# Clear and seed
python backend/seed_naics.py --clear

# Verbose output
python backend/seed_naics.py --verbose
```

VALIDATION RULES:

Code Format:
- Must be 2, 3, 4, or 6 digits
- Cannot be 1, 5, or 7+ digits
- No letters or special characters

Required Fields:
- code must be present
- title must be present
- Empty values rejected

Duplicate Handling:
- Same code multiple times in TSV → only first occurrence imported
- Code exists in database → updated with new data

POST-IMPORT VERIFICATION:

```bash
# Start backend
cd backend && uvicorn main:app --reload

# Test endpoints
curl http://localhost:8000/api/v1/naics/541511
curl http://localhost:8000/api/v1/naics/search?q=software
curl http://localhost:8000/api/v1/naics/categories/summary
```

OFFICIAL DATA SOURCES:
- U.S. Census Bureau: https://www.census.gov/naics/
- NAICS 2022 Manual: https://www.census.gov/naics/?58967?yearbck=2022

[SEE: CHUNK logic-naics-search-001 - Search and keyword population]
[/CHUNK]

[CHUNK: logic-naics-search-001]
================================================================================
ENHANCED NAICS SEARCH SYSTEM
================================================================================

OVERVIEW:

The enhanced NAICS search system leverages keywords, aliases, and advanced search capabilities. The database has 22 columns in the naics_codes table.

ENHANCED COLUMNS:

Denormalized Hierarchy (4 columns):
- sector: 2-digit sector code
- subsector: 3-digit subsector code
- industry_group: 4-digit industry group code
- industry_detail: 6-digit national industry code
Benefit: Fast queries without string parsing

SBA Integration (2 columns):
- sba_size_standard: Small Business Administration size classifications
- sba_source: Source reference for SBA data
Benefit: Business size/eligibility information

Enhanced Search (3 columns):
- keywords: JSON array of searchable terms
- aliases: JSON array of alternative names/synonyms
- examples: Text examples of businesses in category
Benefit: Much better search accuracy and user experience

RELEVANCE SCORING:

| Match Type           | Score | Description                    |
|----------------------|-------|--------------------------------|
| exact_code           | 100   | Code exactly matches query     |
| code_prefix          | 90    | Code starts with query         |
| exact_title          | 85    | Title exactly matches query    |
| title_prefix         | 80    | Title starts with query        |
| exact_alias          | 75    | Alias exactly matches query    |
| exact_keyword        | 70    | Keyword exactly matches query  |
| title_contains       | 60    | Title contains query           |
| alias_contains       | 55    | Alias contains query           |
| keyword_contains     | 50    | Keyword contains query         |
| description_contains | 40    | Description contains query     |

DATABASE INDEXES FOR SEARCH:

```sql
-- Hierarchy indexes
CREATE INDEX ix_naics_codes_sector ON naics_codes(sector);
CREATE INDEX ix_naics_codes_subsector ON naics_codes(subsector);
CREATE INDEX ix_naics_codes_industry_group ON naics_codes(industry_group);

-- JSON indexes for PostgreSQL
CREATE INDEX ix_naics_codes_keywords_gin ON naics_codes USING GIN (keywords);
CREATE INDEX ix_naics_codes_aliases_gin ON naics_codes USING GIN (aliases);
```

QUERY OPTIMIZATION:

Good (uses indexed denormalized field):
```python
codes = db.query(NAICSCodeDB).filter_by(sector="54").all()
```

Slower (string parsing):
```python
codes = db.query(NAICSCodeDB).filter(NAICSCodeDB.code.like("54%")).all()
```

KEYWORD POPULATION:

```bash
# Populate keywords from titles
python -m backend.utils.populate_naics_keywords keywords --commit

# Populate predefined aliases
python -m backend.utils.populate_naics_keywords aliases --commit

# Add custom alias
python -m backend.utils.populate_naics_keywords add-alias \
  --code 541511 --alias "App Development" --commit

# Validate data
python -m backend.utils.populate_naics_keywords validate
```

[SEE: CHUNK api-naics-001 - NAICS API endpoints with search]
[/CHUNK]

================================================================================
END OF PART 2
================================================================================

CONTINUATION:
[SEE: levelith-masterfile-v1-claude-part3.md - API Layer, Frontend Architecture]
[SEE: levelith-masterfile-v1-claude-part4.md - Development Workflow, Quality Standards, Appendices]

================================================================================
