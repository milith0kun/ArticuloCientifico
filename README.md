# Descomposición de la Escasez Muestral y el Ruido NISQ en la Clasificación de Kernel Cuántico

Este repositorio contiene el código fuente, notebooks de experimentación y el artículo científico desarrollado para el curso de Computación Cuántica de la Escuela Profesional de Ingeniería Informática y de Sistemas de la Universidad Nacional de San Antonio Abad del Cusco (UNSAAC).

El estudio propone y valida un protocolo de descomposición de tres puntos que aísla de forma independiente el impacto de la escasez de datos (provocada por las restricciones de cuotas del tier gratuito) y el ruido NISQ sobre clasificadores basados en métodos de kernel cuántico (QSVC) entrenados en el hardware cuántico real de 156 qubits IBM Heron r2 (ibm_fez).

## Autores
* Edmil Jampier Saire Bustamante - 174449@unsaac.edu.pe
* Medaly Lozano Llacctahuaman - 195040@unsaac.edu.pe
* José Francisco Puma Potosino - 164248@unsaac.edu.pe

---

## Estructura del Repositorio

El repositorio está organizado de la siguiente manera:

* escasez_muestral_y_ruido_nisq_qsvc.tex: Código fuente principal del artículo científico en LaTeX (Formato IEEE Conference).
* escasez_muestral_y_ruido_nisq_qsvc.pdf: Versión compilada y terminada del artículo científico en formato PDF.
* contenido/:
  * bibliografia.bib: Base de datos de referencias bibliográficas en formato BibTeX.
* Colab/:
  * GRUPO 1_..._CODIGO.ipynb: Notebook de Jupyter con la implementación completa del proyecto (baselines clásicos, QSVC en simulador, ejecución remota en IBM Quantum con SamplerV2 y mitigación TREX, VQC con optimizador COBYLA).
  * resultados_segunda_parte.json y resumen_articulo.csv: Resultados numéricos y métricas guardadas de las corridas.
* graficos/: Figuras y visualizaciones incrustadas en el paper, incluyendo diagramas de circuitos de Qiskit, matrices de error y radar de métricas.
* InstruccionesTarea.md: Guía y rúbrica académica original para la entrega final del artículo.

---

## Requisitos e Instalación

### Entorno Local
Para ejecutar los notebooks de experimentación localmente, se requiere Python 3.10 o superior. Instala las dependencias necesarias ejecutando el siguiente comando en tu terminal:

```bash
pip install qiskit qiskit-machine-learning qiskit-ibm-runtime scikit-learn matplotlib seaborn pandas numpy
```

### Ejecución en Google Colab
El notebook está adaptado para ser ejecutado directamente en Google Colab. Solo debes subir el archivo .ipynb a tu cuenta de Google Drive y correr la celda de instalación inicial para aprovisionar las dependencias de Qiskit.

### Acceso a IBM Quantum (Hardware Real)
Para replicar las ejecuciones en la QPU ibm_fez (Heron r2), deberás poseer una cuenta activa en IBM Quantum Platform y configurar tu token de API en el notebook:

```python
from qiskit_ibm_runtime import QiskitRuntimeService
service = QiskitRuntimeService(channel="ibm_quantum", token="TU_API_TOKEN_AQUI")
```

---

## Instrucciones de Uso

### 1. Compilación del Artículo Científico (LaTeX)
Si realizas modificaciones al texto en LaTeX y deseas recompilar el PDF, ejecuta la siguiente secuencia de comandos en tu terminal local:

```bash
pdflatex escasez_muestral_y_ruido_nisq_qsvc.tex
bibtex escasez_muestral_y_ruido_nisq_qsvc
pdflatex escasez_muestral_y_ruido_nisq_qsvc.tex
pdflatex escasez_muestral_y_ruido_nisq_qsvc.tex
```

### 2. Réplica de los Experimentos de QML
Abre el notebook GRUPO 1_..._CODIGO.ipynb en tu servidor de Jupyter o Colab. Las secciones están ordenadas cronológicamente siguiendo la metodología del paper:
1. Baselines Clásicos: Ejecución de SVM-RBF, MLP y KNN.
2. Entrenamiento de VQC: Optimización variacional mediante COBYLA (300 iteraciones con semillas 42, 123 y 789).
3. Cálculo de Fidelidad del Estado de Bell: Control de calibración básico en simulador y en ibm_fez.
4. Cadena de Descomposición de 3 Puntos (QSVC): Ejecución secuencial para medir el impacto de la escasez de datos y el ruido cuántico.
5. Mitigación TREX: Evaluación de la mitigación de errores de lectura de SamplerV2 en hardware.
6. Clasificación MNIST: Comparación de desempeño en la tarea binaria MNIST.

---

## Hallazgos y Resultados Clave

* Descomposición Dual del Error: El protocolo de tres puntos demostró que la degradación total de la QSVC sobre Iris se descompone en:
  1. Simulador (Iris Completo): 77.78% de exactitud.
  2. Simulador (Subset 13/9): 33.33% de exactitud (efecto de escasez: -44 pp).
  3. Hardware ibm_fez (Subset 13/9): 11.11% de exactitud (efecto de ruido: -22 pp).
* Fidelidad de Bell vs. Rendimiento del Kernel: A pesar de que ibm_fez arrojó una excelente fidelidad en el estado de Bell (0.9834), el clasificador de kernel cuántico se degradó severamente. Esto se atribuye a que el circuito compute-uncompute acumula ruido de compuertas debido a su profundidad y al colapso por concentración exponencial del kernel, haciendo inefectiva la mitigación de lectura (TREX), que mantuvo el accuracy en 11.11%.
* Anomalía de MNIST: La clasificación binaria de MNIST mostró un resultado donde el hardware superó al simulador (88.89% vs. 77.78%). El estudio advierte que se trata de un artefacto estocástico producto del tamaño muestral reducido (9 de test, donde cada muestra representa ±11.1%), y no una ventaja cuántica genuina.
