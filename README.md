# Skill: Generazione Specifiche OpenAPI Conformi alle Linee Guida AgID

## Panoramica

Questa skill fornisce una guida completa per la creazione di specifiche OpenAPI 3.0+ conformi alle **Linee Guida sull'Interoperabilità Tecnica delle Pubbliche Amministrazioni Italiane** (ModI - Modello di Interoperabilità AgID).

## Cosa Include

### 📚 Documentazione Completa
- **SKILL.md**: Guida dettagliata con tutte le regole e best practices
- Pattern di interazione REST (bloccanti e non bloccanti)
- Pattern di sicurezza (Bearer JWT, OAuth2)
- Raccomandazioni di implementazione
- Template riutilizzabili

### 🎯 Esempi Pratici
- **esempio-completo.yaml**: Specifica completa di un'API per gestione documenti amministrativi
- Tutti i pattern ModI implementati
- Gestione errori RFC 7807
- Paginazione, filtri, ricerca
- Upload/download allegati

## Caratteristiche Principali

### ✅ Conformità Normativa
- Linee Guida AgID sull'interoperabilità tecnica
- Documenti operativi Pattern di Interazione
- Documenti operativi Pattern di Sicurezza  
- RFC 7807 per gestione errori (Problem Details)
- Standard OpenAPI 3.0.3+

### 🔧 Integrazione MCP Tools
- **API Design Canvas**: progettazione collaborativa prima del codice
- **oas-checker-mcp**: validazione automatica conformità ModI
- **dati-semantic-mcp**: arricchimento con ontologie italiane (CPV, DCAT-AP_IT, CLV, COV)
- **Workflow completo**: design → genera → arricchisci → valida → itera

### 🧬 Arricchimento Semantico
- Annotazioni JSON-LD con `x-jsonld-type` e `x-jsonld-context`
- Mappatura automatica a ontologie AgID
- Vocabolari controllati referenziati
- Interoperabilità semantica con linked data
- Compatibilità ANPR, SPID, CIE, DCAT-AP_IT

### 🔒 Sicurezza
- Autenticazione Bearer JWT (pattern ID_AUTH_REST_01)
- OAuth2 con Client Credentials flow
- Rate limiting con header standard
- Request tracing con X-Request-ID
- Logging conforme alle linee guida

### 📊 Best Practices
- Naming convention (kebab-case)
- Paginazione standard con offset/limit
- Filtri e ricerca avanzata
- Gestione ottimistica della concorrenza (ETag)
- Health check endpoint obbligatorio
- Documentazione con esempi concreti

### 🌐 Interoperabilità
- Vocabolari controllati AGID
- Codici fiscali e partite IVA con validazione
- Codici IPA per identificazione enti
- Date in formato ISO 8601
- Content negotiation

## Quando Usare Questa Skill

Utilizza questa skill quando devi:
- ✅ Creare API REST per Pubbliche Amministrazioni italiane
- ✅ Documentare servizi digitali per il Catalogo API PDND
- ✅ Garantire conformità alle linee guida AgID
- ✅ Implementare pattern di interoperabilità standard
- ✅ Preparare API per integrazione con altre PA
- ✅ Validare specifiche OpenAPI esistenti

## Come Usare

### Workflow Standard (con MCP Tools) ⭐ RACCOMANDATO

#### 1. Progettazione Iniziale
```
"Voglio creare un'API ModI per [gestione pratiche edilizie]. 
Guidami nella compilazione del canvas API design."
```

Claude ti aiuterà a:
- Identificare stakeholder e permessi
- Definire casi d'uso principali  
- Mappare risorse REST
- Specificare requisiti sicurezza/performance

#### 2. Generazione Automatica
```
"Basandoti sul canvas, genera la specifica OpenAPI conforme ModI 
con pattern Bearer JWT e paginazione standard."
```

#### 3. Arricchimento Semantico
```
"Arricchisci gli schemi con ontologie usando dati-semantic-mcp:
- Cerca mappature per Persona, Organizzazione, Indirizzo
- Aggiungi x-jsonld-type e x-jsonld-context
- Trova vocabolari controllati per gli enum"
```

#### 4. Validazione e Iterazione
```
"Valida la specifica con oas-checker-mcp profilo ModI.
Correggi tutti gli errori critici identificati."
```

Claude eseguirà cicli di validazione → fix → ri-validazione fino a score ≥95%.

#### 5. Export Finale
```
"La specifica è validata. Genera anche:
- Documentazione HTML con Redoc
- Postman collection per test
- README con esempi cURL"
```

---

### Workflow Rapido (senza MCP)

Se non hai accesso agli MCP tools:
- **Informazioni ente**: nome, codice IPA, contatti
- **Scopo API**: funzionalità principali, casi d'uso
- **Risorse**: entità da esporre (documenti, pratiche, servizi...)
- **Pattern**: sincrono/asincrono, sicurezza richiesta
- **Vincoli**: rate limiting, dimensioni file, formati supportati

### 2. Generazione

Richiedi a Claude:
```
"Crea una specifica OpenAPI 3.0 conforme alle linee guida AgID ModI 
per un'API di [DESCRIZIONE]. L'API deve gestire [RISORSE] e supportare 
[FUNZIONALITÀ]. Pattern di sicurezza: Bearer JWT."
```

### 3. Validazione

Valida la specifica generata con:
- **API OAS Checker** (AgID): https://github.com/teamdigitale/api-oas-checker
- **Swagger Editor**: https://editor.swagger.io/
- **Spectral** con ruleset personalizzato

### 4. Pubblicazione

1. Testa l'API in ambiente di test
2. Prepara la documentazione utente
3. Registra l'e-service sul Catalogo PDND
4. Definisci SLA e accordi di interoperabilità
5. Pubblica in produzione

## Struttura della Skill

```
openapi-agid-skill/
├── SKILL.md                      # Guida completa con workflow MCP
├── README.md                     # Questo file
├── QUICK_REFERENCE.md            # Guida rapida pattern comuni
├── API_DESIGN_CANVAS.md          # Template canvas progettazione
├── MCP_INTEGRATION_GUIDE.md     # Guida uso tool MCP
└── examples/
    └── esempio-completo.yaml     # Esempio con annotazioni semantiche
```

## Novità Versione 2.0

### 🎨 Canvas API Design
Template strutturato per progettare l'API prima di scrivere codice:
- Identificazione stakeholder e casi d'uso
- Mappatura risorse REST
- Definizione modello dati
- Requisiti sicurezza e performance
- Checklist completamento

### 🤖 Integrazione MCP Tools

**oas-checker-mcp** - Validazione Automatica
```
✅ Verifica conformità ModI AgID
✅ Controlla pattern di sicurezza  
✅ Valida naming convention
✅ Identifica breaking changes
✅ Suggerisce miglioramenti
```

**dati-semantic-mcp** - Arricchimento Semantico
```
✅ Cerca ontologie italiane (CPV, DCAT, CLV, COV)
✅ Genera context JSON-LD automaticamente
✅ Mappa proprietà a URI ontologie
✅ Suggerisce vocabolari controllati
✅ Valida mappature semantiche
```

### 📋 Workflow Completo 5 Fasi

1. **PROGETTAZIONE** → Canvas API con stakeholder
2. **GENERAZIONE** → Specifica OpenAPI da canvas
3. **ARRICCHIMENTO** → Ontologie con dati-semantic-mcp
4. **VALIDAZIONE** → Conformità con oas-checker-mcp
5. **ITERAZIONE** → Fix problemi fino a 95+ score

Vedi `MCP_INTEGRATION_GUIDE.md` per dettagli completi.

## Elementi Chiave

### Metadati Obbligatori
```yaml
info:
  title: Nome API
  version: 1.0.0
  description: Descrizione dettagliata
  contact:
    name: Ufficio Responsabile
    email: api@ente.gov.it
  license:
    name: CC BY 4.0
  x-api-id: identificativo-univoco
  x-summary: Descrizione breve
```

### Naming Convention
- **Path**: kebab-case (`/documenti-amministrativi`)
- **Query params**: kebab-case (`data-da`, `numero-protocollo`)
- **Header custom**: Hyphenated-Pascal-Case (`X-Request-ID`)
- **Properties**: kebab-case in JSON (`data_creazione` → `data-creazione`)

### Gestione Errori (RFC 7807)
```yaml
responses:
  '400':
    content:
      application/problem+json:
        schema:
          type: object
          required: [status, title]
          properties:
            type: {type: string, format: uri}
            status: {type: integer}
            title: {type: string}
            detail: {type: string}
            instance: {type: string, format: uri}
```

### Pattern di Sicurezza
```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []
```

### Health Check Obbligatorio
```yaml
paths:
  /status:
    get:
      summary: Verifica stato servizio
      responses:
        '200':
          description: Servizio operativo
```

## Checklist Conformità

- [ ] OpenAPI 3.0.3 o superiore
- [ ] Tutti gli endpoint usano HTTPS
- [ ] Path in kebab-case
- [ ] Metodi HTTP semanticamente corretti
- [ ] Schema Problem (RFC 7807) per errori 4xx/5xx
- [ ] Endpoint /status presente
- [ ] Security scheme definito
- [ ] Rate limiting documentato
- [ ] X-Request-ID per tracciamento
- [ ] Paginazione per collezioni (limit/offset)
- [ ] Esempi per ogni schema
- [ ] Descrizioni in italiano
- [ ] Validazione pattern per codici fiscali/P.IVA
- [ ] Date in formato ISO 8601
- [ ] `x-api-id` univoco presente
- [ ] Contact info completo

## Pattern Supportati

### Pattern di Interazione
- ✅ **BLOCK_REST**: operazioni sincrone (<10s)
- ✅ **NONBLOCK_REST_PULL**: operazioni asincrone con polling
- ⚠️ **NONBLOCK_REST_PUSH**: callback (documentato ma meno comune)

### Pattern di Sicurezza
- ✅ **ID_AUTH_REST_01**: Bearer JWT
- ✅ **ID_AUTH_REST_02**: OAuth2 Client Credentials
- ✅ Rate limiting con header standard
- ✅ Request tracing

## Validazione e Testing

### Strumenti Raccomandati

1. **API OAS Checker** (ufficiale AgID)
   ```bash
   npm install -g @stoplight/spectral-cli
   spectral lint openapi.yaml --ruleset modi-ruleset.yaml
   ```

2. **Swagger Editor**
   - Online: https://editor.swagger.io/
   - Validazione sintattica e semantica

3. **Redocly**
   ```bash
   npm install -g @redocly/cli
   redocly lint openapi.yaml
   ```

### Test Funzionali

1. Genera un server mock:
   ```bash
   docker run -p 8080:4010 stoplight/prism:4 \
     mock -h 0.0.0.0 openapi.yaml
   ```

2. Testa gli endpoint:
   ```bash
   curl -H "Authorization: Bearer token" \
     http://localhost:8080/status
   ```

## Riferimenti Normativi

### Linee Guida AgID
- [Linee Guida Interoperabilità Tecnica](https://www.agid.gov.it/it/infrastrutture/sistema-pubblico-connettivita/il-nuovo-modello-interoperabilita)
- [Pattern di Interazione (PDF)](https://www.agid.gov.it/sites/agid/files/2024-07/Linee_guida_interoperabilit%C3%A0PA_All1_Pattern_interazione.pdf)
- [Pattern di Sicurezza (PDF)](https://www.agid.gov.it/sites/agid/files/2024-05/linee_guida_tecnologie_e_standard_sicurezza_interoperabilit_api_sistemi_informatici.pdf)
- [Raccomandazioni di Implementazione (PDF)](https://www.agid.gov.it/sites/default/files/repository_files/04_raccomandazioni_di_implementazione.pdf)

### Standard Internazionali
- [OpenAPI Specification 3.0](https://spec.openapis.org/oas/v3.0.3)
- [RFC 7807 - Problem Details for HTTP APIs](https://www.rfc-editor.org/rfc/rfc7807)
- [RFC 6749 - OAuth 2.0](https://www.rfc-editor.org/rfc/rfc6749)
- [RFC 7519 - JSON Web Token](https://www.rfc-editor.org/rfc/rfc7519)

### Vocabolari e Ontologie
- [Vocabolari Controllati AgID](https://github.com/italia/daf-ontologie-vocabolari-controllati)
- [DCAT-AP_IT](https://www.dati.gov.it/content/dcat-ap-it-v10-profilo-italiano-dcat-ap-0)

## Domande Frequenti

### Q: Devo sempre usare HTTPS?
**A**: Sì, è obbligatorio per tutte le API pubbliche delle PA. TLS 1.2 o superiore.

### Q: Posso usare versioning nell'URL?
**A**: Sì, è il metodo raccomandato (`/v1/risorse`, `/v2/risorse`).

### Q: Come gestisco file di grandi dimensioni?
**A**: Usa multipart/form-data con limite documentato (es. 50MB). Considera upload multipart per file >10MB.

### Q: È obbligatorio OAuth2?
**A**: No, Bearer JWT (pattern ID_AUTH_REST_01) è sufficiente per molti casi d'uso.

### Q: Devo supportare sia JSON che XML?
**A**: JSON è sufficiente. XML è opzionale, usa content negotiation se necessario.

### Q: Come documento operazioni asincrone?
**A**: Usa pattern NONBLOCK_REST_PULL: POST ritorna 202 con Location header, GET su Location per status.

### Q: Rate limiting è obbligatorio?
**A**: Non obbligatorio ma fortemente raccomandato. Documenta con header X-RateLimit-*.

### Q: Posso usare GraphQL invece di REST?
**A**: Le linee guida AgID sono specifiche per REST. GraphQL non è attualmente coperto dal ModI.

## Contributi e Supporto

### Segnalare Problemi
Per problemi con le linee guida AgID:
- GitHub AgID: https://github.com/AgID
- Forum Developers Italia: https://forum.italia.it/

### Aggiornamenti
Questa skill viene aggiornata in base a:
- Nuove determinazioni AgID
- Feedback dalla community
- Evoluzioni dello standard OpenAPI

### Licenza
Questa skill è rilasciata con licenza MIT.
Le Linee Guida AgID sono pubblicate dall'Agenzia per l'Italia Digitale.

## Esempi di Prompt

### Creazione Completa con MCP
```
Crea un'API ModI per gestione documenti amministrativi. Workflow completo:

1. Compila canvas API design con me (stakeholder: cittadini + PA)
2. Genera specifica OpenAPI conforme
3. Arricchisci con ontologie CPV usando dati-semantic-mcp
4. Valida con oas-checker-mcp
5. Itera fino a score 95+

Iniziamo con il canvas.
```

### Solo Arricchimento Semantico
```
Ho questa specifica OpenAPI [allegato]. Arricchiscila semanticamente:

Per ogni schema principale:
1. Cerca ontologia appropriata con dati-semantic-mcp
2. Aggiungi x-jsonld-type e x-jsonld-context
3. Mappa proprietà a URI ontologie
4. Trova vocabolari controllati per enum
```

### Migrazione API Esistente
```
Ho un'API REST esistente [allegato spec]. Rendila conforme ModI:

1. Valida con oas-checker-mcp per identificare problemi
2. Refactoring verso pattern ModI (errori RFC 7807, paginazione, ecc)
3. Aggiungi arricchimento semantico
4. Ri-valida fino a conformità 95+
```

### Solo Validazione
```
Valida questa specifica OpenAPI con oas-checker-mcp.
Per ogni errore: spiega + mostra fix + applica.
Continua fino a score ≥ 95.
```

---

## File Generati

Dopo il workflow completo avrai:
- ✅ `API_DESIGN_CANVAS_[nome].md` - Documentazione design
- ✅ `openapi-[nome]-v1.0.0.yaml` - Specifica conforme e arricchita
- ✅ `oas-validation-report.json` - Report validazione
- ✅ `README_API.md` - Documentazione per sviluppatori
- ✅ `postman-collection.json` - Collection per testing

---

**Versione Skill**: 1.0.0  
**Ultima Verifica Conformità**: Febbraio 2024  
**Basata su**: Linee Guida AgID Determinazione 547/2021 e successivi aggiornamenti
