# OpenMontage Handbook

> 🌐 **Languages:** **English** | [Tiếng Việt](README.vi.md)

Practical field guide, Windows/Linux setup reference, zero-API-key production workflow, architecture & governance breakdown, prompt cookbook, and curated open-source AI video ecosystem for [OpenMontage](https://github.com/OpenMontageInc/OpenMontage).

## Table of Contents

- [Quick Start](#quick-start)
- [Zero-API-Key Local Stack](#zero-api-key-local-stack)
- [Architecture & Production Governance](#architecture--production-governance)
- [Pipeline Decision Matrix & Output Profiles](#pipeline-decision-matrix--output-profiles)
- [Prompt Cookbook](#prompt-cookbook)
- [Supported Providers Snapshot](#supported-providers-snapshot)
- [Curated Open-Source AI Video Ecosystem](#curated-open-source-ai-video-ecosystem)
- [Credits & References](#credits--references)

## Quick Start

### Prerequisites

- **Python 3.10+**
- **FFmpeg** (available on `PATH`)
- **Node.js** — patched release on `22.x` (`22.23.4+`), `24.x` (`24.22.1+`), or `26.x` (`26.12.1+`); upstream `.node-version` targets `22.23.4`
- **AI Coding Assistant** — Claude Code (`CLAUDE.md`), Cursor (`CURSOR.md`), GitHub Copilot (`COPILOT.md`), Windsurf (`.windsurfrules`), or Codex (`CODEX.md`)

### Install & Run

**macOS / Linux (`make`):**

```bash
git clone https://github.com/OpenMontageInc/OpenMontage.git
cd OpenMontage
make setup
```

**Windows PowerShell (no `make`):**

```powershell
git clone https://github.com/OpenMontageInc/OpenMontage.git
cd OpenMontage
py -3 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install 'pip>=26.2.2'
python -m pip install -r requirements.txt
cd remotion-composer
npm install
cd ..
python -m pip install piper-tts
Copy-Item .env.example .env
```

> **Windows tip:** If `npm install` fails with `ERR_INVALID_ARG_TYPE`, run `npx --yes npm install` inside `remotion-composer`.

### Inspect Available Capabilities

Before starting a production, verify which tools and providers are active in your environment:

```bash
python -c "from tools.tool_registry import registry; import json; registry.discover(); print(json.dumps(registry.support_envelope(), indent=2))"
python -c "from tools.tool_registry import registry; import json; registry.discover(); print(json.dumps(registry.provider_menu(), indent=2))"
```

## Zero-API-Key Local Stack

You can produce complete videos with **zero paid API keys** using the default local & open-source stack:

| Capability | Free / Local Tool | Purpose |
| --- | --- | --- |
| **Narration** | Piper TTS | Offline text-to-speech voiceover |
| **Open Archival Footage** | Archive.org, NASA, Wikimedia Commons | Free public-domain & archival motion clips |
| **Free Stock Media** | Pexels, Unsplash, Pixabay | Extra stock clips and images (free developer keys) |
| **Composition (React)** | Remotion | Spring-animated scenes, stat/text cards, charts, word-level captions, TalkingHead |
| **Composition (HTML/GSAP)** | HyperFrames (`npx hyperframes`) | Kinetic typography, launch reels, rigged SVG character animation |
| **Post-Production** | FFmpeg | Encoding, subtitle burn-in, audio mixing, LUT color grading |
| **Subtitles** | Built-in | Auto-generated word-level captions |

**Three zero-key production paths:**
1. **Image-based explainer:** Piper TTS narrates the script while Remotion animates scene cards, charts, and captions.
2. **Local character animation:** SVG rigs, pose libraries, and GSAP timelines rendered via HyperFrames (`projects/<project-name>/renders/final.mp4`).
3. **Real-footage documentary montage:** Builds a CLIP-indexed corpus from Archive.org, NASA, Wikimedia Commons (plus optional Pexels/Unsplash) and cuts real motion clips into a timeline.

## Architecture & Production Governance

OpenMontage uses an **agent-first architecture** without a monolithic code orchestrator — your AI coding assistant orchestrates the pipeline by reading YAML manifests and Markdown skills:

```text
research -> proposal -> script -> scene_plan -> assets -> edit -> compose
```

### Three-Layer Knowledge Architecture

| Layer | Path | Role |
| --- | --- | --- |
| **Layer 1** | `tools/` + `pipeline_defs/` | Executable Python tools & YAML pipeline manifests ("what exists") |
| **Layer 2** | `skills/` | Stage directors, creative techniques & reviewer checklists ("how OpenMontage uses it") |
| **Layer 3** | `.agents/skills/` | External technology knowledge packs ("how underlying tech works") |

### Built-in Governance & Quality Gates

- **Human Approval Gates:** Proposal, script, scene plan, generated assets, and publish stages pause for visual sign-off via the Backlot Living Storyboard (`backlot/README.md`).
- **Pre-Compose Validation:** Blocks renders if the delivery promise is violated (e.g., requesting motion-led video with mostly static images) or slideshow risk is critical.
- **6-Dimension Slideshow Risk Scoring:** Checks repetition, decorative visuals, weak motion, shot intent, typography overreliance, and unsupported cinematic claims.
- **Post-Render Self-Review:** Runs `ffprobe` validation, extracts frames at 4 positions to detect black frames/broken overlays, checks audio silence/clipping, and verifies subtitles.
- **7-Dimension Provider Scoring:** Ranks providers by task fit (`30%`), output quality (`20%`), control (`15%`), reliability (`15%`), cost efficiency (`10%`), latency (`5%`), and continuity (`5%`).
- **Budget Controls:** Estimate -> Reserve -> Reconcile lifecycle with `observe` / `warn` / `cap` modes, `$0.50` default per-action approval threshold, and `$10` default total cap.

## Pipeline Decision Matrix & Output Profiles

### 11 Core Production Pipelines

| Pipeline | Output | Best Use Case |
| --- | --- | --- |
| **Animated Explainer** | Research-backed narrated visual explainer + music | Tutorials, technical deep-dives, education |
| **Animation** | Motion graphics, kinetic typography, animated sequences | Product promos, abstract concepts, social clips |
| **Avatar Spokesperson** | Presenter-led avatar video | Announcements, training, corporate updates |
| **Cinematic** | Trailer, teaser, and mood-driven film edit | Brand trailers, storytelling teasers |
| **Clip Factory** | Batch of ranked short-form clips from long video | Repurposing long talks/streams for Shorts/Reels |
| **Documentary Montage** | Thematic montage from CLIP-indexed open/stock footage | Video essays, mood pieces without paid video APIs |
| **Hybrid** | User source footage + AI-generated support visuals | Upgrading raw footage with graphics & B-roll |
| **Localization & Dub** | Translated subtitles and dubbed audio tracks | Multi-language content distribution |
| **Podcast Repurpose** | Highlight clips & audiograms from podcasts | Podcast marketing & social distribution |
| **Screen Demo** | Polished software screen recording walkthroughs | SaaS demos, CLI/UI tutorials, docs |
| **Talking Head** | Edited speaker-led footage with B-roll & captions | Vlogs, interviews, founder updates |

### Style Playbooks & Platform Output Profiles

- **Style Playbooks:** `Clean Professional` (SaaS/education), `Flat Motion Graphics` (TikTok/startups), `Minimalist Diagram` (technical architecture).
- **Platform Output Profiles:**
  - `YouTube Landscape` / `LinkedIn`: `1920x1080` (`16:9`) | `YouTube 4K`: `3840x2160` (`16:9`)
  - `YouTube Shorts` / `Instagram Reels` / `TikTok`: `1080x1920` (`9:16`)
  - `Instagram Feed`: `1080x1080` (`1:1`) | `Cinematic`: `2560x1080` (`21:9`)

## Prompt Cookbook

Starter prompts organized by cost tier (see [`guides/prompt-recipes.md`](./guides/prompt-recipes.md) for reusable Vietnamese/English templates and the upstream [`PROMPT_GALLERY.md`](https://github.com/OpenMontageInc/OpenMontage/blob/main/PROMPT_GALLERY.md)):

- **Zero API Keys (Explainer):** `"Create a 60-second video about the history of the internet, with narration and captions"`
- **Free Real-Footage Documentary:** `"Make a 90-second documentary montage about what a city feels like at 4am. Use real footage only, no narration, elegiac tone."`
- **Reference Video Remix:** `"I like the pacing and hook in this video. Keep that energy, but turn it into a 45-second explainer about black holes."`
- **Mid-Tier Image/Video (`~$0.15–$1.50`):** `"Make a product launch teaser for a fictional smart water bottle called AquaPulse"`
- **Full Cinematic Setup (`~$1–$3`):** `"Create a cinematic 30-second trailer for a sci-fi concept: humanity receives a warning from 1000 years in the future"`

## Supported Providers Snapshot

Full pricing, `.env` keys, and setup details are maintained in upstream [`docs/PROVIDERS.md`](https://github.com/OpenMontageInc/OpenMontage/blob/main/docs/PROVIDERS.md) and [`AGENT_GUIDE.md`](https://github.com/OpenMontageInc/OpenMontage/blob/main/AGENT_GUIDE.md):

- **Video Generation (20+):** Kling (fal.ai / direct), Atlas Cloud, Seedance 2.0/2.5, Gemini Omni Flash, Runway Gen-4, Google Veo 3.1, Grok Imagine Video, Higgsfield, MiniMax/H3, HeyGen; **Local GPU:** WAN 2.1/2.2, Hunyuan, CogVideo, LTX-Video; **Free Stock:** Pexels, Pixabay, Wikimedia Commons.
- **Image Generation (15+):** FLUX, Google Imagen 4, Grok Imagine Image, GPT Image 2, Seedream 5.0, Nano Banana 2, Recraft; **Local:** Stable Diffusion, ManimCE; **Free Stock:** Pexels, Pixabay, Unsplash.
- **Text-to-Speech (10+):** ElevenLabs, Google TTS (`700+` voices / `50+` languages), OpenAI TTS, Kling TTS, Azure Speech, DashScope/Doubao/Fish Audio; **Offline Free:** Piper TTS.
- **Music, Audio & Post-Production:** Suno AI, ElevenLabs Music/SFX, FFmpeg, Real-ESRGAN (upscale), rembg/U2Net (background removal), CodeFormer/GFPGAN (face restore), WhisperX (word-level ASR), SadTalker/MuseTalk/Wav2Lip.

## Curated Open-Source AI Video Ecosystem

Core open-source projects powering or complementing OpenMontage local workflows:

- [Remotion](https://github.com/remotion-dev/remotion) — Programmatic React video composition framework
- [FFmpeg](https://github.com/FFmpeg/FFmpeg) — Cross-platform multimedia encoding, filtering, and muxing engine
- [Piper TTS](https://github.com/rhasspy/piper) — Fast, local neural text-to-speech system
- [WhisperX](https://github.com/m-bain/whisperX) — Speech recognition with accurate word-level timestamps & diarization
- [ComfyUI](https://github.com/comfyanonymous/ComfyUI) — Node-based local GPU workflow engine for Wan, Hunyuan, FLUX & SD
- [Manim Community](https://github.com/ManimCommunity/manim) — Programmatic mathematical & technical animation engine

## Credits & References

- **Upstream Project**: [OpenMontageInc/OpenMontage](https://github.com/OpenMontageInc/OpenMontage) — Official Website: [openmontage.video](https://openmontage.video)
- **Upstream License**: OpenMontage source code is licensed under `AGPL-3.0`.
- **Handbook License**: Original documentation, Vietnamese translation ([README.vi.md](README.vi.md)), and prompt recipes in this companion repository are licensed under [MIT](./LICENSE).
