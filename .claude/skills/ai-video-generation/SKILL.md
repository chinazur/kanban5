---
name: ai-video-generation
description: "Create short AI-generated videos for the IT PMO Kanban demo, such as a board walkthrough teaser, a training explainer or an animated README/Pages hero clip, using Google Veo, Seedance 2.0, HappyHorse, Wan and other models via the inference.sh `belt` CLI. Use only when the user asks for a video about the Kanban project. Capabilities: text-to-video, image-to-video (animate a board screenshot), video editing, foley sound, upscaling, merging."
allowed-tools: Bash(belt *)
---

# AI Video Generation: IT PMO Kanban

Adapted for this repo from the `ai-video-generation` skill. It generates short promotional or training clips for the demo board with the [inference.sh](https://inference.sh) CLI (`belt`).

## Ground rules for this project

- **Third-party service.** Every `belt app run` sends the prompt and any image or video URLs to inference.sh and the model provider, and usually costs credits. **Ask the user before the first run in a session**, say which model you plan to use, and keep test runs to the cheapest or fastest variant.
- **Only fictitious content.** The board is a demo for a fictitious bank. Use `docs/screenshot.png` or other demo material only. Never upload real task data, real names, email addresses or anything from outside the repo.
- **Branding.** Never put real UOB logos or trademarks in a prompt and never imitate an official UOB system. Describe the board as a neutral "IT PMO Kanban" in a corporate blue palette.
- **No secrets.** Do not paste the `FORMSUBMIT_ENDPOINT` address or any credential into a prompt or command. `belt login` is done by the user, not by you: if it is needed, tell them to run `! belt login`.
- **Keep the repo clean.** The app is a single file with no media. Do not add video files to the root or reference them from `index.html`. If the user wants a clip in the project, put it under `docs/` (for the README or a docs page) and keep it small, since GitHub rejects files over 100 MB and the Pages workflow only publishes `index.html`. Do not commit or push unless asked.
- **Say what is AI-generated.** If a clip is published, mention in the README caption that it is AI-generated and not a recording of the app.

## Good uses for this project

| Goal | Approach |
|------|----------|
| Animated hero or teaser for the README | Image-to-video from `docs/screenshot.png` with subtle motion (cards sliding between columns, a badge pulsing). 5 to 8 seconds. |
| Training explainer: "How a task moves from Backlog to Done" | Text-to-video with a clear storyboard prompt, then merge clips. |
| Narrated demo | Generate the clip, then add narration separately. Keep the script plain and factual. |

AI video models cannot render the real UI faithfully, so any text in generated frames may be garbled. For accurate walkthroughs, prefer recording the real app with Playwright and use AI video only for stylised intros or backgrounds.

## Prompt template

Keep prompts concrete and short:

```
A clean, modern Kanban board on a laptop screen, four columns labelled Backlog,
In Progress, Blocked and Done, in a corporate blue palette. Cards slide smoothly
from one column to the next. Calm, professional, soft lighting, no logos.
```

Add camera direction (static, slow push-in), duration and aspect ratio (16:9 for README and docs).

## Quick start

```bash
# One-time, run by the user: belt login
belt app run google/veo-3-1-fast --input '{"prompt": "<prompt from the template>"}'
```

Requires the inference.sh CLI (`belt`). If it is missing, tell the user and point them at the install instructions at https://raw.githubusercontent.com/inference-sh/skills/refs/heads/main/cli-install.md. Do not pipe remote scripts into a shell yourself.

## Models

| Use | Model | App ID |
|-----|-------|--------|
| Fast drafts and tests | Veo 3.1 Fast | `google/veo-3-1-fast` |
| Final quality text-to-video | Veo 3.1 | `google/veo-3-1` |
| Cheap drafts | WAN-T2V | `pruna/wan-t2v` |
| Animate the screenshot | Wan 2.5 I2V, Seedance 2.0 | `falai/wan-2-5-i2v`, `bytedance/seedance-2-0` |
| Stylised, physically realistic motion | HappyHorse | `alibaba/happyhorse-1-0-t2v` |
| Edit an existing clip | HappyHorse Edit | `alibaba/happyhorse-1-0-video-edit` |
| Add sound effects | HunyuanVideo Foley | `infsh/hunyuanvideo-foley` |
| Upscale | Topaz Upscaler | `falai/topaz-video-upscaler` |
| Join clips | Media Merger | `infsh/media-merger` |

Browse everything with `belt app list --category video`.

## Examples

### Animate the board screenshot

The model needs a reachable URL. `docs/screenshot.png` is public once pushed: use its raw URL, `https://raw.githubusercontent.com/chinazur/kanban5/main/docs/screenshot.png`.

```bash
belt app run bytedance/seedance-2-0 --input '{
  "image": "https://raw.githubusercontent.com/chinazur/kanban5/main/docs/screenshot.png",
  "prompt": "subtle camera push-in, task cards gently glow and slide to the next column",
  "generate_audio": false,
  "duration": 6
}'
```

### Training explainer clip

```bash
belt app run google/veo-3-1-fast --input '{
  "prompt": "Explainer-style animation: a task card moves from Backlog to In Progress to Done on a blue Kanban board, an Overdue badge fades out as it reaches Done. Flat design, calm, no logos, no text overlays."
}'
```

### Merge clips

```bash
belt app run infsh/media-merger --input '{
  "videos": ["https://clip1.mp4", "https://clip2.mp4"],
  "transition": "fade"
}'
```

## After generating

1. Download the result to the scratchpad directory, not the repo, and review it with the user.
2. Only after they approve, copy it to `docs/` and reference it in the README, using a short, optimised file.
3. Report the model used, the prompt and the credits or time spent.

Docs: [Running Apps](https://inference.sh/docs/apps/running)
