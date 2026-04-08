# Lossless Prompt Compressor

[![GitHub stars](https://img.shields.io/github/stars/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/watchers)
[![GitHub last commit](https://img.shields.io/github/last-commit/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Top language](https://img.shields.io/github/languages/top/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Compression](https://img.shields.io/badge/compression-lossless-111927?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Focus](https://img.shields.io/badge/focus-prompt%20compaction-0f766e?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Works with](https://img.shields.io/badge/works%20with-LLM%20%7C%20agent%20%7C%20chat-4f46e5?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Output](https://img.shields.io/badge/output-smaller%20context-b45309?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Guarantee](https://img.shields.io/badge/guarantee-no%20facts%20lost-2563eb?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | Italiano | [Português](README.pt-BR.md)

## Perche esiste

Ogni token nel tuo system prompt e una tassa che paghi ad ogni chiamata API. Un file di istruzioni da 35.000 parole significa circa 48.000 token di contesto consumati prima ancora che il modello legga un singolo messaggio dell'utente. Moltiplica per migliaia di richieste al giorno e stai bruciando soldi per formattazione, ridondanza e prosa che solo un umano avrebbe mai bisogno di leggere.

Ho costruito questo strumento per risolvere il problema. Lossless Prompt Compressor prende documenti di istruzioni per AI -- system prompt, file CLAUDE.md, regole per agenti, specifiche di prodotto, piani generali -- e li comprime a fondo. Non riassume. Comprime. Ogni fatto, ogni nome di campo, ogni vincolo, ogni riferimento incrociato sopravvive intatto. Quello che viene tagliato e il confezionamento: formattazione Markdown di cui il modello non ha mai avuto bisogno, dichiarazioni duplicate, impalcatura didattica, riempitivo motivazionale e tessuto connettivo grammaticale che costa token senza trasportare informazione.

Su una specifica di prodotto tipica, ottieni il 50-65% di riduzione su tutti e quattro i pass. Sono soldi veri risparmiati ad ogni chiamata API e finestra di contesto reale liberata per la conversazione che conta davvero.

Questo compressore e anche il motore del passo di compressione all'interno di [vibecheck](https://github.com/wallmage/vibecheck).

## Cosa significa davvero "lossless"

Questo non e un riassunto. Un riassunto decide cosa e importante e butta via il resto. Per i file di istruzioni e pericoloso -- manca un vincolo e il modello allucinera una funzionalita che avevi esplicitamente escluso.

Lossless significa: se il documento originale dice "limitare i sussidi del tier gratuito al 3% del fatturato mensile", la versione compressa dice esattamente la stessa cosa. Nessun fatto viene eliminato, riformulato in modo ambiguo o silenziosamente unito. I numeri di sezione restano fissi, quindi i riferimenti incrociati ("vedi S8.7.3") non si rompono mai. L'AI produce decisioni di codice e architettura identiche dalla versione compressa come dall'originale.

La garanzia e applicata da un portale di approvazione -- ogni modifica non meccanica viene proposta con un diff prima/dopo e applicata solo dopo la tua approvazione.

## 23 tecniche in 4 pass

Il compressore opera in quattro pass progressivi. Ognuno va piu in profondita. Decidi tu dove fermarti.

### Pass 1 -- Pulizia meccanica (automatica, 10-25% di riduzione)

Nessuna valutazione discrezionale. Questo pass rimuove la formattazione che gli LLM analizzano in modo identico sia che sia presente o meno:

- **Rimozione Markdown**: intestazioni `##`, `**grassetto**`, elenchi puntati `- `, pipe delle tabelle `| |`, recinzioni di codice ` ``` `, citazioni `> `, linee `---` -- tutto rimosso. Testo e struttura restano; la decorazione va via.
- **Conversione formato**: le tabelle diventano righe separate da punto e virgola. Gli elenchi puntati diventano elementi inline separati da punto e virgola. La sintassi dei link `[testo](url)` si riduce all'URL.
- **Scansione di verifica**: dopo la rimozione, una scansione completa individua i residui Markdown. I colori esadecimali (`#FF0000`), i glob regex (`*.txt`), le pipe shell e il frontmatter YAML vengono riconosciuti come contenuto e preservati.

Tecniche 1-2 del catalogo. Eseguibile in sicurezza su qualsiasi documento. Risparmio tipico: 10-25% di riduzione delle parole.

### Pass 2 -- Compressione che preserva i fatti (richiede approvazione, +5-15%)

Qui diventa creativo. Ogni modifica proposta ti viene mostrata con il risparmio stimato e viene applicata solo dopo approvazione:

- **Deduplicazione tramite riferimenti incrociati** (Tecnica 3): fatti dichiarati in tre punti vengono dichiarati una sola volta; gli altri vengono sostituiti da un puntatore come "secondo S6".
- **Compressione dei blocchi di codice** (Tecnica 4): le definizioni di struct/class diventano descrizioni inline compatte preservando tutti i nomi dei campi, tipi ed enum. Eccezione: Python/YAML/Makefile restano intatti perche gli spazi sono semantici.
- **Compressione config JSON** (Tecnica 5): gli esempi JSON completi diventano descrizioni di schema.
- **Taglio dei dettagli per versione** (Tecnica 6): se la tabella riassuntiva e il dettaglio per versione dicono la stessa cosa, il dettaglio viene rimosso.
- **Compressione dei blocchi motivazionali** (Tecnica 7): le sezioni "perche questo e importante" vengono compresse all'insight tecnico essenziale.
- **Compressione delle giustificazioni di design** (Tecnica 8): quattro paragrafi di argomentazione diventano una frase con le ragioni principali.
- **Compressione delle liste di use case** (Tecnica 9): use case verbosi su piu paragrafi diventano una riga ciascuno.
- **Compressione dei mockup ASCII** (Tecnica 10): l'arte ASCII costosa in token diventa una descrizione testuale compatta.

Ogni modifica deve superare una validazione rigorosa: tutti i fatti presenti, l'AI prenderebbe decisioni identiche, nessuna ambiguita, ogni fatto tracciabile fino alla sezione canonica.

### Pass 3 -- Rimozione del contenuto per umani (richiede approvazione, +15-25%)

La domanda per ogni candidato: "Se rimuovo questo, l'AI produrra output peggiore?" Se no, e un obiettivo.

- **Tutorial per principianti** (Tecnica 11): "Installa il tuo IDE dall'App Store. Ci vogliono 30-60 minuti..." diventa una lista di cosa installare, non come.
- **Esercizi pratici e attivita di apprendimento** (Tecnica 12): l'AI non ha bisogno degli esercizi di apprendimento di qualcun altro.
- **Programmi giornalieri** (Tecnica 13): orari di sveglia e pause caffe vengono rimossi. Nomi delle funzionalita e requisiti tecnici restano.
- **Coaching e contenuto motivazionale** (Tecnica 14): "Niente panico" e "Festeggia!" portano zero informazione per il modello.
- **Matrici di validazione** (Tecnica 15): una tabella di 30 righe dove ogni funzionalita dice "SHIP" diventa due righe.
- **Tabelle comparative con i concorrenti** (Tecnica 16): "Vinciamo su ogni dimensione" motiva gli umani, non i modelli.
- **Esempi di workflow per umani** (Tecnica 17): walkthrough "ecco come sara la tua giornata".

Pass 3 preserva il COSA e il PERCHE. Rimuove il COME-per-umani.

### Pass 4 -- Riscrittura telegrafica (opt-in esplicito, +25-40%)

Una riscrittura completa del documento in stenografia ultra-densa. Ogni parola deve portare informazione; se esiste solo per far scorrere la prosa piacevolmente per un lettore umano, viene eliminata. Per documenti dove l'AI e l'unico consumatore.

- **Eliminazione delle frasi ponte** (Tecnica 18): "Questo significa che", "il che permette", "al fine di", "e importante notare che" -- tutto eliminato.
- **Frammenti separati da punto e virgola** (Tecnica 19): le frasi complete diventano frammenti chiave-valore. Articoli, "e/sono", soggetti di riempimento eliminati.
- **Condensazione di frasi multiple** (Tecnica 20): cinque frasi che spiegano un concetto diventano il fatto essenziale.
- **Eliminazione dei soggetti impliciti** (Tecnica 21): quando il titolo della sezione dice "Funzione di ricerca", non serve iniziare ogni frase con "La funzione di ricerca".
- **Notazione compatta** (Tecnica 22): barre per le opzioni (`immediate/batched/silent`), frecce per i flussi (`offline -> local queue -> sync`), due punti per le relazioni (`Auth: JWT + refresh`).
- **Compressione dei pattern strutturali ripetitivi** (Tecnica 23): descrivere il pattern una volta, poi usare la stenografia.

Gli LLM moderni analizzano lo stile telegrafico con piena fedelta. Punto e virgola, frecce, opzioni separate da barre -- nessuna famiglia di modelli ha problemi con questo.

## Modalita

Tre modalita, ognuna costruita sulla precedente:

- **Strict Lossless** (predefinita): Pass 1 + Pass 2. Ogni fatto sopravvive. Il documento resta leggibile per gli umani. Ideale per file che sia tu che l'AI consultate -- CLAUDE.md, regole di agente condivise, specifiche attive. Sicuro, conservativo, risparmio reale.

- **AI-Lossless**: Aggiunge Pass 3. Rimuove tutorial, coaching, programmi giornalieri, matrici di validazione -- tutto cio che aiuta solo un lettore umano. L'AI riceve esattamente lo stesso segnale. Ideale per system prompt e istruzioni per agenti che l'AI consuma ma che tu rileggi raramente.

- **AI-Only**: Aggiunge Pass 4. Riscrittura completa in stile telegrafico. Densita massima, lettura non piacevole. Ideale per prompt di produzione ad alto volume dove ogni token risparmiato si moltiplica per migliaia di chiamate giornaliere.

## Workflow

| Pass | Cosa fa | Approvazione | Riduzione tipica |
|------|---------|--------------|-----------------|
| 1 | Rimuove formattazione Markdown, converte tabelle/liste | Automatica | 10-25% |
| 2 | Deduplica fatti, comprime codice/JSON/giustificazioni | Per elemento | +5-15% |
| 3 | Rimuove tutorial, coaching, programmi, impalcatura umana | Per elemento | +15-25% |
| 4 | Riscrittura telegrafica completa, frammenti al posto di frasi | Opt-in esplicito | +25-40% |

Tutti e quattro i pass su una specifica di prodotto tipica: **50-65% di riduzione**. Su un documento di 35.000 parole, circa 25.000-32.000 token in meno per chiamata API.

## Principi di design

- **Lossless per default**: Strict Lossless e la modalita predefinita. Per compressione piu aggressiva serve un opt-in esplicito.
- **I numeri di sezione sono sacri**: le ancore di riferimento incrociato non vengono mai rinumerate, anche quando le sezioni circostanti vengono rimosse. "Vedi S8.7.3" funziona sempre.
- **Portale di approvazione**: ogni modifica non meccanica viene proposta con diff e risparmio stimato. Nulla cambia senza la tua approvazione.
- **23 tecniche con esempi**: catalogo completo prima/dopo in `references/techniques.md`.
- **12 casi limite gestiti**: documenti brevi, prompt ricchi di codice, input gia compressi, testi non inglesi/CJK, URL incorporate, documenti multilingue, documenti sul Markdown, prosa creativa/legale, documenti molto grandi, e altro.

## Struttura

```
lossless-prompt-compressor/
├── SKILL.md              — Manuale operativo (~350 righe)
└── references/
    └── techniques.md     — Catalogo completo delle tecniche con esempi prima/dopo
```

## Autore

[Wallny](https://github.com/wallmage)
