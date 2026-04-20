---
name: Organico Scolastico - Contesto Progetto
description: Stato e dettagli tecnici del progetto organico_scolastico_v2.html per riprendere lavoro nelle sessioni future
type: project
originSessionId: 36ae57b2-9fa0-461d-b831-5c5495171512
---
# Organico Scolastico v2

**File principale:** `C:\Users\alfre\Downloads\organico_scolastico_v2.html`
**Repo GitHub:** `https://github.com/LeoDuhr/organico-scolastico` (branch `main`, cartella `Downloads`)
**Backup dati:** `organico_backup_2026-04-20.json` (in Downloads e su GitHub)

## Architettura

App single-file HTML (no backend). Tutto in localStorage.

| Chiave localStorage | Contenuto |
|---|---|
| `organico_db` | array assegnazioni (db) |
| `organico_dip` | override dipartimento per docente |
| `organico_classidb` | lista classi con piani associati |
| `organico_composizioni` | (legacy, non più usato) |

## Strutture dati chiave

**`db`** — array di assegnazioni:
```js
{id, piano, classe, disciplina, codice, cdc_a, ore_a, cdc_b, ore_b, docente_a, docente_b, potenziamento}
```

**`classiDB`** — classi normali e articolate:
```js
// normale
{nome:'ITI 3A', pianoKey:'ITI CHIM 3', articolata:false}
// articolata
{nome:'IPIA 3NP', piano_comune:'IPIA COMUNI', articolata:true,
  sottogruppi:[
    {nome:'IPIA 3NP-N', piano_specifico:'IPIA ELETTRO 3 SP'},
    {nome:'IPIA 3NP-P', piano_specifico:'IPIA MECC 3 SP'}
  ]}
```

**`piani`** — costruito da `buildPianiRaw()`: merge `PIANI_COMUNI_RAW` + `PIANI_SPECIFICI_RAW`

## Concetto articolata

Classe articolata = 1 piano comune + 2 piani specifici per 2 sottogruppi.
- Piano comune → materie sulla classe principale
- Piano specifico 1/2 → materie su ogni sottogruppo

Classi articolate rilevate automaticamente dalla migrazione `COMPOSIZIONI_RAW`:
IPIA 3NP, ITI 4BL, ITI 5BL, ITG 4 AC, (possibile ITI 3 FL)

## Piani di studio

- `PIANI_COMUNI_RAW`: COMUNI 3-4, COMUNI 5, IPIA COMUNI
- `PIANI_SPECIFICI_RAW`: ITI CHIM 3 SP, ITI ELETTRO 3 SP, ITI INF 3 SP, ecc.
- `COMPOSIZIONI_RAW`: mappa composizione → {piano_comune, piano_specifico, classi[]}

## Funzioni principali

| Funzione | Cosa fa |
|---|---|
| `renderClassi()` | Render tabella classi con badge ARTICOLATA e sottogruppi |
| `aggiungiNuovaClasse()` | Aggiunge classe (plesso dropdown + nome libero) |
| `generaAssegnazioni()` | Genera righe db da classiDB |
| `generaRigheClasse(entry, sgIdx)` | Genera righe per una classe/sottogruppo |
| `resetClassiDB()` | Cancella organico_classidb e ricarica da COMPOSIZIONI_RAW |
| `exportJSON()` / `importJSON()` | Backup/ripristino completo stato |
| `importCSV()` | Import da Google Sheets (13 col) o formato app (10 col) |
| `normCdc(cdc)` | Normalizza CDC: "A26/A27" → "A26" |
| `renderDocenti()` | Cards docenti con cattedra completa, filtro dipartimento |

## CDC → Dipartimento

A12→Italiano, A34/B12→Chimica, A26→Matematica, AB24→Inglese,
A48→Scienze Motorie, A50→Scienze e Biologia, A51/A52/B11→Agraria,
A20/B03→Fisica, A46→Diritto, A37/B14→Geometri e TTRG,
A40/B15→Elettrotecnica, RC→Religione, A41/B16→Informatica,
A42/B17→Meccanica, A44/B18→Moda

## Nav pagine

Assegnazioni | Piani di Studio | Docenti | Classi | Dashboard | Esporta

## Note operative

- Italiano e storia: split automatico a import e a load localStorage (4 ore Italiano, 2 Storia)
- CDC normalizzata a import e a load (split su '/', prende primo)
- Plesso nel form Classi: dropdown IPIA/ITI/ITA/ITG + campo nome libero
- Per modifiche JS complesse con caratteri speciali: usare script Python in Downloads
