# Intro**📌 Structure de la fenêtre du shell :**

- **Prompt (invite de commande)** :
  - $ → utilisateur standard
  - # → utilisateur root (⚠️ superadmin)
- **Zone de saisie** : là où tu tapes ta commande.
- **Résultat** : retour de la commande (infos, erreurs, rien si succès silencieux ✅).



**💡 Exemple : ls -l /home/user**

- ls → commande
- -l → option
- /home/user → argument (chemin)
- ➕ Attention aux **espaces** entre commande/option/argument : ils sont des **séparateurs d'expression**, pas des espaces "textes". ⚠️



**🛑 Compte root :**

- 👑 Super administrateur
- Ne **jamais** l'utiliser pour des tâches courantes.
- ❌ Connexion SSH directe avec root souvent désactivée.
- ✅ Bonne pratique : se connecter avec un **compte utilisateur normal**, puis faire une **élévation de privilèges** (ex : sudo).



**🔐 Changer son mot de passe :**

- passwd → permet de changer son MDP (demande l'ancien + le nouveau)



**🌍 Variables d’environnement :**

- Ex : $HOME, $USER, $PATH
- Pour voir toutes les variables : printenv ou env
- Pour en définir une temporairement : export MA_VAR=truc



**🆘 Aide intégrée (super utile quand on est bloqué) :**

- man → manuel (ex : man ls)
- --help → aide rapide (ex : ls --help)
- apropos → recherche dans les manuels (ex : apropos password)
