# GeneXus for Agents — Contexto y Plan de Pruebas

> **Documento vivo**: captura todo el contexto, decisiones y conocimiento acumulado durante el diseño del plan de pruebas. Cualquier sesión futura de trabajo debe leer este documento antes de continuar.
>
> **Artefacto principal**: `GeneXus_for_Agents_Plan_de_Pruebas.xlsx` — contiene los 63 casos de prueba completos con prompts, resultados esperados, hallazgos y distribución entre personas.

---

## 1. Qué es GeneXus for Agents

Sistema que expone GeneXus como un **servidor MCP** (`localhost:8001/mcp`) con tools específicas, permitiendo que un agente LLM genere código GeneXus válido y ejecute operaciones sobre una Knowledge Base (KB) real.

Componentes:
- **Servidor MCP**: expone tools como `create_knowledge_base`, `open_knowledge_base`, `validate_kb_text_files`, `import_text_to_kb`, `build_one`, `build_all`, `export_kb_to_text`, `create_or_impact_database`, `install_module`, `update_module`.
- **CODA-CLI**: interfaz de línea de comandos desde donde se ingresan los prompts al agente.
- **Skill library**: ~97 archivos Markdown que instruyen al agente sobre cómo generar código GeneXus válido para cada tipo de objeto y patrón.
- **GeneXus Next**: plataforma target. Los objetos se serializan como archivos `.gx` en `src/` y `src.ns/`.

---

## 2. Equipo

| Persona | Rol | KB propia |
|---|---|---|
| **Diego** | QA — cadena de infraestructura, UI, MCP tools | `RetailApp-Diego` + `ShopApp` + `ShopApp2` |
| **Rodolfo** | QA — cadena de datos core, BC, reglas, For Each | `RetailApp-Rodolfo` |

**Decisión clave — Opción B**: cada persona tiene su propia KB independiente. No comparten ambiente. Esta decisión se tomó para eliminar dependencias entre personas: si uno rompe algo en la KB, no afecta al otro.

Implicación: cualquier objeto que un caso necesite y que no haya sido creado por un caso anterior de esa misma persona, debe crearse como **setup previo** antes de ejecutar ese caso. Los setups están documentados en la columna "Hallazgos pre-prueba" del xlsx (marcados con 🔧 SETUP PREVIO).

---

## 3. Estructura del plan de pruebas

### 63 casos totales en 4 bloques

| Bloque | Casos | Qué cubre |
|---|---|---|
| **Bloque 1** | 14 | SKILL.md · global-constraints · global-output · object-transaction · object-procedure · object-domain · object-api · object-data-provider · object-structured-data-type · object-table · object-index |
| **Bloque 2** | 12 | object-agent · object-business-process · object-panel · object-design-system · object-query · object-external-object · object-data-selector · object-subtype-group · object-module · object-url-rewrite · object-document · object-file · object-deployment-unit |
| **Bloque 3** | 21 | common-rules · common-functions · common-operators · common-formulas · common-data-types · common-attribute-types · common-collections · common-serialization · common-extended-type-httpclient · common-extended-type-xmlreader/writer · common-extended-type-smtpsession · common-extended-type-file/directory · common-external-object-log · common-external-object-dictionary · common-extended-type-expression · common-extended-type-geography · common-business-component · common-data-picture · common-commands-foreach · common-commands |
| **Bloque 4** | 16 | properties-object-transaction · properties-object-procedure · properties-object-panel · properties-object-domain · properties-knowledge-base · properties-environment · properties-object-module · properties-version · MCP tools (create/open/close/validate/import/build/export/modules) · Integración multi-objeto |

### Distribución completa de casos

#### RODOLFO (34 casos) — cadena de datos core de RetailApp

| ID | Skill(s) | Prioridad |
|---|---|---|
| SKILL-01-P1 | SKILL.md | Crítico |
| SKILL-01B-P1 | SKILL.md (objeto existente) | Alto |
| SKILL-03-P1 | global-output.md | Crítico |
| SKILL-04-P1 | object-transaction.md | Crítico |
| SKILL-05-P1 | object-procedure.md | Crítico |
| SKILL-05B-P1 | object-procedure.md + common-commands (Delete, orden hijo→padre) | Alto |
| SKILL-05C-P1 | object-procedure.md (atributo implícito en parm, llamada entre Procedures) | Alto |
| SKILL-06-P1 | object-domain.md | Crítico |
| SKILL-08-P1 | object-data-provider.md | Alto |
| SKILL-08B-P1 | object-data-provider.md (Input clause, [One], paginación) | Medio |
| SKILL-09-P1 | object-structured-data-type.md | Alto |
| SKILL-10A-P1 | object-table.md + object-index.md | Crítico |
| SKILL-16-P1 | object-data-selector.md | Alto |
| SKILL-17-P1 | object-subtype-group.md | Alto |
| SKILL-22-P1 | common-rules.md | Crítico |
| SKILL-22B-P1 | common-rules.md (Serial, Add, Subtract) | Alto |
| SKILL-23-P1 | common-functions + operators + formulas | Crítico |
| SKILL-24-P1 | common-data-types + attribute-types | Crítico |
| SKILL-25-P1 | common-collections + serialization | Crítico |
| SKILL-33-P1 | common-business-component.md | Crítico |
| SKILL-33B-P1 | common-business-component.md (Check, Insert, Update, RemoveByKey) | Alto |
| SKILL-34-P1 | common-data-picture.md | Medio |
| SKILL-35-P1 | properties-object-transaction + attribute | Alto |
| SKILL-35A-P1 | common-commands-foreach.md | Crítico |
| SKILL-35B-P1 | common-commands-foreach.md (Unique, Order condicional, Delete) | Crítico |
| SKILL-36A-P1 | common-commands.md (ElseIf prohibido, Do Case) | Crítico |
| SKILL-36B-P1 | common-commands.md (For In, For To Step, Rollback, Exit) | Alto |
| SKILL-36C-P1 | common-commands.md (New/EndNew vs BC) | Crítico |

#### DIEGO (35 casos) — cadena de infraestructura + UI + MCP tools

| ID | Skill(s) | Prioridad |
|---|---|---|
| SKILL-02-P1 | global-constraints.md | Crítico |
| SKILL-07-P1 | object-api.md | Crítico |
| SKILL-10-P1 | object-agent.md + common-agent-invocation | Crítico |
| SKILL-11-P1 | object-business-process.md | Crítico |
| SKILL-12-P1 | object-panel.md | Crítico |
| SKILL-13-P1 | object-design-system.md | Alto |
| SKILL-14-P1 | object-query.md | Alto |
| SKILL-15-P1 | object-external-object.md | Alto |
| SKILL-18-P1 | object-module.md | Medio |
| SKILL-19-P1 | object-url-rewrite.md | Alto |
| SKILL-20-P1 | object-document.md + object-file.md | Alto |
| SKILL-21-P1 | object-deployment-unit.md | Bajo |
| SKILL-25B-P1 | common-serialization.md (ToXml/FromXml) | Medio |
| SKILL-26-P1 | common-extended-type-httpclient.md | Crítico |
| SKILL-27-P1 | common-extended-type-xmlreader + xmlwriter | Alto |
| SKILL-28-P1 | common-extended-type-smtpsession + MailMessage | Alto |
| SKILL-29-P1 | common-extended-type-file + directory | Alto |
| SKILL-30-P1 | common-external-object-log + dictionary | Crítico |
| SKILL-31-P1 | common-extended-type-expression.md | Crítico |
| SKILL-32-P1 | common-extended-type-geography + GeoPoint | Medio |
| SKILL-36-P1 | properties-object-procedure.md | Alto |
| SKILL-37-P1 | properties-object-panel + integrated-security | Alto |
| SKILL-38-P1 | properties domain + KB + environment | Crítico |
| SKILL-39-P1 | properties module + version | Alto |
| INT-01-P1 | Integración multi-objeto | Alto |
| MCP-01-P1 | MCP: create_knowledge_base | Crítico |
| MCP-01-P2 | MCP: create_knowledge_base (encadenado) | Crítico |
| MCP-02-P1 | MCP: open + close knowledge_base | Crítico |
| MCP-03-P1 | MCP: validate_kb_text_files | Crítico |
| MCP-03-P2 | MCP: import_text_to_kb | Alto |
| MCP-04-P1 | MCP: build_one | Crítico |
| MCP-04-P2 | MCP: build_all | Crítico |
| MCP-05-P1 | MCP: create_or_impact_database | Crítico |
| MCP-06-P1 | MCP: export_kb_to_text | Alto |
| MCP-07-P1 | MCP: install_module + update_module | Alto |

---

## 4. Skills sin caso de prueba propio (y por qué)

Estos archivos MD no tienen caso de prueba propio porque su contenido queda cubierto dentro de otros casos o porque son demasiado simples para ameritar uno:

- `common-events.md` — cubierto en SKILL-04 (Transaction), SKILL-07 (API), SKILL-12 (Panel).
- `common-data.md` — sintaxis transversal cubierta en todos los casos de Transaction/Procedure/SDT.
- `common-agent-invocation.md` — cubierto en SKILL-10.
- `common-enum-domain.md` — cubierto en SKILL-06.
- `common-semantic-types.md` — cubierto en SKILL-06 (rechazo de Email built-in).
- `common-markdown.md` — cubierto en SKILL-20 (Document).
- `common-extended-type-httprequest.md` / `common-extended-type-httpresponse.md` — mismo patrón que HttpClient; cubiertos en SKILL-26.
- `common-extended-type-mailmessage.md` / `common-extended-type-mailrecipient.md` — cubiertos en SKILL-28.
- `common-extended-type-pop3session.md` — excluido por decisión explícita del equipo.
- `common-extended-type-geopoint.md` / `common-extended-type-geoline.md` / `common-extended-type-geopolygon.md` — cubiertos en SKILL-32.
- `common-extended-type-directory.md` — cubierto en SKILL-29.
- `common-extended-type-regexmatch.md` — patrón simple, cubierto implícitamente por los métodos de string en common-data-types.
- `common-standard-variables.md` — variables Pgmname/Pgmdesc cubiertos en todos los casos; variables de reporte (Msg, Output, Page) excluidas por decisión del equipo (no probar parte visual/print).
- `object-folder.md` — sin sintaxis propia; solo estructura de carpetas. Cubierto implícitamente por SKILL-18 y SKILL-20.
- `object-environment.md` — cubierto en SKILL-38.
- `object-knowledgebase.md` — cubierto en SKILL-38.
- `object-version.md` — cubierto en SKILL-39.
- `properties.md` — es el índice de los demás; no tiene contenido propio.
- Todas las `properties-*` menores (api, data-selector, deployment-unit, documentation, file, image, url-rewrite, design-system, structured-data-type, subtype-group, warning-messages, network, observability) — triviales o cubiertas por los objetos que las usan.

**Funcionalidad excluida intencionalmente** (decisión del equipo):
- Todo lo relacionado con `Print`, `PrintBlock`, `Header`, `Footer`, `#Layout`, `Output_File` — se prueban solo funcionalidades de backend.
- `common-extended-type-pop3session.md` — recepción de emails.

---

## 5. Criterios y decisiones metodológicas tomadas

### 5.1 Criterio de split de prompts largos
Cualquier caso con **4 o más acciones/requisitos distintos** se parte en sub-prompts numerados. El resultado esperado muestra **solo el delta** de cada paso (no el objeto completo acumulado). Los pasos son continuación de la misma sesión CODA-CLI — el agente entiende el contexto conversacional entre pasos.

### 5.2 Columna de prompt alternativo
Para 52 de 63 casos existe un prompt en lenguaje natural de negocio (sin terminología GeneXus) que prueba si el agente interpreta correctamente pedidos vagos. Solo 2 casos no tienen alternativa:
- `SKILL-39-P1` — configuración de longitudes de nombres (no tiene equivalente de negocio).
- `MCP-03-P1` — el punto de esta prueba es que el agente detecte un error; no tiene equivalente natural.

### 5.3 Criterio de asignación entre personas
El principio fue **coherencia de contexto de KB**: toda cadena de dependencia debe estar con la misma persona. Rodolfo crea la KB con `SKILL-01-P1` y es el dueño de todos los objetos que dependen del modelo de datos core (Transactions, BC, For Each, colecciones). Diego maneja la cadena de infraestructura (módulos, DSO, Panels, API, MCP tools).

El único punto de sincronización necesario entre personas es `MCP-03-P2` (Diego): debe ejecutarse después de que Rodolfo complete `SKILL-03-P1`, porque Diego importa un objeto que Rodolfo creó.

### 5.4 Qué probar con cada caso
El xlsx tiene tres tipos de columnas:
1. **Referencia** (fondo coloreado): extraída de los MDs — hallazgos, prompt, resultado esperado, puntos clave, señal de fallo.
2. **Tracking** (fondo blanco): a completar durante la ejecución — resultado obtenido, prioridad del hallazgo, descripción del problema, propuesta de corrección.
3. **Prompt alternativo** (fondo lavanda): versión en lenguaje natural para una segunda pasada de pruebas.

---

## 6. Hallazgos críticos pre-ejecución detectados en las skills

Estos problemas se encontraron al analizar los MDs **antes** de ejecutar cualquier prueba. Son bugs en la documentación de skills que el agente casi seguramente va a reproducir:

| # | Riesgo | Skill afectada | Prioridad |
|---|---|---|---|
| H-001 | Ejemplo de `Procedure.Add()` pasa campos individuales en lugar de un item | object-procedure.md | Crítico |
| H-002 | `doNotExecuteReorg: true` pasado por defecto en builds — omite reorganización DB | SKILL.md | Crítico |
| H-003 | `Numeric` sin decimal explícito (`Numeric(10)` en lugar de `Numeric(10.0)`) | common-data-types.md | Crítico |
| H-004 | `Error_Handler` sin las variables de error en `#Variables` | common-rules.md | Crítico |
| H-005 | Typo `olumnsStyle` en ejemplo de Panel GXML — layout roto | object-panel.md | Crítico |
| H-006 | `Error(!"mensaje")` en ejemplo de Transaction contradice constraint de texto traducible | object-transaction.md + global-constraints | Crítico |
| H-007 | `Log` instanciado con `new()` en lugar de uso estático | common-external-object-log.md | Crítico |
| H-008 | `create_or_impact_database` sin advertencia de pérdida de datos ni confirmación | SKILL.md | Crítico |
| H-009 | `NOT IN` usado en lugar de `NOT (... IN ...)` | common-operators.md | Crítico |
| H-010 | `Messages` declarada con `Collection = 'True'` siendo que ya es colección | common-collections.md | Crítico |
| H-011 | `ElseIf` es un comando inválido en GeneXus — nunca usar | common-commands.md | Crítico |
| H-012 | `Control break` con `Order` no concatenado o `Where` no repetido en niveles internos | common-commands-foreach.md | Alto |
| H-013 | `"Signed" = false` con comillas en el nombre en ejemplo de Domain — contradice global-constraints | object-domain.md | Alto |
| H-014 | `ProductCategoryId` en ejemplo de DataProvider pero el FK en Product se llama `CategoryId` | object-data-provider.md | Alto |
| H-015 | Typo `souce` en lugar de `source` en constraint de object-index.md | object-index.md | Medio |
| H-016 | Contradicción: `Rollback` documentado en common-commands "solo dentro de For Each" pero common-business-component lo muestra fuera | common-commands.md vs common-business-component.md | Medio |

---

## 7. Correcciones aplicadas a los prompts de prueba

Durante el diseño se detectaron inconsistencias entre los objetos que un caso necesita y los objetos que casos anteriores habrían creado. Se corrigieron:

| Caso | Problema detectado | Solución aplicada |
|---|---|---|
| SKILL-01-P1 | `CustomerBirthDate` referenciado en SKILL-23 pero Customer no lo tenía | Agregado al crear Customer en SKILL-01 |
| SKILL-04-P1 | `ProductUpdatedDate` y `ProductStatus` usados en SKILL-01B y SKILL-16 sin existir | Agregados al crear Product en SKILL-04 |
| SKILL-06-P1 | Módulo `BusinessRules` nunca fue creado | Agregado como paso 1 de SKILL-06 |
| SKILL-08-P1 | Usaba `ProductCategoryId` pero el FK se llama `CategoryId` | Corregido el nombre del atributo |
| SKILL-09-P1 | `Attribute:OrderId` requiere que Transaction Order exista — no existía | Agregado al setup de SKILL-09 |
| SKILL-16-P1 | Setup debe actualizar `ProductStatus` en Product con el domain real | Aclarado en setup |
| SKILL-22-P1 | Setup de Invoice no definía `InvoiceTotal`, `InvoiceStatus` ni `CustomerId FK` | Setup de Invoice reescrito con todos los atributos necesarios |
| SKILL-23-P1 | Usaba `OrderStatusType.Confirmed` pero el domain se llama `OrderStatus` | Corregido a `OrderStatus.Confirmed` |
| SKILL-35A-P1 | Invoice necesita `CustomerId FK` a Customer para navegar `CustomerName` | Verificación de FK en setup |
| INT-01-P1 | `GetActiveCategories` devuelve `CategoryData` pero el SDT nunca se creaba | Agregado como paso 4, renumerado el resto |
| MCP-04-P1 | Setup decía "crear Customer si no existe" — ambiguo | Clarificado como paso explícito obligatorio |
| MCP-05-P1 | Nombre del environment `NETDev` no confirmado como default | Aviso en setup para verificar el nombre real |
| SKILL-18-P1 (Diego) | Módulo `BusinessRules` no incluido pero SKILL-38 lo necesita | Agregado como paso 4 de SKILL-18 |
| SKILL-07-P1 (Diego) | Transaction Product no existe en KB de Diego | Agregado stub de Product + WebPanel CreateProduct al setup |

---

## 8. Dependencias de setup por persona

### Rodolfo — orden de ejecución y setups

Los casos de Rodolfo se encadenan — cada uno construye sobre el anterior. Los setups más críticos:

**Antes de SKILL-08-P1**: crear SDT `ProductInfo` con campos `ProductId`, `ProductName`, `ProductPrice`, `CategoryName`.

**Antes de SKILL-09-P1**: crear Transaction `Order` con `OrderId*` autonumber, `CustomerId` FK, `OrderDate`, `OrderAmount Numeric(10.2)`, `OrderStatus Numeric(1.0)`. Sin esto, `Attribute:OrderId` no existe cuando el SDT lo referencia.

**Antes de SKILL-16-P1**: crear Domain `ProductStatusType` (Active=1, Inactive=0). Actualizar Transaction Product para cambiar `ProductStatus` de `Numeric(1.0)` a `DataType = 'ProductStatusType'`. Importar Product actualizado.

**Antes de SKILL-22-P1**: crear Transaction `Invoice` completa: `InvoiceId*` autonumber, `InvoiceNumber Numeric(8.0)`, `InvoiceDate Date`, `CustomerId FK a Customer`, `InvoiceStatus Numeric(1.0)`, `InvoiceTotal Numeric(10.2)`. Sublevel `InvoiceLine`: `InvoiceLineId* Numeric(5.0)`, `InvoiceLineAmount Numeric(10.2)`. Crear Domain `InvoiceStatusType` (Pending=1).

**Antes de SKILL-23-P1**: Transaction Order debe usar Domain `OrderStatus` (del SKILL-06). Habilitar BC en Order. **Importante**: usar `OrderStatus.Confirmed` en el prompt, NO `OrderStatusType.Confirmed`.

**Antes de SKILL-25-P1**: habilitar BC en Transaction Customer.

**Antes de SKILL-33-P1**: crear SDT `OrderData` con `OrderId`, `CustomerId`, `OrderDate` y colección `OrderLines` (`ProductId`, `Quantity`, `UnitPrice`). Asegurar que Order tiene BC y sublevel `OrderLine`.

**Antes de SKILL-35A-P1**: verificar que Invoice tiene `CustomerId FK` (del setup SKILL-22). Crear SDT `ReportLine` con `CustomerId (Attribute:)`, `CustomerName (Attribute:)`, `InvoiceDate Date`, `InvoiceTotal Numeric(10.2)`.

### Diego — setups críticos

**Antes de SKILL-02-P1**: crear Transaction `User` (UserId, UserName). Crear Domain `UserStatus` (Active=1).

**Antes de SKILL-07-P1**: crear stub Transaction `Product` (ProductId* autonumber, ProductName, ProductPrice). Crear Procedures stub `GetProductData` y `SaveProduct`. Crear WebPanel `CreateProduct` vacío.

**Antes de MCP-04-P1**: crear Transaction `Customer` stub en RetailApp-Diego e importar.

**Antes de MCP-05-P1**: verificar nombre real del environment en `src.ns/Preferences/*.environment.main.gx`. Configurar `DatabaseName` y `ServerName` en el `.local.gx`.

---

## 9. Estructura del archivo XLSX

El archivo tiene **7 pestañas**:

1. **Dashboard** — resumen de totales por bloque, leyenda de colores, instrucciones de uso.
2. **Orden de Ejecución** — dos columnas side by side (Rodolfo y Diego), con número de orden, ID del caso y setup previo por persona.
3. **Bloque 1** — 14 casos.
4. **Bloque 2** — 12 casos.
5. **Bloque 3** — 21 casos.
6. **Bloque 4** — 16 casos.
7. **Issues** — registro de problemas detectados durante la ejecución.

### Columnas por hoja de bloque (16 columnas)

| Col | Nombre | Fondo | Tipo |
|---|---|---|---|
| 1 | ID Prueba | Por persona (naranja/azul) | Referencia |
| 2 | Skill(s) | Alternado | Referencia |
| 3 | Asignado | Por persona (dropdown) | Tracking |
| 4 | Estado | Amarillo/verde/rojo (dropdown) | Tracking |
| 5 | Fecha | Blanco | Tracking |
| 6 | ⚠ Hallazgos pre-prueba | Crema | Referencia (del MD) |
| 7 | 📋 Prompt — copiar en CODA-CLI | Azul claro (Courier New) | Referencia |
| 8 | ✅ Resultado esperado | Verde claro | Referencia (del MD) |
| 9 | 🔍 Puntos clave a verificar | Verde claro | Referencia (del MD) |
| 10 | ❌ Señal de fallo | Salmón | Referencia (del MD) |
| 11 | Resultado obtenido | Blanco | Tracking |
| 12 | Prioridad hallazgo | Blanco (dropdown) | Tracking |
| 13 | Descripción del problema | Blanco | Tracking |
| 14 | Propuesta de corrección | Blanco | Tracking |
| 15 | Skill a corregir (.md) | Blanco | Tracking |
| 16 | 🗣 Prompt alternativo (lenguaje natural) | Lavanda | Referencia |

---

## 10. Cómo ejecutar las pruebas

### Flujo por caso de prueba

1. Ir a la hoja del bloque correspondiente y buscar el caso asignado.
2. Cambiar **Estado** a `En curso` (para que el otro sepa que está tomado).
3. Verificar si hay **🔧 SETUP PREVIO** en la columna de Hallazgos. Si hay, ejecutarlo primero.
4. Copiar el **📋 Prompt** de la columna 7 y pegarlo en CODA-CLI.
5. Para casos con prompts en pasos numerados: ejecutar cada paso en la misma sesión de CODA-CLI.
6. Comparar el output del agente con el **✅ Resultado esperado** (columna 8) y los **🔍 Puntos clave** (columna 9).
7. Verificar que no aparece ninguna **❌ Señal de fallo** (columna 10).
8. Completar las columnas de tracking: Resultado obtenido, Prioridad, Descripción, Propuesta, Skill a corregir.
9. Cambiar Estado a `OK`, `FALLO` o `PARCIAL`.
10. Si hay FALLO: registrarlo también en la hoja **Issues**.

### Uso del prompt alternativo

Opcionalmente, después de ejecutar el prompt técnico se puede ejecutar el prompt alternativo (columna 16) para verificar si el agente también responde correctamente a lenguaje de negocio. Esto es una segunda pasada — no reemplaza el prompt técnico.

### Sincronización entre personas

Al cierre de cada día: 15 minutos de sincronización para cruzar fallos. El único punto de coordinación explícito: Diego ejecuta `MCP-03-P2` después de que Rodolfo complete `SKILL-03-P1`.

---

## 11. Cuando se encuentra un fallo

Si el agente genera código incorrecto, el proceso es:

1. Documentarlo en el xlsx (columnas 13-15).
2. Registrarlo en la hoja **Issues** con ID `I-001`, `I-002`...
3. Identificar si el problema es:
   - **Bug en la skill**: el MD tiene una instrucción incorrecta, ambigua o contradictoria. → Proponer corrección del MD.
   - **Limitación del agente**: la skill está bien pero el agente no la sigue. → Documentar para mejorar el prompt engineering del sistema.
4. Si se detectan **3 o más fallos del mismo MD**: pausar, corregir el MD, y re-ejecutar los casos afectados antes de continuar.

---

## 12. Reglas de GeneXus que el agente más probablemente va a violar

En orden de probabilidad basada en el análisis de los MDs:

1. **`Numeric` sin decimal**: usar `Numeric(10)` en lugar de `Numeric(10.0)`. El decimal es obligatorio siempre.
2. **`ElseIf`**: comando inválido en GeneXus. Siempre usar `If` anidados o `Do Case`.
3. **`NOT IN`**: inválido. Siempre usar `NOT (... IN ...)`.
4. **Literales `!` invertidos**: `!` marca literales NO traducibles (URLs, técnicos). Los mensajes de usuario van SIN `!`.
5. **`Log = new()`**: Log es estático, se usa directamente como `Log.Info(...)`.
6. **`Messages` con `Collection = 'True'`**: Messages ya es colección, nunca agregar Collection.
7. **Order en control break no concatenado**: cada nivel interno debe concatenar el Order del nivel superior.
8. **`Where` no repetido en niveles de control break**: cada `For Each` en un control break debe tener las mismas condiciones `Where` que el outer.
9. **`New` auto-commitea**: `New/EndNew` commitea automáticamente, no necesita Commit. A diferencia de BC que sí lo necesita.
10. **`Delete` sin orden hijo→padre**: siempre eliminar sublevels antes que el padre.
11. **Atributo en `parm` sin `&`**: cuando un atributo va en parm como `in:` sin `&`, GeneXus aplica el filtro implícitamente — no agregar Where adicional.
12. **`doNotExecuteReorg: true` por defecto**: nunca pasar este flag salvo que el usuario lo pida explícitamente.

---

## 13. Posibles extensiones futuras del plan

Los siguientes archivos MD tienen cobertura parcial o ninguna y podrían merecer casos adicionales en una segunda ronda:

- `common-extended-type-pop3session.md` — recepción de emails (POP3). Excluido en esta ronda por decisión del equipo pero tiene un flujo propio (Login → GetNextUID → Receive → Logout) diferente al SMTP.
- `common-extended-type-regexmatch.md` — tipo `RegExMatch` con propiedades `Value` y `Groups`. Patrón simple pero nunca probado de forma aislada.
- `object-data-provider.md` — DataProvider con cláusula `Input` iterando sobre colección (`var in collection`). Cubierto en SKILL-08B pero podría ampliar con `[OutputIfDetail]` y `[Default]`.
- Segunda pasada de todos los casos usando el **prompt alternativo** (columna 16) para medir cuánto entiende el agente de lenguaje de negocio natural.

---

## 14. Tecnología y herramientas usadas para este plan

- **Análisis**: lectura exhaustiva de los 97 archivos MD del proyecto + trazado manual de dependencias entre objetos.
- **Generación del xlsx**: Python con `openpyxl` — el script fuente puede regenerarse desde el historial de este proyecto.
- **Control de versiones**: este MD + el xlsx en el repositorio GitHub del proyecto.

---

*Última actualización: sesión de diseño del plan de pruebas — 63 casos, 4 bloques, equipo Diego + Rodolfo.*
*Artefacto principal: `GeneXus_for_Agents_Plan_de_Pruebas.xlsx`*
