---
name: spec-impl
description: Implementa un spec aprobado. Valida que el estado sea "Aprobado", crea una rama git con el nombre del spec, se mueve a ella, y arranca la implementación paso a paso con pausas para revisar diffs.
disable-model-invocation: true
argument-hint: <NN-nombre-del-spec>
allowed-tools: Bash(git status:*), Bash(git branch:*), Bash(git checkout:*), Bash(cat:*), Bash(ls:*)
---

# /spec-impl — Implementador de specs aprobados

## Contexto de la sesión

Estado del repositorio actual:
!`git status --short`

Rama actual:
!`git branch --show-current`

Specs disponibles en esta carpeta:
!`ls specs/ 2>/dev/null || echo "No existe la carpeta specs/"`

---

## Instrucciones

Sigue estas cuatro fases en orden estricto. **No avances a la siguiente fase si la anterior no se completó correctamente.**

---

### Fase 1 — Identificar el spec

El argumento recibido es: `$ARGUMENTS`

Si `$ARGUMENTS` está vacío:
- Lista los archivos disponibles en `specs/` (ya los tienes arriba).
- Pide al usuario que especifique el nombre exacto del spec.
- Detente y espera respuesta. No continúes.

Si `$ARGUMENTS` tiene valor:
- Busca el archivo en `specs/`. El usuario puede haber escrito el nombre completo (`01-mvp-arkanoid`) o solo el número (`01`) o solo el slug (`mvp-arkanoid`). Intenta encontrar el archivo correcto en cualquiera de esos casos.
- Si no encuentras el archivo, muestra los specs disponibles y pide al usuario que corrija el nombre.
- Si lo encuentras, sigue a la Fase 2.

---

### Fase 2 — Validar el estado del spec

Lee el archivo del spec encontrado:
!`cat specs/$ARGUMENTS.md 2>/dev/null || echo "ARCHIVO_NO_ENCONTRADO"`

Busca en el contenido del archivo la línea que contiene `**Estado:**`.

**Regla absoluta:** Solo puedes continuar si el estado es exactamente `Aprobado`.

| Estado encontrado | Acción |
|---|---|
| `Aprobado` | Continúa a Fase 3. |
| `Borrador` | Para. Muestra el mensaje de error de abajo. |
| `En revisión` | Para. Muestra el mensaje de error de abajo. |
| `Implementado` | Para. Muestra el mensaje de error de abajo. |
| `Obsoleto` | Para. Muestra el mensaje de error de abajo. |
| No se encuentra la línea de estado | Para. El archivo no sigue el formato esperado. Indica esto al usuario. |

**Mensaje de error estándar cuando el estado no es Aprobado:**

```
❌ No puedo implementar este spec.

Estado actual: [ESTADO ENCONTRADO]
Solo trabajo con specs en estado "Aprobado".

Para continuar tienes dos opciones:
  1. Si el spec está listo para implementarse, ábrelo y cambia el estado
     a "Aprobado" manualmente. Ese cambio lo hace el humano, no Claude.
  2. Si el spec aún necesita trabajo, usa /spec [nombre] para retomarlo.
```

No ofrezcas alternativas, no sugiereas "puedo igual empezar si quieres". El bloqueo es intencional.

---

### Fase 3 — Crear la rama git y moverse a ella

Una vez confirmado que el estado es `Aprobado`:

1. Deriva el nombre de la rama a partir del nombre completo del archivo del spec, sin la extensión. Formato: `spec-NN-slug`. Ejemplos:
   - `01-mvp-arkanoid.md` → rama `spec-01-mvp-arkanoid`
   - `02-powerups.md` → rama `spec-02-powerups`

2. Verifica si la rama ya existe:
   - Si **no existe**: créala con `git checkout -b spec-NN-slug`.
   - Si **ya existe**: informa al usuario que la rama ya existía (puede significar que se retomó un trabajo previo).
   - En ambos casos: muévete a la rama con `git checkout spec-NN-slug` y confirma que el cambio se realizó correctamente antes de continuar.

3. Confirma visualmente al usuario que la rama fue creada y que estás en ella:
   ```
   ✅ Listo para implementar.

   Spec:   specs/NN-slug.md
   Rama:   spec-NN-slug  (activa)
   Estado: Aprobado
   ```

4. **No empieces a implementar todavía.** Primero muestra el resumen del spec al usuario para que lo tenga fresco. Extrae y muestra:
   - El **objetivo** (la línea después de `**Objetivo:**`).
   - El **alcance** (la sección `## Alcance` completa).
   - El **plan de implementación** (la sección con los pasos numerados).
   - Los **criterios de aceptación** (el checklist).

---

### Fase 4 — Implementar paso a paso

Después de mostrar el resumen del spec, di al usuario:

```
Voy a implementar el spec siguiendo exactamente el plan de implementación.
Voy a pausar después de cada paso para que puedas revisar el diff.

¿Empezamos con el Paso 1?
```

Espera confirmación explícita ("sí", "adelante", "go", o equivalente). No empieces sin ella.

Una vez confirmado, sigue estas reglas durante toda la implementación:

**Una regla por encima de todas:** implementa lo que dice el spec. Si algo del spec te parece subóptimo, menciónalo como observación pero implementa lo acordado. Los cambios al spec van en el spec, no en el código por sorpresa.

**Ritmo de trabajo:**
- Implementa un paso del plan.
- Muestra un resumen de qué archivos tocaste y qué hiciste.
- Di: `Paso N completado. ¿Revisas el diff y me dices si sigo con el Paso N+1?`
- Espera confirmación antes de continuar.

**Si durante la implementación encuentras una ambigüedad** que el spec no resuelve:
- Para.
- Describe exactamente la ambigüedad.
- Presenta dos o tres opciones concretas.
- Espera decisión del usuario.
- No improvises.

**Si el usuario pide algo que está fuera del alcance del spec:**
- Recuérdale que está fuera del alcance de este spec.
- Sugiere anotarlo para el siguiente spec.
- No lo implementes en esta rama.

**Al terminar el último paso:**
```
✅ Todos los pasos del plan están implementados.

Próximo paso: verifica los criterios de aceptación del spec uno por uno.
Si todos pasan, actualiza el estado del spec a "Implementado" y haz el
commit final antes de mergear esta rama.
```

---

## Resumen del comportamiento esperado

```
/impl-spec 01-mvp-arkanoid

  Fase 1  →  Encuentra specs/01-mvp-arkanoid.md
  Fase 2  →  Lee el estado → "Aprobado" → ✅ continúa
  Fase 3  →  git checkout -b spec-01-mvp-arkanoid → git checkout spec-01-mvp-arkanoid
             Muestra objetivo, alcance, plan y criterios
  Fase 4  →  Implementa paso a paso con pausas
             Termina recordando verificar criterios de aceptación

/impl-spec 02-powerups  (estado: Borrador)

  Fase 1  →  Encuentra specs/02-powerups.md
  Fase 2  →  Lee el estado → "Borrador" → ❌ se detiene
             Muestra mensaje de error estándar
             No crea rama, no toca código
```
