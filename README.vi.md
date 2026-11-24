# Cẩm Nang Thực Chiến OpenMontage (OpenMontage Handbook)

> 🌐 **Ngôn ngữ:** [English](README.md) | **Tiếng Việt**

Tài liệu hướng dẫn thực hành, cài đặt trên Windows/Linux, quy trình dựng video **0 đồng API key**, phân tích kiến trúc & cơ chế kiểm soát chất lượng, bộ prompt mẫu và hệ sinh thái công cụ AI Video mã nguồn mở dành cho [OpenMontage](https://github.com/OpenMontageInc/OpenMontage).

## Mục lục

- [Cài đặt Nhanh (Quick Start)](#cài-đặt-nhanh-quick-start)
- [Quy trình 0 API Key (Chạy Cục Bộ & Nguồn Mở)](#quy-trình-0-api-key-chạy-cục-bộ--nguồn-mở)
- [Kiến trúc & Quản trị Sản xuất (Governance)](#kiến-trúc--quản-trị-sản-xuất-governance)
- [Ma trận Chọn Pipeline & Chuẩn Xuất Video](#ma-trận-chọn-pipeline--chuẩn-xuất-video)
- [Bộ Prompt Thực chiến (Prompt Cookbook)](#bộ-prompt-thực-chiến-prompt-cookbook)
- [Tổng quan Nhà cung cấp Hỗ trợ](#tổng-quan-nhà-cung-cấp-hỗ-trợ)
- [Hệ sinh thái AI Video Mã nguồn mở Liên quan](#hệ-sinh-thái-ai-video-mã-nguồn-mở-liên-quan)
- [Credits & References (Nguồn tham khảo & Bản quyền)](#credits--references-nguồn-tham-khảo--bản-quyền)

## Cài đặt Nhanh (Quick Start)

### Yêu cầu hệ thống

- **Python 3.10+**
- **FFmpeg** (đã cấu hình trong biến môi trường `PATH`)
- **Node.js** — bản vá thuộc nhánh `22.x` (`22.23.4+`), `24.x` (`24.22.1+`), hoặc `26.x` (`26.12.1+`); file `.node-version` của repo gốc dùng `22.23.4`
- **Trợ lý lập trình AI (AI Coding Assistant)** — Claude Code (`CLAUDE.md`), Cursor (`CURSOR.md`), GitHub Copilot (`COPILOT.md`), Windsurf (`.windsurfrules`), hoặc Codex (`CODEX.md`)

### Cài đặt & Khởi chạy

**macOS / Linux (dùng `make`):**

```bash
git clone https://github.com/OpenMontageInc/OpenMontage.git
cd OpenMontage
make setup
```

**Windows PowerShell (không cần `make`):**

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

> **Mẹo trên Windows:** Nếu `npm install` gặp lỗi `ERR_INVALID_ARG_TYPE`, hãy chạy `npx --yes npm install` bên trong thư mục `remotion-composer`.

### Kiểm tra Công cụ & Provider Đang Khả Dụng

```bash
python -c "from tools.tool_registry import registry; import json; registry.discover(); print(json.dumps(registry.support_envelope(), indent=2))"
python -c "from tools.tool_registry import registry; import json; registry.discover(); print(json.dumps(registry.provider_menu(), indent=2))"
```

## Quy trình 0 API Key (Chạy Cục Bộ & Nguồn Mở)

Bạn có thể tạo video hoàn chỉnh **không cần trả phí API** nhờ bộ công cụ cục bộ mặc định sau khi chạy setup:

| Chức năng | Công cụ Miễn phí / Cục bộ | Vai trò |
| --- | --- | --- |
| **Thuyết minh (TTS)** | Piper TTS | Đọc lời thoại offline tự nhiên, hoàn toàn miễn phí |
| **Tư liệu Mở (Footage)** | Archive.org, NASA, Wikimedia Commons | Video tư liệu lịch sử, khoa học, giáo dục công khai |
| **Kho Stock Miễn phí** | Pexels, Unsplash, Pixabay | Hình ảnh & video stock bổ sung (API key miễn phí) |
| **Dựng hình (React)** | Remotion | Chuyển động lò xo, thẻ chữ/số liệu, biểu đồ, phụ đề từng từ, TalkingHead |
| **Dựng hình (HTML/GSAP)** | HyperFrames (`npx hyperframes`) | Typography động, promo sản phẩm, hoạt hình nhân vật SVG |
| **Hậu kỳ (Post-Production)** | FFmpeg | Render, gắn phụ đề cứng, trộn âm thanh, chỉnh màu LUT |
| **Phụ đề** | Tích hợp sẵn | Tự động tạo phụ đề khớp từng từ |

**Ba hướng sản xuất video không tốn phí API:**
1. **Video giải thích bằng hình ảnh:** Piper TTS đọc kịch bản, Remotion dựng cảnh chuyển động, biểu đồ và phụ đề.
2. **Hoạt hình nhân vật cục bộ:** Dựng rig SVG, thư viện dáng và timeline GSAP render qua HyperFrames ra `projects/<project-name>/renders/final.mp4`.
3. **Phim tài liệu dựng từ cảnh quay thật:** Tự lập chỉ mục CLIP từ Archive.org, NASA, Wikimedia Commons (kèm Pexels/Unsplash tùy chọn) rồi cắt ghép thành timeline hoàn chỉnh.

## Kiến trúc & Quản trị Sản xuất (Governance)

OpenMontage dùng kiến trúc **agent-first**: không có bộ điều phối code cứng — chính AI coding assistant của bạn đóng vai trò đạo diễn dựa trên manifest YAML và skill Markdown:

```text
research -> proposal -> script -> scene_plan -> assets -> edit -> compose
```

### Kiến trúc Tri thức 3 Lớp

| Lớp | Đường dẫn | Vai trò |
| --- | --- | --- |
| **Lớp 1** | `tools/` + `pipeline_defs/` | Công cụ Python thực thi & định nghĩa pipeline YAML ("hệ thống có gì") |
| **Lớp 2** | `skills/` | Hướng dẫn từng công đoạn, kỹ thuật dựng & checklist duyệt ("cách OpenMontage sử dụng") |
| **Lớp 3** | `.agents/skills/` | Bộ tri thức kỹ thuật bên ngoài ("công nghệ nền tảng hoạt động ra sao") |

### Các Cổng Kiểm soát Chất lượng & Ngân sách

- **Cổng duyệt thủ công (Human Approval Gates):** Dừng xin xác nhận ở các bước đề xuất, kịch bản, phân cảnh, tài nguyên sinh ra và xuất bản thông qua bảng trực quan Backlot (`backlot/README.md`).
- **Kiểm tra trước khi Render (Pre-Compose Validation):** Chặn render nếu sai cam kết đầu ra (ví dụ: yêu cầu video chuyển động nhưng 80% là ảnh tĩnh) hoặc điểm rủi ro slideshow quá cao.
- **Chấm điểm Rủi ro Slideshow 6 Chiều:** Phát hiện lặp cảnh, ảnh trang trí vô nghĩa, thiếu chuyển động, thiếu ý đồ cảnh quay, lạm dụng chữ hoặc thiếu tính điện ảnh.
- **Tự kiểm tra sau Render (Post-Render Self-Review):** Chạy `ffprobe`, trích xuất khung hình tại 4 vị trí kiểm tra khung đen/lỗi lớp phủ, đo ngưỡng im lặng/vỡ tiếng và kiểm tra phụ đề.
- **Chấm điểm Chọn Provider 7 Chiều:** Mức độ khớp tác vụ (`30%`), chất lượng đầu ra (`20%`), khả năng kiểm soát (`15%`), độ ổn định (`15%`), hiệu quả chi phí (`10%`), độ trễ (`5%`), tính nhất quán (`5%`).
- **Kiểm soát Ngân sách:** Quy trình `Estimate -> Reserve -> Reconcile` với 3 chế độ `observe` / `warn` / `cap`, mặc định xin phép khi thao tác vượt `$0.50` và trần tổng `$10`.

## Ma trận Chọn Pipeline & Chuẩn Xuất Video

### 11 Pipeline Sản xuất Chính

| Pipeline | Sản phẩm đầu ra | Phù hợp nhất cho |
| --- | --- | --- |
| **Animated Explainer** | Video giải thích có nghiên cứu web, lời bình, hình ảnh, nhạc | Nội dung giáo dục, tutorial, phân tích chuyên đề |
| **Animation** | Đồ họa chuyển động, kinetic typography | Mạng xã hội, giới thiệu sản phẩm, khái niệm trừu tượng |
| **Avatar Spokesperson** | Video người dẫn ảo (avatar) | Thông báo nội bộ, đào tạo, bản tin |
| **Cinematic** | Trailer, teaser giàu nhịp điệu và cảm xúc | Phim thương hiệu, teaser quảng bá |
| **Clip Factory** | Tách hàng loạt clip ngắn xếp hạng từ video dài | Tái sử dụng livestream/hội thảo cho Shorts/Reels |
| **Documentary Montage** | Dựng phim tư liệu từ kho stock/lưu trữ mở theo chỉ mục CLIP | Video essay, phim tư liệu không cần API trả phí |
| **Hybrid** | Cảnh quay có sẵn kết hợp đồ họa AI hỗ trợ | Nâng cấp video quay thô bằng B-roll & đồ họa |
| **Localization & Dub** | Dịch phụ đề và lồng tiếng đa ngôn ngữ | Mở rộng kênh ra thị trường quốc tế |
| **Podcast Repurpose** | Trích đoạn highlight & audiogram từ podcast | Quảng bá podcast trên mạng xã hội |
| **Screen Demo** | Video quay màn hình hướng dẫn phần mềm hoàn chỉnh | Demo SaaS, hướng dẫn CLI/UI, tài liệu |
| **Talking Head** | Biên tập video người nói kèm B-roll & phụ đề | Vlog, phỏng vấn, cập nhật sản phẩm |

### Style Playbooks & Hồ sơ Nền tảng (Platform Profiles)

- **Style Playbooks:** `Clean Professional` (doanh nghiệp/giáo dục), `Flat Motion Graphics` (TikTok/startup), `Minimalist Diagram` (kiến trúc kỹ thuật).
- **Khung hình chuẩn tích hợp:**
  - `YouTube Landscape` / `LinkedIn`: `1920x1080` (`16:9`) | `YouTube 4K`: `3840x2160` (`16:9`)
  - `YouTube Shorts` / `Instagram Reels` / `TikTok`: `1080x1920` (`9:16`)
  - `Instagram Feed`: `1080x1080` (`1:1`) | `Cinematic`: `2560x1080` (`21:9`)

## Bộ Prompt Thực chiến (Prompt Cookbook)

Các câu lệnh mẫu phân theo mức chi phí (xem đầy đủ mẫu song ngữ tại [`guides/prompt-recipes.md`](./guides/prompt-recipes.md) và thư viện gốc [`PROMPT_GALLERY.md`](https://github.com/OpenMontageInc/OpenMontage/blob/main/PROMPT_GALLERY.md)):

- **0 API Key (Explainer):** `"Create a 60-second video about the history of the internet, with narration and captions"`
- **Tư liệu thật miễn phí (Documentary):** `"Make a 90-second documentary montage about what a city feels like at 4am. Use real footage only, no narration, elegiac tone."`
- **Làm theo video tham khảo:** `"I like the pacing and hook in this video. Keep that energy, but turn it into a 45-second explainer about black holes."`
- **Mức trung bình (`~$0.15–$1.50`):** `"Make a product launch teaser for a fictional smart water bottle called AquaPulse"`
- **Đầy đủ dịch vụ Cloud (`~$1–$3`):** `"Create a cinematic 30-second trailer for a sci-fi concept: humanity receives a warning from 1000 years in the future"`

## Tổng quan Nhà cung cấp Hỗ trợ

Chi tiết bảng giá, biến môi trường `.env` và hướng dẫn cấu hình xem tại [`docs/PROVIDERS.md`](https://github.com/OpenMontageInc/OpenMontage/blob/main/docs/PROVIDERS.md) và [`AGENT_GUIDE.md`](https://github.com/OpenMontageInc/OpenMontage/blob/main/AGENT_GUIDE.md):

- **Sinh Video (20+):** Kling (fal.ai / trực tiếp), Atlas Cloud, Seedance 2.0/2.5, Gemini Omni Flash, Runway Gen-4, Google Veo 3.1, Grok Imagine Video, Higgsfield, MiniMax/H3, HeyGen; **GPU Cục bộ:** WAN 2.1/2.2, Hunyuan, CogVideo, LTX-Video; **Stock Miễn phí:** Pexels, Pixabay, Wikimedia Commons.
- **Sinh Ảnh (15+):** FLUX, Google Imagen 4, Grok Imagine Image, GPT Image 2, Seedream 5.0, Nano Banana 2, Recraft; **Cục bộ:** Stable Diffusion, ManimCE; **Stock Miễn phí:** Pexels, Pixabay, Unsplash.
- **Chuyển Văn bản thành Giọng nói (10+):** ElevenLabs, Google TTS (`700+` giọng / `50+` ngôn ngữ), OpenAI TTS, Kling TTS, Azure Speech, DashScope/Doubao/Fish Audio; **Offline Miễn phí:** Piper TTS.
- **Nhạc, Âm thanh & Hậu kỳ:** Suno AI, ElevenLabs Music/SFX, FFmpeg, Real-ESRGAN (upscale), rembg/U2Net (tách nền), CodeFormer/GFPGAN (phục hồi khuôn mặt), WhisperX (bóc băng khớp từng từ), SadTalker/MuseTalk/Wav2Lip.

## Hệ sinh thái AI Video Mã nguồn mở Liên quan

Các dự án mã nguồn mở cốt lõi vận hành hoặc bổ trợ trực tiếp cho quy trình cục bộ của OpenMontage:

- [Remotion](https://github.com/remotion-dev/remotion) — Framework dựng video bằng React theo lập trình
- [FFmpeg](https://github.com/FFmpeg/FFmpeg) — Bộ công cụ mã hóa, cắt ghép và xử lý đa phương tiện đa nền tảng
- [Piper TTS](https://github.com/rhasspy/piper) — Hệ thống Text-to-Speech neural chạy cục bộ tốc độ cao
- [WhisperX](https://github.com/m-bain/whisperX) — Nhận dạng giọng nói kèm mốc thời gian chính xác từng từ & tách người nói
- [ComfyUI](https://github.com/comfyanonymous/ComfyUI) — Công cụ dựng luồng node trên GPU cục bộ cho Wan, Hunyuan, FLUX & SD
- [Manim Community](https://github.com/ManimCommunity/manim) — Thư viện tạo hoạt họa toán học và biểu đồ kỹ thuật bằng Python

## Credits & References (Nguồn tham khảo & Bản quyền)

- **Dự án gốc (Upstream)**: [OpenMontageInc/OpenMontage](https://github.com/OpenMontageInc/OpenMontage) — Trang chủ: [openmontage.video](https://openmontage.video)
- **Giấy phép dự án gốc**: Mã nguồn OpenMontage phát hành theo giấy phép `AGPL-3.0`.
- **Giấy phép bộ cẩm nang này**: Nội dung biên soạn, bản dịch tiếng Việt ([README.md](README.md)) và bộ prompt mẫu trong repository này được chia sẻ theo giấy phép [MIT](./LICENSE).
