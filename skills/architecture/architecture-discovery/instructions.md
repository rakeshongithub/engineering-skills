# Architecture Discovery - Step-by-Step Instructions

## Overview
Systematic approach to understanding and documenting an unfamiliar system's architecture.

## Phase 1: Preparation (30-60 minutes)

### Step 1: Define Objectives
1. Why are you doing this discovery?
   - Onboarding?
   - Planning migration?
   - Architecture review?
   - Integration planning?

2. What questions need answers?
   - How does the system work?
   - What are the components?
   - How do they integrate?
   - What are the dependencies?

3. What level of detail is needed?
   - High-level overview
   - Medium detail
   - Deep dive

### Step 2: Gather Tools
- Code editor/IDE
- Diagram tool (draw.io, Lucidchart, PlantUML)
- Note-taking tool
- Code analysis tools (depends on language)
- Access to running system (if available)

## Phase 2: Information Gathering (2-4 hours)

### Step 3: Collect Existing Documentation
1. Search for:
   - README files
   - Wiki pages
   - Architecture docs
   - API documentation
   - Deployment guides
   - ADRs (Architecture Decision Records)

2. Interview team members:
   - "Can you give me a 10-minute overview?"
   - "What are the main components?"
   - "What are the biggest pain points?"
   - "What should I know that's not documented?"

### Step 4: Examine Repository Structure
1. Clone the repository
2. Explore folder structure:
   ```
   project/
   ├── src/
   ├── tests/
   ├── config/
   ├── docs/
   └── scripts/
   ```
3. Read top-level README
4. Check build files (package.json, pom.xml, etc.)
5. Review dependency files

## Phase 3: Code Analysis (4-8 hours)

### Step 5: Identify Entry Points
1. Find main application entry:
   - `main()` function
   - Server startup file
   - Application bootstrap

2. Trace from entry point:
   - What gets initialized?
   - What services start?
   - What connections are made?

### Step 6: Map Components
1. List major modules/packages:
   ```
   Component Name | Responsibility | Technology | Dependencies
   ```

2. For each component, document:
   - Purpose
   - Key classes/modules
   - Interfaces exposed
   - Dependencies

### Step 7: Identify Technology Stack
Create inventory:
```markdown
## Languages
- Java 11
- JavaScript (Node.js 14)

## Frameworks
- Spring Boot 2.5
- React 17

## Databases
- PostgreSQL 13
- Redis 6

## Infrastructure
- Docker
- Kubernetes
- AWS (EC2, S3, RDS)
```

## Phase 4: Integration Analysis (3-6 hours)

### Step 8: Map Internal Communication
1. How do components communicate?
   - REST APIs
   - Message queues
   - Shared database
   - gRPC
   - Events

2. Create communication diagram:
   ```
   Component A --REST--> Component B
   Component B --Event--> Message Queue ---> Component C
   ```

### Step 9: Identify External Dependencies
1. List external systems:
   - Third-party APIs
   - External databases
   - Cloud services
   - SaaS integrations

2. Document integration method:
   - REST API
   - SOAP
   - SDK
   - Direct database access

## Phase 5: Data Architecture (2-4 hours)

### Step 10: Map Data Stores
1. List all data stores:
   - Databases
   - Caches
   - File storage
   - Message queues

2. For each, document:
   - Type (SQL, NoSQL, cache, etc.)
   - What data it stores
   - Who accesses it
   - Backup strategy

### Step 11: Trace Data Flows
1. Pick key user scenarios
2. Trace data through system:
   ```
   User Input → API → Service → Database
                          ↓
                    Message Queue
                          ↓
                    Background Job
                          ↓
                    External API
   ```

## Phase 6: Deployment Architecture (2-4 hours)

### Step 12: Understand Deployment
1. Review deployment configs:
   - Dockerfile
   - Kubernetes manifests
   - CI/CD pipelines
   - Infrastructure as Code

2. Document:
   - Environments (dev, staging, prod)
   - Deployment process
   - Scaling approach
   - Monitoring and logging

### Step 13: Map Infrastructure
1. Create infrastructure diagram:
   - Load balancers
   - Application servers
   - Databases
   - Caches
   - Message queues
   - CDN
   - Storage

## Phase 7: Documentation (4-8 hours)

### Step 14: Create Architecture Diagrams

**System Context Diagram:**
```
[Users] --> [System] --> [External Systems]
```

**Container Diagram:**
```
[Web App] --> [API] --> [Database]
              [API] --> [Message Queue] --> [Worker]
```

**Component Diagram:**
```
API Service:
  - Auth Controller
  - User Controller
  - Order Controller
  - Payment Service
  - Notification Service
```

### Step 15: Write Architecture Document

Structure:
```markdown
# System Architecture

## 1. Overview
- Purpose
- Key capabilities
- Users

## 2. System Context
- Diagram
- External dependencies

## 3. Components
- List and describe each component

## 4. Technology Stack
- Languages
- Frameworks
- Infrastructure

## 5. Data Architecture
- Data stores
- Data flows

## 6. Integration Architecture
- Internal communication
- External integrations

## 7. Deployment Architecture
- Infrastructure
- Deployment process
- Environments

## 8. Security
- Authentication
- Authorization
- Data protection

## 9. Scalability
- Current approach
- Bottlenecks

## 10. Technical Debt
- Known issues
- Improvement opportunities

## 11. Unknowns
- Areas needing further investigation
```

### Step 16: Validate Findings
1. Review with team members
2. Walk through diagrams
3. Correct misunderstandings
4. Fill in gaps
5. Get sign-off

## Tips for Efficiency

### Quick Wins
- Start with README and existing docs
- Talk to team early
- Use code search to find patterns
- Run the application and explore
- Check recent commits for active areas

### Tools to Use
- **Code navigation**: IDE features, grep, ripgrep
- **Dependency analysis**: `npm list`, `mvn dependency:tree`, etc.
- **Runtime analysis**: Debugger, profiler, logs
- **Diagram generation**: PlantUML, Mermaid, Structurizr

### Time-Saving Strategies
- Focus on your objectives (don't document everything)
- Start high-level, add detail as needed
- Use existing diagrams as starting point
- Automate diagram generation where possible
- Pair with team member for faster understanding

## Common Pitfalls

- **Analysis paralysis**: Don't try to understand everything
- **Outdated docs**: Always verify against code
- **Skipping team interviews**: Tribal knowledge is valuable
- **Too much detail**: Stay at architecture level
- **No validation**: Always review with team