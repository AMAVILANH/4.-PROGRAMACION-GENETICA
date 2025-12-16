# Programacion genetica – Modulo 4
## Integrantes
- Juan David Meza
- Andres Mauricio Avilan

## Ejercicio 1: BCD → 7-segmentos
Se sintetizan las funciones booleanas que controlan los 7 segmentos (a, b, c, d, e, f, g) de un display de 7 segmentos a partir de la entrada BCD de 4 bits (bits x3 x2 x1 x0). Para ello se utiliza la librería **DEAP** en Python para evolucionar expresiones booleanas (árboles) que aproximen la verdad deseada por cada segmento en los dígitos válidos 0..9. DEAP es una librería ampliamente usada para GP en Python. 

- **Terminales (T)**:
  - Entradas: `x0`, `x1`, `x2`, `x3`
  - Constantes: `0`, `1`
- **Funciones primitivas (F)**:
  - `AND(a,b)` — conjunción (binaria).
  - `OR(a,b)` — disyunción (binaria).
  - `XOR(a,b)` — XOR (binaria).
  - `NOT(a)` — negación (unaria).
  - (Opcional: `NAND`, `NOR`, `IF-THEN-ELSE` o más primitivas según se desee).
- **Función de aptitud**:
  - Para cada individuo (árbol candidato) evaluamos su salida en las 10 entradas válidas (BCD 0000..1001 → decimal 0..9).
  - **fitness** = número de aciertos correctos en los 10 patrones — penalización por tamaño para favorecer circuitos pequeños:
    ```
    fitness = correct_count - alpha * size(individual)
    ```
    donde `alpha` es un coeficiente de parsimonia (por ejemplo 0.01).
  - Se maximiza `fitness`. El objetivo principal es obtener `correct_count == 10` (exactitud total sobre 0..9) con mínima complejidad.

Ejecutar ag_bcd_7seg.ipynb
<img width="400" height="124" alt="image" src="https://github.com/user-attachments/assets/d0e733a9-f2b8-406d-a6a7-b8e4b0d36d90" />
