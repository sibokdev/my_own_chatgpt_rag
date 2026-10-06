# Labs y retos

Los ejercicios están en notebooks dentro de `labs/`: completa las celdas `# TODO` y comprueba tu trabajo con las celdas
de autoverificación ✅.

| Orden | Slide | Notebook | Contenido | Proveedor LLM |
|---|---|---|---|---|
| 0 | 3–4 | `labs/00_setup_check.ipynb` | Verifica Python, paquetes, datos y proveedor | Sí |
| 1 | 14–15 | `labs/lab01_tokens.ipynb` | Contar/visualizar tokens, español vs inglés vs código, tokens → costo | No (opcional sí) |
| 2 | 26–27 | `labs/challenge01_disena_tu_chunking.ipynb` | Radiografía del documento, vista previa de estrategias, ficha de diseño | No |
| 3 | 34–35 | `labs/lab02_chunking.ipynb` | `chunk_text`, estimación, experimento tamaño/overlap, chunking por párrafos y secciones | No |
| 4 | 41–42 | `labs/lab03_busqueda_semantica.ipynb` | Búsqueda literal y TF-IDF vs embeddings, `cosine`, `top_k`, elección de K, PCA 2D | Sí |
| 5 | 55–56 | `labs/lab04_rag_completo.ipynb` | Vector store con metadata, context assembly, grounding, citas, filtros, chat | Sí |
| 6 | 57–58 | `labs/challenge02_rompe_tu_rag.ipynb` | Pipeline parametrizable, hit@k, experimentos extremos, bitácora de fallas | Sí |

> En la presentación, algunas portadas usan otra numeración: "LAB 01: Chunking desde cero" corresponde al **LAB 02**
> y "LAB 02: Construye tu búsqueda semántica" al **LAB 03**. Guíate por el slide de instrucciones que sigue a cada
> portada: indica el notebook exacto que debes abrir.

Documentos de trabajo (ficticios, elige uno en la celda `DOCUMENTO` de cada notebook): `data/cafeteria_nebula_manual.md`, `data/streamly_api_docs.md`,
`data/clinica_veterinaria_huellitas.md`, o el tuyo en `data/mi_documento.md`.
Cada documento incluido tiene preguntas de evaluación en `data/<tema>_preguntas.json`.

## ¿Cuándo terminaste?

| Notebook | Terminado cuando… |
|---|---|
| LAB 01 | Todas las ✅ pasan y puedes explicar por qué enviar el documento completo cuesta más que 3 chunks |
| CHALLENGE 01 | La ficha de diseño está llena con justificación y al menos 2 riesgos |
| LAB 02 | Todas las ✅ pasan y elegiste una estrategia con base en la comparación de la sección 6 |
| LAB 03 | Todas las ✅ pasan y la tabla "literal vs semántica" muestra al menos un caso donde la semántica acierta y la literal no |
| LAB 04 | El RAG responde con citas `[id]` y admite no saber ante una pregunta fuera del documento |
| CHALLENGE 02 | Tabla de experimentos con al menos 6 configuraciones y bitácora con al menos 4 fallas diagnosticadas |

## Retos rápidos (sin notebook, para repaso o tarea)

### Reto 1 — Tokens
Tokeniza:
"La inteligencia artificial generativa puede crear texto, código e imágenes."

Compara cantidad de tokens y palabras.

### Reto 2 — Chunking
Usa un documento de 2,000+ caracteres.
Prueba chunk sizes 250, 500 y 1000.
Prueba overlap 0, 50 y 100.

Analiza número de chunks y continuidad.

### Reto 3 — Retrieval
Crea 10 chunks y 5 preguntas. Recupera Top-3 y decide si realmente contienen la respuesta.

### Reto 4 — K
Compara K=1, K=3 y K=5. Analiza relevancia, ruido y contexto.

### Reto 5 — Pregunta fuera del conocimiento
Pregunta algo que no exista en los documentos. Diseña el prompt para que el modelo diga que no hay información suficiente.

## Reto final (Día 2, en equipos)

Construyan un RAG sobre `data/mi_documento.md` (generado con los prompts de `alumnos/02_material_del_alumno.md`)
o sobre uno de los tres documentos del curso, y presenten una demo de 5 minutos.

Deben presentar:
- documento(s) usados y por qué
- estrategia de chunking, chunk size y overlap
- proveedor y modelo de embeddings
- K elegido
- hit@k con al menos 6 preguntas de prueba (incluida 1 fuera del documento)
- una pregunta en vivo: chunks recuperados → contexto → respuesta con citas
- una falla que encontraron y qué componente la causaba
- justificación de los parámetros

### Rúbrica (20 puntos)

| Criterio | 0 | 2 | 4 |
|---|---|---|---|
| **Pipeline funcional** | No responde | Responde, sin citas | Responde con citas `[id]` a los fragmentos |
| **Chunking justificado** | Parámetros por defecto sin explicación | Explicación general | Decisión ligada a la estructura del documento y comparada con otra opción |
| **Evaluación del retrieval** | No midió | Revisó resultados a ojo | Reporta hit@k con preguntas de prueba |
| **Grounding** | Inventa ante preguntas fuera del documento | A veces admite no saber | Admite no saber y maneja datos contradictorios |
| **Diagnóstico** | No identifica fallas | Identifica una falla | Identifica la falla y el componente responsable, y propone un arreglo |

## Soluciones

El instructor publica las soluciones al terminar cada bloque. Para recibirlas:
```bash
git pull
```
Aparecerán en la carpeta `solutions/` con el mismo nombre del notebook y el sufijo `_solucion`
(por ejemplo `solutions/lab01_tokens_solucion.ipynb`). Intenta resolver cada ejercicio antes de verlas.
