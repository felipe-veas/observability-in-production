# Symptoms vs. Causes: Designing Actionable Signals

## 1. The Real Operational Problem

The fundamental failure of most alerting strategies is a confusion of boundaries. Monitoring systems are frequently configured to page on the internal state of the infrastructure rather than the external experience of the user.

The problem is that we're paging human beings because a machine crossed an arbitrary resource threshold. We treat high CPU utilization, memory pressure, or database lock contention as incidents in themselves. They aren't. They are attributes of a system under load.

An incident is only an incident if a user—whether that's a customer clicking a button or an upstream service making an API call—is experiencing pain. If a backend worker queue backs up for five minutes but the system is designed to tolerate ten minutes of latency without dropping data or violating a contract, there is no incident. Yet, in most organizations, the pager fires anyway.

## 2. Common Incorrect Approaches

This problem shows up most frequently in resource-based alerting:

**"CPU > 80% for 5 minutes"**
This is perhaps the most common and useless alert in modern operations. High CPU utilization often just means you're getting exactly what you paid for from your compute provider. It only matters if it induces latency or errors.

**"Pod Restarts > 5 in 15 minutes"**
Kubernetes and modern orchestrators are explicitly designed to terminate and restart unhealthy processes. This is the orchestrator doing its job. Alerting on a self-healing mechanism creates noise unless the restart loop is actively degrading the service’s ability to serve traffic.

**"Database Connections > 90% of max"**
While risky, this is a symptom of a potential future problem, not a current user-facing failure. It's a capacity planning metric masquerading as an operational emergency.

**Cause-Based Alerting**
Engineers often try to write alerts for every specific failure mode they've encountered in the past (e.g., "Alert if Redis cache hit rate drops below 40%"). This assumes we can predict all future failure modes. We can't.

## 3. Operational Consequences

Cause-based, resource-centric alerting hurts reliability and burns out engineers.

First, it generates massive false positives. If an engineer is paged at 2:00 AM because a database node hit 95% CPU, but the queries are still completing within acceptable latency bounds, the engineer has to log in, verify that users are unaffected, and then go back to sleep. This isn't incident response; it's manual babysitting of infrastructure.

Second, it trains engineers to ignore the pager. When 90% of alerts don't require action, the 10% that actually represent severe user impact are inevitably missed in the noise. This is how Sev-1 outages persist for hours while the on-call engineer assumes it's "just the Kafka alert again."

Finally, it creates a fragile operational posture. Because we're alerting on specific causes, we completely miss novel failure modes. If the system fails in a way we haven't explicitly thresholded (e.g., CPU is fine, memory is fine, but a misconfigured proxy is silently dropping 50% of traffic), the monitoring is completely green while the business burns.

## 4. A Better Reliability-Focused Approach

We need to aggressively shift our alerting strategy from infrastructure causes to user-visible symptoms.

An alert should only fire if the system is currently violating, or is about to violate, its implicit or explicit contract with users. The user doesn't care if your CPU is at 99%. They care if the page takes ten seconds to load, or if their transaction fails.

**Symptom-Based Alerting (The RED Method)**
We alert on the external symptoms of the service boundary:

- **Rate:** Has traffic dropped to zero unexpectedly?
- **Errors:** Are we returning HTTP 5xx errors or dropping messages?
- **Duration (Latency):** Are the 95th or 99th percentile response times exceeding our threshold?

If the CPU is at 100%, but latency and errors are flat, the pager stays silent. We use dashboards and logs during business hours to investigate the CPU spike as a capacity issue. We only use the pager when the CPU spike causes the symptoms to cross into unacceptable territory.

**Rethinking the "Cause"**
Resource metrics (CPU, memory, IOPS) and specific failure states (cache misses, queue depth) are not alerts. They are *investigative context*. When the symptom-based alert fires (e.g., "High API Error Rate"), the engineer opens a dashboard that displays the resource metrics to diagnose *why*.

## 5. Emphasizing Human Factors and Decision-Making

Alert design is fundamentally a contract between the system and the human responder.

When a pager fires, the system is stating: "Human judgment is urgently required to mitigate active harm." If the system breaks this contract by paging for self-healing events or benign resource utilization, it erodes the engineer's trust.

Engineers have to trust their tools. When an alert triggers, there should be zero ambiguity about whether an incident is occurring. The engineer shouldn't have to spend the first critical minutes of an outage trying to prove that the alert is real.

By restricting alerts strictly to user-visible symptoms, we protect the cognitive load of the responder. They wake up knowing the pain is real. They can immediately transition from "Is this a false alarm?" to "Where is the failure originating?" We don't squander their attention on CPU spikes; we reserve it exclusively for the moments when our users are suffering.
