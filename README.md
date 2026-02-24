# Cronogramas PERT / Gantt — Proyecto I: Introducción a la IA

Implementación de diagramas **PERT** y **Gantt** en Python para la planificación del *Ciclo de vida del software* (apartado 7 del proyecto). Se incluyen dos versiones: una introducción básica en Jupyter Notebook y una implementación avanzada con análisis de restricciones de recursos.

---

## Estructura del repositorio

```
cronograma_limpio/
├── cronograma_simple/
│   └── ejercicio_1.ipynb          # Notebook introductorio (ejercicio guiado)
└── cronograma_complejo/
    ├── index.py                   # PERT + Gantt sin restricciones de recursos
    ├── index_recursos.py          # PERT + Gantt con restricciones de recursos
    ├── ANALISIS_IMPACTO_RECURSOS.md  # Análisis comparativo de ambos escenarios
    ├── images-no-requirements/    # Diagramas generados sin restricciones
    │   ├── gantt_izquierda.html
    │   ├── gantt_centrado.html
    │   └── gantt_derecha.html
    └── images-with-requirements/  # Diagramas generados con restricciones
        ├── gantt_izquierda.html
        ├── gantt_centrado.html
        └── gantt_derecha.html
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

**Archivo:** `cronograma_simple/ejercicio_1.ipynb`

Notebook guiado que implementa paso a paso el algoritmo PERT/CPM sobre un conjunto de tareas de ejemplo:

1. Definición manual de tareas con duración y dependencias.
2. Cálculo de **IC** (inicio temprano), **TC** (fin temprano), **TL** (fin tardío) e **IL** (inicio tardío) mediante funciones iterativas.
3. Cálculo de **holguras** e identificación del **camino crítico**.
4. Visualización del grafo PERT con `networkx` + `matplotlib`, resaltando el camino crítico en rojo.
5. Diagrama de Gantt interactivo con `plotly.express`, diferenciando tareas críticas y no críticas por color.

El notebook incluye además respuestas a preguntas de análisis (qué ocurre si una tarea se retrasa, cuándo cambia el camino crítico, etc.).

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

**Duración total del proyecto: 13 semanas.**  
**Camino crítico:** B → D → E → F → G → I → J → K → N → M (10 tareas).

### `index_recursos.py` — Con restricciones de recursos

Extiende el modelo anterior añadiendo disponibilidad de recursos:

| Recurso | Disponible desde |
|---|---|
| Servidor GPU | Semana 5 |
| Responsable Web | Semana 0 |
| Dataset Limpio | Semana 3 |

Ejecuta **dos pasadas** (sin y con restricciones) y guarda los diagramas en `images-no-requirements/` e `images-with-requirements/` respectivamente.

**Duración total CON restricciones: 17 semanas (+4 semanas, +30.8%).**  
**Camino crítico CON restricciones:** C — Documentación técnica (1 sola tarea crítica).

---

## Análisis de impacto de recursos

Consulta [`cronograma_complejo/ANALISIS_IMPACTO_RECURSOS.md`](cronograma_complejo/ANALISIS_IMPACTO_RECURSOS.md) para el análisis detallado, que incluye:

- Comparativa de tiempos y holguras por tarea entre ambos escenarios.
- Identificación del **cuello de botella** (Dataset Limpio bloquea la Documentación Técnica 5 semanas).
- Recomendaciones estratégicas para recuperar hasta 4 semanas de duración.

---

## Ejecución

```bash
# Cronograma complejo sin restricciones de recursos
cd cronograma_complejo
python index.py

# Cronograma complejo con restricciones de recursos
python index_recursos.py
```

Los diagramas Gantt interactivos (`.html`) se pueden abrir directamente en cualquier navegador.
