# Database Schema

```sql
-- Enable extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";

-- Users table (managed by Clerk)
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    clerk_id VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    avatar_url TEXT,
    settings JSONB DEFAULT '{"theme": "system", "notifications_enabled": true, "auto_commit_enabled": false}'::jsonb,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Ideas table
CREATE TABLE ideas (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    content TEXT NOT NULL CHECK (length(content) > 0 AND length(content) <= 10000),
    status VARCHAR(20) NOT NULL DEFAULT 'draft' CHECK (status IN ('draft', 'committed')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    committed_at TIMESTAMP WITH TIME ZONE,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    search_vector TSVECTOR
);

-- Indexes for performance
CREATE INDEX idx_ideas_user_id ON ideas(user_id);
CREATE INDEX idx_ideas_status ON ideas(status);
CREATE INDEX idx_ideas_created_at ON ideas(created_at DESC);
CREATE INDEX idx_ideas_committed_at ON ideas(committed_at DESC) WHERE status = 'committed';

-- Full-text search index
CREATE INDEX idx_ideas_search ON ideas USING GIN(search_vector);

-- Trigger to update search vector
CREATE OR REPLACE FUNCTION update_idea_search_vector()
RETURNS TRIGGER AS $$
BEGIN
    NEW.search_vector := to_tsvector('english', NEW.content);
    NEW.updated_at := NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_ideas_search_vector
    BEFORE INSERT OR UPDATE ON ideas
    FOR EACH ROW EXECUTE FUNCTION update_idea_search_vector();

-- Row Level Security (RLS) for multi-tenant data isolation
ALTER TABLE ideas ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only see their own ideas
CREATE POLICY ideas_user_isolation ON ideas
    FOR ALL
    USING (user_id = auth.uid())
    WITH CHECK (user_id = auth.uid());

-- Optimized search function
CREATE OR REPLACE FUNCTION search_ideas(
    user_uuid UUID,
    search_query TEXT,
    result_limit INTEGER DEFAULT 20
)
RETURNS TABLE (
    id UUID,
    content TEXT,
    status VARCHAR(20),
    created_at TIMESTAMP WITH TIME ZONE,
    committed_at TIMESTAMP WITH TIME ZONE,
    headline TEXT,
    rank REAL
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        i.id,
        i.content,
        i.status,
        i.created_at,
        i.committed_at,
        ts_headline('english', i.content, plainto_tsquery('english', search_query)) as headline,
        ts_rank(i.search_vector, plainto_tsquery('english', search_query)) as rank
    FROM ideas i
    WHERE i.user_id = user_uuid
        AND i.status = 'committed'
        AND i.search_vector @@ plainto_tsquery('english', search_query)
    ORDER BY rank DESC, i.created_at DESC
    LIMIT result_limit;
END;
$$ LANGUAGE plpgsql;
```
