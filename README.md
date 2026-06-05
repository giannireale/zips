# zips

Repository per il progetto **Med.Nac** di Banca Mediolanum - Gestione Crediti.

## 📁 Struttura

```
zips/
├── AGENTS.md    ← Guida completa per agenti di coding
└── rdv-lfr/     ← (directory rimossa)
```

## 🛠️ Stack Tecnologico

- **Liferay Hook** (custom_jsps)
- **StealJS** per il caricamento moduli
- **CanJS / jQueryMX** (`$.Class`, Models)
- **Finch.js** per il routing client-side
- **EJS** per i template
- **jQuery** + Globalize
- **LESS**
- **PDF.js**
- Java 8 + Maven

> ⚠️ Stack legacy (2013-2016). Non usare pattern moderni (React, TypeScript, hooks) a meno che non richiesto.

## 📚 Documentazione

Leggi **[AGENTS.md](AGENTS.md)** per:
- Concetti chiave del dominio (Quotation, Proposal, Simulator, Scoring)
- Struttura delle directory
- Pattern di caricamento moduli
- Flusso principale dell'applicazione
- Guida al lavoro su simulatori e flusso firma digitale

## 🔗 Link Utili

- [Repository GitHub](https://github.com/giannireale/zips)

## 📋 Comandi Utili

```bash
# Clonare il repository
git clone https://github.com/giannireale/zips.git

# Vedere la struttura
find . -type f ! -path "./.git/*" | sort

# Contare i file JS
find . -name "*.js" ! -path "./.git/*" | wc -l
```

## 🏦 Dominio Applicativo

| Termine | Significato |
|---------|-------------|
| **Quotation** | Simulazione/preventivo generato dal simulatore |
| **Proposal** | Proposta creata da un preventivo |
| **Simulator** | Strumento per simulare rate e condizioni |
| **Owner** | Intestatario della proposta |
| **Scoring** | Valutazione del merito creditizio |

---

Per istruzioni dettagliate di sviluppo, consulta [AGENTS.md](AGENTS.md).