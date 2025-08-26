# Gabarit & champs

Un **gabarit** est un **modèle standard de configuration** d’un item matériel ou logiciel.

💡 Il sert à **préremplir automatiquement** les champs communs à une série d’équipements.



### **🔧 À quoi ça sert concrètement ?**

- Éviter de ressaisir 10 fois les mêmes infos à la main
- Gagner du temps quand tu ajoutes **plusieurs équipements du même type**
- Standardiser les données d’inventaire
- Automatiser certains champs avec des **règles dynamiques**



### **🏗️ Comment ça fonctionne ?**

1.  On crée un **gabarit à partir du descriptif constructeur** (ex : fiche technique).
2.  On **remplit tous les champs génériques** (ex : fabricant, modèle, type, statut…).
3.  On **laisse vides les champs spécifiques** à chaque équipement (ex : numéro de série, nom, mémoire RAM si elle est personnalisable).

💡 C’est le même principe qu’un **modèle Word ou Excel pré-rempli** : tu ne modifies que ce qui change.



## **⚙️ Automatisation avec les balises 💡**

GLPI permet d’utiliser des **balises spéciales** pour remplir certains champs automatiquement.

**🔮 Exemple : numéro d’inventaire automatique**

**>** LAPTOP-###

- ### = compteur (ex : LAPTOP-001, LAPTOP-002…)

### 🔣 **Balises disponibles :**

| **Balise** | **Signification**                  |
|------------|------------------------------------|
| #         | Chiffre avec incrément automatique |
| \Y         | Année sur 4 chiffres (ex : 2025)   |
| \y         | Année sur 2 chiffres (ex : 25)     |
| \m         | Mois (01 à 12)                     |
| \d         | Jour (01 à 31)                     |

**Astuce :** Les champs **automatisables** sont repérés par une **petite baguette magique** dans l’interface de création de gabarit.

**📋 Champs les plus courants dans un item de configuration :**

| **Champ** | **Rôle** |
|----|----|
| **Nom** | Identifiant unique dans GLPI |
| **Lieu** | Localisation physique de l’équipement |
| **Statut** | En stock, déployé, en panne, au rebut, etc. |
| **Type** | PC, imprimante, serveur… (à personnaliser dans les intitulés) |
| **Fabricant** | Constructeur de l’équipement (ex : Dell, HP…) |
| **Modèle** | Référence exacte (ex : HP Elitebook 840 G8) |
| **Numéro d’inventaire** | Code interne de suivi |
| **Responsable** | Personne chargée de cet équipement |
| **Groupe technique** | Équipe en charge de la maintenance ou de la gestion |
| **Utilisateur** | Personne qui utilise cet item |
| **Groupe d’utilisateurs** | Si l’équipement est partagé |
| **Commentaires** | Infos diverses utiles à la gestion |

