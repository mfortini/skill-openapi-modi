# Esempi JSON-LD

Questa cartella contiene esempi di mappatura tra JSON standard e JSON-LD con annotazioni semantiche.

## Struttura

Per ogni entità sono presenti due file:

| File | Descrizione |
|------|-------------|
| `*.json` | Risposta JSON standard (application/json) |
| `*.jsonld` | Risposta JSON-LD con @context (application/ld+json) |

## Esempi disponibili

### persona.json / persona.jsonld

Esempio completo di mappatura di una persona fisica usando l'ontologia CPV (Core Person Vocabulary).

**Mappature semantiche:**
- `@type`: `CPV:Person`
- `codice-fiscale` → `CPV:taxCode`
- `nome` → `CPV:givenName`
- `cognome` → `CPV:familyName`
- `data-nascita` → `CPV:dateOfBirth`
- `luogo-nascita` → `CPV:hasBirthPlace`
- `residenza` → `CPV:hasResidenceInTime`
- `email` → `foaf:mbox` (gap CPV)
- `telefono` → `foaf:phone` (gap CPV)

**Gap semantici documentati:**
- `stato-civile`: non presente in JSON-LD (CPV:hasMaritalStatus non esiste)
- `cap`: non mappabile (CLV:postCode non esiste)

### documento.json / documento.jsonld

Esempio di documento amministrativo usando Dublin Core e DCAT.

**Mappature semantiche:**
- `@type`: `dcat:Dataset`
- `titolo` → `dct:title`
- `descrizione` → `dct:description`
- `data-creazione` → `dct:created`
- `data-modifica` → `dct:modified`
- `autore` → `dct:creator` (con nested `COV:Organization`)

**Gap semantici documentati:**
- `stato`: presente in JSON ma escluso da JSON-LD (nessuna mappatura ontologica standard trovata - possibili alternative: dct:status, schema:status)

### status.json / status.jsonld

Esempio di risposta senza elementi semantici mappabili.

**Context vuoto:** Quando uno schema non ha corrispondenze ontologiche, il `@context` deve essere un oggetto vuoto `{}` per mantenere la validità JSON-LD.

```json
{
  "@context": {},
  "status": "healthy",
  "version": "1.0.0"
}
```

## Validazione

Per validare i file JSON-LD:

```bash
# Con jsonld-cli (Node.js)
npm install -g jsonld-cli
jsonld format persona.jsonld

# Con pyld (Python)
pip install pyld
python -c "from pyld import jsonld; import json; print(jsonld.expand(json.load(open('persona.jsonld'))))"

# Online
# https://json-ld.org/playground/
```

## Regole per @context

1. **@context vuoto**: Se non ci sono mappature semantiche, usare `"@context": {}`
2. **Prefissi compatti**: Mai URI espanse, sempre `CPV:givenName` non l'URI completa
3. **@vocab**: Definire per il namespace dominante
4. **@base**: Definire per le URI delle risorse
5. **Gap documentati**: Proprietà senza mappatura vanno escluse dal JSON-LD o documentate
