# 云端博客配置核对与待确认方案（2026-09-30）

## 当前实际状态
- 已读取 automation/README.md、CODEX_TASK_PROMPT_CN.md、seo-topics.json 和唯一导入工作流。
- 当前可访问的云端任务检索返回 3 项：已完成的 AI 翻译 SRT 一次性任务，以及两个已暂停的 BuyingAgent 监控任务。没有本仓库博客任务。未更新任何其他任务，未新建任务。需用户提供原博客任务链接/ID，或切换到保存它的账号/工作区后再检索更新。
- 目标时间为每日 10:00 Asia/Shanghai；未保存到实际任务。确认原任务后使用以下日历配置更新原 ID：
```text
BEGIN:VEVENT
DTSTART;TZID=Asia/Shanghai:20261001T100000
RRULE:FREQ=DAILY;BYHOUR=10;BYMINUTE=0;BYSECOND=0
END:VEVENT
```
若配置发生在该时间后，重新计算下一次北京时间 10:00，不照抄过去日期。
- 云端 prompt 应要求每次从 main 读取 automation/EDITORIAL_PLAN_CN.md、automation/CODEX_TASK_PROMPT_CN.md、automation/README.md、automation/seo-topics.json、投递目录和现有导入工作流，再按仓库长期规则执行。附录给出可直接保存的提示正文。
- GitHub 连接已成功读取/写入仓库，permissions.push=true。allow_auto_merge=false。main 分支响应 protected=false，required_status_checks 为空，rulesets=[]。
- 直接读取分支保护细节返回 403 Resource not accessible by integration。已获得分支概要与 rulesets 信息，但连接不能读取/管理完整保护配置；不能声称所有管理权限可用。
- 仓库树只含 .github/workflows/yamatosub-blog-import.yml。它监听 main 的文章 JSON push，合并后执行导入；没有 pull_request 稿件验证检查。这不是合并前门禁。
- 已根据上传说明及 3.2.5 安装包核对 app/api/internal/blog-import/route.ts、lib/rich-text.ts、lib/repositories/blog-automation.ts，详见 automation/BLOG_IMPORT_RULES_V3.2.5.md。源码默认 minimumCharacters=1200，后台可设置 300–20000，比较清洗后纯文本的 JavaScript UTF-16 length。线上部署是否一致、实际最低值仍未确认；不能以默认值冒充线上设置。历史稿导入成功只证明该稿被接受。
- README 的九字段是仓库编辑规范；服务端要求清理后的 title、slug、正文纯文本非空，另接受可选 coverImage。超长字符串在服务端 trim/slice 截断；验证器主动拒绝超长属于更严格投稿要求。
- 本配置 PR 只涉及规划、提示、选题和本核对文档，不改安全设置、凭据、发布工作流，也不改历史文章。

## 今日手动验证：复用已有稿
- 北京时间 2026-09-30 已有一篇文章：automation/blog-inbox/2026-09-30-fix-srt-subtitle-timeline.json。
- PR #2 已于 16:59:17 合并，合并 SHA：0325ff819946a186ef02d5f8f6b946bbe954963f。
- 工作流：https://github.com/Jackdev88/yamatosubblog/actions/runs/36693175776 ，最新 attempt=2，结果 success，Import articles 步骤确实执行成功。
- 导入响应：ok=true，slug=fix-srt-subtitle-timeline，status=draft（已获取响应证据）。网站草稿已确认；并非公开发布。
- 公开博客列表只显示先前翻译入门文章，目标详情页面无法通过检索工具访问。这支持“未确认公开”，不单独作为草稿证据；草稿结论来自接口响应。
- 本次不生成第二篇、不重复导入、不重跑已成功的流程。旧稿存在固定核验尾注，保留历史原稿；新规则只约束后续稿件。
- 幂等检查按文件日期、usedAt、历史 PR、开放 PR、导入响应和官网共同判断；当前只有另一条验证 PR #1 仍开放，不自动处理它。

## 待用户确认的具体配置方案（尚未执行）
1. 先确认并合并本配置 PR，使 main 可读取长期规划；配置 PR 不属于文章自动合并范围。
2. 单独提交新的 PR 稿件验证工作流与验证脚本，不能混入每日文章 PR，不改变已有导入工作流。建议固定检查名 blog-validation，on: pull_request（opened/synchronize/reopened/ready_for_review）；不使用 pull_request_target 执行投稿代码，不读导入凭据，permissions: contents: read。检查总是产生结果，配置 PR 走明确的配置范围校验分支，不因 paths 过滤导致必要检查永远等待。
3. 稿件检查至少验证：相对 base/head 的完整修改范围；只允许一个新增 automation/blog-inbox/YYYY-MM-DD-slug.json 与选题表；不改旧稿；JSON 的九个约定字段/类型/source（coverImage 可选，不能强制旧稿包含）；slug 与文件名日期一致；北京时间同日一篇；main 与 PR 数据中的标题/slug 重复；允许 HTML 标签/属性、href 协议；导入接口的实际长度和字段阈值；主题更新与稿件对应；已用和未知字段不丢失。访问官网重复性与事实核查在任务中执行，并把证据和精确 head SHA 写 PR；机器检查不能替代编辑核验。网络链接检查区分正常重定向、认证/403 和临时失败，不把不确定当通过。
4. 对配置 PR 使用单独的文档/JSON 校验路径，确认变更范围只涉及获授权配置；它通过检查也不能由日常任务自动合并。增加验证脚本的正反测试（危险 HTML、重复标题、超范围改文件、日期重投、旧条目被覆盖、长度边界）。
5. 验证检查真实运行成功后，在 Settings → Branches/Rules → main 建立/保留保护：Require a pull request before merging；Require status checks to pass，勾选 blog-validation 并要求与 base 保持最新；禁止强推/删除和自动化绕过。已有审核要求保留，自动任务不能代替审核员。不要将合并后才运行的 import-draft 设为 PR 必需检查，否则合并前永远不会出现。
6. Settings → General → Pull Requests → Allow auto-merge。确认 GitHub 连接对本仓库有 Contents 与 Pull requests 写权限、Actions/Checks 读权限；管理规则由管理员操作。导入只需现有 Actions secrets，已成功使用，无需展示或更换凭据。不能只给 Actions 的 GITHUB_TOKEN 写权限就声称云端 GitHub 连接有合并权限。
7. 3.2.5 源码规则已取得；独立配置 PR 复用源码清洗/纯文本算法，最低长度采用源码默认 1200 的明确标注模式。线上实际值通过可信配置确认后再启用文章自动合并；网站保持 draft，不改自动公开状态。
8. 确认原博客任务 ID 后，更新那个任务的 prompt、时区和日历；回读保存结果，明确下一次执行时间。由于当前缺失原任务，目标时间不能报告为已保存。
9. 新流程端到端验收需未来一天的一篇新稿：创建 PR → 最新 head 的必要检查成功 → GitHub auto_merge 接受 → 合并 SHA 对应导入成功 → draft/published 分别核对。今天仅做配置与已有投递的手动验证，不伪称已经测试了自动合并。

## 可保存到原任务的 prompt
执行 Jackdev88/yamatosubblog 的中文博客编辑与投递。每次从仓库 main 读取 automation/EDITORIAL_PLAN_CN.md、automation/CODEX_TASK_PROMPT_CN.md、automation/README.md、automation/seo-topics.json 和当前博客导入工作流；严格执行这些长期规则，不依赖临时附件或聊天记忆。访问 https://www.yamatosub.com/blog 核对已有内容。按 Asia/Shanghai 日期同日最多一篇，已有稿先核对并处理原 PR/导入，不另建稿。交替用户场景，先官方研究再写作，事实不可核验就换题。保留历史和现有 JSON 字段约定。日常 PR 仅改一个新增文章 JSON 与选题表；只有本任务稿件、范围正确、内容自检与实际接口长度规则确认、最新 SHA 必要检查通过、保护规则与 GitHub 自动合并可用时才启用自动合并，不直接推 main，不绕过审核或失败检查。配置未就绪时保留 PR 并报告具体阻塞。合并后检查对应导入工作流与业务响应，成功不重试，草稿不擅自公开。待写不足七篇时研究补题并先去重。每次报告主题/目标用户、PR与合并状态、工作流结果、网站草稿/公开/无法确认和需要用户处理的事项。

## 官方配置依据
- https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/automatically-merging-a-pull-request
- https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-auto-merge-for-pull-requests-in-your-repository
GitHub 自动合并需要仓库启用，并以保护规则的必要审核/检查为依据；启用操作与实际合并完成需分别核对。
