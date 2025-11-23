# DynamoDB Standards

## Overview

When using DynamoDB, use `dynamodb-toolbox` for type-safe interactions. The library provides Entity and Table abstractions that handle the complexities of DynamoDB's single-table design pattern.

## Table Structure

### Standard Table Configuration

```typescript
const AppTable = new Table({
  name: process.env.TABLE_NAME || "AppTable",
  partitionKey: { name: "PK", type: "string" },
  sortKey: { name: "SK", type: "string" },
  indexes: {
    GSI1: {
      type: "global",
      partitionKey: { name: "GSI1PK", type: "string" },
      sortKey: { name: "GSI1SK", type: "string" },
    },
    GSI2: {
      type: "global",
      partitionKey: { name: "GSI2PK", type: "string" },
      sortKey: { name: "GSI2SK", type: "string" },
    },
    GSI3: {
      type: "global",
      partitionKey: { name: "GSI3PK", type: "string" },
      sortKey: { name: "GSI3SK", type: "string" },
    },
  },
});
```

**Index Purpose Guidelines:**
- **Main Table**: Primary entity access (PK/SK)
- **GSI1**: General secondary access patterns
- **GSI2**: Entity-specific queries and relationships
- **GSI3**: Hierarchical queries with temporal sorting

## Key Pattern Design

### Standard Key Patterns

```
Entity Pattern:     ENTITY#{id}
Hierarchy Pattern:  PARENT#{parent_id}#CHILD#{child_id}
Prefix Pattern:     TYPE#{category}#{identifier}
Temporal Pattern:   ENTITY#{id}#{timestamp}
```

### Access Pattern Guidelines

| Access Type | Main Table | GSI1 | GSI2 | GSI3 |
|-------------|------------|------|------|------|
| Direct Entity | PK/SK | - | - | - |
| Secondary Lookup | - | GSI1PK/GSI1SK | - | - |
| Entity Relations | - | - | GSI2PK/GSI2SK | - |
| Hierarchical/Temporal | - | - | - | GSI3PK/GSI3SK |

### Common Query Patterns

1. **Direct Entity Access**: Using PK/SK for single-item retrieval
2. **Secondary Lookup**: GSI1 for alternative access patterns
3. **Entity Relations**: GSI2 for relationship queries
4. **Hierarchical Queries**: GSI3 for parent-child relationships with sorting

## Schema Constructor Module

The schema module (`@src/repos/schema.ts`) provides the core building blocks for DynamoDB operations:

### Core Exports

```typescript
// Schema initialization
export { initializeSchema }

// Pagination utilities
export { encodePageToken, decodePageToken }

// Type definitions
export type {
  GithubSchema,
  GithubTable,
  UserRecord,
  OrganizationRecord,
  RepoRecord,
  UserInput,
  UserFormatted,
  OrganizationInput,
  OrganizationFormatted,
  RepoInput,
  RepoFormatted,
}
```

### Schema Initialization

```typescript
const initializeSchema = (client: DynamoDBClient): AppSchema => {
  AppTable.documentClient = DynamoDBDocumentClient.from(client, {
    marshallOptions: {
      removeUndefinedValues: true,
      convertEmptyValues: false,
    },
  });

  return {
    table: AppTable,
    // Add your entities here
    entity: EntityRecord,
  };
};
```

**Usage:**
```typescript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { initializeSchema } from "@src/repos/schema";

const client = new DynamoDBClient({ region: "us-east-1" });
const schema = initializeSchema(client);

// Now you can use schema.user, schema.organization, schema.repository
```

### Entity Definitions

Each entity follows this pattern:

#### UserRecord
- **Key Fields**: `username` (validated with regex `^[a-zA-Z0-9_-]+$`)
- **Additional Fields**: `email` (validated), `bio` (optional), `payment_plan_id` (optional)
- **Key Strategy**: All keys point to same pattern `ACCOUNT#{username}`

#### OrganizationRecord
- **Key Fields**: `org_name` (validated with regex `^[a-zA-Z0-9_-]+$`)
- **Additional Fields**: `description` (optional), `payment_plan_id` (optional)
- **Key Strategy**: All keys point to same pattern `ACCOUNT#{org_name}`

#### RepoRecord
- **Key Fields**: `owner` and `repo_name` (both validated)
- **Additional Fields**: `description` (optional), `is_private` (boolean, default false), `language` (optional)
- **Key Strategy**: Most keys use `REPO#{owner}#{repo_name}`, GSI3 uses `ACCOUNT#{owner}` for owner-based queries

### Type Safety

The schema provides full TypeScript support:

```typescript
// Input types for creating entities
type UserInput = InputItem<typeof UserRecord>;
type OrganizationInput = InputItem<typeof OrganizationRecord>;
type RepoInput = InputItem<typeof RepoRecord>;

// Formatted types for reading entities
type UserFormatted = FormattedItem<typeof UserRecord>;
type OrganizationFormatted = FormattedItem<typeof OrganizationRecord>;
type RepoFormatted = FormattedItem<typeof RepoRecord>;
```

### Pagination Support

```typescript
function encodePageToken(lastEvaluated?: Record<string, any>): string | undefined
function decodePageToken(token?: string): Record<string, any> | undefined
```

**Usage:**
```typescript
// Encode pagination token
const nextToken = encodePageToken(response.LastEvaluatedKey);

// Decode pagination token
const startKey = decodePageToken(pageToken);
```

## Best Practices

1. **Always initialize schema first** before using entities
2. **Use type-safe inputs** with `UserInput`, `OrganizationInput`, `RepoInput`
3. **Handle pagination** with provided token utilities
4. **Leverage GSI3** for efficient owner-based repository queries
5. **Follow key patterns** consistently across the application
6. **Validate inputs** - the schema includes built-in validation rules
