# Specification: Massive Data Core Client

## Purpose
Define the behavioral requirements, error mapping, and network resilience constraints for the core Massive Data client infrastructure.

## Requirements

### Requirement: Unified Facade Initialization
The `MassiveClient` MUST act as the primary configuration entry point.
- MUST accept `api_key` (string).
- MUST accept an optional `timeout` parameter (float, default: 15.0 seconds).
- MUST initialize a shared `HttpClient` instance that will be passed down to sub-services.

### Requirement: Authentication Injection
The `HttpClient` MUST inject the `Authorization: Bearer <apiKey>` HTTP header into EVERY outgoing request to the Massive Data API.

#### Scenario: Valid Request Authentication
- GIVEN a configured `HttpClient` with an API key
- WHEN a GET request is dispatched
- THEN the raw HTTP headers MUST contain `Authorization: Bearer <apiKey>`

### Requirement: Resilient Network Transport
The `HttpClient` MUST provide enterprise-grade reliability:
1. **Rate Limiting**: On receiving `HTTP 429 (Too Many Requests)`, the transport MUST back off exponentially and retry up to 3 times before failing.
2. **Server Errors**: On `HTTP 500/502/503/504`, the client MUST retry with backoff.
3. **Authentication Failure**: On `HTTP 401 (Unauthorized)`, the client MUST immediately raise `MassiveAuthError` without retrying.
4. **Timeouts**: Requests MUST enforce the configured timeout. If exceeded, the client MUST raise `MassiveTransportError`.

#### Scenario: HTTP 429 Rate limit backoff and recovery
- GIVEN a request that receives `HTTP 429` on the initial attempt
- AND succeeds with `HTTP 200` on the second attempt
- WHEN the request is executed
- THEN the client MUST sleep according to retry backoff and successfully return the data without raising an exception

#### Scenario: Fatal authentication failure
- GIVEN an invalid or expired API key resulting in `HTTP 401`
- WHEN a request is executed
- THEN the client MUST immediately raise `MassiveAuthErrork with an informative error message