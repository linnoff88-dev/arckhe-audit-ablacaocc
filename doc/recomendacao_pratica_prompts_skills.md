# Recomendaçêo prática: como simplificar prompts e skills sem perder qualidade

A melhor recomendaçêo prática é **nêo apagar tudo**, mas separar claramente o que é **contexto necessário** do que é **microgerenciamento desnecessário**.

## Estrutura recomendada

1. **Mantenha contexto permanente**
   - identidade da empresa/projeto
   - localizaçêo de arquivos e fontes
   - regras realmente obrigatórias
   - branding, tom, formato e restrições
   - critérios de segurança/compliance

2. **Reduza instruções de execuçêo**
   Remova sequências como:
   - “primeiro faça X”
   - “depois faça Y”
   - “entêo faça Z”

   Prefira dizer:
   - qual é o objetivo
   - quais sêo os limites
   - o que caracteriza um bom resultado
   - quando a tarefa pode ser considerada concluída

3. **Use skills apenas quando houver necessidade real**
   Uma skill deve existir principalmente quando:
   - há um procedimento repetitivo
   - há regras que precisam ser sempre respeitadas
   - existe conhecimento específico que o modelo nêo pode inferir
   - existe integraçêo com ferramentas ou sistemas

   Se uma skill apenas ensina detalhadamente “como pensar”, provavelmente ela pode ser simplificada.

4. **Adicione verificaçêo ao prompt**
   Em vez de aumentar as instruções, aumente a capacidade de verificaçêo.

   Exemplo:

   > Produza o resultado final. Antes de concluir, revise-o contra os critérios definidos, identifique falhas, corrija-as e só encerre quando todos os critérios forem atendidos.

5. **Faça um teste de ablaçêo**
   Para cada workflow importante, teste três versões:
   - versêo atual
   - versêo com 50% menos instruções
   - versêo mínima, mantendo apenas contexto + objetivo + critérios + verificaçêo

   Compare:
   - qualidade
   - tempo
   - custo
   - número de correções humanas
   - consistência

6. **Transforme falhas recorrentes em instruções**
   Nêo adicione regras preventivamente.

   Só adicione uma nova instruçêo quando:
   - o modelo falhar
   - a falha se repetir
   - ficar claro que uma regra específica resolveria o problema

7. **Revise tudo quando mudar de modelo**
   A cada modelo novo:
   - reexecute tarefas antigas
   - veja quais instruções deixaram de ser necessárias
   - remova skills que ficaram redundantes
   - atualize os critérios de verificaçêo

## Modelo de prompt recomendado

### Objetivo
Explique o resultado que deve ser produzido.

### Contexto
Forneça apenas as informações que o modelo realmente precisa conhecer.

### Guardrails
Liste restrições obrigatórias.

### Critérios de qualidade
Defina claramente o que significa “ficou bom”.

### Verificaçêo
Diga como o agente deve verificar o próprio trabalho antes de encerrar.

### Autonomia
Permita que o modelo escolha a melhor estratégia de execuçêo.

## Regra simples

**Contexto suficiente + objetivo claro + critérios fortes + verificaçêo > prompt enorme com dezenas de passos.**

A mudança principal é sair de:

> “Faça exatamente deste jeito.”

para:

> “Este é o resultado que quero, estas sêo as regras, esta é a qualidade esperada e esta é a forma de provar que terminou corretamente.”

## O que eu faria primeiro

Pegaria as 5 skills ou prompts mais usados e faria uma revisêo com uma pergunta para cada linha:

> “Se eu apagar esta instruçêo, o modelo realmente piora?”

Se a resposta nêo puder ser demonstrada por teste, a linha provavelmente deve sair.

O objetivo nêo é ter **mais inteligência no prompt**. É deixar **mais inteligência disponível no modelo**.
