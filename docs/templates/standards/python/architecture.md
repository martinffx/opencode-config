# Python Architecture Standards

## Layered Architecture Patterns

### Pattern 1: Standard CRUD API (FastAPI)

```
Client → Router → Service (Domain) → Repository → Database
                     ↓
                  Entity
```

### Pattern 2: External API Integration

```
Client → Router → Service (Domain) → Client → External API
                     ↓
                  Entity
```

### Pattern 3: Event-Driven (Consumer)

```
Event Source → Consumer → Service (Domain) → Repository → Database
                            ↓
                         Entity
```

### Pattern 4: Event-Driven (Producer)

```
Client → Router → Service (Domain) → Producer → Event Broker
                     ↓
                  Entity
```

## Python-Specific Layer Implementation

### Router Layer (FastAPI)

```python
from fastapi import APIRouter, HTTPException, Depends
from .service import UserService
from .entities import UserEntity

router = APIRouter(prefix="/users", tags=["users"])

@router.post("/", response_model=UserResponse)
async def create_user(
    request: CreateUserRequest,
    service: UserService = Depends()
) -> UserResponse:
    try:
        entity = UserEntity.from_request(request)
        result = await service.create_user(entity)
        return result.to_response()
    except ValidationError as e:
        raise HTTPException(status_code=422, detail=str(e))
    except BusinessRuleError as e:
        raise HTTPException(status_code=400, detail=str(e))
```

### Service Layer (Domain Logic)

```python
from typing import List, Optional
from .repositories import UserRepository
from .entities import UserEntity

class UserService:
    def __init__(self, repository: UserRepository):
        self.repository = repository

    async def create_user(self, entity: UserEntity) -> UserEntity:
        # Validate business rules
        validation = entity.validate()
        if not validation.is_valid:
            raise ValidationError(validation.errors)

        # Check duplicates
        existing = await self.repository.find_by_email(entity.email)
        if existing:
            raise BusinessRuleError("Email already exists")

        # Persist and return
        record = entity.to_record()
        created = await self.repository.create(record)
        return UserEntity.from_record(created)
```

### Entity Layer (Domain Model)

```python
from dataclasses import dataclass
from typing import Dict, Any
from datetime import datetime
import re

@dataclass
class UserEntity:
    id: Optional[int] = None
    email: str = ""
    name: str = ""
    created_at: Optional[datetime] = None

    @classmethod
    def from_request(cls, dto: CreateUserRequest) -> "UserEntity":
        return cls(
            email=dto.email.lower().strip(),
            name=dto.name.strip()
        )

    @classmethod
    def from_record(cls, record: Dict[str, Any]) -> "UserEntity":
        return cls(
            id=record["id"],
            email=record["email"],
            name=record["name"],
            created_at=record["created_at"]
        )

    def to_record(self) -> Dict[str, Any]:
        return {
            "email": self.email,
            "name": self.name,
            "created_at": datetime.utcnow()
        }

    def to_response(self) -> UserResponse:
        return UserResponse(
            id=self.id,
            email=self.email,
            name=self.name,
            created_at=self.created_at
        )

    def validate(self) -> ValidationResult:
        errors = []

        # Email validation
        if not self.email:
            errors.append("Email is required")
        elif not re.match(r"[^@]+@[^@]+\.[^@]+", self.email):
            errors.append("Invalid email format")

        # Name validation
        if not self.name or len(self.name.strip()) < 2:
            errors.append("Name must be at least 2 characters")

        return ValidationResult(is_valid=len(errors) == 0, errors=errors)
```

### Repository Layer (SQLAlchemy)

```python
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from .models import UserModel
from typing import List, Optional, Dict, Any

class UserRepository:
    def __init__(self, db: AsyncSession):
        self.db = db

    async def create(self, data: Dict[str, Any]) -> Dict[str, Any]:
        db_user = UserModel(**data)
        self.db.add(db_user)
        await self.db.commit()
        await self.db.refresh(db_user)
        return self._to_dict(db_user)

    async def find_by_id(self, user_id: int) -> Optional[Dict[str, Any]]:
        result = await self.db.execute(
            select(UserModel).where(UserModel.id == user_id)
        )
        user = result.scalar_one_or_none()
        return self._to_dict(user) if user else None

    async def find_by_email(self, email: str) -> Optional[Dict[str, Any]]:
        result = await self.db.execute(
            select(UserModel).where(UserModel.email == email.lower())
        )
        user = result.scalar_one_or_none()
        return self._to_dict(user) if user else None

    def _to_dict(self, user: UserModel) -> Dict[str, Any]:
        return {
            "id": user.id,
            "email": user.email,
            "name": user.name,
            "created_at": user.created_at
        }
```

## Dependency Injection (FastAPI)

### Application Setup

```python
from fastapi import FastAPI, Depends
from sqlalchemy.ext.asyncio import AsyncSession
from .database import get_async_session
from .domains.users.services import UserService
from .domains.users.repositories import UserRepository

app = FastAPI()

# Dependency injection chain
def get_user_repository(db: AsyncSession = Depends(get_async_session)) -> UserRepository:
    return UserRepository(db)

def get_user_service(repository: UserRepository = Depends(get_user_repository)) -> UserService:
    return UserService(repository)

# Router uses injected service
app.include_router(user_router, prefix="/api/v1")
```

## Database Integration (SQLAlchemy + PostgreSQL)

### Model Definition

```python
from sqlalchemy import Column, Integer, String, DateTime
from sqlalchemy.sql import func
from .database import Base

class UserModel(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True, nullable=False)
    name = Column(String, nullable=False)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
```

### Async Session Management

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession, async_sessionmaker
from sqlalchemy.orm import declarative_base

Base = declarative_base()

class Database:
    def __init__(self, database_url: str):
        self.engine = create_async_engine(database_url)
        self.async_session = async_sessionmaker(
            self.engine, class_=AsyncSession, expire_on_commit=False
        )

    async def get_session(self) -> AsyncSession:
        async with self.async_session() as session:
            yield session

# FastAPI dependency
async def get_async_session() -> AsyncSession:
    async with database.async_session() as session:
        yield session
```

## External API Integration with VCR.py

### Client Layer with VCR Recording

```python
import httpx
from vcr import VCR
from typing import Dict, Any

class ExternalAPIClient:
    def __init__(self, base_url: str):
        self.base_url = base_url
        self.client = httpx.AsyncClient()

    async def get_user_data(self, user_id: int) -> Dict[str, Any]:
        response = await self.client.get(f"{self.base_url}/users/{user_id}")
        response.raise_for_status()
        return response.json()

# VCR configuration for testing
vcr = VCR(
    cassette_library_dir='tests/cassettes',
    record_mode='once',
    match_on=['method', 'scheme', 'host', 'port', 'path', 'query'],
    filter_headers=['authorization'],
    filter_post_data_parameters=['password', 'token']
)

# Usage in tests
@vcr.use_cassette
async def test_external_api_integration():
    client = ExternalAPIClient("https://api.example.com")
    data = await client.get_user_data(123)
    assert "id" in data
```

## Error Handling (Python)

### Domain Exceptions

```python
class DomainError(Exception):
    def __init__(self, message: str):
        self.message = message
        super().__init__(message)

class ValidationError(DomainError):
    pass

class BusinessRuleError(DomainError):
    pass

class NotFoundError(DomainError):
    pass
```

### Error Translation in Router

```python
from fastapi import HTTPException

@router.exception_handler(ValidationError)
async def validation_exception_handler(request, exc: ValidationError):
    return HTTPException(
        status_code=422,
        detail={"error": "validation", "message": exc.message}
    )

@router.exception_handler(BusinessRuleError)
async def business_rule_exception_handler(request, exc: BusinessRuleError):
    return HTTPException(
        status_code=400,
        detail={"error": "business_rule", "message": exc.message}
    )

@router.exception_handler(NotFoundError)
async def not_found_exception_handler(request, exc: NotFoundError):
    return HTTPException(
        status_code=404,
        detail={"error": "not_found", "message": exc.message}
    )
```

## Docker Test Database Setup

### pytest Fixture for Test Database

```python
import pytest
import asyncio
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker
from docker import DockerClient, types
from yourapp.database import Base
from yourapp.domains.users.repositories import UserRepository

@pytest.fixture(scope="session")
def event_loop():
    """Create an instance of the default event loop for the test session."""
    loop = asyncio.get_event_loop_policy().new_event_loop()
    yield loop
    loop.close()

@pytest.fixture(scope="session")
async def postgres_container():
    """Start PostgreSQL container for tests."""
    docker_client = DockerClient.from_env()

    container = docker_client.containers.run(
        "postgres:15",
        environment={
            "POSTGRES_DB": "test_db",
            "POSTGRES_USER": "test_user",
            "POSTGRES_PASSWORD": "test_pass"
        },
        ports={"5432/tcp": None},  # Random port
        detach=True,
        remove=True
    )

    # Wait for database to be ready
    await asyncio.sleep(10)

    # Get the mapped port
    container.reload()
    port = container.ports["5432/tcp"][0]["HostPort"]

    yield f"postgresql+asyncpg://test_user:test_pass@localhost:{port}/test_db"

    container.stop()

@pytest.fixture
async def test_db(postgres_container):
    """Create test database session."""
    engine = create_async_engine(postgres_container)

    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

    async_session = sessionmaker(
        engine, class_=AsyncSession, expire_on_commit=False
    )

    async with async_session() as session:
        yield session

    await engine.dispose()
```

## Integration Testing with FastAPI TestClient

### Integration Test Example

```python
import pytest
from httpx import AsyncClient
from yourapp.main import app
from yourapp.database import get_async_session

@pytest.mark.asyncio
async def test_create_user_integration(test_db):
    """Test full HTTP → Service → Repository → Database flow."""

    # Override database dependency
    app.dependency_overrides[get_async_session] = lambda: test_db

    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.post("/api/v1/users/", json={
            "email": "test@example.com",
            "name": "Test User"
        })

    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "test@example.com"
    assert data["name"] == "Test User"
    assert "id" in data

    # Clean up
    app.dependency_overrides.clear()

@pytest.mark.asyncio
@vcr.use_cassette
async def test_external_api_integration_with_vcr():
    """Test external API integration with VCR recording."""
    from yourapp.clients import ExternalAPIClient

    client = ExternalAPIClient("https://jsonplaceholder.typicode.com")
    data = await client.get_user_data(1)

    assert "id" in data
    assert "name" in data
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}

