## **📄 Qu’est-ce qu’un script shell ?**

Un **script shell** est un simple fichier texte 📁 contenant une série de **commandes** exécutées dans un shell (bash en général).

Mais pour qu’il soit **fiable, maintenable et réutilisable**, il faut une approche structurée 🧠.



## **🧠 Introduction à l’analyse de scripts**

Avant d’écrire, il faut **analyser le besoin**. C’est une logique de développeur, pas de bidouilleur 💡.

On suit une méthode simple mais solide :

1.  🧩 Définir l’**objectif du script** → Quel problème il résout ?
2.  📘 Rédiger un **cahier des charges** → Liste claire des fonctions attendues
3.  🔁 Concevoir un **algorithme** → Étapes logiques pour atteindre l’objectif

👉 Cette phase permet d’éviter les scripts improvisés qui deviennent ingérables.



## **🌠 Les étapes de l’analyse**

Voici les **3 grandes étapes** :

- **Énoncé du problème** : Formulation simple du besoin (ex : "archiver les fichiers vieux de 7 jours")
- **Cahier des charges** : Entrées/sorties attendues, contraintes, environnement cible, gestion des erreurs…
- **Algorithme** : Suite d’actions logiques (ex : lire dossier → filtrer fichiers → archiver → log)

Cette méthode **structure** la pensée, évite les oublis, et prépare à l’automatisation réelle 🧠.



## **🧾 Édition du script sous Linux**

🖊️ Pour écrire un script, il faut un **éditeur de texte**. Sous Linux :

- `vim` : dispo partout, puissant mais avec une courbe d’apprentissage
- Alternatives : `nano` (simple), `gedit` (graphique), VS Code (avec extensions), etc.

🔥 L’éditeur n’est **pas imposé**, mais **Vim est incontournable** dans les environnements serveurs où aucune interface graphique n’est dispo.

