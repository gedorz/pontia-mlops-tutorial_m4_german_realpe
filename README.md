# Simple ML Training Project
This project trains a RandomForest model on tabular data.
test

# Servicios en ejecución
    ✅ Data loader (local data) - `para entrenamiento 
    ✅ unit test (MLflow) - Conectado exitosamente a HTTP://57.151.65.76:5000

# ambiente de python
# Is done: Descripción explicativa de la actividad entregada
## Creación de un entorno virtual en Python 

### 1. Is done: Crear entorno virtual
    Se crea un entorno virtual de Python para la creación de la API de FastAPI
    y su base de datos mediante la postgres
    Se hizo mediante los siguientes comandos.
```bash
    # Windows
    python -m venv .venv
    .venv\Scripts\activate

    # Linux/Mac
    python -m venv .venv
    source .venv/bin/activate
```
### 2. Is done:  Instalar dependencias
    Mediante el archivo de  requirements.txt
    se realizar la inclusión de los requerimientos de la aplicación.
    Esto se realiza con el siguiente comando

```bash
    pip install -r requirements.txt
```

### 3. Is done:  Instalar integration yml
    Se agrega las pipelines para automatismos al hacer pull_request
    tambien se activa la opcion de ejecucion manual de pipelines
    para esto se crea el archivo  .github\workflows\integration.yml


### 4. Is done:  Conección con render Crear un nuevo Web Service
    a.	Seleccionar repositorio
    b.	Name: (pontia-mlops-tutorial_m4_german_realpe)
    c.	Language/Runtime: `Python`
    d.	Branch: ‘main’
    e.	Region: ‘Frankfurt (EU Central)
    f.	Root Directory: ‘deployment’
    g.	Build Command: `deployment/ $ pip install -r requirements.txt`
    h.	Start Command: `deployment/ $ uvicorn app.main:app --host 0.0.0.0 --port 8080`
    i.	Instance Type: `Free`
    j.	Environment Variables:
    GITHUB_REPO: “gedorz/pontia-mlops-tutorial_m4_german_realpe”

### 4. Is done: deployment and validation
    1. Se agrega los model_test al repositorio
    2. Se configura el build.yml con: Upload model to GitHub Release
    3. Se agrega la data de entrenamiento:  Download adult dataset 
    4. Se suprime: Register model in MLflow
    5. se crea un deployment and validation en github 
