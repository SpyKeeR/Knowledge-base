# **💬 Définition de PowerShell**

PowerShell est un langage **orienté objet** conçu par Microsoft, qui remplace les commandes classiques par des **commandlets** capables de **manipuler des objets** riches en infos.

Contrairement au **CMD ou au Bash**, ici, chaque commande renvoie un objet complet, pas juste une chaîne de texte.



## **⚙️ Commandlets & Objets**

Les **commandlets** (cmdlets) sont les **commandes natives** de PowerShell. Elles manipulent des objets Windows.

Ex. : Chercher un utilisateur “Tom” →

- CMD : ne renvoie qu'une valeur textuelle
- PowerShell : renvoie un **objet** avec plusieurs propriétés (ex : Name, IsAdmin, etc.)



## **🧩 Propriétés & Méthodes**

Un **objet PowerShell** = **données** (propriétés) + **actions possibles** (méthodes)

### 🖊️ Exemple : un **stylo**

- Propriétés : Marque, Couleur, Forme, Taille
- Méthodes : Enlever le bouchon, Écrire, Remettre le bouchon  
  PowerShell fonctionne **pareil** : chaque ressource (service, fichier, processus...) est un objet avec ses attributs et ses actions.



### **🔍 Exemple : get-service**

Cette commande renvoie une **liste d’objets service** avec :

- Propriétés visibles : Status, Name, DisplayName
- Propriétés masquées : accessibles via `Select-Object`, `Format-List`, etc.
- Méthodes : `Start()`, `Stop()`, `Disable()`  
  → On interagit **directement** avec les objets retournés pour piloter les services du système.

