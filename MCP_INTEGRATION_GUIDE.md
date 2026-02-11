# Guida Integrazione MCP Tools

Questa guida spiega come utilizzare gli strumenti MCP per creare specifiche OpenAPI conformi ModI con arricchimento semantico.

---

## 🛠️ MCP Tools Disponibili

### 1. API Design Canvas (Tool Visuale)
Strumento per progettazione collaborativa dell'API prima della codifica.

### 2. oas-checker-mcp
MCP server per validazione conformità linee guida AgID ModI.

**Capabilities**:
- Validazione specifica OpenAPI
- Verifica pattern di interazione
- Controllo pattern di sicurezza
- Analisi naming convention
- Suggerimenti miglioramento

### 3. schema-gov-it (dati-semantic-mcp)
MCP server per accesso a ontologie e vocabolari controllati italiani.

**Capabilities**:
- Ricerca ontologie (CPV, DCAT-AP_IT, CLV, COV)
- Query su classi e proprietà
- Suggerimento vocabolari controllati
- Validazione mappature semantiche
- Generazione context JSON-LD

**Best practices per efficienza**:
- Usare SEMPRE `keyword` e `limit` per filtrare le query
- Preferire `search_concepts` con keyword specifico
- Non scaricare interi vocabolari: usare `browse_vocabulary(keyword=...)`
- Nelle risposte, convertire URI lunghe in notazione prefisso compatta

---

## 📋 Workflow Integrato Completo

### FASE 1: Progettazione (Canvas API)

#### 1.1 Inizializzazione Progetto

**Prompt suggerito**:
```
"Voglio creare un'API per [SCOPO]. Aiutami a compilare il canvas 
API design partendo da questi requisiti: [REQUISITI]"
```

**Claude ti guiderà attraverso**:
- Identificazione stakeholder
- Definizione casi d'uso
- Mappatura risorse REST
- Requisiti sicurezza e performance

#### 1.2 Output Canvas

Salva il canvas completato come riferimento:
```
API_DESIGN_CANVAS_[nome-api].md
```

---

### FASE 2: Generazione Specifica Base

#### 2.1 Creazione Struttura

**Prompt**:
```
"Basandoti sul canvas, genera la specifica OpenAPI 3.0 conforme 
ModI AgID con:
- Info block completo
- Security scheme Bearer JWT
- Tutte le risorse identificate
- Pattern di paginazione ed errori"
```

**Claude genererà**:
```yaml
openapi: 3.0.3
info:
  title: API [Nome]
  version: 1.0.0
  x-api-id: [identificativo]
  # ... metadati completi

components:
  securitySchemes:
    bearerAuth: # ...
  schemas:
    Problem: # RFC 7807
    # ... altri schemi
    
paths:
  /status: # health check
  /[risorsa]: # operazioni CRUD
```

#### 2.2 Salvataggio

Salva la specifica come:
```
openapi-[nome-api]-v1.0.0.yaml
```

---

### FASE 3: Arricchimento Semantico (dati-semantic-mcp)

#### 3.1 Analisi Entità

Per ogni schema principale, identifica il tipo ontologico:

**Esempio - Schema Persona**

**Prompt**:
```
"Usa dati-semantic-mcp per:
1. Cercare la classe Persona nell'ontologia CPV
2. Ottenere le proprietà disponibili
3. Generare il context JSON-LD per questo schema:

Persona:
  properties:
    codice-fiscale: string
    nome: string
    cognome: string
    data-nascita: date
    email: email
```

**Possibile risposta Claude**:
```
Ho consultato dati-semantic-mcp e VERIFICATO ogni URI:

Classe ontologica: https://w3id.org/italia/onto/CPV/Person ✓

Proprietà mappabili (verificate con get_property_details):
- codice-fiscale → CPV:taxCode ✓
- nome → CPV:givenName ✓ (proprietà nativa CPV, NON foaf:givenName)
- cognome → CPV:familyName ✓ (proprietà nativa CPV, NON foaf:familyName)
- data-nascita → CPV:dateOfBirth ✓ (proprietà nativa CPV, NON schema:birthDate)
- email → foaf:mbox (gap CPV: nessuna email nativa in CPV)

Genero il context JSON-LD con URI verificate...
```

#### 3.2 Inserimento Annotazioni (con prefissi compatti)

**Claude aggiornerà lo schema usando prefissi e URI verificate via MCP**:
```yaml
Persona:
  type: object
  x-jsonld-type: 'CPV:Person'  # Usa prefisso, NON URI completa
  x-jsonld-context:
    # Prefissi (definire TUTTI quelli usati)
    CPV: 'https://w3id.org/italia/onto/CPV/'
    foaf: 'http://xmlns.com/foaf/0.1/'
    xsd: 'http://www.w3.org/2001/XMLSchema#'
    # Namespace di default
    '@vocab': 'https://w3id.org/italia/onto/CPV/'
    '@base': 'https://api.example.gov.it/v1/persone/'
    # Mappature con prefissi compatti - VERIFICATE via MCP schema-gov-it
    codice-fiscale:
      '@id': 'CPV:taxCode'
      '@type': 'xsd:string'
    nome:
      '@id': 'CPV:givenName'  # proprietà nativa CPV (NON foaf:givenName)
      '@language': 'it'
    cognome:
      '@id': 'CPV:familyName'  # proprietà nativa CPV (NON foaf:familyName)
      '@language': 'it'
    data-nascita:
      '@id': 'CPV:dateOfBirth'  # proprietà nativa CPV (NON schema:birthDate)
      '@type': 'xsd:date'
    email:
      '@id': 'foaf:mbox'  # Gap CPV: nessuna email nativa, fallback foaf
      '@type': 'xsd:string'
  properties:
    codice-fiscale:
      type: string
      pattern: '^[A-Z]{6}[0-9]{2}[A-Z][0-9]{2}[A-Z][0-9]{3}[A-Z]$'
    nome:
      type: string
    # ... resto dello schema
```

**Regole**:
- Ogni URI nei context DEVE usare prefisso. Se serve un namespace nuovo, aggiungere il prefisso nella sezione prefissi.
- **OGNI URI DEVE essere verificata via MCP** con `get_property_details()` o `inspect_concept()` prima dell'uso. MAI copiare URI da template senza verifica.

#### 3.3 Iterazione per Tutti gli Schemi

Ripeti per:
- [x] Persona
- [x] Organizzazione
- [x] Indirizzo
- [x] Documento
- [x] Pratica
- [ ] ... altri schemi custom

**Prompt tipo**:
```
"Arricchisci semanticamente anche lo schema [NomeSchema] 
usando dati-semantic-mcp"
```

#### 3.4 Vocabolari Controllati

Per campi enum, cerca vocabolari ufficiali:

**Prompt**:
```
"Cerca in schema-gov-it se esiste un vocabolario controllato
per 'tipi di documento amministrativo'"
```

**Claude eseguirà**:
```
list_vocabularies() → elenca vocabolari disponibili
browse_vocabulary(schemeUri="...", keyword="documento") → cerca nel vocabolario
```

**Claude potrebbe rispondere**:
```
Trovato vocabolario:
https://w3id.org/italia/controlled-vocabulary/classifications-for-public-services/authentication-type

Valori disponibili (con prefisso cv:):
- cv:DELIBERA_GIUNTA
- cv:DETERMINA_DIRIGENZIALE
- cv:ORDINANZA_SINDACALE
...

Aggiorno lo schema per referenziare il vocabolario con prefisso compatto.
```

#### 3.5 Gestione Gap Semantici

Quando una proprietà o un tipo NON trova corrispondenza nelle ontologie:

**Claude deve**:
1. **Segnalare il gap** esplicitamente all'utente
2. **Suggerire alternative** da ontologie diverse
3. **Proporre mappature approssimative** documentandole
4. **Arricchire le descrizioni** quando quelle dell'ontologia sono insufficienti

**Esempio risposta Claude**:
```
REPORT COPERTURA SEMANTICA - Schema "Pratica"

Mappate: 8/12 proprietà (67%)
- numero → dct:identifier
- oggetto → dct:title
- stato → CPV:hasStatus (vocabolario custom - nessun CV AgID trovato)
- data-apertura → CPV:startDate
- ...

Gap identificati:
- "numero-protocollo": nessuna proprietà standard.
  SUGGERIMENTO: usare dct:identifier con descrizione che specifichi
  il formato protocollo, oppure proporre estensione a CPV.

- "responsabile-procedimento": concetto specifico PA italiana.
  SUGGERIMENTO: usare org:headOf (W3C Organization Ontology) come
  approssimazione, documentando la differenza.

- Enum "tipo-pratica" [PERMESSO_COSTRUIRE, DIA, SCIA]:
  nessun vocabolario controllato AgID trovato.
  SUGGERIMENTO: verificare periodicamente il repository nazionale
  https://github.com/italia/daf-ontologie-vocabolari-controllati

Descrizioni arricchite: 3
- "codice-fiscale": aggiunta descrizione formato (ontologia la omette)
- "stato": aggiunta lista valori con significato (ontologia generica)
- "data-protocollo": aggiunta nota sul formato ISO 8601
```

---

### FASE 3.5: Validazione Semantica via MCP (OBBLIGATORIA)

Prima di procedere alla validazione OAS, verificare che **tutte le URI ontologiche** usate negli schemi siano corrette:

1. **Per ogni `x-jsonld-type`**: `inspect_concept(uri)` → la classe esiste?
2. **Per ogni proprietà nel context**: `get_property_details(propertyUri)` → esiste? domain/range compatibili?
3. **Per ogni vocabolario referenziato**: `list_vocabularies()` → il ConceptScheme esiste?

> **REGOLA CRITICA**: Non usare MAI URI copiate da template senza verifica MCP.
> I template sono un punto di partenza, NON la fonte di verità.

**Output**: Report di validazione semantica con URI verificate OK, URI inesistenti, e gap documentati.

Vedi `SKILL.md` sezione "FASE 3.5" per il pattern di verifica completo.

---

### FASE 4: Validazione (oas-checker-mcp)

#### 4.1 Validazione Completa

**Prompt**:
```
"Valida la specifica con oas-checker-mcp usando il profilo 
ModI AgID completo"
```

**Claude eseguirà**:
```
oas-checker-mcp.valida({
  file: "openapi-[nome]-v1.0.0.yaml",
  profilo: "modi-agid",
  severità: "error"
})
```

#### 4.2 Analisi Report

**Esempio output**:
```
✅ CONFORMITÀ: 87/100

ERRORI CRITICI (3):
❌ [E001] Path '/documenti/{id documento}' contiene spazi
   → Correzione: usa '/documenti/{id-documento}'
   
❌ [E015] Mancante schema Problem per response 400 in POST /pratiche
   → Aggiungi: $ref: '#/components/responses/BadRequest'
   
❌ [E023] Endpoint /status non implementato
   → Richiesto per health check

WARNING (5):
⚠️ [W012] Schema 'Allegato' senza esempio
   → Aggiungi campo 'example' per documentazione
   
⚠️ [W008] Header X-Request-ID non documentato in GET /pratiche
   → Aggiungi header in response

SUGGERIMENTI (2):
💡 [S005] Considera pattern NONBLOCK_REST_PULL per POST /elaborazioni
💡 [S011] Rate limiting non documentato, valuta aggiunta header
```

#### 4.3 Correzione Iterativa

**Per ogni errore**:

1. **Errori Critici (priorità alta)**
   ```
   "Correggi l'errore E001: spazi nel path parameter"
   ```

2. **Warning (priorità media)**
   ```
   "Aggiungi esempi a tutti gli schemi che ne sono sprovvisti"
   ```

3. **Ri-validazione**
   ```
   "Ri-valida con oas-checker dopo le correzioni"
   ```

**Ciclo fino a**:
```
✅ CONFORMITÀ: 95+/100
Errori critici: 0
```

#### 4.4 Validazione Pattern Specifici

**Test singolo pattern**:
```
"Verifica con oas-checker che il pattern ID_AUTH_REST_01 
sia implementato correttamente"
```

**Claude eseguirà**:
```
oas-checker-mcp.verifica_pattern({
  file: "openapi-[nome]-v1.0.0.yaml",
  pattern: "ID_AUTH_REST_01"
})
```

---

### FASE 5: Refinement e Documentazione

#### 5.1 Generazione Esempi Automatica

**Prompt**:
```
"Genera esempi realistici per tutti gli schemi usando i dati 
dalle ontologie quando possibile"
```

**Claude**:
- Userà vocabolari per enum
- Genererà codici fiscali/P.IVA validi
- Creerà esempi coerenti tra schemi correlati

#### 5.2 Documentazione Migliorata

**Prompt**:
```
"Arricchisci le descrizioni di tutti gli schemi referenziando 
le ontologie quando mappati"
```

**Risultato**:
```yaml
Persona:
  type: object
  description: |
    Persona fisica conforme all'ontologia CPV (Core Vocabulary 
    Pubbliche Amministrazioni).
    
    Riferimento ontologico: https://w3id.org/italia/onto/CPV/Person
    
    Questa rappresentazione è compatibile con:
    - ANPR (Anagrafe Nazionale Popolazione Residente)
    - CIE (Carta Identità Elettronica)
    - SPID (Sistema Pubblico Identità Digitale)
```

#### 5.3 Export Multiformat

**Prompt**:
```
"Genera dalla specifica OpenAPI:
1. Documentazione HTML con Redoc
2. Postman collection
3. README con quick start"
```

---

## 🎯 Prompt Patterns Comuni

### Pattern 1: Creazione da Zero

```
Voglio creare un'API ModI per [DOMINIO]. Segui questo workflow:

1. Guidami nella compilazione del canvas API design
2. Genera la specifica OpenAPI conforme ModI
3. Arricchisci con ontologie usando dati-semantic-mcp
4. Valida con oas-checker-mcp
5. Itera fino a conformità 95+

Iniziamo con il canvas.
```

### Pattern 2: Migrazione API Esistente

```
Ho un'API esistente in [FILE]. Voglio renderla conforme ModI:

1. Analizza la specifica corrente
2. Identifica non conformità con oas-checker-mcp
3. Proponi refactoring verso pattern ModI
4. Aggiungi arricchimento semantico con dati-semantic-mcp
5. Valida risultato finale
```

### Pattern 3: Solo Arricchimento Semantico

```
Ho una specifica OpenAPI già conforme ModI in [FILE].
Arricchiscila semanticamente:

1. Identifica schemi principali
2. Per ciascuno, cerca ontologia con dati-semantic-mcp
3. Aggiungi x-jsonld-type e x-jsonld-context
4. Trova vocabolari controllati per enum
5. Valida che l'arricchimento non rompa conformità
```

### Pattern 4: Validazione e Fix

```
Valida questa specifica OpenAPI con oas-checker-mcp profilo ModI.
Per ogni errore trovato:
1. Spiega il problema
2. Mostra la correzione
3. Applica la fix
4. Ri-valida

Continua fino a score ≥ 95.
```

---

## 📊 Comandi MCP Dettagliati

### schema-gov-it (dati-semantic-mcp)

#### search_concepts - Ricerca fuzzy
```javascript
search_concepts({ keyword: "persona", limit: 10 })
→ Ritorna: concetti matching con tipo e label
// BEST PRACTICE: usare sempre keyword specifico per limitare i risultati
```

#### inspect_concept - Profilo completo
```javascript
inspect_concept({ uri: "https://w3id.org/italia/onto/CPV/Person" })
→ Ritorna: {
  definition, hierarchy, usage, incoming, outgoing
}
// Usa questo per ottenere tutte le proprietà di una classe
```

#### list_ontologies + explore_ontology - Navigazione
```javascript
list_ontologies({ limit: 50 })
→ Ritorna: lista ontologie con URI e titoli

explore_ontology({ ontologyUri: "https://w3id.org/italia/onto/CPV" })
→ Ritorna: classi e proprietà dell'ontologia
```

#### list_properties - Proprietà con filtri
```javascript
list_properties({
  ontologyUri: "https://w3id.org/italia/onto/CPV",
  propertyType: "datatype",  // "object", "datatype", "both"
  limit: 50
})
→ Ritorna: proprietà con dominio, range, label
// BEST PRACTICE: filtrare per ontologyUri e propertyType
```

#### list_vocabularies + browse_vocabulary - Vocabolari
```javascript
list_vocabularies({ limit: 20 })
→ Ritorna: ConceptScheme con label e conteggi

browse_vocabulary({
  schemeUri: "https://w3id.org/.../vocabulary-uri",
  keyword: "documento",  // SEMPRE usare keyword per filtrare!
  limit: 20
})
→ Ritorna: concetti con codice e label + paginazione
```

#### find_relations - Connessioni tra concetti
```javascript
find_relations({
  sourceUri: "https://w3id.org/.../CPV/Person",
  targetUri: "https://w3id.org/.../CLV/Address"
})
→ Ritorna: connessioni dirette e a 1 hop
// Utile per scoprire relazioni non ovvie
```

#### describe_resource - Descrizione completa
```javascript
describe_resource({ uri: "https://w3id.org/...", depth: 1 })
→ Ritorna: tutti i triple della risorsa
// Usa depth: 2 solo se servono risorse collegate
```

#### query_sparql - Query SPARQL dirette
```javascript
query_sparql({ query: "SELECT ?s ?p ?o WHERE { ... } LIMIT 10" })
→ Ritorna: risultati tabellari
// Ultimo resort: preferire i tool specializzati sopra
```

### oas-checker-mcp

#### valida
```javascript
{
  "file": "path/to/openapi.yaml",
  "profilo": "modi-agid" | "modi-base" | "rest-standard",
  "severità": "error" | "warning" | "info",
  "formato_output": "json" | "text" | "html"
}
→ Ritorna: {
  score, errori[], warning[], suggerimenti[]
}
```

#### verifica_pattern
```javascript
{
  "file": "path/to/openapi.yaml",
  "pattern_id": "ID_AUTH_REST_01" | "BLOCK_REST" | ...
}
→ Ritorna: {
  conforme: boolean,
  dettagli: string,
  correzioni_suggerite: []
}
```

#### confronta_versioni
```javascript
{
  "file_old": "openapi-v1.yaml",
  "file_new": "openapi-v2.yaml"
}
→ Ritorna: {
  breaking_changes: [],
  modifiche: [],
  aggiunte: [],
  rimozioni: []
}
```

#### suggerisci_miglioramenti
```javascript
{
  "file": "path/to/openapi.yaml",
  "focus": "performance" | "security" | "semantics" | "all"
}
→ Ritorna: [
  {tipo, priorità, descrizione, implementazione}
]
```

---

## 🔄 Workflow Avanzati

### A. API con Operazioni Asincrone

```
1. Canvas: identifica operazioni lunghe (>10s)
2. Genera specifica con pattern NONBLOCK_REST_PULL:
   - POST → 202 con Location
   - GET {id} → stato avanzamento
   - Polling fino a completamento
3. Valida pattern con oas-checker-mcp
4. Arricchisci con ontologie CPV per stato/progresso
```

### B. API Multi-Tenant (più enti)

```
1. Canvas: mappa enti partecipanti
2. Genera con header/param X-Organization-Code
3. Arricchisci schemi organizzazione con COV ontology
4. Valida separazione dati per tenant
5. Test isolamento con oas-checker
```

### C. API con File Upload

```
1. Canvas: definisci tipi file, max size, virus scan
2. Genera endpoint multipart/form-data
3. Arricchisci Allegato con DCAT distribution properties
4. Valida limiti e formati con oas-checker
5. Documenta best practices upload
```

---

## ✅ Checklist Finale

Prima di pubblicare, verifica:

### Progettazione
- [ ] Canvas completato e approvato
- [ ] Casi d'uso documentati
- [ ] Stakeholder allineati

### Specifica OpenAPI
- [ ] oas-checker score ≥ 95%
- [ ] Tutti gli schemi hanno esempi
- [ ] Descrizioni complete in italiano
- [ ] Health check /status presente

### Arricchimento Semantico
- [ ] **OGNI URI verificata via MCP** (FASE 3.5 completata)
- [ ] Schemi principali hanno x-jsonld-type (con prefisso compatto)
- [ ] Context JSON-LD completi con prefissi (nessuna URI espansa)
- [ ] `@vocab` e `@base` definiti in ogni context
- [ ] Domain/range di ogni proprietà verificati come compatibili
- [ ] Vocabolari controllati referenziati con prefissi
- [ ] Link a ontologie AgID
- [ ] Gap semantici documentati con suggerimenti
- [ ] Descrizioni arricchite dove ontologia insufficiente
- [ ] Report copertura semantica prodotto
- [ ] Report validazione semantica prodotto (FASE 3.5)

### Testing
- [ ] Mock server funzionante
- [ ] Esempi testati
- [ ] Casi d'uso coperti
- [ ] Integrazione con sistemi esterni

### Documentazione
- [ ] README con getting started
- [ ] Guide API per sviluppatori
- [ ] Changelog versioni
- [ ] Contatti supporto

---

## 🚀 Deployment

Una volta validato e arricchito:

1. **Registrazione PDND**
   - Carica specifica su Catalogo API
   - Configura e-service
   - Definisci SLA

2. **Ambiente Test**
   - Deploy mock/sandbox
   - Onboarding primi fruitori
   - Raccolta feedback

3. **Produzione**
   - Monitoring e alerting
   - Dashboard metriche
   - Piano rollback

---

## 📚 Risorse

- **Canvas Template**: `API_DESIGN_CANVAS.md`
- **Skill Completa**: `SKILL.md`
- **Quick Reference**: `QUICK_REFERENCE.md`
- **Esempio Completo**: `examples/esempio-completo.yaml`

---

**Versione Guida**: 1.0  
**Ultima revisione**: Febbraio 2024
