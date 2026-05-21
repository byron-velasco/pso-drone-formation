# PSO Drone Formation — Triangle & H Configurations

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-scientific-blue?logo=numpy&logoColor=white)
![Algorithm](https://img.shields.io/badge/Algorithm-PSO_Metaheuristic-purple)
![License](https://img.shields.io/badge/License-MIT-green)

> Optimización por Enjambre de Partículas (PSO) para posicionamiento coordinado de flota multi-drone.  
> Formaciones triangular y en H con función fitness que penaliza colisiones y violaciones de límites.

---

## ¿Qué hace este proyecto?

Aplica PSO (Particle Swarm Optimization) para resolver el problema de **posicionamiento coordinado de múltiples drones** en formaciones geométricas específicas. Cada partícula del enjambre representa una configuración posible de la flota; la función fitness guía la convergencia hacia formaciones válidas, penalizando cualquier configuración donde los drones colisionen entre sí o excedan los límites del espacio de operación.

**Formaciones implementadas:**
- **Triángulo** — distribución equilátara de la flota con separación mínima garantizada
- **H** — formación en dos columnas con crossbar central, útil para cobertura de área

---

## ¿Por qué importa?

Este proyecto demuestra la capacidad de aplicar algoritmos bio-inspirados a problemas de optimización con restricciones múltiples — una habilidad directamente transferible a planificación de rutas, logística y sistemas autónomos.

Lo que se trabaja aquí:

- **Diseño de función fitness con restricciones:** no basta minimizar la distancia a la formación objetivo; hay que penalizar colisiones y violaciones de límites de forma que el optimizador explore el espacio de soluciones válidas sin colapsar a soluciones triviales
- **Dinámica de enjambre calibrada:** los hiperparámetros de PSO (inercia, coeficientes cognitivo y social) determinan el balance entre exploración y explotación — calibrarlos incorrectamente hace que el enjambre converja prematuramente o no converja
- **Convergencia verificable:** las curvas de fitness por iteración permiten diagnosticar si el algoritmo encontró un óptimo real o quedó atrapado en un mínimo local

**Hallazgo central:** la función fitness es el componente más crítico del sistema — una penalización mal calibrada produce formaciones que minimizan el error geométrico pero violan las restricciones operativas.

---

## Algoritmo PSO

```
Inicializar N partículas con posiciones y velocidades aleatorias
    ↓
Para cada iteración:
    Evaluar fitness de cada partícula (distancia a formación + penalizaciones)
    Actualizar mejor posición personal (pbest)
    Actualizar mejor posición global (gbest)
    Actualizar velocidad: v = w·v + c1·r1·(pbest - x) + c2·r2·(gbest - x)
    Actualizar posición: x = x + v
    ↓
Convergencia → configuración óptima de la flota
```

| Parámetro | Rol |
|-----------|-----|
| `w` — inercia | Controla el momentum de cada partícula |
| `c1` — coeficiente cognitivo | Atracción hacia el mejor histórico personal |
| `c2` — coeficiente social | Atracción hacia el mejor histórico global |

---

## Función fitness

La función fitness evalúa simultáneamente tres componentes:

| Componente | Descripción |
|-----------|-------------|
| Error de formación | Distancia euclidiana de cada drone a su posición objetivo en la formación |
| Penalización por colisión | Activada cuando la distancia entre cualquier par de drones cae bajo el umbral mínimo |
| Penalización por límites | Activada cuando cualquier drone excede el espacio de operación definido |

---

## Estructura del repositorio

```
pso-drone-formation/
│
├── PSO_Drones.ipynb   ← Notebook principal (Colab-ready)
├── README.md
└── requirements.txt
```

---

## Instalación y ejecución

```bash
# Opción recomendada — Google Colab
# Abrir PSO_Drones.ipynb en Colab y ejecutar todas las celdas

# Local
git clone https://github.com/byron-velasco/pso-drone-formation.git
cd pso-drone-formation
pip install -r requirements.txt
jupyter notebook "PSO_Drones.ipynb"
```

**requirements.txt**
```
numpy
matplotlib
scikit-fuzzy
```

---

## Decisiones metodológicas

**¿Por qué PSO y no un algoritmo exacto?**  
El posicionamiento coordinado de múltiples drones con restricciones de colisión y límites es un problema no convexo con espacio de soluciones combinatorio. Los algoritmos exactos (programación lineal, gradiente) no escalan bien con el número de drones ni manejan restricciones discontinuas. PSO explora el espacio de soluciones de forma paralela y tolera funciones fitness no diferenciables.

**¿Por qué penalizar en lugar de restringir?**  
Implementar restricciones duras en PSO requiere reparar partículas inválidas en cada iteración, lo que complica el algoritmo. Convertir las restricciones en penalizaciones suaves integradas en el fitness mantiene la implementación simple y permite al optimizador explorar los bordes del espacio válido antes de converger.

**¿Por qué dos formaciones distintas?**  
Triángulo y H tienen propiedades geométricas diferentes — una es compacta y simétrica, la otra es extendida con estructura interna. Implementar ambas con el mismo optimizador valida que el diseño del sistema es genérico y no dependiente de una geometría específica.

---

## Licencia

MIT — código libre para uso, modificación y distribución.

