# PDP Regulatory Knowledge Base

Structured regulatory knowledge base for data protection consulting. Developed and maintained by PDP Expert.

This repository is the first operational component of the **DATUM Cognitive Architecture** (Intelligent Regulated Data Compliance Framework). It serves as the **Regulatory Knowledge Base** — the structured, machine-readable corpus of applicable norms that feeds all other DATUM components.

## What Is This Repository?

This repository contains the official text of data protection regulations, organized, indexed, and tagged for consumption by intelligent systems (Claude, agents, APIs) in the context of specialized regulatory consulting.

It is not a passive archive. It is a live knowledge base that:
- Enables AI systems to cite regulatory articles with legal precision
- Supports comparative regulatory analysis across jurisdictions
- Facilitates risk factor identification in data processing operations
- Scales to additional regulatory domains (AI governance, cybersecurity, minors' data)
- Serves as the normative foundation for compliance reasoning in any DATUM implementation model

## Language Convention

This repository follows the **English-first, multilingual by design** principle defined in the DATUM Framework:

- **All structural content is in English**: schema field names, catalog IDs, tag identifiers, metadata labels, documentation, and commit messages.
- **Official regulatory text is preserved in its original legislative language** (Spanish for Ecuador and Chile). The `texto_oficial` field is never translated, paraphrased, or summarized.
- **The `comentario` field** may contain interpretive analysis in English or in the language of the jurisdiction, depending on the author's preference. When analysis is in Spanish, an `en_summary` field may optionally be added for cross-language discoverability.
- **Catalog labels** (`label` field in catalog entries) are in English. Where a jurisdiction-specific term has no clean English equivalent, the original term is preserved in parentheses alongside the English label.

This approach ensures the repository is globally accessible while preserving the legal integrity of source texts.

## Jurisdictions Covered

| Code | Country | Status |
|---|---|---|
| `ec` | Ecuador | Active |
| `cl` | Chile | Under construction |

## Source Types

The repository respects the normative hierarchy of each jurisdiction:

1. Constitution / constitutional jurisprudence
2. Organic law
3. Ordinary law
4. Regulations (executive decrees)
5. Secondary norms (resolutions, guidelines)
6. Supervisory authority pronouncements (non-binding)
7. Ordinary jurisprudence
8. Proprietary doctrine and commentary

## Repository Structure

```
/repositorio-normativo-pdp/
│
├── /catalogos/           ← Global reusable catalogs (English IDs)
├── /ec/                  ← Ecuador norms, organized by source type
├── /cl/                  ← Chile norms, organized by source type
└── /mapeos/              ← Cross-jurisdictional and cross-sector mappings
```

### /catalogos

Reference catalogs used to tag articles. All catalog IDs and labels are in English:

| File | Content |
|---|---|
| `temas.json` | Thematic taxonomy — 13 topics, 2 levels |
| `temas-ec.json` | Ecuador-specific topic extensions |
| `temas-cl.json` | Chile-specific topic extensions |
| `materias.json` | Regulatory subject matters (pdp, ai, minors-data, cybersecurity...) |
| `tipos-fuente.json` | Global normative hierarchy with precedence levels |
| `jurisdicciones.json` | Metadata for each covered jurisdiction |
| `factores-riesgo-aepd.json` | AEPD risk factor catalog — European base adapted |
| `hermeneutica.json` | Normative interpretation rules — consulted only in conflict scenarios |

### /ec and /cl

Each jurisdiction has its own folder organized by source type. Within each subfolder, each norm is an independent JSON file.

Example:
```
/ec/ley-organica/ec-lopdp.json
/ec/reglamentos/ec-reglamento-lopdp.json
/ec/pronunciamientos/ec-sentencia-001.json
```

### /mapeos

Cross-jurisdictional and cross-sector relationships:

| File | Content |
|---|---|
| `concordancias-ec-cl.json` | Article equivalences between Ecuador and Chile |
| `sectorial-financiero.json` | PDP norms mapped to financial regulation |
| `sectorial-salud.json` | PDP norms mapped to health regulation |

## Norm Schema

Each norm file has two sections: `meta` (norm-level data) and `unidades` (articles).

```json
{
  "meta": {
    "id": "EC-LOPDP",
    "jurisdiccion": "ec",
    "tipo_fuente": "ley-organica",
    "nivel_jerarquico": 3,
    "titulo": "Ley Orgánica de Protección de Datos Personales",
    "titulo_en": "Organic Law on Personal Data Protection",
    "numero": "Ley s/n",
    "fecha_publicacion": "2021-05-26",
    "fecha_vigencia": "2023-05-26",
    "vigente": true,
    "materia": ["pdp"],
    "es_especializada": true,
    "url_oficial": "https://...",
    "preambulo": "Full text of the preamble in original language, if applicable.",
    "anexos": [],
    "version": "1.0.0"
  },
  "unidades": [
    {
      "id": "EC-LOPDP-Art-13",
      "tipo": "articulo",
      "numero": "13",
      "titulo": "Del consentimiento",
      "titulo_en": "On consent",
      "texto_oficial": "Full official text in original legislative language...",
      "estado": "vigente",
      "sustituido_por": null,
      "temas": ["legal-bases.consent"],
      "roles": ["controller", "data-subject"],
      "ciclo": ["collection"],
      "deontico": ["obligation"],
      "factores_riesgo": ["operations-purposes.profiling"],
      "concordancias": ["EC-LOPDP-Art-7", "EC-LOPDP-Art-14"],
      "comentario": "Interpretive analysis (English preferred, original language accepted).",
      "notas": ""
    }
  ]
}
```

## Preamble and Recitals

Some norms have preambles, explanatory statements, or recitals with interpretive value. These are stored in the `preambulo` field of the norm's `meta`, not as units. They are not queried in ordinary normative analysis but are available when the analysis requires legislative intent context.

## Annexes

Annexes from secondary norms (forms, tables, technical lists) are managed by size:

**Short or frequently referenced annexes** — included in the `unidades` array with `"tipo": "anexo"`.

**Extensive or technical annexes** — stored in a separate JSON file in the same folder, referenced from the main norm's `meta.anexos` field.

## Article States

| State | Meaning |
|---|---|
| `vigente` | In force, unchanged since publication |
| `reformado` | Modified, in force with new content |
| `derogado` | No longer in force |

Repealed articles are never deleted. Their text is preserved for historical reference. Standard queries filter for `vigente` or `reformado` articles only.

## Data Lifecycle Phases

The `ciclo` field tags which processing phase the article operates in:

| Value | Description |
|---|---|
| `collection` | Obtaining data directly from the data subject or third parties |
| `creation` | Generating data by the organization itself |
| `inference` | Deriving new personal data from existing data |
| `storage` | Retention and custody of data |
| `use` | Internal use for the declared purpose |
| `communication` | Transmission to third parties within the same jurisdiction |
| `transfer` | Transmission to third parties in other jurisdictions |
| `deletion` | Elimination or anonymization of data |
| `archival` | Retention for historical, statistical, or research purposes |

## Thematic Taxonomy (Summary)

Articles are tagged with two-level topics (`topic.subtopic`):

| # | Topic | Example Subtopic |
|---|---|---|
| 1 | `principles` | `lawfulness`, `purpose-limitation`, `proportionality` |
| 2 | `legal-bases` | `consent`, `legitimate-interest`, `legal-obligation` |
| 3 | `sensitive-data` | `health`, `biometric`, `racial-origin` |
| 4 | `data-subject-rights` | `access`, `rectification`, `erasure`, `portability` |
| 5 | `controller-obligations` | `processing-records`, `impact-assessment`, `dpo` |
| 6 | `processor` | `processing-agreement`, `sub-processors` |
| 7 | `special-categories` | Limited use in Ecuador — see temas-ec.json |
| 8 | `international-transfers` | `adequate-countries`, `appropriate-safeguards` |
| 9 | `processing-security` | `technical-measures`, `breach-notification` |
| 10 | `supervisory-authority` | `competence`, `powers`, `sanctions-procedure` |
| 11 | `ai-and-automated-decisions` | `profiling`, `explainability` |
| 12 | `large-scale-processing` | `mandatory-dpia` |
| 13 | `scope` | `key-definitions`, `exclusions` |

## Roles

| Value | Description |
|---|---|
| `controller` | Data controller / responsible party |
| `processor` | Data processor / encargado del tratamiento |
| `sub-processor` | Sub-processor / subencargado |
| `data-subject` | Data subject / titular |
| `dpo` | Data Protection Officer / DPD |
| `supervisory-authority` | Supervisory authority / autoridad de control |
| `third-party` | Third party / tercero |

## Versioning Policy

- The minimum unit of change is the **article**, not the entire norm
- When an article is reformed: `estado: "reformado"`, text is updated, previous text is documented in `notas`
- When an article is repealed: `estado: "derogado"`, text is preserved
- If an article is replaced: `sustituido_por: "EC-LOPDP-Art-XX"`
- Standard queries return articles with `estado: vigente` or `estado: reformado`
- Full history is always available in Git

## Scalability

This repository is designed to grow:

- **New jurisdictions:** add `/xx/` folder and entry in `jurisdicciones.json`
- **New regulatory domains:** add value in `materias.json` (e.g., `ai`, `cybersecurity`)
- **New sectors:** add mapping file in `/mapeos/`
- **API access:** the JSON structure is directly consumable without modifications
- **DATUM integration:** this repository is the first operational component of the DATUM Cognitive Architecture, serving as the Regulatory Knowledge Base accessible via MCP connector

## DATUM Alignment

This repository implements DATUM Component 1: Regulatory Knowledge Base. Key architectural principles:

- **Knowledge lives in external repositories**, not in agent skills or application logic. This repository is queried at execution time by any DATUM-compatible agent.
- **English-first metadata** ensures global accessibility. Original-language regulatory text ensures legal integrity.
- **Framework-agnostic tagging**: the thematic taxonomy and risk factor catalogs are designed to serve any compliance framework that references the underlying norms, not just PDP Expert's proprietary models.

## Maintenance

See `CONTRIBUTING.md` for detailed instructions on adding norms, tagging articles, and managing reforms.

---

## Migration Status

> **This section is temporary.** It tracks the migration from the original Spanish-only repository to the English-first convention defined by DATUM. It will be removed once migration is complete.

### What Has Changed

| Aspect | Previous Convention | New Convention |
|---|---|---|
| Documentation language | Spanish | English |
| Catalog IDs | Spanish (`bases-de-licitud.consentimiento`) | English (`legal-bases.consent`) |
| Roles values | Spanish (`responsable`, `titular`) | English (`controller`, `data-subject`) |
| Lifecycle values | Spanish (`recoleccion`, `supresion`) | English (`collection`, `deletion`) |
| Deontic values | Spanish (`obligacion`, `prohibicion`) | English (`obligation`, `prohibition`) |
| Field names | Spanish (`texto_oficial`, `estado`) | Unchanged (kept as-is for backward compatibility during migration) |
| Official text | Original language (Spanish) | Unchanged (always original language) |
| Norm titles | Original language only | Original + `titulo_en` field |
| Commit messages | Spanish | English |

### Migration Approach

Field names (`texto_oficial`, `estado`, `vigente`, etc.) are kept in their current form during migration to avoid breaking the MCP connector. A future breaking-change release will normalize all field names to English. The priority is migrating tag values (topics, roles, lifecycle, deontic) and catalog IDs first, as these are what agents query against.

### What Needs To Be Done

1. Update all catalog files: translate IDs and labels to English, add mapping from old Spanish IDs to new English IDs for backward compatibility
2. Update all existing norm files: replace Spanish tag values with English equivalents
3. Add `titulo_en` field to all norm `meta` and article objects
4. Update MCP connector to serve the new English tag values
5. Validate that all existing queries and skills work with the new values
6. Remove this migration section from README.md
