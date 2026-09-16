---
slug: pt-br/vscode/7-foundry-toolkit
title: "Opcional: Incorporar o Foundry"
description: "Crie um Backer Concierge fundamentado no catálogo com o VS Code e o Microsoft Foundry Toolkit em três módulos focados."
authors:
  - juliamuiruri4
lastUpdated: 2026-09-16
---

| [← Lição anterior: Iterar sobre o trabalho do GitHub Copilot][previous-lesson] |
|:--|

A trilha obrigatória do VS Code está concluída após o Exercício 6. Esta extensão opcional usa o GitHub Copilot Chat e o Microsoft Foundry Toolkit no VS Code para transformar o catálogo da Tailspin em um Backer Concierge, implantá-lo como agente hospedado e conectá-lo ao site por meio de um proxy local.

## Cenário

Os apoiadores fazem perguntas que os filtros não conseguem responder: qual jogo combina com alguém que adora trocadilhos com git, ou o que torna um jogo de quebra-cabeça mais adequado do que outro? A Tailspin Toys precisa de um assistente que recomende títulos reais do catálogo, faça uma pergunta de esclarecimento quando necessário e conquiste a confiança ao admitir quando os números de financiamento ou outros fatos não estão disponíveis.

## Módulos

Cada módulo termina com uma etapa funcional concluída. O mesmo repositório do participante, a mesma branch de funcionalidade e o mesmo projeto do Foundry são usados nos três módulos; o projeto não é recriado entre eles.

| Módulo | Marco de conclusão |
|--------|-----------------------|
| [1. Preparar um projeto e um modelo][module-1] | Catálogo exportado e modelo implantado testado em relação às regras de fundamentação |
| [2. Criar e implantar um agente][module-2] | Agente local depurado e agente hospedado testado |
| [3. Conectar o agente ao site][module-3] | Proxy local e widget acessível testados de ponta a ponta |

> [!IMPORTANT]
> O Microsoft Foundry Toolkit e os agentes hospedados estão em versão prévia pública. Estes módulos criam recursos do Azure sujeitos a cobrança, incluindo uma implantação de modelo e um agente hospedado. Permissões da assinatura, disponibilidade regional, cota e custo podem limitar a participação.

## Escolher um ponto de partida

A extensão usa como base o seu repositório da Tailspin Toys, não o repositório de documentação do workshop.

1. Confirme que o trabalho obrigatório do workshop foi salvo, registrado em commits e enviado ao repositório remoto antes de iniciar a funcionalidade opcional.
2. Revise a assinatura do Azure selecionada, a região, as permissões, a cota e os custos estimados antes de aprovar a criação de recursos.
3. Comece por [Preparar um projeto e um modelo][module-1]. Se estiver retomando o trabalho, use o marco de conclusão desse módulo para identificar o repositório, a branch, o projeto e a implantação existentes antes de continuar.
4. Ao parar após qualquer módulo, siga [Limpar os recursos][cleanup], a menos que decida manter os recursos para o próximo módulo e aceite os custos contínuos.

## Limpar os recursos

A limpeza se aplica mesmo se você parar após criar apenas o projeto e o modelo. Ela não exige a estrutura inicial de um agente nem um projeto `azd`.

> [!WARNING]
> A exclusão de recursos é destrutiva. `azd down --purge` pode remover permanentemente o projeto do Foundry, a implantação do modelo e o agente hospedado gerenciados por essa implantação. A exclusão de um grupo de recursos remove tudo o que ele contém. Nunca exclua um grupo de recursos compartilhado nem recursos que pertençam a outro exercício ou a outra pessoa.

1. Salve o código que deseja manter no repositório da Tailspin Toys, excluindo credenciais e arquivos de ambiente local. Pare todos os processos locais de agente, host do Functions e site que você iniciou.
2. No Azure, verifique a assinatura ativa e inspecione os recursos em `rg-tailspin-toys`. Confirme quais projeto, modelo, agente hospedado e recursos de suporte pertencem a este exercício opcional antes de excluir qualquer coisa.
3. Se você implantou com `azd`, abra o diretório de implantação gerado e verifique o ambiente `azd` selecionado e os recursos que ele gerencia. Somente se eles forem exclusivos deste exercício, execute o comando a seguir e revise a confirmação. Esta é uma opção de limpeza, não um pré-requisito para a próxima etapa.

   ```bash
   azd down --purge
   ```

4. Se você criou o projeto e o modelo sem uma implantação `azd`, ou se ainda houver recursos depois dela, use o Azure para remover apenas os recursos que pertencem a você. Se, e somente se, todo o grupo `rg-tailspin-toys` for exclusivo deste exercício e a CLI do Azure estiver direcionada à assinatura verificada, o comando a seguir será uma alternativa à exclusão individual. Ele pula a confirmação e retorna antes que a exclusão termine.

   ```bash
   az group delete --name rg-tailspin-toys --yes --no-wait
   ```

5. Confirme no Azure que a exclusão foi concluída e que não restam recursos deste exercício sujeitos a cobrança. Se as permissões impedirem a exclusão, peça ao proprietário da assinatura que remova os recursos identificados; parar apenas os processos locais não interrompe as cobranças do Azure.

## Recursos

- [Foundry Toolkit para Visual Studio Code][foundry-toolkit]
- [Visão geral da extensão de agentes do Microsoft Foundry][foundry-extension]

| [Próximo módulo: Preparar um projeto e um modelo →][module-1] |
|--:|

[previous-lesson]: ../6-iterating/
[module-1]: 1-project-and-model/
[module-2]: 2-build-and-deploy/
[module-3]: 3-connect-to-site/
[cleanup]: #limpar-os-recursos
[foundry-toolkit]: https://code.visualstudio.com/docs/intelligentapps/overview
[foundry-extension]: https://learn.microsoft.com/azure/developer/azure-developer-cli/extensions/azure-ai-foundry-extension
