# Python Coding Standards

## Stub-Driven TDD Approach

### 3-Step Process

1. **Create Stub** - Method signatures that raise `NotImplementedError`
2. **Write Test** - Test against stub expecting NotImplementedError, then write tests for actual behavior
3. **Implement** - Replace stub with working code to make tests pass

### Refined TDD Flow

1. **Stub** - Method throws `NotImplementedError`
2. **Red** - Write test, run failing test
3. **Green** - Implement method, run passing tests
4. **Refactor** - Clean up code

### Implementation Order

Always implement in dependency order (bottom-up):

```
Entity → Repository → Service → Router
```

## Stub Pattern (Python)

### Entity Stub

```python
from dataclasses import dataclass
from typing import Dict, Any, Optional
from datetime import datetime

@dataclass
class UserEntity:
    id: Optional[int] = None
    email: str = ""
    name: str = ""
    created_at: Optional[datetime] = None

    @classmethod
    def from_request(cls, dto: Any) -> "UserEntity":
        raise NotImplementedError("Not Implemented: UserEntity.from_request")

    @classmethod
    def from_record(cls, record: Dict[str, Any]) -> "UserEntity":
        raise NotImplementedError("Not Implemented: UserEntity.from_record")

    def to_record(self) -> Dict[str, Any]:
        raise NotImplementedError("Not Implemented: UserEntity.to_record")

    def to_response(self) -> Any:
        raise NotImplementedError("Not Implemented: UserEntity.to_response")

    def validate(self) -> "ValidationResult":
        raise NotImplementedError("Not Implemented: UserEntity.validate")
```

### Repository Stub

```python
from typing import List, Optional, Dict, Any
from sqlalchemy.ext.asyncio import AsyncSession

class UserRepository:
    def __init__(self, db: AsyncSession):
        self.db = db

    async def create(self, data: Dict[str, Any]) -> Dict[str, Any]:
        raise NotImplementedError("Not Implemented: UserRepository.create")

    async def find_by_id(self, user_id: int) -> Optional[Dict[str, Any]]:
        raise NotImplementedError("Not Implemented: UserRepository.find_by_id")

    async def find_by_email(self, email: str) -> Optional[Dict[str, Any]]:
        raise NotImplementedError("Not Implemented: UserRepository.find_by_email")

    async def update(self, user_id: int, data: Dict[str, Any]) -> Optional[Dict[str, Any]]:
        raise NotImplementedError("Not Implemented: UserRepository.update")

    async def delete(self, user_id: int) -> bool:
        raise NotImplementedError("Not Implemented: UserRepository.delete")
```

### Service Stub

```python
from typing import List, Optional
from .repositories import UserRepository
from .entities import UserEntity

class UserService:
    def __init__(self, repository: UserRepository):
        self.repository = repository

    async def create_user(self, entity: UserEntity) -> UserEntity:
        raise NotImplementedError("Not Implemented: UserService.create_user")

    async def get_user(self, user_id: int) -> Optional[UserEntity]:
        raise NotImplementedError("Not Implemented: UserService.get_user")

    async def update_user(self, user_id: int, entity: UserEntity) -> Optional[UserEntity]:
        raise NotImplementedError("Not Implemented: UserService.update_user")

    async def delete_user(self, user_id: int) -> bool:
        raise NotImplementedError("Not Implemented: UserService.delete_user")
```

### Router Stub (FastAPI)

```python
from fastapi import APIRouter, HTTPException, Depends
from typing import List
from .service import UserService
from .requests import CreateUserRequest, UpdateUserRequest
from .responses import UserResponse

router = APIRouter(prefix="/users", tags=["users"])

@router.post("/", response_model=UserResponse)
async def create_user(
    request: CreateUserRequest,
    service: UserService = Depends(get_user_service)
) -> UserResponse:
    raise NotImplementedError("Not Implemented: create_user endpoint")

@router.get("/{user_id}", response_model=UserResponse)
async def get_user(
    user_id: int,
    service: UserService = Depends(get_user_service)
) -> UserResponse:
    raise NotImplementedError("Not Implemented: get_user endpoint")

@router.put("/{user_id}", response_model=UserResponse)
async def update_user(
    user_id: int,
    request: UpdateUserRequest,
    service: UserService = Depends(get_user_service)
) -> UserResponse:
    raise NotImplementedError("Not Implemented: update_user endpoint")

@router.delete("/{user_id}")
async def delete_user(
    user_id: int,
    service: UserService = Depends(get_user_service)
) -> dict:
    raise NotImplementedError("Not Implemented: delete_user endpoint")
```

## Testing Pattern (pytest + pytest-asyncio)

### Step 1 - Test the Stub

```python
import pytest
from yourapp.entities import UserEntity
from yourapp.requests import CreateUserRequest

def test_user_entity_from_request_stub():
    request = CreateUserRequest(email="test@example.com", name="Test User")

    with pytest.raises(NotImplementedError, match="Not Implemented: UserEntity.from_request"):
        UserEntity.from_request(request)
```

### Step 2 - Test Expected Behavior (Red)

```python
def test_user_entity_from_request_success():
    request = CreateUserRequest(email="test@example.com", name="Test User")
    entity = UserEntity.from_request(request)

    assert entity.email == "test@example.com"
    assert entity.name == "Test User"

def test_user_entity_validation_invalid_email():
    entity = UserEntity(email="invalid-email", name="Test User")
    result = entity.validate()

    assert not result.is_valid
    assert "Invalid email format" in result.errors

def test_user_entity_validation_missing_name():
    entity = UserEntity(email="test@example.com", name="")
    result = entity.validate()

    assert not result.is_valid
    assert "Name is required" in result.errors
```

### Step 3 - Implementation (Green)

```python
# After implementing the methods, tests should pass
```

### Step 4 - Refactor

```python
# Clean up code while maintaining test coverage
```

## Python-Specific TDD Patterns

### Async Testing with pytest-asyncio

```python
import pytest
from unittest.mock import AsyncMock, Mock
from yourapp.services import UserService
from yourapp.entities import UserEntity

@pytest.mark.asyncio
async def test_create_user_success():
    # Arrange
    mock_repository = AsyncMock()
    mock_repository.find_by_email.return_value = None
    mock_repository.create.return_value = {
        "id": 1,
        "email": "test@example.com",
        "name": "Test User",
        "created_at": datetime.utcnow()
    }

    service = UserService(mock_repository)
    entity = UserEntity(email="test@example.com", name="Test User")

    # Act
    result = await service.create_user(entity)

    # Assert
    assert result.email == "test@example.com"
    assert result.name == "Test User"
    mock_repository.find_by_email.assert_called_once_with("test@example.com")
    mock_repository.create.assert_called_once()

@pytest.mark.asyncio
async def test_create_user_duplicate_email():
    # Arrange
    mock_repository = AsyncMock()
    mock_repository.find_by_email.return_value = {"id": 1, "email": "test@example.com"}

    service = UserService(mock_repository)
    entity = UserEntity(email="test@example.com", name="Test User")

    # Act & Assert
    with pytest.raises(BusinessRuleError, match="Email already exists"):
        await service.create_user(entity)
```

### Unit Tests with Mocks (Core Layer)

```python
import pytest
from unittest.mock import AsyncMock, patch
from yourapp.services import UserService
from yourapp.entities import UserEntity

@pytest.mark.asyncio
async def test_service_unit_with_mock_repository():
    """Unit test: Service uses mock Repository"""
    # Arrange
    mock_repo = AsyncMock()
    mock_repo.find_by_email.return_value = None
    mock_repo.create.return_value = {"id": 1, "email": "test@example.com", "name": "Test"}

    service = UserService(mock_repo)
    entity = UserEntity(email="test@example.com", name="Test User")

    # Act
    result = await service.create_user(entity)

    # Assert
    assert result.email == "test@example.com"
    mock_repo.find_by_email.assert_called_once()
    mock_repo.create.assert_called_once()
```

### Integration Tests with Docker (Edge Layer)

```python
import pytest
from httpx import AsyncClient
from yourapp.main import app
from yourapp.database import get_async_session

@pytest.mark.asyncio
async def test_create_user_integration(test_db):
    """Integration test: HTTP → Service → Repository → Database"""
    # Override database dependency with test database
    app.dependency_overrides[get_async_session] = lambda: test_db

    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.post("/api/v1/users/", json={
            "email": "test@example.com",
            "name": "Test User"
        })

    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "test@example.com"

    # Clean up
    app.dependency_overrides.clear()
```

### VCR.py Testing for External APIs

```python
import pytest
from vcr import VCR
from yourapp.clients import ExternalAPIClient

vcr = VCR(
    cassette_library_dir='tests/cassettes',
    record_mode='once',
    match_on=['method', 'scheme', 'host', 'port', 'path', 'query'],
    filter_headers=['authorization'],
)

@pytest.mark.asyncio
@vcr.use_cassette
async def test_external_api_client():
    """Test external API client with VCR recording"""
    client = ExternalAPIClient("https://jsonplaceholder.typicode.com")
    data = await client.get_user_data(1)

    assert "id" in data
    assert "name" in data
    # This test will use recorded response on subsequent runs
```

## Code Style Standards

### Directory Structure

```
src/
├── domains/{feature}/        # Feature modules
│   ├── entities/            # Domain entities
│   ├── services/            # Business logic
│   ├── repositories/        # Data access
│   ├── routers/             # API endpoints
│   ├── requests.py          # Pydantic request models
│   ├── responses.py         # Pydantic response models
│   └── models.py            # SQLAlchemy models
├── shared/                   # Shared utilities
│   ├── database/
│   ├── errors/
│   └── types/
└── config/                   # Configuration
```

### File Naming

- **Entities**: `user.py` (class UserEntity)
- **Services**: `user.py` (class UserService)
- **Repositories**: `user.py` (class UserRepository)
- **Routers**: `user.py` (router = APIRouter())
- **Tests**: `test_user.py` or `user_test.py`
- **Models**: `user.py` (class UserModel)

### Naming Conventions

- **Classes**: PascalCase (`UserEntity`, `UserService`)
- **Functions/Methods**: snake_case (`create_user`, `find_by_id`)
- **Variables**: snake_case (`user_id`, `email_address`)
- **Constants**: SCREAMING_SNAKE_CASE (`MAX_RETRY_ATTEMPTS`, `API_BASE_URL`)
- **Private members**: underscore prefix (`_validate_email`, `_db_session`)

### Type Hints

```python
from typing import List, Optional, Dict, Any
from datetime import datetime

def create_user(
    email: str,
    name: str
) -> UserEntity:
    pass

async def find_user_by_id(
    user_id: int
) -> Optional[UserEntity]:
    pass
```

### Import Order

```python
# 1. Standard library
from typing import List, Optional, Dict, Any
from datetime import datetime

# 2. Third-party
from fastapi import APIRouter, Depends
from sqlalchemy.ext.asyncio import AsyncSession
import pytest
import vcr

# 3. Internal application
from .entities import UserEntity
from .repositories import UserRepository
from shared.errors import ValidationError
```

## Development Workflow

### Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=src

# Run specific test file
pytest tests/test_user.py

# Run with verbose output
pytest -v

# Run async tests only
pytest -m asyncio
```

### Code Quality

```bash
# Type checking
mypy src/

# Linting
ruff check src/
ruff format src/

# Security scanning
bandit -r src/

# VCR cassette management
pytest --record-mode=once  # Record new cassettes
pytest --record-mode=none  # Use existing cassettes only
```

### Development Server

```bash
# FastAPI development
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# With environment variables
uvicorn main:app --reload --env-file .env

# Docker test database
docker-compose -f test-docker.yml up -d
pytest
docker-compose -f test-docker.yml down
```

## Async Patterns

### Async/Await Best Practices

```python
# Good: Use async for I/O operations
async def create_user(self, entity: UserEntity) -> UserEntity:
    # Database call
    existing = await self.repository.find_by_email(entity.email)

    # Business logic (sync)
    if existing:
        raise BusinessRuleError("Email already exists")

    # Database call
    record = entity.to_record()
    created = await self.repository.create(record)

    return UserEntity.from_record(created)

# Bad: Mixing sync and async incorrectly
def create_user_bad(self, entity: UserEntity) -> UserEntity:
    # This will block the event loop
    existing = self.repository.find_by_email(entity.email)  # Missing await!
    return entity
```

### Async Context Managers

```python
# Database session management
async def get_user_service() -> AsyncGenerator[UserService, None]:
    async with get_async_session() as db:
        repository = UserRepository(db)
        yield UserService(repository)

# In router
@router.post("/")
async def create_user(
    request: CreateUserRequest,
    service: UserService = Depends(get_user_service)
) -> UserResponse:
    return await service.create_user(entity)
```

## Testing Strategy Summary

### Unit Tests (Core Layer)

- **Service Tests**: Mock Repository/Client, test business logic
- **Entity Tests**: Test validation and transformation logic
- **Repository Tests**: Mock database, test data operations

### Integration Tests (Edge Layer)

- **HTTP Tests**: FastAPI TestClient → Service → Repository → Database
- **External API Tests**: VCR.py for recording HTTP interactions
- **Database Tests**: Docker PostgreSQL containers

### Test Organization

```
tests/
├── unit/
│   ├── test_services/
│   ├── test_entities/
│   └── test_repositories/
├── integration/
│   ├── test_http/
│   ├── test_external_api/
│   └── test_database/
├── cassettes/  # VCR recordings
└── conftest.py  # pytest fixtures
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}

