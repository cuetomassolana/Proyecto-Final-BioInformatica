# ¿Qué genes activa la estimulación eléctrica en las células de la piel?

Proyecto final de **Bioinformática 2026**, Bioingeniería, UNSTA.
**Alumna:** Solana Cueto Mas · **Profesor:** Ignacio García Labari

## Contexto

La estimulación eléctrica se propone como terapia para heridas que no cicatrizan, como las úlceras de pie diabético, pero todavía no está claro qué pasa dentro de las células cuando reciben la corriente. En este trabajo se reanalizan datos públicos de transcriptómica para identificar los genes que responden al campo eléctrico en tres tipos de células de la piel. Esos genes se comparan con los que están alterados en úlceras diabéticas reales y, entre ellos, se elige un candidato a biomarcador para un biosensor.

## Hipótesis

La estimulación eléctrica induciría en los queratinocitos un programa transcripcional propio, que incluiría genes de la vía **EGFR → MAPK-ERK** y de defensa frente al **estrés oxidativo**. Parte de ese programa coincidiría con el que está activado en la úlcera diabética, y al menos uno de esos genes codificaría una **proteína secretada** que se pueda medir en el exudado de la herida.

## Datos

| Dataset | Descripción | Paper |
|---|---|---|
| [GSE110830](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE110830) | Queratinocitos (HaCaT), fibroblastos dérmicos (NHDF) y células endoteliales (HMEC-1) estimulados con un campo eléctrico de alto voltaje (14.000 V, 90 Hz, 6 h) frente a un control *sham*. Microarray Agilent de dos colores (GPL20844), 3 réplicas por tipo celular. | Aoki M. et al. (2019). *Bioelectrochemistry*. doi:10.1016/j.bioelechem.2018.11.014 |
| [GSE80178](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE80178) | Tabla de genes alterados en el borde de úlceras de pie diabético frente a piel sana (6 vs. 6, Affymetrix). | Ramirez H.A. et al. (2018). *J Invest Dermatol* 138(5):1187-1196. doi:10.1016/j.jid.2017.11.038 |

## Análisis

1. **Preparación de los datos:** conversión de log10 a log2, tratamiento de los valores sin señal como faltantes, anotación de las sondas con GPL20844, filtrado a genes codificantes (RefSeq `NM_`) y mediana de las sondas por gen.
2. **Control de calidad:** correlación entre muestras, PCA y clustering (K-Means y jerárquico). Se detecta y excluye una réplica atípica (Fibro_3).
3. **Clasificación con Random Forest:** muestra que con 9 muestras no se puede entrenar un clasificador.
4. **Genes que cambian:** criterio de cambio consistente entre réplicas (mediana ≥ 2 veces), con un t-test y corrección FDR como respaldo, y validación contra los genes del paper original.
5. **Clustering de genes:** K-Means y jerárquico sobre los genes que cambian, para identificar patrones de respuesta por tipo celular.
6. **Contexto clínico:** comparación con los genes alterados en la úlcera diabética.
7. **Candidato a biomarcador:** selección de una proteína secretada para el proyecto de Biosensores.

## Resultados principales

- Se reproducen **7 de los 9 genes** reportados por el paper original, lo que valida el análisis.
- Los **queratinocitos** tienen la respuesta más clara y reproducible. La más fuerte es la activación de **CYP1A1 y CYP1B1** (vía AhR), junto con genes de la vía EGFR → MAPK-ERK (**EREG**, **DUSP5**) y de estrés oxidativo (**HMOX1**, **SLC7A11**).
- Cada tipo celular responde de forma distinta. Los 11 genes que cambian en los tres tipos celulares serían un artefacto del colorante.
- Los genes de respuesta a la herida (EREG, AREG, DUSP5, HMOX1, IL1A) también están aumentados en la úlcera diabética. CYP1A1 y CYP1B1 no aparecen alterados en la úlcera.
- Se propone **EREG (epirregulina)** como biomarcador: es secretada, sube de forma consistente en las 3 réplicas, la reporta el paper original y activa el EGFR.

## Archivos del repositorio

| Archivo | Contenido |
|---|---|
| `ProyectoFinal_BioInformatica_CuetoMas.ipynb` | Notebook con todo el análisis, los gráficos y las conclusiones |
| `datos_limpios_GSE110830.csv` | Matriz de expresión procesada: log2FC estimulado/sham, 18.558 genes × 9 muestras |
| `candidatos_biomarcador.csv` | Genes que suben en queratinocitos, con sus réplicas, su valor en la úlcera y el enlace a UniProt |

