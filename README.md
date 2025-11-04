
```mermaid
flowchart LR
    subgraph USER["User / Business Request"]
        UC["Use Case Input + Context"]
    end

    subgraph ORCH["🧠 Orchestrator Layer (CrewAI)"]
        P1["Planner Agent"]
        R1["Router Agent"]
        O1["Optimizer Agent"]
        G1["Guard Agent"]
    end

    subgraph MCP["🧩 MCP Tool Bus"]
        M1["Registry Server (ClearML SDK)"]
        M2["Eval Server (ClearML / Eval Scripts)"]
        M3["Deploy Server (ClearML-Serving / Triton / KServe)"]
        M4["Data Server (SQL / DW / Data Lake)"]
        M5["Vector DB Server (Milvus / Qdrant)"]
        M6["Policy Server (Security / Risk / OPA Rules)"]
    end

    subgraph CLEARML["📘 ClearML Platform"]
        CL1["Model Registry"]
        CL2["Datasets & Artifacts"]
        CL3["Experiments & Metrics"]
        CL4["Pipelines & Queues"]
        CL5["ClearML-Serving (GPU-based Inference)"]
    end

    subgraph GPU["💻 In-House GPU Cluster"]
        GPUT["Training / Fine-tune Jobs"]
        GPUS["Serving Endpoints"]
    end

    subgraph AIRFLOW["🕒 Apache Airflow (Ops Scheduler)"]
        AF1["Nightly Evals"]
        AF2["Dataset Refresh"]
        AF3["Canary Promotions"]
        AF4["Audit & Governance DAGs"]
    end

    subgraph OUTPUT["📊 Outputs / Human Feedback Loop"]
        H1["Evaluation Reports & Dashboards"]
        H2["Model Cards++ / Use Case Cards"]
        H3["Human Approvals & Feedback"]
    end

    %%==============================
    %% Flow Connections
    %%==============================
    UC --> ORCH

    ORCH -->|Tool Calls via MCP| MCP
    MCP -->|Uses ClearML SDK & APIs| CLEARML
    CLEARML -->|Runs on Queues| GPU
    GPU -->|Results + Models| CLEARML

    AIRFLOW -->|Schedules ClearML Pipelines| CLEARML
    AIRFLOW -->|Triggers MCP (Deploy/Promote)| MCP

    CLEARML -->|Metrics & Lineage| OUTPUT
    OUTPUT -->|Feedback / KPIs| CLEARML

    ORCH -->|Governance Queries| M6
    G1 -->|Security & Policy Checks| M6

    %%==============================
    %% Styling
    %%==============================
    classDef orchestrator fill:#d3e4ff,stroke:#0055aa,stroke-width:1px,color:#000;
    classDef mcp fill:#fef3c7,stroke:#b45309,stroke-width:1px,color:#000;
    classDef clearml fill:#e0f2fe,stroke:#0284c7,stroke-width:1px,color:#000;
    classDef gpu fill:#dcfce7,stroke:#15803d,stroke-width:1px,color:#000;
    classDef airflow fill:#f3e8ff,stroke:#7e22ce,stroke-width:1px,color:#000;
    classDef output fill:#fee2e2,stroke:#b91c1c,stroke-width:1px,color:#000;

    class ORCH,P1,R1,O1,G1 orchestrator;
    class MCP,M1,M2,M3,M4,M5,M6 mcp;
    class CLEARML,CL1,CL2,CL3,CL4,CL5 clearml;
    class GPU,GPUT,GPUS gpu;
    class AIRFLOW,AF1,AF2,AF3,AF4 airflow;
    class OUTPUT,H1,H2,H3 output;
```
