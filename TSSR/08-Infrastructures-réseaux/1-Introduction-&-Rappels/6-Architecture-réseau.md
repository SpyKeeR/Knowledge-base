# Architecture réseau

### **🏛️ Architecture réseau en 3 niveaux**

Une **architecture réseau d’entreprise** est souvent organisée en **trois couches hiérarchiques** : **Cœur (Core)** 🔁, **Distribution** 🪢 et **Accès (Access)** 🔌

👉 C’est le modèle **Hiérarchique Cisco**, qui assure évolutivité, performances et organisation.



## **🔁 Couche Cœur (Core Layer)**

C’est la **colonne vertébrale** du réseau. - Matériel : **Routeurs très performants** ou **switches L3 très puissants**
- Gérer le **trafic inter-réseaux** (campus, data center, succursales…)
- Fournir **vitesse, disponibilité, redondance**
- **Aucun service utilisateur**, juste le **transport rapide** entre zones

💡 On évite les traitements lourds ici (firewall, ACL…), pour que ça aille vite !



## **🪢 Couche Distribution (Distribution Layer)**

C’est l’**interface logique** entre le cœur et l’accès. - Matériel : **Switches de niveau 3** (avec routage)
- Gérer la **segmentation réseau (VLAN)** et le **routage inter-VLAN**
- Appliquer **politiques de sécurité** (ACL, QoS, filtrage)
- Faire office de **barrière logique** entre les utilisateurs et le backbone

📌 Souvent, cette couche regroupe les équipements par service, étage, bâtiment…



## **🔌 Couche Accès (Access Layer)**

C’est là que les **utilisateurs et périphériques** se connectent. - Matériel : **Switches niveau 2 ou L3 léger**, bornes Wi-Fi
- Fournir la **connectivité filaire/sans-fil**
- Appliquer **contrôle d’accès** (port-security, 802.1X)
- Gérer les **VLAN utilisateurs**

👥 C’est la **porte d’entrée** du réseau pour les PC, imprimantes, téléphones IP…



## **🧱 Topologie logique : vision simplifiée**

Une **topologie logique** n'est pas un plan physique (câbles), mais une **organisation fonctionnelle** :

- Utilisateurs ➜ couche Accès ➜ couche Distribution ➜ couche Cœur
- Chaque couche a un **rôle précis**, ce qui permet : **modularité**, **maintenance facilitée**, **ajouts évolutifs**



### **🧬 Pourquoi cette séparation ?**

- **Performance** : chaque couche se concentre sur une tâche
- **Sécurité** : isolation et contrôle du trafic par couches
- **Scalabilité** : facile d’**ajouter des utilisateurs**, un étage ou un site
- **Résilience** : redondance possible à chaque niveau

💡 En gros, plus le réseau grandit, plus cette organisation devient vitale.

