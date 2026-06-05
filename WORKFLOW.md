# Workflow di Sviluppo - Repository zips

## Regole Generali

- **Push sempre**: Ogni creazione, modifica o eliminazione va committata e pushata subito.
- **Regola**: `git add` → `git commit` → `git push` dopo ogni operazione.

---

## Flusso di Lavoro

1. Ricevi codice RFC (es. `RFC-123`)
2. Creo branch: `git checkout -b feature/RFC-123 master`
3. Lavoro sulla feature
4. Test locale
5. Merge diretto su `main`: `git checkout main && git merge feature/RFC-123`
6. Push: `git push origin main`
7. Elimino branch locale: `git branch -d feature/RFC-123`

---

## Convenzioni Commit

**Formato**: `<tipo>: <descrizione in italiano>`

Tipi:
- `feat:` - Nuova funzionalità
- `fix:` - Correzione bug
- `docs:` - Documentazione
- `refactor:` - Refactoring
- `skill:` - Installazione/aggiornamento skill
- `chore:` - Manutenzione ordinaria

**Esempi**:
```
feat: aggiungi componente simulatore prestiti
fix: risolvi bug nel routing
docs: aggiorna istruzioni AGENTS.md
skill: installa skill grill-me
```

---

## Struttura Repository

```
zips/
├── AGENTS.md       ← Istruzioni per agenti di coding
├── README.md       ← Panoramica progetto
├── WORKFLOW.md     ← Questo file (flusso di sviluppo)
└── skills/         ← Skill installate per agenti
    └── productivity/
        ├── handoff/
        └── grill-me/
```

---

## Stack Tecnologico

- **Liferay Hook** (custom_jsps)
- **StealJS** per caricamento moduli
- **CanJS / jQueryMX**
- **Finch.js** per routing
- **EJS** per template
- **jQuery** + Globalize
- **LESS**
- **PDF.js**
- Java 8 + Maven

> ⚠️ Stack legacy (2013-2016). Non usare pattern moderni senza esplicita richiesta.

---

*Ultimo aggiornamento: 2026-06-05*