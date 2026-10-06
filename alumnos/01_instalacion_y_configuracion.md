# Preparación del entorno — Git, uv y Jupyter en VS Code

Sigue estos pasos **en orden y antes del Día 1**. Al final correrás `labs/00_setup_check.ipynb` para confirmar que todo funciona.

| Paso | Qué haces | Tiempo aprox. |
|---|---|---|
| 1 | Instalar Git | 5 min |
| 2 | Clonar el repositorio del curso | 2 min |
| 3 | Instalar uv | 2 min |
| 4 | Instalar dependencias con `uv sync` | 3–5 min |
| 5 | Configurar `.env` (OpenAI u Ollama) | 5 min |
| 6 | Abrir los notebooks en VS Code | 5 min |

---

## 1. Instalar Git

**Windows** (PowerShell):
```powershell
winget install --id Git.Git -e --source winget
```
O descarga el instalador desde https://git-scm.com/download/win (acepta las opciones por defecto).

**macOS:**
```bash
xcode-select --install
# o, si usas Homebrew:
brew install git
```

**Linux (Debian/Ubuntu):**
```bash
sudo apt update && sudo apt install git
```

Cierra y vuelve a abrir la terminal, y verifica:
```bash
git --version
```

Configura tu nombre y correo (solo la primera vez):
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@correo.com"
```

## 2. Clonar el repositorio del curso

Elige una carpeta de trabajo (por ejemplo `Documentos`) y clona:
```bash
cd ~/Documents
git clone https://github.com/sibokdev/my_own_chatgpt_rag.git
cd my_own_chatgpt_rag
```

> Repositorio del curso: https://github.com/sibokdev/my_own_chatgpt_rag

Durante el curso el instructor puede publicar actualizaciones (por ejemplo, las soluciones). Para descargarlas:
```bash
git pull
```

Si modificaste notebooks y `git pull` marca conflicto, guarda tus cambios con otro nombre de archivo (por ejemplo `lab02_chunking_mio.ipynb`) y ejecuta `git checkout -- .` antes de volver a hacer `git pull`. **Cuidado:** ese comando descarta tus cambios en los archivos originales.

## 3. Instalar uv

uv es el gestor de Python y dependencias que usaremos. Instala también la versión de Python correcta, así que **no necesitas instalar Python por separado**.

Windows PowerShell:
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

macOS/Linux:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Cierra y vuelve a abrir la terminal, y verifica:
```bash
uv --version
```

## 4. Instalar las dependencias del curso

Dentro de la carpeta del repositorio (donde está `pyproject.toml`):
```bash
uv sync
```

Esto crea un entorno virtual en `.venv/` con Jupyter, numpy, pandas, scikit-learn, tiktoken, openai, matplotlib y python-dotenv.

## 5. Configurar el proveedor de LLM (`.env`)

Copia el archivo de ejemplo:
```bash
# Windows PowerShell
Copy-Item .env.example .env
# macOS/Linux
cp .env.example .env
```

Abre `.env` y elige **una** opción:

**Opción A — OpenAI (API):**
```env
LLM_PROVEEDOR=openai
OPENAI_API_KEY=sk-...tu_api_key...
```

**Opción B — Ollama (local, sin costo por uso):**
1. Instala Ollama desde https://ollama.com/
2. Descarga los modelos (requiere varios GB de espacio):
   ```bash
   ollama pull llama3.2
   ollama pull nomic-embed-text
   ```
3. En `.env`:
   ```env
   LLM_PROVEEDOR=ollama
   ```

> Los LAB 01 y LAB 02 y el CHALLENGE 01 **no necesitan** proveedor. A partir del LAB 03 sí.
> Para saber si tu equipo puede correr un modelo local, consulta https://www.canirun.ai/

**Nunca subas `.env` a Git** (ya está en `.gitignore`). Tu API key es como una contraseña.

## 6. Correr los notebooks en VS Code

1. Instala [Visual Studio Code](https://code.visualstudio.com/).
2. Instala las extensiones **Python** y **Jupyter** (ambas de Microsoft) desde la pestaña de Extensiones (`Ctrl+Shift+X`).
   O desde la terminal:
   ```bash
   code --install-extension ms-python.python
   code --install-extension ms-toolsai.jupyter
   ```
   Después recarga VS Code: `Ctrl+Shift+P` → **Developer: Reload Window**.
3. Abre la carpeta del repositorio: **File → Open Folder…** (o desde la terminal: `code .`).
4. Abre `labs/00_setup_check.ipynb`.
5. Arriba a la derecha haz clic en **Select Kernel → Python Environments…** y elige el entorno **`.venv`** del proyecto
   (aparece como `.venv (Python 3.x)` con la ruta de la carpeta del repo).
6. Ejecuta las celdas con `Shift+Enter` o con **Run All**. Si todas terminan en ✅, estás listo.

### Alternativa: Jupyter en el navegador

```bash
uv run jupyter lab
```
o
```bash
uv run jupyter notebook
```

### Alternativa: ejecutar un notebook solo desde la terminal

Ejecuta todas las celdas y guarda los resultados en el mismo archivo, sin abrir editor ni navegador:
```bash
uv run jupyter nbconvert --to notebook --execute --inplace labs/00_setup_check.ipynb
```
Sirve para el setup check o para probar un notebook completo. Para resolver los labs (celdas `# TODO`) necesitas un editor interactivo: VS Code o Jupyter en el navegador.

### Alternativa: registrar el kernel con nombre propio

Útil si VS Code no detecta `.venv`:
```bash
uv run python -m ipykernel install --user --name rag-course --display-name "Python (RAG Course)"
```
Después, en **Select Kernel → Jupyter Kernel…** elige **Python (RAG Course)**.

## Estructura del repositorio

```text
README.md      ← empieza aquí
alumnos/       ← guías del curso (esta guía, material del alumno, labs y retos)
labs/          ← notebooks con ejercicios (TODO) que resolverás en clase
data/          ← documentos de ejemplo y preguntas de evaluación
solutions/     ← aparece cuando el instructor publica las soluciones (al final de cada bloque)
```

## Problemas comunes

| Problema | Solución |
|---|---|
| `git` o `uv` "no se reconoce como comando" | Cierra y vuelve a abrir la terminal (y VS Code). Si persiste, reinicia sesión. |
| PowerShell bloquea el script de instalación | Usa exactamente el comando con `-ExecutionPolicy ByPass` del paso 3. |
| VS Code no muestra `.venv` en Select Kernel | Ejecuta `uv sync`, luego `Ctrl+Shift+P` → **Developer: Reload Window**. O usa la alternativa de registrar el kernel. |
| `ModuleNotFoundError` en el notebook | El kernel seleccionado no es el `.venv` del proyecto. Cámbialo en **Select Kernel**. |
| `Falta OPENAI_API_KEY` | Revisa que el archivo se llame exactamente `.env` (no `.env.txt`) y esté en la raíz del repo. Reinicia el kernel. |
| Error de conexión con Ollama | Verifica que Ollama esté abierto (`ollama list` en la terminal) y que descargaste ambos modelos. |
| Red corporativa / proxy bloquea descargas | Configura `HTTP_PROXY` y `HTTPS_PROXY` o usa otra red para instalar. |

## Comandos útiles de uv

```bash
uv run python script.py      # ejecutar un script con el entorno del proyecto
uv add <paquete>             # agregar una dependencia
uv sync                      # sincronizar el entorno con pyproject.toml / uv.lock
uv lock                      # regenerar el lockfile
```

### Opcional: empezar un proyecto propio desde cero

```bash
uv init mi-rag
cd mi-rag
uv add jupyter ipykernel openai numpy pandas scikit-learn tiktoken python-dotenv matplotlib
```
