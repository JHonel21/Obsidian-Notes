flowchart TD
    A[Start - Scan Cart QR Code] --> B[Power Apps Form Opens]
    B --> C[User Selects Reason for Scan]
    C --> D1[Routine Check]
    C --> D2[Cart Movement Only]
    C --> D3[Tray Exchange]
    C --> D4[Full Cart Exchange]
    D1 --> E1[Display Tray Expiration Dates]
    E1 --> F1[Display Attestation Checkbox]
    F1 --> G1[Optional: Update Location]
    G1 --> H1[Submit]
    D2 --> E2[Hide Tray Expiration Fields]
    E2 --> F2[Hide Attestation Checkbox]
    F2 --> G2[Require New Location]
    G2 --> H2[Submit]
    D3 --> E3[Display New Tray ID Fields]
    E3 --> F3[Require Updated Expiration Dates]
    F3 --> G3[Require Attestation]
    G3 --> H3[Optional: Update Location]
    H3 --> I3[Submit]
    D4 --> E4[Display Replacement Cart ID]
    E4 --> F4[Require New Location]
    F4 --> G4[Require Attestation]
    G4 --> H4[Optional: Enter Notes]
    H4 --> I4[Submit]
    H1 --> Z[Save Data to SharePoint/List]
    H2 --> Z
    I3 --> Z
    I4 --> Z
    Z --> Y[Auto-Capture Email, Timestamp, Update Status]
    Y --> End[End]

    style D1 fill:#004990,stroke:#000,color:#fff
    style E1 fill:#004990,stroke:#000,color:#fff
    style F1 fill:#004990,stroke:#000,color:#fff
    style G1 fill:#004990,stroke:#000,color:#fff
    style H1 fill:#004990,stroke:#000,color:#fff
    style D2 fill:#a0e7a0,stroke:#000,color:#fff
    style E2 fill:#a0e7a0,stroke:#000,color:#fff
    style F2 fill:#a0e7a0,stroke:#000,color:#fff
    style G2 fill:#a0e7a0,stroke:#000,color:#fff
    style H2 fill:#a0e7a0,stroke:#000,color:#fff
    style D3 fill:#f4a6a6,stroke:#000,color:#fff
    style E3 fill:#f4a6a6,stroke:#000,color:#fff
    style F3 fill:#f4a6a6,stroke:#000,color:#fff
    style G3 fill:#f4a6a6,stroke:#000,color:#fff
    style H3 fill:#f4a6a6,stroke:#000,color:#fff
    style I3 fill:#f4a6a6,stroke:#000,color:#fff
    style D4 fill:#6595CF,stroke:#000,color:#fff
    style E4 fill:#6595CF,stroke:#000,color:#fff
    style F4 fill:#6595CF,stroke:#000,color:#fff
    style G4 fill:#6595CF,stroke:#000,color:#fff
    style H4 fill:#6595CF,stroke:#000,color:#fff
    style I4 fill:#6595CF,stroke:#000,color:#fff