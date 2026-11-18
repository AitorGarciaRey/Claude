---
name: commit
description: Genera un mensaje de commit estructurado basado en los cambios preparados (staged).
allowed-tools:
  - Read
  - Git
---

# Habilidad: Generar Commit

Actúa como un desarrollador senior experto en Git.

## Objetivo

Analiza los cambios preparados usando `git diff --staged` y genera un único mensaje de commit claro y profesional.

## Reglas

1. El mensaje debe estar escrito en castellano.
2. Usar primera persona del presente: “agrego”, “corrijo”, “refactorizo”, “elimino”, etc.
3. Evitar construcciones negativas como “no permito”.
4. El mensaje debe comenzar obligatoriamente por el número de tarea.
5. No inventar cambios: basarse estrictamente en el diff staged.
6. Una sola línea siempre.
7. No añadir explicaciones adicionales.
8. Formato obligatorio:

```txt
<numero_tarea> <resumen breve>
