# Soliplex

Soliplex is a self-hosted, AI-powered Retrieval-Augmented Generation (RAG)
platform. It combines semantic document retrieval with generative AI to deliver
accurate, cited responses grounded in your document collections. The ecosystem
spans ingestion, indexing, retrieval, and user interface -- providing a
complete pipeline from raw documents to conversational AI.

> Full documentation: [soliplex.github.io](https://soliplex.github.io)

## Architecture

```mermaid
graph TB
    
    subgraph "User Interfaces"
        FE["Frontend<br/>(Flutter / Dart)"]
        TUI["Terminal UI<br/>(Textual)"]
    end


    subgraph "Backend"
        SX["Soliplex<br/>(FastAPI)"]
        MCP["MCP Server<br/>(FastMCP)"]
    end

    subgraph "AI & Retrieval"
        PAI["Pydantic AI<br/>Agents"]
        HR["Haiku RAG<br/>(LanceDB)"]
        HS["Haiku Skills"]
    end

    
    subgraph "Storage"
        LDB["LanceDB<br/>(Vectors)"]
        PG["PostgreSQL / SQLite<br/>(State)"]
    end
    subgraph LLM["LLM Providers"]
        OL["Ollama"]
        OA["OpenAI"]
        GE["Gemini"]
        AN["Anthropic"]
        VL["VLLM"]
    end
        subgraph "Ingestion Pipeline"
        IA["Ingester Agents"]
        SI["Hauku-Rag Ingester"]        
        DL["Docling"]
    end
    subgraph "Document Sources"
        FS["Filesystem"]
        SCM["GitHub / Gitea"]
        WD["WebDAV"]
        WEB["Web Pages"]
        S3["S3"]
    end


    FE -->|"AG-UI Protocol"| SX
    TUI --> SX
    SX --> PAI
    SX --> MCP
    PAI --> HR
    PAI --> HS
    LDB --> HR

    FS --> IA
    SCM --> IA
    WD --> IA
    WEB --> IA
    S3 --> IA
    IA -->|"Discovers & fetches"| SI    
    SI -->|"Parse, chunk, embed"| DL

    PAI <-->|"Inference"| LLM

    SI --> LDB
    SI --> PG
```

## Components

### [Soliplex](https://github.com/soliplex/soliplex) (Backend)

The core backend service built with FastAPI and Python. Soliplex orchestrates
AI agents, RAG retrieval, and real-time chat across isolated room
environments.

- Multi-room architecture with independent configurations and knowledge bases
- Multi-provider LLM support (OpenAI, Ollama, Gemini, Anthropic)
- OIDC/JWT authentication with Keycloak integration
- MCP server and client for extended AI tool integration
- AG-UI protocol for structured frontend communication
- Quiz system with LLM-based evaluation
- 100% unit test branch coverage

### [Frontend](https://github.com/soliplex/frontend)

A cross-platform Flutter application that serves as the primary user
interface. Runs on Android, iOS, macOS, Web, Linux, and Windows.

- Multi-server OIDC authentication with secure credential storage
- Room-based chat with streamed AI responses and execution step visibility
- RAG document filtering with source citations and PDF chunk visualization
- File uploads, interactive quizzes, and network diagnostics
- Responsive Material Design 3 layouts for mobile through desktop
- Modular shell architecture with composable feature modules

### [Ingester Agents](https://github.com/soliplex/ingester-agents)

A multi-source document discovery and delivery system. Ingester Agents
connect to diverse origins and feed documents into the ingestion pipeline.

- Filesystem, WebDAV, GitHub, Gitea, web page, and S3 source support
- Declarative YAML manifests with cron scheduling
- SHA-256 content hashing for incremental sync (only changed files processed)
- Async-first architecture with retry logic and exponential backoff
- FastAPI REST API and Typer CLI


### [Concierge](https://github.com/soliplex/soliplex-concierge)

Concierge provides an extension for LLM-driven, on-demand room requests: 
users chat with a self-serve "about" room to request 
**access to a non-public room** or the **creation of a new room**, 
and the concierge files each request as a tracking issue on a Gitea
repository for an administrator to resolve.


## Key Libraries

| Library | Role |
|---|---|
| [**Haiku RAG**](https://github.com/ggozad/haiku.rag) | RAG engine with hybrid vector + full-text search, agentic QA, and multi-provider embeddings |
| [**Haiku Skills**](https://github.com/ggozad/haiku.skills) | Modular skill system for Pydantic AI agents with sub-agent execution and AG-UI streaming |
| **Docling** | Document conversion and structural extraction for PDFs and other formats |
| **Pydantic AI** | Agent framework with function calling, tool integration, and multi-provider LLM support |
| **LanceDB** | Embedded vector database for semantic search and retrieval |
| **FastMCP** | Model Context Protocol server/client for AI tool interoperability |

## Documentation

Full configuration guides, tutorials, and API reference are available at
[soliplex.github.io](https://soliplex.github.io).
