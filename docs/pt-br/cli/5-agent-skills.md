---
title: "Lição 5 - Criar e usar uma skill quality-checks"
description: "Peça ao Copilot que crie verificações de qualidade reutilizáveis com scripts de shell incluídos, examine a skill e execute-a na branch de filtragem."
authors:
  - geektrainer
lastUpdated: 2026-09-11
---

Seu recurso de filtragem está implementado e verificado com os comandos npm existentes. Agora você reunirá essas verificações em uma **skill de agente** reutilizável. Permaneça na mesma sessão e branch de filtragem durante as Lições 4–8; esta lição não cria um pull request.

Nesta lição, você vai:

- voltar ao modo **Interactive** antes de criar personalizações.
- pedir ao Copilot que crie `quality-checks` e pare para você examiná-la.
- executar as quatro verificações pelos scripts incluídos e comprovar que um argumento que indica um único arquivo de teste seleciona apenas esse arquivo.
- salvar um checkpoint da skill junto com o recurso de filtragem.

## Instruções, scripts e recursos

Skills reúnem instruções de tarefas reutilizáveis, scripts executáveis e recursos de apoio que um agente carrega sob demanda. Agentes personalizados definem papéis especializados, instruções e ferramentas disponíveis. Eles são complementares: um agente personalizado pode executar scripts, incluindo os fornecidos com uma skill.

Uma skill do repositório fica em `.github/skills/<skill-name>/SKILL.md`, com `name` e `description` no frontmatter e instruções em Markdown. Scripts e outros recursos ficam ao lado desse arquivo. Você pedirá ao Copilot que gere `.github/skills/quality-checks/SKILL.md` e `scripts/`, em vez de copiar uma solução pronta. A [especificação de Agent Skills][skill-spec] descreve o formato.

O Copilot usa a descrição de uma skill descoberta para decidir quando carregá-la. Não presuma que uma skill nova seja descoberta imediatamente em uma sessão já aberta; a seção de execução inclui uma alternativa de leitura explícita. Um formato portável não elimina os pré-requisitos do shell ou do projeto.

## Criar a skill

Volte ao modo **Interactive** antes de enviar o prompt. Mantenha a cópia de trabalho e a branch atuais. Se você começou com um modelo antigo que já tem essa skill, examine-a e amplie-a em vez de sobrescrever suas personalizações.

```plaintext
Crie uma skill de agente quality-checks reutilizável em .github/skills/quality-checks/SKILL.md com um subdiretório scripts/. Primeiro examine package.json, README, a configuração de testes e as instruções do repositório para conhecer as verificações e os pré-requisitos existentes. Identifique a cópia de trabalho e a branch atuais. Se esta skill já existir, examine-a e amplie-a em vez de sobrescrevê-la.

Detecte o ambiente de execução pretendido e pergunte se houver ambiguidade. Gere apenas a implementação adequada a ele: scripts Bash .sh para macOS/Linux/WSL ou scripts PowerShell .ps1 para Windows nativo. Não gere scripts .mjs nem ambas as implementações de shell e não exija a instalação de outro shell ou runtime.

Crie pequenos wrappers para lint, testes de unidade, testes de ponta a ponta e verificações de tipos usando os scripts existentes de package.json. Os comandos atuais são npm run lint, npm run test:unit, npm run test:e2e e npm run typecheck:all; confirme-os nesta cópia de trabalho. Limite os wrappers à validação da raiz e do manifesto pretendidos e à invocação dos scripts npm existentes, propagando argumentos, saída e código de saída. Não inclua verificações de portas, código de gerenciamento de processos ou encerramento de processos nos wrappers. Mantenha o design simples, sem estruturas ou abstrações desnecessárias.

Cada script deve resolver a raiz do repositório a partir da própria localização e verificar se a raiz calculada é o diretório que contém o package.json pretendido desta cópia de trabalho. Se não for, o script deve falhar com uma mensagem clara; não dependa da descoberta de pacotes em diretórios ancestrais pelo npm. O script deve funcionar a partir de qualquer diretório atual e em worktrees ou caminhos com espaços, encaminhar argumentos ao script npm subjacente, preservar stdout e stderr e retornar o código de saída do comando que falhar. O wrapper é responsável pelo separador de argumentos do npm: insira -- uma única vez antes dos argumentos encaminhados e faça com que quem o invoca forneça os argumentos da ferramenta de destino diretamente, sem um -- extra. No PowerShell, trate explicitamente as falhas de comandos nativos para impedir que um comando npm com falha pareça ter sido bem-sucedido.

Forneça a SKILL.md um frontmatter válido com name e description, uma sequência de execução concisa e orientações para solucionar problemas. Instrua o Copilot a realmente executar os scripts incluídos, não apenas listar ou executar os comandos npm subjacentes. Documente os caminhos exatos dos scripts, os pré-requisitos e exemplos corretos de invocação, incluindo um exemplo que use um único arquivo existente de testes de unidade. Mantenha SKILL.md reutilizável e sem caminhos absolutos da cópia de trabalho específicos de uma máquina. Use invocação explícita de bash sem depender de bits de permissão de execução ou invocação adequada de PowerShell sem alterar amplamente ou contornar a política de execução.

Coloque o webServer de build/prévia configurado no Playwright, a reutilização de servidores locais e as verificações de quem é responsável pelo servidor nas instruções de execução de SKILL.md, não nos wrappers. Garanta que qualquer servidor de desenvolvimento iniciado por nós anteriormente esteja parado antes das verificações E2E para que elas não reutilizem o servidor errado. Identifique a cópia de trabalho e o servidor em teste. Pare um servidor apenas se você realmente o iniciou; a correspondência do diretório de trabalho ou do nome do processo não comprova que ele pertence a você. Caso contrário, relate o conflito e pergunte ao usuário como resolvê-lo em vez de encerrar o processo. Nunca encerre processos não relacionados.

Não instale automaticamente software, dependências ou navegadores, não exclua dados, não altere código da aplicação nem mude de branch. Relate pré-requisitos ausentes e peça aprovação antes de qualquer instalação. Não faça commit, push nem abra um pull request. Entregue apenas SKILL.md, os wrappers necessários e um helper compartilhado se necessário; não deixe arquivos temporários de sondagem ou depuração. Depois, pare para que eu possa examiná-los antes de executar as verificações.
```

## Examinar a skill

1. Abra `.github/skills/quality-checks/SKILL.md` e o diretório `scripts/` no editor e examine o diff.
2. Verifique se `name` e `description` descrevem a skill e quando ela se aplica. Leia as instruções, não apenas os metadados.
3. Confirme que a sequência de execução realmente invoca scripts em `.github/skills/quality-checks/scripts/` para lint, testes de unidade, E2E e verificação de tipos.
4. Examine em cada wrapper a resolução da raiz relativa ao script e uma verificação explícita de que o diretório calculado contém o `package.json` pretendido desta cópia de trabalho. Um comando bem-sucedido porque o npm pesquisa em diretórios ancestrais não comprova que a raiz está correta. Verifique caminhos entre aspas, encaminhamento de argumentos, saída visível e códigos de saída em caso de falha; o PowerShell precisa propagar falhas nativas do npm.
5. Verifique o exemplo documentado de um único arquivo de testes de unidade. O wrapper insere o separador `--` do npm, então quem o invoca passa os argumentos da ferramenta de destino diretamente, sem outro separador. Mantenha as instruções reutilizáveis sem caminhos absolutos da cópia de trabalho específicos de uma máquina. Peça ao Copilot que corrija as lacunas antes de executar qualquer coisa.
6. Limite os scripts à validação da raiz e do manifesto e à execução das verificações npm existentes. As decisões sobre portas e processos pertencem a SKILL.md, não a código de gerenciamento de processos em shell. Confirme que apenas servidores realmente iniciados pelo agente podem ser parados; a correspondência do diretório de trabalho ou do nome do processo não determina a quem ele pertence. Os arquivos entregues devem conter apenas a skill, os wrappers necessários e qualquer helper compartilhado necessário, sem arquivos temporários de sondagem ou depuração.

> [!NOTE]
> O Tailspin Toys atualmente exige Node.js 22.13 ou posterior, as dependências do projeto e o Chromium do Playwright para verificações E2E. Confirme os pré-requisitos em README e `package.json` da sua cópia de trabalho. Pré-requisitos ausentes ou uma política de execução do PowerShell que bloqueie a execução precisam de uma resolução aprovada, não de instalação automática, de contorno da política ou de troca silenciosa para npm direto.

## Executar a skill

Confirme que o servidor de desenvolvimento da lição anterior parou. O Playwright compila e serve uma prévia para E2E, mas sua configuração local pode reutilizar um servidor na porta `4321`. Um servidor de outra cópia de trabalho não é evidência válida para seu recurso.

Se o Copilot CLI oferecer `/quality-checks`, selecione-o para invocar explicitamente a skill descoberta e inclua a solicitação abaixo. Se ela não tiver sido descoberta, envie a mesma solicitação diretamente nesta sessão; ler a skill é uma alternativa suportada nesta lição.

```plaintext
Leia .github/skills/quality-checks/SKILL.md e siga as instruções para validar o recurso de filtragem nesta cópia de trabalho. Primeiro examine o código de cada wrapper para verificar se ele calcula o diretório que contém o package.json pretendido desta cópia de trabalho e falha explicitamente para uma raiz inválida, em vez de depender da descoberta de pacotes em diretórios ancestrais pelo npm. Não mova, renomeie, exclua nem modifique arquivos do repositório para simular falhas. Execute realmente os scripts incluídos para lint, testes de unidade, testes de ponta a ponta e verificações de tipos. Execute também o exemplo documentado de um único arquivo de testes de unidade, passando os argumentos da ferramenta de destino diretamente porque o wrapper é responsável pelo separador -- do npm. Verifique nos resultados do executor de testes se APENAS o arquivo indicado foi executado e relate o nome desse arquivo e a quantidade de arquivos de teste executados. Exibir os argumentos ou retornar o código de saída 0 não comprova, por si só, que a seleção está correta.

Relate cada invocação de script e seu resultado, incluindo falhas, verificações ignoradas ou pré-requisitos ausentes. Não substitua silenciosamente um script inutilizável da skill por comandos npm diretos. Identifique a cópia de trabalho e o servidor em teste, pare apenas os servidores que você iniciou e pergunte antes de instalar algo ou parar outro processo. Não altere código da aplicação, não mude de branch, não faça commit, push nem abra um pull request.
```

Examine as chamadas de ferramentas e a saída. Os quatro scripts precisam realmente executar; uma descrição das verificações ou uma verificação ignorada não equivale à aprovação. Para o exemplo de um único arquivo, compare o nome do arquivo solicitado com os resultados reais por arquivo do executor e a quantidade relatada: apenas esse arquivo deve executar. Exibir os argumentos ou retornar o código de saída 0 é insuficiente se outros arquivos também executaram. Uma falha é evidência útil: corrija a skill ou resolva o bloqueio de configuração com aprovação e execute novamente as verificações afetadas. Não pare processos não relacionados nem force a resolução de um conflito de porta.

## Salvar um checkpoint

Após revisar a skill e seus resultados, autorize um checkpoint local:

```plaintext
Revise o diff atual e crie um commit de checkpoint apenas para os arquivos da skill quality-checks. Mantenha a branch de filtragem existente. Não faça push nem crie um pull request.
```

Os arquivos da skill acompanharão a filtragem, o perfil de QA e os testes associados no PR do recurso na Lição 8. Continue nesta mesma cópia de trabalho com a [Lição 6 - Validar a funcionalidade com o MCP do Playwright][next-lesson].

## Mais exemplos de skills

Estes exemplos da comunidade são referências, não tarefas adicionais. Revise seus pré-requisitos e comportamento antes de adotá-los:

- [Fluxo de contribuição: `make-repo-contribution`][contribution-example].
- [Documentos de requisitos: `prd`][prd-example].
- [Diagramas e um script de exportação incluído: `drawio`][drawio-example].
- [Testes de navegador: `webapp-testing`][browser-example].

O exemplo original de contribuição se chama `make-repo-contribution`; modelos antigos do Tailspin usavam outro nome, `make-contribution`. Este workshop não depende de nenhuma dessas skills de contribuição.

[previous-lesson]: ../4-build-filtering/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing
