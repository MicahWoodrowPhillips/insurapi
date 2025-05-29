# insurapi
An excercise in dusting off some old mental cobwebs and creating a b2b api gateway.  This one is for a hypothetical insurance validation company.

## Objective
Design an API Gateway that exposes internal insurance services to third-party partners (B2B clients) securely and reliably.

## Key Requirements
- Security: Authenticate clients via OAuth 2.0 or JWT.
-  Rate Limiting: Prevent abuse via per-client limits.
- Routing: Direct requests to appropriate internal services.
- Auditing: Maintain a persistent log of requests.
- Extensibility: Allow easy onboarding of new partners and services.
- Versioning: Support backwards-compatible changes.

---
## High-Level Components
### API Gateway
Handles authentication, authorization, rate limiting, and metrics logging.
Token validation using public key (JWT), issued via Authentication Service.
Forwards validated requests to a request router.

### Authentication Service
Issues tokens to clients via OAuth 2.0 client credentials flow.
Stores client credentials and scopes (permissions).

### Request Router
Forwards incoming requests to the correct internal insurance microservice.
Performs basic schema validation (optional) and version routing.

### Insurance Services
Microservices such as InsuranceServiceA, InsuranceServiceB.
Provide business logic, access to insurance data, CRUD operations.

### Logging & Audit Store
Writes structured logs (timestamp, client ID, request path, status).
Supports compliance auditing and operational debugging.

### Security Design
Use OAuth 2.0 client credentials for machine-to-machine auth.
JWT token issued by auth service and verified at the gateway.

---
## Scalability
Use a reverse proxy/load balancer (e.g., NGINX, Envoy) in front of the gateway.

Scale API gateway instances horizontally behind a load balancer.

Use API key quotas or Redis-backed rate limiting.

## Observability
Structured logging to ELK/CloudWatch for full audit trail.

Prometheus metrics per endpoint and per client.

Alerting on latency, error rate, auth failures.

## Resilience
Timeouts and retries with exponential backoff.

Circuit breaker for flaky downstream services.

Bulkhead pattern to isolate failing services.

---
Data Model (Simplified)
``` json
{
  "client_id": "abc123",
  "request_path": "/api/v1/policy/A",
  "timestamp": "2025-05-27T10:15:00Z",
  "status": 200,
  "response_time_ms": 153
}
```
---
## Tech Stack
Layer -	Tech Choices
API Gateway:	Spring Cloud Gateway, Kong, or AWS API GW
Auth:	Keycloak, Okta, or custom Spring Boot OAuth2
Services:	Spring Boot + JPA + PostgreSQL
Logging:	Fluent Bit → Elasticsearch → Kibana
Rate Limiting:	Redis + Bucket4J (Java)
