# Dungeon Crawler

Dungeon Crawler est un jeu en terminal écrit en Python. Le joueur explore un donjon généré aléatoirement, cherche la sortie, récupère des bonus de visibilité et peut rencontrer des fantômes.

> **Projet :** Projet d'année n°2
>
> **Auteur :** Jawad Cherkaoui
>
> **Date :** 2 avril 2023
>
> **Matricule :** 576517

## Fonctionnalités

- Génération procédurale d'un donjon à partir de ses dimensions et de paramètres de salles.
- Affichage textuel du donjon et visibilité limitée autour du joueur.
- Déplacements au clavier avec `Z`, `Q`, `S` et `D`.
- Bonus (`@`) qui augmentent la portée de visibilité.
- Sortie (`#`) à atteindre pour gagner la partie.
- Fantômes (`G`) optionnels, avec un mode où ils respectent les murs.
- Graine aléatoire optionnelle pour reproduire une génération.

## Prérequis

- Python 3.
- Un terminal compatible avec les caractères de dessin de cadres.

Le projet n'importe que des modules de la bibliothèque standard de Python ; aucune dépendance externe n'est à installer.

## Installation

```bash
git clone https://github.com/9Chrk/DungeonCrawler.git
cd DungeonCrawler
```

## Lancement

`width` et `height` sont des arguments obligatoires. Par exemple, pour générer un donjon de 40 par 20 cases :

```bash
python3 main.py 40 20
```

À chaque tour, saisissez l'une des touches suivantes puis validez avec Entrée :

| Touche | Déplacement |
| --- | --- |
| `Z` | haut |
| `Q` | gauche |
| `S` | bas |
| `D` | droite |

## Paramètres

Les options ci-dessous complètent les deux dimensions obligatoires.

| Option | Rôle | Valeur par défaut |
| --- | --- | --- |
| `--rooms` | Nombre de salles à générer | `5` |
| `--bonuses` | Nombre de bonus | `2` |
| `--seed` | Graine du générateur aléatoire | aucune |
| `--view-radius` | Distance de rendu autour du joueur | `6` |
| `--torch-delay` | Nombre de déplacements entre deux diminutions de la torche | `7` |
| `--bonus-radius` | Augmentation de la visibilité par bonus | `3` |
| `--minwidth` / `--maxwidth` | Largeur minimale / maximale des salles | `4` / `8` |
| `--minheight` / `--maxheight` | Hauteur minimale / maximale des salles | `4` / `8` |
| `--openings` | Nombre d'ouvertures par salle | `2` |
| `--hard` | Active le mode difficile | désactivé |
| `--ghosts` | Nombre de fantômes | `0` |
| `--ghosts-delay` | Nombre de déplacements entre deux déplacements des fantômes | `2` |
| `--ghosts-walls` | Empêche les fantômes de traverser les murs | désactivé |

Exemple avec une génération reproductible et des fantômes :

```bash
python3 main.py 40 20 --rooms 5 --bonuses 3 --ghosts 2 --seed 42
```

## Structure du projet

```text
DungeonCrawler/
├── main.py          # Point d'entrée et lecture des arguments
├── generation.py    # Génération du donjon, des objets et des fantômes
├── grid.py          # Grille, cases et gestion des murs
├── player.py        # Déplacements et règles de jeu
├── renderer.py      # Rendu textuel de la grille
├── pos2d.py         # Positions en deux dimensions
├── box.py           # Représentation des salles rectangulaires
├── test.py          # Tests de la grille, du rendu et du générateur
├── projet2.pdf      # Document du projet
├── Vidéos/          # Vidéos au format AVI
└── LICENSE          # Licence MIT
```

## Ressources

- [Document du projet](projet2.pdf)
- [Vidéo « win »](Vidéos/win.avi)
- [Vidéo « loss »](Vidéos/loss.avi)
- [Vidéo « ghosts »](Vidéos/ghosts.avi)

## Licence

Ce projet est distribué sous la [licence MIT](LICENSE).
