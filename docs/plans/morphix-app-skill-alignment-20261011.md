# OpenClaw 与 Morphix App Skill 对齐方案

Owner：openclaw-morphixai，联合 API 的平台 Skill/guide。日期：2026-10-11。状态：已调研的下一阶段方案，未改工具、Skill 或发布包。

## 当前缺口

当前 checkout `ba14c5b` 与已知 origin/main `3d8c313` 的核心接线均使用 /pipedream/accounts、/pipedream/proxy；resolveAppAccount 省略账号时选择第一个有效旧账号，并把 accountId 而非新 connectionId 传入。mx-link Skill 和错误提示仍要求全选 API Key scopes，Figma 文本标记不可用。当前注册入口未包含远程 MCP 的 App Builder 开发/验证工具，公共 json helper 也未显式保留新 MCP isError 合同。

因此只替换 Skill 文字不能使 Composio/API Key 多供应商或 App 构建可用。[客户端](../../packages/core/src/morphix-client.ts)、[账号 helper](../../packages/openclaw-plugin/src/tools/_tool-helpers.ts)、[插件注册](../../packages/openclaw-plugin/src/index.ts)、[stdio 注册](../../packages/mcp-server/src/index.ts)须共同对齐。Figma 是否可用由本入口实测决定，不直接删除不可用标签冒充验收。

## 目标结构

当前 [OpenClaw 官方 MCP 指南](https://docs.openclaw.ai/tools/mcp) 已提供 Streamable HTTP/OAuth 与实际能力探测，优先评估并验证原生连接 Morphix 的路径；当前机器未安装可直接实测的 OpenClaw CLI，不能把文档支持当目标用户版本已兼容。若原生能力满足本批运行/资源需求，插件主要承担 Skill、引导及旧 mx_* 兼容，不额外复制一个 MCP client。

平台共同服务与 App 工作流以 Morphix 远程 MCP 为准。OpenClaw/stdio 适配器优先做协议/工具/资源薄转发，使用当前广告的 schema、资源 URI 与安全结果；不在插件重建 Supplier 目录、授权、目标规则或另一套 fork/初始化服务。

兼容已有 mx_* 调用时，用明确的薄适配映射至现行服务；未能映射/验收的旧 action 保留明确 capability_unavailable。认证来源独立处理，远程 MCP 的 agent:mcp 不等于旧 REST 的权限合集。保留用户已有配置，不自动迁移或暴露凭据；新建使用统一 connectionId 和 integrationId。

Morphix App Skill 应有一份平台维护的共同正文：把需求转成可运行、可分享/fork 的完整 App。API owner 维护平台工作流，Foundation 提供发布的组件/SDK Skill，第三方应用继续各自独立 Skill。OpenClaw npm/ClawHub 与其他 Agent 安装物由同一源构建/同步，禁止各自手改出不同合同。

共同 App Skill 的完整用户需求、流程和最终版本门槛归 API 的 `docs/plans/morphix-app-skill-20261011.md`，本仓负责让已实现能力在 OpenClaw/stdio 入口可发现、可认证和可调用。插件包的 Skill 只补安装/入口及兼容层信息，组件/第三方知识按需加载，不在本仓复制平台规范。

fork/初始化、音乐等新能力只在服务实际广告后才指导调用。缺工具时给出真实缺口与恢复/安装方式；不新增平台 prepare/confirm，操作授权由实际 Agent harness 和产品边界处理。

## 实施与验收

1. 核对目标 OpenClaw 版本的原生 MCP/OAuth/Skill 读取；仅在确认缺口时采用薄适配。对齐 remote MCP、工具 schema、资源发现和 Connection/error 合同，覆盖 0/1/N/失效账号、明确 handle、Composio/API Key 与正确环境。保留现有非 App 功能的兼容验证。
2. App Skill 初版随前三个代表面板验证；OpenClaw 入口迁移与 Skill 同时验收，原 mx_* 能力与新远程工具分别记录。
3. 十个场景持续记录实际 Agent 犯错/缺失，窄修共同 recipe，收敛最终稳定版本。
4. 在干净客户端目录分别由真实 Codex、Claude Code 和 OpenClaw/另一实际 Agent 独立完成：新建、修改、持久化重开、使用自己的连接、音乐交互、分享/fork 到第二位用户。模型最后回答和进程 exit 0 不作唯一通过依据。
5. 分别检查 npm tarball 的 skills/files、ClawHub 分发内容和各客户端实际工具广告；安装/授权/调用/返回网站使用形成可追踪入口。只有内容与实际客户端通过后才标记最终 Skill 可发布。

初版允许先打通生成/修改已有能力，最终版本以用户数据与 fork、十场景和新增能力的真实闭环作为门槛。此方案不授权 npm/ClawHub 发布，不替用户执行第三方消息或账号迁移。

## 分发、引流与待决策

Skill 可随启用的插件分发，但可发现/已加载不代表工具可调用。按 [OpenClaw Skills](https://docs.openclaw.ai/tools/skills) 核对 metadata gate、路径/安装覆盖与 npm/ClawHub 两个入口；共同内容采用 [Agent Skills](https://agentskills.io/specification) 的轻量 frontmatter、按需 references 和独立资源。

为不支持 remote Skills 扩展的客户端提供可安装的相同正文，开发细节仍从实际 guide 获取；不能假定每个客户端都会读 resources/list。API 的共同 App Skill 暴露、插件原生 MCP/薄适配选择、旧 action 的兼容清单与最低宿主版本尚待验证后固化。

引流验收串起“发现/安装 → 正确环境授权 → 首次能力调用 → 创建可运行 App → 回到主站使用 → 分享/复制”；使用既有产品事件与最小来源信息核对，具体统计合同另行设计，不记录凭据/私密供应商响应。最终 Skill 的验收目标是独立 Agent 实际完成用户任务，不是正文长度或工具名出现次数。
