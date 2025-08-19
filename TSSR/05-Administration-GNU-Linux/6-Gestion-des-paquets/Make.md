# Make**🏗️ Préparer l’environnement**

Avant de commencer :

➔ **Installer l’environnement de compilation** (outils + librairies nécessaires).

➔ **Comprendre que chaque projet peut avoir ses propres besoins.**

**Très important :** toujours **lire la documentation** (README, INSTALL, etc.) du projet avant de se lancer.



**🚀 Cheminement classique d'une installation depuis les sources**



| **Étape** | **Commande** | **Ce que ça fait** |
|----|----|----|
| Configuration | ./configure | Vérifie les dépendances et prépare la compilation. |
| Compilation | make | Compile le code source en binaire. |
| Installation | make install | Installe les fichiers compilés dans le système (avec sudo). |

**Remarques :**

- Chaque erreur sur ./configure = dépendance manquante ➔ il faut l'installer et recommencer.
- Jusqu’à make, on peut tout faire avec un **utilisateur standard**.
- Pour make install, il faut passer **root** (sudo) car on modifie le système.
