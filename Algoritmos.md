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

## 🏢 Ejemplo 1: Empresa con siglas vs nombre extendido (con siglas y razón social larga)

**Cadena A:**  
F A I P FABRICA AMERICANA INDUSTRIALIZADORA DE PAPELES S A I Y C  
**Cadena B:**  
FAIP S.A.I.C

| Algoritmo         | Valor    |
|-------------------|----------|
| PrevLevenshtein   | 90.00%   |
| Levenshtein       | 15.79%   |
| Jaccard           | 9.09%    |
| N-Gram            | 14.62%   |
| QGram (2)         | 25.00%   |
| QGram (3)         | 16.13%   |
| Cosine            | 24.60%   |

### 🧠 Interpretación

El valor de **PrevLevenshtein** (90%) muestra una fuerte coincidencia basada en una versión anterior del algoritmo, probablemente más tolerante con substrings, siglas y orden flexible. En cambio, la versión moderna de **Levenshtein** reduce la similitud a **15.79%**, lo que evidencia que penaliza fuertemente las diferencias de longitud y los caracteres intermedios.

Los métodos como **Jaccard**, **N-Gram** y **QGram (3)** ofrecen valores muy bajos, ya que dependen de coincidencias exactas de fragmentos contiguos, lo cual es poco efectivo en casos con siglas o abreviaciones.

El resultado de **Cosine** y **QGram (2)** es moderado, mostrando que al considerar frecuencias y pares de caracteres se logra una aproximación más equilibrada, aunque todavía distante de la percepción humana de equivalencia.

Este ejemplo destaca cómo distintas variantes del mismo algoritmo pueden producir resultados drásticamente diferentes, y cómo la elección de métrica puede impactar significativamente la detección de entidades similares en datos reales.

---

## 👨‍⚖️ Ejemplo 2: Nombre de estudio jurídico con espacio omitido

**Cadena A:**  
ESTUDIO O FARRELL SOCIEDAD COLECTIVA  
**Cadena B:**  
ESTUDIO OFARRELL

| Algoritmo         | Valor    |
|-------------------|----------|
| PrevLevenshtein   | 93.75%   |
| Levenshtein       | 55.17%   |
| Jaccard           | 41.38%   |
| N-Gram            | 52.87%   |
| QGram (2)         | 65.12%   |
| QGram (3)         | 58.54%   |
| Cosine            | 61.72%   |

### 🧠 Interpretación

El algoritmo **PrevLevenshtein** muestra un alto nivel de similitud (**93.75%**), capturando correctamente que la única diferencia significativa es el espacio omitido entre “O” y “FARRELL”, y una extensión común como “SOCIEDAD COLECTIVA” que no afecta la identidad principal.

Los algoritmos basados en fragmentos como **QGram** y **Cosine** también manejan bien esta variación, con valores por encima del 60%, ya que toleran diferencias mínimas en espaciado o segmentación.

**Levenshtein** moderno baja a **55.17%** por penalizar carácter a carácter, incluso por un solo espacio, mientras que **Jaccard** se ve más afectado al tratar los textos como conjuntos disjuntos de palabras, perdiendo fuerza ante la omisión de un token.

Este caso evidencia cómo pequeños cambios en la segmentación (espacios, puntuación) pueden tener impacto desproporcionado en algunos algoritmos, mientras que otros mantienen una mejor robustez semántica.

---

## 🧍 Ejemplo 3: Nombre de persona mal tipeado

**Cadena A:**  
ERBRENEE  
**Cadena B:**  
ERB RENEE

| Algoritmo         | Valor    |
|-------------------|----------|
| PrevLevenshtein   | 50.00%   |
| Levenshtein       | 88.89%   |
| Jaccard           | 44.44%   |
| N-Gram            | 81.48%   |
| QGram (2)         | 80.00%   |
| QGram (3)         | 61.54%   |
| Cosine            | 61.72%   |

### 🧠 Interpretación

En este caso, la cadena presenta una omisión de espacio, típica de errores de tipeo entre nombres y apellidos. El algoritmo **Levenshtein** moderno logra un excelente desempeño (**88.89%**) al evaluar la transformación mínima entre ambas cadenas.

Curiosamente, **PrevLevenshtein** da un valor bajo (**50.00%**), lo que sugiere que su versión antigua penaliza más la distancia por la concatenación sin espacio.

**N-Gram** y **QGram (2)** también ofrecen valores altos (**81.48%** y **80.00%**, respectivamente), ya que permiten comparar secuencias con tolerancia a desplazamientos leves. Esto indica que trabajan bien en nombres cortos con errores menores.

Por su parte, **Jaccard** sufre al tratar los tokens como conjuntos, y su resultado (**44.44%**) refleja la pérdida de correspondencia debido a la segmentación incorrecta.

Este ejemplo muestra cómo algoritmos sensibles a la distancia de edición o fragmentos flexibles son más eficaces para capturar errores simples de tipeo en nombres personales.

---

## 🏭 Ejemplo 4: Empresa con nombre abreviado

**Cadena A:**  
`ADECO AGROPECUARIA SA`  
**Cadena B:**  
`ADECO ASA`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 90.00%   | ✅ Excelente |
| QGram 3     | 42.86%   | ❌ Bajo |
| N-Gram      | 45.00%   | ❌ Bajo |
| Jaccard     | 28.57%   | ❌ Muy bajo |
| Cosine      | 66.82%   | 🔸 Aceptable |

**Interpretación:**  
**Levenshtein** logra un valor alto reconociendo "ADECO" y el parecido entre "AGROPECUARIA SA" y "ASA".  
**Cosine** también tolera bien las abreviaciones.  
QGram, N-Gram y Jaccard muestran menor desempeño por comparar tokens desalineados o distintos.

---

## 🏪 Ejemplo 5: Nombre con errores tipográficos y mayúsculas

**Cadena A:**  
`SUPERMERCADO EL DORADO`  
**Cadena B:**  
`Supermercado Eldorado`

| Algoritmo   | Valor    | Evaluación |
|-------------|----------|------------|
| Levenshtein | 88.89%   | ✅ Excelente |
| QGram 3     | 72.73%   | 🔸 Bueno |
| N-Gram      | 72.73%   | 🔸 Bueno |
| Jaccard     | 60%      | 🔸 Regular |
| Cosine      | 75.32%   | ✅ Buena |

**Interpretación:**  
Errores menores de espacios y mayúsculas afectan poco a Levenshtein y Cosine.  
QGram y N-Gram responden bien al mantener fragmentos similares.  
Jaccard, más estricto con tokens, baja su puntuación.

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
