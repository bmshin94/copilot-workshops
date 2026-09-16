---
slug: zh-cn/vscode/7-foundry-toolkit
title: "可选：集成 Foundry"
description: "通过三个聚焦的模块，使用 VS Code 和 Microsoft Foundry Toolkit 构建基于目录数据的 Backer Concierge。"
authors:
  - juliamuiruri4
lastUpdated: 2026-09-16
---

| [← 上一课：迭代 GitHub Copilot 的工作][previous-lesson] |
|:--|

完成练习 6 后，VS Code 学习路径的必修部分就结束了。本可选扩展使用 VS Code 中的 GitHub Copilot Chat 和 Microsoft Foundry Toolkit，将 Tailspin 目录转化为 Backer Concierge，将其部署为托管代理，并通过本地代理服务连接到网站。

## 场景

支持者提出的问题，往往不是筛选器能够回答的：哪款游戏适合喜欢 git 双关语的人？一款解谜游戏为什么比另一款更适合自己？Tailspin Toys 需要一位推荐助手，能够推荐目录中真实存在的游戏，在必要时提出澄清问题，并在缺少筹款金额或其他信息时坦诚说明，以此赢得信任。

## 模块

每个模块都以可运行的成果作为完成检查点。三个模块始终使用同一个学员存储库、功能分支和 Foundry 项目，模块之间无需重新创建项目。

| 模块 | 完成检查点 |
|--------|-----------------------|
| [1. 准备项目和模型][module-1] | 已导出目录，并根据数据依据规则测试已部署的模型 |
| [2. 构建并部署代理][module-2] | 已调试本地代理，并测试托管代理 |
| [3. 将代理连接到网站][module-3] | 已对本地代理服务和无障碍小组件进行端到端测试 |

> [!IMPORTANT]
> Microsoft Foundry Toolkit 和托管代理目前处于公共预览阶段。这些模块会创建计费的 Azure 资源，包括模型部署和托管代理。订阅权限、区域可用性、配额和费用可能限制参与。

## 选择起点

本扩展基于学员的 Tailspin Toys 存储库，而不是工作坊文档存储库。

1. 开始可选功能前，确认必修工作坊中的工作已保存、提交并推送。
2. 批准创建资源前，检查所选 Azure 订阅、区域、权限、配额和预计费用。
3. 从[准备项目和模型][module-1]开始。如果是继续之前的工作，先根据该模块的检查点确认已有存储库、分支、项目和部署，再继续操作。
4. 完成任一模块后如果要停止，请执行[清理资源][cleanup]，除非决定为下一个模块保留资源，并接受持续产生的费用。

## 清理资源

即使只创建了项目和模型就停止，也需要清理资源。清理不要求已生成代理框架或拥有 `azd` 项目。

> [!WARNING]
> 删除资源是破坏性操作。`azd down --purge` 可能永久删除该部署管理的 Foundry 项目、模型部署和托管代理。删除资源组会移除组内的所有内容。切勿删除共享资源组，或属于其他练习或他人的资源。

1. 将要保留的代码保存到 Tailspin Toys 存储库，不要包含凭据和本地环境文件。停止之前启动的所有本地代理、Functions 主机和网站进程。
2. 在 Azure 中验证当前订阅，并检查 `rg-tailspin-toys` 中的资源。删除任何内容前，确认哪些项目、模型、托管代理和配套资源属于本可选练习。
3. 如果使用 `azd` 部署，打开生成的部署目录，验证选中的 `azd` 环境及其管理的资源。只有确认这些资源专用于本练习时，才运行以下命令并检查其确认提示。这是一种清理方式，不是执行下一步的前提。

   ```bash
   azd down --purge
   ```

4. 如果创建项目和模型时未使用 `azd` 部署，或执行后仍有资源残留，请通过 Azure 仅移除自己拥有的资源。当且仅当整个 `rg-tailspin-toys` 资源组专用于本练习，且 Azure CLI 指向已验证的订阅时，才可以使用以下命令代替逐项删除。该命令会跳过确认，并在删除完成前返回。

   ```bash
   az group delete --name rg-tailspin-toys --yes --no-wait
   ```

5. 在 Azure 中确认删除已完成，且本练习的计费资源已全部移除。如果权限不足无法删除，请联系订阅所有者移除已确认的资源；仅停止本地进程不会停止 Azure 计费。

## 资源

- [适用于 Visual Studio Code 的 Foundry Toolkit][foundry-toolkit]
- [Microsoft Foundry 代理扩展概述][foundry-extension]

| [下一模块：准备项目和模型 →][module-1] |
|--:|

[previous-lesson]: ../6-iterating/
[module-1]: 1-project-and-model/
[module-2]: 2-build-and-deploy/
[module-3]: 3-connect-to-site/
[cleanup]: #清理资源
[foundry-toolkit]: https://code.visualstudio.com/docs/intelligentapps/overview
[foundry-extension]: https://learn.microsoft.com/azure/developer/azure-developer-cli/extensions/azure-ai-foundry-extension
