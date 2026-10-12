# On-Premise LLMs for Sensitive Data — Internship Case Study

**Matthew Russo** · AI Research Intern, Axtria · Summer 2026

> The source code from this internship is private to the employer. This page summarizes
> what I built, how I evaluated it, and what I learned. All test data used in the project
> was synthetic; no real patient data was involved.

---

## The question

Can open-weight language models running on hardware you own handle sensitive-data tasks
well enough to replace cloud APIs, where the data never leaves the building?

## What I built

**1. On-premise inference stack, from bare metal**
- Set up an NVIDIA DGX Spark (GB10 Grace Blackwell, 128 GB unified memory) running Ubuntu ARM64
- Deployed and benchmarked five open-weight LLMs, including Gemma 4 26B (MoE and dense),
  Llama 4 Scout, Qwen3 30B and DeepSeek-R1 70B
- Served models with **Ollama** and with **vLLM** in Docker, using **NVFP4** and **FP8** quantization
- Measured throughput, time to first token, memory use and long-context behavior

**2. Four AI pipelines built as LangGraph agents**

| Use case | What it does |
|---|---|
| Sensitive-data protection | Finds and redacts protected health information (PHI) in clinical documents |
| Document classification | Labels document type and sensitivity level |
| RAG Q&A | Answers questions from a document set, with answers grounded in retrieved sources |
| Code repair | Finds and fixes bugs in a sandboxed codebase, with permission checks on actions |

Each pipeline has a **human-in-the-loop review gate**, and I built a **Streamlit** app for reviewers.

**3. An evaluation framework behind every result**
- Hand-curated **gold sets** of labeled examples for each use case
- **Pass/fail gates set before any run**, so results couldn't be judged after the fact
  (for example, PHI recall of at least 90%)
- **LangSmith** tracing on every model, retrieval and validator step
- The same gold sets were run against a cloud model (GPT-4o-mini) for a direct comparison

## Results

- **PHI redaction:** Gemma 4 26B, running fully on-premise, reached **93.5% recall** and passed the 90% gate
- **Sensitivity classification:** Gemma 4 26B reached **94.1% accuracy** and passed a compliance
  gate that **GPT-4o-mini failed**
- **Speed:** the mixture-of-experts (MoE) model ran about **7x faster** than the dense model of
  similar quality, which decided which model to recommend
- Presented the findings to leadership and the AI practice team

## What I learned

- **Set the bar before you measure.** Gates written ahead of time made the results trustworthy,
  including the ones that didn't go our way.
- **Quantization and architecture matter as much as model size.** On fixed hardware, MoE
  versus dense and NVFP4 versus FP8 changed what was practical more than parameter count did.
- **Keep human review in the loop.** Even when a model clears a gate, for sensitive data it
  is a triage tool, not a replacement for a reviewer.

## Tech stack

`Python` · `vLLM` · `Ollama` · `Docker` · `LangGraph` · `LangChain` · `LangSmith` · `RAG` ·
`Streamlit` · `OpenAI API` · `NVFP4 / FP8 quantization` · `Ubuntu ARM64` · `NVIDIA DGX Spark`

---

📫 mrusso08@villanova.edu · [LinkedIn](https://linkedin.com/in/matt-russo-035805334)
