---
name: minimax-lyrics
description: >
  使用 Minimax API 生成歌词。适用于用户请求创作歌曲歌词、续写现有歌词、编辑或修改歌词内容的场景。触发场景包括：用户说"帮我写首歌"、"生成歌词"、"续写歌词"、"帮我创作一首关于X的歌"、"写一个歌词"、"根据这个主题写首歌"、"创作歌词"等。确保在用户表达任何歌曲创作、歌词生成、歌词续写/编辑需求时触发此 skill。
---

# Minimax 歌词生成 Skill

此 skill 使用 Minimax API 生成歌词，支持完整歌曲创作和歌词续写/编辑功能。

## 前置要求

确保环境变量 `MINIMAX_API_KEY` 已设置，API Key 可在 [Minimax 平台](https://platform.minimaxi.com/user-center/basic-information/interface-key) 获取。

## 使用模式

### 模式 1：写完整歌曲 (write_full_song)

当用户提供歌曲主题、风格或特定要求时使用此模式。

**API 调用参数：**

- `mode`: "write_full_song"
- `prompt`: 描述歌曲主题、风格、情绪等内容（最多 2000 字符）
- `title`:（可选）指定歌曲标题

**示例 prompt：**

- "一首关于夏日海边的轻快情歌"
- "关于追逐梦想的励志歌曲，风格激昂"
- "写一首分手后释怀的情感民谣"

### 模式 2：编辑/续写歌词 (edit)

当用户提供现有歌词并希望续写或修改时使用此模式。

**API 调用参数：**

- `mode`: "edit"
- `lyrics`: 现有歌词内容（最多 3500 字符）
- `prompt`: 编辑方向或续写要求
- `title`:（可选）指定歌曲标题

## 执行步骤

### Step 1: 调用 Minimax API

优先使用可用的 MCP 工具（如 `MiniMaxCreator_music_generation`），如果没有专门的歌词生成工具，使用 `Bash` 工具通过 curl 调用 API。

**请求端点：** `https://api.minimaxi.com/v1/lyrics_generation`

**请求方法：** POST

**请求头：**

- `Content-Type`: application/json
- `Authorization`: Bearer {MINIMAX_API_KEY}

**使用 curl 调用示例：**

```bash
curl -s -X POST "https://api.minimaxi.com/v1/lyrics_generation" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $MINIMAX_API_KEY" \
  -d '{
    "mode": "write_full_song",
    "prompt": "你的歌曲主题描述",
    "title": "可选的歌曲标题"
  }'
```

**请求体示例 (write_full_song):**

```json
{
  "mode": "write_full_song",
  "prompt": "一首关于夏日海边的轻快情歌",
  "title": "夏日海风"
}
```

**请求体示例 (edit):**

```json
{
  "mode": "edit",
  "lyrics": "[Verse 1]\n阳光洒满了海面\n...",
  "prompt": "续写第二段verse，保持同样的风格",
  "title": "夏日海风"
}
```

### Step 2: 解析响应

API 返回的 JSON 包含：

- `song_title`: 生成的歌名
- `style_tags`: 风格标签（逗号分隔）
- `lyrics`: 生成的歌词（包含结构标签）

### Step 3: 展示结果

以清晰格式展示歌词给用户：

```
🎵 歌名：{song_title}
🏷️ 风格：{style_tags}

{lyrics}
```

### Step 4: 询问保存

在展示完歌词后，主动询问用户是否需要保存：

"需要我将歌词保存到文件吗？支持以下格式：

- `.txt` - 纯文本格式
- `.lrc` - LRC 歌词格式（带时间标签）
- `.md` - Markdown 格式

请告诉我保存路径和格式，例如：`/path/to/song.txt`"

### Step 5: 保存文件

根据用户指定的路径和格式保存文件。

## 错误处理

| status_code | 含义           | 处理方式                      |
| ----------- | -------------- | ----------------------------- |
| 0           | 成功           | 正常展示结果                  |
| 1002        | 触发限流       | 提示用户稍后再试              |
| 1004        | 鉴权失败       | 检查 MINIMAX_API_KEY 是否正确 |
| 1008        | 余额不足       | 提示用户充值                  |
| 1026        | 包含敏感内容   | 提示用户修改 prompt           |
| 2013        | 参数异常       | 检查输入参数                  |
| 2049        | 无效的 api key | 检查 MINIMAX_API_KEY          |

如果 API 调用失败，向用户说明错误原因并提供解决建议。

## 响应格式说明

生成的歌词包含以下结构标签：

- `[Intro]` - 前奏
- `[Verse]` - 主歌
- `[Pre-Chorus]` - 预副歌
- `[Chorus]` - 副歌
- `[Hook]` - 钩子
- `[Drop]` - Drop 部分
- `[Bridge]` - 桥段
- `[Solo]` - 独奏
- `[Build-up]` - 渐进
- `[Instrumental]` - 器乐
- `[Breakdown]` - 分解
- `[Break]` - 停顿
- `[Interlude]` - 间奏
- `[Outro]` - 尾奏

歌词可直接用于 Minimax 音乐生成。

## 示例对话

**用户：** 帮我写一首关于星空的浪漫歌曲

**Claude：**

```
🎵 歌名：星空下的誓言
🏷️ 风格：Romantic, Dreamy, Pop, Soft vocals

[Intro]
(Ooh-ooh-ooh)
(Yeah)

[Verse 1]
星光洒落在这寂静夜晚
你抬头望着天空许愿
我看着你眼中的光芒
像是银河倒映在湖面

[Pre-Chorus]
微风轻轻吹过脸庞
这一刻多么美好
想要时间就此停留

[Chorus]
在这星空下 我向你许诺
牵着你的手 永远不分离
每一颗星星 都是我们的故事
直到永远 你是我唯一
(唯一)

...
```

需要我将歌词保存到文件吗？支持以下格式：

- `.txt` - 纯文本格式
- `.lrc` - LRC 歌词格式（带时间标签）
- `.md` - Markdown 格式

请告诉我保存路径和格式，例如：`/path/to/song.txt`
