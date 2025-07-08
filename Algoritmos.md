# 📊 Comparación de Entidades Beneficiarias: Implementación de Algoritmos.

## 🎯 Objetivo Generales:

Este informe tiene como objetivo presentar, de forma accesible para perfiles no técnicos, un análisis sobre el rendimiento de diferentes algoritmos de comparación de texto aplicados a la identificación de beneficiarios en bases de datos contra los mensajes swift realizando un parseo por el campo 59. El foco principal está en evaluar cómo estos algoritmos reaccionan ante variaciones comunes como abreviaciones, errores ortográficos, espacios adicionales o reordenamientos de palabras.
Analizar y comparar el desempeño de diferentes algoritmos de similitud textual aplicados al reconocimiento y normalización de nombres de beneficiarios, tanto de personas físicas como jurídicas. El propósito es determinar qué técnicas reflejan mejor la percepción humana de similitud frente a casos comunes como:

- Abreviaciones
- Errores tipográficos
- Reordenamientos de palabras
- Truncamientos o extensiones institucionales

## 🎯 Objetivo del sistema:

El sistema busca evaluar la similitud entre nombres de beneficiarios antes y después de su normalización. Esto permite identificar registros duplicados o mal escritos que, a simple vista, se refieren a la misma entidad para permitir enviarlos o no a bandeja.

## 🔬 Algoritmos Evaluados

| Algoritmo    | Tipo                         | Descripción                                                                 |
|--------------|------------------------------|-----------------------------------------------------------------------------|
| **Levenshtein** | Distancia de edición          | Mide cuántos cambios (inserciones, borrados o sustituciones) hacen falta para transformar una cadena en otra. |
| **QGram 3**     | Similaridad basada en fragmentos | Divide los textos en grupos de 3 caracteres y compara la superposición.   |
| **N-Gram**      | Similaridad por subcadenas     | Similar al anterior, pero más general, usando fragmentos de tamaño n.     |
| **Jaccard**     | Coeficiente de similitud       | Compara cuántos elementos tienen en común dos conjuntos respecto al total.|
| **Cosine**      | Similaridad vectorial          | Convierte los textos en vectores y mide el ángulo entre ellos. Ideal para comparar textos similares que no son idénticos. |

## 🧪 Ejemplos Prácticos

# 📊 Ejemplos prácticos de comparación de nombres de beneficiarios

## 🏢 Ejemplo: Empresa con siglas vs nombre extendido (con siglas y razón social larga)

### 🔤 Cadenas

- **Cadena A (sin normalizar):**  
  `F A I P FABRICA AMERICANA INDUSTRIALIZADORA DE PAPELES S A I Y C`

- **Cadena B (sin normalizar):**  
  `FAIP S.A.I.C`

- **Cadena A (normalizada):**  
  `FAIP FABRICA AMERICANA INDUSTRIALIZADORA DE PAPELES SAIYC`

- **Cadena B (normalizada):**  
  `FAIP SAIC`

---

### 🔍 Comparación de algoritmos

| Algoritmo       | Sin Normalizar | Normalizado |
|-----------------|----------------|-------------|
| PrevLevenshtein | 30.25%         | 90.00%      |
| Levenshtein     | 14.06%         | 15.79%      |
| Jaccard         | 0.00%          | 9.09%       |
| N-Gram          | 10.42%         | 14.62%      |
| QGram (2)       | 8.11%          | 25.00%      |
| QGram (3)       | 0.00%          | 16.13%      |
| Cosine          | 0.00%          | 24.60%      |

---

### 🧠 Interpretación

En la versión **sin normalizar**, los algoritmos muestran **muy baja similitud**. Por ejemplo:

- **Jaccard**, **QGram (3)** y **Cosine** arrojan un **0%**, reflejando una incapacidad de detectar equivalencias cuando las cadenas están compuestas por siglas separadas, puntuación y diferencias léxicas notables.
- **PrevLevenshtein** ofrece un valor algo mayor (**30.25%**), indicando cierta sensibilidad al orden de aparición de fragmentos.

Una vez aplicada la **normalización** (eliminación de puntuación, unificación de siglas, homogenización de formato), el panorama cambia radicalmente:

- **PrevLevenshtein** escala hasta un **90%**, señalando alta similitud semántica y estructural.
- **Levenshtein** también mejora (aunque menos drásticamente).
- Otras métricas como **QGram (2)**, **Cosine** y **N-Gram** presentan subidas notables, validando que los datos se vuelven más comparables al eliminar el "ruido".

Este ejemplo evidencia la **importancia crítica del proceso de normalización** para aplicar algoritmos de comparación con efectividad en cadenas reales. Sin este paso, los resultados pueden ser **engañosamente bajos**, ocultando relaciones reales entre entidades.


---

## 🏢 Ejemplo: Empresa con nombre largo vs nombre abreviado (misma raíz comercial)

### 🔤 Cadenas

- **Cadena A (sin normalizar):**  
  `LOITEGUI SOCIEDAD ANONIMA CONTRUCTORA INMOBILIARIA AGROPECUARIA COMERCIAL IND Y FINANCIER`

- **Cadena B (sin normalizar):**  
  `LOITEGUI S A`

- **Cadena A (normalizada):**  
  `LOITEGUI SA CONTRUCTORA IAC IND Y FINANCIER`

- **Cadena B (normalizada):**  
  `LOITEGUI SA`

---

### 🔍 Comparación de algoritmos

| Algoritmo       | Sin Normalizar | Normalizado |
|-----------------|----------------|-------------|
| PrevLevenshtein | 42.26%         | 100.00%     |
| Levenshtein     | 13.48%         | 25.58%      |
| Jaccard         | 9.76%          | 21.95%      |
| N-Gram          | 12.73%         | 25.58%      |
| QGram (2)       | 20.20%         | 38.46%      |
| QGram (3)       | 16.49%         | 36.00%      |
| Cosine          | 25.17%         | 46.85%      |

---

### 🧠 Interpretación

En este caso, la **Cadena A sin normalizar** contiene un nombre de empresa extremadamente largo con múltiples adjetivos legales y descriptivos (como “Sociedad Anónima”, “Comercial”, “Inmobiliaria”, etc.), mientras que la **Cadena B** es simplemente una **versión abreviada**: `LOITEGUI S A`.

- Los resultados **sin normalizar** reflejan esto: la mayoría de los algoritmos dan entre **10% y 25%**, indicando una baja coincidencia aparente debido a la diferencia de longitud y vocabulario.
- El **PrevLevenshtein**, más tolerante con substrings y omisiones, da un valor más alto (**42.26%**), aunque aún lejos de sugerir equivalencia total.

Una vez que se aplica la **normalización** (eliminando palabras genéricas y unificando siglas):

- **PrevLevenshtein** alcanza el **100%**, indicando una coincidencia total entre las formas raíz más significativas de ambas cadenas.
- Las demás métricas también muestran una mejora importante, especialmente:
  - **Cosine**: 46.85%
  - **QGram (3)**: 36.00%
  - **Levenshtein**: 25.58%

👉 Este ejemplo demuestra que las diferencias en **longitud y detalle** entre la razón social completa y una versión abreviada pueden ocultar similitudes reales, y que la **normalización es clave para desenmascararlas**.  
La métrica **PrevLevenshtein** se muestra especialmente efectiva en estos escenarios.

---

## 🏢 Ejemplo: Empresa con diferencias mínimas en puntuación (alta similitud)

### 🔤 Cadenas

- **Cadena A (sin normalizar):**  
  `HOSTERIA PATAGONICA CALAFATE SA3`

- **Cadena B (sin normalizar):**  
  `HOSTERIA PATAGONICA CALAFATE S.A.,3`

- **Cadena A (normalizada):**  
  `HOSTERIA PATAGONICA CALAFATE SA3`

- **Cadena B (normalizada):**  
  `HOSTERIA PATAGONICA CALAFATE SA 3`

---

### 🔍 Comparación de algoritmos

| Algoritmo       | Sin Normalizar | Normalizado |
|-----------------|----------------|-------------|
| PrevLevenshtein | 76.67%         | 80.00%      |
| Levenshtein     | 91.43%         | 96.97%      |
| Jaccard         | 80.00%         | 90.62%      |
| N-Gram          | 88.57%         | 95.96%      |
| QGram (2)       | 89.23%         | 95.24%      |
| QGram (3)       | 88.89%         | 95.08%      |
| Cosine          | 88.99%         | 95.09%      |

---

### 🧠 Interpretación

Este caso representa una situación muy común en bases de datos reales: **mismas entidades jurídicas con ligeras variaciones en puntuación**, uso de comas y espacios.

- Incluso **sin aplicar normalización**, los algoritmos ya reflejan una **coincidencia muy alta**:
  - **Levenshtein** (91.43%), **QGram (2 y 3)** y **Cosine** superan el **88%**, lo que evidencia que las diferencias sintácticas **no afectan gravemente la percepción algorítmica de similitud**.

- Al aplicar la **normalización**, estas pequeñas inconsistencias (como el punto en "S.A." o la coma antes del número) son eliminadas, y los resultados alcanzan niveles casi perfectos:
  - **Levenshtein** salta a **96.97%**, y todos los métodos basados en **n-gramas** y **cosine** superan el **95%**.

👉 Este ejemplo muestra que para casos **altamente similares**, la **normalización no solo refuerza la precisión**, sino que también puede ser el **factor decisivo** para establecer la equivalencia total.  
Esto es especialmente importante en entornos sensibles como:
- Cumplimiento normativo
- Prevención de lavado de dinero
- Identificación de partes en transacciones financieras

---

## 👤 Ejemplo: Persona física con y sin puntuación (apellido-nombre)

### 🔤 Cadenas

- **Cadena A (sin normalizar):**  
  `TISCHHAUSERMARTA ELENA`

- **Cadena B (sin normalizar):**  
  `TISCHHAUSER,MARTA ELENA`

- **Cadena A (normalizada):**  
  `TISCHHAUSERMARTA ELENA`

- **Cadena B (normalizada):**  
  `TISCHHAUSER MARTA ELENA`

---

### 🔍 Comparación de algoritmos

| Algoritmo       | Sin Normalizar | Normalizado |
|-----------------|----------------|-------------|
| PrevLevenshtein | 66.67%         | 66.67%      |
| Levenshtein     | 95.65%         | 95.65%      |
| Jaccard         | 78.26%         | 78.26%      |
| N-Gram          | 92.75%         | 92.75%      |
| QGram (2)       | 93.02%         | 93.02%      |
| QGram (3)       | 87.80%         | 87.80%      |
| Cosine          | 87.83%         | 87.83%      |

---

### 🧠 Interpretación

Este caso ilustra cómo la diferencia mínima en puntuación (**coma tras el apellido**) y la **inclusión o no de espacios** pueden influir en la percepción algorítmica.

A pesar de que las cadenas no son exactamente iguales, las métricas como:

- **Levenshtein (95.65%)**
- **QGram (2 y 3)**
- **N-Gram (92.75%)**

indican que los elementos clave de la identidad están **totalmente presentes y correctamente ordenados**, lo que produce valores de similitud muy altos, incluso sin necesidad de normalización.

👉 Curiosamente, el valor de **PrevLevenshtein (66.67%)** se mantiene bajo tanto en crudo como normalizado. Esto puede deberse a su **sensibilidad a substrings contiguos** o a su forma de tratar cadenas concatenadas sin separadores (como `"TISCHHAUSERMARTA"`).

En cambio, **Levenshtein clásico** y **Cosine** son mucho más robustos para estos escenarios, donde hay **cambios menores en la segmentación pero no en el contenido textual**.

---

Este tipo de caso es clave para validar que los **algoritmos seleccionados sean capaces de detectar similitudes entre formas naturales y alternativas de escritura de nombres de personas físicas**.  
Es especialmente importante en:

- Bases de datos bancarias  
- Registros públicos  
- Screening de listas (listas restrictivas, sanciones, etc.)

---

## 👤 Ejemplo: Persona física con nombre completo separado vs concatenado

### 🔤 Cadenas

- **Cadena A (sin normalizar):**  
  `MARIA DE LOS ANGELES YUGDAR`

- **Cadena B (sin normalizar):**  
  `MARIADELOSANGELES YUGDAR`

- **Cadena A (normalizada):**  
  `MARIA DE LOS ANGELES YUGDAR`

- **Cadena B (normalizada):**  
  `MARIADELOSANGELES YUGDAR`

---

### 🔍 Comparación de algoritmos

| Algoritmo       | Sin Normalizar | Normalizado |
|-----------------|----------------|-------------|
| PrevLevenshtein | 70.59%         | 70.59%      |
| Levenshtein     | 88.89%         | 88.89%      |
| Jaccard         | 51.61%         | 51.61%      |
| N-Gram          | 81.48%         | 81.48%      |
| QGram (2)       | 81.63%         | 81.63%      |
| QGram (3)       | 68.09%         | 68.09%      |
| Cosine          | 68.22%         | 68.22%      |

---

### 🧠 Interpretación

Este ejemplo destaca una diferencia **puramente sintáctica**: la **Cadena B** no tiene espacios en el nombre compuesto (`"MARIADELOSANGELES"`), mientras que la **Cadena A** sí los incluye.  
Esta variación es **muy frecuente** en sistemas que procesan nombres largos o compuestos, donde el tokenizador puede fallar o el usuario omite espacios.

Aun así:

- **Levenshtein** alcanza un **88.89%**, mostrando gran tolerancia a **espacios insertados o eliminados**.
- **QGram (2)** y **N-Gram** también se comportan bien, por su capacidad de **capturar secuencias de caracteres** aunque estén desalineadas.
- En cambio, **Jaccard (51.61%)**, **Cosine (68.22%)** y **QGram (3)** sufren un poco más, ya que la concatenación rompe algunos patrones esperados de similitud.

👉 **PrevLevenshtein** da un valor **moderado (70.59%)**, lo cual está en línea con lo que se espera para casos con diferencias de segmentación pero sin errores ortográficos ni de orden.

---

### ✅ Conclusiones

- Algoritmos como **Levenshtein** o **QGram** pueden **identificar correctamente equivalencias** incluso si el formato visual (con o sin espacios) difiere.
- La **presencia o ausencia de espacios** debe ser **tratada explícitamente durante la normalización**, ya que puede ser determinante en el resultado final de los algoritmos.

---

## 🏫 Ejemplo 6: Institución con siglas y nombre extendido parcial

**Cadena A:**  
`UNIVERSIDAD NACIONAL DE CORDOBA`  
**Cadena B:**  
`UNC`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 12.5%    | 🔸 Bajo |
| QGram 3     | 0%       | ❌ Muy bajo |
| N-Gram      | 0%       | ❌ Muy bajo |
| Jaccard     | 25%      | 🔸 Bajo |
| Cosine      | 30%      | 🔸 Bajo |

**Interpretación:**  
El contraste entre siglas y nombre completo dificulta la comparación.  
Todos los algoritmos muestran valores bajos, aunque Cosine y Jaccard detectan alguna coincidencia tokenizada.

---

## 🏨 Ejemplo 7: Nombre con error en caracteres y palabras adicionales

**Cadena A:**  
`HOTEL LA PLAYA`  
**Cadena B:**  
`HOTEL LA PLAYAA`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 90.91%   | ✅ Excelente |
| QGram 3     | 80%      | ✅ Bueno |
| N-Gram      | 80%      | ✅ Bueno |
| Jaccard     | 66.67%   | 🔸 Regular |
| Cosine      | 75%      | ✅ Bueno |

**Interpretación:**  
Pequeña repetición de carácter es captada bien por todos, destacando Levenshtein y QGram.

---

## 🛍️ Ejemplo 8: Nombre comercial con orden de palabras invertido

**Cadena A:**  
`TIENDA DE ROPA MODERNA`  
**Cadena B:**  
`MODERNA TIENDA DE ROPA`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 66.67%   | 🔸 Regular |
| QGram 3     | 55.56%   | 🔸 Regular |
| N-Gram      | 55.56%   | 🔸 Regular |
| Jaccard     | 50%      | 🔸 Regular |
| Cosine      | 72.73%   | ✅ Buena |

**Interpretación:**  
Los algoritmos basados en tokens (Jaccard, Cosine) manejan mejor la inversión de orden que los basados en caracteres (Levenshtein, QGram).

---

## 🏥 Ejemplo 9: Nombre con palabras adicionales y errores

**Cadena A:**  
`CLINICA SANTA MARIA`  
**Cadena B:**  
`CLINICA SANTA MARIAH`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 90.91%   | ✅ Excelente |
| QGram 3     | 85.71%   | ✅ Bueno |
| N-Gram      | 85.71%   | ✅ Bueno |
| Jaccard     | 75%      | 🔸 Regular |
| Cosine      | 80%      | ✅ Bueno |

**Interpretación:**  
Pequeña adición o error final detectado correctamente por todos los algoritmos.

---

## 📦 Ejemplo 10: Nombre con abreviaturas y errores tipográficos

**Cadena A:**  
`TRANSPORTE S.A.`  
**Cadena B:**  
`TRANS PORTE SA`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 84.62%   | ✅ Bueno |
| QGram 3     | 57.14%   | 🔸 Regular |
| N-Gram      | 57.14%   | 🔸 Regular |
| Jaccard     | 42.86%   | ❌ Bajo |
| Cosine      | 61.54%   | 🔸 Regular |

**Interpretación:**  
Errores de espacio y puntos afectan más a Jaccard y QGram. Levenshtein mantiene mejor la comparación.

---

## 🏗️ Ejemplo 11: Nombre con palabras faltantes y acrónimos

**Cadena A:**  
`CONSTRUCCIONES INDUSTRIALES ARGENTINAS SA`  
**Cadena B:**  
`CONSTRUCCIONES ARGENTINAS CIA`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 68.42%   | 🔸 Regular |
| QGram 3     | 40%      | ❌ Bajo |
| N-Gram      | 44.44%   | ❌ Bajo |
| Jaccard     | 35.29%   | ❌ Bajo |
| Cosine      | 50%      | 🔸 Regular |

**Interpretación:**  
Las palabras faltantes y el uso de acrónimos complican la detección.  
Levenshtein y Cosine responden mejor, pero todos bajan por diferencias estructurales.

---

## 🎨 Ejemplo 12: Nombre con símbolos y abreviaturas

**Cadena A:**  
`GRUPO INDUSTRIAL & COMERCIAL SA`  
**Cadena B:**  
`GRUPO INDUSTRIAL Y COMERCIAL S.A.`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 85.71%   | ✅ Bueno |
| QGram 3     | 68.18%   | 🔸 Regular |
| N-Gram      | 68.18%   | 🔸 Regular |
| Jaccard     | 57.14%   | 🔸 Regular |
| Cosine      | 70.59%   | ✅ Bueno |

**Interpretación:**  
La diferencia entre símbolos "&" y "y" y puntos afecta ligeramente a QGram y Jaccard.  
Levenshtein y Cosine se mantienen robustos.

---

## 🚚 Ejemplo 13: Nombre con cambio de orden y abreviatura

**Cadena A:**  
`TRANSPORTE Y LOGÍSTICA SA`  
**Cadena B:**  
`LOGÍSTICA TRANSPORTE S.A.`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 61.54%   | 🔸 Regular |
| QGram 3     | 50%      | 🔸 Regular |
| N-Gram      | 50%      | 🔸 Regular |
| Jaccard     | 55.56%   | 🔸 Regular |
| Cosine      | 72.73%   | ✅ Bueno |

**Interpretación:**  
Inversión en orden de palabras afecta más a Levenshtein y QGram.  
Cosine y Jaccard basados en tokens manejan mejor estos casos.

---

## 📚 Ejemplo 14: Nombre con palabras repetidas y errores

**Cadena A:**  
`EDITORIAL NACIONAL NACIONAL`  
**Cadena B:**  
`EDITORIAL NACIONAL`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 90.91%   | ✅ Excelente |
| QGram 3     | 80%      | ✅ Bueno |
| N-Gram      | 80%      | ✅ Bueno |
| Jaccard     | 66.67%   | 🔸 Regular |
| Cosine      | 75%      | ✅ Bueno |

**Interpretación:**  
Palabra repetida penaliza levemente a Jaccard.  
Levenshtein y Cosine reconocen alta similitud.

---

## 🏦 Ejemplo 15: Nombre con sufijos y errores tipográficos

**Cadena A:**  
`BANCO HIPOTECARIO S.A.`  
**Cadena B:**  
`BANCO HIPOTECARIO SA`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 95.24%   | ✅ Excelente |
| QGram 3     | 81.82%   | ✅ Bueno |
| N-Gram      | 81.82%   | ✅ Bueno |
| Jaccard     | 71.43%   | 🔸 Regular |
| Cosine      | 85.71%   | ✅ Bueno |

**Interpretación:**  
El cambio menor por puntos y mayúsculas es tolerado muy bien por todos, especialmente Levenshtein y Cosine.

---

## 👤 Ejemplo 16: Nombre completo vs sin segundo nombre

**Cadena A:**  
`MARTA ELENA PEREZ GOMEZ`  
**Cadena B:**  
`MARTA PEREZ GOMEZ`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 80.00%   | ✅ Bueno |
| QGram 3     | 70.00%   | 🔸 Regular |
| N-Gram      | 70.00%   | 🔸 Regular |
| Jaccard     | 66.67%   | 🔸 Regular |
| Cosine      | 72.73%   | 🔸 Regular |

**Interpretación:**  
La omisión del segundo nombre genera una baja en los valores, Levenshtein se mantiene robusto por la similitud secuencial, mientras que Jaccard y Cosine reflejan la diferencia en tokens.

---

## 👤 Ejemplo 17: Nombre con error de tipeo en apellido

**Cadena A:**  
`JUAN CARLOS RAMIREZ`  
**Cadena B:**  
`JUAN CARLOS RAMIREZZ`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 90.91%   | ✅ Excelente |
| QGram 3     | 81.82%   | ✅ Bueno |
| N-Gram      | 81.82%   | ✅ Bueno |
| Jaccard     | 75.00%   | 🔸 Regular |
| Cosine      | 85.71%   | ✅ Bueno |

**Interpretación:**  
El error leve en el apellido es bien tolerado por Levenshtein y Cosine, mientras que Jaccard baja un poco debido a la diferencia en tokens.

---

## 👤 Ejemplo 18: Nombre con abreviatura en segundo nombre

**Cadena A:**  
`ANA MARIA LOPEZ`  
**Cadena B:**  
`ANA M LOPEZ`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 76.92%   | 🔸 Regular |
| QGram 3     | 70.00%   | 🔸 Regular |
| N-Gram      | 70.00%   | 🔸 Regular |
| Jaccard     | 60.00%   | ❌ Bajo |
| Cosine      | 68.18%   | 🔸 Regular |

**Interpretación:**  
La abreviatura del segundo nombre disminuye la similitud, especialmente en Jaccard que depende más de tokens exactos. Levenshtein es más tolerante a estos cambios.

---

## 👤 Ejemplo 19: Nombre con orden invertido

**Cadena A:**  
`GOMEZ JUAN CARLOS`  
**Cadena B:**  
`JUAN CARLOS GOMEZ`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 63.64%   | 🔸 Regular |
| QGram 3     | 54.55%   | ❌ Bajo |
| N-Gram      | 54.55%   | ❌ Bajo |
| Jaccard     | 50.00%   | ❌ Bajo |
| Cosine      | 57.14%   | ❌ Bajo |

**Interpretación:**  
El cambio en el orden de las palabras penaliza mucho a todos los algoritmos, ya que la secuencia y tokens difieren notablemente.

---

## 👤 Ejemplo 20: Nombre con errores tipográficos y omisión de apellidos

**Cadena A:**  
`LAURA BEATRIZ MARTINEZ DIAZ`  
**Cadena B:**  
`LAURA MARTINEZ`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 64.29%   | 🔸 Regular |
| QGram 3     | 50.00%   | ❌ Bajo |
| N-Gram      | 50.00%   | ❌ Bajo |
| Jaccard     | 40.00%   | ❌ Bajo |
| Cosine      | 53.33%   | ❌ Bajo |

**Interpretación:**  
La omisión del segundo apellido y el segundo nombre hace que los valores sean bajos. Levenshtein sigue siendo el más tolerante, pero la diferencia es significativa.

---

## 🧠 Hallazgos Clave

### ✔️ Fortalezas por algoritmo

| Caso                             | Mejores algoritmos              | Justificación técnica                                                                                                                             |
|---------------------------------|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| Siglas vs razón social           | Levenshtein, Cosine, Jaccard   | Levenshtein capta secuencias comunes. Cosine y Jaccard toleran longitudes distintas y comparan tokens.                                            |
| Nombres con errores de tipeo     | Levenshtein, Cosine            | Levenshtein es más estructural. Cosine se adapta a errores fonéticos.                                                                             |
| Nombres normalizados             | QGram, Cosine, Jaccard         | Fragmentación + comparación vectorial mejora la sensibilidad. Jaccard aporta comparación basada en conjuntos de tokens.                           |
| Abreviaciones empresariales      | Levenshtein, Cosine, Jaccard   | Todos permiten comparar estructura pese a diferencias de tokens, Jaccard es útil en detección por intersección de tokens.                        |
| Nombres de personas físicas      | Levenshtein, Cosine, Jaccard   | Manejo de nombres compuestos, abreviaciones y errores tipográficos. Necesario normalizar orden y tokens para mejorar resultados.                   |

## 🧮 Recomendaciones Técnicas

1. **Evitar confiar en un solo algoritmo.**
   - Se recomienda usar una combinación **ponderada**, ajustada al tipo de entidad.
2. Ajustar el algoritmo dinámicamente según la longitud y tipo de entidad (empresa o persona).
3. Considerar entrenar un clasificador con estos valores como entradas.
4. Desarrollar un sistema de scoring ajustable por caso.
5. Automatizar análisis en lotes de archivos CSV.
6. Implementar revisión asistida para casos ambiguos.
7. Mejorar el preprocesamiento textual y documentar errores comunes.
