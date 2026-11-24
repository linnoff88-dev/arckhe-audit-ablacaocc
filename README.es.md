# audit-ablacaocc â€” auditorÃ­a de ablaciÃ³n para Claude Code

**ðŸ‡§ðŸ‡· [PortuguÃªs](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [EspaÃ±ol](README.es.md)**

Skill de **diagnÃ³stico** (solo lectura) que audita tu `CLAUDE.md`, tus skills y tus hooks, clasifica cada instrucciÃ³n y propone una versiÃ³n mÃ­nima â€” sin modificar ningÃºn archivo.

> Cada lÃ­nea de tu configuraciÃ³n es **culpable de complejidad hasta que se demuestre su utilidad**.

## ðŸ“– GuÃ­a de uso

GuÃ­a completa (landing + paso a paso): **https://linnoff88-dev.github.io/

- Skill: [`SKILL.md`](SKILL.md)
- Base conceptual: [`doc/`](doc/) â€” resumen de la charla de Boris Cherny (Y Combinator, un dÃ­a despuÃ©s del lanzamiento de Opus 5)

---

## Ãndice

1. [La visiÃ³n de Boris: por quÃ© borrar tu propia configuraciÃ³n](#1-a-visÃ£o-do-boris-por-que-apagar-a-prÃ³pria-config)
2. [Por quÃ© esta limpieza es Ãºtil en la prÃ¡ctica](#2-por-que-essa-faxina-Ã©-Ãºtil-na-prÃ¡tica)
3. [QuÃ© hace la skill (y quÃ© nunca hace)](#3-o-que-a-skill-faz-e-o-que-ela-nunca-faz)
4. [InstalaciÃ³n](#4-instalaÃ§Ã£o)
5. [CÃ³mo usarla](#5-como-usar)
6. [Consejo de uso: invoca skills todo el tiempo](#6-dica-de-uso-chame-skills-o-tempo-todo)
7. [Ciclo recomendado](#7-ciclo-recomendado)
8. [GuÃ­a prÃ¡ctica: publicar en Git y en el portal](#8-guia-prÃ¡tico-publicar-no-git-e-no-portal)

---

## 1. La visiÃ³n de Boris: por quÃ© borrar tu propia configuraciÃ³n

Boris Cherny creÃ³ Claude Code en Anthropic. Su tesis, resumida:

**La configuraciÃ³n envejece.** Cada instrucciÃ³n que escribes corrige una debilidad de un modelo especÃ­fico. Cuando el siguiente modelo deja de tener esa debilidad, la instrucciÃ³n se convierte en peso muerto y sigue leyÃ©ndose cada vez que se usa, ocupando contexto y limitando al modelo.

Eso fue literalmente lo que ocurriÃ³ en Claude Code: buena parte del prompt de sistema existÃ­a solo para corregir comportamientos que el modelo deberÃ­a poder ejecutar por sÃ­ solo. Con Opus 5, el equipo **eliminÃ³ mÃ¡s del 80% del prompt de sistema**. Lo que quedÃ³ es casi todo seguridad, permisos, anÃ¡lisis estÃ¡tico e interfaz.

**AblaciÃ³n** es el mÃ©todo. Es un tÃ©rmino de investigaciÃ³n: eliminas algo para medir su impacto. Aplicado a prompts, significa borrar todo y volver a incorporar cada lÃ­nea, una por una, para descubrir quÃ© hace realmente. El consejo de Boris para quienes solo *usan* Claude Code:

> Cada ~6 meses, y sobre todo cuando se lance un nuevo modelo importante: borra tu `CLAUDE.md`, borra tus skills, borra tus hooks y mira quÃ© hace el modelo sin ellos.

La reconstrucciÃ³n tiene cuatro etapas, y la cuarta es la que importa:

1. Borra la configuraciÃ³n.
2. Usa la herramienta en **trabajo real**, no en una prueba hipotÃ©tica.
3. Observa dÃ³nde funciona bien y dÃ³nde tropieza.
4. Solo vuelve a agregar una instrucciÃ³n **despuÃ©s de ver que el modelo falla repetidamente en lo mismo**.

Hay dos razones para seguir esta disciplina: eres muy malo para predecir quÃ© instrucciones necesita el modelo, y cada lÃ­nea que mantienes consume contexto en cada ejecuciÃ³n.

Los otros pilares de esta visiÃ³n:

- **Especificar demasiado es el error mÃ¡s comÃºn** â€” y es mÃ¡s frecuente entre quienes tienen aÃ±os de experiencia en ingenierÃ­a. Indicar paso a paso Â«haz A, luego B y despuÃ©s CÂ» funcionaba con modelos antiguos; hoy impide que el modelo encuentre un camino mejor. Escribe **tarea + lÃ­mites de seguridad + criterios de salida** y vuelve despuÃ©s a comprobar el resultado.
- **La verificaciÃ³n es la palanca en la que casi todo el mundo se equivoca.** Un prompt corto con una forma real de que el modelo compruebe su propio trabajo es mejor que un prompt enorme sin verificaciÃ³n. El ejemplo clÃ¡sico: reescribir la app de Electron en Swift, ejecutar la original en una VM, tomar una captura de pantalla, comparar pÃ­xel por pÃ­xel y no parar hasta terminar. Producir â†’ observar â†’ comparar â†’ condiciÃ³n de parada.
- **Apunta por encima del lÃ­mite.** Dale una tarea un poco mÃ¡s difÃ­cil de lo que crees que el modelo puede manejar y vuelve a probar tus fracasos anteriores con cada nueva versiÃ³n: la causa del fallo puede haber desaparecido.
- **Trabajar con modelos se ha vuelto una ciencia empÃ­rica**, no teÃ³rica. Se parece mÃ¡s a conocer a una criatura viva que a configurar un sistema. El nivel de instrucciones adecuado es el mismo que usarÃ­as para orientar a un colega.
- **No hay trucos secretos.** El ciclo es: tarea difÃ­cil â†’ medios para verificar â†’ observar dÃ³nde se atasca â†’ corregir *eso* (con un mejor prompt, una skill o un MCP) â†’ repetir.

Para hacer pruebas por tu cuenta, Boris mencionÃ³ dos opciones: la flag de prompt de sistema al iniciar y `CLAUDE_CODE_SIMPLE=1` (elimina todos los prompts de sistema, incluidos los de las herramientas; es la lÃ­nea base de ablaciÃ³n que se usa internamente). Un detalle contraintuitivo: sin los prompts, el modelo se vuelve *ligeramente mÃ¡s inteligente*; se mantienen porque hacen que el producto se comporte como la gente espera.

## 2. Por quÃ© esta limpieza es Ãºtil en la prÃ¡ctica

Quienes usan Claude Code desde hace tiempo acumulan la misma capa de sedimentos:

| SÃ­ntoma | Lo que suele ser |
|---|---|
| Un `CLAUDE.md` de 300 lÃ­neas con excepciones desactualizadas | correcciones para modelos que ya no existen |
| La misma regla en `CLAUDE.md` y dentro de 3 skills | redundancia que genera conflictos cuando cambia una de las copias |
| Una skill que enseÃ±a Â«cÃ³mo pensarÂ» en 400 lÃ­neas | razonamiento genÃ©rico que el modelo ya hace por sÃ­ solo |
| Un paso a paso rÃ­gido de 12 etapas | microgestiÃ³n que bloquea una mejor ruta |
| Dos reglas que se contradicen | el modelo elige una y tÃº no sabes cuÃ¡l |
| Nada que diga *cÃ³mo comprobar* que quedÃ³ bien | el vacÃ­o mÃ¡s costoso de todos |

El costo es real y acumulativo: contexto consumido en cada llamada, menos autonomÃ­a, comportamiento inconsistente y reglas zombi que nadie se atreve a borrar porque ya nadie sabe quÃ© sostienen.

Lo que ganas con la limpieza:

- **Recuperas contexto** para el trabajo real.
- **Menos contradicciones** â€” una regla que solo existe en un lugar no entra en conflicto con ninguna otra.
- **MÃ¡s autonomÃ­a** â€” los criterios de salida permiten que el modelo encuentre una ruta mejor que la tuya.
- **Una configuraciÃ³n auditable** â€” sabrÃ¡s *por quÃ©* estÃ¡ cada lÃ­nea, porque cada una habrÃ¡ superado una pregunta.
- **La verificaciÃ³n entra en el cÃ¡lculo** â€” la auditorÃ­a muestra dÃ³nde falta una forma objetiva de demostrar que el resultado es correcto.

El objetivo **no** es maximizar la reducciÃ³n. Es maximizar:

**calidad + autonomÃ­a + verificabilidad Ã· complejidad**

Por eso la skill preserva explÃ­citamente lo que el modelo no puede inferir: identidad del proyecto, rutas y fuentes de verdad, branding, seguridad, compliance, integraciones, contratos de interfaz y convenciones internas.

## 3. QuÃ© hace la skill (y quÃ© nunca hace)

**Hace lo siguiente:**

- lee `CLAUDE.md` (del proyecto y global), skills, hooks y `settings.json`;
- clasifica cada instrucciÃ³n â€” `CONTEXTO`, `GUARDRAIL`, `CRITERIO DE CALIDAD`, `VERIFICACIÃ“N`, `INTEGRACIÃ“N/HERRAMIENTA`, `PROCEDIMIENTO REPETIBLE`, `MICROGESTIÃ“N`, `REDUNDANCIA`, `LEGADO/OBSOLETA`, `AMBIGUA/NO COMPROBADA`;
- decide `KEEP` / `SIMPLIFY` / `MOVE` / `MERGE` / `TEST` / `REMOVE` (en caso de duda, `TEST`);
- audita las skills una por una â€” `KEEP` / `SIMPLIFY` / `MERGE` / `SPLIT` / `LOAD-ON-DEMAND` / `CONVERT-TO-CONTEXT` / `DELETE-CANDIDATE`;
- entrega un informe de 10 secciones: resumen ejecutivo, mÃ©tricas, problemas por archivo, candidatas a eliminaciÃ³n, redundancias y conflictos, skills, propuesta de `CLAUDE.md` mÃ­nimo, skills propuestas, plan de prueba de ablaciÃ³n (versiones A/B/C) y las 10 principales propuestas de cambio segÃºn impacto Ã· riesgo.

**Nunca hace lo siguiente:** editar, mover, borrar, sobrescribir la configuraciÃ³n ni hacer commits. Es un diagnÃ³stico. Aplicarlo es decisiÃ³n tuya y requiere un pedido aparte â€” es intencional: una auditorÃ­a que ya modifica cosas es una auditorÃ­a en la que no confÃ­as.

Diferencia con la skill de al lado: `memory-audit` se ocupa de la **memoria** guardada de la sesiÃ³n. Esta se ocupa de la **configuraciÃ³n del agente**.

## 4. InstalaciÃ³n

```bash
git clone https://github.com/linnoff88-dev/audit-ablacaocc.git
mkdir -p ~/.claude/skills/audit-ablacao
cp audit-ablacaocc/SKILL.md ~/.claude/skills/audit-ablacao/SKILL.md
```

O, para instalarla solo en el proyecto actual:

```bash
mkdir -p .claude/skills/audit-ablacao
cp /caminho/audit-ablacaocc/SKILL.md .claude/skills/audit-ablacao/SKILL.md
```

Reinicia la sesiÃ³n de Claude Code para que cargue la skill.

## 5. CÃ³mo usarla

InvocaciÃ³n directa:

```
/audit-ablacao
```

O mediante un activador, en lenguaje natural:

- Â«haz una auditorÃ­a de ablaciÃ³n de mi CLAUDE.md globalÂ»
- Â«audita las skills de este proyecto, mi prompt estÃ¡ infladoÂ»
- Â«Â¿quÃ© puedo eliminar de mi configuraciÃ³n sin romper nada?Â»

Alcances que vale la pena revisar:

| Alcance | CuÃ¡ndo |
|---|---|
| Global (`~/.claude/`) | cada ~6 meses o cuando se lance un nuevo modelo importante |
| Un proyecto | cuando su `CLAUDE.md` supera ~150 lÃ­neas |
| Un conjunto de skills | cuando 3+ skills compiten por el mismo activador |

DespuÃ©s del informe, el flujo recomendado es elegir las **10 principales segÃºn impacto Ã· riesgo**, aplicarlas en otra sesiÃ³n y ejecutar el **plan de ablaciÃ³n** (versiones A / B / C) en tareas reales del proyecto antes de aprobar los cambios.

## 6. Consejo de uso: invoca skills todo el tiempo

Esta es una prÃ¡ctica que adoptÃ© y que cambia el resultado mÃ¡s que cualquier ajuste de prompt: **usa las skills al mÃ¡ximo, invocÃ¡ndolas directamente o dentro de los proyectos.**

Â¿Por quÃ©?

- **Una skill es un procedimiento, no una instrucciÃ³n global.** Las reglas que viven en `CLAUDE.md` se leen en *todas* las ejecuciones, incluidas el 90% que no tiene nada que ver con ellas. La misma regla dentro de una skill solo consume contexto cuando la tarea corresponde. Mover un procedimiento de `CLAUDE.md` a una skill es la forma mÃ¡s sencilla de reducir la configuraciÃ³n sin perder nada: es exactamente lo que proponen `MOVE` y `LOAD-ON-DEMAND` en el informe.
- **Invocarla directamente (`/nombre-de-la-skill`) elimina la loterÃ­a del activador.** Dejas de depender de que la descripciÃ³n coincida con tu frase. Cuando varias skills compiten por el mismo tema, la invocaciÃ³n explÃ­cita desempata y elimina la necesidad de escribir una regla de enrutamiento en `CLAUDE.md`.
- **Una skill dentro del proyecto (`.claude/skills/`) queda versionada junto con el cÃ³digo.** El procedimiento acompaÃ±a al repo, se incluye en el PR, se puede revisar y no se filtra a otros proyectos.
- **Una skill es la unidad adecuada para corregir problemas.** En el ciclo de Boris, cuando el modelo tropieza tienes tres soluciones: un mejor prompt (la instrucciÃ³n no era clara), una **skill** (falta un procedimiento repetible) o un MCP (falta contexto al que no puede acceder). Elegir la soluciÃ³n adecuada evita el reflejo de agregar otra regla a `CLAUDE.md`.
- **Es fÃ¡cil auditar y retirar una skill.** Tiene lÃ­mites, nombre y alcance propios. Puedes desactivar una skill y medir el resultado; desactivar Â«ese pÃ¡rrafo en medio de `CLAUDE.md`Â» es mucho mÃ¡s difÃ­cil.

En la prÃ¡ctica, `CLAUDE.md` se queda solo con lo que es verdad **siempre** (identidad, guardrails, fuentes de verdad, seguridad). Todo lo demÃ¡s â€” procedimientos, formatos, recetas e integraciones â€” se convierte en una skill que invocas directamente cuando sabes lo que quieres.

## 7. Ciclo recomendado

1. **Ejecuta** `/audit-ablacao` en el alcance elegido.
2. **Recorta** las 10 principales propuestas segÃºn impacto Ã· riesgo (en una sesiÃ³n aparte; la skill no las aplica).
3. **Ãšsala en trabajo real** durante unos dÃ­as. No en pruebas hipotÃ©ticas.
4. **Anota los fallos** y vuelve a agregar instrucciones solo cuando se repita el mismo fallo, y en la forma mÃ¡s breve posible.
5. **Repite** cada ~6 meses o con cada lanzamiento de un nuevo modelo importante.

## 8. GuÃ­a prÃ¡ctica: publicar en Git y en el portal

Esta es la receta que siguiÃ³ este mismo repositorio. Sirve para cualquier skill o proyecto de ARCKHÉ.

### 8.1 Estructura del repo

```
audit-ablacaocc/
â”œâ”€â”€ SKILL.md        # la skill (la raÃ­z permite instalarla con un cp)
â”œâ”€â”€ README.md       # este archivo
â”œâ”€â”€ capa/capa.png   # portada 1280x720 (skill capa-ARCKHÉ)
â”œâ”€â”€ guia/index.html # landing + guÃ­a self-contained (skill projetos-landing-guia)
â”œâ”€â”€ doc/            # material de referencia
â””â”€â”€ .github/workflows/pages.yml   # deploy de GitHub Pages mediante Actions
```

Regla para evitar rehacer el trabajo: **la guÃ­a va en `guia/`, nunca en la raÃ­z**; la raÃ­z es para el cÃ³digo o la skill. Y es **una pÃ¡gina dentro del mismo repo**, nunca un repositorio separado. El nombre del repo remoto es el mismo que el de la carpeta local.

### 8.2 Git: de cero al push

```bash
cd ~/projetos/audit-ablacaocc

git init -b main
git config user.name  "linnoff88-dev"
git config user.email "linnoff88-dev@gmail.com"   # el autor corresponde a la cuenta de destino
git add -A
git commit -m "feat: skill audit-ablacao + guia"

gh repo create linnoff88-dev/audit-ablacaocc --public --source=. --remote=origin --push
```

Una comprobaciÃ³n que ahorra tiempo: revisa `git config user.email` **antes** de hacer el commit. Un autor que no corresponde a la cuenta de destino es la razÃ³n nÂº 1 por la que se bloquean los deploys.

Si el push falla por `.github/workflows/*.yml` (falta el alcance `workflow` en el token HTTPS), envÃ­a ese commit por SSH:

```bash
git push git@github.com:linnoff88-dev/audit-ablacaocc.git main
```

### 8.3 GitHub Pages

Publica **mediante GitHub Actions**, no con el build legacy (Â«deploy from a branchÂ»): la cola legacy se bloquea, con builds atascados en `building` durante horas y el deploy devuelve `Deployment failed, try again later`.

Crea `.github/workflows/pages.yml` para servir desde la raÃ­z (la guÃ­a sigue en `/guia/`):

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

DespuÃ©s, configura Pages para usar Actions y sigue la ejecuciÃ³n:

```bash
gh api -X POST repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow \
  || gh api -X PUT repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow
gh run watch <run-id> --repo linnoff88-dev/audit-ablacaocc --exit-status
```

Confirma que estÃ¡ publicado antes de decir que lo publicaste:

```bash
curl -sI https://linnoff88-dev.github.io/ | head -1   # espera 200
```

La primera ejecuciÃ³n tarda ~1 min. Como la guÃ­a estÃ¡ en `guia/`, la URL pÃºblica termina en `/guia/`.

### 8.4 Portal (ARCKHÉ AI)

Cuando la URL de Pages responda, publica en las 3 superficies de ARCKHÉ (portal, ARCKHÉbuscas y catÃ¡logo PRO):

```
/atualiza-portal https://linnoff88-dev.github.io/
```

La skill se encarga de la tarjeta, la portada y del commit + push en los tres repositorios.

### 8.5 DefiniciÃ³n de Â«publicadoÂ»

Publicar = **commit + push a `origin`**. El deploy es automÃ¡tico mediante el webhook git â†’ Vercel (y GitHub Pages, si corresponde) y estÃ¡ fuera de nuestro control: no abras el dashboard, no revises el estado ni vuelvas a activar el deploy. Se da por terminado cuando el push entra en `origin` y, en el caso de Pages, cuando el archivo responde.

---

## CrÃ©ditos

MÃ©todo y visiÃ³n: Boris Cherny (Anthropic), charla en Y Combinator sobre la reducciÃ³n del 80% del prompt de sistema de Claude Code. ResÃºmenes y material de referencia en [`doc/`](doc/). Empaquetado como skill por ARCKHÉ.
