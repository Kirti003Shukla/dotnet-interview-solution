# Solution Documentation

**Candidate Name:** [Kirti Shukla]  
**Completion Date:** [18/7/2026]

---

## Problems Identified

_Describe the issues you found in the original implementation. Consider aspects like:_
- Architecture and design patterns
- Code quality and maintainability
- Security vulnerabilities
- Performance concerns
- Testing gaps

- SQL injection vulnerability in data access methods because SQL statements were built with string interpolation.
- Data access code was tightly coupled to raw SQL strings, making behavior brittle when user input contains special characters.
- Security risk had higher priority than optional framework migration work due to the two-day time constraint.
- Controller actions manually instantiated the service, which increased coupling and reduced testability.
- Database schema creation was tied to application startup only, causing failures when the service was used directly (for example from tests).
- API contract used action-style POST endpoints for all operations, including reads and deletes, which is not aligned with HTTP semantics.

---

## Architectural Decisions

_Explain the architecture you chose and why. Consider:_
- Design patterns applied
- Project structure changes
- Technology choices
- Separation of concerns

- Keep the existing SQLite + ADO.NET approach for this iteration, but harden the implementation using parameterized SQL in all CRUD operations.
- Apply minimal, high-impact refactoring first to reduce security risk without introducing broad persistence changes.
- Keep the service boundary intact so EF Core can be introduced later with lower risk.
- Register TodoService in dependency injection and inject it into the controller via constructor injection.
- Centralize table initialization in TodoService with a one-time guarded initialization to ensure schema availability in both API runtime and test execution paths.
- Adopt REST-style endpoints and verbs: POST for create, GET for retrieval, PUT for updates, and DELETE for removal under a single `/api/todos` resource route.

---

## Trade-offs

_Discuss compromises you made and the reasoning behind them. Consider:_
- What did you prioritize?
- What did you defer or simplify?
- What alternatives did you consider?

- Prioritized immediate security and correctness improvements over ORM migration.
- Deferred EF Core adoption to a follow-up iteration to avoid migration and behavior-change risk within the two-day window.
- Introduced DI without adding extra abstraction layers in this step to keep refactoring focused and low risk.
- Accepted a lightweight static initialization guard in the service to improve reliability quickly, with the option to evolve to migration-driven startup in a later iteration.
- Accepted a breaking API route change from action-style endpoints to RESTful resource routes to improve long-term API usability and standards compliance.


---

## How to Run

### Prerequisites
- .NET 8.0 SDK

### Build
```bash
dotnet build TodoApi.sln
```

### Run
```bash
dotnet run --project TodoApi
```

### Test
```bash
dotnet test
```

---

## API Documentation

### Endpoints

#### Create TODO
```
Method: POST
URL: /api/todos
Request Body: { "title": "Buy milk", "description": "2 liters", "isCompleted": false }
Response: 201 Created + created TODO item
```

#### Get TODO(s)
```
Method: GET
URL: /api/todos
Request: no body
Response: 200 OK + array of TODO items

Method: GET
URL: /api/todos/{id}
Request: path parameter `id`
Response: 200 OK + TODO item, or 404 Not Found
```

#### Update TODO
```
Method: PUT
URL: /api/todos/{id}
Request Body: { "title": "Buy milk", "description": "3 liters", "isCompleted": true }
Response: 200 OK + updated TODO item, or 404 Not Found
```

#### Delete TODO
```
Method: DELETE
URL: /api/todos/{id}
Request: path parameter `id`
Response: 204 No Content, or 404 Not Found
```

---

## Future Improvements

_What would you do if you had more time? Consider:_
- Additional features
- Performance optimizations
- Enhanced testing
- Better documentation
- Deployment considerations

- Replace the current ADO.NET data access with EF Core and migrations once the refactored API behavior is stable, to improve maintainability, schema evolution, and developer productivity.
