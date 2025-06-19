# Twider - Modern Twitter Clone
##  Architecture Overview

Twider is built as a distributed microservices architecture with three main components:

### ** Backend (`twider-backend`)**
- **Framework**: ASP.NET Core 8.0 with Clean Architecture
- **Authentication**: Keycloak integration with OAuth 2.0 + PKCE flow
- **Database**: PostgreSQL with Entity Framework Core
- **Message Broker**: NATS JetStream for real-time event processing
- **File Storage**: MinIO for media uploads
- **API Documentation**: OpenAPI/Swagger with Scalar UI
- **Testing**: Comprehensive integration tests

### ** Frontend (`twider-frontend`)**
- **Framework**: Next.js 15 with TypeScript and App Router
- **Styling**: Tailwind CSS with shadcn/ui components
- **State Management**: Zustand with Immer for immutable updates
- **Data Fetching**: TanStack Query (React Query) for server state
- **Authentication**: NextAuth.js with Keycloak provider
- **Animations**: Framer Motion for smooth interactions

### ** ML Service (`twider-ml`)**
- **Framework**: FastAPI with Python
- **Vector Database**: Qdrant for hybrid vector search
- **Search Engine**: Dense + Sparse vector search with Reciprocal Rank Fusion (RRF)
- **Event Processing**: NATS JetStream consumer for real-time indexing
- **Configuration**: Pydantic for environment-based configuration

##  Key Features

### **Core Social Features**
-  **Post Creation & Management**: Create, edit, delete posts with rich text content
-  **Nested Comments System**: Multi-level comment threading with infinite scroll
-  **Rich Reactions**: 7 reaction types (Like, Love, Haha, Wow, Sad, Angry, Care)
-  **Media Support**: Image uploads with MinIO storage and CDN
-  **User Profiles**: Complete user management with profile pictures and bios

### **Advanced Features**
-  **AI-Powered Search**: Hybrid vector search combining semantic and keyword matching
-  **Real-time Updates**: Event-driven architecture with NATS JetStream
-  **Enterprise Auth**: Keycloak integration with JWT tokens and refresh handling
-  **Performance Optimized**: Cursor-based pagination and optimistic updates
-  **Modern UI/UX**: Responsive design with smooth animations and dark mode

### **Technical Excellence**
-  **Comprehensive Testing**: Integration tests for all major features
-  **API-First Design**: Auto-generated TypeScript clients from OpenAPI
-  **Containerized**: Docker Compose for easy development setup
-  **Event Sourcing**: Real-time ML indexing of content changes


##  Getting Started

### Prerequisites
- **Docker & Docker Compose** (for infrastructure)
- **Node.js 20+** and **pnpm** (for frontend)
- **.NET 8 SDK** (for backend)
- **Python 3.12+** and **uv** (for ML service)

### 1. Infrastructure Setup

Start all required services:

```bash
cd twider-backend/Backend
docker compose up -d
```

This starts:
- PostgreSQL (port 5432)
- Keycloak (port 6969)
- MinIO (ports 9000, 9001)
- Qdrant (port 6333)
- NATS (ports 4222, 6222, 8222)

### 2. Backend Setup

```bash
cd twider-backend

# Restore dependencies
dotnet restore

# Run database migrations
dotnet ef database update --project Backend

# Start the backend API
dotnet run --project Backend
```

Backend will be available at: `http://localhost:5224`

### 3. Frontend Setup

```bash
cd twider-frontend

# Install dependencies
pnpm install

# Start development server
pnpm dev
```

Frontend will be available at: `http://localhost:3000`

### 4. ML Service Setup

```bash
cd twider-ml

# Install dependencies
uv sync

# Initialize Qdrant collections
uv run python scripts/init_qdrant_collection.py

# Start ML service
make run
# or
uv run main.py
```

ML service will be available at: `http://localhost:8000`

##  Configuration

### Environment Variables

#### Backend (`twider-backend/Backend/appsettings.Development.json`)
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=postgres;Username=user;Password=user"
  },
  "OAuth": {
    "ClientId": "your-client-id",
    "Authority": "http://localhost:6969/realms/master",
    "Audience": "account"
  },
  "Keycloak": {
    "Url": "http://localhost:6969",
    "Realm": "master",
    "Username": "admin",
    "Password": "admin"
  },
  "MinIO": {
    "Endpoint": "localhost:9000",
    "AccessKey": "minioadmin",
    "SecretKey": "minioadmin"
  },
  "Qdrant": {
    "Url": "http://localhost:6333"
  },
  "MlSearch": {
    "BaseUrl": "http://localhost:8000"
  }
}
```

#### Frontend (`twider-frontend/.env.local`)
```bash
AUTH_KEYCLOAK_ID=your-client-id
AUTH_KEYCLOAK_SECRET=your-client-secret
AUTH_KEYCLOAK_ISSUER=http://localhost:6969/realms/master
AUTH_SECRET=your-nextauth-secret
NEXT_PUBLIC_API_URL=http://localhost:5224
```