# Crea tu propio ChatGPT — RAG desde cero

Curso práctico de 2 días para construir un RAG desde cero: tokens, chunking, overlap, embeddings,
similarity search, Top-K, contexto y generación con un LLM.

## Empieza aquí
1. Sigue `alumnos/01_instalacion_y_configuracion.md`: instalar Git → clonar este repositorio → instalar uv →
   `uv sync` → configurar `.env` → abrir en VS Code.
2. Corre `labs/00_setup_check.ipynb`: si todo termina en ✅, estás listo.
3. Lee `alumnos/02_material_del_alumno.md`: cuestionario de bienvenida, documentos de ejemplo, prompts y recursos.

## Contenido

| Ruta | Qué es |
|---|---|
| `alumnos/01_instalacion_y_configuracion.md` | Git, uv, `.env` (OpenAI u Ollama) y Jupyter en VS Code |
| `alumnos/02_material_del_alumno.md` | Material del alumno, documentos de ejemplo, prompts y recursos |
| `alumnos/03_labs_y_retos.md` | Mapa de labs y challenges, retos rápidos y reto final con rúbrica |
| `labs/` | Notebooks de ejercicios (celdas `# TODO` + autoverificación ✅) |
| `data/` | Documentos ficticios y preguntas de evaluación |
| `solutions/` | Aparece cuando el instructor publica las soluciones (`git pull`) |
| `pyproject.toml`, `uv.lock`, `.env.example` | Dependencias y configuración del proyecto |

Proveedor de LLM híbrido: OpenAI (API key) u Ollama (local), configurable en `.env` con `LLM_PROVEEDOR`.
