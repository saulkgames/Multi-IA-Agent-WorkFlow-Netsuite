# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Propósito

Flujo de trabajo aumentado con IA para trabajar y resolver tickets de proyectos o soluciones personalizadas en múltiples cuentas de Oracle NetSuite (fuente: `README.md`).

## Estado actual del repositorio

El repositorio contiene solo documentos de diseño. No hay código, scripts, plantillas, skills ni definiciones de agentes; tampoco comandos de build, lint o test. No inventarlos: cuando se agregue el kit (agentes, skills, scripts `.mjs`, proyecto SDF), documentar aquí los comandos reales.

| Archivo | Contenido |
|---|---|
| `Arquitectura multiagente – Flujo de desarrollo NetSuite v0.4.md` | Diseño vigente. Fuente de verdad de la arquitectura. Parte de un kit v0.3 que no está en este repo. |
| `Auditoria de Diseno de Sistema.json` | Auditoría externa del diseño v0.4: FODA, recomendaciones de prompts y plan de remediación. No aprobado, calificación 6.5. Sus recomendaciones no están validadas contra el diseño. |

Los nombres de archivo llevan espacios y una raya larga (`–`); citarlos entre comillas en la terminal.

## Arquitectura (según diseño v0.4)

Supervisor jerárquico que ejecuta una máquina de estados persistida en el archivo `.md` de cada ticket.

| Pieza | Definición |
|---|---|
| Orquestador | La sesión principal de Claude Code. Único escritor de `estado` y de los contadores (`iteracion`, `rondas_plan`). No diseña, no programa, no consulta NetSuite. |
| Especialistas | Bóveda (ingesta y cierre), Arquitecto, Coder, Verificador NetSuite, Debug. Agrupados por permisos y por confianza de la entrada, no por tema. |
| Comunicación | Ningún subagente habla con otro. Todo pasa por el orquestador y por secciones del ticket. Contrato de entrada y salida en YAML (sección 3 del diseño). |
| Estado | Frontmatter del ticket, `perfil.md` por cliente, inventario del ambiente y Git (rama `t-<folio>`, folio en cada commit). Una variable, un escritor. |
| Acceso a NetSuite | Un puerto con tres adaptadores según `conexion` del perfil: `mcp`, `sdf`, `manual`. |
| Puertas humanas | Solo el usuario aprueba y solo el usuario despliega. Ningún agente ejecuta `suitecloud project:deploy`. |
| Fiscal | `fiscal-cfdi` es la única fuente de verdad fiscal (modos diseño, diagnóstico, validación). `impacto_fiscal` solo puede pasar de `false` a `true`. |

Al modificar el diseño, mantener coherentes entre sí la tabla de transiciones (sección 3), el diagrama de estados (sección 5), la matriz de herramientas (sección 2) y la tabla de variables (sección 4). Hoy tienen discrepancias; por ejemplo, `en_diagnostico` existe en el diagrama y no en la tabla.

## Convenciones

Documentación y comunicación en español de México. Los valores sin medición se marcan como "supuesto", igual que en el diseño.
