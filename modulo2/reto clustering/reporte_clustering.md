# Reporte: Segmentación de Perfiles de Usuario mediante Clustering

**Dataset:** Online Shoppers Purchasing Intention Dataset — 12,330 sesiones de navegación de un e-commerce, 18 variables de comportamiento y contexto, sin valores nulos.

## 1. Metodología

1. **EDA:** las variables numéricas de comportamiento (páginas vistas, duración, tasas de rebote/salida, valor de página) están fuertemente sesgadas, con escalas muy distintas entre sí. No se detectaron valores nulos.
2. **Pipeline de preprocesamiento** (`ColumnTransformer` de Scikit-learn):
   - Numéricas → `SimpleImputer(median)` + `StandardScaler`.
   - Categóricas (`VisitorType`, `Weekend`) → `SimpleImputer(most_frequent)` + `OneHotEncoder`.
   - Se excluyó `Revenue` de las variables de entrada para evitar fuga de información (se usa solo para validar los perfiles).
3. **Reducción de dimensionalidad:** PCA mostró que se necesitan **8 componentes para retener el 92.6% de la varianza**; ese espacio se usó para entrenar los modelos (mitigando la maldición de la dimensionalidad). PCA y UMAP en 2D se usaron para exploración y visualización.
4. **Modelado — Duelo de algoritmos:**

| Modelo | Selección de hiperparámetro | N° clústeres | Silueta | % Ruido | Balance (min/max) |
|---|---|---|---|---|---|
| **K-Means** | k óptimo por silueta (codo poco definido) | 3 | **0.4415** | 0% | 0.168 |
| DBSCAN | `eps=1.0`, `min_samples=16` (grid search sobre k-distancias) | 3 | 0.4358 | 12.3% | 0.0018 |
| GMM | n_components por silueta (BIC no convergía, señal de sobreajuste) | 3 | 0.2040 | 0% | 0.209 |

**Modelo elegido: K-Means (k=3).** Ofrece la mejor silueta con clústeres balanceados y accionables. DBSCAN logra una silueta similar pero con una segmentación muy desbalanceada (un clúster domina con 10,684 sesiones) — es más útil como detector de sesiones atípicas que como segmentador de negocio. GMM, pese a ser más flexible, no logra una separación geométrica tan clara.

## 2. Perfiles de Clúster (K-Means, k=3)

### 🟢 Clúster 0 — Compradores de Alto Valor (1,624 sesiones · 13.2%)
- Máximo compromiso: ~97 páginas de producto vistas (3,838 s en promedio), `PageValues` más alto (9.70), menores tasas de rebote/salida.
- **Conversión: 27.8%** (~1.8× el promedio global de 15.5%).
- Concentrados en noviembre (38%), mayo y diciembre — temporada de descuentos/fin de año. 94% son visitantes recurrentes.
- **Recomendación:** priorizar en campañas premium, acceso anticipado a ofertas y programas de fidelización.

### 🔵 Clúster 1 — Navegantes Regulares (9,653 sesiones · 78.3%)
- Compromiso moderado (~24 páginas de producto, 874 s), `PageValues` medio (5.89).
- **Conversión: 15.0%**, en línea con el promedio global. Mayor proporción de visitantes nuevos (16.2%).
- **Recomendación:** nutrir con recomendaciones personalizadas y retargeting para migrarlos hacia el perfil del Clúster 0.

### 🔴 Clúster 2 — Visitantes Fugaces / Alto Rebote (1,053 sesiones · 8.5%)
- Compromiso casi nulo (~3 páginas, 57 s), tasas de rebote (0.17) y salida (0.18) más altas, `PageValues` ≈ 0.
- **Conversión: 0.6%** (25× menor que el promedio global).
- **Recomendación:** auditar el canal/anuncio que trae este tráfico y revisar la landing page; no priorizar presupuesto de retargeting aquí.

## 3. Conclusiones

- El pipeline de preprocesamiento + PCA permitió comparar tres algoritmos de clustering bajo las mismas condiciones de forma objetiva.
- K-Means (k=3) fue el modelo más útil para segmentación de marketing; DBSCAN es más valioso como detector de anomalías; GMM evidenció el riesgo de optimizar solo por BIC.
- **Próximos pasos:** incorporar `Month`/`TrafficType` con codificación más rica, probar HDBSCAN, y validar las recomendaciones con pruebas A/B por segmento.

## 4. Formato de Entrega
- Notebook: `reto_clustering_segmentacion_usuarios.ipynb` (ejecutado, con todas las gráficas y tablas). Carga el dataset automáticamente: primero intenta `RUTA_LOCAL` y, si no existe, lo descarga desde `URL_RESPALDO`.
- Este reporte: `reporte_clustering.md`.
