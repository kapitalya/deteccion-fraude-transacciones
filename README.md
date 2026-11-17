# Detección de fraude en transacciones con tarjeta

> Notebook y script de Python que comparan detección de anomalías y clasificación supervisada con balanceo de clases, y eligen el umbral por costo de negocio. Para analistas y estudiantes de machine learning aplicado a fintech.

**Estado (2026-11-17):** PARCIAL — `modelo_fraude.py` y `deteccion_fraude.ipynb` presentes; el dataset (`datos/creditcard.csv`) no está en el repo; el notebook no tiene outputs guardados, así que las tablas de resultados no son verificables; falta `requirements.txt`, `.gitignore` y licencia.
**Repo:** kapitalya/deteccion-fraude-transacciones · **Rama por defecto:** main · **Alias/nombres anteriores:** ninguno

> Aviso: análisis educativo, no es consejo financiero ni un sistema antifraude en producción. Usa un dataset público y anonimizado.

## Qué hace

- `deteccion_fraude.ipynb`: lee `datos/creditcard.csv` y compara Isolation Forest (no supervisado), regresión logística, Random Forest, Random Forest + SMOTE y submuestreo aleatorio (`RandomUnderSampler`). Describe el costo asimétrico de los errores y el umbral óptimo. Sin outputs guardados.
- `modelo_fraude.py` (raíz): pipeline con Random Forest y SMOTE. Funciones: `cargar_y_preprocesar`, `entrenar`, `evaluar` (AUC-ROC y Average Precision), `_calcular_umbral_optimo` (umbral que minimiza el costo, con falso positivo a `COSTO_FP = 2.0` dólares), `guardar_modelo` (en `modelos/modelo_fraude.pkl`) y `predecir_transaccion`.

## Stack

Python. Librerías importadas: `scikit-learn` (Isolation Forest, Random Forest, regresión logística, métricas), `imbalanced-learn` (SMOTE, RandomUnderSampler), `pandas`, `numpy`, `joblib`, `scipy`, `matplotlib`, `seaborn`. Jupyter Notebook.

## Estructura

```
deteccion-fraude-transacciones/
├── deteccion_fraude.ipynb    # Exploración y comparación de modelos
├── modelo_fraude.py          # Pipeline entrenar/evaluar/predecir (en la raíz, no en src/)
└── README.md
```

`datos/`, `modelos/` e `img/` no existen en el repo: `modelo_fraude.py` crea `modelos/` e `img/` al ejecutarse; `datos/` lo crea el usuario.

## Instalación y uso

```bash
git clone https://github.com/kapitalya/deteccion-fraude-transacciones
cd deteccion-fraude-transacciones

python -m venv venv && source venv/bin/activate   # Windows: venv\Scripts\activate
pip install numpy pandas scipy scikit-learn imbalanced-learn joblib matplotlib seaborn jupyter   # sin requirements.txt
mkdir -p datos
# Descargar creditcard.csv de Kaggle y colocarlo en datos/
```

Dataset: Credit Card Fraud Detection (Kaggle, ULB Machine Learning Group, https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud). El archivo no se incluye en el repo; lo requieren el notebook y el script (`--datos`, por defecto `datos/creditcard.csv`).

Notebook:

```bash
jupyter lab deteccion_fraude.ipynb
```

Script (desde la raíz del repo):

```bash
# Entrenamiento con umbral automático
python modelo_fraude.py --datos datos/creditcard.csv --umbral auto

# Entrenamiento con umbral fijo
python modelo_fraude.py --datos datos/creditcard.csv --umbral 0.35
```

Predicción de una transacción (requiere haber entrenado antes, para que exista `modelos/modelo_fraude.pkl`):

```python
from modelo_fraude import predecir_transaccion

transaccion = {f'V{i}': 0.0 for i in range(1, 29)}   # V1..V28 (componentes PCA)
transaccion.update({'V1': -1.36, 'V2': -0.07, 'Amount': 149.62, 'Time': 3600})
print(predecir_transaccion(transaccion))
```

## Variables de entorno (solo nombres)

Ninguna. No hay `.env.example`.

## Pruebas y CI

- Sin pruebas automatizadas ni workflows de CI en el repo.

### Resultados

- Las tablas del README anterior (AUC-ROC ~0.95 a ~0.98 y Average Precision ~0.28 a ~0.85 según el modelo) no tienen respaldo: el notebook no tiene outputs guardados. Se retiran. Resultados no verificables en el repo; para obtenerlos, ejecutar el notebook o `modelo_fraude.py` con el dataset.
- El ejemplo de salida de `predecir_transaccion` del README anterior (`score` 0.0423) no tiene modelo ni outputs que lo respalden. Resultado no verificable en el repo.
- Datos del dataset (284,807 transacciones, 492 fraudes, 0.172%): coinciden con la descripción pública de Kaggle; no verificables desde el repo porque el CSV no está incluido.
- Costo: `COSTO_FP = 2.0` está en `modelo_fraude.py`. La fórmula completa con falsos negativos valorados al monto de la transacción no se verificó línea por línea en este corte.

## Despliegue

No aplica: scripts y notebooks de ejecución local.

## Estado y relaciones

- Repositorio independiente. El README anterior enlazaba `Serius69/scoring-credito-fintech-latam` y `Serius69/dolar-paralelo-bolivia-arima`, repos de una cuenta personal fuera de la organización; no auditados en este corte.

## Precios y planes

No aplica (repositorio de análisis, sin venta). Precios y planes: fuente única `Vault/EMPRESA/portafolio/PRECIOS-APROBADOS-20261108.md`.

## Seguridad, privacidad y derechos

- Sin secretos en el repo.
- Dataset: Kaggle / ULB, con sus propios términos de uso; no verificados en este corte. Las columnas V1 a V28 están anonimizadas por PCA.
- Licencia: el repo no tiene archivo LICENSE; no hay derechos de reutilización declarados.

## Convenciones de trabajo

Cambios por rama `codex/*` y PR contra `main` (no hay rama `develop`). CI: no hay workflows configurados.
