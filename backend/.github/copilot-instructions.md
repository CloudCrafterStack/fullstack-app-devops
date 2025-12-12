# AI Coding Guidelines for CRUD Spring Application

## Architecture Overview

This is a Spring Boot 3.2.4 REST API (Java 21) implementing a CRUD system for **Courses** with nested **Lessons**.

**Key Structure:**
- **Controller** → **Service** (business logic + validation) → **Repository** (JPA) → **Database**
- **DTOs** for request/response transformation via **Mappers**
- **Soft deletes** using `@SQLDelete` and `@SQLRestriction` on entities
- **Custom validation** with `@ValueOfEnum` for enum string-to-enum conversion

## Core Patterns & Conventions

### 1. Service Layer Validation
- Services use `@Validated` and method-level constraints: `@Positive @NotNull Long id`
- Tests use `ValidationAdvice` (test config) to enable method-parameter validation during unit tests
- Exceptions: `RecordNotFoundException` for missing records, `BusinessException` for rule violations
- Global error handling via `ApplicationControllerAdvice` returns HTTP 400/404/etc

### 2. DTOs & Records
- **Request**: `CourseRequestDTO` uses records + jakarta validation annotations
- **Response**: `CourseDTO` for entity serialization; `CoursePageDTO` for paginated results
- **Custom annotations**: `@ValueOfEnum(enumClass = Category.class)` validates enum string values
- Mappers convert entities ↔ DTOs; use `courseMapper.convertCategoryValue()` for enum conversion

### 3. Enum Pattern with Database Converters
- Enums (Category, Status) define `getValue()` returning database string representation
- `CategoryConverter`/`StatusConverter` implement JPA `AttributeConverter` for DB persistence
- Controllers accept string values; converters handle translation automatically
- Endpoint example: POST with `"category": "Front-end"` automatically converts to enum

### 4. Pagination
- Service returns `Page<Course>` from repository's `findAll(PageRequest.of(page, pageSize))`
- Mapped to `CoursePageDTO(list, totalElements, totalPages)` for clients
- Page parameter defaults to 0, size to 10 (max 1000)

### 5. Cascading & Orphan Removal
- `Course.lessons` uses `cascade = CascadeType.ALL, orphanRemoval = true`
- Updating course lessons: remove missing lessons from set, add new ones
- `mergeLessonsForUpdate()` in service handles lesson lifecycle during updates

## Build & Test Commands

```bash
# Full test run with JaCoCo coverage report
mvn jacoco:prepare-agent test install jacoco:report

# Watch coverage in VSCode: install "Coverage Gutters" extension, then click "Watch" in status bar
```

**Test Structure:** 
- Unit tests in `src/test/java/com/loiane/`
- Uses `@SpringJUnitConfig` for minimal Spring context loading (not `@SpringBootTest`)
- Mocks repositories; validates service logic in isolation
- `TestData` class provides fixtures

## File Organization Reference

- **Controllers**: `src/main/java/com/loiane/course/CourseController.java` (REST endpoints)
- **Services**: `CourseService.java` (business logic, validation, error handling)
- **Entities**: `Course.java`, `Lesson.java` (JPA entities with decorators)
- **DTOs**: `src/main/java/com/loiane/course/dto/` (request/response objects)
- **Mappers**: `CourseMapper.java` (entity ↔ DTO transformation)
- **Validation**: `src/main/java/com/loiane/shared/validation/` (custom validators)
- **Exceptions**: `src/main/java/com/loiane/exception/` (custom exception types)

## Endpoint Pattern

All endpoints return DTOs (never raw entities):
- `GET /api/courses` → `CoursePageDTO` (paginated)
- `GET /api/courses/searchByName?name=X` → `List<CourseDTO>`
- `GET /api/courses/{id}` → `CourseDTO`
- `POST /api/courses` → `CourseDTO` (request: `CourseRequestDTO`)
- `PUT /api/courses/{id}` → `CourseDTO`
- `DELETE /api/courses/{id}` → no body (204)

## When Implementing Features

1. **Adding a field to Course**: Update entity, DTO, mapper, tests
2. **New validation rule**: Use Jakarta annotations or create custom validator in `shared/validation/`
3. **Service method**: Apply parameter-level constraints; throw `BusinessException` or `RecordNotFoundException`
4. **Testing service logic**: Use `@SpringJUnitConfig(classes = {ServiceClass.class, Mapper.class})` with `@MockBean` repository
5. **Modifying database logic**: Soft deletes work automatically; queries respect `@SQLRestriction`
