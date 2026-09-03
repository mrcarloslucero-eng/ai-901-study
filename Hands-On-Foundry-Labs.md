# AI-901 Hands-On Foundry Labs

> 7 labs mapped to every Domain 2 sub-objective. One Foundry project with a small chat-model deployment covers everything.
> Code patterns follow the official [Foundry SDK quickstart](https://learn.microsoft.com/en-us/azure/foundry/quickstarts/get-started-code) (`azure-ai-projects>=2.3.0`, keyless `DefaultAzureCredential`). If the portal UI differs slightly, trust the portal — it evolves.
>
> **One-time setup:** create a Foundry project (portal: ai.azure.com → Create project). Deploy a small chat model (a mini-tier model is fine) from the model catalog. Locally: Python 3.10+, `pip install azure-ai-projects azure-identity`, and run `az login`.
> Set `AZURE_AI_PROJECT_ENDPOINT=https://<your-resource>.ai.azure.com/api/projects/<your-project>` as an environment variable.

---

## Lab 1 — Deploy a model and interact with it in the portal (30 min)

**Objective:** *Deploy a model and interact with it in the Foundry portal.*

1. In the Foundry portal, open **Models** → **Deploy a model** from the catalog; pick a small chat model, note the deployment name.
2. Open the chat playground, send: "Explain what a model deployment is in two sentences."
3. Change the temperature parameter (low → high) and resend the same prompt twice each. Observe output consistency.
4. Set a system prompt: "You are a concise study coach for Azure certifications. Answer in under 50 words." Resend.

**Self-check — you can now answer:**
- What is a deployment, and what name does your code use to call it?
- What did raising temperature visibly change?

---

## Lab 2 — Prompts that actually work (30 min)

**Objective:** *Create effective system and user prompts.*

In the playground, iterate on these three tasks until output is perfect every time:

1. **Classifier:** system prompt must force exactly one word output — Billing / Technical / Other — for pasted support tickets. Test with 5 tickets, including an ambiguous one.
2. **Extractor:** prompt for JSON only: `{"sender": "...", "order_number": "...", "total": "..."}` from a pasted order email. Give one worked example (few-shot) and compare accuracy with and without it.
3. **Refusal scope:** add "If the question is not about orders or shipping, reply exactly: OUT OF SCOPE." Test with an off-topic question.

**Pass criteria:** no re-prompts needed; format holds on 5/5 runs at temperature 0.

---

## Lab 3 — Lightweight chat client with the Foundry SDK (45 min)

**Objective:** *Create a lightweight chat client application by using the Foundry SDK.*

Create `chat_client.py`:

```python
import os
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient

project = AIProjectClient(
    endpoint=os.environ["AZURE_AI_PROJECT_ENDPOINT"],
    credential=DefaultAzureCredential(),
)
openai = project.get_openai_client()
MODEL = os.environ.get("MODEL_DEPLOYMENT", "your-deployment-name")

history = [{"role": "system", "content": "You are a concise Azure study coach."}]
print("Chat ready. Type 'quit' to exit.")
while True:
    user = input("You: ")
    if user.lower() == "quit":
        break
    history.append({"role": "user", "content": user})
    response = openai.responses.create(model=MODEL, input=history)
    reply = response.output_text
    print(f"Coach: {reply}\n")
    history.append({"role": "assistant", "content": reply})
```

Run: `az login` then `python chat_client.py`. Ask a question, then a follow-up ("and give an example") to prove history works.

**Stretch:** add a max-token cap and measure how history length changes the request.

---

## Lab 4 — Agent in the portal + lightweight agent client (60 min)

**Objective:** *Create and test a single-agent solution in the portal; create a lightweight client for an agent.*

**Part A — portal:** In the Foundry portal's agent area, create an agent: model = your deployment; instructions = "You are the Study Buddy for the AI-901 exam. Answer only Azure AI/Foundry questions; otherwise reply exactly: OUT OF SCOPE. Keep answers under 80 words." Test in the agent playground, including two off-topic probes.

**Part B — client:** create `agent_client.py`:

```python
import os
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient

project = AIProjectClient(
    endpoint=os.environ["AZURE_AI_PROJECT_ENDPOINT"],
    credential=DefaultAzureCredential(),
)
openai = project.get_openai_client(agent_name="your-agent-name")

conversation = openai.conversations.create()
for question in [
    "What is a model deployment in Foundry?",
    "Give me a one-line reminder about responsible AI fairness.",
    "Who won the 1998 FIFA World Cup?",   # should return OUT OF SCOPE
]:
    r = openai.responses.create(conversation=conversation.id, input=question)
    print("Agent:", r.output_text, "\n")
```

Verify the follow-up context works (the conversation object) and the off-topic refusal fires.

---

## Lab 5 — Text analysis + speech (60 min)

**Objectives:** *lightweight text-analysis app; spoken prompts via multimodal model; app using Azure Speech in Foundry Tools.*

**A. Text analysis** — `text_analysis.py`: send a 3-paragraph product review to your model with a system prompt demanding JSON: `{"sentiment": "...", "key_phrases": [...], "entities": [...], "summary": "..."}`. Run it on 3 reviews of different tones.

**B. Speech.** If your project supports audio input on the multimodal deployment, try a spoken prompt in the playground (microphone input). Otherwise use **Azure Speech in Foundry Tools**: attach a Speech resource to your project, then use the Speech SDK quickstart pattern to transcribe a short WAV you record (`speech to text`), and separately synthesize a sentence to a WAV (`text to speech`). Wire them: your Lab 3 chat client answering by voice — STT in, model call, TTS out.

**Pass:** you speak a question, the app answers aloud.

---

## Lab 6 — Vision + image generation (45 min)

**Objectives:** *interpret visual input in prompts; create new visual outputs; lightweight vision app.*

1. In the chat playground, upload/attach a photo (screenshot, receipt, whiteboard) and ask the model to describe it, then to answer a detail question about it.
2. `vision_client.py`: extend the Lab 3 pattern to send an image with a text prompt ("What is in this image? Summarize in 2 sentences."). Test with 3 different images.
3. In the portal, find an image-generation model in the catalog, deploy it (if available in your region), and generate an illustration from a one-line brief. Note how deployment works the same way as chat models.

---

## Lab 7 — Information extraction with Azure Content Understanding (60 min)

**Objectives:** *extract from documents/forms, images, audio, video; lightweight extraction app.*

1. In your Foundry project, enable/open **Content Understanding** (Foundry Tools).
2. Create analyzers and test with your own samples:
   - **Document/form:** an invoice or receipt PDF/photo → extract vendor, date, total.
   - **Image:** a photo containing text/signage → extract the text and objects.
   - **Audio:** a short recording → transcript plus topics/action items.
   - **Video:** a short clip → scene/description extraction.
3. Build `extract_invoice.py`: submit your invoice sample and print the structured result fields.

**Pass:** four modality extractions each return structured, correct fields.

---

## Cost control & cleanup (do this after every session)

- Use a mini-tier deployment; cap tokens; one project for all labs.
- After each session: delete test agents/analyzer iterations you don't need.
- When fully done: delete the resource group of the project (Azure portal → Resource groups → Delete). This removes all lab charges.

## Suggested order

Labs 1 → 2 → 3 → 4 are the exam core (agents + clients are the biggest sub-domain). Then 5 → 7 → 6, then redo each from scratch unaided — that's the skill the 55–60% domain actually tests.
