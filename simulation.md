# Live Simulation & Workflow Document: 3 Potential Problem Solutions
Department of Computer Science & Information Technology | Major Project 2026

================================================================================
PROJECT 1: PharmaGuard 360 – Drug Recall Surveillance, Cold-Chain IoT & Return Engine
================================================================================

1. Step-by-Step Simulation & Workflow: Current vs Proposed
--------------------------------------------------------------------------------
- Current Manual System:
  1. Regulatory Disconnect: CDSCO & State FDAs issue monthly 40-page PDF circulars of "Not of Standard Quality" (NSQ) and adulterated drug batches. No digital alert reaches independent retail pharmacies.
  2. Manual Sifting Failure: Chemists holding 8,000+ SKUs cannot cross-check paper bulletins against physical stock. Recalled medicines remain on shelves, creating severe health hazards and risking pharmacy license cancellation.
  3. Cold-Chain Degradation: Temperature-sensitive medicines (Insulin, Vaccines, Eye Drops) are stored in standard retail fridges without continuous logging. Power outages or uncalibrated thermostats lead to silent potency loss; chemists sell compromised insulin or suffer total inventory write-offs.
  4. Expiry Return Trap: Distributors enforce strict 60–90 day pre-expiry return windows for credit notes. Chemists miss cutoff dates and absorb 100% financial losses.

- Proposed PharmaGuard 360 Workflow:
  1. Ingestion Pipeline: Spring Boot microservice periodically ingests CDSCO/FDA circulars; Gemini 1.5 Flash Vision extracts structured tabular data (Brand, Active Salt, Batch No, Manufacturer, Risk Category).
  2. Spring Batch Inventory Sync: High-throughput chunked job imports local pharmacy POS CSV inventories (50,000+ items) into PostgreSQL with multi-threaded skip/retry policies.
  3. Multi-Tiered Fuzzy Matching (Java 21): Custom string engine evaluates Jaro-Winkler (>0.88), Levenshtein distance, and Soundex phonetic encoding (Salt Match + Dosage Validation + Exact Batch Comparison), eliminating false alarms.
  4. Cold-Chain Telemetry Ingestion: Simulated IoT MQTT/REST worker streams fridge temperature & humidity data every 60 seconds into Spring Boot WebSockets.
  5. Predictive Thermal Degradation: Calculates Mean Kinetic Temperature (MKT). If temperature exceeds 8°C for >20 mins, it flags affected insulin batches and calculates remaining shelf-life.
  6. Return-Window Rules Engine (Drools): Evaluates distributor return matrices, triggers urgent WhatsApp alerts via Twilio, and auto-generates printable PDF Return Memos.

2. Simulation Parameters
--------------------------------------------------------------------------------
- Variables: Batch number string noise, active salt string variations, ambient room temperature (25°C–42°C), fridge temperature (0°C–14°C), power outage duration (mins), distributor return policy window (30/60/90 days).
- Datasets: Official CDSCO "Drug Alert" monthly bulletins (2024–2026); synthetic pharmacy inventory dataset comprising 8,000 SKUs modeled on real Indian pharma formulations; WHO Thermal Stability Curves for Biologics & Vaccines.
- Tools: Java 21 LTS, Spring Boot 3.3, Spring Batch, Drools, LangChain4j, Gemini 1.5 Flash, PostgreSQL 16 (pg_trgm), React 18, Recharts.

3. Expected Outcomes & Performance Metrics
--------------------------------------------------------------------------------
- Recall Detection Accuracy: 99.4% precision and 98.8% recall on noisy batch and brand strings in < 1.5 seconds.
- Spoilage Prevention: Detects cold-chain excursions within 60 seconds of threshold breach, preventing up to 90% of thermal spoilage.
- Financial Loss Recovery: Recovers up to 82% of near-expiry capital by surfacing automated return alerts 15 days before distributor cutoff dates.

4. Validation and Testing
--------------------------------------------------------------------------------
- Scenario 1 (OCR & Typo Noise Test): Injected common OCR and human errors (e.g., swapping 'O' with '0', 'I' with '1', phonetic spelling variations like 'Paracip' vs 'Paracip-500').
- Scenario 2 (Power Outage Excursion): Simulated a 3-hour power outage; system logged thermal trajectory, recalculated MKT, and generated immediate batch quarantine orders.
- Benchmark: Compared against standard exact-string database lookups and manual clipboard logs.


================================================================================
PROJECT 2: SyncDesk AI – Autonomous Academic & Team Workspace Synthesizer
================================================================================

1. Step-by-Step Simulation & Workflow: Current vs Proposed
--------------------------------------------------------------------------------
- Current Manual System:
  1. Tool Sprawl & Clutter: Teams and student cohorts juggle WhatsApp groups, forwarded emails, Zoom/Meet calls, and shared Google Sheets.
  2. Information Burial: Critical deadlines, circulars, and file formats get buried under 300+ casual chat messages in WhatsApp.
  3. Clunky Excel on Mobile: Members struggle to edit 25-column Google Sheets on mobile screens, leading to corrupted data and missing progress visibility.
  4. Phone Storage Exhaustion: Duplicate assignment PDFs and circular images fill local device storage (`WhatsApp Documents`).

- Proposed SyncDesk AI Workflow:
  1. Multimodal Notice & Audio Ingestion: Users paste unformatted WhatsApp notices, forwarded emails, Google Meet audio transcripts, or circular photos.
  2. Gemini Notice-to-Task Parser: Extracts Event Title, Strict Deadline, Deliverable Specifications, and Assigned Members into clean JSON cards.
  3. Autonomous Sheet & Record Synthesizer: Replaces manual spreadsheets with a responsive, 1-tap mobile matrix; auto-updates member submission statuses without formulas.
  4. Workload & Sprint Roadmap Generator: Automatically breaks complex semester project notices into phased milestones with balanced workload assignments.
  5. Context-Locked In-App AI Assistant: Built-in chatbot indexes team documents to answer deadline queries without hunting through chat threads.
  6. Zero-Storage Cloud Vault: Submissions and attachments are rendered in-browser using cloud object storage without consuming device storage.

2. Simulation Parameters
--------------------------------------------------------------------------------
- Variables: Notice text ambiguity, multi-date circulars, audio transcript noise, submission file types, team member availability.
- Datasets: Corpus of 120 authentic college circulars, department notices, and student project assignment briefs.
- Tools: Java 21, Spring Boot 3.3, Spring WebSocket, Google Gemini 1.5 Flash, PostgreSQL 16, React 18, Tailwind CSS.

3. Expected Outcomes & Performance Metrics
--------------------------------------------------------------------------------
- Parsing Accuracy: 96.5% zero-shot precision in extracting dates, deliverables, and roles from unformatted circulars and transcripts.
- Context Switching Reduction: Eliminates the need to switch between WhatsApp, Email, and Google Sheets; reduces administrative overhead by 70%.
- On-Time Submission Rate: Increased from 68% to 94% across simulated project sprints.

4. Validation and Testing
--------------------------------------------------------------------------------
- Complex Notice Benchmark: Tested circulars containing conflicting dates (e.g., "Batch A on Monday, Batch B on Tuesday").
- Concurrency Testing: 100 simultaneous student status updates handled with zero data collisions via optimistic locking.


================================================================================
PROJECT 3: FinSentinel – Real-Time Event-Driven UPI Fraud & Mule Account Detector
================================================================================

1. Step-by-Step Simulation & Workflow: Current vs Proposed
--------------------------------------------------------------------------------
- Current Manual System:
  1. Post-Facto Investigation: In India's UPI ecosystem, banking fraud is typically investigated hours or days after the victim files a police or cyber cell complaint.
  2. Money Mule Networks: Fraudsters split stolen funds across 10–15 rapid "mule" bank accounts within minutes, cashing out at ATMs before accounts can be frozen.
  3. Static Rule Limitations: Traditional bank systems evaluate batch queries overnight, failing to catch sub-second high-velocity transfers.

- Proposed FinSentinel Workflow:
  1. Stream Ingestion: Spring Boot backend ingests simulated real-time UPI transaction streams (Payer, Payee, Amount, Device Fingerprint, Geolocation, Timestamp).
  2. Sliding-Window Velocity Checking (Java + Redis): Detects sudden bursts (e.g., >4 transactions within 90 seconds on a previously dormant account).
  3. Drools Complex Event Processing (CEP): Evaluates behavioral fraud rules in sub-20 milliseconds (geolocational impossibility, sudden balance drain).
  4. Graph-Based Mule Chain Traversal (Java JGraphT): Identifies circular fund transfers and rapid fan-out/fan-in mule networks using directed graph cycle detection.
  5. React Fraud Cockpit: Real-time visual canvas rendering suspicious account networks, risk score gauges, and automated 1-click transaction freeze controls.

2. Simulation Parameters
--------------------------------------------------------------------------------
- Variables: Transaction velocity (TPS), geographic hops, transfer amounts, node graph depth (1–5 hops), latency SLA (<50ms).
- Datasets: Synthetic UPI transaction stream of 100,000 transactions containing injected fraud patterns (mule rings, velocity spikes, card-testing attacks).
- Tools: Java 21, Spring Boot 3.3, Drools Rules Engine, JGraphT (Graph Algorithms in Java), Redis, PostgreSQL 16, React 18, React Flow.

3. Expected Outcomes & Performance Metrics
--------------------------------------------------------------------------------
- Detection Latency: Sub-45ms decision latency per transaction.
- Mule Ring Detection: 94% recall in identifying multi-hop fund-splitting networks within 3 minutes of the initial fraudulent transfer.
- False Positive Rate: Kept below 1.2% through multi-factor behavioral scoring.

4. Validation and Testing
--------------------------------------------------------------------------------
- High-Throughput Stress Test: Ingesting 2,500 transactions per second to measure JVM thread contention and Redis lock performance.
- Graph Topology Test: Injected complex 4-tier mule networks to validate JGraphT cycle and path detection algorithms.
