# Material del alumno

## Antes del curso

1. **Responde el cuestionario de bienvenida:**
   https://docs.google.com/forms/d/1-Z_W0RfKiZoB4NFKvL0TJZuToNvXmoBh2qshJtWg_6U/edit
2. **Prepara tu entorno** siguiendo `alumnos/01_instalacion_y_configuracion.md` y corre `labs/00_setup_check.ipynb`.
3. **Ten a la mano tu celular** o una pestaña del navegador para los quizzes en vivo en **menti.com** (hay ranking 🏆).

## Proyecto
Construir un RAG que responda preguntas utilizando documentos propios.

## Pipeline
```text
Documento
↓
Chunking + Overlap
↓
Embeddings
↓
Vector Index
↓
Pregunta
↓
Embedding de pregunta
↓
Top-K
↓
Contexto
↓
LLM
↓
Respuesta
```

## Labs y challenges

| Momento | Notebook | Necesita LLM |
|---|---|---|
| Antes del curso | `labs/00_setup_check.ipynb` | Sí |
| LAB 01 — Observando tokens | `labs/lab01_tokens.ipynb` | No (sección opcional sí) |
| CHALLENGE 01 — Diseña tu chunking | `labs/challenge01_disena_tu_chunking.ipynb` | No |
| LAB 02 — Chunking desde cero | `labs/lab02_chunking.ipynb` | No |
| LAB 03 — Búsqueda semántica | `labs/lab03_busqueda_semantica.ipynb` | Sí |
| LAB 04 — RAG completo | `labs/lab04_rag_completo.ipynb` | Sí |
| CHALLENGE 02 — Rompe tu RAG | `labs/challenge02_rompe_tu_rag.ipynb` | Sí |

Las celdas con `# TODO` son las que debes completar. Las celdas **✅ Autoverificación** te dicen si tu solución es correcta.
El instructor publicará las soluciones en `solutions/` al terminar cada bloque: haz `git pull` para recibirlas (inténtalo primero 😉).

## Documentos de ejemplo

Elige el tema que más te guste; todos los notebooks tienen una celda `DOCUMENTO = "..."` para cambiarlo.
Todos son **ficticios** y fueron diseñados para el curso: tienen secciones, tablas, sinónimos y algunos datos
desactualizados a propósito para poner a prueba tu RAG.

| `DOCUMENTO` | Archivo | Tema | Ideal si te interesa… |
|---|---|---|---|
| `cafeteria` | `data/cafeteria_nebula_manual.md` | Manual y políticas de la cadena de cafeterías "Nébula" (horarios, recetas, lealtad, devoluciones, mantenimiento de máquinas) | atención a clientes, retail, operaciones |
| `streamly` | `data/streamly_api_docs.md` | Documentación de la API de la plataforma de streaming "Streamly" (autenticación, endpoints, límites, errores, planes) | desarrollo de software, documentación técnica, código |
| `veterinaria` | `data/clinica_veterinaria_huellitas.md` | Guía para clientes de la "Clínica Veterinaria Huellitas" (urgencias, vacunas, cirugías, precios) | salud, servicios, preguntas frecuentes |
| `mi_documento` | `data/mi_documento.md` | Tu propio documento (créalo con los prompts de abajo) | traer tu propio caso de uso |

Cada documento tiene un archivo `data/<tema>_preguntas.json` con preguntas de prueba, la respuesta esperada y la
**evidencia** (texto exacto) que el retrieval debe encontrar.

## Prompts para generar tu propio documento de prueba

Si quieres trabajar con un tema propio, usa ChatGPT (u otro LLM) con alguno de estos prompts, guarda el resultado como
`data/mi_documento.md` y cambia `DOCUMENTO = "mi_documento"` en el notebook.

> ⚠️ **No uses documentos reales confidenciales de tu empresa** si trabajas con una API externa. Pide siempre datos ficticios.

### Prompt 1 — Manual técnico (LAB 02, LAB 03, LAB 04)
```text
Genera en Markdown un manual de usuario ficticio de unos 1,500 palabras para [PRODUCTO, p. ej. "una bicicleta eléctrica"].
Requisitos:
- Usa encabezados ## y ### para 10 a 12 secciones (instalación, uso, mantenimiento, garantía, solución de problemas, etc.).
- Incluye al menos 2 tablas (especificaciones y calendario de mantenimiento).
- Incluye datos concretos (cifras, plazos, horarios) que permitan responder preguntas precisas.
- Usa a veces sinónimos distintos para el mismo concepto (p. ej. "batería" y "acumulador").
- Todos los datos deben ser inventados. Indica al inicio que es un documento ficticio.
```

### Prompt 2 — Políticas internas de una empresa (LAB 04)
```text
Escribe en Markdown un documento ficticio de políticas de Recursos Humanos para una empresa de software de 200 personas
(vacaciones, trabajo remoto, gastos de viaje, equipo de cómputo, capacitación, código de conducta).
Unas 1,500 palabras, con secciones ## y reglas concretas con números (días, montos, plazos).
Agrega al final una sección "Preguntas frecuentes" con 6 preguntas y respuestas.
Todos los datos deben ser inventados.
```

### Prompt 3 — Documentación de API con código (LAB 01 y LAB 03)
```text
Genera la documentación ficticia en Markdown de una API REST para [DOMINIO, p. ej. "reservas de restaurantes"].
Incluye: autenticación, 5 endpoints con ejemplos en curl y Python, tabla de códigos de error, límites de uso por plan,
paginación y una sección de versiones donde la versión anterior tenga límites diferentes a la actual.
Unas 1,300 palabras. Todo debe ser inventado.
```

### Prompt 4 — Texto bilingüe para comparar tokens (LAB 01)
```text
Escribe un mismo párrafo técnico de unas 120 palabras sobre [TEMA] en español, en inglés y en portugués,
y después un fragmento de código Python de 15 líneas que haga lo que describe el párrafo.
Sepáralos con encabezados.
```

### Prompt 5 — Documento con trampas para romper tu RAG (CHALLENGE 02)
```text
Escribe un documento ficticio en Markdown de unas 1,500 palabras con la base de conocimiento de soporte de [EMPRESA FICTICIA].
Incluye a propósito:
- Una sección "Avisos recientes" que contradiga un dato de otra sección (por ejemplo, un horario o un precio que cambió).
- Una tabla de precios vigente y un anexo con precios del año anterior marcado como "histórico".
- Dos secciones que hablen de temas parecidos con palabras distintas.
- Términos técnicos con sinónimos.
Después del documento, en una sección aparte, escribe 8 preguntas de prueba: 4 con respuesta directa, 2 que dependan del
dato contradictorio y 2 cuya respuesta NO esté en el documento. Para cada una da la respuesta esperada.
```

### Prompt 6 — Preguntas de evaluación para tu documento (CHALLENGE 02)
```text
Te voy a pasar un documento. Genera 8 preguntas de prueba en formato JSON con esta estructura:
[{"id": "doc-01", "tipo": "directa|sinonimo|conflicto|fuera", "pregunta": "...",
  "respuesta_esperada": "...", "evidencia": ["frase EXACTA copiada del documento"], "seccion_esperada": "..."}]
Reglas: 3 directas, 2 que usen sinónimos de las palabras del documento, 1 de conflicto si existe, 2 fuera del documento
(con "evidencia": []). La evidencia debe ser un fragmento corto (3 a 8 palabras) copiado tal cual del documento.

DOCUMENTO:
<pega aquí tu documento>
```
Guarda la respuesta como `data/mi_documento_preguntas.json` y, en la celda de configuración del notebook, cambia
`"mi_documento": ("mi_documento.md", None)` por `("mi_documento.md", "mi_documento_preguntas.json")`.

## Recursos útiles

| Recurso | Para qué sirve | Relación con el curso |
|---|---|---|
| [Ollama](https://ollama.com/) | Descargar y correr LLMs y modelos de embeddings en tu computadora | Local LLM vs API LLM; proveedor alternativo de los labs |
| [LM Studio](https://lmstudio.ai/) | Aplicación de escritorio para descargar y chatear con modelos locales | Experimentar con modelos abiertos sin programar |
| [DeepSeek Chat](https://chat.deepseek.com/) | Chat con los modelos de DeepSeek | Comparar respuestas entre distintos LLMs |
| [OpenAI API](https://openai.com/es-ES/index/openai-api/) | Plataforma para usar los modelos de OpenAI desde código | Proveedor de embeddings y chat en los labs |
| [YourGPT — LLM comparison & leaderboard](https://yourgpt.ai/tools/llm-comparison-and-leaderboard) | Comparar modelos (capacidades, ventana de contexto, precios) | Elegir modelo según contexto, costo y calidad |
| [OpenRouter Rankings](https://openrouter.ai/rankings) | Ver qué modelos se están usando más en aplicaciones reales | Panorama del ecosistema de modelos |
| [Can I Run AI?](https://www.canirun.ai/) | Estimar si tu hardware puede correr un modelo local | Decidir entre API y modelo local |

> Las capacidades, precios y rankings de los modelos cambian constantemente: consulta siempre la fuente oficial.

## Preguntas que debes poder contestar
1. ¿Qué es un token?
2. ¿Por qué importan los tokens?
3. ¿Qué es un chunk?
4. ¿Qué es overlap?
5. ¿Cómo elegir chunk size?
6. ¿Qué es un embedding?
7. ¿Qué mide cosine similarity?
8. ¿Qué es Top-K?
9. ¿RAG y fine-tuning son lo mismo?
10. ¿Por qué puede fallar el retrieval?
11. ¿Qué es grounding?
12. ¿Cómo evaluarías un RAG?

Regla: no existe un chunk size universalmente correcto. Se debe probar con preguntas reales.

## Cómo escribir buenas preguntas de prueba

- Escribe **al menos 5 preguntas con respuesta conocida** y anota en qué parte del documento está la respuesta.
- Incluye preguntas que usen **palabras distintas** a las del documento (sinónimos): ponen a prueba la búsqueda semántica.
- Incluye **al menos 1 pregunta cuya respuesta no esté** en el documento: tu RAG debe decir que no lo sabe.
- Si tu documento tiene datos que cambiaron con el tiempo, pregunta por ellos: ¿tu RAG elige el dato vigente?
- Evalúa primero el **retrieval** (¿el fragmento correcto está en el Top-K?) y después la **respuesta**.
