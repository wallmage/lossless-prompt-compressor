# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

Komprimiert AI-Instruktionsdokumente, Regeldateien, Planungsdokumente und Produktspezifikationen. Ohne Faktenverlust.

Das ist Kompression, keine Zusammenfassung. Maschinenrelevante Fakten bleiben erhalten; Formatierungsoverhead, Redundanz und rein menschenlesbare Abschnitte werden entfernt. Optional wird alles in ultradichte AI-only-Kurzschrift umgeschrieben.

## Modi

- **Strict Lossless** (Standard): Mechanische Bereinigung (Pass 1) plus genehmigte faktenerhaltende Bearbeitungen (Pass 2). Jedes Fakt bleibt bestehen. Weiterhin menschenlesbar.
- **AI-Lossless**: Fügt Pass 3 hinzu. Entfernt Tutorials, Anleitungen, Zeitpläne — alles, was nur ein Mensch lesen würde.
- **AI-Only**: Fügt Pass 4 hinzu. Vollständige Telegrammstil-Umschreibung für maximale Dichte.

## Workflow

| Pass | Typ | Genehmigung | Typische Reduktion |
|------|-----|-------------|-------------------|
| 1 | Mechanische Bereinigung | Automatisch | 10-25% |
| 2 | Faktenerhaltende Kompression | Pro Eintrag | +5-15% |
| 3 | Entfernung menschenlesbarer Inhalte | Pro Eintrag | +15-25% |
| 4 | Telegrammstil-Umschreibung | Explizites Opt-in | +25-40% |

Pass 1 entfernt die Verpackung. Pass 2 vakuumversiegelt. Pass 3 wirft die Bedienungsanleitung weg, die nur ein Mensch braucht. Pass 4 wandelt alles in Kurzschrift um.

## Design

- Standard ist Strict Lossless: Kompression, keine Zusammenfassung
- Abschnittsnummern-sicher: Querverweisanker werden nie umnummeriert
- Genehmigungspflichtig: Jede nicht-mechanische Bearbeitung braucht Nutzerfreigabe
- 23 nummerierte Techniken mit Vorher/Nachher-Beispielen in `references/techniques.md`
- 12 Grenzfälle — von code-lastigen Prompts bis zu mehrsprachigen Dokumenten

## Verzeichnisstruktur

```
lossless-prompt-compressor/
├── SKILL.md              — Betriebsanleitung (~350 Zeilen)
└── references/
    └── techniques.md     — Vollständiger Technikkatalog mit Beispielen
```

## Autor

[Wallny](https://github.com/wallmage)
