# The On-Call Experience: Chaos, Clarity, and Resolution

## 1. The Real Operational Problem

The industry routinely commits a fundamental error in how it approaches system reliability: treating incident response as a purely technical problem. We obsess over architecture, redundancy, failover mechanisms, and MTTR metrics. We completely ignore the biological reality of the human being executing the recovery.

The problem is the assumption that an engineer at 3:00 AM, abruptly awakened from deep sleep by a pager, possesses the same cognitive capacity and analytical rigor as they do sitting at their desk at 10:00 AM with a cup of coffee. They don't.

When the pager fires, the responder is thrust into the fog of war. They operate with incomplete information, face a rapidly degrading system, and feel the immense pressure of active customer impact. If your observability stack and operational processes aren't explicitly designed to support a human in this compromised state, you are planning to fail.

## 2. Common Incorrect Approaches

Organizations routinely set their on-call engineers up for failure through negligent operational practices:

**The "Deep End" Approach**
New engineers are often put on the primary rotation as a "learning experience," armed with little more than a wiki link and the expectation that they'll figure it out. This isn't training; it's a hazing ritual that guarantees prolonged outages and traumatized personnel.

**Useless Runbooks**
Many teams demand that every alert have an attached runbook. But because alerts are often cause-based (e.g., "High CPU"), the runbook inevitably devolves into generic advice: "Check the dashboard. If it looks bad, restart the pod. If that doesn't work, escalate." A runbook that doesn't provide deterministic, diagnostic steps is an insult to the responder's time.

**Alerting as a Substitution for Automation**
If the mitigation for a specific alert is always the exact same sequence of manual steps—clearing a cache, failing over a primary database, restarting a stuck worker—paging a human is an architectural failure. We're using the human nervous system as a crutch for incomplete automation.

## 3. Operational Consequences

Ignoring the human element of incident response has severe consequences for both the business and engineering culture.

During an incident, cognitive overload leads directly to poor decision-making. Overwhelmed by a flood of alerts and complex dashboards, the responder experiences tunnel vision. They might fixate on a single anomalous metric entirely unrelated to the root cause, ignoring the actual failure plane.

Panic leads to dangerous mitigation attempts. Engineers might execute destructive commands, drop critical tables, or restart healthy infrastructure in a desperate bid to make the graphs go green. This often transforms a minor degradation into a catastrophic total outage.

Culturally, an unmanaged, highly stressful on-call rotation destroys retention. High-performing engineers will simply refuse to work in an environment where they are routinely subjected to unmitigated chaos without organizational support.

## 4. A Better Reliability-Focused Approach

We have to architect the entire on-call experience—from the first alert to the post-incident review—around the limitations and needs of the human responder.

**Engineering the Pager Experience**
The first five minutes after the pager fires must be rigorously structured. The alert payload itself has to answer the immediate questions:

- **What is broken?** (The specific user-facing symptom)
- **How bad is it?** (The severity and scope of the impact)
- **Where do I look first?** (A direct link to the single, hierarchical dashboard that visualizes the symptom)

We don't page engineers to execute runbooks; we automate runbooks and page engineers when the automation fails or encounters a novel situation requiring human judgment.

**Diagnostic Runbooks**
Runbooks must shift from instruction manuals to diagnostic aids. Instead of telling the engineer what to *do*, the runbook should tell them what to *check* to prove or disprove a hypothesis. "Run this query to verify if the queue is backing up due to a poison pill message. If yes, follow mitigation A. If no, proceed to the database lock check."

## 5. Emphasizing Human Factors and Decision-Making

We must design for the adrenaline dump.

When an engineer is paged for a Sev-1, their body enters a fight-or-flight response. Heart rate elevates, and the ability to process complex, multi-variable logic sharply declines. The observability stack has to act as a cognitive prosthetic. It must present information so clearly, and with such unambiguous visual hierarchy, that the engineer doesn't have to *think* to understand system state; they merely have to *look*.

We also have to foster a culture of profound psychological safety during incident response. An engineer must know that if they make a good-faith decision based on the telemetry available to them, and that decision ultimately makes the outage worse, they won't be blamed or punished. The failure lies in the system that allowed a human to make a dangerous mistake, not in the human who made it.

On-call is not a technical function; it is a human endurance event. Our job as senior engineers and SREs is to build the scaffolding that ensures our colleagues can survive the event, resolve the crisis, and go back to sleep with their confidence and sanity intact.
