# audit-ablacaocc ê¢â¬â auditorêÂ­a de ablaciêÂ³n para Claude Code

**ê°Å¸â¡Â§ê°Å¸â¡Â· [PortuguêÂªs](README.md) êÂ· ê°Å¸â¡Âºê°Å¸â¡Â¸ [English](README.en.md) êÂ· ê°Å¸â¡Âªê°Å¸â¡Â¸ [EspaêÂ±ol](README.es.md)**

Skill de **diagnêÂ³stico** (solo lectura) que audita tu `CLAUDE.md`, tus skills y tus hooks, clasifica cada instrucciêÂ³n y propone una versiêÂ³n mêÂ­nima ê¢â¬â sin modificar ningêÂºn archivo.

> Cada lêÂ­nea de tu configuraciêÂ³n es **culpable de complejidad hasta que se demuestre su utilidad**.

## ê°Å¸ââ GuêÂ­a de uso

GuêÂ­a completa (landing + paso a paso): **https://linnoff88-dev.github.io/

- Skill: [`SKILL.md`](SKILL.md)
- Base conceptual: [`doc/`](doc/) ê¢â¬â resumen de la charla de Boris Cherny (Y Combinator, un dêÂ­a despuêÂ©s del lanzamiento de Opus 5)

---

## êÂndice

1. [La visiêÂ³n de Boris: por quêÂ© borrar tu propia configuraciêÂ³n](#1-a-visêÂ£o-do-boris-por-que-apagar-a-prêÂ³pria-config)
2. [Por quêÂ© esta limpieza es êÂºtil en la prêÂ¡ctica](#2-por-que-essa-faxina-êÂ©-êÂºtil-na-prêÂ¡tica)
3. [QuêÂ© hace la skill (y quêÂ© nunca hace)](#3-o-que-a-skill-faz-e-o-que-ela-nunca-faz)
4. [InstalaciêÂ³n](#4-instalaêÂ§êÂ£o)
5. [CêÂ³mo usarla](#5-como-usar)
6. [Consejo de uso: invoca skills todo el tiempo](#6-dica-de-uso-chame-skills-o-tempo-todo)
7. [Ciclo recomendado](#7-ciclo-recomendado)
8. [GuêÂ­a prêÂ¡ctica: publicar en Git y en el portal](#8-guia-prêÂ¡tico-publicar-no-git-e-no-portal)

---

## 1. La visiêÂ³n de Boris: por quêÂ© borrar tu propia configuraciêÂ³n

Boris Cherny creêÂ³ Claude Code en Anthropic. Su tesis, resumida:

**La configuraciêÂ³n envejece.** Cada instrucciêÂ³n que escribes corrige una debilidad de un modelo especêÂ­fico. Cuando el siguiente modelo deja de tener esa debilidad, la instrucciêÂ³n se convierte en peso muerto y sigue leyêÂ©ndose cada vez que se usa, ocupando contexto y limitando al modelo.

Eso fue literalmente lo que ocurriêÂ³ en Claude Code: buena parte del prompt de sistema existêÂ­a solo para corregir comportamientos que el modelo deberêÂ­a poder ejecutar por sêÂ­ solo. Con Opus 5, el equipo **eliminêÂ³ mêÂ¡s del 80% del prompt de sistema**. Lo que quedêÂ³ es casi todo seguridad, permisos, anêÂ¡lisis estêÂ¡tico e interfaz.

**AblaciêÂ³n** es el mêÂ©todo. Es un têÂ©rmino de investigaciêÂ³n: eliminas algo para medir su impacto. Aplicado a prompts, significa borrar todo y volver a incorporar cada lêÂ­nea, una por una, para descubrir quêÂ© hace realmente. El consejo de Boris para quienes solo *usan* Claude Code:

> Cada ~6 meses, y sobre todo cuando se lance un nuevo modelo importante: borra tu `CLAUDE.md`, borra tus skills, borra tus hooks y mira quêÂ© hace el modelo sin ellos.

La reconstrucciêÂ³n tiene cuatro etapas, y la cuarta es la que importa:

1. Borra la configuraciêÂ³n.
2. Usa la herramienta en **trabajo real**, no en una prueba hipotêÂ©tica.
3. Observa dêÂ³nde funciona bien y dêÂ³nde tropieza.
4. Solo vuelve a agregar una instrucciêÂ³n **despuêÂ©s de ver que el modelo falla repetidamente en lo mismo**.

Hay dos razones para seguir esta disciplina: eres muy malo para predecir quêÂ© instrucciones necesita el modelo, y cada lêÂ­nea que mantienes consume contexto en cada ejecuciêÂ³n.

Los otros pilares de esta visiêÂ³n:

- **Especificar demasiado es el error mêÂ¡s comêÂºn** ê¢â¬â y es mêÂ¡s frecuente entre quienes tienen aêÂ±os de experiencia en ingenierêÂ­a. Indicar paso a paso êÂ«haz A, luego B y despuêÂ©s CêÂ» funcionaba con modelos antiguos; hoy impide que el modelo encuentre un camino mejor. Escribe **tarea + lêÂ­mites de seguridad + criterios de salida** y vuelve despuêÂ©s a comprobar el resultado.
- **La verificaciêÂ³n es la palanca en la que casi todo el mundo se equivoca.** Un prompt corto con una forma real de que el modelo compruebe su propio trabajo es mejor que un prompt enorme sin verificaciêÂ³n. El ejemplo clêÂ¡sico: reescribir la app de Electron en Swift, ejecutar la original en una VM, tomar una captura de pantalla, comparar pêÂ­xel por pêÂ­xel y no parar hasta terminar. Producir ê¢â â observar ê¢â â comparar ê¢â â condiciêÂ³n de parada.
- **Apunta por encima del lêÂ­mite.** Dale una tarea un poco mêÂ¡s difêÂ­cil de lo que crees que el modelo puede manejar y vuelve a probar tus fracasos anteriores con cada nueva versiêÂ³n: la causa del fallo puede haber desaparecido.
- **Trabajar con modelos se ha vuelto una ciencia empêÂ­rica**, no teêÂ³rica. Se parece mêÂ¡s a conocer a una criatura viva que a configurar un sistema. El nivel de instrucciones adecuado es el mismo que usarêÂ­as para orientar a un colega.
- **No hay trucos secretos.** El ciclo es: tarea difêÂ­cil ê¢â â medios para verificar ê¢â â observar dêÂ³nde se atasca ê¢â â corregir *eso* (con un mejor prompt, una skill o un MCP) ê¢â â repetir.

Para hacer pruebas por tu cuenta, Boris mencionêÂ³ dos opciones: la flag de prompt de sistema al iniciar y `CLAUDE_CODE_SIMPLE=1` (elimina todos los prompts de sistema, incluidos los de las herramientas; es la lêÂ­nea base de ablaciêÂ³n que se usa internamente). Un detalle contraintuitivo: sin los prompts, el modelo se vuelve *ligeramente mêÂ¡s inteligente*; se mantienen porque hacen que el producto se comporte como la gente espera.

## 2. Por quêÂ© esta limpieza es êÂºtil en la prêÂ¡ctica

Quienes usan Claude Code desde hace tiempo acumulan la misma capa de sedimentos:

| SêÂ­ntoma | Lo que suele ser |
|---|---|
| Un `CLAUDE.md` de 300 lêÂ­neas con excepciones desactualizadas | correcciones para modelos que ya no existen |
| La misma regla en `CLAUDE.md` y dentro de 3 skills | redundancia que genera conflictos cuando cambia una de las copias |
| Una skill que enseêÂ±a êÂ«cêÂ³mo pensarêÂ» en 400 lêÂ­neas | razonamiento genêÂ©rico que el modelo ya hace por sêÂ­ solo |
| Un paso a paso rêÂ­gido de 12 etapas | microgestiêÂ³n que bloquea una mejor ruta |
| Dos reglas que se contradicen | el modelo elige una y têÂº no sabes cuêÂ¡l |
| Nada que diga *cêÂ³mo comprobar* que quedêÂ³ bien | el vacêÂ­o mêÂ¡s costoso de todos |

El costo es real y acumulativo: contexto consumido en cada llamada, menos autonomêÂ­a, comportamiento inconsistente y reglas zombi que nadie se atreve a borrar porque ya nadie sabe quêÂ© sostienen.

Lo que ganas con la limpieza:

- **Recuperas contexto** para el trabajo real.
- **Menos contradicciones** ê¢â¬â una regla que solo existe en un lugar no entra en conflicto con ninguna otra.
- **MêÂ¡s autonomêÂ­a** ê¢â¬â los criterios de salida permiten que el modelo encuentre una ruta mejor que la tuya.
- **Una configuraciêÂ³n auditable** ê¢â¬â sabrêÂ¡s *por quêÂ©* estêÂ¡ cada lêÂ­nea, porque cada una habrêÂ¡ superado una pregunta.
- **La verificaciêÂ³n entra en el cêÂ¡lculo** ê¢â¬â la auditorêÂ­a muestra dêÂ³nde falta una forma objetiva de demostrar que el resultado es correcto.

El objetivo **no** es maximizar la reducciêÂ³n. Es maximizar:

**calidad + autonomêÂ­a + verificabilidad êÂ· complejidad**

Por eso la skill preserva explêÂ­citamente lo que el modelo no puede inferir: identidad del proyecto, rutas y fuentes de verdad, branding, seguridad, compliance, integraciones, contratos de interfaz y convenciones internas.

## 3. QuêÂ© hace la skill (y quêÂ© nunca hace)

**Hace lo siguiente:**

- lee `CLAUDE.md` (del proyecto y global), skills, hooks y `settings.json`;
- clasifica cada instrucciêÂ³n ê¢â¬â `CONTEXTO`, `GUARDRAIL`, `CRITERIO DE CALIDAD`, `VERIFICACIêâN`, `INTEGRACIêâN/HERRAMIENTA`, `PROCEDIMIENTO REPETIBLE`, `MICROGESTIêâN`, `REDUNDANCIA`, `LEGADO/OBSOLETA`, `AMBIGUA/NO COMPROBADA`;
- decide `KEEP` / `SIMPLIFY` / `MOVE` / `MERGE` / `TEST` / `REMOVE` (en caso de duda, `TEST`);
- audita las skills una por una ê¢â¬â `KEEP` / `SIMPLIFY` / `MERGE` / `SPLIT` / `LOAD-ON-DEMAND` / `CONVERT-TO-CONTEXT` / `DELETE-CANDIDATE`;
- entrega un informe de 10 secciones: resumen ejecutivo, mêÂ©tricas, problemas por archivo, candidatas a eliminaciêÂ³n, redundancias y conflictos, skills, propuesta de `CLAUDE.md` mêÂ­nimo, skills propuestas, plan de prueba de ablaciêÂ³n (versiones A/B/C) y las 10 principales propuestas de cambio segêÂºn impacto êÂ· riesgo.

**Nunca hace lo siguiente:** editar, mover, borrar, sobrescribir la configuraciêÂ³n ni hacer commits. Es un diagnêÂ³stico. Aplicarlo es decisiêÂ³n tuya y requiere un pedido aparte ê¢â¬â es intencional: una auditorêÂ­a que ya modifica cosas es una auditorêÂ­a en la que no confêÂ­as.

Diferencia con la skill de al lado: `memory-audit` se ocupa de la **memoria** guardada de la sesiêÂ³n. Esta se ocupa de la **configuraciêÂ³n del agente**.

## 4. InstalaciêÂ³n

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

Reinicia la sesiêÂ³n de Claude Code para que cargue la skill.

## 5. CêÂ³mo usarla

InvocaciêÂ³n directa:

```
/audit-ablacao
```

O mediante un activador, en lenguaje natural:

- êÂ«haz una auditorêÂ­a de ablaciêÂ³n de mi CLAUDE.md globalêÂ»
- êÂ«audita las skills de este proyecto, mi prompt estêÂ¡ infladoêÂ»
- êÂ«êÂ¿quêÂ© puedo eliminar de mi configuraciêÂ³n sin romper nada?êÂ»

Alcances que vale la pena revisar:

| Alcance | CuêÂ¡ndo |
|---|---|
| Global (`~/.claude/`) | cada ~6 meses o cuando se lance un nuevo modelo importante |
| Un proyecto | cuando su `CLAUDE.md` supera ~150 lêÂ­neas |
| Un conjunto de skills | cuando 3+ skills compiten por el mismo activador |

DespuêÂ©s del informe, el flujo recomendado es elegir las **10 principales segêÂºn impacto êÂ· riesgo**, aplicarlas en otra sesiêÂ³n y ejecutar el **plan de ablaciêÂ³n** (versiones A / B / C) en tareas reales del proyecto antes de aprobar los cambios.

## 6. Consejo de uso: invoca skills todo el tiempo

Esta es una prêÂ¡ctica que adoptêÂ© y que cambia el resultado mêÂ¡s que cualquier ajuste de prompt: **usa las skills al mêÂ¡ximo, invocêÂ¡ndolas directamente o dentro de los proyectos.**

êÂ¿Por quêÂ©?

- **Una skill es un procedimiento, no una instrucciêÂ³n global.** Las reglas que viven en `CLAUDE.md` se leen en *todas* las ejecuciones, incluidas el 90% que no tiene nada que ver con ellas. La misma regla dentro de una skill solo consume contexto cuando la tarea corresponde. Mover un procedimiento de `CLAUDE.md` a una skill es la forma mêÂ¡s sencilla de reducir la configuraciêÂ³n sin perder nada: es exactamente lo que proponen `MOVE` y `LOAD-ON-DEMAND` en el informe.
- **Invocarla directamente (`/nombre-de-la-skill`) elimina la loterêÂ­a del activador.** Dejas de depender de que la descripciêÂ³n coincida con tu frase. Cuando varias skills compiten por el mismo tema, la invocaciêÂ³n explêÂ­cita desempata y elimina la necesidad de escribir una regla de enrutamiento en `CLAUDE.md`.
- **Una skill dentro del proyecto (`.claude/skills/`) queda versionada junto con el cêÂ³digo.** El procedimiento acompaêÂ±a al repo, se incluye en el PR, se puede revisar y no se filtra a otros proyectos.
- **Una skill es la unidad adecuada para corregir problemas.** En el ciclo de Boris, cuando el modelo tropieza tienes tres soluciones: un mejor prompt (la instrucciêÂ³n no era clara), una **skill** (falta un procedimiento repetible) o un MCP (falta contexto al que no puede acceder). Elegir la soluciêÂ³n adecuada evita el reflejo de agregar otra regla a `CLAUDE.md`.
- **Es fêÂ¡cil auditar y retirar una skill.** Tiene lêÂ­mites, nombre y alcance propios. Puedes desactivar una skill y medir el resultado; desactivar êÂ«ese pêÂ¡rrafo en medio de `CLAUDE.md`êÂ» es mucho mêÂ¡s difêÂ­cil.

En la prêÂ¡ctica, `CLAUDE.md` se queda solo con lo que es verdad **siempre** (identidad, guardrails, fuentes de verdad, seguridad). Todo lo demêÂ¡s ê¢â¬â procedimientos, formatos, recetas e integraciones ê¢â¬â se convierte en una skill que invocas directamente cuando sabes lo que quieres.

## 7. Ciclo recomendado

1. **Ejecuta** `/audit-ablacao` en el alcance elegido.
2. **Recorta** las 10 principales propuestas segêÂºn impacto êÂ· riesgo (en una sesiêÂ³n aparte; la skill no las aplica).
3. **êÅ¡sala en trabajo real** durante unos dêÂ­as. No en pruebas hipotêÂ©ticas.
4. **Anota los fallos** y vuelve a agregar instrucciones solo cuando se repita el mismo fallo, y en la forma mêÂ¡s breve posible.
5. **Repite** cada ~6 meses o con cada lanzamiento de un nuevo modelo importante.

## 8. GuêÂ­a prêÂ¡ctica: publicar en Git y en el portal

Esta es la receta que siguiêÂ³ este mismo repositorio. Sirve para cualquier skill o proyecto de ARCKHê.

### 8.1 Estructura del repo

```
audit-ablacaocc/
ê¢âÅê¢ââ¬ê¢ââ¬ SKILL.md        # la skill (la raêÂ­z permite instalarla con un cp)
ê¢âÅê¢ââ¬ê¢ââ¬ README.md       # este archivo
ê¢âÅê¢ââ¬ê¢ââ¬ capa/capa.png   # portada 1280x720 (skill capa-ARCKHê)
ê¢âÅê¢ââ¬ê¢ââ¬ guia/index.html # landing + guêÂ­a self-contained (skill projetos-landing-guia)
ê¢âÅê¢ââ¬ê¢ââ¬ doc/            # material de referencia
ê¢ââê¢ââ¬ê¢ââ¬ .github/workflows/pages.yml   # deploy de GitHub Pages mediante Actions
```

Regla para evitar rehacer el trabajo: **la guêÂ­a va en `guia/`, nunca en la raêÂ­z**; la raêÂ­z es para el cêÂ³digo o la skill. Y es **una pêÂ¡gina dentro del mismo repo**, nunca un repositorio separado. El nombre del repo remoto es el mismo que el de la carpeta local.

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

Una comprobaciêÂ³n que ahorra tiempo: revisa `git config user.email` **antes** de hacer el commit. Un autor que no corresponde a la cuenta de destino es la razêÂ³n nêÂº 1 por la que se bloquean los deploys.

Si el push falla por `.github/workflows/*.yml` (falta el alcance `workflow` en el token HTTPS), envêÂ­a ese commit por SSH:

```bash
git push git@github.com:linnoff88-dev/audit-ablacaocc.git main
```

### 8.3 GitHub Pages

Publica **mediante GitHub Actions**, no con el build legacy (êÂ«deploy from a branchêÂ»): la cola legacy se bloquea, con builds atascados en `building` durante horas y el deploy devuelve `Deployment failed, try again later`.

Crea `.github/workflows/pages.yml` para servir desde la raêÂ­z (la guêÂ­a sigue en `/guia/`):

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

DespuêÂ©s, configura Pages para usar Actions y sigue la ejecuciêÂ³n:

```bash
gh api -X POST repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow \
  || gh api -X PUT repos/linnoff88-dev/audit-ablacaocc/pages -f build_type=workflow
gh run watch <run-id> --repo linnoff88-dev/audit-ablacaocc --exit-status
```

Confirma que estêÂ¡ publicado antes de decir que lo publicaste:

```bash
curl -sI https://linnoff88-dev.github.io/ | head -1   # espera 200
```

La primera ejecuciêÂ³n tarda ~1 min. Como la guêÂ­a estêÂ¡ en `guia/`, la URL pêÂºblica termina en `/guia/`.

### 8.4 Portal (ARCKHê AI)

Cuando la URL de Pages responda, publica en las 3 superficies de ARCKHê (portal, ARCKHêbuscas y catêÂ¡logo PRO):

```
/atualiza-portal https://linnoff88-dev.github.io/
```

La skill se encarga de la tarjeta, la portada y del commit + push en los tres repositorios.

### 8.5 DefiniciêÂ³n de êÂ«publicadoêÂ»

Publicar = **commit + push a `origin`**. El deploy es automêÂ¡tico mediante el webhook git ê¢â â Vercel (y GitHub Pages, si corresponde) y estêÂ¡ fuera de nuestro control: no abras el dashboard, no revises el estado ni vuelvas a activar el deploy. Se da por terminado cuando el push entra en `origin` y, en el caso de Pages, cuando el archivo responde.

---

## CrêÂ©ditos

MêÂ©todo y visiêÂ³n: Boris Cherny (Anthropic), charla en Y Combinator sobre la reducciêÂ³n del 80% del prompt de sistema de Claude Code. ResêÂºmenes y material de referencia en [`doc/`](doc/). Empaquetado como skill por ARCKHê.
