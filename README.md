# Dungeon Crawler

![Python](https://img.shields.io/badge/Python-3-blue?style=flat-square)
![Interface](https://img.shields.io/badge/Interface-terminal-green?style=flat-square)

Dungeon Crawler est un **jeu d’exploration de donjons en terminal**, écrit en **Python**. Trouvez la sortie d’un donjon généré aléatoirement avant que votre torche ne s’éteigne, en collectant des bonus de visibilité et en évitant les fantômes.

Les dimensions, la génération et les règles sont configurables en ligne de commande. Un mode difficile transforme le donjon en labyrinthe et une graine permet de reproduire une partie. Le jeu utilise uniquement la bibliothèque standard de Python.

> Projet académique ULB — INFO-F106.
> Projet d’informatique 2 · 2022–2023

<a id="captures-decran"></a>

## 📸 Captures d’écran

![Exploration d’un donjon dans le terminal](https://github.com/user-attachments/assets/ae1b5fdd-f0a6-43c3-9cee-67e89bfa7510)


<a id="demonstrations-video"></a>

## 📸 Démonstrations vidéo

Les démonstrations fournies montrent les issues et le comportement liés aux fantômes.

| Victoire | Défaite | Fantômes |
| --- | --- | --- |
| [Vidéo « win »](Vidéos/win.avi) | [Vidéo « loss »](Vidéos/loss.avi) | [Vidéo « ghosts »](Vidéos/ghosts.avi) |

---

## 📖 Sommaire

- [Fonctionnalités](#fonctionnalites)
- [Prérequis](#prerequis)
- [Configuration](#configuration)
- [Installation](#installation)
- [Lancement](#lancement)
- [Utilisation](#utilisation)
- [Architecture](#architecture)
- [Flux général](#flux-general)
- [Structure du projet](#structure-du-projet)
- [Tests](#tests)
- [Problèmes fréquents](#problemes-frequents)
- [Documentation](#documentation)
- [Licence](#licence)

<a id="fonctionnalites"></a>

## ✨ Fonctionnalités

- **Génération procédurale** : crée des salles rectangulaires non adjacentes, leurs ouvertures et un réseau de couloirs à partir des dimensions demandées.
- **Deux topologies de donjon** : le mode normal combine deux arbres couvrants aléatoires pour créer davantage de passages ; `--hard` conserve un seul arbre couvrant et produit un labyrinthe.
- **Exploration à visibilité limitée** : le rendu n'affiche que les cases situées dans un disque autour du joueur ; la portée diminue périodiquement avec la torche.
- **Objectif et bonus** : le symbole `#` marque la sortie à atteindre, tandis que les bonus `@` augmentent la portée de visibilité lorsqu'ils sont ramassés.
- **Fantômes optionnels** : les fantômes `G` se déplacent à intervalles configurables ; avec `--ghosts-walls`, ils choisissent uniquement des cases accessibles sans traverser les murs.
- **Parties reproductibles** : `--seed` initialise le générateur aléatoire afin de retrouver la même génération et le même placement initial.

<a id="prerequis"></a>

## 🧰 Prérequis

- Python 3.
- Un terminal compatible avec l'encodage UTF-8 et les caractères de dessin de cadres.

Les modules importés par le projet sont `argparse`, `os` et `random`, qui font partie de la bibliothèque standard de Python. Aucune dépendance externe n'est à installer.

<a id="configuration"></a>

## ⚙️ Configuration

Le projet ne contient pas de fichier de configuration : les réglages sont passés à `main.py` en arguments de ligne de commande. `width` et `height` sont les deux arguments positionnels obligatoires.

| Option | Rôle | Valeur par défaut |
| --- | --- | --- |
| `--rooms` | Nombre de salles à générer | `5` |
| `--bonuses` | Nombre de bonus | `2` |
| `--seed` | Graine du générateur aléatoire | aucune |
| `--view-radius` | Rayon transmis au rendu autour du joueur | `6` |
| `--torch-delay` | Nombre de déplacements entre deux diminutions de la torche | `7` |
| `--bonus-radius` | Augmentation de la portée de visibilité par bonus | `3` |
| `--minwidth` / `--maxwidth` | Largeur minimale / maximale des salles | `4` / `8` |
| `--minheight` / `--maxheight` | Hauteur minimale / maximale des salles | `4` / `8` |
| `--openings` | Nombre d'ouvertures demandées par salle | `2` |
| `--hard` | Génère un labyrinthe à partir d'un seul arbre couvrant | désactivé |
| `--ghosts` | Nombre de fantômes | `0` |
| `--ghosts-delay` | Nombre de déplacements du joueur entre deux déplacements des fantômes | `2` |
| `--ghosts-walls` | Empêche les fantômes de traverser les murs | désactivé |

<a id="installation"></a>

## 📦 Installation

```bash
git clone https://github.com/9Chrk/DungeonCrawler.git
cd DungeonCrawler
```

<a id="lancement"></a>

## ▶️ Lancement

Depuis la racine du dépôt, lancez une partie en indiquant la largeur puis la hauteur du donjon :

```bash
python3 main.py 40 20
```

L'exemple suivant fixe une graine, ajoute des bonus et active deux fantômes :

```bash
python3 main.py 40 20 --rooms 5 --bonuses 3 --ghosts 2 --seed 42
```

<a id="utilisation"></a>

## 🎮 Utilisation

À chaque tour, saisissez une direction puis validez avec Entrée. Un déplacement bloqué par un mur ne modifie pas l'état de la partie.

| Touche | Déplacement |
| --- | --- |
| `Z` | Haut |
| `Q` | Gauche |
| `S` | Bas |
| `D` | Droite |

Le joueur est affiché par `X`. Il gagne en rejoignant `#`, perd s'il rencontre un fantôme `G` ou lorsque sa portée de visibilité atteint zéro. Les bonus `@` prolongent cette portée.

<a id="architecture"></a>

## 🧱 Architecture

Le point d'entrée `main.py` analyse les arguments, construit un `DungeonGenerator`, puis transmet la grille et les éléments générés à `Player`. La boucle principale efface le terminal, instancie `Renderer` avec la position et la visibilité courantes, lit une touche et délègue le déplacement au joueur jusqu'à la victoire ou la défaite.

`generation.py` coordonne la construction du monde. `DungeonGenerator` crée une `Grid`, place les `Box` représentant les salles, ouvre leurs murs puis place bonus, départ, sortie et fantômes. La génération utilise `Grid.spanning_tree()` : un parcours en profondeur aléatoire choisit les passages à conserver. En mode normal, l'union de deux arbres couvrants ajoute des alternatives ; en mode difficile, un seul arbre est utilisé.

`grid.py` porte l'état structurel du donjon. Chaque `Node` conserve les quatre passages (`up`, `down`, `left`, `right`) ainsi que les marqueurs de jeu. `Grid` garantit que les modifications de murs sont appliquées des deux côtés d'une case, fournit les voisins accessibles et isole les salles. Les valeurs de position sont encapsulées dans `Pos2D` (`pos2d.py`) ; `Box` (`box.py`) calcule les bornes et les bords des salles.

`player.py` gère l'état dynamique : position, compteurs de torche, portée, bonus et fantômes. Après un déplacement autorisé, il applique les effets de la case puis, au rythme configuré, déplace les fantômes. `renderer.py` transforme finalement les murs et les marqueurs de `Grid` en caractères de terminal ; `Renderer` restreint l'affichage aux positions situées dans le rayon euclidien autour du joueur.

<a id="flux-general"></a>

## 🧬 Flux général

```text
arguments CLI
    │
    ▼
main.py ──► DungeonGenerator.generate()
    │              │
    │              ├── Grid / Box / Pos2D : grille, salles et passages
    │              └── bonus, départ, sortie, fantômes
    ▼
Player ──► déplacement, torche et fantômes
    │
    ▼
Renderer ──► rendu UTF-8 limité au champ de vision
    │
    └──► victoire ou défaite
```

<a id="structure-du-projet"></a>

## 📂 Structure du projet

```text
DungeonCrawler/
├── main.py          # Analyse les arguments et exécute la boucle de jeu
├── generation.py    # Crée le donjon, les salles, objets et fantômes
├── grid.py          # Modèles Node/Grid, murs, voisins et arbre couvrant
├── player.py        # Déplacements, torche, bonus et logique des fantômes
├── renderer.py      # Conversion de la grille en rendu terminal UTF-8
├── pos2d.py         # Valeur de position à deux coordonnées
├── box.py           # Bornes et bords des salles rectangulaires
├── test.py          # Tests de la grille, du rendu et de la génération
├── _test.py         # Copie des tests présents dans test.py
├── projet2.pdf      # Document associé au projet
├── Vidéos/          # Démonstrations AVI : victoire, défaite et fantômes
├── utf8.txt         # Fichier vide présent dans le dépôt
└── LICENSE          # Licence MIT
```

<a id="tests"></a>

## 🧪 Tests

`test.py` contient des tests de style `pytest` pour les positions, les murs, les voisins accessibles, le rendu textuel et le générateur. Ils vérifient notamment la symétrie des passages, la connexion du donjon généré et le fait qu'un donjon sans salle en mode difficile soit un labyrinthe. `_test.py` contient actuellement le même jeu de tests.

<a id="problemes-frequents"></a>

## ❗ Problèmes fréquents

### Les caractères du donjon sont illisibles

Le rendu de `renderer.py` utilise des caractères Unicode tels que `┌`, `─` et `│`. Utilisez un terminal configuré en UTF-8 avec une police qui les prend en charge.

### Une erreur survient lors de la création des salles

Les dimensions doivent être cohérentes avec les tailles minimales et maximales de salles. Le générateur ne valide pas ces combinaisons avant d'appeler `random.randint()` : augmentez `width` et `height`, réduisez les bornes des salles ou demandez moins de salles.

### Les imports locaux ne sont pas trouvés

Lancez la commande depuis la racine du dépôt, où se trouvent `main.py`, `generation.py`, `grid.py` et les autres modules importés.

<a id="documentation"></a>

## 📄 Documentation

- [Document du projet](projet2.pdf)
- [Vidéo « win »](Vidéos/win.avi)
- [Vidéo « loss »](Vidéos/loss.avi)
- [Vidéo « ghosts »](Vidéos/ghosts.avi)

<a id="licence"></a>

## 📜 Licence

Ce projet est distribué sous la [licence MIT](LICENSE).
