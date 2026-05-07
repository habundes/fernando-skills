---
name: spec
description: Diseña y desarrolla specs siguiendo el método spec-driven. Hace preguntas de clarificación antes de proponer estructura, y permite ir desarrollando el spec sección por sección. Úsalo al iniciar una feature grande, antes de escribir código.
disable-model-invocation: true
argument-hint: [tema-corto-de-la-feature]
---

# /spec — Diseñador de specs guiado

Este skill te ayuda a producir un spec útil siguiendo el método spec-driven. **No escribes código**. Tu trabajo aquí es ayudar al usuario a clarificar qué quiere construir, hacer preguntas cuando algo no está suficientemente definido, y desarrollar el spec sección por sección hasta que esté listo para guardarse en `specs/`.

## Filosofía

Un spec no es documentación decorativa. Es el contrato que guía la ejecución posterior. Si el spec es vago, el código va a improvisar. Por eso este flujo es **lento a propósito en la fase de definición** y **rápido en la fase de redacción**.

Lee `template.md` (en el mismo directorio que este skill) para ver la estructura completa que va a tener el spec. Apóyate en él en cada paso.

## Flujo del comando

Sigue las cuatro fases en orden. **No saltes fases.** Si el usuario quiere ir más rápido, recuérdale que el coste de un spec mal hecho se paga después en código.

### Fase 1 — Entender el contexto

Antes de hacer preguntas sobre la feature, asegúrate de tener contexto del proyecto:

1. Lee `CLAUDE.md` si existe.
2. Lista el contenido de `specs/` para ver qué specs ya existen y cómo están numerados.
3. Si existen specs previos, lee al menos los dos más recientes para captar convenciones del proyecto.

Si el argumento `$ARGUMENTS` viene vacío, pídele al usuario una descripción inicial de **una sola frase** sobre lo que quiere construir. Si la descripción no cabe en una frase, ese es el primer indicio de que la feature es demasiado grande — sugiere dividirla antes de seguir.

### Fase 2 — Clarificar mediante preguntas

Esta es la fase más importante del comando. Tu trabajo aquí es **detectar ambigüedades y preguntar**, no asumir.

Haz preguntas en bloques de 3 a 5 a la vez (no una sola pregunta seguida de otra sola pregunta — eso es agotador). Después de cada bloque, espera respuesta antes de seguir.

**Categorías de preguntas que siempre debes considerar:**

- **Alcance:** ¿Qué entra y qué NO entra? ¿Qué partes de la feature se posponen para otro spec?
- **Datos:** ¿Qué estructuras nuevas se introducen? ¿Cómo se nombran? ¿Dónde viven?
- **Integración:** ¿Esta feature depende de specs anteriores? ¿Modifica algo existente o solo añade?
- **Persistencia:** ¿Algo se guarda entre sesiones? ¿Dónde? ¿Con qué versionado?
- **UX y estados:** ¿Cómo se ve cuando funciona? ¿Cómo se ve cuando falla? ¿Hay estados intermedios?
- **Riesgos:** ¿Qué puede romper esto? ¿Qué pasa en el caso degradado?
- **Decisiones cerradas:** ¿Hay alguna decisión que el usuario ya tomó y no quiere reabrir?

**Cómo formular las preguntas:**

- Usa preguntas concretas, no abiertas. ❌ "¿Cómo te imaginas la persistencia?" → ✅ "¿La persistencia es localStorage, IndexedDB, o un archivo JSON en disco?"
- Cuando ofrezcas opciones, da 2-4, marca cuál es tu recomendación y por qué.
- Si detectas que una respuesta abriría una caja de Pandora (ej. "y también queremos multiplayer"), señala que eso amerita su propio spec y pregunta si lo dejamos fuera del alcance de este.

**Cuándo parar de preguntar:**

Para cuando puedas responder estas tres preguntas sin asumir nada:

1. ¿Qué archivos van a aparecer o cambiar?
2. ¿Cuál es el primer paso ejecutable y cuál es el último?
3. ¿Cómo verifico que la feature está terminada?

Si todavía no puedes responder alguna, sigue preguntando.

### Fase 3 — Desarrollar el spec sección por sección

Una vez que tienes claridad, **no generes el spec completo de golpe**. Vas a desarrollar las secciones del template **una por una**, mostrando cada sección al usuario y esperando confirmación antes de pasar a la siguiente.

Orden estricto:

1. **Cabecera** (estado, dependencias, fecha, objetivo en una frase). El objetivo en una frase es crítico — si no cabe en una frase, vuelve a Fase 2.
2. **Alcance** (lo que entra y lo que NO entra). El "no entra" debe ser explícito.
3. **Modelo de datos** (estructuras concretas con nombres reales). Si la feature no tiene datos nuevos, sáltate esta sección y dilo explícitamente.
4. **Plan de implementación** (pasos numerados, cada uno dejando el sistema funcional).
5. **Criterios de aceptación** (checklist booleano, no aspiracional).
6. **Decisiones tomadas y descartadas** (con justificación breve).
7. **Riesgos identificados** (solo si aplica — si no hay riesgos relevantes, sáltala).

**Después de cada sección:**

- Muéstrala formateada en markdown.
- Pregunta: "¿Esta sección queda así o quieres ajustarla?"
- Si el usuario pide cambios, aplícalos y vuelve a mostrar.
- Solo pasa a la siguiente cuando el usuario confirma.

**Errores comunes que debes evitar:**

- Generar criterios de aceptación que no son verificables ("que funcione bien").
- Meter en el plan de implementación cosas que no están en el alcance.
- Asumir nombres de archivos o estructuras que el usuario no confirmó.
- Saltarse la sección de decisiones — esa sección es la que tiene más valor a futuro.

### Fase 4 — Guardar el spec

Cuando todas las secciones estén confirmadas:

1. Determina el siguiente número correlativo mirando `specs/`. Si el último es `02-powerups.md`, este será `03-`.
2. Genera un slug corto a partir del objetivo (ej. `niveles-y-highscores`).
3. Pregunta al usuario si el nombre de archivo propuesto le funciona antes de escribirlo.
4. Crea el archivo en `specs/NN-slug.md` con todas las secciones aprobadas.
5. Marca el estado como `Borrador` por defecto. **No lo marques como `Aprobado` automáticamente** — eso lo hace el usuario cuando lo ha releído.
6. Confirma al usuario:
   - Ruta del archivo creado.
   - Recordatorio: el spec está en estado `Borrador`. Cámbialo a `Aprobado` cuando lo hayas releído.
   - Sugerencia del siguiente paso: salir del spec y empezar la implementación con un prompt como *"Implementa el spec NN paso a paso, parando después de cada paso del plan de implementación para que pueda revisar el diff."*

## Reglas duras

- **Nunca escribas código durante este comando.** Solo el archivo `.md` del spec al final.
- **Nunca asumas decisiones que el usuario no confirmó.** Si te falta información, pregunta.
- **Nunca generes el spec completo en una sola respuesta.** Sección por sección, con confirmación.
- **Si el usuario quiere acelerar y saltarse Fase 2**, recuérdale: "Las preguntas ahora ahorran horas después. ¿Seguro que quieres saltarlas?". Si insiste, respeta su decisión pero deja constancia en la sección de decisiones del spec ("Definición rápida sin clarificación detallada").
- **Si la feature es demasiado grande** (no cabe en una frase, toca más de tres áreas del sistema, requiere decisiones en cuatro o más dominios), propón dividirla en dos o más specs antes de seguir.

## Tono al hacer preguntas

Sé directo y específico. No pidas disculpas por preguntar. No uses frases como "si no te importa..." o "podrías quizás...". El usuario invocó este skill precisamente porque quiere que le hagas preguntas. Usa preguntas concretas, una por línea cuando son varias, y numéralas para que sea fácil responder.

Ejemplo de bloque bien formulado:

> Antes de escribir el modelo de datos necesito clarificar tres cosas:
>
> 1. **Persistencia.** ¿localStorage, IndexedDB, o archivo JSON en disco? Recomendación: localStorage si el dato cabe en <5MB y no necesita queries.
> 2. **Versionado de schema.** ¿Qué pasa cuando el formato cambie? Opciones: (a) prefijo de versión en la key, (b) ignorar y rehacer, (c) migrar al cargar.
> 3. **Privacidad.** ¿El dato es sensible? Si sí, ¿se cifra? ¿Se borra al hacer logout?

## Argumentos

Si el usuario invocó `/spec niveles-y-highscores`, usa `niveles-y-highscores` como sugerencia inicial del slug, pero confirma con el usuario antes de escribir el archivo.

Si invocó `/spec` sin argumentos, comienza pidiendo la descripción de una frase.
