# Project Protostar Documentation Guidelines

## 📂 Structure

### 1. Central Documentation (`/docs`)

Located in the root of the workspace. This is the **Single Source of Truth** for high-level decisions.

- **`docs/requirements/`**: Business requirements, user stories, and product specs.
- **`docs/design/`**: System architecture, database schema, API standards, and UI/UX design systems.
- **`docs/meetings/`**: Meeting notes and decision logs.

### 2. Repository Documentation (`/project-*/docs`)

Located within each microservice/project.

- Specific implementation details.
- API references (Swagger/OpenAPI).
- Setup and troubleshooting guides for that specific service.

## 📝 Workflow

1. **Plan in Central**: Define *what* and *why* in the root `docs`.
2. **Implement in Repo**: Define *how* in the specific repo's `docs` or code.
3. **Sync**: Keep them linked. If a requirement changes, update the central doc first.
