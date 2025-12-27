# Semantic Router: The AI Traffic Controller

> **Stop blindly sending every user query to GPT-4.**

The **Semantic Router** is a lightweight (42MB) Multi-Label classifier designed to sit at the front of your data pipeline. It predicts *which* entities are present in a text (Address, Crypto Wallet, Person Name, Date) before you attempt extraction.

This allows you to route traffic to cheap, specialized engines (like Libpostal or Regex) instead of expensive LLMs, slashing compute costs and latency.

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0%2B-orange)
![HuggingFace](https://img.shields.io/badge/🤗-Transformers-yellow)
![License](https://img.shields.io/badge/License-MIT-green)

## Key Results

I distilled a **1GB Teacher Model** (XLM-RoBERTa) into a **42MB Student Model** (TinyBERT). The result is a router that is **96% smaller** and **9x faster** while retaining **96% of the accuracy**.

| Model | Parameters | Size (MB) | Speed (samples/sec)* | F1 Retention |
| :--- | :--- | :--- | :--- | :--- |
| **Teacher (XLM-R)** | 278M | 1,060 MB | ~362 | 100% (Baseline) |
| **Student (TinyBERT)** | **11M** | **42 MB** | **~3,304** | **96%** |

*\*Benchmarks run on Apple Silicon (MPS).*

## The Architecture

Instead of a linear pipeline where data flows through every extractor, the Semantic Router acts as a gatekeeper:

```mermaid
graph LR
    A[User Query] --> B(Semantic Router);
    B -->|Has Address?| C[Libpostal];
    B -->|Has Email/IP?| D[Regex Scanner];
    B -->|Has Name?| E[NER Model];
    B -->|No Entities?| F[Direct to LLM];
