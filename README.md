# FO Label Manager (Beta) 1.0.6

Extensión para **Visual Studio 2022** que gestiona archivos `.label.txt` de **Dynamics 365 Finance & Operations** como una tabla multilenguaje.

En lugar de editar `ALG_BAL.en-US.label.txt`, `ALG_BAL.es.label.txt`, etc. por separado, ves todas las traducciones en la misma fila. Al guardar, solo se reescriben las líneas que cambian (diff pequeño en Git), salvo que uses **Ordenar alfabéticamente**.

## Novedades de esta versión

- Al pasar el cursor sobre `@Grupo:LabelId` (en X++, XML u otros archivos) aparece el texto de la label. Se puede desactivar y elegir qué idiomas muestra.
- Si buscas un texto que **no existe** y pulsas **Nueva label**, el diálogo se rellena con esa búsqueda. Configurable: todos los idiomas, solo el fuente, o uno concreto.
- Formato del Label ID sugerido: **PascalCase**, **camelCase** o conservar las mayúsculas del texto.
- Las labels nuevas se insertan **en orden alfabético** (puedes elegir otro modo al crear).
- **Analizar → Ordenar alfabéticamente**: reordena cada `.label.txt` por Label ID. Los comentarios van con su label.
- Al **importar**, si el ID ya existe y el texto difiere, pregunta si conservas el actual o usas el importado.
- Click derecho en X++: **Insertar label…** (buscar una existente) e **Insertar nueva label…**.
- En la ventana **Propiedades** del diseñador, puedes crear o elegir una label y dejar `@Grupo:Id` en casillas como Label, HelpText o Caption (**Ctrl+Alt+Shift+I** / **P**).
- **Rellenar vacías** y **Completar con IA**: traducen celdas vacías con OpenAI (opcional). No pisan textos existentes. La API key se guarda en el Administrador de credenciales de Windows. Sin clave (y sin modo Dictionary), esos botones quedan **deshabilitados**.
- **Copias de seguridad** (desactivadas por defecto): al Guardar se guarda un snapshot en `%TEMP%`. Puedes restaurar uno anterior y limitar cuántos se conservan.

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

Hace falta un **grupo** cargado (`ALG_BAL`, `ALG_LOG`, …) para editar, importar o insertar referencias en X++ / Propiedades.

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
2. Escribe el texto en el idioma fuente. El **Label ID** se sugiere según **Configuración → Formato del Label ID sugerido**:
   - **PascalCase** (por defecto): `CUSTOMER ORDER` → `CustomerOrder`
   - **camelCase**: `CUSTOMER ORDER` → `customerOrder`
   - **Conservar mayúsculas del texto**: `CUSTOMER ORDER` → `CUSTOMERORDER`
3. El ID: letras, dígitos y `_`; máximo 50 caracteres; debe empezar por letra o `_`.
4. Rellena solo los idiomas que quieras. Por defecto **no** se escriben idiomas vacíos.
5. **Completar con IA** (junto al idioma fuente): escribe el texto en castellano (u otro idioma), deja el Label ID en inglés y pulsa el botón. Rellena **todos** los idiomas vacíos (incluido en-US) a partir de ese texto. No pisa lo que ya hayas escrito. Sin API key el botón está deshabilitado.
6. Si marcas **Rellenar idiomas vacíos**, se generan traducciones **locales** (diccionario) para el resto, sin IA.
7. Elige dónde insertar. Por defecto **alfabético**; también al final del archivo, después de otra label o en un grupo.
8. **Crear**. Si está activa la confirmación, verás un preview de las líneas.

La label queda en memoria hasta **Guardar**, salvo que tengas **Autoguardar**.

### Crear desde una búsqueda sin resultados

Si escribes en el buscador un texto que **no coincide con ninguna label** y pulsas **Nueva label**, el diálogo se abre con ese texto ya escrito (y el Label ID sugerido). Luego cambias lo que necesites.

En **Configuración → Al crear desde el buscador**:

| Opción | Qué rellena |
| --- | --- |
| **No rellenar** | El diálogo sale vacío |
| **Solo idioma fuente** | Solo el idioma fuente (p. ej. `en-US`) |
| **Todos los idiomas** | El mismo texto en todos los archivos cargados (por defecto) |
| **Solo este idioma** | El idioma que elijas (`es`, `nl`, …) |

Si la búsqueda ya encontró alguna label, no se rellena. Al crear desde el editor X++, se sigue usando el texto seleccionado solo en el idioma fuente.

## Insertar desde X++ o Propiedades

Con un archivo `.xpp`, un XML de F&O o el **diseñador** abierto, y un grupo de labels cargado:

| Opción | Qué hace | Atajo |
| --- | --- | --- |
| **Insertar label…** | Buscador de labels existentes. Click en una fila (o Enter) inserta `@Grupo:LabelId`. Si hay texto seleccionado, se usa como filtro. | **Ctrl+Alt+Shift+P** |
| **Insertar nueva label…** | Crea una label nueva e inserta la referencia. | **Ctrl+Alt+Shift+I** |

**En el editor** (X++ / XML): click derecho, o el atajo, con el cursor donde quieras la referencia. Si había texto seleccionado, se sustituye.

**En la ventana Propiedades** (casilla de un control, campo, menú, …):

1. Abre el formulario / tabla / EDT en el diseñador.
2. Selecciona el objeto.
3. En **Propiedades**, pulsa la casilla **Label**, **HelpText**, **Caption**, etc. (puedes estar editándola).
4. **Ctrl+Alt+Shift+I** para crear una label nueva, o **Ctrl+Alt+Shift+P** para elegir una existente.
5. La referencia `@Grupo:LabelId` se escribe en esa propiedad.

También en **Herramientas → F&O Label Manager**. Si no se puede escribir en la casilla, la referencia queda en el portapapeles para pegarla con Ctrl+V.

Puedes ocultar las opciones del click derecho del editor en **Configuración** o en **Herramientas → Opciones → FO Label Manager → Editor**.

## Texto al pasar el cursor sobre @...

Si dejas el cursor sobre una referencia `@Grupo:LabelId` en el editor, sale un tooltip con el texto de esa label:

```
@ALG_BAL:Amount
en-US: Amount
es: Importe
```

Funciona en **X++**, **XML de F&O** y otros archivos de texto. No hace falta tener la ventana de labels enfocada.

El texto se busca así:

1. En el grupo **cargado** (incluye cambios sin guardar).
2. Si ese grupo no está abierto, en los `.label.txt` del **modelo** del archivo actual (`AxLabelFile`).

Si la referencia no existe, no se muestra nada. Las labels de sistema sin dos puntos (`@SYS33480`) no se resuelven.

En **Configuración**:

| Opción | Qué hace |
| --- | --- |
| **Mostrar texto al pasar el cursor sobre @...** | Activa o desactiva el tooltip (por defecto activo). |
| **Idiomas del tooltip → Todos los idiomas** | Muestra cada idioma encontrado. |
| **Idiomas del tooltip → Solo estos idiomas** | Marca `en-US`, `es`, `nl`, … |

También en **Herramientas → Opciones → FO Label Manager → Editor** (*Show label text on hover* y *Hover languages*, p. ej. `en-US;es`; vacío = todos).

Tras instalar o actualizar el VSIX, **reinicia Visual Studio** para que el tooltip cargue.

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

Al crear una label, la IA **no** se llama sola. El checkbox **Rellenar idiomas vacíos** usa solo el diccionario local. Si quieres IA en ese diálogo, pulsa **Completar con IA** (hace falta API key). En el grupo ya cargado, usa **Rellenar vacías**.

| Acción | Qué hace |
| --- | --- |
| **Rellenar vacías** | Todas las celdas vacías del grupo (idioma origen → idiomas cargados). |
| **Rellenar vacías...** | Eliges origen, destinos y si quieres revisar. |
| **Rellenar vacías y revisar** | Muestra un diálogo para aceptar, editar o rechazar cada traducción. |
| **Traducir selección** | Solo las filas seleccionadas. |
| **Completar con IA** | En **Nueva label**: traduce los idiomas vacíos a partir del texto que hayas escrito (p. ej. en `es`). |

Solo toca celdas **vacías** (o labels que aún no existen en ese idioma). Nunca pisa un texto existente. No escribe a disco: hay que **Guardar**.

Orden interno: traducción ya presente en el grupo → término exacto del diccionario → caché → IA (si está configurada). Agrupa por idioma, deduplica textos iguales y manda lotes (no una petición por label).

Puedes **Cancelar** a mitad: se conservan las que ya hayan llegado.

### API key (OpenAI)

**Herramientas → Opciones → FO Label Manager → AI / Translation**:

- Provider: OpenAI
- API Key: se guarda en el **Administrador de credenciales de Windows**, no en el proyecto ni en el VSIX. La UI nunca muestra la clave completa.
- Modelo (p. ej. `gpt-4o-mini`)
- Modo: **Dictionary** (sin red), **AI**, o **AI + Dictionary** (recomendado)
- **Test connection** / **Eliminar API key**

Sin API key:

- **Completar con IA** queda deshabilitado (el tooltip indica dónde configurar la clave).
- **Rellenar vacías**, **Rellenar vacías...**, **Rellenar vacías y revisar**, **Traducir selección** y **Reintentar fallidas** también quedan deshabilitados, salvo que el modo sea **Dictionary** (solo diccionario local, sin red).
- Al guardar la clave en Opciones, los botones se habilitan de inmediato.

La barra de estado muestra `AI: Ready`, `AI: Not configured` o `AI: Off`.

## Importar y exportar

**Archivo → Exportar JSON / CSV** genera un informe del grupo cargado (`LabelId`, `Language`, `Value`, estado).

**Archivo → Importar JSON / CSV** aplica textos al grupo activo:

- Acepta el formato de la exportación (`LabelId,Language,Value`).
- También un CSV/JSON “ancho”: `LabelId` + una columna o propiedad por idioma (`en-US`, `es`, …). El CSV admite `;` (Excel en español).
- Crea labels nuevas (en orden alfabético) y actualiza las que cambian.
- No borra nada: valores vacíos, idiomas no cargados y Label ID inválidos se omiten.
- Si el ID ya existe y el texto es distinto, un diálogo pide **conservar el actual** o **usar el importado**.
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
| **Ordenar alfabéticamente** | Reordena cada archivo por Label ID. Los comentarios se mueven con su label. Si ya estaban ordenados, no hace nada. Git verá un diff grande si el fichero no estaba ordenado. |
| **Referencias en el modelo** | Dónde se usa `@Grupo:LabelId` en el modelo del label. |
| **Referencias en el entorno** | Igual, recorriendo `PackagesLocalDirectory` (todos los modelos del AOS). |
| **Diagnóstico** | Registro técnico de la sesión. |

`qtyNull` y `qtynull` **no se fusionan**. El validador avisa; tú decides.

Para **Referencias en el entorno**, si no detecta la carpeta, indícala en **Herramientas → Opciones → FO Label Manager → Find references → PackagesLocalDirectory**.

## Guardar y recargar

- **Guardar** / **Ctrl+S**: escribe los cambios pendientes. Si la confirmación está activa, muestra el preview exacto de líneas.
- **Recargar**: vuelve a leer los archivos del disco. Pide confirmación si hay cambios sin guardar.
- **Restaurar copia de seguridad...** (**Archivo**): lista los snapshots del grupo y sustituye en disco los `.label.txt` cargados. Pide confirmación. Los cambios sin guardar se pierden.
- **Autoguardar** (**Configuración**): escribe a disco unos instantes después de editar, crear, renombrar o eliminar. No pide el preview de Guardar. Si hay Label ID duplicados, no guarda. Si las copias están activas, también genera snapshot.

Si un archivo cambió fuera de Visual Studio, avisa antes de pisarlo.

### Copias de seguridad

Desactivadas por defecto. **Herramientas → Opciones → FO Label Manager → General**:

- **Automatic backups**: al Guardar (o Autoguardar) copia el estado *anterior* de los archivos a `%TEMP%\FoLabelManager\backups\{grupo}\{fecha}\`. Nunca junto al modelo F&O ni en Git.
- **Maximum backups to keep**: cuántas copias conservar por grupo (1–100, por defecto 10). Las más antiguas se borran.

**Archivo → Restaurar copia de seguridad...** para volver a una de ellas.

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
| **Ctrl+Shift+C** | Ventana de labels | Copiar `@Grupo:LabelId` |

## Configuración

En la propia ventana, menú **Configuración**:

- **Al crear desde el buscador** — no rellenar / idioma fuente / todos / un idioma concreto
- **Formato del Label ID sugerido** — PascalCase, camelCase o conservar mayúsculas
- Copiar `@...` al crear una label
- Mostrar u ocultar **Insertar label** / **Insertar nueva label** en el click derecho del editor
- **Mostrar texto al pasar el cursor sobre @...** y **Idiomas del tooltip**
- Solo iconos en la barra
- Autoguardar
- Confirmar al aplicar cambios (crear, traducir, renombrar, eliminar, guardar, importar)
- Columnas visibles (Label ID, X++, Estado, idiomas)

Más opciones en **Herramientas → Opciones → FO Label Manager**:

**General** — dónde insertar labels nuevas, conservar saltos de línea y encoding, copias de seguridad (activarlas y cuántas guardar), sugerir idiomas hermanos.

**New label** — prellenado desde el buscador y el idioma concreto si eliges *SpecificLanguage*.

**Editor** — estilo del Label ID sugerido; mostrar u ocultar Insertar label / Insertar nueva label; tooltip al pasar el cursor sobre `@Grupo:LabelId` y qué idiomas muestra.

**Translation** — idioma fuente (`en-US` si está cargado), proveedor local (`literal` por defecto).

**AI / Translation** — API key (Windows Credential Manager), modelo, modo Dictionary / AI / AI + Dictionary, lotes y concurrencia. Sin clave, Completar con IA y Rellenar vacías (modos AI) quedan deshabilitados.

**Validation** — severidad de faltantes, duplicados, placeholders, etc.

**Find references** — ruta a `PackagesLocalDirectory` si no se detecta sola.

## Buenas prácticas

- Trabaja con **todos** los idiomas del grupo abiertos. Si solo abres `es`, una label nueva no se creará en `en-US`.
- Revisa el preview antes de Guardar en proyectos de cliente (o deja **Confirmar al aplicar cambios** activo).
- No renombres IDs a la ligera: el X++ con `@Grupo:Id` antiguo dejará de resolver.
- Editar una celda no reescribe el resto del archivo (encoding, comentarios y líneas ajenas se conservan).
- **Ordenar alfabéticamente** sí reordena todo el fichero: úsalo a propósito; Git verá un diff grande si no estaba ordenado.

## Solución de problemas

| Síntoma | Qué hacer |
| --- | --- |
| No aparece la ventana | Reinstala el VSIX, reinicia VS. Luego **Ver → Otras ventanas**. |
| Detectar no encuentra nada | Abre la solución que contiene los `.label.txt` (a veces están en la raíz, no dentro de un proyecto). O usa **Abrir**. |
| Insertar label no sale en el click derecho | Tiene que ser un `.xpp` o XML de F&O, con un grupo cargado. Comprueba **Configuración** que las opciones del menú del editor estén marcadas. |
| No inserta en la casilla de Propiedades | Selecciona primero la propiedad (Label, HelpText, …). Usa el atajo, no hace falta el click derecho. Si falla, la referencia queda en el portapapeles (Ctrl+V). |
| No sale el texto al pasar el cursor sobre `@...` | Reinicia VS tras instalar. Comprueba **Configuración → Mostrar texto al pasar el cursor**. Detecta o abre el grupo, o edita un archivo del modelo que tenga `AxLabelFile`. No aplica a `@SYS12345` (sin `:`). |
| Nueva label no rellena el texto buscado | Solo ocurre si la búsqueda **no** encuentra nada. Revisa **Configuración → Al crear desde el buscador**. |
| Guardar está deshabilitado | No hay cambios pendientes, no hay workspace, o hay Label ID duplicados (con Autoguardar). |
| Click derecho no copia | Selecciona primero la fila. La barra de estado debe mostrar `Copiado @...`. |
| Git muestra un diff enorme | Al editar no debería: solo se reescriben líneas nuevas o cambiadas. Si usaste **Ordenar alfabéticamente**, el reordenado de todo el fichero es esperado. Si no, recarga y comprueba encoding/CRLF en Opciones. |
| Referencias en el entorno no encuentra nada | Indica `PackagesLocalDirectory` (p. ej. `K:\AosService\PackagesLocalDirectory`) en Opciones. |
| Rellenar vacías / Completar con IA están deshabilitados | No hay API key (o el modo no es Dictionary). **Herramientas → Opciones → FO Label Manager → AI / Translation**. La clave no se guarda en el repo. |
| Rellenar vacías no pisa un texto | Es el comportamiento correcto. Solo rellena celdas vacías. |
| La IA no se llama sola al crear una label | Correcto. Usa **Completar con IA** en el diálogo o **Rellenar vacías** después. |
| No hay copias para restaurar | Están desactivadas por defecto. Activa **Automatic backups** en Opciones (General) y vuelve a Guardar. |
| Restaurar no cambia un idioma | Esa copia no incluía ese archivo (p. ej. solo guardaste un idioma). |
