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

## Ejercicio 2: Robot repartidor de galletas

Se tiene un robot móvil encargado de repartir galletas a un grupo de ingenieros de diseño de robots, quienes se encuentran distribuidos dentro de una sala cuadrada. Cada vez que un ingeniero recibe una galleta, el sistema gana puntos. El objetivo es programar el recorrido y comportamiento del robot mediante programacion genetica, de tal forma que se maximice la cantidad de galletas entregadas de manera eficiente y equitativa.

El comportamiento del robot se modela como un programa (árbol GP) que, a partir de información sensorial del entorno, decide la acción a ejecutar en cada paso de tiempo.

- Sala cuadrada de tamaño NxN.
- Ingenieros ubicados en posiciones fijas dentro de la sala.
- El robot inicia en el centro del entorno.
- Horizonte de simulación limitado a un número máximo de pasos.
- En cada paso, el robot ejecuta una acción y el entorno se actualiza.

La salida del árbol GP es un valor numérico real que se transforma en una acción discreta:

| Acción | Descripción |
|------|------------|
| 0 | Mover al norte |
| 1 | Mover al sur |
| 2 | Mover al oeste |
| 3 | Mover al este |
| 4 | Entregar galleta (`DELIVER`) |

Si el robot ejecuta la acción `DELIVER` y se encuentra en la misma celda que un ingeniero, se entrega una galleta y se obtiene un punto.

Los terminales representan la información sensorial disponible para el robot y constantes numéricas:

- `dx`: diferencia en el eje X entre el robot y el ingeniero más cercano (normalizada).
- `dy`: diferencia en el eje Y entre el robot y el ingeniero más cercano (normalizada).
- `dist`: distancia al ingeniero más cercano (normalizada).
- `remaining`: fracción de ingenieros que aún no han recibido galleta.
- `tleft`: fracción de tiempo restante en la simulación.
- Constantes: `0.0`, `1.0`, `-1.0`, `0.5`.

Cada individuo de la población es un arbol GP que:
- recibe como entrada los cinco sensores (`dx`, `dy`, `dist`, `remaining`, `tleft`),
- produce como salida un valor real,
- dicho valor se transforma en una acción discreta mediante: action = int(abs(output)) % 5

### Función de aptitud
La calidad de cada individuo se evalúa simulando un episodio completo del robot. La función de aptitud busca maximizar las entregas y penalizar comportamientos ineficientes:
fitness = total_deliveries
- α · varianza_entregas
- β · idle_steps
El objetivo es maximizar el valor de la funcion de aptitud.

Parámetros usados:
- Población: 150 individuos
- Generaciones: 25
- Tamaño de la sala: 7×7
- Pasos máximos: 50

Ejecutar ag_robot_galletas.ipynb
<img width="576" height="70" alt="image" src="https://github.com/user-attachments/assets/1662b5cd-fa95-464c-ad7e-45852ae98c28" />

El algoritmo converge rápidamente hacia una solución estable, sin embargo, el comportamiento aprendido solo logra realizar una entrega. Esto sugiere que el algoritmo cayó en un óptimo local, favoreciendo soluciones simples que obtienen una recompensa mínima sin explorar estrategias más complejas de reparto.
