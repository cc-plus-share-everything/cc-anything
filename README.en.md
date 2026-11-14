# cc+Anything

[中文](README.md) | English

Remaking viral videos in pure code with Claude Code. Each episode open-sources the **flow diagram** (how the production pipeline is designed) and the **prompts**. No paid generation calls; everything is rendered locally.

Douyin / TikTok account: cc+Anything

## Episodes

| No. | Remake of | Tech | Docs |
|---|---|---|---|
| 01 | "中华民族的救亡图存" (China's struggle for survival, 1916–1949), a 3D jigsaw-puzzle map (2:09) | Blender Python + PIL + ffmpeg | [01-jindaishi-puzzle-map](01-jindaishi-puzzle-map/README.en.md) |

## What each episode folder contains

- `README.md` / `README.en.md`: flow diagram, production process, pitfalls
- `PROMPTS.md` / `PROMPTS.en.md`: copy-ready prompts for every stage
- `img/`: screenshots of the remake

## What is not included

- The render project source (Blender scene scripts, textures, timeline) is not in this repository.
- The original video, its audio and its on-screen copy are not in this repository. The visual design and the copy belong to the original creator, and every episode's docs credit the source.

## License

The docs and prompts in this repository are shared under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): use and adapt them freely, with attribution.
