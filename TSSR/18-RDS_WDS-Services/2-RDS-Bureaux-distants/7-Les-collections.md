# Les collections

## 🎯 **📌 Rôle de la collection dans les services RDS**

La **collection** est un composant **indispensable** du déploiement RDS. Elle regroupe un ensemble de **serveurs hôtes de session** offrant des services spécifiques aux utilisateurs.

Elle permet :

- 🖥️ La fourniture des **bureaux distants**
- 🧩 La **publication d'applications** (RemoteApp)

Chaque collection peut être dédiée à un service métier (ex. : une collection pour la comptabilité).



## 🛠️ **🧠 Importance et configuration de la collection**

- ✔️ **Obligatoire après le déploiement** du rôle RDS
- ✔️ Permet de **structurer et centraliser** l’accès aux ressources RDS
- ✔️ Se configure uniquement via le **Gestionnaire de serveur**, pas par une MMC



## 🧭 **🔧 Étapes de création d’une collection**

1.  📂 Ouvrir le **Gestionnaire de serveur**
2.  🧭 Aller dans **"Services de Bureau à Distance"**
3.  📑 Cliquer sur **"Collections"**
4.  ➕ Choisir **"Créer une nouvelle collection"**
5.  🧙‍♂️ Suivre l’assistant pour configurer :
    - Les serveurs membres  
    - Les utilisateurs autorisés  
    - Les applications à publier  
    - Les paramètres de session
