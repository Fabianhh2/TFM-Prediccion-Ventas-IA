# Predicción de ventas semanales mediante aprendizaje automático y deep learning

Repositorio complementario del Trabajo Fin de Máster **«Modelo Predictivo Basado en Deep Learning para la Optimización de Precios en Entornos Competitivos»**.

| Información académica | Detalle |
|---|---|
| Autor | Fabián Jesús Huarcaya Huaman |
| Titulación | Máster Universitario en Inteligencia Artificial |
| Curso académico | 2025–2026 |
| Director del TFM | D. Junior Altamiranda |

## Resumen

El trabajo estudia la predicción de ventas monetarias semanales por tienda y departamento con los datos de la competición *Walmart Recruiting – Store Sales Forecasting*. La referencia estacional basada en las ventas registradas 52 semanas antes motiva el aprendizaje de correcciones residuales. Tras comparar modelos lineales, perceptrones multicapa, embeddings, especialistas y mecanismos de puerta, se selecciona un **Soft Mixture of Experts** que combina dicha referencia con una corrección neuronal ponderada.

En 113 330 observaciones de validación, el modelo seleccionado obtiene un WMAE de 1 691,68, frente a 1 831,16 de la referencia estacional, una reducción del 7,62 %. Después del reentrenamiento con el histórico etiquetado disponible, se generan predicciones para el 99,00 % de las observaciones del conjunto de prueba. Este conjunto no incluye ventas reales, por lo que no es posible medir su precisión en test.

El desarrollo experimental se centra en la **predicción de ventas semanales minoristas** a partir de información histórica, temporal y comercial. Aunque el título del TFM sitúa el proyecto en el contexto de la optimización de precios, este repositorio no implementa un sistema de precios dinámicos, no estima elasticidades y no utiliza aprendizaje por refuerzo. La predicción de ventas se aborda como un problema de interés propio y como una posible base para futuras aplicaciones de apoyo a decisiones comerciales.

## Objetivo

El objetivo del proyecto es construir y evaluar un modelo capaz de predecir las ventas semanales para distintas combinaciones de tienda y departamento. El trabajo sigue una evolución progresiva:

1. análisis exploratorio y preparación temporal de los datos;
2. construcción de variables y modelos de referencia;
3. desarrollo de redes neuronales globales, embeddings y especialistas;
4. combinación de expertos mediante una arquitectura *Mixture of Experts*;
5. reentrenamiento final con el historial etiquetado disponible;
6. análisis de validación, generación de predicciones para test e interpretación mediante SHAP.

El hilo conductor es comprobar si una arquitectura global puede aprovechar patrones compartidos entre series heterogéneas sin perder la capacidad de especializarse en comportamientos concretos.

## Estructura del repositorio

```text
.
├── data/
│   ├── processed/
│   ├── neural_networks/
│   └── final_model/
├── models/
│   ├── linear_baselines/
│   ├── mlp_residual_global/
│   ├── mlp_residual_high_embeddings/
│   ├── mlp_residual_high_specialist/
│   ├── mlp_residual_high_feature_engineering_v1/
│   ├── frozen_expert_soft_gate/
│   ├── soft_moe_conjunto/
│   ├── soft_moe_feature_engineering_v1/
│   └── final_soft_moe_fe_full_history/
├── notebooks/
│   ├── 01_EDA.ipynb
│   ├── 02_ModeloPredictivo.ipynb
│   ├── 03_RedesNeuronales.ipynb
│   └── 04_TestModeloFinal.ipynb
├── results/
│   └── final_soft_moe_fe/
├── .gitattributes
├── .gitignore
└── README.md
```

### `data/`

Contiene los conjuntos intermedios necesarios para reproducir las distintas etapas:

- `processed/`: particiones temporales resultantes del procesamiento inicial;
- `neural_networks/`: datos preparados para los experimentos con redes neuronales;
- `final_model/`: particiones y configuración de variables empleadas por el modelo final.

Los archivos `*.json` conservan firmas y configuraciones que permiten comprobar la procedencia y coherencia de los datos exportados.

### `models/`

Reúne los artefactos de los modelos entrenados: pesos, modelos serializados, preprocesadores, escaladores, historiales y metadatos. Se incluyen:

- regresiones lineales de referencia;
- MLP residual global;
- MLP con embeddings para variables categóricas de alta cardinalidad;
- modelos especialistas;
- variantes con ingeniería de características;
- mecanismos de combinación mediante puerta suave;
- modelo final Soft Mixture of Experts entrenado con el historial completo.

### `notebooks/`

Los notebooks documentan el desarrollo metodológico y experimental. Su orden recomendado de lectura es:

1. **`01_EDA.ipynb`**: carga, revisión, análisis exploratorio y preparación inicial de los datos.
2. **`02_ModeloPredictivo.ipynb`**: planteamiento predictivo, construcción de variables y modelos de referencia.
3. **`03_RedesNeuronales.ipynb`**: experimentación con redes neuronales, embeddings, especialistas y mecanismos de combinación.
4. **`04_TestModeloFinal.ipynb`**: selección de la política de entrenamiento con validación, reentrenamiento final, generación de predicciones para test, análisis de cobertura e interpretabilidad.

### `results/`

Contiene las salidas del modelo final:

- métricas y predicciones de validación y test;
- análisis por tienda, departamento, volumen y semanas festivas;
- observaciones excluidas o seleccionadas para inspección;
- gráficos descriptivos y de error;
- configuraciones y resúmenes de ejecución;
- explicaciones globales y locales mediante SHAP, con análisis separado para periodos ordinarios y campañas.

## Enfoque metodológico

La predicción se formula como un problema supervisado sobre series semanales relacionadas. Además de las variables disponibles, se incorporan características temporales y retardos que permiten representar la dinámica histórica y la estacionalidad anual.

Los modelos lineales proporcionan referencias interpretables frente a las que comparar las arquitecturas neuronales. Posteriormente se estudian modelos globales capaces de aprender de todas las series, embeddings para representar identificadores categóricos y especialistas destinados a subconjuntos con comportamientos diferenciados.

La propuesta final utiliza una arquitectura **Soft Mixture of Experts con ingeniería de características**. En lugar de asignar cada observación rígidamente a un único experto, una puerta suave aprende una combinación ponderada de sus predicciones. El modelo seleccionado se reentrena con el historial disponible antes de realizar la evaluación final sobre el conjunto de test.

## Reproducibilidad

Los notebooks están preparados para **Google Colab**: montan Google Drive y esperan encontrar los datos y modelos bajo `MyDrive/TFM/`. En consecuencia, clonar el repositorio no basta para ejecutarlos sin cambios. Para ejecutarlos en Colab, coloca las carpetas `data/`, `models/` y `results/` dentro de `MyDrive/TFM/` respetando la estructura indicada, abre los notebooks en orden y habilita el acceso a Drive. El primer notebook también descarga los datos de la competición mediante la API de Kaggle; necesitarás una cuenta y tus propias credenciales de Kaggle. No subas `kaggle.json` al repositorio.

Para una revisión rápida del trabajo no es necesario volver a entrenar todos los modelos: el repositorio conserva notebooks, datos procesados y artefactos de entrenamiento. La ejecución completa puede requerir más memoria y tiempo que la evaluación de un modelo ya entrenado.

Para ejecutar el flujo completo se recomienda:

1. clonar el repositorio;
2. crear un entorno virtual de Python;
3. instalar las dependencias desde `requirements.txt`;
4. ejecutar el código fuera de Colab solo después de adaptar las celdas de montaje de Drive y las rutas absolutas `/content/drive/MyDrive/TFM`.

```bash
git clone https://github.com/Fabianhh2/TFM-Prediccion-Ventas-IA.git
cd TFM-Prediccion-Ventas-IA

python -m venv .venv
pip install -r requirements.txt
```

En Windows:

```powershell
.venv\Scripts\activate
```

En Linux o macOS:

```bash
source .venv/bin/activate
```

El entorno registrado en la salida del notebook final fue Python 3.13.16, NumPy 2.1.3, pandas 2.2.3, scikit-learn 1.6.1 y TensorFlow 2.21.0. `requirements.txt` recoge las dependencias directas utilizadas; el resto de versiones no se fijan porque los notebooks no conservan una captura completa del entorno. Estas versiones reflejan la ejecución registrada, no garantizan por sí solas que cualquier ejecución futura sea idéntica.

## Formatos incluidos

- `*.ipynb`: desarrollo metodológico y experimental;
- `*.parquet`: datos procesados, características y predicciones;
- `*.keras` y `*.h5`: modelos y pesos neuronales;
- `*.joblib`: preprocesadores, escaladores y modelos clásicos;
- `*.json`: configuraciones, firmas y metadatos;
- `*.csv`: métricas, historiales y resultados tabulares;
- `*.png`: gráficos de evaluación e interpretabilidad;
- `*.npz`: valores auxiliares del análisis SHAP.

## Resultados e interpretación

La evaluación no se limita a una única métrica agregada. El repositorio conserva métricas de validación desglosadas para estudiar el comportamiento del modelo en diferentes tiendas, departamentos, niveles de volumen y tipos de semana. El análisis SHAP permite examinar tanto la importancia global de las variables como casos locales concretos, además de comparar el comportamiento durante campañas y periodos ordinarios.

El conjunto de test de la competición de Kaggle no incluye la variable objetivo `Weekly_Sales`. Por ello, sobre test se generan predicciones y se estudia la cobertura, pero no pueden calcularse métricas de error reales. Los valores cuantitativos de rendimiento deben consultarse en `results/final_soft_moe_fe/validation_metrics.csv`, en los resúmenes de ejecución y en el notebook `04_TestModeloFinal.ipynb`.

## Limitaciones

- El sistema desarrollado predice ventas, pero no determina precios óptimos.
- La calidad de las predicciones está condicionada por las variables disponibles y por la existencia de historial suficiente para cada serie.
- El rendimiento agregado puede ocultar diferencias relevantes entre tiendas, departamentos o periodos de alta demanda; por ello se incluyen análisis segmentados.
- Los artefactos entrenados dependen de las transformaciones y configuraciones guardadas junto a cada modelo.

## Uso académico

Este repositorio se publica como material complementario de un Trabajo Fin de Máster. El conjunto de datos procede de la competición **Walmart Recruiting – Store Sales Forecasting** de Kaggle. Antes de redistribuir los ficheros originales, comprueba y respeta las reglas y condiciones de Kaggle y de los titulares de los datos. Si se reutilizan el código, los resultados o partes de la metodología, debe citarse el trabajo correspondiente y respetarse la licencia que finalmente se asigne al repositorio y las condiciones de uso de los datos.

