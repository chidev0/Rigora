# Rigora

> **Not unfiltered. Unflinching.**

An AI conversation platform built around honest, challenging, anti-sycophantic dialogue. Where mainstream AI optimizes for making you feel good, Rigora optimizes for making you think harder.

***

## What This Is

Most AI tools are structurally incentivized to validate users — they're trained on engagement signals that all improve when the model agrees with you. Rigora is built on the opposite premise: productive friction is more valuable than comfortable confirmation. The core behavior is a fine-tuned model that pushes back, questions assumptions, and refuses easy validation — without being cruel.

**Free Tier** — Honest, opinionated conversations with a daily conversation limit.  
**Pro Tier ($15/mo)** — Unlimited conversations, persistent memory across sessions, Reflection Mode (structured Socratic sessions), full 27B model, adjustable directness, and session export.

***

## Milestones

### v0 — Proof of Behavior
*"Does the thing I'm building actually do the thing?"*

The entire goal of v0 is proving the core behavior is achievable. No backend. No frontend. No infrastructure. Just: fine-tune a model, talk to it, confirm it behaves fundamentally differently from base models.

**Issues:**
- [ ] `[dataset]` Write 50 JSONL conversation pairs demonstrating Rigora behavior — AI pushing back, questioning assumptions, refusing validation
- [ ] `[dataset]` Validate JSONL format, clean noise, ensure balance across scenarios
- [ ] `[ml]` Install Ollama on Arch rig, pull `gemma2:9b`, verify local inference works
- [ ] `[ml]` Set up Python venv + Unsloth + Hugging Face on RTX 4060 build
- [ ] `[ml]` Run first QLoRA fine-tune on 50 examples using Gemma 4 E4B (4B dense, fits 8GB VRAM)
- [ ] `[ml]` Export fine-tuned model to GGUF, load in Ollama
- [ ] `[eval]` Have 10+ conversations with fine-tuned model — document behavioral delta vs. base model

**Milestone gate:** Fine-tuned model demonstrably challenges users where a base model would validate them.

***

### v1 — Working Skeleton
*"Can a stranger actually use this thing?"*

v1 is the first deployable version. A working API backend, async request queue, basic auth, a minimal frontend, and free-tier functionality fully operational. No pro features yet — the goal is a shareable product that real people can try.

**Issues:**
- [ ] `[backend]` FastAPI app scaffold — project structure, environment config, logging
- [ ] `[backend]` Async request queue with tier-priority routing (free users deprioritized)
- [ ] `[backend]` Ollama integration — route requests to local model, return streamed response
- [ ] `[backend]` API fallback routing — Claude Haiku via API for overflow/free tier
- [ ] `[backend]` Basic JWT auth — register, login, token refresh
- [ ] `[backend]` Rate limiting middleware — gateway-level, not application-level
- [ ] `[backend]` Conversation storage — save/load message history per user
- [ ] `[frontend]` UI shell — chat interface, auth screens, basic navigation
- [ ] `[frontend]` Streaming response rendering
- [ ] `[infra]` Docker Compose setup for local development
- [ ] `[infra]` Basic health check endpoint + uptime monitoring
- [ ] `[dataset]` Expand dataset to 200 conversation pairs across varied scenarios

**Milestone gate:** Working product deployed locally. Free-tier conversations functional end-to-end. A stranger could sign up and have a real conversation.

***

### v2 — Pro Tier Live
*"Is this worth $15/month?"*

v2 introduces the features that justify the pro tier. Persistent memory is the single most important conversion driver — once a user has session history they don't want to lose, switching costs are real. Reflection Mode is the second differentiator.

**Issues:**
- [ ] `[ml]` Fine-tune on expanded 500+ example dataset (Gemma 4 26B A4B target — prepare configs now, run on upgraded hardware)
- [ ] `[ml]` A/B evaluation: 26B fine-tuned vs. 7B fine-tuned vs. prompted frontier model
- [ ] `[backend]` Cross-session memory — store embeddings of prior sessions, inject relevant context into system prompt
- [ ] `[backend]` Reflection Mode session structure — opening, challenge, and reflection phases with state machine
- [ ] `[backend]` Session export endpoint — generate downloadable journal-style summary (PDF or Markdown)
- [ ] `[backend]` Stripe integration — subscription management, pro tier gating, webhook handling
- [ ] `[backend]` Directness slider — user-controlled system prompt parameter (0 = default, 10 = maximally direct)
- [ ] `[backend]` Pro vs. free model routing — 27B for pro, 7B or Haiku for free
- [ ] `[frontend]` Pro upgrade flow — paywall, Stripe checkout, confirmation
- [ ] `[frontend]` Session history UI — archive view, session summaries, search
- [ ] `[frontend]` Reflection Mode UI — structured session interface distinct from general chat
- [ ] `[infra]` Deploy to VPS (Hetzner or similar) — backend + model serving separated

**Milestone gate:** First paying user. Pro features functional and coherently differentiated from free tier.

***

### v3 — Revenue-Positive & Moat-Building
*"Is the data flywheel turning?"*

v3 is about closing the feedback loop between user conversations and model quality. The fine-tuned model trained on real Rigora conversations should begin to outperform any prompt-engineered competitor. This is the moat.

**Issues:**
- [ ] `[ml]` Data pipeline — opt-in conversation logging for training data, anonymization, JSONL export
- [ ] `[ml]` Retrain on real user conversation data — first iteration of flywheel
- [ ] `[ml]` Evaluation framework — automated perplexity scoring + human eval rubric for Rigora behavior
- [ ] `[backend]` Onboarding flow — first-session questionnaire to seed user context for memory
- [ ] `[backend]` Spike handling — auto-throttle free tier on queue saturation, cloud burst routing (RunPod/Lambda Labs)
- [ ] `[backend]` Admin dashboard — active users, conversion rate, model latency, queue depth
- [ ] `[frontend]` Onboarding UI — intake questionnaire, tutorial conversation
- [ ] `[frontend]` Public homepage — waitlist/marketing page, value proposition, demo
- [ ] `[infra]` Phase 2 hardware — Mac Mini M4 Pro 48GB as always-on inference server (post first revenue)
- [ ] `[infra]` Monitoring stack — Prometheus + Grafana or equivalent, alerting on queue depth and error rate
- [ ] `[dataset]` 1,000+ training examples with real user conversation data mixed in

**Milestone gate:** Monthly revenue covers infrastructure costs. Model quality measurably improves from user data. Business is self-sustaining.

***

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| **Language** | Python 3.11+ | Entire ML ecosystem is Python-first |
| **Backend** | FastAPI | Async, fast, great for LLM streaming |
| **Queue** | asyncio + Redis | Tier-priority routing for free vs. pro |
| **Auth** | JWT (python-jose) | Stateless, simple for solo dev |
| **Payments** | Stripe | Industry standard, excellent Python SDK |
| **Inference (local)** | Ollama + llama.cpp | Serve GGUF models locally |
| **Fine-tuning** | Unsloth + QLoRA | 2× faster, 60% less VRAM than vanilla HF |
| **Model (free tier)** | Claude Haiku via API | ~$0.000325/500 tokens |
| **Model (pro tier)** | Gemma 4 26B A4B (fine-tuned) | MoE: 26B quality at 4B inference speed |
| **Frontend** | TBD (React or SvelteKit) | Decide at v1 |
| **Database** | PostgreSQL + pgvector | Structured data + memory embeddings |
| **Deployment** | Docker Compose → Hetzner VPS | Simple, cost-effective |

***

## Getting Started (EndeavourOS / Arch Linux + PyCharm)

### Prerequisites

Make sure the following are installed on your Arch rig. Most of these may already be present on your setup:

```bash
# Update system first
sudo pacman -Syu

# Python 3.11+ (Arch ships a recent Python, verify version)
python --version

# pip and venv
sudo pacman -S python-pip

# Git (likely already installed)
sudo pacman -S git

# CUDA toolkit (for RTX 4060 fine-tuning)
sudo pacman -S cuda cudnn

# Verify CUDA is visible
nvidia-smi
nvcc --version
```

***

### Install Ollama

Ollama is your local inference server. Install it first — getting a model running locally is the fastest way to understand how inference serving works, and you'll use it to test fine-tuned models before deploying them.

```bash
# Install via AUR (yay or paru)
yay -S ollama

# Or the official install script
curl -fsSL https://ollama.com/install.sh | sh

# Start the Ollama service
sudo systemctl enable --now ollama

# Pull a test model (fits in 8GB VRAM)
ollama pull gemma2:9b

# Test it works
ollama run gemma2:9b "Explain sycophancy in AI briefly"
```

***

### Clone the Repo & Set Up PyCharm

```bash
# Clone
git clone https://github.com/YOUR_USERNAME/rigora.git
cd rigora
```

Open PyCharm (install via AUR if needed: `yay -S pycharm-community-edition`).

1. **File → Open** → select the `rigora/` directory
2. **File → Settings → Project → Python Interpreter → Add Interpreter → Add Local Interpreter**
3. Select **Virtualenv Environment → New**
4. Set location to `rigora/.venv`, base interpreter to `/usr/bin/python3`
5. Click **OK** — PyCharm creates the venv

Then in the PyCharm terminal (or your regular terminal):

```bash
# Activate venv (if using terminal directly)
source .venv/bin/activate

# Install base dependencies
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
pip install transformers datasets peft accelerate bitsandbytes
pip install unsloth
pip install huggingface_hub

# Verify GPU is visible to PyTorch
python -c "import torch; print(torch.cuda.is_available()); print(torch.cuda.get_device_name(0))"
```

If `torch.cuda.is_available()` returns `False`, CUDA isn't wired up correctly — check that `nvidia-smi` works and that the PyTorch install used the right CUDA version (`cu121` for CUDA 12.1, adjust to match your `nvcc --version` output).

***

### Your First Fine-Tune (v0 Goal)

Once the environment is set up, your first real task is writing the dataset. Create `dataset/v0_examples.jsonl` manually — this is the most important file in the whole project:

```jsonl
{"messages": [{"role": "user", "content": "I think my idea is really unique, nobody has thought of this before."}, {"role": "assistant", "content": "What makes you confident of that? Have you searched for existing products, patents, or research in the space? 'Nobody has thought of this' is usually a signal that either the search wasn't thorough, or there's a reason others passed on it. What specifically did you look for?"}]}
{"messages": [{"role": "user", "content": "I work really hard, I deserve to be promoted."}, {"role": "assistant", "content": "Working hard is necessary but not sufficient for promotion — that's a common and genuinely painful gap. What do your peers say about your work? What does your manager say, specifically, when you ask what would get you there? If you haven't asked directly, that's worth doing before assuming the effort isn't being recognized."}]}
```

Each line is one training example. Write 50 of these before touching any training code — the writing process forces you to define what "Rigora behavior" actually is.

To run the fine-tune once you have 50+ examples:

```bash
# A minimal Unsloth QLoRA fine-tune on Gemma 4B (fits RTX 4060 8GB)
# See fine-tuning/train_v0.py for the full script (added in v0 issues)
python fine-tuning/train_v0.py
```

***

### GitHub Project Setup

1. Create the repo on GitHub: `rigora`
2. Go to **Projects → New Project → Board** — name it "Rigora Kanban"
3. Add columns: **Backlog | In Progress | Done**
4. Create four labels: `ml`, `backend`, `frontend`, `dataset`, `infra`
5. Create four milestones: `v0 — Proof of Behavior`, `v1 — Working Skeleton`, `v2 — Pro Tier Live`, `v3 — Revenue-Positive`
6. File all the issues from this README under their respective milestones

Commit message convention (same discipline as chIKEA):
```
feat(dataset): add 25 pushback conversation examples
fix(ml): correct JSONL format for Unsloth ingestion
chore(infra): add ollama systemd service config
```

***

## Financial Model (Reference)

| Free Users | Pro Users (5%) | Monthly Revenue | Total Cost | Net Profit |
|---|---|---|---|---|
| 100 | 5 | $75 | ~$112 | **-$37** |
| 500 | 25 | $375 | ~$131 | **+$244** |
| 1,000 | 50 | $750 | ~$156 | **+$594** |
| 5,000 | 250 | $3,750 | ~$351 | **+$3,399** |
| 10,000 | 500 | $7,500 | ~$595 | **+$6,906** |

Breakeven at approximately 500 free users. Infrastructure cost stays nearly flat as user count grows — the asymmetry is the entire business case.

***

## Hardware Notes

**Current (RTX 4060 8GB):** Sufficient for all v0 and v1 work. Handles 7B–8B inference in Ollama and QLoRA fine-tuning of models up to ~3B. Not production inference for 13B+.

**Phase 1 upgrade (target before v2):** RTX 5080 16GB (~$1,600). Add to existing i5-13600KF build, keep 4060 as display card to free full 5080 VRAM. Runs Gemma 4 26B A4B at ~45 tok/s.

**Phase 2 upgrade (post first revenue):** Mac Mini M4 Pro 48GB (~$1,999). Dedicated always-on inference server. Draws 15W vs. 250W for discrete GPU — critical for 24/7 cost. Handles 70B quantized models.

***

## Positioning

**Not** a mental health app. **Not** an unfiltered/NSFW model.  
**Is** an honest conversation partner that treats you like an adult.

Never use the word "therapy" in the product. The session mode is called "Reflection Mode." The value proposition is intellectual honesty, not clinical support.

The moat: a fine-tuned model trained on thousands of real challenging conversations will outperform any prompt-engineered competitor. Every month of user data widens the gap.

***

*Blueprint authored April 2026. Model landscape and hardware pricing current as of April 2026.*
