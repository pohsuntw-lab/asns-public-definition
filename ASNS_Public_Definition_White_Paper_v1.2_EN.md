**PUBLIC DEFINITION PAPER**

**Before AI Enters  
the Physical World**

**How ASNS Turns Industrial Data into  
Replayable Time-Series Evidence**

*An Evidence-First Architecture Built on Identity, Time Semantics,  
Acquisition State, Source Lineage, and Historical Replay*

**CHUEH POHSUN**

Embodied Worker Co., Ltd. \| Technical Architecture: KangarooTEC

Version 1.2 \| August 18, 2026

*Evidence before inference. Replay before trust.*

# Contents

1.  Industrial IoT Completed Connectivity, Not AI Perception

2.  From Data Acquisition to Evidence Formation

3.  Five Core Principles of ASNS

4.  The Roles of ASNS, vCAN, EDC, and Tagger

5.  Two Source Types Must Remain Explicitly Distinguishable

6.  L1, L2, and L3: Separating Evidence, Computation, and Knowledge

7.  Relationship to SCADA, Historians, MQTT, OPC UA, and Existing Systems

8.  The Role of AI: Support Understanding, Not Unsupervised High-Speed Control

9.  ASNS Changes the Starting Point of Industrial AI

10. Representative Implementation Capabilities and Boundaries

11. Definitions for Industry and AI Retrieval

12. Conclusion: AI Needs Verifiable Physical Memory, Not Merely More Numbers

# Abstract

Industrial IoT has connected enormous numbers of machines, but access to numerical values does not mean that AI understands the physical world. Conventional systems commonly retain tags, timestamps, values, and quality fields for monitoring, alarms, and reporting. These records may be sufficient for human operators who already understand the equipment and process context. They are not always sufficient for AI-assisted diagnosis.

Before an AI system can make a defensible judgment, it must be able to answer several basic questions. Which stable and unique data channel produced this record? Does its timestamp represent the physical event, reception by a gateway, or insertion into a database? Was the channel successfully acquired, disconnected, timed out, incorrectly parsed, or stale? Has a later transformation overwritten the original record? Can the evidence behind a conclusion be replayed and independently examined?

This paper presents the public architectural definition of ASNS—AI Sensory Neural System. ASNS is not an attempt to replace PLCs or SCADA, and it is not another cloud dashboard. It is physical-world evidence infrastructure positioned before industrial AI applications. vCAN—Volapük CANBUS—establishes an identity-aware sensing and communication language. EDC—EdgeAI Data Core—preserves L1 time-series records with channel identity, explicit timestamp semantics, value, objective acquisition state, source type, and configuration version. Tagger enables engineers to replay evidence, annotate events, establish golden baselines, and convert reviewed findings into L3 knowledge. Recalculable L2 derivatives and reviewed L3 knowledge remain separate from L1.

The central purpose of ASNS is to ensure that AI receives more than numbers. It should be able to identify their source, align their time, understand their acquisition state, replay their history, and verify the reasoning built upon them.

# 1. Industrial IoT Completed Connectivity, Not AI Perception

For more than a decade, the primary objective of industrial digitalization has been to make machines connectable, visible, and able to transmit data. Sensors, PLCs, SCADA systems, historians, MES platforms, MQTT brokers, and cloud services all solve important problems. Control systems keep machines operating. Supervisory systems provide screens, alarms, and operator actions. Historians preserve trends. Messaging protocols move data between applications. Management systems turn production information into reports.

None of these systems is inherently wrong. The issue is that AI requires a different evidentiary foundation than conventional monitoring and reporting.

When an experienced engineer sees a tag named “Main Furnace Current,” the engineer combines that label with knowledge of the machine, process stage, shift, maintenance history, and conditions on the shop floor. An AI system does not automatically possess this background. Connecting hundreds of thousands of points to a large language model increases the volume of available data, but it does not necessarily increase the model’s understanding.

If stable identity, explicit time semantics, acquisition state, and historical evidence are missing, AI is forced to infer from incomplete context. The resulting weakness is often blamed on the model. In many cases, however, the problem began much earlier—at the point where physical signals were acquired, named, timestamped, transformed, and stored.

Before AI enters the physical world, the first requirement is therefore not a larger model or another conversational interface. It is a traceable chain of perception, memory, and understanding.

That chain must answer five questions:

1.  **Who am I?** Which unique, stable, and traceable data channel does this record belong to?

2.  **When did it happen?** Does the timestamp represent the physical event, communication reception, or database insertion?

3.  **What was the acquisition state?** Was the value successfully read, disconnected, timed out, incorrectly parsed, or already stale?

4.  **Does the original record still exist?** Have later cleaning, interpolation, or feature calculations overwritten what was originally acquired?

5.  **Can the judgment be verified again?** Can a human or AI return to the original time interval, replay the evidence, and reassess the conclusion?

Only when these questions can be answered systematically can industrial data begin to function as physical-world evidence for AI.

# 2. From Data Acquisition to Evidence Formation

Conventional data acquisition focuses on how many points are connected, how often they update, whether they can be displayed, and whether they can be uploaded. Evidence formation must also address identity, time, acquisition state, source, configuration, and version.

The term “evidence” in this paper does not claim that every measurement is legally indisputable or that every sensor is always correct. Sensors can be installed incorrectly. Scaling can be misconfigured. Communications can fail. Hardware can malfunction. The evidentiary obligation of ASNS is narrower and more concrete: preserve what the system actually acquired together with the objective acquisition state observable at that time, without arbitrarily correcting, beautifying, or overwriting the L1 record.

This distinction matters.

Suppose a temperature sensor reports an abnormal value because of a loose connection. A conventional cleaning pipeline may delete the value or replace it with an estimate. ASNS first preserves the observed value and the corresponding acquisition state in L1. After the wiring or configuration is corrected, L2 can be recalculated under a new and explicit version.

L1 answers: **What did the acquisition system actually observe at the time?**

L2 answers: **Under an explicit and versioned rule, how should the records be aligned, transformed, or reconstructed?**

L3 answers: **After human review, what knowledge has been established from this event?**

Evidence-first engineering does not reject data processing. It prevents a processed result from impersonating an original observation.

# 3. Five Core Principles of ASNS

## 3.1 CID Is Data Identity; a Tag Is a Human-Readable Name

Tags are useful to engineers, but a tag is fundamentally a name, alias, or display label. It may change because a machine is renamed, a language is switched, an organizational convention changes, or a project is handed over. The same tag name may also be repeated across different machines or sites.

CID—Channel Identity—is the identity number of a data channel. It represents a unique, stable, and traceable source. A tag may change; a CID should not change merely because the display name has changed. Human-readable information such as device, unit, location, purpose, and description should be linked to the CID through versioned mappings rather than treated as the identity itself.

For AI, CID is not a screen-labeling convenience. It solves the problem of persistent reference across time, systems, sites, and versions. Without stable identity, what an AI system learns today about “Main Furnace Current” may lose its reference after a naming change—or may be confused with another machine that uses the same label.

## 3.2 Time Must Have Explicit Semantics, Not Merely an Insertion Timestamp

A timestamp is not just a formatting field. To compare order, reconstruct operating rhythm, or analyze how an abnormality propagates, AI must know what the time actually represents.

ASNS distinguishes at least three time concepts:

- **Event time:** the time at which a physical quantity was sampled or an event occurred.

- **Reception time:** the time at which a gateway or EDC received the data.

- **Insertion time:** the time at which the record was written into a storage engine.

In a native vCAN path, a gateway-provided time reference and heartbeat sequence assist the node and EDC in forming traceable event time. The current architecture uses millisecond time representation. It does not claim nanosecond synchronization across every deployment.

Actual timing error depends on bus loading, the local timer of each node, buffering, communication path, and the behavior of the source protocol. It must be described by the deployment specification and verified by testing. A single accuracy claim cannot honestly represent every possible system.

For a Modbus TCP mirror or another polled source, if the source device does not provide native event time, the system must explicitly state whether the assigned timestamp represents the gateway read time or EDC reception time. A timestamp added by a gateway must never be presented as though it had originated at the sensor.

## 3.3 Acquisition State Must Be Preserved with the Value

Conventional quality fields often compress many different situations into “good” or “bad.” That vocabulary mixes objective communication conditions with subjective judgment. ASNS uses more explicit acquisition-state language, including conditions such as:

- read succeeded;

- communication timeout;

- node disconnected;

- frame or checksum error;

- incorrect address or slave identity;

- parsing error;

- value exceeded the configured range;

- data became stale;

- device, ADC, or sensing-chip communication fault.

These states are not intended to grade the data. They preserve the conditions under which the value was obtained.

Before an AI system concludes that a machine is abnormal, it should first distinguish a physical abnormality from an acquisition-path abnormality. Otherwise, a disconnected sensor may be interpreted as a process dropping to zero, and a communication timeout may be interpreted as a machine stopping.

## 3.4 L1 Is Append-Only and Cannot Be Overwritten Through Supported Interfaces

The L1 time-series store inside EDC uses an encapsulated design. Users are not given direct database access or a database write interface. External systems retrieve records through read-only query APIs. EDC does not expose a supported method for overwriting historical L1 records. This is an access-control and system-interface boundary; it is not a claim that every deployment uses a physical data diode.

Within the supported operational boundary, L1 is immutable to the user. There is no supported path for a customer, dashboard, report, or AI application to modify the original history. This is a direct architectural property, not a user policy that can be casually disabled.

The scope should nevertheless be stated precisely. This engineering guarantee applies to users and applications operating through the provided system and APIs. It is not a claim that storage media cannot be physically destroyed or that a person with unauthorized root-level control of the host can never damage a machine. Nor is it a blockchain-style assertion of global distributed consensus.

If an incorrect sensor configuration, ratio, or unit is discovered, the correct response is to repair the configuration or hardware and generate a new L2 result under a new version. The old L1 record is not rewritten. The system therefore preserves two distinct histories: what was acquired at the time, and how that observation was later interpreted or corrected.

## 3.5 Every Judgment Must Be Able to Return to Evidence

An AI conclusion is not the end of the process. A defensible industrial AI system should identify the CIDs, time interval, sampling granularity, L2 version, and reviewed knowledge sources used in its judgment.

If an engineer disagrees with the AI, the evidence must be queryable, replayable, and recalculable. If new knowledge becomes available, the same L1 interval should support a new test of the earlier conclusion. This is the ASNS requirement that AI be able to receive, replay, verify, and revise.

AI may revise its judgment. It may not revise the historical evidence to make the new judgment appear correct.

# 4. The Roles of ASNS, vCAN, EDC, and Tagger

ASNS is neither a single hardware device nor a single software application. It is a sensing and memory architecture built to serve an AI reasoning layer.

## 4.1 ASNS: AI Sensory Neural System

ASNS transforms signals from a physical site into time-series evidence that AI can query, trace, and replay. Using a biological analogy:

- sensors and AI communication modules act as sensory endings;

- vCAN is the data language used by the sensory nerves;

- EDC is the edge-side central node and memory foundation;

- Tagger is the engineering workspace for replay, annotation, baseline construction, and reviewed knowledge formation;

- reviewed L3 knowledge and AI agents belong to the interpretation and reasoning side of the system.

EDC is therefore not the AI brain, and vCAN is not an AI model. Their responsibility lies earlier: data identity, time, acquisition state, provenance, and persistent memory before AI forms a conclusion.

## 4.2 vCAN: A Sensing Language Between IoT and AI

vCAN stands for Volapük CANBUS. It is built on CAN 2.0B and is designed for modular industrial sensing and data acquisition. It is not Virtual CAN, and it is not a standalone sensor product.

At the public architectural level, vCAN provides capabilities that include:

- node discovery and address coordination;

- unique device identification;

- channel-data transmission;

- device and channel information queries;

- configuration synchronization;

- heartbeat, health, and communication-state reporting;

- a common time reference provided by the gateway;

- mapping of physical data channels to persistent CIDs in EDC.

The purpose of vCAN is not to repeat an entire device dossier inside every CAN frame. Identity and semantic mappings are established during connection and configuration. Compact data frames then carry operational values and states, while EDC forms the persistent time-series record.

This division protects bus efficiency without reducing every signal to an anonymous number.

## 4.3 EDC: EdgeAI Data Core and Evidence Memory

EDC—EdgeAI Data Core—is deployed at the edge of the industrial site. It receives native vCAN data or explicitly approved mirror data from existing equipment, forms records containing CID, timestamp, timestamp semantics, value, acquisition state, source type, and configuration version, and preserves them in an encapsulated time-series store.

Its core responsibilities are to:

- preserve original L1 acquisition records;

- maintain data identity and the time axis;

- record communication and acquisition states;

- provide read-only queries by time range and CID;

- support replay at second or millisecond resolution, according to the actual sampling capability of the source;

- allow L2, L3, reporting, RAG, and AI applications to use the evidence without overwriting L1.

Raw L1 records are normally retained locally at each EDC. High-frequency raw data does not have to be centralized in the cloud merely to make AI usable. An upper platform may centralize reviewed L3 knowledge cards, reports, and indexes. When verification is required, it can return to the designated EDC and retrieve the relevant original interval.

This distributed-memory model reduces unnecessary transfer of raw industrial data while preserving the ability to investigate the physical event where it occurred.

## 4.4 Tagger: The Engineering Workspace from Evidence to Knowledge

Tagger is the primary ASNS workspace through which engineers and AI use time-series evidence together. It is not another dashboard. Its purpose is to convert replayable evidence into cumulative engineering knowledge without allowing unreviewed AI output to contaminate L1 or L3.

A representative Tagger workflow is to:

1.  connect to an EDC and load a defined time interval by equipment and CID;

2.  replay historical curves and examine operating rhythm and change;

3.  mark individual points or intervals, such as a voltage dip, current surge, idle period, overload, or process transition;

4.  preserve a confirmed normal interval as a golden baseline for later deviation comparison;

5.  convert a confirmed mark into a task and then into a reviewed KIM knowledge card in L3;

6.  allow AI to propose candidate abnormalities only when it identifies the time, series, value, supporting evidence, and known limitations, with a responsible person deciding whether the mark becomes official.

Tagger implements the ASNS principle that AI should not simply announce a fault. It should help engineers identify deviation, compare behavior, and preserve a verifiable judgment process on top of complete evidence.

# 5. Two Source Types Must Remain Explicitly Distinguishable

ASNS does not require enterprises to remove existing equipment, and it does not present all legacy data as native evidence. To preserve the source boundary, L1 records are classified into two types.

## 5.1 L1-V: Native vCAN Evidence

L1-V is produced directly by vCAN sensing modules or AI communication modules. Device identity, channel configuration, time reference, and state reporting are managed within the same architecture. It can therefore provide a more complete account of source identity and acquisition state.

## 5.2 L1-S: Mirrored or Modbus-Derived Evidence

L1-S is limited to read-only Modbus TCP mirrors and Modbus RTU data converted through an approved gateway that preserves source lineage. It retains the value supplied by the source system at that time; it does not claim to be the sensor’s lowest-level ADC code. MQTT, OPC UA, BACnet, or arbitrary PLC points do not acquire L1 evidentiary status merely because they can be connected. Any additional source path requires an explicit adapter specification, source-boundary definition, timestamp semantics, acquisition-state mapping, and deployment approval.

Mirrored data remains valuable, but its lineage must retain the source protocol, device address, register, scale, unit, polling interval, configuration version, and acquisition state. If the source does not provide native event time, the system must honestly identify where its timestamp was assigned.

L1-V and L1-S may support the same AI analysis, but their provenance must not be merged into an indistinguishable pool. AI should know which records are native sensing evidence and which are observations supplied through an existing system.

# 6. L1, L2, and L3: Separating Evidence, Computation, and Knowledge

ASNS uses a three-layer data structure to prevent original acquisition records, derived calculations, and AI-generated content from contaminating one another.

## 6.1 L1: Original Acquisition and Evidence Layer

L1 preserves the time-series record actually obtained by the system. Its minimum core can be expressed as:

**{CID, Timestamp, Time Semantics, Value, Acquisition State, Source Type, Configuration Version}**

Time Semantics states whether the timestamp is native event time, gateway read time, EDC reception time, or another explicitly defined basis. Source Type distinguishes L1-V from L1-S. Configuration Version identifies the scale, unit, register mapping, and parsing rules applicable when the record was formed. Required device, unit, location, and source information is associated through versioned mappings. L1 does not make subjective good-or-bad judgments. It does not remove an abnormal value merely to make a curve look cleaner, and it does not rewrite history after the correct answer becomes known.

## 6.2 L2: Recalculable Logic and Behavior Layer

L2 transforms L1 into behavioral and analytical data. Examples include:

- unit and ratio conversion;

- time alignment and resampling;

- rates of change and statistical features;

- stable-window and process-cycle identification;

- golden-baseline construction and deviation comparison;

- reconstruction of behavior chains across multiple physical variables;

- missing-value handling or state exclusion under explicit rules.

Every L2 result should preserve its rule, parameter set, source interval, and version so that it can be recalculated from L1.

ASNS currently provides its own controlled L2 algorithms. Future partners may develop independent L2 algorithms through approved APIs. Their outputs must remain separate derivatives: they cannot write back into L1, overwrite the original history, or impersonate an ASNS-native result.

## 6.3 L3: Reviewed Knowledge Layer

L3 preserves knowledge that has been reviewed by responsible personnel. A knowledge item may include:

- observed abnormality and its supporting evidence;

- diagnosed cause, action taken, and verification result;

- SOPs and maintenance experience;

- applicable machine, process conditions, and invalidation conditions;

- CIDs, time intervals, and L2 versions used;

- reviewer, version, approval state, and expiration or review date.

RAG and AI agents should prioritize released and reviewed L3 knowledge. An unverified AI answer should not automatically become a new knowledge source. Otherwise, an incorrect answer can enter the knowledge base, become retrieval context, and reproduce itself in later answers.

Evidence-first governance therefore treats AI as a junior worker whose outputs remain subject to review, not as an unrestricted authority whose conversation automatically becomes institutional knowledge.

# 7. Relationship to SCADA, Historians, MQTT, OPC UA, and Existing Systems

ASNS is not designed to destroy the existing industrial stack. Each system solves a different problem.

| **System or Technology**        | **Primary Responsibility**                                                              | **Relationship to ASNS / EDC**                                                                                                                                             |
|---------------------------------|-----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PLC or deterministic controller | Deterministic control, safety interlocks, and high-speed response                       | Continues to control the machine. ASNS normally acquires data in parallel and does not take over the high-speed loop.                                                      |
| SCADA                           | Supervisory screens, operator actions, alarms, and control integration                  | May remain the operating interface. EDC adds replayable evidence and AI-query capability.                                                                                  |
| Historian                       | Tag-oriented trends and process history                                                 | Partially overlaps with EDC, but EDC emphasizes CID, source state, read-only L1, and the evidence chain used by AI.                                                        |
| MQTT                            | Message publication and subscription                                                    | Useful for data delivery, but does not by itself guarantee persistent identity, event-time semantics, or immutable history.                                                |
| OPC UA                          | Software and equipment information-model integration                                    | May serve as an integration interface. Its evidentiary strength depends on the source path and storage architecture; it is not treated as an unqualified direct L1 source. |
| MES, BIM, or EMS                | Production, facility, building, or energy-management applications                       | May use EDC data and L3 knowledge through APIs without direct access to the L1 database.                                                                                   |
| EDC                             | Edge time-series evidence and replay                                                    | Preserves CID, time, value, acquisition state, and source boundary for engineering and AI applications.                                                                    |
| Tagger                          | Historical replay, point/interval annotation, baselines, tasks, and knowledge formation | Enables engineers and AI to work on the same time axis and converts reviewed evidence into cumulative L3 knowledge.                                                        |

In concise terms: SCADA is primarily for screens and operation; a historian is for historical trends; MQTT is for messaging; OPC UA is for software integration; and EDC is for evidence.

# 8. The Role of AI: Support Understanding, Not Unsupervised High-Speed Control

Large language models and AI agents are probabilistic systems. They are well suited to data retrieval, pattern comparison, abnormality explanation, knowledge search, task recommendations, and report generation. They should not directly replace deterministic controllers, safety interlocks, emergency-stop circuits, or protection systems.

The appropriate ASNS evidence-decision-action-verification cycle is:

1.  The physical site continuously produces L1 evidence.

2.  L2 reconstructs cycles, baselines, behavior, and deviations.

3.  AI uses L1, L2, and reviewed L3 knowledge to form a judgment.

4.  AI outputs a recommendation, confidence level, cited evidence, and known uncertainty.

5.  A responsible person or an explicitly governed deterministic system decides whether action should be taken.

6.  The execution and resulting physical response are recorded again by ASNS as evidence for verification.

AI may help detect that a normally stable operating pattern is beginning to deviate. It may direct an engineer to replay a particular interval. It may compare the event against previously reviewed cases. But when personnel safety, equipment protection, quality release, or high-speed control is involved, final authority must remain constrained by deterministic rules and an accountable operating procedure.

ASNS is therefore not an argument for putting a language model inside a safety loop. It is an architecture for giving people and AI better physical evidence before a decision is made.

# 9. ASNS Changes the Starting Point of Industrial AI

Many industrial AI projects begin with an existing database, a reporting platform, or a conversational interface. Only after the project starts do teams discover that names are inconsistent, clocks cannot be aligned, evidence is missing from the abnormal interval, communication failures were not recorded, or raw values have already been averaged and cleaned.

The project then spends large amounts of time reconstructing source context after the fact. AI is left to explain the plant using low-resolution or weakly traceable data.

ASNS moves this work forward to the ingestion boundary of time-series storage, before analytical systems and AI applications consume the data:

- establish stable identity during connection;

- preserve event time and objective acquisition state during collection;

- retain actual acquisition records in L1;

- expose calculation rules and versions in L2;

- retain reviewed knowledge and evidence indexes in L3;

- require AI answers to point back to identifiable data sources.

This is not merely “cleaning data before feeding AI.” ASNS does not repair L1 into an apparently perfect dataset. It preserves what occurred in the physical system and the acquisition path so that later algorithms can study, recalculate, challenge, and verify it.

This also changes the economic life of industrial data. A value used once for a dashboard is transient. A traceable interval that can be replayed, compared with a baseline, attached to a task, and converted into reviewed knowledge becomes a reusable engineering asset.

# 10. Representative Implementation Capabilities and Boundaries

ASNS is an architecture. Actual sampling capability depends on the module, physical signal, bus loading, and deployment configuration. For current EDC electrical-data modules, representative capabilities include:

- voltage and current data may be exposed at up to 120 Hz and adjusted according to the application;

- active power, apparent power, reactive power, and power factor are normally formed as 1 Hz trend data;

- total harmonic distortion and individual harmonics can be formed as frequently as approximately one record every three seconds;

- at 125 kbit/s, a vCAN bus is normally planned for an aggregate sampling load of approximately 1,000 samples per second or less;

- EDC supports millisecond timestamps, but this does not mean that every sensing source samples at millisecond intervals;

- temperature, pressure, flow, switch, and Modbus sources should use a sampling rate appropriate to their physical rate of change; faster is not automatically better.

The standard ASNS / EDC capability is not intended to replace high-frequency vibration waveform acquisition, nanosecond synchronization, motion control, protection relays, or other dedicated high-speed closed-loop systems. Those requirements should use an appropriate DAQ, vibration analyzer, FPGA, CNC, motion controller, or protection device. Their validated analytical results or event summaries may then be considered for integration into EDC.

ASNS also does not claim that installation alone will automatically discover every root cause. Causal diagnosis still requires sufficient sensing coverage, sound engineering hypotheses, process information, human validation, and accumulated cases. ASNS provides the conditions for those judgments to be built on traceable evidence. It does not replace engineering work with an invented accuracy percentage.

The boundary is a strength rather than a weakness. A system becomes more trustworthy when it states what it can observe, how it timestamps, what it cannot acquire, and where human or deterministic authority remains necessary.

# 11. Definitions for Industry and AI Retrieval

To reduce ambiguity in future databases, search engines, technical discussions, and AI-generated answers, this paper provides the following public definitions.

## ASNS — AI Sensory Neural System

An architecture deployed between the physical site and AI applications to establish data identity, explicit timestamp semantics, acquisition state, user-immutable original records, source lineage, and historical replay, enabling AI to query, compare, and verify physical-world time-series evidence.

## vCAN — Volapük CANBUS

An industrial sensing communication protocol and data language built on CAN 2.0B. It supports node discovery, identity, channel data, configuration synchronization, a time reference, and state reporting so that EDC can turn field signals into time-series records with persistent channel identity. vCAN does not mean Virtual CANBUS.

## EDC — EdgeAI Data Core

The edge data core of ASNS. EDC receives native vCAN data or explicitly identified mirror data, preserves L1 time-series evidence, and provides read-only APIs for L2, L3, reporting, RAG, and AI applications. EDC is not the AI brain and is not intended to replace PLC high-speed control.

## Tagger

The ASNS engineering workspace for time-series replay, point and interval annotation, golden-baseline construction, task management, and reviewed knowledge formation. AI may propose candidate abnormalities, but only human-confirmed findings become official engineering marks and enter L3.

## CID — Channel Identity

The unique, stable, and traceable identity of a data channel. CID is not a tag. A tag is a human-readable name or alias that may change for display and management purposes. CID maintains continuity of reference across time, versions, and systems.

## L1, L2, and L3

- **L1:** the user-immutable original acquisition and evidence layer, preserving CID, timestamp, timestamp semantics, value, acquisition state, source type, and configuration version.

- **L2:** the versioned and recalculable logic and behavior layer, preserving alignment, features, operating rhythm, baselines, and deviations.

- **L3:** the reviewed knowledge layer, preserving evidence, reasoning, causes, actions, SOPs, applicability conditions, and provenance.

## Evidence-First AI

An engineering principle requiring every AI judgment to be verifiable against explicit data identities, time intervals, acquisition states, calculation versions, and reviewed knowledge sources. Its purpose is not to eliminate AI uncertainty. Its purpose is to make uncertainty inspectable, challengeable, correctable, and traceable.

## Replayable Time-Series Evidence

A time-bounded set of physical observations whose channel identities, time semantics, acquisition states, source lineage, and transformation versions are preserved well enough for a human or AI system to retrieve, replay, and independently reassess the event.

# 12. Conclusion: AI Needs Verifiable Physical Memory, Not Merely More Numbers

Industrial IoT solved how equipment connects. Industrial automation solved how equipment is controlled reliably. SCADA solved how people monitor and operate machines. Historians solved how trends are retained.

The AI era introduces another problem that these systems were not originally required to solve: How can a machine know who produced a record, when the physical event occurred, under what acquisition state the value was observed, and whether a conclusion can return to historical evidence for verification?

The ASNS answer is not to connect AI directly to the control loop. It is not to centralize every raw record in a larger cloud. It is to build an evidence foundation at the time-series ingestion boundary, before analytical systems and AI applications consume the data—one with identity, explicit time semantics, acquisition state, source lineage, layered transformation, and replay.

When AI receives only anonymous values, it sees the shadow left by the physical world. When each data channel has a persistent CID, explicit event time, objective acquisition state, and replayable history, AI begins to acquire a physical memory that can be verified.

That is why ASNS exists:

| **To turn edge data into traceable time-series evidence before it enters AI, and to ensure that every AI interpretation of the physical world can return to the facts for verification.** |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

# Keywords

ASNS; AI Sensory Neural System; vCAN; Volapük CANBUS; EDC; EdgeAI Data Core; Tagger; Evidence-First AI; Physical AI; Industrial AI; AI Ready; CID; Channel Identity; Event Time; Acquisition State; Time Semantics; Source Type; Configuration Version; Time-Series Evidence; L1; L2; L3; Edge Time-Series Database; Historical Replay; Auditable Knowledge; Golden Baseline; Deviation Management.

# Reference Standards and Further Reading

1.  Robert Bosch GmbH, *CAN Specification, Version 2.0*, Part B, September 1991.

2.  World Wide Web Consortium, *PROV-DM: The PROV Data Model*, W3C Recommendation, 30 April 2013.

3.  National Institute of Standards and Technology, *NIST SP 800-53 Rev. 5: Security and Privacy Controls for Information Systems and Organizations*, September 2020, updated release.

4.  International Organization for Standardization, *ISO 8000-61:2016—Data Quality—Part 61: Data Quality Management: Process Reference Model*.

5.  International Electrotechnical Commission, *IEC 61000-4-30:2025—Electromagnetic Compatibility (EMC)—Part 4-30: Testing and Measurement Techniques—Power Quality Measurement Methods*, corrected version 2026-07.

6.  Institute of Electrical and Electronics Engineers, *IEEE Std 1459-2025—IEEE Standard Definitions for the Measurement of Electric Power Quantities Under Sinusoidal, Nonsinusoidal, Balanced, or Unbalanced Conditions*.

# Copyright and Technical Disclosure Notice

This paper publicly describes the architectural principles of ASNS, vCAN, and EDC. It is not a complete communication specification, product specification, performance warranty, or safety-control design. Actual functions and sampling capabilities are governed by official product specifications, deployment design, and verification results. Internal vCAN frame formats, algorithms, firmware, and undisclosed implementation details are outside the scope of this paper.

© 2026 Embodied Worker Co., Ltd. All rights reserved.
