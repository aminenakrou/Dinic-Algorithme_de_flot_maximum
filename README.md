<p align="center">
  <img src="https://img.shields.io/badge/Langage-C11-00599C?style=for-the-badge&logo=c&logoColor=white" />
  <img src="https://img.shields.io/badge/Algorithme-Dinic-22d3ee?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Polytech-Lille-e06060?style=for-the-badge" />
  <img src="https://img.shields.io/badge/IS3-2025-34d399?style=for-the-badge" />
</p>

<h1 align="center">Algorithme de Dinic — Flot Maximum</h1>

<p align="center">
  Implémentation en C de l'algorithme de <strong>Dinic</strong> pour le calcul du flot maximum<br>
  dans un réseau orienté à partir d'un fichier au format DIMACS.
</p>

<p align="center">
  <a href="https://aminenakrou.github.io/Dinic-Algorithme_de_flot_maximum/">🌐 Site interactif</a> &nbsp;·&nbsp;
  <a href="https://github.com/aminenakrou/Dinic-Algorithme_de_flot_maximum/blob/main/rapport.pdf">📄 Rapport PDF</a> &nbsp;·&nbsp;
  <a href="#-exécution">🚀 Démarrage rapide</a>
</p>

---

## 📌 À propos

Le **problème du flot maximum** consiste à déterminer la quantité maximale de flot transportable d'une source à un puits dans un réseau orienté, en respectant les capacités des arcs. Ce problème est fondamental en optimisation combinatoire et trouve des applications dans les réseaux de transport, la distribution d'énergie et le routage de données.

L'**algorithme de Dinic** résout ce problème de manière efficace grâce à une recherche itérative de chemins augmentants par **parcours en largeur (BFS)** dans un graphe résiduel, garantissant une complexité en **O(V² × E)**.

> 🔗 **[Voir la démonstration interactive →](https://aminenakrou.github.io/Dinic-Algorithme_de_flot_maximum/)**

---

## ⚡ Résultats

| Réseau | Sommets | Arcs | Flot maximum | Temps |
|:-------|:-------:|:----:|:------------:|:-----:|
| **R1** | 5 | 7 | 40 | 2 ms |
| **R2** | 6 | 8 | 15 | 2 ms |
| **G_100_300** | 102 | 290 | 9 860 177 | 4 ms |
| **G_900_2700** | 902 | 2 670 | 28 258 807 | 6 ms |
| **G_2500_7500** | 2 502 | 7 450 | 42 791 871 | 6 ms |

> Tous les calculs s'exécutent en **1 à 6 ms**, démontrant l'efficacité de l'implémentation même sur des graphes de grande taille.

---

## 📁 Structure du projet

```
├── dinic.h          # Structures de données et prototypes
├── dinic.c          # Implémentation complète de Dinic
├── Makefile         # Compilation et exécution
├── index.html       # Site web interactif (GitHub Pages)
├── rapport.pdf      # Rapport d'analyse et de conception
└── README.md
```

---

## 🛠 Prérequis

- **GCC** (ou tout compilateur compatible C11)
- **Make**

```bash
gcc --version
make --version
```

---

## 🔧 Compilation

```bash
make
```

Cela génère l'exécutable `dinic` et le fichier objet `dinic.o`.

---

## 🚀 Exécution

**Avec le Makefile :**

```bash
make run FILE=chemin/vers/fichier.dimacs
```

**Manuellement :**

```bash
./dinic chemin/vers/fichier.dimacs
```

Le programme génère un fichier `resultat.txt` contenant le flot maximal et le détail des flux sur chaque arc.

**Exemple :**

```bash
make run FILE=R1
# -> resultat.txt créé
```

---

## 📝 Format d'entrée (DIMACS)

```
c Commentaire
p max <nb_sommets> <nb_arcs>
n <id> s                        # source
n <id> t                        # puits
a <u> <v> <capacité>            # arc de u vers v
```

---

## 📄 Format de sortie

Le fichier `resultat.txt` produit :

```
Flot maximal : 40

Flux sur les arcs :
1 -> 4 : flux 20 / capacité 20
1 -> 2 : flux 20 / capacité 20
2 -> 4 : flux 10 / capacité 10
2 -> 3 : flux 10 / capacité 10
3 -> 5 : flux 20 / capacité 30
4 -> 5 : flux 20 / capacité 20
4 -> 3 : flux 10 / capacité 10
```

---

## 🧠 Architecture du code

Le programme est décomposé en fonctions modulaires :

| Fonction | Rôle |
|:---------|:-----|
| `buildGraph` | Lecture du fichier DIMACS et construction du réseau initial |
| `buildResidualGraph` | Construction du graphe résiduel (arcs directs + inverses) |
| `shortestPath` | BFS pour trouver le plus court chemin source → puits |
| `minCapacity` | Calcul de la capacité minimale sur un chemin |
| `updateResidualGraph` | Mise à jour des capacités résiduelles |
| `updateNetworkFlow` | Propagation des flots dans le réseau initial |
| `ecrireResultat` | Écriture des résultats dans `resultat.txt` |
| `freeGraph` / `freePath` | Libération mémoire |

---

## 📊 Choix de la structure de données

Trois structures ont été comparées. La **liste de successeurs** a été retenue :

| Critère | Matrice d'incidence | Tableau de sommets | Liste de successeurs |
|:--------|:-------------------:|:------------------:|:--------------------:|
| **Mémoire** | O(n × m) | O(n + m) | ✅ O(n + m) |
| **Accès successeurs** | O(m) | O(deg⁺) | ✅ O(deg⁺) |
| **Ajout d'arc** | O(n) | O(1) | ✅ O(1) |

Chaque sommet pointe vers une liste chaînée de `Maillon` contenant le sommet destination, la capacité et le flot courant.

---

## 🔄 Déroulement sur R1

Réseau R1 : 5 sommets, 7 arcs, source = 1, puits = 5.

```
Itération 1 : BFS → 1→4→5       min_cap = 20    flot = 20
Itération 2 : BFS → 1→2→3→5     min_cap = 10    flot = 30
Itération 3 : BFS → 1→2→4→5     min_cap = 10    flot = 40
Itération 4 : Aucun chemin       → STOP          flot_max = 40
```

> 💡 Testez l'exécution pas à pas sur le **[site interactif](https://aminenakrou.github.io/Dinic-Algorithme_de_flot_maximum/)** — simulez l'algorithme visuellement avec des particules de flot animées !

---

## 🧹 Nettoyage

```bash
make clean
```

Supprime l'exécutable, les fichiers objets et `resultat.txt`.

---

## 👥 Auteurs

| | Nom | Rôle |
|:-:|:----|:-----|
| 🔵 | **Amine Nakrou** | Implémentation C, site interactif |
| 🟢 | **Yassine Bourhaba** | Pseudo-codes, rédaction du rapport |

**Tutrice** : Marie-Eléonore Kessaci — Polytech Lille, IS3 — 2025

---

## 📬 Contact

Pour questions, suggestions ou rapports de bugs :  
📧 [Amine.Nakrou@polytech-lille.net](mailto:Amine.Nakrou@polytech-lille.net)

---

<p align="center">
  <sub>Polytech Lille · IS3 · Analyse et Conception · 2025</sub>
</p>
