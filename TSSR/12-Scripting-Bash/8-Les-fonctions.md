# **🧠 Pourquoi utiliser des fonctions ?**

Les fonctions servent à **regrouper des commandes réutilisables** dans un script.

🔄 Elles évitent les répétitions, rendent le script plus **modulaire** et **lisible**.

👀 Convention : noms en **minuscules**, avec préfixe function_ ou func_ pour distinguer des commandes natives.



## **🛠️ Déclaration et appel**

Déclaration :
```bash
nom_fonction() {
commandes
}
```

⚠️ La fonction doit être **déclarée avant d’être appelée**.

Appel : Simplement en écrivant le nom de la fonction comme une commande dans le script.



## **💬 Variables et fonctions**

✔️ Les **variables sont globales par défaut**, elles restent accessibles à l'intérieur des fonctions.

🔁 Modifier une variable dans une fonction affecte sa valeur dans tout le script.

Exemple logique :

- `nbr=50` (hors fonction)
- `func_majnbr()` modifie `nbr=100`
  ➡️ Après l’appel : nbr vaut **100**



## **📦 Passage de paramètres**

Les fonctions acceptent des **paramètres positionnels** comme les scripts :

- `$1`, `$2`, etc. → accèdent aux arguments passés à la fonction
- `$@` ou `$*` → tous les arguments

Exemple :

Appel avec : `func_accueil "Marc" "Dubois"`

Contenu fonction : `echo "Bonjour $1 $2"`



## **🧩 Fichier de fonctions externe**

Tu peux **séparer les fonctions** dans un autre fichier (mesfonctions)

👨‍🔧 Il suffit de les importer dans le script principal avec : `source ~/mesfonctions` **ou** `. ~/mesfonctions`

🎁 Avantages :

➕ meilleure lisibilité

➕ réutilisation entre plusieurs scripts

➕ plus simple à déboguer

⚠️ Le fichier **n’a pas besoin d’être exécutable**, il suffit d’avoir les droits de lecture.

