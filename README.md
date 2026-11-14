# Framework de cumplimiento de la Ley 21.719 mediante un agente RAG para PyMEs chilenas

Código y resultados de la tesis de Ingeniería en Información y Control de Gestión y Magíster en Gestión de Negocios Digitales y Analítica de Datos (Facultad de Administración y Economía).

## De qué trata

Un agente de **Generación Aumentada por Recuperación (RAG)** que se ejecuta de forma local y responde consultas de PyMEs sobre la Ley 21.719 de protección de datos personales. Cada respuesta cita fuentes verificables del texto legal. El agente combina:

- un corpus de 6 normas chilenas (Leyes 21.719, 19.628, 19.496, 20.285 y 20.416, y los capítulos I y III de la Constitución), segmentado jerárquicamente por ley, artículo e inciso (1.439 fragmentos);
- **expansión de vocabulario**, que traduce el lenguaje coloquial al jurídico;
- **búsqueda híbrida** (BGE-M3 + BM25), fusión por rangos recíprocos y reordenamiento con `bge-reranker-v2-m3`;
- generación local con modelos abiertos (Qwen3 8B y Gemma 4) mediante Ollama;
- **auditoría automática de citas** a nivel de fuente y de subdivisión.

## Notebooks

| Notebook | Contenido | Sección de la tesis |
|---|---|---|
| [`RAG_Leyes_PYME.ipynb`](RAG_Leyes_PYME.ipynb) | El agente: conversión y limpieza de los PDF, segmentación, índices, recuperación, generación y auditoría de citas (celdas C0–C16). | Metodología |
| [`Evaluacion_RAG_FINAL.ipynb`](Evaluacion_RAG_FINAL.ipynb) | **Evaluación v1.0.** Compara C0 (modelo sin recuperación), C1 (RAG tradicional) y C2 (framework): Recall@k por etapa, citas inventadas, abstención, RAGAS con juez externo y contraste de hipótesis. | Resultados 5.1–5.6 |
| [`Evaluacion_RAG_v1_1.ipynb`](Evaluacion_RAG_v1_1.ipynb) | **Evaluación v1.1.** Corrige el tokenizador de BM25, el umbral del reordenador y la ventana de contexto, y replica la evaluación en un subconjunto nuevo de 50 consultas, con la condición pareada C2v10. | Resultados 5.7 |
| [`RAG_Leyes_PYME_v2_compliance.ipynb`](RAG_Leyes_PYME_v2_compliance.ipynb) | **Versión 2, orientada a cumplimiento** (exploratoria). Agrega el Decreto 662 (modelos de prevención de infracciones), un perfil de empresa y respuestas en tres niveles: qué exige la ley, dónde está el riesgo y cómo prevenirlo. | Resultados 5.8 |

Los notebooks se guardan **con sus salidas**, para revisar los resultados sin ejecutarlos.

## Resultados principales

| Métrica | C0 | C1 | C2 | Prueba (C2 vs.) |
|---|---|---|---|---|
| Citas inventadas, v1.0 | 32,0 % | 0,0 % | 0,0 % | C0: p = 0,0002 ✅ |
| Cita del artículo esperado, v1.0 | 11,1 % | 51,1 % | 51,1 % | C0: p = 0,0017 ✅ · C1: n. s. |
| Citas inventadas, v1.1 | 22,0 % | 2,0 % | 0,0 % | C0: p = 0,004 ✅ |
| Cita del artículo esperado, v1.1 | 6,7 % | 84,4 % | 91,1 % | C0: p < 0,001 ✅ · C1: n. s. |

*p corregidos por Holm. n. s. = no significativo.*

- **H1 (C2 supera al modelo sin recuperación):** se confirma en dos subconjuntos independientes.
- **H2 (la segmentación jerárquica supera a un RAG tradicional):** tendencia favorable, pero no significativa.
- **Abstención:** solo C2 se abstiene ante consultas fuera del corpus.

## Cómo ejecutar

1. Abrir el notebook en Google Colab con GPU T4 (*Entorno de ejecución → Cambiar tipo de entorno*).
2. Agente: ejecutar `RAG_Leyes_PYME.ipynb` en orden (C0 → C13). La celda C2 lee los PDF de las normas desde Google Drive; ajustar `RUTA_LEYES` en esa celda.
3. Evaluación: los notebooks `Evaluacion_RAG_*` clonan este repositorio y ejecutan las celdas de `RAG_Leyes_PYME.ipynb` sin copiarlas. El orden de ejecución está en la primera celda de cada notebook.
4. RAGAS usa Gemini como juez. La clave se guarda en los Secretos de Colab con el nombre `GEMINI_API_KEY` y no se incluye en el código.

Los PDF de las normas se descargan desde la Biblioteca del Congreso Nacional ([leychile.cl](https://www.bcn.cl/leychile)).

## Aviso

Las respuestas del agente son orientación general basada en la normativa cargada y **no reemplazan la asesoría legal**.
