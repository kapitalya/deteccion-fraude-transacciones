# Detección de fraude en transacciones con tarjeta

> Notebook y script de Python que comparan detección de anomalías y clasificación supervisada con balanceo de clases, y eligen el umbral por costo de negocio. Para analistas y estudiantes de machine learning aplicado a fintech.

**Estado (2026-11-17):** PARCIAL — `modelo_fraude.py` y `deteccion_fraude.ipynb` presentes; el dataset (`creditcard.csv`) no está en el repo; el notebook no tiene outputs guardados, así que las tablas de resultados no son verificables; falta `requirements.txt`, `.gitignore` y licencia.
**Repo:** kapitalya/deteccion-fraude-transacciones · **Rama por defecto:** main · **Alias/nombres anteriores:** ninguno

> Aviso: análisis educativo, no es consejo financiero ni un sistema antifraude en producción. Usa un dataset público y anonimizado.

## Qué hace

- Compara Isolation Forest (no supervisado) con Random Forest y Random Forest + SMOTE (supervisado) sobre el dataset de tarjetas de Kaggle (ULB), con métricas AUC-ROC y Average Precision.
- `modelo_fraude.py`: pipeline para entrenar (`entrenar`), evaluar (`evaluar`), elegir el umbral minimizando un costo (`_calcular_umbral_optimo`: falso positivo a $2, falso negativo al monto de la transacción), guardar el modelo y predecir una transacción (`predecir_transaccion`).
- `deteccion_fraude.ipynb`: análisis exploratorio y comparación de modelos. Sin outputs guardados.

## Stack

Python, `scikit-learn`, `imbalanced-learn` (SMOTE), `pandas`, `numpy`, `joblib`, Jupyter Notebook.

## Estructura

```
deteccion-fraude-transacciones/
├── deteccion_fraude.ipynb    # Exploración y comparación de modelos
├── modelo_fraude.py          # Pipeline entrenar/evaluar/predecir (en la raíz, no en src/)
└── README.md
```

`datos/`, `modelos/` e `img/` no existen en el repo: el script crea `modelos/` e `img/`; `datos/` lo crea el usuario.

## Instalación y uso

```bash
git clone https://github.com/kapitalya/deteccion-fraude-transacciones
cd deteccion-fraude-transacciones

python -m venv venv && source venv/bin/activate   # Windows: venv\Scripts\activate
pip install numpy pandas scikit-learn imbalanced-learn joblib jupyter   # sin requirements.txt
mkdir -p datos
# Descargar creditcard.csv de Kaggle y colocarlo en datos/
```

Dataset: Credit Card Fraud Detection (Kaggle, ULB Machine Learning Group, https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud). El archivo no se incluye en el repo.

Notebook:

```bash
jupyter lab deteccion_fraude.ipynb
```

Script (desde la raíz del repo):

```bash
# Entrenamiento con umbral automático (por defecto: datos/creditcard.csv)
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

El README anterior mostraba una salida de ejemplo (`score` 0.0423); no hay modelo ni outputs en el repo que la respalden. Resultado no verificable en el repo.

## Variables de entorno (solo nombres)

Ninguna. No hay `.env.example`.

## Pruebas y CI

Sin pruebas automatizadas ni workflows de CI en el repo.

## Despliegue

No aplica: scripts y notebooks de ejecución local.

## Resultados

Las tablas del README anterior (Isolation Forest, LR + SMOTE, RF + SMOTE con AUC ~0.95 a ~0.98 y Average Precision ~0.28 a ~0.85) no tienen respaldo en el repo: el notebook no tiene outputs guardados. Resultados no verificables en el repo; se retiran del README. Para obtenerlos, ejecutar el notebook o `modelo_fraude.py` con el dataset.

Dato del dataset (284,807 transacciones, 492 fraudes, 0.172%): coincide con la descripción pública de Kaggle; no verificable desde el repo, porque el CSV no está incluido.

El costo por falso positivo es `COSTO_FP = 2.0` en `modelo_fraude.py`. La fórmula completa de costo (falsos negativos valorados al monto de la transacción) no se verificó línea por línea en este corte.

## Estado y relaciones

- Repositorio independiente. El README anterior enlazaba `Serius69/scoring-credito-fintech-latam` y `Serius69/dolar-paralelo-bolivia-arima`, repos de una cuenta personal fuera de la organización; no auditados en este corte.

## Precios y planes

No aplica (repositorio de análisis, sin venta). Precios y planes: fuente única `Vault/EMPRESA/portafolio/PRECIOS-APROBADOS-20261108.md`.

## Seguridad, privacidad y derechos

- Sin secretos en el repo.
- Dataset: Kaggle / ULB, con sus propios términos de uso. Sin verificar términos en este corte. Las columnas V1 a V28 están anonimizadas por PCA.
- Licencia: el repo no tiene archivo LICENSE; no hay derechos de reutilización declarados.

## Convenciones de trabajo

Cambios por rama `codex/*` y PR contra `main` (no hay rama `develop`). CI: no hay workflows configurados.
