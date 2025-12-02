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

### Resultados Completos del Modelo Baseline

#### Dataset Limpio (`Data_Clean`) - Modelo Final

**Métricas Globales:**
- **Accuracy**: 0.65
- **F1-Score Macro**: 0.70
- **Recall Macro**: 0.70

**Métricas por Clase:**

| Clase | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| Adenocarcinoma | 0.66 | 0.69 | 0.67 | 51 |
| Large Cell Carcinoma | 0.70 | 0.50 | 0.58 | 28 |
| Normal | 0.91 | 1.00 | 0.95 | 10 |
| Squamous Cell Carcinoma | 0.55 | 0.62 | 0.58 | 39 |

#### Comparación con Dataset Original (`Data`)

| Métrica | Dataset Original | Dataset Limpio | Mejora |
|---------|------------------|-----------------|--------|
| Accuracy | 0.55 | **0.65** | +18% |
| F1-Score Macro | 0.54 | **0.70** | +30% |
| Recall Macro | 0.58 | **0.70** | +21% |

### Análisis de Resultados

Se observa que los resultados con el dataset original fueron peores que usando el dataset limpio de duplicados, por lo que se avanzará con este último.

**Hallazgos principales:**

1. **Mejora significativa con dataset limpio**: El modelo con `Data_Clean` supera consistentemente al modelo entrenado con el dataset original en todas las métricas principales.

2. **Clase Normal**: Presenta excelente desempeño (recall = 1.00, F1 = 0.95), lo cual es clínicamente relevante ya que minimiza los falsos negativos en casos normales.

3. **Clases problemáticas identificadas**:
   - **Large Cell Carcinoma**: Presenta un recall bajo (0.50), indicando que el modelo tiene dificultades para detectar correctamente esta clase.
   - **Confusión entre Adenocarcinoma y Squamous Cell Carcinoma**: La matriz de confusión muestra confusiones entre estas dos clases tumorales, lo que podría indicar similitudes visuales entre ellas.

4. **Balanceo de clases**: A pesar del desbalance en el dataset, el modelo logra un buen desempeño general, sugiriendo que las técnicas de data augmentation aplicadas están mitigando adecuadamente este efecto.

### Estrategias Futuras

Tras identificar que **Large Cell Carcinoma** presenta un recall bajo (0.50) y que existe confusión entre **Adenocarcinoma** y **Squamous Cell Carcinoma**, se evaluarán las siguientes estrategias:

1. **Arquitecturas más profundas**: Probar modelos como **DenseNet121** o **ResNet50** que puedan capturar características más complejas y mejorar la discriminación entre clases similares.

2. **Fine-tuning de capas adicionales**: En lugar de entrenar solo la última capa, descongelar progresivamente capas más profundas del modelo preentrenado para permitir un ajuste más específico a las características del dominio médico.

### Notebooks de Baseline

- **[`3_Baseline_VGG16_data_original.ipynb`](3_Baseline_VGG16_data_original.ipynb)**: Entrenamiento con dataset original
- **[`3_Baseline_VGG16.ipynb`](3_Baseline_VGG16.ipynb)**: Entrenamiento con dataset limpio (`Data_Clean`)

## Modelos Complejos: DenseNet121 y ResNet50

Se implementaron dos modelos más modernos y con arquitecturas más profundas, ambos preentrenados en ImageNet y ajustados mediante *transfer learning* sobre el dataset limpio (`Data_Clean`):

- **DenseNet121**
- **ResNet50**

En ambos casos se reutilizó el mismo esquema de preprocesamiento y *data augmentation* definido para el baseline con VGG16 (conversiones a escala de grises, *resize*, rotaciones leves, *flip* horizontal, variaciones de brillo/contraste y normalización tipo ImageNet).

### DenseNet121 – Modelo intermedio

DenseNet121 se seleccionó por su uso extendido en imágenes médicas y por su arquitectura con **conexiones densas** entre capas, que favorecen la reutilización de características y la captura de detalles finos.

1. **Primer experimento (solo clasificador)**  
   Inicialmente se entrenó únicamente la capa de clasificación final, manteniendo congelado el extractor de características.  
   En este escenario, el desempeño fue comparable pero **no superior** al modelo baseline con VGG16.

2. **Fine-tuning parcial de capas profundas**  
   A partir de la revisión bibliográfica y de los resultados obtenidos, se observó que DenseNet121 tiende a generar representaciones muy ricas pero también más especializadas en el dominio de ImageNet.  
   Por este motivo, se realizó un **fine-tuning parcial**, descongelando el último bloque convolucional (**`denseblock4` y `norm5`**) además del clasificador final.  
   Con un *learning rate* bajo (`lr = 5e-5`) y *early stopping* se obtuvo un **salto significativo en performance**, alcanzando aproximadamente:

   - **Accuracy (test)** ≈ 0.92  
   - **F1-Score Macro** ≈ 0.93  
   - **Recall Macro** ≈ 0.94  

   Esto demuestra que, en este dataset, un ajuste fino de las capas profundas de DenseNet121 permite “desaprender” parte de lo específico de ImageNet y especializarse en patrones propios de las CT de tórax.

La implementación detallada se encuentra en la notebook:

- **[`4_Modelo_DenseNet121.ipynb`](4_Modelo_DenseNet121.ipynb)**

---

### ResNet50 – Modelo final

Como segundo modelo complejo se utilizó **ResNet50**, una arquitectura basada en **bloques residuales** con *skip connections* que facilitan el entrenamiento de redes profundas al mejorar el flujo de gradientes.

Sobre este modelo se aplicó también *transfer learning* con:

- Congelación inicial del extractor de características.
- Reemplazo de la capa de clasificación final para 4 clases.
- Fine-tuning parcial de las capas profundas (bloque `layer4`) junto con el clasificador.
- Mismo esquema de *data augmentation* y *early stopping* que en DenseNet121.

Con esta configuración inicial, **ResNet50** obtuvo un **excelente desempeño global**, alcanzando en el conjunto de test:

- **Accuracy (test)**: 0.9688 (96.88%)
- **F1-Score Macro**: 0.97
- **Recall Macro**: 0.98

**Métricas por clase (versión inicial):**

| Clase | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| Adenocarcinoma | 0.98 | 0.96 | 0.97 | 51 |
| Large Cell Carcinoma | 0.93 | 1.00 | 0.97 | 28 |
| Normal | 0.91 | 1.00 | 0.95 | 10 |
| Squamous Cell Carcinoma | 1.00 | 0.95 | 0.97 | 39 |

La implementación detallada se encuentra en la notebook:

- **[`5_Modelo_ResNet50.ipynb`](5_Modelo_ResNet50.ipynb)**

---

### ResNet50 Optimizado – Mejora basada en Grad-CAM

Tras el análisis de errores mediante **Grad-CAM** (ver sección de Interpretabilidad), se identificó que uno de los errores de clasificación (Adenocarcinoma clasificado como Large Cell Carcinoma) estaba relacionado con diferencias en el brillo de las imágenes. 

Basándose en esta observación, se realizó una **optimización dirigida** del modelo:

- **Modificación**: Aumento del parámetro `brightness` en la transformación `ColorJitter` de 0.1 a 0.2
- **Objetivo**: Mejorar la capacidad del modelo para generalizar ante variaciones de brillo, específicamente para casos de Adenocarcinoma

**Resultados del modelo optimizado:**

- **Accuracy (test)**: 0.9766 (97.66%)
- **F1-Score Macro**: 0.97
- **Recall Macro**: 0.98

**Métricas por clase (versión optimizada):**

| Clase | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| Adenocarcinoma | 0.98 | 0.98 | 0.98 | 51 |
| Large Cell Carcinoma | 0.97 | 1.00 | 0.98 | 28 |
| Normal | 0.91 | 1.00 | 0.95 | 10 |
| Squamous Cell Carcinoma | 1.00 | 0.95 | 0.97 | 39 |

**Impacto de la optimización:**
- Se corrigió específicamente el caso de Adenocarcinoma que motivó el cambio
- Mejora en la precisión de Adenocarcinoma (de 0.96 a 0.98 de recall)
- El modelo optimizado logra un mejor equilibrio entre todas las clases

Este resultado demuestra el valor de la **interpretabilidad mediante Grad-CAM** para guiar mejoras específicas y dirigidas del modelo, en lugar de realizar búsquedas exhaustivas de hiperparámetros.

La implementación del modelo optimizado se encuentra en:

- **[`7_Modelo_ResNet50_optimizado.ipynb`](7_Modelo_ResNet50_optimizado.ipynb)**

---

## Interpretabilidad: Grad-CAM

Para garantizar la confiabilidad clínica del modelo, se implementó un análisis completo de interpretabilidad utilizando **Grad-CAM** (Gradient-weighted Class Activation Mapping). Esta técnica permite visualizar las regiones de la imagen que más influyen en las decisiones del modelo.

### Implementación

Se desarrolló un notebook independiente que:

1. **Carga el modelo ResNet50 entrenado** y genera mapas de activación
2. **Identifica errores de clasificación** en el conjunto de test
3. **Compara errores con aciertos** de la misma clase para identificar diferencias en los patrones de activación
4. **Visualiza mapas de calor** superpuestos sobre las imágenes originales

**Capa utilizada**: `layer4` (última capa convolucional de ResNet50), que contiene las características de más alto nivel antes del pooling global.

### Resultados del Análisis

El análisis Grad-CAM reveló información clave:

- **Patrones de activación**: El modelo se enfoca en regiones anatómicamente relevantes de las imágenes
- **Identificación de problemas**: Se detectó que uno de los errores estaba relacionado con variaciones de brillo en las imágenes
- **Guía para optimización**: Esta observación directa permitió realizar una mejora específica y dirigida

### Impacto en el Modelo

Basándose en los hallazgos de Grad-CAM, se optimizó el modelo aumentando el parámetro `brightness` en `ColorJitter` de 0.1 a 0.2, lo que resultó en:

- Corrección del caso problemático identificado
- Mejora del accuracy de 96.88% a 97.66%

Este resultado demuestra el valor de la interpretabilidad no solo para validar el modelo, sino también para guiar mejoras específicas y efectivas.

La implementación completa se encuentra en:

- **[`6_GradCAM_ResNet50.ipynb`](6_GradCAM_ResNet50.ipynb)**

---

### Comparación de Modelos

| Modelo        | Fine-tuning          | Accuracy (test) | Comentarios principales                                   |
|---------------|----------------------|-----------------|-----------------------------------------------------------|
| VGG16         | Solo clasificador    | ≈ 0.65          | Baseline, mejora con dataset limpio respecto al dataset original, y permitió evaluación de transformaciones aplicadas. |
| DenseNet121   | Clasificador + bloque final | ≈ 0.92  | Gran salto en F1-macro y recall con FT parcial.           |
| ResNet50      | Clasificador + bloque final | 0.9688 (96.88%) | Excelente desempeño global; modelo base para optimización. |
| **ResNet50 Optimizado** | Clasificador + bloque final | **0.9766 (97.66%)** | **Mejor desempeño final; optimizado basado en análisis Grad-CAM.** |

Estos resultados muestran cómo, partiendo de un baseline razonable con VGG16, la combinación de **dataset limpio**, **data augmentation específico para imágenes médicas** y **fine-tuning parcial de arquitecturas modernas (DenseNet121 y ResNet50)** permite alcanzar desempeños cercanos al uso clínico, manteniendo un buen equilibrio entre sensibilidad y precisión.

También se demuestra cómo aplicando **Transfer Learning** y realizando un fine-tuning de solamente las últimas capas convolucionales, logramos que la red entrenada originalmente en ImageNet obtenga excelentes resultados en un dataset diferente como lo es el de imágenes médicas.

**Lección clave aprendida**: El uso de técnicas de interpretabilidad como **Grad-CAM** no solo permite validar el comportamiento del modelo, sino que también puede guiar optimizaciones específicas y dirigidas que resultan más efectivas que búsquedas exhaustivas de hiperparámetros, especialmente cuando el modelo ya tiene un rendimiento alto.

## Trabajo a Futuro

A pesar de los buenos resultados obtenidos con DenseNet121 y ResNet50, existen algunas líneas de mejora que podrían aumentar la robustez, interpretabilidad y aplicabilidad clínica del modelo.

### **1. Refinamiento adicional del dataset (`Data_Clean`)**
Si bien se eliminaron duplicados exactos mediante hash MD5, el análisis mostró que aún existen **imágenes "casi duplicadas"**. Según lo observado en el EDA, en algunos casos corresponden a una misma imagen con variaciones mínimas como presencia de marcas o artefactos, o imágenes de cortes consecutivos de un mismo paciente. 

Como trabajo a futuro se propone:

- Aplicar umbrales más estrictos de similitud utilizando el **hash perceptual**.
- Separar explícitamente las imágenes por **paciente**, para evitar posibles fugas de información entre splits.

Esto permitiría construir un dataset más independiente, balanceado y representativo.

### **2. Interpretabilidad mediante Grad-CAM**

Para modelos aplicados a imágenes médicas es fundamental comprender **qué regiones de la imagen utiliza el modelo** para tomar decisiones. Esta funcionalidad ha sido **implementada y aplicada exitosamente**.

**Implementación realizada:**

Se desarrolló un análisis completo de interpretabilidad usando **Grad-CAM** (Gradient-weighted Class Activation Mapping) para el modelo ResNet50:

- **Técnica**: Grad-CAM aplicado a la capa `layer4` (última capa convolucional)
- **Análisis de errores**: Visualización de dónde se enfoca el modelo cuando comete errores
- **Comparación con aciertos**: Análisis comparativo entre errores y predicciones correctas de la misma clase
- **Superposición de mapas de calor**: Visualización de regiones de alta activación sobre imágenes originales

**Resultados y aplicaciones:**

1. **Identificación de patrones problemáticos**: El análisis Grad-CAM reveló que uno de los errores (Adenocarcinoma clasificado como Large Cell Carcinoma) estaba relacionado con variaciones de brillo en las imágenes.

2. **Optimización dirigida**: Esta observación guió una modificación específica en el preprocesamiento (aumento de `brightness` en `ColorJitter` de 0.1 a 0.2), que resultó en una mejora del accuracy de 96.88% a 97.66%.

3. **Validación clínica**: Los mapas de calor permiten verificar que el modelo se enfoca en regiones anatómicamente relevantes, aumentando la confiabilidad clínica del sistema.

La implementación completa se encuentra en:

- **[`6_GradCAM_ResNet50.ipynb`](6_GradCAM_ResNet50.ipynb)**

**Beneficios obtenidos:**

- Verificación de que el modelo se enfoca en regiones relevantes
- Identificación de características problemáticas (brillo, contraste)
- Guía para mejoras específicas y dirigidas del modelo
- Aumento de la confiabilidad clínica mediante interpretabilidad

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
├── 4_Modelo_DenseNet121.ipynb           # Modelo DenseNet121 con dataset limpio
├── 5_Modelo_ResNet50.ipynb              # Modelo ResNet50 con dataset limpio
├── 6_GradCAM_ResNet50.ipynb             # Análisis de interpretabilidad con Grad-CAM
├── 7_Modelo_ResNet50_optimizado.ipynb   # ResNet50 optimizado basado en Grad-CAM
├── Data/                                # Dataset de imágenes CT original
│   ├── train/                           # Conjunto de entrenamiento
│   ├── valid/                           # Conjunto de validación
│   └── test/                            # Conjunto de prueba
├── Data_Clean/                          # Dataset limpio sin duplicados
│   ├── train/                           # Conjunto de entrenamiento
│   ├── valid/                           # Conjunto de validación
│   ├── test/                            # Conjunto de prueba
│   ├── vgg16_baseline_best.pth          # Modelo VGG16 entrenado
│   ├── DenseNet121_best.pth             # Modelo DenseNet121 entrenado
│   └── ResNet50_best.pth                # Modelo ResNet50 optimizado (final)
├── main.py                              # Script principal
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

## Hallazgos Principales del EDA

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
