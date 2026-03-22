# Guía de Contribución — Repositorio Normativo PDP

Este documento contiene las instrucciones operativas para trabajar con el repositorio. Está dirigido tanto a colaboradores humanos como a sistemas de IA (Claude / Cowork) que apoyen en la construcción y mantenimiento del repositorio.

---

## Principios de trabajo

1. **El texto oficial es sagrado.** El campo `texto_oficial` nunca se parafrasea ni resume. Siempre es el texto literal de la norma publicada en el registro oficial.
2. **Un artículo, un objeto JSON.** La unidad mínima es el artículo. No se combinan artículos en un solo objeto.
3. **Las etiquetas son consistentes.** Siempre se usan los IDs exactos de los catálogos. Nunca se inventan etiquetas nuevas sin actualizar primero el catálogo correspondiente.
4. **Los catálogos se actualizan antes que las normas.** Si necesitas una etiqueta que no existe, primero la agregas al catálogo, luego la usas en el artículo.
5. **Nada se elimina, solo se marca.** Los artículos derogados no se borran. Se marcan con `vigente: false` y `derogado: true`.

---

## Cómo agregar una norma nueva

### Paso 1 — Identificar la norma

Antes de crear el archivo, verifica:
- ¿En qué jurisdicción aplica? (`ec` / `cl`)
- ¿Qué tipo de fuente es? (ver `catalogos/tipos-fuente.json`)
- ¿Qué nivel jerárquico tiene?
- ¿Está vigente?
- ¿Cuál es la URL oficial del texto?

### Paso 2 — Nombrar el archivo

El nombre del archivo sigue este patrón:
```
[jurisdiccion]-[nombre-corto-norma].json
```

Ejemplos:
```
ec-lopdp.json
ec-reglamento-lopdp.json
cl-ley-pdp.json
```

Siempre en minúsculas, sin espacios, con guiones.

### Paso 3 — Crear el archivo

Ubicarlo en la carpeta correcta según tipo de fuente:
```
/ec/ley-organica/ec-lopdp.json
/ec/reglamentos/ec-reglamento-lopdp.json
/cl/ley/cl-ley-pdp.json
```

### Paso 4 — Poblar el meta de la norma

```json
{
  "meta": {
    "id": "EC-LOPDP",
    "jurisdiccion": "ec",
    "tipo_fuente": "ley-organica",
    "nivel_jerarquico": 3,
    "titulo": "Nombre oficial completo de la norma",
    "numero": "Número o identificador oficial",
    "fecha_publicacion": "YYYY-MM-DD",
    "fecha_vigencia": "YYYY-MM-DD",
    "vigente": true,
    "materia": ["pdp"],
    "es_especializada": true,
    "url_oficial": "URL del registro oficial",
    "preambulo": "Texto del preámbulo o considerandos si los tiene, o cadena vacía.",
    "anexos": [],
    "version": "1.0.0"
  },
  "unidades": []
}
```

### Paso 5 — Extraer y agregar los artículos

Ver sección "Cómo extraer artículos" más abajo.

---

## Cómo extraer artículos

### Si trabajas con un PDF o texto de la norma

1. Extraer el texto de cada artículo de forma literal, sin modificaciones
2. Crear un objeto JSON por artículo siguiendo el esquema
3. Agregar el objeto al array `unidades` del archivo de la norma

### Esquema de cada artículo

```json
{
  "id": "EC-LOPDP-Art-13",
  "tipo": "articulo",
  "numero": "13",
  "titulo": "Título oficial del artículo si lo tiene, o vacío",
  "texto_oficial": "Texto literal completo del artículo, incluyendo todos sus literales e incisos.",
  "estado": "vigente",
  "sustituido_por": null,
  "temas": [],
  "roles": [],
  "ciclo": [],
  "deontico": [],
  "factores_riesgo": [],
  "concordancias": [],
  "comentario": "",
  "notas": ""
}
```

### Convención para el ID del artículo

```
[JURISDICCION]-[NORMA]-Art-[NUMERO]
```

Ejemplos:
```
EC-LOPDP-Art-1
EC-LOPDP-Art-13
CL-LPDP-Art-5
```

Si la norma tiene artículos con letras (Art. 4-A), usar:
```
EC-LOPDP-Art-4A
```

---

## Cómo etiquetar un artículo

El etiquetado se hace en cinco dimensiones. Cada una usa IDs de los catálogos correspondientes.

### 1. temas

Usar `temas.json` (y extensiones por jurisdicción si aplica).
Formato: `"tema"` o `"tema.subtema"`

```json
"temas": ["bases-de-licitud.consentimiento", "principios.transparencia"]
```

Regla: un artículo puede tener varios temas. Incluir todos los que apliquen directamente al contenido del artículo.

### 2. roles

¿A quién se dirige o afecta este artículo?

Valores válidos:
- `responsable`
- `encargado`
- `subencargado`
- `titular`
- `dpd`
- `autoridad-control`
- `tercero`

```json
"roles": ["responsable", "titular"]
```

### 3. ciclo

¿En qué fase del ciclo de vida del dato opera este artículo?

Valores válidos:
- `recoleccion` — obtención de datos directamente del titular o de terceros
- `creacion` — generación de datos por la propia organización
- `inferencia` — derivación de nuevos datos personales a partir de datos existentes
- `almacenamiento` — conservación y custodia de los datos
- `uso` — utilización interna para el fin declarado
- `comunicacion` — transmisión a terceros dentro de la misma jurisdicción
- `transferencia` — transmisión a terceros en otras jurisdicciones
- `supresion` — eliminación o anonimización de los datos
- `archivo` — conservación con fines históricos, estadísticos o de investigación

```json
"ciclo": ["recoleccion", "uso"]
```

### 4. deontico

¿Qué tipo de norma es?

Valores válidos:
- `obligacion` — el responsable/encargado debe hacer algo
- `prohibicion` — está expresamente prohibido
- `derecho` — reconoce un derecho al titular
- `permiso` — autoriza algo bajo condiciones
- `definicion` — define un concepto
- `excepcion` — establece una excepción a otra regla

```json
"deontico": ["obligacion"]
```

### 5. factores_riesgo

¿Qué factores de riesgo del catálogo AEPD activa o regula este artículo?
Usar IDs del archivo `catalogos/factores-riesgo-aepd.json`.

Formato: `"categoria.factor"`

```json
"factores_riesgo": ["operaciones-fines.perfilado", "tipos-datos.datos-salud"]
```

Si el artículo no activa factores de riesgo específicos, dejar el array vacío `[]`.

---

## Cómo agregar concordancias

Las concordancias son artículos de otras normas (de la misma jurisdicción) que se relacionan directamente con este artículo.

```json
"concordancias": ["EC-LOPDP-Art-7", "EC-LOPDP-Art-14", "EC-REGLAMENTO-LOPDP-Art-5"]
```

Regla: solo concordancias de la misma jurisdicción. Las equivalencias entre jurisdicciones van en `/mapeos/concordancias-ec-cl.json`.

---

## Cómo gestionar el preámbulo y los considerandos

Si la norma tiene preámbulo, exposición de motivos o considerandos, copiar el texto completo en el campo `preambulo` del `meta`. Si no tiene, dejar como cadena vacía `""`.

El preámbulo no se incluye en `unidades`. No se etiqueta. Solo está disponible para consulta interpretativa cuando se necesite contexto del legislador.

---

## Cómo gestionar anexos

### Anexo corto o de referencia frecuente

Agregar dentro del array `unidades` con `"tipo": "anexo"`. No lleva las dimensiones de etiquetado temático (temas, roles, ciclo, deontico, factores_riesgo) a menos que el contenido del anexo lo justifique explícitamente.

```json
{
  "id": "EC-RESOLUCION-001-Anexo-1",
  "tipo": "anexo",
  "numero": "1",
  "titulo": "Formulario de registro de actividades de tratamiento",
  "texto_oficial": "Contenido completo del anexo...",
  "estado": "vigente",
  "sustituido_por": null,
  "temas": [],
  "roles": [],
  "ciclo": [],
  "deontico": [],
  "factores_riesgo": [],
  "concordancias": [],
  "comentario": "",
  "notas": ""
}
```

### Anexo extenso o técnico

Crear un archivo JSON separado en la misma carpeta de la norma principal, siguiendo la convención de nombre:

```
[nombre-norma]-anexo-[numero].json
```

Ejemplo:
```
/ec/normativa-secundaria/ec-resolucion-001.json
/ec/normativa-secundaria/ec-resolucion-001-anexo-1.json
```

En el `meta` de la norma principal, referenciar el archivo:

```json
"anexos": ["ec-resolucion-001-anexo-1.json"]
```

El archivo del anexo sigue la misma estructura de norma (`meta` + `unidades`), donde cada sección o tabla del anexo es una unidad con `"tipo": "anexo"`.

---

## Cómo gestionar reformas y derogaciones

### Artículo reformado (texto cambia pero sigue vigente)

```json
"estado": "reformado",
"notas": "Reformado por [norma] el [fecha]. Texto anterior: [texto previo]"
```

### Artículo derogado sin reemplazo

```json
"estado": "derogado",
"sustituido_por": null,
"notas": "Derogado por [norma] el [fecha]."
```

### Artículo derogado con reemplazo

```json
"estado": "derogado",
"sustituido_por": "EC-LOPDP-Art-15B",
"notas": "Derogado por [norma] el [fecha]. Reemplazado por Art. 15-B."
```

Importante: el texto original siempre se conserva. Nunca se borra.

---

## Cómo actualizar los catálogos

### Agregar un tema nuevo

1. Abrir `catalogos/temas.json`
2. Agregar el objeto con `id`, `label`, `descripcion` y `parent` si es subtema
3. Si aplica solo a una jurisdicción, agregarlo en `temas-ec.json` o `temas-cl.json` con una nota explicativa

### Agregar una materia nueva

1. Abrir `catalogos/materias.json`
2. Agregar el nuevo valor con `id`, `label` y `descripcion`
3. Actualizar `README.md` con la nueva materia

### Agregar un factor de riesgo

Los factores de riesgo del catálogo AEPD no se modifican. Si hay factores adicionales relevantes para Latinoamérica, agregarlos en una sección separada dentro de `factores-riesgo-aepd.json` con prefijo `latam`.

---

## Convenciones generales

### Fechas
Siempre en formato ISO: `YYYY-MM-DD`

### IDs
Siempre en mayúsculas para normas: `EC-LOPDP`
Siempre en minúsculas con guiones para etiquetas: `bases-de-licitud.consentimiento`

### Texto oficial
- Copiar literalmente, incluyendo puntuación original
- Conservar saltos de párrafo usando `\n`
- No corregir errores tipográficos del texto oficial — documentarlos en `notas`

### Versioning del archivo de norma
Cada vez que se modifica un artículo, actualizar el campo `version` del `meta` de la norma:
- Cambio menor (notas, comentarios): incrementar tercer número `1.0.0 → 1.0.1`
- Modificación de artículo: incrementar segundo número `1.0.0 → 1.1.0`
- Derogación o reestructuración mayor: incrementar primer número `1.0.0 → 2.0.0`

---

## Instrucciones específicas para Claude / Cowork

Cuando se te pida trabajar con este repositorio:

1. **Antes de etiquetar**, lee `catalogos/temas.json` y `catalogos/factores-riesgo-aepd.json` para usar los IDs correctos
2. **Antes de crear una norma**, lee `catalogos/tipos-fuente.json` y `catalogos/jurisdicciones.json`
3. **El texto oficial nunca se modifica**. Si hay ambigüedad, documéntala en `notas`
4. **Ante conflicto normativo**, consulta `catalogos/hermeneutica.json` antes de emitir criterio
5. **Si necesitas una etiqueta que no existe**, propónla al usuario antes de usarla
6. **Un commit por norma**. No mezclar varias normas en un solo commit
7. **Mensaje de commit estándar:**
   - Nueva norma: `feat(ec): agregar LOPDP completa`
   - Modificación: `fix(ec): actualizar Art-13 por reforma [fecha]`
   - Catálogo: `chore(catalogos): agregar tema ia-y-decisiones-automatizadas`
