# API Design Canvas - Template

Usa questo template per progettare l'API prima di scrivere la specifica OpenAPI.

---

## 🎯 1. OBIETTIVO E CONTESTO

### Nome API
`API [Nome Servizio]`

### Scopo
_Descrivi in 2-3 frasi cosa fa questa API e perché esiste_

**Esempio**: "API per la gestione delle pratiche edilizie che permette a cittadini e professionisti di presentare richieste di permessi, consultare lo stato delle pratiche e scaricare documentazione."

### Problemi che risolve
- [ ] Problema 1: _es. "Eliminare code agli sportelli"_
- [ ] Problema 2: _es. "Ridurre tempi di risposta"_
- [ ] Problema 3: _es. "Centralizzare informazioni sparse"_

### Metriche di successo
- KPI 1: _es. "Riduzione 50% accessi fisici"_
- KPI 2: _es. "Tempo medio risposta < 5 giorni"_
- KPI 3: _es. "Soddisfazione utenti > 80%"_

---

## 👥 2. STAKEHOLDER

### Chi usa l'API?

#### Stakeholder Primari
- [ ] **Cittadini**
  - Autenticati: SPID/CIE
  - Non autenticati: consultazione pubblica
  - Volumi stimati: _N richieste/giorno_
  
- [ ] **Imprese/Professionisti**
  - Autenticati: SPID/CIE/eIDAS
  - Requisiti: _es. iscrizione albo_
  - Volumi stimati: _N richieste/giorno_

- [ ] **Altre PA**
  - Enti: _specificare quali_
  - Scopo integrazione: _es. verifiche automatiche_
  - Pattern: machine-to-machine

#### Stakeholder Secondari
- [ ] Sistema interno PA (backend)
- [ ] Operatori back-office
- [ ] Fornitori esterni (se presenti)

### Permessi e Autorizzazioni

| Stakeholder | Operazioni Permesse | Note |
|-------------|-------------------|------|
| Cittadino | Lettura proprie pratiche, creazione | Solo dati personali |
| Professionista | Lettura/scrittura pratiche clienti | Delega richiesta |
| PA interna | Tutte le operazioni | Admin |
| PA esterna | Lettura per verifiche | Solo dati pubblici |

---

## 📋 3. CASI D'USO PRINCIPALI

### Caso d'uso 1: [Titolo]
**Attore**: _chi_  
**Obiettivo**: _cosa vuole ottenere_  
**Precondizioni**: _stato iniziale richiesto_  
**Flusso principale**:
1. L'attore...
2. Il sistema...
3. ...

**Postcondizioni**: _stato finale_  
**Eccezioni**: _cosa può andare storto_

---

### Caso d'uso 2: [Titolo]
[Ripeti struttura sopra]

---

### Caso d'uso 3: [Titolo]
[Ripeti struttura sopra]

---

## 🗂️ 4. RISORSE E OPERAZIONI REST

### Risorsa 1: [Nome Risorsa] (es: Pratiche)

**Descrizione**: _Cos'è questa risorsa_

**Path base**: `/pratiche`

**Operazioni**:

#### GET /pratiche
- **Scopo**: Elenco pratiche con filtri
- **Chi può**: Cittadini (proprie), PA (tutte)
- **Query params**:
  - `stato`: filtro per stato
  - `tipo`: filtro per tipologia
  - `data-da`, `data-a`: range temporale
  - `limit`, `offset`: paginazione
- **Response 200**: Lista paginata
- **Tempo medio**: < 500ms

#### POST /pratiche
- **Scopo**: Crea nuova pratica
- **Chi può**: Cittadini, Professionisti
- **Request body**: Dati pratica
- **Response 201**: Pratica creata con Location header
- **Tempo medio**: 1-2s

#### GET /pratiche/{id}
- **Scopo**: Dettaglio singola pratica
- **Chi può**: Proprietario, PA autorizzate
- **Path param**: `id` (UUID)
- **Response 200**: Dettagli completi
- **Response 404**: Pratica non trovata

#### PUT /pratiche/{id}
- **Scopo**: Aggiorna pratica esistente
- **Chi può**: Proprietario (se bozza), PA (sempre)
- **Request body**: Dati aggiornati
- **Header**: `If-Match` con ETag
- **Response 200**: Pratica aggiornata
- **Response 409**: Conflitto ETag

#### DELETE /pratiche/{id}
- **Scopo**: Elimina pratica
- **Chi può**: Proprietario (se bozza)
- **Response 204**: Eliminata
- **Response 403**: Non autorizzato

---

### Risorsa 2: Allegati
**Path**: `/pratiche/{id-pratica}/allegati`

#### GET /pratiche/{id-pratica}/allegati
- Elenco allegati della pratica

#### POST /pratiche/{id-pratica}/allegati
- Upload nuovo allegato
- Content-Type: `multipart/form-data`
- Max size: 50MB
- Formati: PDF, P7M, XML

#### GET /pratiche/{id-pratica}/allegati/{id-allegato}
- Download allegato specifico
- Content-Type: in base al file

---

### Risorsa 3: [Altra risorsa se necessaria]

---

## 📊 5. MODELLO DATI

### Entità Principali

#### Pratica
```yaml
Pratica:
  - id: UUID (generato)
  - tipo: enum [PERMESSO_COSTRUIRE, DIA, SCIA, ...]
  - stato: enum [BOZZA, PRESENTATA, IN_ISTRUTTORIA, APPROVATA, RESPINTA]
  - numero-protocollo: string (auto)
  - data-presentazione: date-time
  - richiedente: Persona
  - indirizzo-intervento: Indirizzo
  - descrizione-intervento: string
  - documentazione: array[Allegato]
  - note-istruttoria: string
  - responsabile-procedimento: Persona
  - data-creazione: date-time
  - data-ultima-modifica: date-time
```

#### Persona
```yaml
Persona:
  - codice-fiscale: string (pattern CF)
  - nome: string
  - cognome: string
  - data-nascita: date
  - luogo-nascita: Comune
  - residenza: Indirizzo
  - email: email
  - telefono: string
  - pec: email (opzionale)
```

#### Indirizzo
```yaml
Indirizzo:
  - via: string
  - numero-civico: string
  - cap: string (5 digit)
  - comune: string
  - provincia: string (2 char)
  - regione: string
  - coordinate: Coordinate (opzionale)
```

#### Allegato
```yaml
Allegato:
  - id: UUID
  - nome-file: string
  - tipo-documento: enum [PLANIMETRIA, RELAZIONE_TECNICA, ...]
  - mime-type: string
  - dimensione: integer (bytes)
  - checksum-sha256: string
  - data-upload: date-time
  - utente-upload: string
```

### Relazioni
- Pratica 1:N Allegati
- Pratica 1:1 Richiedente (Persona)
- Pratica 1:1 Indirizzo Intervento

---

## 🔍 6. VALIDAZIONI E VINCOLI

### Validazioni Input

| Campo | Vincoli | Messaggio Errore |
|-------|---------|------------------|
| codice-fiscale | Pattern CF italiano | "Codice fiscale non valido" |
| email | Format email + dominio | "Email non valida" |
| cap | 5 cifre numeriche | "CAP deve essere 5 cifre" |
| tipo-pratica | Enum predefinito | "Tipo pratica non riconosciuto" |
| allegato | Max 50MB, PDF/P7M | "File troppo grande o formato non supportato" |

### Business Rules
- Una pratica in stato APPROVATA non può essere modificata
- Gli allegati obbligatori dipendono dal tipo di pratica
- La provincia deve essere coerente con il comune
- Il responsabile procedimento deve essere un dipendente abilitato

---

## 🔒 7. SICUREZZA

### Autenticazione
- [ ] **Pattern**: Bearer JWT (ID_AUTH_REST_01)
- [ ] **Provider**: SPID/CIE per cittadini, PDND per PA
- [ ] **Token lifetime**: 1 ora
- [ ] **Refresh**: supportato

### Autorizzazione
- [ ] **Modello**: RBAC (Role-Based Access Control)
- [ ] **Ruoli**:
  - `CITTADINO`: CRUD proprie pratiche
  - `PROFESSIONISTA`: CRUD pratiche clienti
  - `ISTRUTTORE`: lettura + modifica stato
  - `DIRIGENTE`: tutte le operazioni
  - `SISTEMA_PA`: solo lettura per verifiche

### Dati Sensibili
- [ ] Codice fiscale: log anonimizzato
- [ ] Indirizzi: accessibili solo ad autorizzati
- [ ] Note istruttoria: visibili solo a PA interna
- [ ] Allegati: controllo virus obbligatorio

### Rate Limiting
- [ ] **Limite**: 1000 richieste/ora per utente
- [ ] **Limite burst**: 100 richieste/minuto
- [ ] **Response**: 429 con Retry-After header

---

## ⚡ 8. REQUISITI NON FUNZIONALI

### Performance
- [ ] **Tempo risposta medio**: < 500ms (95° percentile)
- [ ] **Throughput**: 100 richieste/secondo
- [ ] **Operazioni asincrone**: processamento > 10 secondi

### Disponibilità
- [ ] **SLA**: 99.9% (max 8.7 ore downtime/anno)
- [ ] **Manutenzione**: finestre programmate fuori orario

### Scalabilità
- [ ] **Architettura**: stateless (horizontal scaling)
- [ ] **Cache**: Redis per dati frequenti
- [ ] **Database**: cluster PostgreSQL

### Volumi Stimati
- [ ] **Pratiche totali**: ~50.000
- [ ] **Nuove pratiche**: ~200/giorno
- [ ] **Richieste API**: ~10.000/giorno
- [ ] **Storage allegati**: ~500GB

---

## 🔄 9. INTEGRATIONS

### Sistemi Integrati

#### Sistema Protocollo
- **Tipo**: API REST interna
- **Scopo**: Generare numero protocollo automatico
- **Pattern**: sincrono
- **Endpoint**: `POST /protocolli`

#### Sistema Pagamenti
- **Tipo**: PagoPA
- **Scopo**: Pagamento oneri/diritti
- **Pattern**: asincrono con callback

#### Catasto (Agenzia Entrate)
- **Tipo**: API REST esterna
- **Scopo**: Verifica dati catastali
- **Pattern**: sincrono
- **SLA**: risposta entro 5 secondi

#### ANPR (Anagrafe Nazionale)
- **Tipo**: PDND e-service
- **Scopo**: Verifica residenza cittadino
- **Pattern**: sincrono

---

## 📈 10. MONITORAGGIO E LOGGING

### Metriche da Tracciare
- [ ] Latenza per endpoint (p50, p95, p99)
- [ ] Tasso errori per codice (4xx, 5xx)
- [ ] Throughput per endpoint
- [ ] Tasso successo autenticazione
- [ ] Dimensione media allegati
- [ ] Pratiche per stato (dashboard)

### Log Events
- [ ] Ogni richiesta con X-Request-ID
- [ ] Operazioni CRUD su pratiche
- [ ] Login/logout utenti
- [ ] Errori e eccezioni
- [ ] Accessi a dati sensibili

### Alert
- [ ] Errore rate > 5% per 5 minuti
- [ ] Latenza p95 > 2 secondi
- [ ] Servizio down
- [ ] Rate limit superato frequentemente

---

## 🧪 11. PIANO DI TEST

### Test Funzionali
- [ ] CRUD completo per ogni risorsa
- [ ] Filtri e paginazione
- [ ] Upload/download allegati
- [ ] Validazioni input
- [ ] Gestione errori

### Test Sicurezza
- [ ] Autenticazione: token validi/invalidi/scaduti
- [ ] Autorizzazione: access control per ruolo
- [ ] Rate limiting: superamento limiti
- [ ] Input validation: SQL injection, XSS

### Test Performance
- [ ] Load test: 100 richieste/secondo
- [ ] Stress test: individuare breaking point
- [ ] Soak test: stabilità su 24 ore

### Test Integrazione
- [ ] Protocollo: generazione numero
- [ ] PagoPA: flusso pagamento
- [ ] ANPR: verifica residenza

---

## 📚 12. DOCUMENTAZIONE

### Per Sviluppatori
- [ ] Specifica OpenAPI 3.0 completa
- [ ] Guida Getting Started
- [ ] Esempi cURL per ogni endpoint
- [ ] Codici errore e troubleshooting
- [ ] Changelog versioni

### Per Utenti Finali
- [ ] Guida utente cittadini
- [ ] Guida professionisti
- [ ] FAQ
- [ ] Video tutorial

### Per Operations
- [ ] Deployment guide
- [ ] Monitoring dashboard
- [ ] Runbook operativo
- [ ] Disaster recovery plan

---

## ✅ 13. CHECKLIST COMPLETAMENTO

### Design
- [ ] Tutti gli stakeholder identificati
- [ ] Casi d'uso documentati e approvati
- [ ] Modello dati validato con domain experts
- [ ] Requisiti sicurezza definiti
- [ ] SLA concordati

### Implementazione
- [ ] Specifica OpenAPI generata
- [ ] Annotazioni semantiche complete (x-jsonld-*)
- [ ] Validata con oas-checker (score ≥ 95%)
- [ ] Esempi funzionanti per ogni endpoint
- [ ] Test coverage ≥ 80%

### Pre-Produzione
- [ ] Ambiente test disponibile
- [ ] Documentazione pubblicata
- [ ] Onboarding primi utenti pilota
- [ ] Metriche e alert configurati
- [ ] Piano di rollout definito

---

## 🚀 PROSSIMI PASSI

1. [ ] Review canvas con team
2. [ ] Approvazione stakeholder
3. [ ] Generazione specifica OpenAPI
4. [ ] Arricchimento semantico con ontologie
5. [ ] Validazione conformità ModI
6. [ ] Implementazione API
7. [ ] Test e QA
8. [ ] Deployment ambiente test
9. [ ] Pilota con utenti selezionati
10. [ ] Go-live produzione

---

**Data compilazione**: ___________  
**Compilato da**: ___________  
**Versione canvas**: 1.0  
**Review approvata da**: ___________  
**Data approvazione**: ___________
