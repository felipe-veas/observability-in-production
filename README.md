# Observability in Production

This repository documents the operational observability practices, principles, and philosophy required to run resilient, large-scale distributed systems. It serves as internal documentation for engineering teams to understand how we approach reliability, incident response, and signal design.

## Purpose

Observability isn't a tooling problem; it's an operational decision-making capability. This repository exists to shift our focus away from the mechanics of data collection (metrics, logs, traces) and toward the human factors of operating complex systems under duress.

Our goal isn't to have the most dashboards, the highest telemetry volume, or the most intricate alerting rules. Our goal is to ensure that when a system fails—and it will—the humans restoring service have the exact context required to make good decisions quickly, without drowning in noise.

This is **not** a monitoring setup guide. You won't find configuration snippets, YAML files, tool installation steps, or product comparisons here. Those details belong in our infrastructure-as-code repositories. The content here focuses strictly on operational risk, signal quality, and the socio-technical realities of on-call engineering.

## The Core Premise: Decision Support

Most organizations fail at observability because they treat it as an archiving exercise. They collect every possible metric and log line, dump it into a time-series database, and hope that answers will spontaneously emerge during an outage. They don't.

Data without context is a liability. An alert that doesn't require immediate human intervention is a distraction. A dashboard that takes five minutes of cognitive processing to understand is a broken tool. Observability exists exclusively to support decision-making during incidents. If a signal doesn't inform a decision, it has no operational value.

We view observability through the lens of human cognitive load. When an incident occurs at 3:00 AM, the responding engineer operates with diminished cognitive capacity, high stress, and limited time. The tools we provide them have to compensate for this state, not make it worse. Every piece of telemetry we emit must be justified by its utility in answering the fundamental incident response questions: "Is it broken?", "Where is it broken?", and "Why is it broken?"

## Target Audience

This documentation is written for:

- **Senior Engineers** designing systems that must fail gracefully and surface degradation clearly to the humans operating them.
- **Site Reliability Engineers (SREs)** responsible for defining acceptable reliability boundaries, enforcing signal quality, and protecting the sleep and sanity of the engineering org.
- **Platform Teams** building the paved paths and internal developer platforms that product teams rely on for default telemetry and operational standards.
- **Engineering Managers** responsible for the sustainability of on-call rotations, the operational health of their teams, and balancing feature velocity with systemic reliability.

## Structure of the Repository

To understand our operational posture, engineers should internalize the following documents. Each one addresses a specific facet of system operability and human response:

- [**philosophy.md**](./philosophy.md): Explores the fundamental difference between visibility and operability. Details why most monitoring systems fail when they are needed most and why hoarding metrics rarely correlates with higher reliability.
- [**symptoms-vs-causes.md**](./symptoms-vs-causes.md): Explains the critical distinction between user-visible symptoms and underlying causes, and why we strictly refuse to page on infrastructure state alone.
- [**alert-fatigue.md**](./alert-fatigue.md): Details the physiological and organizational damage caused by alert noise, and how the normalization of deviance turns severe warnings into background radiation.
- [**slo-alerting.md**](./slo-alerting.md): Outlines how Service Level Objectives (SLOs) and burn rate alerting force a mathematical alignment between operational action and actual user pain, removing emotion from incident declarations.
- [**dashboards.md**](./dashboards.md): Defines the difference between peacetime reporting and wartime decision support, establishing strict standards for cognitive load management and visual hierarchy.
- [**oncall-experience.md**](./oncall-experience.md): Maps the raw reality of incident response—the adrenaline dump, the fog of war—and how we must design systems to support the responder in high-stress moments.

## Engineering Judgment and Operational Maturity

The principles outlined here aren't arbitrary rules drawn from a vendor's whitepaper; they are written in the scars of past outages. They are designed to protect our most valuable and fragile operational asset: human attention.

We optimize for clarity over completeness. We aggressively delete alerts that cry wolf, knowing a noisy pager is more dangerous than a silent one. We build dashboards that answer specific questions rather than displaying data just because we have it. We treat the on-call engineer's cognitive capacity as a strict, non-negotiable upper bound on our operational complexity.

Read these documents to understand the *why* behind our operational standards. Reliability is a byproduct of engineering discipline, and that discipline starts with how we choose to observe, interpret, and react to system behavior in production. Everything else is just data.
