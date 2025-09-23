# Configuration et restrictions

## 👥 **Gestion des accès par groupes Active Directory**

- Possibilité de restreindre l’accès aux collections via des groupes AD déjà créés
- Flexibilité importante pour gérer les droits utilisateurs selon les besoins organisationnels



## 🔧 **Paramétrage des sessions et connexions**

- Contrôle précis des actions autorisées durant une session RDS
- Possibilité de filtrer ce que l’utilisateur peut faire pendant la connexion (ex : impression, copier-coller...)



## 🗂️ **Gestion des profils utilisateur**

- Profil utilisateur lié localement à un serveur = risque de perte de données en cas de connexion sur un autre serveur
- Solution : mettre en place un **disque de profil utilisateur sur un partage réseau** pour que le profil suive l’utilisateur partout (profil itinérant)



## 📋 **Configuration des applications publiées**

- Chaque application a ses propres propriétés et options de filtrage
- Possibilité d’autoriser ou restreindre l’accès aux apps par utilisateur ou groupes spécifiques via l’onglet **"Affectation d’utilisation"**
- Imbrication possible des groupes pour une gestion fine des droits



## 🔒 **Gestion des restrictions : deux niveaux de filtrage**

1.  **Filtrage** au niveau de la **collection**

    - Permet un **contrôle global** des accès **utilisateurs/groupes**
    - Gestion **centralisée simplifiée**

2.  **Filtrage** au niveau des **RemoteApps**

    - **Affinage** des restrictions **par application**
    - Plus de **précision** dans les **droits d’accès**
