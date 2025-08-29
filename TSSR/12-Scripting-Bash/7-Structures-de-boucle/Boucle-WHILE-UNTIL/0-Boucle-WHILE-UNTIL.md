## **📘 Définition & principe - WHILE**

La boucle while répète un bloc d’actions **tant que** la **condition est vraie** ✅

Dès que la condition échoue ❌, on sort de la boucle.

📌 *La condition est souvent un test (`[[ ... ]]`, `[ ... ]`, etc.), mais peut aussi être **n'importe quelle commande** dont le code retour est 0 (= succès).*



### 🛠️ **🧱 Syntaxe de base**

- **Moderne** : `while CONDITION ; do ACTIONS ; done`
- **Classique** :
  ```bash
  while CONDITION
    do
    ACTIONS
    done
  ```

Les deux versions font exactement la même chose. Le style "moderne" permet des scripts plus compacts.



### 👤 **🎤 Exemple : forcer la saisie d’un nom**

Objectif : **tant que l’utilisateur n’a rien saisi**, on redemande son prénom.

Condition : `[[ -z "$nom" ]]` → vrai si `$nom` est vide.

Fonctionnement :

1.  On teste `$nom`
2.  Si vide, on affiche un message et on lit une nouvelle valeur avec read
3.  Sinon, on sort et on affiche "Bonjour `$nom`"



### 🔁 **♾ Cas particulier : les boucles infinies**

Pour créer une boucle infinie (utile pour les **menus**), on utilise : Tant que true retourne 0, la boucle tourne sans fin.

- `while true ; do ... done`
- ou plus léger : `while : ; do ... done`



## 🧠 **📘 Définition & logique - UNTIL**

La boucle until est le **miroir logique** de la boucle while :

➡️ Elle **boucle tant que la condition est fausse** ❌

➡️ Elle **s'arrête dès que la condition devient vraie** ✅

📌 *Même structure, même syntaxe que while. Ce qui change, c’est juste la logique d’entrée dans la boucle.*



### 🛠️ **🧱 Syntaxe de base**

- **Forme compacte** : `until CONDITION ; do ACTIONS ; done`
- Identique au while, mais la logique est inversée : ici on exécute tant que la **CONDITION échoue** (code retour ≠ 0).



### ♾ **🔁 Cas particulier : boucle infinie**

Tu peux faire une boucle infinie en `until false ; do ... done` 

➡️ La commande false retourne **toujours 1**, donc la boucle ne s'arrête jamais.

Pratique pour les menus ou des scripts qui tournent en continu sans interruption.

