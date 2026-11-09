# Cuestionario de auditoría de diseño

Versión 1.1 · 2026-11-09 · Documento a auditar: diseño v0.5 y posteriores.

## Propósito

Este cuestionario asegura que la auditoría de cada nueva versión del diseño revise todas las observaciones previas, aunque el auditor no las repita. Complementa la auditoría abierta; no la sustituye. El auditor puede reportar hallazgos nuevos fuera del cuestionario.

| Bloque | Origen | Preguntas |
|---|---|---|
| A | Auditoría v1 (`Auditoria de Diseno de Sistema.json`) | A-01 a A-13 |
| B | Revisión propia de la sesión de Claude Code del 2026-11-09 sobre v0.4 y v0.4.1. No forma parte de la auditoría v1. | B-01 a B-14 |
| C | Dictamen v0.2 sobre el diseño v0.4.1 (`dictamen_auditoria_netsuite_v0_4_1.json`) | C-01 a C-20 |

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

## Bloque C. Observaciones del dictamen v0.2

Origen: `dictamen_auditoria_netsuite_v0_4_1.json` (dictamen 1.1 del 2026-11-09 sobre el diseño v0.4.1). Las observaciones del dictamen que ya cubre una pregunta de los bloques A o B se asignan a esa pregunta en la tabla de cobertura C.3; este bloque solo agrega los riesgos que no estaban cubiertos y las fortalezas nuevas que deben conservarse. La prioridad indicada es la severidad máxima de los hallazgos de origen.

### C.1 Riesgos

#### C-01. Entrada del material del cliente

| Campo | Contenido |
|---|---|
| Origen | F-001, `swot_analysis.weaknesses[0]`, `swot_analysis.threats[0]`, `prompt_engineering_recommendations[2]`, `remediation_plan` paso 1 |
| Prioridad en v0.2 | CRITICAL |
| Pregunta | ¿Por dónde entra el material del cliente (mensajes, capturas, XML) y qué herramientas tiene técnicamente la sesión que lo lee primero? |
| Criterio de aprobación | (a) El punto de entrada está definido y la sesión que lee primero el material no tiene Bash, MCP ni escritura fuera de su alcance, o esas herramientas están limitadas técnicamente para esa sesión. (b) Existe una prueba con un mensaje que contiene una orden incrustada y su resultado esperado. (c) El material con instrucciones incrustadas queda señalado al usuario. |
| No basta | Una regla de conducta para el usuario o para el modelo sin control técnico. |

#### C-02. Acceso a NetSuite por agente

| Campo | Contenido |
|---|---|
| Origen | F-011, F-036, `prompt_engineering_recommendations[8]`, `remediation_plan` pasos 5 y 18 |
| Prioridad en v0.2 | HIGH |
| Pregunta | ¿Cada agente con acceso a NetSuite tiene definidas la verificación de entorno, el tope de consultas y el registro de las consultas ejecutadas? |
| Criterio de aprobación | (a) Las reglas de entorno y de tope aplican a todo agente con MCP, no solo al Verificador. (b) El tope se cuenta con un registro persistido, no con la memoria del agente. (c) Las consultas ejecutadas quedan registradas en el ticket. |
| No basta | Reglas presentes solo en el prompt de un agente. |

#### C-03. Datos del cliente enviados al proveedor del modelo

| Campo | Contenido |
|---|---|
| Origen | F-012, `swot_analysis.threats[7]`, `remediation_plan` paso 14 |
| Prioridad en v0.2 | MEDIUM |
| Pregunta | ¿El diseño declara qué datos del cliente pueden enviarse al modelo, bajo qué condiciones y con qué alcance de permisos del rol de NetSuite? |
| Criterio de aprobación | (a) Clases de datos permitidas y prohibidas. (b) Dónde se registra el criterio o la autorización por cliente. (c) El rol del conector tiene el alcance mínimo que exigen las consultas del diseño, con justificación. (d) Coherencia con la decisión de datos locales (D12). |
| No basta | Declarar los datos como locales sin considerar lo que viaja al modelo en cada delegación. |

#### C-04. Aislamiento entre clientes

| Campo | Contenido |
|---|---|
| Origen | F-013, F-016, `swot_analysis.weaknesses[3]`, `swot_analysis.opportunities[1]`, `swot_analysis.threats[5]`, `remediation_plan` paso 4 |
| Prioridad en v0.2 | HIGH |
| Pregunta | ¿Qué impide técnicamente que una sesión abierta en el repo de un cliente lea o escriba datos de otro cliente? |
| Criterio de aprobación | (a) Mecanismo de permisos, hooks u otro control técnico, no solo rutas en las instrucciones. (b) Alcance declarado: herramientas de archivo y Bash. (c) Prueba: leer y escribir en la carpeta de otro cliente falla. (d) Riesgo residual declarado si algún canal queda sin control. |
| No basta | Que cada repo indique su carpeta de cliente en `CLAUDE.md`. |

#### C-05. Aprobaciones intermedias persistidas

| Campo | Contenido |
|---|---|
| Origen | F-015, `prompt_engineering_recommendations[5]`, `remediation_plan` paso 10 |
| Prioridad en v0.2 | HIGH |
| Pregunta | ¿Las aprobaciones del usuario dentro de una etapa, como el plan de consultas, se guardan de forma que lo ejecutado coincida con lo aprobado? |
| Criterio de aprobación | (a) Lo aprobado se guarda en el ticket con identificador y texto exacto. (b) La ejecución usa ese texto sin regenerarlo. (c) La pausa para aprobación no se confunde con otro resultado del contrato que lleve a un estado distinto. (d) Una ejecución distinta de lo aprobado se detecta. |
| No basta | Una aprobación que solo existe en la conversación. |

#### C-06. Concurrencia entre equipos y sesiones

| Campo | Contenido |
|---|---|
| Origen | F-017, F-022, `remediation_plan` paso 13 |
| Prioridad en v0.2 | MEDIUM |
| Pregunta | ¿Qué evita que dos equipos o dos sesiones modifiquen el mismo ticket o generen el mismo folio al mismo tiempo? |
| Criterio de aprobación | (a) Regla de equipo o sesión activa por cliente o por ticket. (b) Mecanismo que detecta o impide la escritura concurrente. (c) Procedimiento ante copias en conflicto. |
| No basta | Suponer un solo equipo sin una regla que lo haga cumplir. |

#### C-07. Operaciones deterministas a cargo del modelo

| Campo | Contenido |
|---|---|
| Origen | F-019, F-020, F-022, `swot_analysis.weaknesses[4]`, `swot_analysis.opportunities[2]`, `remediation_plan` pasos 11 y 13 |
| Prioridad en v0.2 | HIGH |
| Pregunta | ¿Las operaciones que deben dar siempre el mismo resultado (folio, captura del inventario, validación de XML contra XSD y catálogos) las ejecuta un script? |
| Criterio de aprobación | (a) Lista de operaciones deterministas y el script o mecanismo de cada una. (b) Controles de integridad: unicidad del folio, conteo de filas y validación de esquema. (c) Lo que queda a cargo del modelo está declarado con su riesgo. |
| No basta | Un formato de salida definido que el modelo debe llenar a mano. |

#### C-08. Reintentos sin efectos duplicados

| Campo | Contenido |
|---|---|
| Origen | F-021, `remediation_plan` paso 3 |
| Prioridad en v0.2 | MEDIUM |
| Pregunta | ¿Repetir una delegación puede duplicar commits, casos en fichas de conocimiento o líneas de Bitácora? |
| Criterio de aprobación | (a) Cada delegación tiene un identificador. (b) Antes de repetir se verifica qué aplicó la ejecución anterior. (c) Los escritores evitan duplicar con ese identificador. |
| No basta | Limitar el número de reintentos. |

#### C-09. Contenido externo tratado como dato

| Campo | Contenido |
|---|---|
| Origen | F-023, F-024, F-028, `swot_analysis.weaknesses[0]`, `swot_analysis.threats[0]`, `prompt_engineering_recommendations[2]` y `[7]`, `remediation_plan` paso 7 |
| Prioridad en v0.2 | HIGH |
| Pregunta | ¿Todos los agentes delimitan el contenido externo (ficha, adjuntos, resultados de SuiteQL, CSV, logs, inventario y fichas de `00_Conocimiento`) y lo tratan como dato? |
| Criterio de aprobación | (a) Cada prompt identifica sus fuentes externas y las delimita. (b) Define la conducta ante instrucciones incrustadas: no seguirlas y reportarlas. (c) El contrato de entrada no lleva texto del cliente en campos de instrucción. (d) Existe una prueba por tipo de fuente. |
| No basta | Una sola instrucción genérica de "ignorar instrucciones" sin delimitación ni prueba. |

#### C-10. Dependencias externas con versión fija

| Campo | Contenido |
|---|---|
| Origen | F-025, `swot_analysis.threats[2]`, `remediation_plan` paso 14 |
| Prioridad en v0.2 | MEDIUM |
| Pregunta | ¿Las skills de terceros y los paquetes que instala el kit tienen versión o commit fijo y revisión antes de actualizarse? |
| Criterio de aprobación | (a) Versión del paquete y commit del repositorio fijados. (b) Procedimiento de actualización con revisión. (c) Registro de la versión instalada. |
| No basta | Instalar la última versión disponible. |

#### C-11. Artefactos ejecutables del diseño

| Campo | Contenido |
|---|---|
| Origen | F-026, `prompt_engineering_recommendations[0]`, `[8]` y `[9]`, `remediation_plan` paso 9 |
| Prioridad en v0.2 | HIGH |
| Pregunta | ¿Existe el texto de los prompts del Orquestador y de cada agente, y de las skills nuevas que el diseño declara, de modo que su calidad sea evaluable? |
| Criterio de aprobación | (a) Prompts completos del Orquestador y de los cinco agentes. (b) Skills `orquestacion-ticket`, `debug-netsuite` y `fiscal-cfdi` en su versión de tres modos. (c) Especificación o código de los scripts y hooks de los que dependen los controles. (d) Lo que falte está listado con su motivo. |
| No basta | Tablas de rol, metas y herramientas sin el texto del prompt. |

#### C-12. Coherencia entre artefactos y diseño

| Campo | Contenido |
|---|---|
| Origen | F-027, F-030, F-035, `swot_analysis.weaknesses[5]`, `prompt_engineering_recommendations[1]` y `[4]`, `remediation_plan` pasos 9 y 18 |
| Prioridad en v0.2 | HIGH |
| Pregunta | ¿Los prompts, plantillas y skills usan los mismos estados, tipos de dato, valores permitidos y contrato que el diseño? |
| Criterio de aprobación | (a) Ningún prompt cambia variables que el diseño asigna a otro escritor. (b) El tipo de cada campo coincide entre prompts y plantillas. (c) Las salidas siguen el contrato. (d) Todo agente con skills indica invocarlas si la precarga falla. |
| No basta | Declarar que los artefactos v0.3 se reescribirán. |

#### C-13. Contexto acumulado en la sesión principal

| Campo | Contenido |
|---|---|
| Origen | F-031, `swot_analysis.opportunities[5]`, `remediation_plan` paso 15 |
| Prioridad en v0.2 | MEDIUM |
| Pregunta | ¿Cómo se acota el contexto que acumula la sesión principal a lo largo de un ticket o de un día de trabajo? |
| Criterio de aprobación | (a) Regla para iniciar o limpiar la sesión por etapa o por ticket. (b) El estado necesario para continuar se obtiene de archivos. (c) Cada delegación indica qué secciones debe leer el agente. |
| No basta | Confiar en la compactación automática del contexto. |

#### C-14. Medición y calibración

| Campo | Contenido |
|---|---|
| Origen | F-033, `swot_analysis.opportunities[6]`, `swot_analysis.threats[6]`, `remediation_plan` paso 16 |
| Prioridad en v0.2 | MEDIUM |
| Pregunta | ¿Las métricas declaradas se pueden calcular con los campos de la plantilla, y existe un procedimiento para calibrar los límites con datos? |
| Criterio de aprobación | (a) Cada métrica tiene sus campos y su consulta. (b) La métrica 1 aplica su ventana de tiempo. (c) Procedimiento y momento para ajustar los límites de iteración. |
| No basta | Métricas definidas sin campos que las alimenten. |

#### C-15. Supuestos de plataforma

| Campo | Contenido |
|---|---|
| Origen | F-034, `swot_analysis.threats[1]`, `[3]` y `[4]`, `remediation_plan` paso 17 |
| Prioridad en v0.2 | MEDIUM |
| Pregunta | ¿Los controles que dependen del comportamiento de Claude Code o de NetSuite están verificados antes del piloto, y se vuelven a verificar al cambiar la plataforma? |
| Criterio de aprobación | (a) Cada supuesto de plataforma del que depende un control está verificado o refutado, con evidencia. (b) Los que siguen abiertos tienen prueba y criterio de aceptación. (c) Procedimiento de reverificación al actualizar Claude Code, refrescar un Sandbox o cambiar el conector. |
| No basta | Listar los supuestos como pendientes del piloto sin prueba definida. |

### C.2 Fortalezas que deben conservarse

#### C-16. Comparación determinista del inventario

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[3]` |
| Pregunta | ¿La versión auditada conserva la comparación del inventario por script, aplicada solo con aprobación y con falla cerrada? |
| Criterio de aprobación | (a) La comparación la hace un script. (b) La línea base solo se actualiza con aprobación. (c) Ante un error, la línea base no se modifica. |

#### C-17. Ciclos acotados con escalamiento

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[4]` |
| Pregunta | ¿La versión auditada conserva un límite por ciclo y el escalamiento inmediato ante un rechazo que repite el motivo anterior? |
| Criterio de aprobación | (a) Cada ciclo tiene límite. (b) La regla de motivo repetido sigue presente. |

#### C-18. Reglas de acceso del Verificador

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[5]` |
| Pregunta | ¿La versión auditada conserva en el Verificador la verificación de entorno, el tope de consultas, los filtros en producción, la aprobación previa y el manejo de errores de permisos? |
| Criterio de aprobación | Las cinco reglas están presentes en el prompt o en un control técnico equivalente. |

#### C-19. Reglas fiscales

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[6]` |
| Pregunta | ¿La versión auditada conserva la prohibición de inventar fundamentos, la exigencia de versión del catálogo y el orden SAT antes que PAC? |
| Criterio de aprobación | Las tres reglas están presentes en `fiscal-cfdi` o en el agente que la aplica. |

#### C-20. Registro de decisiones, supuestos e inconsistencias

| Campo | Contenido |
|---|---|
| Origen | `swot_analysis.strengths[7]` |
| Pregunta | ¿La versión auditada conserva el registro explícito de decisiones, supuestos e inconsistencias con marcas de evidencia? |
| Criterio de aprobación | (a) El registro existe. (b) Está actualizado a la versión auditada. |

### C.3 Cobertura del dictamen v0.2

Cada observación del dictamen v0.2 queda cubierta por al menos una pregunta, salvo la oportunidad `swot_analysis.opportunities[3]`, que es una mejora de eficiencia sin riesgo asociado.

#### Hallazgos

| Hallazgo | Severidad | Título | Preguntas |
|---|---|---|---|
| F-001 | CRITICAL | Texto del cliente entra por la sesión con más privilegios | C-01, A-02 |
| F-002 | HIGH | Tabla de transiciones incompleta: faltan en_diagnostico y confirmada→borrador | B-01 |
| F-003 | HIGH | El bloqueo tiene dos destinos contradictorios | B-02 |
| F-004 | HIGH | Tres de cinco valores de tipo no tienen ruta | B-14 |
| F-005 | HIGH | Sin política de ramas: despliegues concurrentes pueden sobrescribirse | B-09 |
| F-006 | MEDIUM | Límites declarados sin contador que los haga cumplir | B-03 |
| F-007 | MEDIUM | Reinicio de contadores no definido al salir de bloqueada o reabrir | B-03, B-06 |
| F-008 | HIGH | Alcance de Bash por agente solo existe en el prompt | A-04 |
| F-009 | HIGH | La negación del despliegue no está probada y admite rodeos | B-08, A-04 |
| F-010 | HIGH | Compuerta de producción blanda y diseño del hook autoaprobable | B-07 |
| F-011 | HIGH | Debug con MCP sin verificación de entorno ni límite de consultas | C-02 |
| F-012 | MEDIUM | Procesamiento de datos del cliente por el proveedor del modelo no declarado | C-03 |
| F-013 | HIGH | Toda la bóveda es accesible desde el repo de cualquier cliente | C-04 |
| F-014 | HIGH | Escritor único y Bitácora de solo agregar no son aplicables | B-04, B-05 |
| F-015 | HIGH | La aprobación del plan de consultas no persiste entre invocaciones | C-05 |
| F-016 | MEDIUM | El Coder escribe en conocimiento compartido sin reglas de anonimización | C-04, B-11 |
| F-017 | MEDIUM | Varios equipos sin control de concurrencia sobre el ticket | C-06, A-08 |
| F-018 | HIGH | La máquina de estados la ejecuta el modelo, no un script | A-05, A-01 |
| F-019 | HIGH | El modelo transcribe a JSON la línea base del inventario | C-07 |
| F-020 | HIGH | Validación de XML contra XSD y catálogos asignada al modelo | C-07 |
| F-021 | MEDIUM | El reintento por contrato inválido no es idempotente | C-08 |
| F-022 | MEDIUM | El folio, llave de todo el flujo, lo calcula el modelo | C-07, C-06 |
| F-023 | HIGH | Contenido no confiable llega a agentes con Bash y MCP | C-09, B-13 |
| F-024 | HIGH | 00_Conocimiento es un canal entre clientes sin control determinista | B-11, C-09 |
| F-025 | MEDIUM | Skills de Oracle y paquete npx instalados sin versión fija | C-10 |
| F-026 | HIGH | Los prompts v0.4 del Orquestador y los cinco agentes no existen | C-11 |
| F-027 | HIGH | Los prompts v0.3 contradicen estados, contrato y escritor único de v0.4 | C-12 |
| F-028 | HIGH | Ningún prompt trata el contenido externo como dato | C-09 |
| F-029 | HIGH | La verificación de entorno lee la URL de un servidor fijo | B-07 |
| F-030 | MEDIUM | impacto_fiscal se define como sí o no y la plantilla usa booleano | C-12 |
| F-031 | MEDIUM | La sesión principal acumula el historial de todo el ticket | C-13 |
| F-032 | LOW | Cada delegación relee ticket, perfil, inventario y skills completos | A-09 |
| F-033 | MEDIUM | Métricas sin campos: el flujo no se puede medir completo | C-14 |
| F-034 | MEDIUM | Los controles técnicos dependen de supuestos de Claude Code sin verificar | C-15 |
| F-035 | LOW | Tres prompts con skills no indican invocarlas si falla la precarga | C-12 |
| F-036 | LOW | El límite de consultas del Verificador solo existe en el prompt | C-02 |

#### Análisis FODA

| Observación v0.2 | Texto | Preguntas |
|---|---|---|
| `strengths[0]` | Supervisor con máquina de estados persistida en archivos: el flujo se retoma en otra sesión o en otro equipo leyendo el ticket. | A-10 |
| `strengths[1]` | Agrupación de agentes por permisos y confianza de la entrada: el agente que lee texto del cliente no tiene Bash ni MCP. | A-11 |
| `strengths[2]` | El control principal en NetSuite es un rol de solo lectura con SuiteQL; ningún agente escribe en la cuenta ni despliega por diseño. | B-08, A-11 |
| `strengths[3]` | Diferencias del inventario con script determinista, aplicación solo con aprobación y falla cerrada; probado con 4 casos. | C-16 |
| `strengths[4]` | Ciclos acotados con escalamiento, y escalamiento inmediato ante un rechazo que repite el motivo anterior. | C-17 |
| `strengths[5]` | El prompt del Tester fija entorno, máximo de consultas, filtros en producción, aprobación previa y manejo de 403. | C-18 |
| `strengths[6]` | El prompt fiscal prohíbe inventar fundamentos, exige versión del catálogo y fija el orden SAT antes que PAC. | C-19 |
| `strengths[7]` | Registro explícito de decisiones, supuestos e inconsistencias con marcas de evidencia. | C-20 |
| `weaknesses[0]` | La entrada no confiable llega a la sesión y a los agentes con más privilegios. | A-02, B-13, C-01, C-09 (vía F-001, F-023, F-028) |
| `weaknesses[1]` | Los controles críticos son texto de prompt: Bash, producción, escritor único y transiciones. | A-01, A-04, A-05, B-04, B-05, B-07, C-02, C-15 (vía F-008, F-010, F-014, F-018, F-034, F-036) |
| `weaknesses[2]` | La tabla de transiciones es incompleta y contradictoria, y faltan contadores y reglas de reinicio. | B-01, B-02, B-03, B-06, B-14 (vía F-002, F-003, F-004, F-006, F-007) |
| `weaknesses[3]` | El aislamiento entre clientes depende de rutas, no de permisos. | B-11, C-04, C-09 (vía F-013, F-016, F-024) |
| `weaknesses[4]` | Tareas deterministas asignadas al modelo: folio, inventario y validación XSD. | C-06, C-07 (vía F-019, F-020, F-022) |
| `weaknesses[5]` | No existen prompts v0.4 y la base v0.3 es incoherente con el diseño. | B-07, C-11, C-12 (vía F-026, F-027, F-029, F-030, F-035) |
| `weaknesses[6]` | Despliegue y ramas sin política; la negación del despliegue no está probada. | A-04, B-08, B-09 (vía F-005, F-009) |
| `weaknesses[7]` | Pausas de aprobación y reintentos sin estado ni idempotencia; métricas sin campos para medir el flujo. | C-05, C-08, C-14 (vía F-015, F-021, F-033) |
| `opportunities[0]` | Script validar-ticket.mjs que aplique la tabla de transiciones, los contadores y el contrato. | A-05 |
| `opportunities[1]` | Hook de rutas que limite la escritura a la carpeta del cliente del repo actual. | C-04 |
| `opportunities[2]` | Validar XML contra XSD con el JDK que ya exige SuiteCloud CLI, o con xmllint. | C-07 |
| `opportunities[3]` | Ejecutar el levantamiento mientras el ticket espera la confirmación del cliente. | Sin pregunta: mejora de eficiencia sin riesgo asociado |
| `opportunities[4]` | Contrato de salida validado por script contra un esquema. | A-03 |
| `opportunities[5]` | Una sesión por etapa del ticket, apoyada en el estado persistido. | C-13 |
| `opportunities[6]` | Consulta de control posterior al despliegue en producción. | C-14 |
| `opportunities[7]` | Escáner determinista de anonimización con nombres del perfil, patrón de RFC y prefijos de IDs. | B-11 |
| `threats[0]` | Capturas, XML de PAC o correos reenviados con instrucciones incrustadas. | C-01, C-09 |
| `threats[1]` | Cambios de Claude Code en subagentes, precarga de skills, reglas deny o hooks. | C-15 |
| `threats[2]` | Cambios aguas arriba en las skills de Oracle. | C-10 |
| `threats[3]` | Conector actual apuntando a producción. | B-07, C-15 |
| `threats[4]` | Refresh de Sandbox que cambia rol, integración o URL. | C-15 |
| `threats[5]` | Más clientes en una bóveda con acceso global. | C-04 |
| `threats[6]` | Límites sin calibrar que bloquean tickets sanos o consumen tiempo. | C-14 |
| `threats[7]` | Rol de solo lectura con permisos amplios de personalización. | C-03 |

#### Recomendaciones de prompts

| Observación v0.2 | Agente | Problema detectado | Preguntas |
|---|---|---|---|
| `prompt_engineering_recommendations[0]` | Orquestador | No existe prompt del Orquestador; la tabla, los contadores y el contrato dependen del criterio del modelo. | A-01, A-05, B-01, B-02, C-11 (vía F-026, F-018, F-002, F-003) |
| `prompt_engineering_recommendations[1]` | agents/documentador.md | El documentador cambia estado y fechas del frontmatter y responde en texto libre. | B-04, B-05, C-12 (vía F-027, F-014) |
| `prompt_engineering_recommendations[2]` | agents/ingesta.md | El prompt que recibe el texto menos confiable no lo delimita ni indica qué hacer con instrucciones incrustadas. | A-02, B-13, C-01, C-09 (vía F-028, F-001, F-023) |
| `prompt_engineering_recommendations[3]` | agents/tester.md | La comprobación de entorno usa la URL del servidor netsuite aunque la herramienta invocada pertenezca a otro servidor. | B-07 (vía F-029, F-010) |
| `prompt_engineering_recommendations[4]` | agents/ingesta.md | Tipos de campo inconsistentes (sí o no frente a booleano) y valores de tipo sin ruta. | B-14, C-12 (vía F-030, F-004) |
| `prompt_engineering_recommendations[5]` | agents/tester.md | El plan de consultas aprobado vive solo en la respuesta y se regenera en la siguiente invocación. | C-05 (vía F-015) |
| `prompt_engineering_recommendations[6]` | agents/documentador.md | La anonimización depende de una revisión libre del modelo, y la Bóveda también recibe texto no confiable. | B-11, C-09 (vía F-024) |
| `prompt_engineering_recommendations[7]` | agents/coder.md | El Coder lee la ficha y la evidencia con Bash completo y sin instrucción de tratarlas como dato. | A-04, B-11, C-04, C-09 (vía F-028, F-008, F-016) |
| `prompt_engineering_recommendations[8]` | Debug | El Debug tiene MCP y Bash sin verificación de entorno, sin tope de consultas y sin prompt. | C-02, C-11 (vía F-011, F-026) |
| `prompt_engineering_recommendations[9]` | Debug | El método de cinco pasos de debug-netsuite no fija cuántas hipótesis abrir ni cómo descartarlas. | C-11 (vía F-026) |

#### Plan de remediación

| Paso | Prioridad | Acción | Preguntas |
|---|---|---|---|
| 1 | CRITICAL | Ingresar el mensaje del cliente como archivo en adjuntos y pasar solo su ruta. Restringir Bash y MCP del Orquestador con reglas de permisos y declarar el modo con aprobación por comando. | A-02, C-01 (vía F-001) |
| 2 | HIGH | Cerrar con el usuario la tabla de transiciones: en_diagnostico, confirmada→borrador, tipo_bloqueo, valores de tipo con ruta, contadores rondas_ficha y rondas_diagnostico y reglas de reinicio. | B-01, B-02, B-03, B-06, B-14 (vía F-002, F-003, F-004, F-006, F-007) |
| 3 | HIGH | Escribir validar-ticket.mjs, validar-contrato.mjs y registrar.mjs: transición legal, contadores, salida YAML, escritor único, huella de la Bitácora y clave de idempotencia por delegación. | A-01, A-05, B-04, B-05, C-08 (vía F-018, F-014, F-021) |
| 4 | HIGH | Sustituir la raíz de la bóveda en additionalDirectories por 00_Conocimiento y la carpeta del cliente en cada repo, o bloquear con hook las rutas de otros clientes. | B-11, C-04 (vía F-013, F-016) |
| 5 | HIGH | Escribir el hook de producción: resolver la URL del servidor de la herramienta, aceptar solo una confirmación fuera del alcance de escritura de los agentes y aplicarlo a Verificador y Debug. | B-07, C-02 (vía F-010, F-011, F-029) |
| 6 | HIGH | Convertir el alcance de Bash en reglas de permisos y probar la negación del despliegue con script propio, shell anidado, alias y npx. | A-04, B-08 (vía F-008, F-009) |
| 7 | HIGH | Agregar defensa de inyección y delimitación de contenido externo a todos los prompts; prohibir texto del cliente en el campo instrucciones. | B-13, C-09 (vía F-023, F-028) |
| 8 | HIGH | Crear un escáner determinista de anonimización y exigir revisión del usuario antes de publicar en 00_Conocimiento. | B-11, C-09 (vía F-024) |
| 9 | HIGH | Escribir los prompts v0.4 del Orquestador y de los cinco agentes con el contrato YAML, los tipos de la plantilla v0.4 y sin escritura de frontmatter. | C-11, C-12 (vía F-026, F-027, F-030) |
| 10 | HIGH | Persistir el plan de consultas del Verificador en el ticket y ejecutar solo consultas idénticas a las aprobadas. | C-05 (vía F-015) |
| 11 | HIGH | Agregar controles deterministas: conteo por entidad contra el JSON del inventario, validación XSD con el JDK o xmllint y búsqueda de catálogos SAT por script. | C-07 (vía F-019, F-020) |
| 12 | HIGH | Aprobar la política de ramas: main igual a producción, integración después de verificar, despliegue desde main con deploy.xml acotado y etiqueta por folio. | B-09 (vía F-005) |
| 13 | MEDIUM | Generar el folio con script y bloqueo de archivo; fijar un solo equipo activo por cliente cuando se use Syncthing. | A-08, C-06, C-07 (vía F-022, F-017) |
| 14 | MEDIUM | Declarar qué datos del cliente pueden enviarse al modelo y fijar la versión del paquete y el commit de las skills de Oracle. | C-03, C-10 (vía F-012, F-025) |
| 15 | MEDIUM | Abrir una sesión por etapa del ticket y pasar en el contrato las secciones que el agente debe leer. | A-09, C-13 (vía F-031, F-032) |
| 16 | MEDIUM | Agregar fecha_cierre y min_revision_doc a la plantilla del ticket y la ventana de 30 días a la consulta de la métrica 1 en Tablero.md. | C-14 (vía F-033) |
| 17 | MEDIUM | Validar en el equipo, antes del piloto, los supuestos S3, S8 a S10 y S13; S14 a S16 se validan con los criterios de aceptación de los pasos 4 a 6. | C-15 (vía F-034) |
| 18 | LOW | Agregar la instrucción de invocar skills a fiscal, levantamiento y documentador; registrar en el contrato las consultas ejecutadas y sumarlas por ticket. | C-02, C-12 (vía F-035, F-036) |

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
| C-01 | | | |
| C-02 | | | |
| C-03 | | | |
| C-04 | | | |
| C-05 | | | |
| C-06 | | | |
| C-07 | | | |
| C-08 | | | |
| C-09 | | | |
| C-10 | | | |
| C-11 | | | |
| C-12 | | | |
| C-13 | | | |
| C-14 | | | |
| C-15 | | | |
| C-16 | | | |
| C-17 | | | |
| C-18 | | | |
| C-19 | | | |
| C-20 | | | |

```json
{
  "cuestionario_version": "1.1",
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
| 1.1 | 2026-11-09 | 20 preguntas del dictamen v0.2 (bloque C): 15 de riesgos y 5 de fortalezas. Sin cambios en las preguntas de la versión 1.0. |
