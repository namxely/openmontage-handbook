# OpenMontage Prompt Recipes & Budget Starter Guide

> [English README](../README.md) | [Tiếng Việt README](../README.vi.md)

Reusable prompt templates and `.env` cost-tier setups for common OpenMontage production workflows.

---

## 1. Budget Tier Starter Reference (`.env`)

| Tier | Typical Cost / Video | Recommended Keys / Tools | Best Output Path |
| --- | --- | --- | --- |
| **Tier 0 — Zero Keys** | `$0.00` | Piper TTS + Remotion / HyperFrames + FFmpeg + Archive.org / NASA / Wikimedia | Animated explainers, SVG character animation, archival documentaries |
| **Tier 1 — Free Stock** | `$0.00` | Tier 0 + free `PEXELS_API_KEY`, `UNSPLASH_ACCESS_KEY`, `PIXABAY_API_KEY` | Real-footage montages, B-roll essays, talking-head enrichment |
| **Tier 2 — Low-Cost Cloud** | `~$0.15–$1.50` | `FAL_KEY` or `ATLASCLOUD_API_KEY` (FLUX / Kling / Seedance) + Google/OpenAI TTS | Stylized short-form animations, product teasers |
| **Tier 3 — Full Studio** | `~$1.00–$3.00` | Veo 3.1 / Kling + ElevenLabs Voice & Music / Suno AI + Remotion | High-polish trailers, multi-language commercial localization |

---

## 2. Prompt Templates (EN & VI)

### Recipe A: Short-Form 45–60s Tech Explainer (9:16 Shorts / TikTok — Zero Keys)

**English:**
```text
Create a 50-second animated explainer in 9:16 vertical format (TikTok / YouTube Shorts) explaining how vector databases power RAG search.
Use Minimalist Diagram style, Piper TTS narration, word-level captions, and Remotion spring animations. No paid API keys.
```

**Tiếng Việt (gợi ý giao việc cho agent):**
```text
Hãy tạo một video giải thích dài 50 giây định dạng dọc 9:16 (TikTok / YouTube Shorts) về cách Vector Database hoạt động trong hệ thống RAG.
Dùng phong cách Minimalist Diagram, giọng đọc Piper TTS offline, phụ đề từng từ và render bằng Remotion. Không sử dụng API key trả phí.
```

### Recipe B: Real-Footage Documentary Montage (16:9 — Zero Keys)

**English:**
```text
Make a 75-second documentary montage in 16:9 about late-night urban architecture and rain.
Use real footage only from Archive.org, Wikimedia Commons, and Pexels. No narration, calm instrumental pacing, LUT color grading.
```

**Tiếng Việt:**
```text
Dựng một video tư liệu 75 giây tỉ lệ 16:9 về kiến trúc đô thị ban đêm trong mưa.
Chỉ dùng cảnh quay thật (real footage only) từ Archive.org, Wikimedia Commons và Pexels. Không lời thoại, nhịp dựng chậm rãi kèm chỉnh màu LUT.
```

### Recipe C: Software Product Demo Walkthrough (Screen Demo)

**English:**
```text
Produce a 60-second Screen Demo walkthrough using my local recording in assets/demo.mp4.
Use Clean Professional style, tight pacing, callout cards for the 3 core features, background ducking music, and 1080p YouTube output.
```

**Tiếng Việt:**
```text
Dựng video Screen Demo dài 60 giây từ bản quay màn hình có sẵn tại assets/demo.mp4.
Dùng phong cách Clean Professional, nhịp cắt gọn, thêm thẻ nổi bật 3 tính năng chính, tự giảm âm lượng nhạc nền khi có lời thoại và xuất chuẩn YouTube 1080p.
```

### Recipe D: Podcast-to-Shorts Repurposing (Clip Factory)

**English:**
```text
Run Clip Factory on assets/episode-01.mp4 to extract the top 3 high-retention vertical (9:16) clips between 30 and 55 seconds each,
with WhisperX word-level captions and clean audio normalization.
```

**Tiếng Việt:**
```text
Chạy pipeline Clip Factory trên tệp assets/episode-01.mp4 để trích xuất 3 đoạn clip dọc (9:16) hấp dẫn nhất dài 30–55 giây,
gắn phụ đề chính xác từng từ bằng WhisperX và chuẩn hóa âm lượng.
```
