# Structured Logging: Context Over Volume

## 1. The Real Operational Problem

The prevailing wisdom in software engineering is to "log everything." Developers sprinkle `logger.info("Doing X")` and `logger.debug("Finished Y")` throughout their codebase, assuming this exhaustive commentary will reveal the root cause of an incident.

In production, this approach fails.

When a system processes thousands of requests per second, free-text logging creates an unreadable, high-velocity stream of disjointed text. During an outage, an engineer searching for the reason a specific checkout failed has to use complex regex to piece together fragments of text spread across multiple log lines, often interleaved with logs from concurrent requests. This wastes the most critical minutes of an incident.

Furthermore, free-text logs are inherently opaque to machines. You cannot efficiently graph, alert on, or query unstructured strings without building fragile parsing rules (like grok filters) that break the moment a developer changes the wording of a log message.

## 2. The Solution: Structured Logging (JSON)

We fundamentally reject free-text logging in production. **All production logs must be structured (JSON).**

Structured logging treats a log entry not as a string of text for a human to read, but as a strongly-typed event object for a machine to query.

```json
// BAD: Free text
2023-10-24 10:15:30 INFO  [UserService] User u_12345 failed to login due to invalid_password

// GOOD: Structured JSON
{
  "timestamp": "2023-10-24T10:15:30Z",
  "level": "INFO",
  "service": "user-service",
  "trace_id": "8f9a2b3c4d5e6f7a",
  "event": "user_login_failed",
  "user_id": "u_12345",
  "reason": "invalid_password"
}
```

With structured logs, an engineer during an incident doesn't have to guess the phrasing of the error message. They simply query: `service:user-service AND event:user_login_failed AND reason:invalid_password`. This query is deterministic, exact, and instantaneous.

## 3. The Canonical Log Line

The second major flaw in traditional logging is volume. Emitting five separate log lines for a single HTTP request ("Received request", "Fetched user from DB", "Calculated price", "Saved to DB", "Returned response") destroys your signal-to-noise ratio.

We enforce the pattern of the **Canonical Log Line**.

Instead of logging at every step of a transaction, a service should build up an execution context in memory as the request is processed. When the request completes (or fails), the service emits **exactly one** comprehensive JSON log line containing all the context for that entire transaction.

A Canonical Log Line must include:

* **Identification:** `trace_id`, `span_id`, `request_id`.
* **Execution Context:** `http.method`, `http.route`, `http.status_code`, `user_agent`.
* **Business Context:** `user_id`, `tenant_id`, `order_id` (Crucial for high-cardinality debugging).
* **Performance:** `duration_ms`, `db_query_count`, `cache_hits`, `cache_misses`.

If you need to know *why* a request was slow, you don't look for a "slow query" log. You look at the Canonical Log Line for that request and observe that `db_query_count` is 500 instead of the expected 2.

## 4. Emphasizing Human Factors and Decision-Making

We enforce structured logging and canonical lines because we optimize for the cognitive capacity of the engineer at 3:00 AM.

When the pager fires, the engineer shouldn't have to mentally reconstruct the execution path of a request by joining fragmented text strings across terminal windows. The Canonical Log Line delivers the entire state of the transaction in a single, pre-correlated payload. It shifts the burden of correlation from the stressed human brain to the machine, allowing the engineer to focus entirely on diagnosis and mitigation.
