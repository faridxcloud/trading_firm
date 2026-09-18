# Open Spec: Massive Data Core Client

## Context
The trading firm requires robust, reliable connectivity to the Massive Data REST API. Before implementing specific data endpoints (like Tickers, Aggregates, Quotes), a foundational infrastructure layer is required to manage network transport, authentication, and error handling.

## Proposal
Implement the `massive.core` module to serve as the unified entry point and resilient transport layer for all Massive Data API integrations.

### Key Components
1. **HttpClient**: A specialized HTTP session wrapper responsible for automatically injecting the `Authorization: Bearer <apiKey>` header into every request, enforcing timeouts, and implementing exponential backoff for `HTTP 429` (Rate Limit) and `HTTP 5xx` (Server Error) responses.
2. **MassiveClient (Facade)**: The main entry point for the consumer. It initializes the `HttpClient` and acts as the orchestrator to mount domain-specific sub-services (which will be defined in subsequent specs).
3. **Error Hierarchy**: A standardized set of exceptions (`MassiveError`, `MassiveAuthError`, `MassiveRateLimitError`, `MassiveTransportError`) to decouple the application from raw HTTP status codes.

## Success Criteria
- A `MassiveClient` can be instantiated with an `api_key`.
- Rate limits and transient network failures are retried automatically without bubbling up to the caller immediately.
- Authentication failures (`401`) fail fast with a descriptive `MassiveAuthError`.