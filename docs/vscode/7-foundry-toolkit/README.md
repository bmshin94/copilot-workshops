---
slug: vscode/7-foundry-toolkit
title: "Optional: Incorporate Foundry"
description: "Build a grounded Backer Concierge with VS Code and Microsoft Foundry Toolkit in three focused modules."
authors:
  - juliamuiruri4
lastUpdated: 2026-09-16
---

| [← Previous lesson: Iterating on GitHub Copilot's work][previous-lesson] |
|:--|

The required VS Code harness is complete after Exercise 6. This optional extension uses GitHub Copilot Chat and Microsoft Foundry Toolkit in VS Code to turn the Tailspin catalog into a Backer Concierge, deploy it as a hosted agent, and connect it to the site through a local proxy.

## Scenario

Backers ask questions that filters cannot answer: which game suits someone who loves git puns, or what makes one puzzle game a better fit than another? Tailspin Toys needs a concierge that recommends real catalog titles, asks a clarifying question when needed, and earns trust by admitting when funding numbers or other facts are unavailable.

## Modules

Each module ends with a working checkpoint. The same learner repository, feature branch, and Foundry project carry through all three; there is no project recreation between modules.

| Module | Completion checkpoint |
|--------|-----------------------|
| [1. Prepare a project and model][module-1] | Catalog exported and deployed model tested against grounding rules |
| [2. Build and deploy an agent][module-2] | Local agent debugged and hosted agent tested |
| [3. Connect the agent to the site][module-3] | Local proxy and accessible widget tested end to end |

> [!IMPORTANT]
> Microsoft Foundry Toolkit and hosted agents are in public preview. These modules create billable Azure resources, including a model deployment and a hosted agent. Subscription permissions, region availability, quota, and cost can limit participation.

## Choose a starting point

The extension builds on your Tailspin Toys repository, not the workshop documentation repository.

1. Confirm the required workshop work is saved, committed, and pushed before starting the optional feature.
2. Review the selected Azure subscription, region, permissions, quota, and estimated costs before approving resource creation.
3. Start with [Prepare a project and model][module-1]. If resuming, use that module's checkpoint to identify your existing repository, branch, project, and deployment before continuing.
4. When stopping after any module, follow [Clean up your resources][cleanup] unless you intentionally keep resources for the next module and accept ongoing costs.

## Clean up your resources

Cleanup applies even if you stop after creating only the project and model. It does not require a scaffolded agent or an `azd` project.

> [!WARNING]
> Deleting resources is destructive. `azd down --purge` can permanently remove the Foundry project, model deployment, and hosted agent managed by that deployment. Resource-group deletion removes everything in the group. Never delete a shared resource group or resources owned by another exercise or person.

1. Save the code you want to keep in the Tailspin Toys repository, excluding credentials and local environment files. Stop any local agent, Functions host, and site processes you started.
2. In Azure, verify the active subscription and inspect the resources in `rg-tailspin-toys`. Confirm which project, model, hosted agent, and supporting resources belong to this optional exercise before deleting anything.
3. If you deployed with `azd`, open the generated deployment directory and verify the selected `azd` environment and the resources it manages. Only if they are dedicated to this exercise, run the following command and review its confirmation. This is one cleanup option, not a prerequisite for the next step.

   ```bash
   azd down --purge
   ```

4. If you created the project and model without an `azd` deployment, or resources remain afterward, use Azure to remove only the resources you own. If and only if the entire `rg-tailspin-toys` group is dedicated to this exercise and the Azure CLI targets the verified subscription, the following is an alternative to individual deletion. It skips confirmation and returns before deletion finishes.

   ```bash
   az group delete --name rg-tailspin-toys --yes --no-wait
   ```

5. Confirm in Azure that deletion completed and that no billable resources from this exercise remain. If permissions prevent deletion, ask the subscription owner to remove the identified resources; stopping local processes alone does not stop Azure charges.

## Resources

- [Foundry Toolkit for Visual Studio Code][foundry-toolkit]
- [Microsoft Foundry agent extension overview][foundry-extension]

| [Next module: Prepare a project and model →][module-1] |
|--:|

[previous-lesson]: ../6-iterating/
[module-1]: 1-project-and-model/
[module-2]: 2-build-and-deploy/
[module-3]: 3-connect-to-site/
[cleanup]: #clean-up-your-resources
[foundry-toolkit]: https://code.visualstudio.com/docs/intelligentapps/overview
[foundry-extension]: https://learn.microsoft.com/azure/developer/azure-developer-cli/extensions/azure-ai-foundry-extension
