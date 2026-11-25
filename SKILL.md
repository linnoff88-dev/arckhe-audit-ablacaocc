---
name: audit-ablacao
description: Auditoria de ablaçêo (somente diagnóstico) do CLAUDE.md, das skills e dos hooks de um projeto ou da config global. Classifica cada instruçêo (contexto, guardrail, critério, verificaçêo, microgerenciamento, redundância, legado) e entrega relatório + proposta de versêo mínima + plano de teste de ablaçêo. NUNCA edita, move ou apaga arquivos. Gatilhos - "auditoria de ablaçêo", "audita meu CLAUDE.md", "audita minhas skills", "meu prompt está inchado", "cortar instruções", "ablation audit", "enxugar skills", "o que dá pra remover da config".
---

# Auditoria de Ablaçêo — CLAUDE.md, skills e hooks

Diagnóstico da configuraçêo do agente. Descobre quais instruções ainda pagam o próprio custo e quais viraram peso morto de modelos antigos.

**Somente leitura.** Esta skill lê, classifica e propõe. Nêo escreve, nêo edita, nêo move, nêo apaga, nêo roda `git`. A aplicaçêo das mudanças é decisêo e açêo do usuário, em outra sessêo/pedido.

Escopo: configuraçêo de agente (`CLAUDE.md`, `~/.claude/skills/*/SKILL.md`, `.claude/settings*.json`, hooks, `AGENTS.md`). Para auditar a **memória** guardada da sessêo, use `memory-audit` — é outra coisa.

## Princípio central

Toda linha é **culpada de complexidade até provar utilidade**. Uma instruçêo existir nêo prova que ela é necessária.

Mas o objetivo nêo é reduzir ao máximo. É maximizar:

**qualidade + autonomia + verificabilidade ÷ complexidade**

Nêo corte contexto que o modelo nêo consegue inferir sozinho: identidade do projeto, caminhos de arquivos, fontes de verdade, branding, segurança, compliance, contratos de interface, integrações, convenções internas.

## Inventário

Leia o que estiver no escopo pedido (se o usuário nêo disser, pergunte em texto: config global, um projeto, ou ambos):

- `CLAUDE.md` do projeto e o global (`~/.claude/CLAUDE.md`);
- as skills relevantes;
- hooks e `settings.json`, quando existirem;
- arquivos referenciados, só quando forem necessários para entender uma instruçêo.

Classifique cada instruçêo em uma destas categorias:

`CONTEXTO` · `GUARDRAIL` · `CRITÉRIO DE QUALIDADE` · `VERIFICAÇêO` · `INTEGRAÇêO/FERRAMENTA` · `PROCEDIMENTO REPETÍVEL` · `MICROGERENCIAMENTO` · `REDUNDÂNCIA` · `LEGADO/OBSOLETA` · `AMBÍGUA/NêO COMPROVADA`

E decida: `KEEP` · `SIMPLIFY` · `MOVE` · `MERGE` · `TEST` · `REMOVE`.

Sem evidência suficiente, prefira `TEST` a `KEEP`.

## Perguntas por instruçêo

1. O que ela tenta evitar ou garantir?
2. O modelo atual ainda precisa dela?
3. Ela diz **o que deve acontecer** ou tenta dirigir **como o modelo pensa/executa**?
4. Está duplicada em outro arquivo?
5. Limita autonomia sem necessidade?
6. Existe forma mais curta de preservar a intençêo?
7. O que quebra se ela sumir?

## Procure especificamente

Passo a passo desnecessário · regras duplicadas entre `CLAUDE.md` e skills · skills grandes demais · skills ensinando raciocínio genérico · instruções que compensam modelos antigos · excesso de exemplos · formataçêo rígida sem motivo · exceções acumuladas · contradições e regras que competem · contexto global que só serve a poucas tarefas · regras que deveriam ser critérios de saída · **verificações ausentes** · regras substituíveis por teste objetivo.

## Converta microgerenciamento em critério

De "faça A, depois B, depois C" para:

> Produza X. Respeite Y. O resultado deve atingir Z. Verifique usando W. Escolha a estratégia.

## Cada skill

Ela precisa existir? Que problema recorrente resolve? O que nela é contexto, o que é procedimento, o que é microgerenciamento? Caberia numa instruçêo curta? Deveria carregar só sob demanda? Dá pra dividir ou fundir? Contém coisas que o modelo já faz sozinho?

Classifique: `KEEP` · `SIMPLIFY` · `MERGE` · `SPLIT` · `LOAD-ON-DEMAND` · `CONVERT-TO-CONTEXT` · `DELETE-CANDIDATE`.

## Formato da entrega

Relatório em chat ou, se o usuário pedir arquivo, num `.md` **novo** dentro do diretório de trabalho (nunca sobrescrevendo config). Seções, nesta ordem:

1. **Resumo executivo** — os 5 maiores problemas.
2. **Métricas** — instruções analisadas; contagem de `KEEP`/`SIMPLIFY`/`MOVE`/`MERGE`/`TEST`/`REMOVE`; reduçêo estimada em %.
3. **Problemas por arquivo** — tabela: Arquivo | Problema | Severidade | Recomendaçêo | Justificativa.
4. **Candidatas à remoçêo** — tabela: Trecho | Arquivo | Motivo | Risco | Como testar.
5. **Redundâncias e conflitos** — duplicações e contradições, com os arquivos envolvidos.
6. **Skills** — tabela: Skill | Funçêo atual | Diagnóstico | Recomendaçêo | Reduçêo estimada.
7. **`CLAUDE.md` proposto** — versêo mínima completa, em bloco de código (proposta, nêo aplicada).
8. **Skills propostas** — versões reduzidas das skills mantidas, em bloco de código.
9. **Plano de ablaçêo** — 5 a 10 tarefas reais e representativas do projeto, comparando: **A** config atual · **B** simplificada · **C** mínima (contexto + objetivo + guardrails + critérios + verificaçêo). Avalie qualidade, aderência, autonomia, número de correções humanas, consistência, tempo, uso desnecessário de ferramentas, complexidade e capacidade de verificar o próprio trabalho.
10. **Top 10 mudanças** — ordenadas por impacto esperado ÷ risco.

## Regra de reintroduçêo

Uma instruçêo removida só volta quando: (1) uma falha real acontecer, (2) a mesma classe de falha se repetir, (3) ficar claro que uma instruçêo específica resolve, e (4) ela couber na forma mais curta possível.

## Regras

1. **Nêo altere nada.** Nenhum edit, nenhum `rm`, nenhum `mv`, nenhum commit. Só leitura e relatório.
2. **Leia antes de opinar.** Nada de diagnóstico por suposiçêo sobre arquivo nêo lido; se nêo conseguiu ler, diga isso.
3. **Cite o trecho.** Toda recomendaçêo de remoçêo aponta arquivo e trecho.
4. **Curto nêo é sempre melhor.** Explique cada remoçêo relevante e o risco dela.
5. **Na dúvida, `TEST`.**
6. Sem emoji, sem hype. Terso e concreto.
