# Predicción de ventas semanales mediante aprendizaje automático y deep learning

Repositorio complementario del Trabajo Fin de Máster **«Modelo Predictivo Basado en Deep Learning para la Optimización de Precios en Entornos Competitivos»**.

El desarrollo experimental se centra en la **predicción de ventas semanales minoristas** a partir de información histórica, temporal y comercial. Aunque el título del TFM sitúa el proyecto en el contexto de la optimización de precios, este repositorio no implementa un sistema de precios dinámicos, no estima elasticidades y no utiliza aprendizaje por refuerzo. La predicción de ventas se aborda como un problema de interés propio y como una posible base para futuras aplicaciones de apoyo a decisiones comerciales.

## Objetivo

El objetivo del proyecto es construir y evaluar un modelo capaz de predecir las ventas semanales para distintas combinaciones de tienda y departamento. El trabajo sigue una evolución progresiva:

1. análisis exploratorio y preparación temporal de los datos;
2. construcción de variables y modelos de referencia;
3. desarrollo de redes neuronales globales, embeddings y especialistas;
4. combinación de expertos mediante una arquitectura *Mixture of Experts*;
5. entrenamiento final con el historial disponible;
6. evaluación fuera de muestra e interpretación mediante SHAP.

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
4. **`04_TestModeloFinal.ipynb`**: entrenamiento definitivo, evaluación sobre test, análisis de errores e interpretabilidad.

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

Para una revisión rápida del trabajo no es necesario volver a entrenar todos los modelos: los notebooks conservan sus salidas principales y el repositorio incluye los artefactos utilizados en la evaluación.

Para ejecutar el flujo completo se recomienda:

1. clonar el repositorio;
2. crear un entorno virtual de Python;
3. instalar las dependencias del proyecto;
4. abrir Jupyter Notebook o JupyterLab desde la raíz del repositorio;
5. ejecutar los notebooks siguiendo el orden indicado anteriormente.

```bash
git clone https://github.com/Fabianhh2/TFM-Prediccion-Ventas-IA.git
cd TFM-Prediccion-Ventas-IA

python -m venv .venv
```

En Windows:

```powershell
.venv\Scripts\activate
```

En Linux o macOS:

```bash
source .venv/bin/activate
```

Las versiones exactas de las bibliotecas deberán recogerse en un archivo `requirements.txt` para asegurar la reproducción del entorno.

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

La evaluación final no se limita a una única métrica agregada. El repositorio conserva resultados desglosados para estudiar el comportamiento del modelo en diferentes tiendas, departamentos, niveles de volumen y tipos de semana. El análisis SHAP permite examinar tanto la importancia global de las variables como casos locales concretos, además de comparar el comportamiento durante campañas y periodos ordinarios.

Los valores numéricos definitivos deben consultarse en `results/final_soft_moe_fe/validation_metrics.csv`, en los resúmenes de ejecución y en el notebook `04_TestModeloFinal.ipynb`.

## Limitaciones

- El sistema desarrollado predice ventas, pero no determina precios óptimos.
- La calidad de las predicciones está condicionada por las variables disponibles y por la existencia de historial suficiente para cada serie.
- El rendimiento agregado puede ocultar diferencias relevantes entre tiendas, departamentos o periodos de alta demanda; por ello se incluyen análisis segmentados.
- Los artefactos entrenados dependen de las transformaciones y configuraciones guardadas junto a cada modelo.

## Uso académico

Este repositorio se publica como material complementario de un Trabajo Fin de Máster. Si se reutilizan el código, los resultados o partes de la metodología, debe citarse el trabajo correspondiente y respetarse la licencia y las condiciones de uso del conjunto de datos original.

