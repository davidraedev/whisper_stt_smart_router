# SpeechWeave: Local-First STT & AI Inference Router

A scalable, edge-compute routing engine designed for Absolute Data Sovereignty. SpeechWeave automates asynchronous audio transcription, summarization, and compliance flagging utilizing a self-hosted GPU architecture for both Speech-to-Text (Whisper) and Large Language Model (LLM) inference.

> **Note:** The core source code for this architecture is proprietary and under NDA. This repository serves as a high-level architectural overview of the system design and routing logic.

## The Architecture (Zero-Leak Topology)

Designed specifically for strict compliance environments (Legal, Healthcare, Gov) where transmitting raw audio or sensitive text to third-party APIs (like OpenAI) violates data privacy regulations. 

The Orchestrator dynamically routes heavy processing jobs to isolated, self-hosted GPU worker nodes, ensuring that 100% of the data remains within the client's secure perimeter.

```text
[ Client Application ]
        │ (REST API / HTTPS - Audio Payload)
        ▼
+---------------------------------------------------+
|               ORCHESTRATOR NODE                   |
|  (Node.js / Redis Job Queue)                      |
|  - Authenticates & validates incoming payloads    |
|  - Routes based on GPU spot-pricing & SLA speeds  |
|  - Secured via Zero-Trust Mesh (Tailscale)        |
+---------------------------------------------------+
        │                                   │
        ▼                                   ▼
[ GPU Worker Node A ]               [ GPU Worker Node B ]
(Self-Hosted High-Compute)          (Self-Hosted High-Compute)
- Whisper AI (STT)                  - Whisper AI (STT)
- Local LLM (Llama 3 / Phi-4)     - Local LLM (Llama 3 / Phi-4)
- Contextual Summarization          - Contextual Summarization
        │                                   │
        +-------------------+---------------+
                            │ 
                            │ (Sanitized, Summarized JSON Payloads)
                            ▼
               [ Client Webhook / Database ]
               (Private VPC / Secure Subnet)
```

## Core Technology Stack

* **Orchestration:** Node.js, Express, Redis (Job Queuing)
* **AI / ML Pipelines:** OpenAI Whisper (Local STT), vLLM (Local LLM Inference)
* **Networking & Security:** Tailscale (Zero-Trust Mesh Network)
* **Infrastructure:** Dedicated Cloud GPU Instances (VPC) / Private Cloud VMs

## System Impact & Market Fit

1. **Absolute Data Sovereignty:** By running STT and LLM inference entirely within dedicated, private-cloud GPU instances, the system achieves "Zero-Leak" processing. It bypasses multi-tenant SaaS APIs, satisfying strict enterprise data privacy policies, corporate NDAs, and supporting healthcare compliance frameworks like HIPAA.
2. **Dynamic Cost Optimization:** The orchestrator continuously evaluates GPU instance pricing and availability against client transcription speed priorities (SLAs). It intelligently routes non-urgent batch jobs to cheaper, lower-tier GPUs, while reserving high-performance compute for critical, real-time analysis.
3. **Decoupled Processing:** The Node.js orchestrator isolates the heavy compute environment from the ingestion API, allowing the worker nodes to scale horizontally as hardware is provisioned, without system downtime.
