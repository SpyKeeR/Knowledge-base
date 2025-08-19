# Navigation IOS**🧑‍💻 Modes d'exécution IOS**

Sous IOS, on trouve **plusieurs niveaux d'accès** :

- **Mode utilisateur** (prompt : >) ➜ accès basique, commandes non destructives (ex : ping, show).
- **Mode privilégié** (prompt : #) ➜ accès complet aux commandes (ex : sauvegarde, reload, config).

🔁 Passage du mode utilisateur au mode privilégié avec la commande : enable



**⚙️ Modes de configuration**

Une fois en mode privilégié, on peut entrer en **mode configuration** (global ou spécifique) via configure terminal :

- **Global** ➜ prompt : (config)#
- **Interface** ➜ prompt : (config-if)#
- **Ligne** (console, vty, etc.) ➜ prompt : (config-line)#

📤 Sortir du mode courant : exit

🔚 Sortir de tous les modes ➜ end ou Ctrl+Z



**🧭 Navigation entre les modes**

| **Objectif**               | **Commande**       | **Résultat (Prompt)** |
|----------------------------|--------------------|-----------------------|
| Accéder au mode privilégié | enable             | Switch#               |
| Entrer en config globale   | configure terminal | Switch(config)#       |
| Accéder à la console       | line console 0     | Switch(config-line)#  |
| Accéder à une interface    | interface <nom>  | Switch(config-if)#    |
| Sortir d’un sous-mode      | exit               | Retour au mode global |
| Quitter tous les modes     | end ou Ctrl+Z      | Retour en privilégié  |



**🧃 À retenir**

- Chaque mode a un **niveau d'accès** et un **prompt spécifique**
- La **commande enable** ouvre l'accès aux configurations
- configure terminal permet de **modifier le système**
- exit et end sont essentiels pour **naviguer efficacement**
- Savoir **identifier les prompts** t’aide à ne pas te tromper de niveau
