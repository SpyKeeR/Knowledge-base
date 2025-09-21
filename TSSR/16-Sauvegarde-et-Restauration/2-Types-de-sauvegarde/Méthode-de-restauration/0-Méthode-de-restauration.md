# Méthode de restauration

La **restauration** est la **finalité essentielle** de toute stratégie de sauvegarde. Une **sauvegarde non testée** est **inutile** :

🧪 **Tests obligatoires**

- ✔️ Les sauvegardes doivent être **testées régulièrement**
- 🔁 Un plan de sauvegarde **complet** inclut la **vérification des restaurations**



## 📂 **Restauration à la demande**

Concerne les **fichiers perdus, effacés ou altérés.** Options disponibles :

1.  📝 **Restaurer sur l’original >** ⚠️ Risque d’écrasement des données actuelles
2.  📁 **Restaurer sur un autre emplacement (même système) >** ✅ Méthode la plus courante
3.  🖥️ **Restaurer sur un autre système >** 🔍 Vérification avant retour en production
4.  🧠 **Versionning >** 🔙 Retour à une version précédente du fichier
5.  💾 **Restauration complète de VM >** 🔄 Remise en service rapide d’un environnement complet



## 🌐 **Restauration d’un annuaire Active Directory (AD)**

- 🗑️ **Objets simples** → Corbeille AD
- 💥 **Perte d’un contrôleur de domaine (CD)**
  - 🖼️ Restauration via **image système complète**
  - 🔄 Vérification de la **réplication entre CDs**
- 🧱 **Corruption de la base AD uniquement**
  - 🔧 Démarrage du CD en **mode sans échec**
  - 📂 Restauration des fichiers de la base
  - 🔄 Vérification de la **réplication**



## 🖥️ **Restauration complète d’un système (Bare-Metal Recovery)**

🔧 Permet de **reconstruire un serveur** sur un nouveau disque :

1.  🧬 Architecture cible **identique** à l’originale
2.  💽 **Restauration de l’image** système
3.  📦 **Restauration du delta** (données modifiées depuis l’image)


## 🗄️ **Restauration d’une Base de Données**

Chaque BDD a ses propres méthodes de restauration :

- 🏢 **SQL Server >** 🔧 Restauration via **SQL Server Management Studio**
- 🐬 **MySQL >** 📥 Import d’un **fichier DUMP.sql >** mysql --user=mon_user --password=mon_password < fichier_source.sql
- 🧱 **Oracle >** 📁 **Fichiers depuis une sauvegarde à froid** 📦 **Datadump Import** 🛠️ **RMAN**
