# README — Ricerca Inquilino · Via Monte Grappa 5, Verona

> Progetto: selezione inquilino per locazione abitativa tramite landing page pubblica + form strutturato + raccolta documenti + analisi AI + comunicazioni standardizzate.
>
> Scadenza decisione: **20 giugno 2026**
>
> **Comando rapido:** `apri link salita monte grappa` / `riapri link salita monte grappa` → leggere questo README e aprire `index.html` nel browser
>
> **Log sessioni di lavoro:** [`LOG_sessioni.md`](LOG_sessioni.md) — cronologia delle sessioni Claude, stato avanzamento, prossimi passi. **Aggiornare dopo ogni sessione.**
>
> **Guida replicabile:** [`README_creazione_landingpage_sistema.md`](README_creazione_landingpage_sistema.md) — processo completo (Fase 0–8) per replicare questo sistema su un altro immobile: raccolta info, archiviazione, landing page, GitHub Pages, Google Form, Sheet, Apps Script, test, gestione candidature.

---

## Struttura cartella

```
LINK E RICERCA INQUILINO/
├── README_Ricerca_Inquilino.md        ← questo file
├── LOG_sessioni.md                    ← cronologia sessioni Claude
├── index.html                         ← landing page (GitHub Pages)
├── modello_invito_candidatura.md      ← template email invito candidatura da portale
├── foto/                              ← foto appartamento
├── apps-script/
│   └── email_conferma.gs              ← script Google Apps Script per email automatica
├── google-sheet/
│   └── struttura_sheet.md             ← schema colonne Google Sheet candidature
└── python/
    ├── estrai_buste_paga.py           ← script AI estrazione dati da buste paga
    ├── requirements.txt               ← dipendenze Python
    └── SETUP.md                       ← istruzioni configurazione
```

---

## Moduli del sistema

| # | Modulo | Stato | Note |
|---|--------|-------|------|
| 1 | Landing page (index.html) | ✅ Pubblicato | https://andreatosato-2026.github.io/tosato-montegrappa-inquilino/ |
| 2 | Google Form candidature | ✅ Configurato | Form ID: 1lBzs3vp5_jBDImh_CPlcpUNhdgP852ZIIFEjZOnI8YU |
| 3 | Google Sheet "Candidature" | ✅ Collegato | Sheet ID: 1sWP4mZ9VBMY6ucbmrUl5kPMuc5p8xrOTIPMrOp-2qyM |
| 4 | Email conferma automatica | ✅ Attivo | Script installato + trigger onFormSubmit configurato |
| 5 | Script AI estrazione buste paga | ✅ Creato | python/ — vedi SETUP.md |
| 6 | Template email risposta (3 tipi) | ✅ Documentato | Vedi sezione sotto |
| 7 | Procedura CHECK MONTE GRAPPA | ✅ Documentato | Vedi sezione sotto — `modello_invito_candidatura.md` |

---

## MODULO 1 — Landing Page (GitHub Pages)

### Pubblicazione

1. Creare repo GitHub: `tosato-montegrappa-inquilino` (privato o pubblico)
2. Copiare `index.html` e cartella `foto/` nella root del repo
3. Attivare GitHub Pages: Settings → Pages → Branch: main → / (root)
4. URL finale: `https://andreatosato-2026.github.io/tosato-montegrappa-inquilino/`
5. URL Google Form (già inserito): `https://docs.google.com/forms/d/e/1FAIpQLSdWt7IFD7vps8gHezomrtCVSZVl12K0x0GWBJ4NRs_9JZxp_A/viewform?usp=header`

### ⚠️ Nota mappa Google Maps

Il parametro `q=` nell'iframe deve essere **`Via+Salita+Monte+Grappa+5,+Verona+VR,+Italia`** (con "Salita").
Usare solo `Via+Monte+Grappa+5` porta Google Maps a geolocalizzare una via omonima a Villafranca di Verona.

### Foto

Le foto sono in `foto/foto-01.jpg` … `foto-31.jpg`. La gallery nella landing page ne mostra una selezione.
Se vuoi escludere alcune foto, rimuoverle dalla cartella prima di caricare su GitHub.

**Nota:** Le foto sono rotated (scattate in verticale con telefono). Prima di pubblicare è consigliato ruotarle con:
```bash
cd "foto/"
brew install imagemagick   # se non installato
for f in foto-*.jpg; do mogrify -auto-orient "$f"; done
```

---

## MODULO 2 — Google Form

### Struttura form (3 sezioni)

**Sezione 1 — Dati personali e lavorativi**
- Nome e Cognome (Risposta breve, obbligatorio)
- Indirizzo email (Email, obbligatorio)
- Telefono (Risposta breve, obbligatorio)
- Attività lavorativa / professione (Risposta breve, obbligatorio)
- Ragione sociale o nome datore di lavoro (Risposta breve, obbligatorio)
- Sede di lavoro — Comune (Risposta breve, obbligatorio)
- Tipo di contratto di lavoro (Scelta multipla: Tempo indeterminato / Tempo determinato / Lavoro autonomo / Libero professionista / Altro)
- Data inizio contratto / attività (Data, obbligatorio)
- Data fine contratto (Data, opzionale — solo se determinato)
- Stipendio netto mensile dichiarato (Risposta breve, obbligatorio)
- Note aggiuntive (Paragrafo, opzionale)

**Sezione 2 — Upload documenti**
- Ultime 3 buste paga (File upload, max 3 file, PDF o JPEG, max 10MB cad., **facoltativo**)
- Copia contratto di lavoro (File upload, max 1 file, PDF, **facoltativo**)
- *Nota: i campi sono stati resi facoltativi. La sezione 3 contiene un avviso: senza documenti non è possibile completare l'analisi.*

**Sezione 3 — Conferma**
- Messaggio di conferma personalizzato (vedi testo sotto)

### Testo conferma nel form

```
Candidatura ricevuta.

Analizzeremo i documenti caricati entro 5 giorni lavorativi.
Se la documentazione risulterà completa, la ricontatteremo entro il 10 giugno.
In caso di preselezione positiva, la inviteremo a visitare l'immobile: in quella sede
le saranno forniti tutti i dettagli contrattuali.

La proprietà comunicherà la propria decisione entro il 20 giugno 2026.

Per qualsiasi necessità: andrea.tosato@tosatointermediazioni.com
```

### Impostazioni form
- Raccogli indirizzi email: SÌ (obbligatorio)
- Limita a 1 risposta: SÌ (richiede login Google)
- Mostra riepilogo risposte: NO
- Destinazione risposte: collegare al Google Sheet "Candidature Via Monte Grappa"

---

## MODULO 3 — Google Sheet "Candidature"

### Schema colonne

| Col | Campo | Fonte |
|-----|-------|-------|
| A | Timestamp | Google Forms |
| B | Nome e Cognome | Forms |
| C | Email | Forms |
| D | Telefono | Forms |
| E | Attività lavorativa | Forms |
| F | Datore di lavoro / RS | Forms |
| G | Sede lavoro | Forms |
| H | Tipo contratto | Forms |
| I | Data inizio contratto | Forms |
| J | Data fine contratto | Forms |
| K | Netto mensile dichiarato | Forms |
| L | Note candidate | Forms |
| M | Link buste paga (Drive) | Forms |
| N | Link contratto (Drive) | Forms |
| — | — Campi estratti da AI — | — |
| O | Netto mese 1 (AI) | Script Python |
| P | Netto mese 2 (AI) | Script Python |
| Q | Netto mese 3 (AI) | Script Python |
| R | Netto medio AI | Formula =(O+P+Q)/3 |
| S | Datore lavoro (AI) | Script Python |
| T | Tipo contratto (AI) | Script Python |
| U | Data inizio (AI) | Script Python |
| — | — Analisi — | — |
| V | Rapporto netto/canone | Formula =R/1000 |
| W | Soglia OK (≥3x) | Formula =SE(V>=3;"SI";"NO") |
| X | Esito preanalisi | Manuale (POSITIVO / INTEGRARE / NEGATIVO) |
| Y | Note proprietà | Manuale |
| Z | Email inviata | Manuale (data invio) |

### Formattazione consigliata
- Intestazioni bloccate (riga 1)
- Colonna W: formattazione condizionale → verde se "SI", rosso se "NO"
- Colonna X: elenco a discesa (POSITIVO / INTEGRARE / NEGATIVO / IN ATTESA)

---

## MODULO 4 — Email conferma automatica (Apps Script)

Il file `apps-script/email_conferma.gs` contiene lo script da installare nel Google Sheet.

### Installazione
1. Aprire il Google Sheet candidature
2. Menu → Estensioni → Apps Script
3. Incollare il contenuto di `email_conferma.gs`
4. Salvare e autorizzare
5. Configurare trigger: Esegui → `onFormSubmit` → Trigger: Da foglio → All'invio del modulo

---

## MODULO 5 — Estrazione AI buste paga (da sviluppare)

Script Python che:
1. Legge il Google Sheet per le righe senza dati AI (colonne O-U vuote)
2. Scarica i file da Google Drive
3. Invia a Claude API (base64) → estrae dati strutturati
4. Scrive i dati estratti nelle colonne O-U

**Sicurezza:** i file non vengono mai aperti localmente — solo inviati base64 all'API.
**Prerequisito:** Claude API key in `~/.anthropic_api_key` o variabile d'ambiente.

Da sviluppare nella prossima sessione.

---

## Prefisso universale — Email e Agenda

**⚠️ REGOLA OBBLIGATORIA:** Tutte le email e tutti gli eventi in agenda relativi a questo progetto devono avere il prefisso:

```
MONTE GRAPPA | 
```

### Email

Oggetto di ogni email in uscita (automatiche da Apps Script e manuali):
```
MONTE GRAPPA | [oggetto specifico]
```

Esempi:
- `MONTE GRAPPA | Candidatura ricevuta — Via Salita Monte Grappa 5, Verona`
- `MONTE GRAPPA | Informativa privacy — trattamento dati candidatura locazione`
- `MONTE GRAPPA | Via Salita Monte Grappa 5, Verona — Candidatura selezionata`
- `MONTE GRAPPA | Via Salita Monte Grappa 5, Verona — Integrazione documentazione`
- `MONTE GRAPPA | Via Salita Monte Grappa 5, Verona — Esito candidatura`
- `MONTE GRAPPA | Via Salita Monte Grappa 5, Verona — Informazioni per la candidatura`

### Agenda

Titolo di ogni evento in calendario (attività, promemoria, eventi fatti):
```
MONTE GRAPPA | [titolo specifico]
```

Esempi:
- `MONTE GRAPPA | Inviata email invito candidatura a [Nome] - - fatto`
- `MONTE GRAPPA | Verificare risposta [Nome]`
- `MONTE GRAPPA | Candidatura ricevuta [Nome] - - fatto`
- `MONTE GRAPPA | Analisi candidature`
- `MONTE GRAPPA | Visita immobile [Nome]`

---

## MODULO 6 — Template email risposta (3 tipi)

### Tipo A — Esito positivo (invito a visita)

```
Oggetto: MONTE GRAPPA | Via Monte Grappa 5, Verona — Candidatura selezionata

Buongiorno [Nome],

la informiamo che la sua candidatura è stata esaminata con esito positivo.

La invitiamo a contattarci per fissare un appuntamento di visita all'immobile,
nel corso del quale le saranno illustrati tutti i dettagli contrattuali.

Per concordare data e orario: andrea.tosato@tosatointermediazioni.com oppure [TELEFONO].

Distinti saluti,
Andrea Tosato
TOSATO Intermediazioni S.r.l.
```

### Tipo B — Richiesta integrazione documenti

**Variante B1 — Lavoratore dipendente** (buste paga + contratto)

```
Oggetto: MONTE GRAPPA | Via Monte Grappa 5, Verona — Integrazione documentazione

Buongiorno [Nome],

la ringraziamo per aver inviato la sua candidatura per l'immobile di Via Monte Grappa 5, Verona.

Nel verificare i documenti caricati, abbiamo riscontrato che la documentazione risulta incompleta
o che alcuni file non sono leggibili. In particolare:

- [es. buste paga: ne sono pervenute solo 2 su 3 richieste]
- [es. contratto di lavoro: il file caricato non è leggibile]

La preghiamo di caricare nuovamente i documenti indicati rispondendo a questa email, oppure
tramite il seguente link:
https://docs.google.com/forms/d/e/1FAIpQLSdWt7IFD7vps8gHezomrtCVSZVl12K0x0GWBJ4NRs_9JZxp_A/viewform

La ringraziamo per la collaborazione.

Distinti saluti,
Andrea Tosato
TOSATO Intermediazioni S.r.l.
andrea.tosato@tosatointermediazioni.com
```

**Variante B2 — Lavoratore autonomo / libero professionista** (CU o dichiarazione dei redditi)

```
Oggetto: MONTE GRAPPA | Via Monte Grappa 5, Verona — Integrazione documentazione

Buongiorno [Nome],

la ringraziamo per aver inviato la sua candidatura per l'immobile di Via Monte Grappa 5, Verona.

Per i lavoratori autonomi e i liberi professionisti, in luogo delle buste paga e del contratto
di lavoro dipendente, è necessario allegare:

- le ultime 2 Certificazioni Uniche (CU), oppure
- le ultime 2 dichiarazioni dei redditi (modello 730 o modello Redditi)

La preghiamo di inviarci la documentazione rispondendo a questa email, oppure tramite
il seguente link:
https://docs.google.com/forms/d/e/1FAIpQLSdWt7IFD7vps8gHezomrtCVSZVl12K0x0GWBJ4NRs_9JZxp_A/viewform

La ringraziamo per la collaborazione.

Distinti saluti,
Andrea Tosato
TOSATO Intermediazioni S.r.l.
andrea.tosato@tosatointermediazioni.com
```

### Tipo C — Esito negativo

```
Oggetto: MONTE GRAPPA | Via Monte Grappa 5, Verona — Esito candidatura

Buongiorno [Nome],

la ringraziamo per aver candidato la sua candidatura per l'immobile di Via Monte Grappa 5.

All'esito della valutazione, per questa specifica proposta abbiamo orientato 
la scelta verso un altro profilo.

Le auguriamo buona fortuna nella ricerca.

Distinti saluti,
Andrea Tosato
TOSATO Intermediazioni S.r.l.
```

---

---

## MODULO 7 — Procedura CHECK MONTE GRAPPA

**Trigger:** `Check Monte Grappa`

**Scopo:** verificare se sono arrivate richieste da portali immobiliari, analizzare il profilo del richiedente e decidere se inviare l'email di invito alla candidatura.

**Riferimento annuncio sui portali:** `53mq-AT1131`

---

### Step 1 — Cerca email (oggi + ieri)

Cercare nelle ultime 48h in entrambi gli account Gmail:

| Account | Tool | Termini di ricerca |
|---------|------|--------------------|
| Gmail aziendale | `mcp__gmail-lavoro__search_emails` | `53mq-AT1131 OR "Monte Grappa" OR AT1131` — escludi mittente andrea.tosato@tosatointermediazioni.com |
| Gmail personale | `mcp__gmail__search_emails` | stessa query (BCC notifiche automatiche) |

Per ogni email trovata, estrarre: mittente (nome + email), numero di telefono (se presente nel testo), testo integrale della richiesta, data/ora.

---

### Step 2 — Presentare le email trovate

Per ogni email trovare e mostrare:
- Mittente: nome + email
- Data/ora ricezione
- Testo della richiesta (sintesi)
- Telefono (se presente)

---

### Step 3 — Analisi richiedente

Per ciascun richiedente eseguire in sequenza:

**3a — Nome italiano o straniero**
Analisi testuale del nome e cognome: indicare se il nome è presumibilmente italiano, europeo, extraeuropeo o non determinabile.

**3b — WebSearch del nome** (solo se nome+cognome entrambi presenti)
```
WebSearch: "[Nome] [Cognome]" site:linkedin.com OR site:facebook.com OR site:paginebianche.it
```
Riportare i primi 2-3 risultati significativi (chi è, dove lavora, se corrisponde ai dati dichiarati).

**3c — WebSearch del numero di telefono** (solo se presente)
```
WebSearch: "[numero telefono]"
```
Segnalare se il numero risulta associato a nomi, attività, segnalazioni truffe o spam.

**3d — WebSearch dell'indirizzo email**
```
WebSearch: "[email richiedente]"
```
Segnalare se l'email risulta associata a profili social, siti, attività o segnalazioni anomale.

---

### Step 4 — Proposta invio email

Dopo l'analisi, chiedere:
> "Invio l'email di invito candidatura a [Nome / email]?"

Se sì → usare `modello_invito_candidatura.md` e inviare da Gmail aziendale (`mcp__gmail-lavoro__send_email`).

---

### Step 5 — Post invio

Dopo l'invio:
1. Creare evento agenda oggi: `MONTE GRAPPA | Inviata email invito candidatura a [Nome] - - fatto` (colorId 10, 15 min, nessun promemoria)
2. Creare promemoria a +4 giorni lavorativi: `MONTE GRAPPA | Verificare risposta [Nome]` (colorId 10, 15 min, nessun promemoria)
3. **Applicare etichetta `Processato con AI` (Label_16)** a TUTTE le email gestite in questo check (sia quelle per cui si invia l'invito, sia quelle scartate), tramite `mcp__gmail-lavoro__modify_email` con `addLabelIds: ["Label_16"]`. Questo evita che vengano riprocessate nel prossimo Check Monte Grappa.

> **Nota:** applicare l'etichetta solo alle email dei portali (Idealista, Immobiliare.it), non alle email di sistema (conferme automatiche da andrea.tosato1@gmail.com, UNIPOL, ecc.).

---

### Step 1 — Filtro email già processate

**Prima di presentare le email al Step 2**, escludere automaticamente quelle che hanno già l'etichetta `Processato con AI` — sono state gestite in un check precedente. Aggiungere `-label:processato-con-ai` alla query di ricerca Gmail.

---

### Se non ci sono email

Rispondere: "Nessuna email con riferimento 53mq-AT1131 / Monte Grappa nelle ultime 48h."

---

## Dati immobile (riferimento)

| Campo | Valore |
|-------|--------|
| Indirizzo | Via Monte Grappa 5, Verona |
| Zona | Borgo Trento / Valdonega |
| Tipologia | Casa terra-cielo indipendente |
| Superficie | 53 mq |
| Livelli | 3 (PT: soggiorno + angolo cottura; P1: bagno; P2: camera matrimoniale) |
| Contratto | 4+4 anni uso abitativo |
| Canone anni 1-4 | € 900/mese |
| Canone anni 5-8 | € 1.100/mese |
| Spese condominiali | Nessuna |
| Assicurazione | € 80/anno (conduttore) |
| Manutenzione caldaia | € 120/anno (conduttore) |
| Piccole manutenzioni | A carico conduttore |
| Spese straordinarie | A carico locatore |

---

## Timeline candidature

| Data | Azione |
|------|--------|
| Pubblicazione link | Appena pronto |
| Chiusura form | 20 giugno 2026 |
| Pre-analisi entro | 5 giorni lavorativi dalla candidatura |
| Ricontatto completi | Intorno al 10 giugno 2026 |
| Visite | Da concordare dopo preselezione |
| Decisione finale | Entro 20 giugno 2026 |
