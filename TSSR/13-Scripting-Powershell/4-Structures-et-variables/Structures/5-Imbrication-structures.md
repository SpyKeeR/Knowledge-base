# 🔥 **L'importance des structures**

Les structures (condition, boucle, switch) ont un rôle clé : elles organisent la logique dans un script. Elles permettent d’automatiser des tâches d’admin ou techniques, en rendant le script interactif et dynamique.

## 🔗 **Qu'est-ce que l'imbrication ?**

Imbriquer, c’est mettre une structure à l’intérieur d’une autre, en respectant la logique et la ponctuation propre à chaque structure (accollades, conditions). Ça crée un enchaînement clair et organisé dans le script.



### ⚙️ **Exemple simple d'imbrication**

Une boucle do-while englobe un if qui teste $x. Si $x > 10, on utilise un switch pour afficher un message selon la valeur de $x (ex: 11 ou 12). Sinon, on demande à l'utilisateur de saisir une nouvelle valeur.

La boucle continue tant que $x < 100.



## 🧩 **Fonctionnement détaillé**

- La boucle do-while tourne tant que $x < 100
- À chaque tour, on teste $x avec if
- Si $x > 10 → switch analyse $x
- switch affiche un message selon la valeur (ex : 11 ou 12)
- Sinon → demande à l’utilisateur de saisir un nouveau nombre
- La variable $x est mise à jour à chaque tour pour éviter une boucle infinie



## ⚠️ **Attention à la ponctuation et la portée**

Chaque structure doit être bien délimitée par ses accolades { }. Si elles manquent, le script génère des erreurs détectées par un IDE (ex : VS Code).

Respecter ça, c’est la base pour gérer l’imbrication sans bug.



## 🚀 **Exemple amélioré avec interaction**

- On récupère $x via Read-Host
- do-while tourne tant que $x > 0
- if teste si $x > 10
- switch gère plusieurs cas (20, 30, default)
- else affiche un message d’erreur si $x ≤ 10
- À chaque tour, $x est demandé à nouveau, pour garder le contrôle

