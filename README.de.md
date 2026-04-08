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

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | Deutsch | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## Warum es das gibt

Jeder Token in deinem System-Prompt ist eine Steuer, die du bei jedem einzelnen API-Call zahlst. Eine 35.000 Wort lange Instruktionsdatei bedeutet rund 48.000 Token an Kontext, die verbraucht sind, bevor das Modell eine einzige Nutzernachricht liest. Multipliziere das mit tausenden Anfragen pro Tag und du verbrennst Geld fuer Formatierung, Redundanz und Prosa, die nur ein Mensch jemals brauchen wuerde.

Ich habe das gebaut, um genau dieses Problem zu loesen. Lossless Prompt Compressor nimmt AI-Instruktionsdokumente -- System-Prompts, CLAUDE.md-Dateien, Agent-Regeln, Produktspezifikationen, Masterplaene -- und komprimiert sie rigoros. Nicht zusammenfassen. Komprimieren. Jedes Fakt, jeder Feldname, jede Einschraenkung, jede Querverweis bleibt intakt. Was entfernt wird, ist die Verpackung: Markdown-Formatierung, die das Modell nie brauchte, duplizierte Aussagen, Tutorial-Geruest, motivationales Fuellmaterial und grammatische Bindeglieder, die Token kosten, ohne Information zu transportieren.

Bei einer typischen Produktspezifikation erreichst du ueber alle vier Passes 50-65% Reduktion. Das ist echtes Geld zurueck in deiner Tasche bei jedem API-Call und echtes Context-Window, das fuer die Konversation frei wird, die tatsaechlich zaehlt.

Dieser Kompressor ist auch der Kern des Komprimierungsschritts in [vibecheck](https://github.com/wallmage/vibecheck).

## Was "lossless" wirklich bedeutet

Das ist keine Zusammenfassung. Eine Zusammenfassung entscheidet, was wichtig ist, und wirft den Rest weg. Bei Instruktionsdateien ist das gefaehrlich -- eine fehlende Einschraenkung und das Modell halluziniert ein Feature, das du explizit ausgeschlossen hast.

Lossless bedeutet: Wenn das Originaldokument sagt "Freemium-Subventionen auf 3% des Monatsumsatzes begrenzen", sagt die komprimierte Version exakt dasselbe. Keine Fakten werden gestrichen, mehrdeutig umformuliert oder stillschweigend zusammengefuehrt. Abschnittsnummern bleiben fixiert, sodass Querverweise ("siehe S8.7.3") nie brechen. Die AI produziert identische Code- und Architekturentscheidungen aus der komprimierten Version wie aus dem Original.

Die Garantie wird durch ein Freigabe-Gate durchgesetzt -- jede nicht-mechanische Bearbeitung wird mit einem Vorher/Nachher-Diff vorgeschlagen und erst nach deiner Freigabe angewandt.

## 23 Techniken in 4 Passes

Der Kompressor arbeitet in vier progressiven Passes. Jeder geht tiefer. Du entscheidest, wo du aufhoerst.

### Pass 1 -- Mechanische Bereinigung (automatisch, 10-25% Reduktion)

Keine Ermessensentscheidungen. Dieser Pass entfernt Formatierung, die LLMs identisch parsen, ob vorhanden oder nicht:

- **Markdown-Entfernung**: `##` Ueberschriften, `**fett**`, `- ` Aufzaehlungszeichen, `| Tabelle |` Pipe-Syntax, ` ``` ` Code-Fences, `> ` Blockquotes, `---` Trennlinien -- alles entfernt. Text und Struktur bleiben; die Dekoration geht.
- **Formatkonvertierung**: Tabellen werden zu semikolon-getrennten Zeilen. Aufzaehlungslisten werden zu Inline-semikolon-getrennten Elementen. Link-Syntax `[Text](url)` wird zur URL reduziert.
- **Verifikations-Scan**: Nach dem Entfernen ein vollstaendiger Scan auf verbliebene Markdown-Reste. Hex-Farben (`#FF0000`), Regex-Globs (`*.txt`), Shell-Pipes und YAML-Frontmatter werden als Inhalt erkannt und bleiben erhalten.

Techniken 1-2 aus dem Katalog. Sicher fuer jedes Dokument ausfuehrbar. Typische Ersparnis: 10-25% Wortreduktion.

### Pass 2 -- Faktenerhaltende Kompression (freigabepflichtig, +5-15%)

Hier wird es kreativ. Jede vorgeschlagene Bearbeitung wird dir mit geschaetzter Ersparnis gezeigt und erst nach Freigabe angewandt:

- **Querverweis-Deduplizierung** (Technik 3): Fakten, die an drei Stellen stehen, werden einmal kanonisch formuliert; die anderen erhalten Pointer wie "gemaess S6".
- **Codeblock-Kompression** (Technik 4): Struct-/Klassen-Definitionen werden zu kompakten Inline-Beschreibungen mit allen Feldnamen, Typen und Enums. Ausnahme: Python/YAML/Makefile bleiben unveraendert, da Whitespace semantisch ist.
- **JSON-Config-Kompression** (Technik 5): Vollstaendige JSON-Beispiele werden zu Schema-Beschreibungen.
- **Versions-Detail-Kuerzung** (Technik 6): Wenn Summary-Tabelle und Versions-Detail dasselbe sagen, wird das Detail entfernt.
- **Motivationsblock-Kompression** (Technik 7): "Warum das wichtig ist"-Abschnitte werden auf die technische Kernerkenntnis eingedampft.
- **Design-Begruendungs-Kompression** (Technik 8): Vier Absaetze Argumentation werden zu einem Satz mit den Top-Gruenden.
- **Use-Case-Listen-Kompression** (Technik 9): Ausfuehrliche Mehrparagraphen-Use-Cases werden zu je einem Einzeiler.
- **ASCII-Mockup-Kompression** (Technik 10): Token-intensive ASCII-Art wird zu kompakten Textbeschreibungen.

Jede Bearbeitung muss eine strikte Validierung bestehen: Alle Fakten vorhanden, die AI wuerde identische Entscheidungen treffen, keine Mehrdeutigkeit, jedes Fakt zum kanonischen Abschnitt zurueckverfolgbar.

### Pass 3 -- Entfernung menschenspezifischer Inhalte (freigabepflichtig, +15-25%)

Die Frage fuer jeden Kandidaten: "Wenn ich das entferne, produziert die AI schlechtere Ergebnisse?" Falls nein, ist es ein Ziel.

- **Anfaenger-Tutorials** (Technik 11): "Installiere deine IDE aus dem App Store. Das dauert 30-60 Minuten..." wird zu einer Liste, was installiert werden muss, nicht wie.
- **Uebungsaufgaben und Lernuebungen** (Technik 12): Die AI braucht keine fremden Lernuebungen.
- **Tagesplaene** (Technik 13): Aufstehzeiten und Kaffeepausen werden entfernt. Feature-Namen und technische Anforderungen bleiben.
- **Coaching und Motivationsinhalte** (Technik 14): "Keine Panik" und "Feier das!" tragen null Information fuer das Modell.
- **Validierungsmatrizen** (Technik 15): Eine 30-Zeilen-Tabelle, in der jedes Feature "SHIP" sagt, wird zu zwei Zeilen.
- **Wettbewerbsvergleichstabellen** (Technik 16): "Wir gewinnen in jeder Dimension" motiviert Menschen, nicht Modelle.
- **Menschliche Workflow-Beispiele** (Technik 17): "So sieht dein Tag aus"-Walkthroughs.

Pass 3 behaelt WAS und WARUM. Es entfernt WIE-fuer-Menschen.

### Pass 4 -- Telegramm-Umschreibung (explizites Opt-in, +25-40%)

Eine vollstaendige Dokumentumschreibung in ultra-dichter Kurzschrift. Jedes Wort muss Information tragen; existiert es nur, damit Prosa fuer einen menschlichen Leser fliessend klingt, wird es entfernt. Fuer Dokumente, bei denen die AI der einzige Konsument ist.

- **Brueckenphrasen-Eliminierung** (Technik 18): "Das bedeutet, dass", "was ermoeglicht", "um zu", "es ist wichtig zu beachten, dass" -- alles gestrichen.
- **Semikolon-getrennte Fragmente** (Technik 19): Vollstaendige Saetze werden zu Key-Value-Fragmenten. Artikel, "ist/sind", Fueller-Subjekte entfallen.
- **Mehrsatz-Zusammenfaltung** (Technik 20): Fuenf Saetze, die ein Konzept erklaeren, werden zum wesentlichen Fakt.
- **Implizite Subjekte weglassen** (Technik 21): Wenn die Abschnittsueberschrift "Suchfunktion" lautet, muss nicht jeder Satz mit "Die Suchfunktion" beginnen.
- **Kompakt-Notation** (Technik 22): Schraegstriche fuer Optionen (`immediate/batched/silent`), Pfeile fuer Ablaeufe (`offline -> local queue -> sync`), Doppelpunkte fuer Beziehungen (`Auth: JWT + refresh`).
- **Repetitive Strukturmuster-Kompression** (Technik 23): Muster einmal beschreiben, danach Kurzschrift.

Moderne LLMs parsen Telegramm-Stil mit voller Wiedergabetreue. Semikolons, Pfeile, schraegstrich-getrennte Optionen -- keine Modellfamilie hat damit Probleme.

## Modi

Drei Modi, jeder baut auf dem vorherigen auf:

- **Strict Lossless** (Standard): Pass 1 + Pass 2. Jedes Fakt bleibt erhalten. Das Dokument ist weiterhin menschenlesbar. Ideal fuer Dateien, die sowohl du als auch die AI referenzieren -- CLAUDE.md, geteilte Agent-Regeln, aktive Spezifikationen. Sicher, konservativ, echte Einsparungen.

- **AI-Lossless**: Fuegt Pass 3 hinzu. Entfernt Tutorials, Coaching, Tagesplaene, Validierungsmatrizen -- alles, was nur einem menschlichen Leser hilft. Die AI erhaelt exakt dasselbe Signal. Ideal fuer System-Prompts und Agent-Instruktionen, die die AI konsumiert, aber du selten nachschliesst.

- **AI-Only**: Fuegt Pass 4 hinzu. Vollstaendige Telegramm-Stil-Umschreibung. Maximale Dichte, kein angenehmes Lesen. Ideal fuer High-Volume-Produktions-Prompts, bei denen jeder eingesparte Token sich ueber tausende taegliche Calls multipliziert.

## Workflow

| Pass | Was es tut | Freigabe | Typische Reduktion |
|------|-----------|----------|-------------------|
| 1 | Markdown-Formatierung entfernen, Tabellen/Listen konvertieren | Automatisch | 10-25% |
| 2 | Fakten deduplizieren, Code/JSON/Begruendungen komprimieren | Pro Eintrag | +5-15% |
| 3 | Tutorials, Coaching, Zeitplaene, menschliches Geruest entfernen | Pro Eintrag | +15-25% |
| 4 | Vollstaendige Telegramm-Umschreibung, Fragmente statt Saetze | Explizites Opt-in | +25-40% |

Alle vier Passes auf einer typischen Produktspezifikation: **50-65% Reduktion**. Bei einem 35.000-Wort-Dokument sind das rund 25.000-32.000 weniger Token pro API-Call.

## Design-Prinzipien

- **Standardmaessig verlustfrei**: Strict Lossless ist der Standardmodus. Fuer aggressivere Kompression musst du explizit zustimmen.
- **Abschnittsnummern sind heilig**: Querverweis-Anker werden nie umnummeriert, selbst wenn umliegende Abschnitte entfernt werden. "Siehe S8.7.3" funktioniert immer.
- **Freigabe-Gate**: Jede nicht-mechanische Bearbeitung wird mit Diff und geschaetzter Ersparnis vorgeschlagen. Ohne deine Freigabe aendert sich nichts.
- **23 Techniken mit Beispielen**: Vollstaendiger Vorher/Nachher-Katalog in `references/techniques.md`.
- **12 Grenzfaelle abgedeckt**: Kurze Dokumente, code-lastige Prompts, bereits komprimierte Eingaben, nicht-englische/CJK-Texte, eingebettete URLs, mehrsprachige Dokumente, Dokumente ueber Markdown, literarische/juristische Texte, sehr grosse Dokumente und mehr.

## Verzeichnisstruktur

```
lossless-prompt-compressor/
├── SKILL.md              — Betriebsanleitung (~350 Zeilen)
└── references/
    └── techniques.md     — Vollstaendiger Technikkatalog mit Vorher/Nachher-Beispielen
```

## Autor

[Wallny](https://github.com/wallmage)
