# Repositorio Normativo PDP

Base de conocimiento normativo estructurado para consultoría en protección de datos personales (PDP). Desarrollado y mantenido por PDP Expert.

## ¿Qué es este repositorio?

Este repositorio contiene el texto oficial de normas de protección de datos personales, organizadas, indexadas y etiquetadas para ser consumidas por sistemas de inteligencia artificial (Claude) en el contexto de consultoría jurídica especializada.

No es un archivo pasivo. Es una base de conocimiento viva que:
- Permite a Claude citar artículos con precisión jurídica
- Soporta análisis normativo comparado entre jurisdicciones
- Facilita la identificación de factores de riesgo en tratamientos de datos
- Escala hacia otras materias (IA, ciberseguridad, datos de menores)

## Jurisdicciones cubiertas

| Código | País | Estado |
|---|---|---|
| `ec` | Ecuador | Activo |
| `cl` | Chile | En construcción |

## Tipos de fuentes incluidas

El repositorio respeta la jerarquía normativa de cada jurisdicción:

1. Constitución / jurisprudencia constitucional
2. Ley orgánica
3. Ley ordinaria
4. Reglamentos
5. Normativa secundaria
6. Pronunciamientos y criterios de la autoridad de control (no vinculantes)
7. Jurisprudencia ordinaria
8. Doctrina y comentarios propios

## Estructura del repositorio

```
/repositorio-normativo-pdp/
│
├── /catalogos/           ← catálogos globales reutilizables
├── /ec/                  ← normas Ecuador, organizadas por tipo de fuente
├── /cl/                  ← normas Chile, organizadas por tipo de fuente
└── /mapeos/              ← relaciones transversales entre jurisdicciones y sectores
```

### /catalogos

Contiene los catálogos de referencia que se usan para etiquetar los artículos:

| Archivo | Contenido |
|---|---|
| `temas.json` | Taxonomía temática PDP — 13 temas, 2 niveles |
| `temas-ec.json` | Extensiones temáticas para Ecuador |
| `temas-cl.json` | Extensiones temáticas para Chile |
| `materias.json` | Materias regulatorias (pdp, ia, datos-menores, ciberseguridad...) |
| `tipos-fuente.json` | Jerarquía normativa global con nivel de prelación |
| `jurisdicciones.json` | Metadatos de cada jurisdicción cubierta |
| `factores-riesgo-aepd.json` | Catálogo AEPD de factores de riesgo — base europea adaptada |
| `hermeneutica.json` | Reglas de interpretación normativa — se consulta solo ante conflicto |

### /ec y /cl

Cada jurisdicción tiene su propia carpeta organizada por tipo de fuente. Dentro de cada subcarpeta, cada norma es un archivo JSON independiente.

Ejemplo:
```
/ec/ley-organica/lopdp.json         ← Ley Orgánica de Protección de Datos Personales
/ec/reglamentos/reglamento-lopdp.json
/ec/pronunciamientos/sentencia-001.json
```

### /mapeos

Relaciones que cruzan jurisdicciones o sectores:

| Archivo | Contenido |
|---|---|
| `concordancias-ec-cl.json` | Equivalencias de artículos entre Ecuador y Chile |
| `sectorial-financiero.json` | Mapeo de normas PDP con regulación financiera |
| `sectorial-salud.json` | Mapeo de normas PDP con regulación sanitaria |

## Esquema de una norma

Cada archivo de norma tiene dos secciones: `meta` (datos de la norma completa) y `unidades` (artículos).

```json
{
  "meta": {
    "id": "EC-LOPDP",
    "jurisdiccion": "ec",
    "tipo_fuente": "ley-organica",
    "nivel_jerarquico": 3,
    "titulo": "Ley Orgánica de Protección de Datos Personales",
    "numero": "Ley s/n",
    "fecha_publicacion": "2021-05-26",
    "fecha_vigencia": "2023-05-26",
    "vigente": true,
    "materia": ["pdp"],
    "es_especializada": true,
    "url_oficial": "https://...",
    "preambulo": "Texto completo del preámbulo o considerandos de la norma, si los tiene.",
    "anexos": [],
    "version": "1.0.0"
  },
  "unidades": [
    {
      "id": "EC-LOPDP-Art-13",
      "tipo": "articulo",
      "numero": "13",
      "titulo": "Del consentimiento",
      "texto_oficial": "Texto oficial completo del artículo...",
      "estado": "vigente",
      "sustituido_por": null,
      "temas": ["bases-de-licitud.consentimiento"],
      "roles": ["responsable", "titular"],
      "ciclo": ["recoleccion"],
      "deontico": ["obligacion"],
      "factores_riesgo": ["operaciones-fines.perfilado"],
      "concordancias": ["EC-LOPDP-Art-7", "EC-LOPDP-Art-14"],
      "comentario": "Criterio interpretativo propio.",
      "notas": ""
    }
  ]
}
```

## Preámbulo y considerandos

Algunas normas tienen preámbulos, exposiciones de motivos o considerandos con valor interpretativo. Se almacenan en el campo `preambulo` del `meta` de la norma, no como unidades. No se consultan en análisis normativo ordinario pero están disponibles cuando el análisis requiere contexto del legislador.

## Anexos

Los anexos de normativa secundaria (formularios, tablas, listas técnicas) se gestionan según su extensión:

**Anexos cortos o de referencia frecuente** — se incluyen dentro del array `unidades` con `"tipo": "anexo"`:

```json
{
  "id": "EC-RESOLUCION-001-Anexo-1",
  "tipo": "anexo",
  "titulo": "Formulario de registro de actividades de tratamiento",
  "texto_oficial": "Contenido completo del anexo...",
  "estado": "vigente",
  "sustituido_por": null,
  "notas": ""
}
```

**Anexos extensos o técnicos** — se almacenan en un archivo JSON separado en la misma carpeta, y se referencian desde el `meta` de la norma principal:

```
/ec/normativa-secundaria/ec-resolucion-001.json
/ec/normativa-secundaria/ec-resolucion-001-anexo-1.json
```

```json
"meta": {
  ...
  "anexos": ["ec-resolucion-001-anexo-1.json"]
}
```

## Estados de un artículo

Cada artículo tiene un único campo `estado` que refleja su situación normativa:

| Estado | Significado |
|---|---|
| `vigente` | Texto en vigor sin cambios desde su publicación |
| `reformado` | Texto modificado, sigue en vigor con nuevo contenido |
| `derogado` | Ya no está en vigor |

Los artículos derogados nunca se eliminan del repositorio. Su texto se conserva para consulta histórica. Las consultas normales filtran solo artículos con `estado: vigente` o `estado: reformado`.

## Ciclo de vida del dato

El campo `ciclo` etiqueta en qué fase del tratamiento opera el artículo:

| Valor | Descripción |
|---|---|
| `recoleccion` | Obtención de datos directamente del titular o de terceros |
| `creacion` | Generación de datos por la propia organización |
| `inferencia` | Derivación de nuevos datos personales a partir de datos existentes |
| `almacenamiento` | Conservación y custodia de los datos |
| `uso` | Utilización interna de los datos para el fin declarado |
| `comunicacion` | Transmisión de datos a terceros dentro de la misma jurisdicción |
| `transferencia` | Transmisión de datos a terceros en otras jurisdicciones |
| `supresion` | Eliminación o anonimización de los datos |
| `archivo` | Conservación con fines históricos, estadísticos o de investigación |

## Taxonomía temática (resumen)

Los artículos se etiquetan con temas de dos niveles (`tema.subtema`):

| # | Tema | Ejemplo de subtema |
|---|---|---|
| 1 | `principios` | `licitud`, `finalidad`, `proporcionalidad` |
| 2 | `bases-de-licitud` | `consentimiento`, `interes-legitimo`, `obligacion-legal` |
| 3 | `datos-sensibles` | `salud`, `biometricos`, `origen-racial` |
| 4 | `derechos-del-titular` | `acceso`, `rectificacion`, `supresion`, `portabilidad` |
| 5 | `obligaciones-responsable` | `registro-actividades`, `evaluacion-impacto`, `dpd` |
| 6 | `encargado-del-tratamiento` | `contrato-encargo`, `subencargados` |
| 7 | `categorias-especiales` | uso limitado en Ecuador — ver nota en temas-ec.json |
| 8 | `transferencias-internacionales` | `paises-adecuados`, `garantias-apropiadas` |
| 9 | `seguridad-del-tratamiento` | `medidas-tecnicas`, `notificacion-brechas` |
| 10 | `autoridad-de-control` | `competencia`, `poderes`, `procedimiento-sancionador` |
| 11 | `ia-y-decisiones-automatizadas` | `perfilado`, `explicabilidad` |
| 12 | `tratamiento-gran-escala` | `eipd-obligatoria` |
| 13 | `ambito-de-aplicacion` | `definiciones-clave`, `exclusiones` |

## Política de versioning

- La unidad mínima de cambio es el **artículo**, no la norma completa
- Cuando un artículo se reforma: `estado: "reformado"`, se actualiza el texto, el anterior se documenta en `notas`
- Cuando un artículo se deroga: `estado: "derogado"`, el texto se conserva
- Si el artículo es reemplazado por otro: `sustituido_por: "EC-LOPDP-Art-XX"`
- Las consultas normales devuelven artículos con `estado: vigente` o `estado: reformado`
- El historial completo siempre está disponible en Git

## Escalabilidad

Este repositorio está diseñado para crecer:

- **Nuevas jurisdicciones:** agregar carpeta `/xx/` y entrada en `jurisdicciones.json`
- **Nuevas materias:** agregar valor en `materias.json` (ej. `ia`, `ciberseguridad`)
- **Nuevos sectores:** agregar archivo en `/mapeos/`
- **API de acceso:** la estructura JSON es directamente consumible sin modificaciones

## Mantenimiento

Ver `CONTRIBUTING.md` para instrucciones detalladas sobre cómo agregar normas, etiquetar artículos y gestionar reformas.
