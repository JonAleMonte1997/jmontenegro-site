---
title: "Cómo armar prompts efectivos"
description: "Guía práctica de prompt engineering: estructuras, patrones y ejemplos concretos para obtener mejores resultados con modelos de lenguaje."
date: 2026-04-17
draft: false
---

## ¿Qué es un prompt?

Un prompt es la instrucción que le das a un modelo de lenguaje. La calidad del resultado depende directamente de cómo estructures esa instrucción.

## Principios básicos

**1. Sé específico sobre el formato de salida**

En lugar de: "explicame qué es una API REST"
Mejor: "Explicame qué es una API REST en 3 bullets, en español, para alguien que ya sabe programar en Java"

**2. Dá contexto de rol**

"Sos un desarrollador senior revisando este código. ¿Qué cambiarías y por qué?"

**3. Usá ejemplos (few-shot prompting)**

Mostrarle al modelo 1-2 ejemplos del output que esperás antes de pedirle que genere uno nuevo mejora drásticamente la calidad.

## Patrones útiles

- **Chain of thought:** pedirle que piense paso a paso antes de responder
- **Role prompting:** asignarle un rol específico
- **Output format:** especificar el formato exacto de la respuesta (JSON, lista, párrafo)
