<!-- markdownlint-disable MD033 MD041 -->

# awesome-gamedev-agent-skills

<p align="center">
  <img src="docs/assets/awesome-gamedev-agent-skills-banner.png" width="820"
       alt="awesome-gamedev-agent-skills — 面向 AI 编程智能体的游戏开发技能。76 个技能与一个路由器，覆盖 12 个引擎，并包含一套美术方向与资产制作流程。">
</p>

**给你的 AI 编程智能体装上 76 个游戏开发技能 —— 安装一次，路由器就会在你做任何事时加载合适的技能。**

[![License](https://img.shields.io/badge/license-Apache--2.0-blue)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-76%20%2B%20router-brightgreen)](skills/)
[![Format](https://img.shields.io/badge/format-Agent%20Skills-informational)](docs/SKILL-FORMAT.md)
[![Last commit](https://img.shields.io/github/last-commit/gamedev-skills/awesome-gamedev-agent-skills)](https://github.com/gamedev-skills/awesome-gamedev-agent-skills/commits/main)

[Agent Skills](docs/SKILL-FORMAT.md) 是 AI 智能体按需加载的小型能力文件。本仓库为你的智能体提供
**76 个游戏开发技能**，外加一个帮你挑选技能的**路由器**。你只需描述自己在做什么，智能体就会在
动手写代码之前，加载匹配的引擎技能与任务技能。

- **跨引擎。** Godot、Unity、Unreal、Ebitengine、Phaser、PixiJS、three.js、Bevy、pygame、LÖVE、Defold、Roblox。
- **技能不用你挑。** 路由器会识别你的引擎和任务，只加载适配的那几个。
- **美术方向，而非一次性生成。** 资产流程会先锁定视觉目标，再成系列产出，确定性地归一化输出，
  并在游戏内校验结果。
- **值得信任。** 每个技能都依据一手文档编写，版本锁定到明确的引擎发行版，并通过
  [校验器](scripts/validate-skills.py) 检查。当前基线版本与升级策略见
  [版本支持](docs/VERSION-SUPPORT.md)。

## 目录

- [快速开始](#快速开始)
- [你可以这样提问](#你可以这样提问)
- [路由器如何挑选技能](#路由器如何挑选技能)
- [技能目录](#技能目录)
- [智能体兼容性](#智能体兼容性)
- [参与贡献](#参与贡献)

## 快速开始

**一条命令，适配任何智能体。** [`skills`](https://www.npmjs.com/package/skills) CLI 会检测你正在使用的
编程智能体，把路由器与全部 76 个技能安装到正确位置：

```bash
npx skills add gamedev-skills/awesome-gamedev-agent-skills
```

先加 `--list` 预览，`-g` 安装到所有项目，或用 `-a <agent>` 指定工具（`-a cursor`、
`-a claude-code`、`-a gemini-cli` 等）。该 CLI 需要 Node.js 22.20 或更高版本。同样的 `SKILL.md` 文件可以在 Claude Code、Cursor、
Windsurf、Cline、Codex、Gemini CLI、GitHub Copilot、Kiro 以及
[另外几十种工具](docs/COMPATIBILITY.md) 中原生加载 —— 无需任何转换。

**Claude Code 插件（备选）。** 本仓库同时是一个 Claude Code 插件市场，因此可以用 Claude 自己的命令安装：

```bash
claude plugin marketplace add gamedev-skills/awesome-gamedev-agent-skills
claude plugin install gamedev@awesome-gamedev-agent-skills
```

只做一个引擎？安装更小的组合包 —— `router` 加上你的引擎：

```bash
claude plugin install router@awesome-gamedev-agent-skills
claude plugin install godot@awesome-gamedev-agent-skills    # 或：unity · unreal · ebitengine · web-engines · other-engines
```

更想手动复制文件，或者需要每个工具的确切路径？见
**[`docs/INSTALLATION.md`](docs/INSTALLATION.md)**。

想把技能目录当网页浏览？每个技能、引擎分组和逐智能体的安装路径都可以在
**[gamedev-skills.github.io/awesome-gamedev-agent-skills](https://gamedev-skills.github.io/awesome-gamedev-agent-skills/)**
查看，站点直接由本仓库的 `SKILL.md` 文件生成。

然后直接和智能体对话即可 —— 见下文。

## 你可以这样提问

用自然语言描述任务，路由器会自己找出需要的技能：

| 你说 | 它会为你加载的技能 |
|------|---------------------|
| "给我的 Godot 玩家加二段跳" | `godot-2d-movement` + `platformer` |
| "给我的 Unity RPG 做背包" | `unity-scriptableobjects` + `rpg` + `save-systems` |
| "用 Go 和 Ebitengine 做一个 2D 解谜游戏" | `ebitengine-game` + `puzzle` |
| "给我的 Defold 游戏加玩家移动" | `defold-core` + `input-systems` |
| "在 Godot 里做程序化地牢 roguelike" | `godot-tilemap` + `procedural-gen` + `roguelike` |
| "做一套风格统一的像素风玩家、敌人和瓦片" | `create-game-assets` + 识别出的引擎导入技能 |
| "存档槽位该怎么设计？" | `save-systems` |
| "用 butler 把游戏发布到 itch" | `itch-publish` |

右边的技能名你从来不需要自己输入，那是路由器的活。

## 路由器如何挑选技能

你从不需要点名技能。每次请求时，路由器做三件事：

1. **从项目文件识别你的引擎** —— 有 `project.godot` 就是 Godot，有 `*.uproject` 就是 Unreal，以此类推。
   判断不出来时，它会直接问你。
2. **从请求里读出任务** —— 一个概念（AI、着色器、存档）、一个品类（平台跳跃、roguelike），
   或一个上线步骤（game jam、Steam）。
3. **只加载匹配的技能**，并按顺序加载：先引擎基础，再概念，最后是品类衔接。切换到新话题时会重新路由。

所以"给我的 Godot 玩家加二段跳"只会加载 `godot-2d-movement` + `platformer`，别的一概不动。

<details>
<summary>具体的识别信号与路由表</summary>

完整算法 —— 每个引擎指纹、任务到技能的对照表，以及引擎、学科、品类、工作流技能如何组合 ——
都在路由器技能本身里：[`router/SKILL.md`](router/SKILL.md)。简言之：引擎选择是排他的（恰好一个引擎），
而学科、品类与工作流技能在此基础上叠加。

</details>

## 技能目录

9 个类别、76 个技能 —— 每一项都链接到下方对应的 `SKILL.md`。

### 引擎

#### Godot —— 16 个（[`skills/godot/`](skills/godot/)）· Godot 4.7

| 技能 | 适用范围 |
|------|----------|
| [`godot-gdscript`](skills/godot/godot-gdscript/SKILL.md) | GDScript 语言：类型标注、生命周期、`@export`、信号、惯用写法 |
| [`godot-nodes-scenes`](skills/godot/godot-nodes-scenes/SKILL.md) | 场景树、节点组合、实例化、autoload、`PackedScene` |
| [`godot-signals-groups`](skills/godot/godot-signals-groups/SKILL.md) | 用信号 + 分组做事件驱动设计 |
| [`godot-2d-movement`](skills/godot/godot-2d-movement/SKILL.md) | `CharacterBody2D` 运动学移动、`move_and_slide`、斜坡 |
| [`godot-tilemap`](skills/godot/godot-tilemap/SKILL.md) | `TileMapLayer`/`TileSet`：自动拼接、地形、碰撞/导航层 |
| [`godot-physics`](skills/godot/godot-physics/SKILL.md) | 刚体/区域/静态体（2D+3D）、碰撞层、射线检测 |
| [`godot-ui-control`](skills/godot/godot-ui-control/SKILL.md) | `Control` 节点：锚点、容器、主题、焦点导航 |
| [`godot-animation`](skills/godot/godot-animation/SKILL.md) | `AnimationPlayer`、`AnimationTree`、`Tween` |
| [`godot-shaders`](skills/godot/godot-shaders/SKILL.md) | Godot 着色语言：2D `canvas_item` 与 3D `spatial` 着色器 |
| [`godot-3d-essentials`](skills/godot/godot-3d-essentials/SKILL.md) | 3D 节点、相机、灯光、环境/后处理、`GridMap` |
| [`godot-resources`](skills/godot/godot-resources/SKILL.md) | 自定义 `Resource` 类、`.tres`、数据驱动设计 |
| [`godot-audio`](skills/godot/godot-audio/SKILL.md) | `AudioStreamPlayer`、音频总线、效果、节拍同步 |
| [`godot-multiplayer`](skills/godot/godot-multiplayer/SKILL.md) | 高层多人：`MultiplayerAPI`、RPC、生成器/同步 |
| [`godot-export`](skills/godot/godot-export/SKILL.md) | 导出预设/模板、平台构建、无头 CLI 导出 |
| [`godot-csharp`](skills/godot/godot-csharp/SKILL.md) | Godot 中的 C#/.NET：绑定、把信号当事件用、与 GDScript 互操作 |
| [`godot-gdscript-headless-testing`](skills/godot/godot-gdscript-headless-testing/SKILL.md) | 用 `godot --headless` 跑 GDScript 测试，为 CI 提供退出码 |

#### Unity —— 8 个（[`skills/unity/`](skills/unity/)）· Unity 6.3 LTS (6000.3)

| 技能 | 适用范围 |
|------|----------|
| [`unity-csharp-scripting`](skills/unity/unity-csharp-scripting/SKILL.md) | `MonoBehaviour` 生命周期、组件模型、协程、序列化 |
| [`unity-input-system`](skills/unity/unity-input-system/SKILL.md) | 新 Input System：action、binding、action map、设备 |
| [`unity-physics`](skills/unity/unity-physics/SKILL.md) | Rigidbody、碰撞体、层、关节 |
| [`unity-animation`](skills/unity/unity-animation/SKILL.md) | Animator Controller、状态机、混合树、人形 IK |
| [`unity-scriptableobjects`](skills/unity/unity-scriptableobjects/SKILL.md) | 用 `ScriptableObject` 搭建数据架构 |
| [`unity-tilemap-2d`](skills/unity/unity-tilemap-2d/SKILL.md) | 2D Tilemap/Tile Palette、规则瓦片（rule tile）、tilemap 碰撞体 |
| [`unity-navmesh`](skills/unity/unity-navmesh/SKILL.md) | AI 导航：烘焙 NavMesh、`NavMeshAgent` |
| [`unity-build-pipeline`](skills/unity/unity-build-pipeline/SKILL.md) | 构建/播放器/质量设置、代码裁剪、Addressables |

#### Unreal —— 6 个（[`skills/unreal/`](skills/unreal/)）· Unreal Engine 5.8

| 技能 | 适用范围 |
|------|----------|
| [`unreal-blueprints`](skills/unreal/unreal-blueprints/SKILL.md) | 蓝图可视化脚本：类、图表、通信、常用节点 |
| [`unreal-cpp-gameplay`](skills/unreal/unreal-cpp-gameplay/SKILL.md) | C++ 玩法 + Gameplay Framework（GameMode、Pawn、Controller） |
| [`unreal-enhanced-input`](skills/unreal/unreal-enhanced-input/SKILL.md) | Enhanced Input：Input Action、Mapping Context、Modifier、Trigger |
| [`unreal-behavior-trees`](skills/unreal/unreal-behavior-trees/SKILL.md) | AI 行为树 + Blackboard、task/decorator/service |
| [`unreal-niagara`](skills/unreal/unreal-niagara/SKILL.md) | Niagara 特效：系统、发射器、模块、参数 |
| [`unreal-packaging`](skills/unreal/unreal-packaging/SKILL.md) | 打包/烹饪项目、构建配置、发布构建 |

#### Ebitengine —— 1 个（[`skills/ebitengine/`](skills/ebitengine/)）· Ebitengine v2.10.5 · Go 1.25+

| 技能 | 适用范围 |
|------|----------|
| [`ebitengine-game`](skills/ebitengine/ebitengine-game/SKILL.md) | 可玩的 Go 2D 游戏：update/draw 契约、输入、渲染、文本、音频、玩法测试、可选起步模板与平台构建 |

#### Web 引擎 —— 6 个（[`skills/web-engines/`](skills/web-engines/)）· Phaser 4.2 · PixiJS 8.22 · three.js r186

| 技能 | 适用范围 |
|------|----------|
| [`phaser-core`](skills/web-engines/phaser-core/SKILL.md) | Phaser 4.2 游戏配置、Scene 生命周期、加载器、相机 |
| [`phaser-arcade-physics`](skills/web-engines/phaser-arcade-physics/SKILL.md) | Arcade Physics：刚体、速度、碰撞器/overlap、分组 |
| [`pixijs-rendering`](skills/web-engines/pixijs-rendering/SKILL.md) | PixiJS v8 场景图：`Application`、`Container`、`Sprite`、事件 |
| [`threejs-scene-setup`](skills/web-engines/threejs-scene-setup/SKILL.md) | Three.js 场景/相机/渲染器/动画循环、缩放的适配、控制器 |
| [`threejs-gltf-loading`](skills/web-engines/threejs-gltf-loading/SKILL.md) | 加载 glTF/GLB + 蒙皮动画（`GLTFLoader`/`AnimationMixer`） |
| [`threejs-materials-lighting`](skills/web-engines/threejs-materials-lighting/SKILL.md) | 材质（PBR）、灯光、阴影、环境贴图 |

#### 其他引擎 —— 11 个（[`skills/other-engines/`](skills/other-engines/)）· Bevy · pygame · LÖVE · Defold · Roblox

| 技能 | 适用范围 |
|------|----------|
| [`bevy-ecs`](skills/other-engines/bevy-ecs/SKILL.md) | Bevy 应用 + ECS：component、system、query、resource、plugin（Bevy 0.20） |
| [`pygame-core`](skills/other-engines/pygame-core/SKILL.md) | pygame 主循环、`Surface`/`Rect`、精灵/分组、事件（pygame-ce 2.5.8） |
| [`love2d-core`](skills/other-engines/love2d-core/SKILL.md) | LÖVE `load/update/draw` 循环、受 dt 驱动的运动、输入、状态（LÖVE 11.5） |
| [`defold-core`](skills/other-engines/defold-core/SKILL.md) | Defold collection、game object、脚本生命周期、输入、消息、factory、proxy、Bob 构建检查（Defold 1.14.0） |
| [`roblox-luau`](skills/other-engines/roblox-luau/SKILL.md) | Roblox Luau 脚本：service、instance、客户端/服务端模型 |
| [`roblox-datastores`](skills/other-engines/roblox-datastores/SKILL.md) | 用 `DataStoreService` 持久化数据：会话、限额、有序存储 |
| [`roblox-ui`](skills/other-engines/roblox-ui/SKILL.md) | Roblox UI：`ScreenGui` 布局、响应式缩放、安全区、手柄/触摸导航 |
| [`roblox-networking`](skills/other-engines/roblox-networking/SKILL.md) | Remote、服务端权威校验、限流、复制边界 |
| [`roblox-characters`](skills/other-engines/roblox-characters/SKILL.md) | 角色生命周期：生成/重生作用域、`Humanoid`、rig、`Animator` 轨道 |
| [`roblox-physics`](skills/other-engines/roblox-physics/SKILL.md) | 装配体、约束、碰撞/查询策略、射线检测、力、网络所有权 |
| [`roblox-studio-workflow`](skills/other-engines/roblox-studio-workflow/SKILL.md) | 以 Explorer 为先的 place 编辑、结构保持、多上下文 Studio 验证 |

### 学科（Disciplines）—— 15 个（[`skills/disciplines/`](skills/disciplines/)）

与识别出的引擎技能一起加载的跨引擎概念。

| 技能 | 适用范围 |
|------|----------|
| [`create-game-assets`](skills/disciplines/create-game-assets/SKILL.md) | 面向风格统一的精灵、瓦片、贴图、图标、UI 美术与 3D 资产的美术方向与制作流程 |
| [`game-ai`](skills/disciplines/game-ai/SKILL.md) | NPC 决策：有限状态机、行为树、转向、寻路 |
| [`ai-behavior-trees-utility-ai`](skills/disciplines/ai-behavior-trees-utility-ai/SKILL.md) | 生产级行为树运行时（Blackboard、组合节点、装饰器、叶节点）+ Utility AI（曲线、考量、评估器）+ 混合 AI |
| [`procedural-gen`](skills/disciplines/procedural-gen/SKILL.md) | 噪声、随机数、种子、网格/地牢/地形生成 |
| [`dialogue-systems`](skills/disciplines/dialogue-systems/SKILL.md) | 分支对话/叙事：节点、条件、变量（Yarn/Ink） |
| [`save-systems`](skills/disciplines/save-systems/SKILL.md) | 序列化与恢复游戏状态：格式、槽位、版本迁移、自动保存 |
| [`audio-design`](skills/disciplines/audio-design/SKILL.md) | 总线/混音、自适应音乐、音效、闪避（ducking） |
| [`shader-programming`](skills/disciplines/shader-programming/SKILL.md) | 跨引擎着色器概念：顶点/片元、UV、常见效果 |
| [`physics-tuning`](skills/disciplines/physics-tuning/SKILL.md) | 调物理手感：固定时间步、质量/阻力、CCD、稳定性、层 |
| [`level-design`](skills/disciplines/level-design/SKILL.md) | 白盒/blockout 结构、瓦片/网格布局、节奏设计 |
| [`input-systems`](skills/disciplines/input-systems/SKILL.md) | 输入架构：动作映射、重绑定、多设备、输入缓冲 |
| [`game-feel`](skills/disciplines/game-feel/SKILL.md) | 打击感/爽感：屏幕震动、命中停顿、补间/缓动、挤压与拉伸、反馈层级 |
| [`game-ui-ux`](skills/disciplines/game-ui-ux/SKILL.md) | 跨引擎 HUD/菜单：锚点、分辨率缩放、安全区、焦点导航 |
| [`camera-systems`](skills/disciplines/camera-systems/SKILL.md) | 2D 跟随/死区/前瞻 + 边界，3D 轨道/第一人称，屏幕震动钩子 |
| [`performance-optimization`](skills/disciplines/performance-optimization/SKILL.md) | 先做性能剖析：帧预算、CPU/GPU 排查、draw call、对象池、GC、资产预算 |

### 品类（Genres）—— 9 个（[`skills/genres/`](skills/genres/)）

用于编排引擎技能 + 学科技能的组合模板；它们从不重复讲底层原语。

| 技能 | 适用范围 |
|------|----------|
| [`platformer`](skills/genres/platformer/SKILL.md) | 2D 平台跳跃：跑/跳/土狼时间、瓦片关卡、危险物、目标 |
| [`roguelike`](skills/genres/roguelike/SKILL.md) | 网格/回合制移动、程序化地牢、永久死亡、视野（FOV）、战利品 |
| [`rpg`](skills/genres/rpg/SKILL.md) | 属性/升级、背包、任务、对话、存档/读档、战斗 |
| [`fps-shooter`](skills/genres/fps-shooter/SKILL.md) | 第一人称控制器、相机、射击/hitscan、敌人 AI |
| [`tower-defense`](skills/genres/tower-defense/SKILL.md) | 波次生成、路线寻路、塔/索敌、经济 |
| [`card-game`](skills/genres/card-game/SKILL.md) | 卡牌数据、牌库/手牌/弃牌区、出牌规则、回合结构 |
| [`visual-novel`](skills/genres/visual-novel/SKILL.md) | 分支脚本、角色/背景显示、文本框 + 选项、回顾（backlog） |
| [`survival-crafting`](skills/genres/survival-crafting/SKILL.md) | 资源采集、背包、制作、需求、基地建造 |
| [`puzzle`](skills/genres/puzzle/SKILL.md) | 网格/棋盘状态、消除/规则结算、撤销、关卡推进 |

### 工作流（Workflows）—— 4 个（[`skills/workflows/`](skills/workflows/)）

| 技能 | 适用范围 |
|------|----------|
| [`game-jam`](skills/workflows/game-jam/SKILL.md) | 在限时内规划/执行 jam 作品并提交 |
| [`prototype-fast`](skills/workflows/prototype-fast/SKILL.md) | 快速灰盒出可玩的垂直切片；砍范围；验证乐趣 |
| [`steam-publish`](skills/workflows/steam-publish/SKILL.md) | Steamworks 接入：应用/depot、商店页、构建、发布清单 |
| [`itch-publish`](skills/workflows/itch-publish/SKILL.md) | 在 itch.io 发布/更新：项目页、渠道、`butler` 上传 |

## 智能体兼容性

这里的每个技能都是一个普通 `SKILL.md` 文件夹，遵循 [Agent Skills](https://agentskills.io) 开放标准 ——
即 Anthropic 以 Claude Skills 名义推出的格式：元数据加上一份由智能体按需阅读的 Markdown 操作手册。

同一份文件可以**原生**在生态中被加载：Claude Code、Claude、Cursor、Windsurf、Cline、OpenAI Codex、
Gemini CLI、GitHub Copilot、Kiro、Antigravity、VS Code，以及
[更多工具](https://agentskills.io/clients)。Oh My Pi（omp）同样原生读取该格式。
无需维护编辑器专用的规则文件，也没有任何转换步骤。

**一条命令安装。** 通用 CLI 会检测你的智能体，并把技能写到正确位置：

```bash
npx skills add gamedev-skills/awesome-gamedev-agent-skills
```

**或者手动复制。** 把某个技能的 `<name>/` 文件夹放进你的智能体技能目录：

| 智能体 | 技能目录 |
|--------|----------|
| Claude Code | `.claude/skills/` |
| Codex · Gemini CLI · GitHub Copilot · Antigravity | `.agents/skills/`（共享路径） |
| Cursor | `.cursor/skills/`（也会读取 `.agents/skills/`） |
| Windsurf | `.devin/skills/`（也会读取 `.windsurf/skills/`） |
| Cline | `.cline/skills/` |
| Kiro | `.kiro/skills/` |
| Oh My Pi（omp） | `.omp/skills/`（也会读取 `.agents/skills/`） |

各智能体的确切路径、触发方式，以及随智能体不同而略有差异的可选字段，见
[`docs/COMPATIBILITY.md`](docs/COMPATIBILITY.md)。

## 演示

一句大白话请求进去，最小必要的技能出来，然后是版本锁定的代码。下面是一次交互的大致形态（示意）：

```text
> 给我的玩家加二段跳

检测到 Godot（project.godot）。加载 godot-2d-movement 处理控制器，
加载 platformer 处理跳跃手感 —— 其余 74 个技能跳过。
```

```gdscript
extends CharacterBody2D

@export var speed := 220.0
@export var jump_velocity := -380.0
@export var max_jumps := 2

var _jumps_left := max_jumps

func _physics_process(delta: float) -> void:
    if not is_on_floor():
        velocity += get_gravity() * delta   # Godot 4.7 基线
    else:
        _jumps_left = max_jumps

    if Input.is_action_just_pressed("ui_accept") and _jumps_left > 0:
        velocity.y = jump_velocity
        _jumps_left -= 1

    velocity.x = Input.get_axis("ui_left", "ui_right") * speed
    move_and_slide()
```

## 仓库结构

```
skills/        76 个专门技能，按引擎 / 学科 / 品类 / 工作流分组
router/        主路由器技能（含 references/）
docs/          编写标准、安装、兼容性
templates/     SKILL.md 模板
scripts/       validate-skills.py、generate-site.py 等工具
tests/         校验器的回归测试
```

## 参与贡献

欢迎贡献。请先阅读 [`CONTRIBUTING.md`](CONTRIBUTING.md) 以及
[`docs/SKILL-FORMAT.md`](docs/SKILL-FORMAT.md) 中的编写标准。每个技能都必须通过校验器与评分表才能合并：

```bash
python scripts/validate-skills.py
```

## 维护者

[awesome-gamedev-agent-skills](https://github.com/gamedev-skills/awesome-gamedev-agent-skills)
由 [Abhishek Barali](https://github.com/AbhishekBarali) 创建并维护。

## 许可证

[Apache-2.0](LICENSE)。署名与商标说明见 [NOTICE](NOTICE)。
