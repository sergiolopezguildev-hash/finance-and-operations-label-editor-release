# FO Label Manager (Beta) 1.0.6

Extensión para **Visual Studio 2022** que gestiona archivos `.label.txt` de **Dynamics 365 Finance & Operations** como una tabla multilenguaje.

En lugar de editar `ALG_BAL.en-US.label.txt`, `ALG_BAL.es.label.txt`, etc. por separado, ves todas las traducciones en la misma fila. Al guardar, solo se reescriben las líneas que cambian (diff pequeño en Git).

## Requisitos

- Visual Studio 2022 (Community, Professional o Enterprise), 17.0 o posterior.
- Una solución con archivos `{Grupo}.{idioma}.label.txt` (por ejemplo `ALG_BAL.en-US.label.txt`).

## Instalación

1. Descarga `FoLabelManager.vsix` de esta release.
2. En Visual Studio: **Extensiones → Administrar extensiones → Instalar desde VSIX**.
3. Elige el `.vsix` y reinicia Visual Studio.
4. Abre la solución que contiene los labels.

## Abrir la ventana

Cualquiera de estas opciones:

- **Ver → Otras ventanas → F&O Label Manager (Beta)**
- **Herramientas → Abrir F&O Label Manager (Beta)**
- Atajo **Ctrl+Shift+L**

## Cargar labels

Hace falta un **grupo** cargado (`ALG_BAL`, `ALG_LOG`, …) para editar, importar o insertar referencias en X++.

### Detectar desde la solución

1. Pulsa **Detectar** (o **Archivo → Detectar desde solución**).
2. La extensión busca `*.label.txt` junto al `.sln` y en los proyectos.
3. Confirma los grupos encontrados.
4. Si hay varios grupos, elige el activo en el desplegable **Grupo**.

### Abrir archivos a mano

1. Pulsa **Abrir** y selecciona uno o varios `*.label.txt`.
2. Si abres un solo archivo y existen hermanos del mismo grupo, pregunta si quieres abrirlos todos.

### Desde Solution Explorer

- Click derecho en un `.label.txt` → **Abrir con F&O Label Manager (Beta)**.
- Varios archivos seleccionados → **Abrir como Label Workspace**.
- También puedes arrastrar `.label.txt` sobre la ventana.

## La tabla

Cada fila es una Label ID. Las columnas de idioma se generan según los archivos cargados.

| Columna | Qué es |
| --- | --- |
| **Label ID** | Clave en el `.label.txt` (`Amount=...`). Es **case-sensitive**: `qtyNull` y `qtynull` son IDs distintos. |
| **X++** | Referencia para código: `@ALG_BAL:Amount` |
| **en-US, es, …** | Texto de cada idioma. Se guarda en memoria al salir de la celda; a disco al **Guardar** (o con Autoguardar). |
| **Estado** | `OK`, `MISSING`, `UNTRANSLATED`, `MODIFIED`, `NEW`, … |

Puedes mostrar u ocultar columnas en **Configuración → Columnas**.

### Buscar y filtrar

- Cuadro **Buscar**: filtra por ID, texto o comentarios.
- Modo: **Contiene**, **Empieza por**, **Exacto**.
- Filtro: **Todas**, **Faltantes**, **Traducidas**, **Sin traducir**, **Modificadas**, **Nuevas**, **Duplicadas**, **Errores**, **Avisos**.

**Faltante** = el ID no existe en ese idioma. **Sin traducir** = la línea existe pero el valor está vacío (`CustomerStatus=`).

## Crear una label

1. **Nueva label**, **Ctrl+N** o **Insert** (con la ventana enfocada).
2. Escribe el texto en el idioma fuente. El **Label ID** se sugiere en PascalCase (`Shipment tracking number` → `ShipmentTrackingNumber`).
3. El ID: letras, dígitos y `_`; máximo 50 caracteres; debe empezar por letra o `_`.
4. Rellena solo los idiomas que quieras. Por defecto **no** se escriben idiomas vacíos.
5. Si marcas **Rellenar idiomas vacíos**, se generan traducciones para el resto (sin pisar lo que ya hayas escrito).
6. Elige dónde insertar (final del archivo, alfabético, después de otra label, en un grupo).
7. **Crear**. Si está activa la confirmación, verás un preview de las líneas.

La label queda en memoria hasta **Guardar**, salvo que tengas **Autoguardar**.

## Insertar desde X++

Con un archivo `.xpp` o XML de F&O abierto y un grupo de labels cargado, click derecho en el editor:

| Opción | Qué hace | Atajo |
| --- | --- | --- |
| **Insertar label…** | Buscador de labels existentes. Click en una fila (o Enter) inserta `@Grupo:LabelId` en el cursor. Si hay texto seleccionado, se usa como filtro. | **Ctrl+Alt+Shift+P** |
| **Insertar nueva label…** | Crea una label nueva (mismo diálogo que en la ventana) e inserta la referencia. | **Ctrl+Alt+Shift+I** |

Si tenías texto seleccionado, se sustituye por la referencia.

Puedes ocultar estas dos opciones en **Configuración** de la herramienta o en **Herramientas → Opciones → FO Label Manager → Editor**.

## Editar textos

Haz doble click en una columna de idioma y cambia el texto. El estado pasa a `MODIFIED`. Las líneas que no tocas se conservan tal cual (encoding, BOM, CRLF, comentarios).

## Cambiar el Label ID

Afecta a **todos** los idiomas cargados de esa label.

1. Selecciona la fila.
2. **Cambiar ID**, **F2**, doble click en **Label ID**, o click derecho → **Cambiar Label ID**.
3. El ID nuevo no puede existir ya en el grupo.
4. Confirma el preview.

Cuidado: el X++ que use `@Grupo:Id` antiguo dejará de resolver.

## Eliminar una label

1. Selecciona la fila.
2. **Eliminar**, **Supr**, o click derecho → **Eliminar label**.
3. Confirma el preview. No se escribe a disco hasta **Guardar** (salvo Autoguardar).

## Copiar la referencia X++

Para pegarla en un formulario, `label()` o un literal de X++:

- Click derecho en la fila → **Copiar @Grupo:LabelId**
- **Editar → Copiar referencia X++**
- **Ctrl+Shift+C** (con la ventana enfocada)

Ejemplo: `@ALG_BAL:Amount`.

En **Configuración** puedes activar **Copiar @... al crear una label** para dejarla en el portapapeles al crear.

## Traducir labels vacías

**Traducir** solo rellena celdas vacías o labels nuevas. **No sobrescribe** traducciones existentes salvo que actives *Overwrite existing translations* en opciones.

El motor es **offline** (literal + diccionario de términos + reutilizar textos ya presentes). No llama a servicios externos.

## Importar y exportar

**Archivo → Exportar JSON / CSV** genera un informe del grupo cargado (`LabelId`, `Language`, `Value`, estado).

**Archivo → Importar JSON / CSV** aplica textos al grupo activo:

- Acepta el formato de la exportación (`LabelId,Language,Value`).
- También un CSV/JSON “ancho”: `LabelId` + una columna o propiedad por idioma (`en-US`, `es`, …). El CSV admite `;` (Excel en español).
- Crea labels nuevas y actualiza las que cambian.
- No borra nada: valores vacíos, idiomas no cargados y Label ID inválidos se omiten.
- Si **Confirmar al aplicar cambios** está activo, muestra el preview. Con **Autoguardar**, escribe a disco después.

## Analizar

En la barra o en **Analizar**:

| Acción | Para qué |
| --- | --- |
| **Validar** | Placeholders (`%1`), duplicados, IDs raros, etc. |
| **Comparar** | Diferencias de texto entre idiomas. |
| **Labels faltantes** | ID que existe en un idioma y no en otro. |
| **Labels huérfanas** | Existen en un idioma secundario pero no en el primario. |
| **Duplicados** | Mismo ID repetido o mismo texto en IDs distintos. |
| **Labels similares** | Parecidos a la fila seleccionada (typos, casing). |
| **Referencias en el modelo** | Dónde se usa `@Grupo:LabelId` en el modelo del label. |
| **Referencias en el entorno** | Igual, recorriendo `PackagesLocalDirectory` (todos los modelos del AOS). |
| **Diagnóstico** | Registro técnico de la sesión. |

`qtyNull` y `qtynull` **no se fusionan**. El validador avisa; tú decides.

Para **Referencias en el entorno**, si no detecta la carpeta, indícala en **Herramientas → Opciones → FO Label Manager → Find references → PackagesLocalDirectory**.

## Guardar y recargar

- **Guardar** / **Ctrl+S**: escribe los cambios pendientes. Si la confirmación está activa, muestra el preview exacto de líneas.
- **Recargar**: vuelve a leer los archivos del disco. Pide confirmación si hay cambios sin guardar.
- **Autoguardar** (**Configuración**): escribe a disco unos instantes después de editar, crear, renombrar o eliminar. No pide el preview de Guardar. Si hay Label ID duplicados, no guarda.

Si un archivo cambió fuera de Visual Studio, avisa antes de pisarlo.

Los backups automáticos (si los activas en Opciones) van a `%TEMP%\FoLabelManager\backups`, nunca al lado del modelo F&O.

## Atajos

| Atajo | Dónde | Acción |
| --- | --- | --- |
| **Ctrl+Shift+L** | Visual Studio | Abrir la ventana |
| **Ctrl+Alt+Shift+N** | Visual Studio | Nueva label (abre la ventana si hace falta) |
| **Ctrl+Alt+Shift+P** | Editor X++ / XML F&O | Insertar label existente |
| **Ctrl+Alt+Shift+I** | Editor X++ / XML F&O | Insertar nueva label |
| **Ctrl+N** / **Insert** | Ventana de labels | Nueva label |
| **Ctrl+S** | Ventana de labels | Guardar todo |
| **Ctrl+F** | Ventana de labels | Ir al buscador |
| **F2** | Ventana de labels | Cambiar Label ID |
| **Supr** | Ventana de labels | Eliminar label |
| **Ctrl+Shift+C** | Ventana de labels | Copiar `@Grupo:LabelId` |

## Configuración

En la propia ventana, menú **Configuración**:

- Copiar `@...` al crear una label
- Mostrar u ocultar **Insertar label** / **Insertar nueva label** en el click derecho del editor
- Solo iconos en la barra
- Autoguardar
- Confirmar al aplicar cambios (crear, traducir, renombrar, eliminar, guardar, importar)
- Columnas visibles (Label ID, X++, Estado, idiomas)

Más opciones en **Herramientas → Opciones → FO Label Manager**:

**General** — dónde insertar labels nuevas, conservar saltos de línea y encoding, backups, sugerir idiomas hermanos.

**Translation** — idioma fuente (`en-US` si está cargado), proveedor (`literal` por defecto), sobrescribir existentes (desactivado).

**Validation** — severidad de faltantes, duplicados, placeholders, etc.

**Find references** — ruta a `PackagesLocalDirectory` si no se detecta sola.

## Buenas prácticas

- Trabaja con **todos** los idiomas del grupo abiertos. Si solo abres `es`, una label nueva no se creará en `en-US`.
- Revisa el preview antes de Guardar en proyectos de cliente (o deja **Confirmar al aplicar cambios** activo).
- No renombres IDs a la ligera: el X++ con `@Grupo:Id` antiguo dejará de resolver.
- El editor no “arregla” el archivo: no reordena, no cambia casing, no toca comentarios ni líneas que no entiende.

## Solución de problemas

| Síntoma | Qué hacer |
| --- | --- |
| No aparece la ventana | Reinstala el VSIX, reinicia VS. Luego **Ver → Otras ventanas**. |
| Detectar no encuentra nada | Abre la solución que contiene los `.label.txt` (a veces están en la raíz, no dentro de un proyecto). O usa **Abrir**. |
| Insertar label no sale en el click derecho | Tiene que ser un `.xpp` o XML de F&O, con un grupo cargado. Comprueba **Configuración** que las opciones del menú del editor estén marcadas. |
| Guardar está deshabilitado | No hay cambios pendientes, no hay workspace, o hay Label ID duplicados (con Autoguardar). |
| Click derecho no copia | Selecciona primero la fila. La barra de estado debe mostrar `Copiado @...`. |
| Git muestra un diff enorme | No debería: solo se reescriben líneas nuevas o editadas. Recarga y comprueba encoding/CRLF en Opciones. |
| Referencias en el entorno no encuentra nada | Indica `PackagesLocalDirectory` (p. ej. `K:\AosService\PackagesLocalDirectory`) en Opciones. |
