# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

Compresse les documents d'instructions AI, fichiers de règles, documents de planification et spécifications produit. Sans perte de faits.

C'est de la compression, pas du résumé. Les faits utiles à la machine sont préservés ; le balisage superflu, les redondances et les sections destinées uniquement aux humains sont supprimés. En option, le tout peut être réécrit en sténo ultra-dense réservée à l'AI.

## Modes

- **Strict Lossless** (par défaut) : Nettoyage mécanique (Pass 1) + éditions approuvées préservant les faits (Pass 2). Chaque fait est conservé. Reste lisible par un humain.
- **AI-Lossless** : Ajoute le Pass 3. Supprime tutoriels, guides, calendriers — tout ce que seul un humain lirait.
- **AI-Only** : Ajoute le Pass 4. Réécriture complète en style télégraphique pour une densité maximale.

## Workflow

| Pass | Type | Approbation | Réduction typique |
|------|------|-------------|-------------------|
| 1 | Nettoyage mécanique | Automatique | 10-25% |
| 2 | Compression préservant les faits | Par élément | +5-15% |
| 3 | Suppression du contenu humain | Par élément | +15-25% |
| 4 | Réécriture télégraphique | Opt-in explicite | +25-40% |

Le Pass 1 retire l'emballage. Le Pass 2 met sous vide. Le Pass 3 jette le mode d'emploi que seul un humain consulterait. Le Pass 4 convertit tout en sténo.

## Conception

- Strict Lossless par défaut : compression, pas résumé
- Numéros de section préservés : les ancres de référence croisée ne sont jamais renumérotées
- Contrôle par approbation : toute édition non mécanique nécessite la validation de l'utilisateur
- 23 techniques numérotées avec exemples avant/après dans `references/techniques.md`
- 12 cas limites couvrant les prompts riches en code jusqu'aux documents multilingues

## Structure

```
lossless-prompt-compressor/
├── SKILL.md              — Manuel d'utilisation (~350 lignes)
└── references/
    └── techniques.md     — Catalogue complet des techniques avec exemples
```

## Auteur

[Wallny](https://github.com/wallmage)
