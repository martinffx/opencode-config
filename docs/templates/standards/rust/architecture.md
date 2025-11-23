# Rust Architecture Standards

## Layered Architecture Patterns

### Pattern 1: Standard CRUD API (Axum)

```
Client → Handler → Service (Domain) → Repository → Database
                     ↓
                  Entity
```

### Pattern 2: External API Integration

```
Client → Handler → Service (Domain) → Client → External API
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
Client → Handler → Service (Domain) → Producer → Event Broker
                     ↓
                  Entity
```

## Rust-Specific Layer Implementation

### Handler Layer (Axum)

```rust
use axum::{
    extract::{Path, State},
    http::StatusCode,
    response::Json,
    routing::{get, post, put, delete},
    Router,
};
use serde_json::json;
use crate::domains::users::{
    service::UserService,
    entities::UserEntity,
    requests::CreateUserRequest,
    responses::UserResponse,
};
use crate::shared::errors::{AppError, ValidationError, BusinessRuleError};

pub fn user_router() -> Router<AppState> {
    Router::new()
        .route("/", post(create_user))
        .route("/:id", get(get_user))
        .route("/:id", put(update_user))
        .route("/:id", delete(delete_user))
}

async fn create_user(
    State(service): State<UserService>,
    Json(request): Json<CreateUserRequest>,
) -> Result<Json<UserResponse>, AppError> {
    let entity = UserEntity::from_request(request)?;
    let result = service.create_user(entity).await?;
    Ok(Json(result.to_response()))
}

async fn get_user(
    State(service): State<UserService>,
    Path(user_id): Path<i32>,
) -> Result<Json<UserResponse>, AppError> {
    let result = service.get_user(user_id).await?;
    Ok(Json(result.to_response()))
}
```

### Service Layer (Domain Logic)

```rust
use std::sync::Arc;
use crate::domains::users::{
    repositories::UserRepository,
    entities::UserEntity,
};
use crate::shared::errors::{BusinessRuleError, ValidationError, NotFoundError};

pub struct UserService {
    repository: Arc<dyn UserRepository>,
}

impl UserService {
    pub fn new(repository: Arc<dyn UserRepository>) -> Self {
        Self { repository }
    }

    pub async fn create_user(&self, entity: UserEntity) -> Result<UserEntity, Box<dyn std::error::Error>> {
        // Validate business rules
        entity.validate()?;

        // Check duplicates
        if let Some(_) = self.repository.find_by_email(&entity.email).await? {
            return Err(BusinessRuleError::new("Email already exists").into());
        }

        // Persist and return
        let record = entity.to_record();
        let created = self.repository.create(record).await?;
        Ok(UserEntity::from_record(created)?)
    }

    pub async fn get_user(&self, user_id: i32) -> Result<UserEntity, Box<dyn std::error::Error>> {
        match self.repository.find_by_id(user_id).await? {
            Some(record) => Ok(UserEntity::from_record(record)?),
            None => Err(NotFoundError::new("User not found").into()),
        }
    }
}
```

### Entity Layer (Domain Model)

```rust
use serde::{Deserialize, Serialize};
use chrono::{DateTime, Utc};
use regex::Regex;
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
        Ok(Self {
            id: None,
            email: dto.email.to_lowercase().trim().to_string(),
            name: dto.name.trim().to_string(),
            created_at: None,
        })
    }

    pub fn from_record(record: HashMap<String, serde_json::Value>) -> Result<Self, Box<dyn std::error::Error>> {
        Ok(Self {
            id: record.get("id").and_then(|v| v.as_i64()).map(|i| i as i32),
            email: record.get("email")
                .and_then(|v| v.as_str())
                .unwrap_or("")
                .to_string(),
            name: record.get("name")
                .and_then(|v| v.as_str())
                .unwrap_or("")
                .to_string(),
            created_at: record.get("created_at")
                .and_then(|v| v.as_str())
                .and_then(|s| DateTime::parse_from_rfc3339(s).ok())
                .map(|dt| dt.with_timezone(&Utc)),
        })
    }

    pub fn to_record(&self) -> HashMap<String, serde_json::Value> {
        let mut record = HashMap::new();
        record.insert("email".to_string(), serde_json::Value::String(self.email.clone()));
        record.insert("name".to_string(), serde_json::Value::String(self.name.clone()));

        if let Some(created_at) = self.created_at {
            record.insert("created_at".to_string(),
                serde_json::Value::String(created_at.to_rfc3339()));
        }

        record
    }

    pub fn to_response(&self) -> UserResponse {
        UserResponse {
            id: self.id,
            email: self.email.clone(),
            name: self.name.clone(),
            created_at: self.created_at,
        }
    }

    pub fn validate(&self) -> Result<(), ValidationError> {
        let mut errors = Vec::new();

        // Email validation
        if self.email.is_empty() {
            errors.push("Email is required".to_string());
        } else {
            let email_regex = Regex::new(r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$")
                .map_err(|_| ValidationError::new("Invalid email regex".to_string()))?;

            if !email_regex.is_match(&self.email) {
                errors.push("Invalid email format".to_string());
            }
        }

        // Name validation
        if self.name.trim().len() < 2 {
            errors.push("Name must be at least 2 characters".to_string());
        }

        if !errors.is_empty() {
            return Err(ValidationError::new(errors.join(", ")));
        }

        Ok(())
    }
}
```

### Repository Layer (SQLx)

```rust
use sqlx::{PgPool, Row};
use std::collections::HashMap;
use async_trait::async_trait;

#[async_trait]
pub trait UserRepository: Send + Sync {
    async fn create(&self, data: HashMap<String, serde_json::Value>) -> Result<HashMap<String, serde_json::Value>, Box<dyn std::error::Error>>;
    async fn find_by_id(&self, user_id: i32) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>>;
    async fn find_by_email(&self, email: &str) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>>;
    async fn update(&self, user_id: i32, data: HashMap<String, serde_json::Value>) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>>;
    async fn delete(&self, user_id: i32) -> Result<bool, Box<dyn std::error::Error>>;
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
    async fn create(&self, data: HashMap<String, serde_json::Value>) -> Result<HashMap<String, serde_json::Value>, Box<dyn std::error::Error>> {
        let record = sqlx::query!(
            r#"
            INSERT INTO users (email, name, created_at)
            VALUES ($1, $2, NOW())
            RETURNING id, email, name, created_at
            "#,
            data.get("email").and_then(|v| v.as_str()).unwrap_or(""),
            data.get("name").and_then(|v| v.as_str()).unwrap_or("")
        )
        .fetch_one(&self.pool)
        .await?;

        let mut result = HashMap::new();
        result.insert("id".to_string(), serde_json::Value::Number(serde_json::Number::from(record.id)));
        result.insert("email".to_string(), serde_json::Value::String(record.email));
        result.insert("name".to_string(), serde_json::Value::String(record.name));

        if let Some(created_at) = record.created_at {
            result.insert("created_at".to_string(),
                serde_json::Value::String(created_at.to_rfc3339()));
        }

        Ok(result)
    }

    async fn find_by_id(&self, user_id: i32) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>> {
        let record = sqlx::query!(
            r#"
            SELECT id, email, name, created_at
            FROM users
            WHERE id = $1
            "#,
            user_id
        )
        .fetch_optional(&self.pool)
        .await?;

        match record {
            Some(row) => {
                let mut result = HashMap::new();
                result.insert("id".to_string(), serde_json::Value::Number(serde_json::Number::from(row.id)));
                result.insert("email".to_string(), serde_json::Value::String(row.email));
                result.insert("name".to_string(), serde_json::Value::String(row.name));

                if let Some(created_at) = row.created_at {
                    result.insert("created_at".to_string(),
                        serde_json::Value::String(created_at.to_rfc3339()));
                }

                Ok(Some(result))
            }
            None => Ok(None),
        }
    }

    async fn find_by_email(&self, email: &str) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>> {
        let record = sqlx::query!(
            r#"
            SELECT id, email, name, created_at
            FROM users
            WHERE email = $1
            "#,
            email.to_lowercase()
        )
        .fetch_optional(&self.pool)
        .await?;

        match record {
            Some(row) => {
                let mut result = HashMap::new();
                result.insert("id".to_string(), serde_json::Value::Number(serde_json::Number::from(row.id)));
                result.insert("email".to_string(), serde_json::Value::String(row.email));
                result.insert("name".to_string(), serde_json::Value::String(row.name));

                if let Some(created_at) = row.created_at {
                    result.insert("created_at".to_string(),
                        serde_json::Value::String(created_at.to_rfc3339()));
                }

                Ok(Some(result))
            }
            None => Ok(None),
        }
    }

    async fn update(&self, user_id: i32, data: HashMap<String, serde_json::Value>) -> Result<Option<HashMap<String, serde_json::Value>>, Box<dyn std::error::Error>> {
        let record = sqlx::query!(
            r#"
            UPDATE users
            SET email = $1, name = $2
            WHERE id = $3
            RETURNING id, email, name, created_at
            "#,
            data.get("email").and_then(|v| v.as_str()).unwrap_or(""),
            data.get("name").and_then(|v| v.as_str()).unwrap_or(""),
            user_id
        )
        .fetch_optional(&self.pool)
        .await?;

        match record {
            Some(row) => {
                let mut result = HashMap::new();
                result.insert("id".to_string(), serde_json::Value::Number(serde_json::Number::from(row.id)));
                result.insert("email".to_string(), serde_json::Value::String(row.email));
                result.insert("name".to_string(), serde_json::Value::String(row.name));

                if let Some(created_at) = row.created_at {
                    result.insert("created_at".to_string(),
                        serde_json::Value::String(created_at.to_rfc3339()));
                }

                Ok(Some(result))
            }
            None => Ok(None),
        }
    }

    async fn delete(&self, user_id: i32) -> Result<bool, Box<dyn std::error::Error>> {
        let result = sqlx::query!(
            r#"
            DELETE FROM users
            WHERE id = $1
            "#,
            user_id
        )
        .execute(&self.pool)
        .await?;

        Ok(result.rows_affected() > 0)
    }
}
```

## Dependency Injection (Axum)

### Application State

```rust
use axum::Router;
use std::sync::Arc;
use sqlx::PgPool;

#[derive(Clone)]
pub struct AppState {
    pub user_service: Arc<UserService>,
}

impl AppState {
    pub async fn new(pool: PgPool) -> Result<Self, Box<dyn std::error::Error>> {
        let user_repository = Arc::new(PostgresUserRepository::new(pool.clone()));
        let user_service = Arc::new(UserService::new(user_repository));

        Ok(Self { user_service })
    }
}

pub fn create_app(state: AppState) -> Router {
    Router::new()
        .nest("/api/v1/users", user_routes::user_router())
        .with_state(state)
}
```

## Database Integration (SQLx + PostgreSQL)

### Database Model

```rust
use sqlx::FromRow;
use chrono::{DateTime, Utc};

#[derive(Debug, FromRow)]
pub struct UserModel {
    pub id: i32,
    pub email: String,
    pub name: String,
    pub created_at: DateTime<Utc>,
}
```

### Database Connection Setup

```rust
use sqlx::{PgPool, postgres::PgPoolOptions};

pub async fn create_pool(database_url: &str) -> Result<PgPool, Box<dyn std::error::Error>> {
    let pool = PgPoolOptions::new()
        .max_connections(10)
        .connect(database_url)
        .await?;

    // Run migrations
    sqlx::migrate!("./migrations").run(&pool).await?;

    Ok(pool)
}
```

## Docker Test Database Setup

### Test Container Setup

```rust
use testcontainers::{clients::Cli, images::postgres::Postgres, Container};
use sqlx::{PgPool, postgres::PgPoolOptions};

pub struct TestDatabase {
    pub container: Container<'static, Postgres>,
    pub pool: PgPool,
}

impl TestDatabase {
    pub async fn new() -> Result<Self, Box<dyn std::error::Error>> {
        let docker = Cli::default();
        let postgres_image = Postgres::default();

        let container = docker.run(postgres_image);
        let port = container.get_host_port_ipv4(5432);

        let database_url = format!(
            "postgresql://postgres:postgres@localhost:{}/postgres",
            port
        );

        let pool = PgPoolOptions::new()
            .max_connections(5)
            .connect(&database_url)
            .await?;

        // Run migrations
        sqlx::migrate!("./migrations").run(&pool).await?;

        Ok(Self { container, pool })
    }
}

#[cfg(test)]
pub mod test_helpers {
    use super::*;
    use std::sync::OnceLock;

    static TEST_DB: OnceLock<TestDatabase> = OnceLock::new();

    pub async fn get_test_db() -> &'static TestDatabase {
        TEST_DB.get_or_init(|| async {
            TestDatabase::new().await.expect("Failed to create test database")
        }).await
    }
}
```

## Error Handling (Rust)

### Custom Error Types

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum AppError {
    #[error("Validation error: {0}")]
    ValidationError(#[from] ValidationError),

    #[error("Business rule error: {0}")]
    BusinessRuleError(#[from] BusinessRuleError),

    #[error("Not found error: {0}")]
    NotFoundError(#[from] NotFoundError),

    #[error("Database error: {0}")]
    DatabaseError(#[from] sqlx::Error),

    #[error("Internal server error: {0}")]
    InternalError(String),
}

#[derive(Error, Debug)]
#[error("{message}")]
pub struct ValidationError {
    pub message: String,
}

impl ValidationError {
    pub fn new(message: String) -> Self {
        Self { message }
    }
}

#[derive(Error, Debug)]
#[error("{message}")]
pub struct BusinessRuleError {
    pub message: String,
}

impl BusinessRuleError {
    pub fn new(message: &str) -> Self {
        Self { message: message.to_string() }
    }
}

#[derive(Error, Debug)]
#[error("{message}")]
pub struct NotFoundError {
    pub message: String,
}

impl NotFoundError {
    pub fn new(message: &str) -> Self {
        Self { message: message.to_string() }
    }
}
```

### Error Response Implementation

```rust
use axum::{
    http::StatusCode,
    response::{IntoResponse, Response},
    Json,
};
use serde_json::json;

impl IntoResponse for AppError {
    fn into_response(self) -> Response {
        let (status, error_message) = match self {
            AppError::ValidationError(err) => (StatusCode::UNPROCESSABLE_ENTITY, err.message),
            AppError::BusinessRuleError(err) => (StatusCode::BAD_REQUEST, err.message),
            AppError::NotFoundError(err) => (StatusCode::NOT_FOUND, err.message),
            AppError::DatabaseError(err) => (StatusCode::INTERNAL_SERVER_ERROR, err.to_string()),
            AppError::InternalError(err) => (StatusCode::INTERNAL_SERVER_ERROR, err),
        };

        let body = Json(json!({
            "error": "application_error",
            "message": error_message
        }));

        (status, body).into_response()
    }
}
```

## Integration Testing with Axum

### Integration Test Example

```rust
#[cfg(test)]
mod integration_tests {
    use super::*;
    use axum_test::TestServer;
    use crate::test_helpers::get_test_db;

    #[tokio::test]
    async fn test_create_user_integration() {
        // Arrange
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
        // Arrange
        let test_db = get_test_db().await;
        let state = AppState::new(test_db.pool.clone()).await.unwrap();
        let app = create_app(state);
        let server = TestServer::new(app).unwrap();

        // Act
        let response = server.get("/api/v1/users/999").await;

        // Assert
        assert_eq!(response.status_code(), 404);
    }
}
```

---

**Version**: {{version}}
**Last Updated**: {{updated_date}}

