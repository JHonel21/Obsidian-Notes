flowchart TD
    subgraph External
        A1["User"] --> A2["Drupal Web Form"]
        A3["User"] --> A4["Email / Phone / Mail"]
    end
    subgraph Frontend
        A2 --> B1["Form Processor"]
    end
    subgraph Automation
        B1 --> B2["Generate Excel File"]
        B2 --> C1["Upload to SFTP (SCHS)"]
        C1 --> C2["MFT (File Transfer)"]
    end
    subgraph Backend
        C2 --> D1["SAS Queue (Auto Case Creation)"]
        D1 --> D2["Case Management System"]
        B1 --> E1["Email Service"]
        E1 --> E2["7-Day Letter Sent to Submitter & PX Team"]
        E2 --> D2
    end
    subgraph PX_Team
        A4 --> F1["Manual Review"]
        F1 --> F2["Manual Case Entry"]
        F2 --> D2
        D2 --> F3["Follow-Up Needed?"]
        F3 -- Yes --> F4["Follow Up with Submitter"]
        F3 -- No --> G1["End"]
    end