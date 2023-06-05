```mermaid
flowchart TD
    %% Define the clients
    ClientVSCode(["Client - VS Code IDE"])
    LanguageServer["Language Services 
    Platform (LSP)"]

    %% Define the remote index components
    NLB["Network Load Balancer - DNS"]
    GleanService["API Abstraction Service
    and N+ Glean Servers"]
    GleanDB[("Remote Index
    noSQL / JSON
    Schema / Thift
    DB Age <6 hours")]
    GleanIndexer((("reindex every
    ~3 hours")))

    %% Define the remote index flow
    ClientVSCode --> |foobar.cpp|LanguageServer --> |async1|NLB
    NLB --> |Thrift API / Kerberos|GleanService --> |Angle Query|GleanDB -.-> GleanIndexer -.->GleanDB

    %% Define the local index components
    Buck["Build Code - Buck
    with #includes"]
    clangd["Index Code - clangd
    with #includes"]
    clangdDB[("Local Index
    noSQL / JSON
    Compilation DB
    DB Age <1 sec")]
    clangdMemCache[["Cache Index in Memory
    reindex when file changes
    ~2GB Memory"]]

    %% Define the local index flow
    LanguageServer --> |async2|Buck
    subgraph "Initialization: 3min P90 / 30sec P50"
    Buck --> |Compiler Flags|clangd --> |clang-query|clangdDB
    end
    clangdDB --> clangdMemCache
```