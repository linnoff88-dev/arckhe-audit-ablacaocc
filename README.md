# audit-ablacaocc â€” auditoria de ablaÃ§Ã£o para Claude Code

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**ðŸ‡§ðŸ‡· [PortuguÃªs](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [EspaÃ±ol](README.es.md)**

Skill de **diagnÃ³stico** (somente leitura) que audita seu `CLAUDE.md`, suas skills e seus hooks, classifica cada instruÃ§Ã£o e propÃµe uma versÃ£o mÃ­nima â€” sem tocar em nenhum arquivo.

> Toda linha da sua configuraÃ§Ã£o Ã© **culpada de complexidade atÃ© provar utilidade**.

## ðŸ“– Guia de uso

Guia completo (landing + passo a passo): **https://linnoff88-dev.github.io/

- Skill: [`SKILL.md`](SKILL.md)
- Base conceitual: [`doc/`](doc/) â€” resumo da palestra do Boris Cherny (Y Combinator, um dia apÃ³s o lanÃ§amento do Opus 5)

---

## Ãndice

1. [A visÃ£o do Boris: por que apagar a prÃ³pria config](#1-a-visÃ£o-do-boris-por-que-apagar-a-prÃ³pria-config)
2. [Por que essa faxina Ã© Ãºtil na prÃ¡tica](#2-por-que-essa-faxina-Ã©-Ãºtil-na-prÃ¡tica)
3. [O que a skill faz (e o que ela nunca faz)](#3-o-que-a-skill-faz-e-o-que-ela-nunca-faz)
4. [InstalaÃ§Ã£o](#4-instalaÃ§Ã£o)
5. [Como usar](#5-como-usar)
6. [Dica de uso: chame skills o tempo todo](#6-dica-de-uso-chame-skills-o-tempo-todo)
7. [Ciclo recomendado](#7-ciclo-recomendado)
8. [Guia prÃ¡tico: publicar no Git e no portal](#8-guia-prÃ¡tico-publicar-no-git-e-no-portal)

---

## 1. A visÃ£o do Boris: por que apagar a prÃ³pria config

Boris Cherny criou o Claude Code na Anthropic. A tese dele, resumida:

**ConfiguraÃ§Ã£o envelhece.** Cada instruÃ§Ã£o que vocÃª escreve Ã© o conserto da fraqueza de um modelo especÃ­fico. Quando o modelo seguinte deixa de ter aquela fraqueza, a instruÃ§Ã£o vira peso morto â€” e continua sendo relida a cada uso, ocupando contexto e limitando o modelo.

Foi literalmente o que aconteceu no Claude Code: boa parte do prompt de sistema existia sÃ³ para corrigir comportamentos que o modelo deveria saber executar sozinho. Com o Opus 5, a equipe **removeu mais de 80% do prompt de sistema**. O que sobrou Ã© quase todo seguranÃ§a, permissÃµes, anÃ¡lise estÃ¡tica e interface.

**AblaÃ§Ã£o** Ã© o mÃ©todo. Ã‰ termo de pesquisa: vocÃª remove algo para medir o impacto. Aplicado a prompt, significa apagar tudo e trazer de volta linha por linha, descobrindo o que cada linha realmente faz. O conselho do Boris para quem sÃ³ *usa* Claude Code:

> A cada ~6 meses, e principalmente em lanÃ§amento grande de modelo: apague seu `CLAUDE.md`, apague suas skills, apague seus hooks â€” e veja o que o modelo faz sem eles.

A reconstruÃ§Ã£o tem quatro etapas, e a quarta Ã© a que importa:

1. Apague a config.
2. Use a ferramenta em **trabalho real**, nÃ£o em teste hipotÃ©tico.
3. Observe onde ela vai bem e onde tropeÃ§a.
4. SÃ³ devolva uma instruÃ§Ã£o **depois de ver o modelo falhar repetidamente na mesma coisa**.

Duas razÃµes para essa disciplina: vocÃª Ã© um pÃ©ssimo previsor de qual instruÃ§Ã£o o modelo precisa, e cada linha mantida custa contexto em toda execuÃ§Ã£o.

Os outros pilares da visÃ£o:

- **EspecificaÃ§Ã£o excessiva Ã© o erro mais comum** â€” e Ã© mais frequente em quem tem anos de engenharia. Roteirizar "faÃ§a A, depois B, depois C" funcionava com modelos antigos; hoje impede o modelo de achar um caminho melhor. Escreva **tarefa + guardrails + critÃ©rios de saÃ­da**, e volte depois para conferir.
- **VerificaÃ§Ã£o Ã© a alavanca que quase todo mundo erra.** Prompt curto com um jeito real de o modelo conferir o prÃ³prio trabalho ganha de prompt gigante sem verificaÃ§Ã£o. O exemplo clÃ¡ssico: reescrever o app Electron em Swift, rodar o original numa VM, tirar screenshot, comparar pixel a pixel, nÃ£o parar atÃ© terminar. Produzir â†’ observar â†’ comparar â†’ condiÃ§Ã£o de parada.
- **Mire acima do teto.** DÃª uma tarefa um pouco mais difÃ­cil do que vocÃª acha que o modelo aguenta, e reteste seus fracassos antigos a cada versÃ£o nova â€” o motivo da falha pode ter desaparecido.
- **Trabalhar com modelo virou ciÃªncia empÃ­rica**, nÃ£o teÃ³rica. Ã‰ mais parecido com conhecer uma criatura viva do que configurar um sistema. O nÃ­vel certo de instruÃ§Ã£o Ã© o mesmo que vocÃª usaria orientando um colega de trabalho.
- **NÃ£o existe truque secreto.** O ciclo Ã©: tarefa difÃ­cil â†’ meios de verificar â†’ observar onde trava â†’ corrigir *aquilo* (prompt melhor, ou uma skill, ou um MCP) â†’ repetir.

Para testar sozinho, Boris citou duas alavancas: a flag de prompt de sistema na inicializaÃ§Ã£o, e `CLAUDE_CODE_SIMPLE=1` (remove todos os prompts de sistema, inclusive os das ferramentas â€” Ã© a linha de base de ablaÃ§Ã£o usada internamente). Detalhe contraintuitivo: sem os prompts, o modelo fica *ligeiramente mais inteligente*; eles sÃ£o mantidos porque fazem o produto se comportar como a pessoa espera.

## 2. Por que essa faxina Ã© Ãºtil na prÃ¡tica

Quem usa Claude Code hÃ¡ um tempo acumula a mesma camada de sedimento:

| Sintoma | O que costuma ser |
|---|---|
| `CLAUDE.md` de 300 linhas com exceÃ§Ãµes datadas | correÃ§Ãµes de modelos que jÃ¡ saÃ­ram de cena |
| A mesma regra no `CLAUDE.md` e dentro de 3 skills | redundÃ¢ncia que gera conflito quando uma das cÃ³pias muda |
| Skill que ensina "como pensar" em 400 linhas | raciocÃ­nio genÃ©rico que o modelo jÃ¡ faz sozinho |
| Passo a passo rÃ­gido de 12 etapas | microgerenciamento que trava a rota melhor |
| Duas regras que se contradizem | o modelo escolhe uma, e vocÃª nÃ£o sabe qual |
| Nada dizendo *como conferir* que ficou certo | o buraco mais caro de todos |

O custo Ã© real e composto: contexto queimado em toda chamada, autonomia reduzida, comportamento inconsistente, e regras zumbis que ninguÃ©m ousa apagar porque ninguÃ©m sabe mais o que elas seguram.

O ganho da faxina:

- **Contexto de volta** para o trabalho de verdade.
- **Menos contradiÃ§Ã£o** â€” regra que sÃ³ existe em um lugar nÃ£o briga com nada.
- **Mais autonomia** â€” critÃ©rios de saÃ­da deixam o modelo achar caminho melhor que o seu.
- **Config auditÃ¡vel** â€” vocÃª passa a saber *por que* cada linha estÃ¡ lÃ¡, porque cada uma sobreviveu a uma pergunta.
- **VerificaÃ§Ã£o entra na conta** â€” a auditoria expÃµe onde falta um jeito objetivo de provar que terminou certo.

O objetivo **nÃ£o** Ã© maximizar a reduÃ§Ã£o. Ã‰ maximizar:

**qualidade + autonomia + verificabilidade Ã· complexidade**

Por isso a skill preserva explicitamente o que o modelo nÃ£o consegue inferir: identidade do projeto, caminhos e fontes de verdade, branding, seguranÃ§a, compliance, integraÃ§Ãµes, contratos de interface e convenÃ§Ãµes internas.

## 3. O que a skill faz (e o que ela nunca faz)

**Faz:**

- lÃª `CLAUDE.md` (projeto + global), skills, hooks e `settings.json`;
- classifica cada instruÃ§Ã£o â€” `CONTEXTO`, `GUARDRAIL`, `CRITÃ‰RIO DE QUALIDADE`, `VERIFICAÃ‡ÃƒO`, `INTEGRAÃ‡ÃƒO/FERRAMENTA`, `PROCEDIMENTO REPETÃVEL`, `MICROGERENCIAMENTO`, `REDUNDÃ‚NCIA`, `LEGADO/OBSOLETA`, `AMBÃGUA/NÃƒO COMPROVADA`;
- decide `KEEP` / `SIMPLIFY` / `MOVE` / `MERGE` / `TEST` / `REMOVE` (na dÃºvida, `TEST`);
- audita skill por skill â€” `KEEP` / `SIMPLIFY` / `MERGE` / `SPLIT` / `LOAD-ON-DEMAND` / `CONVERT-TO-CONTEXT` / `DELETE-CANDIDATE`;
- entrega relatÃ³rio em 10 seÃ§Ãµes: resumo executivo, mÃ©tricas, problemas por arquivo, candidatas Ã  remoÃ§Ã£o, redundÃ¢ncias e conflitos, skills, `CLAUDE.md` mÃ­nimo proposto, skills propostas, plano de teste de ablaÃ§Ã£o (versÃµes A/B/C) e top 10 mudanÃ§as por impacto Ã· risco.

**Nunca faz:** editar, mover, apagar, sobrescrever config ou commitar. Ã‰ diagnÃ³stico. Aplicar Ã© decisÃ£o sua, num pedido separado â€” e Ã© de propÃ³sito: auditoria que jÃ¡ sai mexendo Ã© auditoria em que vocÃª nÃ£o confia.

DiferenÃ§a de vizinhanÃ§a: `memory-audit` cuida da **memÃ³ria** guardada da sessÃ£o. Esta aqui cuida da **configuraÃ§Ã£o do agente**.

## 4. InstalaÃ§Ã£o

```bash
git clone https://github.com/linnoff88-dev/audit-ablacaocc.git
mkdir -p ~/.claude/skills/audit-ablacao
cp audit-ablacaocc/SKILL.md ~/.claude/skills/audit-ablacao/SKILL.md
```

Ou, para instalar sÃ³ no projeto atual:

```bash
mkdir -p .claude/skills/audit-ablacao
cp /caminho/audit-ablacaocc/SKILL.md .claude/skills/audit-ablacao/SKILL.md
```

Reinicie a sessÃ£o do Claude Code para ele carregar a skill.

## 5. Como usar

Chamada direta:

```
/audit-ablacao
```

Ou por gatilho, em linguagem natural:

- "faz uma auditoria de ablaÃ§Ã£o do meu CLAUDE.md global"
- "audita as skills deste projeto, meu prompt tÃ¡ inchado"
- "o que dÃ¡ pra remover da minha config sem quebrar nada?"

Escopos que valem a pena:

| Escopo | Quando |
|---|---|
| Global (`~/.claude/`) | a cada ~6 meses ou em lanÃ§amento grande de modelo |
| Um projeto | quando o `CLAUDE.md` dele passa de ~150 linhas |
| Um conjunto de skills | quando 3+ skills brigam pelo mesmo gatilho |

Depois do relatÃ³rio, o fluxo saudÃ¡vel Ã©: escolher o **Top 10 por impacto Ã· risco**, aplicar em outra sessÃ£o, e rodar o **plano de ablaÃ§Ã£o** (versÃµes A / B / C) nas tarefas reais do projeto antes de dar a mudanÃ§a por boa.

## 6. Dica de uso: chame skills o tempo todo

Essa Ã© uma prÃ¡tica que eu apliquei e que muda o resultado mais do que qualquer ajuste de prompt: **use skills ao mÃ¡ximo â€” chamando direto ou dentro dos projetos.**

Por quÃª:

- **Skill Ã© procedimento, nÃ£o instruÃ§Ã£o global.** Regra que vive no `CLAUDE.md` Ã© lida em *toda* execuÃ§Ã£o, inclusive nas 90% que nÃ£o tÃªm nada a ver com ela. A mesma regra dentro de uma skill sÃ³ custa contexto quando a tarefa Ã© aquela. Mover procedimento do `CLAUDE.md` para uma skill Ã© a forma mais barata de emagrecer a config sem perder nada â€” Ã© exatamente o `MOVE` e o `LOAD-ON-DEMAND` do relatÃ³rio.
- **Chamar direto (`/nome-da-skill`) elimina a loteria do gatilho.** VocÃª para de depender de a descriÃ§Ã£o casar com a sua frase. Quando vÃ¡rias skills competem pelo mesmo assunto, invocaÃ§Ã£o explÃ­cita Ã© o desempate â€” e mata a necessidade de escrever regra de roteamento no `CLAUDE.md`.
- **Skill dentro do projeto (`.claude/skills/`) versiona junto com o cÃ³digo.** O procedimento anda com o repo, entra no PR, Ã© revisÃ¡vel e nÃ£o vaza para os outros projetos.
- **Skill Ã© a unidade certa de conserto.** No ciclo do Boris, quando o modelo tropeÃ§a vocÃª tem trÃªs remÃ©dios: prompt melhor (instruÃ§Ã£o estava obscura), **skill** (falta procedimento repetÃ­vel) ou MCP (falta contexto que ele nÃ£o alcanÃ§a). Escolher o remÃ©dio certo evita o reflexo de despejar mais uma regra no `CLAUDE.md`.
- **Skill Ã© fÃ¡cil de auditar e de aposentar.** Ela tem fronteira, nome e escopo. DÃ¡ para desativar uma skill e medir; desativar "aquele parÃ¡grafo do meio do `CLAUDE.md`" Ã© bem mais difÃ­cil.

Na prÃ¡tica: `CLAUDE.md` fica sÃ³ com o que Ã© verdade **sempre** (identidade, guardrails, fontes de verdade, seguranÃ§a). Todo o resto â€” procedimento, formato, receita, integraÃ§Ã£o â€” vira skill, invocada direto quando vocÃª sabe o que quer.

## 7. Ciclo recomendado

1. **Rodar** `/audit-ablacao` no escopo escolhido.
2. **Cortar** o Top 10 por impacto Ã· risco (numa sessÃ£o separada â€” a skill nÃ£o aplica).
3. **Usar em trabalho real** por alguns dias. NÃ£o em teste hipotÃ©tico.
4. **Anotar as falhas** e devolver instruÃ§Ã£o sÃ³ quando a mesma falha se repetir â€” e na forma mais curta possÃ­vel.
5. **Repetir** a cada ~6 meses ou a cada lanÃ§amento grande de modelo.

## 8. Guia prÃ¡tico: publicar no Git e no portal

Receita que este prÃ³prio repositÃ³rio seguiu. Vale para qualquer skill/projeto ARCKHÉ.

### 8.1 Estrutura do repo

```
audit-ablacaocc/
â”œâ”€â”€ SKILL.md        # a skill (raiz = instalÃ¡vel com um cp)
â”œâ”€â”€ README.md       # este arquivo
â”œâ”€â”€ capa/capa.png   # capa 1280x720 (skill capa-ARCKHÉ)
â”œâ”€â”€ guia/index.html # landing + guia self-contained (skill projetos-landing-guia)
â”œâ”€â”€ doc/            # material de base
â””â”€â”€ .github/workflows/pages.yml   # deploy do GitHub Pages via Actions
```

Regra que evita retrabalho: **o guia vai em `guia/`, nunca na raiz** â€” a raiz Ã© do cÃ³digo/skill. E Ã© **uma pÃ¡gina dentro do prÃ³prio repo**, nunca um repositÃ³rio separado. Nome do repo remoto = nome da pasta local.

### 8.2 Git â€” do zero ao push

```bash
cd ~/projetos/audit-ablacaocc

git init -b main
git config user.name  "linnoff88-dev"
git config user.email "linnoff88-dev@gmail.com"   # autor acompanha a conta de destino
git add -A
git commit -m "feat: skill audit-ablacao + guia"

gh repo create linnoff88-dev/audit-ablacaocc --public --source=. --remote=origin --push
```

Checagem que salva tempo: confira `git config user.email` **antes** de commitar. Autor divergente da conta de destino Ã© o motivo nÂº 1 de deploy bloqueado depois.

Se o push falhar por causa de `.github/workflows/*.yml` (falta de escopo `workflow` no token HTTPS), empurre esse commit por SSH:

```bash
git push git@github.com:linnoff88-dev/audit-ablacaocc.git main
```

### 8.3 GitHub Pages

Publique **via GitHub Actions**, nÃ£o pelo build legacy ("deploy from a branch"): a fila legacy trava, com builds presos em `building` por horas e o deploy retornando `Deployment failed, try again later`.

Crie `.github/workflows/pages.yml` servindo da raiz (o guia continua em `/guia/`):

```yaml
name: Deploy static site to Pages
on:
  push: { branches: [main, master] }
  workflow_dispatch:
permissions: { contents: read, pages: write, id-token: write }
concurrency: { group: pages, cancel-in-progress: false }
jobs:
  deploy:
    environment: { name: github-pages, url: "${{ steps.deployment.outputs.page_url }}" }
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with: { path: '.' }
      - id: deployment
        uses: actions/deploy-pages@v4
```

Depois aponte o Pages para o Actions e acompanhe o run:

```bash
gh api -X POST repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow \
  || gh api -X PUT repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow
gh run watch <run-id> --repo linnoff88-dev/audit-ablacaocc --exit-status
```

Confirme antes de dizer que publicou:

```bash
curl -sI https://linnoff88-dev.github.io/ | head -1   # espera 200
```

O primeiro run leva ~1 min. Como o guia estÃ¡ em `guia/`, a URL pÃºblica termina em `/guia/`.

### 8.4 Portal (ARCKHÉ AI)

Com a URL do Pages respondendo, publique nas 3 superfÃ­cies ARCKHÉ (portal, ARCKHÉbuscas e catÃ¡logo PRO):

```
/atualiza-portal https://linnoff88-dev.github.io/
```

A skill cuida do card, da capa e do commit + push nos trÃªs repositÃ³rios.

### 8.5 DefiniÃ§Ã£o de "publicado"

Publicar = **commit + push no `origin`**. O deploy Ã© automÃ¡tico via webhook git â†’ Vercel (e GitHub Pages, quando for o caso) e nÃ£o estÃ¡ sob nosso controle: nada de abrir dashboard, ficar checando status ou re-disparar deploy. Terminou quando o push entrou no `origin` e, no caso do Pages, quando o arquivo responde.

---

## CrÃ©ditos

MÃ©todo e visÃ£o: Boris Cherny (Anthropic), palestra na Y Combinator sobre o corte de 80% do prompt de sistema do Claude Code. Resumos e material de base em [`doc/`](doc/). Empacotado como skill por ARCKHÉ.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>