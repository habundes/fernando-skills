# Plantilla de un spec útil

Este archivo es la referencia que el skill `/spec` consulta para generar specs. Cada sección incluye su propósito y un ejemplo mínimo. **No es texto que se copie literal** — es la forma que el skill debe respetar.

---

## Cabecera

Cada spec arranca con metadatos en formato de bloque de cita:

```markdown
# SPEC NN — Título corto y descriptivo

> **Estado:** Borrador · **Depende de:** SPEC 01, SPEC 02 · **Fecha:** YYYY-MM-DD
> **Objetivo:** Una sola frase. Si necesitas dos frases, la feature es demasiado grande.
```

**Estados válidos:** `Borrador`, `En revisión`, `Aprobado`, `Implementado`, `Obsoleto`.

**Regla del objetivo:** una frase que un humano lea en 5 segundos y entienda qué se va a construir. Si no cabe en una frase, divide la feature.

---

## Sección 1 — Por qué este spec existe (opcional)

Para specs que toman decisiones no obvias o que rompen patrones del proyecto, una sección breve explicando el **por qué** del trabajo. No el qué — el qué viene después.

Para specs simples, omítela.

---

## Sección 2 — Alcance

Dos sub-bloques explícitos. **Los dos son obligatorios.**

```markdown
## Alcance

**Dentro:**

- Cosa concreta uno.
- Cosa concreta dos.

**Fuera de alcance (para specs futuros):**

- Cosa que se podría hacer pero no ahora.
- Cosa que se mencionó en la conversación pero no entra.
```

**Por qué importa el "fuera":** captura las cosas que el usuario mencionó durante la fase de preguntas pero que se decidió posponer. Sin ese registro, en la implementación va a haber tentación de meterlas "ya que estamos".

---

## Sección 3 — Modelo de datos

Las estructuras concretas que aparecen o cambian. Usa código real, no pseudocódigo abstracto.

```markdown
## Modelo de datos

\`\`\`js
// Estado del juego
const state = {
  level: 1,
  score: 0,
  highScores: [/* { score, level, date } */],
};
\`\`\`

Convenciones:

- Coordenadas: origen arriba-izquierda.
- Velocidades en píxeles/frame.
```

Si la feature no introduce datos nuevos, escríbelo explícitamente: *"Esta feature no introduce estructuras de datos nuevas. Reusa el modelo del SPEC 01."*

---

## Sección 4 — Plan de implementación

Pasos numerados. Cada paso tiene que dejar el sistema en un estado **funcional y ejecutable**. Nada de "implementar la mitad y seguir mañana".

```markdown
## Plan de implementación

1. Crear archivo X con esqueleto vacío.
2. Implementar función A en X. Test manual: ejecutar Y, ver Z.
3. Conectar X con módulo W existente.
4. ...
```

**Reglas:**

- Cada paso debe ser commitable por sí solo.
- Si un paso requiere más de 30-50 líneas de código, divídelo.
- El último paso del plan **no** es "probar todo" — eso son los criterios de aceptación.

---

## Sección 5 — Criterios de aceptación

Checklist de booleanos. Cada item se puede verificar con sí o no.

```markdown
## Criterios de aceptación

- [ ] El juego carga sin errores en consola.
- [ ] Romper un ladrillo suma 10 puntos exactos.
- [ ] Recargar la página preserva los high-scores.
```

**Anti-patrones a evitar:**

- ❌ "Que funcione bien." → no es verificable.
- ❌ "Buena UX." → subjetivo.
- ❌ "Sin bugs." → no operacional.
- ✅ "Pulsar Esc pausa el juego y muestra el menú." → verificable, booleano.

---

## Sección 6 — Decisiones tomadas y descartadas

La sección que más valor tiene a 3 meses vista. Captura **lo que consideraste**, no solo lo que elegiste.

```markdown
## Decisiones

- **Sí:** localStorage para persistencia. Cabe en <5MB y no necesitamos queries.
- **No:** IndexedDB. Sobreingeniería para este caso.
- **Sí:** key versionada (`save:v1`). Permite migrar el schema más adelante sin romper.
- **No:** sincronización con cloud. Va en otro spec si llega.
```

Cada decisión idealmente tiene una razón breve. Las decisiones sin razón son las primeras que se cuestionan después.

---

## Sección 7 — Riesgos identificados (opcional)

Solo cuando hay riesgos no obvios. Tabla simple:

```markdown
## Riesgos

| Riesgo | Mitigación |
|--------|-----------|
| localStorage deshabilitado en modo privado | Fallback a objeto en memoria. El juego sigue funcionando, solo no persiste. |
| Schema futuro incompatible | Key incluye `:v1`. Migración documentada en `persistence.js`. |
```

Para specs pequeños o de feature muy contenida, omítela.

---

## Sección final — Lo que NO entra (refuerzo)

Repetir explícitamente al final lo que **no** se va a hacer en este spec. Esta repetición es deliberada — la sección de Alcance lo dice, pero al final del documento sirve como recordatorio cuando alguien lee solo las últimas líneas.

```markdown
## Lo que **no** entra en este spec

- Editor visual (otro spec si llega).
- Multiplayer.
- Versión móvil.

Cada uno de esos, si llega, va en su propio spec.
```

---

## Reglas globales sobre el documento entero

- **Una frase por idea.** Si una frase tiene dos comas y un punto y coma, divídela.
- **Nombres concretos.** Si dices "el módulo de niveles", di `src/levels.js`. Si dices "una clave", da el string exacto.
- **Sin TODOs.** Un TODO en un spec significa que la decisión no se tomó. Tómala o anótala como decisión pendiente con razón.
- **Sin código ejecutable largo.** El spec describe; el código se escribe después. Snippets cortos para ilustrar estructuras de datos están bien; funciones completas no.
- **Markdown estándar.** Sin extensiones raras. Tiene que renderizar en GitHub sin sorpresas.
