# API Specification

Using REST API with tRPC for type safety:

## REST API Specification
```yaml
openapi: 3.0.0
info:
  title: IdeaStash API
  version: 1.0.0
  description: RESTful API for IdeaStash idea management platform
servers:
  - url: https://ideastash.vercel.app/api
    description: Production API
  - url: http://localhost:3000/api
    description: Development API

paths:
  /ideas:
    get:
      summary: List user's ideas
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [draft, committed, all]
        - name: limit
          in: query
          schema:
            type: integer
            default: 50
        - name: cursor
          in: query
          schema:
            type: string
      responses:
        '200':
          description: List of ideas
          content:
            application/json:
              schema:
                type: object
                properties:
                  ideas:
                    type: array
                    items:
                      $ref: '#/components/schemas/Idea'
                  nextCursor:
                    type: string
    post:
      summary: Create new idea
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                content:
                  type: string
                  maxLength: 10000
                status:
                  type: string
                  enum: [draft, committed]
                  default: draft
      responses:
        '201':
          description: Idea created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Idea'

  /ideas/{id}:
    get:
      summary: Get specific idea
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Idea details
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Idea'
    patch:
      summary: Update idea
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                content:
                  type: string
                status:
                  type: string
                  enum: [draft, committed]
      responses:
        '200':
          description: Idea updated
    delete:
      summary: Delete idea
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '204':
          description: Idea deleted

  /search:
    get:
      summary: Search committed ideas
      parameters:
        - name: q
          in: query
          required: true
          schema:
            type: string
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
      responses:
        '200':
          description: Search results
          content:
            application/json:
              schema:
                type: object
                properties:
                  results:
                    type: array
                    items:
                      $ref: '#/components/schemas/IdeaSearchResult'
                  total:
                    type: integer

  /sync:
    post:
      summary: Sync offline ideas
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                ideas:
                  type: array
                  items:
                    $ref: '#/components/schemas/OfflineIdea'
      responses:
        '200':
          description: Sync results
          content:
            application/json:
              schema:
                type: object
                properties:
                  synced:
                    type: array
                    items:
                      $ref: '#/components/schemas/Idea'
                  conflicts:
                    type: array
                    items:
                      $ref: '#/components/schemas/SyncConflict'

components:
  schemas:
    Idea:
      type: object
      properties:
        id:
          type: string
        user_id:
          type: string
        content:
          type: string
        status:
          type: string
          enum: [draft, committed]
        created_at:
          type: string
          format: date-time
        committed_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time
    IdeaSearchResult:
      allOf:
        - $ref: '#/components/schemas/Idea'
        - type: object
          properties:
            headline:
              type: string
            rank:
              type: number
    OfflineIdea:
      type: object
      properties:
        local_id:
          type: string
        content:
          type: string
        created_at:
          type: string
          format: date-time
    SyncConflict:
      type: object
      properties:
        local_id:
          type: string
        server_idea:
          $ref: '#/components/schemas/Idea'
        conflict_type:
          type: string
          enum: [content_mismatch, timestamp_conflict]
```
