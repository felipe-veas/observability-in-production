# SLO-Based Alerting: Aligning Operations with User Impact

## 1. The Real Operational Problem

The most pervasive debate in any engineering organization during an outage is the subjective definition of "broken."

When an API's latency spikes from 200ms to 800ms, is that an incident? The backend engineer might say no, arguing that the database is still serving requests and no errors are being thrown. The frontend engineer might say yes, pointing out that the UI feels sluggish. The product manager might be completely unaware until a VIP customer complains.

This subjectivity is a massive operational liability. Without a mathematically rigorous, universally agreed-upon definition of reliability, alerting becomes an exercise in personal preference. Engineers page each other based on gut feelings about metric spikes. Conversely, they ignore slow-burning degradations because no single static threshold was crossed. We lack a common language to quantify user pain.

## 2. Common Incorrect Approaches

Historically, the industry has attempted to solve this subjectivity through deeply flawed technical implementations:

**Static Thresholding**
The most common approach is guessing a number and hardcoding it into an alert rule: "Page if latency > 500ms for 5 minutes." This is arbitrary. Why 500ms? Why 5 minutes? When traffic patterns change, or when a new feature inherently alters the performance profile, the threshold breaks. It pages when users are fine, or it stays silent while users abandon the platform.

**Alerting on Microservice Dependencies**
In distributed architectures, teams often alert on the health of internal dependencies rather than the external user journey. If Service A calls Service B, and Service B's error rate spikes, Service B's team gets paged. However, if Service A implements aggressive retries and circuit breakers that completely mask the failure from the user, paging Service B in the middle of the night is a waste of human capital.

**Percentile Guesswork**
Engineers often use p99 or p95 metrics for alerting without understanding the distribution of their traffic. Alerting on a p99 latency spike during a low-traffic period might mean that exactly two requests were slow. Paging a human for two slow requests is the definition of operational immaturity.

## 3. Operational Consequences

The consequences of these approaches are a severely degraded on-call experience and a profound misalignment between engineering effort and customer satisfaction.

When alerts aren't tied to user impact, engineers spend their on-call shifts fighting the monitoring system instead of improving reliability. They wake up at 2:00 AM to acknowledge an alert for a system that is operating exactly as designed under load. They write complex, brittle alerting rules with dozens of exceptions to quiet the noise, which invariably leads to missing a real incident when the rules become too complex to reason about.

Conversely, a system can suffer a slow bleed—a 2% error rate that persists for days—and never trigger a static threshold alert. The business loses revenue, users churn, and the engineering team remains blissfully unaware because their dashboards are technically green.

## 4. A Better Reliability-Focused Approach

The solution is entirely abandoning static thresholds in favor of **Service Level Objectives (SLOs)** and **Burn Rate Alerting**.

An SLO is a mathematical contract regarding the reliability of a specific user journey (e.g., "99.9% of all checkout requests in a 30-day window will succeed in under 500ms"). This immediately creates an **Error Budget**—the exact number of requests allowed to fail before we violate our contract.

We do not alert on CPU, memory, or static latency numbers. We alert exclusively on the **Burn Rate** of the Error Budget.

If our budget is draining so fast that it will be entirely consumed in the next 4 hours, that is a critical Sev-1 incident. We page the on-call engineer immediately, regardless of the time. The alert is indisputable: users are actively feeling pain at a rate that threatens the business.

If the budget is burning at a rate that will consume it in 3 days, we do not page. We create a high-priority ticket for the team to investigate during normal business hours. The system is degraded, but it's not an emergency.

If a microservice fails completely, but the architecture gracefully degrades and the user-facing SLO is unaffected, the pager remains silent.

## 5. Emphasizing Human Factors and Decision-Making

SLOs and burn rates fundamentally change the psychology of operations. They remove emotion and debate from incident response.

When the pager rings, the engineer knows with mathematical certainty that the system is broken and users are suffering. There is no need to check five different dashboards to see if the alert is real. The alert is the definition of real.

This creates immense psychological safety. An engineer can confidently go to sleep knowing the pager will only wake them if the business is actively losing its reliability contract. It empowers them to say "no" to paging on infrastructure metrics, pointing to the SLO as the ultimate arbiter of system health.

By aligning our alerting strategy entirely with user impact, we align our engineering effort with business value. We stop managing servers and start managing the user experience. We treat the on-call engineer's attention as a scarce resource, deployed only when the mathematics of reliability demand it.
