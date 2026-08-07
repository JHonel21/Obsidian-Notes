%%{init: {'theme':'base', 'themeVariables': {'primaryColor':'#1F5C4F','primaryTextColor':'#ffffff','primaryBorderColor':'#123D34','lineColor':'#4A4A4A','secondaryColor':'#E8F1EE','tertiaryColor':'#F5F5F5','fontSize':'15px'}}}%%
flowchart TD
    A["<b>1. Identify AI Tool</b><br/>Requester (often SA)<br/>finds new AI vendor or use case"] --> B["<b>2. Send Vendor Questionnaire</b><br/>Solutions Architecture<br/>Security & AI Governance Questionnaire"]
    B --> C["<b>3. Vendor Completes & Returns</b><br/>Vendor<br/>~2-3 weeks"]
    C --> D["<b>4. SA Reviews Response</b><br/>Solutions Architecture<br/>Completeness & red-flag check"]
    D --> E["<b>5. Submit ServiceNow Intake</b><br/>Requester<br/>AI Governance Intake Form"]
    E --> F{"<b>6. Committee Review</b><br/>AI Governance Committee<br/>Approve / Conditional / Reject"}
    F -->|Approved| G["<b>7. Attribute to Inventory</b><br/>Solutions Architecture<br/>Add or update AI Inventory record"]
    F -->|Conditional| H["<b>7a. Guardrails Tracked</b><br/>AI Governance Committee<br/>Conditions logged, re-check scheduled"]
    F -->|Rejected| I["<b>Stop</b><br/>Requester notified, no deployment"]
    H --> G
    G --> J["<b>8. Periodic Re-Attestation</b><br/>AI Governance Committee<br/>Annual or trigger-based review"]

    classDef requester fill:#2E7D6B,stroke:#123D34,color:#fff
    classDef vendor fill:#8C8C8C,stroke:#4A4A4A,color:#fff
    classDef committee fill:#C9A227,stroke:#8C6D0E,color:#fff
    classDef stop fill:#B3413E,stroke:#7A2A28,color:#fff

    class A,B,D,E,G requester
    class C vendor
    class F,H,J committee
    class I stop