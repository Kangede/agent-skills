# Agent Skills

可供 Codex、Claude Code 及其他兼容 Agent Skills 的工具安装使用的个人 Skill 集合。

## Skills

### ask-vision

让缺少原生视觉能力的 Agent 调用外部多模态模型，分析图片、截图、图表、PDF、音频或视频。支持 OpenAI-compatible 和 Anthropic-compatible 接口。

- Skill：[skills/ask-vision](skills/ask-vision)
- 独立上游仓库：[Kangede/ask-vision](https://github.com/Kangede/ask-vision)
- 基本用法：向 Agent 提供可访问的媒体文件或 URL，并要求使用 `ask-vision` 完成 OCR、界面检查、图表提取或媒体理解。

推荐的 Agent 安装提示词：

```text
请把 ask-vision 作为全局 Agent Skill 安装或更新到当前 Agent 的用户级 Skill 目录。Skill 来源为 https://github.com/Kangede/agent-skills/tree/main/skills/ask-vision，独立上游仓库为 https://github.com/Kangede/ask-vision 。请保留 SKILL.md、scripts、references 和 agents 目录；如果已安装同名 Skill，请更新现有安装，不要生成重复或多层嵌套目录。

安装后，从当前会话正在使用的模型提供方配置中读取 OpenAI-compatible base_url，以及该 provider 的 env_key 所指向的 API Key；不要在回复、命令输出或仓库文件中显示完整密钥，也不要让我把密钥粘贴到聊天中。将 ask-vision 配置为 provider=openai、model=qwen-3.8，并把配置保存到 ask-vision 的用户级私有配置文件（权限限制为仅当前用户可读写）。最后查询 /v1/models，确认 qwen-3.8 可用，并执行一次不泄露密钥的最小验证；如果当前 Agent 无法读取会话提供方配置，请只告诉我需要设置哪些环境变量。
```

### qwen-image-local

通过 LocalLLMProxy 的 Qwen-Image-2.1 Images API 生成或编辑图片，支持透明 PNG、多个有序参考图和项目内文件输出。它只用于当前 Agent 模型不是 GPT 系列的场景；GPT 系列会话应使用 Codex 内置 `imagegen`。

- Skill：[skills/qwen-image-local](skills/qwen-image-local)
- 代理项目：[Kangede/LocalLLMProxy](https://github.com/Kangede/LocalLLMProxy)
- 基本用法：在非 GPT Agent 会话中要求生成新图片，或提供最多五张本地参考图片进行编辑。

推荐的 Agent 安装提示词：

```text
请把 qwen-image-local 作为全局 Agent Skill 安装或更新到当前 Agent 的用户级 Skill 目录。Skill 来源为 https://github.com/Kangede/agent-skills/tree/main/skills/qwen-image-local 。请完整保留 SKILL.md、scripts、references、config、assets 和 agents 目录；如果已安装同名 Skill，请更新现有安装，不要创建重复目录。

该 Skill 只应在当前 Agent 模型不是 GPT 系列时启用；如果当前是 GPT 系列 Codex 会话，应继续使用内置 imagegen，不要让两个 Skill 竞争。安装后，从当前会话的模型提供方配置读取 LocalLLMProxy 的 base_url，以及 env_key 所指向的 API Key；不要输出、复制到命令参数、写入仓库或让我在聊天中粘贴完整密钥。默认生图模型别名使用 Qwen-Image-2.1。运行 scripts/qwen_image.py check，通过 /v1/models 确认模型可用；不要绕过代理直连 SGLang。如果当前 Agent 无法读取会话配置，请只告诉我需要设置 QWEN_IMAGE_BASE_URL 和 QWEN_IMAGE_API_KEY。
```

## 手动安装

克隆本仓库后，将所需 Skill 目录复制到 Agent 的用户级 Skill 目录：

```bash
git clone https://github.com/Kangede/agent-skills.git

# Codex / 通用 Agent Skills
mkdir -p ~/.agents/skills
cp -a agent-skills/skills/ask-vision ~/.agents/skills/
cp -a agent-skills/skills/qwen-image-local ~/.agents/skills/

# Claude Code
mkdir -p ~/.claude/skills
cp -a agent-skills/skills/ask-vision ~/.claude/skills/
cp -a agent-skills/skills/qwen-image-local ~/.claude/skills/
```

也可以让支持 Skill 安装器的 Agent 直接从上面的 `skills/<name>` GitHub 路径全局安装。安装后若未立即出现，请重启对应 Agent。

## 安全提示

- 不要提交 API Key、代理凭据或本地配置文件。
- 优先从当前 Agent 的 provider 配置和环境变量读取凭据。
- 安装前可先审阅每个目录中的 `SKILL.md` 和脚本。
