### 一、推荐学习网址（免费 + 权威 + 中文友好）

| 类型                   | 名称                                         | 地址                                                                                                                             | 特点                                         |
| -------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
| 🌐 **综合入门（中文）**      | Linux 命令行艺术（开源书）                           | [https://github.com/brendanmorrison/linux-command-line-book-zh](https://github.com/brendanmorrison/linux-command-line-book-zh) | 免费、结构清晰、面向初学者，含大量类比和原理讲解，PDF/在线版均可读        |
| 🌐 **交互式练习（英文，极推荐）** | Learn Shell (by Codecademy)                | [https://www.codecademy.com/learn/learn-the-command-line](https://www.codecademy.com/learn/learn-the-command-line)             | 免费基础课 + 实时终端模拟，边学边敲，即时反馈，适合零基础“肌肉记忆”训练     |
| 🌐 **深度进阶（英文）**      | The Linux Command Line (TLCL, free online) | [https://linuxcommand.org/tlcl.php](https://linuxcommand.org/tlcl.php)                                                         | 经典权威教材，覆盖 Bash 脚本、管道、正则、环境配置等，适合进阶为日常生产力工具 |
| 🌐 **中文速查 + 实战技巧**   | 命令行手册（cmdline.dev）                         | [https://cmdline.dev](https://cmdline.dev/)                                                                                    | 简洁搜索式手册，每条命令配示例、参数说明、常见错误，适合学完后快速复习或查漏补缺   |


> 🔔 提示：建议**先完成 Learn Shell（约2–3小时）建立手感 → 再精读《Linux 命令行艺术》前8章 → 后续用 TLCL 和 cmdline.dev 深挖与查证**。

---

### ✅ 二、核心学习内容（分阶段，不罗列全部命令）

|阶段|关键能力|必掌握内容（带目的说明）|
|---|---|---|
|**🌱 零基础入门**  <br>（1–3天）|认识终端、安全操作、文件导航|`pwd`, `ls -la`, `cd`, `mkdir`, `touch`, `cp`, `mv`, `rm -i`  <br>→ _目标：能安全地在自己电脑中找文件、建目录、备份文档_|
|**🛠️ 日常提效**  <br>（3–7天）|快速处理文本、自动化小任务|`cat`, `less`, `head/tail -n`, `grep -r "关键词" .`, `find`, `history`, `Ctrl+R`, `alias`  <br>→ _目标：5秒内搜出日志里的报错、一键清理临时文件、自定义常用命令别名_|
|**⚙️ 进阶掌控**  <br>（1–2周）|编写脚本、连接工具链、管理环境|Bash 变量/条件判断/循环、`$PATH` 原理、`.bashrc`/`.zshrc` 配置、`curl`/`jq` 处理 API、`cron` 定时任务  <br>→ _目标：写一个每日自动备份笔记目录并压缩的脚本；用 curl + jq 抓取 GitHub trending 并格式化输出_|
|**🚀 精通实战**  <br>（持续实践）|工程级协作、DevOps 基础、故障排查|`git` 命令流（非 GUI）、`ssh` 远程管理、`systemctl` 服务控制、`journalctl` 查日志、`ps`, `top`, `netstat`, `lsof` 排查卡顿/端口占用  <br>→ _目标：独立部署一个静态网站到云服务器；诊断本地开发环境端口冲突；读懂 CI 日志中的失败环节_|

---

### ✅ 三、4个渐进式实战项目（附简要步骤）

|项目|目标|CLI 技能应用|建议耗时|
|---|---|---|---|
|**📁 个人笔记自动归档器**|每天把 `~/Downloads/*.md` 移到 `~/Notes/daily/2026-04-17.md` 并添加时间戳|`date`, `mv`, `cp`, `if [ -f ]`, `crontab -e||