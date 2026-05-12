# 本地使用配置：ChatGPT Pro + Claude Pro

本文档记录本机使用 PPT Master 的实际配置策略：用户已有 ChatGPT Pro 和 Claude Pro 月租，可使用 Codex / Claude Code 作为 Agent，但不默认购买或接入第三方 API 代理。

## 结论

- 主流程足够使用：Codex / Claude Code 负责阅读资料、规划结构、生成 `design_spec.md` / `spec_lock.md`、逐页编写 SVG，并调用本地脚本导出 PPTX。
- ChatGPT Pro / Claude Pro 是产品订阅，不等同于 OpenAI API Key、Anthropic API Key 或任何 OpenAI-compatible API endpoint。
- 不默认使用 PackyCode 或其他 API 代理；只有用户明确提供 API Key 和 Base URL 时才配置。
- 没有生图 API 时，PPT Master 仍然可以正常生成以文字、图表、形状、图标、信息图为主的可编辑 PPT。

## 推荐工作方式

1. 使用 Codex 或 Claude Code 打开本仓库。
2. 把源文件放入 `projects/<project>/sources/`，或让 Agent 用 `project_manager.py import-sources` 导入。
3. 让 Agent 按 `skills/ppt-master/SKILL.md` 生成 PPT。
4. 如果需要图片，优先使用：
   - 用户自己提供的图片素材；
   - 零配置网络图片搜索；
   - ChatGPT Pro 网页端手动生成图片后，保存到项目 `images/` 目录；
   - 占位图，后续人工替换。
5. 只有在用户明确提供 API 凭据时，才启用 `image_gen.py` 的 `IMAGE_BACKEND=*` 生图流程。

## `.env` 策略

默认不创建 `.env`，避免误以为已有可用 API。

如以后获得 OpenAI API Key，可在仓库根目录新建 `.env`：

```env
IMAGE_BACKEND=openai
OPENAI_API_KEY=sk-xxx
OPENAI_MODEL=gpt-image-2
```

如使用 OpenAI-compatible 代理，则必须由用户明确提供代理地址：

```env
IMAGE_BACKEND=openai
OPENAI_API_KEY=代理商提供的 key
OPENAI_MODEL=gpt-image-2
OPENAI_BASE_URL=https://代理商提供的/v1
```

不要使用已废弃的通用变量：

```env
IMAGE_API_KEY=...
IMAGE_MODEL=...
IMAGE_BASE_URL=...
```

## Agent 执行约束

在没有明确 API Key 的情况下：

- 不要尝试运行 `image_gen.py` 生成图片。
- 不要把 ChatGPT Pro / Claude Pro 订阅描述为可被脚本直接调用的 API。
- 不要默认建议注册 PackyCode 或其他 API 中转服务。
- 在 Strategist 的图片资源规划中，优先选择 `user`、`web` 或 `placeholder`，避免将关键页面依赖设置为 `ai`。
- 如果用户要求高质量封面图或场景图，先说明需要额外 API Key，或建议用户用 ChatGPT Pro 网页端手动生成后放入 `images/`。

## 可直接使用的命令

```powershell
cd D:\project\ppt-master
python -m pip install -r requirements.txt
python skills\ppt-master\scripts\project_manager.py init my_deck --format ppt169
python skills\ppt-master\scripts\project_manager.py validate projects\my_deck_ppt169_YYYYMMDD
python skills\ppt-master\scripts\finalize_svg.py projects\my_deck_ppt169_YYYYMMDD
python skills\ppt-master\scripts\svg_to_pptx.py projects\my_deck_ppt169_YYYYMMDD
```

