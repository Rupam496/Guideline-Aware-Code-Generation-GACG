# Privacy-Preserving Federated Embedder Model Training using Homomorphic Encryption

A secure federated learning framework for training embedding models using encrypted model aggregation, knowledge distillation, and distributed retrieval training.

---

# Overview

This project proposes a **privacy-preserving federated framework for embedding model training** where multiple clients collaboratively train retrieval models without sharing raw data.

The framework integrates:

* Federated Learning (FL)
* CKKS-based Homomorphic Encryption
* Secure Aggregation
* Knowledge Distillation
* Contrastive Learning
* Distributed Retrieval Training
* FAISS-based Vector Retrieval

The primary objective is to improve embedding models while ensuring that client data and model updates remain private.

---

# Key Features

* Privacy-preserving federated training
* CKKS encrypted model aggregation
* Retrieval-focused embedding optimization
* Knowledge distillation for model alignment
* Local vector databases using FAISS
* No raw data sharing across clients
* Distributed multi-client architecture
* Secure encrypted aggregation using homomorphic encryption

---

# System Architecture

```text
                    +----------------------+
                    |      Server Node     |
                    |----------------------|
                    | Global Model         |
                    | Secure Aggregation   |
                    | Distillation Engine  |
                    | Flower Server        |
                    +----------+-----------+
                               |
          -------------------------------------------
          |                                         |
          |                                         |
+----------------------+               +----------------------+
|      Client 1        |               |      Client 2        |
|----------------------|               |----------------------|
| Local Dataset        |               | Local Dataset        |
| FAISS Vector Store   |               | FAISS Vector Store   |
| Local Training       |               | Local Training       |
| Encrypt Weights      |               | Encrypt Weights      |
+----------------------+               +----------------------+
```

---

# Model Details

## Embedding Model

Base model:

```text
sentence-transformers/all-MiniLM-L6-v2
```

Model Type:

```text
Sentence Transformer
```

Purpose:

```text
Generate dense embeddings for retrieval tasks
```

Training Objective:

```text
Improve embedding quality using federated optimization
```

---

# Training Methodology

The framework combines multiple objectives.

## 1. Contrastive Learning

Training process:

* Retrieve top-k chunks
* Create positive pairs
* Create negative pairs
* Compute similarity scores
* Optimize retrieval representations

Loss:

```math
L_contrastive
```

---

## 2. Knowledge Distillation

Shared query-chunk pairs align local models with global representations.

Process:

* Server computes similarity logits
* Clients compute local logits
* Distillation loss aligns representations

Loss:

```math
L_distillation
```

---

## Total Loss Function

```math
L_total = L_contrastive + λ × L_distillation
```

---

# Secure Aggregation using CKKS

Encryption scheme:

```text
CKKS (Coppersmith–Kim–Kim–Song)
```

Workflow:

```text
Client Weights
      ↓
Encrypt Using Public Context
      ↓
Transmit Encrypted Weights
      ↓
Encrypted Aggregation
      ↓
Decrypt Aggregated Model
```

Privacy guarantee:

```text
Individual client weights remain hidden
Only aggregated model is decrypted
```

---

# Setup Instructions (Multi-Machine Deployment)

This framework uses:

* 1 Server Machine
* Multiple Client Machines

---

## Step 1: Generate CKKS Context (Server Machine)

Run:

```bash
python encryption/generate_context.py
```

Creates:

```text
ckks_server_context.bin
ckks_public_context.bin
```

Purpose:

* `ckks_server_context.bin` → server secret key
* `ckks_public_context.bin` → shared with clients

---

## Step 2: Distribute Public Context

Copy:

```text
ckks_public_context.bin
```

to all client machines.

Do NOT distribute:

```text
ckks_server_context.bin
```

---

## Step 3: Prepare Client Data and Build FAISS Index

Each client maintains local datasets:

```text
client/Client1/data/
client/Client2/data/
```

Build vector indices:

```bash
python build_index.py
```

Creates:

```text
faiss_index/
├── index.faiss
├── chunks.npy
```

---

## Step 4: Machine Configuration

### Server Machine

Keep:

```text
server.py
server/
encryption/
config/
ckks_server_context.bin
ckks_public_context.bin
```

---

### Client Machines

Keep:

```text
client.py
client/
encryption/
models/
rag_pipeline/
ckks_public_context.bin
```

---

## Step 5: Start Server

Run:

```bash
python server.py
```

---

## Step 6: Start Clients

Run on every client machine:

```bash
python client.py
```

---

# Federated Training Workflow

```text
Initialize Model
      ↓
Send Global Weights
      ↓
Local Training
      ↓
Encrypt Weights
      ↓
Send Updates
      ↓
Encrypted Aggregation
      ↓
Global Model Update
      ↓
Repeat
```

---

# Evaluation Metrics

Metrics monitored:

* Mean Similarity
* Average Loss
* Retrieval Quality
* Embedding Alignment

Example observations:

```text
Round 1 Similarity ≈ 0.74
Round 5 Similarity ≈ 0.79
```

---

# Advantages

* No raw data sharing
* Secure encrypted aggregation
* Distributed training
* Better privacy guarantees
* Retrieval-aware optimization

---

# Limitations

* CKKS computational overhead
* Higher communication cost
* Increased latency
* Large encrypted tensor size

---

# Future Work

Potential improvements:

* Differential Privacy
* Dynamic Query Generation
* Adaptive Client Selection
* Efficient Encryption Parameters
* Multi-Key Homomorphic Encryption

---

# License

This project is intended for educational and research purposes.
