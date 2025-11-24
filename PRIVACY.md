# Privacy & Data Security

## Your Code Never Leaves Your Machine

Codanna is designed with privacy as a core principle. All code analysis, indexing, and semantic search happens **100% locally on your machine**. No data is sent to external servers.

## How It Works

### 1. Model Download (One-Time Only)

**Model Used:** `Qdrant/all-MiniLM-L6-v2-onnx`
- **Downloaded from:** Hugging Face (https://huggingface.co/Qdrant/all-MiniLM-L6-v2-onnx)
- **Stored locally in:** `~/.codanna/models/`
- **Size:** ~97MB
- **Format:** ONNX (optimized for CPU inference)

**What Qdrant Sees:** Nothing. Qdrant hosts the model file on Hugging Face, but they have zero visibility into:
- Your code
- Your queries
- Your projects
- Any data processed by the model

**Think of it like downloading VS Code from Microsoft** - just because Microsoft hosts the download doesn't mean they see your files.

### 2. Code Indexing (100% Local)

When you run `codanna index`, here's what happens:

```
Your Source Files
    ↓
Tree-sitter Parser (local CPU)
    ↓
Extract Symbols & Relationships (local memory)
    ↓
Local ONNX Model (runs on your CPU)
    ↓
Vector Embeddings Generated (in RAM)
    ↓
Stored in .codanna/index/ (local filesystem)
```

**No network activity. No external API calls. No cloud services.**

### 3. Semantic Search (100% Local)

When you search for code:

```
Your Query
    ↓
Local ONNX Model (runs on your CPU)
    ↓
Query Embedding Generated (in RAM)
    ↓
Compare against .codanna/index/ (local filesystem)
    ↓
Results Returned (local memory)
```

**Zero network requests. Zero external dependencies after model download.**

## Data Storage

All your project data stays in the `.codanna/` directory within your project:

```
your-project/
└── .codanna/
    ├── index/
    │   ├── semantic/        # Vector embeddings (local files)
    │   ├── tantivy/         # Full-text search index (local files)
    │   └── symbol_cache.bin # Symbol lookup cache (memory-mapped)
    ├── settings.toml        # Project settings
    └── .project-id          # Unique local project identifier
```

**These are regular files on your disk.** They can be:
- Added to `.gitignore` (recommended)
- Deleted at any time
- Backed up with your project
- Never transmitted anywhere

## Model Files (Shared Across Projects)

The ONNX model is downloaded once and shared across all projects:

```
~/.codanna/
└── models/
    └── models--Qdrant--all-MiniLM-L6-v2-onnx/
        ├── blobs/      # Model weights and configuration
        ├── refs/       # Version references
        └── snapshots/  # Model snapshots
```

**This directory contains only the model** - no project data, no code, no personal information.

## Offline Use

The `model_included` branch includes pre-packaged models, allowing you to:

1. **Install without internet access**
2. **Use in restricted networks** (corporate firewalls, air-gapped systems)
3. **Avoid any model download step**

See [docs/getting-started/offline-setup.md](docs/getting-started/offline-setup.md) for instructions.

## No Telemetry, No Tracking

Codanna does not:
- Send usage statistics
- Report crashes
- Phone home
- Track analytics
- Require authentication
- Need API keys
- Connect to external services (except one-time model download)

## Perfect For

- **Proprietary codebases** - Your trade secrets stay secret
- **Corporate environments** - Meets strict data security policies
- **Regulated industries** - No data leaving your infrastructure
- **Open source projects** - Privacy-respecting by design
- **Air-gapped systems** - Use the `model_included` branch

## Comparison With Cloud Services

| Feature | Codanna | Cloud Code Search |
|---------|---------|-------------------|
| Code stays local | ✅ Yes | ❌ Uploaded to cloud |
| Works offline | ✅ Yes | ❌ Requires internet |
| API keys needed | ❌ No | ✅ Required |
| Data processed on your CPU | ✅ Yes | ❌ Cloud servers |
| Corporate firewall friendly | ✅ Yes | ⚠️ May be blocked |
| Proprietary code safe | ✅ Yes | ⚠️ Depends on TOS |

## Technical Details

### ONNX Model
The model is a **transformer-based sentence embedding model**:
- **Base:** `sentence-transformers/all-MiniLM-L6-v2`
- **Converted by:** Qdrant for optimal inference performance
- **Runs on:** Your CPU using ONNX Runtime
- **Purpose:** Convert code and queries into vector embeddings for semantic similarity search

### Why ONNX?
- **No Python runtime needed** - Rust can load it directly
- **Fast inference** - Optimized for CPU execution
- **Small size** - ~97MB vs 500MB+ for full PyTorch models
- **Production-ready** - Battle-tested inference format

### Local Vector Storage
Codanna uses **memory-mapped files** for vector storage:
- **Fast lookups** - <10ms for symbol resolution
- **Efficient memory** - OS handles caching automatically
- **Standard file format** - No proprietary database required

## Questions?

**Q: Does Qdrant see my code?**
A: No. Qdrant only hosts the model file on Hugging Face. They have no access to your projects.

**Q: What if I don't trust the model?**
A: The model is open source and can be audited. It's also the same model used by many embedding libraries. You can inspect the ONNX file with tools like Netron.

**Q: Can I use Codanna on proprietary code?**
A: Yes. Everything runs locally. Your code never leaves your machine.

**Q: What about the MCP protocol?**
A: MCP is a local protocol between your AI assistant (Claude, etc.) and Codanna running on your machine. No external communication.

**Q: Is semantic search secure?**
A: Yes. The embeddings are mathematical representations stored locally. They cannot be reverse-engineered to reconstruct your source code.

## License & Attribution

Codanna is licensed under Apache License 2.0. The ONNX model is from Hugging Face under the Apache 2.0 license.

See [LICENSE](LICENSE) and [NOTICE](NOTICE) for details.

---

**Privacy is not a feature. It's a requirement.**

Your code is your intellectual property. Codanna respects that by keeping everything local.
