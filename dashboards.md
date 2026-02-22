# Dashboards: Decision Support vs. Vanity Metrics

## 1. The Real Operational Problem

During a high-severity incident, the first five minutes define the trajectory of the outage. In those minutes, the responding engineer transitions from sleep—or deep focus—into a high-stress, cognitively demanding environment. Their immediate need is to rapidly build an accurate mental model of a degrading system.

The problem is that most organizations give these engineers the wrong tools. They provide dashboards designed for peacetime exploration, not wartime decision support.

When an engineer opens a dashboard during a Sev-1, they aren't looking for interesting trends, capacity planning insights, or business intelligence. They are looking for answers to three specific questions:

1. Is the system actually broken?
2. Where is the boundary of the failure?
3. What is the most likely cause?

Confronted with a wall of 150 disorganized graphs displaying raw infrastructure metrics, the engineer experiences severe cognitive overload. They have to visually parse the data, filter out noise, remember baselines, and manually correlate spikes across dozens of panels. This wastes the most critical minutes of the response.

## 2. Common Incorrect Approaches

Most dashboards fail because they're treated as data dumps rather than engineered interfaces.

**The "Kitchen Sink" Dashboard**
Teams often build a single massive dashboard for a service and endlessly append new panels whenever a new metric is instrumented. They cram CPU, memory, thread counts, garbage collection pauses, disk I/O, cache hit rates, queue depths, and business KPIs onto the same screen. There's no hierarchy or narrative flow.

**Mixing Peacetime and Wartime Contexts**
Dashboards frequently mix operational state (e.g., API Error Rate) with business reporting (e.g., Daily Active Users). While both matter, they serve entirely different purposes. A drop in active users might be a marketing problem; a spike in API errors is an operational emergency. Mixing them confuses the responder and dilutes the signal.

**Lack of Context and Baselines**
A graph showing a queue depth of 5,000 means nothing without context. Is 5,000 normal for this time of day? Is the queue draining or growing? Does the system fall over at 10,000 or 50,000? Displaying raw numbers without thresholds, historical baselines, or visual indicators of "good" vs. "bad" forces the human brain to supply missing context from memory. Under stress, memory is unreliable.

## 3. Operational Consequences

Poor dashboard design prolongs outages and increases human error.

When an engineer can't quickly deduce the state of the system, they resort to "dashboard hunting"—frantically clicking through tabs, looking for a graph that seems wrong. This drastically increases Mean Time to Identify (MTTI).

Worse, noisy dashboards lead to false correlations. In a distributed system, a core database failure will cause hundreds of downstream metrics to spike simultaneously. If the dashboard lacks visual hierarchy, the engineer might fixate on a secondary symptom (like a cache connection timeout) rather than the root cause. They waste precious time debugging a system that is merely suffering from a downstream dependency failure.

## 4. A Better Reliability-Focused Approach

We need to treat dashboard design as a strict discipline of UX and cognitive load management. A dashboard is an operational interface; it needs the same rigor as an API.

**Hierarchical Design (The USE/RED Methods)**
Dashboards must follow a top-down hierarchy that mirrors the troubleshooting process.

1. **Top Level (The Symptoms):** The very first row of panels must display RED metrics (Rate, Errors, Duration) for the service boundary. This instantly answers: "Is it broken?"
2. **Mid Level (The Dependencies):** The next rows should show the health (RED metrics) of critical downstream dependencies like databases, caches, or other microservices. This answers: "Where is the failure coming from?"
3. **Bottom Level (The Causes):** Only at the bottom—or ideally linked out to a separate drill-down dashboard—do we show USE metrics (Utilization, Saturation, Errors) for the infrastructure (CPU, memory, disk). This answers: "Why is it failing?"

**Contextual Data**
Every graph must be self-explanatory to a new engineer. If a metric has a known failure threshold, draw it as a bright red line on the graph. Use anomaly detection or week-over-week overlays to immediately visualize deviation from the norm, removing the need for the engineer to memorize what "normal" looks like.

## 5. Emphasizing Human Factors and Decision-Making

We have to design for visual processing under stress. During an adrenaline dump, peripheral vision narrows and complex cognitive reasoning degrades. The human visual cortex, however, remains exceptionally good at spotting colors and simple pattern deviations.

A wartime dashboard should be entirely green when healthy. If something is broken, it should be glaringly, unequivocally red. An engineer should be able to glance at a screen from across the room and instantly know if the system is suffering.

We don't build dashboards to prove how complex our systems are; we build them to make complex systems simple to operate. Every panel on an operational dashboard must earn its place by directly supporting a diagnostic decision. If a graph is just "interesting to know," delete it. During an outage, interesting is the enemy of actionable.
