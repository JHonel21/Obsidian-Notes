%%{init: {'theme':'base', 'themeVariables': {'primaryColor':'#1F5C4F','primaryTextColor':'#ffffff','primaryBorderColor':'#123D34','lineColor':'#4A4A4A','secondaryColor':'#E8F1EE','tertiaryColor':'#F5F5F5','fontSize':'14px'}}}%%
flowchart TD

    subgraph P1["PHASE 1 - INTAKE PREPARATION"]
        direction TB
        A1["1.1 Identify candidate AI tool / vendor"]
        A2{"1.2 Does the tool access PHI<br/>via EHR or EpicCare Link?"}
        A3["1.3 Notify External Data Governance (EDG)<br/>for parallel PHI-access review"]
        A4["1.4 Send Vendor Security & AI<br/>Governance Questionnaire"]
        A1 --> A2
        A2 -->|Yes| A3
        A2 -->|No| A4
        A3 -.->|"OPEN QUESTION: does EDG<br/>review gate the AI Governance<br/>submission, or run in parallel?"| A4
    end

    subgraph P2["PHASE 2 - VENDOR RESPONSE"]
        direction TB
        B1["2.1 Vendor completes Sections 1-4:<br/>Data Architecture, IAM, Offshore,<br/>AI Governance & Machine Identity"]
        B2["2.2 Vendor returns completed workbook<br/>with evidence citations"]
        B1 --> B2
    end

    subgraph P3["PHASE 3 - INTERNAL REVIEW"]
        direction TB
        C1["3.1 SA reviews response for<br/>completeness and red flags"]
        C2["3.2 IT InfoSec reviews Sections 1-3:<br/>encryption, IAM, offshore controls"]
        C3["3.3 EDG issues determination<br/>(if triggered in Phase 1)"]
        C1 --> C2
        C2 -.->|"OPEN QUESTION: required<br/>sequence InfoSec -> AI Gov,<br/>or concurrent?"| C3
    end

    subgraph P4["PHASE 4 - SERVICENOW INTAKE"]
        direction TB
        D1["4.1 SA/requester submits AI Governance<br/>Intake catalog item"]
        D2["4.2 Attach: questionnaire, SOC2/HITRUST<br/>reports, architecture diagram, InfoSec<br/>and EDG determinations"]
        D3["4.3 ServiceNow generates governance<br/>review record and routes to committee"]
        D1 --> D2 --> D3
    end

    subgraph P5["PHASE 5 - AI GOVERNANCE COMMITTEE REVIEW"]
        direction TB
        E1["5.1 Committee reviews intake record<br/>and attached evidence"]
        E2{"5.2 Decision"}
        E3["5.3a Approved"]
        E4["5.3b Conditionally approved<br/>with defined guardrails"]
        E5["5.3c Rejected - requester notified,<br/>record closed"]
        E6["5.4 Guardrail conditions logged as<br/>child tasks with owner and due date"]
        E1 --> E2
        E2 --> E3
        E2 --> E4
        E2 --> E5
        E4 --> E6
        E6 -.->|"OPEN QUESTION: how is<br/>guardrail compliance verified<br/>and enforced post-approval?"| E4
    end

    subgraph P6["PHASE 6 - INVENTORY ATTRIBUTION"]
        direction TB
        F1["6.1 Governance outcome and key fields<br/>sync to AI Inventory record"]
        F2["6.2 Contract / BAA status linked<br/>to inventory record"]
        F3["6.3 SA finalizes Risk Tier, PHI flag,<br/>Oversight Model, review dates"]
        F1 --> F2 --> F3
        F1 -.->|"OPEN QUESTION: native<br/>ServiceNow table vs scripted<br/>export/import, and sContracts<br/>integration feasibility"| F2
    end

    subgraph P7["PHASE 7 - ONGOING GOVERNANCE"]
        direction TB
        G1["7.1 Scheduled re-attestation<br/>per Risk Tier cadence"]
        G2["7.2 Guardrail spot-check for<br/>conditional approvals"]
        G1 --> G2
    end

    P1 --> P2 --> P3 --> P4 --> P5 --> P6 --> P7

    classDef requester fill:#2E7D6B,stroke:#123D34,color:#fff
    classDef vendor fill:#8C8C8C,stroke:#4A4A4A,color:#fff
    classDef infosec fill:#3E6B9E,stroke:#22405E,color:#fff
    classDef edg fill:#7A5195,stroke:#4F3462,color:#fff
    classDef committee fill:#C9A227,stroke:#8C6D0E,color:#fff
    classDef system fill:#4A4A4A,stroke:#2A2A2A,color:#fff
    classDef stop fill:#B3413E,stroke:#7A2A28,color:#fff

    class A1,A4,C1,D1,D2,F1,F2,F3 requester
    class A3,C3 edg
    class B1,B2 vendor
    class C2 infosec
    class D3 system
    class E1,E2,E3,E4,E6,G1,G2 committee
    class E5 stop