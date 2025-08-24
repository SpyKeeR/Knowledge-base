# Variables Environnement

## **🔹 C’est quoi une variable dans le shell ?**

- Une **zone de mémoire vive** où on stocke une info temporairement
- Elle disparaît à l’extinction du shell (ou de la machine)
- On peut créer, modifier, lire, ou rendre globale une variable



## **🛠️ Création et lecture d'une variable** 
```bash
var="Bonjour"
echo $var`
```
- Pas d’espace autour du =
- Pour **lire** une variable, on met $ devant son nom



## **🌍 Rendre une variable disponible partout (export)**
- `export var="Bonjour Monde"`

➡️ Devient une **variable d’environnement** : Disponible dans tous les **sous-shells** (Pas inscrit au redémarrage, ni aux sessions non-enfant du shell actuel)  
Pour inscrire une variable > \~/.bashrc (\~/.profile across all shells) (ouverture même session user) OU /etc/environment (Toute la machine)



## **🔁 Créer une variable à partir d’une commande** 
```bash
heure=$(date +"%H:%M")
echo $heure
```

- Exécute date et stocke le résultat dans la variable
- ⚠️ Syntaxe : $() (et pas juste $)



## **📦 Les variables d’environnement système (exemples courants)**

| **Variable**        | **Contenu**                           |
|---------------------|---------------------------------------|
| $HOME              | Répertoire personnel                  |
| $USER ou $LOGNAME | Nom d’utilisateur connecté            |
| $SHELL             | Chemin du shell utilisé               |
| $HOSTNAME          | Nom de la machine                     |
| $PATH              | Répertoires où chercher les commandes |

### **🔍 À quoi sert $PATH ?**

- Contient la **liste des chemins** où le shell cherche les exécutables (Le PATH est d'ailleurs initialisé par le /etc/profile en vérifiant UID 0)
- Exemple : `echo $PATH`

➡️ Résultat : /usr/local/sbin:/usr/local/bin:/usr/bin:/bin



## **🔧 Trouver où se trouve une commande**
`which ls`

- Affiche le chemin complet de la commande (/usr/bin/ls)
- Le shell va utiliser ce chemin réel pour exécuter la commande


