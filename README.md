# audit-ablacaocc ê¢â¬â auditoria de ablaêÂ§êÂ£o para Claude Code

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**ê°Å¸â¡Â§ê°Å¸â¡Â· [PortuguêÂªs](README.md) êÂ· ê°Å¸â¡Âºê°Å¸â¡Â¸ [English](README.en.md) êÂ· ê°Å¸â¡Âªê°Å¸â¡Â¸ [EspaêÂ±ol](README.es.md)**

Skill de **diagnêÂ³stico** (somente leitura) que audita seu `CLAUDE.md`, suas skills e seus hooks, classifica cada instruêÂ§êÂ£o e propêÂµe uma versêÂ£o mêÂ­nima ê¢â¬â sem tocar em nenhum arquivo.

> Toda linha da sua configuraêÂ§êÂ£o êÂ© **culpada de complexidade atêÂ© provar utilidade**.

## ê°Å¸ââ Guia de uso

Guia completo (landing + passo a passo): **https://linnoff88-dev.github.io/

- Skill: [`SKILL.md`](SKILL.md)
- Base conceitual: [`doc/`](doc/) ê¢â¬â resumo da palestra do Boris Cherny (Y Combinator, um dia apêÂ³s o lanêÂ§amento do Opus 5)

---

## êÂndice

1. [A visêÂ£o do Boris: por que apagar a prêÂ³pria config](#1-a-visêÂ£o-do-boris-por-que-apagar-a-prêÂ³pria-config)
2. [Por que essa faxina êÂ© êÂºtil na prêÂ¡tica](#2-por-que-essa-faxina-êÂ©-êÂºtil-na-prêÂ¡tica)
3. [O que a skill faz (e o que ela nunca faz)](#3-o-que-a-skill-faz-e-o-que-ela-nunca-faz)
4. [InstalaêÂ§êÂ£o](#4-instalaêÂ§êÂ£o)
5. [Como usar](#5-como-usar)
6. [Dica de uso: chame skills o tempo todo](#6-dica-de-uso-chame-skills-o-tempo-todo)
7. [Ciclo recomendado](#7-ciclo-recomendado)
8. [Guia prêÂ¡tico: publicar no Git e no portal](#8-guia-prêÂ¡tico-publicar-no-git-e-no-portal)

---

## 1. A visêÂ£o do Boris: por que apagar a prêÂ³pria config

Boris Cherny criou o Claude Code na Anthropic. A tese dele, resumida:

**ConfiguraêÂ§êÂ£o envelhece.** Cada instruêÂ§êÂ£o que vocêÂª escreve êÂ© o conserto da fraqueza de um modelo especêÂ­fico. Quando o modelo seguinte deixa de ter aquela fraqueza, a instruêÂ§êÂ£o vira peso morto ê¢â¬â e continua sendo relida a cada uso, ocupando contexto e limitando o modelo.

Foi literalmente o que aconteceu no Claude Code: boa parte do prompt de sistema existia sêÂ³ para corrigir comportamentos que o modelo deveria saber executar sozinho. Com o Opus 5, a equipe **removeu mais de 80% do prompt de sistema**. O que sobrou êÂ© quase todo seguranêÂ§a, permissêÂµes, anêÂ¡lise estêÂ¡tica e interface.

**AblaêÂ§êÂ£o** êÂ© o mêÂ©todo. êâ° termo de pesquisa: vocêÂª remove algo para medir o impacto. Aplicado a prompt, significa apagar tudo e trazer de volta linha por linha, descobrindo o que cada linha realmente faz. O conselho do Boris para quem sêÂ³ *usa* Claude Code:

> A cada ~6 meses, e principalmente em lanêÂ§amento grande de modelo: apague seu `CLAUDE.md`, apague suas skills, apague seus hooks ê¢â¬â e veja o que o modelo faz sem eles.

A reconstruêÂ§êÂ£o tem quatro etapas, e a quarta êÂ© a que importa:

1. Apague a config.
2. Use a ferramenta em **trabalho real**, nêÂ£o em teste hipotêÂ©tico.
3. Observe onde ela vai bem e onde tropeêÂ§a.
4. SêÂ³ devolva uma instruêÂ§êÂ£o **depois de ver o modelo falhar repetidamente na mesma coisa**.

Duas razêÂµes para essa disciplina: vocêÂª êÂ© um pêÂ©ssimo previsor de qual instruêÂ§êÂ£o o modelo precisa, e cada linha mantida custa contexto em toda execuêÂ§êÂ£o.

Os outros pilares da visêÂ£o:

- **EspecificaêÂ§êÂ£o excessiva êÂ© o erro mais comum** ê¢â¬â e êÂ© mais frequente em quem tem anos de engenharia. Roteirizar "faêÂ§a A, depois B, depois C" funcionava com modelos antigos; hoje impede o modelo de achar um caminho melhor. Escreva **tarefa + guardrails + critêÂ©rios de saêÂ­da**, e volte depois para conferir.
- **VerificaêÂ§êÂ£o êÂ© a alavanca que quase todo mundo erra.** Prompt curto com um jeito real de o modelo conferir o prêÂ³prio trabalho ganha de prompt gigante sem verificaêÂ§êÂ£o. O exemplo clêÂ¡ssico: reescrever o app Electron em Swift, rodar o original numa VM, tirar screenshot, comparar pixel a pixel, nêÂ£o parar atêÂ© terminar. Produzir ê¢â â observar ê¢â â comparar ê¢â â condiêÂ§êÂ£o de parada.
- **Mire acima do teto.** DêÂª uma tarefa um pouco mais difêÂ­cil do que vocêÂª acha que o modelo aguenta, e reteste seus fracassos antigos a cada versêÂ£o nova ê¢â¬â o motivo da falha pode ter desaparecido.
- **Trabalhar com modelo virou ciêÂªncia empêÂ­rica**, nêÂ£o teêÂ³rica. êâ° mais parecido com conhecer uma criatura viva do que configurar um sistema. O nêÂ­vel certo de instruêÂ§êÂ£o êÂ© o mesmo que vocêÂª usaria orientando um colega de trabalho.
- **NêÂ£o existe truque secreto.** O ciclo êÂ©: tarefa difêÂ­cil ê¢â â meios de verificar ê¢â â observar onde trava ê¢â â corrigir *aquilo* (prompt melhor, ou uma skill, ou um MCP) ê¢â â repetir.

Para testar sozinho, Boris citou duas alavancas: a flag de prompt de sistema na inicializaêÂ§êÂ£o, e `CLAUDE_CODE_SIMPLE=1` (remove todos os prompts de sistema, inclusive os das ferramentas ê¢â¬â êÂ© a linha de base de ablaêÂ§êÂ£o usada internamente). Detalhe contraintuitivo: sem os prompts, o modelo fica *ligeiramente mais inteligente*; eles sêÂ£o mantidos porque fazem o produto se comportar como a pessoa espera.

## 2. Por que essa faxina êÂ© êÂºtil na prêÂ¡tica

Quem usa Claude Code hêÂ¡ um tempo acumula a mesma camada de sedimento:

| Sintoma | O que costuma ser |
|---|---|
| `CLAUDE.md` de 300 linhas com exceêÂ§êÂµes datadas | correêÂ§êÂµes de modelos que jêÂ¡ saêÂ­ram de cena |
| A mesma regra no `CLAUDE.md` e dentro de 3 skills | redundêÂ¢ncia que gera conflito quando uma das cêÂ³pias muda |
| Skill que ensina "como pensar" em 400 linhas | raciocêÂ­nio genêÂ©rico que o modelo jêÂ¡ faz sozinho |
| Passo a passo rêÂ­gido de 12 etapas | microgerenciamento que trava a rota melhor |
| Duas regras que se contradizem | o modelo escolhe uma, e vocêÂª nêÂ£o sabe qual |
| Nada dizendo *como conferir* que ficou certo | o buraco mais caro de todos |

O custo êÂ© real e composto: contexto queimado em toda chamada, autonomia reduzida, comportamento inconsistente, e regras zumbis que ninguêÂ©m ousa apagar porque ninguêÂ©m sabe mais o que elas seguram.

O ganho da faxina:

- **Contexto de volta** para o trabalho de verdade.
- **Menos contradiêÂ§êÂ£o** ê¢â¬â regra que sêÂ³ existe em um lugar nêÂ£o briga com nada.
- **Mais autonomia** ê¢â¬â critêÂ©rios de saêÂ­da deixam o modelo achar caminho melhor que o seu.
- **Config auditêÂ¡vel** ê¢â¬â vocêÂª passa a saber *por que* cada linha estêÂ¡ lêÂ¡, porque cada uma sobreviveu a uma pergunta.
- **VerificaêÂ§êÂ£o entra na conta** ê¢â¬â a auditoria expêÂµe onde falta um jeito objetivo de provar que terminou certo.

O objetivo **nêÂ£o** êÂ© maximizar a reduêÂ§êÂ£o. êâ° maximizar:

**qualidade + autonomia + verificabilidade êÂ· complexidade**

Por isso a skill preserva explicitamente o que o modelo nêÂ£o consegue inferir: identidade do projeto, caminhos e fontes de verdade, branding, seguranêÂ§a, compliance, integraêÂ§êÂµes, contratos de interface e convenêÂ§êÂµes internas.

## 3. O que a skill faz (e o que ela nunca faz)

**Faz:**

- lêÂª `CLAUDE.md` (projeto + global), skills, hooks e `settings.json`;
- classifica cada instruêÂ§êÂ£o ê¢â¬â `CONTEXTO`, `GUARDRAIL`, `CRITêâ°RIO DE QUALIDADE`, `VERIFICAêâ¡êÆO`, `INTEGRAêâ¡êÆO/FERRAMENTA`, `PROCEDIMENTO REPETêÂVEL`, `MICROGERENCIAMENTO`, `REDUNDêâNCIA`, `LEGADO/OBSOLETA`, `AMBêÂGUA/NêÆO COMPROVADA`;
- decide `KEEP` / `SIMPLIFY` / `MOVE` / `MERGE` / `TEST` / `REMOVE` (na dêÂºvida, `TEST`);
- audita skill por skill ê¢â¬â `KEEP` / `SIMPLIFY` / `MERGE` / `SPLIT` / `LOAD-ON-DEMAND` / `CONVERT-TO-CONTEXT` / `DELETE-CANDIDATE`;
- entrega relatêÂ³rio em 10 seêÂ§êÂµes: resumo executivo, mêÂ©tricas, problemas por arquivo, candidatas êÂ  remoêÂ§êÂ£o, redundêÂ¢ncias e conflitos, skills, `CLAUDE.md` mêÂ­nimo proposto, skills propostas, plano de teste de ablaêÂ§êÂ£o (versêÂµes A/B/C) e top 10 mudanêÂ§as por impacto êÂ· risco.

**Nunca faz:** editar, mover, apagar, sobrescrever config ou commitar. êâ° diagnêÂ³stico. Aplicar êÂ© decisêÂ£o sua, num pedido separado ê¢â¬â e êÂ© de propêÂ³sito: auditoria que jêÂ¡ sai mexendo êÂ© auditoria em que vocêÂª nêÂ£o confia.

DiferenêÂ§a de vizinhanêÂ§a: `memory-audit` cuida da **memêÂ³ria** guardada da sessêÂ£o. Esta aqui cuida da **configuraêÂ§êÂ£o do agente**.

## 4. InstalaêÂ§êÂ£o

```bash
git clone https://github.com/linnoff88-dev/audit-ablacaocc.git
mkdir -p ~/.claude/skills/audit-ablacao
cp audit-ablacaocc/SKILL.md ~/.claude/skills/audit-ablacao/SKILL.md
```

Ou, para instalar sêÂ³ no projeto atual:

```bash
mkdir -p .claude/skills/audit-ablacao
cp /caminho/audit-ablacaocc/SKILL.md .claude/skills/audit-ablacao/SKILL.md
```

Reinicie a sessêÂ£o do Claude Code para ele carregar a skill.

## 5. Como usar

Chamada direta:

```
/audit-ablacao
```

Ou por gatilho, em linguagem natural:

- "faz uma auditoria de ablaêÂ§êÂ£o do meu CLAUDE.md global"
- "audita as skills deste projeto, meu prompt têÂ¡ inchado"
- "o que dêÂ¡ pra remover da minha config sem quebrar nada?"

Escopos que valem a pena:

| Escopo | Quando |
|---|---|
| Global (`~/.claude/`) | a cada ~6 meses ou em lanêÂ§amento grande de modelo |
| Um projeto | quando o `CLAUDE.md` dele passa de ~150 linhas |
| Um conjunto de skills | quando 3+ skills brigam pelo mesmo gatilho |

Depois do relatêÂ³rio, o fluxo saudêÂ¡vel êÂ©: escolher o **Top 10 por impacto êÂ· risco**, aplicar em outra sessêÂ£o, e rodar o **plano de ablaêÂ§êÂ£o** (versêÂµes A / B / C) nas tarefas reais do projeto antes de dar a mudanêÂ§a por boa.

## 6. Dica de uso: chame skills o tempo todo

Essa êÂ© uma prêÂ¡tica que eu apliquei e que muda o resultado mais do que qualquer ajuste de prompt: **use skills ao mêÂ¡ximo ê¢â¬â chamando direto ou dentro dos projetos.**

Por quêÂª:

- **Skill êÂ© procedimento, nêÂ£o instruêÂ§êÂ£o global.** Regra que vive no `CLAUDE.md` êÂ© lida em *toda* execuêÂ§êÂ£o, inclusive nas 90% que nêÂ£o têÂªm nada a ver com ela. A mesma regra dentro de uma skill sêÂ³ custa contexto quando a tarefa êÂ© aquela. Mover procedimento do `CLAUDE.md` para uma skill êÂ© a forma mais barata de emagrecer a config sem perder nada ê¢â¬â êÂ© exatamente o `MOVE` e o `LOAD-ON-DEMAND` do relatêÂ³rio.
- **Chamar direto (`/nome-da-skill`) elimina a loteria do gatilho.** VocêÂª para de depender de a descriêÂ§êÂ£o casar com a sua frase. Quando vêÂ¡rias skills competem pelo mesmo assunto, invocaêÂ§êÂ£o explêÂ­cita êÂ© o desempate ê¢â¬â e mata a necessidade de escrever regra de roteamento no `CLAUDE.md`.
- **Skill dentro do projeto (`.claude/skills/`) versiona junto com o cêÂ³digo.** O procedimento anda com o repo, entra no PR, êÂ© revisêÂ¡vel e nêÂ£o vaza para os outros projetos.
- **Skill êÂ© a unidade certa de conserto.** No ciclo do Boris, quando o modelo tropeêÂ§a vocêÂª tem trêÂªs remêÂ©dios: prompt melhor (instruêÂ§êÂ£o estava obscura), **skill** (falta procedimento repetêÂ­vel) ou MCP (falta contexto que ele nêÂ£o alcanêÂ§a). Escolher o remêÂ©dio certo evita o reflexo de despejar mais uma regra no `CLAUDE.md`.
- **Skill êÂ© fêÂ¡cil de auditar e de aposentar.** Ela tem fronteira, nome e escopo. DêÂ¡ para desativar uma skill e medir; desativar "aquele parêÂ¡grafo do meio do `CLAUDE.md`" êÂ© bem mais difêÂ­cil.

Na prêÂ¡tica: `CLAUDE.md` fica sêÂ³ com o que êÂ© verdade **sempre** (identidade, guardrails, fontes de verdade, seguranêÂ§a). Todo o resto ê¢â¬â procedimento, formato, receita, integraêÂ§êÂ£o ê¢â¬â vira skill, invocada direto quando vocêÂª sabe o que quer.

## 7. Ciclo recomendado

1. **Rodar** `/audit-ablacao` no escopo escolhido.
2. **Cortar** o Top 10 por impacto êÂ· risco (numa sessêÂ£o separada ê¢â¬â a skill nêÂ£o aplica).
3. **Usar em trabalho real** por alguns dias. NêÂ£o em teste hipotêÂ©tico.
4. **Anotar as falhas** e devolver instruêÂ§êÂ£o sêÂ³ quando a mesma falha se repetir ê¢â¬â e na forma mais curta possêÂ­vel.
5. **Repetir** a cada ~6 meses ou a cada lanêÂ§amento grande de modelo.

## 8. Guia prêÂ¡tico: publicar no Git e no portal

Receita que este prêÂ³prio repositêÂ³rio seguiu. Vale para qualquer skill/projeto ARCKHê.

### 8.1 Estrutura do repo

```
audit-ablacaocc/
ê¢âÅê¢ââ¬ê¢ââ¬ SKILL.md        # a skill (raiz = instalêÂ¡vel com um cp)
ê¢âÅê¢ââ¬ê¢ââ¬ README.md       # este arquivo
ê¢âÅê¢ââ¬ê¢ââ¬ capa/capa.png   # capa 1280x720 (skill capa-ARCKHê)
ê¢âÅê¢ââ¬ê¢ââ¬ guia/index.html # landing + guia self-contained (skill projetos-landing-guia)
ê¢âÅê¢ââ¬ê¢ââ¬ doc/            # material de base
ê¢ââê¢ââ¬ê¢ââ¬ .github/workflows/pages.yml   # deploy do GitHub Pages via Actions
```

Regra que evita retrabalho: **o guia vai em `guia/`, nunca na raiz** ê¢â¬â a raiz êÂ© do cêÂ³digo/skill. E êÂ© **uma pêÂ¡gina dentro do prêÂ³prio repo**, nunca um repositêÂ³rio separado. Nome do repo remoto = nome da pasta local.

### 8.2 Git ê¢â¬â do zero ao push

```bash
cd ~/projetos/audit-ablacaocc

git init -b main
git config user.name  "linnoff88-dev"
git config user.email "linnoff88-dev@gmail.com"   # autor acompanha a conta de destino
git add -A
git commit -m "feat: skill audit-ablacao + guia"

gh repo create linnoff88-dev/audit-ablacaocc --public --source=. --remote=origin --push
```

Checagem que salva tempo: confira `git config user.email` **antes** de commitar. Autor divergente da conta de destino êÂ© o motivo nêÂº 1 de deploy bloqueado depois.

Se o push falhar por causa de `.github/workflows/*.yml` (falta de escopo `workflow` no token HTTPS), empurre esse commit por SSH:

```bash
git push git@github.com:linnoff88-dev/audit-ablacaocc.git main
```

### 8.3 GitHub Pages

Publique **via GitHub Actions**, nêÂ£o pelo build legacy ("deploy from a branch"): a fila legacy trava, com builds presos em `building` por horas e o deploy retornando `Deployment failed, try again later`.

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

O primeiro run leva ~1 min. Como o guia estêÂ¡ em `guia/`, a URL pêÂºblica termina em `/guia/`.

### 8.4 Portal (ARCKHê AI)

Com a URL do Pages respondendo, publique nas 3 superfêÂ­cies ARCKHê (portal, ARCKHêbuscas e catêÂ¡logo PRO):

```
/atualiza-portal https://linnoff88-dev.github.io/
```

A skill cuida do card, da capa e do commit + push nos trêÂªs repositêÂ³rios.

### 8.5 DefiniêÂ§êÂ£o de "publicado"

Publicar = **commit + push no `origin`**. O deploy êÂ© automêÂ¡tico via webhook git ê¢â â Vercel (e GitHub Pages, quando for o caso) e nêÂ£o estêÂ¡ sob nosso controle: nada de abrir dashboard, ficar checando status ou re-disparar deploy. Terminou quando o push entrou no `origin` e, no caso do Pages, quando o arquivo responde.

---

## CrêÂ©ditos

MêÂ©todo e visêÂ£o: Boris Cherny (Anthropic), palestra na Y Combinator sobre o corte de 80% do prompt de sistema do Claude Code. Resumos e material de base em [`doc/`](doc/). Empacotado como skill por ARCKHê.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>