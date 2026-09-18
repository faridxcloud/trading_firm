# Specification: Massive Data Core Client

## Purpose
Define the behavioral requirements, clear boundaries, and network resilience constraints for the core Massive Data client infrastructure.

## Module 1: Error Hierarchy (errors.py)
**Responsibility:** Define a standardized set of exceptions to decouple the application layer from raw HTTP status codes.

### Requirement: Strongly Typed Exceptions
The core MUST provide the following exception hierarchy inheriting from a base `MassiveError`:
- `MassiveError`: Base class for all library exceptions.
- `MassiveAuthError`: Represents an invalid or missing API key.
- `MassiveRateLimitError`: Represents a hard rate limit block that could not be recovered by backoff.
- `MassiveTransportError`: Represents timeouts, DNS failures, or unrecoverable server errors.

---

## Module 2: Network Transport (http.py)
**Responsibility:** Handle low-level HTTP communication, authentication injection, response parsing, and resilience (retries). This module MUST NOT define the errors, it only raises them.

### Requirement: Authentication Injection
The `HttpClient` MUST inject the `Authorization: Bearer <apiKey>` header into EVERY outgoing request.

#### Scenario: Valid Request Authentication
- GIVEN a configured `HttpClient` with an API key
- WHEN a GET request is dispatched
- THEN the raw HTTP headers MUST contain `Authorization: Bearer <apiKey>`

### Requirement: Resilient Backoff Strategy
The `HttpClient` MUST provide self-healing mechanisms for transient failures:
- **HTTP 429 (Too Many Requests)**: MUST retry up to 3 times with exponential backoff before raising `MassiveRateLimitError`.
- **HTTP 5xx (Server Errors)**: MUST retry up to 3 times with exponential backoff before raising `MassiveTransportError`.

#### Scenario: Recovery from HTTP 429
- GIVEN an initial request that receives HTTT 429
- AND the subsequent retry succeeds with HTTP 200
- WHEN the request is executed
- THEN the client MUST sleep according to backoff logic, retry, and successfully return the data without bubbling the exception.

### Requirement: Fail-Fast Mapping
The `HttpClient` MUST immediately fail without retrying for non-transient errors:
- **HTTP 401 (Unauthorized)**: MUST immediately raise `MassiveAuthError`.

---

## Module 3: Unified Facade (client.py)
**Responsibility:** Act as the primary entry point for the consumer, managing the lifecycle of the `HttpClient` and composing domain-specific sub-services.

### Requirement: Facade Initialization
The `MassiveClient` MUST initialize exactly ONE instance of `HttpClient` per configuration.
- MUST accept `api_key` (string).
- MUST accept an optional `timeout` parameter (float, default: 15.0 seconds).
- MUST expose the configured `HttpClient` internally so domain services (like Tickers) can receive it via dependency injection.