---
title: "练习 5 - 创建并使用 quality-checks 技能"
description: "让 Copilot 创建带有配套 shell 脚本的可复用质量检查技能，检查技能内容，并在筛选功能分支上执行。"
authors:
  - geektrainer
lastUpdated: 2026-09-11
---

筛选功能已经实现，并已使用现有 npm 命令完成检查。现在，将这些检查封装为可复用的**智能体技能**。练习 4–8 始终使用同一个筛选功能会话和分支；本练习不创建 pull request。

在本练习中，将：

- 在创建自定义配置前返回 **Interactive** 模式。
- 让 Copilot 创建 `quality-checks`，然后停下来供你检查。
- 通过配套脚本执行全部四项检查，并证明单文件测试参数只会选中指定文件。
- 在筛选功能分支上为技能创建检查点。

## 指令、脚本和资源

技能将可复用的任务指令、可执行脚本和辅助资源打包，供智能体按需加载。自定义智能体定义专业角色、指令和可用工具。两者相辅相成：自定义智能体可以执行脚本，包括技能附带的脚本。

存储库技能位于 `.github/skills/<skill-name>/SKILL.md`，包含带有 `name` 和 `description` 的 frontmatter 以及 Markdown 指令。脚本和其他资源存放在旁边。这里将让 Copilot 生成 `.github/skills/quality-checks/SKILL.md` 和 `scripts/`，而不是复制现成答案。[Agent Skills 规范][skill-spec]介绍了这种格式。

Copilot 根据已发现技能的描述，决定何时加载它。不要假定新技能会立即被已打开的会话发现；运行部分提供了明确读取技能的备用方式。格式可移植并不意味着无需满足 shell 或项目的前提条件。

## 创建技能

发送提示前返回 **Interactive** 模式。保持当前检出目录和分支。如果使用的旧版模板已经包含此技能，应先检查并扩展它，而不是覆盖已有的自定义内容。

```plaintext
在 .github/skills/quality-checks/SKILL.md 中创建可复用的 quality-checks 智能体技能，并添加 scripts/ 子目录。先检查 package.json、README、测试配置和存储库指令，了解现有检查和前提条件。确认当前检出目录和分支。如果此技能已存在，先检查并扩展它，不要覆盖。

识别预期的执行环境；如果无法确定，向我询问。只生成适合该环境的实现：macOS/Linux/WSL 使用 Bash .sh 脚本，原生 Windows 使用 PowerShell .ps1 脚本。不要生成 .mjs 脚本或同时生成两种 shell 实现，也不要要求安装其他 shell 或运行时。

利用 package.json 中现有的脚本，为 lint、单元测试、端到端测试和类型检查创建小型封装脚本。当前命令为 npm run lint、npm run test:unit、npm run test:e2e 和 npm run typecheck:all；请在此检出目录中确认。封装脚本仅负责验证预期根目录和清单文件，然后调用现有 npm 脚本，并传递参数、输出和退出代码。不要在封装脚本中加入端口检查、进程管理代码或进程终止操作。保持设计简单，不要添加不必要的脚手架或抽象。

每个脚本必须根据自身位置解析存储库根目录，并验证推导出的根目录确实包含此检出目录中预期的 package.json。如果不是，明确报错并失败退出；不要依赖 npm 在祖先目录中查找包。脚本应能从任意当前目录运行，并支持工作树及包含空格的路径，将参数转发给底层 npm 脚本，保留 stdout 和 stderr，并返回失败命令的退出代码。npm 参数分隔符由封装脚本负责：在转发的参数前只插入一次 --，调用方直接提供目标工具的参数，不额外添加 --。在 PowerShell 中，显式处理原生命令失败，确保 npm 命令失败时不会显示为成功。

为 SKILL.md 提供有效的 name 和 description frontmatter、简明的执行顺序以及故障排查指导。要求 Copilot 实际运行配套脚本，而不只是列出或运行底层 npm 命令。记录准确的脚本路径、前提条件和调用示例，其中包含一个仅运行某个现有单元测试文件的示例。保持 SKILL.md 可复用，不包含特定机器的检出目录绝对路径。显式使用 bash 调用，不依赖可执行权限位；或者使用适当的 PowerShell 调用，不大范围更改或绕过执行策略。

将 Playwright 配置的构建/预览 webServer、本地服务器复用和服务器归属检查写入 SKILL.md 的执行指令，而不是封装脚本。确保我们先前启动的开发服务器在 E2E 检查前已停止，避免检查复用错误的服务器。确认待测试的检出目录和服务器。只有确实由你启动的服务器才可以停止；工作目录或进程名称匹配不能证明归属。否则应报告冲突，询问用户如何解决，而不是终止进程。绝不终止无关进程。

不要自动安装软件、依赖项或浏览器，不要删除数据、修改应用代码或切换分支。报告缺失的前提条件，并在任何安装前征得批准。不要提交、推送或创建 pull request。只交付 SKILL.md、必需的封装脚本，以及按需提供的共享辅助文件；不要留下临时探测或调试文件。然后停止，让我在运行检查前检查这些文件。
```

## 检查技能

1. 在编辑器中打开 `.github/skills/quality-checks/SKILL.md` 及其 `scripts/` 目录，并检查差异。
2. 检查 `name` 和 `description` 是否说明了技能及其适用场景。阅读指令，不要只看元数据。
3. 确认执行顺序确实调用 `.github/skills/quality-checks/scripts/` 中的脚本，执行 lint、单元测试、E2E 和类型检查。
4. 检查每个封装脚本是否根据自身位置解析根目录，并明确检查推导出的目录是否包含此检出目录中预期的 `package.json`。命令因 npm 搜索祖先目录而成功，并不能证明根目录正确。检查路径是否加引号、参数是否转发、输出是否可见，以及失败时是否正确退出；PowerShell 必须传递原生 npm 命令的失败状态。
5. 检查文档中只运行一个单元测试文件的示例。封装脚本负责插入 npm 的 `--` 分隔符，因此调用方应直接传递目标工具的参数，不再添加分隔符。可复用指令中不应包含特定机器的检出目录绝对路径。在运行任何内容之前，让 Copilot 修正遗漏或问题。
6. 脚本应仅负责根目录和清单文件验证，以及运行现有 npm 检查。端口和进程相关决策应放在 SKILL.md 中，而不是通过 shell 进程管理代码实现。确认只有智能体实际启动的服务器才可以停止；工作目录或进程名称匹配不能证明归属。交付的文件应仅包含技能、必需的封装脚本和必要的共享辅助文件，不含临时探测或调试文件。

> [!NOTE]
> 当前 Tailspin Toys 需要 Node.js 22.13 或更高版本、项目依赖项，以及用于 E2E 检查的 Playwright Chromium。在检出目录的 README 和 `package.json` 中确认前提条件。缺少前提条件或 PowerShell 执行策略阻止运行时，需要经批准的解决方案，而不是自动安装、绕过策略或悄悄改为直接运行 npm。

## 运行技能

确认上一练习的开发服务器已停止。Playwright 会为 E2E 构建并提供预览服务，但其本地配置可以复用端口 `4321` 上的服务器。其他检出目录的服务器不能为当前功能提供有效证据。

如果 Copilot CLI 提供 `/quality-checks`，选择它来显式调用已发现的技能，并附上以下请求。如果未发现技能，直接在此会话中发送相同请求；本练习支持通过读取技能的方式运行它。

```plaintext
读取 .github/skills/quality-checks/SKILL.md，并按照其中的指令验证此检出目录中的筛选功能。先检查每个封装脚本的代码，确认其推导出的目录包含此检出目录中预期的 package.json，且根目录无效时会明确报错并失败退出，而不是依赖 npm 在祖先目录中查找包。不要为模拟失败而移动、重命名、删除或修改存储库文件。实际运行其配套脚本，执行 lint、单元测试、端到端测试和类型检查。同时运行文档中只运行一个单元测试文件的示例，直接传递目标工具的参数，因为 npm 的 -- 分隔符由封装脚本负责。根据测试运行器的结果，确认仅运行了指定文件，并报告该文件名及实际执行的测试文件数量。仅回显参数或返回退出代码 0，不能证明文件选择正确。

报告每次脚本调用及其结果，包括失败、跳过的检查或缺失的前提条件。不要在技能脚本无法使用时悄悄改为直接运行 npm 命令。确认待测试的检出目录和服务器，只停止你启动的服务器，并在安装任何内容或停止其他进程前询问。不要修改应用代码、切换分支、提交、推送或创建 pull request。
```

检查工具调用和输出。四个脚本都必须实际执行；描述检查内容或跳过检查都不算通过。对于单文件示例，将请求的文件名与运行器实际输出的文件结果及报告的数量进行比较：应只运行该文件。如果还运行了其他文件，回显参数或退出代码 0 都不足以证明正确。失败是有用的证据：修正技能，或在获批后解决环境配置阻碍，再重新运行受影响的检查。不要停止无关进程，也不要强行消除端口冲突。

## 保存检查点

检查技能及其运行结果后，授权创建本地检查点：

```plaintext
检查当前差异，仅为 quality-checks 技能文件创建检查点提交。保留现有筛选功能分支。不要推送或创建 pull request。
```

技能文件将与筛选功能、QA 配置和相关测试一起纳入练习 8 的功能 PR。继续在同一检出目录中完成[练习 6 - 使用 Playwright MCP 验证功能][next-lesson]。

## 更多技能示例

以下社区示例仅供参考，不是额外任务。采用前先检查其前提条件和行为：

- [贡献工作流：`make-repo-contribution`][contribution-example]。
- [需求文档：`prd`][prd-example]。
- [图表及配套导出脚本：`drawio`][drawio-example]。
- [浏览器测试：`webapp-testing`][browser-example]。

上游贡献示例名为 `make-repo-contribution`；旧版 Tailspin 模板使用另一个名称 `make-contribution`。本工作坊不依赖其中任何一个贡献技能。

[previous-lesson]: ../4-build-filtering/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing
