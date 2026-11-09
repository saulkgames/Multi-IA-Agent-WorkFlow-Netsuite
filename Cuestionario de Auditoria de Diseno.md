# Cuestionario de auditoría de diseño

Versión 1.0 · 2026-11-09 · Documento a auditar: diseño v0.5 y posteriores.

## Propósito

Este cuestionario asegura que la auditoría de cada nueva versión del diseño revise todas las observaciones previas, aunque el auditor no las repita. Complementa la auditoría abierta; no la sustituye. El auditor puede reportar hallazgos nuevos fuera del cuestionario.

| Bloque | Origen | Preguntas |
|---|---|---|
| A | Auditoría v1 (`Auditoria de Diseno de Sistema.json`) | A-01 a A-13 |
| B | Revisión propia de la sesión de Claude Code del 2026-11-09 sobre v0.4 y v0.4.1. No forma parte de la auditoría v1. | B-01 a B-14 |

## Reglas de mantenimiento

1. La versión 1.0 se registra en Git antes de escribir el diseño v0.5. El historial demuestra que no se ajustó al diseño.
2. Las versiones siguientes solo agregan preguntas. No modifican ni eliminan las existentes.
3. Una pregunta que deje de aplicar se marca "retirada" con su motivo; no se borra.
4. Cada pregunta evalúa si el diseño trata el riesgo, no si aplicó un remedio específico.

## Instrucciones para el auditor

1. Para cada pregunta, buscar la evidencia en el documento auditado y citar la sección.
2. Calificar con la escala siguiente y justificar en una oración.
3. Aplicar las reglas de calificación sin excepciones.

| Resultado | Significado |
|---|---|
| Cumple | El documento satisface todos los incisos del criterio de aprobación. |
| Parcial | Satisface una parte, o el control existe solo como instrucción del prompt cuando el criterio pide un control técnico, o depende de un supuesto con prueba y criterio de aceptación definidos. |
| No cumple | No hay evidencia, o el control se menciona como futuro sin especificación. |
| No aplica | La pregunta no corresponde a la versión auditada. Requiere motivo. |

| Regla de calificación | Efecto |
|---|---|
| El criterio pide un control técnico y el diseño solo tiene una instrucción del prompt | Máximo Parcial |
| El control depende de un comportamiento no comprobado, marcado como `[supuesto]` con prueba y criterio de aceptación | Máximo Parcial; anotar el supuesto |
| El control está descrito como futuro, pendiente o propuesto, sin especificación | No cumple |
| El diseño resuelve el riesgo con un remedio distinto al sugerido por la auditoría v1 | Válido; se califica contra el criterio, no contra el remedio |

## Bloque A. Observaciones de la auditoría v1

### A.1 Riesgos y recomendaciones

#### A-01. Integridad del flujo frente a instrucciones del usuario

| Campo | Contenido |
|---|---|
| Origen | `audit_summary.primary_bottleneck`, `swot_analysis.weaknesses[1]`, `prompt_engineering_recommendations[0]` |
| Prioridad en v1 | Cuello de botella principal |
| Pregunta | ¿Qué impide que el Orquestador ejecute una transición que la tabla no permite cuando el usuario da una instrucción informal, ambigua o contradictoria? |
| Criterio de aprobación | (a) El Orquestador valida el estado y el evento contra la tabla antes de cada delegación. (b) El diseño separa el estado leído del ticket de las instrucciones del usuario. (c) Define qué hace ante una instrucción fuera de la tabla, de forma compatible con las facultades que el propio diseño da al usuario: aprobar, editar el frontmatter y replantear. (d) La desviación queda registrada. |
| No basta | Una instrucción del prompt sin validación verificable. |

#### A-02. Privilegios técnicos de la sesión principal

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.threats[0]` |
| Prioridad en v1 | Sin prioridad asignada |
| Pregunta | ¿Qué herramientas tiene técnicamente la sesión principal y qué control, distinto del prompt, impide que ejecute Bash, escriba en el repo o consulte NetSuite? |
| Criterio de aprobación | (a) La lista de herramientas de la sesión principal está definida por configuración. (b) Se indica el mecanismo que la aplica. (c) Se indica la prueba para comprobarlo. |
| No basta | Una tabla de herramientas que solo describe la intención. |

#### A-03. Contrato de salida: esquema y validación

| Campo | Contenido |
|---|---|
| Origen | `audit_summary.primary_bottleneck`, `swot_analysis.weaknesses[0]`, `swot_analysis.opportunities[0]`, `prompt_engineering_recommendations[0]`, `remediation_plan[0]` |
| Prioridad en v1 | HIGH |
| Pregunta | ¿Cómo se garantiza que la salida de cada subagente cumpla el contrato y que el Orquestador la interprete sin ambigüedad? |
| Criterio de aprobación | (a) Esquema formal por agente y modo: campos, tipos, obligatoriedad y valores permitidos. (b) Validación determinista antes de que el Orquestador use la salida. (c) Reacción definida ante una salida inválida, con límite de reintentos. El formato (YAML, JSON o XML) es libre si cumple a y b. |
| No basta | Cambiar el formato sin esquema ni validador. |

#### A-04. Bash: control técnico por agente

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.weaknesses[2]`, `swot_analysis.threats[0]`, `remediation_plan[1]` |
| Prioridad en v1 | CRITICAL |
| Pregunta | ¿Qué control técnico limita los comandos Bash de cada agente y qué pasa con un comando fuera de su límite? |
| Criterio de aprobación | (a) Lista de comandos permitidos o denegados por agente. (b) Mecanismo que la aplica y cómo identifica al agente que ejecuta el comando. (c) Comportamiento ante un comando no permitido. (d) Prueba, incluida la invocación indirecta de un comando denegado. |
| No basta | Instrucciones del prompt, o un hook mencionado como futuro sin especificación. |

#### A-05. Validación del estado antes de delegar

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.opportunities[2]` |
| Prioridad en v1 | Sin prioridad asignada |
| Pregunta | ¿Se comprueba de forma determinista que el ticket está en un estado coherente antes de cada delegación? |
| Criterio de aprobación | (a) Qué se valida: estado frente a secciones escritas, contadores y precondiciones de la transición. (b) Qué lo ejecuta y en qué momento. (c) Qué ocurre si la validación falla. |
| No basta | Una revisión hecha por el modelo al leer el ticket. |

#### A-06. Alineación y proporcionalidad del plan

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.opportunities[1]`, `prompt_engineering_recommendations[1]` |
| Prioridad en v1 | Sin prioridad asignada |
| Pregunta | ¿Cómo asegura el diseño que el plan del Arquitecto cubra los criterios confirmados y no exceda la complejidad necesaria? |
| Criterio de aprobación | (a) Cada criterio de aceptación se vincula con un elemento del plan. (b) El Arquitecto compara al menos una alternativa más simple y registra por qué la descarta. (c) El nivel de proporcionalidad está justificado. (d) Existe un punto posterior que detecta una desalineación. |
| No basta | Exigir una técnica de razonamiento sin registro verificable. |

#### A-07. Causa raíz con evidencia

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.opportunities[1]`, `prompt_engineering_recommendations[2]` |
| Prioridad en v1 | Sin prioridad asignada |
| Pregunta | ¿Qué impide que el Debug declare una causa raíz sin evidencia suficiente? |
| Criterio de aprobación | (a) El Debug registra cada hipótesis con su evidencia a favor y en contra. (b) Se define qué evidencia es aceptable. (c) Se define cuándo declara "no determinada". (d) Los campos de salida del Debug son coherentes con el contrato. |
| No basta | Un número fijo de hipótesis sin criterio de evidencia. |

#### A-08. Edición simultánea del ticket

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.threats[1]` |
| Prioridad en v1 | Sin prioridad asignada |
| Pregunta | ¿Qué ocurre si el usuario edita el ticket mientras un agente lo escribe? |
| Criterio de aprobación | (a) Regla sobre quién puede editar el ticket y cuándo. (b) Mecanismo que detecta un cambio concurrente o un estado incoherente. (c) Procedimiento de recuperación. |
| No basta | Suponer que no ocurrirá. |

#### A-09. Tamaño del ticket y ventana de contexto

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.threats[2]`, `remediation_plan[2]` |
| Prioridad en v1 | MEDIUM |
| Pregunta | ¿Cómo evita el diseño que el ticket, incluida la Bitácora, exceda la ventana de contexto en tickets largos, sin perder la trazabilidad que el propio diseño exige? |
| Criterio de aprobación | (a) Estimación o límite del tamaño por ticket. (b) Qué parte del ticket lee cada agente. (c) Qué ocurre si se supera el límite. (d) Compatibilidad con las reglas del diseño sobre la Bitácora. |
| No basta | Una rutina de resumen que elimine registros que otra regla del diseño necesita. |

### A.2 Fortalezas que deben conservarse

#### A-10. Estado persistido en archivos

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[0]` |
| Pregunta | ¿La versión auditada conserva el estado del flujo en archivos, de modo que un ticket se retome tras cortar la sesión solo con leerlo? |
| Criterio de aprobación | (a) El estado completo, incluidos los contadores, está en archivos. (b) Existe una regla para retomar un ticket. (c) Si el mecanismo cambió, el cambio conserva la propiedad. |

#### A-11. Separación de permisos entre agentes

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[1]` |
| Pregunta | ¿La versión auditada conserva la separación de Bash, MCP y escritura entre agentes? |
| Criterio de aprobación | (a) Existe una matriz de permisos por agente. (b) Ningún agente gana permisos respecto a la versión anterior sin un motivo declarado. |

#### A-12. Operación sin MCP

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[2]` |
| Pregunta | ¿La versión auditada conserva los adaptadores `mcp`, `sdf` y `manual` sin cambiar las etapas del flujo? |
| Criterio de aprobación | (a) Cada función que usa NetSuite tiene su equivalente en los tres modos, o el hueco está declarado. (b) Las etapas del flujo son las mismas en los tres modos. |

#### A-13. Contrato de delegación definido

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[3]` |
| Pregunta | ¿La versión auditada mantiene un contrato de entrada y salida para cada delegación? |
| Criterio de aprobación | (a) El contrato está documentado para todos los agentes y modos. (b) Incluye la regla para salidas incompletas o inválidas. |

### A.3 Cobertura de la auditoría v1

Cada observación de la auditoría v1 queda cubierta por al menos una pregunta.

| Observación v1 | Texto | Preguntas |
|---|---|---|
| `audit_summary.primary_bottleneck` | "La arquitectura depende críticamente de un Orquestador (Claude Code) que delega y hace cumplir reglas mediante prompting sobre una sesión de chat abierta con el usuario, lo que genera alto riesgo de saltos de estado y contaminación de contexto. Falta de definición explícita de esquemas JSON rígidos en los contratos de los workers." | A-01, A-03 |
| `swot_analysis.strengths[0]` | "Diseño claro de la máquina de estados persistida en un archivo Markdown (ticket.md), lo que garantiza resiliencia ante cortes de sesión." | A-10 |
| `swot_analysis.strengths[1]` | "Separación estricta de permisos (Bash, MCP, File System) entre roles especialistas (Bóveda, Arquitecto, Coder, Verificador, Debug)." | A-11 |
| `swot_analysis.strengths[2]` | "Manejo explícito de modos sin MCP (adaptadores sdf/manual) considerando limitaciones del entorno del cliente." | A-12 |
| `swot_analysis.strengths[3]` | "Uso de un contrato de entrada/salida definido para la delegación de subagentes." | A-13 |
| `swot_analysis.weaknesses[0]` | "El contrato de salida de los subagentes está definido en YAML al final del texto libre, lo que es propenso a alucinaciones de formato; debería ser un esquema JSON estricto o bloques XML puros." | A-03 |
| `swot_analysis.weaknesses[1]` | "El Orquestador (sesión principal) comparte contexto con el usuario, siendo vulnerable a inyecciones de prompt accidentales o directivas contradictorias que rompan la máquina de estados." | A-01 |
| `swot_analysis.weaknesses[2]` | "Dependencia excesiva en instrucciones de prompt para restricciones críticas de seguridad (ej. límites de Bash), a pesar de mencionar hooks futuros." | A-04 |
| `swot_analysis.opportunities[0]` | "Implementación de esquemas JSON formales (JSON Schema) para las respuestas de los subagentes (Verificador, Arquitecto) para forzar parsing programático seguro." | A-03 |
| `swot_analysis.opportunities[1]` | "Uso de técnicas de razonamiento (Chain-of-Thought) en el Arquitecto y Debugger para documentar hipótesis antes de emitir la salida estructurada." | A-06, A-07 |
| `swot_analysis.opportunities[2]` | "Automatización de la verificación de estado mediante un script pre-hook antes de cada delegación del Orquestador." | A-05 |
| `swot_analysis.threats[0]` | "La incapacidad técnica actual de Claude Code para aislar completamente la sesión principal del usuario puede permitir jailbreaks que ejecuten comandos Bash no autorizados." | A-02, A-04 |
| `swot_analysis.threats[1]` | "Condiciones de carrera o corrupción de estado si el usuario edita manualmente el `ticket.md` de forma concurrente con un subagente." | A-08 |
| `swot_analysis.threats[2]` | "Fallos de rendimiento o desbordamiento de contexto si la bitácora del `ticket.md` crece sin límite en ciclos largos de debug." | A-09 |
| `prompt_engineering_recommendations[0]` | Orquestador: "Vulnerabilidad a desvíos de flujo causados por mensajes de usuario informales y falta de rigidez en la validación del contrato YAML." | A-01, A-03 |
| `prompt_engineering_recommendations[1]` | Arquitecto: "El Arquitecto genera planes complejos basados en texto que pueden perder alineación con los requerimientos originales o exceder la proporcionalidad." | A-06 |
| `prompt_engineering_recommendations[2]` | Debug: "Riesgo de alucinación de causas raíz sin evidencia técnica suficiente." | A-07 |
| `remediation_plan[0]` (HIGH) | "Migrar todos los contratos de salida de los subagentes de YAML a JSON estricto delimitado por bloques Markdown de código para asegurar un parsing determinista por parte del Orquestador." | A-03 |
| `remediation_plan[1]` (CRITICAL) | "Implementar el script de validación pre-hook propuesto (PreToolUse) de forma inmediata, no solo para validación MCP, sino para denegar el uso de Bash fuera de los límites definidos, sin depender exclusivamente del System Prompt." | A-04 |
| `remediation_plan[2]` (MEDIUM) | "Configurar una rutina de truncamiento o resumen en la Bóveda para el archivo `ticket.md` (específicamente la Bitácora) para evitar superar la ventana de contexto en tickets de larga duración." | A-09 |

## Bloque B. Revisión propia

Origen: revisión de la sesión de Claude Code del 2026-11-09. Las prioridades de este bloque son de esa revisión, no de la auditoría v1.

### B.0 Correspondencia con las correcciones de la revisión

| Corrección | Tema | Pregunta |
|---|---|---|
| 1 | Una sola tabla de transiciones, con el diagnóstico en iteración y el regreso a borrador | B-01 |
| 2 | Tipo de bloqueo con destino fijo | B-02 |
| 3 | Contadores, valor inicial de `iteracion`, meta frente a límite, puertas humanas | B-01, B-03 |
| 4 | Un solo escritor real del frontmatter | B-04 |
| 5 | Límite de escritura aplicable por agente | B-05 |
| 6 | Reapertura por regresión | B-06 |
| 7 | Sesión principal con herramientas restringidas | A-02 |
| 8 | Control técnico de Bash y de rutas de escritura por agente | A-04, B-05 |
| 9 | Validación del contrato | A-03 |
| 10 | Autorización para consultar producción | B-07 |
| 11 | Barreras independientes de Claude Code | B-08 |
| 12 | Ramas, integración y despliegue | B-09 |
| 13 | Activación de la revisión fiscal | B-10 |
| 14 | Anonimización verificable | B-11 |
| 15 | Remedios de la auditoría v1 que contradicen el diseño | B-12 |
| — | Entrada no confiable en Verificador y Debug (análisis inicial de v0.4) | B-13 |
| — | Ruta por tipo de ticket (consolidación de v0.4.1) | B-14 |

### B.1 Preguntas

#### B-01. Coherencia de la máquina de estados

| Campo | Contenido |
|---|---|
| Prioridad | Alta |
| Riesgo | Si la tabla de transiciones, el diagrama y la tabla de rechazos no coinciden, la skill de orquestación se construye con rutas incompletas. |
| Pregunta | ¿La tabla de transiciones, el diagrama de estados y la tabla de rechazos describen el mismo conjunto de estados, eventos y transiciones? |
| Criterio de aprobación | (a) Cada estado y transición aparece en todas las representaciones, o se declara una sola como fuente de verdad. (b) Incluye el diagnóstico durante la iteración y el regreso a `borrador` por criterios ambiguos, o justifica su eliminación. (c) Las puertas humanas están enumeradas y ubicadas en la tabla. |

#### B-02. Resultado de bloqueo sin ambigüedad

| Campo | Contenido |
|---|---|
| Prioridad | Alta |
| Riesgo | El mismo resultado lleva a estados distintos según la tabla que se consulte; la transición deja de ser determinista. |
| Pregunta | ¿Cada tipo de bloqueo lleva a un único estado siguiente? |
| Criterio de aprobación | (a) Los tipos de bloqueo están enumerados en el contrato. (b) Cada tipo tiene destino y efecto sobre los contadores. (c) La tabla y el diagrama coinciden. |

#### B-03. Límites aplicables al retomar el ticket

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | Tras cortar la sesión, un límite sin contador persistido no se puede aplicar y el ciclo continúa sin escalar. |
| Pregunta | ¿Cada límite tiene un contador persistido con valor inicial, momento de incremento y momento de reinicio? |
| Criterio de aprobación | (a) Todos los límites del diseño tienen contador o una regla de conteo determinista. (b) Cada contador declara valor inicial, incremento y reinicio. (c) Se distingue una meta de desempeño de un límite que bloquea. |

#### B-04. Un escritor por variable

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | Dos fuentes contradictorias sobre quién escribe cada dato; la regla "un escritor por variable" no se puede verificar. |
| Pregunta | ¿La matriz de herramientas y la tabla de variables coinciden en quién escribe cada variable del frontmatter y cada sección del ticket? |
| Criterio de aprobación | (a) Cada variable y cada sección tiene un solo escritor. (b) Ambas tablas lo dicen igual. (c) La regla de `impacto_fiscal`, que solo pasa de `false` a `true`, se aplica en un punto único. |

#### B-05. Límite de escritura aplicable

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | Las herramientas de escritura operan sobre archivos completos; si el límite es por sección, cualquier agente con permiso de edición puede cambiar `estado` u otras secciones. |
| Pregunta | ¿El límite de lo que puede escribir cada agente se aplica técnicamente o depende del prompt? |
| Criterio de aprobación | (a) El límite se expresa en una unidad que el mecanismo de control puede aplicar, como archivo o ruta. (b) Se indica el mecanismo. (c) Si el límite queda solo en el prompt, se declara como riesgo aceptado. |

#### B-06. Reapertura por regresión

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | Las regresiones se cuentan como tickets nuevos y la métrica 1 (errores que llegan a producción) queda subestimada. |
| Pregunta | ¿Está definido qué pasa con la ficha nueva, el ticket original, sus contadores y su rama cuando una falla en producción se atribuye a un ticket cerrado? |
| Criterio de aprobación | (a) Destino de cada uno de esos elementos. (b) La métrica 1 cuenta la regresión una sola vez. |

#### B-07. Autorización para consultar producción

| Campo | Contenido |
|---|---|
| Prioridad | Alta |
| Riesgo | Si la autorización es un archivo o una marca que un agente puede escribir, la confirmación del usuario se puede anular. |
| Pregunta | ¿La autorización para consultar producción la da el usuario por un medio que un agente no puede crear ni simular, y la detección de producción es confiable cuando hay más de un servidor MCP? |
| Criterio de aprobación | (a) Medio de autorización y por qué un agente no puede generarlo. (b) Regla de detección del entorno aplicable a cada servidor configurado. (c) Ante duda sobre el entorno, la operación se detiene. |

#### B-08. Barreras independientes de Claude Code

| Campo | Contenido |
|---|---|
| Prioridad | Alta |
| Riesgo | Una regla de permisos de Bash compara el texto del comando; una invocación indirecta o un script escrito por el Coder la evade y permite desplegar. |
| Pregunta | ¿Qué impide modificar datos en NetSuite o desplegar si fallan todos los controles de Claude Code? |
| Criterio de aprobación | (a) El rol de NetSuite del conector es de solo lectura y es un requisito verificable del alta. (b) Se definen la ubicación y el uso de las credenciales de despliegue SDF, de forma que un agente no pueda usarlas. (c) El diseño no presenta la denegación de comandos por texto como barrera suficiente. |

#### B-09. Ramas, integración y despliegue

| Campo | Contenido |
|---|---|
| Prioridad | Alta |
| Riesgo | El despliegue SDF lleva lo que lista `deploy.xml`; sin política, un despliegue puede llevar código no verificado de otro ticket o revertir uno ya desplegado. |
| Pregunta | ¿Están definidos la rama base, el momento de integración, la rama desde la que se despliega, el alcance de `deploy.xml` y el manejo de tickets simultáneos? |
| Criterio de aprobación | (a) Cada uno de esos puntos está definido. (b) El trabajo simultáneo de dos tickets del mismo cliente queda aislado. |

#### B-10. Activación de la revisión fiscal

| Campo | Contenido |
|---|---|
| Prioridad | Alta |
| Riesgo | Un falso negativo en la ingesta desactiva `fiscal-cfdi` en todo el ticket. Los requisitos del CFDI los fijan los arts. 29 y 29-A del CFF y el Anexo 20 de la RMF. Consecuencia: comprobantes rechazados al timbrar o emitidos sin cumplir requisitos; para el receptor, riesgo de no deducibilidad (art. 27, fr. III, LISR). |
| Pregunta | ¿Se puede activar la revisión fiscal si el impacto se descubre después de la ingesta? |
| Criterio de aprobación | (a) Cualquier agente puede solicitar la activación. (b) Una regla determinista complementa la clasificación inicial; por ejemplo, objetos o campos que alimentan el CFDI o la Carta Porte. (c) La activación sigue siendo de un solo sentido. |

#### B-11. Anonimización verificable

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | Datos de un cliente quedan expuestos en la bóveda que comparten todos los clientes. |
| Pregunta | ¿Existe un control determinista que impida escribir identificadores del cliente en el conocimiento compartido? |
| Criterio de aprobación | (a) Un script o hook revisa patrones antes de escribir en `00_Conocimiento`: RFC, UUID de CFDI, IDs de cuenta, correos y nombre del cliente. (b) Se define qué ocurre si encuentra uno. |

#### B-12. Coherencia con los remedios de la auditoría v1

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | Adoptar un remedio externo sin ajustar las reglas propias deja el diseño internamente contradictorio. |
| Pregunta | ¿Adoptó la versión auditada algún remedio de la auditoría v1 que contradiga sus propias reglas? |
| Criterio de aprobación | (a) No hay contradicción. (b) Si una regla cambió para adoptar un remedio, el cambio se declara con su motivo. Casos a revisar: ignorar instrucciones del usuario frente a las facultades que el diseño le da; truncar o resumir una Bitácora de solo agregar; asignar un campo del contrato a un agente distinto del que lo define; exigir un número fijo de hipótesis. |

#### B-13. Entrada no confiable en agentes con más permisos

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | El diseño agrupa agentes por la confianza de su entrada y deja a la Bóveda sin Bash ni MCP por recibir texto del cliente. El Verificador y el Debug reciben texto capturado por usuarios del cliente (registros, logs, exportaciones) y sí tienen Bash y MCP. Consecuencia: una instrucción inyectada en esos datos llega a agentes con más privilegios que la Bóveda. |
| Pregunta | ¿Cómo trata el diseño el texto no confiable que llega al Verificador y al Debug desde NetSuite? |
| Criterio de aprobación | (a) Reconoce esa fuente como no confiable. (b) Define límites técnicos que reducen el daño de una instrucción inyectada. (c) Es coherente con el criterio de agrupación por confianza de la entrada. |

#### B-14. Ruta por tipo de ticket

| Campo | Contenido |
|---|---|
| Prioridad | Media |
| Riesgo | La plantilla v0.3 define cinco valores de `tipo` (bug, requerimiento, cfdi, reporte, automatizacion) y el diseño solo enruta requerimiento y bug. Un ticket de otro tipo no tiene agente ni transición definidos. |
| Pregunta | ¿Cada valor de `tipo` tiene una ruta definida? |
| Criterio de aprobación | (a) Los valores de `tipo` están enumerados. (b) Cada valor tiene su agente y su transición desde `borrador`. |

## Formato de respuesta

El auditor entrega la tabla de resultados completa. Puede entregarla también en JSON con la estructura indicada.

| ID | Resultado | Sección citada | Justificación |
|---|---|---|---|
| A-01 | | | |
| A-02 | | | |
| A-03 | | | |
| A-04 | | | |
| A-05 | | | |
| A-06 | | | |
| A-07 | | | |
| A-08 | | | |
| A-09 | | | |
| A-10 | | | |
| A-11 | | | |
| A-12 | | | |
| A-13 | | | |
| B-01 | | | |
| B-02 | | | |
| B-03 | | | |
| B-04 | | | |
| B-05 | | | |
| B-06 | | | |
| B-07 | | | |
| B-08 | | | |
| B-09 | | | |
| B-10 | | | |
| B-11 | | | |
| B-12 | | | |
| B-13 | | | |
| B-14 | | | |

```json
{
  "cuestionario_version": "1.0",
  "documento_auditado": "<nombre del archivo de diseño>",
  "resultados": [
    { "id": "A-01", "resultado": "cumple | parcial | no_cumple | no_aplica", "seccion": "<sección citada>", "justificacion": "<una oración>" }
  ],
  "resumen": { "cumple": 0, "parcial": 0, "no_cumple": 0, "no_aplica": 0 },
  "hallazgos_fuera_del_cuestionario": []
}
```

## Historial de versiones

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0 | 2026-11-09 | 13 preguntas de la auditoría v1 (bloque A) y 14 de revisión propia (bloque B). |
