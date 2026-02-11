# Esempi OpenAPI ModI AgID

Questa cartella contiene esempi progressivi di specifiche OpenAPI conformi alle Linee Guida AgID ModI.

## 📁 File Disponibili

### 1. `template-minimal.yaml` ⭐ START HERE
**Livello**: Principiante  
**Dimensione**: ~200 righe  
**Cosa include**:
- Metadati obbligatori (info, x-api-id, x-summary)
- Security scheme Bearer JWT
- Health check endpoint `/status`
- Schema Problem RFC 7807
- Response templates riutilizzabili
- Header standard (X-Request-ID, rate limiting)

**Quando usare**:
- Punto di partenza per nuove API
- Prototipazione rapida
- Studio della struttura minima ModI

**Prossimi passi**:
1. Copia questo file
2. Aggiungi i tuoi path sotto `/paths`
3. Definisci schemi in `/components/schemas`
4. Valida con oas-checker-mcp

---

### 2. `esempio-completo.yaml`
**Livello**: Intermedio/Avanzato  
**Dimensione**: ~800 righe  
**Cosa include**:
- API CRUD completa per gestione documenti amministrativi
- Paginazione, filtri, ordinamento
- Upload/download allegati multipart
- Ricerca avanzata con POST
- Gestione ottimistica concorrenza (ETag)
- Rate limiting completo
- Tutti i codici risposta standard
- Esempi realistici per ogni schema

**Quando usare**:
- Riferimento per API complesse
- Studio pattern avanzati
- Base per API production-ready

**Evidenzia**:
- Pattern per upload file
- Gestione collezioni con paginazione
- Validazione dati italiani (CF, P.IVA, protocollo)
- Organizzazione multi-tag

---

### 3. `esempio-semantico.yaml`
**Livello**: Avanzato  
**Dimensione**: ~350 righe  
**Cosa include**:
- **Annotazioni JSON-LD complete** (`x-jsonld-type`, `x-jsonld-context`)
- Mappature a ontologie AgID (CPV, CLV, FOAF)
- Vocabolari controllati referenziati
- Support content negotiation (application/ld+json)
- Schemi interoperabili con ANPR, SPID, CIE

**Quando usare**:
- API che devono integrarsi con Knowledge Graph
- Massima interoperabilità semantica
- Conformità DCAT-AP_IT
- Open Data e Linked Data

**Evidenzia**:
- Schema `Persona` con mappatura CPV completa (proprietà native CPV, non FOAF)
- `Indirizzo` con ontologia CLV (gap documentati: CAP non mappabile)
- `Comune` con note su proprietà inesistenti (CLV:cityName, CLV:code)
- `Coordinate` con WGS84
- Context JSON-LD per ogni entità
- Gap semantici esplicitamente documentati nelle description

**Tool consigliato**: `dati-semantic-mcp` per arricchimento

> **IMPORTANTE**: Le mappature ontologiche in questo file sono state verificate
> il 2025-02-11 tramite MCP schema-gov-it. Prima di usarle in produzione,
> ri-verificare sempre con `inspect_concept()` o `get_property_details()`
> che le URI siano ancora valide. Vedi SKILL.md sezione "FASE 3.5" e
> "Registro Correzioni Semantiche" per dettagli.

---

### 4. `esempio-asincrono.yaml`
**Livello**: Avanzato  
**Dimensione**: ~450 righe  
**Cosa include**:
- **Pattern NONBLOCK_REST_PULL** (operazioni asincrone)
- POST → 202 Accepted con Location
- GET stato con polling
- Gestione progresso (0-100%)
- Download risultati multi-formato
- Annullamento elaborazioni
- Callback opzionali

**Quando usare**:
- Operazioni long-running (> 10 secondi)
- Elaborazioni batch
- Integrazione sistemi esterni lenti
- Report complessi

**Evidenzia**:
- Workflow completo client/server
- Stati elaborazione (IN_CODA, IN_CORSO, COMPLETATA, ERRORE)
- Retry-After header per polling ottimale
- Retention policy risultati

**Pattern ModI**: NONBLOCK_REST_PULL

---

## 🎯 Percorso di Apprendimento

### Livello 1: Fondamenti
1. Leggi `template-minimal.yaml`
2. Copia e personalizza per il tuo caso d'uso
3. Valida con oas-checker-mcp
4. Obiettivo: score ≥ 90%

### Livello 2: API Complete
1. Studia `esempio-completo.yaml`
2. Implementa CRUD per le tue risorse
3. Aggiungi paginazione e filtri
4. Testa con mock server
5. Obiettivo: API production-ready

### Livello 3: Arricchimento Semantico
1. Analizza `esempio-semantico.yaml`
2. Identifica ontologie rilevanti con dati-semantic-mcp
3. Aggiungi x-jsonld-* ai tuoi schemi
4. **Valida OGNI URI** con `inspect_concept()` o `get_property_details()` via MCP (FASE 3.5)
5. Documenta gap semantici nelle description
6. Obiettivo: interoperabilità semantica con URI verificate

### Livello 4: Pattern Avanzati
1. Comprendi `esempio-asincrono.yaml`
2. Identifica operazioni long-running nella tua API
3. Implementa pattern NONBLOCK_REST_PULL
4. Gestisci code e retry
5. Obiettivo: API scalabile e resiliente

---

## 🔧 Come Usare gli Esempi

### Visualizzazione
**Swagger Editor** (online):
```
1. Apri https://editor.swagger.io/
2. Incolla contenuto YAML
3. Esplora documentazione interattiva
```

**Redoc** (documentazione statica):
```bash
npx @redocly/cli preview-docs esempio-completo.yaml
```

### Validazione
**Con oas-checker-mcp** (se disponibile):
```
"Valida esempio-completo.yaml con oas-checker-mcp profilo ModI"
```

**Con Spectral CLI**:
```bash
npm install -g @stoplight/spectral-cli
spectral lint esempio-completo.yaml
```

### Testing
**Mock Server con Prism**:
```bash
docker run -p 8080:4010 stoplight/prism:4 \
  mock -h 0.0.0.0 esempio-completo.yaml
```

**Test con cURL**:
```bash
# Health check
curl http://localhost:8080/status

# Con autenticazione
curl -H "Authorization: Bearer fake-token" \
  http://localhost:8080/documenti?limit=10
```

### Generazione Client
**Con OpenAPI Generator**:
```bash
# Client JavaScript
openapi-generator-cli generate \
  -i esempio-completo.yaml \
  -g javascript \
  -o ./client-js

# Client Python
openapi-generator-cli generate \
  -i esempio-completo.yaml \
  -g python \
  -o ./client-py
```

---

## 📊 Confronto Esempi

| Caratteristica | Minimal | Completo | Semantico | Asincrono |
|----------------|---------|----------|-----------|-----------|
| **Righe YAML** | ~200 | ~800 | ~350 | ~450 |
| **Complessità** | ⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Endpoints** | 1 | 8+ | 2 | 4 |
| **CRUD** | ❌ | ✅ | ❌ | ❌ |
| **Paginazione** | ❌ | ✅ | ❌ | ❌ |
| **Upload File** | ❌ | ✅ | ❌ | ❌ |
| **JSON-LD** | ❌ | ❌ | ✅ | ❌ |
| **Ontologie** | ❌ | ❌ | ✅ | ❌ |
| **Operazioni Async** | ❌ | ❌ | ❌ | ✅ |
| **Pattern ModI** | Base | BLOCK_REST | BLOCK_REST + Semantico | NONBLOCK_REST_PULL |
| **Score OAS-Checker** | 85-90% | 95%+ | 95%+ | 95%+ |

---

## 🎨 Customizzazione

### Cambio Dominio
Per adattare gli esempi al tuo dominio:

1. **Info Block**:
   ```yaml
   info:
     title: API [Tuo Servizio]
     x-api-id: [tuo-ente]-[servizio]-api
   ```

2. **Servers**:
   ```yaml
   servers:
     - url: https://api.[tuo-dominio].gov.it/[servizio]/v1
   ```

3. **Schemi**:
   - Rinomina schemi secondo il tuo modello dati
   - Mantieni validazione pattern (CF, P.IVA, ecc.)
   - Conserva annotazioni semantiche se rilevanti

4. **Sicurezza**:
   - Conferma Bearer JWT o adatta a OAuth2
   - Verifica integrazione con PDND se necessario

### Combinazione Pattern
Puoi combinare elementi da diversi esempi:

```yaml
# Da esempio-completo.yaml: CRUD + paginazione
# Da esempio-semantico.yaml: annotazioni JSON-LD
# Da esempio-asincrono.yaml: pattern per report long-running

# Risultato: API completa con semantica e operazioni async
```

---

## ✅ Checklist Pre-Produzione

Prima di usare in produzione:

- [ ] Score oas-checker-mcp ≥ 95%
- [ ] Tutti gli schemi hanno esempi
- [ ] Descrizioni in italiano complete
- [ ] Security scheme configurato correttamente
- [ ] Rate limiting definito
- [ ] Health check funzionante
- [ ] **Tutte le URI ontologiche verificate via MCP** (FASE 3.5)
- [ ] Gap semantici documentati nelle description
- [ ] Mock server testato
- [ ] Client generato e validato
- [ ] Documentazione pubblicata
- [ ] Onboarding primi utenti completato

---

## 📚 Risorse Aggiuntive

### Documentazione ModI
- [Linee Guida AgID](https://www.agid.gov.it/it/infrastrutture/sistema-pubblico-connettivita/il-nuovo-modello-interoperabilita)
- [Pattern di Interazione](https://docs.italia.it/italia/piano-triennale-ict/lg-modellointeroperabilita-docs/)
- [Ontologie CPV](https://w3id.org/italia/onto/CPV/)

### Tool e Validator
- [API OAS Checker](https://github.com/teamdigitale/api-oas-checker)
- [Swagger Editor](https://editor.swagger.io/)
- [Redocly CLI](https://redocly.com/docs/cli/)

### Community
- [Forum Developers Italia](https://forum.italia.it/)
- [Slack Developers Italia](https://slack.developers.italia.it/)

---

## 🤝 Contributi

Hai creato un esempio interessante? Condividilo!

1. Assicurati che sia conforme ModI (score ≥ 95%)
2. Aggiungi commenti esplicativi
3. Include esempi realistici
4. Documenta il caso d'uso

---

**Versione Examples**: 1.0  
**Ultimo aggiornamento**: Febbraio 2024  
**Conformità**: ModI AgID - Linee Guida v1.2
