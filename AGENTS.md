# AGENTS.md

Guidance for agents working on the Ekatra backend.

Ekatra is an India-focused dating and relationship platform. The backend must be built with Clean Architecture, CQRS, .NET, Redis, SignalR, strong safety controls, and production-minded security from the beginning.

Do not create application code, solution files, project files, migrations, database scripts, packages, controllers, handlers, entities, hubs, or services until the user explicitly asks for implementation.

## Product Vision

Ekatra means togetherness, connection, and bringing people together.

The product should help adults in India create meaningful, safe, and respectful connections. It should support users from metro cities as well as Tier 2 and Tier 3 cities, with culturally aware and regional-language-friendly experiences.

Ekatra should provide premium-style user features for free. Core features must not be locked behind subscriptions, forced payments, or pay-to-message flows in the MVP.

Free core features include profile creation, photos, prompts, interests, discovery, likes, passes, matching, messaging, read receipts, blocking, reporting, verification features, safety tools, basic compatibility information, and reasonable discovery filters.

## Target Users

- Adults aged 18 and above.
- Users in India.
- Users looking for friendship, dating, serious relationships, long-term relationships, or marriage-oriented relationships.
- Users from metro, Tier 2, and Tier 3 cities.
- Users who prefer regional languages and culturally aware experiences.

The application must not knowingly allow users below 18 years of age.

## MVP Scope

The MVP must use a modular monolith. Do not start with microservices, complex AI matching, video calling, live events, advanced payments, cryptocurrency, a large analytics platform, multiple databases, or complex event sourcing.

MVP features:

- Phone OTP authentication.
- User account management.
- Profile creation and editing.
- Profile photos.
- Interests.
- Profile prompts.
- Dating intentions.
- Discovery feed.
- Like and pass.
- Mutual matching.
- Match list.
- Text chat.
- Read status.
- Block, report, and unmatch.
- Admin moderation.
- Basic notifications.
- Account deletion.
- Logging.
- Tests.
- Production-ready security basics.

## Core Modules

Expected backend modules:

- Authentication.
- Users.
- Profiles.
- Photos.
- Interests.
- Languages.
- Discovery.
- Matching.
- Conversations.
- Messaging.
- Notifications.
- Safety.
- Reporting.
- Verification.
- Moderation.
- Administration.
- Audit logging.
- File storage.
- Background jobs.

## Technology Direction

Use:

- C#.
- ASP.NET Core Web API.
- .NET 8 or the approved current .NET version.
- Clean Architecture.
- CQRS.
- MediatR, if approved for the solution.
- FluentValidation, if approved for the solution.
- Entity Framework Core.
- Redis.
- SignalR.
- Docker, when infrastructure setup is requested.
- xUnit.
- Integration testing.
- OpenAPI/Swagger.
- Structured logging.
- Health checks.

Possible future tools:

- Dapper for optimized read queries.
- Background job processing.
- Push notification provider.
- Cloud object storage.
- OpenTelemetry.
- Content moderation provider.

Database note: the earlier repository instruction specified SQL Server managed through SSMS, while the pasted product overview specifies PostgreSQL. Do not implement persistence, migrations, database providers, or SQL scripts until the user confirms the database choice. Until then, keep database guidance provider-neutral except where SQL Server/SSMS is explicitly requested.

## Architecture

Use Clean Architecture with dependencies pointing inward.

Expected projects when implementation is requested:

- `Ekatra.Api`
- `Ekatra.Application`
- `Ekatra.Domain`
- `Ekatra.Infrastructure`
- `Ekatra.Contracts`
- `Ekatra.SharedKernel`

Layer responsibilities:

- `Domain`: entities, value objects, domain events, enums, domain exceptions, invariants, and pure business behavior.
- `Application`: use cases, CQRS commands and queries, DTOs, validators, interfaces, pipeline behaviors, authorization checks, and orchestration.
- `Infrastructure`: database access, Redis, SignalR infrastructure, external services, file storage, background jobs, and implementations of application interfaces.
- `Api`: controllers or minimal API endpoints, request/response mapping, authentication setup, middleware, dependency injection composition, OpenAPI, and API behavior.
- `Contracts`: external request/response contracts and integration contracts when needed.
- `SharedKernel`: truly shared primitives only; keep it small.

Dependency rules:

- `Domain` must not depend on `Application`, `Infrastructure`, `Api`, ASP.NET Core, EF Core, Redis, or SignalR.
- `Application` may depend on `Domain` and shared abstractions, but not `Infrastructure` or `Api`.
- `Infrastructure` may depend on `Application` and `Domain`.
- `Api` may depend on `Application`, `Infrastructure`, and `Contracts`.
- API request and response DTOs must not be domain entities.
- Do not return EF Core entities directly from controllers.
- Controllers and hubs must remain thin.
- Business rules belong in `Domain`.
- Use cases belong in `Application`.
- External integrations belong in `Infrastructure`.

## CQRS Rules

Commands change state. Queries only read data.

Command examples:

- `SendOtpCommand`
- `VerifyOtpCommand`
- `CreateProfileCommand`
- `UpdateProfileCommand`
- `LikeProfileCommand`
- `PassProfileCommand`
- `SendMessageCommand`
- `BlockUserCommand`
- `ReportUserCommand`

Query examples:

- `GetCurrentUserQuery`
- `GetMyProfileQuery`
- `GetDiscoveryFeedQuery`
- `GetMyMatchesQuery`
- `GetConversationsQuery`
- `GetConversationMessagesQuery`
- `GetBlockedUsersQuery`

Use this feature structure when implementation begins:

```text
FeatureName/
  Commands/
    ActionName/
      ActionNameCommand.cs
      ActionNameCommandHandler.cs
      ActionNameCommandValidator.cs
  Queries/
    QueryName/
      QueryNameQuery.cs
      QueryNameQueryHandler.cs
```

Rules:

- Each command or query should have one clear responsibility.
- Commands should return only what the caller needs.
- Queries must not mutate state.
- Prefer MediatR-style request handlers if MediatR is approved.
- Use validators before handlers execute.
- Use pipeline behaviors for validation, logging, transactions, authorization checks, and other cross-cutting concerns when appropriate.
- Do not put business rules in controllers, SignalR hubs, or infrastructure services.

## .NET Coding Rules

- Use modern C# and .NET conventions already present in the repository.
- Enable nullable reference types for new projects.
- Use `async` and `await` for I/O.
- Accept and pass `CancellationToken` in async API, application, and infrastructure methods.
- Do not block async code with `.Result`, `.Wait()`, or sync-over-async.
- Prefer constructor injection.
- Prefer explicit models over loosely typed dictionaries across boundaries.
- Keep methods small and intention-revealing.
- Do not introduce global mutable state.
- Avoid static service locators.
- Prefer options classes for configuration.
- Keep secrets out of source control.
- Do not swallow exceptions silently.
- Add comments only when they explain non-obvious decisions.

## Authentication

Authentication requirements:

- Phone number OTP login.
- OTP expiry.
- OTP retry limit.
- OTP request rate limiting.
- Hashed OTP storage.
- JWT access tokens.
- Refresh tokens.
- Refresh token revocation.
- Current user endpoint.
- Logout.
- Account status checking.
- Age eligibility confirmation.

Security rules:

- Never log OTP values.
- Never store OTP values in plain text.
- Never store secrets in source control.
- Do not trust user IDs from request bodies.
- Get the authenticated user ID from claims.
- Add authorization and ownership checks to protected operations.

## Profiles

Profile fields may include:

- Display name.
- Date of birth.
- Gender.
- City.
- Location coordinates, if enabled.
- Bio.
- Dating intention.
- Languages.
- Interests.
- Profile prompts.
- Profile photos.
- Verification status.
- Profile visibility.
- Last active status, controlled by privacy settings.

Rules:

- Users must be 18 or older.
- Users can edit only their own profile.
- Profile photos must be validated.
- Image size and file type must be restricted.
- Do not expose private profile fields.
- Do not expose exact location by default.
- Use approximate distance instead of exact coordinates.

Dating intentions may include friendship, casual dating, serious relationship, long-term relationship, marriage, and open to exploring. Visibility must respect privacy settings.

## Discovery

Discovery should support:

- Age preferences.
- Distance preferences.
- Gender preferences.
- Dating intention preferences.
- Language preferences.
- Interest-based filtering.
- Profile completion filtering.
- Blocked-user exclusion.
- Already-seen profile exclusion.
- Pagination.
- Basic ranking.

Discovery must not show blocked users, users who blocked the current user, suspended users, banned users, deleted users, ineligible users, or profiles hidden by privacy settings.

The first version should use a simple, explainable ranking system. Do not build a complex AI recommendation system in the MVP.

## Matching

A match is created when two eligible users mutually like each other.

Rules:

- Prevent self-matching.
- Prevent duplicate matches.
- Respect blocks.
- Respect account status.
- Store match creation time.
- Support unmatching.
- Notify both users when appropriate.
- Allow chat only for eligible matched users.

## Messaging and SignalR

Messaging requirements:

- Only matched users can message each other.
- Conversation membership must be verified.
- Store messages securely.
- Support text messages in the MVP.
- Support read status.
- Support message timestamps.
- Support SignalR real-time delivery.
- Support offline message retrieval.
- Support blocking and reporting.
- Add message rate limits.
- Do not log private message contents unnecessarily.

SignalR rules:

- Hubs should be thin.
- Hubs should delegate work to application commands or services.
- Do not put business logic in hub methods.
- Validate and authorize hub actions.
- Use strongly typed hub contracts when practical.
- Prefer groups for scoped broadcasts.
- Do not broadcast sensitive data to broad channels.
- Persist important state before notifying clients.
- Treat SignalR messages as delivery notifications, not the source of truth.

Future messaging features may include voice messages, video calling, media messages, message translation, and conversation safety warnings. Do not implement these in the MVP unless explicitly requested.

## Safety and Moderation

Safety is a core feature, not an optional premium feature.

Required safety features:

- Block user.
- Unblock user.
- Report user.
- Report message.
- Unmatch.
- Hide profile.
- Report reason selection.
- Moderation case creation.
- Admin review queue.
- User suspension.
- User ban.
- Audit logging.
- Rate limiting.
- Abuse detection.
- Basic profanity filtering.

Report statuses:

- `Open`
- `UnderReview`
- `Resolved`
- `Rejected`
- `Escalated`

## Verification

Verification may include phone verification, email verification, selfie verification, profile photo review, and a verification badge.

Rules:

- Design verification with privacy in mind.
- Do not expose verification documents publicly.
- Store only the minimum required information.
- Restrict access to authorized administrators.
- Keep verification actions auditable.

## Notifications

Notification types:

- OTP notification.
- New match notification.
- New message notification.
- Verification status notification.
- Report status notification.
- Safety notification.
- Account status notification.

Process notifications asynchronously where appropriate.

## Administration

Admin functionality may include:

- Admin authentication.
- Role-based permissions.
- User search.
- User profile review.
- Suspend user.
- Ban user.
- Restore user.
- Review reports.
- Review verification requests.
- Review moderation cases.
- Manage interests.
- Manage profile prompts.
- View audit logs.
- View basic platform metrics.

Every sensitive admin action must create an audit log.

## Privacy and Account Management

Required capabilities:

- Account deletion.
- Profile visibility controls.
- Data minimization.
- Privacy-safe location handling.
- Secure file access.
- Consent tracking where needed.
- Privacy policy support.
- Terms and community guidelines support.
- User data export planning.

## Database Rules

Do not create migrations automatically without approval from the designated migration owner.

Provider-neutral rules:

- Use stable unique identifiers.
- Use UTC timestamps.
- Use foreign keys.
- Add proper indexes based on access patterns.
- Add unique constraints where needed.
- Use soft deletion only where justified.
- Use audit fields where useful.
- Use database transactions for important state changes.
- Keep constraints close to the data where appropriate.
- Do not store secrets or credentials in SQL scripts.
- Avoid business logic in stored procedures unless explicitly approved.

If SQL Server is confirmed:

- SSMS may be used for inspection, query validation, and operational debugging.
- Prefer migrations or versioned SQL scripts for schema changes once the project structure exists.
- Do not make untracked manual schema changes in SSMS without recording them in the repository.

If PostgreSQL is confirmed:

- Prefer UUID identifiers unless the user approves another strategy.
- Use PostgreSQL-specific features only when they provide clear value and are isolated from domain logic.

## Redis

Use Redis for cache, distributed coordination, ephemeral state, rate limiting, or pub/sub only when it is the right tool.

Rules:

- Treat Redis as disposable unless a feature explicitly requires durable behavior elsewhere.
- Do not make Redis the source of truth for business data.
- Use clear key naming with a project prefix and feature namespace.
- Set expirations for cache keys unless there is a deliberate reason not to.
- Prevent cache stampedes for expensive data.
- Invalidate or update cache entries as part of the relevant use case.
- Do not cache sensitive data unless explicitly approved and protected.

Example key style:

```text
ekatra:{environment}:{feature}:{identifier}
```

## API Rules

- Keep controllers or endpoints thin.
- Use application commands and queries for use cases.
- Return consistent error responses.
- Validate input before processing.
- Use appropriate HTTP status codes.
- Do not expose domain entities directly as API responses.
- Version public APIs if breaking changes are introduced.
- Keep authentication and authorization explicit.
- Use OpenAPI/Swagger when API implementation begins.

## Error Handling and Observability

- Use structured logging.
- Log enough context to debug, but never log secrets, tokens, passwords, OTPs, private message contents, or sensitive personal data.
- Prefer centralized exception handling middleware.
- Convert known application and domain errors into stable API responses.
- Use health checks for database, Redis, and other critical dependencies when available.
- Add correlation or request IDs where practical.

## Testing Rules

Every implemented feature should include appropriate tests.

Required test types:

- Domain unit tests.
- Command handler tests.
- Query handler tests.
- Validator tests.
- Repository or infrastructure tests.
- API integration tests.
- Authorization tests.
- Security-related tests.

Important scenarios:

- A user cannot access another user's private data.
- A user cannot message an unmatched user.
- Blocked users cannot interact.
- Suspended users cannot use protected features.
- Duplicate likes do not create duplicate matches.
- Duplicate matches are prevented.
- OTP expiry and retry limits work.
- Reports create moderation cases.
- Account deletion works correctly.

Prefer focused tests over broad fragile tests. Do not require live production services for automated tests.

## Security Rules

- Never commit secrets.
- Use parameterized queries or EF Core query APIs; never concatenate user input into SQL.
- Validate all external input.
- Enforce authorization server-side.
- Use least privilege for database users and service accounts.
- Protect real-time channels with authentication and group-level authorization.
- Avoid leaking internal exception details to clients.
- Keep private location, moderation, verification, and messaging data protected by default.

## Development Workflow For Agents

Before making changes:

1. Read this `AGENTS.md`.
2. Inspect the existing repository.
3. Explain the implementation plan for non-trivial work.
4. List files expected to change.
5. Identify database changes.
6. Identify API changes.
7. Ask for clarification if requirements are ambiguous enough to risk building the wrong thing.

While coding:

- Implement one task at a time.
- Do not modify unrelated files.
- Do not rewrite working code unnecessarily.
- Do not add packages without approval.
- Do not invent requirements.
- Use async methods and cancellation tokens.
- Follow existing naming conventions.
- Add validation and authorization.
- Add tests with each feature.

After coding:

1. Run formatting when available.
2. Run `dotnet build` when a solution exists.
3. Run `dotnet test` when tests exist.
4. Review the git diff.
5. Report changed files.
6. Report database changes.
7. Report API changes.
8. Report remaining issues.
9. Explain any skipped tests.

## Git and Change Discipline

- Keep commits focused.
- Do not rewrite or revert user changes unless explicitly asked.
- Do not add generated files, binaries, local database files, or environment-specific settings unless explicitly required.
- Before editing, inspect nearby code and follow existing patterns.
- Before finishing implementation work, run the relevant build and tests when available.

## What Not To Do Yet

Until the user explicitly asks for implementation:

- Do not create solution or project files.
- Do not scaffold controllers, entities, handlers, migrations, hubs, or services.
- Do not add NuGet packages.
- Do not create database scripts.
- Do not start a server.

