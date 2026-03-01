# Cronogramas PERT / Gantt — Proyecto I: Introducción a la IA

Implementación de diagramas **PERT** y **Gantt** en Python para la planificación del *Ciclo de vida del software* (apartado 7 del proyecto). Se incluyen dos versiones: una introducción básica en Jupyter Notebook (ejercicios del pdf `Práctica 2`) y una implementación avanzada con análisis de restricciones de recursos.

---

## Estructura del repositorio

```
cronograma_limpio/
├── cronograma_simple/
│   ├── ejercicio_1.ipynb          # PERT/CPM básico — Ejercicio 1: Cronograma 1
│   └── ejercicio_2.ipynb          # PERT/CPM con simulación de recursos — Ejercicio 2: Cronograma 2
└── cronograma_complejo/
    ├── index.py                   # PERT + Gantt sin restricciones de recursos
    ├── index_recursos.py          # PERT + Gantt con restricciones de recursos
    ├── ANALISIS_IMPACTO_RECURSOS.md  # Análisis comparativo de ambos escenarios
    ├── images/                    # Diagramas generados por index.py
    │   ├── cronograma_pert.png
    │   ├── gantt_izquierda.html
    │   ├── gantt_centrado.html
    │   └── gantt_derecha.html
    ├── images-no-requirements/    # Diagramas generados sin restricciones (por index_recursos.py)
    │   ├── cronograma_pert.png
    │   ├── gantt_izquierda.html
    │   ├── gantt_izquierda.png
    │   ├── gantt_centrado.html
    │   ├── gantt_centrado.png
    │   ├── gantt_derecha.html
    │   └── gantt_derecha.png
    └── images-with-requirements/  # Diagramas generados con restricciones (por index_recursos.py)
        ├── cronograma_pert.png
        ├── gantt_izquierda.html
        ├── gantt_izquierda.png
        ├── gantt_centrado.html
        ├── gantt_centrado.png
        ├── gantt_derecha.html
        └── gantt_derecha.png
```

---

## Dependencias

| Librería | Uso |
|---|---|
| `pandas` | Construcción de DataFrames con tiempos y holguras |
| `networkx` | Modelado del grafo de dependencias (PERT) |
| `matplotlib` | Renderizado estático del diagrama PERT |
| `plotly` | Diagramas de Gantt interactivos (HTML) |
| `datetime` | Conversión de semanas a fechas de calendario |
| `os` / `copy` | Gestión de carpetas y copia de estructuras de datos |

Instalación rápida:
```bash
conda install pandas networkx matplotlib plotly kaleido
```

---

## Cronograma Simple

Los dos ejercicios de esta carpeta están resueltos en base a los **ejercicios del PDF** de la práctica `PRÁCTICA 2: Gestión del tiempo, cronogramas`, siguiendo fielmente el orden de sus apartados **a), b), c)…**

### `ejercicio_1.ipynb`

Notebook guiado que implementa paso a paso el algoritmo PERT/CPM sobre el **primer conjunto de tareas del PDF** (tareas A–F):

- **a)** Introducción de los datos de la tabla (duración y dependencias).
- **b)** Cálculo de **IC** (inicio temprano), **TC** (fin temprano), **TL** (fin tardío) e **IL** (inicio tardío) mediante funciones iterativas.
- **c)** Cálculo de **holguras** e identificación del **camino crítico**.
- **d)** Visualización de resultados en un `DataFrame` (Tarea, Duración, IC, TC, IL, TL, Holgura).
- **e)** Diagrama PERT con `networkx` + `matplotlib`, resaltando el camino crítico en rojo.
- **f)** Diagrama de Gantt interactivo con `plotly.express`, diferenciando tareas críticas y no críticas por color.
- **g)** Respuestas a las preguntas de análisis del PDF (efecto de retrasos, cambio del camino crítico, duración mínima, etc.).

### `ejercicio_2.ipynb`

Repite y amplía el análisis del Ejercicio 1 sobre el **segundo conjunto de tareas del PDF** (tareas A–I), incorporando recursos humanos y materiales:

- **a)** Análisis PERT/CPM equivalente al Ejercicio 1 (IC, TC, TL, IL, holguras y camino crítico) con los nuevos datos del PDF.
- **b)** Simulación con **recursos limitados**: estructuras de control (tiempo, tareas en marcha, finalizadas, recursos en uso) y función `puede_empezar()`.
- **c)** Diagrama PERT y Gantt del calendario simulado con restricciones de recursos.
- **d)** Comparativa de duración entre el caso sin y con restricciones de recursos.
- **e)** Respuestas a las preguntas de discusión del PDF (cuello de botella, tarea limitante, GPU vs. personas, efecto de aumentar disponibilidad).

---

## Cronograma Complejo

Modela el **proyecto real de IA** con 14 tareas (A–N), duración en semanas y fecha de inicio el **1 de marzo de 2026**.

### Tareas del proyecto

| ID | Nombre | Duración (sem) | Dependencias |
|---|---|---|---|
| A | Detectar Stakeholders | 1 | — |
| B | Identificar infraestructuras | 1 | — |
| C | Documentación técnica | 8 | — |
| D | Buscar Dataset secundario | 1 | — |
| E | Explorar Dataset | 1 | D |
| F | Unificar datasets | 1 | E |
| G | Definir batches | 1 | F |
| H | Definir Cronograma (PERT/Gantt) | 1 | B |
| I | Entrenar Modelo | 2 | G, H |
| J | Validación cruzada | 1 | I |
| K | Clasificadora CLI | 2 | J |
| L | Desarrollar UI/UX Web | 0.5 | — |
| N | Integrar IA en la Web | 1 | K, L |
| M | Despliegue web | 1 | N |

### `index.py` — Sin restricciones de recursos

Calcula los tiempos PERT mediante **recursión** y genera:

- **`images/cronograma_pert.png`** — Diagrama PERT estático con IC, TC, IL, TL y holgura por nodo. Camino crítico resaltado en rojo.
- **`images/gantt_izquierda.html`** — Gantt con inicio temprano (máxima holgura al final).
- **`images/gantt_centrado.html`** — Gantt con inicio equilibrado (holgura repartida).
- **`images/gantt_derecha.html`** — Gantt con inicio tardío (máxima holgura al inicio).

#### Declaración de datos iniciales
```python
tasks = {
    "A": {"nombre": "Detectar Stakeholders", "duracion": 1, "dependencias": []},
    "B": {"nombre": "Identificar infraestructuras", "duracion": 1, "dependencias": []},
    "C": {"nombre": "Documentación técnica", "duracion": 8, "dependencias": []},
    "D": {"nombre": "Buscar Dataset secundario", "duracion": 1, "dependencias": []},
    "E": {"nombre": "Explorar Dataset", "duracion": 1, "dependencias": ["D"]},
    "F": {"nombre": "Unificar datasets", "duracion": 1, "dependencias": ["E"]},
    "G": {"nombre": "Definir batches", "duracion": 1, "dependencias": ["F"]},
    "H": {"nombre": "Definir Cronograma (PERT/Gantt)", "duracion": 1, "dependencias": [ "B"]},
    "I": {"nombre": "Entrenar Modelo", "duracion": 2, "dependencias": ["G", "H"]},
    "J": {"nombre": "Validación cruzada", "duracion": 1, "dependencias": ["I"]},
    "K": {"nombre": "Clasificadora CLI", "duracion": 2, "dependencias": ["J"]},
    "L": {"nombre": "Desarrollar UI/UX Web", "duracion": 0.5, "dependencias": []},
    "N": {"nombre": "Integrar IA en la Web", "duracion": 1, "dependencias": ["K", "L"]},
    "M": {"nombre": "Despliegue web", "duracion": 1, "dependencias": ["N"]}
}
```

**Duración total del proyecto: 11 semanas.**  
**Camino crítico:** D → E → F → G → I → J → K → N → M (9 tareas).

### `index_recursos.py` — Con restricciones de recursos

Extiende el modelo anterior añadiendo disponibilidad de recursos:

| Recurso | Disponible desde |
|---|---|
| Servidor GPU | Semana 5 |
| Responsable Web | Semana 0 |
| Dataset Limpio | Semana 3 |

Ejecuta **dos pasadas** (sin y con restricciones) y guarda los diagramas en `images-no-requirements/` e `images-with-requirements/` respectivamente.

#### Declaración de datos iniciales
```python
# Diccionario de disponibilidad de recursos (semana en que están disponibles)
disponibilidad_recursos = {
    'Servidor': 5,
    'Responsable Web': 0,
    'Dataset': 3
}

tasks = {
    "A": {"nombre": "Detectar Stakeholders", "duracion": 1, "dependencias": [], "requisitos": []},
    "B": {"nombre": "Identificar infraestructuras", "duracion": 1, "dependencias": [], "requisitos": []},
    "C": {"nombre": "Documentación técnica", "duracion": 8, "dependencias": [], "requisitos": []},
    "D": {"nombre": "Buscar Dataset secundario", "duracion": 1, "dependencias": [], "requisitos": []},
    "E": {"nombre": "Explorar Dataset", "duracion": 1, "dependencias": ["D"], "requisitos": ["Dataset"]},
    "F": {"nombre": "Unificar datasets", "duracion": 1, "dependencias": ["E"], "requisitos": ["Dataset"]},
    "G": {"nombre": "Definir batches", "duracion": 1, "dependencias": ["F"], "requisitos": []},
    "H": {"nombre": "Definir Cronograma (PERT/Gantt)", "duracion": 1, "dependencias": [ "B"], "requisitos": []},
    "I": {"nombre": "Entrenar Modelo", "duracion": 2, "dependencias": ["G", "H"], "requisitos": ["Servidor", "Dataset"]},
    "J": {"nombre": "Validación cruzada", "duracion": 1, "dependencias": ["I"], "requisitos": ["Servidor", "Dataset"]},
    "K": {"nombre": "Clasificadora CLI", "duracion": 2, "dependencias": ["J"], "requisitos": []},
    "L": {"nombre": "Desarrollar UI/UX Web", "duracion": 0.5, "dependencias": [], "requisitos": ["Responsable Web"]},
    "N": {"nombre": "Integrar IA en la Web", "duracion": 1, "dependencias": ["K", "L"], "requisitos": ["Responsable Web"]},
    "M": {"nombre": "Despliegue web", "duracion": 1, "dependencias": ["N"], "requisitos": ["Responsable Web"]}
}
```

**Duración total CON restricciones: 13 semanas (+2 semanas, +18.2%).**  
**Camino crítico CON restricciones:** E → F → G → I → J → K → M → N (8 tareas).


## Ejecución

```bash
# Cronograma complejo sin restricciones de recursos
cd cronograma_complejo
python index.py

# Cronograma complejo con restricciones de recursos
python index_recursos.py
```

Los diagramas Gantt interactivos (`.html`) se pueden abrir directamente en cualquier navegador.
