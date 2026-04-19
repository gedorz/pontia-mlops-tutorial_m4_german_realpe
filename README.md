# PontIA MLOps Tutorial - Equipo M4

**Integrantes del Proyecto:**
- Diego Gil Sánchez
- German Dario Realpe

Este repositorio es un tutorial completo de **MLOps (Machine Learning Operations)** que implementa el ciclo de vida completo de un modelo de machine learning: desde la integración (CI), construcción (Build), hasta el despliegue en producción (Deploy).

## Funcionalidad General

El proyecto implementa un pipeline de ML que incluye:
- **Carga y preprocesamiento de datos**: Limpieza, encoding de variables categóricas y escalado de features.
- **Entrenamiento del modelo**: Un clasificador RandomForest optimizado.
- **Evaluación**: Métricas de accuracy y reporte de clasificación.
- **Registro y versionado**: Uso de MLflow para tracking y registro de modelos.
- **Despliegue**: API REST con FastAPI desplegada en Render, que descarga automáticamente la última versión del modelo desde GitHub Releases.

## Estructura de Directorios

```
├── .github/
│   └── workflows/
│       └── deploy.yml          # Workflow de GitHub Actions para despliegue en Render
├── data/
│   ├── raw/                    # Datos crudos del dataset Adult Income
│   └── deployment/
│       └── requirements.txt    # Dependencias específicas para el despliegue
├── deployment/
│   └── app/
│       ├── __init__.py
│       └── main.py             # Aplicación FastAPI para servir predicciones
├── model_tests/                # Tests de integración del modelo
├── models/                     # Directorio para guardar modelos entrenados localmente
├── scripts/
│   └── register_model.py       # Script para registrar el modelo en MLflow
├── src/                        # Código fuente principal
│   ├── __init__.py
│   ├── data_loader.py          # Funciones para cargar y preprocesar datos
│   ├── evaluate.py             # Funciones de evaluación del modelo
│   ├── main.py                 # Script principal para entrenamiento
│   └── model.py                # Definición y entrenamiento del modelo
├── unit_tests/                 # Tests unitarios
├── pytest.ini                  # Configuración de pytest
├── render.yml                  # Configuración de despliegue para Render
├── requirements.txt            # Dependencias del proyecto
└── README.md                   # Este archivo
```

### Descripción de Componentes Principales

- **`.github/workflows/deploy.yml`**: Automatiza el despliegue en Render mediante un webhook cuando se dispara manualmente.
- **`data/raw/`**: Contiene los archivos `adult.data` y `adult.test` del dataset Adult Income.
- **`deployment/app/main.py`**: API FastAPI que:
  - Descarga el modelo desde GitHub Releases al iniciar.
  - Expone endpoints para predicciones (`/predict`) y health check (`/health`).
  - Maneja métricas básicas de uso.
- **`scripts/register_model.py`**: Registra el modelo entrenado en MLflow, lo transita a "Staging" y lo marca como "champion".
- **`src/`**:
  - `data_loader.py`: Carga datos CSV, maneja valores faltantes, aplica label encoding y scaling.
  - `evaluate.py`: Calcula accuracy y genera reporte de clasificación.
  - `main.py`: Orquesta el entrenamiento completo, logging y guardado de artifacts.
  - `model.py`: Define y entrena el RandomForestClassifier.
- **`unit_tests/` y `model_tests/`**: Suites de tests para validar funcionalidad y rendimiento.

## Cómo Poner en Marcha el Proyecto

### Prerrequisitos

- Python 3.10+
- Git
- Cuenta en GitHub (para releases)
- Cuenta en Render (opcional para despliegue)
- MLflow server local (opcional para registro avanzado)

### Instalación y Configuración

1. **Clona el repositorio**:
   ```bash
   git clone https://github.com/gedorz/pontia-mlops-tutorial_m4_german_realpe.git
   cd pontia-mlops-tutorial_m4_german_realpe
   ```

2. **Instala las dependencias**:
   ```bash
   pip install -r requirements.txt
   ```

3. **Descarga los datos** (si no están incluidos):
   - El dataset Adult Income debe estar en `data/raw/adult.data` y `data/raw/adult.test`.
   - Puedes descargarlos desde [UCI Repository](https://archive.ics.uci.edu/dataset/2/adult).

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
    1.	Seleccionar repositorio
    2.	Name: (pontia-mlops-tutorial_m4_german_realpe)
    3.	Language/Runtime: `Python`
    4.	Branch: ‘main’
    5.	Region: ‘Frankfurt (EU Central)
    6.	Root Directory: ‘deployment’
    7.	Build Command: `deployment/ $ pip install -r requirements.txt`
    8.	Start Command: `deployment/ $ uvicorn app.main:app --host 0.0.0.0 --port 8080`
    9.	Instance Type: `Free`
    10.	Environment Variables:
    GITHUB_REPO: “gedorz/pontia-mlops-tutorial_m4_german_realpe”

### 4. Is done: deployment and validation para release
    1. Se agrega los model_test al repositorio
    2. Se configura el build.yml con: Upload model to GitHub Release
    3. Se agrega la data de entrenamiento:  Download adult dataset 
    4. Se suprime: Register model in MLflow
    5. se crea un deployment and validation en github
    6. se cambia Run model tests a Ejecutar los tests desde la raíz del repo.
    7. Remove mlFlow 

### 4. Is done: Crear deployment
    1. Se agrega las varibles de secreto para la conección con RENDER_DEPLOY_HOOK
    2. se agrega el token secrets.GITHUB_TOKEN


## Despliegue en Render

### Opción 1: Despliegue con Infrastructure as Code (IaC)

El proyecto incluye un archivo `render.yml` que define toda la infraestructura como código. Esto permite replicar el despliegue de forma consistente sin configuración manual por UI.

**Configuración del `render.yml`:**
```yaml
services:
  - type: web
    name: pontia-mlops-api
    env: python
    plan: free
    region: frankfurt
    buildCommand: "cd deployment && pip install -r requirements.txt"
    startCommand: "cd deployment && uvicorn app.main:app --host 0.0.0.0 --port 8080"
    envVars:
      - key: GITHUB_REPO
        value: gedorz/pontia-mlops-tutorial_m4_german_realpe
```

**Pasos para desplegar con `render.yml`:**

1. **Conecta tu repositorio a Render**:
   - Ve a [Render Dashboard](https://dashboard.render.com)
   - Selecciona "New +" → "Web Service" → "Build and deploy from Git"
   - Conecta tu repositorio GitHub `pontia-mlops-tutorial_m4_german_realpe`

2. **Render detectará automáticamente la configuración**:
   - El servicio se creará con los parámetros definidos en `render.yml`
   - Plan: Free
   - Región: Frankfurt (EU Central)
   - Python 3.10

3. **Despliegue automático**:
   - Después del primer despliegue, cada push a `main` dispara un nuevo build y despliegue automáticamente
   - Logs disponibles en Render Dashboard para debugging

4. **Verifica la API**:
   ```bash
   # Health check
   curl https://tu-servicio.onrender.com/health
   
   # Predicción (ejemplo)
   curl -X POST https://tu-servicio.onrender.com/predict \
     -H "Content-Type: application/json" \
     -d '{"age": 30, "education": "Bachelors", "occupation": "Tech-support", ...}'
   ```

### Opción 2: Despliegue Manual (sin `render.yml`)

Si prefieres configurar manualmente en la UI de Render:

1. **Crea un Web Service**:
   - Nombre: `pontia-mlops-api`
   - Repositorio: `https://github.com/gedorz/pontia-mlops-tutorial_m4_german_realpe`
   - Rama: `main`
   - Lenguaje: Python
   - Region: Frankfurt
   - Build Command: `cd deployment && pip install -r requirements.txt`
   - Start Command: `cd deployment && uvicorn app.main:app --host 0.0.0.0 --port 8080`

2. **Configura variables de entorno**:
   - `GITHUB_REPO`: `gedorz/pontia-mlops-tutorial_m4_german_realpe`

3. **Dispara el despliegue** y espera a que esté en verde

## Simulación de un Proceso de Rollback

En un entorno de producción, los rollbacks son necesarios cuando una nueva versión del modelo introduce problemas (como degradación de performance o errores). Este proyecto simula un rollback usando MLflow para versionado de modelos.

### Escenario de Rollback

Imagina que has desplegado la versión 2.0 del modelo, pero los usuarios reportan una disminución en la accuracy. Necesitas revertir rápidamente a la versión 1.0 anterior.

### Pasos para Simular el Rollback

1. **Identifica las versiones**:
   - En MLflow UI, revisa el modelo registrado (ej. "adult-income-model").
   - Versión 2.0 está marcada como "champion" (en producción).
   - Versión 1.0 está en "Archived" o "Staging".

2. **Cambia el alias en MLflow**:
   ```python
   from mlflow.tracking import MlflowClient

   client = MlflowClient()
   model_name = "adult-income-model"

   # Remueve el alias "champion" de la versión 2.0
   client.delete_registered_model_alias(model_name, "champion")

   # Asigna "champion" a la versión 1.0
   client.set_registered_model_alias(model_name, "champion", "1")
   ```

3. **Actualiza el despliegue**:
   - Si la app descarga automáticamente desde GitHub Releases, crea un nuevo release con la versión 1.0 del modelo.
   - Actualiza el tag en `render.yml` o variables de entorno para apuntar al release anterior.
   - Dispara el workflow de despliegue para redeployar con la versión anterior.

4. **Verifica el rollback**:
   - Monitorea las métricas de la API (`/health`).
   - Ejecuta tests de integración para confirmar que la versión anterior funciona correctamente.
   - Notifica a stakeholders sobre el rollback.

### Mejores Prácticas para Rollbacks

- Mantén múltiples versiones del modelo en staging.
- Implementa canary deployments para probar nuevas versiones con un subset de usuarios.
- Automatiza el proceso de rollback con scripts o pipelines CI/CD.
- Registra métricas de performance en producción para detectar issues temprano.

## Simulación de un Proceso de Rollback

En un entorno de producción, los rollbacks son necesarios cuando una nueva versión introduce problemas. Este proyecto implementa rollback mediante GitHub Releases y Render.

### Escenario de Rollback

Supongamos que has desplegado una nueva versión del modelo pero los usuarios reportan una disminución en accuracy. Necesitas revertir rápidamente.

### Pasos para Realizar un Rollback

**1. Identifica la versión anterior en GitHub Releases:**
```bash
# Ver releases disponibles
git tag

# O consulta en: https://github.com/gedorz/pontia-mlops-tutorial_m4_german_realpe/releases
```

**2. Revierte los cambios localmente:**
```bash
# Revierte el último commit
git revert HEAD

# O revierte a un commit específico
git revert <commit-hash>
```

**3. Pushea los cambios a main:**
```bash
git push origin main
```

**4. Render redeploya automáticamente:**
- El webhook de Render se dispara al detectar cambios en `main`
- La API se actualiza con la versión anterior
- Monitorea el despliegue en Render Dashboard

**5. Verifica que el rollback funcionó:**
```bash
# Health check
curl https://tu-servicio.onrender.com/health

# Prueba la API con datos de test
curl -X POST https://tu-servicio.onrender.com/predict \
  -H "Content-Type: application/json" \
  -d '{...}'
```

### Mejores Prácticas para Rollbacks

- Mantén un historial limpio de releases en GitHub
- Usa tags descriptivos: `model-v1.0`, `model-v1.1`, etc.
- Monitorea métricas en producción (accuracy, latencia, error rate)
- Documenta cambios en los commits y releases
- Prueba todas las versiones en staging antes de producción

## Git Workflow y Pull Requests

Este proyecto sigue un workflow profesional de Git con Code Review.

### Estructura de Ramas

- **`main`**: Rama de producción. Requiere PRs mergeadas con CI en verde
- **`feature/*`**: Ramas para nuevas características
- **`hotfix/*`**: Ramas para correcciones urgentes

### Crear un Pull Request

```bash
# 1. Crea una rama para tu cambio
git checkout -b feature/nombre-descriptivo

# 2. Realiza tus cambios
# ... edita archivos ...

# 3. Haz commit con mensaje descriptivo
git add .
git commit -m "feat: descripción clara del cambio"

# 4. Pushea la rama
git push origin feature/nombre-descriptivo

# 5. En GitHub, abre un Pull Request:
#    - Describe qué cambia y por qué
#    - Linkea issues relacionados si aplica
#    - Solicita revisión de compañeros
```

### Merge a Main

- [ ] Tests unitarios pasando (integration.yml)
- [ ] Code review aprobado (mínimo 1 revisor)
- [ ] Rama está actualizada con main
- [ ] Todos los comentarios resueltos
- **Merge**: GitHub Actions automáticamente dispara `build.yml`

### Después del Merge

1. **Build Pipeline** entrena el modelo automáticamente
2. **Model Tests** se ejecutan en el dataset de validación
3. **GitHub Release** se crea con los artifacts (model.pkl, scaler.pkl, encoders.pkl)
4. **Despliegue automático** a Render (si el webhook está configurado)

## Verificación de Pipelines en GitHub Actions

El proyecto cuenta con **3 pipelines automatizadas**:

### 1. Integration Pipeline (`.github/workflows/integration.yml`)
- **Se dispara**: En cada Pull Request y manualmente
- **Qué hace**:
  - Instala dependencias
  - Ejecuta tests unitarios
  - Reporta cobertura de código
  - Comenta resultados en el PR
- **Para pasar**: Todos los tests en `unit_tests/` deben pasar

### 2. Build Pipeline (`.github/workflows/build.yml`)
- **Se dispara**: En cada push a `main`
- **Qué hace**:
  - Descarga dataset Adult Income
  - Entrena el modelo RandomForest
  - Ejecuta tests de integración
  - Crea release en GitHub con artifacts
- **Para pasar**: Tests de integración en `model_tests/` deben pasar

### 3. Deploy Pipeline (`.github/workflows/deploy.yml`)
- **Se dispara**: Manualmente o con webhook desde Render
- **Qué hace**:
  - Dispara despliegue automático en Render
  - Actualiza la API en producción
- **Para pasar**: El servicio debe iniciar correctamente en Render

### Cómo Verificar el Estado de los Pipelines

1. **En GitHub**:
   - Ve a tu repositorio → pestaña "Actions"
   - Observa el estado de cada workflow (✅ verde = pasó, ❌ rojo = falló)
   - Haz click en un workflow para ver logs detallados

2. **En una PR**:
   - Observa la sección "Checks" al final de la descripción
   - Los tests comentados muestran cobertura y resultados

3. **Localmente** (antes de hacer push):
   ```bash
   # Ejecuta tests locales para evitar fallos
   pytest unit_tests/ --cov
   pytest model_tests/
   ```

## Testing

### Tests Unitarios
```bash
# Ejecuta todos los tests unitarios con cobertura
pytest unit_tests/ --cov=src --cov-report=html

# O individual
pytest unit_tests/test_data_loader.py -v
```

### Tests de Integración
```bash
# Ejecuta tests del modelo
pytest model_tests/test_model.py -v
```