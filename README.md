# 🤖 Nubia LinkedIn Agent

> **Agente automatizado que convierte noticias reales de tecnología en posts de LinkedIn** con la voz de marca de Nubia (IA y automatización para pymes).
>
> Diseñado e implementado como **caso práctico del Máster de Hiperautomatización en EBIS**.
>
> ---
>
> ## 🚀 ¿Qué hace este agente?
>
> El flujo recorre un ciclo completamente automatizado desde la captación de noticias hasta la publicación en LinkedIn, con aprobación humana intermedia:
>
> 1. **Agrega noticias** de 4 fuentes RSS en español: Hipertextual, Xataka, Genbeta y MuyPymes.
> 2. 2. **Un agente de IA (GPT)** selecciona UNA noticia relevante para pymes, comprueba que no sea un tema ya publicado y redacta el post + el prompt de imagen, todo en JSON estructurado.
>    3. 3. **Genera una ilustración** con GPT-IMAGE basada en ese prompt dinámico.
>       4. 4. **Aprobación humana por Telegram** (sendAndWait) antes de publicar nada.
>          5. 5. **Publica texto + imagen en LinkedIn** y registra todo en Google Sheets para evitar duplicados futuros.
>            
>             6. ---
>            
>             7. ## 🏗️ Arquitectura del flujo
>            
>             8. ```
> RSS Feeds (4 fuentes)
>         │
>         ▼
>   Merge + Normalización
>         │
>         ▼
>   Agente GPT (selección + redacción)
>     ├── Comprueba duplicados (Google Sheets)
>     ├── Genera post en voz de marca Nubia
>     └── Genera prompt de imagen
>         │
>         ▼
>   GPT-IMAGE → Ilustración
>         │
>         ▼
>   Telegram sendAndWait ← Aprobación humana
>         │
>    ┌────┴────┐
>    ▼         ▼
> Aprobar   Rechazar
>    │
>    ▼
> LinkedIn API (texto + imagen)
>    │
>    ▼
> Google Sheets (registro + deduplicación)
> ```
>
> ---
>
> ## ⚙️ Decisiones técnicas clave
>
> ### 🔍 Deduplicación semántica
> No se compara por coincidencia literal de titulares, sino por **tema, keywords y resumen**. El agente evalúa si el tema ya fue cubierto aunque el artículo sea diferente.
>
> ### 📦 JSON forzado en la salida del agente
> La salida del nodo agente siempre produce un JSON estructurado con campos definidos (`post_text`, `image_prompt`, `topic`, `keywords`, `summary`). Un **nodo Code parser con fallback** garantiza que nunca haya fallos silenciosos si el modelo desvía el formato.
>
> ### 🔗 Merge por posición para re-adjuntar el binario
> Durante la pausa de aprobación humana (Telegram `sendAndWait`), el binario de la imagen generada se pierde del flujo. Se resuelve con un **nodo Merge por posición** que re-adjunta el binario cuando se reanuda la ejecución.
>
> ### 🛡️ Gestión de errores en 3 capas
> - **Capa 1 — Tolerancia a caída de feeds**: si una fuente RSS falla, el flujo continúa con las fuentes disponibles.
> - **Capa 2 — Validación de la salida del agente**: el parser detecta respuestas malformadas y reintenta o notifica.
> - **Capa 3 — Notificaciones de error por Telegram**: cualquier fallo no controlado dispara un mensaje de alerta al operador.
>
> ---
>
> ## 🛠️ Stack tecnológico
>
> | Componente | Tecnología |
> |---|---|
> | Orquestación | n8n (self-hosted en Docker) |
> | LLM (selección + redacción) | OpenAI GPT-4o |
> | Generación de imágenes | OpenAI GPT-IMAGE (dall-e-3) |
> | Aprobación humana | Telegram Bot (sendAndWait) |
> | Publicación | LinkedIn API |
> | Registro y deduplicación | Google Sheets |
> | Fuentes de contenido | RSS (Hipertextual, Xataka, Genbeta, MuyPymes) |
>
> ---
>
> ## 📋 Fuentes RSS monitorizadas
>
> - 🗞️ [Hipertextual](https://hipertextual.com) — tecnología en español
> - 🗞️ [Xataka](https://www.xataka.com) — tecnología y gadgets
> - 🗞️ [Genbeta](https://www.genbeta.com) — software y productividad
> - 🗞️ [MuyPymes](https://www.muypymes.com) — tecnología para empresas
>
> ---
>
> ## 🎓 Contexto académico
>
> Este proyecto es el **caso práctico** desarrollado durante el **Máster de Hiperautomatización de EBIS Business School**.
>
> El objetivo del caso era demostrar cómo un flujo de hiperautomatización puede:
> - Eliminar trabajo manual repetitivo (monitorización de noticias + redacción de contenido)
> - Mantener la voz de marca consistente mediante prompting estructurado
> - Incorporar supervisión humana en el punto crítico (antes de publicar)
> - Escalar sin coste adicional de personal
>
> ---
>
> ## 📁 Estructura del repositorio
>
> ```
> nubia-linkedin-agent/
> ├── README.md                  # Este archivo
> ├── workflow/
> │   └── nubia_linkedin_agent.json   # Flujo n8n exportado
> ├── docs/
> │   ├── arquitectura.md        # Diagrama detallado del flujo
> │   └── prompts.md             # Prompts del agente GPT
> └── assets/
>     └── ejemplo_post.png       # Ejemplo de post generado
> ```
>
> ---
>
> ## 🚦 Estado del proyecto
>
> - [x] Flujo base funcional
> - [x] Deduplicación semántica implementada
> - [x] Gestión de errores en 3 capas
> - [x] Aprobación humana por Telegram
> - [x] Publicación en LinkedIn con imagen
> - [ ] Dashboard de métricas en Google Sheets
> - [ ] Soporte multi-idioma
>
> ---
>
> ## 👤 Autor
>
> **Nubia** — IA y automatización para pymes
> Caso práctico · Máster de Hiperautomatización · EBIS Business School
