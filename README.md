# Agent Skills

可供 Codex、Claude Code 及其他兼容 Agent Skills 的工具安装使用的个人 Skill 集合。

## Skills

### ask-vision

让缺少原生视觉能力的 Agent 调用外部多模态模型，分析图片、截图、图表、PDF、音频或视频。支持 OpenAI-compatible 和 Anthropic-compatible 接口。

- Skill submodule：[skills/ask-vision](skills/ask-vision)
- 独立源仓库：[Kangede/ask-vision](https://github.com/Kangede/ask-vision)
- 基本用法：向 Agent 提供可访问的媒体文件或 URL，并要求使用 `ask-vision` 完成 OCR、界面检查、图表提取或媒体理解。

推荐的 Agent 安装提示词：

```text
请把 ask-vision 作为全局 Agent Skill 安装或更新到当前 Agent 的用户级 Skill 目录。Skill 来源为 https://github.com/Kangede/ask-vision。请保留 SKILL.md、scripts、references 和 agents 目录；如果已安装同名 Skill，请更新现有安装，不要生成重复或多层嵌套目录。

安装后，复用当前会话正在使用的模型服务地址和认证凭据，不要假设当前客户端采用哪一种 API 协议或固定配置字段：

- Codex 或其他使用 provider 配置的 Agent，通常可从当前 provider 的服务地址字段以及其凭据环境变量配置中获取。
- Claude Code 或其他 Anthropic-compatible 客户端，通常可从 ANTHROPIC_BASE_URL 与 ANTHROPIC_AUTH_TOKEN、ANTHROPIC_API_KEY 或当前配置的凭据辅助程序中获取。
- 其他 Agent 应读取其当前活动模型提供方的等价连接配置。

不要修改当前客户端原有的模型连接配置，也不要在回复、命令参数、日志或仓库文件中显示完整凭据，更不要让我把凭据粘贴到聊天中。

ask-vision 自身配置使用 provider=openai、model=qwen-3.8，并复用上述会话服务地址和凭据调用 OpenAI-compatible 接口；当前客户端即使使用 Anthropic-compatible 协议，也不影响 ask-vision 使用同一服务地址。将配置保存到 ask-vision 的用户级私有配置文件，并限制为仅当前用户可读写。

最后查询 /v1/models，确认 qwen-3.8 可用，并执行一次不泄露凭据的最小验证。如果当前 Agent 无法读取活动会话的连接信息，请只说明需要设置 ASK_VISION_BASE_URL 和 ASK_VISION_API_KEY，不要要求用户在聊天中提供具体值。
```

### qwen-image-local

通过当前会话配置的 OpenAI-compatible Images API 使用 Qwen-Image-2.1 生成或编辑图片，支持透明 PNG、多个有序参考图和项目内文件输出。它只用于当前 Agent 模型不是 GPT 系列的场景；GPT 系列会话应使用 Codex 内置 `imagegen`。

- 来源 submodule：[skills/qwen-image-local](skills/qwen-image-local)
- 独立源仓库：[Kangede/qwen-image-local](https://github.com/Kangede/qwen-image-local)
- Skill 目录：`skills/qwen-image-local`
- 基本用法：在非 GPT Agent 会话中要求生成新图片，或提供最多五张本地参考图片进行编辑。

推荐的 Agent 安装提示词：

```text
请把 qwen-image-local 作为全局 Agent Skill 安装或更新到当前 Agent 的用户级 Skill 目录。Skill 来源为 https://github.com/Kangede/qwen-image-local。请完整保留 SKILL.md、scripts、references、config、assets 和 agents 目录；如果已安装同名 Skill，请更新现有安装，不要创建重复目录。

该 Skill 只应在当前 Agent 模型不是 GPT 系列时启用；如果当前是 GPT 系列 Codex 会话，应继续使用内置 imagegen，不要让两个 Skill 竞争。

安装后，复用当前会话正在使用的模型服务地址和认证凭据，不要假设当前客户端采用哪一种 API 协议或固定配置字段：

- Codex 或其他使用 provider 配置的 Agent，通常可从当前 provider 的服务地址字段以及其凭据环境变量配置中获取。
- Claude Code 或其他 Anthropic-compatible 客户端，通常可从 ANTHROPIC_BASE_URL 与 ANTHROPIC_AUTH_TOKEN、ANTHROPIC_API_KEY 或当前配置的凭据辅助程序中获取。
- 其他 Agent 应读取其当前活动模型提供方的等价连接配置。

不要修改当前客户端原有的模型连接配置，也不要在回复、命令参数、日志或仓库文件中显示完整凭据，更不要让我把凭据粘贴到聊天中。qwen-image-local 会复用同一服务地址访问 Images API，并自行规范化所需路径；当前客户端使用 OpenAI-compatible 还是 Anthropic-compatible 协议，不影响该地址的复用。

默认生图模型别名使用 Qwen-Image-2.1。安装完成后运行 scripts/qwen_image.py check，通过 /v1/models 确认该模型可用，不要替换为其他服务地址。如果当前 Agent 无法读取活动会话的连接信息，请只说明需要设置 QWEN_IMAGE_BASE_URL 和 QWEN_IMAGE_API_KEY，不要要求用户在聊天中提供具体值。
```

## 手动安装

克隆本仓库后，将所需 Skill 目录复制到 Agent 的用户级 Skill 目录：

```bash
git clone --recurse-submodules https://github.com/Kangede/agent-skills.git

# Codex / 通用 Agent Skills
mkdir -p ~/.agents/skills
cp -a agent-skills/skills/ask-vision ~/.agents/skills/
cp -a agent-skills/skills/qwen-image-local ~/.agents/skills/

# Claude Code
mkdir -p ~/.claude/skills
cp -a agent-skills/skills/ask-vision ~/.claude/skills/
cp -a agent-skills/skills/qwen-image-local ~/.claude/skills/
```

也可以让支持 Skill 安装器的 Agent 直接从各自的源 GitHub 路径全局安装。安装后若未立即出现，请重启对应 Agent。

## 安全提示

- 不要提交 API Key、接口凭据或本地配置文件。
- 优先从当前 Agent 的 provider 配置和环境变量读取凭据。
- 安装前可先审阅每个目录中的 `SKILL.md` 和脚本。
