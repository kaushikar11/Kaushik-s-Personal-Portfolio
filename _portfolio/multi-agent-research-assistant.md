---
title: "Multi-Agent Research Assistant"
excerpt: "Self-correcting multi-agent RAG system for querying academic research papers through natural language. Built with LangGraph, FAISS, and MCP — featuring a critic-refine loop, intelligent query routing, and Claude Desktop integration."
collection: portfolio
date: 2026-03-01
order: 1
github_url: https://github.com/kaushikar11/Multi-Agent-Research-Assistant
---

## Overview
Built a fully autonomous multi-agent pipeline for querying academic paper collections through natural language. Papers are ingested once into a local FAISS vector index. At query time, a LangGraph agent graph routes the question, retrieves the most relevant chunks, summarises them, scores the quality of that summary through a critic agent, and iteratively rewrites it until the answer meets a quality threshold — all before producing a final structured response.

The retrieval layer is served through an MCP server, meaning the same FAISS index can be queried both through the Streamlit web UI and directly inside Claude Desktop as native tools — without any code changes.

## Agent Pipeline
The system is built as a directed graph with six specialised nodes:

- **Router** — classifies each query as requiring document retrieval or answerable from general LLM knowledge, routing simple conceptual questions directly to the synthesiser
- **Retriever** — calls the MCP `search_papers` tool, performing FAISS similarity search and returning the top-5 most relevant chunks with source metadata (filename, page number)
- **Summariser** — condenses raw retrieved chunks into structured key points that directly address the query, giving the critic a clean target to evaluate
- **Critic** — evaluates the summary across four axes (relevance, completeness, accuracy, clarity) using Pydantic structured output via `with_structured_output()`, returning a float score, a list of specific issues, and a `needs_revision` flag
- **Refine** — runs only when `needs_revision: true`, using the critic's issue list as targeted editing instructions; loops back into the critic with a 3-iteration cap
- **Synthesiser** — final step for both retrieval and direct paths; produces a clean structured answer with source attribution

## Key Features
- **Self-correcting refinement loop** — the critic-refine cycle runs iteratively until the summary scores well or hits the 3-iteration cap; each refine pass uses the critic's specific issues as editing instructions rather than a generic prompt
- **MCP-decoupled retrieval** — FAISS is served as two MCP tools (`search_papers` and `search_with_threshold`) over stdio, allowing the retrieval layer to be swapped or connected to other clients without touching the agent code
- **Intelligent query routing** — avoids retrieval overhead for questions the LLM can answer from general knowledge; both paths converge at the synthesiser for consistent output formatting
- **Structured critic output** — uses Pydantic `BaseModel` with `with_structured_output()` rather than free-text JSON parsing, constrained to schema via function calling for reliability
- **Source attribution** — retrieved answers surface originating PDF filenames, deduped and passed through graph state from retrieval to the final response
- **Batched ingestion** — embedding pipeline processes chunks in batches of 50 with a live ASCII progress bar for large paper collections
- **Streamlit UI with answer metadata** — shows final answer, answer type (retrieved vs general knowledge), number of refinement iterations run, and source PDFs
- **Claude Desktop integration** — MCP server registers via a single `claude_desktop_config.json` entry, enabling direct paper index queries in conversation

## Technologies
LangGraph, LangChain, FAISS, OpenAI API (gpt-4.1-nano, text-embedding-3-small), MCP (Model Context Protocol), langchain-mcp-adapters, PyMuPDF, Pydantic, Streamlit, Python asyncio
