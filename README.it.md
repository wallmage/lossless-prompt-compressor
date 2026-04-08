# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

Comprime documenti di istruzioni per AI, file di regole, documenti di pianificazione e specifiche di prodotto. Nessun fatto perso.

Questa è compressione, non riassunto. I fatti rilevanti per la macchina restano; la formattazione superflua, le ridondanze e il contenuto destinato solo agli umani vengono tagliati. Opzionalmente, riscrive tutto in stenografia ultradensa riservata all'AI.

## Modalità

- **Strict Lossless** (predefinita): Pulizia meccanica (Pass 1) + modifiche approvate che preservano i fatti (Pass 2). Ogni fatto sopravvive. Resta leggibile per gli umani.
- **AI-Lossless**: Aggiunge il Pass 3. Rimuove tutorial, guide, calendari — tutto ciò che solo un umano leggerebbe.
- **AI-Only**: Aggiunge il Pass 4. Riscrittura completa in stile telegrafico per massima densità.

## Workflow

| Pass | Tipo | Approvazione | Riduzione tipica |
|------|------|--------------|-----------------|
| 1 | Pulizia meccanica | Automatica | 10-25% |
| 2 | Compressione con preservazione dei fatti | Per elemento | +5-15% |
| 3 | Rimozione contenuto umano | Per elemento | +15-25% |
| 4 | Riscrittura telegrafica | Opt-in esplicito | +25-40% |

Il Pass 1 toglie l'imballaggio. Il Pass 2 sigilla sottovuoto. Il Pass 3 butta via il manuale che solo un umano consulterebbe. Il Pass 4 converte tutto in stenografia.

## Design

- Strict Lossless come predefinito: compressione, non riassunto
- Numeri di sezione sicuri: gli ancoraggi di riferimento incrociato non vengono mai rinumerati
- Controllo tramite approvazione: ogni modifica non meccanica richiede conferma dell'utente
- 23 tecniche numerate con esempi prima/dopo in `references/techniques.md`
- 12 casi limite che coprono dai prompt ricchi di codice ai documenti multilingue

## Struttura

```
lossless-prompt-compressor/
├── SKILL.md              — Manuale operativo (~350 righe)
└── references/
    └── techniques.md     — Catalogo completo delle tecniche con esempi
```

## Autore

[Wallny](https://github.com/wallmage)
