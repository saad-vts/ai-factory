flowchart LR
    %%==============================
    %% Agentic AI Concept Factory (Tool-integrated Flow)
    %%==============================

    subgraph user["User / Business Request"]
        uc["Use Case Input + Context"]
    end

    subgraph orchestrator["Orchestrator Layer (CrewAI)"]
        p1["Planner Agent"]
        r1["Router Agent"]
        o1["Optimizer Agent"]
        g1["Guard Agent"]
    end

    subgraph mcp_bus["MCP Tool Bus"]
        m1["Registry Server (ClearML SDK)"]
        m2["Eval Server (ClearML / Eval Scripts)"]
        m3["Deploy Server (ClearML-Serving / Triton / KServe)"]
        m4["Data Server (SQL / DW / Data Lake)"]
        m5["Vector DB Server (Milvus / Qdrant)"]
        m6["Policy Server (Security / Risk / OPA Rules)"]
    end

    subgraph clearml["ClearML Platform"]
        cl1["Model Registry"]
        cl2["Datasets & Artifacts"]
        cl3["Experiments & Metrics"]
        cl4["Pipelines & Queues"]
        cl5["ClearML-Serving (GPU-based Inference)"]
    end

    subgraph gpu_cluster["In-House GPU Cluster"]
        gput["Training / Fine-tune Jobs"]
        gpus["Serving Endpoints"]
    end

    subgraph airflow_sys["Apache Airflow (Ops Scheduler)"]
        af1["Nightly Evals"]
        af2["Dataset Refresh"]
        af3["Canary Promotions"]
        af4["Audit & Governance DAGs"]
    end

    subgraph output_sys["Outputs / Human Feedback Loop"]
        h1["Evaluation Reports & Dashboards"]
        h2["Model Cards++ / Use Case Cards"]
        h3["Human Approvals & Feedback"]
    end

    %%==============================
    %% Flow Connections
    %%==============================
    uc --> orchestrator
    orchestrator -->|Tool Calls via MCP| mcp_bus
    mcp_bus -->|Uses ClearML SDK & APIs| clearml
    clearml -->|Runs on Queues| gpu_cluster
    gpu_cluster -->|Results + Models| clearml

    airflow_sys -->|Schedules ClearML Pipelines| clearml
    airflow_sys -->|Triggers MCP (Deploy/Promote)| mcp_bus

    clearml -->|Metrics & Lineage| output_sys
    output_sys -->|Feedback / KPIs| clearml

    orchestrator -->|Governance Queries| m6
    g1 -->|Security & Policy Checks| m6

    %%==============================
    %% Styling
    %%==============================
    classDef orchestrator fill:#d3e4ff,stroke:#0055aa,stroke-width:1px,color:#000;
    classDef mcp fill:#fef3c7,stroke:#b45309,stroke-width:1px,color:#000;
    classDef clearml fill:#e0f2fe,stroke:#0284c7,stroke-width:1px,color:#000;
    classDef gpu fill:#dcfce7,stroke:#15803d,stroke-width:1px,color:#000;
    classDef airflow fill:#f3e8ff,stroke:#7e22ce,stroke-width:1px,color:#000;
    classDef output fill:#fee2e2,stroke:#b91c1c,stroke-width:1px,color:#000;

    class orchestrator,p1,r1,o1,g1 orchestrator;
    class mcp_bus,m1,m2,m3,m4,m5,m6 mcp;
    class clearml,cl1,cl2,cl3,cl4,cl5 clearml;
    class gpu_cluster,gput,gpus gpu;
    class airflow_sys,af1,af2,af3,af4 airflow;
    class output_sys,h1,h2,h3 output;
