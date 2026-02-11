---
name: skill-openapi-modi
description: Creazione specifiche OpenAPI 3.0+ conformi alle Linee Guida AgID ModI (Modello di Interoperabilità) con arricchimento semantico JSON-LD. Include workflow completo con API Design Canvas, generazione automatica, validazione conformità e mappatura ontologie italiane (CPV, DCAT-AP_IT, CLV, COV). Usa questa skill quando l'utente chiede di creare specifiche OpenAPI per la PA italiana, menziona linee guida AgID, ModI, interoperabilità PA, o richiede conformità agli standard di interoperabilità italiani.
license: MIT
---

# Skill: OpenAPI Conforme alle Linee Guida AgID ModI

## Descrizione
Questa skill guida la creazione di specifiche OpenAPI 3.0+ conformi alle Linee Guida sull'Interoperabilità Tecnica delle Pubbliche Amministrazioni Italiane (ModI - Modello di Interoperabilità).

Le specifiche generate rispettano:
- Linee Guida AgID sull'interoperabilità tecnica delle PA
- Pattern di interazione REST
- Pattern di sicurezza per API
- Raccomandazioni di implementazione
- Standard OpenAPI 3.0+

## Trigger
Usa questa skill quando:
- L'utente chiede di creare una specifica OpenAPI per la PA italiana
- Si menzionano "linee guida AgID", "ModI", "interoperabilità PA"
- Si deve documentare un'API REST per enti pubblici italiani
- Si richiede conformità agli standard di interoperabilità italiani

## Workflow Integrato con MCP Tools

Questa skill integra tre strumenti MCP per un workflow completo:

### 1. **API Canvas Design** - Progettazione Iniziale
Prima di scrivere codice YAML, usa il canvas per progettare l'API:
- Definire scopo, stakeholder, casi d'uso
- Identificare risorse e operazioni
- Mappare dati di input/output
- Validare il design con il team

### 2. **OAS Checker MCP** - Validazione Conformità
Dopo aver generato la specifica, valida con oas-checker-mcp:
- Verifica conformità alle linee guida AgID
- Controlla pattern di sicurezza
- Valida naming convention
- Identifica problemi RFC 7807

### 3. **Dati Semantic MCP** - Arricchimento Semantico
Arricchisci gli schemi con ontologie italiane:
- Cerca vocabolari controllati AgID
- Trova riferimenti a ontologie nazionali (DCAT-AP_IT, CPV, ecc.)
- Aggiungi `x-jsonld-context` per linked data
- Inserisci `x-jsonld-type` per mappature semantiche
- **Suggerisci miglioramenti semantici** quando le descrizioni sono assenti o insufficienti
- **Usa sempre prefissi compatti** (`@vocab`, prefissi named) per minimizzare la verbosità delle URI

### Workflow Completo Passo-Passo

```
FASE 1: PROGETTAZIONE
→ Usa canvas API design per definire:
  - Chi usa l'API (stakeholder)
  - Cosa fa l'API (use cases)
  - Come funziona (risorse e operazioni)
  - Perché serve (obiettivi di business)

FASE 2: GENERAZIONE
→ Genera specifica OpenAPI basata sul canvas
→ Applica template ModI e pattern standard
→ Crea schemi dati preliminari

FASE 2.5: GENERAZIONE ESEMPI REQUEST/RESPONSE
→ Genera example/examples per ogni requestBody (POST, PUT, PATCH)
→ Genera example per ogni response 2xx con body
→ Genera examples multipli per operazioni con varianti (es. polling asincrono)
→ Verifica coerenza dati tra request e response
→ Usa valori realistici (nomi, codici, date italiane)

FASE 3: ARRICCHIMENTO SEMANTICO
→ Usa dati-semantic-mcp per:
  - Cercare ontologie rilevanti
  - Mappare entità a vocabolari controllati
  - Aggiungere annotazioni JSON-LD
  - Inserire riferimenti x-jsonld-*
  - IMPORTANTE: Usare sempre prefissi per compattare le URI (vedi sezione dedicata)
  - Se una proprietà/classe NON ha corrispondenza, suggerire alternative o estensioni custom
  - Se le descrizioni ontologiche sono assenti/povere, proporre descrizioni arricchite

FASE 3.5: VALIDAZIONE SEMANTICA VIA MCP (OBBLIGATORIA)
→ Per OGNI URI in x-jsonld-type: inspect_concept(uri) per verificare che esista
→ Per OGNI proprietà in x-jsonld-context: get_property_details(propertyUri) per verificare esistenza e domain/range
→ Per OGNI vocabolario controllato referenziato: list_vocabularies() per verificare che il ConceptScheme esista
→ Produrre report di validazione semantica:
  - URI verificate OK
  - URI inesistenti (da rimuovere o sostituire)
  - URI con domain/range incompatibile (da segnalare)
  - Gap semantici documentati

REGOLA CRITICA: Non usare MAI una URI ontologica copiata da un template
senza prima verificarla con schema-gov-it MCP.
I template in questa skill sono un PUNTO DI PARTENZA, non una fonte di verità.
La fonte di verità sono le ontologie accessibili via MCP.

Pattern di verifica per ogni mappatura:
1. search_concepts(keyword="nome proprietà")
2. Se trovata: get_property_details(propertyUri) per verificare domain/range
3. Se domain/range compatibili: usare nel context con prefisso
4. Se domain/range incompatibili: segnalare e cercare alternativa
5. Se non trovata: segnalare gap, cercare in ontologie alternative

FASE 4: VALIDAZIONE
→ Usa oas-checker-mcp per verificare:
  - Conformità ModI AgID
  - Pattern di sicurezza
  - Struttura e naming
  - Gestione errori

FASE 5: ITERAZIONE
→ Correggi problemi identificati
→ Ri-valida con oas-checker-mcp
→ Raffina annotazioni semantiche

FASE 6: GENERAZIONE LOG.md
→ Genera LOG.md nella stessa directory del file OpenAPI
→ Documenta: fasi completate, decisioni di design, report semantico
→ Elenca: mappature verificate, gap identificati, vocabolari usati
→ Include: istruzioni operative per ampliare/modificare le semantiche
→ Include: storico modifiche per tracciare l'evoluzione
```

## Struttura del Documento OpenAPI

### 1. Metadati Obbligatori (info)

```yaml
openapi: 3.0.3
info:
  title: Nome dell'API
  version: 1.0.0
  description: |
    Descrizione dettagliata dell'API che include:
    - Scopo e contesto di utilizzo
    - Funzionalità principali
    - Link a documentazione aggiuntiva
  termsOfService: 'https://example.gov.it/terms/'
  contact:
    name: Nome Ufficio/Struttura
    url: 'https://example.gov.it'
    email: api@example.gov.it
  license:
    name: CC BY 4.0
    url: 'https://creativecommons.org/licenses/by/4.0/'
  x-summary: Breve descrizione (max 80 caratteri)
  x-api-id: identificativo-unico-api
```

**Regole:**
- `version`: DEVE seguire il semantic versioning (MAJOR.MINOR.PATCH)
- `description`: DEVE essere esaustiva e includere informazioni sul contesto
- `x-api-id`: identificativo univoco dell'API nel catalogo
- `x-summary`: sintesi per il catalogo delle API

### 2. Server e Ambienti

```yaml
servers:
  - url: 'https://api.example.gov.it/v1'
    description: Ambiente di produzione
  - url: 'https://api-test.example.gov.it/v1'
    description: Ambiente di test
```

**Regole:**
- DEVE utilizzare HTTPS (TLS 1.2+)
- Il versionamento DOVREBBE essere nel path (es. /v1, /v2)
- DEVONO essere presenti almeno ambienti di test e produzione

### 3. Naming Convention per i Path

**Regole Obbligatorie:**
- Utilizzare il separatore `-` (kebab-case)
- Nomi al plurale per le collezioni: `/users`, `/documents`
- Nomi al singolare per le risorse: `/users/{user-id}`
- Path semplici, intuitivi e coerenti
- Evitare verbi nei path (usare i metodi HTTP)

**Esempi Corretti:**
```
GET    /documenti
GET    /documenti/{id-documento}
POST   /documenti
PUT    /documenti/{id-documento}
DELETE /documenti/{id-documento}
GET    /documenti/{id-documento}/allegati
```

**Esempi Scorretti:**
```
❌ /getDocumenti
❌ /documento_list
❌ /Documenti
❌ /documenti/get/{id}
```

### 4. Metodi HTTP e Semantica

| Metodo | Uso | Idempotente | Safe |
|--------|-----|-------------|------|
| GET | Lettura risorsa/collezione | ✓ | ✓ |
| POST | Creazione nuova risorsa | ✗ | ✗ |
| PUT | Aggiornamento completo | ✓ | ✗ |
| PATCH | Aggiornamento parziale | ✗ | ✗ |
| DELETE | Eliminazione risorsa | ✓ | ✗ |

**Regole:**
- DEVE rispettare la semantica HTTP standard
- GET e HEAD DEVONO essere safe (non modificano stato)
- PUT, DELETE DEVONO essere idempotenti
- POST NON è idempotente

### 5. Codici di Risposta Standard

**Codici Obbligatori:**

```yaml
responses:
  '200':
    description: La richiesta è stata elaborata con successo
  '201':
    description: La risorsa è stata creata con successo
  '204':
    description: Operazione completata senza contenuto di ritorno
  '400':
    description: Richiesta non valida
    content:
      application/problem+json:
        schema:
          $ref: '#/components/schemas/Problem'
  '401':
    description: Non autenticato
  '403':
    description: Non autorizzato
  '404':
    description: Risorsa non trovata
  '429':
    description: Troppe richieste (rate limiting)
    headers:
      X-RateLimit-Limit:
        schema:
          type: integer
        description: Limite di richieste
      X-RateLimit-Remaining:
        schema:
          type: integer
        description: Richieste rimanenti
      Retry-After:
        schema:
          type: integer
        description: Secondi prima di riprovare
  '500':
    description: Errore interno del server
    content:
      application/problem+json:
        schema:
          $ref: '#/components/schemas/Problem'
  '503':
    description: Servizio temporaneamente non disponibile
```

### 6. Schema Errori RFC 7807 (Problem Details)

**Obbligatorio** per tutte le risposte di errore:

```yaml
components:
  schemas:
    Problem:
      type: object
      required:
        - status
        - title
      properties:
        type:
          type: string
          format: uri
          description: URI che identifica il tipo di problema
          example: 'https://example.gov.it/probs/validation-error'
        status:
          type: integer
          format: int32
          description: Codice di stato HTTP
          example: 400
        title:
          type: string
          description: Titolo del problema
          example: Richiesta non valida
        detail:
          type: string
          description: Descrizione dettagliata dell'errore
          example: Il campo 'codice_fiscale' non è valido
        instance:
          type: string
          format: uri
          description: URI dell'istanza specifica del problema
          example: '/documenti/123'
```

### 7. Header HTTP

**Header Standard da Utilizzare:**

```yaml
components:
  headers:
    X-Request-ID:
      description: Identificativo univoco della richiesta per tracciamento
      schema:
        type: string
        format: uuid
    X-Correlation-ID:
      description: Identificativo per correlare richieste multiple
      schema:
        type: string
        format: uuid
    X-RateLimit-Limit:
      description: Numero massimo di richieste consentite
      schema:
        type: integer
    X-RateLimit-Remaining:
      description: Numero di richieste rimanenti
      schema:
      type: integer
```

**Naming Convention Header:**
- DOVREBBE preferirsi Hyphenated-Pascal-Case (X-Request-ID)
- Header custom DEVONO iniziare con X-

### 8. Paginazione

**Standard per Collezioni:**

```yaml
paths:
  /documenti:
    get:
      parameters:
        - name: limit
          in: query
          description: Numero massimo di elementi da restituire
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: offset
          in: query
          description: Numero di elementi da saltare
          schema:
            type: integer
            minimum: 0
            default: 0
        - name: cursor
          in: query
          description: Cursore per paginazione (alternativo a offset)
          schema:
            type: string
      responses:
        '200':
          description: Lista documenti
          headers:
            Link:
              description: Link per navigazione (first, prev, next, last)
              schema:
                type: string
          content:
            application/json:
              schema:
                type: object
                properties:
                  items:
                    type: array
                    items:
                      $ref: '#/components/schemas/Documento'
                  count:
                    type: integer
                    description: Numero totale di elementi
                  limit:
                    type: integer
                  offset:
                    type: integer
```

### 9. Filtri e Ricerca

**Query Parameters per Filtraggio:**

```yaml
parameters:
  - name: q
    in: query
    description: Testo di ricerca
    schema:
      type: string
  - name: stato
    in: query
    description: Filtro per stato
    schema:
      type: string
      enum: [BOZZA, PUBBLICATO, ARCHIVIATO]
  - name: data_da
    in: query
    description: Data inizio (ISO 8601)
    schema:
      type: string
      format: date
  - name: data_a
    in: query
    description: Data fine (ISO 8601)
    schema:
      type: string
      format: date
  - name: sort
    in: query
    description: Campo per ordinamento (+ ascendente, - discendente)
    schema:
      type: string
      example: "+data_creazione"
```

### 10. Pattern di Sicurezza

**DEVE implementare almeno uno dei seguenti pattern:**

#### Pattern ID_AUTH_REST_01 - Bearer Token

```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: |
        Token JWT per autenticazione.
        Include l'header Authorization: Bearer {token}

security:
  - bearerAuth: []
```

#### Pattern ID_AUTH_REST_02 - OAuth2

```yaml
components:
  securitySchemes:
    oauth2:
      type: oauth2
      flows:
        clientCredentials:
          tokenUrl: 'https://auth.example.gov.it/oauth2/token'
          scopes:
            read:documenti: Lettura documenti
            write:documenti: Scrittura documenti

security:
  - oauth2: [read:documenti]
```

### 11. Tags per Organizzazione

```yaml
tags:
  - name: Documenti
    description: Operazioni sui documenti
    x-goal: Gestione del ciclo di vita dei documenti
  - name: Allegati
    description: Gestione degli allegati
    x-goal: Upload e download di file allegati

paths:
  /documenti:
    get:
      tags:
        - Documenti
```

**Regole:**
- Raggruppare operazioni correlate
- Usare `x-goal` per indicare lo scopo funzionale

### 12. Annotazioni Semantiche con JSON-LD

Per massimizzare l'interoperabilità semantica, usa le estensioni `x-jsonld-*`:

#### x-jsonld-context - Contesto Linked Data

Il contesto JSON-LD mappa le proprietà a URI di ontologie. **Usare SEMPRE prefissi compatti**.

**Principi chiave per il context**:
1. Definire prima TUTTI i prefissi necessari
2. Impostare `@vocab` sul namespace più usato nello schema
3. Impostare `@base` sull'URL base delle risorse
4. Mappare ogni proprietà con notazione `prefisso:nome`
5. **Mai inserire URI complete** nelle mappature delle proprietà

```yaml
components:
  schemas:
    Documento:
      type: object
      x-jsonld-context:
        # 1. Prefissi (definire TUTTI quelli necessari)
        CPV: 'https://w3id.org/italia/onto/CPV/'
        dct: 'http://purl.org/dc/terms/'
        foaf: 'http://xmlns.com/foaf/0.1/'
        dcat: 'http://www.w3.org/ns/dcat#'
        schema: 'http://schema.org/'
        # 2. Namespace di default e base risorse
        '@vocab': 'https://w3id.org/italia/onto/CPV/'
        '@base': 'https://api.example.gov.it/v1/documenti/'
        # 3. Alias strutturali
        id: '@id'
        type: '@type'
        # 4. Mappature proprietà con prefissi compatti
        titolo: 'dct:title'
        descrizione: 'dct:description'
        data-creazione: 'dct:created'
        data-modifica: 'dct:modified'
        formato: 'dct:format'
        identificativo: 'dct:identifier'
      properties:
        id:
          type: string
          format: uri
        titolo:
          type: string
        descrizione:
          type: string
```

#### x-jsonld-type - Tipo Semantico

Specifica il tipo ontologico dell'entità:

```yaml
Documento:
  type: object
  x-jsonld-type: 'dcat:Dataset'
  x-jsonld-context:
    '@vocab': 'https://w3id.org/italia/onto/CPV/'
  properties:
    # ...

Persona:
  type: object
  x-jsonld-type: 'CPV:Person'  # Usare CPV:Person per contesto PA italiana
  x-jsonld-context:
    CPV: 'https://w3id.org/italia/onto/CPV/'
    foaf: 'http://xmlns.com/foaf/0.1/'
    nome: 'CPV:givenName'     # proprietà nativa CPV (NON foaf:firstName)
    cognome: 'CPV:familyName' # proprietà nativa CPV (NON foaf:lastName)
    email: 'foaf:mbox'        # Gap CPV: nessuna email nativa, fallback foaf
  properties:
    nome: {type: string}
    cognome: {type: string}
    email: {type: string, format: email}

Organizzazione:
  type: object
  x-jsonld-type: 'http://www.w3.org/ns/org#Organization'
  properties:
    nome: {type: string}
    codice-ipa: {type: string}
```

#### Ontologie e Vocabolari Italiani Comuni

**Core Vocabularies Pubbliche Amministrazioni (CPV)**

```yaml
x-jsonld-context:
  '@vocab': 'https://w3id.org/italia/onto/CPV/'
  # Classi principali
  Persona: 'CPV:Person'
  Indirizzo: 'CLV:Address'  # Core Location Vocabulary
  Organizzazione: 'COV:Organization'
  Documento: 'CPV:Document'
```

**DCAT-AP_IT (Dati aperti)**

```yaml
x-jsonld-context:
  dcat: 'http://www.w3.org/ns/dcat#'
  dct: 'http://purl.org/dc/terms/'
  foaf: 'http://xmlns.com/foaf/0.1/'
  
Dataset:
  x-jsonld-type: 'dcat:Dataset'
  x-jsonld-context:
    titolo: 'dct:title'
    descrizione: 'dct:description'
    parole-chiave: 'dcat:keyword'
    temi: 'dcat:theme'
    editore: 'dct:publisher'
    contatto: 'dcat:contactPoint'
    distribuzione: 'dcat:distribution'
```

**Schema.org (interoperabilità globale)**

```yaml
x-jsonld-context:
  schema: 'http://schema.org/'
  
Evento:
  x-jsonld-type: 'schema:Event'
  x-jsonld-context:
    nome: 'schema:name'
    descrizione: 'schema:description'
    data-inizio: 'schema:startDate'
    data-fine: 'schema:endDate'
    luogo: 'schema:location'
```

#### Strategia Prefissi e Compattazione URI

**REGOLA FONDAMENTALE**: Usare SEMPRE prefissi per ridurre la verbosità delle URI nei contesti JSON-LD. Questo riduce la dimensione dei dati trasmessi e migliora la leggibilità.

**Prefissi Standard da Definire Sempre**:

```yaml
x-jsonld-context:
  # Prefissi per ontologie italiane
  CPV: 'https://w3id.org/italia/onto/CPV/'
  CLV: 'https://w3id.org/italia/onto/CLV/'
  COV: 'https://w3id.org/italia/onto/COV/'
  SM: 'https://w3id.org/italia/onto/SM/'
  TI: 'https://w3id.org/italia/onto/TI/'
  l0: 'https://w3id.org/italia/onto/l0/'
  # Prefissi per ontologie internazionali
  dct: 'http://purl.org/dc/terms/'
  dcat: 'http://www.w3.org/ns/dcat#'
  foaf: 'http://xmlns.com/foaf/0.1/'
  schema: 'http://schema.org/'
  org: 'http://www.w3.org/ns/org#'
  skos: 'http://www.w3.org/2004/02/skos/core#'
  xsd: 'http://www.w3.org/2001/XMLSchema#'
  # Prefisso base per le risorse dell'API
  '@base': 'https://api.example.gov.it/v1/'
  # Vocabolario di default (il più usato nello schema)
  '@vocab': 'https://w3id.org/italia/onto/CPV/'
```

**Regole di Compattazione**:

1. **`@vocab`** per il namespace più frequente nello schema: evita di ripetere il prefisso per ogni proprietà del namespace dominante
2. **`@base`** per le URI delle risorse dell'API: permette di usare path relativi come `@id`
3. **Prefissi named** per tutti gli altri namespace: usare `dct:title` invece di `http://purl.org/dc/terms/title`
4. **Mai URI espanse** nel context: se una URI non ha prefisso definito, crearne uno
5. **Prefissi per vocabolari controllati**: definire prefissi anche per i `ConceptScheme` referenziati

**Esempio - PRIMA (verboso, da evitare)**:
```yaml
x-jsonld-context:
  nome: 'https://w3id.org/italia/onto/CPV/givenName'
  cognome: 'https://w3id.org/italia/onto/CPV/familyName'
  indirizzo: 'https://w3id.org/italia/onto/CLV/hasAddress'
  organizzazione: 'https://w3id.org/italia/onto/COV/Organization'
```

**Esempio - DOPO (compatto, corretto)**:
```yaml
x-jsonld-context:
  CPV: 'https://w3id.org/italia/onto/CPV/'
  CLV: 'https://w3id.org/italia/onto/CLV/'
  COV: 'https://w3id.org/italia/onto/COV/'
  nome: 'CPV:givenName'      # proprietà nativa CPV (NON foaf:givenName)
  cognome: 'CPV:familyName'  # proprietà nativa CPV (NON foaf:familyName)
  indirizzo: 'CLV:hasAddress'
  organizzazione: 'COV:Organization'
```

**Compattazione nelle risposte MCP**: Quando si interrogano i tool MCP (schema-gov-it), i risultati possono contenere URI lunghe. Prima di inserirle nel context JSON-LD:
1. Identificare il namespace comune e assegnare un prefisso
2. Usare il prefisso per tutte le proprietà di quel namespace
3. Se il namespace è già tra i prefissi standard, riutilizzarlo

#### Suggerimenti Proattivi per la Semantica

Quando si arricchisce uno schema con annotazioni semantiche, la skill DEVE comportarsi proattivamente:

**1. Proprietà senza corrispondenza ontologica**

Se una proprietà dello schema non trova corrispondenza nelle ontologie italiane/internazionali:
- **Segnalarlo esplicitamente** all'utente con una nota
- **Suggerire alternative**: cercare proprietà simili in altre ontologie (schema.org, Dublin Core, FOAF)
- **Proporre estensione custom**: se nessuna ontologia copre il concetto, suggerire una URI custom nel namespace dell'ente con pattern `https://w3id.org/italia/onto/{Ontologia}/{NomeProprietà}`
- **Documentare il gap**: aggiungere un commento YAML che segnali l'assenza di mappatura standard

```yaml
# Esempio: proprietà senza corrispondenza
numero-protocollo:
  type: string
  description: |
    Numero di protocollo dell'ente.
    NOTA SEMANTICA: Non esiste una proprietà standard nelle ontologie
    italiane per il numero di protocollo. Si suggerisce:
    - Opzione 1: usare dct:identifier con qualificatore
    - Opzione 2: proporre estensione a ontologia CPV
  # Mappatura provvisoria con best-effort
  # x-jsonld-mapping: 'dct:identifier' (approssimativa)
```

**2. Descrizioni ontologiche assenti o insufficienti**

Se l'ontologia fornisce una classe/proprietà ma la `description` è:
- **Assente**: proporre una descrizione in italiano basata sul contesto d'uso
- **Solo in inglese**: suggerire la traduzione italiana
- **Generica/vaga**: arricchirla con dettagli specifici del dominio PA

```yaml
# Esempio: descrizione arricchita
codice-fiscale:
  type: string
  description: |
    Codice fiscale italiano della persona fisica.
    Identificativo univoco rilasciato dall'Agenzia delle Entrate.
    Formato: 16 caratteri alfanumerici (6 lettere + 2 cifre + 1 lettera
    + 2 cifre + 1 lettera + 3 cifre + 1 lettera di controllo).
    Riferimento ontologico: CPV:taxCode
    (l'ontologia non fornisce descrizione - questa è stata integrata)
  pattern: '^[A-Z]{6}[0-9]{2}[A-Z][0-9]{2}[A-Z][0-9]{3}[A-Z]$'
```

**3. Tipi enum senza vocabolario controllato**

Se uno schema ha campi `enum` che non trovano corrispondenza in vocabolari controllati AgID:
- **Segnalare** che il vocabolario non esiste o non copre quei valori
- **Suggerire** il vocabolario più vicino, anche se parziale
- **Proporre** la creazione di un vocabolario custom con URI strutturate
- **Fornire i codici skos:notation** quando disponibili nel vocabolario trovato

```yaml
# Esempio: enum con suggerimento vocabolario
stato:
  type: string
  description: |
    Stato della pratica.
    NOTA: Non esiste un vocabolario controllato AgID specifico per
    gli stati delle pratiche edilizie. Si suggerisce di:
    1. Verificare periodicamente https://github.com/italia/daf-ontologie-vocabolari-controllati
    2. Proporre un vocabolario al repository nazionale
    3. Nel frattempo, usare la codifica locale documentata qui sotto
  enum: [BOZZA, PRESENTATA, IN_ISTRUTTORIA, APPROVATA, RESPINTA]
```

**4. Workflow di analisi semantica per ogni schema**

Per ogni schema nell'API, eseguire questo ciclo:

```
PER OGNI SCHEMA:
  1. Cerca tipo ontologico → Se trovato: usa x-jsonld-type
                            → Se NON trovato: suggerisci tipo più vicino + segnala gap

  2. PER OGNI PROPRIETÀ:
     a. Cerca mappatura ontologica → Se trovata: aggiungi al context con prefisso
                                   → Se NON trovata: suggerisci alternativa
     b. Verifica description ontologica → Se buona: citala
                                        → Se assente/povera: proponi descrizione arricchita
     c. Se è un enum: cerca vocabolario controllato → Se trovato: referenzia con URI + prefisso
                                                    → Se NON trovato: segnala e suggerisci

  3. Genera report di copertura semantica:
     - N proprietà mappate / N totali
     - N gap identificati con suggerimenti
     - N descrizioni arricchite
```

#### Mappature Complete per Entità Comuni PA

**Persona (Cittadino/Dipendente)**

```yaml
Persona:
  type: object
  x-jsonld-type: 'CPV:Person'
  x-jsonld-context:
    '@vocab': 'https://w3id.org/italia/onto/CPV/'
    foaf: 'http://xmlns.com/foaf/0.1/'
    # Mappature verificate via MCP schema-gov-it
    nome: 'CPV:givenName'       # DatatypeProperty, domain: CPV:Person, range: xsd:string
    cognome: 'CPV:familyName'   # DatatypeProperty (Functional), domain: CPV:Person, range: xsd:string
    codice-fiscale: 'CPV:taxCode'
    data-nascita: 'CPV:dateOfBirth'  # DatatypeProperty (Functional), domain: CPV:Person, range: xsd:dateTime
    email: 'foaf:mbox'          # Gap CPV: nessuna proprietà email nativa
    telefono: 'foaf:phone'      # Gap CPV: nessuna proprietà telefono nativa
  required: [codice-fiscale, nome, cognome]
  properties:
    codice-fiscale:
      type: string
      pattern: '^[A-Z]{6}[0-9]{2}[A-Z][0-9]{2}[A-Z][0-9]{3}[A-Z]$'
    nome: {type: string}
    cognome: {type: string}
    data-nascita: {type: string, format: date}
    email: {type: string, format: email}
    telefono: {type: string}
```

**Indirizzo**

```yaml
Indirizzo:
  type: object
  x-jsonld-type: 'CLV:Address'
  x-jsonld-context:
    '@vocab': 'https://w3id.org/italia/onto/CLV/'
    # Mappature verificate via MCP schema-gov-it
    via: 'CLV:fullAddress'     # DatatypeProperty (Functional), domain: CLV:Address, range: Literal
    civico: 'CLV:hasNumber'    # ObjectProperty, domain: CLV:Address, range: CLV:CivicNumbering
    # cap: GAP SEMANTICO - CLV:postCode NON ESISTE nell'ontologia CLV
    comune: 'CLV:hasCity'      # ObjectProperty, domain: CLV:Address, range: CLV:City
    provincia: 'CLV:hasProvince'  # ObjectProperty, range: CLV:Province (NON stringa)
    regione: 'CLV:hasRegion'   # ObjectProperty, range: CLV:Region (NON stringa)
  properties:
    via: {type: string}
    civico: {type: string}
    cap:
      type: string
      pattern: '^\d{5}$'
      description: 'GAP SEMANTICO: CLV:postCode non esiste in CLV. Nessuna mappatura ontologica.'
    comune: {type: string}
    provincia: {type: string, minLength: 2, maxLength: 2}
    regione: {type: string}
```

**Organizzazione/Ente**

```yaml
Organizzazione:
  type: object
  x-jsonld-type: 'COV:Organization'
  x-jsonld-context:
    '@vocab': 'https://w3id.org/italia/onto/COV/'
    CLV: 'https://w3id.org/italia/onto/CLV/'
    SM: 'https://w3id.org/italia/onto/SM/'
    # Mappature verificate via MCP schema-gov-it
    nome: 'COV:legalName'      # DatatypeProperty, domain: COV:Organization, range: Literal
    codice-ipa: 'COV:IPAcode'
    partita-iva: 'COV:VATnumber'  # DatatypeProperty (Functional), domain: COV:PrivateOrganization, range: Literal
    # NOTA: COV:taxCode è il codice fiscale dell'organizzazione, NON la partita IVA!
    # email: GAP SEMANTICO - COV:email NON ESISTE. Alternativa: SM:emailAddress (domain: SM:Email)
    # pec: GAP SEMANTICO - COV:pec NON ESISTE. Nessuna proprietà standard per PEC in COV.
    indirizzo: 'CLV:hasAddress'
  properties:
    nome: {type: string}
    codice-ipa:
      type: string
      pattern: '^[a-z0-9_]+$'
    partita-iva:
      type: string
      pattern: '^\d{11}$'
    email:
      type: string
      format: email
      description: 'GAP SEMANTICO: COV:email non esiste. Alternativa: SM:emailAddress (domain SM:Email).'
    pec:
      type: string
      format: email
      description: 'GAP SEMANTICO: COV:pec non esiste. Nessuna proprietà standard per PEC nelle ontologie nazionali.'
    indirizzo:
      $ref: '#/components/schemas/Indirizzo'
```

**Documento Amministrativo**

```yaml
DocumentoAmministrativo:
  type: object
  x-jsonld-type: 'CPV:Document'
  x-jsonld-context:
    '@vocab': 'https://w3id.org/italia/onto/CPV/'
    dct: 'http://purl.org/dc/terms/'
    foaf: 'http://xmlns.com/foaf/0.1/'
    identificativo: 'dct:identifier'
    titolo: 'dct:title'
    descrizione: 'dct:description'
    tipo: 'dct:type'
    formato: 'dct:format'
    data-creazione: 'dct:created'
    data-modifica: 'dct:modified'
    autore: 'dct:creator'
    editore: 'dct:publisher'
    soggetto: 'dct:subject'
    classificazione: 'CPV:hasClassification'
  properties:
    identificativo: {type: string}
    titolo: {type: string}
    descrizione: {type: string}
    tipo:
      type: string
      enum: [DELIBERA, DETERMINA, ORDINANZA, DECRETO]
    numero-protocollo: {type: string}
    data-protocollo: {type: string, format: date-time}
```

**Pratica/Procedimento**

```yaml
Pratica:
  type: object
  x-jsonld-type: 'https://w3id.org/italia/onto/CPV/Procedure'
  x-jsonld-context:
    '@vocab': 'https://w3id.org/italia/onto/CPV/'
    dct: 'http://purl.org/dc/terms/'
    numero: 'dct:identifier'
    oggetto: 'dct:title'
    descrizione: 'dct:description'
    stato: 'CPV:hasStatus'
    data-apertura: 'CPV:startDate'
    data-chiusura: 'CPV:endDate'
    responsabile: 'CPV:hasResponsible'
  properties:
    numero: {type: string}
    oggetto: {type: string}
    descrizione: {type: string}
    stato:
      type: string
      enum: [APERTA, IN_LAVORAZIONE, SOSPESA, CONCLUSA]
    data-apertura: {type: string, format: date-time}
    data-chiusura: {type: string, format: date-time}
```

#### Uso con dati-semantic-mcp (schema-gov-it)

Quando crei uno schema, usa i tool MCP schema-gov-it per:

1. **Cercare ontologie rilevanti** - con `search_concepts` o `explore_classes`
   ```
   search_concepts(keyword="persona") → trova CPV:Person
   ```

2. **Ispezionare classi e proprietà** - con `inspect_concept` e `list_properties`
   ```
   inspect_concept(uri="https://w3id.org/italia/onto/CPV/Person")
   → Restituisce: definizione, gerarchia, proprietà in/out, usage
   ```

3. **Scoprire vocabolari controllati** - con `list_vocabularies` e `browse_vocabulary`
   ```
   list_vocabularies() → elenca ConceptScheme disponibili
   browse_vocabulary(schemeUri="...", keyword="documento")
   ```

4. **Navigare ontologie** - con `list_ontologies` e `explore_ontology`
   ```
   list_ontologies() → lista ontologie con titoli
   explore_ontology(ontologyUri="https://w3id.org/italia/onto/CPV")
   ```

5. **Limitare i dati ricevuti** - FONDAMENTALE per efficienza
   - Usare sempre `keyword` e `limit` per filtrare le query
   - Preferire `search_concepts` con keyword specifico invece di browse generici
   - Usare `list_properties(ontologyUri=..., propertyType="object"|"datatype")` per filtrare
   - Usare `browse_vocabulary(keyword=...)` invece di scaricare tutto il vocabolario
   - Se il risultato contiene URI lunghe, estrarre il prefisso comune e riutilizzarlo

6. **Quando NON trovi corrispondenza**
   - Provare keyword alternativi (sinonimi, inglese/italiano)
   - Cercare in ontologie diverse (CPV, CLV, COV, schema.org)
   - Usare `find_relations` per scoprire connessioni indirette
   - Se il gap è confermato: **segnalarlo all'utente con suggerimento**

#### Template con Annotazioni Semantiche Complete

```yaml
components:
  schemas:
    # Persona con pieno supporto Linked Data
    PersonaCompleta:
      type: object
      description: |
        Persona fisica con mappatura completa a ontologie nazionali.
        Conforme a CPV (Core Vocabulary Pubbliche Amministrazioni).
      x-jsonld-type: 'https://w3id.org/italia/onto/CPV/Person'
      x-jsonld-context:
        '@vocab': 'https://w3id.org/italia/onto/CPV/'
        '@base': 'https://api.example.gov.it/persone/'
        foaf: 'http://xmlns.com/foaf/0.1/'
        xsd: 'http://www.w3.org/2001/XMLSchema#'
        # Mappature verificate via MCP schema-gov-it
        id: '@id'
        type: '@type'
        codice-fiscale:
          '@id': 'CPV:taxCode'
          '@type': 'xsd:string'
        nome:
          '@id': 'CPV:givenName'  # DatatypeProperty, domain: CPV:Person, range: xsd:string
          '@language': 'it'
        cognome:
          '@id': 'CPV:familyName'  # DatatypeProperty (Functional), domain: CPV:Person, range: xsd:string
          '@language': 'it'
        data-nascita:
          '@id': 'CPV:dateOfBirth'  # DatatypeProperty (Functional), domain: CPV:Person, range: xsd:dateTime
          '@type': 'xsd:date'
        luogo-nascita:
          '@id': 'CPV:hasBirthPlace'  # ObjectProperty, range: l0:Location - OK
          '@type': '@id'
        residenza:
          '@id': 'CPV:hasResidenceInTime'  # ObjectProperty, domain: CPV:Person, range: CPV:ResidenceInTime
          '@type': '@id'
        cittadinanza:
          '@id': 'CPV:hasCitizenship'
          '@type': '@id'
        contatti:
          '@id': 'CPV:hasContactPoint'
          '@container': '@set'
      required:
        - codice-fiscale
        - nome
        - cognome
      properties:
        id:
          type: string
          format: uri
          readOnly: true
          example: 'https://api.example.gov.it/persone/RSSMRA85M01H501U'
        codice-fiscale:
          type: string
          pattern: '^[A-Z]{6}[0-9]{2}[A-Z][0-9]{2}[A-Z][0-9]{3}[A-Z]$'
          example: 'RSSMRA85M01H501U'
        nome:
          type: string
          minLength: 1
          maxLength: 100
          example: 'Mario'
        cognome:
          type: string
          minLength: 1
          maxLength: 100
          example: 'Rossi'
        data-nascita:
          type: string
          format: date
          example: '1985-08-01'
        luogo-nascita:
          $ref: '#/components/schemas/Comune'
        residenza:
          $ref: '#/components/schemas/Indirizzo'
        cittadinanza:
          type: string
          description: Codice ISO 3166-1 alpha-2 della nazione
          pattern: '^[A-Z]{2}$'
          example: 'IT'
        contatti:
          type: array
          items:
            $ref: '#/components/schemas/Contatto'
```

### 13. Schemi Riutilizzabili

```yaml
components:
  schemas:
    Timestamp:
      type: string
      format: date-time
      description: Timestamp in formato ISO 8601
      example: '2024-02-10T14:30:00Z'
    
    CodiceFiscale:
      type: string
      pattern: '^[A-Z]{6}[0-9]{2}[A-Z][0-9]{2}[A-Z][0-9]{3}[A-Z]$'
      description: Codice fiscale italiano
      example: 'RSSMRA85M01H501U'
    
    PartitaIVA:
      type: string
      pattern: '^[0-9]{11}$'
      description: Partita IVA italiana
      example: '12345678901'
    
    IPA:
      type: string
      pattern: '^[a-z0-9_]+$'
      description: Codice IPA dell'ente
      example: 'comune_roma'
```

### 14. Monitoraggio e Health Check

**Endpoint Obbligatorio:**

```yaml
paths:
  /status:
    get:
      summary: Verifica stato del servizio
      description: Endpoint per monitoraggio e health check
      tags:
        - System
      responses:
        '200':
          description: Servizio operativo
          content:
            application/json:
              schema:
                type: object
                properties:
                  status:
                    type: string
                    enum: [OK, DEGRADED, DOWN]
                  version:
                    type: string
                  timestamp:
                    type: string
                    format: date-time
        '503':
          description: Servizio non disponibile
```

### 15. Documentazione e Esempi

**Ogni schema DEVE includere:**
- `description`: Descrizione chiara
- `example` o `examples`: Esempi concreti
- Riferimenti a vocabolari controllati quando applicabile

```yaml
components:
  schemas:
    Documento:
      type: object
      description: |
        Rappresenta un documento amministrativo.
        Conforme al vocabolario DCAT-AP_IT.
      required:
        - id
        - titolo
        - tipo
      properties:
        id:
          type: string
          format: uuid
          description: Identificativo univoco del documento
          example: '550e8400-e29b-41d4-a716-446655440000'
        titolo:
          type: string
          minLength: 1
          maxLength: 500
          description: Titolo del documento
          example: 'Delibera di Giunta n. 123/2024'
        tipo:
          type: string
          description: Tipologia documento secondo classificazione AGID
          enum:
            - DELIBERA
            - DETERMINA
            - ORDINANZA
            - DECRETO
          example: DELIBERA
        data_protocollo:
          $ref: '#/components/schemas/Timestamp'
        numero_protocollo:
          type: string
          pattern: '^\d{4}/\d{7}$'
          description: Numero di protocollo nel formato ANNO/NUMERO
          example: '2024/0001234'
```

## Pattern di Interazione

### Pattern BLOCK_REST - Interazione Bloccante

Per operazioni sincrone che completano rapidamente (< 10 secondi):

```yaml
paths:
  /calcola-imposte:
    post:
      summary: Calcolo imposte
      operationId: calcolaImposte
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RichiestaCalcolo'
      responses:
        '200':
          description: Calcolo completato
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RisultatoCalcolo'
        '400':
          $ref: '#/components/responses/BadRequest'
```

### Pattern NONBLOCK_REST_PULL - Interazione Non Bloccante (Polling)

Per operazioni asincrone che richiedono tempo (> 10 secondi):

```yaml
paths:
  /elaborazioni:
    post:
      summary: Avvia elaborazione asincrona
      operationId: avviaElaborazione
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RichiestaElaborazione'
      responses:
        '202':
          description: Elaborazione avviata
          headers:
            Location:
              description: URL per verificare lo stato
              schema:
                type: string
                format: uri
          content:
            application/json:
              schema:
                type: object
                properties:
                  id_elaborazione:
                    type: string
                    format: uuid
                  stato:
                    type: string
                    enum: [IN_CORSO, COMPLETATA, ERRORE]
                  link_stato:
                    type: string
                    format: uri
  
  /elaborazioni/{id}:
    get:
      summary: Verifica stato elaborazione
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Stato elaborazione
          content:
            application/json:
              schema:
                type: object
                properties:
                  id:
                    type: string
                    format: uuid
                  stato:
                    type: string
                    enum: [IN_CORSO, COMPLETATA, ERRORE]
                  progresso:
                    type: integer
                    minimum: 0
                    maximum: 100
                  risultato:
                    description: Presente solo se stato=COMPLETATA
                    $ref: '#/components/schemas/RisultatoElaborazione'
                  errore:
                    description: Presente solo se stato=ERRORE
                    $ref: '#/components/schemas/Problem'
```

## Best Practices Specifiche ModI

### 1. Logging e Tracciamento

Ogni operazione DEVE tracciare:
- Timestamp della richiesta
- Identificativo del fruitore
- Identificativo dell'operazione
- Esito della chiamata
- ID univoco richiesta (X-Request-ID)

**NON tracciare:**
- Password
- Chiavi private
- Token di autenticazione
- Dati sensibili non necessari

### 2. Rate Limiting

```yaml
paths:
  /api-resource:
    get:
      responses:
        '200':
          headers:
            X-RateLimit-Limit:
              description: Numero massimo di richieste per finestra temporale
              schema:
                type: integer
              example: 1000
            X-RateLimit-Remaining:
              description: Richieste rimanenti nella finestra corrente
              schema:
                type: integer
              example: 750
            X-RateLimit-Reset:
              description: Timestamp Unix del reset del contatore
              schema:
                type: integer
              example: 1707577200
```

### 3. Versioning

**Strategie supportate:**
- URL path versioning (raccomandato): `/v1/risorse`, `/v2/risorse`
- Header versioning: `Accept: application/vnd.api.v2+json`

**Regole:**
- Incrementare MAJOR version per breaking changes
- Mantenere compatibilità per almeno 2 versioni major
- Documentare deprecazioni con anticipo

### 4. Content Negotiation

```yaml
paths:
  /documenti/{id}:
    get:
      parameters:
        - name: Accept
          in: header
          schema:
            type: string
            enum:
              - application/json
              - application/xml
              - application/pdf
      responses:
        '200':
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Documento'
            application/xml:
              schema:
                $ref: '#/components/schemas/Documento'
            application/pdf:
              schema:
                type: string
                format: binary
```

### 5. CORS e Sicurezza

```yaml
# Da configurare a livello server, non nella specifica OpenAPI
# ma documentare i requisiti:
info:
  description: |
    ## Requisiti CORS
    L'API supporta CORS con le seguenti configurazioni:
    - Access-Control-Allow-Origin: domini autorizzati
    - Access-Control-Allow-Methods: GET, POST, PUT, DELETE
    - Access-Control-Allow-Headers: Authorization, Content-Type, X-Request-ID
    - Access-Control-Max-Age: 3600
```

## Checklist di Conformità ModI

Prima di pubblicare la specifica, verificare:

### Struttura e Conformità
- [ ] OpenAPI versione 3.0.3 o superiore
- [ ] Info block completo (title, version, description, contact)
- [ ] `x-api-id` univoco presente
- [ ] Tutti gli endpoint usano HTTPS
- [ ] Path in kebab-case
- [ ] Metodi HTTP usati correttamente
- [ ] Risposte 4xx e 5xx con schema Problem (RFC 7807)
- [ ] Endpoint /status per health check
- [ ] Security scheme definito e applicato
- [ ] Rate limiting headers documentati
- [ ] Paginazione implementata per collezioni
- [ ] X-Request-ID per tracciamento
- [ ] Esempi presenti per ogni requestBody (POST, PUT, PATCH)
- [ ] Esempi presenti per ogni response 2xx con body
- [ ] Esempi di errore presenti nei components/responses
- [ ] Esempi coerenti tra request e response (dati corrispondenti)
- [ ] Esempi con valori realistici (nomi, codici, date italiane)
- [ ] Collezioni con almeno 2 item diversificati negli esempi
- [ ] Descrizioni chiare e in italiano
- [ ] Codici fiscali/P.IVA con pattern validation
- [ ] Date in formato ISO 8601
- [ ] Riferimenti a vocabolari controllati AGID quando applicabile

### Semantica e JSON-LD
- [ ] **OGNI URI ontologica verificata via MCP** (inspect_concept o get_property_details) - MAI copiare da template senza verificare
- [ ] Tutti i context JSON-LD usano prefissi compatti (NO URI espanse)
- [ ] `@vocab` definito per il namespace dominante di ogni schema
- [ ] `@base` definito per le URI delle risorse dell'API
- [ ] Ogni schema principale ha `x-jsonld-type` con prefisso
- [ ] Domain/range di ogni proprietà verificati come compatibili con lo schema
- [ ] Gap semantici segnalati con nota nelle description
- [ ] Proprietà senza mappatura ontologica documentate con suggerimento alternativo
- [ ] Enum senza vocabolario controllato: gap segnalato con riferimento a repository nazionale
- [ ] Descrizioni arricchite dove l'ontologia non fornisce testo sufficiente
- [ ] Report di copertura semantica incluso (proprietà mappate vs totali)
- [ ] Report di validazione semantica prodotto (FASE 3.5)

## Template Base

```yaml
openapi: 3.0.3
info:
  title: API [Nome Servizio]
  version: 1.0.0
  description: |
    Descrizione dettagliata dell'API.
    
    ## Scopo
    [Spiegare lo scopo dell'API]
    
    ## Funzionalità
    - Funzionalità 1
    - Funzionalità 2
    
    ## Documentazione
    - [Link documentazione estesa]
  contact:
    name: [Nome Ufficio]
    email: [email]@[ente].gov.it
    url: https://[ente].gov.it
  license:
    name: CC BY 4.0
    url: https://creativecommons.org/licenses/by/4.0/
  x-api-id: [identificativo-univoco]
  x-summary: [Descrizione breve max 80 caratteri]

servers:
  - url: https://api.[ente].gov.it/v1
    description: Produzione
  - url: https://api-test.[ente].gov.it/v1
    description: Test

tags:
  - name: [Categoria1]
    description: [Descrizione categoria]
    x-goal: [Obiettivo funzionale]

paths:
  /status:
    get:
      summary: Health check
      operationId: getStatus
      tags:
        - System
      responses:
        '200':
          description: Servizio operativo
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/StatusResponse'
        '503':
          description: Servizio non disponibile

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: Token JWT per autenticazione

  schemas:
    Problem:
      type: object
      required:
        - status
        - title
      properties:
        type:
          type: string
          format: uri
        status:
          type: integer
          format: int32
        title:
          type: string
        detail:
          type: string
        instance:
          type: string
          format: uri

    StatusResponse:
      type: object
      properties:
        status:
          type: string
          enum: [OK, DEGRADED, DOWN]
        version:
          type: string
        timestamp:
          type: string
          format: date-time

  responses:
    BadRequest:
      description: Richiesta non valida
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
    
    Unauthorized:
      description: Non autenticato
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
    
    Forbidden:
      description: Non autorizzato
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
    
    NotFound:
      description: Risorsa non trovata
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
    
    TooManyRequests:
      description: Troppe richieste
      headers:
        X-RateLimit-Limit:
          schema:
            type: integer
        X-RateLimit-Remaining:
          schema:
            type: integer
        Retry-After:
          schema:
            type: integer
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
    
    InternalServerError:
      description: Errore interno del server
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'

security:
  - bearerAuth: []
```

## Workflow di Creazione

Quando l'utente richiede una specifica OpenAPI:

### FASE 1: Progettazione con Canvas API

**Obiettivo**: Raccogliere requisiti e progettare l'API prima di scrivere YAML.

**Azioni**:
1. Chiedi all'utente di usare un canvas API design (strumento visuale o template)
2. Definire insieme:
   - **Chi**: Stakeholder (cittadini, imprese, altre PA)
   - **Cosa**: Casi d'uso principali (es: "richiedere certificato", "consultare pratiche")
   - **Come**: Risorse REST (es: `/certificati`, `/pratiche/{id}`)
   - **Perché**: Obiettivi di business e metriche di successo

**Template Domande Canvas**:
```
🎯 OBIETTIVO API
- Qual è lo scopo principale di questa API?
- Quali problemi risolve?

👥 STAKEHOLDER
- Chi userà questa API? (cittadini, imprese, altre PA)
- Quali permessi servono per accedere?

📋 CASI D'USO PRINCIPALI (3-5)
1. [Attore] vuole [azione] per [scopo]
2. ...

🗂️ RISORSE PRINCIPALI
- Risorsa 1: [nome] (es: Documenti)
  - GET: elenco con filtri
  - POST: creazione
  - GET /{id}: dettaglio
  - PUT /{id}: aggiornamento
  - DELETE /{id}: eliminazione

📊 DATI INPUT/OUTPUT
- Quali dati sono necessari in input?
- Quali dati vengono restituiti?
- Ci sono vincoli di validazione?

🔒 REQUISITI SICUREZZA
- Autenticazione: JWT / OAuth2 / mTLS?
- Autorizzazione: ruoli, ACL?
- Dati sensibili: quali e come proteggerli?

⚡ REQUISITI NON FUNZIONALI
- Volumi attesi: richieste/giorno?
- Tempi di risposta: sincrono (<1s) / asincrono?
- Rate limiting: necessario?
- Disponibilità richiesta: 99.9%?
```

**Output Fase 1**: Documento di design condiviso con il team.

---

### FASE 2: Generazione Specifica OpenAPI

**Obiettivo**: Trasformare il design in specifica OpenAPI conforme ModI.

**Azioni**:
1. Genera struttura base (info, servers, security)
2. Per ogni risorsa del canvas:
   - Crea path REST
   - Definisci operazioni (GET, POST, PUT, DELETE)
   - Crea schemi dati
3. Aggiungi pattern ModI:
   - Paginazione per collezioni
   - Filtri e ricerca
   - Gestione errori RFC 7807
   - Health check `/status`

**Output Fase 2**: File `openapi.yaml` conforme alle linee guida base, con esempi request/response per ogni operazione.

---

### FASE 2.5: Generazione Esempi Request/Response (OBBLIGATORIA)

**Obiettivo**: Ogni operazione DEVE avere esempi realistici e coerenti tra loro per request body e response body. Gli esempi servono a documentare l'API e a facilitare test e integrazione.

**REGOLA**: Non generare MAI una specifica OpenAPI senza esempi. Gli esempi sono obbligatori per:
- Ogni `requestBody` (POST, PUT, PATCH)
- Ogni response `2xx` con body
- Le response di errore (`4xx`, `5xx`) nei `components/responses`

**Azioni**:

1. **Per ogni operazione con requestBody**, generare un blocco `example` o `examples`:
   ```yaml
   # Singolo esempio (per operazioni semplici)
   requestBody:
     content:
       application/json:
         schema:
           $ref: '#/components/schemas/DocumentoCreate'
         example:
           tipo: DELIBERA
           titolo: "Delibera di Giunta n. 45/2024"
           descrizione: "Approvazione piano urbanistico zona nord"
           numero-protocollo: "2024/0004521"
           data-protocollo: "2024-03-15T09:00:00Z"
           ufficio-responsabile:
             codice: URB-003

   # Esempi multipli (per operazioni con varianti significative)
   requestBody:
     content:
       application/json:
         schema:
           $ref: '#/components/schemas/DocumentoCreate'
         examples:
           delibera:
             summary: Creazione delibera di giunta
             value:
               tipo: DELIBERA
               titolo: "Delibera di Giunta n. 45/2024"
               descrizione: "Approvazione piano urbanistico zona nord"
               numero-protocollo: "2024/0004521"
           determina:
             summary: Creazione determina dirigenziale
             value:
               tipo: DETERMINA
               titolo: "Determina n. 123/2024 - Affidamento servizio mensa"
               descrizione: "Affidamento diretto servizio mensa scolastica"
   ```

2. **Per ogni response 2xx con body**, generare esempi coerenti con la request:
   ```yaml
   responses:
     '201':
       content:
         application/json:
           schema:
             $ref: '#/components/schemas/Documento'
           example:
             id: "550e8400-e29b-41d4-a716-446655440000"
             tipo: DELIBERA
             titolo: "Delibera di Giunta n. 45/2024"
             descrizione: "Approvazione piano urbanistico zona nord"
             stato: BOZZA
             numero-protocollo: "2024/0004521"
             data-protocollo: "2024-03-15T09:00:00Z"
             data-creazione: "2024-03-15T09:00:12Z"
             utente-creazione: "m.bianchi"
   ```

3. **Per response di collezione (GET lista)**, generare un esempio con almeno 2 item:
   ```yaml
   responses:
     '200':
       content:
         application/json:
           schema:
             $ref: '#/components/schemas/DocumentiCollection'
           example:
             items:
               - id: "550e8400-e29b-41d4-a716-446655440000"
                 tipo: DELIBERA
                 titolo: "Delibera di Giunta n. 45/2024"
                 stato: PUBBLICATO
                 data-creazione: "2024-03-15T09:00:12Z"
               - id: "660f9511-f30c-52e5-b827-557766551111"
                 tipo: DETERMINA
                 titolo: "Determina n. 123/2024"
                 stato: BOZZA
                 data-creazione: "2024-03-14T11:30:00Z"
             count: 42
             limit: 20
             offset: 0
   ```

4. **Per operazioni con stati multipli**, usare `examples` (plurale) con varianti:
   ```yaml
   # Esempio: polling stato elaborazione asincrona
   examples:
     in-corso:
       summary: Elaborazione in corso (45%)
       value:
         id: "550e8400-e29b-41d4-a716-446655440000"
         stato: IN_CORSO
         progresso: 45
         messaggio: "Processamento record 4500/10000"
     completata:
       summary: Elaborazione completata con successo
       value:
         id: "550e8400-e29b-41d4-a716-446655440000"
         stato: COMPLETATA
         progresso: 100
         risultato:
           totale-record: 10000
           record-validi: 9876
     errore:
       summary: Elaborazione fallita
       value:
         id: "550e8400-e29b-41d4-a716-446655440000"
         stato: ERRORE
         progresso: 67
         errore:
           codice: "ERR_TIMEOUT"
           messaggio: "Timeout elaborazione superato"
   ```

**Regole per gli esempi**:
- I valori DEVONO essere realistici (nomi italiani, codici fiscali validi, date plausibili, codici ISTAT reali)
- Gli esempi DEVONO essere coerenti tra loro: il dato inviato in POST deve ritornare nella response
- I codici fiscali devono rispettare il pattern `^[A-Z]{6}[0-9]{2}[A-Z][0-9]{2}[A-Z][0-9]{3}[A-Z]$`
- Le date devono essere in formato ISO 8601 con timezone
- Gli UUID devono essere plausibili (non tutti zeri)
- Per gli errori, includere `detail` specifico e utile
- Se ci sono enum, gli esempi devono coprire almeno 2 valori diversi
- Per le collezioni, mostrare almeno 2 item con dati diversificati

**Output Fase 2.5**: Specifica con esempi completi per ogni operazione.

---

### FASE 3: Arricchimento Semantico con schema-gov-it MCP

**Obiettivo**: Collegare gli schemi a ontologie e vocabolari italiani, segnalando gap e proponendo miglioramenti.

**Azioni per ogni schema**:

1. **Identifica il tipo di entità**
   ```
   Schema: Persona
   → search_concepts(keyword="persona") o inspect_concept(uri=...)
   → Se trovato: x-jsonld-type: 'CPV:Person'
   → Se NON trovato: SEGNALA all'utente, suggerisci tipo più vicino
   ```

2. **Mappa le proprietà con prefissi**
   ```
   Proprietà: nome, cognome, codice-fiscale
   → list_properties(ontologyUri="https://w3id.org/italia/onto/CPV")
   → Per ogni proprietà: cerca corrispondenza
   → Definisci prefissi PRIMA, poi usa notazione compatta
   ```

3. **Aggiungi annotazioni con prefissi compatti**
   ```yaml
   Persona:
     x-jsonld-type: 'CPV:Person'  # Usa prefisso, NON URI completa
     x-jsonld-context:
       # 1. Definisci TUTTI i prefissi necessari
       CPV: 'https://w3id.org/italia/onto/CPV/'
       CLV: 'https://w3id.org/italia/onto/CLV/'
       foaf: 'http://xmlns.com/foaf/0.1/'
       schema: 'http://schema.org/'
       '@vocab': 'https://w3id.org/italia/onto/CPV/'  # namespace dominante
       '@base': 'https://api.example.gov.it/v1/persone/'
       # 2. Mappa proprietà con prefissi compatti
       nome: 'CPV:givenName'        # proprietà nativa CPV (NON foaf)
       cognome: 'CPV:familyName'   # proprietà nativa CPV (NON foaf)
       codice-fiscale: 'CPV:taxCode'
       residenza: 'CLV:hasAddress'
   ```

4. **Verifica vocabolari controllati**
   ```
   Enum: tipo-documento [DELIBERA, DETERMINA, ...]
   → list_vocabularies() per trovare ConceptScheme rilevanti
   → browse_vocabulary(schemeUri=..., keyword="documento")
   → Se trovato: referenzia con URI compatta (prefisso + notation)
   → Se NON trovato: SEGNALA gap, suggerisci codifica locale documentata
   ```

5. **Analisi proattiva dei gap semantici**
   Per ogni proprietà dello schema che NON trova mappatura:
   - Provare sinonimi e termini correlati in italiano e inglese
   - Cercare in ontologie alternative (schema.org, Dublin Core, FOAF, W3C org)
   - Se confermato assente: segnalare nel description dello schema
   - Proporre: (a) mappatura approssimativa con nota, (b) estensione custom

6. **Arricchimento descrizioni**
   Per ogni proprietà mappata, verificare che la description:
   - Sia presente e in italiano
   - Citi il riferimento ontologico (es. "Conforme a CPV:taxCode")
   - Se l'ontologia ha description insufficiente: proporre testo migliorato
   - Se l'ontologia ha description solo in inglese: proporre traduzione

**Strumenti MCP da usare** (schema-gov-it):
- `search_concepts(keyword)`: ricerca fuzzy concetti
- `inspect_concept(uri)`: profilo completo di classe/proprietà
- `list_ontologies()` + `explore_ontology(uri)`: navigazione ontologie
- `list_properties(ontologyUri, propertyType)`: proprietà di un'ontologia
- `list_vocabularies()` + `browse_vocabulary(schemeUri, keyword)`: vocabolari controllati
- `find_relations(sourceUri, targetUri)`: connessioni tra concetti
- `describe_resource(uri)`: descrizione completa di una risorsa

**IMPORTANTE - Efficienza nelle query MCP**:
- Usare SEMPRE `keyword` e `limit` per filtrare
- Non scaricare interi vocabolari: usare `browse_vocabulary` con keyword
- Preferire `search_concepts` con termine specifico a esplorazioni generiche
- Cachare mentalmente i prefissi già identificati per non ripetere query

**Output Fase 3**: Specifica arricchita con annotazioni semantiche complete + report di copertura con gap identificati e suggerimenti.

---

### FASE 3.5: Validazione Semantica via MCP (OBBLIGATORIA)

**Obiettivo**: Verificare che TUTTE le URI ontologiche usate negli schemi esistano realmente e abbiano domain/range coerenti.

> **REGOLA CRITICA**: Non usare MAI una URI ontologica copiata da un template
> senza prima verificarla con schema-gov-it MCP.
> I template in questa skill sono un **PUNTO DI PARTENZA**, non una fonte di verità.
> La fonte di verità sono le ontologie accessibili via MCP.

**Azioni**:

1. **Per OGNI URI in `x-jsonld-type`**:
   ```
   inspect_concept(uri="https://w3id.org/italia/onto/CPV/Person")
   → Verifica che la classe esista e sia del tipo atteso (owl:Class)
   ```

2. **Per OGNI proprietà in `x-jsonld-context`**:
   ```
   get_property_details(propertyUri="https://w3id.org/italia/onto/CPV/givenName")
   → Verifica: esiste? domain compatibile? range corretto?
   ```

3. **Per OGNI vocabolario controllato referenziato**:
   ```
   list_vocabularies() → Verifica che il ConceptScheme esista
   browse_vocabulary(schemeUri=...) → Verifica che i valori enum corrispondano
   ```

4. **Pattern di verifica per ogni mappatura**:
   ```
   Per ogni proprietà da mappare:
   1. search_concepts(keyword="nome proprietà")
   2. Se trovata: get_property_details(propertyUri) per verificare domain/range
   3. Se domain/range compatibili: usare nel context con prefisso
   4. Se domain/range incompatibili: segnalare e cercare alternativa
   5. Se non trovata: segnalare gap, cercare in ontologie alternative
   ```

5. **Produrre report di validazione semantica**:
   ```
   REPORT VALIDAZIONE SEMANTICA
   =============================
   URI verificate OK: N
   URI inesistenti (rimosse/sostituite): N
   URI con domain/range incompatibile: N
   Gap semantici documentati: N
   ```

**Output Fase 3.5**: Report di validazione semantica con conferma che tutte le URI usate esistono e sono coerenti.

---

### FASE 4: Validazione con oas-checker-mcp

**Obiettivo**: Verificare conformità alle linee guida AgID.

**Azioni**:

1. **Validazione automatica**
   ```
   oas-checker-mcp valida openapi.yaml --profilo modi
   ```

2. **Controlla errori critici**
   - ❌ Mancanza di schema Problem per errori
   - ❌ Path non in kebab-case
   - ❌ Security scheme non definito
   - ❌ Mancanza endpoint /status

3. **Controlla warning**
   - ⚠️ Descrizioni incomplete
   - ⚠️ Esempi mancanti
   - ⚠️ Rate limiting non documentato

4. **Analizza suggerimenti**
   - 💡 Pattern di sicurezza raccomandati
   - 💡 Miglioramenti naming
   - 💡 Ottimizzazioni struttura

**Comandi oas-checker-mcp**:
- `valida(file)`: validazione completa
- `verifica_pattern(file, pattern_id)`: test pattern specifico
- `suggerisci_miglioramenti(file)`: consigli
- `confronta_versioni(file1, file2)`: breaking changes

**Output Fase 4**: Report di validazione con score di conformità.

---

### FASE 5: Iterazione e Raffinamento

**Obiettivo**: Correggere problemi e migliorare la qualità.

**Ciclo iterativo**:
1. Correggi errori critici identificati da oas-checker
2. Migliora annotazioni semantiche dove mancanti
3. Aggiungi esempi concreti per ogni schema
4. Rivalidare con oas-checker fino a score 100%
5. Review team + stakeholder

**Checklist finale**:
- [ ] Score oas-checker ≥ 95%
- [ ] Tutti gli schemi principali hanno x-jsonld-type (con prefisso compatto)
- [ ] Tutti i context usano prefissi, nessuna URI espansa nelle mappature
- [ ] `@vocab` e `@base` definiti in ogni context
- [ ] Vocabolari controllati referenziati dove possibile
- [ ] Gap semantici documentati con suggerimenti per ogni proprietà non mappata
- [ ] Descrizioni arricchite dove l'ontologia è carente
- [ ] Report copertura semantica: % proprietà mappate, gap, suggerimenti
- [ ] Esempi request presenti per ogni POST/PUT/PATCH con valori realistici
- [ ] Esempi response presenti per ogni 2xx con body
- [ ] Esempi errore nei components/responses
- [ ] Esempi coerenti tra request e response
- [ ] Collezioni con almeno 2 item diversificati negli esempi
- [ ] Documentazione completa in italiano
- [ ] Test funzionali passati su mock server
- [ ] LOG.md generato con report semantico e istruzioni per ampliare le semantiche

**Output Fase 5**: Specifica production-ready validata.

---

### FASE 6: Generazione LOG.md (OBBLIGATORIA)

**Obiettivo**: Produrre un file `LOG.md` nella stessa directory della specifica OpenAPI generata/modificata. Il LOG documenta il processo, le decisioni semantiche, i gap trovati e soprattutto **come ampliare e modificare le semantiche** in futuro.

**REGOLA**: Ogni volta che si crea o modifica una specifica OpenAPI, DEVE essere generato/aggiornato il file `LOG.md` accanto al file YAML.

**Struttura del LOG.md**:

```markdown
# LOG - [Nome API]

## Metadata
- **File**: `nome-file.yaml`
- **Data generazione/modifica**: YYYY-MM-DD
- **Versione API**: X.Y.Z
- **Azione**: Creazione | Modifica | Arricchimento semantico

## Riepilogo Processo

### Fasi completate
- [ ] FASE 1: Progettazione (API Canvas)
- [ ] FASE 2: Generazione specifica OpenAPI
- [ ] FASE 2.5: Generazione esempi request/response
- [ ] FASE 3: Arricchimento semantico
- [ ] FASE 3.5: Validazione semantica via MCP
- [ ] FASE 4: Validazione oas-checker
- [ ] FASE 5: Iterazione e raffinamento

### Decisioni di design
[Elencare le scelte architetturali fatte, es. pattern sincrono vs asincrono,
schema di autenticazione scelto, struttura delle risorse]

## Report Semantico

### Ontologie utilizzate
| Prefisso | URI | Uso |
|----------|-----|-----|
| CPV | `https://w3id.org/italia/onto/CPV/` | Persone, documenti |
| CLV | `https://w3id.org/italia/onto/CLV/` | Indirizzi, luoghi |
| ... | ... | ... |

### Copertura semantica per schema
| Schema | Proprietà totali | Mappate | Gap | Copertura |
|--------|-----------------|---------|-----|-----------|
| Persona | 10 | 8 | 2 | 80% |
| Indirizzo | 7 | 5 | 2 | 71% |
| ... | ... | ... | ... | ... |

### Mappature verificate via MCP
| Proprietà | Mappatura | Verifica |
|-----------|-----------|----------|
| nome → CPV:givenName | domain: CPV:Person, range: xsd:string | OK |
| cap → ??? | CLV:postCode non esiste | GAP |
| ... | ... | ... |

### Gap semantici identificati
| Proprietà | Schema | Ontologia attesa | Stato | Suggerimento |
|-----------|--------|-----------------|-------|-------------|
| cap | Indirizzo | CLV | ASSENTE | Nessuna alternativa trovata |
| email | Persona | CPV | GAP | Fallback foaf:mbox |
| ... | ... | ... | ... | ... |

### Vocabolari controllati
| Campo enum | Vocabolario AgID | Stato |
|-----------|-----------------|-------|
| tipo-documento | [trovato/non trovato] | [dettaglio] |
| stato-civile | Non disponibile | Gap segnalato |

## Come Ampliare le Semantiche

### Aggiungere un nuovo schema con semantica

1. **Identificare il tipo ontologico**:
   ```
   Usare: search_concepts(keyword="nome entità")
   Poi: inspect_concept(uri="URI trovata")
   ```
   Se trovato → aggiungere `x-jsonld-type`
   Se non trovato → documentare il gap nel LOG

2. **Mappare le proprietà**:
   ```
   Usare: list_properties(ontologyUri="URI ontologia")
   Per ogni proprietà: get_property_details(propertyUri="URI")
   ```
   Verificare domain/range compatibili prima di inserire nel context

3. **Aggiungere il context JSON-LD**:
   - Definire PRIMA i prefissi necessari
   - Impostare `@vocab` sul namespace dominante
   - Mappare con notazione compatta `prefisso:nome`

### Modificare una mappatura esistente

1. **Verificare la mappatura corrente** con MCP:
   ```
   get_property_details(propertyUri="URI attuale")
   ```
2. **Cercare alternative**:
   ```
   search_concepts(keyword="termine alternativo")
   ```
3. **Aggiornare** il context nello schema YAML
4. **Aggiornare** questo LOG con la nuova mappatura e il motivo del cambio

### Aggiungere un vocabolario controllato

1. **Cercare vocabolari esistenti**:
   ```
   list_vocabularies()
   search_in_vocabulary(schemeUri="URI", keyword="termine")
   ```
2. Se trovato: referenziare con URI e codice `skos:notation`
3. Se non trovato: documentare il gap, proporre codifica locale

### Colmare un gap semantico

Per ogni gap elencato sopra, le opzioni sono:
1. **Cercare in ontologie alternative**: schema.org, Dublin Core, FOAF, W3C org
2. **Proporre estensione**: suggerire proprietà al repository
   `https://github.com/italia/daf-ontologie-vocabolari-controllati`
3. **Usare mappatura approssimativa**: con nota esplicita nel description
4. **Creare URI custom**: `https://w3id.org/italia/onto/{Ontologia}/{Nome}`
   (solo come ultima risorsa, documentando la proposta)

### Ontologie e tool MCP di riferimento

| Cosa cerco | Tool MCP da usare |
|-----------|------------------|
| Classe/tipo di un'entità | `search_concepts(keyword)` → `inspect_concept(uri)` |
| Proprietà di un'ontologia | `list_properties(ontologyUri)` |
| Dettaglio proprietà | `get_property_details(propertyUri)` |
| Vocabolario controllato | `list_vocabularies()` → `browse_vocabulary(schemeUri, keyword)` |
| Relazione tra concetti | `find_relations(sourceUri, targetUri)` |
| Lista ontologie disponibili | `list_ontologies()` → `explore_ontology(ontologyUri)` |
| Comuni/province | `list_municipalities(keyword)` / `list_provinces(keyword)` |

## Storico modifiche

| Data | Azione | Dettaglio |
|------|--------|----------|
| YYYY-MM-DD | Creazione | Generazione iniziale specifica |
```

**Azioni per generare il LOG.md**:

1. **Alla creazione di una nuova specifica**: generare `LOG.md` completo con tutte le sezioni
2. **Alla modifica di una specifica esistente**: se `LOG.md` esiste già, aggiornarlo aggiungendo:
   - Una riga allo "Storico modifiche"
   - Aggiornamento delle tabelle di copertura semantica
   - Nuovi gap o gap risolti
3. **Compilare sempre** la sezione "Come Ampliare le Semantiche" con istruzioni operative specifiche per l'API in questione (non generiche)
4. **Personalizzare** gli esempi di comandi MCP con i nomi reali delle entità e proprietà dell'API

**Output Fase 6**: File `LOG.md` nella stessa directory del file OpenAPI.

---

## Workflow di Creazione (Legacy - senza MCP tools)

> **Nota**: Questo workflow è per ambienti senza tool MCP.
> Se hai accesso agli MCP tools, usa il workflow sopra.

Quando l'utente richiede una specifica OpenAPI:

1. **Raccogliere Informazioni**
   - Nome dell'API e dell'ente
   - Scopo e contesto
   - Risorse principali da esporre
   - Pattern di sicurezza richiesto
   - Pattern di interazione (sincrono/asincrono)

2. **Generare Struttura Base**
   - Info block completo
   - Server URLs
   - Security schemes
   - Health check endpoint

3. **Definire Risorse**
   - Path per ogni risorsa
   - Operazioni CRUD appropriate
   - Schemi dati con validazione
   - Esempi concreti

4. **Aggiungere Pattern**
   - Paginazione per collezioni
   - Filtri e ricerca
   - Gestione errori
   - Rate limiting

5. **Validare Conformità**
   - Verificare checklist ModI
   - Testare con validator OpenAPI
   - Documentare esempi d'uso

6. **Documentare**
   - Aggiungere descrizioni esaustive
   - Includere esempi pratici
   - Riferimenti a normativa

## Strumenti di Validazione

Suggerire all'utente di validare con:
- **Spectral** con ruleset AgID: https://github.com/teamdigitale/api-oas-checker
- **Swagger Editor**: https://editor.swagger.io/
- **Redoc**: per generare documentazione

## Riferimenti

- [Linee Guida Interoperabilità Tecnica PA](https://www.agid.gov.it/it/infrastrutture/sistema-pubblico-connettivita/il-nuovo-modello-interoperabilita)
- [Pattern di Interazione](https://docs.italia.it/italia/piano-triennale-ict/lg-modellointeroperabilita-docs/)
- [Pattern di Sicurezza](https://www.agid.gov.it/sites/agid/files/2024-05/linee_guida_tecnologie_e_standard_sicurezza_interoperabilit_api_sistemi_informatici.pdf)
- [RFC 7807 - Problem Details](https://www.rfc-editor.org/rfc/rfc7807)
- [Vocabolari Controllati AgID](https://github.com/italia/daf-ontologie-vocabolari-controllati)

## Registro Correzioni Semantiche

Questa sezione documenta le correzioni apportate alle mappature ontologiche,
i gap semantici noti e serve come "memoria" per evitare di ripetere errori già scoperti.

### Ultima verifica MCP: 2025-02-11

### Errori corretti

| Mappatura errata | Correzione | Motivo |
|---|---|---|
| `foaf:givenName` per nome persona | `CPV:givenName` | Proprietà nativa CPV con domain CPV:Person |
| `foaf:familyName` per cognome persona | `CPV:familyName` | Proprietà nativa CPV con domain CPV:Person |
| `schema:birthDate` per data nascita | `CPV:dateOfBirth` | Proprietà nativa CPV (range: xsd:dateTime) |
| `CPV:hasResidence` per residenza | `CPV:hasResidenceInTime` | `CPV:hasResidence` NON ESISTE; range: CPV:ResidenceInTime |
| `foaf:gender` per sesso | `CPV:hasSex` | ObjectProperty CPV con range CPV:Sex |
| `CLV:streetNumber` per numero civico | `CLV:hasNumber` | ObjectProperty con range CLV:CivicNumbering |
| `CLV:cityName` per nome comune | `rdfs:label` o `skos:prefLabel` | `CLV:cityName` NON ESISTE in CLV |
| `CLV:code` per codice ISTAT | `CLV:hasIdentifier` | ObjectProperty con range CLV:Identifier |
| `dct:title` per nome organizzazione | `COV:legalName` | Proprietà nativa COV con domain COV:Organization |
| `COV:taxCode` per partita IVA | `COV:VATnumber` | `COV:taxCode` è per il codice fiscale organizzazione, non la P.IVA |
| `foaf:Person` come tipo | `CPV:Person` | Usare ontologia nativa italiana per contesto PA |
| `foaf:firstName`/`foaf:lastName` | `CPV:givenName`/`CPV:familyName` | `foaf:firstName` e `foaf:lastName` non sono standard FOAF |

### Gap semantici noti (proprietà senza mappatura)

| Proprietà | Ontologia attesa | Stato | Note |
|---|---|---|---|
| CAP (codice postale) | CLV | ASSENTE | `CLV:postCode` non esiste. Nessuna alternativa trovata. |
| Stato civile | CPV | ASSENTE | `CPV:hasMaritalStatus` non esiste. Nessuna proprietà standard. |
| Email (persona) | CPV | GAP | Nessuna email nativa in CPV. Fallback: `foaf:mbox`. |
| Telefono (persona) | CPV | GAP | Nessuna proprietà telefono nativa in CPV. Fallback: `foaf:phone`. |
| Email (organizzazione) | COV | ASSENTE | `COV:email` non esiste. Alternativa: `SM:emailAddress` (domain: SM:Email). |
| PEC (organizzazione) | COV | ASSENTE | `COV:pec` non esiste. Nessuna proprietà standard per PEC. |

### Proprietà con range complesso (ObjectProperty vs valore semplice)

Alcune proprietà CLV sono ObjectProperty che richiedono un oggetto complesso,
ma nelle API REST si rappresentano spesso come valori semplici:

| Proprietà | Tipo | Range | Nota |
|---|---|---|---|
| `CLV:hasNumber` | ObjectProperty | CLV:CivicNumbering | Nella rappresentazione API si usa il valore testuale |
| `CLV:hasProvince` | ObjectProperty | CLV:Province | Nella rappresentazione API si usa la sigla (es. "RM") |
| `CLV:hasRegion` | ObjectProperty | CLV:Region | Nella rappresentazione API si usa il nome (es. "Lazio") |
| `CLV:hasIdentifier` | ObjectProperty | CLV:Identifier | Nella rappresentazione API si usa il codice ISTAT diretto |
| `CPV:hasSex` | ObjectProperty | CPV:Sex | Nella rappresentazione API si usa un enum (M/F/X) |
| `CPV:hasResidenceInTime` | ObjectProperty | CPV:ResidenceInTime | Pattern n-ario per storico residenze |

## Note Finali

Questa skill garantisce che le specifiche OpenAPI generate siano:
- Conformi alle Linee Guida AgID
- Interoperabili con altre PA
- Sicure e tracciabili
- Ben documentate
- Pronte per la pubblicazione sul Catalogo API
- Compatibili con la Piattaforma Digitale Nazionale Dati (PDND)
- Semanticamente validate (tutte le URI verificate via MCP)
