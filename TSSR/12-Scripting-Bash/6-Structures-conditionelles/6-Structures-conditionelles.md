# 6 - Structures conditionelles**🧠 Objectifs du module**
- Comprendre les **structures conditionnelles**
- Savoir **écrire des tests** pour piloter des scripts
- Apprendre à **comparer** nombres et chaînes efficacement



**🛠️ Contexte d'utilisation des conditions**

Dans le monde réel, les scripts **n’exécutent pas toujours tout** :

Ils prennent **des décisions** selon le contexte.

Ex :

Tu écris un script qui attend un argument utilisateur.

S’il n’y en a pas, il prendra une valeur par défaut.

Mais si l’argument n’est **pas un entier valide**, il faut aussi gérer ça !

👉 Donc, tes scripts doivent souvent tester :

- S’il y a des **arguments** fournis,
- Si une variable est **vide ou non**,
- Si un contenu est **valide ou invalide**.



**⚙️ Cas pratique d’introduction**

Dans l’exemple donné, on a une variable nb qui dépend :

- du **nombre d’arguments** passés au script,
- de la **présence** ou non d’un contenu dans nb,
- de la **validité** du contenu (ex : est-ce un entier ?).

🧩 Logique simplifiée :

- Si **1 argument** → nb = $1
- Sinon → nb = nbdefault
- Si nb est vide ou invalide → **réaffectation avec valeur par défaut**



**📌 Structure de condition en Shell : if**

Le shell utilise une syntaxe simple mais puissante :

if condition ; then  
action  
fi

💡 En pratique, on utilise souvent des **tests intégrés** via [[ ... ]].

if [[ $nbarg -eq 1 ]] ; then  
nb=$1  
fi
