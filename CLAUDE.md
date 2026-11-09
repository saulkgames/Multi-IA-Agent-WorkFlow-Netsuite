# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Propósito

Flujo de trabajo aumentado con IA para trabajar y resolver tickets de proyectos o soluciones personalizadas en múltiples cuentas de Oracle NetSuite (fuente: `README.md`).

## Estado actual del repositorio

El repositorio solo contiene `README.md`. No existe todavía código, manifiesto de dependencias (`package.json`, `suitecloud.config.js`, `manifest.xml`), configuración de lint ni pruebas.

No hay comandos de build, lint o test definidos. No inventarlos: cuando se agregue el primer proyecto (por ejemplo, un proyecto SuiteCloud Development Framework), actualizar esta sección con los comandos reales que declare el repositorio.

## Arquitectura

Pendiente de definir. Inferencia a partir del README, no verificada en código: el alcance declarado implica dos dimensiones que la estructura deberá separar.

| Dimensión | Implicación |
|---|---|
| Multi-cuenta NetSuite | Cada cuenta tiene su propio ID, roles, campos personalizados y scripts. Credenciales y configuración por cuenta no deben compartirse ni versionarse. |
| Por ticket / solución | El trabajo se organiza por ticket o solución personalizada; cada uno debe ser rastreable a su cuenta destino. |

Cuando se defina la estructura de carpetas, documentar aquí cómo se mapea cuenta → proyecto → ticket y dónde vive la configuración por cuenta.

## Convenciones

Documentación y comunicación en español de México.
