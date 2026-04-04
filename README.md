# Seedance 2.0 Agent Skills

Agent skills for generating videos with [Seedance 2.0](https://console.volcengine.com/ark/region:ark+cn-beijing/model/detail?Id=doubao-seedance-2-0) and Seedance 2.0 fast models on the Volcengine Ark platform.

## Install

```bash
npx skills add mercallureAI/seedance
```

Then select the skill(s) you want and the agent(s) to install them on.

## Skills

### `seedance`

For use with the **meme-master CLI**. Covers all CLI commands (`video generate`, `video list`, `video get`), every generation mode, flags, validation rules, and prompt techniques.

```bash
npx skills add mercallureAI/seedance --skill seedance
```

### `seedance-api`

For **direct API usage** (curl, Python SDK, etc.) without any CLI wrapper. Covers all REST endpoints, request/response schemas, async polling, and code recipes.

```bash
npx skills add mercallureAI/seedance --skill seedance-api
```

## What's Included

Both skills are fully self-contained and can be installed independently.

```
skills/
├── seedance/                    CLI-based (meme-master)
│   ├── SKILL.md                 Core skill instructions
│   └── references/
│       ├── cli-reference.md     Complete flag & command reference
│       ├── input-specs.md       Input constraints & validation rules
│       ├── examples.md          CLI recipes for every generation mode
│       └── prompt-tips.md       Advanced prompt techniques
│
└── seedance-api/                Direct API (curl / Python SDK)
    ├── SKILL.md                 Core skill instructions
    └── references/
        ├── api-reference.md     Endpoint specs & response schemas
        ├── input-specs.md       Input constraints & resolution tables
        ├── examples.md          Code recipes for every generation mode
        └── prompt-tips.md       Advanced prompt techniques
```

## Supported Agents

These skills work with any agent that supports the [Agent Skills](https://github.com/vercel-labs/skills) format, including Claude Code, Cursor, Codex, Gemini CLI, and others.

## Prerequisites

You need an `ARK_API_KEY` from the [Volcengine Ark console](https://console.volcengine.com/ark/region:ark+cn-beijing/apiKey). Set it as an environment variable:

```bash
export ARK_API_KEY="your-key-here"
```

No credentials are stored in or required by these skill files.

## Models

| Model | Model ID | Best For |
|---|---|---|
| Seedance 2.0 fast | `doubao-seedance-2-0-fast-260128` | Speed & cost efficiency |
| Seedance 2.0 | `doubao-seedance-2-0-260128` | Maximum quality |

Both models support: text-to-video, image-to-video (first frame, first+last frame), multimodal reference, video editing, video extension, audio-synced generation, and web search enhancement.

## License

MIT
