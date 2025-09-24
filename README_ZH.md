# Civitai Recipe Finder (Civitai 配方查找器)

## 项目简介

在 AI 绘画创作中，找到理想的“**配方 (Recipe)**”——即模型、触发词、提示词与生成参数的最佳组合，往往比单纯的参数调节更为关键。

**Civitai Recipe Finder** 是一套专为 **ComfyUI** 设计的强大节点工具集，能够帮助你快速探索、复现和分析 Civitai 社区的创作配方，甚至为本地模型提供即时的可视化参考。

无论是想一键复现热门作品，还是深入研究社区的使用趋势，本工具都能为你提供 **高效、直观、灵活** 的支持。

---

## 功能亮点

* 🔍 **可视化查找配方**
  即时浏览某个本地模型在 Civitai 的热门作品，并一键复现完整配方（提示词、参数、LoRA 组合、工作流等）。

* ⚡ **即时触发词获取**
  快速提取 LoRA 模型的官方触发词和本地元数据触发词。

* 📊 **深度社区趋势分析**
  汇总数百张社区作品，找出最常用的正/负向提示词、采样器、CFG、步数等参数。

* 🔗 **黄金组合发现**
  揭示某个模型常与哪些其他 LoRA 搭配使用。

* 🛠 **模块化与高扩展性**
  工具以节点套件形式提供，既能满足日常的轻量级操作，也能支持复杂的深度分析工作流。

---

## 节点套件说明

Recipe Finder 包含三大类工具节点，可满足不同使用场景。

### 1. 可视化配方查找器 (Visual Recipe Finder)

#### `Civitai Recipe Gallery` (Civitai 配方画廊)

* **功能**:
  浏览指定本地模型的热门作品画廊，支持一键复现完整配方。
* **新增特性**:

  * 🚀 **一键加载工作流**：智能安全地加载图片的原始工作流（兼容 ComfyUI-Manager，自动新标签页打开）。
  * 💾 **保存源文件**：下载包含完整元数据的原始图片，存档到 `output` 文件夹。

| 输出端口            | 类型              | 说明                                          |
| --------------- | --------------- | ------------------------------------------- |
| `image`         | `IMAGE`         | 选中的示例图片                                     |
| `info_md`       | `STRING`        | 配方完整 Markdown 报告（推荐连接到 `MarkdownPresenter`） |
| `recipe_params` | `RECIPE_PARAMS` | 核心参数管道（需配合 `Get Parameters from Recipe` 使用） |

> ⚠️ **首次运行提示**
>
> * 初次运行会计算所有本地模型的 **hash**，耗时较长。
> * 数据缓存在 `Civitai_Recipe_Finder/data` 目录，后续仅处理新增模型。

![gallery example](./example_workflows/Recipe_Gallery.png)

#### `Get Parameters from Recipe` (从配方获取参数)

* **功能**: 解包 `recipe_params` 管道，将参数直接输出，可与 `KSampler` 等下游节点兼容。
* **输出**: `ckpt_name`, `positive_prompt`, `negative_prompt`, `seed`, `steps`, `cfg`, `sampler_name`, `scheduler`, `width`, `height`, `denoise`

---

### 2. 模型深度分析 (In-Depth Model Analysis)

核心节点：**`Model Analyzer (Checkpoint / LoRA)`**

* **功能**:

  * 一站式完成 **数据抓取 → 社区统计 → 参数分析 → 报告输出**
  * 取代旧版 `Data Fetcher` + 多个 Analyzer 的繁琐组合
* **输入**: `model_name`, `image_limit`, `sort`, `nsfw_level`, `filter_type`, `summary_top_n`, `force_refresh`
* **输出**:

  * `full_report_md`: 完整分析报告（Markdown）
  * `fetch_summary`: 抓取摘要（如“成功分析 100 个项目”）
  * `params_pipe`: 最常用参数管道（配合 `Get Parameters from Analysis` 解包）

![Model\_DeepResearch example](./example_workflows/Model_DeepResearch.png)

#### `Get Parameters from Analysis` (从分析获取参数)

* **功能**: 解包 `params_pipe`，提取社区常用参数。
* **输出**: 与 `Get Parameters from Recipe` 相同。

---

### 3. 轻量级工具 (Lightweight Tool)

#### `Lora Trigger Words` (LoRA 触发词)

* **功能**: 即时获取指定 LoRA 的触发词。
* **输出**:

  * `metadata_triggers`: 本地元数据触发词
  * `civitai_triggers`: 官方 API 触发词
  * `triggers_md`: 精美对照表（Markdown）

![lora\_trigger\_words example](./example_workflows/LoRA_Trigger_Words.png)

---

## 安装与使用

1. 将项目文件夹放入 `ComfyUI/custom_nodes/`，例如：

   ```bash
   ComfyUI/custom_nodes/CivitaiProject/
   ```
2. 安装依赖：

   ```bash
   pip install -r requirements.txt
   ```
3. 重启 ComfyUI，即可在 `Civitai` 菜单中找到节点。

> 💡 **小贴士**:
> `Markdown Presenter` 节点可在 `Display` 菜单找到，或在搜索框中输入“Markdown Presenter”。

---

## 工作流示例

* **ComfyUI 内置**: *Templates → Custom Nodes → ComfyUI-Civitai-Recipe*
* **仓库目录**: [example\_workflows](./example_workflows)

---

## 版本兼容性

* **3.1 及之前版本迁移**
  `Settings → CivitaiUtils → Migration`
  支持将旧版 JSON 缓存直接迁移至数据库。

---

## 国内网络支持

* 在 `Settings → CivitaiUtils → Civitai Helper Network` 中可选择访问环境：

  * 🌏 **International** (默认) – 国际用户
  * 🇨🇳 **China Mirror** – 国内用户，访问更快更稳定

![Network\_setting](./image/Network_setting.png)

---

## 更新日志

### \[3.2.0] - 2025-09-23

#### 新增

* **数据库管理面板**：支持一键清理缓存（分析器、API 响应、触发词等）。
* **视频资源支持**：`Recipe Gallery` 与 `Model Analyzer` 现已支持视频配方。

#### 变更

* **核心架构重构**：缓存由零散 JSON 文件升级为统一 `SQLite` 数据库。
* **节点简化**：合并为单一 `Model Analyzer` 节点，流程更高效。
* **命名统一**：

  * `Recipe Params Parser` → **`Get Parameters from Recipe`**
  * 分析参数解包节点 → **`Get Parameters from Analysis`**
* **数据库工具**：新增 **`🗃️ Database & Models`** 面板，可查看模型、刷新数据库、清除缓存，支持旧版数据迁移。

---

## 鸣谢

* 触发词逻辑部分参考自：
  [Extraltodeus/LoadLoraWithTags](https://github.com/Extraltodeus/LoadLoraWithTags)
  [idrirap/ComfyUI-Lora-Auto-Trigger-Words](https://github.com/idrirap/ComfyUI-Lora-Auto-Trigger-Words)

* 画廊节点设计思路参考：
  [Firetheft/ComfyUI\_Civitai\_Gallery](https://github.com/Firetheft/ComfyUI_Civitai_Gallery)

在此向以上项目及其作者们致以诚挚的感谢！

---