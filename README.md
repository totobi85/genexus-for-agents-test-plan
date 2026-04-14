## genexus-for-agents-test-plan
Plan de pruebas para GeneXus for Agents — skills, MCP tools y distribución de casos

## Qué captura el MD

- Secciones 1-3: qué es el sistema, el equipo, la estructura completa del plan con los 63 casos y su distribución.
- Sección 4: todos los MDs que no tienen caso propio y por qué — incluyendo las exclusiones intencionales (Print/Layout, POP3).
- Sección 5: las tres decisiones metodológicas más importantes — cómo se parten los prompts, el prompt alternativo, y el criterio de asignación por dependencia de KB.
- Sección 6: los 16 hallazgos pre-ejecución detectados en los MDs — bugs en las skills que el agente casi seguramente va a reproducir.
- Sección 7: tabla completa de las 14 correcciones que se aplicaron a los prompts para resolver inconsistencias de dependencias.
- Sección 8: los setups críticos por persona en orden.
- Secciones 9-10: cómo está estructurado el xlsx y cómo ejecutar las pruebas.
- Sección 11-12: protocolo cuando se encuentra un fallo y las 12 reglas de GeneXus que el agente más probablemente va a violar.
- Secciones 13-14: extensiones futuras y tecnología usada.

Con esto, cualquier sesión futura de Claude que lea este MD puede retomar el trabajo exactamente desde donde quedó, sin perder ninguno de los criterios ni decisiones que tomamos a lo largo de todas estas iteraciones.

## Contenido

- **`GENEXUS_FOR_AGENTS_CONTEXT.md`** — contexto completo del proyecto:
  decisiones metodológicas, hallazgos pre-ejecución, distribución de casos,
  dependencias entre pruebas y reglas de GeneXus más probables de violar.

- **`GeneXus_for_Agents_Plan_de_Pruebas.xlsx`** — 63 casos de prueba con
  prompts exactos para CODA-CLI, resultados esperados, señales de fallo
  y prompts alternativos en lenguaje natural.

## Resumen

| | |
|---|---|
| Total de casos | 63 |
| Bloque 1 | 14 casos — objetos core (Transaction, Procedure, Domain, API, SDT, Table) |
| Bloque 2 | 12 casos — objetos avanzados (Agent, BPD, Panel, Query, ExternalObject) |
| Bloque 3 | 21 casos — skills common-* (reglas, comandos, tipos, extended types, BC) |
| Bloque 4 | 16 casos — properties y MCP tools |
| Equipo | Diego + Rodolfo (KBs independientes — Opción B) |

## Cómo usar el xlsx

1. Descargarlo y abrirlo en Excel o Google Sheets.
2. Ver la pestaña **Orden de Ejecución** para saber qué hacer primero.
3. Copiar el prompt de la columna 📋 directamente en CODA-CLI.
4. Registrar resultados en las columnas blancas.
5. Los fallos van a la pestaña **Issues**.
