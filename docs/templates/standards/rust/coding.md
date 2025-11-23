# Rust Coding Standards

## Stub-Driven TDD Approach

### 3-Step Process

1. **Create Stub** - Method signatures that panic with `todo!()` macro
2. **Write Test** - Test against stub expecting panic, then write tests for actual behavior
3. **Implement** - Replace stub with working code to make tests pass

### Refined TDD Flow

1. **Stub** - Method panics with `todo!()`
2. **Red** - Write test, run failing test
3. **Green** - Implement method, run passing tests
4. **Refactor** - Clean up code

### Implementation Order

Always implement in dependency order (bottom-up):

```
Entity → Repository → Service → Handler
```

## Stub Pattern (Rust)

### Entity Stub

```rust
use serde::{Deserialize, Serialize};
use chrono::{DateTime, Utc};
use std::collections::HashMap;

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct UserEntity {
    pub id: Option<i32>,
    pub email: String,
    pub name: String,
    pub created_at: Option<DateTime<Utc>>,
}

impl UserEntity {
    pub fn from_request(dto: CreateUserRequest) -> Result<Self, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserEntity.from_request")
    }

    pub fn from_record(record: HashMap<String, serde_json::Value>) -> Result<Self, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserEntity.from_record")
    }

    pub fn to_record(&self) -> HashMap<String, serde_json::Value> {
        todo!("Not Implemented: UserEntity.to_record")
    }

    pub fn to_response(&self) -> UserResponse {
        todo!("Not Implemented: UserEntity.to_response")
    }

    pub fn validate(&self) -> Result<(), ValidationError> {
        todo!("Not Implemented: UserEntity.validate")
    }
}
```

### Repository Stub

```rust
use async_trait::async_trait;
use std::collections::HashMap;

#[async_trait]
pub trait UserRepository: Send + Sync {
    async fn create(&self, data: HashMap<String, serde_json::Value>) -> Result<HashMap<String, serde_json::Value>, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserRepository.create")
    }

    async fn find_by_id(&self, user_id: i32) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserRepository.find_by_id")
    }

    async fn find_by_email(&self, email: &str) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserRepository.find_by_email")
    }

    async fn update(&self, user_id: i32, data: HashMap<String, serde_json::Value>) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserRepository.update")
    }

    async fn delete(&self, user_id: i32) -> Result<bool, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserRepository.delete")
    }
}

pub struct PostgresUserRepository {
    pool: PgPool,
}

impl PostgresUserRepository {
    pub fn new(pool: PgPool) -> Self {
        Self { pool }
    }
}

#[async_trait]
impl UserRepository for PostgresUserRepository {
    // All methods will panic with todo!() until implemented
}
```

### Service Stub

```rust
use std::sync::Arc;

pub struct UserService {
    repository: Arc<dyn UserRepository>,
}

impl UserService {
    pub fn new(repository: Arc<dyn UserRepository>) -> Self {
        Self { repository }
    }

    pub async fn create_user(&self, entity: UserEntity) -> Result<UserEntity, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserService.create_user")
    }

    pub async fn get_user(&self, user_id: i32) -> Result<UserEntity, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserService.get_user")
    }

    pub async fn update_user(&self, user_id: i32, entity: UserEntity) -> Result<UserEntity, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserService.update_user")
    }

    pub async fn delete_user(&self, user_id: i32) -> Result<bool, Box<dyn std::error::Error>> {
        todo!("Not Implemented: UserService.delete_user")
    }
}
```

### Handler Stub (Axum)

```rust
use axum::{
    extract::{Path, State},
    response::Json,
};
use crate::domains::users::{
    service::UserService,
    requests::CreateUserRequest,
    responses::UserResponse,
};

pub async fn create_user(
    State(service): State<UserService>,
    Json(request): Json<CreateUserRequest>,
) -> Result<Json<UserResponse>, AppError> {
    todo!("Not Implemented: create_user handler")
}

pub async fn get_user(
    State(service): State<UserService>,
    Path(user_id): Path<i32>,
) -> Result<Json<UserResponse>, AppError> {
    todo!("Not Implemented: get_user handler")
}

pub async fn update_user(
    State(service): State<UserService>,
    Path(user_id): Path<i32>,
    Json(request): Json<CreateUserRequest>,
) -> Result<Json<UserResponse>, AppError> {
    todo!("Not Implemented: update_user handler")
}

pub async fn delete_user(
    State(service): State<UserService>,
    Path(user_id): Path<i32>,
) -> Result<(), AppError> {
    todo!("Not Implemented: delete_user handler")
}
```

## Testing Pattern (cargo test + tokio-test)

### Step 1 - Test the Stub

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use crate::domains::users::requests::CreateUserRequest;

    #[test]
    fn test_user_entity_from_request_stub() {
        let request = CreateUserRequest {
            email: "test@example.com".to_string(),
            name: "Test User".to_string(),
        };

        let result = std::panic::catch_unwind(|| {
            UserEntity::from_request(request)
        });

        assert!(result.is_err());
        if let Err(panic_info) = result {
            let panic_msg = panic_info.downcast_ref::<&str>().unwrap_or(&"");
            assert!(panic_msg.contains("Not Implemented: UserEntity.from_request"));
        }
    }
}
```

### Step 2 - Test Expected Behavior (Red)

```rust
#[test]
fn test_user_entity_from_request_success() {
    let request = CreateUserRequest {
        email: "test@example.com".to_string(),
        name: "Test User".to_string(),
    };

    let entity = UserEntity::from_request(request).unwrap();

    assert_eq!(entity.email, "test@example.com");
    assert_eq!(entity.name, "Test User");
}

#[test]
fn test_user_entity_validation_invalid_email() {
    let entity = UserEntity {
        id: None,
        email: "invalid-email".to_string(),
        name: "Test User".to_string(),
        created_at: None,
    };

    let result = entity.validate();
    assert!(result.is_err());
    assert!(result.unwrap_err().message.contains("Invalid email format"));
}
```

### Step 3 - Implementation (Green)

```rust
// After implementing the methods, tests should pass
```

### Step 4 - Refactor

```rust
// Clean up code while maintaining test coverage
```

## Rust-Specific TDD Patterns

### Async Testing with tokio-test

```rust
#[cfg(test)]
mod service_tests {
    use super::*;
    use mockall::{predicate::*, mockall};
    use tokio_test;

    #[tokio::test]
    async fn test_create_user_success() {
        // Arrange
        let mut mock_repo = MockUserRepository::new();
        mock_repo
            .expect_find_by_email()
            .with(eq("test@example.com"))
            .returning(|_| Ok(None));

        let mut expected_record = std::collections::HashMap::new();
        expected_record.insert("id".to_string(), serde_json::Value::Number(serde_json::Number::from(1)));
        expected_record.insert("email".to_string(), serde_json::Value::String("test@example.com".to_string()));
        expected_record.insert("name".to_string(), serde_json::Value::String("Test User".to_string()));

        mock_repo
            .expect_create()
            .returning(move |_| Ok(expected_record.clone()));

        let service = UserService::new(Arc::new(mock_repo));
        let entity = UserEntity {
            id: None,
            email: "test@example.com".to_string(),
            name: "Test User".to_string(),
            created_at: None,
        };

        // Act
        let result = service.create_user(entity).await.unwrap();

        // Assert
        assert_eq!(result.email, "test@example.com");
        assert_eq!(result.name, "Test User");
        assert_eq!(result.id, Some(1));
    }

    #[tokio::test]
    async fn test_create_user_duplicate_email() {
        // Arrange
        let mut mock_repo = MockUserRepository::new();

        let mut existing_record = std::collections::HashMap::new();
        existing_record.insert("id".to_string(), serde_json::Value::Number(serde_json::Number::from(1)));
        existing_record.insert("email".to_string(), serde_json::Value::String("test@example.com".to_string()));

        mock_repo
            .expect_find_by_email()
            .with(eq("test@example.com"))
            .returning(move |_| Ok(Some(existing_record.clone())));

        let service = UserService::new(Arc::new(mock_repo));
        let entity = UserEntity {
            id: None,
            email: "test@example.com".to_string(),
            name: "Test User".to_string(),
            created_at: None,
        };

        // Act & Assert
        let result = service.create_user(entity).await;
        assert!(result.is_err());
        assert!(result.unwrap_err().to_string().contains("Email already exists"));
    }
}
```

### Unit Tests with Mocks (Core Layer)

```rust
#[cfg(test)]
mod unit_tests {
    use super::*;
    use mockall::{predicate::*, mockall};

    #[mockall::automock]
    trait MockUserRepository: UserRepository {
        // Mock all methods automatically
    }

    #[tokio::test]
    async fn test_service_unit_with_mock_repository() {
        // Arrange
        let mut mock_repo = MockUserRepository::new();
        mock_repo
            .expect_find_by_email()
            .with(eq("test@example.com"))
            .returning(|_| Ok(None));

        mock_repo
            .expect_create()
            .returning(|data| {
                let mut result = std::collections::HashMap::new();
                result.insert("id".to_string(), serde_json::Value::Number(serde_json::Number::from(1)));
                result.insert("email".to_string(), serde_json::Value::String("test@example.com".to_string()));
                result.insert("name".to_string(), serde_json::Value::String("Test User".to_string()));
                Ok(result)
            });

        let service = UserService::new(Arc::new(mock_repo));
        let entity = UserEntity {
            id: None,
            email: "test@example.com".to_string(),
            name: "Test User".to_string(),
            created_at: None,
        };

        // Act
        let result = service.create_user(entity).await.unwrap();

        // Assert
        assert_eq!(result.email, "test@example.com");
        assert_eq!(result.name, "Test User");
        assert_eq!(result.id, Some(1));
    }
}
```

### Integration Tests with Docker (Edge Layer)

```rust
#[cfg(test)]
mod integration_tests {
    use super::*;
    use axum_test::TestServer;
    use crate::test_helpers::get_test_db;

    #[tokio::test]
    async fn test_create_user_integration() {
        // Integration test: HTTP → Service → Repository → Database
        let test_db = get_test_db().await;
        let state = AppState::new(test_db.pool.clone()).await.unwrap();
        let app = create_app(state);
        let server = TestServer::new(app).unwrap();

        // Act
        let response = server
            .post("/api/v1/users/")
            .json(&serde_json::json!({
                "email": "test@example.com",
                "name": "Test User"
            }))
            .await;

        // Assert
        assert_eq!(response.status_code(), 201);
        let json: serde_json::Value = response.json();
        assert_eq!(json["email"], "test@example.com");
        assert_eq!(json["name"], "Test User");
        assert!(json["id"].is_number());
    }

    #[tokio::test]
    async fn test_get_user_not_found() {
        let test_db = get_test_db().await;
        let state = AppState::new(test_db.pool.clone()).await.unwrap();
        let app = create_app(state);
        let server = TestServer::new(app).unwrap();

        let response = server.get("/api/v1/users/999").await;

        assert_eq!(response.status_code(), 404);
    }
}
```

## Code Style Standards

### Directory Structure

```
src/
├── domains/{feature}/        # Feature modules
│   ├── entities/            # Domain entities
│   ├── services/            # Business logic
│   ├── repositories/        # Data access
│   ├── handlers/            # HTTP handlers
│   ├── requests.rs          # Request structs
│   ├── responses.rs         # Response structs
│   └── models.rs            # Database models
├── shared/                   # Shared utilities
│   ├── database/
│   ├── errors/
│   └── types/
└── config/                   # Configuration
```

### File Naming

- **Entities**: `user.rs` (struct UserEntity)
- **Services**: `user.rs` (struct UserService)
- **Repositories**: `user.rs` (trait UserRepository, struct PostgresUserRepository)
- **Handlers**: `user.rs` (handler functions)
- **Tests**: `user_tests.rs` or `tests/user.rs`
- **Models**: `user.rs` (struct UserModel)

### Naming Conventions

- **Structs/Enums**: PascalCase (`UserEntity`, `UserService`)
- **Functions/Methods**: snake_case (`create_user`, `find_by_id`)
- **Variables**: snake_case (`user_id`, `email_address`)
- **Constants**: SCREAMING_SNAKE_CASE (`MAX_RETRY_ATTEMPTS`, `API_BASE_URL`)
- **Modules**: snake_case (`user_service`, `user_repository`)

### Type Annotations

```rust
use std::collections::HashMap;

pub fn create_user(
    email: String,
    name: String
) -> Result<UserEntity, ValidationError> {
    // Implementation
}

pub async fn find_user_by_id(
    user_id: i32
) -> Result<Option<UserEntity>, Box<dyn std::error::Error>> {
    // Implementation
}
```

### Import Order

```rust
// 1. Standard library
use std::collections::HashMap;
use std::sync::Arc;

// 2. Third-party
use axum::{extract::*, response::*, routing::*};
use sqlx::{PgPool, Row};
use serde::{Deserialize, Serialize};
use tokio_test;
use mockall;

// 3. Internal application
use crate::domains::users::entities::UserEntity;
use crate::domains::users::repositories::UserRepository;
use crate::shared::errors::ValidationError;
```

## Development Workflow

### Running Tests

```bash
# Run all tests
cargo test

# Run specific test
cargo test test_user_entity_from_request

# Run with output
cargo test -- --nocapture

# Run integration tests only
cargo test --test integration_tests

# Run unit tests only
cargo test --lib
```

### Code Quality

```bash
# Format code
cargo fmt

# Lint code
cargo clippy -- -D warnings

# Check code without building
cargo check

# Audit dependencies
cargo audit
```

### Development Server

```bash
# Run development server
cargo run

# Run with environment variables
DATABASE_URL=postgresql://user:pass@localhost/db cargo run

# Run tests with Docker
cargo test --features test-docker
```

## Async Patterns

### Async/Await Best Practices

```rust
// Good: Use async for I/O operations
pub async fn create_user(&self, entity: UserEntity) -> Result<UserEntity, Box<dyn std::error::Error>> {
    // Database call
    let existing = self.repository.find_by_email(&entity.email).await?;

    // Business logic (sync)
    if existing.is_some() {
        return Err(BusinessRuleError::new("Email already exists").into());
    }

    // Database call
    let record = entity.to_record();
    let created = self.repository.create(record).await?;

    UserEntity::from_record(created)
}

// Bad: Blocking async operations
pub fn create_user_bad(&self, entity: UserEntity) -> Result<UserEntity, Box<dyn std::error::Error>> {
    // This will block the async runtime
    let existing = self.repository.find_by_email(&entity.email).unwrap(); // Missing await!
    Ok(entity)
}
```

### Error Handling in Async Context

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum ServiceError {
    #[error("Database error: {0}")]
    Database(#[from] sqlx::Error),

    #[error("Validation error: {0}")]
    Validation(#[from] ValidationError),

    #[error("Business rule error: {0}")]
    BusinessRule(String),
}

impl UserService {
    pub async fn create_user(&self, entity: UserEntity) -> Result<UserEntity, ServiceError> {
        // Validate
        entity.validate()?;

        // Check duplicates
        if self.repository.find_by_email(&entity.email).await?.is_some() {
            return Err(ServiceError::BusinessRule("Email already exists".to_string()));
        }

        // Create
        let record = entity.to_record();
        let created = self.repository.create(record).await?;

        UserEntity::from_record(created).map_err(ServiceError::Validation)
    }
}
```

## Testing Strategy Summary

### Unit Tests (Core Layer)

- **Service Tests**: Mock Repository/Client, test business logic
- **Entity Tests**: Test validation and transformation logic
- **Repository Tests**: Mock database, test data operations

### Integration Tests (Edge Layer)

- **HTTP Tests**: axum-test → Service → Repository → Database
- **Database Tests**: testcontainers + Docker PostgreSQL
- **End-to-End Tests**: Full application stack

### Test Organization

```
tests/
├── unit/
│   ├── service_tests.rs
│   ├── entity_tests.rs
│   └── repository_tests.rs
├── integration/
│   ├── http_tests.rs
│   └── database_tests.rs
└── test_helpers.rs  # Test utilities and fixtures
```

### Mock Testing with mockall

```rust
use mockall::mock;

#[mockall::automock]
pub trait ExternalService {
    async fn get_data(&self, id: i32) -> Result<String, ServiceError>;
}

#[tokio::test]
async fn test_service_with_external_mock() {
    let mut mock_service = MockExternalService::new();
    mock_service
        .expect_get_data()
        .with(eq(123))
        .returning(|_| Ok("mock data".to_string()));

    // Test with mock
}
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}

