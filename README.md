# FO Label Manager (Beta) 1.0.7

Extensión para **Visual Studio 2022** que gestiona los archivos `.label.txt` de **Dynamics 365 Finance & Operations** como una tabla multilenguaje.

En lugar de editar `DEMO.en-US.label.txt`, `DEMO.es.label.txt`, etc. por separado, ves todas las traducciones en la misma fila. Al guardar, solo se reescriben las líneas que cambian (diff pequeño en Git), salvo que uses **Ordenar alfabéticamente**.

> Todos los ejemplos usan el grupo ficticio `DEMO` (`DEMO.en-US.label.txt`, `DEMO.en-GB.label.txt`, `DEMO.es.label.txt`).

## Índice

1. [Qué incluye](#qué-incluye) · [Estados de una label](#estados)
2. [Novedades de esta versión](#novedades-de-esta-versión)
3. [Requisitos e instalación](#requisitos)
4. [Abrir la ventana](#abrir-la-ventana)
5. [Cargar labels](#cargar-labels)
6. [La tabla](#la-tabla)
7. [Crear una label](#crear-una-label)
8. [Editar, renombrar y eliminar](#editar-textos) · [Editar label en todos los idiomas](#editar-una-label-en-todos-los-idiomas)
9. [Labels con ID duplicado](#labels-con-id-duplicado)
10. [Insertar desde X++ o Propiedades](#insertar-desde-x-o-propiedades)
11. [Texto al pasar el cursor sobre @...](#texto-al-pasar-el-cursor-sobre-)
12. [Traducir labels vacías](#traducir-labels-vacías)
13. [Importar y exportar](#importar-y-exportar)
14. [Analizar](#analizar)
15. [Guardar, recargar y copias de seguridad](#guardar-y-recargar)
16. [Protección de los archivos](#protección-de-los-archivos)
17. [Atajos](#atajos)
18. [Configuración](#configuración)
19. [Buenas prácticas](#buenas-prácticas)
20. [Solución de problemas](#solución-de-problemas)

## Qué incluye

Todas las features de la extensión, agrupadas. Cada una enlaza a su explicación con ejemplos.

| Área | Feature | Qué hace |
| --- | --- | --- |
| **Cargar** | [Detectar desde la solución](#detectar-desde-la-solución) | Busca `*.label.txt` junto al `.sln` y en los proyectos y carga los grupos encontrados. |
| | [Abrir archivos](#abrir-archivos-a-mano) | Abre uno o varios `.label.txt`; ofrece abrir los demás idiomas del mismo grupo. |
| | [Solution Explorer y arrastrar](#desde-solution-explorer) | *Abrir con F&O Label Manager*, *Abrir como Label Workspace*, o arrastrar archivos a la ventana. |
| | Varios grupos | Desplegable **Grupo** para cambiar entre `DEMO`, `LOGISTICS`, … |
| **Tabla** | [Matriz multilenguaje](#la-tabla) | Una fila por Label ID y una columna por idioma, con referencia X++ y estado. |
| | [Estados](#estados) | `NEW`, `DUPLICATE`, `MISSING`, `UNTRANSLATED`, `MODIFIED`, `OK`. |
| | [Buscar y filtrar](#buscar-y-filtrar) | Por ID, texto o comentario (contiene / empieza por / exacto) y 9 filtros. |
| | Columnas | Mostrar, ocultar, redimensionar y reordenar; Label ID fija a la izquierda. |
| **Crear y editar** | [Nueva label](#crear-una-label) | ID sugerido (PascalCase / camelCase / conservar), textos por idioma, 4 modos de inserción, prefill desde la búsqueda. |
| | [Editar celdas](#editar-textos) | Doble click en un idioma; crea la línea si faltaba en ese archivo. |
| | [Editar label…](#editar-una-label-en-todos-los-idiomas) | Diálogo con el ID y todos los idiomas para cambiarlos a la vez (todo o nada). |
| | [Cambiar Label ID](#cambiar-el-label-id) | Renombra en todos los idiomas cargados. |
| | [Eliminar](#eliminar-una-label) | Borra la label (y sus comentarios) de todos los idiomas. |
| | [Resolver duplicado…](#labels-con-id-duplicado) | Elige qué aparición de un ID repetido conservar, mezclando comentarios. |
| | [Copiar referencia X++](#copiar-la-referencia-x) | `@Grupo:LabelId` al portapapeles (Ctrl+C / Ctrl+Shift+C). |
| **X++ y diseñador** | [Insertar label / Insertar nueva label](#insertar-desde-x-o-propiedades) | En el editor X++ / XML y en casillas de **Propiedades** (Ctrl+Alt+Shift+P / I). |
| | [Tooltip sobre @…](#texto-al-pasar-el-cursor-sobre-) | Muestra el texto de `@Grupo:LabelId` al pasar el cursor, en los idiomas que elijas. |
| **Traducción** | [Rellenar vacías](#traducir-labels-vacías) | Traduce celdas vacías del grupo (todas, con opciones, con revisión, solo la selección, reintentar fallidas, cancelar). |
| | [Completar con IA](#crear-una-label) | En Nueva label / Editar label, traduce los idiomas vacíos desde el texto escrito. |
| | [Diccionario y caché](#diccionario-y-caché) | Términos de F&O sin red y caché de traducciones. |
| | [OpenAI opcional](#api-key-openai) | API key en el Administrador de credenciales de Windows; modos Dictionary / AI / AI + Dictionary. |
| **Importar / exportar** | [Exportar](#exportar) | JSON y CSV con valor y estado por label e idioma. |
| | [Importar](#importar) | JSON y CSV, formato largo o ancho, `;` de Excel, diálogo de conflictos. |
| **Analizar** | [Validar](#reglas-de-validación) | 8 reglas (placeholders, IDs, duplicados, mayúsculas…) con severidad configurable. |
| | [Comparar, faltantes, huérfanas, duplicados, similares](#analizar) | Informes entre idiomas. |
| | [Ordenar alfabéticamente](#analizar) | Reordena cada archivo por Label ID; los comentarios van con su label. |
| | [Buscar referencias](#buscar-referencias) | Dónde se usa `@Grupo:LabelId` en el modelo o en todo `PackagesLocalDirectory`. |
| | Diagnóstico | Registro técnico de la sesión. |
| **Guardar** | [Guardar y vista previa](#guardar-y-recargar) | Preview exacto de líneas (`+`, `-`, `~`) antes de aplicar o guardar. |
| | Autoguardar | Escribe a disco poco después de cada cambio. |
| | [Copias de seguridad](#copias-de-seguridad) | Snapshot en `%TEMP%` al guardar, con restauración y límite de copias. |
| | Aviso al cerrar | Pregunta por cambios sin guardar al cerrar la solución. |
| **Seguridad de datos** | [Protección de los archivos](#protección-de-los-archivos) | Diff mínimo, ida y vuelta byte a byte, ANSI, escritura atómica, cambios externos, TFVC, **Guardar todo atómico**, errores de codificación con línea y Label ID. |
| **Configuración** | [Configuración](#configuración) | Menú de la ventana y **Herramientas → Opciones → FO Label Manager (Beta)**. |
| | [Atajos](#atajos) | Teclado para abrir, crear, insertar, guardar, buscar, renombrar, eliminar y copiar. |

Qué significa cada estado de la columna **Estado**, con ejemplos y qué hacer en cada caso: ver [Estados](#estados).

## Novedades de esta versión

### 1.0.7

- **Editar label…** (click derecho en una fila): un diálogo como el de Nueva label con el Label ID y los textos de todos los idiomas para cambiarlos a la vez. Ver [Editar una label en todos los idiomas](#editar-una-label-en-todos-los-idiomas).
- **Resolver duplicado…**: si un archivo tiene el mismo Label ID dos veces, la celda sale con borde rojo y un diálogo te deja elegir qué aparición conservar (y mezclar sus comentarios). Ver [Labels con ID duplicado](#labels-con-id-duplicado).
- **Eliminar** borra todas las apariciones de un ID duplicado (antes solo la primera).
- **Guardar todo es atómico entre idiomas**: si un idioma no se puede guardar, no se guarda ninguno, y si una escritura falla a mitad se restauran los demás.
- El error de codificación al guardar indica **línea, Label ID y caracteres** que no caben (p. ej. `línea 9, label ShipTitle: '出' (U+51FA)`).
- **Protección de archivos**: escritura atómica, detección de cambios externos, archivos ANSI (Windows-1252), checkout automático en TFVC, aviso al cerrar la solución con cambios sin guardar y bloqueo de archivos que no se pueden reescribir sin cambios. Ver [Protección de los archivos](#protección-de-los-archivos).
- Los diálogos **Nueva label** y **Editar label** se ajustan al número de idiomas (sin espacio vacío debajo).
- Guía ampliada: lista completa de features ([Qué incluye](#qué-incluye)) y significado de cada **[estado](#estados)**.

### 1.0.6

- Al pasar el cursor sobre `@Grupo:LabelId` (en X++, XML u otros archivos) aparece el texto de la label. Se puede desactivar y elegir qué idiomas muestra.
- Si buscas un texto que **no existe** y pulsas **Nueva label**, el diálogo se rellena con esa búsqueda.
- Formato del Label ID sugerido: **PascalCase**, **camelCase** o conservar las mayúsculas del texto.
- Las labels nuevas se insertan **en orden alfabético** (puedes elegir otro modo al crear).
- **Analizar → Ordenar alfabéticamente**: reordena cada `.label.txt` por Label ID. Los comentarios van con su label.
- Al **importar**, si el ID ya existe con otro texto, pregunta si conservas el actual o usas el importado.
- Click derecho en X++: **Insertar label…** e **Insertar nueva label…**, también en casillas de la ventana **Propiedades**.
- **Rellenar vacías** y **Completar con IA** (OpenAI, opcional). No pisan textos existentes.
- **Copias de seguridad** opcionales en `%TEMP%`, con restauración y límite de copias.

## Requisitos

- Visual Studio 2022 (Community, Professional o Enterprise), 17.0 o posterior, con .NET Framework 4.8.
- Una solución con archivos `{Grupo}.{idioma}.label.txt` (por ejemplo `DEMO.en-US.label.txt`).
- Opcional: una API key de OpenAI para las traducciones con IA.

## Instalación

1. Descarga `FoLabelManager.vsix` de esta release.
2. En Visual Studio: **Extensiones → Administrar extensiones → Instalar desde VSIX**. También vale doble click sobre el `.vsix`.
3. Elige el `.vsix` y **reinicia Visual Studio** (el tooltip sobre `@...` no carga hasta reiniciar).
4. Abre la solución que contiene los labels.

Para actualizar, instala el VSIX nuevo encima; no hace falta desinstalar. La configuración y la API key se conservan.

## Abrir la ventana

Cualquiera de estas opciones:

- **Ver → Otras ventanas → F&O Label Manager (Beta)**
- **Herramientas → Abrir F&O Label Manager (Beta)**
- Atajo **Ctrl+Shift+L**

La ventana se acopla por defecto como pestaña junto al Explorador de soluciones.

En **Herramientas → F&O Label Manager (Beta)** también hay un submenú con las acciones más usadas: Detectar labels de la solución, Nueva label, Insertar label / Insertar nueva label, Traducir labels vacías, Validar, Comparar idiomas, Buscar labels faltantes, Guardar todo y Recargar.

### Partes de la ventana

| Zona | Qué contiene |
| --- | --- |
| **Menú** | Archivo, Editar, Analizar, Configuración. |
| **Barra de botones** | Detectar, Abrir, Nueva label, Cambiar ID, Eliminar, Rellenar vacías, Traducir selección, Validar, Comparar, Guardar, Recargar. Se puede dejar en solo iconos. |
| **Barra de progreso** | Aparece mientras se traduce con IA, con botón **Cancelar**. |
| **Cabecera** | Nombre del grupo, idiomas cargados, referencia `@Grupo:Id` de la fila seleccionada y desplegable **Grupo**. |
| **Buscador** | Texto, modo (Contiene / Empieza por / Exacto) y filtro. |
| **Tabla** | Una fila por Label ID. |
| **Diagnóstico** | Panel con el registro técnico (se muestra con **Analizar → Diagnóstico**). |
| **Barra de estado** | Resultado de la última acción y estado de la IA (`AI: Ready`, `AI: Not configured`, `AI: Off`). |

## Cargar labels

Hace falta un **grupo** cargado (`DEMO`, `LOGISTICS`, …) para editar, importar o insertar referencias en X++ / Propiedades.

### Detectar desde la solución

1. Pulsa **Detectar** (o **Archivo → Detectar desde solución**).
2. La extensión busca `*.label.txt` junto al `.sln` y en los proyectos.
3. Confirma los grupos encontrados.
4. Si hay varios grupos, elige el activo en el desplegable **Grupo**.

Ejemplo: en una solución con `DEMO.en-US.label.txt`, `DEMO.en-GB.label.txt` y `DEMO.es.label.txt`, Detectar carga el grupo `DEMO` con las columnas `en-US`, `en-GB` y `es`.

### Abrir archivos a mano

1. Pulsa **Abrir** y selecciona uno o varios `*.label.txt`.
2. Si abres un solo archivo y existen hermanos del mismo grupo, pregunta si quieres abrirlos todos (opción *Suggest related language files*).

### Desde Solution Explorer

- Click derecho en un `.label.txt` → **Abrir con F&O Label Manager (Beta)**.
- Varios archivos seleccionados → **Abrir como Label Workspace**.
- También puedes **arrastrar** `.label.txt` desde el Explorador de Windows sobre la ventana.

### Nombres de archivo que se reconocen

```text
{grupo}.{idioma}.label.txt
```

| Archivo | Grupo | Idioma |
| --- | --- | --- |
| `DEMO.en-US.label.txt` | `DEMO` | `en-US` |
| `DEMO.es.label.txt` | `DEMO` | `es` |
| `LOGISTICS.pt-BR.label.txt` | `LOGISTICS` | `pt-BR` |
| `DEMO.label.txt` | — | No se reconoce (falta el idioma) |

Solo puede haber **un archivo por grupo e idioma**. Si la solución contiene `DEMO.es.label.txt` en dos carpetas, el segundo se rechaza y se avisa.

## La tabla

Cada fila es una Label ID. Las columnas de idioma se generan según los archivos cargados. El idioma fuente (por defecto `en-US`, si está cargado) va primero.

| Columna | Qué es |
| --- | --- |
| **Label ID** | Clave en el `.label.txt` (`Amount=...`). Es **case-sensitive**: `itemQty` y `itemqty` son IDs distintos. Está fija a la izquierda al hacer scroll. |
| **X++** | Referencia para código: `@DEMO:Amount` |
| **en-US, es, …** | Texto de cada idioma. Se guarda en memoria al salir de la celda; a disco al **Guardar** (o con Autoguardar). |
| **Estado** | Situación de la label en el conjunto de idiomas cargados. Ver [Estados](#estados). |

Puedes mostrar u ocultar columnas en **Configuración → Columnas**, redimensionarlas y reordenarlas arrastrando la cabecera. Se pueden seleccionar varias filas (Ctrl / Mayús + click) para **Traducir selección**.

### Estados

Cada fila muestra un solo estado. Si se cumplen varios, gana el primero de esta tabla (de arriba abajo):

| Estado | Qué significa | Ejemplo | Qué hacer |
| --- | --- | --- | --- |
| `NEW` | La label se ha creado en esta sesión y aún no se ha guardado. | Acabas de crear `ExpectedDeliveryDate` con **Nueva label**. | **Guardar** (o Autoguardar). |
| `DUPLICATE` | El Label ID aparece más de una vez en algún archivo. La celda afectada tiene borde rojo. | `Amount=Importe` y `Amount=Importe total` en `DEMO.es.label.txt`. | Click derecho → **Resolver duplicado…**. Mientras exista no se puede editar esa celda ni guardar. |
| `MISSING` | La label existe en unos idiomas y **no existe** en otros (falta la línea). | `Auxiliary1` está en en-US pero no en en-GB ni en es. | Escribe el texto en la celda vacía, usa **Editar label…** o **Rellenar vacías**. |
| `UNTRANSLATED` | La línea existe en todos los idiomas, pero en alguno el texto está vacío. | `CustomerStatus=` en es. | Igual que `MISSING`: escribe el texto o usa **Rellenar vacías**. |
| `MODIFIED` | Has cambiado el texto o el ID en algún idioma y aún no se ha guardado. | Cambias el ID de `Currency` con **Cambiar Label ID**. | **Guardar**. |
| `OK` | Existe en todos los idiomas cargados, con texto, sin duplicados ni cambios pendientes. | `Currency` en en-US, en-GB y es. | Nada. |

Notas:

- **Prioridad:** una label nueva que además falta en un idioma sale como `NEW`; una modificada que falta en otro idioma sale como `MISSING`. El filtro **Modificadas** la encuentra igualmente, porque mira los cambios pendientes y no el estado.
- **Cuándo se calcula:** al construir la tabla, es decir, al cargar, **Recargar**, crear, renombrar, eliminar, **Editar label…**, importar u ordenar. Editar una celda directamente **no** lo actualiza (una fila `OK` editada sigue en `OK`), y tras **Guardar** una fila `NEW` o `MODIFIED` conserva ese estado hasta que la tabla se vuelve a construir. Para verlo al día, pulsa **Recargar** después de guardar (es una limitación conocida que se corregirá).
- El estado solo tiene en cuenta los **idiomas cargados**: si no abres `DEMO.es.label.txt`, una label que falta en es no sale como `MISSING`.
- `WARNING` y `ERROR` existen en el código y en los filtros **Avisos** y **Errores**, pero hoy ninguna fila recibe esos estados (limitación conocida). Para ver avisos y errores (placeholders, IDs no válidos, mayúsculas…) usa **Analizar → Validar**. El filtro **Avisos** muestra de momento las filas `MISSING` y `UNTRANSLATED`.

Relación con los filtros del buscador:

| Filtro | Muestra |
| --- | --- |
| **Faltantes** | Filas `MISSING`. |
| **Traducidas** | Filas con texto en todos los idiomas cargados. |
| **Sin traducir** | Filas `UNTRANSLATED`. |
| **Modificadas** | Filas con cambios sin guardar (`MODIFIED`, y también `NEW` o `MISSING` si tienen cambios). |
| **Nuevas** | Filas `NEW`. |
| **Duplicadas** | Filas `DUPLICATE`. |
| **Avisos** | Filas `MISSING` y `UNTRANSLATED`. |
| **Errores** | Hoy, ninguna (ver nota anterior). |

### Ejemplo con el grupo `DEMO`

| Label ID | en-US | en-GB | es | Estado |
| --- | --- | --- | --- | --- |
| `Amount` | Amount | Amount | `Importe ` (con espacio final) | OK |
| `Auxiliary1` | Auxiliary field | — | — | MISSING |
| `Contact` | — | Contact | Contacto | MISSING |
| `ProductCode` | — | — | Código de producto | MISSING (huérfana) |
| `itemQty` | Item quantity | Item quantity | — | MISSING |
| `itemqty` | — | — | Cantidad del artículo | MISSING |

`itemQty` / `itemqty` aparecen como dos filas: el editor **nunca** las fusiona. El validador avisa (*CaseMismatch*) y tú decides.

### Buscar y filtrar

- Cuadro **Buscar** (**Ctrl+F**): filtra por ID, texto o comentarios.
- Modo: **Contiene**, **Empieza por**, **Exacto**.
- Filtro: **Todas**, **Faltantes**, **Traducidas**, **Sin traducir**, **Modificadas**, **Nuevas**, **Duplicadas**, **Errores**, **Avisos**.

**Faltante** = el ID no existe en ese idioma. **Sin traducir** = la línea existe pero el valor está vacío (`CustomerStatus=`).

Ejemplos:

| Busco | Modo | Encuentra |
| --- | --- | --- |
| `amount` | Contiene | `Amount`, `SampleBonus` (texto *Sample bonus amount*) |
| `Ledger` | Empieza por | `LedgerAccountValue`, `LedgerAccountVaue` |
| `Importe` | Exacto | Nada: el texto real es `Importe ` (con espacio) |
| `REF001` | Contiene | Las labels que llevan ese comentario (`Amount`, `Currency`) |

## Crear una label

1. **Nueva label**, **Ctrl+N**, **Insert** (con la ventana enfocada) o **Ctrl+Alt+Shift+N** desde cualquier parte de VS.
2. Escribe el texto en el idioma fuente. El **Label ID** se sugiere según **Configuración → Formato del Label ID sugerido**:
   - **PascalCase** (por defecto): `CUSTOMER ORDER` → `CustomerOrder`
   - **camelCase**: `CUSTOMER ORDER` → `customerOrder`
   - **Conservar mayúsculas del texto**: `CUSTOMER ORDER` → `CUSTOMERORDER`
3. El ID: letras, dígitos y `_`; máximo **50** caracteres; debe empezar por letra o `_`. Ejemplos válidos: `CustomerOrder`, `itemQty`, `_Temp1`. No válidos: `1stOrder` (empieza por dígito), `Customer Order` (espacio), `Importe-Total` (guion).
4. Rellena solo los idiomas que quieras. Por defecto **no** se escriben idiomas vacíos.
5. **Completar con IA** (junto al idioma fuente): escribe el texto en castellano (u otro idioma), deja el Label ID en inglés y pulsa el botón. Rellena **todos** los idiomas vacíos (incluido en-US) a partir de ese texto. No pisa lo que ya hayas escrito. Sin API key el botón está deshabilitado.
6. Si marcas **Rellenar idiomas vacíos (diccionario local)**, se generan traducciones locales para el resto, sin IA.
7. Elige dónde insertar (**Inserción**):

   | Modo | Dónde queda |
   | --- | --- |
   | **Alfabético** (por defecto) | En su posición por Label ID (orden ordinal: mayúsculas antes que minúsculas). |
   | **Final del archivo** | Última línea. |
   | **Después de** | Detrás de la label que indiques en *Insertar después* (y de sus comentarios). |
   | **En grupo** | Tras la última label cuyo comentario coincide con *Comentario grupo* (p. ej. `REF001`). |

8. **Crear**. Si está activa la confirmación, verás un preview de las líneas.

La label queda en memoria hasta **Guardar**, salvo que tengas **Autoguardar**.

### Ejemplo

Texto en `es`: `Fecha de entrega prevista`, ID sugerido `ExpectedDeliveryDate` (escrito a mano), **Completar con IA**:

```text
DEMO.en-US.label.txt  + ExpectedDeliveryDate=Expected delivery date
DEMO.en-GB.label.txt  + ExpectedDeliveryDate=Expected delivery date
DEMO.es.label.txt     + ExpectedDeliveryDate=Fecha de entrega prevista
```

La referencia para X++ es `@DEMO:ExpectedDeliveryDate`. Si tienes activo **Copiar @... al crear una label**, ya está en el portapapeles.

### Crear desde una búsqueda sin resultados

Si escribes en el buscador un texto que **no coincide con ninguna label** y pulsas **Nueva label**, el diálogo se abre con ese texto ya escrito (y el Label ID sugerido).

En **Configuración → Al crear desde el buscador**:

| Opción | Qué rellena |
| --- | --- |
| **No rellenar** | El diálogo sale vacío |
| **Solo idioma fuente** | Solo el idioma fuente (p. ej. `en-US`) |
| **Todos los idiomas** | El mismo texto en todos los archivos cargados (por defecto) |
| **Solo este idioma** | El idioma que elijas (`es`, `nl`, …). Si no está cargado, se usa el fuente. |

Ejemplo: buscas `Tracking number`, no hay resultados → Nueva label sale con ID `TrackingNumber` y el texto `Tracking number` en en-US, en-GB y es; cambias el de es a `Número de seguimiento` y creas.

Si la búsqueda ya encontró alguna label, no se rellena. Al crear desde el editor X++, se usa el texto seleccionado solo en el idioma fuente.

## Editar textos

Haz doble click en una columna de idioma y cambia el texto. El estado pasa a `MODIFIED`. Las líneas que no tocas se conservan tal cual (encoding, BOM, CRLF, comentarios).

- Escribir en una celda **vacía de un idioma donde el ID no existe** crea la entrada en ese archivo, en orden alfabético. Ejemplo: escribir `Auxiliary field` en la celda en-GB de `Auxiliary1` añade `Auxiliary1=Auxiliary field` a `DEMO.en-GB.label.txt`.
- Dejar vacía una celda que no existía **no** crea `Id=` vacío.
- Escribir el mismo texto que ya había no marca el archivo como modificado.
- No se admiten saltos de línea en el texto: partirían la label en dos líneas. Si pegas texto con varias líneas, se rechaza y la celda vuelve a su valor.
- Para cambiar varios idiomas a la vez, usa **Editar label…** (siguiente sección).

## Editar una label en todos los idiomas

Para cambiar varios idiomas de una vez (o el texto y el ID a la vez) sin ir celda por celda:

1. Click derecho en la fila → **Editar label…** (también en **Editar → Editar label…**).
2. Se abre el mismo diálogo que **Nueva label**, titulado *Editar label {Id}*, con el **Label ID** y el texto actual de **cada idioma cargado**. Bajo cada idioma, una nota indica:
   - **No existe**: la label no está en ese archivo. Si escribes un texto, se crea (en orden alfabético); si lo dejas vacío, ese archivo no se toca.
   - **Sin traducir**: la línea existe con el texto vacío (`Id=`).
3. Cambia lo que necesites. **Completar con IA** funciona igual que al crear: rellena los idiomas vacíos a partir del texto escrito, sin pisar los demás.
4. **Aplicar** (o **Ctrl+Enter**). Si **Confirmar al aplicar cambios** está activo, verás la vista previa con todas las líneas.

Reglas:

- Solo cambian los idiomas cuyo texto modificas. Abrir el diálogo y aplicar sin tocar nada no cambia ningún archivo (tampoco quita espacios iniciales o finales que ya estuvieran, como `Importe `).
- Vaciar el texto de un idioma donde la label existe deja la línea como `Id=` (sin traducir); no la borra. Para borrarla, usa **Eliminar label**.
- Si cambias el **Label ID**, se renombra en todos los idiomas cargados, igual que con **Cambiar Label ID** (pide confirmación porque el X++ con el ID antiguo dejará de resolver). El ID nuevo no puede existir ya en el grupo.
- Todo o nada: si un idioma no se puede escribir (texto con salto de línea, archivo bloqueado, ID nuevo que ya existe), no se cambia ningún idioma.
- Si el ID está duplicado en algún archivo, primero se abre **Resolver duplicado…**.
- Si la fila tiene un ID no válido (p. ej. `Amount ` con espacio final), puedes editar sus textos, pero para crearla en otro idioma hay que corregir antes el ID en el diálogo. Así el ID erróneo nunca se copia a otro archivo.
- Si escribes un texto que ya usa otra label en ese idioma, avisa y te deja continuar.
- Como el resto de ediciones, queda en memoria hasta **Guardar** (salvo con **Autoguardar**).

Ejemplo con `DEMO`: click derecho en `Auxiliary1` → **Editar label…**

| Idioma | Antes | Escribes | Vista previa |
| --- | --- | --- | --- |
| en-US | Auxiliary field | Auxiliary field 1 | `~ Auxiliary1=Auxiliary field 1` |
| en-GB | *No existe* | Auxiliary field 1 | `+ Auxiliary1=Auxiliary field 1` (nueva, en orden alfabético) |
| es | *No existe* | *(vacío)* | Sin cambios |

Si además cambias el ID a `AuxiliaryField1`, en en-US la vista previa muestra `~ Auxiliary1=Auxiliary field -> AuxiliaryField1=Auxiliary field 1`, y en en-GB la línea nueva ya lleva `AuxiliaryField1`.

## Cambiar el Label ID

Afecta a **todos** los idiomas cargados de esa label.

1. Selecciona la fila.
2. **Cambiar ID**, **F2**, doble click en **Label ID**, o click derecho → **Cambiar Label ID**.
3. El ID nuevo no puede existir ya en el grupo (en ningún idioma) ni estar duplicado.
4. Confirma el preview.

Ejemplo: `LedgerAccountVaue` → `LedgerAccountValue` falla en es porque ya existe; en ese caso borra la fila con la errata y rellena la buena.

Cuidado: el X++ que use `@Grupo:Id` antiguo dejará de resolver. Usa antes **Buscar referencias en el modelo** para ver dónde se usa.

## Eliminar una label

1. Selecciona la fila.
2. **Eliminar**, **Supr**, o click derecho → **Eliminar label**.
3. Confirma el preview. Se borra de todos los idiomas cargados, con sus comentarios asociados. No se escribe a disco hasta **Guardar** (salvo Autoguardar).

Si el ID está duplicado en un archivo, se borran **todas** sus apariciones; el preview las lista y el título avisa.

## Labels con ID duplicado

Un mismo ID dos veces en un archivo es un error real (F&O solo usa una). El editor no lo corrige solo:

- La celda del idioma duplicado tiene **borde rojo**. Al pasar el ratón, un tooltip lista cada aparición: línea, texto y comentarios.
- No se puede editar el texto de esa celda hasta resolverlo.
- **Guardar** se bloquea mientras exista; el aviso abre el diálogo de resolución.

Para resolverlo: click derecho → **Resolver duplicado…**, o doble click / empezar a editar la celda marcada.

En el diálogo:

1. Marca en **Conservar** la aparición buena (o usa **Conservar la primera** / **Conservar la última**).
2. **Mezclar comentarios** (marcada por defecto) pasa a la aparición conservada los comentarios de las eliminadas que no tenga ya.
3. La columna **Resultado** muestra cómo quedará antes de aplicar.
4. **Aplicar**. Solo cambia ese archivo.

Ejemplo:

```text
Antes (es)                      Después (conservar la segunda, mezclar comentarios)
Amount=Importe                  Amount=Importe total
 ;REF001                     ;REF002
Amount=Importe total             ;REF001
 ;REF002
```

## Copiar la referencia X++

Para pegarla en un formulario, `label()` o un literal de X++:

- **Ctrl+C** o **Ctrl+Shift+C** con una fila seleccionada
- Click derecho en la fila → **Copiar @Grupo:LabelId**
- **Editar → Copiar referencia X++**

Ejemplo: `@DEMO:Amount`. La barra de estado muestra `Copiado @...`.

En **Configuración** puedes activar **Copiar @... al crear una label** para dejarla en el portapapeles al crear.

## Insertar desde X++ o Propiedades

Con un archivo `.xpp`, un XML de F&O o el **diseñador** abierto, y un grupo de labels cargado:

| Opción | Qué hace | Atajo |
| --- | --- | --- |
| **Insertar label…** | Buscador de labels existentes (Label ID, Texto, X++). Click en una fila (o Enter) inserta `@Grupo:LabelId`. Si hay texto seleccionado, se usa como filtro. | **Ctrl+Alt+Shift+P** |
| **Insertar nueva label…** | Abre Nueva label con el texto seleccionado e inserta la referencia al crear. | **Ctrl+Alt+Shift+I** |

**En el editor** (X++ / XML): click derecho, o el atajo, con el cursor donde quieras la referencia. Si había texto seleccionado, se sustituye.

```xpp
// Antes: seleccionas "Amount" y pulsas Ctrl+Alt+Shift+P → eliges Amount
info("Amount");
// Después
info("@DEMO:Amount");
```

**En la ventana Propiedades** (casilla de un control, campo, menú, …):

1. Abre el formulario / tabla / EDT en el diseñador.
2. Selecciona el objeto.
3. En **Propiedades**, pulsa la casilla **Label**, **HelpText**, **Caption**, etc. (puedes estar editándola).
4. **Ctrl+Alt+Shift+I** para crear una label nueva, o **Ctrl+Alt+Shift+P** para elegir una existente.
5. La referencia `@Grupo:LabelId` se escribe en esa propiedad.

También en **Herramientas → F&O Label Manager (Beta)**. Si no se puede escribir en la casilla, la referencia queda en el portapapeles para pegarla con Ctrl+V.

Puedes ocultar las opciones del click derecho del editor en **Configuración** o en **Herramientas → Opciones → FO Label Manager (Beta) → General** (categoría *Editor*).

## Texto al pasar el cursor sobre @...

Si dejas el cursor sobre una referencia `@Grupo:LabelId` en el editor, sale un tooltip con el texto de esa label:

```text
@DEMO:Amount
en-US: Amount
es: Importe
```

Funciona en **X++**, **XML de F&O** y otros archivos de texto. No hace falta tener la ventana de labels enfocada.

El texto se busca así:

1. En el grupo **cargado** (incluye cambios sin guardar).
2. Si ese grupo no está abierto, en los `.label.txt` del **modelo** del archivo actual (carpeta `AxLabelFile`).

Si la referencia no existe, no se muestra nada. Las labels de sistema sin dos puntos (`@SYS33480`) no se resuelven.

En **Configuración**:

| Opción | Qué hace |
| --- | --- |
| **Mostrar texto al pasar el cursor sobre @...** | Activa o desactiva el tooltip (por defecto activo). |
| **Idiomas del tooltip → Todos los idiomas** | Muestra cada idioma encontrado. |
| **Idiomas del tooltip → Solo estos idiomas** | Marca `en-US`, `es`, `nl`, … |

También en **Herramientas → Opciones → FO Label Manager (Beta) → General**, categoría *Editor* (*Show label text on hover* y *Hover languages*, p. ej. `en-US;es`; vacío = todos).

## Traducir labels vacías

Al crear una label, la IA **no** se llama sola. El checkbox **Rellenar idiomas vacíos** usa solo el diccionario local. Si quieres IA en ese diálogo, pulsa **Completar con IA**. En el grupo ya cargado, usa **Rellenar vacías**.

| Acción | Qué hace |
| --- | --- |
| **Rellenar vacías** | Todas las celdas vacías del grupo (idioma fuente → resto de idiomas cargados). |
| **Rellenar vacías...** | Diálogo para elegir idioma origen, idiomas destino y si quieres revisar. |
| **Rellenar vacías y revisar** | Igual que Rellenar vacías, pero muestra el diálogo de revisión antes de aplicar. |
| **Traducir selección** | Solo las filas seleccionadas (también en el click derecho). |
| **Reintentar fallidas** | Vuelve a pedir solo las traducciones que fallaron en la última ejecución. |
| **Completar con IA** | En **Nueva label**: traduce los idiomas vacíos a partir del texto que hayas escrito. |

Solo toca celdas **vacías** (o labels que aún no existen en ese idioma). Nunca pisa un texto existente. No escribe a disco: hay que **Guardar**.

Orden interno: traducción ya presente en el grupo → término exacto del diccionario → caché → IA (si está configurada). Agrupa por idioma, deduplica textos iguales y manda lotes (no una petición por label). Los placeholders (`%1`, `%2`) se conservan; una traducción que los pierda, que traiga saltos de línea o que venga vacía se descarta y cuenta como fallida.

Mientras traduce aparece una barra de progreso con **Cancelar**: se conservan las que ya hayan llegado.

### Revisar antes de aplicar

En el diálogo **Revisar traducciones** cada fila muestra Label ID, Idioma, Origen, Traducción y **Fuente**. Fuente indica de dónde salió: traducción ya existente en el grupo, diccionario, caché o IA. Puedes editar la traducción, desmarcar las que no quieras, o usar **Marcar todas** / **Ninguna**, y después **Aceptar seleccionadas**.

### Ejemplo con `DEMO`

Idioma origen `en-US`, destinos `en-GB` y `es`:

| Label ID | Celda vacía | Resultado | Fuente |
| --- | --- | --- | --- |
| `Auxiliary1` | en-GB | Auxiliary field | IA |
| `Auxiliary1` | es | Campo auxiliar | IA |
| `Amount` (si hubiera una celda vacía en `es`) | es | Importe | Diccionario (*amount* → *importe*) |
| `Currency` | — | No se toca: ya tiene texto en todos | — |

### Diccionario y caché

- El diccionario local incluye términos habituales de F&O en inglés → castellano (*purchase order* → *pedido de compra*, *delivery note* → *albarán*, *warehouse* → *almacén*, *invoice* → *factura*, …). Funciona sin red.
- La caché de traducciones se guarda en `%LOCALAPPDATA%\FoLabelManager\translation-cache.tsv`. La misma terna (texto, origen, destino) no se vuelve a pedir a la IA. Borra el archivo para vaciarla.

### API key (OpenAI)

**Herramientas → Opciones → FO Label Manager (Beta) → AI / Translation**:

- **Provider**: OpenAI
- **API Key**: se guarda en el **Administrador de credenciales de Windows**, no en el proyecto ni en el VSIX. La UI nunca muestra la clave completa (`••••••••`).
- **Model** (por defecto `gpt-4o-mini`)
- **Translation mode**: **Dictionary** (sin red), **AI**, o **AI + Dictionary** (recomendado)
- **Max concurrent requests** (3) y **Max request characters** (12 000): tamaño de los lotes
- **Revisar traducciones antes de aplicar**: equivale a usar siempre *Rellenar vacías y revisar*
- **Test connection** / **Eliminar API key**

Sin API key:

- **Completar con IA** queda deshabilitado (el tooltip indica dónde configurar la clave).
- **Rellenar vacías**, **Rellenar vacías...**, **Rellenar vacías y revisar**, **Traducir selección** y **Reintentar fallidas** también quedan deshabilitados, salvo que el modo sea **Dictionary** (solo diccionario local, sin red).
- Al guardar la clave en Opciones, los botones se habilitan de inmediato.

La barra de estado muestra `AI: Ready`, `AI: Not configured` o `AI: Off`.

Qué se envía a OpenAI: los textos a traducir, el idioma origen y destino, y los términos del diccionario aplicables (con `temperature` 0 y respuesta JSON estructurada). No se envían rutas ni el contenido del resto del archivo.

## Importar y exportar

### Exportar

**Archivo → Exportar JSON / CSV** genera un informe del grupo cargado, una fila por Label ID e idioma, con estas columnas:

`LabelId, Language, Value, Exists, Missing, Empty, Modified, Duplicate, ValidationStatus`

```csv
LabelId,Language,Value,Exists,Missing,Empty,Modified,Duplicate,ValidationStatus
Amount,en-US,Amount,true,false,false,false,false,OK
Amount,es,Importe ,true,false,false,false,false,OK
Auxiliary1,es,,false,true,true,false,false,MissingLabel
```

```json
[
  {"LabelId":"Amount","Language":"es","Value":"Importe ","Exists":true,"Missing":false,"Empty":false,"Modified":false,"Duplicate":false,"ValidationStatus":"OK"}
]
```

### Importar

**Archivo → Importar JSON / CSV** aplica textos al grupo activo. Se aceptan dos formas:

**Largo** (el de la exportación): una fila por label e idioma.

```csv
LabelId,Language,Value
ExpectedDeliveryDate,en-US,Expected delivery date
ExpectedDeliveryDate,es,Fecha de entrega prevista
```

**Ancho**: una fila por label y una columna por idioma. El CSV admite `;` como separador (Excel en español).

```csv
LabelId;en-US;en-GB;es
ExpectedDeliveryDate;Expected delivery date;Expected delivery date;Fecha de entrega prevista
TrackingNumber;Tracking number;;Número de seguimiento
```

```json
[
  {"LabelId":"TrackingNumber","en-US":"Tracking number","es":"Número de seguimiento"}
]
```

Nombres de columna aceptados: `LabelId` / `Label ID` / `Id`; `Language` / `Lang` / `Locale` / `Idioma`; `Value` / `Text` / `Translation` / `Valor` / `Texto`. En JSON vale un array o un objeto con `rows`, `items`, `labels` o `data`. Las columnas de estado de la exportación se ignoran.

Reglas:

- Crea labels nuevas (en orden alfabético) y actualiza las que cambian.
- No borra nada: valores vacíos, idiomas no cargados y Label ID inválidos se omiten y se informa en el resumen.
- Un valor con salto de línea (CSV multilínea, JSON con `\n`) no se importa.
- Si el import toca un ID duplicado en el archivo, se rechaza entero antes de cambiar nada.
- Si el ID ya existe y el texto es distinto, el diálogo **Labels duplicadas al importar** muestra valor actual e importado: marca **Usar importado** en las que quieras sustituir (o **Conservar actuales** / **Usar importados** para todas) y **Continuar**.
- Si **Confirmar al aplicar cambios** está activo, muestra el preview. Con **Autoguardar**, escribe a disco después.

## Analizar

En la barra o en **Analizar**:

| Acción | Para qué |
| --- | --- |
| **Validar** | Informe de todos los problemas del grupo (ver tabla de reglas). |
| **Comparar idiomas** | Elige origen y destino y ve las diferencias label a label (Label, Origen, Destino, Estado). |
| **Labels faltantes** | ID que existe en un idioma y no en otro. |
| **Labels huérfanas** | Existen en un idioma secundario pero no en el primario. |
| **Duplicados** | Mismo ID repetido en un archivo o mismo texto en IDs distintos. |
| **Labels similares** | Parecidas a la fila seleccionada (typos, casing). |
| **Ordenar alfabéticamente** | Reordena cada archivo por Label ID. Los comentarios se mueven con su label. Si ya estaban ordenados, no hace nada. Git verá un diff grande si el fichero no estaba ordenado. |
| **Referencias en el modelo** | Dónde se usa `@Grupo:LabelId` en el modelo del label. |
| **Referencias en el entorno** | Igual, recorriendo `PackagesLocalDirectory` (todos los modelos del AOS). |
| **Diagnóstico** | Muestra u oculta el panel con el registro técnico de la sesión. |

### Reglas de validación

| Código | Qué detecta | Ejemplo | Severidad por defecto |
| --- | --- | --- | --- |
| `EmptyLabelId` | Línea `=Texto` sin ID | `=Text without Label ID` | Error |
| `InvalidLabelId` | ID que no cumple las reglas de F&O | `Amount ` (espacio final) | Aviso |
| `EmptyValue` | Label con texto vacío | `CustomerStatus=` | Aviso |
| `DuplicateLabelId` | Mismo ID dos veces en un archivo | `Amount` repetido en es | Error |
| `DuplicateValue` | Mismo texto en IDs distintos del mismo idioma | `Amount` y `TotalAmount` = *Amount* | Aviso |
| `MissingLabel` | El ID falta en algún idioma | `Auxiliary1` en en-GB y es | Aviso |
| `PlaceholderMismatch` | `%1`, `%2` distintos entre idiomas | en-US `Order %1` / es `Pedido` | Aviso |
| `CaseMismatch` | IDs que solo difieren en mayúsculas | `itemQty` / `itemqty` | Aviso |

Las severidades se cambian en **Herramientas → Opciones → FO Label Manager (Beta) → Validation** (`Ignore`, `Info`, `Warning`, `Error`). Los avisos no bloquean Guardar; los ID duplicados sí.

### Buscar referencias

Recorre los `*.xpp` y `*.xml` buscando `@Grupo:LabelId` exacto (`@DEMO:Amount` no coincide con `@DEMO:AmountTotal`). Omite carpetas `bin`, `obj` y similares y se detiene a las 2 500 coincidencias. El resultado lista Archivo, Línea y Código; **Abrir** (o doble click) abre el archivo en esa línea.

- **En el modelo**: solo la carpeta del modelo al que pertenece el `.label.txt` (la que tiene `Descriptor` / `AxLabelFile`), aunque esté fuera de la solución abierta.
- **En el entorno**: todo `PackagesLocalDirectory`. Si no lo detecta, indícalo en **Herramientas → Opciones → FO Label Manager (Beta) → General**, categoría *Find references* (p. ej. `K:\AosService\PackagesLocalDirectory`).

## Guardar y recargar

- **Guardar** / **Ctrl+S**: escribe los cambios pendientes de todos los idiomas. Si la confirmación está activa, muestra el preview exacto de líneas.
- **Recargar**: vuelve a leer los archivos del disco. Pide confirmación si hay cambios sin guardar.
- **Restaurar copia de seguridad...** (**Archivo**): lista los snapshots del grupo (Fecha, Archivos) y sustituye en disco los `.label.txt` cargados. Pide confirmación. Los cambios sin guardar se pierden; antes se guarda una copia del estado actual.
- **Autoguardar** (**Configuración**): escribe a disco unos instantes después de editar, crear, renombrar o eliminar. No pide el preview de Guardar. Si hay Label ID duplicados, no guarda. Si las copias están activas, también genera snapshot.
- Al **cerrar la solución** (o Visual Studio con la solución abierta) se pregunta si guardar los cambios pendientes.

### Vista previa de cambios

Con **Confirmar al aplicar cambios** activo, cada operación muestra un título y, por archivo, las líneas exactas: `+` se añade, `-` se borra, `~` cambia (en un renombrado, `~ antes -> después`).

```text
Rellenar vacías

DEMO.en-GB.label.txt
  + Auxiliary1=Auxiliary field

DEMO.es.label.txt
  + Auxiliary1=Campo auxiliar
  ~ Amount=Importe total
```

**Aplicar** lo ejecuta; **Editar** y **Cancelar** vuelven atrás sin aplicar.

### Copias de seguridad

Desactivadas por defecto. **Herramientas → Opciones → FO Label Manager (Beta) → General**:

- **Automatic backups**: al Guardar (o Autoguardar) copia el estado *anterior* de los archivos a `%TEMP%\FoLabelManager\backups\{grupo}\{fecha}\`. Nunca junto al modelo F&O ni en Git.
- **Maximum backups to keep**: cuántas copias conservar por grupo (1–100, por defecto 10). Las más antiguas se borran.

**Archivo → Restaurar copia de seguridad...** para volver a una de ellas.

## Protección de los archivos

El editor está pensado para no romper archivos de proyectos de cliente:

| Situación | Qué hace |
| --- | --- |
| Editas una celda | Solo se reescribe esa línea. El resto del archivo (encoding, BOM, CRLF/LF, comentarios, líneas desconocidas) queda byte a byte igual. |
| El archivo no se puede reescribir idéntico sin cambios (bytes inválidos, codificación rara) | Se abre **bloqueado**: se ve, pero no se puede editar ni guardar. El motivo sale en Diagnóstico y al intentar guardar. |
| El archivo es ANSI (Windows-1252, sin BOM) | Se lee y guarda en ANSI; los acentos se conservan. Si escribes un carácter que ANSI no admite (p. ej. japonés), no se guarda ese archivo y se avisa. |
| Otro programa cambia el archivo | Si no tenías cambios pendientes, se recarga solo. Si los tenías, pregunta antes de pisarlo. Nunca sobrescribe en silencio. |
| El archivo se borró fuera de VS | No se vuelve a crear al guardar sin que lo elijas. |
| Archivo de solo lectura / TFVC | Antes de guardar pide a Visual Studio el checkout. Si sigue en solo lectura, no se escribe. |
| Corte o cierre a mitad de guardar | Escritura atómica (temporal + reemplazo): el original queda intacto. |
| Un idioma no se puede guardar | **Guardar todo** no escribe ninguno (todo o nada). Si falla una escritura a mitad, se restauran los ya escritos. |
| Operación masiva con un error (crear en todos, renombrar, importar, ordenar) | Se valida todo antes; si un idioma falla, no se aplica nada. |

**Guardar todo es todo o nada.** Antes de escribir se comprueban todos los idiomas pendientes. Si uno no se puede guardar (solo lectura, ANSI que no admite un carácter, cambio fuera de VS…), no se guarda ninguno y un mensaje lista todos los problemas; los cambios siguen en el editor. Si una escritura falla a mitad, los archivos ya escritos se restauran. Así una label nunca queda guardada solo en algunos idiomas. Lo mismo vale para Autoguardar (que se pone en pausa y lo indica en la barra de estado).

Ejemplo: label nueva `ShipTitle` en en-US, en-GB y es con texto japonés en es, que es ANSI. Al Guardar sale:

```text
No se ha guardado ningún archivo. Para no dejar labels guardadas solo en algunos idiomas, Guardar todo se cancela si algún archivo no se puede guardar.

• DEMO.es.label.txt: la codificación del archivo (windows-1252) no admite algunos caracteres. No se ha escrito nada.
  • línea 9, label ShipTitle: '出' (U+51FA), '荷' (U+8377), 'タ' (U+30BF), 'イ' (U+30A4), 'ト' (U+30C8), 'ル' (U+30EB)
Cambia esos textos o convierte el archivo a UTF-8 con BOM.
```

El mensaje indica el archivo, su codificación, la línea que tendría en el archivo guardado, el Label ID y cada carácter que no cabe (sin repetir, con su código Unicode). Si hay más de 5 líneas afectadas, se listan 5 y se resume el resto. en-US y en-GB tampoco se escriben; cambias el texto de es (o conviertes el archivo a UTF-8) y vuelves a Guardar.

## Atajos

| Atajo | Dónde | Acción |
| --- | --- | --- |
| **Ctrl+Shift+L** | Visual Studio | Abrir la ventana |
| **Ctrl+Alt+Shift+N** | Visual Studio | Nueva label (abre la ventana si hace falta) |
| **Ctrl+Alt+Shift+P** | Editor X++ / XML F&O / **Propiedades** | Insertar label existente |
| **Ctrl+Alt+Shift+I** | Editor X++ / XML F&O / **Propiedades** | Insertar nueva label |
| **Ctrl+N** / **Insert** | Ventana de labels | Nueva label |
| **Ctrl+S** | Ventana de labels | Guardar todo |
| **Ctrl+F** | Ventana de labels | Ir al buscador |
| **F2** | Ventana de labels | Cambiar Label ID |
| **Supr** | Ventana de labels | Eliminar label |
| **Ctrl+C** / **Ctrl+Shift+C** | Ventana de labels | Copiar `@Grupo:LabelId` de la fila seleccionada |

Los atajos globales (los cuatro primeros) se pueden reasignar en **Herramientas → Opciones → Entorno → Teclado**.

## Configuración

En la propia ventana, menú **Configuración**:

- **Al crear desde el buscador** — no rellenar / idioma fuente / todos / un idioma concreto
- **Formato del Label ID sugerido** — PascalCase, camelCase o conservar mayúsculas
- Copiar `@...` al crear una label
- Mostrar u ocultar **Insertar label** / **Insertar nueva label** en el click derecho del editor
- **Mostrar texto al pasar el cursor sobre @...** y **Idiomas del tooltip**
- Mostrar solo iconos en la barra
- Autoguardar
- Confirmar al aplicar cambios (crear, traducir, renombrar, eliminar, guardar, importar)
- Columnas visibles (Label ID, Referencia X++, Estado, cada idioma)
- **Opciones de Visual Studio...** — abre la página de opciones

Más opciones en **Herramientas → Opciones → FO Label Manager (Beta)**. Hay cuatro páginas; la página **General** agrupa varias categorías:

| Página | Categoría | Opciones |
| --- | --- | --- |
| **General** | General | *Default insertion mode* (Alphabetical, EndOfFile, AfterSelected, InGroup), *Preserve line endings*, *Preserve encoding*, *Automatic backups*, *Maximum backups to keep*, *Confirm before applying changes*, *Suggest related language files*, *Auto-save label files* |
| | Editor | *Suggested Label ID style*, *Show Insert label / Insert new label in editor context menu*, *Show label text on hover*, *Hover languages* |
| | New label | *Prefill from search when not found*, *Prefill language* |
| | Clipboard | *Copy @... reference after creating a label* |
| | Appearance | *Show toolbar icons only* |
| | Columns | *Show Label ID / X++ reference / status column*, *Hidden language columns* (p. ej. `fr-FR;de`) |
| | Find references | *PackagesLocalDirectory (environment)* |
| **Translation** | Translation | *Default source language* (`en-US` si está cargado; si no, el primero), *Translation provider* (`manual`, `literal`, `existing`), *Literal translation enabled*, *Terminology dictionary enabled* |
| **AI / Translation** | AI Translation | Provider, API key, modelo, modo, concurrencia, tamaño de lote, revisar antes de aplicar, Test connection, Eliminar API key |
| **Validation** | Validation | Severidad de faltantes, ID duplicados, textos duplicados, placeholders y mayúsculas |

## Buenas prácticas

- Trabaja con **todos** los idiomas del grupo abiertos. Si solo abres `es`, una label nueva no se creará en `en-US`.
- Revisa el preview antes de Guardar en proyectos de cliente (o deja **Confirmar al aplicar cambios** activo).
- Resuelve los duplicados (borde rojo) en cuanto aparezcan: mientras existan no se puede guardar.
- No renombres IDs a la ligera: el X++ con `@Grupo:Id` antiguo dejará de resolver. Busca antes las referencias.
- Editar una celda no reescribe el resto del archivo (encoding, comentarios y líneas ajenas se conservan).
- **Ordenar alfabéticamente** sí reordena todo el fichero: úsalo a propósito, en un commit aparte.
- Activa las copias de seguridad si vas a importar o traducir en lote.

## Solución de problemas

| Síntoma | Qué hacer |
| --- | --- |
| No aparece la ventana | Reinstala el VSIX, reinicia VS. Luego **Ver → Otras ventanas**. |
| Detectar no encuentra nada | Abre la solución que contiene los `.label.txt` (a veces están en la raíz, no dentro de un proyecto). O usa **Abrir**. Comprueba que el nombre sigue `{grupo}.{idioma}.label.txt`. |
| Un idioma no se carga y avisa de archivo duplicado | Hay dos `{grupo}.{idioma}.label.txt` iguales en carpetas distintas. Quita uno de la solución. |
| No puedo editar un archivo ("bloqueado") | No se puede reescribir idéntico sin cambios (codificación o bytes no válidos). Mira **Analizar → Diagnóstico**; corrige el archivo fuera o ábrelo con otro editor. |
| Editar label… está deshabilitado | Selecciona antes una fila (el click derecho la selecciona) y comprueba que hay un grupo cargado. |
| Una celda tiene borde rojo y no se puede editar | El ID está duplicado en ese archivo. Click derecho → **Resolver duplicado…**. |
| Guardar dice que no se ha guardado ningún archivo | Un idioma no se puede guardar y, para no dejar la label a medias, no se escribe ninguno. El mensaje lista cada archivo y el motivo (solo lectura, ANSI, cambio externo…). Corrígelo y vuelve a Guardar. |
| Guardar dice que la codificación "no admite algunos caracteres" | El archivo es ANSI. El mensaje da la línea, el Label ID y los caracteres: cámbialos (busca el Label ID en la tabla) o convierte el archivo a UTF-8 con BOM fuera del editor. |
| Al guardar avisa de que el archivo cambió fuera de VS | Otro programa (Git, otro VS) lo modificó. Recarga para ver la versión de disco o elige sobrescribir. |
| Insertar label no sale en el click derecho | Tiene que ser un `.xpp` o XML de F&O, con un grupo cargado. Comprueba en **Configuración** que las opciones del menú del editor estén marcadas. |
| No inserta en la casilla de Propiedades | Selecciona primero la propiedad (Label, HelpText, …). Usa el atajo, no hace falta el click derecho. Si falla, la referencia queda en el portapapeles (Ctrl+V). |
| No sale el texto al pasar el cursor sobre `@...` | Reinicia VS tras instalar. Comprueba **Configuración → Mostrar texto al pasar el cursor**. Detecta o abre el grupo, o edita un archivo del modelo que tenga `AxLabelFile`. No aplica a `@SYS12345` (sin `:`). |
| Nueva label no rellena el texto buscado | Solo ocurre si la búsqueda **no** encuentra nada. Revisa **Configuración → Al crear desde el buscador**. |
| Guardar está deshabilitado | No hay cambios pendientes o no hay workspace cargado. |
| Copiar no hace nada | Selecciona primero la fila. La barra de estado debe mostrar `Copiado @...`. |
| Git muestra un diff enorme | Al editar no debería: solo se reescriben líneas nuevas o cambiadas. Si usaste **Ordenar alfabéticamente**, el reordenado es esperado. Si no, recarga y comprueba *Preserve encoding* / *Preserve line endings* en Opciones. |
| Referencias en el entorno no encuentra nada | Indica `PackagesLocalDirectory` (p. ej. `K:\AosService\PackagesLocalDirectory`) en Opciones → General → *Find references*. |
| Rellenar vacías / Completar con IA están deshabilitados | No hay API key (o el modo no es Dictionary). **Herramientas → Opciones → FO Label Manager (Beta) → AI / Translation**. La clave no se guarda en el repo. |
| Rellenar vacías no pisa un texto | Es el comportamiento correcto. Solo rellena celdas vacías. |
| Algunas traducciones salen como fallidas | La IA devolvió un texto sin los placeholders, vacío o con saltos de línea. Usa **Reintentar fallidas** o escríbelas a mano. |
| La IA no se llama sola al crear una label | Correcto. Usa **Completar con IA** en el diálogo o **Rellenar vacías** después. |
| No hay copias para restaurar | Están desactivadas por defecto. Activa **Automatic backups** en Opciones (General) y vuelve a Guardar. |
| Restaurar no cambia un idioma | Esa copia no incluía ese archivo (p. ej. solo guardaste un idioma). |
