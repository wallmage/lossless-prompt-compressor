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

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | Français | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## Pourquoi cet outil existe

Chaque token dans ton system prompt est une taxe que tu paies a chaque appel API. Un fichier d'instructions de 35 000 mots represente environ 48 000 tokens de contexte consommes avant meme que le modele lise le premier message utilisateur. Multiplie ca par des milliers de requetes par jour et tu brules de l'argent pour du formatage, de la redondance et de la prose que seul un humain aurait besoin de lire.

J'ai construit cet outil pour regler ce probleme. Lossless Prompt Compressor prend des documents d'instructions AI -- system prompts, fichiers CLAUDE.md, regles d'agents, specifications produit, plans directeurs -- et les compresse a fond. Pas un resume. De la compression. Chaque fait, chaque nom de champ, chaque contrainte, chaque reference croisee reste intact. Ce qui est coupe, c'est l'emballage : le formatage Markdown dont le modele n'a jamais eu besoin, les declarations en double, l'echafaudage pedagogique, le remplissage motivationnel et le tissu conjonctif grammatical qui coute des tokens sans vehiculer d'information.

Sur une specification produit typique, tu obtiens 50-65% de reduction sur les quatre passes. C'est de l'argent reel economise a chaque appel API, et du contexte reel libere pour la conversation qui compte vraiment.

Ce compresseur est aussi le moteur de la passe de compression dans [vibecheck](https://github.com/wallmage/vibecheck).

## Ce que "lossless" signifie vraiment

Ce n'est pas de la synthese. Une synthese decide ce qui est important et jette le reste. C'est dangereux pour des fichiers d'instructions -- une contrainte manquante et le modele hallucine une fonctionnalite que tu avais explicitement exclue.

Lossless veut dire : si le document original dit "plafonner les subventions freemium a 3% du revenu mensuel", la version compressee dit exactement la meme chose. Aucun fait n'est supprime, reformule de maniere ambigue ou silencieusement fusionne. Les numeros de section restent fixes, donc les references croisees ("voir S8.7.3") ne cassent jamais. L'AI produit des decisions de code et d'architecture identiques a partir de la version compressee comme a partir de l'originale.

La garantie est assuree par un portail d'approbation -- chaque modification non mecanique est proposee avec un diff avant/apres et n'est appliquee qu'apres ta validation.

## 23 techniques en 4 passes

Le compresseur fonctionne en quatre passes progressives. Chacune va plus en profondeur. C'est toi qui decides ou t'arreter.

### Pass 1 -- Nettoyage mecanique (automatique, 10-25% de reduction)

Aucun jugement ici. Cette passe supprime le formatage que les LLMs parsent de maniere identique qu'il soit present ou non :

- **Suppression du Markdown** : titres `##`, `**gras**`, puces `- `, pipes de tableau `| |`, clotures de code ` ``` `, citations `> `, lignes `---` -- tout supprime. Le texte et la structure restent ; la decoration part.
- **Conversion de format** : les tableaux deviennent des lignes separees par des point-virgules. Les listes a puces deviennent des elements en ligne separes par des point-virgules. La syntaxe de lien `[texte](url)` est reduite a l'URL.
- **Scan de verification** : apres la suppression, un scan complet detecte les residus Markdown. Les couleurs hexadecimales (`#FF0000`), les globs regex (`*.txt`), les pipes shell et le frontmatter YAML sont reconnus comme du contenu et conserves.

Techniques 1-2 du catalogue. Executable en toute securite sur n'importe quel document. Economies typiques : 10-25% de reduction en mots.

### Pass 2 -- Compression preservant les faits (soumise a approbation, +5-15%)

C'est la que ca devient creatif. Chaque modification proposee t'est presentee avec les economies estimees et n'est appliquee qu'apres approbation :

- **Deduplication par references croisees** (Technique 3) : les faits enonces a trois endroits sont enonces une fois, les autres remplacees par un pointeur comme "selon S6".
- **Compression des blocs de code** (Technique 4) : les definitions de struct/class deviennent des descriptions inline compactes preservant tous les noms de champs, types et enums. Exception : Python/YAML/Makefile restent intacts car les espaces y sont semantiques.
- **Compression des configs JSON** (Technique 5) : les exemples JSON complets deviennent des descriptions de schema.
- **Suppression des details par version** (Technique 6) : si un tableau resume et un detail par version disent la meme chose, le detail est supprime.
- **Compression des blocs motivationnels** (Technique 7) : les sections "pourquoi c'est important" sont reduites a l'insight technique essentiel.
- **Compression des justifications de design** (Technique 8) : quatre paragraphes d'argumentation deviennent une phrase avec les raisons principales.
- **Compression des listes de cas d'usage** (Technique 9) : les cas d'usage detailles sur plusieurs paragraphes deviennent chacun une ligne.
- **Compression des maquettes ASCII** (Technique 10) : l'art ASCII couteux en tokens devient une description textuelle compacte.

Chaque modification doit passer une validation stricte : tous les faits presents, l'AI prendrait des decisions identiques, aucune ambiguite, chaque fait retracable jusqu'a sa section canonique.

### Pass 3 -- Suppression du contenu destine aux humains (soumise a approbation, +15-25%)

La question pour chaque candidat : "Si je supprime ca, l'AI produira-t-elle de moins bons resultats ?" Si non, c'est une cible.

- **Tutoriels pour debutants** (Technique 11) : "Installez votre IDE depuis l'App Store. Ca prend 30-60 minutes..." devient une liste de ce qu'il faut installer, pas comment.
- **Exercices pratiques et activites d'apprentissage** (Technique 12) : l'AI n'a pas besoin des exercices d'apprentissage de quelqu'un d'autre.
- **Plannings journaliers** (Technique 13) : les heures de reveil et les pauses cafe sont supprimees. Les noms de fonctionnalites et les exigences techniques restent.
- **Coaching et contenu motivationnel** (Technique 14) : "Pas de panique" et "Felicitations !" portent zero information pour le modele.
- **Matrices de validation** (Technique 15) : un tableau de 30 lignes ou chaque fonctionnalite dit "SHIP" devient deux lignes.
- **Tableaux comparatifs concurrentiels** (Technique 16) : "Nous gagnons sur toutes les dimensions" motive les humains, pas les modeles.
- **Exemples de workflow humain** (Technique 17) : les walkthroughs "a quoi ressemble ta journee".

Pass 3 conserve le QUOI et le POURQUOI. Il supprime le COMMENT-pour-humains.

### Pass 4 -- Reecriture telegraphique (opt-in explicite, +25-40%)

Une reecriture complete du document en stenographie ultra-dense. Chaque mot doit porter de l'information ; s'il n'existe que pour rendre la prose fluide pour un lecteur humain, il est supprime. Pour les documents ou l'AI est le seul consommateur.

- **Elimination des phrases de liaison** (Technique 18) : "Cela signifie que", "ce qui permet", "afin de", "il est important de noter que" -- tout supprime.
- **Fragments separes par des point-virgules** (Technique 19) : les phrases completes deviennent des fragments cle-valeur. Articles, "est/sont", sujets de remplissage supprimes.
- **Condensation de phrases multiples** (Technique 20) : cinq phrases expliquant un concept deviennent le fait essentiel.
- **Suppression des sujets implicites** (Technique 21) : quand le titre de section dit "Fonction de recherche", pas besoin de repeter "La fonction de recherche" au debut de chaque phrase.
- **Notation compacte** (Technique 22) : barres obliques pour les options (`immediate/batched/silent`), fleches pour les flux (`offline -> local queue -> sync`), deux-points pour les relations (`Auth: JWT + refresh`).
- **Compression des motifs structurels repetitifs** (Technique 23) : decrire le motif une fois, puis utiliser la stenographie.

Les LLMs modernes parsent le style telegraphique avec une fidelite totale. Point-virgules, fleches, options separees par des barres obliques -- aucune famille de modeles n'a de probleme avec ca.

## Modes

Trois modes, chacun construisant sur le precedent :

- **Strict Lossless** (par defaut) : Pass 1 + Pass 2. Chaque fait survit. Le document reste lisible par un humain. Ideal pour les fichiers que toi et l'AI consultez -- CLAUDE.md, regles d'agent partagees, specifications actives. Sur, conservateur, economies reelles.

- **AI-Lossless** : Ajoute le Pass 3. Supprime tutoriels, coaching, plannings journaliers, matrices de validation -- tout ce qui n'aide qu'un lecteur humain. L'AI recoit exactement le meme signal. Ideal pour les system prompts et instructions d'agent que l'AI consomme mais que tu relis rarement.

- **AI-Only** : Ajoute le Pass 4. Reecriture complete en style telegraphique. Densite maximale, lecture pas agreable. Ideal pour les prompts de production a haut volume ou chaque token economise se multiplie sur des milliers d'appels quotidiens.

## Workflow

| Pass | Ce qu'il fait | Approbation | Reduction typique |
|------|--------------|-------------|-------------------|
| 1 | Supprime le formatage Markdown, convertit tableaux/listes | Automatique | 10-25% |
| 2 | Deduplique les faits, compresse code/JSON/justifications | Par element | +5-15% |
| 3 | Supprime tutoriels, coaching, plannings, echafaudage humain | Par element | +15-25% |
| 4 | Reecriture telegraphique complete, fragments plutot que phrases | Opt-in explicite | +25-40% |

Les quatre passes sur une specification produit typique : **50-65% de reduction**. Sur un document de 35 000 mots, ca represente environ 25 000-32 000 tokens en moins par appel API.

## Principes de conception

- **Lossless par defaut** : Strict Lossless est le mode par defaut. Toute compression plus agressive necessite un opt-in explicite.
- **Les numeros de section sont sacres** : les ancres de reference croisee ne sont jamais renumerotees, meme quand les sections environnantes sont supprimees. "Voir S8.7.3" fonctionne toujours.
- **Portail d'approbation** : chaque modification non mecanique est proposee avec un diff et des economies estimees. Rien ne change sans ta validation.
- **23 techniques avec exemples** : catalogue complet avant/apres dans `references/techniques.md`.
- **12 cas limites geres** : documents courts, prompts riches en code, entrees deja compressees, textes non anglais/CJK, URLs embarquees, documents multilingues, documents sur le Markdown, prose creative/juridique, tres grands documents, et plus.

## Structure

```
lossless-prompt-compressor/
├── SKILL.md              — Manuel d'utilisation (~350 lignes)
└── references/
    └── techniques.md     — Catalogue complet des techniques avec exemples avant/apres
```

## Auteur

[Wallny](https://github.com/wallmage)
