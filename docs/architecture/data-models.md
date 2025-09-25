# Data Models

## User Model
**Purpose:** Represents authenticated users with their account information and preferences

**Key Attributes:**
- id: string (UUID) - Unique user identifier
- email: string - Primary authentication email
- name: string - Display name for the user
- avatar_url: string (optional) - Profile picture URL
- created_at: timestamp - Account creation time
- updated_at: timestamp - Last profile update
- settings: JSON - User preferences and configuration

### TypeScript Interface
```typescript
export interface User {
  id: string;
  email: string;
  name: string;
  avatar_url?: string;
  created_at: Date;
  updated_at: Date;
  settings: {
    theme: 'light' | 'dark' | 'system';
    notifications_enabled: boolean;
    auto_commit_enabled: boolean;
  };
}
```

### Relationships
- One-to-many with Ideas (user can have multiple ideas)

## Idea Model
**Purpose:** Core entity representing both draft and committed ideas with full audit trail

**Key Attributes:**
- id: string (UUID) - Unique idea identifier
- user_id: string (UUID, FK) - Owner reference
- content: text - The actual idea content
- status: enum - 'draft' or 'committed'
- created_at: timestamp - Initial capture time
- committed_at: timestamp (optional) - When idea was committed
- updated_at: timestamp - Last modification time
- search_vector: tsvector - PostgreSQL full-text search vector

### TypeScript Interface
```typescript
export interface Idea {
  id: string;
  user_id: string;
  content: string;
  status: 'draft' | 'committed';
  created_at: Date;
  committed_at?: Date;
  updated_at: Date;
}

// API Response types
export interface IdeaWithUser extends Idea {
  user: Pick<User, 'name' | 'avatar_url'>;
}

export interface IdeaSearchResult extends Idea {
  headline?: string; // Search result highlight
  rank?: number; // Search relevance score
}
```

### Relationships
- Many-to-one with User (each idea belongs to one user)
