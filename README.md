Sistema de Detección de Patrones y Clasificación de Pacientes con Diabetes

Proyecto académico — Universidad Tecnológica de Panamá (UTP) Facultad de Ingeniería de Sistemas Computacionales 

📋 Descripción

Un sistema de salud necesita analizar automáticamente miles de registros de pacientes para detectar patrones relacionados con la diabetes. Este proyecto implementa una solución que:

Procesa y valida los datos de pacientes
Identifica inconsistencias en los registros
Analiza estadísticamente los factores clínicos asociados a la diabetes
Genera un sistema de puntuación (score) de riesgo
Clasifica pacientes nuevos a partir de reglas y umbrales estadísticos

Restricción de diseño: todo el sistema usa únicamente reglas y métodos estadísticos (pruebas Chi-cuadrado, Odds Ratio, regresión logística y lineal, percentiles). No se utilizan modelos de machine learning de caja negra (SVM, Random Forest, redes neuronales, etc.).

📊 Dataset

Fuente: BRFSS 2015 (Behavioral Risk Factor Surveillance System)

Archivo: diabetes_012_health_indicators_BRFSS2015.csv
253,680 registros × 22 variables
Variable objetivo Diabetes_012: 0 = sin diabetes, 1 = prediabetes, 2 = diabetes
Variables predictoras: presión alta, colesterol alto, IMC, tabaquismo, actividad física, salud general, edad, entre otras
🧩 Metodología
1. Detección de inconsistencias (reglas)

Validación de rangos válidos por variable, detección de outliers en IMC mediante rango intercuartílico (IQR), identificación de registros duplicados y de contradicciones lógicas entre variables (ej. CholCheck=0 con HighChol=1).

2. Análisis estadístico de factores clínicos

Prueba Chi-cuadrado y cálculo de Odds Ratio para cada factor clínico binario frente a la variable objetivo, con el fin de cuantificar su asociación con la diabetes.

3. Sistema de puntuación

Se usa una regresión logística únicamente para estimar el peso (coeficiente β) de cada factor. Estos coeficientes se convierten en una tabla de puntos interpretable, siguiendo la misma lógica de scores clínicos reales (FINDRISC, Framingham).

4. Clasificación por umbrales

Los pacientes se agrupan en Bajo / Moderado / Alto riesgo según percentiles (P33/P66) de la distribución del score — una regla estadística, no un clasificador de ML.

5. Regresión lineal vs. logística

Se compara el modelo lineal (OLS) contra el logístico: la regresión lineal predice probabilidades inválidas (fuera de [0,1]) en ~15% de los casos, lo que justifica el uso de la logística para el score y el modelo final.

6. Modelo predictivo

Validación con partición entrenamiento/prueba (80/20): métricas de accuracy, sensibilidad, especificidad y AUC-ROC, con análisis del efecto de mover el umbral de decisión (0.5 → 0.3) para priorizar la detección de casos reales en un contexto de salud.

📈 Resultados clave
Categoría de riesgo	% real con diabetes/prediabetes
Bajo riesgo	1.8%
Riesgo moderado	9.3%
Alto riesgo	32.7%
AUC del modelo predictivo: 0.813
Factores de mayor riesgo: presión alta (OR=4.78), colesterol alto (OR=3.24)
Factores protectores: actividad física (OR=0.50), consumo de alcohol* (OR=0.41)

statsmodels (regresión logística y lineal)
scikit-learn (partición de datos y métricas)
