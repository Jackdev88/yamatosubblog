# Codex / ChatGPT Plus 云端定时任务建议 Prompt

每天执行一次。使用连接的 YamatoSub GitHub 仓库，不依赖本地电脑，也不要调用 OpenAI API。

任务：
1. 读取 `automation/seo-topics.json`，选择第一条 `status=pending` 的主题；如果没有待写主题则结束，不创建文章。
2. 围绕该主题检索必要的公开资料，优先官方文档和一手资料。文章必须真正解决一个字幕处理、日语字幕翻译或音视频字幕问题，不要为了关键词堆砌重复内容。
3. 检查仓库 `automation/blog-inbox/` 和已有博客内容，避免相同主题、标题或搜索意图重复。
4. 生成 1500～2500 字中文教程，结构包括：明确标题、简短导语、2～6 个有信息量的小标题、实际操作步骤或排错方法、与 YamatoSub 现有页面自然相关的内部链接、简短 FAQ。不要虚构产品能力，不要写夸张的“最佳/第一”等营销结论。
5. 将文章保存为 `automation/blog-inbox/YYYY-MM-DD-<slug>.json`。JSON 字段必须符合 `automation/README.md`，`content` 使用安全 HTML（p/h2/h3/ul/ol/li/strong/a 等），不要包含 script/style/iframe。
6. slug 使用简短英文小写与连字符；SEO title 和 description 自然表达，不堆关键词。
7. 修改本次主题的 `status` 为 `used`，并增加 `usedAt` 为当天 YYYY-MM-DD。
8. 提交并推送这两个文件的修改。不要修改应用代码、配置、依赖、数据库或其他文件。

默认把站内自动导入状态保持为 draft，文章进入 YamatoSub 后由管理员审核发布。
