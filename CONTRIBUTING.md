# Contribution Guide — PDP Regulatory Knowledge Base

This document contains the operational instructions for working with the repository. It is intended for both human contributors and AI systems (Claude / Cowork) that support the construction and maintenance of the repository.

This repository is DATUM Component 1: Regulatory Knowledge Base. All contributions must follow the DATUM architectural principles, particularly the **English-first, multilingual by design** convention.

---

## Language Rules

These rules apply to every contribution:

1. **All metadata, tags, catalog IDs, and structural content are in English.** Field values for `temas`, `roles`, `ciclo`, `deontico`, and `factores_riesgo` use English identifiers from the catalogs.
2. **Official regulatory text (`texto_oficial`) is always in its original legislative language.** Never translate, paraphrase, or summarize it.
3. **The `preambulo` field** preserves original-language text.
4. **The `comentario` field** may be in English or in the jurisdiction's language. English is preferred for global accessibility.
5. **The `notas` field** may be in any language (it is operational, not analytical).
6. **Norm titles** include the original-language title in `titulo` and an English translation in `titulo_en`.
7. **Commit messages** are in English.
8. **Documentation and catalog labels** are in English. Where a jurisdiction-specific legal term has no clean English equivalent, the original term is preserved in parentheses.

---

## Working Principles

1. **Official text is sacred.** The `texto_oficial` field is never paraphrased or summarized. It is always the verbatim text from the official gazette, in the original legislative language.
2. **One article, one JSON object.** The minimum unit is the article. Articles are never combined into a single object.
3. **Tags are consistent.** Always use the exact IDs from the catalogs. Never invent new tags without updating the corresponding catalog first.
4. **Catalogs are updated before norms.** If you need a tag that does not exist, add it to the catalog first, then use it in the article.
5. **Nothing is deleted, only marked.** Repealed articles are not removed. They are marked with `estado: "derogado"`.

---

## How to Add a New Norm

### Step 1 — Identify the Norm

Before creating the file, verify:
- Which jurisdiction does it apply to? (`ec` / `cl`)
- What source type is it? (see `catalogos/tipos-fuente.json`)
- What hierarchical level does it have?
- Is it currently in force?
- What is the official URL of the text?

### Step 2 — Name the File

File names follow this pattern:
```
[jurisdiction]-[short-norm-name].json
```

Examples:
```
ec-lopdp.json
ec-reglamento-lopdp.json
cl-ley-pdp.json
```

Always lowercase, no spaces, with hyphens.

### Step 3 — Create the File

Place it in the correct folder by source type:
```
/ec/ley-organica/ec-lopdp.json
/ec/reglamentos/ec-reglamento-lopdp.json
/cl/ley/cl-ley-pdp.json
```

### Step 4 — Populate the Norm Metadata

```json
{
  "meta": {
    "id": "EC-LOPDP",
    "jurisdiccion": "ec",
    "tipo_fuente": "ley-organica",
    "nivel_jerarquico": 3,
    "titulo": "Official title in the original legislative language",
    "titulo_en": "Official title translated to English",
    "numero": "Official number or identifier",
    "fecha_publicacion": "YYYY-MM-DD",
    "fecha_vigencia": "YYYY-MM-DD",
    "vigente": true,
    "materia": ["pdp"],
    "es_especializada": true,
    "url_oficial": "URL from the official gazette",
    "preambulo": "Full preamble text in original language, or empty string.",
    "anexos": [],
    "version": "1.0.0"
  },
  "unidades": []
}
```

### Step 5 — Extract and Add Articles

See "How to Extract Articles" below.

---

## How to Extract Articles

### When Working from a PDF or Text of the Norm

1. Extract the text of each article verbatim, without modifications
2. Create one JSON object per article following the schema
3. Add the object to the `unidades` array of the norm file

### Article Schema

```json
{
  "id": "EC-LOPDP-Art-13",
  "tipo": "articulo",
  "numero": "13",
  "titulo": "Original article title in legislative language, or empty",
  "titulo_en": "English translation of the article title, or empty",
  "texto_oficial": "Complete verbatim text of the article in its original legislative language, including all subsections and paragraphs.",
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

### Article ID Convention

```
[JURISDICTION]-[NORM]-Art-[NUMBER]
```

Examples:
```
EC-LOPDP-Art-1
EC-LOPDP-Art-13
CL-LPDP-Art-5
```

For articles with letters (Art. 4-A):
```
EC-LOPDP-Art-4A
```

---

## How to Tag an Article

Tagging is done across five dimensions. Each dimension uses IDs from the corresponding catalogs. **All tag values are in English.**

### 1. temas (Topics)

Use `temas.json` (and jurisdiction extensions if applicable).
Format: `"topic"` or `"topic.subtopic"`

```json
"temas": ["legal-bases.consent", "principles.transparency"]
```

Rule: an article can have multiple topics. Include all that directly apply to the article's content.

### 2. roles

Who does this article address or affect?

Valid values:
- `controller`
- `processor`
- `sub-processor`
- `data-subject`
- `dpo`
- `supervisory-authority`
- `third-party`

```json
"roles": ["controller", "data-subject"]
```

### 3. ciclo (Lifecycle Phase)

Which phase of the data lifecycle does this article operate in?

Valid values:
- `collection` — obtaining data directly from the data subject or third parties
- `creation` — generating data by the organization itself
- `inference` — deriving new personal data from existing data
- `storage` — retention and custody of data
- `use` — internal use for the declared purpose
- `communication` — transmission to third parties within the same jurisdiction
- `transfer` — transmission to third parties in other jurisdictions
- `deletion` — elimination or anonymization of data
- `archival` — retention for historical, statistical, or research purposes

```json
"ciclo": ["collection", "use"]
```

### 4. deontico (Deontic Type)

What type of norm is this?

Valid values:
- `obligation` — the controller/processor must do something
- `prohibition` — expressly prohibited
- `right` — recognizes a data subject right
- `permission` — authorizes something under conditions
- `definition` — defines a concept
- `exception` — establishes an exception to another rule

```json
"deontico": ["obligation"]
```

### 5. factores_riesgo (Risk Factors)

Which risk factors from the AEPD catalog does this article activate or regulate?
Use IDs from `catalogos/factores-riesgo-aepd.json`.

Format: `"category.factor"`

```json
"factores_riesgo": ["operations-purposes.profiling", "data-types.health-data"]
```

If the article does not activate specific risk factors, leave the array empty `[]`.

---

## How to Add Concordances

Concordances are articles from other norms (within the same jurisdiction) that directly relate to this article.

```json
"concordancias": ["EC-LOPDP-Art-7", "EC-LOPDP-Art-14", "EC-REGLAMENTO-LOPDP-Art-5"]
```

Rule: only same-jurisdiction concordances. Cross-jurisdictional equivalences go in `/mapeos/concordancias-ec-cl.json`.

---

## How to Manage Preambles and Recitals

If the norm has a preamble, explanatory statement, or recitals, copy the complete text in the original language into the `preambulo` field of `meta`. If it has none, use an empty string `""`.

The preamble is not included in `unidades`. It is not tagged. It is only available for interpretive consultation when legislative intent context is needed.

---

## How to Manage Annexes

### Short or Frequently Referenced Annex

Add within the `unidades` array with `"tipo": "anexo"`. Tagging dimensions (temas, roles, ciclo, deontico, factores_riesgo) are not required unless the annex content explicitly justifies them.

```json
{
  "id": "EC-RESOLUCION-001-Anexo-1",
  "tipo": "anexo",
  "numero": "1",
  "titulo": "Original title in legislative language",
  "titulo_en": "English translation of the annex title",
  "texto_oficial": "Complete annex content in original language...",
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

### Extensive or Technical Annex

Create a separate JSON file in the same folder as the main norm, following the naming convention:
```
[norm-name]-anexo-[number].json
```

Reference it from the main norm's `meta`:
```json
"anexos": ["ec-resolucion-001-anexo-1.json"]
```

---

## How to Manage Reforms and Repeals

### Reformed Article (text changes but remains in force)

```json
"estado": "reformado",
"notas": "Reformed by [norm] on [date]. Previous text: [previous text]"
```

### Repealed Article Without Replacement

```json
"estado": "derogado",
"sustituido_por": null,
"notas": "Repealed by [norm] on [date]."
```

### Repealed Article With Replacement

```json
"estado": "derogado",
"sustituido_por": "EC-LOPDP-Art-15B",
"notas": "Repealed by [norm] on [date]. Replaced by Art. 15-B."
```

Important: the original text is always preserved. Never deleted.

---

## How to Update Catalogs

### Adding a New Topic

1. Open `catalogos/temas.json`
2. Add the object with `id` (English), `label` (English), `descripcion` (English), and `parent` if it is a subtopic
3. If it applies only to a jurisdiction, add it to `temas-ec.json` or `temas-cl.json` with an explanatory note

### Adding a New Subject Matter

1. Open `catalogos/materias.json`
2. Add the new value with `id` (English), `label` (English), and `descripcion` (English)
3. Update `README.md` with the new subject matter

### Adding a Risk Factor

The AEPD risk factor catalog core is not modified. If there are additional factors relevant to Latin America, add them in a separate section within `factores-riesgo-aepd.json` with prefix `latam`.

---

## General Conventions

### Dates
Always in ISO format: `YYYY-MM-DD`

### IDs
Always uppercase for norms: `EC-LOPDP`
Always lowercase with hyphens for tags: `legal-bases.consent`

### Official Text
- Copy verbatim, including original punctuation
- Preserve paragraph breaks using `\n`
- Do not correct typographical errors in the official text — document them in `notas`

### Norm File Versioning
Each time an article is modified, update the `version` field in the norm's `meta`:
- Minor change (notes, comments): increment third number `1.0.0 → 1.0.1`
- Article modification: increment second number `1.0.0 → 1.1.0`
- Major repeal or restructuring: increment first number `1.0.0 → 2.0.0`

---

## Instructions for Claude / Cowork

When asked to work with this repository:

1. **Before tagging**, read `catalogos/temas.json` and `catalogos/factores-riesgo-aepd.json` to use the correct English IDs
2. **Before creating a norm**, read `catalogos/tipos-fuente.json` and `catalogos/jurisdicciones.json`
3. **Official text is never modified**. If there is ambiguity, document it in `notas`
4. **When there is a normative conflict**, consult `catalogos/hermeneutica.json` before issuing a criterion
5. **If you need a tag that does not exist**, propose it to the user before using it
6. **One commit per norm**. Do not mix multiple norms in a single commit
7. **Commit message standard (English):**
   - New norm: `feat(ec): add LOPDP complete`
   - Modification: `fix(ec): update Art-13 per reform [date]`
   - Catalog: `chore(catalogs): add topic ai-and-automated-decisions`
8. **All new tag values must be in English.** Never introduce Spanish-language tag values in new contributions.
9. **When migrating existing content**, use the tag mapping table in the Migration section below to translate Spanish tags to their English equivalents.

---

## Migration: Spanish to English Tags

> **This section is temporary.** It provides the mapping from the original Spanish tag values to the new English equivalents. It will be removed once migration is complete.

### Topic Tag Mapping

| Old (Spanish) | New (English) |
|---|---|
| `principios` | `principles` |
| `principios.licitud` | `principles.lawfulness` |
| `principios.finalidad` | `principles.purpose-limitation` |
| `principios.proporcionalidad` | `principles.proportionality` |
| `principios.transparencia` | `principles.transparency` |
| `principios.confidencialidad` | `principles.confidentiality` |
| `principios.calidad` | `principles.data-quality` |
| `principios.conservacion` | `principles.storage-limitation` |
| `principios.seguridad` | `principles.security` |
| `principios.responsabilidad-proactiva` | `principles.accountability` |
| `principios.lealtad` | `principles.fairness` |
| `principios.minimizacion` | `principles.data-minimization` |
| `bases-de-licitud` | `legal-bases` |
| `bases-de-licitud.consentimiento` | `legal-bases.consent` |
| `bases-de-licitud.interes-legitimo` | `legal-bases.legitimate-interest` |
| `bases-de-licitud.obligacion-legal` | `legal-bases.legal-obligation` |
| `bases-de-licitud.interes-publico` | `legal-bases.public-interest` |
| `bases-de-licitud.relacion-contractual` | `legal-bases.contractual-relationship` |
| `bases-de-licitud.interes-vital` | `legal-bases.vital-interest` |
| `datos-sensibles` | `sensitive-data` |
| `datos-sensibles.salud` | `sensitive-data.health` |
| `datos-sensibles.biometricos` | `sensitive-data.biometric` |
| `datos-sensibles.origen-racial` | `sensitive-data.racial-origin` |
| `datos-sensibles.opinion-politica` | `sensitive-data.political-opinion` |
| `datos-sensibles.creencias-religiosas` | `sensitive-data.religious-beliefs` |
| `datos-sensibles.orientacion-sexual` | `sensitive-data.sexual-orientation` |
| `datos-sensibles.datos-geneticos` | `sensitive-data.genetic` |
| `datos-sensibles.afiliacion-sindical` | `sensitive-data.trade-union` |
| `datos-sensibles.antecedentes-penales` | `sensitive-data.criminal-records` |
| `derechos-del-titular` | `data-subject-rights` |
| `derechos-del-titular.acceso` | `data-subject-rights.access` |
| `derechos-del-titular.rectificacion` | `data-subject-rights.rectification` |
| `derechos-del-titular.supresion` | `data-subject-rights.erasure` |
| `derechos-del-titular.oposicion` | `data-subject-rights.objection` |
| `derechos-del-titular.portabilidad` | `data-subject-rights.portability` |
| `derechos-del-titular.limitacion` | `data-subject-rights.restriction` |
| `derechos-del-titular.no-decision-automatizada` | `data-subject-rights.no-automated-decision` |
| `derechos-del-titular.revocacion-consentimiento` | `data-subject-rights.consent-withdrawal` |
| `derechos-del-titular.informacion` | `data-subject-rights.information` |
| `derechos-del-titular.consulta` | `data-subject-rights.consultation` |
| `obligaciones-responsable` | `controller-obligations` |
| `obligaciones-responsable.registro-actividades` | `controller-obligations.processing-records` |
| `obligaciones-responsable.evaluacion-impacto` | `controller-obligations.impact-assessment` |
| `obligaciones-responsable.dpd` | `controller-obligations.dpo` |
| `obligaciones-responsable.medidas-tecnicas` | `controller-obligations.technical-measures` |
| `obligaciones-responsable.notificacion-brechas` | `controller-obligations.breach-notification` |
| `obligaciones-responsable.privacidad-por-diseno` | `controller-obligations.privacy-by-design` |
| `obligaciones-responsable.responsabilidad-proactiva` | `controller-obligations.accountability` |
| `encargado-del-tratamiento` | `processor` |
| `encargado-del-tratamiento.contrato-encargo` | `processor.processing-agreement` |
| `encargado-del-tratamiento.subencargados` | `processor.sub-processors` |
| `categorias-especiales` | `special-categories` |
| `transferencias-internacionales` | `international-transfers` |
| `transferencias-internacionales.paises-adecuados` | `international-transfers.adequate-countries` |
| `transferencias-internacionales.garantias-apropiadas` | `international-transfers.appropriate-safeguards` |
| `transferencias-internacionales.excepciones` | `international-transfers.exceptions` |
| `seguridad-del-tratamiento` | `processing-security` |
| `seguridad-del-tratamiento.medidas-tecnicas` | `processing-security.technical-measures` |
| `seguridad-del-tratamiento.notificacion-brechas` | `processing-security.breach-notification` |
| `autoridad-de-control` | `supervisory-authority` |
| `autoridad-de-control.competencia` | `supervisory-authority.competence` |
| `autoridad-de-control.poderes` | `supervisory-authority.powers` |
| `autoridad-de-control.procedimiento-sancionador` | `supervisory-authority.sanctions-procedure` |
| `ia-y-decisiones-automatizadas` | `ai-and-automated-decisions` |
| `ia-y-decisiones-automatizadas.perfilado` | `ai-and-automated-decisions.profiling` |
| `ia-y-decisiones-automatizadas.explicabilidad` | `ai-and-automated-decisions.explainability` |
| `tratamiento-gran-escala` | `large-scale-processing` |
| `tratamiento-gran-escala.eipd-obligatoria` | `large-scale-processing.mandatory-dpia` |
| `ambito-de-aplicacion` | `scope` |
| `ambito-de-aplicacion.definiciones-clave` | `scope.key-definitions` |
| `ambito-de-aplicacion.exclusiones` | `scope.exclusions` |

### Role Tag Mapping

| Old (Spanish) | New (English) |
|---|---|
| `responsable` | `controller` |
| `encargado` | `processor` |
| `subencargado` | `sub-processor` |
| `titular` | `data-subject` |
| `dpd` | `dpo` |
| `autoridad-control` | `supervisory-authority` |
| `tercero` | `third-party` |

### Lifecycle Tag Mapping

| Old (Spanish) | New (English) |
|---|---|
| `recoleccion` | `collection` |
| `creacion` | `creation` |
| `inferencia` | `inference` |
| `almacenamiento` | `storage` |
| `uso` | `use` |
| `comunicacion` | `communication` |
| `transferencia` | `transfer` |
| `supresion` | `deletion` |
| `archivo` | `archival` |

### Deontic Tag Mapping

| Old (Spanish) | New (English) |
|---|---|
| `obligacion` | `obligation` |
| `prohibicion` | `prohibition` |
| `derecho` | `right` |
| `permiso` | `permission` |
| `definicion` | `definition` |
| `excepcion` | `exception` |

### Risk Factor Tag Mapping

| Old (Spanish) | New (English) |
|---|---|
| `operaciones-fines.perfilado` | `operations-purposes.profiling` |
| `operaciones-fines.decisiones-automatizadas` | `operations-purposes.automated-decisions` |
| `operaciones-fines.vigilancia-sistematica` | `operations-purposes.systematic-monitoring` |
| `operaciones-fines.tratamiento-gran-escala` | `operations-purposes.large-scale-processing` |
| `operaciones-fines.cruce-fuentes` | `operations-purposes.data-matching` |
| `tipos-datos.datos-sensibles` | `data-types.sensitive-data` |
| `tipos-datos.datos-salud` | `data-types.health-data` |
| `tipos-datos.datos-biometricos` | `data-types.biometric-data` |
| `tipos-datos.datos-geneticos` | `data-types.genetic-data` |
| `tipos-datos.datos-menores` | `data-types.minors-data` |
| `tipos-datos.datos-financieros` | `data-types.financial-data` |
| `tipos-datos.datos-ubicacion` | `data-types.location-data` |
| `titulares.vulnerables` | `data-subjects.vulnerable` |
| `titulares.menores` | `data-subjects.minors` |
| `titulares.empleados` | `data-subjects.employees` |
| `tecnologias.nuevas-tecnologias` | `technologies.novel-technologies` |
| `tecnologias.ia` | `technologies.ai` |
| `tecnologias.iot` | `technologies.iot` |
| `tecnologias.reconocimiento-facial` | `technologies.facial-recognition` |
| `comunicacion.transferencia-internacional` | `disclosure.international-transfer` |
| `comunicacion.comunicacion-terceros` | `disclosure.third-party-disclosure` |
| `impacto.impedimento-derechos` | `impact.rights-impediment` |
| `impacto.dano-significativo` | `impact.significant-harm` |
| `impacto.efecto-juridico` | `impact.legal-effect` |

> Note: This mapping covers the tags currently in use. If additional tags are encountered during migration that are not listed here, propose the English equivalent to the repository owner before applying it.
