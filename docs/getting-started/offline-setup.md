# Offline Setup for Corporate Environments

This guide explains how to set up Codanna in environments where internet access is restricted or model downloads are blocked.

## Overview

Codanna uses the `all-MiniLM-L6-v2-onnx` model for semantic code search. In corporate environments with restricted internet access, you can use the pre-packaged models included in this branch.

## Quick Start

### Option 1: Use Pre-packaged Models (Recommended for Offline)

If you're on the `model_included` branch, the models are already included in the repository:

```bash
# 1. Install Codanna
cargo install --path . --all-features

# 2. Copy models to the global directory
mkdir -p ~/.codanna/models
cp -r models/models--Qdrant--all-MiniLM-L6-v2-onnx ~/.codanna/models/

# 3. Initialize your project
cd /path/to/your/project
codanna init

# 4. Index your code
codanna index . --progress
```

### Option 2: Manual Model Transfer

If you have the models on another machine with internet access:

**On the machine with internet access:**

```bash
# Install and run init to download models
cargo install codanna --all-features
codanna init

# Package the models
tar -czf codanna-models.tar.gz -C ~/.codanna models
```

**Transfer `codanna-models.tar.gz` to your offline machine, then:**

```bash
# Extract models
mkdir -p ~/.codanna
tar -xzf codanna-models.tar.gz -C ~/.codanna

# Now use Codanna normally
codanna init
codanna index . --progress
```

## Model Details

**Model Used:** `Qdrant/all-MiniLM-L6-v2-onnx`
- **Size:** ~97MB
- **Purpose:** Semantic embedding for code search
- **Format:** ONNX (optimized for inference)
- **Source:** https://huggingface.co/Qdrant/all-MiniLM-L6-v2-onnx

## Directory Structure

After setup, your model directory should look like this:

```
~/.codanna/
└── models/
    └── models--Qdrant--all-MiniLM-L6-v2-onnx/
        ├── blobs/
        ├── refs/
        └── snapshots/
```

## Verification

To verify the models are correctly installed:

```bash
# Check if models directory exists
ls ~/.codanna/models/

# Test semantic search (requires an indexed project)
cd /path/to/indexed/project
codanna mcp semantic_search_docs query:"find authentication logic" limit:5
```

## Troubleshooting

### Error: "Failed to load model"

**Cause:** Models not found in `~/.codanna/models/`

**Solution:**
```bash
# Verify model directory exists
ls -la ~/.codanna/models/models--Qdrant--all-MiniLM-L6-v2-onnx/

# If missing, copy from repository
cp -r models/models--Qdrant--all-MiniLM-L6-v2-onnx ~/.codanna/models/
```

### Error: "Network error during initialization"

**Cause:** Codanna trying to download models

**Solution:** Ensure models are already in `~/.codanna/models/` before running `codanna init`

### Corporate Proxy Issues

If you're behind a corporate proxy and getting connection errors:

1. Use the offline setup method (Option 1 or 2 above)
2. Models are pre-packaged - no internet required after installation
3. Ensure `~/.codanna/models/` is populated before first use

## Alternative: Custom Model Directory

You can specify a custom model directory using environment variables:

```bash
# Set custom model cache directory
export HF_HOME=/custom/path/to/models

# Copy models to custom location
cp -r models/models--Qdrant--all-MiniLM-L6-v2-onnx $HF_HOME/

# Use Codanna normally
codanna init
```

## Notes

- The `model_included` branch is specifically for offline/restricted environments
- The main branch downloads models automatically from Hugging Face
- Model files are cached globally and shared across all projects
- You only need to set up models once per machine

## See Also

- [Installation Guide](installation.md)
- [Getting Started](getting-started.md)
- [Configuration](../user-guide/configuration.md)
