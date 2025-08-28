# Conteneurs / UO

## **🗃️ Conteneurs systèmes par défaut**

AD propose **4 conteneurs par défaut** dans chaque domaine, qu’il faut **éviter d’utiliser pour l’organisation** :

- 🧱 **Built-in** ➜ groupes locaux issus de la **base SAM** avant promotion DC
- 💻 **Computers** ➜ lieu par défaut des **objets ordinateurs** (postes/serveurs rejoints au domaine)
- ⚙️ **System** ➜ contient les **données critiques du fonctionnement AD** (peu manipulé, très sensible)
- 👥 **Users** ➜ stockage par défaut des **utilisateurs/groupes** (à ne pas utiliser tel quel en production)

💡 Ces conteneurs sont figés (non personnalisables) → Utilise plutôt des **Unités d’Organisation (UO)** pour structurer proprement.



## **🗂️ Unités d’Organisation (UO) : l’outil de gestion logique**

Les **UO (OU)** sont comme des **dossiers virtuels** dans AD :

Elles permettent de **classer** et **déléguer** de manière propre.

📌 Avantages :

- 🔐 délégation de gestion possible par UO
- 🧠 application ciblée des **GPO (stratégies de groupe)**
- 🧹 séparation logique (par service, site, usage, etc.)

⚠️ Un **objet ne peut être dans une seule UO à la fois** (pas de duplication).

✅ Les UO sont protégées contre la suppression accidentelle (par défaut activé).



## **🏢 Structuration logique avec des exemples**

Tu peux créer des UO comme :

- OU_Services ➜ sous-UO Comptabilité, IT, Marketing
- OU_Ordinateurs ➜ Postes_Bureau, Serveurs, Portables
- OU_Groups ➜ regroupe tous les groupes AD
- OU_Users ➜ séparation par rôle ou localisation (Users_Tours, Users_Remote…)

💡 Structure = **clé d'une administration propre + GPO efficaces**



## **🧭 Pourquoi ne pas rester sur les conteneurs par défaut ?**

- Pas de **liaison GPO directe** sur les conteneurs (Users, Computers)
- Pas de **délégation granulaire** possible
- Pas d’**arborescence personnalisable**

👉 Crée rapidement une structure d’UO **dès le début**, même simple, pour éviter la galère plus tard.

