# 情景记忆（轻量版）— 默认禁用

> ⚠️ **本插件默认不启用**：`main.py` 已被重命名为 `main.py.disabled`。
> 需要使用时，请手动把 `main.py.disabled` 改回 `main.py`，然后重启 AstrBot。

---

## 这是什么

给 AstrBot 的**长期记忆**插件（轻量版）：让机器人记住聊过的事、记住你是谁，并且随着时间自然遗忘。

**完整版 vs 轻量版的唯一区别**：

| 项 | 完整版 | **轻量版（本插件）** |
|---|---|---|
| 重排 | bge-reranker 本地模型 | **规则重排**（实体/关键词重合 + 时间新鲜度）|
| 模型下载 | 1.08 GB | **不需要** |
| 额外依赖 | torch + sentence-transformers（约 2GB）| **零依赖** |
| 首次启动 | 预热 2~3 秒 | **0 秒** |
| 内存占用 | +500 MB | **0** |
| 重排精度 | 高 | 中 |

**其余功能完全一致**：5 层流水线、所有权、防伪装、2000 字预算、自动归档、转生、灵魂记忆。

---

## 🚀 如何启用（重要）

### 步骤

1. 打开插件目录：
   ```
   AstrBot/data/plugins/astrbot_plugin_episodic_memory_lite/
   ```
2. 把 **`main.py.disabled`** 重命名为 **`main.py`**
3. 重启 AstrBot
4. 在 WebUI 插件列表里应该能看到「情景记忆（轻量版）」

### 为什么默认禁用

- 防止和完整版**同时运行**导致数据库冲突；
- 给你一个"备用方案"——不想装 torch 时直接切换；
- 两个版本共用同一套 `data/memory.db` 格式（可迁移）。

---

## ⚙️ 必填配置

启用后，在 AstrBot WebUI → 插件 → 情景记忆（轻量版）→ 配置：

| 配置项 | 必填 | 说明 |
|---|---|---|
| **`embed_key`** | ✅ **必填** | 智谱 API key |
| `embed_model` | 默认 `embedding-3` | 向量模型 |
| `context_token_limit` | 默认 `32000` | 转生阈值 |

### 获取智谱 API Key

1. 注册 [智谱开放平台](https://open.bigmodel.cn)
2. 创建 API Key
3. 填入 `embed_key`
4. `embedding-3` 免费额度足够个人使用

---

## ⚠️ 使用前必读

### ① 不要和完整版同时启用

两个插件共用 `data/memory.db` 的结构，**同时运行会冲突**。

**切换方式**：
1. 停用完整版（`main.py` → `main.py.disabled`）；
2. 启用轻量版（`main.py.disabled` → `main.py`）；
3. 重启 AstrBot。

### ② 数据库是共用的

轻量版和完整版**读同一个 `data/memory.db`**：
- 从完整版切到轻量版：**记忆保留**；
- 从轻量版切回完整版：**记忆保留**。

### ③ 重排精度会下降

轻量版用规则打分，**不如 bge 模型准**：
- 简单查询（"上次说的出差"）→ 差别不大；
- 复杂语义（"那个让我很烦的事"）→ 可能不如完整版准。

### ④ 没有预热开销

轻量版**不需要预热**，启动即用。

---

## 📦 安装

### 方式一：直接复制（当前状态）

本插件已经在 `data/plugins/astrbot_plugin_episodic_memory_lite/`，**只需要改名启用**。

### 方式二：给别人

把整个目录打包：
```
astrbot_plugin_episodic_memory_lite/
├── main.py.disabled   ← 改名为 main.py 即可用
├── metadata.yaml
├── _conf_schema.json
└── README.md
```

---

## 🏗️ 架构（和完整版相同）

```
消息 → pending
  ↓
① 触发层（5 条）
  ↓
② 过滤层（纯闲聊丢弃）
  ↓
③ 提炼层（type/confidence/important）
  ↓
④ 归档层（向量去重 + 90天归档）
  ↓
⑤ 落库

──────────── 读取 ────────────

向量检索 Top-10
  ↓
规则重排 → Top-3
  ↓
2000 字预算
  ↓
注入 system_prompt
```

---

## ❓ 常见问题

### Q1：轻量版和完整版能同时用吗？

**不能**。共用数据库，会冲突。

### Q2：切换版本会丢记忆吗？

**不会**。两个版本共用 `data/memory.db` 格式。

### Q3：轻量版够用吗？

**大多数场景够用**。规则重排对简单查询差别不大，只有复杂语义查询不如完整版。

### Q4：轻量版要不要装 torch？

**不需要**。零额外依赖。

### Q5：轻量版耗 token 吗？

**和完整版一样**（记忆注入 ≤2000 字符，占上下文 3%）。

### Q6：为什么默认禁用？

防止和完整版冲突，同时给你一个"不想装 torch 时的备用方案"。

---

## 📝 版本

| 版本 | 说明 |
|---|---|
| **1.1.0-lite** | 轻量版：规则重排，零依赖，默认禁用 |

---

## 📜 协议

MIT License

---

## 🙏 致谢

- [AstrBot](https://github.com/AstrBotDevs/AstrBot) —— 插件框架
- [智谱 AI](https://open.bigmodel.cn) —— Embedding API
