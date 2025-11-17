# CEIA-VisionPorComputadoraII

Repositorio del Trabajo Final de la materia **Visión por Computadora II** de la **CEIA-UBA**.

## Descripción del Proyecto

Este proyecto se enfoca en el desarrollo de un modelo de clasificación de imágenes médicas para la detección y clasificación de nódulos pulmonares en tomografías computadas (CT) de tórax. El objetivo es clasificar las imágenes en diferentes tipos de carcinoma pulmonar y casos normales.

**Dataset**: [Chest CT-Scan images Dataset](https://www.kaggle.com/datasets/mohamedhanyyy/chest-ctscan-images) (Kaggle)

## Objetivos

1. **Clasificación de imágenes CT de tórax** en 4 categorías:
   - Normal
   - Adenocarcinoma
   - Large Cell Carcinoma
   - Squamous Cell Carcinoma

2. **Análisis exploratorio** del dataset para identificar:
   - Balanceo de clases
   - Calidad e integridad de los datos
   - Características de las imágenes (tamaños, formatos, canales)
   - Separabilidad entre clases
   - Problemas de dataset shift y fugas de datos

3. **Desarrollo de un modelo de deep learning** utilizando:
   - Transfer learning con modelos preentrenados
   - Técnicas de data augmentation apropiadas para imágenes médicas
   - Estrategias de balanceo de clases
   - Validación robusta del modelo

4. **Evaluación y análisis** de resultados con métricas apropiadas para problemas médicos.

## Análisis Exploratorio de Datos (EDA)

El análisis exploratorio completo se encuentra en la notebook:

**[`1_EDA.ipynb`](1_EDA.ipynb)**

### Contenido del EDA:

- **Balanceo de clases**: Análisis de distribución de imágenes por clase y split
- **Visualización de imágenes**: Muestras representativas de cada clase
- **Análisis de tamaños**: Distribución de dimensiones de imágenes
- **Histogramas de colores**: Análisis de intensidades y separabilidad de clases
- **Estadísticas RGB y HSV**: Caracterización de canales de color
- **Integridad de datos**: Detección de duplicados y archivos corruptos
- **Análisis de canales**: Identificación de imágenes grayscale vs RGB
- **Detección de dataset shift**: Análisis de diferencias entre splits
- **Conclusiones y recomendaciones**: Guía para el preprocesamiento y modelado

## Eliminación de Duplicados

Tras el análisis exploratorio, se identificaron 153 imágenes duplicadas exactas (59 grupos de duplicados) en el dataset original. Para resolver este problema, se desarrolló un proceso de limpieza que se encuentra documentado en:

**[`2_Eliminar_duplicados.ipynb`](2_Eliminar_duplicados.ipynb)**

### Proceso de Limpieza:

1. **Detección de duplicados**: Se utilizó hash MD5 para identificar imágenes duplicadas exactas en todo el dataset
2. **Eliminación**: Se mantuvo una sola copia de cada imagen duplicada
3. **Reorganización**: Se unificó el dataset, eliminó duplicados y se realizó un nuevo split estratificado (train/valid/test) con proporciones 70%/15%/15%
4. **Resultado**: Se generó el dataset `Data_Clean` con 847 imágenes únicas (reducción de 1000 a 847 imágenes)

### Distribución Final en Data_Clean:

- **Train**: 592 imágenes
  - Adenocarcinoma: 235
  - Squamous Cell Carcinoma: 180
  - Large Cell Carcinoma: 131
  - Normal: 46

- **Valid**: 127 imágenes
  - Adenocarcinoma: 51
  - Squamous Cell Carcinoma: 38
  - Large Cell Carcinoma: 28
  - Normal: 10

- **Test**: 128 imágenes
  - Adenocarcinoma: 51
  - Squamous Cell Carcinoma: 39
  - Large Cell Carcinoma: 28
  - Normal: 10

Este dataset limpio (`Data_Clean`) se utiliza para todos los entrenamientos posteriores, ya que elimina el sesgo introducido por las imágenes duplicadas y mejora la generalización del modelo.

## Modelos Baseline

Se implementaron modelos baseline utilizando VGG16 preentrenada en ImageNet con transfer learning. Se entrenaron dos versiones: una con el dataset original (`Data`) y otra con el dataset limpio sin duplicados (`Data_Clean`).

### Resultados F1-Score por Clase

#### Dataset Original (`Data`)

| Clase | F1-Score |
|-------|----------|
| Adenocarcinoma | 0.35 |
| Large Cell Carcinoma | 0.26 |
| Normal | 0.97 |
| Squamous Cell Carcinoma | 0.58 |
| **Macro Average** | **0.54** |

#### Dataset Limpio (`Data_Clean`)

| Clase | F1-Score |
|-------|----------|
| Adenocarcinoma | 0.64 |
| Large Cell Carcinoma | 0.35 |
| Normal | 0.90 |
| Squamous Cell Carcinoma | 0.56 |
| **Macro Average** | **0.61** |

### Análisis de Resultados

Los resultados muestran una **mejora significativa** al utilizar el dataset limpio (`Data_Clean`) en comparación con el dataset original:

- **Macro F1-Score**: Aumentó de **0.54** a **0.61** (+13% de mejora relativa)
- **Adenocarcinoma**: Mejoró de 0.35 a **0.64** (+83% de mejora relativa)
- **Large Cell Carcinoma**: Mejoró de 0.26 a **0.35** (+35% de mejora relativa)
- **Normal**: Se mantuvo alto en ambos casos (0.97 vs 0.90)
- **Squamous Cell Carcinoma**: Se mantuvo similar (0.58 vs 0.56)

Estos resultados confirman la importancia de la limpieza de datos: **eliminar duplicados mejora significativamente el rendimiento del modelo**, especialmente en las clases de carcinoma que son más difíciles de clasificar. El dataset limpio permite al modelo generalizar mejor y evitar el sobreajuste causado por imágenes duplicadas.

### Notebooks de Baseline

- **[`3_Baseline_VGG16_data_original.ipynb`](3_Baseline_VGG16_data_original.ipynb)**: Entrenamiento con dataset original
- **[`3_Baseline_VGG16.ipynb`](3_Baseline_VGG16.ipynb)**: Entrenamiento con dataset limpio (`Data_Clean`)

## Integrantes

- **Agustín López Fredes** (agustin.lopezfredes@gmail.com)

- **Natalia Espector** (nataliaespector@gmail.com)

## Estructura del Proyecto

```
CEIA-VisionPorComputadoraII/
├── 1_EDA.ipynb                          # Análisis Exploratorio de Datos completo
├── 2_Eliminar_duplicados.ipynb         # Eliminación de imágenes duplicadas
├── 3_Baseline_VGG16_data_original.ipynb # Baseline VGG16 con dataset original
├── 3_Baseline_VGG16.ipynb               # Baseline VGG16 con dataset limpio
├── Data/                                # Dataset de imágenes CT original
│   ├── train/                           # Conjunto de entrenamiento
│   ├── valid/                           # Conjunto de validación
│   └── test/                            # Conjunto de prueba
├── Data_Clean/                          # Dataset limpio sin duplicados
│   ├── train/                           # Conjunto de entrenamiento
│   ├── valid/                           # Conjunto de validación
│   └── test/                            # Conjunto de prueba
├── main.py                              # Script principal (pendiente)
├── pyproject.toml                       # Configuración de dependencias
├── uv.lock                              # Lock file de dependencias
└── README.md                            # Este archivo
```

## Instalación y Configuración

### Requisitos

- Python >= 3.12, < 3.13
- Gestor de paquetes: `uv` (recomendado) o `pip`

### Instalación de dependencias

```bash
# Con uv (recomendado)
uv sync

# O con pip
pip install -r requirements.txt
```

### Dependencias principales

- `torch` >= 2.8.0
- `opencv-python` >= 4.11.0.86
- `pandas` >= 2.3.3
- `numpy` >= 2.3.4
- `matplotlib` >= 3.10.7
- `seaborn` >= 0.13.2
- `scikit-learn` >= 1.7.2
- `imagehash` >= 4.3.1
- `jupyter` >= 1.1.1

## Uso

### Ejecutar el EDA

```bash
jupyter notebook 1_EDA.ipynb
```

O con JupyterLab:

```bash
jupyter lab 1_EDA.ipynb
```

## 🔍 Hallazgos Principales del EDA

### Problemas Críticos Identificados

1. **Fugas de datos entre splits**: Imágenes del mismo hash aparecen en múltiples splits
2. **Dataset shift**: Diferencias significativas en intensidades entre train, valid y test (21.79% en test)
3. **Duplicados**: 59 grupos de duplicados exactos detectados (153 archivos)

### Características del Dataset

- **Total de imágenes**: 1000
- **Clases**: 4 (Normal, Adenocarcinoma, Large Cell Carcinoma, Squamous Cell Carcinoma)
- **Formato**: Principalmente PNG, algunas JPEG
- **Tipo**: Imágenes grayscale almacenadas en formato RGB/RGBA
- **Tamaños**: Variables (168x110 px a 1200x874 px, promedio: 447x318 px)

### Recomendaciones Clave

1. **Preprocesamiento obligatorio**:
   - Eliminar duplicados exactos
   - Reorganizar splits para eliminar fugas de datos
   - Convertir a grayscale (1 canal)
   - Resize uniforme
   - Normalización consistente usando estadísticas del train

2. **Estrategia de modelado**:
   - Transfer learning con feature extraction (no fine-tuning completo)
   - Data augmentation apropiada para imágenes médicas
   - Pesos de clase para balanceo
   - Validación cruzada k-fold

## Referencias

- Dataset: [Chest CT-Scan images Dataset](https://www.kaggle.com/datasets) en Kaggle
- Materia: Visión por Computadora II - CEIA-UBA


---
