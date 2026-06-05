# AGENTS.md — Guida per Agenti di Coding

Questo documento è ottimizzato per essere letto da agenti di coding (Cursor, Claude Code, GitHub Copilot Agent, Windsurf, ecc.).

## Panoramica del Progetto

**Nome**: `rdv-fe-lfr7-crediti-hook`  
**Tipo**: Liferay Hook (war)  
**Versione**: `5.2.0-SNAPSHOT`  
**Contesto**: Modulo frontend della piattaforma **Med.Nac** di Banca Mediolanum per la gestione di **Crediti** (prestiti, fidi, ecobonus, ecc.).

Si tratta di un'applicazione frontend monolitica legacy che gestisce l'intero flusso di simulazione, preventivazione e proposta di credito.

## Stack Tecnologico

- **Liferay Hook** (custom_jsps)
- **StealJS** per il caricamento moduli
- **CanJS / jQueryMX style** (`$.Class`, Models)
- **Finch.js** per il routing client-side
- **EJS** per i template
- **jQuery** + Globalize
- **LESS**
- **PDF.js** (due implementazioni)
- Java 8 + Maven

**Nota importante**: Stack molto datato (circa 2013-2016). Non usare pattern moderni (React, hooks, TypeScript, ecc.) a meno che non ti venga esplicitamente chiesto di modernizzare.

## Concetti Chiave del Dominio

| Termine       | Significato |
|---------------|-----------|
| **Quotation** / **Preventivo** | Simulazione/preventivo generato dal simulatore |
| **Proposal** / **Proposta**   | Proposta vera e propria creata da un preventivo |
| **Simulator**                 | Strumento per simulare rate e condizioni |
| **Owner** / **Intestatario**  | Persona fisica o giuridica intestataria della proposta |
| **Guarantee**                 | Garanzia (fideiussione, ipoteca, ecc.) |
| **Scoring**                   | Valutazione del merito creditizio (CRIF, Scipafi) |
| **Step**                      | Stato del flusso (es. `step_digisign`) |

## Struttura delle Directory

```
rdv-fe-lfr7-crediti-hook/
├── pom.xml
├── src/main/webapp/META-INF/custom_jsps/html/js/med/nac/credit/
│   ├── credit.js                    ← Bootstrap principale + caricamento moduli
│   ├── router.js                    ← Definizione di tutte le rotte Finch
│   ├── models/                      ← Layer dati (Models CanJS-style)
│   ├── simulator/                   ← Simulatore crediti
│   ├── proposal/                    ← Flusso completo della proposta (il cuore)
│   ├── quotation_list/              ← Elenco preventivi
│   ├── quotation_compare/           ← Confronto preventivi
│   ├── commons/                     ← Componenti condivisi
│   ├── ui/                          ← Componenti UI base
│   ├── fixtures/                    ← Dati mock (solo sviluppo)
│   ├── plugins/pdfjs*               ← Integrazione PDF.js
│   └── ...
```

## Come Funziona il Caricamento Moduli

Il progetto usa **StealJS**.

Esempio tipico in `credit.js`:

```js
steal(function ($) {
    // codice
}).then(
    "med/nac/credit/text",
    "med/nac/credit/models",
    "med/nac/credit/router.js"
).then(function () {
    // inizializzazione
});
```

Ogni cartella di feature contiene generalmente:
- `nomefeature.js` (controller/view)
- `views/init.ejs` (template)
- `css/` (se necessario)

## Router Principale (`router.js`)

Usa **Finch.js**. Le rotte più importanti sono sotto `/Credit/`:

- `/Credit/Simulator`
- `/Credit/Simulator/:type`
- `/Credit/QuotationCompare`
- `/Credit/Proposal/Owner`
- `/Credit/Proposal/Form`
- `/Credit/Proposal/Guarantee`
- `/Credit/Proposal/Outcome`
- `/Credit/Proposal/Sign*` (vari step di firma)
- `/Credit/QuotationList`

**Pattern comune**:
```js
Finch.route('/Credit/Proposal/Owner', function () {
    routeFunction('/Credit/Proposal/Owner', Med.Nac.Credit.Proposal.Owner);
});
```

La funzione `routeFunction` pulisce la canvas e carica il componente tramite `Med.Ib.Page.mainCanvas.trigger('loadCanvas', ...)`.

## Pattern dei Models

I modelli si trovano in `models/`.

Esempio (`models/proposal.js`):

```js
Med.Nac.Credit.Models.AdvancedList("Med.Nac.Credit.Models.Proposal", {
    findAll: Aria.rebaseUrl('{@urlPrefix}/credit/Proposal'),
    findOne: Aria.rebaseUrl('{@urlPrefix}/credit/Proposal/{id}'),
    // ...
}, {
    save: function() { ... },
    // metodi di istanza
});
```

Molti modelli ereditano da `AdvancedList`.

**Importante**: I modelli usano spesso `Aria.rebaseUrl`.

## Flusso Principale

1. **Simulator** → Scelta famiglia prodotto + inserimento dati → Generazione preventivi
2. **Quotation List / Compare** → Scelta del preventivo
3. **Proposal**:
   - Owner (intestatari)
   - Form (questionario)
   - Guarantee
   - Outcome / Scoring
   - Upload documenti
   - Signing (multi-step: PIN → Contratto → OTP → altri metodi)

## File Chiave da Conoscere

| File | Scopo |
|------|-------|
| `credit.js` | Entry point principale, patching, caricamento di tutti i moduli |
| `router.js` | Tutte le rotte dell'applicazione |
| `models/models.js` | Caricamento di tutti i modelli |
| `simulator/simulator.js` | Controller principale del simulatore |
| `proposal/form/form.js` | Questionario della proposta |
| `proposal/owner/owner.js` | Gestione intestatari |
| `step/step.js` | Gestione dello stato corrente (simulation, proposal, quotation) |

## Come Lavorare su Questa Codebase (per Agenti)

### Aggiungere una nuova schermata / step

1. Crea una nuova cartella sotto `med/nac/credit/`
2. Crea `tua-feature.js` + `views/init.ejs`
3. Aggiungi la rotta in `router.js`
4. Carica il modulo in `credit.js` nella chain `.then()`

### Aggiungere un nuovo Model

1. Crea `models/tuo-model.js`
2. Aggiungilo in `models/models.js`
3. Caricalo in `credit.js` se necessario

### Lavorare sul simulatore

La logica delle diverse famiglie prodotto è in:
- `simulator/loans/`
- `simulator/ecobonus/`
- `simulator/disaster/`
- ecc.

Il file `simulator/simulator.js` contiene la logica comune.

### Firma Digitale

Il flusso di firma è complesso e diviso in diversi step:
- `proposal/sign_pin/`
- `proposal/sign_contract/`
- `proposal/sign_otp/`
- `proposal/sign_medass/`
- `proposal/sign_fb/`
- `proposal/sign_confirm_doc/`

## Limitazioni e Debito Tecnico (importante per gli agenti)

- Codice legacy con forte uso di namespace globali (`Med.Nac.*`)
- Molta duplicazione tra moduli simili
- Uso pesante di `setInterval` per aspettare che altri moduli siano pronti
- Due versioni di PDF.js (evita di mischiare)
- Molti file di fixture ancora presenti
- Assenza di test
- Difficile estrarre componenti riutilizzabili

**Quando modifichi il codice**, mantieni lo stile esistente a meno che non ti venga chiesto esplicitamente di refactoring.

## Comandi Utili

```bash
# Vedere la struttura
find . -type f ! -path "./.git/*" | sort

# Contare i file JS
find . -name "*.js" ! -path "./.git/*" | wc -l

# Estrarre solo i sorgenti (senza .git)
unzip -o rdv-fe-lfr7-crediti-hook.zip -d . -x "*.git/*"
```

## Glossario Rapido

- **Med.Nac** = Piattaforma principale (Mediolanum + NAC?)
- **IB** = Internet Banking
- **Aria** = Framework interno usato per URL e modelli
- **Step** = Contesto corrente della simulazione/proposta
- **codPrevvo** = Codice del preventivo (molto usato)

---

**Istruzioni per gli agenti**: 
Quando ti viene chiesto di modificare questa codebase, parti sempre leggendo prima `credit.js`, `router.js` e i modelli principali. Chiedi chiarimenti se il task richiede di toccare flussi di firma o scoring.

**Regola importante**: Ogni volta che crei, modifichi o elimini file/cartelle, esegui subito `git add`, `git commit` e `git push` per mantenere il repository aggiornato.