# User Flow Sequence Diagrams

## 1. User Registration & Authentication Flow
```mermaid
sequenceDiagram
    participant U as User
    participant C as Client (React)
    participant A as Auth Service
    participant DB as Database

    U->>C: Access IdeaStash
    C->>U: Show Login/Register Screen
    U->>C: Enter email/password for registration
    C->>A: POST /register
    A->>DB: Create user record
    DB->>A: User created with ID
    A->>C: Registration success + JWT token
    C->>U: Show email verification prompt
    U->>A: Verify email via link
    A->>C: Email verified confirmation
    C->>U: Redirect to Quick Capture Screen
```

## 2. Quick Idea Capture Flow
```mermaid
sequenceDiagram
    participant U as User
    participant C as Client
    participant LS as Local Storage
    participant API as Backend API
    participant DB as Database

    U->>C: Start typing idea
    C->>LS: Auto-save draft locally
    U->>C: Click "Save Draft"
    C->>API: POST /ideas/draft
    API->>DB: Insert draft idea
    DB->>API: Draft saved with ID & timestamp
    API->>C: Draft creation success
    C->>U: Show success feedback
    C->>U: Display idea in Draft Management View
```

## 3. Draft-to-Commit Workflow
```mermaid
sequenceDiagram
    participant U as User
    participant C as Client
    participant API as Backend API
    participant DB as Database

    U->>C: View draft in Draft Management
    U->>C: Click "Commit This Idea"
    C->>U: Show commit confirmation
    U->>C: Confirm commit action
    C->>API: PATCH /ideas/{id}/commit
    API->>DB: Update idea status + commit_timestamp
    DB->>API: Commit successful
    API->>C: Idea committed response
    C->>U: Show commit success feedback
    C->>C: Move idea from drafts to Idea Pool
    C->>U: Update Idea Pool Dashboard
```

## 4. Search & Discovery Flow
```mermaid
sequenceDiagram
    participant U as User
    participant C as Client
    participant API as Backend API
    participant DB as PostgreSQL

    U->>C: Enter search term
    C->>API: GET /search?q={term}&limit=10
    API->>DB: SELECT with full-text search
    DB->>API: Return matching ideas with highlights
    API->>C: Search results with metadata
    C->>U: Display results with highlighted terms
    U->>C: Click on search result
    C->>U: Show full idea content
```

## 5. Intentional Deletion Flow
```mermaid
sequenceDiagram
    participant U as User
    participant C as Client
    participant API as Backend API
    participant DB as Database

    U->>C: Click delete on committed idea
    C->>U: Show deletion warning dialog
    C->>U: Display idea preview in dialog
    U->>C: Type "DELETE" in confirmation field
    U->>C: Click final delete confirmation
    C->>API: DELETE /ideas/{id}
    API->>DB: Remove idea permanently
    DB->>API: Deletion confirmed
    API->>C: Idea deleted response
    C->>C: Remove from Idea Pool view
    C->>U: Show deletion success message
```

## 6. Offline Sync Flow
```mermaid
sequenceDiagram
    participant U as User
    participant C as Client
    participant SW as Service Worker
    participant LS as Local Storage
    participant API as Backend API
    participant DB as Database

    Note over C,SW: User goes offline
    U->>C: Capture new idea
    C->>LS: Store draft locally
    C->>SW: Queue for sync when online
    C->>U: Show "Saved offline" indicator

    Note over C,SW: User comes back online
    SW->>C: Detect network connectivity
    C->>API: POST /ideas/sync (queued drafts)
    API->>DB: Bulk insert offline ideas
    DB->>API: Sync successful with IDs
    API->>C: Return synced ideas with server IDs
    C->>LS: Update local IDs with server IDs
    C->>U: Show "Sync complete" notification
```
