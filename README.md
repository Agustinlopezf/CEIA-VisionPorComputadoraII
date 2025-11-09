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

## Integrantes

- **Agustín López Fredes** (agustin.lopezfredes@gmail.com)

- **Natalia Espector** (nataliaespector@gmail.com)

## Estructura del Proyecto

```
CEIA-VisionPorComputadoraII/
├── 1_EDA.ipynb              # Análisis Exploratorio de Datos completo
├── Data/                    # Dataset de imágenes CT
│   ├── train/               # Conjunto de entrenamiento
│   ├── valid/               # Conjunto de validación
│   └── test/                # Conjunto de prueba
├── main.py                  # Script principal (pendiente)
├── pyproject.toml           # Configuración de dependencias
├── uv.lock                  # Lock file de dependencias
└── README.md                # Este archivo
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
