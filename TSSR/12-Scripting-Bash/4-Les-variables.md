# 4 - Les variables

Les **variables** servent à **stocker temporairement** des infos pendant l’exécution d’un script. Elles permettent de :

➡️ Réutiliser des données dans plusieurs commandes

➡️ Rendre un script dynamique et personnalisable

➡️ Contrôler des flux logiques (condition, boucle…)



## **🧩 Catégories de variables**

🔹 **Locales** → valables uniquement dans le Shell courant

🔹 **D’environnement** → exportées vers les sous-shells

🔹 **Constantes** (lecture seule) → créées avec `declare -r`, non modifiables

🔹 **Réservées** → gérées par le Shell, servent à des infos systèmes

⚠️ Convention :
- Noms **en minuscule** pour locales (mon_nom) 
- **MAJUSCULE + _** pour env & constantes (HOME, LOGNAME, etc.)



### **📂 Variables locales – Exemples pratiques**

🧷 Affectation : `logdir="/var/log/"`

📢 Affichage : `echo "$logdir"`

📁 Utilisation dans commande : `mkdir -p "$logdir" 2>/dev/null`



### 🌍 **Variables d’environnement – Partage entre Shells**

Une variable devient **d’environnement** lorsqu’elle est **exportée**, et donc disponible pour tous les sous-shells :

🔸 Depuis une locale : `logdir="/var/log"` puis `export logdir`

🔸 Directement : `export logdir="/var/log"`

## **🔒 Variables réservées – Spéciales & Automatiques**

- `$$` → PID du processus actuel
- `$?` → Code retour de la dernière commande (0 si OK)
- `$!` → PID du dernier processus lancé en arrière-plan
- `$#` → Nombre de paramètres passés au script
- `$1` à `$9` → Les paramètres eux-mêmes
- `$0` → Nom du script exécuté
- `$@` ou `$*` → Tous les paramètres passés (enchaînés ou séparés)

## **🧰 Commande set – Affectation ou affichage**

- `set` sans argument → affiche **toutes les variables** du Shell
- `set "$LOGNAME" $(uname -n)` → `$1=$LOGNAME`, `$2=$(uname -n)`
  Très utile pour injecter des valeurs dans des variables positionnelles ($1, $2…)



## **🎤 Commande read – Saisie interactive**

Permet de demander une **entrée clavier** à l'utilisateur et de la stocker :

➡️ Variante raccourcie : `read -p "Entrez votre nom : " nom`

➡️ Plusieurs valeurs saisies : `read -p "Nom et prénom : " nom prenom`

⚠️ Tous les mots en trop vont dans la **dernière variable** (risque de bazar)

🔄 Astuce : utiliser une variable **tampon** pour capturer l'excédent :

`read -p "Nom et prénom : " nom prenom tampon`

Puis `unset tampon` pour nettoyer

**🔁 Cycle de vie d'une variable**

1️⃣ Création → `prenom=Sofia`

2️⃣ Lecture → `$prenom` → donne **Sofia**

3️⃣ Modification → `prenom=Romain`

4️⃣ Relecture → `$prenom` → donne **Romain**

➡️ **Export** → `export nom` ou `export nom=valeur`

5️⃣ Suppression → `unset prenom` libère la mémoire
