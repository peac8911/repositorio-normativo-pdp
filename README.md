# Repositorio Normativo PDP

Base de conocimiento normativo estructurado para protección de datos personales.

## Jurisdicciones
- Ecuador (/ec)
- Chile (/cl)

## Estructura

### /catalogos
Catálogos globales reutilizables entre jurisdicciones:
- 	emas.json — taxonomía temática PDP (13 temas, 2 niveles)
- 	emas-ec.json / 	emas-cl.json — extensiones por jurisdicción
- materias.json — materias (pdp, ia, datos-menores, ciberseguridad...)
- 	ipos-fuente.json — jerarquía normativa global
- actores-riesgo-aepd.json — catálogo AEPD de factores de riesgo
- hermeneutica.json — reglas de interpretación (se consulta solo ante conflicto normativo)

### /ec y /cl
Normas organizadas por tipo de fuente y nivel jerárquico.
Cada archivo contiene una norma con sus artículos como unidades indexadas.

### /mapeos
Relaciones transversales: concordancias entre jurisdicciones y mapeos sectoriales.

## Esquema de cada artículo
`json
{
  "id": "EC-LOPDP-Art-13",
  "numero": "13",
  "titulo": "Del consentimiento",
  "texto_oficial": "...",
  "vigente": true,
  "modificado": false,
  "derogado": false,
  "sustituido_por": null,
  "materia": ["pdp"],
  "es_especializada": true,
  "temas": ["bases-de-licitud.consentimiento"],
  "roles": ["responsable", "titular"],
  "ciclo": ["recoleccion"],
  "deontico": ["obligacion"],
  "factores_riesgo": ["operaciones-fines.perfilado"],
  "concordancias": ["EC-LOPDP-Art-7"],
  "comentario": "",
  "notas": ""
}
`

## Política de versioning
- Unidad mínima: artículo
- Solo el artículo afectado cambia de estado al reformarse o derogarse
- igente: false conserva el texto histórico
- sustituido_por apunta al ID del artículo que lo reemplaza

## Uso con Claude
Los catálogos y normas se consumen vía fetch autenticado desde skills de Claude.
Carga bajo demanda: cada archivo se consulta solo cuando se necesita.
