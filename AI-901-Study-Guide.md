# AI-901: Microsoft Azure AI Fundamentals — Personalized Study Guide

> Built Aug 2026 from the official [AI-901 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) (skills measured as of April 15, 2026).
> Profile: completed the MS Learn AI-901 tutorials, has an Azure account, wants hands-on Foundry practice. Pass = 700/1000.

## Exam at a glance

| Item | Detail |
|---|---|
| Exam | AI-901 — Microsoft Azure AI Fundamentals (successor to AI-900, retired June 30, 2026) |
| Passing score | 700 (scale of 100–1000) |
| Practice assessment | 50 questions on AI Skills Navigator (sign-in required) |
| Expected background | Basic Python, familiarity with Azure resources |
| Key change from AI-900 | AI-900 asked you to *describe* services; AI-901 asks you to *build* with them |

## Domain weights

| Domain | Weight | Your focus |
|---|---|---|
| 1. Identify AI concepts and capabilities | 40–45% | Mostly covered by the tutorials you finished — polish only |
| 2. Implement AI solutions by using Microsoft Foundry | 55–60% | **The core — spend most of your time here** |

## Domain 1 — Identify AI concepts and capabilities (40–45%)

### Responsible AI — the six principles
Match each scenario to its principle:

| Principle | Signal phrases in questions |
|---|---|
| Fairness | "performs worse for one group", "biased training data" |
| Reliability & safety | "hallucinations", "harmful content", "adversarial inputs" |
| Privacy & security | "personal data", "encryption", "data retention" |
| Inclusiveness | "accents", "disabilities", "diverse users" |
| Transparency | "users know it's AI", "explainable decisions" |
| Accountability | "human review", "audit trail", "who is responsible" |

### Model components and configurations
- Generative models predict tokens sequentially — outputs are probabilistic, not deterministic.
- A **deployment** is a named, hosted instance of a model in your project; code references it by deployment name.
- Key parameters: **temperature** (higher = more random), **max output tokens** (caps length), **top_p**, system message. Low temperature for structured extraction; higher for creative work.
- Choose models by capability: text-only vs multimodal (vision/audio), mini-tier for cost, larger for quality.

### Workload scenario matcher

| Keywords | Workload |
|---|---|
| People/places/dates in text | Entity recognition (NER) |
| Positive/negative review | Sentiment analysis |
| Key words for a search index | Keyword extraction |
| Shorter digest of a document | Summarization |
| Voice → text / text → voice | Speech recognition / speech synthesis |
| Labels/objects in photos | Computer vision (object detection) |
| New image from a description | Image generation |
| Fields from invoices/forms | Information extraction (Content Understanding) |
| Multi-step tasks with tool use | Agentic AI |

## Domain 2 — Implement AI solutions by using Microsoft Foundry (55–60%)

Four sub-domains, each covered by a lab in `Hands-On-Foundry-Labs.md`:

1. **Generative AI apps and agents** — effective system/user prompts; deploy a model and use it in the portal; lightweight chat client with the Foundry SDK; single-agent solution in the portal; lightweight client for an agent. (Labs 1–4)
2. **Text and speech** — lightweight text-analysis app; spoken prompts to a multimodal model; apps on Azure Speech in Foundry Tools. (Lab 5)
3. **Vision and image generation** — interpret images in prompts; generate new images; lightweight vision app. (Lab 6)
4. **Content Understanding** — extract from documents/forms, images, audio, video; lightweight extraction app. (Lab 7)

The exam expects small Python ("lightweight") clients: `azure-ai-projects` SDK, `DefaultAzureCredential` (`az login`), an OpenAI client obtained from `AIProjectClient`. Full working patterns are in the labs file and the [official quickstart](https://learn.microsoft.com/en-us/azure/foundry/quickstarts/get-started-code).

## Study plan (~3 weeks)

**Week 1 — Baseline + Domain 1 polish.** Take the 50-question practice test cold; log every miss by objective. Re-study missed Domain 1 objectives only; self-test by rewriting the two tables above from memory.

**Week 2 — Domain 2 core.** Labs 1–4 (deploy, prompts, chat client, agent). After each lab, redo it from a blank project without the instructions.

**Week 3 — Modalities + exam prep.** Labs 5–7 (speech, vision, Content Understanding). Retake the practice test targeting 85%+. Try the exam sandbox so the question UI holds no surprises.

**Question bank:** 200 questions live in `question_bank.py`; run `python quiz.py` for 20 random ones (`python quiz.py 40` for 40, `--domain1` for Domain 1 only). Options are re-shuffled every run, so answer positions are random.

## Key resources

- [Official AI-901 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)
- [Foundry SDK quickstart](https://learn.microsoft.com/en-us/azure/foundry/quickstarts/get-started-code)
- [Foundry SDK overview](https://learn.microsoft.com/en-us/azure/foundry/how-to/develop/sdk-overview)
- Tim Warner's [ai901 repo](https://github.com/timothywarner-org/ai901) — 16 exam-aligned lessons with runnable Foundry demos
- [Exam AI-901 page](https://learn.microsoft.com/en-us/credentials/certifications/exams/ai-901)
