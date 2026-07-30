# Comentarios extraidos: 2026-07-28 - Comments - Planificación v4.pdf

- **PDF de origen**: `context/2026-07-28 - Comments - Planificación v4.pdf`
- **Total**: 15 comentarios - 2 replicas
- **Extraido el**: 2026-07-30

---
## Comentario 1 - p. 5

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "D 36000, c"
- **Comentario**:

  > Aclarar si los USD 36.000 corresponden únicamente a horas profesionales. Los costos de cómputo mencionados posteriormente no están incluidos en el importe del acta. Unificar el presupuesto o distinguir explícitamente “costo de ingeniería” y “costos operativos contingentes”.

## Comentario 2 - p. 7

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "no contempla financiamiento de programas públicos o privados adicionales. No existen acuerdos de propiedad intelectual que restrinjan la difusión de los resultados."
- **Comentario**:

  > Aclarar la articulación entre el NDA mencionado en la sección de interesados y la afirmación de que no existen restricciones para difundir los resultados. Indicar concretamente qué información cubre el NDA y qué resultados podrán publicarse.

## Comentario 3 - p. 10

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "El proyecto incluye:"
- **Comentario**:

  > Identificar en una lista separada los componentes obligatorios y opcionales. TransFace y el segundo fine-tuning aparecen como opcionales, pero el módulo de liveness se presenta a veces como parte central y otras veces como baseline o trabajo futuro.

- **Replicas**:
  - **Cursos CAPSE** - 2026-07-29:

    > La gobernanza afirma que el prototipo implementará control de acceso y mecanismos de eliminación o actualización de registros, pero estas funciones no aparecen en el alcance, el backlog, los criterios de aceptación ni el WBS. Incorporarlas en la planificación o redactarlas únicamente como requisitos para un despliegue futuro.

## Comentario 4 - p. 10

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "Alcance"
- **Comentario**:

  > Chequea con tu tutora si el alcance no está sobredimensionado para 600 horas: incluye dos flujos funcionales, varios modelos, fine-tuning, múltiples benchmarks, evaluación demográfica, liveness, harness y demo. Debe distinguirse claramente el producto mínimo obligatorio de los componentes opcionales.

## Comentario 5 - p. 10

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "3. Propósito del proyecto"
- **Comentario**:

  > Simplificar el propósito. Actualmente incluye la pregunta experimental completa, modelos, datasets, degradaciones, métricas implícitas, demo, liveness y exclusiones. El propósito debería concentrarse en el problema y el resultado principal. Las exclusiones corresponden a la sección de alcance.

- **Replicas**:
  - **Cursos CAPSE** - 2026-07-29:

    > Definir una métrica y un conjunto de evaluación primarios. En el estado actual no queda claro si el éxito principal se medirá mediante EER en AgeDB, rendimiento en variantes degradadas, equidad en RFW o costo computacional.

## Comentario 6 - p. 10

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "¿cuánto mejora"
- **Comentario**:

  > Cambiar “cuánto mejora” por “qué efecto produce” o “si mejora y en qué magnitud”. El resultado experimental puede ser nulo o incluso negativo, posibilidad reconocida posteriormente en los criterios de aceptación y los riesgos.

## Comentario 7 - p. 12

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "En caso de no disponer"
- **Comentario**:

  > Se mezclan supuestos con riesgos, decisiones y planes de contingencia: por ejemplo, disponibilidad de nube, contratación de Colab y presupuesto adicional. Los supuestos deben ser condiciones consideradas verdaderas; las respuestas ante su incumplimiento deben trasladarse a riesgos o presupuesto.

## Comentario 8 - p. 14

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "Épica 3: Evaluación experimental y fine-tuning"
- **Comentario**:

  > Algunas historias son demasiado grandes y heterogéneas, especialmente las vinculadas con evaluación y fine-tuning. Cada HU debería representar un resultado concreto, breve y estimable, evitando que una sola historia contenga varios modelos, benchmarks y productos diferentes.

## Comentario 9 - p. 16

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "7. Criterios de aceptación de historias"
- **Comentario**:

  > Varios criterios no son objetivamente verificables: aparecen expresiones como “nitidez suficiente”, “tiempo razonable” y “réplicas suficientes”. Deben reemplazarse por cantidades, umbrales, número de ensayos y condiciones de prueba explícitas. La clave exige criterios objetivos, medibles y verificables.

## Comentario 10 - p. 17

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "nitidez suficiente"
- **Comentario**:

  > Definir cuantitativamente “nitidez suficiente”. Por ejemplo, indicar el método de medición y el umbral de varianza del Laplaciano u otra métrica que permita decidir objetivamente.

## Comentario 11 - p. 21

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "e ¿99 %)."
- **Comentario**:

  > Corregir “¿99 %”. Probablemente se quiso indicar “≈ 99 %” o “superior al 99 %”. El error se repite en distintas secciones.

## Comentario 12 - p. 36

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "12. Gobernanza de datos"
- **Comentario**:

  > Las medidas de cumplimiento no tienen trazabilidad con el resto del plan: se mencionan control de acceso, eliminación de registros, consentimiento y protección de embeddings, pero estas funciones no aparecen como HU, criterios de aceptación, tareas o sprints.

## Comentario 13 - p. 42

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "Plan de mitigación"
- **Comentario**:

  > La reevaluación posterior a la mitigación confunde severidad con ocurrencia: varias acciones reducen la probabilidad, pero se utiliza esa justificación para bajar la severidad, o viceversa. Deben revisarse individualmente los valores S* y O* y sus fundamentos.

## Comentario 14 - p. 44

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "criterios de aceptación de HU1 cumplidos"
- **Comentario**:

  > La evidencia de cumplimiento es un tanto genérica: indicar “criterios cumplidos” resulta circular. Cada revisión debe identificar pruebas concretas, resultados esperados, archivos o reportes generados y quién aprobará la historia.

## Comentario 15 - p. 44

- **Autor**: Cursos CAPSE
- **Fecha**: 2026-07-29
- **Texto resaltado en el plan**: "seleccio- nada HU1 Implemen del módu captura fa del flujo d registro (3 Sprint 1) HU5 Implemen del pipelin verificació tiempo in al inicio d (28 h en S HU8 Diseño de protocolo experimen j i´"
- **Comentario**:

  > revisar alineación de la tabla
