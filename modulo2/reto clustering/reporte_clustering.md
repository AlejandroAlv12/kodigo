# Resumen: Segmentación de Usuarios

**Modelo Elegido:** K-Means (k=3) superó a DBSCAN y GMM (Silueta: 0.4415) tras preprocesamiento y PCA (8 componentes).

## Perfiles Detectados

1. **C0 - Alto Valor (13.2%):** Alto compromiso (~97 vistas), conversión del 27.8%. Tráfico fuerte en Nov/Dic. 
   *Recomendación:* Priorizar campañas premium y programas de fidelización.
2. **C1 - Regulares (78.3%):** Compromiso medio (~24 vistas), conversión del 15.0%. Mayor proporción de nuevos visitantes.
   *Recomendación:* Personalizar recomendaciones y retargeting para moverlos al C0.
3. **C2 - Fugaces (8.5%):** Compromiso casi nulo (~3 vistas), alto rebote y salida. Conversión del 0.6%.
   *Recomendación:* Auditar canales de adquisición, no priorizar presupuesto aquí.

## Conclusiones y Entregables

- **Conclusión:** K-Means es el mejor segmentador de negocio; DBSCAN sirve más para anomalías. 
- **Próximos pasos:** Probar HDBSCAN, mejorar variables de tiempo y validar con test A/B.
- **Entregables:** Notebook (`reto_clustering_segmentacion_usuarios.ipynb`) con código/gráficos y este reporte.
