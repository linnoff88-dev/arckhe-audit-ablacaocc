Quero que você faça uma **AUDITORIA DE ABLAÇêO** do meu `CLAUDE.md` e das minhas skills.

## OBJETIVO

Reduzir instruções desnecessárias, redundantes, antigas ou excessivamente prescritivas sem perder:

- contexto importante do projeto/negócio;
- regras realmente obrigatórias;
- preferências de marca e formato;
- segurança;
- integrações;
- critérios de qualidade;
- comportamentos que já demonstraram ser necessários.

## PRINCÍPIO CENTRAL

Nêo assuma que uma instruçêo é necessária só porque existe.

Considere cada linha **culpada de complexidade até que sua utilidade seja demonstrada**.

Ao mesmo tempo, nêo simplifique de forma irresponsável. Contexto específico do negócio, localizaçêo de arquivos, branding, compliance, segurança, integrações e procedimentos realmente determinísticos podem ser essenciais.

## 1. INVENTÁRIO

Leia:

- `CLAUDE.md`;
- todas as skills relevantes;
- arquivos diretamente referenciados por eles quando forem necessários para entender uma instruçêo.

Classifique cada instruçêo como:

- **CONTEXTO** — informaçêo que o modelo precisa conhecer.
- **GUARDRAIL** — restriçêo obrigatória.
- **CRITÉRIO DE QUALIDADE** — define o que significa um bom resultado.
- **VERIFICAÇêO** — define como confirmar que o resultado está correto.
- **INTEGRAÇêO / FERRAMENTA** — necessária para acessar sistemas ou ferramentas.
- **PROCEDIMENTO REPETÍVEL** — processo que realmente justifica uma skill.
- **MICROGERENCIAMENTO** — detalha passos que o modelo provavelmente consegue decidir sozinho.
- **REDUNDÂNCIA** — repete algo existente em outro lugar.
- **LEGADO / OBSOLETA** — parece corrigir limitações de modelos anteriores.
- **AMBÍGUA / NêO COMPROVADA** — nêo há evidência suficiente de que ajuda.

## 2. AUDITORIA LINHA A LINHA

Para cada instruçêo relevante, responda:

1. O que esta instruçêo tenta evitar ou garantir?
2. O modelo atual provavelmente precisa dela?
3. Ela descreve **o que deve acontecer** ou tenta determinar **como o modelo deve pensar/executar**?
4. Existe duplicaçêo em outro arquivo?
5. Ela limita desnecessariamente a autonomia?
6. Existe uma forma mais curta de preservar sua intençêo?
7. O que pode dar errado se ela for removida?

Dê uma decisêo:

- `KEEP`
- `SIMPLIFY`
- `MOVE`
- `MERGE`
- `TEST`
- `REMOVE`

Quando nêo houver evidência suficiente, prefira `TEST` a `KEEP`.

## 3. PROCURE ESPECIFICAMENTE ESTES PROBLEMAS

Identifique:

- instruções passo a passo desnecessárias;
- regras duplicadas entre `CLAUDE.md` e skills;
- skills excessivamente grandes;
- skills tentando ensinar raciocínio genérico;
- instruções criadas para compensar modelos antigos;
- exemplos excessivos;
- formataçêo rígida sem necessidade;
- exceções acumuladas ao longo do tempo;
- contradições;
- regras que competem entre si;
- contexto carregado globalmente que só é útil em poucas tarefas;
- regras que deveriam ser critérios de saída;
- verificações que estêo ausentes;
- regras que poderiam ser substituídas por teste ou validaçêo objetiva.

## 4. PRESERVE O QUE REALMENTE É VALIOSO

Tenha cuidado para nêo remover:

- identidade e objetivos do projeto;
- arquitetura relevante;
- fontes de verdade;
- diretórios importantes;
- convenções internas essenciais;
- requisitos de segurança;
- compliance;
- branding;
- contratos de interface;
- critérios objetivos de qualidade;
- mecanismos de validaçêo;
- conhecimento que o modelo nêo conseguiria descobrir sozinho.

## 5. CONVERTA MICROGERENCIAMENTO EM CRITÉRIOS

Sempre que possível, transforme:

> Faça A, depois B, depois C, depois D.

em:

> Produza X.  
> Respeite Y.  
> O resultado deve atingir Z.  
> Verifique usando W.  
> Escolha autonomamente a melhor estratégia.

## 6. AUDITE CADA SKILL

Para cada skill, determine:

- ela realmente precisa existir?
- qual problema recorrente resolve?
- qual parte é contexto?
- qual parte é procedimento?
- qual parte é microgerenciamento?
- poderia ser apenas uma instruçêo curta?
- deveria ser carregada apenas sob demanda?
- pode ser dividida?
- pode ser fundida com outra?
- contém instruções que o modelo atual provavelmente já executa sem ajuda?

Classifique cada skill como:

- `KEEP`
- `SIMPLIFY`
- `MERGE`
- `SPLIT`
- `LOAD-ON-DEMAND`
- `CONVERT-TO-CONTEXT`
- `DELETE-CANDIDATE`

## 7. CRIE UMA VERSêO MÍNIMA

Depois da análise, gere:

### A. Diagnóstico do `CLAUDE.md` atual

Explique seus principais problemas.

### B. `CLAUDE.md MINIMAL`

Crie uma versêo significativamente menor preservando apenas:

- contexto necessário;
- guardrails;
- critérios de qualidade;
- verificações;
- integrações;
- regras comprovadamente úteis.

### C. `SKILLS MINIMAL`

Para cada skill mantida, gere uma versêo reduzida.

**Nêo altere nenhum arquivo ainda. Apenas apresente a proposta.**

## 8. PLANO DE ABLAÇêO

Escolha entre 5 e 10 tarefas reais e representativas deste projeto.

Para cada uma, proponha comparar:

### VERSêO A
Configuraçêo atual.

### VERSêO B
`CLAUDE.md` simplificado + skills simplificadas.

### VERSêO C
Configuraçêo mínima:

**contexto essencial + objetivo + guardrails + critérios + verificaçêo**

Avalie cada versêo em:

- qualidade final;
- aderência ao objetivo;
- autonomia;
- quantidade de correções humanas;
- consistência;
- tempo;
- uso desnecessário de ferramentas;
- complexidade;
- capacidade de verificar o próprio trabalho.

## 9. REGRA PARA REINTRODUZIR INSTRUÇÕES

Uma instruçêo removida só deve voltar se:

1. uma falha real ocorrer;
2. a mesma classe de falha se repetir;
3. ficar claro que uma instruçêo específica resolveria o problema;
4. ela puder ser escrita da forma mais curta possível.

## 10. FORMATO DA RESPOSTA

Entregue exatamente estas seções:

### 1. Resumo executivo
Os 5 maiores problemas encontrados.

### 2. Métricas
- total de instruções analisadas;
- `KEEP`;
- `SIMPLIFY`;
- `MOVE`;
- `MERGE`;
- `TEST`;
- `REMOVE`;
- reduçêo estimada em %.

### 3. Problemas por arquivo

| Arquivo | Problema | Severidade | Recomendaçêo | Justificativa |
|---|---|---|---|---|

### 4. Instruções candidatas à remoçêo

| Trecho | Arquivo | Motivo | Risco | Como testar |
|---|---|---|---|---|

### 5. Redundâncias e conflitos

Liste todas as duplicações e contradições importantes.

### 6. Skills

| Skill | Funçêo atual | Diagnóstico | Recomendaçêo | Reduçêo estimada |
|---|---|---|---|---|

### 7. `CLAUDE.md` proposto

Forneça a versêo minimalista completa.

### 8. Skills propostas

Forneça as versões minimalistas completas.

### 9. Plano de testes de ablaçêo

Mostre as tarefas, versões e métricas.

### 10. Top 10 mudanças de maior impacto

Ordene por:

**impacto esperado / risco**

## REGRAS IMPORTANTES

- Nêo faça alterações automaticamente.
- Nêo apague arquivos durante a auditoria.
- Nêo assuma que “mais curto” é sempre melhor.
- Nêo preserve uma instruçêo apenas porque ela parece inteligente.
- Nêo remova contexto específico que o modelo nêo conseguiria inferir.
- Prefira critérios de resultado e verificaçêo a sequências rígidas.
- Prefira evidência observada a suposiçêo.
- Quando estiver incerto, use `TEST`.
- Explique cada remoçêo relevante.
- O objetivo nêo é maximizar a reduçêo.

O objetivo é maximizar:

**qualidade + autonomia + verificabilidade ÷ complexidade**