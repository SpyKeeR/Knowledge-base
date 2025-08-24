# Variables

## **📦 C’est quoi une variable ?**

Une **variable** est une **zone mémoire nommée** → elle contient une donnée que tu peux lire, modifier, ou supprimer.

🟢 Création : `var="contenu de la variable"`

📢 Affichage (on lit le **contenu**, pas le nom) : `echo $var`

**👤 Exemple simple**

```bash
prénom="Romain"
echo $prénom # Affiche : Romain
```
```bash
prénom="Sophia"  
echo $prénom # Affiche : Sophia
```

🧠 *→ même contenant (prénom), mais contenu différent selon le contexte.*



## **🧹 Supprimer une variable**

`unset prénom`



## **🌍 Variables d’environnement**

➡️ **Créées par le système** au démarrage, ou lors de la connexion.

📢 Liste toutes les variables d’environnement : `env`

📢 Liste *tout* (env + fonctions) → très verbeux : `set`



## **✨ Variables utiles (majuscules = convention)**

| **Variable**        | **Contenu**                      |
|---------------------|----------------------------------|
| $HOME              | Répertoire perso (/home/user)    |
| $USER ou $LOGNAME | Nom d'utilisateur                |
| $PWD               | Répertoire courant               |
| $PATH              | Dossiers contenant les commandes |
| $LANG              | Langue + encodage                |
| $SHELL             | Chemin de l’exécutable du shell  |

🔁 **Équivalent Windows** : %PATH%, %USERNAME%, etc.

🌀 Raccourci ultime : ~ (ALT-G-R-2 sur clavier FR) = $HOME



