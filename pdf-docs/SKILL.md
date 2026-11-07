---
name: pdf-docs
description: Produit un PDF et le range dans le projet quand l'utilisateur demande un cours ou une explication sur une notion (-> course/), un audit ou une review de son code (-> audit/), ou une roadmap, un planning ou les étapes du projet (-> planning/). Déclencheurs - "fais-moi un cours sur", "explique-moi la notion de", "audite mon code", "fais une review", "roadmap", "planning", "étapes du projet".
argument-hint: "[sujet du cours | fichiers à auditer | projet à planifier]"
---

# PDF docs : cours, audit, planning

## 1. Identifier le type de demande

| Demande | Dossier |
|---|---|
| Cours, explication d'une notion | `course/` |
| Audit, review, analyse de code | `audit/` |
| Roadmap, planning, étapes du projet | `planning/` |

Si tu ne peux pas trancher, demande à l'utilisateur avant d'écrire quoi que ce soit.

## 2. Préparer le projet

1. Racine du projet : `git rev-parse --show-toplevel`. Hors d'un repo git, prends le dossier courant.
2. Crée le dossier cible : `mkdir -p <racine>/<dossier>`.
3. Ajoute au `.gitignore` de la racine **les trois** dossiers, même si un seul sert aujourd'hui, et seulement les lignes qui manquent :
   ```bash
   cd "$(git rev-parse --show-toplevel 2>/dev/null || pwd)"
   touch .gitignore
   for d in /course/ /audit/ /planning/; do
     grep -qxF "$d" .gitignore || echo "$d" >> .gitignore
   done
   ```

## 3. Écrire le contenu

Rédige en français, en syntaxe **Typst**, dans un fichier temporaire hors du projet :

```bash
tmp="$(mktemp -d)" && src="$tmp/doc.typ"
```

En tête du document : `#set page(margin: 2cm)`, `#set text(lang: "fr")`, `#set heading(numbering: "1.")`, puis un titre `= ...`. Utilise `#table(...)` pour les tableaux et des blocs ```` ```lang ```` pour le code. Le contenu suit ce plan :

### Cours (`course/`)
1. Objectifs : ce que l'utilisateur saura faire à la fin.
2. Prérequis.
3. Explication progressive, de l'intuition vers le détail.
4. Exemples de code commentés, dans le langage du projet quand c'est pertinent.
5. Pièges fréquents.
6. Résumé en quelques points.
7. 2 ou 3 exercices, avec les solutions en fin de document.

### Audit (`audit/`)
Lis réellement le code avant d'écrire, et **ne modifie aucun fichier**.
1. Périmètre : fichiers et modules lus, commit (`git rev-parse --short HEAD`).
2. Synthèse : état général et les 3 problèmes les plus importants.
3. Tableau des constats : `Sévérité (critique/haute/moyenne/basse) | Fichier:ligne | Problème | Recommandation`.
4. Détail des constats critiques et hauts, avec des extraits de code.
5. Points positifs.
6. Plan d'action priorisé.

### Planning (`planning/`)
1. Objectif du projet et définition de "terminé".
2. État actuel, déduit du code et de l'historique git s'il y en a.
3. Étapes ou jalons, dans l'ordre, chacun avec ses livrables et ses dépendances.
4. Tableau récapitulatif : `Étape | Livrable | Dépend de | Statut`.
5. Risques et points ouverts.

## 4. Générer le PDF

Le PDF s'appelle `<dossier>/AAAA-MM-JJ-<slug>.pdf` (slug court en kebab-case, par exemple `2026-11-07-ownership-rust.pdf`).

```bash
typst compile "$src" "<racine>/<dossier>/AAAA-MM-JJ-<slug>.pdf" && rm -rf "$tmp"
```

- Si la compilation échoue sur une erreur de syntaxe, corrige le `.typ` et recompile.
- Si `typst` n'est pas installé, arrête-toi et dis-le à l'utilisateur (`brew install typst` sur macOS, `cargo install --locked typst-cli` sur Linux). Ne passe pas par une autre méthode.

Seul le PDF reste dans le projet : la source Typst est supprimée.

## 5. Terminer

Donne à l'utilisateur le chemin du PDF et un résumé de deux ou trois lignes. Ne recopie pas le contenu du document dans la réponse.
